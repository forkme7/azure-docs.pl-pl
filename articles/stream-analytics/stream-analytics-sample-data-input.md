---
title: Testowanie przy użyciu przykładowych danych w usłudze Azure Stream Analytics kwerendy
description: W tym artykule opisano sposób testowania zapytania przy użyciu przykładowych danych wejściowych w Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 305b767ee86de6c8b04fed17514a9092afc2d735
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2018
---
# <a name="test-a-query-and-sample-input-in-azure-stream-analytics"></a>Testowanie zapytań i przykładowych danych wejściowych w Azure Stream Analytics 

Za pomocą usługi Azure Stream Analytics, możesz przykładowe zdarzenia wejściowe, które pochodzą z pliku i testowanie zapytań w portalu, bez konieczności uruchamiania lub zatrzymywania zadania.

## <a name="testing-your-query"></a>Testowanie kwerendy

W okienku szczegółów zadania Stream Analytics, otwórz **edytora zapytań** bloku, klikając nazwę zapytania w obszarze **zapytania**. (W naszym przykładowym scenariuszu, ponieważ bez określenia zapytania nie utworzono jeszcze, kliknij **< >** symbolu zastępczego.)

![Edytor zapytań usługi Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

Jak w poprzedniej wersji, zostanie wyświetlony blok Zaawansowany edytor do tworzenia zapytania. Teraz bloku została zaktualizowana o nowe okienko po lewej stronie, pokazujący wejściach i wyjściach, które są używane przez zapytanie i są zdefiniowane dla tego zadania.

![Edytor zapytań usługi analiza strumienia danych wejściowych i wyprowadza list](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

Także wyświetlane są dodatkowe dane wejściowe i wyjściowe, które nie zostały zdefiniowane. Pochodzą z nowego szablonu zapytania, który rozpoczyna. Zmień lub nawet niewidoczny, jak edytować zapytanie. Można bezpiecznie zignorować je teraz.

Aby przetestować przykładowych danych wejściowych, kliknij prawym przyciskiem myszy dane wejściowe, a następnie wybierz **przekazać dane przykładowe z pliku**.

![Edytor przekazywania przykładowych danych z pliku polecenia zapytań usługi Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

Po zakończeniu pobierania kliknij **Test** do przetestowania tego zapytania dotyczącego przykładowe dane zostały podane.

![Przycisk Test Edytor zapytań usługi Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Jeśli chcesz zapisać testu, dane wyjściowe w celu późniejszego użycia, danych wyjściowych kwerendy jest wyświetlana w przeglądarce z łączem do pobierania wyników. Można teraz łatwo i wielokrotnie powtarzane zmodyfikuj zapytanie i przetestować go wielokrotnie, aby zobaczyć, jak zmiany danych wyjściowych.

![Edytor przykładowe dane wyjściowe zapytań usługi Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

Na poprzedniej ilustracji, został dodany drugi dane wyjściowe, nazywany **HighAvgTempOutput**.

Użycie wielu wyjść w zapytaniu, można wyświetlić wyniki każdego dane wyjściowe oddzielnie i łatwo przełączać się między nimi.

Po zakończeniu wyniki można zapisać kwerendę, uruchom zadanie, zaczekaj i obejrzyj magic usługi Stream Analytics.

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dodatkową pomoc, spróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
