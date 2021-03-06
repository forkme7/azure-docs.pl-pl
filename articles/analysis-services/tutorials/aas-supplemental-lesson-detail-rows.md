---
title: 'Samouczek Azure Analysis Services: lekcja uzupełniająca — wiersze szczegółów | Microsoft Docs'
description: Opisuje sposób tworzenia wyrażenia wierszy szczegółów w samouczku usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 02e9edd966e64c0bfa32e2b80f4c26f797e58582
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="supplemental-lesson---detail-rows"></a>Lekcja uzupełniająca — wiersze szczegółów

W tej lekcji uzupełniającej zdefiniujesz niestandardowe wyrażenie wierszy szczegółów przy użyciu edytora języka DAX. Wyrażenie wierszy szczegółów jest właściwością miary, która dostarcza użytkownikom końcowym więcej informacji na temat zagregowanych wyników miary. 
  
Szacowany czas trwania lekcji: **10 minut**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ta lekcja uzupełniająca stanowi część samouczka modelowania tabelarycznego. Przed wykonaniem zadań w tej lekcji uzupełniającej należy ukończyć wszystkie poprzednie lekcje lub wykonać przykładowy projekt modelu Adventure Works Internet Sales.  
  
## <a name="whats-the-issue"></a>Na czym polega problem?
Przyjrzyjmy się szczegóły miary InternetTotalSales przed dodaniem wyrażenia do szczegółów wierszy.

1.  W programie SSDT kliknij menu **Model** > **Analiza w programie Excel**, aby otworzyć program Excel i utworzyć pustą tabelę przestawną.
  
2.  W obszarze **Pola tabeli przestawnej** dodaj miarę **InternetTotalSales** z tabeli FactInternetSales do pola **Wartości**, element **CalendarYear** z tabeli DimDate do pola **Kolumny** oraz element **EnglishCountryRegionName** do pola **Wiersze**. Tabelę przestawną daje obecnie wyników zagregowany z miary InternetTotalSales regiony i roku. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. W tabeli przestawnej kliknij dwukrotnie zagregowaną wartość dla roku i nazwy regionu. Wartość dla Australii i 2014 roku. Otwarty został nowy arkusz zawierający dane, które jednak nie są użyteczne.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
Celem jest tabelę zawierającą kolumn i wierszy danych, które przyczyniają się do wyniku zagregowanych InternetTotalSales miary. W tym celu Dodaj wyrażenie wiersze szczegółów jako właściwość miary.

## <a name="add-a-detail-rows-expression"></a>Dodawanie wyrażenia wierszy szczegółów

#### <a name="to-create-a-detail-rows-expression"></a>Dodanie wyrażenia wierszy szczegółów 
  
1. W siatce miar tabeli FactInternetSales kliknij miarę **InternetTotalSales**. 

2. W obszarze **Właściwości** > **Wyrażenie wierszy szczegółów** kliknij przycisk edytora, aby otworzyć edytor języka DAX.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. W edytorze języka DAX wprowadź następujące wyrażenie:

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    To wyrażenie określa nazwy, kolumny i wyniki miary z tabeli FactInternetSales i tabel powiązanych, które będą zwracane po dwukrotnym kliknięciu zagregowanych wyników w tabeli przestawnej lub raporcie.

4. Po powrocie do programu Excel usuń arkusz utworzony w kroku 3, a następnie kliknij dwukrotnie zagregowaną wartość. Tym razem dzięki zdefiniowaniu dla miary właściwości wyrażenia wierszy szczegółów nowy arkusz po otwarciu będzie zawierać znacznie więcej przydatnych danych.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Wykonaj ponowne wdrożenie modelu.

  
## <a name="see-also"></a>Zobacz także  

[Funkcja SELECTCOLUMNS (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[Uzupełniające lekcji - dynamiczne zabezpieczeń](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Lekcja uzupełniająca — niewyrównane hierarchie](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
 