---
title: Konfiguracja usługi analiza strumienia Azure tools dla programu Visual Studio
description: W tym artykule opisano wymagania dotyczące instalacji i konfiguracji usługi Azure Stream Analytics tools dla programu Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/19/2017
ms.openlocfilehash: 511658fc0e2b480987455007dac5f55cd7850feb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Zainstaluj narzędzia analiza strumienia Azure dla programu Visual Studio
Azure Stream Analytics tools są teraz obsługiwane w Visual Studio 2017 r. 2015 i 2013. Ten dokument zawiera opis sposobu instalowania i odinstalowywania narzędzia.

Aby uzyskać więcej informacji na temat używania narzędzia, zobacz [Stream Analytics tools dla Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Instalowanie
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Pobierz [programu Visual Studio 2017 (15.3 lub nowszej)](https://www.visualstudio.com/). Enterprise (Ultimate/Premium), Professional i społeczności wersje są obsługiwane. Express edition nie jest obsługiwane. 
* Narzędzia do analizy strumienia są częścią **Azure programowanie** i **magazynu danych i przetwarzania** obciążenia w programie Visual Studio 2017 r. Włącz jedno z tych dwóch obciążeń w ramach instalacji programu Visual Studio.

Włącz **magazynu danych i przetwarzania** obciążenia, jak pokazano:

![Obciążenie przechowywania i przetwarzania danych](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Włącz **Azure programowanie** obciążenia, jak pokazano:

![Programowanie Azure obciążenia](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Zainstaluj program Visual Studio 2015 lub Visual Studio 2013 Update 4. Enterprise (Ultimate/Premium), Professional i społeczności wersje są obsługiwane. Express edition nie jest obsługiwane. 
* Zainstaluj zestaw Microsoft Azure SDK dla platformy .NET w wersji 2.7.1 lub nowszej przy użyciu [Instalatora platformy sieci Web](http://www.microsoft.com/web/downloads/platform.aspx).
* Zainstaluj [analiza strumienia Azure tools dla Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Aktualizacja

### <a name="visual-studio-2017"></a>Visual Studio 2017
Monit wersji zostaną wyświetlone powiadomienia programu Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Analiza strumienia narzędzia Visual Studio sprawdzał nowe wersje. Postępuj zgodnie z instrukcjami w wyskakującym oknie, aby zainstalować najnowszą wersję. 


## <a name="uninstall"></a>Dezinstalacja

### <a name="visual-studio-2017"></a>Visual Studio 2017
Kliknij dwukrotnie pozycję Instalator programu Visual Studio i wybierz **Modyfikuj**. Wyczyść **usługi Azure Data Lake i narzędzia do analizy strumienia** pole wyboru przy użyciu dowolnego **magazynu danych i przetwarzania** obciążenia lub **Azure programowanie** obciążenia.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Przejdź do panelu sterowania, a następnie odinstaluj **Microsoft Azure Data Lake i Stream Analytics tools dla Visual Studio**.





