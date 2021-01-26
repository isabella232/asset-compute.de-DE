---
title: Machen Sie sich mit der Funktionsweise einer benutzerdefinierten Anwendung vertraut.
description: Interne Funktionsweise einer benutzerdefinierten  [!DNL Asset Compute Service] -Anwendung, um deren Funktionsweise besser zu verstehen.
translation-type: ht
source-git-commit: d26ae470507e187249a472ececf5f08d803a636c
workflow-type: ht
source-wordcount: '751'
ht-degree: 100%

---


# Interne Funktionsweise einer benutzerdefinierten Anwendung {#how-custom-application-works}

Verwenden Sie die folgende Abbildung, um den durchgängigen Workflow zu verstehen, wenn ein digitales Asset mithilfe einer benutzerdefinierten Anwendung von einem Client verarbeitet wird.

![Workflow für benutzerdefinierte Anwendungen](assets/customworker.png)

*Abbildung: Schritte zur Verarbeitung eines Assets mit [!DNL Asset Compute Service].*

## Registrierung {#registration}

Der Client muss [`/register`](api.md#register) vor der ersten Anfrage an [`/process`](api.md#process-request) einmal aufrufen, um die Journal-URL für den Empfang von [!DNL Adobe I/O]-Ereignissen für Adobe Asset Compute einzurichten und abzurufen.

```sh
curl -X POST \
  https://asset-compute.adobe.io/register \
  -H "x-ims-org-id: $ORG_ID" \
  -H "x-gw-ims-org-id: $ORG_ID" \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "x-api-key: $API_KEY"
```

Die JavaScript-Bibliothek [`@adobe/asset-compute-client`](https://github.com/adobe/asset-compute-client#usage) kann in NodeJS-Anwendungen verwendet werden, um alle erforderlichen Schritte von der Registrierung über die Verarbeitung bis zur asynchronen Ereignisbehandlung auszuführen. Weitere Informationen zu den erforderlichen Kopfzeilen finden Sie unter [Authentifizierung und Autorisierung](api.md).

## Verarbeitung {#processing}

Der Client sendet eine [Verarbeitungsanfrage](api.md#process-request).

```sh
curl -X POST \
  https://asset-compute.adobe.io/process \
  -H "x-ims-org-id: $ORG_ID" \
  -H "x-gw-ims-org-id: $ORG_ID" \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "x-api-key: $API_KEY" \
  -d "<RENDITION_JSON>
```

Der Client ist für die korrekte Formatierung der Ausgabedarstellungen mit vorab signierten URLs verantwortlich. Die JavaScript-Bibliothek [`@adobe/node-cloud-blobstore-wrapper`](https://github.com/adobe/node-cloud-blobstore-wrapper#presigned-urls) kann in NodeJS-Anwendungen zum Vorsignieren von URLs verwendet werden. Derzeit unterstützt die Bibliothek nur Azure Blob-Speicher und AWS S3-Container.

Die Verarbeitungsanfrage gibt eine `requestId` zurück, die für die Abfrage von [!DNL Adobe I/O]-Ereignissen verwendet werden kann.

Nachfolgend finden Sie eine Beispielanfrage zur Verarbeitung benutzerdefinierter Anwendungen.

```json
{
    "source": "https://www.adobe.com/some-source-file.jpg",
    "renditions" : [
        {
            "worker": "https://my-project-namespace.adobeioruntime.net/api/v1/web/my-namespace-version/my-worker",
            "name": "rendition1.jpg",
            "target": "https://some-presigned-put-url-for-rendition1.jpg",
        }
    ],
    "userData": {
        "my-asset-id": "1234567890"
    }
}
```

[!DNL Asset Compute Service] sendet die Ausgabedarstellungsanfragen für die benutzerdefinierte Anwendung an die benutzerdefinierte Anwendung. Es wird ein HTTP-POST an die angegebene Anwendungs-URL verwendet, bei der es sich um die gesicherte Web-Aktions-URL von Project Firefly handelt. Alle Anfragen verwenden das HTTPS-Protokoll, um die Datensicherheit zu maximieren.

Das von einer benutzerdefinierten Anwendung verwendete [Asset Compute-SDK](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk) verarbeitet die HTTP-POST-Anfrage. Es übernimmt auch das Herunterladen der Quelle, das Hochladen von Ausgabedarstellungen, das Senden von [!DNL Adobe I/O]-Ereignissen und die Fehlerbehandlung.

<!-- TBD: Add the application diagram. -->

### Anwendungs-Code {#application-code}

Benutzerdefinierter Code muss nur einen Callback bereitstellen, der die lokal verfügbare Quelldatei (`source.path`) akzeptiert. `rendition.path` ist der Speicherort, an dem das Endergebnis einer Asset-Verarbeitungsanfrage platziert werden soll. Die benutzerdefinierte Anwendung verwendet den Callback, um die lokal verfügbaren Quelldateien unter Verwendung des in (`rendition.path`) angegebenen Namens in eine Ausgabedarstellungsdatei umzuwandeln. Eine benutzerdefinierte Anwendung muss in `rendition.path` schreiben, um eine Ausgabedarstellung zu erstellen:

```javascript
const { worker } = require('@adobe/asset-compute-sdk');
const fs = require('fs').promises;

// worker() is the entry point in the SDK "framework".
// The asynchronous function defined is the rendition callback.
exports.main = worker(async (source, rendition) => {

    // Tip: custom worker parameters are available in rendition.instructions.
    console.log(rendition.instructions.name); // should print out `rendition.jpg`.

    // Simplest example: copy the source file to the rendition file destination so as to transfer the asset as is without processing.
    await fs.copyFile(source.path, rendition.path);
});
```

### Herunterladen von Quelldateien {#download-source}

Eine benutzerdefinierte Anwendung behandelt nur lokale Dateien. Das Herunterladen der Quelldatei erfolgt über das [Asset Compute-SDK](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk).

### Erstellen von Ausgabedarstellungen {#rendition-creation}

Das SDK ruft für jede Ausgabedarstellung eine asynchrone [Ausgabedarstellungs-Callback-Funktion](https://github.com/adobe/asset-compute-sdk#rendition-callback-for-worker-required) auf.

Die Callback-Funktion hat Zugriff auf die [Quell](https://github.com/adobe/asset-compute-sdk#source)- und [Ausgabedarstellungsobjekte](https://github.com/adobe/asset-compute-sdk#rendition). `source.path` ist bereits vorhanden und ist der Pfad zur lokalen Kopie der Quelldatei. `rendition.path` ist der Pfad, in dem die verarbeitete Ausgabedarstellung gespeichert werden muss. Sofern das [Flag disableSourceDownload](https://github.com/adobe/asset-compute-sdk#worker-options-optional) nicht gesetzt ist, muss die Anwendung den genauen Pfad `rendition.path` verwenden. Andernfalls kann das SDK die Ausgabedarstellungsdatei nicht finden oder identifizieren und schlägt fehl.

Die übermäßige Vereinfachung des Beispiels dient dazu, die Anatomie einer benutzerdefinierten Anwendung zu illustrieren und sich darauf zu konzentrieren. Die Anwendung kopiert die Quelldatei einfach in das Ausgabedarstellungsziel.

Weitere Informationen zu den Callback-Parametern für Ausgabedarstellungen finden Sie unter [Asset Compute-SDK-API](https://github.com/adobe/asset-compute-sdk#api-details).

### Hochladen von Ausgabedarstellungen {#upload-rendition}

Nachdem jede Ausgabedarstellung erstellt und in einer Datei mit dem in `rendition.path` angegebenen Pfad gespeichert wurde, lädt das [Asset Compute-SDK](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk) jede Ausgabedarstellung in einen Cloud-Speicher hoch (entweder AWS oder Azure). Eine benutzerdefinierte Anwendung erhält genau dann mehrere Ausgabedarstellungen gleichzeitig, wenn die eingehende Anfrage mehrere Ausgabedarstellungen enthält, die auf dieselbe Anwendungs-URL verweisen. Der Upload in den Cloud-Speicher erfolgt nach jeder Ausgabedarstellung und vor dem Ausführen des Callback für die nächste Ausgabedarstellung.

Das Verhalten von `batchWorker()` unterscheidet sich, da sie tatsächlich alle Ausgabedarstellungen verarbeitet und diese erst hochlädt, nachdem alle verarbeitet wurden.

## [!DNL Adobe I/O] Ereignisse {#aio-events}

Das SDK sendet [!DNL Adobe I/O]-Ereignisse für jede Ausgabedarstellung. Diese Ereignisse sind je nach Ergebnis entweder vom Typ `rendition_created` oder `rendition_failed`. Weitere Informationen zu Ereignissen finden Sie unter [Asynchrone Asset Compute-Ereignisse](api.md#asynchronous-events).

## [!DNL Adobe I/O]-Ereignisse empfangen {#receive-aio-events}

Der Client fragt das [[!DNL Adobe I/O] -Ereignisjournal](https://www.adobe.io/apis/experienceplatform/events/ioeventsapi.html#/Journaling) gemäß seiner Verbrauchslogik ab. Die anfängliche Journal-URL ist die in der `/register`-API-Antwort angegebene. Ereignisse können mit der in den Ereignissen vorhandenen `requestId` identifiziert werden, die mit der in `/process` zurückgegebenen übereinstimmt. Jede Ausgabedarstellung verfügt über ein separates Ereignis, das gesendet wird, sobald die Ausgabedarstellung hochgeladen wurde (oder fehlgeschlagen ist). Sobald der Client ein passendes Ereignis erhält, kann er die resultierenden Ausgabedarstellungen anzeigen oder anderweitig verarbeiten.

Die JavaScript-Bibliothek [`asset-compute-client`](https://github.com/adobe/asset-compute-client#usage) vereinfacht die Journalabfrage mithilfe der `waitActivation()`-Methode zum Abrufen aller Ereignisse.

```javascript
const events = await assetCompute.waitActivation(requestId);
await Promise.all(events.map(event => {
    if (event.type === "rendition_created") {
        // get rendition from cloud storage location
    }
    else if (event.type === "rendition_failed") {
        // failed to process
    }
    else {
        // other event types
        // (could be added in the future)
    }
}));
```

Weitere Informationen zum Abrufen von Journalereignissen finden Sie unter [[!DNL Adobe I/O]  Events-API](https://www.adobe.io/apis/experienceplatform/events/ioeventsapi.html#!adobedocs/adobeio-events/master/events-api-reference.yaml).

<!-- TBD:
* Illustration of the controls/data flow.
* Basic overview, in text and not code, of how an application works.
-->
