---
title: Kontrolowanie Azure CDN buforowanie z ciągami zapytań - warstwy standardowa | Dokumentacja firmy Microsoft
description: Ciąg zapytania usługi Azure CDN buforowanie kontroli, jak pliki są buforowane na żądania sieci web zawiera ciąg zapytania. W tym artykule opisano buforowania w standardowe produkty Azure CDN ciągu zapytania.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mazha
ms.openlocfilehash: ed6f0b2c021fc4b31b85986c07df0502dba826f2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Formant Azure CDN buforowanie z ciągami zapytań — warstwa standardowa
> [!div class="op_single_selector"]
> * [Warstwa standardowa](cdn-query-string.md)
> * [Warstwa Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Przegląd
Z buforowania ciągu kwerendy Azure sieci dostarczania zawartości (CDN) określa, jak pliki są buforowane dla żądania sieci web, który zawiera ciąg zapytania. W ramach żądania sieci web z ciągu zapytania ciąg zapytania jest częścią żądania, która występuje po znaku zapytania (?). Ciąg zapytania mogą zawierać pary klucz wartość, w których nazwy pól i wartości są oddzielone znakiem równości (=). Każda para klucz wartość oddzielona znakiem handlowego "i" (&). Na przykład http:\//www.contoso.com/content.mov?field1=value1 & pole2 = wartość2. Jeśli istnieje więcej niż jedną parę klucz wartość ciągu zapytania żądania, ich kolejność nie ma znaczenia. 

> [!NOTE]
> Usługa Azure CDN standard i premium produktów Podaj ten sam ciąg zapytania buforowanie funkcji, ale interfejs użytkownika jest inny.  W tym artykule opisano interfejs dla **Azure CDN Standard from Akamai** i **Azure CDN Standard from Verizon**. Dla zapytania z buforowania ciągu **Azure CDN Premium from Verizon**, zobacz [kontroli usługi Azure CDN buforowanie z ciągami zapytań — warstwa premium](cdn-query-string-premium.md).
>

Dostępne są trzy tryby ciągu zapytania:

- **Ignorować ciągi kwerendy**: domyślny tryb. W tym trybie węzeł CDN punktu z obecności (POP) przekazuje ciągi zapytań z obiektu żądającego do serwera pochodzenia na pierwsze żądanie i buforuje elementu zawartości. Wszystkie kolejne żądania dla elementu zawartości, które są obsługiwane z punktu obecności ignorować ciągi kwerendy do momentu wygaśnięcia elementu pamięci podręcznej zawartości.

- **Pomiń buforowanie dla ciągów zapytań**: W tym trybie żądań z ciągami zapytań nie są buforowane w węźle POP w sieci CDN. Węzeł POP pobiera elementu zawartości bezpośrednio z serwera pochodzenia i przekazuje je do obiektu żądającego z każdym żądaniem.

- **Buforuj każdy unikatowy adres URL**: W tym trybie każde żądanie o unikatowym adresie URL, ciąg zapytania w tym jest traktowany jako unikatowy zasób ze swojej własnej pamięci podręcznej. Na przykład odpowiedź z serwera pochodzenia żądania dla example.ashx?q=test1 jest buforowane w węźle POP i zwrócony dla kolejnych pamięci podręcznych ciągiem zapytania. Żądanie example.ashx?q=test2 jest buforowany jako osobne zasobów z własną ustawienie czasu wygaśnięcia.
   
    >[!IMPORTANT] 
    > W tym trybie nie należy używać, jeśli ciąg zapytania zawiera parametry, które zmieni się z każdym żądaniu, takich jak identyfikator sesji lub nazwę użytkownika, ponieważ spowoduje niski Stosunek trafień w pamięci podręcznej.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Zmiana ustawienia profilów sieci CDN w warstwie standardowa buforowanie ciągów zapytań
1. Otwieranie profilu CDN, a następnie wybierz punkt końcowy CDN, którą chcesz zarządzać.
   
   ![Punkty końcowe profilu CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. W lewym okienku w obszarze Ustawienia, kliknij przycisk **buforowanie reguły**.
   
    ![Przycisk reguły buforowania CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. W **zachowanie buforowania ciągu kwerendy** listy, wybierz tryb ciąg zapytania, a następnie kliknij przycisk **zapisać**.
   
   ![Opcje buforowania ciągu kwerendy CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Ponieważ czas rejestracji propagację za pośrednictwem sieci CDN, zmiany ustawień pamięci podręcznej ciąg może nie być od razu widoczne: 
> - Aby uzyskać **Azure CDN Standard from Akamai** profile, propagacji zazwyczaj kończy w ciągu jednej minuty. 
> - Aby uzyskać **Azure CDN Standard from Verizon** profile, propagacji zazwyczaj kończy w ciągu 90 minut.
>


