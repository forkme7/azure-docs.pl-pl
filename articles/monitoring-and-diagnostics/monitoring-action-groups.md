---
title: Utwórz i Zarządzaj grupami akcji w portalu Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia i obsługi grup działań w portalu Azure.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: dukek
ms.openlocfilehash: e3185b8d8ce97ffd04188b2b49a457bd14d5c6c8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Utwórz i Zarządzaj grupami akcji w portalu Azure
## <a name="overview"></a>Przegląd ##
W tym artykule przedstawiono sposób tworzenia i obsługi grup działań w portalu Azure.

Można skonfigurować listę akcji przy użyciu grup działań. Każdy alert, zdefiniowane przez użytkownika, zapewnienie, że te same akcje podejmowane są zawsze, gdy alert jest wyzwalany następnie można te grupy.

Każda akcja składa się z następującymi właściwościami:

* **Nazwa**: Unikatowy identyfikator grupy działań.  
* **Typ akcji**: wysyłanie połączenie głosowe lub wiadomości SMS, Wyślij wiadomość e-mail, wywołania elementu webhook, wysyłania danych do narzędzia Zarządzanie usługami IT —, wywołania aplikacji logiki, wysyłania powiadomień wypychanych do aplikacji Azure lub uruchom element runbook usługi Automatyzacja.
* **Szczegóły**: odpowiadającego phone numer, adres e-mail, identyfikator URI elementu webhook lub Zarządzanie usługami IT — szczegóły połączenia.

Aby uzyskać informacje na temat sposobu konfigurowania grup akcji za pomocą szablonów usługi Azure Resource Manager, zobacz [szablony Menedżera zasobów grupy akcji](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Utwórz grupę akcji przy użyciu portalu Azure ##
1. W [portal](https://portal.azure.com), wybierz pozycję **Monitor**. **Monitor** bloku konsoliduje wszystkich monitorowania ustawień i danych w jednym widoku.

    ![Usługa "Monitora"](./media/monitoring-action-groups/home-monitor.png)
2. W **ustawienia** zaznacz **grupy akcji**.

    ![Na karcie "Grup akcji"](./media/monitoring-action-groups/action-groups-blade.png)
3. Wybierz **Dodaj grupę akcji**, a następnie wypełnij pola.

    ![Polecenie "Dodaj grupę akcji"](./media/monitoring-action-groups/add-action-group.png)
4. Wprowadź nazwę w **nazwy grupy akcji** i wprowadzić nazwę w **krótką nazwę** pole. Krótka nazwa jest używana zamiast akcji Pełna nazwa grupy, podczas powiadomienia są wysyłane przy użyciu tej grupy.

      ![Okno dialogowe Dodawanie grupy akcji"](./media/monitoring-action-groups/action-group-define.png)

5. **Subskrypcji** polu autofills z Twojej bieżącej subskrypcji. Ta subskrypcja jest jeden, w którym jest zapisany grupy akcji.

6. Wybierz **grupy zasobów** w jest zapisywany grupy akcji.

7. Zdefiniuj listę działań, zapewniając każdej akcji:

    a. **Nazwa**: wprowadź unikatowy identyfikator dla tej akcji.

    b. **Typ akcji**: Wybierz E-mail/SMS/wypychanej/głosowych, aplikację logiki, elementu Webhook, zarządzanie usługami IT — lub elementu Runbook automatyzacji.

    c. **Szczegóły**: oparte na typ akcji, wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook, aplikację usługi Azure, zarządzanie usługami IT — połączenie lub elementu runbook automatyzacji. Zarządzanie usługami IT — akcji, należy również określić **elementu roboczego** i wymaga narzędzie Zarządzanie usługami IT — innych pól.

8. Wybierz **OK** można utworzyć grupy działań.

## <a name="action-specific-information"></a>Informacje o określonych akcji
<dl>
<dt>Wypychane aplikacji Azure</dt>
<dd>Może mieć maksymalnie 10 akcje aplikacji Azure w grupy akcji.</dd>
<dd>W tym momencie działania aplikacji Azure obsługuje tylko ServiceHealth alertów. Inne czasu alertu zostanie zignorowany. Zobacz [skonfigurować alerty, gdy powiadomienie usługi kondycji jest przesyłana](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>Adres e-mail</dt>
<dd>Może mieć maksymalnie 50 pocztą e-mail akcje w grupy akcji</dd>
<dd>Zobacz [szybkość Ograniczanie informacji](./monitoring-alerts-rate-limiting.md) artykułu</dd>

<dt>ZARZĄDZANIE USŁUGAMI IT —</dt>
<dd>Może mieć maksymalnie 10 akcje Zarządzanie usługami IT — w grupie akcji</dd>
<dd>Zarządzanie usługami IT — akcja wymaga połączenia Zarządzanie usługami IT —. Dowiedz się, jak utworzyć [połączenia Zarządzanie usługami IT —](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>Aplikacji logiki</dt>
<dd>Może mieć maksymalnie 10 działania aplikacji logiki w grupy akcji</dd>

<dt>Element runbook</dt>
<dd>Może mieć maksymalnie 10 działania elementu Runbook w grupy akcji</dd>

<dt>SMS</dt>
<dd>Może mieć maksymalnie 10 akcje programu SMS w grupy akcji</dd>
<dd>Zobacz [szybkość Ograniczanie informacji](./monitoring-alerts-rate-limiting.md) artykułu</dd>
<dd>Zobacz [SMS alertów zachowanie](monitoring-sms-alert-behavior.md) artykułu</dd>

<dt>głosu</dt>
<dd>Może mieć maksymalnie 10 akcje głosowe w grupy akcji</dd>
<dd>Zobacz [szybkość Ograniczanie informacji](./monitoring-alerts-rate-limiting.md) artykułu</dd>

<dt>Element Webhook</dt>
<dd>Może mieć maksymalnie 10 Akcje elementu Webhook w grupy akcji
<dd>Logika - ponawiania próby wywołania elementu webhook zostanie ponowiona maksymalnie 3 razy podczas następujące kody stanu HTTP są zwracane: 408 429, 503, 504</dd>
</dl>

## <a name="manage-your-action-groups"></a>Zarządzanie grupami akcji ##
Po utworzeniu grupy akcji jest widoczna w **grupy akcji** sekcji **Monitor** bloku. Wybierz grupę akcji, którą chcesz zarządzać, aby:

* Dodawanie, edytowanie lub usuwanie akcji.
* Usuwanie grupy działań.

## <a name="next-steps"></a>Kolejne kroki ##
* Dowiedz się więcej o [SMS alertów zachowanie](monitoring-sms-alert-behavior.md).  
* Uzyskaj [zrozumienia schemat alertu elementu webhook dziennika aktywności](monitoring-activity-log-alerts-webhook.md).  
* Dowiedz się więcej o [Zarządzanie usługami IT — łącznika](../log-analytics/log-analytics-itsmc-overview.md)
* Dowiedz się więcej o [limitów szybkości](monitoring-alerts-rate-limiting.md) alertów.
* Pobierz [Przegląd alertów dotyczących działań w dzienniku](monitoring-overview-alerts.md)i dowiedzieć się, jak otrzymywać alerty.  
* Dowiedz się, jak [skonfigurować alerty, gdy powiadomienie usługi kondycji jest przesyłana](monitoring-activity-log-alerts-on-service-notifications.md).
