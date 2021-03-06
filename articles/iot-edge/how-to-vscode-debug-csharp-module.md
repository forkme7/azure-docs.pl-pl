---
title: Debugowanie modułu C# z krawędzią IoT Azure przy użyciu programu Visual Studio Code | Dokumentacja firmy Microsoft
description: Debugowanie modułu C# przy użyciu krawędź IoT Azure w programie Visual Studio Code.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 65f2fb4526f1048ae88193f85a552a2202afa1d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Debugowanie modułu C# z krawędzią IoT Azure przy użyciu programu Visual Studio Code
Ten artykuł zawiera szczegółowe instrukcje dotyczące używania [Visual Studio Code](https://code.visualstudio.com/) jako Narzędzia główne programowanie debugowania moduły Azure IoT krawędzi.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenie brzegowe IoT może być inne urządzenie fizyczne lub urządzenia IoT krawędzi można symulować na komputerze deweloperskim.

Przed wykonaniem wskazówki zawarte w tym artykule, wykonaj kroki [opracowywania rozwiązań IoT Edge z wieloma modułami w programie Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Po wykonaniu tej powinny mieć gotowy następujące elementy:
- Lokalnego rejestru Docker uruchomiony na komputerze deweloperskim. Zalecane jest korzystanie lokalnego rejestru Docker prototypu i celów testowych. Można zaktualizować klucza rejestru kontenera `module.json` plik w folderze każdego modułu.
- Krawędź IoT rozwiązania obszar roboczy projektu z podfolderu modułu C# w nim.
- `Program.cs` Pliku z najnowszą kodu modułu.
- Uruchomiony na komputerze deweloperskim środowiska uruchomieniowego krawędzi.

## <a name="build-your-iot-edge-c-module-for-debugging"></a>Tworzenie modułu IoT krawędzi C# do debugowania
1. Aby rozpocząć debugowanie, należy użyć **Dockerfile.amd64.debug** Aby ponownie skompilować obraz docker i wykonaj ponowne wdrożenie rozwiązania krawędzi. W kodzie VS Eksploratora, przejdź do `deployment.template.json` pliku. Zaktualizuj adres URL obrazu funkcja przez dodanie `.debug` w końcu.

2. Skompiluj ponownie rozwiązanie. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi: rozwiązań IoT Edge kompilacji**.

3. W Eksploratorze urządzenia Centrum IoT Azure, kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a następnie wybierz **tworzenie wdrożenia dla urządzenie brzegowe**. Wybierz `deployment.json` w obszarze `config` folderu. Następnie można zauważyć, że pomyślnie utworzono wdrożenie z wdrożeniem, który zintegrowane Identyfikatora w kodzie VS terminala.

> [!NOTE]
> Można sprawdzić stan kontenera w Eksploratorze Docker kodu programu VS lub uruchom `docker images` w terminalu.

## <a name="start-debugging-c-module-in-vs-code"></a>Rozpocznij debugowanie modułu C# w kodzie VS
1. Informacje o konfiguracji w śledzi debugowanie kodu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania IoT krawędzi. I zostanie zaktualizowany po dodaniu każdego nowy moduł, który obsługuje debugowania. Przejdź do widoku debugowania, a następnie wybierz odpowiedni plik konfiguracji debugowania.
    ![Wybierz opcję debugowania konfiguracji](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Przejdź do adresu `program.cs`. Dodaj punkt przerwania w tym pliku.

3. Kliknij przycisk Rozpocznij debugowanie lub naciśnij klawisz **F5**i wybierz można dołączyć do procesu.

4. W widoku debugowania kodu VS widać zmiennych w lewym panelu. 

> [!NOTE]
> Poprzednim przykładzie pokazano, jak można debugować modułów krawędzi IoT Core .NET do kontenerów. Jest on oparty na wersji do debugowania z `Dockerfile.debug`, w tym VSDBG (debuger wiersza polecenia platformy .NET Core) w obrazie kontenera podczas jego tworzenia. Po zakończeniu debugowania modułów C#, zaleca się bezpośrednio użyć lub dostosować `Dockerfile` bez VSDBG dla modułów krawędzi IoT gotowe do produkcji.

## <a name="next-steps"></a>Kolejne kroki

[Użyj programu Visual Studio kodu do debugowania usługi Azure Functions krawędzi IoT Azure](how-to-vscode-debug-azure-function.md)

