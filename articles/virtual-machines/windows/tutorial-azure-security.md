---
title: Centrum zabezpieczeń Azure i systemu Windows maszyny wirtualne na platformie Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zabezpieczeń dla maszyny wirtualnej systemu Windows Azure z Centrum zabezpieczeń Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4597de035e352387c22e92412ee6361f9c38a8ca
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>Monitorowanie zabezpieczeń maszyny wirtualnej przy użyciu usługi Azure Security Center

Usługa Azure Security Center ułatwia wgląd w rozwiązania z zakresu zabezpieczeń zasobów platformy Azure. Usługa Security Center oferuje zintegrowane monitorowanie zabezpieczeń. Potrafi wykrywać zagrożenia, które inaczej mogłyby ujść uwadze. W tym samouczku poznasz usługę Azure Security Center oraz następujące zagadnienia:
 
> [!div class="checklist"]
> * Konfigurowanie zbierania danych
> * Konfigurowanie zasad zabezpieczeń
> * Wyświetlanie problemów z kondycją konfiguracji i ich rozwiązywanie
> * Przeglądanie wykrytych zagrożeń  

## <a name="security-center-overview"></a>Security Center — Przegląd

Usługa Security Center identyfikuje potencjalne problemy z konfiguracją maszyny wirtualnej i ukierunkowane zagrożenia bezpieczeństwa. Mogą one obejmować maszyny wirtualne bez sieciowych grup zabezpieczeń, niezaszyfrowane dyski i ataki siłowe przez protokół RDP (Remote Desktop Protocol). Informacje są wyświetlane na pulpicie nawigacyjnym usługi Security Center w postaci czytelnych wykresów.

Aby uzyskać dostęp do pulpitu nawigacyjnego usługi Security Center, w menu w witrynie Azure Portal wybierz pozycję **Security Center**. Na pulpicie nawigacyjnym można sprawdzić kondycję zabezpieczeń środowiska platformy Azure, liczbę zalecanych rozwiązań i bieżący stan alertów dotyczących zagrożeń. Każdy wykres wysokiego poziomu można rozwinąć, aby wyświetlić bardziej szczegółowe informacje.

![Pulpit nawigacyjny usługi Security Center](./media/tutorial-azure-security/asc-dash.png)

Usługa Security Center wykracza poza odnajdywanie danych i udostępnia zalecenia dla wykrytych problemów. Na przykład jeśli maszyna wirtualna została wdrożona bez dołączonej sieciowej grupy zabezpieczeń, usługa Security Center wyświetla zalecenie z krokami korygowania do wykonania. Automatyczne kroki korygowania uzyskuje się bez opuszczania kontekstu usługi Security Center.  

![Zalecenia](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Konfigurowanie zbierania danych

Zanim będzie możliwe uzyskanie wglądu w konfiguracje zabezpieczeń maszyn wirtualnych, trzeba skonfigurować zbieranie danych przez usługę Security Center. Obejmuje to włączenie zbierania danych i utworzenie konta magazynu platformy Azure do przechowywania zebranych danych. 

1. Na pulpicie nawigacyjnym usługi Security Center kliknij pozycję **Zasady zabezpieczeń**, a następnie wybierz swoją subskrypcję. 
2. Dla pozycji **Zbieranie danych** wybierz ustawienie **Włączone**.
3. Aby utworzyć konto magazynu, wybierz pozycję **Wybierz konto magazynu**. Następnie wybierz przycisk **OK**.
4. W bloku **Zasady zabezpieczeń** wybierz pozycję **Zapisz**. 

Następnie na wszystkich maszynach wirtualnych jest instalowany agent zbierania danych usługi Security Center i rozpoczyna się zbieranie danych. 

## <a name="set-up-a-security-policy"></a>Konfigurowanie zasad zabezpieczeń

Zasady zabezpieczeń służą do definiowania elementów, dla których usługa Security Center zbiera dane i przygotowuje zalecenia. Różne zasady zabezpieczeń można stosować do różnych zestawów zasobów platformy Azure. Mimo że domyślnie zasoby platformy Azure są sprawdzane pod kątem wszystkich elementów zasad, można wyłączyć pojedyncze elementy zasad dla wszystkich zasobów platformy Azure lub dla grupy zasobów. Aby uzyskać szczegółowe informacje na temat zasad zabezpieczeń usługi Security Center, zobacz [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](../../security-center/security-center-policies.md). 

Aby skonfigurować zasady zabezpieczeń dla wszystkich zasobów platformy Azure:

1. Na pulpicie nawigacyjnym usługi Security Center wybierz pozycję **Zasady zabezpieczeń**, a następnie wybierz swoją subskrypcję.
2. Wybierz pozycję **Zasady zapobiegania**.
3. Włącz lub wyłącz elementy zasad, które chcesz zastosować do wszystkich zasobów platformy Azure.
4. Po zakończeniu wybierania ustawień wybierz przycisk **OK**.
5. W bloku **Zasady zabezpieczeń** wybierz pozycję **Zapisz**. 

Aby skonfigurować zasady dla konkretnej grupy zasobów:

1. Na pulpicie nawigacyjnym usługi Security Center wybierz pozycję **Zasady zabezpieczeń**, a następnie wybierz grupę zasobów.
2. Wybierz pozycję **Zasady zapobiegania**.
3. Włącz lub wyłącz elementy zasad, które chcesz zastosować do grupy zasobów.
4. W obszarze **DZIEDZICZENIE** wybierz pozycję **Unikatowe**.
5. Po zakończeniu wybierania ustawień wybierz przycisk **OK**.
6. W bloku **Zasady zabezpieczeń** wybierz pozycję **Zapisz**.  

Na tej stronie możesz też wyłączyć zbieranie danych dla konkretnej grupy zasobów.

W poniższym przykładzie utworzono unikatowe zasady dla grupy zasobów o nazwie *myResoureGroup*. W tych zasadach wyłączone są zalecenia dotyczące szyfrowania dysku i zapory aplikacji internetowych.

![Unikatowe zasady](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Wyświetlanie kondycji konfiguracji maszyny wirtualnej

Po włączeniu zbierania danych i ustawieniu zasad zabezpieczeń usługa Security Center zacznie udostępniać alerty i zalecenia. Gdy maszyny wirtualne są wdrażane, instalowany jest agent zbierania danych. Usługa Security Center jest następnie wypełniana danymi dotyczącymi nowych maszyn wirtualnych. Aby uzyskać szczegółowe informacje o kondycji konfiguracji maszyny wirtualnej, zobacz [Ochrona maszyn wirtualnych w usłudze Security Center](../../security-center/security-center-virtual-machine-recommendations.md). 

W miarę gromadzenia danych agregowana jest kondycja zasobu dla każdej maszyny wirtualnej i powiązanego zasobu platformy Azure. Informacje są pokazywane na czytelnym wykresie. 

Aby wyświetlić kondycję zasobu:

1.  Na pulpicie nawigacyjnym usługi Security Center w obszarze **Kondycja zabezpieczeń zasobów** wybierz pozycję **Obliczenia**. 
2.  W bloku **Obliczenia** wybierz pozycję **Maszyny wirtualne**. Ten widok zawiera podsumowanie stanu konfiguracji dla wszystkich maszyn wirtualnych.

![Kondycja obliczeń](./media/tutorial-azure-security/compute-health.png)

Aby wyświetlić wszystkie zalecenia dotyczące maszyny wirtualnej, wybierz maszynę wirtualną. Zalecenia i kroki korygowania są bardziej szczegółowo omówione w następnej sekcji tego samouczka.

## <a name="remediate-configuration-issues"></a>Rozwiązywanie problemów z konfiguracją

Gdy już dane konfiguracji zaczną spływać do usługi Security Center, rozpocznie się przygotowywanie zaleceń na podstawie skonfigurowanych zasad zabezpieczeń. Na przykład jeśli maszyna wirtualna zostanie skonfigurowana bez skojarzonej sieciowej grupy zabezpieczeń, pojawi się zalecenie, aby ją utworzyć. 

Aby zobaczyć listę wszystkich zaleceń: 

1. Na pulpicie nawigacyjnym usługi Security Center wybierz pozycję **Zalecenia**.
2. Wybierz konkretne zalecenie. Zostanie wyświetlona lista wszystkich zasobów, dla których zalecenie ma zastosowanie.
3. Aby zastosować zalecenie, wybierz konkretny zasób. 
4. Postępuj zgodnie z instrukcjami, aby wykonać kroki korygowania. 

W wielu przypadkach usługa Security Center przedstawia kroki z możliwością działania, które można wykonać w celu zastosowania zalecenia bez opuszczania usługi Security Center. W poniższym przykładzie usługa Security Center wykrywa sieciową grupę zabezpieczeń, która ma nieograniczoną regułę ruchu przychodzącego. Na stronie z zaleceniem możesz wybrać przycisk **Edytuj reguły dla ruchu przychodzącego**. Zostanie wyświetlony interfejs użytkownika, który jest potrzebny do zmodyfikowania reguły. 

![Zalecenia](./media/tutorial-azure-security/remediation.png)

W miarę stosowania się do zaleceń są one oznaczane jako rozwiązane. 

## <a name="view-detected-threats"></a>Wyświetlanie wykrytych zagrożeń

Oprócz zaleceń dotyczących konfiguracji zasobów usługa Security Center wyświetla alerty dotyczące wykrywania zagrożeń. Funkcja alertów zabezpieczeń agreguje dane zbierane z każdej maszyny wirtualnej, dzienników sieci platformy Azure i połączonych rozwiązań partnerów w celu wykrywania zagrożeń bezpieczeństwa dotyczących zasobów platformy Azure. Aby uzyskać szczegółowe informacje na temat możliwości wykrywania zagrożeń w usłudze Security Center, zobacz [Funkcje wykrywania usługi Azure Security Center](../../security-center/security-center-detection-capabilities.md).

Funkcja alertów zabezpieczeń wymaga podniesienia warstwy cenowej usługi Security Center z *Bezpłatna* do *Standardowa*. Przy przechodzeniu na tę wyższą warstwę cenową dostępny jest 30-dniowy **bezpłatny okres próbny**. 

Aby zmienić warstwę cenową:  

1. Na pulpicie nawigacyjnym usługi Security Center kliknij pozycję **Zasady zabezpieczeń**, a następnie wybierz swoją subskrypcję.
2. Wybierz **warstwę cenową**.
3. Wybierz nową warstwę, a następnie wybierz pozycję **Wybierz**.
4. W bloku **Zasady zabezpieczeń** wybierz pozycję **Zapisz**. 

Po zmianie warstwy cenowej wykres alertów zabezpieczeń zacznie być wypełniany w miarę wykrywania zagrożeń.

![Alerty zabezpieczeń](./media/tutorial-azure-security/security-alerts.png)

Wybierz alert, aby wyświetlić informacje. Można na przykład zobaczyć opis zagrożenia, czas wykrycia, wszystkie wystąpienia zagrożenia i zalecane kroki korygowania. W poniższym przykładzie wykryto atak siłowy przez protokół RDP. Zarejestrowano 294 nieudane próby ataku. Udostępniono też zalecane rozwiązanie.

![Atak przez protokół RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku skonfigurowano usługę Azure Security Center, a następnie sprawdzono maszyny wirtualne w usłudze Security Center. W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie zbierania danych
> * Konfigurowanie zasad zabezpieczeń
> * Wyświetlanie problemów z kondycją konfiguracji i ich rozwiązywanie
> * Przeglądanie wykrytych zagrożeń

Przejdź do następnego samouczkiem, aby dowiedzieć się, jak utworzyć potok CI/CD z Visual Studio Team Services i maszyny Wirtualnej systemu Windows usług IIS.

> [!div class="nextstepaction"]
> [Visual Studio Team Services CI/CD potoku](./tutorial-vsts-iis-cicd.md)
