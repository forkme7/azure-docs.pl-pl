---
title: Tworzenie klastra usługi Service Fabric z systemem Windows na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wdrożyć klaster usługi Service Fabric z systemem Windows w sieci wirtualnej platformy Azure oraz w sieciowej grupie zabezpieczeń za pomocą programu PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: b85c3030984758a0c425fb526a32fcd8eff974c9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Samouczek: wdrażanie klastra usługi Service Fabric z systemem Windows w sieci wirtualnej platformy Azure
Niniejszy samouczek jest pierwszą częścią serii. Dowiesz się w nim, jak wdrożyć klaster usługi Service Fabric, na którym działa system Windows w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) i [sieciowej grupie zabezpieczeń](../virtual-network/virtual-networks-nsg.md) za pomocą programu PowerShell i szablonu. Po wykonaniu tych czynności powstanie działający w chmurze klaster, w którym można będzie wdrażać aplikacje.  Aby utworzyć klaster z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie bezpiecznego klastra z systemem Linux na platformie Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

W tym samouczku opisano scenariusz produkcyjny.  Jeśli chcesz szybko utworzyć niewielki klaster do celów testowych, zobacz [Tworzenie klastra testowego z trzema węzłami](./scripts/service-fabric-powershell-create-test-cluster.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej na platformie Azure przy użyciu programu PowerShell
> * Tworzenie magazynu kluczy i przekazywanie certyfikatu
> * Tworzenie bezpiecznego klastra usługi Service Fabric w programie Azure PowerShell
> * Zabezpieczanie klastra za pomocą certyfikatu X.509
> * Nawiązywanie połączenia z klastrem przy użyciu programu PowerShell
> * Usuwanie klastra

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego klastra na platformie Azure
> * [Skalowanie klastra na zewnątrz lub do wewnątrz](service-fabric-tutorial-scale-cluster.md)
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * [Wdrażanie usługi API Management z usługą Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka:
- Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Zainstaluj [zestaw SDK usługi Service Fabric i moduł programu PowerShell](service-fabric-get-started.md)
- Zainstaluj [moduł Azure PowerShell w wersji 4.1 lub nowszej](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Poniższe procedury umożliwiają utworzenie klastra usługi Service Fabric z pięcioma węzłami. Aby obliczyć koszt działania klastra usługi Service Fabric na platformie Azure, skorzystaj z [Kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Kluczowe pojęcia
[Klaster usługi Service Fabric](service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym wdraża się mikrousługi i nimi zarządza. Klastry mogą obejmować nawet tysiące maszyn. Maszyna lub maszyna wirtualna, która jest częścią klastra, jest nazywana węzłem. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają swoje właściwości — na przykład właściwości dotyczące umieszczania.

Typ węzła definiuje rozmiar, liczbę i właściwości zestawu maszyn wirtualnych w klastrze. Każdy zdefiniowany typ węzła jest konfigurowany jako [zestaw skalowania maszyn wirtualnych](/azure/virtual-machine-scale-sets/), czyli zasób obliczeniowy platformy Azure używany do wdrażania kolekcji maszyn wirtualnych i zarządzania nimi. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Typy węzłów służą do definiowania ról zestawu węzłów klastra, takich jak „fronton” lub „zaplecze”.  Klaster może mieć więcej niż jeden typ węzła, ale podstawowy typ węzła musi obejmować co najmniej pięć maszyn wirtualnych w przypadku klastrów produkcyjnych (lub co najmniej trzy maszyny wirtualne w przypadku klastrów testowych).  [Usługi systemowe Service Fabric](service-fabric-technical-overview.md#system-services) są umieszczane w węzłach podstawowego typu.

Klaster jest zabezpieczony przy użyciu certyfikatu klastra. Certyfikat klastra to certyfikat X.509 używany do zabezpieczania komunikacji między węzłami i uwierzytelniania punktów końcowych zarządzania klastrem na kliencie zarządzania.  Certyfikat klastra zapewnia również protokół SSL dla interfejsu API zarządzania protokołu HTTPS i narzędzia Service Fabric Explorer za pośrednictwem protokołu HTTPS. Certyfikaty z podpisem własnym są przydatne w przypadku klastrów testowych.  W przypadku klastrów produkcyjnych jako certyfikatu klastra należy używać certyfikatu z urzędu certyfikacji.

Certyfikat klastra musi spełniać następujące wymagania:

- Zawiera klucz prywatny.
- Został utworzony na potrzeby wymiany kluczy, co umożliwia eksport do pliku wymiany informacji osobistych (pfx).
- Ma nazwę podmiotu zgodną z domeną używaną w celu uzyskiwania dostępu do klastra usługi Service Fabric. To dopasowanie jest wymagane, aby można było zapewnić protokół SSL dla punktów końcowych zarządzania protokołu HTTPS klastra i dla narzędzia Service Fabric Explorer. Nie można uzyskać certyfikatu SSL od urzędu certyfikacji dla domeny .cloudapp.azure.com. Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla danego klastra.

Usługa Azure Key Vault służy do zarządzania certyfikatami klastrów usługi Service Fabric na platformie Azure.  Po wdrożeniu klastra na platformie Azure dostawca zasobów platformy Azure odpowiedzialny za utworzenie klastrów usługi Service Fabric pobiera certyfikaty z usługi Key Vault i instaluje je w klastrze maszyn wirtualnych.

W tym samouczku przedstawiono wdrożenie klastra z pięcioma węzłami o tym samym typie. Ważnym etapem wdrożeń produkcyjnych jest [planowanie pojemności](service-fabric-cluster-capacity.md). Poniżej przedstawiono niektóre zagadnienia, które należy uwzględnić w ramach tego procesu.

- Liczba węzłów oraz typy węzłów wymagane dla danego klastra 
- Właściwości poszczególnych typów węzłów (np. rozmiar, typ podstawowy, połączenie z Internetem i liczba maszyn wirtualnych)
- Charakterystyka niezawodności i trwałości klastra

## <a name="download-and-explore-the-template"></a>Pobieranie i eksplorowanie szablonu
Pobierz poniższe pliki szablonu usługi Resource Manager:
- [vnet-cluster.json][template]
- [vnet-cluster.parameters.json][parameters]

Plik [vnet-cluster.json][template] umożliwia wdrożenie szeregu zasobów, w tym następujących. 

### <a name="service-fabric-cluster"></a>Klaster usługi Service Fabric
Klaster z systemem Windows jest wdrażany z następującą charakterystyką:
- Jeden typ węzła 
- Pięć węzłów o podstawowym typie (z możliwością konfiguracji za pomocą parametrów szablonu)
- System operacyjny: Windows Server 2016 Datacenter z kontenerami (z możliwością konfiguracji za pomocą parametrów szablonu)
- Zabezpieczenie przy użyciu certyfikatu (z możliwością konfiguracji za pomocą parametrów szablonu)
- [Odwrotny serwer proxy](service-fabric-reverseproxy.md) jest włączony
- [Usługa DNS](service-fabric-dnsservice.md) jest włączona
- [Poziom trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster): Brązowy (z możliwością konfiguracji za pomocą parametrów szablonu)
- [Poziom niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster): Srebrny (z możliwością konfiguracji za pomocą parametrów szablonu)
- Punkt końcowy połączenia klienta: 19000 (z możliwością konfiguracji za pomocą parametrów szablonu)
- Punkt końcowy bramy protokołu HTTP: 19080 (z możliwością konfiguracji za pomocą parametrów szablonu)

### <a name="azure-load-balancer"></a>Moduł równoważenia obciążenia platformy Azure
Wdrażany jest moduł równoważenia obciążenia i konfigurowane są sondy oraz reguły dla następujących portów:
- Punkt końcowy połączenia klienta: 19000
- Punkt końcowy bramy protokołu HTTP: 19080 
- Port aplikacji: 80
- Port aplikacji: 443
- Odwrotny serwer proxy usługi Service Fabric: 19081

Jeśli będą potrzebne dowolne inne porty aplikacji, będzie trzeba dostosować zasoby Microsoft.Network/loadBalancers i Microsoft.Network/networkSecurityGroups, aby umożliwić ruch przychodzący.

### <a name="virtual-network-subnet-and-network-security-group"></a>Sieć wirtualna, podsieć i sieciowa grupa zabezpieczeń
Nazwy sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń są deklarowane w parametrach szablonu.  Przestrzenie adresowe sieci wirtualnej i podsieci również są deklarowane w parametrach szablonu:
- Przestrzeń adresowa sieci wirtualnej: 172.16.0.0/20
- Przestrzeń adresowa podsieci usługi Service Fabric: 172.16.2.0/23

W sieciowej grupie zabezpieczeń są włączane poniższe reguły ruchu przychodzącego. Wartości portów można zmienić, modyfikując zmienne szablonu.
- ClientConnectionEndpoint (TCP): 19000
- HttpGatewayEndpoint (HTTP/TCP): 19080
- SMB: 445
- Internodecommunication: 1025, 1026, 1027
- Zakres portów efemerycznych: od 49152 do 65534 (wymaganych jest co najmniej 256 portów)
- Porty do użytku aplikacji: 80 i 443
- Zakres portów aplikacji: od 49152 do 65534 (używane do komunikacji między usługami, ale nie są otwarte w przypadku modułu równoważenia obciążenia)
- Wszystkie pozostałe porty są zablokowane

Jeśli będą potrzebne dowolne inne porty aplikacji, będzie trzeba dostosować zasoby Microsoft.Network/loadBalancers i Microsoft.Network/networkSecurityGroups, aby umożliwić ruch przychodzący.

## <a name="set-template-parameters"></a>Ustawianie parametrów szablonu
Plik parametrów [vnet-cluster.parameters.json][parameters] deklaruje wiele wartości służących do wdrażania klastra i skojarzonych zasobów. Niektóre parametry, które być może będzie trzeba zmodyfikować na potrzeby danego wdrożenia:

|Parametr|Przykładowa wartość|Uwagi|
|---|---||
|adminUserName|vmadmin| Nazwa użytkownika będącego administratorem maszyn wirtualnych klastra. |
|adminPassword|Haslo#1234| Hasło administratora maszyn wirtualnych klastra.|
|clusterName|mojklastersf123| Nazwa klastra. |
|location|southcentralus| Lokalizacja klastra. |
|certificateThumbprint|| <p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź wartość odcisku palca certyfikatu. Na przykład „6190390162C988701DB5676EB81083EA608DCCF3”</p>. | 
|certificateUrlValue|| <p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta. </p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź adres URL certyfikatu. Na przykład „https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346”.</p>|
|sourceVaultValue||<p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź wartość magazynu źródłowego. Na przykład „/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Wdrażanie sieci wirtualnej i klastra
Następnym etapem jest skonfigurowanie topologii sieci i wdrożenie klastra usługi Service Fabric. Szablon [vnet-cluster.json][template] usługi Resource Manager umożliwia utworzenie sieci wirtualnej, podsieci oraz sieciowej grupy zabezpieczeń dla usługi Service Fabric. Szablon pozwala również wdrożyć klaster z włączonymi zabezpieczeniami opartymi na certyfikacie.  W przypadku klastrów produkcyjnych jako certyfikatu klastra należy używać certyfikatu z urzędu certyfikacji. Do zabezpieczenia klastrów testowych może służyć certyfikat z podpisem własnym.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Tworzenie klastra przy użyciu istniejącego certyfikatu
Poniższy skrypt wdraża nowy klaster na platformie Azure za pomocą polecenia cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) i szablonu. Polecenie cmdlet powoduje także utworzenie nowego magazynu kluczy na platformie Azure i przekazanie danego certyfikatu. 

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-cluster.json" `
-ParameterFile "$templatepath\vnet-cluster.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Tworzenie klastra przy użyciu nowego certyfikatu z podpisem własnym
Poniższy skrypt wdraża nowy klaster na platformie Azure za pomocą polecenia cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) i szablonu. Polecenie cmdlet tworzy również nowy magazyn kluczy na platformie Azure, dodaje nowy certyfikat z podpisem własnym do magazynu kluczy i pobiera plik certyfikatu do środowiska lokalnego. 

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-cluster.json" `
-ParameterFile "$templatepath\vnet-cluster.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem
Nawiąż połączenie z klastrem za pomocą modułu programu PowerShell dla usługi Service Fabric instalowanego wraz z zestawem SDK usługi Service Fabric.  Najpierw zainstaluj certyfikat w magazynie osobistym (Mój) bieżącego użytkownika na komputerze.  Uruchom następujące polecenie programu PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Teraz możesz przystąpić do nawiązywania połączenia z zabezpieczonym klastrem.

Moduł programu PowerShell dla usługi **Service Fabric** udostępnia wiele poleceń cmdlet służących do zarządzania usługami, aplikacjami i klastrami usługi Service Fabric.  Za pomocą polecenia cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) nawiąż połączenie z zabezpieczonym klastrem. Szczegóły dotyczące punktu końcowego połączenia i odcisku palca certyfikatu można znaleźć w danych wyjściowych poprzedniego kroku.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Za pomocą polecenia cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) sprawdź, czy połączenie zostało nawiązane oraz czy klaster jest w dobrej kondycji.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
W pozostałych artykułach z tej serii samouczków jest używany utworzony klaster. Jeśli nie chcesz od razu przejść do następnego artykułu, rozważ usunięcie klastra i magazynu kluczy, aby uniknąć naliczania opłat. Najprostszym sposobem na usunięcie klastra i wszystkich wykorzystywanych przez niego zasobów jest usunięcie grupy zasobów.

Usuń grupę zasobów i wszystkie zasoby klastra, korzystając z polecenia cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).  Usuń również grupę zasobów zawierającą magazyn kluczy.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej na platformie Azure przy użyciu programu PowerShell
> * Tworzenie magazynu kluczy i przekazywanie certyfikatu
> * Tworzenie bezpiecznego klastra usługi Service Fabric na platformie Azure przy użyciu programu PowerShell
> * Zabezpieczanie klastra za pomocą certyfikatu X.509
> * Nawiązywanie połączenia z klastrem przy użyciu programu PowerShell
> * Usuwanie klastra

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak skalować klaster.
> [!div class="nextstepaction"]
> [Skalowanie klastra](service-fabric-tutorial-scale-cluster.md)


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.parameters.json
