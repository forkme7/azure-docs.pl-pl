---
title: Rozwiązywanie problemów z usługą Azure IoT Edge | Microsoft Docs
description: Rozwiązywanie typowych problemów i nabywanie umiejętności rozwiązywania problemów z usługą Azure IoT Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: article
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: b03ece52c4ff77c9e0abbc794325cd7e9a20c915
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Typowe problemy z usługą Azure IoT Edge i ich rozwiązania

Jeśli wystąpią problemy z uruchamianiem usługi Azure IoT Edge w danym środowisku, użyj tego artykułu jako przewodnika ułatwiającego rozwiązywanie problemów. 

## <a name="standard-diagnostic-steps"></a>Standardowe kroki diagnostyki 

W przypadku wystąpienia problemu dowiedz się więcej o stanie urządzenia usługi IoT Edge, przeglądając dzienniki kontenera i komunikaty przekazywane do i z urządzenia. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje. 

* Sprawdź dzienniki kontenerów Docker, aby wykryć problemy. Rozpocznij od wdrożonych kontenerów, a następnie sprawdź kontenery, które tworzą środowisko uruchomieniowe usługi IoT Edge: Agent usługi Edge i Centrum usługi Edge. Dzienniki agenta usługi Edge zwykle zawierają informacje o cyklu życia każdego kontenera. Dzienniki centrum usługi Edge zawierają informacje dotyczące obsługi komunikatów i routingu. 

   ```cmd
   docker logs <container name>
   ```

* Przejrzyj komunikaty przechodzące przez centrum usługi Edge i zbierz analizy dotyczące aktualizacji właściwości urządzenia za pomocą szczegółowych dzienników z kontenerów środowiska wykonawczego.

   ```cmd
   iotedgectl setup --connection-string "{device connection string}" --runtime-log-level debug
   ```
   
* Wyświetl pełne dzienniki za pomocą poleceń iotedgectl:

   ```cmd
   iotedgectl --verbose DEBUG <command>
   ```

* Jeśli wystąpią problemy z łącznością, zbadaj zmienne środowiskowe urządzenia Edge, takie jak parametry połączenia urządzenia:

   ```cmd
   docker exec edgeAgent printenv
   ```

Możesz także sprawdzić komunikaty przesyłane między usługą IoT Hub i urządzeniami usługi IoT Edge. Te komunikaty można wyświetlić przy użyciu rozszerzenia [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) programu Visual Studio Code. Więcej pomocy zawiera temat [Handy tool when you develop with Azure IoT (Przydatne narzędzie dla programowania w usłudze Azure IoT)](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

Po przeanalizowaniu dzienników i komunikatów pod kątem informacji możesz także spróbować uruchomić ponownie środowisko uruchomieniowe usługi Azure IoT Edge:

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Agent usługi Edge jest zatrzymywany po około minucie

Agent usługi Edge jest uruchamiany i działa poprawnie przez około minutę, a następnie jest zatrzymywany. Dzienniki wskazują, że agent usługi Edge próbuje nawiązać połączenie z usługą IoT Hub za pośrednictwem protokołu AMQP, a następnie po około 30 sekundach próbuje nawiązać połączenie za pomocą protokołu AMQP przez WebSocket. Jeśli to się nie powiedzie, agent usługi Edge kończy pracę. 

Przykład dzienników agenta usługi Edge:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Główna przyczyna
Konfiguracja sieci na hoście uniemożliwia agentowi usługi Edge dostęp do sieci. Agent najpierw próbuje nawiązać połączenie za pośrednictwem protokołu AMQP (port 5671). W przypadku niepowodzenia podejmuje próbę przy użyciu protokołu WebSocket (port 443).

Środowisko uruchomieniowe usługi IoT Edge konfiguruje sieć dla każdego z modułów na potrzeby komunikacji. W systemie Linux ta sieć jest siecią mostka. W systemie Windows jest używany translator adresów sieciowych. Ten problem występuje częściej na urządzeniach z systemem Windows używających kontenerów systemu Windows korzystających z sieci NAT. 

### <a name="resolution"></a>Rozwiązanie
Upewnij się, że istnieje trasa do Internetu dla adresów IP przypisanych do tej sieci mostka lub sieci NAT. Czasem konfiguracja sieci VPN na hoście przesłania sieć usługi IoT Edge. 

## <a name="edge-hub-fails-to-start"></a>Nie można uruchomić centrum usługi Edge

Nie można uruchomić centrum usługi Edge, a w dzienniku znajduje się następujący komunikat: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Główna przyczyna
Jakiś inny proces na komputerze hosta korzysta z portu 443. Centrum usługi Edge mapuje porty 5671 i 443 do użycia w scenariuszach z bramą. To mapowanie portów kończy się niepowodzeniem, jeśli inny proces jest już powiązany z portem 443. 

### <a name="resolution"></a>Rozwiązanie
Znajdź i zatrzymaj proces używający portu 443. Ten proces to zwykle serwer internetowy.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Agent usługi Edge nie ma dostępu do obrazu modułu (błąd 403)
Uruchomienie kontenera nie powodzi się, a w dziennikach agenta usługi Edge jest zgłaszany błąd 403. 

### <a name="root-cause"></a>Główna przyczyna
Agent usługi Edge nie ma uprawnień dostępu do obrazu modułu. 

### <a name="resolution"></a>Rozwiązanie
Spróbuj uruchomić ponownie polecenie `iotedgectl login`.

## <a name="iotedgectl-cant-find-docker"></a>Polecenie iotedgectl nie może znaleźć platformy Docker

Polecenia `iotedgectl setup` lub `iotedgectl start` zakończyć się niepowodzeniem i Drukuj komunikat do dzienników:
```output
File "/usr/local/lib/python2.7/dist-packages/edgectl/host/dockerclient.py", line 98, in get_os_type
  info = self._client.info()
File "/usr/local/lib/python2.7/dist-packages/docker/client.py", line 174, in info
  return self.api.info(*args, **kwargs)
File "/usr/local/lib/python2.7/dist-packages/docker/api/daemon.py", line 88, in info
  return self._result(self._get(self._url("/info")), True)
```

### <a name="root-cause"></a>Główna przyczyna
Polecenie iotedgectl nie może znaleźć platformy Docker, która jest wymaganiem wstępnym.

### <a name="resolution"></a>Rozwiązanie
Zainstaluj platformę Docker, upewnij się, że jest uruchomiona, i spróbuj ponownie.

## <a name="iotedgectl-setup-fails-with-an-invalid-hostname"></a>iotedgectl instalacja zakończy się niepowodzeniem z nieprawidłową nazwą hosta

Polecenie `iotedgectl setup` nie powiedzie się i wyświetla następujący komunikat: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Główna przyczyna
Środowisko uruchomieniowe krawędzi IoT może obsługiwać tylko nazwy hostów, które są krótsze niż 64 znaki. To zwykle nie jest problemem w przypadku komputerów fizycznych, ale może wystąpić podczas konfigurowania środowiska uruchomieniowego na maszynie wirtualnej. Automatycznie generowane nazwy hostów dla maszyn wirtualnych systemu Windows hostowana na platformie Azure, w szczególności, zwykle za długa. 

### <a name="resolution"></a>Rozwiązanie
Gdy zostanie wyświetlony ten błąd, można rozwiązać, konfigurowanie nazwę DNS maszyny wirtualnej, a następnie ustawić nazwę DNS jako nazwa hosta polecenia Instalatora.

1. W portalu Azure przejdź do strony Przegląd maszyny wirtualnej. 
2. Wybierz **skonfigurować** pod nazwą DNS. Maszyna wirtualna ma już skonfigurowaną nazwę DNS, nie trzeba skonfigurować nowy. 

   ![Konfigurowanie nazwy DNS](./media/troubleshoot/configure-dns.png)

3. Podaj wartość dla **etykieta nazwy DNS** i wybierz **zapisać**.
4. Skopiuj nowej nazwy DNS, która powinna być w formacie  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. Na maszynie wirtualnej Użyj następującego polecenia, aby skonfigurować środowisko uruchomieniowe krawędzi IoT z nazwą DNS:

   ```input
   iotedgectl setup --connection-string "<connection string>" --nopass --edge-hostname "<DNS name>"
   ```

## <a name="next-steps"></a>Kolejne kroki
Uważasz, że znaleziono usterkę platformy IoT Edge? [Prześlij problem](https://github.com/Azure/iot-edge/issues), aby umożliwić nam naprawę. 
