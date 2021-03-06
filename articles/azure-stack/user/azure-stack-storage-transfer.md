---
title: Narzędzia do magazynu Azure stosu
description: Dowiedz się więcej o Azure stosu magazynu danych narzędzia transferu
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/25/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 2876565f3d6a3411eb170d4da640166fa3e607eb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tools-for-azure-stack-storage"></a>Narzędzia do magazynu Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Microsoft Azure stosu zapewnia zbiór usług magazynu dla dysków, obiekty BLOB, tabel, kolejek i funkcje zarządzania konta. Zestaw narzędzi usługi Azure Storage można użyć, jeśli chcesz zarządzać lub przenieść dane z usługi Azure Storage stosu lub. Ten artykuł zawiera szybki przegląd dostępne narzędzia.

Narzędzia, który jest najbardziej zależy od wymagań:
* [Narzędzie AzCopy](#azcopy)

    Narzędzie pamięci masowej, wiersza polecenia, które można pobierać na skopiowanie danych z jednego obiektu do innego obiektu w ramach konta magazynu lub między kontami magazynu.

* [Azure PowerShell](#azure-powershell)

    Powłoka opartego na zadaniach, wiersza polecenia i język skryptowy zwłaszcza do administrowania systemem.

* [Interfejs wiersza polecenia platformy Azure](#azure-cli)

    Narzędziem open source, obsługujący wiele platform, które udostępnia zestaw poleceń do pracy z platformy Azure i stosu Azure.

* [Eksplorator usługi Storage firmy Microsoft](#microsoft-azure-storage-explorer)

    Łatwy w użyciu aplikacji autonomicznej z interfejsem użytkownika.

Z powodu różnic usług magazynu platformy Azure i stosu Azure może być niektórych określonych wymagań dotyczących poszczególnych narzędzi opisanych w poniższych sekcjach. Porównanie między Azure stosu magazynu i magazynu Azure, zobacz [usługi Azure Storage stosu: różnice i zagadnienia dotyczące](azure-stack-acs-differences.md).


## <a name="azcopy"></a>Narzędzie AzCopy

Narzędzie AzCopy to narzędzie wiersza polecenia umożliwia kopiowanie danych do i z magazynu obiektów blob i tabeli Microsoft Azure przy użyciu prostych poleceń z optymalną wydajnością. Można skopiować danych z jednego obiektu do drugiego, w ramach konta magazynu lub między kontami magazynu.

### <a name="download-and-install-azcopy"></a>Pobierz i zainstaluj narzędzie AzCopy

Istnieją dwie wersje narzędzia AzCopy: AzCopy w systemach Windows i AzCopy w systemie Linux.

 - **Narzędzie AzCopy w systemie Windows**  
    - Pobierz obsługiwaną wersję programu AzCopy stosu Azure. Można zainstalować i używać narzędzia AzCopy na stosie Azure taki sam sposób jak Azure. Aby dowiedzieć się więcej, zobacz [AzCopy w systemie Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).
        - 1802 aktualizacji lub nowsze wersje [Pobierz narzędzia AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).
        - W poprzednich wersjach [Pobierz narzędzia AzCopy 5.0.0](https://aka.ms/azcopyforazurestack20170417).

 - **Narzędzie AzCopy w systemie Linux**  

    - Narzędzie AzCopy w systemie Linux obsługuje 1802 stosu Azure aktualizacji lub w nowszych wersjach. Można zainstalować i używać narzędzia AzCopy na stosie Azure taki sam sposób jak Azure. Aby dowiedzieć się więcej, zobacz [AzCopy w systemie Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="azcopy-command-examples-for-data-transfer"></a>Przykłady polecenia AzCopy do transferu danych

Typowe scenariusze kopiowania danych do i z obiektów blob Azure stosu przykładów. Aby dowiedzieć się więcej, zobacz [AzCopy w systemie Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) i [AzCopy w systemie Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="download-all-blobs-to-a-local-disk"></a>Pobierz wszystkie obiekty BLOB na dysku lokalnym

**Windows**

````AzCopy  
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
````

**Linux**

````AzCopy  
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
````

### <a name="upload-single-file-to-virtual-directory"></a>Przekaż pojedynczy plik do katalogu wirtualnego

**Windows**

```AzCopy  
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

````AzCopy  
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
````

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Przenoszenie danych między Azure i usługi Azure Storage stosu

Transfer danych asynchronicznych między usługi Azure Storage i Azure stosu nie jest obsługiwany. Należy określić transfer z **/SyncCopy** lub **— kopia synchroniczna** opcji.

**Windows**

````AzCopy  
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
````

**Linux**

````AzCopy  
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
````

### <a name="azcopy-known-issues"></a>Narzędzie Azcopy znane problemy

 - Wszelkie operacje AzCopy na magazyn plików jest niedostępna, ponieważ magazyn plików nie jest jeszcze dostępna w stosie Azure.
 - Transfer danych asynchronicznych między usługi Azure Storage i Azure stosu nie jest obsługiwany. Można określić transfer z **/SyncCopy** opcję, aby skopiować dane.
 - Wersji systemu Linux Azcopy obsługuje tylko 1802 aktualizacji lub nowszy. I nie obsługuje usługi tabel.

## <a name="azure-powershell"></a>Azure PowerShell

Program Azure PowerShell jest moduł, który udostępnia polecenia cmdlet do zarządzania usługami na Azure oraz Azure stosu. Jest powłoka wiersza polecenia, opartemu na zadaniach oraz językiem skryptowym opracowanym specjalnie do administrowania systemem.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Instalowanie i konfigurowanie programu PowerShell dla usługi Azure stosu

Azure stosu zgodne Azure moduły programu PowerShell są wymagane do pracy z stosu Azure. Aby uzyskać więcej informacji, zobacz [Zainstaluj program PowerShell Azure stosu](azure-stack-powershell-install.md) i [konfigurowania środowiska PowerShell użytkownika stosu Azure](azure-stack-powershell-configure-user.md) Aby dowiedzieć się więcej.

### <a name="powershell-sample-script-for-azure-stack"></a>Skrypt programu PowerShell dla stosu Azure 

W tym przykładzie założono, że masz pomyślnie [zainstalowany programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md). Ten skrypt pomoże ukończenie konfiguracji i poproś dzierżawy Azure stosu poświadczenia, aby dodać konto do lokalnej środowiska PowerShell. Następnie skrypt zostanie ustawiona wartość domyślna subskrypcji platformy Azure, Utwórz nowe konto magazynu na platformie Azure utworzyć nowy kontener w tym nowe konto magazynu i przekazać istniejący plik obrazu (blob) do tego kontenera. Po skrypt znajduje się lista wszystkich obiektów blob w tym kontenerze, utworzy nowy katalog docelowy na komputerze lokalnym i pobranie pliku obrazu.

1. Zainstaluj [modułów programu Azure PowerShell platformy Azure zgodnego stosu](azure-stack-powershell-install.md).  
2. Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md).  
3. Otwórz **programu Windows PowerShell ISE** i **Uruchom jako Administrator**, kliknij przycisk **pliku** > **nowy** Aby utworzyć nowy plik skryptu.
4. Skopiuj poniższy skrypt i Wklej do nowego pliku skryptu.
5. Zaktualizuj zmienne skryptu na podstawie własnych ustawień konfiguracji. 
  > ! [Uwaga]  
  > Ten skrypt ma być uruchamiane w katalogu głównym **AzureStack_Tools**. 

```PowerShell 
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>Znane problemy w programie PowerShell 
Bieżąca wersja modułu zgodne programu Azure PowerShell w stosu Azure to pkt 1.2.12. Różni się od najnowszej wersji programu Azure PowerShell. Ta różnica ma wpływ operacji usług magazynu:

* Format wartości zwracanej `Get-AzureRmStorageAccountKey` w wersji pkt 1.2.12 ma dwie właściwości: `Key1` i `Key2`, podczas gdy obecna wersja Azure zwraca tablicę zawierającą wszystkie klucze konta.
   ```
   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```
   Aby uzyskać więcej informacji, zobacz [Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Interfejsu wiersza polecenia Azure to środowisko wiersza polecenia platformy Azure do zarządzania zasobami Azure. Można go zainstalować na macOS, Linux i Windows i uruchom go z poziomu wiersza polecenia. 

Interfejs wiersza polecenia platformy Azure jest zoptymalizowany do zarządzania i administrowania zasobami Azure z poziomu wiersza polecenia, a także tworzenie skryptów automatyzacji, współpracujących z usługą Azure Resource Manager. Udostępnia wiele z tych samych funkcji w portalu Azure stosu, w tym dostęp do zaawansowanych danych.

Stos Azure wymaga wiersza polecenia platformy Azure w wersji 2.0. Aby uzyskać więcej informacji o instalowaniu i konfigurowaniu wiersza polecenia platformy Azure z stosem platformy Azure, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure stosu](azure-stack-version-profiles-azurecli2.md). Aby uzyskać więcej informacji o sposobie używania wykonać kilka czynności, Praca z zasobów na koncie usługi Azure Storage stosu 2.0 interfejsu wiersza polecenia Azure, zobacz [przy użyciu Azure CLI2.0 z usługą Azure Storage](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Azure CLI przykładowy skrypt Azure stosu 
Po zakończeniu interfejsu wiersza polecenia instalacji i konfiguracji, możesz spróbować następujące kroki, aby pracować z powłoki małych przykładowy skrypt do interakcji z zasobami Azure stosu magazynu. Skrypt najpierw tworzy nowy kontener na koncie magazynu, a następnie przekazuje istniejącego pliku (jako obiektu blob) do tego kontenera, zawiera listę wszystkich obiektów blob w kontenerze i na koniec pobierze plik do miejsca docelowego na komputerze lokalnym, który określisz. Przed uruchomieniem tego skryptu, upewnij się, pomyślnie nawiązywać połączeń i zaloguj się do obiektu docelowego stosu Azure. 
1. Otwórz w ulubionym edytorze tekstów, a następnie skopiuj i Wklej powyższy skrypt w edytorze.
2. Zaktualizuj zmienne skryptu, aby odzwierciedlić ustawień konfiguracji. 
3. Po zaktualizowaniu niezbędne zmienne zapisać skrypt, a następnie zamknij Edytor. Następnych krokach założono nazwanego skryptu **my_storage_sample.sh**.
4. Należy oznaczyć skrypt jako plik wykonywalny, w razie potrzeby: `chmod +x my_storage_sample.sh`
5. Uruchom skrypt. Na przykład w Bash: `./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack Storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

Eksplorator magazynu Microsoft Azure jest aplikacją autonomiczną firmy Microsoft. Dzięki temu można łatwo pracować przy użyciu magazynu Azure i Azure stosu magazynu danych w systemie Windows, system macOS i Linux. Jeśli chcesz, aby łatwo zarządzać danymi Azure stosu magazynu, należy rozważyć przy użyciu Eksploratora magazynu Microsoft Azure.

 - Aby dowiedzieć się więcej o konfigurowaniu Eksploratora magazynu Azure do pracy z stosu Azure, zobacz [połączenia Eksploratora usługi Storage z subskrypcją platformy Azure stosu](azure-stack-storage-connect-se.md).
 - Aby dowiedzieć się więcej na temat Eksploratora magazynu Microsoft Azure, zobacz [wprowadzenie do Eksploratora usługi Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>Kolejne kroki
* [Połącz z subskrypcją platformy Azure stosu Eksploratora usługi Storage](azure-stack-storage-connect-se.md)
* [Wprowadzenie do Eksploratora usługi Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Magazyn Azure spójne: różnice i zagadnienia](azure-stack-acs-differences.md)
* [Wprowadzenie do usługi Microsoft Azure Storage](../../storage/common/storage-introduction.md)

