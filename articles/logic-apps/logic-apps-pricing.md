---
title: "Cennik i rozliczenia — usługi Azure Logic Apps | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak działa cennik i rozliczenia dla usługi Azure Logic Apps"
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: LADocs; klam
ms.openlocfilehash: 096fdd5a6604ed8cecc931da2169194b777664d2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
---
# <a name="logic-apps-pricing-model"></a>Model cen aplikacji logiki

Można utworzyć i uruchomić zautomatyzowanych skalowalnej integracji przepływów pracy w chmurze za pomocą usługi Azure Logic Apps. Poniżej przedstawiono szczegółowe informacje o rozliczeniach i cenach dla usługi Logic Apps. 

## <a name="consumption-pricing-model"></a>Użycie modelu cenowego

Dzięki aplikacjom logiki nowo utworzony płaci się tylko w przypadku należy użyć. Nowe aplikacje logiki Użyj planu zużycia i modelu cenowego, co oznacza, że są naliczane wykonania działania wykonywane przez wystąpienie aplikacji logiki. Każdy krok w definicji aplikacji logiki jest akcji, która zawiera wyzwalacze, kroki przepływu sterowania wywołania akcje wbudowane i wywołania łączników. Aby uzyskać więcej informacji, zobacz [cennik aplikacje logiki](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Wyzwalacze

Wyzwalacze są specjalne działania, które Utwórz wystąpienie aplikacji logiki w przypadku określonego zdarzenia. Wyzwalacze pełniących różne sposoby, które mają wpływ na sposób taryfowych aplikacji logiki.

* **Wyzwalacz sondowania** — tego wyzwalacza stale sprawdza punktu końcowego dla wiadomości, które spełniają kryteria służące do tworzenia wystąpienia aplikacji logiki i uruchamianie przepływu pracy. Każde żądanie sondowania zlicza jak wykonanie i są naliczane nawet wtedy, gdy jest tworzone żadne wystąpienie aplikacji logiki. Aby określić interwał sondowania, należy skonfigurować za pomocą projektanta aplikacji logiki wyzwalacza.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Wyzwalacz Webhook** — tego wyzwalacza czeka na klienta wysłać żądania do określonego punktu końcowego. Zlicza każde żądanie wysłane do punktu końcowego elementu webhook jak wykonanie akcji. Na przykład wyzwalacz żądania i HTTP elementu Webhook są obie wyzwalaczy elementu webhook.

* **Wyzwalacz cyklu** — tego wyzwalacza tworzy wystąpienie aplikacji logiki oparte na interwał cyklu, skonfigurowanego w wyzwalaczu. Na przykład można ustawić górę wyzwalacz cyklu co trzy dni lub bardziej złożonych zgodnie z harmonogramem.

Wyzwalacz wykonaniami można znaleźć w okienku przeglądu aplikację logiki w sekcji historii wyzwalacza.

## <a name="actions"></a>Akcje

Wbudowane akcje, takie jak akcje wywołać HTTP, funkcje platformy Azure lub interfejsu API zarządzania, a także sterować przepływem kroki są mierzone jako działania natywnego, które mają ich odpowiednich typów. Akcje, które wywołują [łączniki](https://docs.microsoft.com/connectors) ma typ "ApiConnection". Te łączniki są sklasyfikowane jako standard lub enterprise łączników, które są naliczane oparte na odpowiednich [cennik][pricing]. 

Wszystkie akcje pomyślnie i niepomyślnie wykonywania są zliczane i mierzone jako wykonania akcji. Jednak akcje, które są pomijane, ze względu na warunki unmet lub akcje, które nie działają, ponieważ aplikacji logiki przerwane przed ukończeniem, nie są liczone jak wykonania akcji. Aplikacje logiki wyłączone nie można utworzyć wystąpienia nowych wystąpień, więc ich nie są naliczane opłaty, gdy są one wyłączone.

> [!NOTE]
> Po wyłączeniu aplikacji logiki, wszystkie uruchomione wystąpienia może potrwać pewien czas, zanim całkowicie zatrzymać.

Akcje, które są uruchamiane wewnątrz pętli są liczone każdy cykl w pętli. Na przykład jednej akcji w pętli "for each", który przetwarza listę 10 elementu jest liczony mnożąc liczbę elementów listy (10) przez liczbę akcji w pętli (1) plus jeden uruchamiania pętli. Tak, na przykład obliczenie jest (10 * 1) + 1, co prowadzi do 11 wykonania akcji.

## <a name="integration-account-usage"></a>Użycie konta integracji

Na podstawie zużycia użycia obejmuje [konta integracji](logic-apps-enterprise-integration-create-integration-account.md) gdzie można eksplorować, opracowanie i przetestowanie [B2B/EDI](logic-apps-enterprise-integration-b2b.md) i [przetwarzania XML](logic-apps-enterprise-integration-xml.md) funkcji Logic Apps bez dodatkowych kosztów. Może mieć jedno z tych kont integracji według regionu i magazynu maksymalnie 10 umów oraz 25 mapy. Możesz mieć i przekaż nieograniczone partnerów, schematy i certyfikaty.

Logic Apps oferuje również konta integracji basic i standard z obsługiwanych SLA aplikacji logiki. Możesz użyć kont podstawową integrację, gdy użytkownik ma być używany tylko obsługa komunikatów lub działa jako partner małych firm, która ma relację handlowym partnera z większą jednostki biznesowe. Konta standardowe integracji obsługuje bardziej złożonych relacji B2B i zwiększyć liczbę obiektów, którymi można zarządzać. Aby uzyskać więcej informacji, zobacz [cennik platformy Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o aplikacjach Logic Apps][whatis]
* [Tworzenie pierwszej aplikacji logiki][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

