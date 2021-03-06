---
title: Przewodnik Szybki start platformy Azure — tworzenie obiektu blob w magazynie obiektów przy użyciu języka Go | Microsoft Docs
description: Ten przewodnik Szybki start przedstawia tworzenie konta magazynu i kontenera w magazynie obiektów (blob). Następnie przy użyciu biblioteki klienta języka Go przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz listę obiektów blob w kontenerze.
services: storage
author: seguler
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: seguler
ms.openlocfilehash: 171cfaadce4bdafeadfdc26dedaedbba3025bd3c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą języka Go

W tym przewodniku Szybki start dowiesz się, w jaki sposób za pomocą języka programowania Go przekazywać, pobierać i wyświetlać listę blokowych obiektów blob w kontenerze usługi Azure Blob Storage. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start: 
* Zainstaluj program [Go 1.8 lub nowszy](https://golang.org/dl/).
* Pobierz i zainstaluj [zestaw SDK usługi Azure Storage Blob dla języka Go](https://github.com/azure/azure-storage-blob-go/) za pomocą polecenia `go get -u github.com/azure/azure-storage-blob-go/2016-05-31/azblob`. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji
[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) używana w tym przewodniku Szybki start to podstawowa aplikacja w języku Go.  

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć przykładową aplikację w języku Go do obsługi usługi Blob Storage, poszukaj pliku storage-quickstart.go.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu
To rozwiązanie wymaga, aby nazwa i klucz konta magazynu były bezpiecznie przechowywane w zmiennych środowiskowych lokalnych względem komputera, na którym działa aplikacja przykładowa. Postępuj zgodnie z jednym z poniższych przykładów w zależności od używanego systemu operacyjnego, aby utworzyć zmienne środowiskowe.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej
Ta aplikacja przykładowa tworzy plik testowy w bieżącym folderze, przekazuje ten plik do usługi Blob Storage, wyświetla listę obiektów blob w kontenerze i pobiera plik do bufora. 

Aby uruchomić próbki, wykonaj następujące polecenie: 

```go run storage-quickstart.go```

Poniższej przedstawiono przykładowe dane wyjściowe zwracane po uruchomieniu aplikacji:
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Po naciśnięciu klawisza w celu kontynuowania aplikacja przykładowa usunie kontener magazynu i pliki. 

> [!TIP]
> Możesz również wyświetlić pliki w usłudze Blob Storage za pomocą narzędzia takiego jak [Eksplorator usługi Azure Storage](http://storageexplorer.com). Eksplorator usługi Azure Storage to darmowe narzędzie międzyplatformowe, które umożliwia dostęp do informacji na koncie magazynu. 
>

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

W kolejnej części omówimy przykładowy kod, aby wyjaśnić, w jaki sposób działa.

### <a name="create-containerurl-and-bloburl-objects"></a>Tworzenie obiektów ContainerURL i BlobURL
Najpierw należy utworzyć odwołania do obiektów ContainerURL i BlobURL używane w celu uzyskania dostępu do usługi Blob Storage i zarządzania nią. Te obiekty oferują interfejsy API niskiego poziomu, takie jak Create, PutBlob i GetBlob, na potrzeby wywoływania interfejsów API REST.

* Użyj struktury **SharedKeyCredential** do przechowywania poświadczeń. 

* Utwórz **potok** przy użyciu poświadczeń i opcji. Potok określa zasady ponawiania, rejestrowanie, deserializacji ładunków odpowiedzi HTTP i inne elementy.  

* Utwórz wystąpienie nowego obiektu ContainerURL i nowego obiektu BlobURL umożliwiające uruchamianie operacji na kontenerze (Create) i obiektach blob (PutBlob i GetBlob).


Po utworzeniu obiektu ContainerURL możesz utworzyć wystąpienie obiektu **BlobURL**, który wskazuje obiekt blob, i wykonać operacje, takie jak przekazywanie, pobieranie i kopiowanie.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

W tej sekcji utworzysz nowy kontener. Kontener nosi nazwę **quickstartblobs-[losowy ciąg]**. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential := azblob.NewSharedKeyCredential(accountName, accountKey)
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err := containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Blokowe obiekty blob są używane najczęściej i dlatego zostały użyte w tym przewodniku Szybki start.  

Aby przekazać plik do obiektu blob, otwórz plik przy użyciu metody **os.Open**. Następnie można przekazać ten pliku do określonej ścieżki przy użyciu jednego z interfejsów API REST: PutBlob, PutBlock/PutBlockList. 

Zestaw SDK udostępnia również [interfejsy API wysokiego poziomu](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go), które bazują na interfejsach API REST niskiego poziomu. Na przykład funkcja ***UploadFileToBlockBlob*** używa operacji PutBlock w celu współbieżnego przekazania pliku we fragmentach, aby zoptymalizować przepływność. Jeśli plik ma rozmiar mniejszy niż 256 MB, zamiast tej operacji używana jest metoda PutBlob, która kończy transfer w ramach jednej transakcji.

Następujący kod przykładowy przekazuje plik do kontenera o nazwie **quickstartblobs-[losowy ciąg]**.

```go
// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level PutBlob API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that PutBlob can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/en-us/rest/api/storageservices/put-blob
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.PutBlob(ctx, file, azblob.BlobHTTPHeaders{}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls PutBlock/PutBlockList for files larger 256 MBs, and calls PutBlob for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Pobierz listę plików w kontenerze, używając metody **ListBlobs** względem adresu **ContainerURL**. Metoda ListBlobs zwraca pojedynczy segment obiektów blob (maksymalnie 5000), począwszy od określonego **znacznika**. Użyj pustego znacznika, aby rozpocząć wyliczanie od początku. Nazwy obiektów blob są zwracane w porządku leksykograficznym. Po otrzymaniu segmentu przetwórz go, a następnie ponownie wywołaj metodę ListBlobs, przekazując do niej zwrócony poprzednio znacznik.  

```go
// List the blobs in the container
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobs(ctx, marker, azblob.ListBlobsOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Blobs.Blob {
        fmt.Print("Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Pobieranie obiektu blob

Pobierz obiekty blob za pomocą metody niskiego poziomu **GetBlob** z użyciem obiektu BlobURL. Możesz również utworzyć strumień i odczytać z niego zakresy przy użyciu interfejsu API wysokiego poziomu **NewDownloadStream** udostępnionego w pliku [highlevel.go](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go). Funkcja NewDownloadStream ponawia próbę w przypadku awarii połączenia, a interfejs API Get Blob ponawia próbę tylko po napotkaniu kodów stanu, na przykład 503 (Serwer zajęty). Poniższy kod pobiera obiekt blob przy użyciu funkcji **NewDownloadStream**. Zawartość obiektu blob jest zapisywana w buforze i wyświetlana w konsoli.

```go
// Here's how to download the blob. NOTE: This method automatically retries if the connection fails
// during download (the low-level GetBlob function does NOT retry errors when reading from its stream).
stream := azblob.NewDownloadStream(ctx, blobURL.GetBlob, azblob.DownloadStreamOptions{})
downloadedData := &bytes.Buffer{}
_, err = downloadedData.ReadFrom(stream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie potrzebujesz już obiektów blob przekazanych podczas pracy z tym przewodnikiem Szybki start, możesz usunąć cały kontener, korzystając z metody **Delete**. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press the enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>Zasoby używane do tworzenia aplikacji języka Go z obiektami blob

Zobacz dodatkowe zasoby używane podczas tworzenia aplikacji języka Go przy użyciu usługi Blob Storage:

- W witrynie GitHub wyświetl [kod źródłowy biblioteki klienta Go](https://github.com/Azure/azure-storage-blob-go) dla usługi Azure Storage i zainstaluj go.
- Zapoznaj się z [przykładami magazynu Blob Storage](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob#pkg-examples) napisanymi przy użyciu biblioteki klienta Go.

## <a name="next-steps"></a>Następne kroki
 
W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu języka Go. Aby uzyskać więcej informacji o zestawie SDK usługi Azure Storage Blob, przejrzyj [kod źródłowy](https://github.com/Azure/azure-storage-blob-go/) i [dokumentację interfejsu API](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob).
