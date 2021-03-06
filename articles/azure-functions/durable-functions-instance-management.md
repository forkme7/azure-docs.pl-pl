---
title: Zarządzanie wystąpieniami w funkcjach trwałe - Azure
description: Dowiedz się, jak zarządzać wystąpień w rozszerzeniu trwałe funkcji dla usługi Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 01a6fefc10dfd83997acc290dbd1c85ba86a4799
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Zarządzanie wystąpieniami w funkcjach trwałe (usługi Azure Functions)

[Funkcje trwałe](durable-functions-overview.md) wystąpień orchestration można można uruchomić, zakończone zbadać i wysyłane zdarzenia powiadomień. Wszystkie wystąpienia zarządzania odbywa się przy użyciu [klienta aranżacji powiązanie](durable-functions-bindings.md). W tym artykule przechodzi w stan szczegóły każdej operacji zarządzania wystąpienia.

## <a name="starting-instances"></a>Uruchamianie wystąpienia

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metoda [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) uruchamia nowe wystąpienie funkcji programu orchestrator. Wystąpienia tej klasy można uzyskać za pomocą `orchestrationClient` powiązania. Wewnętrznie, ta metoda enqueues wiadomości do kolejki kontroli, następnie wyzwala początku funkcji o określonej nazwie, która używa `orchestrationTrigger` wyzwolenia powiązania. 

Zadanie kończy się po uruchomieniu procesu aranżacji. Rozpocząć procesu aranżacji w ciągu 30 sekund. Jeśli trwa dłużej, `TimeoutException` jest generowany. 

Parametry [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) są następujące:

* **Nazwa**: Nazwa funkcji programu orchestrator do zaplanowania.
* **Wejściowy**: wszystkie dane do serializacji JSON, który powinien zostać przekazany jako dane wejściowe do funkcji programu orchestrator.
* **Identyfikator wystąpienia**: (opcjonalnie) Unikatowy identyfikator wystąpienia. Jeśli nie zostanie określony, zostanie wygenerowany identyfikator wystąpienia losowej.

Poniżej przedstawiono prosty przykład C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

Dla języków .NET z systemem innym niż funkcja powiązania wyjściowego można uruchomić także nowego wystąpienia. W takim przypadku można użyć dowolnego obiektu podlegającego serializacji JSON, zawierający powyżej trzy parametry jako pola. Rozważmy na przykład następująca funkcja Node.js:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> Zalecane jest użycie losowy identyfikator identyfikatora wystąpienia. Zapewni dystrybucji obciążenia równe podczas skalowania funkcje programu orchestrator między wieloma maszynami wirtualnymi. Prawidłowego czasu na korzystanie z identyfikatorów — losowe wystąpienia jest identyfikator muszą pochodzić ze źródła zewnętrznego lub podczas implementowania [orchestrator pojedyncze](durable-functions-singletons.md) wzorca.

## <a name="querying-instances"></a>Wykonywanie zapytania wystąpień

[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metoda [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy zapytanie o stan wystąpienia aranżacji. Trwa `instanceId` (wymagane), `showHistory` (opcjonalnie) i `showHistoryOutput` (opcjonalnie) jako parametry. Jeśli `showHistory` ma ustawioną wartość `true`, odpowiedź będzie zawierać historii wykonywania. Jeśli `showHistoryOutput` ustawiono `true` jak również historię wykonywania będzie zawierać danych wyjściowych działania. Metoda zwraca obiekt z następującymi właściwościami:

* **Nazwa**: Nazwa funkcji programu orchestrator.
* **Identyfikator wystąpienia**: Identyfikatora wystąpienia orchestration (powinna być taka sama jak `instanceId` wejściowych).
* **CreatedTime**: czasu, jaką funkcja orchestrator uruchomienia.
* **LastUpdatedTime**: czas, w którym orchestration ostatnich użyciu.
* **Wejściowy**: dane wejściowe funkcji jako wartość JSON.
* **Dane wyjściowe**: dane wyjściowe funkcji jako wartość JSON (Jeśli funkcja zostało ukończone). Funkcji programu orchestrator nie powiodło się, ta właściwość będzie zawierać szczegóły niepowodzenia. Jeśli funkcja orchestrator zostało zakończone, ta właściwość będzie zawierać podana Przyczyna zakończenia (jeśli istnieje).
* **RuntimeStatus**: jeden z następujących wartości:
    * **Uruchomiona**: wystąpienie uruchomieniu.
    * **Ukończono**: wystąpienie zostało zakończone normalnie.
    * **ContinuedAsNew**: wystąpienie uruchomieniu się o nowych historii. Jest to stan przejściowy.
    * **Nie powiodło się**: wystąpienie nie powiodło się z powodu błędu.
    * **Zakończone**: wystąpienie zostało nagle przerwane.
* **Historia**: historii wykonywania orchestration. To pole jest wypełniane tylko, jeśli `showHistory` ma ustawioną wartość `true`.
    
Ta metoda zwraca `null` Jeśli wystąpienie nie istnieje lub nie została jeszcze uruchomiona uruchomiona.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Wystąpienie zapytania jest obecnie obsługiwany tylko w przypadku funkcji orchestrator C#.

## <a name="terminating-instances"></a>Trwa przerywanie działania wystąpień

Uruchomione wystąpienie orchestration można przerywać działanie przy użyciu [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy. Są dwa parametry `instanceId` i `reason` ciąg, który będą zapisywane do dzienników i stan wystąpienia. Zakończone wystąpienia spowoduje zatrzymanie uruchomiony zaraz po dotarciu serwerów do następnej `await` punktu lub zostanie zakończona natychmiast, jeśli jest już włączone `await`. 

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Przerwanie wystąpienia jest obecnie obsługiwany tylko w przypadku funkcji orchestrator C#.

> [!NOTE]
> Kończenie działania wystąpienia nie obecnie propaguje. Działania funkcji i podrzędne orchestrations będzie działać do zakończenia niezależnie od tego, czy wystąpienie aranżacji, które wywołuje ich zostało przerwane.

## <a name="sending-events-to-instances"></a>Wysyłanie zdarzeń do wystąpień

Mogą być wysyłane powiadomienia o zdarzeniach do uruchomienia wystąpień przy użyciu [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy. Wystąpienia, które może obsłużyć te zdarzenia są tymi, które oczekują na wywołanie [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

Parametry [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) są następujące:

* **Identyfikator wystąpienia**: Unikatowy identyfikator wystąpienia.
* **EventName**: Nazwa zdarzenia do wysłania.
* **EventData**: ładunku serializacji JSON do wysłania do wystąpienia.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Wywoływanie zdarzeń jest obecnie obsługiwane tylko dla funkcji programu orchestrator C#.

> [!WARNING]
> Jeśli żadne wystąpienie aranżacji z określonym *identyfikator wystąpienia* lub jeśli wystąpienie nie oczekuje na określonym *Nazwa zdarzenia*, komunikaty o zdarzeniach zostaną odrzucone. Aby uzyskać więcej informacji dotyczących tego zachowania, zobacz [problem GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="wait-for-orchestration-completion"></a>Czekaj na zakończenie aranżacji

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy ujawnia [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) interfejsu API, który może służyć do pobrania synchronicznie rzeczywiste dane wyjściowe z wystąpienia aranżacji. Metoda korzysta z wartości domyślnej 10 sekund `timeout` i 1 sekundę dla `retryInterval` gdy nie są ustawione.  

Oto przykład funkcja wyzwalacza HTTP, który demonstruje sposób użycia tego interfejsu API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

Funkcja może być wywołany z następującego wiersza przy użyciu 2 sekund limitu czasu i interwał ponawiania 0,5 sekundy:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Istnieją dwa przypadki, w zależności od czas potrzebny na odpowiedź z wystąpienia orchestration:

1. Wystąpień aranżacji ukończona w ciągu zdefiniowanego limitu czasu (w tym przypadku 2 sekundy), odpowiedź rzeczywiste aranżacji wystąpienia przekazywane są dostarczane synchronicznie:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. Wystąpienia orchestration nie może zakończyć w ciągu zdefiniowanego limitu czasu (w tym przypadku 2 sekundy), odpowiedź jest domyślnym, co opisano w **odnajdywania HTTP URL interfejsu API**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Format adresów URL elementu webhook mogą się różnić w zależności od posiadanej wersji programu host usługi Azure Functions. Powyższy przykład jest przeznaczony dla hosta Azure funkcji 2.0.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać interfejsów API HTTP dla wystąpienia zarządzania](durable-functions-http-api.md)
