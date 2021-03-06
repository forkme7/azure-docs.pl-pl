---
title: Azure Quickstart — przekazywanie, pobieranie i wyświetlanie listy obiektów blob w usłudze Azure Storage przy użyciu platformy Node.js | Microsoft Docs
description: W tym przewodniku Szybki start utworzysz konto magazynu i kontener. Następnie przy użyciu biblioteki klienta platformy Node.js przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz listę obiektów blob w kontenerze.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: cshoe
ms.openlocfilehash: 8783b83a1a94caf4a49f9da7a2dd30c9cb52df22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu platformy Node.js

Dzięki temu przewodnikowi Szybki start dowiesz się, w jaki sposób za pomocą środowiska Node.js przekazywać, pobierać i wyświetlać listę blokowych obiektów blob w kontenerze usługi Azure Blob Storage.

Do wykonania kroków tego przewodnika Szybki start jest potrzebna [subskrypcja platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) używana w tym przewodniku Szybki start to prosta aplikacja konsolowa Node.js. Aby rozpocząć, sklonuj repozytorium na swoją maszynę przy użyciu następującego polecenia:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Aby otworzyć tę aplikację, wyszukaj folder *storage-blobs-node-quickstart* i otwórz go w środowisku edycji kodu.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

Aby uruchomić aplikację, musisz wprowadzić parametry połączenia konta magazynu. Przykładowe repozytorium zawiera plik o nazwie *.env.example*. Zmień nazwę tego pliku, usuwając rozszerzenie *.example*, aby otrzymać plik o nazwie *.env*. W pliku *.env* dodaj wartość parametrów połączenia po kluczu *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

W katalogu aplikacji uruchom polecenie *npm install*, aby zainstalować pakiety wymagane przez aplikację.

```bash
npm install
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej
Po zainstalowaniu zależności możesz uruchomić aplikację przykładową, przekazując polecenia do skryptu. Na przykład do utworzenia kontenera obiektów blob służy następujące polecenie:

```bash
node index.js --command createContainer
```

Dostępne polecenia to:

| Polecenie | Opis |
|---------|---------|
|*createContainer* | Tworzy kontener o nazwie *test-container* (zostanie wykonane pomyślnie nawet jeśli kontener już istnieje) |
|*upload*          | Przekazuje plik *example.txt* do kontenera *test-container* |
|*download*        | Pobiera zawartość obiektu blob *example* do pliku *example.downloaded.txt* |
|*usuwanie*          | Usuwa obiekt blob *example* |
|*list*            | Wyświetla zawartość kontenera *test-container* w konsoli |


## <a name="understanding-the-sample-code"></a>Omówienie przykładowego kodu
Ten przykładowy kod korzysta z kilku modułów służących do połączenia interfejsem z systemem plików i wierszem polecenia. 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

Poniżej przedstawiono funkcje tych modułów: 

- *dotenv* — ładuje zmienne środowiskowe zdefiniowane w pliku *.env* do bieżącego kontekstu wykonania
- *path* — jest wymagany do określenia ścieżki bezwzględnej do pliku przekazywanego do magazynu obiektów blob
- *yargs* — udostępnia prosty interfejs umożliwiający dostęp do argumentów wiersza polecenia
- *azure-storage* jest modułem [zestawu SDK usługi Azure Storage](/nodejs/api/azure-storage) dla środowiska Node.js

Następnie jest inicjowanych kilka zmiennych:

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

Zmienne są ustawiane na następujące wartości:

- *blobService* jest ustawiana na nowe wystąpienie usługi Azure Blob Service
- *containerName* jest ustawiana na nazwę kontenera
- *sourceFilePath* jest ustawiana na ścieżkę bezwzględną do pliku, który ma zostać przekazany
- *blobName* jest tworzona na podstawie nazwy pliku, z której usunięto rozszerzenie

W poniższej implementacji każda funkcja *blobService* jest opakowana w obiekt *Promise*. Pozwala to korzystać z funkcji *async* i operatora *await* języka JavaScript w celu usprawnienia wywołań zwrotnych [interfejsu API usługi Azure Storage](/nodejs/api/azure-storage/blobservice). Jeśli poszczególne funkcje zwrócą pomyślne odpowiedzi, obiekt Promise wskazuje na właściwe dane i komunikat specyficzny dla akcji.

### <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob

Funkcja *createContainer* wywołuje funkcję [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) i ustawia odpowiedni poziom dostępu dla obiektu blob.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Drugi parametr — (*options*) — funkcji **createContainerIfNotExists** przyjmuje wartość [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists). Wartość *blob* parametru *publicAccessLevel* określa, że dane konkretnego obiektu blob są dostępne publicznie. To ustawienie jest inne niż poziom dostępu *container*, który umożliwia wyświetlanie zawartości kontenera.

Użycie funkcji **createContainerIfNotExists** pozwala aplikacji wielokrotnie uruchamiać polecenie *createContainer* bez zwracania błędów, jeśli kontener już istnieje. W środowisku produkcyjnym funkcja **createContainerIfNotExists** zwykle jest wywoływana tylko raz, ponieważ w całej aplikacji jest używany ten sam kontener. W takiej sytuacji można wcześniej utworzyć kontener za pośrednictwem portalu lub przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="upload-a-blob-to-the-container"></a>Przekazywanie obiektu blob do kontenera

Za pomocą funkcji [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) funkcja *upload* przekazuje plik z systemu plików do magazynu obiektów blob i go zapisuje lub nadpisuje. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
W kontekście aplikacji przykładowej plik o nazwie *example.txt* jest przekazywany do obiektu blob o nazwie *example* wewnątrz kontenera o nazwie *test-container*. Inne metody przekazywania zawartości do obiektów blob obejmują użycie [tekstu](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) i [strumieni](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream).

Aby sprawdzić, czy plik został przekazany do magazynu obiektów blob, można użyć [Eksploratora usługi Azure Storage](https://azure.microsoft.com/en-us/features/storage-explorer/) w celu wyświetlenia danych na koncie.

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Funkcja *list* wywołuje metodę [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) w celu zwrócenia listy metadanych obiektu blob w kontenerze. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

Metoda *listBlobsSegmented* zwraca metadane obiektu blob jako tablicę wystąpień klasy [BlobResult](/nodejs/api/azure-storage/blobresult). Wyniki są zwracane w partiach inkrementowanych o 5000 (segmenty). Jeśli kontener zawiera ponad 5000 obiektów blob, do wyników jest dołączana wartość **tokenu kontynuacji**. Aby wyświetlić listę kolejnych segmentów z kontenera obiektów blob, można przekazać token kontynuacji do metody **listBlobsSegmented** jako drugi argument.

### <a name="download-a-blob-from-the-container"></a>Pobieranie obiektu blob z kontenera

Za pomocą funkcji [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) funkcja *download* pobiera zawartość obiektu blob do danej ścieżki bezwzględnej do pliku.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
W przedstawionej implementacji następuje zmiana ścieżki do pliku źródłowego — do nazwy pliku jest dołączany człon *.downloaded.txt*. W rzeczywistych kontekstach można zmienić zarówno nazwę pliku, jak i docelową lokalizację pobierania.

### <a name="delete-blobs-in-the-container"></a>Usuwanie obiektów blob w kontenerze

Funkcja *deleteBlock* (aliasowana jako polecenie konsoli *delete*) wywołuje funkcję [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists). Jak sugeruje jej nazwa, ta funkcja nie zwraca błędu, jeśli obiekt blob został już usunięty.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>Przekazywanie i wyświetlanie listy

Jedną z zalet korzystania z obiektów Promise jest możliwość łączenia poleceń. Funkcja **uploadAndList** demonstruje, jak łatwo można utworzyć listę zawartości obiektu blob bezpośrednio po przekazaniu pliku.

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>Kod wywołujący

Każda z tych funkcji jest mapowana na literału obiektu, co pozwala udostępnić zaimplementowane funkcje w wierszu polecenia.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

Dzięki zdefiniowaniu stałej *_module* poszczególne polecenia są dostępne w wierszu polecenia.

```javascript
const commandExists = () => exists = !!_module[args.command];
```

Jeśli dane polecenie nie istnieje, właściwości stałej *_module* są wyświetlane w konsoli jako tekst pomocy dla użytkownika. 

Funkcja *executeCommand* to funkcja *async*, która wywołuje dane polecenie przy użyciu operatora *await* i rejestruje w konsoli komunikaty dotyczące danych.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

Na koniec w wykonywanym kodzie najpierw jest wywoływana stała *commandExists*, która umożliwia sprawdzenie, czy do skryptu jest przekazywane znane polecenie. Jeśli wybrano istniejące polecenie, jest ono uruchamiane, a wszelkie błędy są rejestrowane w konsoli.

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie zamierzasz używać danych ani kont utworzonych w tym artykule, możesz je usunąć, aby uniknąć naliczenia opłat. Obiekt blob i kontenery możesz usunąć za pomocą metod [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) i [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_). Możesz także usunąć konto magazynu [za pośrednictwem portalu](../common/storage-create-storage-account.md).

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Zasoby używane do tworzenia aplikacji Node.js z obiektami blob

Zobacz dodatkowe zasoby używane podczas tworzenia aplikacji Node.js z magazynem obiektów blob:

### <a name="binaries-and-source-code"></a>Pliki binarne i kod źródłowy

- W witrynie GitHub wyświetl [kod źródłowy biblioteki klienta Node.js](https://github.com/Azure/azure-storage-node) dla usługi Azure Storage i zainstaluj go.

### <a name="client-library-reference-and-samples"></a>Dokumentacja i przykłady dotyczące biblioteka klienta

- Aby uzyskać więcej informacji na temat biblioteki klienta Node.js, zobacz [dokumentację interfejsu API platformy Node.js](https://docs.microsoft.com/en-us/javascript/api/overview/azure/storage).
- Zapoznaj się z [przykładami użycia usługi Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) napisanymi przy użyciu biblioteki klienta Node.js.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono metodę przekazywania pliku między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu środowiska Node.js. Aby dowiedzieć się więcej na temat pracy z usługą Blob Storage, przejdź do instrukcji dotyczących magazynu obiektów blob.

> [!div class="nextstepaction"]
> [Instrukcje: Operacje wykonywane w usłudze Blob Storage](storage-nodejs-how-to-use-blob-storage.md)

Aby zapoznać się z dokumentacją języka Node.js dotyczącą usługi Azure Storage, zobacz [pakiet azure-storage](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).