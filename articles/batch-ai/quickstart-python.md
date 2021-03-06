---
title: Szybki start na platformie Azure — szkolenie CNTK przy użyciu usługi Batch AI — Python | Microsoft Docs
description: Szybko dowiedz się, jak uruchamiać zadanie szkoleniowe CNTK przy użyciu usługi Batch AI i zestawu SDK języka Python
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: f535c9adf4926f29ae9cade6382debedab73937d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Uruchamianie zadania szkoleniowego CNTK przy użyciu zestawu Azure Python SDK

Ten przewodnik Szybki start zawiera szczegóły dotyczące używania zestawu Azure Python SDK do uruchamiania zadań szkoleniowych Microsoft Cognitive Toolkit (CNTK) przy użyciu usługi Batch AI. 

W tym przykładzie używa się bazy danych MNIST ręcznie zapisanych obrazów do szkolenia splotowej sieci neuronowej (CNN) w jednowęzłowym klastrze procesorów GPU. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja Azure — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

* Azure Python SDK — zobacz [instrukcje dotyczące instalacji](/python/azure/python-sdk-azure-install)

* Konto magazynu platformy Azure — zobacz [Tworzenie konta magazynu platformy Azure](../storage/common/storage-create-storage-account.md)

* Poświadczenia nazwy głównej usługi Azure Active Directory — zobacz [Tworzenie nazwy głównej usługi przy użyciu interfejsu wiersza polecenia](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* Zarejestruj dostawców zasobów usługi Batch AI dla subskrypcji przy użyciu programu Azure Cloud Shell lub interfejsu wiersza polecenia platformy Azure. Rejestracja dostawcy może potrwać do 15 minut.

  ```azurecli
  az provider register -n Microsoft.BatchAI
  az provider register -n Microsoft.Batch
  ```


## <a name="configure-credentials"></a>Konfiguracja poświadczeń
Utwórz następujące parametry w skrypcie języka Python, podstawiając własne wartości:

```Python
# credentials used for authentication
client_id = 'my_aad_client_id'
secret = 'my_aad_secret_key'
token_uri = 'my_aad_token_uri'
subscription_id = 'my_subscription_id'

# credentials used for storage
storage_account_name = 'my_storage_account_name'
storage_account_key = 'my_storage_account_key'

# specify the credentials used to remote login your GPU node
admin_user_name = 'my_admin_user_name'
admin_user_password = 'my_admin_user_password'
```

Najlepszym rozwiązaniem jest przechowywanie wszystkich poświadczeń w oddzielnych pliku konfiguracji, który nie jest pokazany w tym przykładzie. Sprawdź [przepisy](https://github.com/Azure/BatchAI/tree/master/recipes), aby wdrożyć plik konfiguracji. 

## <a name="authenticate-with-batch-ai"></a>Uwierzytelnianie za pomocą usługi Batch AI

Aby uzyskać dostęp do usługi Azure Batch AI, musisz dokonać uwierzytelnienia przy użyciu usługi Azure Active Directory. Poniżej znajduje się kod umożliwiający uwierzytelnienie w usłudze (utwórz obiekt `BatchAIManagementClient`) przy użyciu poświadczeń nazwy głównej usługi i identyfikatora subskrypcji:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=client_id, secret=secret, token_uri=token_uri)

batchai_client = batchai.BatchAIManagementClient(credentials=creds,
                                         subscription_id=subscription_id
)
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Klastry i zadania usługi Batch AI to zasoby platformy Azure i muszą być umieszczone w grupie zasobów platformy Azure. Poniższy fragment kodu tworzy grupę zasobów:

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'

resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)

resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Przygotowanie udziału plików platformy Azure
W celach ilustracyjnych ten przewodnik Szybki start używa udziału plików platformy Azure do hostowania danych szkoleniowych i skryptów na potrzeby zadania uczenia. 

1. Utwórz udział plików o nazwie *batchaiquickstart*.

  ```Python
  from azure.storage.file import FileService 
 
  azure_file_share_name = 'batchaiquickstart' 
 
  service = FileService(storage_account_name, storage_account_key) 
 
  service.create_share(azure_file_share_name, fail_on_exist=False)
  ``` 
 
2. Utwórz katalog w udziale o nazwie *mnistcntksample* 

  ```Python
  mnist_dataset_directory = 'mnistcntksample' 
 
  service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False) 
  ```
3. Pobierz [pakiet przykładowy](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) i rozpakuj go. Przekaż zawartość do katalogu, w którym będziesz wykonywać skrypt języka Python.

  ```Python
  for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt', 'ConvNet_MNIST.py']:     
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f) 
  ```

## <a name="create-gpu-cluster"></a>Tworzenie klastra procesora GPU

Utwórz klaster usługi Batch AI. W tym przykładzie klaster zawiera jeden węzeł maszyny wirtualnej STANDARD_NC6. Rozmiar maszyny wirtualnej ma jeden procesor GPU NVIDIA K80. Zainstaluj udział plików w folderze o nazwie *azurefileshare*. Pełna ścieżka do tego folderu w węźle obliczeniowym procesora GPU to $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare' 
 
parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus', 
 
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6', 
 
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
         admin_user_name=admin_user_name,
         admin_user_password=admin_user_password), 
 
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
         manual=models.ManualScaleSettings(target_node_count=1)
     ), 
 
    # Configure each node in the cluster
    node_setup=models.NodeSetup( 
 
        # Mount shared volumes to the host
         mount_volumes=models.MountVolumes(
             azure_file_shares=[
                 models.AzureFileShareReference(
                     account_name=storage_account_name,
                     credentials=models.AzureStorageCredentialsInfo(
         account_key=storage_account_key),
         azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
               storage_account_name, azure_file_share_name),
                  relative_mount_path = relative_mount_point)],
         ), 
    ), 
) 
batchai_client.clusters.create(resource_group_name, cluster_name, parameters).result() 
```

## <a name="get-cluster-status"></a>Pobieranie stanu klastra

Monitoruj stan klastra przy użyciu następującego polecenia: 

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count)) 
```

Powyższy kod wyświetla podstawowe informacje dotyczące alokacji klastra, takie jak poniższe:

```Shell
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving 0
 
```  

Klaster będzie gotowy, gdy węzły zostaną przydzielone i zostanie zakończone przygotowanie (patrz atrybut `nodeStateCounts`). Jeśli wystąpił błąd, atrybut `errors` zawiera opis błędu.

## <a name="create-training-job"></a>Tworzenie zadania szkolenia

Gdy klaster jest gotowy, skonfiguruj i prześlij zadanie uczenia. 

```Python
job_name = 'myjob' 
 
parameters = models.job_create_parameters.JobCreateParameters( 
 
     # Location where the job will run
     # Ideally this should be co-located with the cluster.
     location='eastus', 
 
     # The cluster this job will run on
     cluster=models.ResourceId(cluster.id), 
 
     # The number of VMs in the cluster to use
     node_count=1, 
 
     # Override the path where the std out and std err files will be written to.
     # In this case we will write these out to an Azure Files share
     std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point), 
 
     input_directories=[models.InputDirectory(
         id='SAMPLE',
         path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(relative_mount_point, mnist_dataset_directory))], 
 
     # Specify directories where files will get written to 
     output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")], 
 
     # Container configuration
     container_settings=models.ContainerSettings(
        models.ImageSourceRegistry(image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')), 
 
     # Toolkit specific settings
     cntk_settings = models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
 ) 
 
# Create the job 
batchai_client.jobs.create(resource_group_name, job_name, parameters).result() 
```

## <a name="monitor-job"></a>Monitorowanie zadania
Możesz sprawdzić stan zadania przy użyciu następującego polecenia: 
 
```Python
job = batchai_client.jobs.get(resource_group_name, job_name) 
 
print('Job state: {0} '.format(job.execution_state.name))
```

Dane wyjściowe są podobne do następujących: `Job state: running`.

Atrybut `executionState` zawiera bieżący stan wykonania zadania:
* `queued`: zadanie oczekuje na dostępność węzłów klastra
* `running`: zadanie zostało uruchomione
* `succeeded` (lub `failed`): zadanie jest zakończone; atrybut `executionInfo` zawiera szczegóły dotyczące wyniku
 
## <a name="list-stdout-and-stderr-output"></a>Wyświetlanie danych wyjściowych dzienników stdout i stderr
Użyj poniższego polecenia, aby wyświetlić listę linków do plików dziennika stdout i stderr:

```Python
files = batchai_client.jobs.list_output_files(resource_group_name, job_name, models.JobsListOutputFilesOptions("stdouterr")) 
 
for file in list(files):
     print('file: {0}, download url: {1}'.format(file.name, file.download_url)) 
```
## <a name="delete-resources"></a>Usuwanie zasobów

Użyj następującego polecenia, aby usunąć zadanie:
```Python
batchai_client.jobs.delete(resource_group_name, job_name) 
```

Użyj następującego polecenia, aby usunąć klaster:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```
## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób uruchamiania zadania szkolenia CNTK w klastrze usługi Batch AI przy użyciu zestawu Azure Python SDK. Aby dowiedzieć się więcej o używaniu usługi Batch AI z różnymi zestawami narzędzi, zobacz [przepisy na szkolenie](https://github.com/Azure/BatchAI).
