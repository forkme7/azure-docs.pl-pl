---
title: Rozwiązywanie problemów z dołączonych dysków VHD na maszynach wirtualnych Azure Windows | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy, takie jak wirtualne dyski twarde dołączonych nieoczekiwane ponowne uruchomienie maszyn wirtualnych systemu Windows lub problemy podczas usuwania konta magazynu zawierającego.
keywords: SSH połączenia zostało odrzucone, ssh błędu, platforma azure ssh, połączenia SSH nie powiodło się
services: virtual-machines-windows
author: roygara
manager: jeconnoc
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/28/2018
ms.author: rogarana
ms.openlocfilehash: d0103d8ea608014e53f70402220b302b6da445e9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshoot-attached-vhds-on-azure-windows-virtual-machines"></a>Rozwiązywanie problemów z dołączonych dysków VHD na maszynach wirtualnych Azure Windows

Maszyny wirtualne platformy Azure polegają na wirtualne dyski twarde (VHD) dla dysku systemu operacyjnego oraz wszelkie dołączonych dysków z danymi. Wirtualne dyski twarde są przechowywane jako stronicowe obiekty BLOB w co najmniej jedno konto magazynu Azure. W tym artykule opisano sposoby rozwiązywania typowych problemów, które mogą wynikać z wirtualnych dysków twardych. 

  * [Nieoczekiwane ponowne uruchomienie maszyn wirtualnych z dołączonych dysków VHD]
  * [Magazyn usuwaniem we wdrożeniu usługi Resource Manager]

## <a name="you-are-experiencing-unexpected-reboots"></a>Nieoczekiwane ponowne uruchomienie maszyn wirtualnych z dołączonych dysków VHD

Jeśli maszyna wirtualna Azure (VM) jest duża liczba dołączonych dysków VHD, które znajdują się w tym samym koncie magazynu, może przekroczyć wartości docelowe skalowalności konta magazynu powoduje maszyna wirtualna może się nie powieść. Sprawdź minuty metryki dla konta magazynu (**TotalRequests**/**TotalIngress**/**TotalEgress**) maksymalnej, które przekraczają wartości docelowe skalowalności konta magazynu. Zobacz sekcję "[metryki pokazywanie wzrost PercentThrottlingError]" Aby uzyskać pomoc w określeniu, czy ograniczania wystąpił na koncie magazynu.

Ogólnie rzecz biorąc, każdej poszczególnych danych wejściowych lub wyjściowych operacji na wirtualny dysk twardy z maszyny wirtualnej przekłada się **Get strony** lub **umieścić strony** operacje na podstawowym stronicowych obiektów blob. W związku z tym umożliwia szacowany IOPS dla danego środowiska Dostosuj liczbę dysków VHD, użytkownik może mieć na koncie magazynu jednego na podstawie określone zachowanie aplikacji. Firma Microsoft zaleca o 40 lub mniejszą liczbę dysków na koncie magazynu jednego. Zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](../../storage/common/storage-scalability-targets.md) szczegółowe informacje o wartości docelowe skalowalności w przypadku kont magazynu, w szczególności liczby całkowitej żądań i łączną przepustowość dla typu konta magazynu są używane.

Jeśli doszło do przekroczenia wartości docelowe skalowalności konta magazynu, należy umieścić dyski VHD w wielu kont magazynu w celu ograniczenia działania w poszczególnych poszczególnych kont.

## <a name="storage-delete-errors-in-rm"></a>Magazyn usuwaniem we wdrożeniu usługi Resource Manager

Ta sekcja zawiera wskazówki dotyczące rozwiązywania problemów, gdy jeden z następujących błędów podczas próby usunięcia konta magazynu platformy Azure, kontenera lub obiektu blob we wdrożeniu usługi Azure Resource Manager.

>**Nie można usunąć konta magazynu "StorageAccountName". Błąd: Nie można usunąć konta magazynu z powodu jego artefakty są nadal używane.**

>**Nie można usunąć # poza kontenerów #:<br>wirtualne dyski twarde: obecnie jest dzierżawa na kontenerze i identyfikator dzierżawy nie został określony w żądaniu.**

>**Nie można usunąć # poza # obiektów blob:<br>BlobName.vhd: jest obecnie dzierżawy w obiekcie blob i identyfikator dzierżawy nie został określony w żądaniu.**

Pliki VHD używane w maszynach wirtualnych platformy Azure są pliki VHD przechowywany jako stronicowe obiekty BLOB na koncie magazynu standard lub premium na platformie Azure.  Więcej informacji o dyskach platformy Azure można znaleźć [tutaj](../../virtual-machines/windows/about-disks-and-vhds.md). Azure uniemożliwia usuwanie dysku, który jest dołączony do maszyny Wirtualnej, aby zapobiec uszkodzeniu. Uniemożliwia także usunięcie kontenery i kont magazynu, które mają stronicowych obiektów blob, który jest dołączony do maszyny Wirtualnej. 

Proces usuwania konta magazynu, kontenera lub obiektu blob podczas odbierania jeden z tych błędów jest: 
1. [Zidentyfikować obiekty BLOB dołączony do maszyny Wirtualnej](#step-1-identify-blobs-attached-to-a-vm)
2. [Dołączony maszyn wirtualnych DELETE o **dysku systemu operacyjnego**](#step-2-delete-vm-to-detach-os-disk)
3. [Odłącz wszystkich **dyski danych** z pozostałych wirtualne](#step-3-detach-data-disk-from-the-vm)

Po wykonaniu tych kroków, ponów próbę usuwania konta magazynu, kontenera lub obiektu blob.

### <a name="step-1-identify-blob-attached-to-a-vm"></a>: Krok 1 obiektów blob dołączony do maszyny Wirtualnej

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scenariusz 1: Usuwanie obiektu blob — zidentyfikować podłączone maszyny Wirtualnej
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz **wszystkie zasoby**. Przejdź do konta magazynu, w obszarze **usługa Blob** wybierz **kontenery**i przejdź do obiektu blob do usunięcia.
3. Jeśli obiekt blob **stanu dzierżawy** jest **Leased**, kliknij prawym przyciskiem myszy i wybierz **metadanych Edytuj** aby otworzyć okienko metadane obiektu Blob. 

    ![Zrzut ekranu przedstawiający portal, z magazynem konta obiekty BLOB i kliknij prawym przyciskiem myszy > "Edytuj metadanych" wyróżnione](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. W okienku Metadane obiektu Blob, sprawdź i Zapisz tę wartość dla **MicrosoftAzureCompute_VMName**. Ta wartość jest nazwą dołączonego wirtualnego dysku twardego do maszyny wirtualnej. (Zobacz **ważne** Jeśli to pole nie istnieje)
5. W okienku Metadane obiektu Blob, sprawdź i rejestrować wartości **MicrosoftAzureCompute_DiskType**. Identyfikuje tę wartość, jeśli dołączone dysk jest dyskiem systemu operacyjnego lub danych (zobacz **ważne** Jeśli to pole nie istnieje). 

     ![Zrzut ekranu przedstawiający portal, otwórz okienko "Metadane obiektu Blob" magazynu](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Jeśli jest typu obiektu blob dysku **OSDisk** wykonaj [krok 2: usuwanie maszyny Wirtualnej, aby odłączyć dysk systemu operacyjnego](#step-2-delete-vm-to-detach-os-disk). W przeciwnym razie, jeśli jest typu obiektu blob dysku **DataDisk** postępuj zgodnie z instrukcjami [krok 3: odłączyć dysku danych z maszyny Wirtualnej](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Jeśli **MicrosoftAzureCompute_VMName** i **MicrosoftAzureCompute_DiskType** nie zostaną wyświetlone metadane obiektu blob wskazuje, czy obiekt blob jest jawnie dzierżawiony i nie jest dołączony do maszyny Wirtualnej. Nie można usunąć dzierżawionych obiekty BLOB bez przerywania pierwszy dzierżawy. Aby przerwać dzierżawy, kliknij prawym przyciskiem myszy w obiekcie blob i wybierz **dzierżawy podziału**. Dzierżawionych obiektów blob, które nie są dołączone do maszyn wirtualnych zapobiec usunięciu obiektu blob, ale nie uniemożliwiają usunięcie konta kontener lub magazynu.

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scenariusz 2: Usuwanie kontenera - zidentyfikować wszystkie blob(s) w kontenerze, które są dołączone do maszyn wirtualnych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz **wszystkie zasoby**. Przejdź do konta magazynu, w obszarze **usługa Blob** wybierz **kontenery**oraz zapoznać się z kontenera do usunięcia.
3. Kliknij, aby otworzyć kontenera i zostanie wyświetlona lista obiektów blob w nim. Zidentyfikuj wszystkie obiekty BLOB z typem obiektu Blob = **stronicowych obiektów blob** i stanu dzierżawy = **Leased** z tej listy. Postępuj zgodnie z [scenariusz 1](#step-1-identify-blobs-attached-to-a-vm) do identyfikowania maszyny Wirtualnej skojarzone z każdą z tych obiektów blob.

    ![Zrzut ekranu przedstawiający portal, obiektów blob z konta magazynu i "Dzierżawy stanu" z "Leased" wyróżnione](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Postępuj zgodnie z [krok 2](#step-2-delete-vm-to-detach-os-disk) i [kroku 3](#step-3-detach-data-disk-from-the-vm) usunąć wirtualne z **OSDisk** i odłączania **DataDisk**. 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scenariusz 3: Usuwanie magazynu konta — zidentyfikować wszystkie blob(s) w ramach konta magazynu, które są dołączone do maszyn wirtualnych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz **wszystkie zasoby**. Przejdź do konta magazynu, w obszarze **usługa Blob** wybierz **kontenery**.

    ![Zrzut ekranu przedstawiający portal, kontenery konta magazynu i "Dzierżawy stanu" z "Leased" wyróżnione](./media/troubleshoot-vhds/utd-containers-sm.png)

3. W **kontenery** okienku zidentyfikować wszystkich kontenerów gdzie **stanu dzierżawy** jest **Leased** i postępuj zgodnie z [2 scenariusza](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) dla każdego  **Wydzierżawionym** kontenera.
4. Postępuj zgodnie z [krok 2](#step-2-delete-vm-to-detach-os-disk) i [kroku 3](#step-3-detach-data-disk-from-the-vm) usunąć wirtualne z **OSDisk** i odłączania **DataDisk**. 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>Krok 2: Usuwanie maszyny Wirtualnej można odłączyć dysku systemu operacyjnego
Jeśli wirtualny dysk twardy, dysk systemu operacyjnego, należy usunąć maszynę Wirtualną przed usunięciem dołączonego wirtualnego dysku twardego. Żadne dodatkowe działania jest wymagana w przypadku dyskach danych dołączonych do tej samej maszyny Wirtualnej, po wykonaniu tych kroków:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz **maszyn wirtualnych**.
3. Wybierz wirtualny dysk twardy dołączony do maszyny Wirtualnej.
4. Upewnij się, że nic nie jest aktywnie przy użyciu maszyny wirtualnej i nie są już potrzebne maszyny wirtualnej.
5. W górnej części **szczegóły maszyny wirtualnej** okienku wybierz **usunąć**, a następnie kliknij przycisk **tak** o potwierdzenie.
6. Maszyna wirtualna powinien zostać usunięty, ale wirtualnego dysku twardego mogą być przechowywane. Jednak dysku VHD nie powinien być dołączony do maszyny Wirtualnej lub ma dzierżawę na nim. Może upłynąć kilka minut, aż dzierżawa do zwolnienia. Aby sprawdzić zwolnieniu dzierżawy, przejdź do lokalizacji obiektu blob i w **właściwości obiektu Blob** okienku **stanu dzierżawy** powinien być **dostępne**.

### <a name="step-3-detach-data-disk-from-the-vm"></a>Krok 3: Odłączyć dysku danych z maszyny Wirtualnej
Jeśli wirtualny dysk twardy jest dysk z danymi, odłącz wirtualny dysk twardy z maszyny Wirtualnej, aby usunąć dzierżawy:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz **maszyn wirtualnych**.
3. Wybierz wirtualny dysk twardy dołączony do maszyny Wirtualnej.
4. Wybierz **dysków** na **szczegóły maszyny wirtualnej** okienka.
5. Wybierz dysk danych do usunięcia wirtualny dysk twardy jest podłączony do. Można określić, które obiekt blob jest dołączony na dysku, sprawdzając adres URL dysku VHD.
6. Sprawdź lokalizacji obiektu blob, klikając na dysku, aby zaewidencjonować ścieżka **identyfikator URI dysku VHD** pola.
7. Wybierz **Edytuj** na wierzchu z **dysków** okienka.
8. Kliknij przycisk **odłączyć ikona** dysku danych do usunięcia.

     ![Zrzut ekranu przedstawiający portal, otwórz okienko "Metadane obiektu Blob" magazynu](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Wybierz pozycję **Zapisz**. Teraz odłączono dysk od maszyny Wirtualnej, i wirtualny dysk twardy jest już dzierżawy. Może upłynąć kilka minut, aż dzierżawa do zwolnienia. Aby sprawdzić zwolnienie dzierżawy, przejdź do lokalizacji obiektu blob i w **właściwości obiektu Blob** okienku **stanu dzierżawy** wartość powinna być **odblokowany** lub **Dostępne**.

[Nieoczekiwane ponowne uruchomienie maszyn wirtualnych z dołączonych dysków VHD]: #you-are-experiencing-unexpected-reboots
[Magazyn usuwaniem we wdrożeniu usługi Resource Manager]: #storage-delete-errors-in-rm