---
title: Machen Sie sich mit der Arbeit einer benutzerdefinierten Anwendung vertraut.
description: Interne Arbeit [!DNL Asset Compute Service] der benutzerdefinierten Anwendung, um deren Funktionsweise besser zu verstehen.
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '774'
ht-degree: 0%

---


# Interne einer benutzerdefinierten Anwendung {#how-custom-application-works}

Verwenden Sie die folgende Abbildung, um den durchgängigen Arbeitsablauf zu verstehen, wenn ein digitales Asset mithilfe einer benutzerdefinierten Anwendung von einem Client verarbeitet wird.

![Arbeitsablauf für benutzerdefinierte Anwendungen](assets/customworker.png)

*Abbildung: Schritte, die zur Verarbeitung eines Assets mit [!DNL Asset Compute Service]durchgeführt werden.*

## Registrierung {#registration}

Der Client muss vor der ersten Anforderung [`/register`](api.md#register) [`/process`](api.md#process-request) einmal aufrufen, um die Protokoll-URL für den Empfang von Adoben-E/A-Ereignissen für die Adobe Asset Compute einzurichten und abzurufen.

```sh
curl -X POST \
  https://asset-compute.adobe.io/register \
  -H "x-ims-org-id: $ORG_ID" \
  -H "x-gw-ims-org-id: $ORG_ID" \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "x-api-key: $API_KEY"
```

Die [`@adobe/asset-compute-client`](https://github.com/adobe/asset-compute-client#usage) JavaScript-Bibliothek kann in NodeJS-Anwendungen verwendet werden, um alle erforderlichen Schritte von der Registrierung über die Verarbeitung bis hin zur asynchronen Verarbeitung von Ereignissen durchzuführen. Weitere Informationen zu den erforderlichen Überschriften finden Sie unter [Authentifizierung und Autorisierung](api.md).

## Verarbeitung {#processing}

Der Client sendet eine [Verarbeitungsanfrage](api.md#process-request) .

```sh
curl -X POST \
  https://asset-compute.adobe.io/process \
  -H "x-ims-org-id: $ORG_ID" \
  -H "x-gw-ims-org-id: $ORG_ID" \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "x-api-key: $API_KEY" \
  -d "<RENDITION_JSON>
```

Der Client ist für die korrekte Formatierung der Darstellungen mit vorzeichenbehafteten URLs verantwortlich. Die [`@adobe/node-cloud-blobstore-wrapper`](https://github.com/adobe/node-cloud-blobstore-wrapper#presigned-urls) JavaScript-Bibliothek kann in NodeJS-Anwendungen zum Vorsignieren von URLs verwendet werden. Derzeit unterstützt die Bibliothek nur die Datenspeicherung von AWS und S3 Container.

Die Verarbeitungsanfrage gibt eine zurück, `requestId` die für die Abfrage von Adobe-E/A-Ereignissen verwendet werden kann.

Nachfolgend finden Sie eine Beispiel-Anforderung zur Verarbeitung benutzerdefinierter Anwendungen.

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

Die [!DNL Asset Compute Service] sendet die benutzerdefinierten Anwendungsanforderungen an die benutzerdefinierte Anwendung. Es verwendet eine HTTP-POST zur angegebenen Anwendungs-URL, bei der es sich um die gesicherte URL der Webaktion von Project Firefly handelt. Alle Anforderungen verwenden das HTTPS-Protokoll, um die Datensicherheit zu maximieren.

Das von einer benutzerdefinierten Anwendung verwendete [Asset Compute-SDK](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk) verarbeitet die HTTP-POST-Anforderung. Es verarbeitet auch das Herunterladen der Quelle, das Hochladen von Darstellungen, das Senden von E/A-Ereignissen und die Fehlerbehandlung.

<!-- TBD: Add the application diagram. -->

### Application code {#application-code}

Benutzerdefinierter Code muss nur einen Rückruf bereitstellen, der die lokal verfügbare Quelldatei (`source.path`) akzeptiert. Der Speicherort `rendition.path` ist der Ort, an dem das Endergebnis einer Asset-Verarbeitungsanfrage platziert werden soll. Die benutzerdefinierte Anwendung verwendet den Rückruf, um die lokal verfügbaren Quelldateien unter Verwendung des angegebenen Namens (`rendition.path`) in eine Darstellungsdatei umzuwandeln. Eine benutzerdefinierte Anwendung muss schreiben, `rendition.path` um eine Darstellung zu erstellen:

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

### Quelldateien herunterladen {#download-source}

Eine benutzerdefinierte Anwendung behandelt nur lokale Dateien. Das Herunterladen der Quelldatei erfolgt über das [Asset Compute-SDK](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk).

### Erstellung von Darstellungen {#rendition-creation}

Das SDK ruft für jede Darstellung eine asynchrone [Darstellungsrückruffunktion](https://github.com/adobe/asset-compute-sdk#rendition-callback-for-worker-required) auf.

Die Rückruffunktion hat Zugriff auf die [Quell](https://github.com/adobe/asset-compute-sdk#source) - und [Darstellungsobjekte](https://github.com/adobe/asset-compute-sdk#rendition) . Der `source.path` ist bereits vorhanden und der Pfad zur lokalen Kopie der Quelldatei. Der Pfad `rendition.path` ist der Pfad, in dem die verarbeitete Darstellung gespeichert werden muss. Wenn das Flag [disableSourceDownload nicht gesetzt](https://github.com/adobe/asset-compute-sdk#worker-options-optional) ist, muss die Anwendung exakt die Variable verwenden `rendition.path`. Andernfalls kann das SDK die Darstellungsdatei nicht finden oder identifizieren und schlägt fehl.

Die übermäßige Vereinfachung des Beispiels dient dazu, die Anatomie einer benutzerdefinierten Anwendung zu illustrieren und sich darauf zu konzentrieren. Die Anwendung kopiert die Quelldatei einfach in das Ausgabeziel.

Weitere Informationen zu den Rückrufparametern für Darstellungen finden Sie unter [Asset Compute SDK API](https://github.com/adobe/asset-compute-sdk#api-details).

### Darstellungen hochladen {#upload-rendition}

Nachdem jede Darstellung erstellt und in einer Datei mit dem von bereitgestellten Pfad gespeichert wurde, lädt `rendition.path`das [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk) jede Darstellung in eine Cloud-Datenspeicherung hoch (entweder AWS oder AWS). Eine benutzerdefinierte Anwendung erhält mehrere Darstellungen gleichzeitig, wenn und nur, wenn die eingehende Anforderung über mehrere Darstellungen verfügt, die auf dieselbe Anwendungs-URL zeigen. Der Upload in die Cloud-Datenspeicherung erfolgt nach jeder Darstellung und vor dem Ausführen des Rückrufs für die nächste Darstellung.

Das Verhalten `batchWorker()` unterscheidet sich, da es tatsächlich alle Darstellungen verarbeitet und diese erst dann hochgeladen werden, wenn sie verarbeitet wurden.

## Adoben-E/A-Ereignis {#aio-events}

Das SDK sendet Adobe-I/O-Ereignis für jede Darstellung. Diese Ereignis sind entweder vom Typ `rendition_created` oder `rendition_failed` vom Ergebnis abhängig. Weitere Informationen zu Ereignissen finden Sie unter [Asset-Ereignis](api.md#asynchronous-events) berechnen.

## E/A-Ereignisse der Adobe empfangen {#receive-aio-events}

Der Kunde fragt das Protokoll [der](https://www.adobe.io/apis/experienceplatform/events/ioeventsapi.html#/Journaling) Adobe-I/O-Ereignis gemäß seiner Konsummlogik ab. Die anfängliche Protokoll-URL ist die in der `/register` API-Antwort angegebene. Ereignis können mit der identifiziert werden, die in den Ereignissen vorhanden `requestId` ist und mit der zurückgegebenen übereinstimmt `/process`. Jede Darstellung verfügt über ein separates Ereignis, das gesendet wird, sobald die Darstellung hochgeladen wurde (oder fehlgeschlagen ist). Sobald der Client ein passendes Ereignis erhält, kann er die resultierenden Darstellungen anzeigen oder anderweitig bearbeiten.

Die JavaScript-Bibliothek [`asset-compute-client`](https://github.com/adobe/asset-compute-client#usage) vereinfacht die Abfrage von Protokollen mithilfe der `waitActivation()` Methode zum Abrufen aller Ereignis.

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

Weitere Informationen zum Abrufen von Protokoll-Ereignissen finden Sie unter [Adobe-E/A-Ereignisse-API](https://www.adobe.io/apis/experienceplatform/events/ioeventsapi.html#!adobedocs/adobeio-events/master/events-api-reference.yaml).

<!-- TBD:
* Illustration of the controls/data flow.
* Basic overview, in text and not code, of how an application works.
-->
