---
title: Publikowanie elementów marketplace niestandardowych w stosie Azure (operatorowi chmury) | Dokumentacja firmy Microsoft
description: Jako operator stosu Azure jak opublikować element marketplace niestandardowych w stosie Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 588da055d06d7e63510085ff48169f3ea756c53c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="the-azure-stack-marketplace-overview"></a>Omówienie usługi Azure Marketplace stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Witryny Marketplace to zbiór usług, aplikacji i zasobów dostosowany do stosu Azure. Zasoby obejmują sieci, maszyny wirtualne, magazynu i tak dalej. Użytkownicy w tym miejscu pochodzić do tworzenia nowych zasobów i wdrażania nowej aplikacji. Go traktować jako zakupów katalog, w którym użytkownicy mogą przeglądać i wybierz elementy, które mają być użyte. Aby użyć elementu portalu Marketplace, użytkownicy musi subskrybować ofertę, który przyznaje im dostępu do elementu.

Jako operator stosu Azure zdecydujesz elementy, które można dodać (publikowanie) w portalu Marketplace. Można opublikować bazy danych, usługi aplikacji i tak dalej. Publikowanie sposób będą one widoczne dla wszystkich użytkowników. Możesz opublikować niestandardowe elementy, które możesz utworzyć. Można również opublikować elementy z rosnącym [listy elementów portalu Azure Marketplace](azure-stack-marketplace-azure-items.md). Podczas publikowania elementu portalu Marketplace, użytkownicy mogą zobaczyć ją w ciągu pięciu minut.

Aby otworzyć witryny Marketplace, kliknij przycisk **nowy**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Elementy portalu Marketplace
Element Azure Marketplace stosu jest usługi, aplikacji lub zasobów, które użytkownicy mogą pobrać i użyć. Wszystkie elementy Azure Marketplace stosu są widoczne dla wszystkich użytkowników, w tym elementy administracyjne, takie jak plany i oferty. Elementy te nie wymagają subskrypcji do widoku, ale są niefunkcjonalne dla użytkowników.

Każdy element Marketplace zawiera:

* Szablon usługi Azure Resource Manager alokacji zasobów
* Metadane, takie jak ciągi, ikony i inne materiały marketingowe
* Formatowanie informacje do wyświetlania elementu w portalu

Każdy element publikowane w witrynie Marketplace w formacie pakietu galerii Azure (.azpkg). Dodaj oddzielnie, wdrożenia lub środowisko uruchomieniowe zasobów (takich jak kod, pliki zip za pomocą oprogramowania, obrazy maszyny wirtualnej) Azure stosu nie jako część elementu portalu Marketplace. 

Z wersją 1803 i nowsze stos Azure konwertuje obrazy plików rozrzedzonych podczas pobierania z platformy Azure lub gdy przekazywanie niestandardowych obrazów. Ten proces wydłuża czas przy dodawaniu obrazu, ale pozwala zaoszczędzić miejsce i przyspiesza wdrażanie tych obrazów. Konwersja ma zastosowanie tylko do nowych obrazów.  Istniejących obrazów nie są zmieniane. 

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md)

