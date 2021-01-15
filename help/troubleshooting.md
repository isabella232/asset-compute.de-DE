---
title: Fehlerbehebung [!DNL Asset Compute Service].
description: Fehlerbehebung und Debugging von benutzerdefinierten Anwendungen mit  [!DNL Asset Compute Service].
translation-type: tm+mt
source-git-commit: d26ae470507e187249a472ececf5f08d803a636c
workflow-type: tm+mt
source-wordcount: '290'
ht-degree: 79%

---


# Fehlerbehebung {#troubleshoot}

Einige allgemeine Tipps, die Ihnen bei der Fehlerbehebung mit Asset Compute Service helfen können:

* Stellen Sie sicher, dass die JavaScript-Anwendung beim Start nicht abstürzt. Solche Abstürze hängen normalerweise mit einer fehlenden Bibliothek oder Abhängigkeit zusammen.
* Stellen Sie sicher, dass alle zu installierenden Abhängigkeiten in der `package.json`-Datei der Anwendung referenziert sind.
* Stellen Sie sicher, dass alle Fehler, die von der Bereinigung eines Fehlers herrühren können, keine eigenen Fehler erzeugen, die das ursprüngliche Problem verdecken.

* Beim erstmaligen Starten des Entwickler-Tools mit einer neuen [!DNL Asset Compute Service]-Integration kann es vorkommen, dass die erste Verarbeitungsanfrage fehlschlägt, da das Asset Compute-Ereignisjournal möglicherweise nicht vollständig eingerichtet wurde. Warten Sie einige Zeit, bis das Journal eingerichtet ist, bevor Sie eine weitere Anfrage senden.
* Wenn beim Senden von Asset compute-`/register`- oder `/process`-Anforderungen Fehler auftreten, stellen Sie sicher, dass alle erforderlichen APIs dem [!DNL Adobe I/O]-Projekt und Arbeitsbereich hinzugefügt werden, d. h. Asset compute, IO-Ereignis, IO-Ereignisse-Verwaltung und Laufzeit.

## Anmeldungsprobleme über [!DNL Adobe I/O] CLI {#login-via-aio-cli}

Wenn Sie Probleme haben, sich [!DNL Adobe Developer Console][über CLI [!DNL Adobe I/O]  bei ](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli) anzumelden, fügen Sie manuell die Anmeldeinformationen hinzu, die zum Entwickeln, Testen und Bereitstellen Ihrer benutzerdefinierten Anwendung erforderlich sind:

1. Navigieren Sie in der [Adobe Developer Console](https://console.adobe.io/) zu Ihrem Firefly-Projekt und Arbeitsbereich und klicken Sie oben rechts auf **[!UICONTROL Download]**. Öffnen Sie diese Datei und speichern Sie diese JSON an einem sicheren Ort auf Ihrem Computer.

1. Navigieren Sie in Ihrer Firefly-Anwendung zur ENV-Datei.

1. hinzufügen Sie die Laufzeitberechtigungen [!DNL Adobe I/O]. Rufen Sie die [!DNL Adobe I/O]-Laufzeitberechtigungen vom heruntergeladenen JSON ab. Die Anmeldeinformationen finden Sie unter `project.workspace.services.runtime`. hinzufügen Sie die Laufzeitberechtigungen für [!DNL Adobe I/O] in den Variablen `AIO_runtime_XXX` an:

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
