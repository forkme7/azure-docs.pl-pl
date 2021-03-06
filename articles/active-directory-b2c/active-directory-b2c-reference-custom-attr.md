---
title: 'Usługi Azure Active Directory B2C: Atrybuty niestandardowe | Dokumentacja firmy Microsoft'
description: Jak używać atrybutów niestandardowych w usłudze Azure Active Directory B2C do zbierania informacji o użytkownikach
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 6f285c10b7d8ff92c8568c42b6a78dc4ea9bcc74
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Usługa Azure Active Directory B2C: Wykorzystaj niestandardowe atrybuty do zbierania informacji o użytkownikach
Katalogu usługi Azure Active Directory (Azure AD) B2C zawiera zestaw wbudowanych informacji (atrybuty): imię, nazwisko, Miasto, kod pocztowy i innych atrybutów. Jednak każda aplikacja dla użytkownika ma unikatowe wymagania na atrybuty, jakie można zebrać konsumentów. Z usługi Azure AD B2C można rozszerzyć zestaw atrybutów przechowywanych dla każdego konta użytkownika. Atrybuty niestandardowe można tworzyć na [portalu Azure](https://portal.azure.com/) i używać go w wypełnieniu zasad, jak pokazano poniżej. Może także odczytywać i zapisywać te atrybuty za pomocą [interfejsu API usługi Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [!NOTE]
> Użyj atrybutów niestandardowych [Azure AD Graph interfejsu API katalogu schematu rozszerzenia](https://msdn.microsoft.com/library/azure/dn720459.aspx).
> 
> 

## <a name="create-a-custom-attribute"></a>Tworzenie niestandardowego atrybutu
1. [Wykonaj następujące kroki, aby przejść do bloku funkcji B2C w portalu Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kliknij przycisk **atrybuty użytkownika**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj **nazwa** atrybutu niestandardowego (na przykład "ShoeSize") i opcjonalnie **opis**. Kliknij przycisk **Utwórz**.
   
   > [!NOTE]
   > Tylko "String", "Boolean" i "Int" **typy danych** są obecnie dostępne.
   > 
   > 

Atrybut niestandardowy jest teraz dostępna na liście **atrybuty użytkownika**i do użytku w zasadach tworzenia konta.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Użyj atrybutu niestandardowego w ramach zasad rejestracji
1. [Wykonaj następujące kroki, aby przejść do bloku funkcji B2C w portalu Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kliknij przycisk **Zasady tworzenia konta**.
3. Kliknij zasady rejestracji (na przykład "B2C_1_SiUp"), aby go otworzyć. Kliknij przycisk **Edytuj** w górnej części bloku.
4. Kliknij przycisk **atrybuty rejestracji** i wybierz atrybutu niestandardowego (na przykład "ShoeSize"). Kliknij przycisk **OK**.
5. Kliknij przycisk **oświadczenia aplikacji** i wybierz atrybut niestandardowy. Kliknij przycisk **OK**.
6. Kliknij przycisk **zapisać** w górnej części bloku.

Za pomocą funkcji "Uruchom teraz" na zasady można sprawdzić w środowisku użytkownika. Powinny teraz zobacz "ShoeSize" na liście atrybutów zebrane podczas tworzenia konta użytkownika i zobaczyć ją w tokenie wysyłane z powrotem do aplikacji.

## <a name="notes"></a>Uwagi
* Wraz z zasadami tworzenia konta atrybuty niestandardowe można również zasady rejestracji i logowania i profilu edytowanie zasad.
* Jest to znane ograniczenie atrybutów niestandardowych. Jest ona tylko tworzony po raz pierwszy jest używany w żadnych zasad, a nie, po dodaniu go do listy **atrybuty użytkownika**.

