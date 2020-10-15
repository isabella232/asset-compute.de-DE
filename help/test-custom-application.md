---
title: Benutzerdefinierte Anwendung testen und [!DNL Asset Compute Service] debuggen
description: Benutzerdefinierte Anwendung testen und [!DNL Asset Compute Service] debuggen
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '788'
ht-degree: 0%

---


# Testen und Debuggen einer benutzerdefinierten Anwendung {#test-debug-custom-worker}

## Komponententests für eine benutzerdefinierte Anwendung ausführen {#test-custom-worker}

Installieren Sie [Docker Desktop](https://www.docker.com/get-started) auf Ihrem Computer. Um einen benutzerdefinierten Worker zu testen, führen Sie den folgenden Befehl im Stamm der Anwendung aus:

```bash
$ aio app test
```

<!-- TBD
To run tests for a custom application, run `adobe-asset-compute test-worker` command in the root of the custom application application application.

Document interactively running `adobe-asset-compute` commands `test-worker` and `run-worker`.
-->

Dadurch wird ein benutzerdefiniertes Komponententestframework für Asset Compute-Anwendungsaktionen im Projekt ausgeführt, wie nachfolgend beschrieben. Er wird durch eine Konfiguration in der `package.json` Datei hochgeladen. Es ist auch möglich, JavaScript-Komponententests wie Jest durchzuführen. `aio app test` führt beide aus.

Das [Plugin aio-cli-plugin-asset-compute](https://github.com/adobe/aio-cli-plugin-asset-compute#install-as-local-devdependency) ist als Entwicklungsabhängigkeiten in der benutzerdefinierten App eingebettet, sodass es nicht auf Build/Test-Systemen installiert werden muss.

### Testrahmen für Anwendungseinheiten {#unit-test-framework}

Mit dem Asset Compute Application Unit Test Framework können Sie Anwendungen testen, ohne Code schreiben zu müssen. Es beruht auf dem Prinzip der Quelle für Darstellungsdateien von Anwendungen. Eine bestimmte Datei- und Ordnerstruktur muss eingerichtet werden, um Testfälle mit Testquelldateien, optionalen Parametern, erwarteten Darstellungen und benutzerdefinierten Überprüfungsskripten zu definieren. Standardmäßig werden die Darstellungen mit der Byte-Gleichheit verglichen. Darüber hinaus können externe HTTP-Dienste einfach mit einfachen JSON-Dateien verspottet werden.

### hinzufügen {#add-tests}

Tests werden im `test` Ordner auf der Stammebene des AIO-Projekts erwartet. Die Testfälle für jede Anwendung sollten sich im Pfad befinden `test/asset-compute/<worker-name>`, wobei für jeden Testfall ein Ordner vorhanden sein muss:

```yaml
action/
manifest.yml
package.json
...
test/
  asset-compute/
    <worker-name>/
        <testcase1>/
            file.jpg
            params.json
            rendition.png
        <testcase2>/
            file.jpg
            params.json
            rendition.gif
            validate
        <testcase3>/
            file.jpg
            params.json
            rendition.png
            mock-adobe.com.json
            mock-console.adobe.io.json
```

Sehen Sie sich einige Beispiele für [benutzerdefinierte Anwendungen](https://github.com/adobe/asset-compute-example-workers/) an. Nachstehend finden Sie eine ausführliche Referenz.

### Test output {#test-output}

Die detaillierte Testausgabe einschließlich der Protokolle der benutzerdefinierten Anwendung wird im `build` Ordner im Stammverzeichnis der Firefly-App bereitgestellt, wie in der Ausgabe gezeigt `aio app test` wird.

### Externe Dienstleistungen {#mock-external-services}

Sie können externe Dienstaufrufe in Ihren Aktionen verfolgen, indem Sie `mock-<HOST_NAME>.json` Dateien in Testfällen definieren, wobei HOST_NAME der Host ist, den Sie verfolgen möchten. Ein Beispielverwendungsfall ist eine Anwendung, die einen separaten Aufruf an S3 ausführt. Die neue Teststruktur würde wie folgt aussehen:

```json
test/
  asset-compute/
    <worker-name>/
      <testcase3>/
        file.jpg
        params.json
        rendition.png
        mock-<HOST_NAME1>.json
        mock-<HOST_NAME2>.json
```

Die Musterdatei ist eine HTTP-Antwort im JSON-Format. For more information, see [this documentation](https://www.mock-server.com/mock_server/creating_expectations.html). Wenn mehrere Hostnamen zu vergleichen sind, definieren Sie mehrere `mock-<mocked-host>.json` Dateien. Im Folgenden finden Sie eine Beispieldatei mit dem `google.com` Namen `mock-google.com.json`:

```json
[{
    "httpRequest": {
        "path": "/images/hello.txt"
        "method": "GET"
    },
    "httpResponse": {
        "statusCode": 200,
        "body": {
          "message": "hello world!"
        }
    }
}]
```

Das Beispiel `worker-animal-pictures` enthält eine [Musterdatei](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/test/asset-compute/worker-animal-pictures/simple-test/mock-upload.wikimedia.org.json) für den Wikimedia-Dienst, mit dem er interagiert.

#### Freigeben von Dateien in Testfällen {#share-files-across-test-cases}

Es wird empfohlen, relative Symlinks zu verwenden, wenn Sie mehrere Tests gemeinsam nutzen `file.*``params.json` oder Skripten `validate` nutzen. Sie werden mit git unterstützt. Achten Sie darauf, Ihren freigegebenen Dateien einen eindeutigen Namen zu geben, da Sie möglicherweise unterschiedliche Dateien haben. Im folgenden Beispiel werden einige freigegebene Dateien und ihre eigenen Dateien gemischt und zugeordnet:

```json
tests/
    file-one.jpg
    params-resize.json
    params-crop.json
    validate-image-compare

    jpg-png-resize/
        file.jpg    - symlink: ../file-one.jpg
        params.json - symlink: ../params-resize.json
        rendition.png
        validate    - symlink: ../validate-image-compare

    jpg2-png-crop/
        file.jpg
        params.json - symlink: ../params-crop.json
        rendition.gif
        validate    - symlink: ../validate-image-compare

    jpg-gif-crop/
        file.jpg    - symlink: ../file-one.jpg
        params.json - symlink: ../params-crop.json
        rendition.gif
        validate
```

### Erwartete Fehler testen {#test-unexpected-errors}

Fehlertestfälle sollten keine erwartete `rendition.*` Datei enthalten und die erwarteten Werte `errorReason` in der `params.json` Datei definieren.

Fehlerprüfungsfallstruktur:

```json
<error_test_case>/
    file.jpg
    params.json
```

Parameterdatei mit Fehlergrund:

```javascript
{
    "errorReason": "SourceUnsupported",
    // ... other params
}
```

Siehe vollständige Liste und Beschreibung der [Asset-Compute-Fehlergründe](https://github.com/adobe/asset-compute-commons#error-reasons).

## Debuggen einer benutzerdefinierten Anwendung {#debug-custom-worker}

Die folgenden Schritte zeigen, wie Sie Ihre benutzerdefinierte Anwendung mit Visual Studio-Code debuggen können. Es ermöglicht die Anzeige von Live-Protokollen, Treffer-Haltepunkten und schrittweisen Code sowie das Live-Neuladen lokaler Codeänderungen bei jeder Aktivierung.

Viele dieser Schritte werden in der Regel standardmäßig automatisiert. Weitere Informationen finden Sie im Abschnitt Debugging der Anwendung in der Dokumentation `aio` [](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md)Firefly. Die folgenden Schritte beinhalten derzeit eine Problemumgehung.

1. Installieren Sie das neueste [wskdebug](https://github.com/apache/openwhisk-wskdebug) von GitHub und das optionale [ngrok](https://www.npmjs.com/package/ngrok).

   ```shell
   npm install -g @openwhisk/wskdebug
   npm install -g ngrok --unsafe-perm=true
   ```

1. hinzufügen in die JSON-Datei mit Ihren Benutzereinstellungen. Es verwendet weiterhin den alten VS-Code-Debugger, der neue hat [einige Probleme](https://github.com/apache/openwhisk-wskdebug/issues/74) mit wskdebug: `"debug.javascript.usePreview": false`.
1. Schließen Sie alle Apps, die über `aio app run`geöffnet werden.
1. Stellen Sie den neuesten Code mit `aio app deploy`bereit.
1. Führen Sie nur das Asset-Tool zum Berechnen des Geräts aus `npx adobe-asset-compute devtool`. Halte es offen.
1. Fügen Sie im VS-Code-Editor die folgende Debugkonfiguration zu Ihrem Code hinzu `launch.json`:

   ```json
   {
     "type": "node",
     "request": "launch",
     "name": "wskdebug worker",
     "runtimeExecutable": "wskdebug",
     "args": [
       "PROJECT-0.0.1/__secured_worker",           // Replace this with your ACTION NAME
       "${workspaceFolder}/actions/worker/index.js",
       "-l",
       "--ngrok"
     ],
     "localRoot": "${workspaceFolder}",
     "remoteRoot": "/code",
     "outputCapture": "std",
     "timeout": 30000
   }
   ```

   Abrufen des ACTION-NAMENS aus der Ausgabe von `aio app deploy`. Es sieht aus wie `Your deployed actions -> TypicalCoffeeCat-0.0.1/__secured_worker`.

1. Wählen Sie `wskdebug worker` in der Konfiguration &quot;Ausführen/Debuggen&quot;aus und drücken Sie das Symbol &quot;Abspielen&quot;. Warten Sie, bis der Beginn im Fenster &quot; **[!UICONTROL Bereit für Aktivierungen]** &quot; **[!UICONTROL Debug-Konsole]** angezeigt wird.

1. Klicken Sie im Devtool auf **[!UICONTROL Ausführen]** . Sie können die ausgeführten Aktionen im VS-Code-Editor sehen und den Beginn der Protokolle anzeigen.

1. Legen Sie einen Haltepunkt im Code fest, führen Sie ihn erneut aus und drücken Sie ihn.

Alle Codeänderungen werden in Echtzeit geladen und sind wirksam, sobald die nächste Aktivierung eintritt.

>[!NOTE]
>
>Für jede Anforderung in benutzerdefinierten Anwendungen stehen zwei Aktivierungen zur Verfügung. Die erste Anforderung ist eine Webaktion, die sich im SDK-Code asynchron aufruft. Die zweite Aktivierung trifft den Code.
