---
title: Wdrażanie aplikacji Spring Boot w usłudze Azure Service Fabric | Microsoft Docs
description: W tym przewodniku Szybki start wdrożysz aplikację Spring Boot dla usługi Azure Service Fabric, korzystając z przykładowej aplikacji Spring Boot.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: e41a7754e6e170dda7818bceadab7858a9d9fa76
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-azure"></a>Szybki start: wdrażanie aplikacji Java Spring Boot na platformie Azure
Usługa Azure Service Fabric to platforma systemów rozproszonych umożliwiająca wdrażanie mikrousług i kontenerów, a także zarządzanie nimi. 

Ten przewodnik Szybki start przedstawia sposób wdrażania aplikacji Spring Boot na platformie Service Fabric. W tym przewodniku Szybki start użyto przykładu [Wprowadzenie](https://spring.io/guides/gs/spring-boot/) z witryny internetowej platformy Spring. W tym przewodniku Szybki start objaśniono wdrażanie przykładu ze środowiska Spring Boot jako aplikacji usługi Service Fabric za pomocą znanych narzędzi wiersza polecenia. Po zakończeniu w usłudze Service Fabric będzie działać przykład Wprowadzenie środowiska Spring Boot. 

![Zrzut ekranu aplikacji](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Wdrażanie aplikacji Spring Boot w usłudze Service Fabric
* Wdrażanie aplikacji w klastrze lokalnym 
* Wdrażanie aplikacji w klastrze na platformie Azure
* Skalowanie aplikacji w poziomie na wiele węzłów
* Przenoszenie usługi w tryb failover bez wywierania wpływu na jej dostępność

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki start:
1. [Zainstaluj zestaw SDK usługi Service Fabric i interfejs wiersza polecenia (CLI) usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
3. [Zainstaluj narzędzie Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [Skonfiguruj środowisko Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Pobierz przykład
W oknie terminala uruchom następujące polecenie, aby sklonować przykładową aplikację Wprowadzenie środowiska Spring Boot na komputer lokalny.
```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Tworzenie pakietu aplikacji Spring Boot 
1. W sklonowanym katalogu `gs-spring-boot` uruchom polecenie `yo azuresfguest`. 

2. Wprowadź następujące szczegóły dla każdego monitu. 

    ![Wpisy narzędzia Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. W folderze `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` utwórz plik o nazwie `entryPoint.sh`. Dodaj następującą zawartość do pliku. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

Na tym etapie utworzono aplikację usługi Service Fabric dla przykładu Wprowadzenie środowiska Spring Boot, którą można wdrożyć w usłudze Service Fabric.

## <a name="run-the-application-locally"></a>Uruchamianie aplikacji lokalnie
1. Uruchom klaster lokalny, uruchamiając następujące polecenie:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Uruchamianie klastra lokalnego zajmuje nieco czasu. Aby potwierdzić, że klaster jest w pełni uruchomiony, otwórz narzędzie Service Fabric Explorer dostępne pod adresem **http://localhost:19080**. Pięć węzłów w dobrej kondycji oznacza, że klaster lokalny jest uruchomiony. 
    
    ![Klaster lokalny w dobrej kondycji](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. Przejdź do folderu `gs-spring-boot/SpringServiceFabric`.
3. Uruchom następujące polecenie, aby połączyć się z klastrem lokalnym. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Uruchom skrypt `install.sh`. 

    ```bash
    ./install.sh
    ```

5. Uruchom przeglądarkę internetową i uzyskaj dostęp do aplikacji, przechodząc do adresu **http://localhost:8080**. 

    ![Fronton aplikacji — lokalny](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
Teraz możesz uzyskiwać dostęp do aplikacji Spring Boot, która została wdrożona w klastrze usługi Service Fabric.  

## <a name="deploy-the-application-to-azure"></a>Wdrażanie aplikacji na platformie Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Konfigurowanie klastra usługi Azure Service Fabric
Aby wdrożyć aplikację w klastrze na platformie Azure, utwórz własny klaster.

Klastry testowe to bezpłatne, ograniczone czasowo klastry usługi Service Fabric hostowane na platformie Azure i obsługiwane przez zespół usługi Service Fabric. Klastry testowe pozwalają wdrażać aplikacje i uzyskiwać informacje o platformie. Klaster używa jednego certyfikatu z podpisem własnym na potrzeby zabezpieczeń między węzłami oraz między klientem a węzłem.

Zaloguj się i dołącz do [klastra systemu Linux](http://aka.ms/tryservicefabric). Pobierz certyfikat PFX na komputer, klikając link **PFX**. Kliknij link **ReadMe**, aby znaleźć hasło certyfikatu i instrukcje dotyczące konfigurowania różnych środowisk do użycia certyfikatu. Pozostaw otwarte strony **Powitalna** i **ReadMe**, ponieważ będziesz korzystać z niektórych instrukcji w poniższej procedurze. 

> [!Note]
> Liczba klastrów testowych dostępnych na godzinę jest ograniczona. Jeśli wystąpi błąd podczas próby tworzenia konta umożliwiającego korzystanie z klastra testowego, możesz poczekać, a następnie spróbować ponownie. Możesz też wykonać kroki opisane w artykule [Create a Service Fabric cluster on Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) (Tworzenie klastra usługi Service Fabric na platformie Azure), aby utworzyć klaster usługi w swojej subskrypcji. 
>
> Usługa Spring Boot została skonfigurowana do nasłuchiwania ruchu przychodzącego na porcie 8080. Upewnij się, że port w klastrze został otwarty. Jeśli używasz klastra testowego, ten port jest otwarty.
>

Usługa Service Fabric udostępnia kilka narzędzi, których możesz używać do zarządzania klastrem i jego aplikacjami:

- Service Fabric Explorer — narzędzie oparte na przeglądarce.
- Interfejs wiersza polecenia (CLI) usługi Service Fabric, który korzysta z interfejsu wiersza polecenia platformy Azure 2.0.
- Polecenia programu PowerShell. 

W tym przewodniku Szybki start jest używany interfejs wiersza polecenia usługi Service Fabric i narzędzie Service Fabric Explorer. 

Aby korzystać z interfejsu wiersza polecenia, musisz utworzyć plik PEM na podstawie pobranego pliku PFX. Aby przekonwertować plik, użyj następującego polecenia. (W przypadku klastrów testowych możesz skopiować polecenie specyficzne dla Twojego pliku PFX z instrukcji na stronie **ReadMe**).

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

Aby użyć narzędzia Service Fabric Explorer, musisz zaimportować plik PFX certyfikatu pobrany z witryny internetowej klastra testowego do swojego magazynu certyfikatów (w systemie Windows lub Mac) lub do samej przeglądarki (w systemie Ubuntu). Będzie potrzebne hasło klucza prywatnego PFX, które możesz pobrać ze strony **ReadMe**.

Aby zaimportować certyfikat w swoim systemie, użyj dowolnej, preferowanej przez siebie metody. Na przykład:

- W systemie Windows: kliknij dwukrotnie plik PFX i postępuj zgodnie z monitami, aby zainstalować certyfikat w magazynie osobistym `Certificates - Current User\Personal\Certificates`. Alternatywnie możesz użyć polecenia programu PowerShell podanego w instrukcjach na stronie **ReadMe**.
- Na komputerach Mac: kliknij dwukrotnie plik PFX, a następnie postępuj zgodnie z monitami, aby zainstalować certyfikat w pęku kluczy.
- W systemie Ubuntu: domyślną przeglądarką w systemie Ubuntu 16.04 jest Mozilla Firefox. Aby zaimportować certyfikat w przeglądarce Firefox, kliknij przycisk menu w prawym górnym rogu przeglądarki, a następnie kliknij pozycję **Opcje**. Na stronie **Preferencje** użyj pola wyszukiwania, aby wyszukać ciąg „certyfikaty”. Kliknij przycisk **Wyświetl certyfikaty**, wybierz kartę **Twoje certyfikaty**, kliknij pozycję **Importuj** i postępuj zgodnie z monitami, aby zaimportować certyfikat.
 
   ![Instalowanie certyfikatu w programie Firefox](./media/service-fabric-quickstart-java-spring-boot/install-cert-firefox.png) 


### <a name="deploy-the-application-using-cli"></a>Wdrażanie aplikacji przy użyciu interfejsu wiersza polecenia
Kiedy aplikacja i klaster są gotowe, możesz wdrożyć aplikację w klastrze bezpośrednio z wiersza polecenia.

1. Przejdź do folderu `gs-spring-boot/SpringServiceFabric`.
2. Uruchom następujące polecenie, aby połączyć się z klastrem platformy Azure. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    Gdy klaster jest zabezpieczony certyfikatem z podpisem własnym, uruchamiane jest następujące polecenie: 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Uruchom skrypt `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Uruchom przeglądarkę internetową i uzyskaj dostęp do aplikacji, przechodząc do adresu **http://\<ConnectionIPOrURL>:8080**. 

    ![Fronton aplikacji — lokalny](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
Teraz możesz uzyskać dostęp do aplikacji Spring Boot uruchomionej w klastrze usługi Service Fabric na platformie Azure.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Skalowanie aplikacji i usług w klastrze
Usługi można skalować na klaster w celu dostosowania ich do zmiany obciążenia. Skalowanie usługi odbywa się przez zmienianie liczby wystąpień uruchomionych w klastrze. Istnieje wiele sposobów skalowania usług. Na przykład można użyć skryptów lub poleceń interfejsu wiersza polecenia usługi Service Fabric (sfctl). W poniższych krokach będzie używane narzędzie Service Fabric Explorer.

Narzędzie Service Fabric Explorer działa we wszystkich klastrach usługi Service Fabric i można uzyskać do niego dostęp z przeglądarki, przechodząc do portu HTTP zarządzania klastrami (19080), na przykład `http://localhost:19080`.

Aby skalować usługę internetową frontonu, wykonaj następujące czynności:

1. Otwórz narzędzie Service Fabric Explorer w klastrze — na przykład `http://localhost:19080`.
2. Kliknij wielokropek (trzy kropki) obok węzła **fabric:/SpringServiceFabric/SpringGettingStarted** w widoku drzewa i wybierz pozycję **Skaluj usługę**.

    ![Usługa skalowania narzędzia Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Teraz możesz skalować liczbę wystąpień usługi.

3. Zmień liczbę na **3** i kliknij pozycję **Skaluj usługę**.

    Alternatywny sposób skalowania usługi to użycie wiersza polecenia w następujący sposób.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Kliknij węzeł **fabric:/SpringServiceFabric/SpringGettingStarted** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

    ![Usługa skalowania narzędzia Service Fabric Explorer — zakończone](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    Usługa ma trzy wystąpienia, a widok drzewa pokazuje węzły, na których wystąpienia są uruchomione.

Za pomocą tego prostego zadania zarządzania zostały podwojone zasoby dostępne dla usługi frontonu na potrzeby przetwarzania obciążenia użytkownika. Pamiętaj, że nie musisz mieć wielu wystąpień usługi, aby działała ona niezawodnie. W przypadku awarii usługa Service Fabric gwarantuje uruchomienie nowego wystąpienie usługi w klastrze.

## <a name="fail-over-services-in-a-cluster"></a>Przenoszenie usług do trybu failover w klastrze 
Aby przedstawić przenoszenie usługi w tryb failover, ponowne uruchomienie węzła jest symulowane przy użyciu narzędzia Service Fabric Explorer. Upewnij się, że jest uruchomione tylko jedno wystąpienie usługi. 

1. Otwórz narzędzie Service Fabric Explorer w klastrze — na przykład `http://localhost:19080`.
2. Kliknij wielokropek (trzy kropki) obok węzła, w którym uruchomiono wystąpienie usługi, i uruchom ponownie węzeł. 

    ![Service Fabric Explorer — ponowne uruchamianie węzła](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. Wystąpienie usługi zostanie przeniesione do innego węzła bez przestoju w działaniu aplikacji. 

    ![Service Fabric Explorer — ponowne uruchamianie węzła zakończone powodzeniem](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Wdrażanie aplikacji Spring Boot w usłudze Service Fabric
* Wdrażanie aplikacji w klastrze lokalnym 
* Wdrażanie aplikacji w klastrze na platformie Azure
* Skalowanie aplikacji w poziomie na wiele węzłów
* Przenoszenie usługi w tryb failover bez wywierania wpływu na jej dostępność

Aby dowiedzieć się więcej o pracy z aplikacjami Java w usłudze Service Fabric, przejdź do samouczka dotyczącego aplikacji Java.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji Java](./service-fabric-tutorial-create-java-app.md)
