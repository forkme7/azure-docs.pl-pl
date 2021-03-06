> [!div class="op_single_selector"]
> * [C w systemie Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C w systemie Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (ogólny)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Node.js na urządzeniu Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [C na urządzeniu Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-c.md)

W tym samouczku zaimplementowaniem **Chłodnica** urządzenia, które wysyła następujące dane telemetryczne do monitorowania zdalnego [wstępnie skonfigurowane rozwiązanie](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md):

* Temperatura
* wykorzystania
* Wilgotność

Dla uproszczenia kodu generuje przykładowe wartości telemetrii **Chłodnica**. Można rozszerzyć próbki przez połączenie czujników rzeczywistego urządzenia i wysyła rzeczywistych danych telemetrycznych.

Urządzenie próbki również:

* Wysyła metadane do rozwiązania do opisywania jego możliwości.
* Odpowiada na działania wywoływane z **urządzeń** strony w rozwiązaniu.
* Wyślij odpowiada na zmiany w konfiguracji z **urządzeń** strony w rozwiązaniu.

Do wykonania kroków tego samouczka jest potrzebne aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed przystąpieniem do napisania kodu dla danego urządzenia, wdrożyć zdalnego monitorowania wstępnie skonfigurowane rozwiązanie i dodać nowe urządzenie fizyczne do rozwiązania.

### <a name="deploy-your-remote-monitoring-preconfigured-solution"></a>Wdrażanie zdalnego wstępnie skonfigurowane rozwiązanie monitorowania

**Chłodnica** urządzenia, możesz utworzyć w tym samouczku wysyłania danych do wystąpienia [monitorowania zdalnego](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) wstępnie skonfigurowane rozwiązanie. Jeśli nie zostało już wstępnie skonfigurowane zdalnego wstępnie skonfigurowane rozwiązanie monitorowania w konta platformy Azure, zobacz [wdrożyć zdalnego wstępnie skonfigurowane rozwiązanie monitorowania](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

Podczas procesu wdrażania dla zdalnego rozwiązanie monitorowania zakończy, kliknij przycisk **uruchamianie** aby otworzyć pulpit nawigacyjny rozwiązania w przeglądarce.

![Pulpit nawigacyjny rozwiązania](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Dodaj urządzenia do zdalnego rozwiązanie monitorowania

> [!NOTE]
> Jeśli masz już dodany urządzenia w rozwiązaniu, możesz pominąć ten krok. Parametry połączenia urządzenia wymaga jednak następnego kroku. Można pobrać parametrów połączenia do urządzenia z [portalu Azure](https://portal.azure.com) lub przy użyciu [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) narzędzia interfejsu wiersza polecenia.

Aby urządzenie mogło nawiązać połączenie ze wstępnie skonfigurowanym rozwiązaniem, musi ono zidentyfikować się względem usługi IoT Hub za pomocą prawidłowych poświadczeń. Użytkownik ma możliwość zapisania rozwiązania parametry połączenia urządzenia, które zawiera te poświadczenia podczas dodawania urządzenia. Ciąg połączenia urządzenia możesz uwzględnić w dalszej części tego samouczka aplikacji klienta.

Aby dodać urządzenie do zdalnego rozwiązanie monitorowania, wykonaj następujące czynności na **urządzeń** strony w rozwiązaniu:

1. Wybierz **+ nowe urządzenie**, a następnie wybierz pozycję **fizycznych** jako **typu urządzenia**:

    ![Dodaj urządzenia fizycznego](media/iot-suite-selector-connecting/devicesprovision.png)

1. Wprowadź **fizyczna Chłodnica** jako identyfikator urządzenia. Wybierz **klucza symetrycznego** i **automatycznego generowania kluczy** opcje:

    ![Wybierz opcje urządzenia](media/iot-suite-selector-connecting/devicesoptions.png)

1. Wybierz **zastosować**. Następnie zanotuj **identyfikator urządzenia**, **klucza podstawowego**, i **klucza podstawowego połączenia ciąg** wartości:

    ![Pobieranie poświadczeń](media/iot-suite-selector-connecting/credentials.png)

Zostało teraz dodane urządzenie fizyczne do zdalnego wstępnie skonfigurowane rozwiązanie monitorowania i oznaczane w parametrach połączenia urządzenia. W poniższych sekcjach można zaimplementować aplikacji klienckiej, która używa ciągu połączenia urządzenia do łączenia się rozwiązania.

Aplikacja kliencka implementuje wbudowane **Chłodnica** model urządzenia. Model urządzenia wstępnie skonfigurowane rozwiązanie określa następujące informacje o urządzeniu:

* Właściwości urządzenia raporty dla rozwiązania. Na przykład **Chłodnica** urządzenie raportuje informacje o jego oprogramowania układowego i lokalizacji.
* Typy danych telemetrycznych urządzenie wysyła do rozwiązania. Na przykład **Chłodnica** temperatury i wilgotności oraz wartości wykorzystania wysłania przez urządzenie.
* Metody można zaplanować z rozwiązania do uruchomienia na urządzeniu. Na przykład **Chłodnica** urządzenia musi implementować **ponowny rozruch**, **FirmwareUpdate**, **EmergencyValveRelease**, i  **IncreasePressure** metody.