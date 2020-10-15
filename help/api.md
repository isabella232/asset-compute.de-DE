---
title: '[!DNL Asset Compute Service] HTTP-API.'
description: '[!DNL Asset Compute Service] HTTP-API zum Erstellen benutzerdefinierter Anwendungen.'
translation-type: tm+mt
source-git-commit: 18e97e544014933e9910a12bc40246daa445bf4f
workflow-type: tm+mt
source-wordcount: '2931'
ht-degree: 3%

---


# [!DNL Asset Compute Service] HTTP-API {#asset-compute-http-api}

Die Verwendung der API ist auf Entwicklungszwecke beschränkt. Die API wird als Kontext bei der Entwicklung benutzerdefinierter Anwendungen bereitgestellt. [!DNL Adobe Experience Manager] als Cloud Service die API zum Übermitteln der Verarbeitungsinformationen an eine benutzerdefinierte Anwendung verwendet. Weitere Informationen finden Sie unter [Verwenden von Asset-Mikrodiensten und Verarbeitungs-Profilen](https://docs.adobe.com/content/help/de-DE/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html).

>[!NOTE]
>
>[!DNL Asset Compute Service] ist nur zur Verwendung mit [!DNL Experience Manager] als Cloud Service verfügbar.

Jeder Client der [!DNL Asset Compute Service] HTTP-API muss diesem Fluss auf hoher Ebene folgen:

1. Ein Client wird als Projekt in einer IMS-Organisation bereitgestellt [!DNL Adobe Developer Console] . Jeder separate Client (System oder Umgebung) benötigt ein eigenes Projekt, um den Datenfluss des Ereignisses zu trennen.

1. Ein Client generiert ein Zugriffstoken für das technische Konto mit der [JWT-Authentifizierung](https://www.adobe.io/authentication/auth-methods.html)(Service Account).

1. Ein Client ruft [`/register`](#register) nur einmal auf, um die Protokoll-URL abzurufen.

1. Ein Client ruft [`/process`](#process-request) für jedes Asset auf, für das er Darstellungen generieren möchte. Der Aufruf ist asynchron.

1. Ein Kunde fragt das Protokoll regelmäßig ab, um Ereignis zu [empfangen](#asynchronous-events). Es empfängt Ereignis für jede angeforderte Darstellung, wenn die Darstellung erfolgreich verarbeitet wurde (`rendition_created` Ereignistyp) oder ein Fehler (`rendition_failed` Ereignistyp) aufgetreten ist.

Das [@adobe/asset-compute-client](https://github.com/adobe/asset-compute-client) -Modul erleichtert die Verwendung der API im Code Node.js.

## Authentifizierung und Autorisierung {#authentication-and-authorization}

Für alle APIs ist eine Zugriffstoken-Authentifizierung erforderlich. Die Anforderungen müssen die folgenden Header festlegen:

1. `Authorization` -Header mit Inhabertoken, dem technischen Konto-Token, der über den [JWT-Austausch](https://www.adobe.io/authentication/auth-methods.html) vom Adobe Developer Console-Projekt empfangen wird. Die [Bereiche](#scopes) sind nachfolgend beschrieben.

<!-- TBD: Change the existing URL to a new path when a new path for docs is available. The current path contains master word that is not an inclusive term. Logged ticket in AIO's GitHub repo to get a new URL.
-->

1. `x-gw-ims-org-id` mit der IMS-Organisations-ID.

1. `x-api-key` mit der Client-ID des [!DNL Adobe Developers Console] Projekts.

### Bereiche {#scopes}

Stellen Sie die folgenden Bereiche für das Zugriffstoken sicher:

* `openid`
* `AdobeID`
* `asset_compute`
* `read_organizations`
* `event_receiver`
* `event_receiver_api`
* `adobeio_api`
* `additional_info.roles`
* `additional_info.projectedProductContext`

Dazu muss das [!DNL Adobe Developer Console] Projekt abonniert werden `Asset Compute`, `I/O Events`und `I/O Management API` Dienste. Die einzelnen Bereiche werden wie folgt unterteilt:

* Einfach
   * scopes: `openid,AdobeID`

* Asset-Berechnung
   * metascope: `asset_compute_meta`
   * scopes: `asset_compute,read_organizations`

* Adoben-E/A-Ereignis
   * metascope: `event_receiver_api`
   * scopes: `event_receiver,event_receiver_api`

* API zur Adobe-E/A-Verwaltung
   * metascope: `ent_adobeio_sdk`
   * scopes: `adobeio_api,additional_info.roles,additional_info.projectedProductContext`

## Registrierung {#register}

Jeder Client des [!DNL Asset Compute service] - ein eindeutiges [!DNL Adobe Developer Console] Projekt, das den Dienst abonniert hat - muss sich vor der Bearbeitung von Anforderungen [registrieren](#register-request) . Der Registrierungsschritt gibt das eindeutige Ereignis-Protokoll zurück, das zum Abrufen der asynchronen Ereignis aus der Darstellungsverarbeitung erforderlich ist.

Am Ende seines Lebenszyklus kann ein Client die [Registrierung](#unregister-request)aufheben.

### Anfrage registrieren {#register-request}

Mit diesem API-Aufruf wird ein [!DNL Asset Compute] Client eingerichtet und die Ereignis-Protokoll-URL bereitgestellt. Dies ist ein depotenter Vorgang und muss nur einmal für jeden Client aufgerufen werden. Sie kann erneut aufgerufen werden, um die Protokoll-URL abzurufen.

| Parameter | Wert |
|--------------------------|------------------------------------------------------|
| Methode | `POST` |
| Pfad           | `/register` |
| Header `Authorization` | Alle [Autorisierungskopfzeilen](#authentication-and-authorization). |
| Header `x-request-id` | Optional können Clients eine eindeutige End-to-End-Kennung der Verarbeitungsanfragen systemübergreifend festlegen. |
| Einrichtung anfordern | Muss leer sein. |

### Antwort registrieren {#register-response}

| Parameter | Wert |
|-----------------------|------------------------------------------------------|
| MIME-Typ | `application/json` |
| Header `X-Request-Id` | Entweder identisch mit dem `X-Request-Id` Anforderungsheader oder einem eindeutig erstellten. Verwendung zum Identifizieren von Anforderungen über Systeme und/oder Supportanfragen hinweg. |
| Reaktionskörper | Ein JSON-Objekt mit `journal`- `ok` und/oder `requestId` -Feldern. |

Die HTTP-Statuscodes lauten:

* **200 Erfolg**: Bei erfolgreicher Anforderung. Es enthält die `journal` URL, die über die Ergebnisse der asynchronen Verarbeitung benachrichtigt wird, die über ausgelöst wird `/process` (als Ereignis-Typ `rendition_created` bei Erfolg oder `rendition_failed` bei Fehler).

   ```json
   {
       "ok": true,
       "journal": "https://api.adobe.io/events/organizations/xxxxx/integrations/xxxx/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
       "requestId": "1234567890"
   }
   ```

* **401 Nicht autorisiert**: tritt auf, wenn die Anforderung keine gültige [Authentifizierung](#authentication-and-authorization)aufweist. Ein Beispiel hierfür ist ein ungültiges Zugriffstoken oder ein ungültiger API-Schlüssel.

* **403 Verboten**: tritt auf, wenn die Anforderung keine gültige [Autorisierung](#authentication-and-authorization)besitzt. Ein Zugriffstoken kann beispielsweise gültig sein, aber das Adobe Developer Console-Projekt (technisches Konto) wird nicht für alle erforderlichen Dienste abonniert.

* **429 Zu viele Anträge**: auftritt, wenn das System von diesem Client oder anderweitig überlastet wird. Kunden sollten es erneut mit einem [exponentiellen Rückschlag](https://en.wikipedia.org/wiki/Exponential_backoff)versuchen. Der Körper ist leer.
* **4xx-Fehler**: Wenn ein anderer Client-Fehler aufgetreten ist und die Registrierung fehlgeschlagen ist. Normalerweise wird eine JSON-Antwort wie diese zurückgegeben, obwohl dies nicht für alle Fehler garantiert ist:

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **5xx-Fehler**: tritt auf, wenn ein anderer serverseitiger Fehler aufgetreten ist und die Registrierung fehlgeschlagen ist. Normalerweise wird eine JSON-Antwort wie diese zurückgegeben, obwohl dies nicht für alle Fehler garantiert ist:

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

### Anfrage rückgängig machen {#unregister-request}

Durch diesen API-Aufruf wird die Registrierung eines [!DNL Asset Compute] Clients aufgehoben. Danach kann man nicht mehr anrufen `/process`. Wenn Sie den API-Aufruf für einen nicht registrierten Client oder einen noch nicht registrierten Client verwenden, wird ein `404` Fehler zurückgegeben.

| Parameter | Wert |
|--------------------------|------------------------------------------------------|
| Methode | `POST` |
| Pfad           | `/unregister` |
| Header `Authorization` | Alle [Autorisierungskopfzeilen](#authentication-and-authorization). |
| Header `x-request-id` | Optional können Clients eine eindeutige End-to-End-Kennung der Verarbeitungsanfragen systemübergreifend festlegen. |
| Einrichtung anfordern | Leer. |

### Antwort löschen {#unregister-response}

| Parameter | Wert |
|-----------------------|------------------------------------------------------|
| MIME-Typ | `application/json` |
| Header `X-Request-Id` | Entweder identisch mit dem `X-Request-Id` Anforderungsheader oder einem eindeutig erstellten. Verwendung zum Identifizieren von Anforderungen über Systeme und/oder Supportanfragen hinweg. |
| Reaktionskörper | Ein JSON-Objekt mit `ok` und `requestId` Feldern. |

Die Statuscodes sind:

* **200 Erfolg**: tritt auf, wenn die Registrierung und das Protokoll gefunden und entfernt werden.

   ```json
   {
       "ok": true,
       "requestId": "1234567890"
   }
   ```

* **401 Nicht autorisiert**: tritt auf, wenn die Anforderung keine gültige [Authentifizierung](#authentication-and-authorization)aufweist. Ein Beispiel hierfür ist ein ungültiges Zugriffstoken oder ein ungültiger API-Schlüssel.

* **403 Verboten**: tritt auf, wenn die Anforderung keine gültige [Autorisierung](#authentication-and-authorization)besitzt. Ein Zugriffstoken kann beispielsweise gültig sein, aber das Adobe Developer Console-Projekt (technisches Konto) wird nicht für alle erforderlichen Dienste abonniert.

* **404 Nicht gefunden**: tritt auf, wenn keine aktuelle Registrierung für die angegebenen Anmeldeinformationen vorhanden ist.

   ```json
   {
       "ok": true,
       "requestId": "1234567890"
   }
   ```

* **429 Zu viele Anträge**: auftritt, wenn das System überlastet ist. Kunden sollten es erneut mit einem [exponentiellen Rückschlag](https://en.wikipedia.org/wiki/Exponential_backoff)versuchen. Der Körper ist leer.

* **4xx-Fehler**: tritt auf, wenn ein anderer Client-Fehler aufgetreten ist und die Registrierung fehlgeschlagen ist. Normalerweise wird eine JSON-Antwort wie diese zurückgegeben, obwohl dies nicht für alle Fehler garantiert ist:

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **5xx-Fehler**: tritt auf, wenn ein anderer serverseitiger Fehler aufgetreten ist und die Registrierung fehlgeschlagen ist. Normalerweise wird eine JSON-Antwort wie diese zurückgegeben, obwohl dies nicht für alle Fehler garantiert ist:

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

## Prozessanforderung {#process-request}

Der `process` Vorgang sendet einen Auftrag, der ein Quell-Asset basierend auf den Anweisungen in der Anforderung in mehrere Darstellungen transformiert. Benachrichtigungen über den erfolgreichen Abschluss (Ereignistyp `rendition_created`) oder über Fehler (Ereignistyp `rendition_failed`) werden an ein Ereignis-Protokoll gesendet, das vor der Ausführung einer beliebigen Anzahl von [Anforderungen einmal mit](#register) /registrieren `/process` abgerufen werden muss. Falsch geformte Anforderungen schlagen sofort mit einem 400-Fehlercode fehl.

Auf Binärdateien wird mithilfe von URLs, wie vorzeichenbehafteten Amazon AWS S3-URLs oder SAS-URLs der Azurblase-Datenspeicherung, verwiesen, um das `source` Asset (`GET` URLs) zu lesen und die Darstellungen (`PUT` URLs) zu schreiben. Der Client ist für das Generieren dieser vorab signierten URLs verantwortlich.

| Parameter | Wert |
|--------------------------|------------------------------------------------------|
| Methode | `POST` |
| Pfad           | `/process` |
| MIME-Typ | `application/json` |
| Header `Authorization` | Alle [Autorisierungskopfzeilen](#authentication-and-authorization). |
| Header `x-request-id` | Optional können Clients eine eindeutige End-to-End-Kennung der Verarbeitungsanfragen systemübergreifend festlegen. |
| Einrichtung anfordern | Muss sich im JSON-Format für die Prozessanforderung befinden, wie unten beschrieben. Es enthält Anweisungen dazu, welches Asset verarbeitet und welche Darstellungen generiert werden sollen. |

### Prozessanforderung JSON {#process-request-json}

Der Anforderungstext von `/process` ist ein JSON-Objekt mit diesem Schema auf hoher Ebene:

```json
{
    "source": "",
    "renditions" : []
}
```

Die verfügbaren Felder sind:

| Name | Typ | Beschreibung | Beispiel |
|--------------|----------|-------------|---------|
| `source` | `string` | URL des zu verarbeitenden Quellassets. Optional je nach angeforderten Ausgabeformat (z. `fmt=zip`). | `"http://example.com/image.jpg"` |
| `source` | `object` | Beschreibung des zu verarbeitenden Quellassets. Siehe Beschreibung der [Quellobjektfelder](#source-object-fields) unten. Optional je nach angeforderten Ausgabeformat (z. `fmt=zip`). | `{"url": "http://example.com/image.jpg", "mimeType": "image/jpeg" }` |
| `renditions` | `array` | Aus der Quelldatei zu generierende Darstellungen. Jedes Darstellungsobjekt unterstützt [Darstellungsanweisungen](#rendition-instructions). Erforderlich. | `[{ "target": "https://....", "fmt": "png" }]` |

Die `source` kann entweder eine URL sein, `<string>` die als URL erkannt wird, oder eine mit einem zusätzlichen Feld `<object>` . Die folgenden Varianten sind ähnlich:

```json
"source": "http://example.com/image.jpg"
```

```json
"source": {
    "url": "http://example.com/image.jpg"
}
```

### Quellobjektfelder {#source-object-fields}

| Name | Typ | Beschreibung | Beispiel |
|-----------|----------|-------------|---------|
| `url` | `string` | URL des zu verarbeitenden Quellassets. Erforderlich. | `"http://example.com/image.jpg"` |
| `name` | `string` | Name der Quellelementdatei. Dateierweiterung im Namen kann verwendet werden, wenn kein MIME-Typ erkannt werden kann. Vorrang vor dem Dateinamen im URL-Pfad oder Dateinamen in der `content-disposition` Kopfzeile der binären Ressource. Der Standardwert ist &quot;file&quot;. | `"image.jpg"` |
| `size` | `number` | Größe der Quell-Asset-Datei in Byte. Vorrang vor der `content-length` Kopfzeile der binären Ressource. | `10234` |
| `mimetype` | `string` | MIME-Typ der Quellelementdatei. Vorrang vor der `content-type` Kopfzeile der binären Ressource. | `"image/jpeg"` |

### Beispiel für eine vollständige `process` Anforderung {#complete-process-request-example}

```json
{
    "source": "https://www.adobe.com/content/dam/acom/en/lobby/lobby-bg-bts2017-logged-out-1440x860.jpg",
    "renditions" : [{
            "name": "image.48x48.png",
            "target": "https://some-presigned-put-url-for-image.48x48.png",
            "fmt": "png",
            "width": 48,
            "height": 48
        },{
            "name": "image.200x200.jpg",
            "target": "https://some-presigned-put-url-for-image.200x200.jpg",
            "fmt": "jpg",
            "width": 200,
            "height": 200
        },{
            "name": "cqdam.xmp.xml",
            "target": "https://some-presigned-put-url-for-cqdam.xmp.xml",
            "fmt": "xmp"
        },{
            "name": "cqdam.text.txt",
            "target": "https://some-presigned-put-url-for-cqdam.text.txt",
            "fmt": "text"
    }]
}
```

## Prozessantwort {#process-response}

Die `/process` Anforderung wird sofort mit einem Erfolg oder einem Fehler zurückgegeben, der auf der Grundlage der grundlegenden Anforderungsüberprüfung ermittelt wurde. Die tatsächliche Asset-Verarbeitung erfolgt asynchron.

| Parameter | Wert |
|-----------------------|------------------------------------------------------|
| MIME-Typ | `application/json` |
| Header `X-Request-Id` | Entweder identisch mit dem `X-Request-Id` Anforderungsheader oder einem eindeutig erstellten. Verwendung zum Identifizieren von Anforderungen über Systeme und/oder Supportanfragen hinweg. |
| Reaktionskörper | Ein JSON-Objekt mit `ok` und `requestId` Feldern. |

Statuscodes:

* **200 Erfolg**: Wenn die Anforderung erfolgreich eingereicht wurde. Antwort-JSON umfasst `"ok": true`:

   ```json
   {
       "ok": true,
       "requestId": "1234567890"
   }
   ```

* **400 Ungültige Anforderung**: Wenn die Anforderung falsch formatiert ist, z. B. fehlende erforderliche Felder im Anforderungs-JSON. Antwort-JSON umfasst `"ok": false`:

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **401 Nicht autorisiert**: Wenn die Anforderung keine gültige [Authentifizierung](#authentication-and-authorization)aufweist. Ein Beispiel hierfür ist ein ungültiges Zugriffstoken oder ein ungültiger API-Schlüssel.
* **403 Verboten**: Wenn die Anforderung keine gültige [Autorisierung](#authentication-and-authorization)besitzt. Ein Zugriffstoken kann beispielsweise gültig sein, aber das Adobe Developer Console-Projekt (technisches Konto) wird nicht für alle erforderlichen Dienste abonniert.
* **429 Zu viele Anträge**: Wenn das System von diesem Client oder generell überlastet wird. Die Kunden können es mit einem [exponentiellen Rückschlag](https://en.wikipedia.org/wiki/Exponential_backoff)erneut versuchen. Der Körper ist leer.
* **4xx-Fehler**: Wenn ein anderer Client-Fehler aufgetreten ist. Normalerweise wird eine JSON-Antwort wie diese zurückgegeben, obwohl dies nicht für alle Fehler garantiert ist:

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **5xx-Fehler**: Wenn ein anderer serverseitiger Fehler aufgetreten ist. Normalerweise wird eine JSON-Antwort wie diese zurückgegeben, obwohl dies nicht für alle Fehler garantiert ist:

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

Die meisten Clients sind wahrscheinlich geneigt, die gleiche Anforderung mit [exponentieller Absicherung](https://en.wikipedia.org/wiki/Exponential_backoff) bei jedem Fehler erneut auszuführen, *mit Ausnahme* von Konfigurationsproblemen wie 401 oder 403 oder ungültigen Anforderungen wie 400. Abgesehen von der Begrenzung der regulären Rate durch 429 Antworten kann ein vorübergehender Ausfall oder eine Beschränkung zu 5xx-Fehlern führen. Es wird dann empfohlen, es nach einer bestimmten Zeit erneut zu versuchen.

Alle JSON-Antworten (sofern vorhanden) enthalten den Wert, `requestId` der mit dem `X-Request-Id` Header identisch ist. Es wird empfohlen, aus der Kopfzeile zu lesen, da sie immer vorhanden ist. Der `requestId` wird auch in allen Ereignissen, die mit Verarbeitungsanforderungen zusammenhängen, als `requestId`. Clients dürfen keine Annahme über das Format dieser Zeichenfolge vornehmen, sondern es handelt sich um einen undurchsichtigen Zeichenfolgenbezeichner.

## Bei der Nachbearbeitung anmelden {#opt-in-to-post-processing}

Das [Asset Compute-SDK](https://github.com/adobe/asset-compute-sdk) unterstützt eine Reihe grundlegender Optionen für die Nachbearbeitung von Bildern. Benutzerdefinierte Mitarbeiter können explizit Opt-in Nachbearbeitung durchführen, indem Sie das Feld `postProcess` im Ausgabeobjekt auf `true`.

Folgende Anwendungsfälle werden unterstützt:

* Beschneiden einer Darstellung auf ein Rechteck, dessen Grenzen durch &quot;Beschneiden.w&quot;, &quot;Beschneiden.h&quot;, &quot;Beschneiden.x&quot;und &quot;Beschneiden.y&quot;definiert werden. Sie wird `instructions.crop` im Darstellungsobjekt definiert.
* Passen Sie die Größe von Bildern mit Breite, Höhe oder beidem an. Sie wird vom Objekt &quot;Darstellung&quot; `instructions.width` und `instructions.height` im Objekt &quot;Darstellung&quot;definiert. Wenn Sie die Größe nur mit Breite oder Höhe ändern möchten, legen Sie nur einen Wert fest. Der Compute Service behält das Seitenverhältnis bei.
* Legen Sie die Qualität für ein JPEG-Bild fest. Sie wird `instructions.quality` im Darstellungsobjekt definiert. Die beste Qualität wird durch `100` kleinere Werte gekennzeichnet.
* Erstellen Sie Interlaced-Bilder. Sie wird `instructions.interlace` im Darstellungsobjekt definiert.
* Legen Sie DPI fest, um die gerenderte Größe für die DPS-Veröffentlichung anzupassen, indem Sie die Skalierung anpassen, die auf die Pixel angewendet wird. Es wird `instructions.dpi` im Darstellungsobjekt definiert, um die Auflösung von dpi zu ändern. Um die Bildgröße jedoch mit einer anderen Auflösung zu ändern, verwenden Sie die `convertToDpi` Anweisungen.
* Passen Sie die Bildgröße so an, dass die gerenderte Bildbreite oder -höhe mit der angegebenen Zielgruppe (DPI) identisch bleibt. Sie wird `instructions.convertToDpi` im Darstellungsobjekt definiert.

## Wasserzeichenanlagen {#add-watermark}

Das [Asset Compute-SDK](https://github.com/adobe/asset-compute-sdk) unterstützt das Hinzufügen eines Wasserzeichens zu PNG-, JPEG-, TIFF- und GIF-Bilddateien. Das Wasserzeichen wird entsprechend den Darstellungsanweisungen im `watermark` Objekt in der Darstellung hinzugefügt.

Das Wasserzeichen wird während der Wiedergabe nach der Verarbeitung durchgeführt. Um Assets mit Wasserzeichen zu versehen, [wählt der benutzerdefinierte Worker die Nachbearbeitung](#opt-in-to-post-processing) , indem er das Feld `postProcess` im Wiedergabeobjekt auf `true`. Wenn der Arbeiter sich nicht anmeldet, wird keine Wasserzeichenfunktion angewendet, auch wenn das Wasserzeichenobjekt auf dem Wiedergabeobjekt in der Anforderung eingestellt ist.

## Darstellungsanweisungen {#rendition-instructions}

Dies sind die verfügbaren Optionen für das `renditions` Array in [/process](#process-request).

### Gemeinsame Felder {#common-fields}

| Name | Typ | Beschreibung | Beispiel |
|-------------------|----------|-------------|---------|
| `fmt` | `string` | Das Format für die Zielgruppe von Darstellungen kann auch `text` zur Extraktion von Text und `xmp` zum Extrahieren XMP Metadaten als XML verwendet werden. Siehe [Unterstützte Formate](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html) | `png` |
| `worker` | `string` | URL einer [benutzerdefinierten Anwendung](develop-custom-application.md). Muss eine `https://` URL sein. Wenn dieses Feld vorhanden ist, wird die Darstellung von einer benutzerdefinierten Anwendung erstellt. In der benutzerdefinierten Anwendung wird dann jedes andere Feld für die Darstellung des Sets verwendet. | `"https://1234.adobeioruntime.net`<br>`/api/v1/web`<br>`/example-custom-worker-master/worker"` |
| `target` | `string` | URL, auf die die generierte Darstellung mit HTTP PUT hochgeladen werden soll. | `http://w.com/img.jpg` |
| `target` | `object` | Mehrteilige vorzeichenbehaftete URL-Upload-Informationen für die generierte Darstellung. Dies ist für [AEM/Oak Direct Binary Upload](https://jackrabbit.apache.org/oak/docs/features/direct-binary-access.html) mit diesem [mehrteiligen Upload-Verhalten](http://jackrabbit.apache.org/oak/docs/apidocs/org/apache/jackrabbit/api/binary/BinaryUpload.html).<br>Felder:<ul><li>`urls`: Zeichenfolgen-Array, einer für jede vorzeichenbehaftete Teil-URL</li><li>`minPartSize`: die Mindestgröße für ein Teil = URL</li><li>`maxPartSize`: die maximale Größe für ein Teil = URL</li></ul> | `{ "urls": [ "https://part1...", "https://part2..." ], "minPartSize": 10000, "maxPartSize": 100000 }` |
| `userData` | `object` | Optionaler reservierter Bereich, der vom Client gesteuert und wie Ereignis der Darstellung weitergegeben wird. Ermöglicht es Kunden, benutzerdefinierte Informationen zur Identifizierung von Darstellungsabläufen hinzuzufügen. Darf nicht in benutzerdefinierten Anwendungen geändert oder verwendet werden, da Clients dies jederzeit ändern können. | `{ ... }` |

### Darstellungsspezifische Felder {#rendition-specific-fields}

Eine Liste der derzeit unterstützten Dateiformate finden Sie unter [Unterstützte Dateiformate](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html).

| Name | Typ | Beschreibung | Beispiel |
|-------------------|----------|-------------|---------|
| `*` | `*` | Erweiterte, benutzerdefinierte Felder können hinzugefügt werden, die eine [benutzerdefinierte Anwendung](develop-custom-application.md) versteht. |  |
| `embedBinaryLimit` | `number` in Byte | Wenn dieser Wert festgelegt ist und die Dateigröße der Darstellung kleiner als dieser ist, wird die Darstellung in das Ereignis eingebettet, das nach Abschluss der Darstellungsgenerierung gesendet wird. Die maximal zulässige Größe für die Einbettung beträgt 32 KB (32 x 1024 Byte). Wenn eine Darstellung größer als die `embedBinaryLimit` Beschränkung ist, wird sie an einer Position in der Cloud-Datenspeicherung platziert und nicht in das Ereignis eingebettet. | `3276` |
| `width` | `number` | Breite in Pixel. nur für Bilddarstellungen. | `200` |
| `height` | `number` | Höhe in Pixeln. nur für Bilddarstellungen. | `200` |
|  |  | Das Seitenverhältnis wird immer beibehalten, wenn: <ul> <li> Sowohl `width` als auch `height` werden angegeben, dann passt das Bild in die Größe, wobei das Seitenverhältnis erhalten bleibt </li><li> Nur `width` oder nur `height` angegeben wird, verwendet das resultierende Bild die entsprechende Dimension und behält dabei das Seitenverhältnis bei</li><li> Wenn weder `width` noch `height` angegeben, wird die Pixelgröße des Originalbilds verwendet. Es hängt vom Quelltyp ab. Bei einigen Formaten, z. B. PDF-Dateien, wird eine Standardgröße verwendet. Es kann eine maximale Größe geben.</li></ul> |  |
| `quality` | `number` | Geben Sie die JPEG-Qualität im Bereich von `1` bis `100`. Gilt nur für Bilddarstellungen. | `90` |
| `xmp` | `string` | Wird nur von XMP Metadaten-Schreibback verwendet, ist es Base64-kodierte XMP, um in die angegebene Darstellung zurückzuschreiben. |  |
| `interlace` | `bool` | Erstellen Sie PNG- oder GIF-Interlaced- oder progressives JPEG-Format, indem Sie es auf `true`. Sie hat keine Auswirkungen auf andere Dateiformate. |  |
| `jpegSize` | `number` | Ungefähre Größe der JPEG-Datei in Byte. Es setzt jede `quality` Einstellung außer Kraft. Hat keine Auswirkungen auf andere Formate. |  |
| `dpi` | `number` oder `object` sein. | Legen Sie DPI für x und y fest. Aus Gründen der Einfachheit kann es auch auf eine einzige Zahl eingestellt werden, die sowohl für x als auch für y verwendet wird. Es hat keine Auswirkungen auf das Bild selbst. | `96` oder `{ xdpi: 96, ydpi: 96 }` sein. |
| `convertToDpi` | `number` oder `object` sein. | x- und y-DPI-Werte neu berechnen, wobei die physische Größe erhalten bleibt. Aus Gründen der Einfachheit kann es auch auf eine einzige Zahl eingestellt werden, die sowohl für x als auch für y verwendet wird. | `96` oder `{ xdpi: 96, ydpi: 96 }` sein. |
| `files` | `array` | Liste der Dateien, die in das ZIP-Archiv aufgenommen werden sollen (`fmt=zip`). Jeder Eintrag kann entweder eine URL-Zeichenfolge oder ein Objekt mit den Feldern sein:<ul><li>`url`: URL zum Herunterladen der Datei</li><li>`path`: Datei unter diesem Pfad in der ZIP speichern</li></ul> | `[{ "url": "https://host/asset.jpg", "path": "folder/location/asset.jpg" }]` |
| `duplicate` | `string` | Duplikat-Handling für ZIP-Archive (`fmt=zip`). Standardmäßig werden mehrere Dateien, die unter demselben Pfad in der ZIP gespeichert sind, als Fehler ausgegeben. Wird `duplicate` die Einstellung auf `ignore` festgelegt, wird nur das erste Asset gespeichert und der Rest wird ignoriert. | `ignore` |
| `watermark` | `object` | Enthält Anweisungen zum [Wasserzeichen](#watermark-specific-fields). |  |

### Wasserzeichenspezifische Felder {#watermark-specific-fields}

Das PNG-Format wird als Wasserzeichen verwendet.

| Name | Typ | Beschreibung | Beispiel |
|-------------------|----------|-------------|---------|
| `scale` | `number` | Skalierung des Wasserzeichens zwischen `0.0` und `1.0`. `1.0` bedeutet, dass das Wasserzeichen seine ursprüngliche Skalierung (1:1) hat und die niedrigeren Werte die Wasserzeichengröße verringern. | Ein Wert von `0.5` bedeutet die Hälfte der Originalgröße. |
| `image` | `url` | URL zur PNG-Datei, die zum Wasserzeichen verwendet werden soll. |  |

## Asynchrone Ereignis {#asynchronous-events}

Nachdem die Verarbeitung einer Darstellung abgeschlossen ist oder ein Fehler auftritt, wird ein Ereignis an ein E/A-Ereignis-Protokoll [der](https://www.adobe.io/apis/experienceplatform/events/documentation.html#!adobedocs/adobeio-events/master/intro/journaling_api.md)Adobe gesendet. Kunden müssen die Protokoll-URL abrufen, die über [/registrieren](#register)bereitgestellt wird. Die Protokoll-Antwort enthält ein `event` Array, das aus einem Objekt für jedes Ereignis besteht, von dem das `event` Feld die tatsächliche Ereignis-Nutzlast enthält.

Der E/A-Ereignistyp der Adobe für alle Ereignisse des [!DNL Asset Compute Service] ist `asset_compute`. Das Protokoll wird automatisch nur für diesen Ereignistyp abonniert und es besteht keine weitere Filterpflicht, die auf dem Adobe-E/A-Ereignistyp basiert. Die dienstspezifischen Ereignistyp stehen in der `type` Eigenschaft des Ereignisses zur Verfügung.

### Ereignistypen {#event-types}

| Ereignis | Beschreibung |
|---------------------|-------------|
| `rendition_created` | Wird für jede erfolgreich verarbeitete und hochgeladene Darstellung gesendet. |
| `rendition_failed` | Wird für jede Darstellung gesendet, bei der die Verarbeitung oder das Hochladen fehlgeschlagen ist. |

### Ereignis-Attribute {#event-attributes}

| Attribut | Typ | Ereignis | Beschreibung |
|-------------|----------|---------------|-------------|
| `date` | `string` | `*` | Zeitstempel, wenn Ereignis im vereinfachten erweiterten [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) -Format gesendet wurde, wie von JavaScript [Date.toISOString()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString)definiert. |
| `requestId` | `string` | `*` | Die Anforderungs-ID der ursprünglichen Anforderung an `/process`, identisch mit der `X-Request-Id` -Kopfzeile. |
| `source` | `object` | `*` | Die `source` des `/process` Antrags. |
| `userData` | `object` | `*` | Die Darstellung `userData` der `/process` Anforderung, falls festgelegt. |
| `rendition` | `object` | `rendition_*` | Das entsprechende Darstellungsobjekt, das übergeben wird `/process`. |
| `metadata` | `object` | `rendition_created` | Die [Metadateneigenschaften](#metadata) der Darstellung. |
| `errorReason` | `string` | `rendition_failed` | Grund [für](#error-reasons) einen Wiedergabefehler, falls vorhanden. |
| `errorMessage` | `string` | `rendition_failed` | Text mit detaillierteren Informationen zum ggf. fehlgeschlagenen Ausgabeformat. |

### Metadaten {#metadata}

| Eigenschaft | Beschreibung |
|--------|-------------|
| `repo:size` | Die Größe der Darstellung in Byte. |
| `repo:sha1` | Der SHA1-Digest der Darstellung. |
| `dc:format` | Der MIME-Typ der Darstellung. |
| `repo:encoding` | Die Zeichenanzahl-Kodierung der Darstellung, falls es sich um ein textbasiertes Format handelt. |
| `tiff:ImageWidth` | Die Breite der Darstellung in Pixel. Nur für Bilddarstellungen vorhanden. |
| `tiff:ImageLength` | Die Länge der Darstellung in Pixel. Nur für Bilddarstellungen vorhanden. |

### Fehlergründe {#error-reasons}

| Grund | Beschreibung |
|---------|-------------|
| `RenditionFormatUnsupported` | Das angeforderte Ausgabeformat wird für die angegebene Quelle nicht unterstützt. |
| `SourceUnsupported` | Die spezifische Quelle wird nicht unterstützt, obwohl der Typ unterstützt wird. |
| `SourceCorrupt` | Die Quelldaten sind beschädigt. Schließt leere Dateien ein. |
| `RenditionTooLarge` | Die Darstellung konnte nicht mithilfe der vorzeichenbehafteten URL(s) hochgeladen werden, die in `target`. Die tatsächliche Darstellungsgröße ist als Metadaten in verfügbar `repo:size` und kann vom Client zur erneuten Verarbeitung dieser Darstellung mit der richtigen Anzahl vorzeichenbehafteter URLs verwendet werden. |
| `GenericError` | Jeder andere unerwartete Fehler. |
