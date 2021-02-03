---
title: Fehlerbehebung [!DNL Asset Compute Service]
description: Fehlerbehebung und Debugging von benutzerdefinierten Programmen mit  [!DNL Asset Compute Service].
translation-type: ht
source-git-commit: 95e384d2a298b3237d4f93673161272744e7f44a
workflow-type: ht
source-wordcount: '288'
ht-degree: 100%

---


# Fehlerbehebung {#troubleshoot}

Einige allgemeine Tipps, die Ihnen bei der Fehlerbehebung mit Asset Compute Service helfen können:

* Stellen Sie sicher, dass das JavaScript-Programm beim Start nicht abstürzt. Solche Abstürze hängen normalerweise mit einer fehlenden Bibliothek oder Abhängigkeit zusammen.
* Stellen Sie sicher, dass alle zu installierenden Abhängigkeiten in der `package.json`-Datei des Programms referenziert sind.
* Stellen Sie sicher, dass alle Fehler, die von der Bereinigung eines Fehlers herrühren können, keine eigenen Fehler erzeugen, die das ursprüngliche Problem verdecken.

* Beim erstmaligen Starten des Entwickler-Tools mit einer neuen [!DNL Asset Compute Service]-Integration kann es vorkommen, dass die erste Verarbeitungsanfrage fehlschlägt, da das Asset Compute-Ereignisjournal möglicherweise nicht vollständig eingerichtet wurde. Warten Sie einige Zeit, bis das Journal eingerichtet ist, bevor Sie eine weitere Anfrage senden.
* Wenn beim Senden von `/register`- oder `/process`-Anfragen in Asset Compute Fehler auftreten, stellen Sie sicher, dass alle erforderlichen APIs zum [!DNL Adobe I/O]-Projekt und -Arbeitsbereich hinzugefügt werden, d. h. Asset Compute, [!DNL Adobe I/O] Events, [!DNL Adobe I/O] Events Management und [!DNL Adobe I/O] Runtime.

## Anmeldeprobleme über [!DNL Adobe I/O] CLI {#login-via-aio-cli}

Wenn Sie Probleme haben, sich [!DNL Adobe Developer Console] [über CLI [!DNL Adobe I/O]  bei](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli) anzumelden, fügen Sie manuell die Anmeldeinformationen hinzu, die zum Entwickeln, Testen und Bereitstellen Ihres benutzerdefinierten Programms erforderlich sind:

1. Navigieren Sie in der [Adobe Developer Console](https://console.adobe.io/) zu Ihrem Firefly-Projekt und Arbeitsbereich und klicken Sie oben rechts auf **[!UICONTROL Download]**. Öffnen Sie diese Datei und speichern Sie diese JSON an einem sicheren Ort auf Ihrem Computer.

1. Navigieren Sie in Ihrem Firefly-Programm zur ENV-Datei.

1. Fügen Sie die [!DNL Adobe I/O] Runtime-Anmeldeinformationen hinzu. Rufen Sie die [!DNL Adobe I/O] Runtime-Anmeldeinformationen von der heruntergeladenen JSON ab. Die Anmeldeinformationen finden Sie unter `project.workspace.services.runtime`. Fügen Sie die [!DNL Adobe I/O] Runtime-Anmeldeinformationen in den `AIO_runtime_XXX`-Variablen hinzu:

   ```json
   AIO_runtime_auth=
   AIO_runtime_namespace=
   ```

1. Fügen Sie den absoluten Pfad zur in Schritt 1 heruntergeladenen JSON hinzu:

   ```json
       ASSET_COMPUTE_INTEGRATION_FILE_PATH=
   ```

1. Richten Sie den Rest der [erforderlichen Anmeldeinformationen](develop-custom-application.md) für das Entwickler-Tool ein.

<!-- TBD for later:
Add any best practices for developers in this section:
* Any items to take care of when creating projects.
* Any naming conventions, reserved keywords, etc.?
* Any terms that can become a source of confusion later based on our OOTB naming.

* If required, add limitations for custom applications and spin those off as best practices.
* Do NOT borrow any content from https://git.corp.adobe.com/nui/nui/blob/master/doc/worker_api.md. It is outdated and irrelevant for 3rd party custom applications.
-->
