---
title: Tworzenie usługi Load Balancer w warstwie Standardowa — witryna Azure Portal | Microsoft Docs
description: Dowiedz się, jak utworzyć usługę Load Balancer w warstwie Standardowa przy użyciu witryny Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/18
ms.author: kumud
ms.openlocfilehash: f67da7dc84878ca7418eb644daec1a9681e2f6f2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Tworzenie usługi Load Balancer w warstwie Standardowa przy użyciu witryny Azure Portal w celu równoważenia obciążenia maszyn wirtualnych

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. Za pomocą witryny Azure Portal można utworzyć moduł równoważenia obciążenia na potrzeby równoważenia obciążenia maszyn wirtualnych. W tym przewodniku Szybki start pokazano, jak zrównoważyć obciążenie maszyn wirtualnych przy użyciu usługi Load Balancer w warstwie Standardowa.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-load-balancer"></a>Tworzenie publicznego modułu równoważenia obciążenia

W tej sekcji utworzysz publiczny moduł równoważenia obciążenia, który pomaga równoważyć obciążenie maszyn wirtualnych. Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczny adres IP w warstwie Standardowa. Podczas tworzenia usługi Load Balancer w warstwie Standardowa musisz także utworzyć nowy publiczny adres IP w warstwie Standardowa, który jest skonfigurowany jako fronton (domyślnie o nazwie *LoadBalancerFrontend*) dla usługi Load Balancer w warstwie Standardowa. 

1. W lewym górnym rogu ekranu kliknij pozycję **Utwórz zasób** > **Sieć** > **Moduł równoważenia obciążenia**.
2. Na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź następujące wartości dla modułu równoważenia obciążenia:
    - *myLoadBalancer* — jako nazwę modułu równoważenia obciążenia.
    - **Public** — jako typ modułu równoważenia obciążenia.
     - *myPublicIP* — jako **nowy** publiczny adres IP, który utworzono.
    - *myResourceGroupSLB* — jako nazwę **nowej** grupy zasobów, którą wybrano do utworzenia.
    - **westeurope** — jako lokalizację.
3. Kliknij pozycję **Utwórz**, aby utworzyć moduł równoważenia obciążenia.
   
    ![Tworzenie modułu równoważenia obciążenia](./media/load-balancer-standard-public-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz sieć wirtualną, następnie dwie maszyny wirtualne dla puli zaplecza modułu równoważenia obciążenia, a następnie zainstalujesz usługi IIS na maszynach wirtualnych, aby ułatwić testowanie modułu równoważenia obciążenia.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
1. W lewej górnej części ekranu kliknij pozycję **Nowy** > **Sieć** > **Sieć wirtualna**, a następnie wprowadź następujące wartości dla sieci wirtualnej:
    - *myVnet* — jako nazwę sieci wirtualnej.
    - *myResourceGroupSLB* — jako nazwę istniejącej grupy zasobów.
    - *myBackendSubnet* — jako nazwę podsieci.
2. Kliknij pozycję **Utwórz**, aby utworzyć sieć wirtualną.

    ![Tworzenie sieci wirtualnej](./media/load-balancer-standard-public-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

1. W lewej górnej części ekranu kliknij pozycję **Nowy** > **Obliczenia** > **Windows Server 2016 Datacenter** i wprowadź następujące wartości dla maszyny wirtualnej:
    - *myVM1* — jako nazwę maszyny wirtualnej.        
    - *azureuser* — jako nazwę użytkownika administratora.    
    - *myResourceGroupSLB* — w obszarze **Grupa zasobów** wybierz opcję **Użyj istniejącej**, a następnie wybierz wartość *myResourceGroupSLB*.
2. Kliknij przycisk **OK**.
3. Wybierz **DS1_V2** jako rozmiar maszyny wirtualnej, a następnie kliknij pozycję **Wybierz**.
4. Wprowadź następujące wartości ustawień maszyny wirtualnej:
    - *myAvailabilitySet* — jako nazwę nowego zestawu dostępności, który tworzysz.
    -  *myVNet* — jako sieć wirtualną.
    - *myBackendSubnet* — jako podsieć.
    - *myNetworkSecurityGroup* — jako nazwę nowej grupy zabezpieczeń sieci (zapora), którą musisz utworzyć.
5. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
6. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **Utwórz**.
7. Korzystając z kroków 1–6, utwórz drugą maszynę wirtualną o nazwie *VM2* z następującymi ustawieniami: zestawem dostępności *myAvailibilityset*, siecią wirtualną *myVnet*, podsiecią *myBackendSubnet* i sieciową grupą zabezpieczeń **myNetworkSecurityGroup*. 

### <a name="create-nsg-rules"></a>Tworzenie reguł sieciowej grupy zabezpieczeń

W tej sekcji utworzysz reguły sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące przy użyciu protokołów HTTP i RDP.

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myNetworkSecurityGroup** znajdującą się w grupie zasobów **myResourceGroupSLB**.
2. W obszarze **Ustawienia** kliknij pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie kliknij przycisk **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie *myHTTPRule*, aby umożliwić obsługę przychodzących połączeń HTTP przy użyciu portu 80:
    - *Tag usługi* — w polu **Źródło**.
    - *Internet* — w polu **Tag usługi źródłowej**
    - *80* — w polu **Docelowe zakresy portów**
    - *TCP* — w polu **Protokół**
    - *Zezwalaj* — w polu **Akcja**
    - *100* — w polu **Priorytet**
    - *myHTTPRule* — jako nazwę
    - *Zezwalaj na HTTP* —jako opis
4. Kliknij przycisk **OK**.
 
 ![Tworzenie sieci wirtualnej](./media/load-balancer-standard-public-portal/8-load-balancer-nsg-rules.png)
5. Powtórz kroki od 2 do 4, używając następujących wartości, aby utworzyć inną regułę o nazwie *myRDPRule* zezwalającą na przychodzące połączenia RDP przy użyciu portu 3389:
    - *Tag usługi* — w polu **Źródło**.
    - *Internet* — w polu **Tag usługi źródłowej**
    - *3389* — w polu **Docelowe zakresy portów**
    - *TCP* — w polu **Protokół**
    - *Zezwalaj* — w polu **Akcja**
    - *200* — w polu **Priorytet**
    - *myRDPRule* — w polu nazwy
    - *Zezwalaj na RDP* — w polu opisu

### <a name="install-iis"></a>Instalowanie usług IIS

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVM1** znajdującą się w grupie zasobów *myResourceGroupLB*.
2. Na stronie **Przegląd** kliknij pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
3. Zaloguj się do maszyny wirtualnej przy użyciu nazwy użytkownika *azureuser*.
4. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne systemu Windows**>**Menedżer serwera**.
5. W Menedżerze serwera kliknij pozycję **Dodaj role i funkcje**.
6. W **kreatorze dodawania ról i funkcji** użyj następujących wartości:
    - Na stronie **Wybieranie typu instalacji** kliknij pozycję **Instalacja oparta na rolach lub funkcjach**.
    - Na stronie **Wybieranie serwera docelowego** kliknij pozycję **myVM1**.
    - Na stronie **Wybieranie roli serwera** kliknij pozycję **Serwer sieci Web (IIS)**.
    - Postępuj zgodnie z instrukcjami, aby ukończyć pozostałą część kreatora. 
7. Powtórz kroki od 1 do 6 dla maszyny wirtualnej *myVM2*.

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji oraz określisz reguły modułu równoważenia obciążenia i reguły translatora adresów sieciowych.


### <a name="create-a-backend-address-pool"></a>Tworzenie puli adresów zaplecza

Na potrzeby rozdzielania ruchu między maszyny wirtualne używana jest pula adresów zaplecza, zawierająca adresy IP wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia. Utwórz pulę adresów zaplecza *myBackendPool* obejmującą maszyny wirtualne *VM1* i *VM2*.

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myLoadBalancer**.
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie kliknij pozycję **Dodaj**.
3. Na stronie **Dodawanie puli zaplecza** wykonaj następujące czynności:
    - W polu nazwy wpisz *myBackEndPool jako nazwę puli zaplecza.
    - W polu **Skojarzone z** kliknij w menu rozwijanym pozycję **Zestaw dostępności**
    - W polu **Zestaw dostępności** kliknij pozycję **myAvailabilitySet**.
    - Kliknij pozycję **Dodaj docelową konfigurację adresu IP sieci**, aby dodać każdą utworzoną maszynę wirtualną (*myVM1* & *myVM2*) do puli zaplecza.
    - Kliknij przycisk **OK**.

    ![Dodawanie do puli adresów zaplecza ](./media/load-balancer-standard-public-portal/3-load-balancer-backend-02.png)

3. Upewnij się, że ustawienie puli zaplecza modułu równoważenia obciążenia wyświetla obie maszyny wirtualne — **VM1** i **VM2**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji umożliwia modułowi równoważenia obciążenia monitorowanie stanu aplikacji. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Utwórz sondę kondycji *myHealthProbe*, aby monitorować kondycję maszyn wirtualnych.

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myLoadBalancer**.
2. W obszarze **Ustawienia** kliknij pozycję **Sondy kondycji**, a następnie kliknij pozycję **Dodaj**.
3. Utwórz sondę kondycji, używając następujących wartości:
    - *myHealthProbe* — jako nazwy sondy kondycji.
    - **HTTP** — jako typu protokołu.
    - *80* — jako numeru portu.
    - *15* — w polu **Interwał** jako liczbę sekund między próbami sondy.
    - *2* — w polu **Próg złej kondycji** jako liczbę kolejnych niepowodzeń sondy, które muszą wystąpić, aby maszyna wirtualna została uznana za będącą w złej kondycji.
4. Kliknij przycisk **OK**.

   ![Dodawanie sondy](./media/load-balancer-standard-public-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myLoadBalancerRuleWeb* w celu nasłuchiwania na porcie 80 w puli frontonu *myFrontEndPool* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool*, również przy użyciu portu 80. 

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myLoadBalancer**.
2. W obszarze **Ustawienia** kliknij pozycję **Reguły równoważenia obciążenia**, a następnie kliknij pozycję **Dodaj**.
3. Skonfiguruj regułę równoważenia obciążenia, używając następujących wartości:
    - *myHTTPRule* — jako nazwy reguły równoważenia obciążenia.
    - **TCP** — jako typu protokołu.
    - *80* — jako numeru portu.
    - *80* — jako portu zaplecza.
    - *myBackendPool* — jako nazwy puli zaplecza.
    - *myHealthProbe* — jako nazwy sondy kondycji.
4. Kliknij przycisk **OK**.
    
    ![Dodawanie reguły równoważenia obciążenia](./media/load-balancer-standard-public-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia
1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij pozycję **myPublicIP**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

      ![Internetowy serwer usług IIS](./media/load-balancer-standard-public-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
