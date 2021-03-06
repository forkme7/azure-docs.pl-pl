---
title: Konfigurowanie środowiska projektowego w systemie Linux | Microsoft Docs
description: Zainstaluj środowisko uruchomieniowe i zestaw SDK oraz utwórz lokalny klaster projektowy w systemie Linux. Po ukończeniu tej konfiguracji wszystko będzie gotowe do tworzenia aplikacji.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: bf88e4c702321a7810ec6a3e50eb6cd47a788734
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="prepare-your-development-environment-on-linux"></a>Przygotowywanie środowiska projektowego w systemie Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Aby wdrażać i uruchamiać [aplikacje usługi Azure Service Fabric](service-fabric-application-model.md) na maszynie deweloperskiej z systemem Linux, należy zainstalować środowisko uruchomieniowe i wspólny zestaw SDK. Można także zainstalować opcjonalne zestawy SDK na potrzeby programowania na platformach Java i .NET Core. 

Na potrzeby kroków opisanych w tym artykule założono, że instalujesz natywnie w systemie Linux lub korzystasz z obrazu kontenera OneBox usługi Service Fabric, `microsoft/service-fabric-onebox`. 

Instalowanie środowiska uruchomieniowego usługi Service Fabric i zestawu SDK w podsystemie systemu Windows dla systemu Linux nie jest obsługiwane. Obsługiwany jest jednak interfejs wiersza polecenia usługi Azure Service Fabric, który umożliwia zarządzanie jednostkami usługi Service Fabric hostowanymi w dowolnej lokalizacji w chmurze lub środowisku lokalnym. Aby uzyskać informacje na temat instalowania interfejsu wiersza polecenia, zobacz [Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric](./service-fabric-cli.md).


## <a name="prerequisites"></a>Wymagania wstępne

* Na potrzeby tworzenia aplikacji obsługiwane są następujące wersje systemu operacyjnego:

    * Ubuntu 16.04 (`Xenial Xerus`)

      * Upewnij się, że pakiet `apt-transport-https` został zainstalowany:
         
         ```bash
         sudo apt-get install apt-transport-https
         ```
    * Red Hat Enterprise Linux 7.4 (obsługa wersji zapoznawczej usługi Service Fabric)


## <a name="installation-methods"></a>Metody instalacji

### <a name="1-script-installation-ubuntu"></a>1. Instalacja skryptu (Ubuntu)

Skrypt jest dostarczany jako udogodnienie dotyczące instalowania środowiska uruchomieniowego usługi Service Fabric i wspólnego zestawu SDK usługi Service Fabric wraz z interfejsem wiersza polecenia **sfctl**. Wykonaj kroki instalacji ręcznej w następnej sekcji, aby określić aktualnie instalowane elementy i uzgadniane licencje. Podczas uruchamiania skryptu jest przyjmowane założenie, że wyrażasz zgodę na warunki licencji wszystkich instalowanych programów. 

Po pomyślnym wykonaniu skryptu możesz bezpośrednio przejść do części [Konfigurowanie klastra lokalnego](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="2-manual-installation"></a>2. Instalacja ręczna
Aby przeprowadzić ręczną instalację środowiska uruchomieniowego usługi Service Fabric i wspólnego zestawu SDK, wykonaj kroki z dalszej części tego przewodnika.

## <a name="update-your-apt-sourcesyum-repositories"></a>Aktualizowanie źródeł APT/repozytoriów Yum
Aby zainstalować zestaw SDK i skojarzony pakiet środowiska uruchomieniowego przy użyciu narzędzia wiersza polecenia apt-get, należy najpierw zaktualizować źródła APT (Advanced Packaging Tool).

### <a name="ubuntu"></a>Ubuntu

1. Otwórz terminal.
2. Dodaj repozytorium usługi Service Fabric do listy źródeł.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Dodaj repozytorium `dotnet` do listy źródeł.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Dodaj nowy klucz Gnu Privacy Guard (GnuPG lub GPG) do swojego pęku kluczy APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Dodaj oficjalny klucz GPG platformy Docker do pęku kluczy APT.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Skonfiguruj repozytorium platformy Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Odśwież listę pakietów na podstawie nowo dodanych repozytoriów.

    ```bash
    sudo apt-get update
    ```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (obsługa wersji zapoznawczej usługi Service Fabric)

1. Otwórz terminal.
2. Pobierz i zainstaluj dodatkowe pakiety dla systemu Enterprise Linux (EPEL).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Dodaj do systemu repozytorium pakietów EfficiOS RHEL7.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Zaimportuj klucz podpisywania pakietu efficios do lokalnego pęku kluczy GPG.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Dodaj do systemu repozytorium Microsoft RHEL.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

6. Zainstaluj zestaw SDK platformy dotnet.

    ```bash
    yum install rh-dotnet20 -y
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-local-cluster-setup"></a>Instalowanie i konfigurowanie zestawu Service Fabric SDK dla instalacji klastra lokalnego

Po zaktualizowaniu źródeł można zainstalować zestaw SDK. Zainstaluj pakiet zestawu SDK usług Service Fabric, potwierdź instalację i zaakceptuj umowę licencyjną.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Poniższe polecenia pozwalają zautomatyzować akceptowanie postanowień licencji dla pakietów usługi Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (obsługa wersji zapoznawczej usługi Service Fabric)

```bash
sudo yum install servicefabricsdkcommon
```

Środowisko uruchomieniowe usługi Service Fabric, które uwzględnia powyższą instalację, obejmuje pakiety przedstawione w poniższej tabeli. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | OpenJDK 1.8 | Niejawne z rozwiązania npm | najnowsza |
RHEL | - | OpenJDK 1.8 | Niejawne z rozwiązania npm | najnowsza |

## <a name="set-up-a-local-cluster"></a>Tworzenie klastra lokalnego
  Po ukończeniu instalacji powinno być możliwe uruchomienie klastra lokalnego.

  1. Uruchom skrypt instalacji klastra.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Otwórz przeglądarkę internetową i przejdź do narzędzia [Service Fabric Explorer](http://localhost:19080/Explorer) (`http://localhost:19080/Explorer`). Jeśli klaster został uruchomiony, powinien być widoczny pulpit nawigacyjny narzędzia Service Fabric Explorer. Może upłynąć kilka minut, zanim klaster zostanie całkowicie skonfigurowany. Jeśli przeglądarka nie może otworzyć adresu URL lub narzędzie Service Fabric Explorer nie wyświetla informacji o gotowości systemu, zaczekaj kilka minut i spróbuj ponownie.

      ![Narzędzie Service Fabric Explorer w systemie Linux][sfx-linux]

  W tym momencie możliwe jest wdrożenie wstępnie skompilowanych pakietów aplikacji usługi Service Fabric lub nowych pakietów opartych na kontenerach gościa bądź plikach wykonywalnych gościa. Aby tworzyć nowe usługi przy użyciu zestawów SDK platformy Java lub .NET Core, wykonaj opcjonalne kroki instalacji opisane w kolejnych sekcjach.


  > [!NOTE]
  > Klastry autonomiczne nie są obsługiwane w systemie Linux.
  >


>   [!TIP]
    Jeśli dysponujesz dyskiem SSD, zalecane jest przekazanie ścieżki folderu SSD przy użyciu pliku devclustersetup.sh z parametrem `--clusterdataroot` w celu uzyskania najwyższej wydajności.

## <a name="set-up-the-service-fabric-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric

[Interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md) zawiera polecenia służące do interakcji z jednostkami usługi Service Fabric, w tym klastrami i aplikacjami.
Aby zainstalować interfejs wiersza polecenia, postępuj zgodnie z instrukcjami w artykule [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md).


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Konfigurowanie generatorów usługi Yeoman na potrzeby kontenerów i plików wykonywalnych gościa
Usługa Service Fabric udostępnia narzędzia do tworzenia szkieletów, które ułatwiają tworzenie aplikacji usługi Service Fabric z poziomu terminalu przy użyciu generatora szablonów narzędzia Yeoman. Wykonaj poniższe kroki, aby skonfigurować generatory szablonów narzędzia Yeoman w usłudze Service Fabric:

1. Zainstaluj środowisko NodeJs i menedżera NPM na swojej maszynie

Ubuntu
  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```

Red Hat Enterprise Linux 7.4 (obsługa wersji zapoznawczej usługi Service Fabric)
  ```bash
  sudo yum install nodejs
  sudo yum install npm
  ```
2. Zainstaluj generator szablonów [narzędzia Yeoman](http://yeoman.io/) na swoim komputerze z poziomu narzędzia NPM

  ```bash
  sudo npm install -g yo
  ```
3. Zainstaluj generator kontenerów Yeo i generator plików wykonywalnych gościa usługi Service Fabric z poziomu narzędzia NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

Po zainstalowaniu generatorów powinno być możliwe tworzenie usług kontenerów lub plików wykonywalnych gościa przez uruchomienie odpowiednio polecenia `yo azuresfguest` lub `yo azuresfcontainer`.

## <a name="set-up-net-core-20-development"></a>Konfigurowanie tworzenia aplikacji w programie .NET Core 2.0

Zainstaluj [zestaw .NET Core 2.0 SDK dla systemu Ubuntu](https://www.microsoft.com/net/core#linuxubuntu), aby rozpocząć [tworzenie aplikacji usługi Service Fabric w języku C#](service-fabric-create-your-first-linux-application-with-csharp.md). Pakiety dla aplikacji .NET Core 2.0 usługi Service Fabric są hostowane w witrynie NuGet.org, obecnie w wersji zapoznawczej.

## <a name="set-up-java-development"></a>Konfigurowanie środowiska programowania w języku Java

W celu kompilowania usług Service Fabric za pomocą języka Java zainstaluj rozwiązania JDK 1.8 i Gradle umożliwiające uruchamianie zadań kompilacji. Poniższy fragment kodu instaluje zestaw Open JDK 1.8 wraz z narzędziem Gradle. Biblioteki Java usługi Service Fabric są ściągane z narzędzia Maven.


Ubuntu 
 ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

Red Hat Enterprise Linux 7.4 (obsługa wersji zapoznawczej usługi Service Fabric)
  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Instalowanie wtyczki środowiska Eclipse (opcjonalnie)

Wtyczkę środowiska Eclipse dla usługi Service Fabric można zainstalować z poziomu środowiska Eclipse IDE for Java Developers lub Java EE Developers. Za pomocą środowiska Eclipse można tworzyć aplikacje wykonywalne gościa i aplikacje kontenera usługi Service Fabric, a także aplikacje Java usługi Service Fabric.

> [!IMPORTANT]
> Wtyczka usługi Service Fabric wymaga środowiska Eclipse Neon lub nowszej wersji. Aby sprawdzić wersję środowiska Eclipse, zobacz poniższe instrukcje. Jeśli masz zainstalowaną starszą wersję środowiska Eclipse, możesz pobrać nowsze wersje z [witryny środowiska Eclipse](https://www.eclipse.org). Nie zaleca się instalowania nowej wersji na istniejącej instalacji środowiska Eclipse. Można usunąć starszą wersję przed uruchomieniem instalatora lub zainstalować nowszą wersję w innym katalogu. 
> 
> W systemie Ubuntu zaleca się instalowanie bezpośrednio z witryny środowiska Eclipse, a nie za pomocą instalatora pakietu (`apt` lub `apt-get`). Gwarantuje to uzyskanie najnowszej wersji środowiska Eclipse. Można zainstalować środowisko Eclipse IDE for Java Developers lub Java EE Developers.

1. W środowisku Eclipse upewnij się, że masz zainstalowaną wersję Eclipse Neon lub nowszą oraz zestaw Buildship 2.2.1 lub nowszy. Wersje zainstalowanych składników można sprawdzić, wybierając kolejno pozycje **Help** > **About Eclipse** > **Installation Details** (Pomoc > Informacje o środowisku Eclipse > Szczegóły instalacji). Zestaw Buildship można zaktualizować zgodnie z instrukcjami podanymi w artykule [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: wtyczka Eclipse dla narzędzia Gradle).

2. Aby zainstalować wtyczkę usługi Service Fabric, wybierz pozycję **Help** > **Install New Software** (Pomoc > Zainstaluj nowe oprogramowanie).

3. W polu **Work with** (Praca z) wpisz adres **http://dl.microsoft.com/eclipse**.

4. Kliknij pozycję **Add** (Dodaj).

    ![Strona dostępnego oprogramowania][sf-eclipse-plugin]

5. Wybierz wtyczkę usługi **Service Fabric**, a następnie kliknij przycisk **Next** (Dalej).

6. Wykonaj kroki instalacji, a następnie zaakceptuj postanowienia licencyjne.

Jeśli wtyczka usługi Service Fabric Eclipse jest już zainstalowana, upewnij się, że używasz najnowszej wersji. W tym celu wybierz kolejno pozycje **Help** > **About Eclipse** > **Installation Details** (Pomoc > Informacje o środowisku Eclipse > Szczegóły instalacji) i wyszukaj pozycję Service Fabric na liście zainstalowanych wtyczek. Wybierz opcję **Aktualizacja**, jeśli jest dostępna nowsza wersja.

Aby uzyskać więcej informacji, zobacz artykuł [Wtyczka usługi Service Fabric na potrzeby tworzenia aplikacji Java w środowisku Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Aktualizowanie zestawu SDK i środowiska uruchomieniowego

Aby zaktualizować zestaw SDK i środowisko uruchomieniowe do najnowszej wersji, uruchom następujące polecenia:

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
W celu zaktualizowania plików binarnych zestawu SDK Java z poziomu narzędzia Maven musisz zaktualizować szczegóły wersji odpowiedniego pliku binarnego w pliku ``build.gradle`` w taki sposób, aby wskazywał najnowszą wersję. Aby dowiedzieć się, w którym dokładnie miejscu należy zaktualizować wersję, możesz skorzystać z dowolnego pliku ``build.gradle`` w przykładach ułatwiających rozpoczęcie pracy dla usługi Service Fabric dostępnych [tutaj](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Zaktualizowanie pakietów może spowodować zatrzymanie lokalnego klastra projektowego. Po uaktualnieniu uruchom ponownie klaster lokalny, postępując zgodnie z instrukcjami opisanymi na tej stronie.

## <a name="remove-the-sdk"></a>Usuwanie zestawu SDK
Aby usunąć zestawy Service Fabric SDK, uruchom następujące polecenie:

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (obsługa wersji zapoznawczej usługi Service Fabric)

```bash
sudo yum remote servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
```

## <a name="next-steps"></a>Następne kroki

* [Create and deploy your first Service Fabric Java application on Linux using Yeoman](service-fabric-create-your-first-linux-application-with-java.md) (Tworzenie i wdrażanie pierwszej aplikacji Java usługi Service Fabric w systemie Linux przy użyciu programu Yeoman)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse](service-fabric-get-started-eclipse.md) (Tworzenie i wdrażanie pierwszej aplikacji Java usługi Service Fabric w systemie Linux przy użyciu wtyczki usługi Service Fabric dla środowiska Eclipse)
* [Create your first CSharp application on Linux](service-fabric-create-your-first-linux-application-with-csharp.md) (Tworzenie pierwszej aplikacji CSharp w systemie Linux)
* [Przygotowywanie środowiska projektowego w systemie OSX](service-fabric-get-started-mac.md)
* [Przygotowywanie środowiska projektowego systemu Linux w systemie Windows](service-fabric-local-linux-cluster-windows.md)
* [Use the Service Fabric CLI to manage your applications](service-fabric-application-lifecycle-sfctl.md) (Zarządzanie aplikacjami przy użyciu interfejsu wiersza polecenia usługi Service Fabric)
* [Różnice w usłudze Service Fabric w systemie Windows i Linux](service-fabric-linux-windows-differences.md)
* [Automate operating system patching on your Linux cluster (Automatyzowanie stosowania poprawek do systemu operacyjnego w klastrze z systemem Linux)](service-fabric-patch-orchestration-application-linux.md)
* [Get started with Service Fabric CLI](service-fabric-cli.md) (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
