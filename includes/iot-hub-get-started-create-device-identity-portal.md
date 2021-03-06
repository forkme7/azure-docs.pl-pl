---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/05/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b08bfcd4cb9e85f9e682efe0f599b6dd88897962
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
## <a name="create-a-device-identity"></a>Tworzenie tożsamości urządzenia

W tej sekcji użyjesz [portalu Azure] [ lnk-azure-portal] do tworzenia tożsamości urządzenia w rejestrze tożsamości w Centrum IoT. Urządzenie nie może połączyć się z centrum IoT, jeśli nie ma wpisu w rejestrze tożsamości. Więcej informacji znajduje się w sekcji „Identity registry” (Rejestr tożsamości) artykułu [IoT Hub Developer Guide][lnk-devguide-identity] (Usługa IoT Hub — przewodnik dewelopera). Użyj **urządzenia IoT** panelu w portalu, aby wygenerować unikatowy identyfikator urządzenia i klucz dla urządzenia w celu używania w celu zidentyfikowania się Centrum IoT. W identyfikatorach urządzeń jest uwzględniana wielkość liter.

1. Upewnij się, że użytkownik jest zalogowany do [portalu Azure][lnk-azure-portal].

1. Na pasku przechodzenia kliknij **wszystkie zasoby** i Znajdź zasobu Centrum IoT.

    ![Przejdź do Centrum Iot][img-find-iothub]

1. Po otwarciu zasobu Centrum IoT kliknij **urządzenia IoT** narzędzia, a następnie kliknij przycisk **Dodaj** u góry. Podaj nazwę dla nowego urządzenia, na przykład **myDeviceId**i kliknij przycisk **zapisać**.

    ![Tworzenie tożsamości urządzenia w portalu][img-create-device]

   Ta akcja tworzy nową tożsamość urządzenia Centrum IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. W **urządzenia IoT**na listę urządzeń, kliknij urządzenie nowo utworzona i zanotuj **ciąg połączenia---klucz podstawowy**.

    ![Ciąg połączenia urządzenia][img-connection-string]

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. Przechowuje identyfikatory urządzeń i klucze, które będą używane jako poświadczenia zabezpieczeń, oraz flagę włączone/wyłączone, która umożliwia wyłączenie dostępu do poszczególnych urządzeń. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Więcej informacji znajduje się w temacie [IoT Hub Developer Guide][lnk-devguide-identity] (Usługa IoT Hub — przewodnik dewelopera).

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

