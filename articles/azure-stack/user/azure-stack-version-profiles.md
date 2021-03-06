---
title: Zarządzanie profilami wersji interfejsu API w stosie Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat profilów wersji interfejsu API Azure stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 8A336052-8520-41D2-AF6F-0CCE23F727B4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 452ed1de0588b380747edaa44dd0cc3805c51392
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Zarządzanie profilami wersji interfejsu API Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Profile interfejsu API Określ dostawcy zasobów platformy Azure i wersja interfejsu API Azure REST punktów końcowych. Można tworzyć niestandardowe klientów w różnych językach przy użyciu interfejsu API profilów. Każdy klient używa profil interfejsu API można skontaktować się z dostawcy zasobów prawo i wersja interfejsu API Azure stosu. 

Możesz utworzyć aplikację do pracy z dostawców zasobów platformy Azure, bez konieczności przystąpić dokładnie wersji każdego z dostawców zasobów interfejsu API jest zgodny z stosu Azure. Po prostu Dopasuj aplikacji do profilu; zestaw SDK wraca do prawej wersji interfejsu API.


Ten temat ułatwia:
 - Zrozumienie profile interfejsu API Azure stosu.
 - Jak można użyć interfejsu API profile do opracowywania rozwiązań.
 - Gdzie szukać wskazówki dotyczące kodu.

## <a name="summary-of-api-profiles"></a>Podsumowanie profilów interfejsu API

- Profile interfejsu API są używane do reprezentowania zestaw dostawców zasobów platformy Azure i ich wersje interfejsu API.
- Profile interfejsu API zostały utworzone dla deweloperów do tworzenia szablonów w wielu chmury Azure. Są one przeznaczone do spełnia potrzeby interfejsy zgodne i stabilna.
- Profile są wydawane cztery razy w roku.
- Są trzy profile konwencji nazewnictwa:
    - **latest**  
        Najnowsze wersje interfejsu API wydane na platformie Azure.
    - **yyyy-mm-dd-hybrid**  
    Wydawane w okresach organizowanych, to wydanie koncentruje się na spójność i stabilność przez wiele chmur.
    - **yyyy-mm-dd-profile**  
    Znajduje się pomiędzy optymalną stabilności i najnowszych funkcji.

## <a name="azure-resource-manager-api-profiles"></a>Profile interfejs API Menedżera zasobów Azure

Azure stosu nie używać najnowszej wersji z wersji interfejsu API w globalnej Azure. Podczas tworzenia własnych rozwiązania, należy znaleźć wersja interfejsu API dla każdego dostawcy zasobów platformy Azure zgodnego z stosu Azure.

Zamiast niż badania każdy dostawca zasobów i obsługiwane przez stos Azure wersji, można użyć profilu interfejsu API. Profil określa zestaw dostawców zasobów i wersje interfejsu API. Zestaw SDK lub skompilowane przy użyciu zestawu SDK narzędzia zostaną przywrócone do wersji interfejsu api docelowej określona w profilu. Przy użyciu profilów interfejsu API można określić wersji profilu, która ma zastosowanie do całego szablonu, a następnie w czasie wykonywania, usługi Azure Resource Manager wybierze właściwej wersji zasobu.

Interfejs API profile pracować z narzędziami, które używają usługi Azure Resource Manager, takich jak programu PowerShell, interfejsu wiersza polecenia Azure, kod w zestawie SDK i programu Microsoft Visual Studio. Profile można użyć narzędzia i zestawy SDK można odczytać wersji modułów i biblioteki do uwzględnienia podczas tworzenia aplikacji.

Na przykład użyj programu PowerShell do utworzenia magazynu konta przy użyciu **Microsoft.Storage** dostawcy zasobów, która obsługuje interfejs api-version 2016-03-30 i maszyny Wirtualnej za pomocą dostawcy zasobów Microsoft.Compute z interfejsu api-version 2015-12-01 , należy wyszukać obsługujący moduł PowerShell 2016-03-30 dla magazynu i który moduł obsługuje 2015-02-01 obliczania i zainstaluj je. Zamiast tego można użyć profilu. Użyj polecenia cmdlet ** instalacji profilu * profilename *** i programu PowerShell ładuje właściwej wersji modułów.

Podobnie podczas tworzenia aplikacji opartych na języku Python za pomocą zestawu SDK Python, można określić profil. Zestaw SDK ładuje prawo modułów dla dostawców zasobów, określonych w skrypcie.

Deweloperzy mogą skupić się na zapisywanie rozwiązania. Zamiast badanie, której wersji interfejsu api, dostawca zasobów i które chmurze współpracuje ze sobą, Użyj profilu i dowiedzieć się, że kod będzie działać we wszystkich chmur, które obsługują tego profilu.

## <a name="api-profile-code-samples"></a>Przykłady kodu interfejsu API profilu

Przykłady kodu, aby pomóc w zintegrowaniu rozwiązania w języku preferowanym stosu Azure przy użyciu profilów można znaleźć. Obecnie wskazówki i przykłady można znaleźć w następujących językach:

- **Program PowerShell**  
Można użyć **AzureRM.Bootstrapper** modułu dostępne za pośrednictwem galerii programu PowerShell, można pobrać poleceń cmdlet programu PowerShell wymaganych do pracy z profilami wersji interfejsu API.  
Aby uzyskać informacje, zobacz [profile w wersji Użyj interfejsu API środowiska PowerShell](azure-stack-version-profiles-powershell.md).
- **Interfejs wiersza polecenia platformy Azure 2.0**  
Można aktualizować konfiguracji środowiska, aby użyć określonego profilu wersji interfejsu API Azure stosu.  
Aby uzyskać informacje, zobacz [wersji profilów Użyj interfejsu API Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
W zestawie SDK Przejdź profil jest kombinacją różnych typów zasobów z różnymi wersjami z różnych usług. Profile są dostępne w obszarze Profile / ścieżki z ich wersji w **RRRR-MM-DD** format.  
Aby uzyskać informacje, zobacz [Użyj interfejsu API w wersji profilów dla Przejdź](azure-stack-version-profiles-go.md).

## <a name="next-steps"></a>Kolejne kroki
* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
* [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](azure-stack-powershell-configure-user.md)
* [Szczegółowe informacje o wersji interfejsu API dostawcy zasobów obsługiwanych przez profile](azure-stack-profiles-azure-resource-manager-versions.md).
