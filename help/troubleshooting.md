---
title: Fehlerbehebung [!DNL Asset Compute Service].
description: Fehlerbehebung und Debugging von benutzerdefinierten Anwendungen mit [!DNL Asset Compute Service].
translation-type: tm+mt
source-git-commit: 68d910cd092fccb599c361f24daff80460129e1c
workflow-type: tm+mt
source-wordcount: '306'
ht-degree: 1%

---


# Fehlerbehebung {#troubleshoot}

Einige allgemeine Tipps zur Fehlerbehebung, die Ihnen bei der Fehlerbehebung beim Asset Compute-Dienst helfen können, sind:

* Stellen Sie sicher, dass die JavaScript-Anwendung beim Start nicht abstürzt. Solche Abstürze sind in der Regel auf eine fehlende Bibliothek oder eine Abhängigkeit zurückzuführen.
* Stellen Sie sicher, dass alle zu installierenden Abhängigkeiten in der `package.json` Datei der Anwendung referenziert sind.
* Stellen Sie sicher, dass Fehler, die bei einer Bereinigung auftreten können, keine eigenen Fehler erzeugen, die das ursprüngliche Problem ausblenden.

* Beim erstmaligen Starten des Developer-Tools mit einer neuen [!DNL Asset Compute Service] Integration kann es vorkommen, dass die erste Verarbeitungsanfrage fehlschlägt, da das Asset Compute-Ereignis-Protokoll möglicherweise nicht vollständig eingerichtet wurde. Warten Sie einige Zeit, bis das Protokoll eingerichtet ist, bevor Sie eine weitere Anforderung senden.
* Wenn beim Senden von Asset Compute- `/register` oder `/process` -Anforderungen Fehler auftreten, stellen Sie sicher, dass alle erforderlichen APIs zum E/A-Projekt und Arbeitsbereich der Adobe hinzugefügt werden, d. h. zum Berechnen von Assets, zu IO-Ereignissen, zur Verwaltung von IO-Ereignissen und zur Laufzeit.

## Anmeldungsprobleme über Adobe-I/O-CLI {#login-via-aio-cli}

Wenn Sie Probleme haben, sich [!DNL Adobe Developer Console] über die Adobe-E/ [O-CLI](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli)bei der Anmeldung anzumelden, fügen Sie dann manuell die Anmeldeinformationen hinzu, die für die Entwicklung, Tests und Bereitstellung Ihrer benutzerdefinierten Anwendung erforderlich sind:

1. Navigieren Sie zu Ihrem Firefly-Projekt und zum Arbeitsbereich in der [Adobe Developer Console](https://console.adobe.io/) und drücken Sie die **[!UICONTROL Downloadtaste]** oben rechts. Öffnen Sie diese Datei und speichern Sie diese JSON an einem sicheren Ort auf Ihrem Computer.

1. Navigieren Sie zur ENV-Datei in Ihrer Firefly-Anwendung.

1. hinzufügen Sie die Adobe I/O Runtime-Anmeldeinformationen. Rufen Sie die Adobe I/O Runtime-Anmeldeinformationen vom heruntergeladenen JSON ab. The credentials are under `project.workspace.services.runtime`. hinzufügen der E/A-Laufzeitberechtigungen in den `AIO_runtime_XXX` Variablen:

   ```json
   AIO_runtime_auth=
   AIO_runtime_namespace=
   ```

1. hinzufügen Sie den absoluten Pfad zum heruntergeladenen JSON in Schritt 1:

   ```json
       ASSET_COMPUTE_INTEGRATION_FILE_PATH=
   ```

1. Richten Sie die restlichen [erforderlichen Anmeldeinformationen](develop-custom-application.md) für das Entwicklerwerkzeug ein.

<!-- TBD for later:
Add any best practices for developers in this section:
* Any items to take care of when creating projects.
* Any naming conventions, reserved keywords, etc.?
* Any terms that can become a source of confusion later based on our OOTB naming.

* If required, add limitations for custom applications and spin those off as best practices.
* Do NOT borrow any content from https://git.corp.adobe.com/nui/nui/blob/master/doc/worker_api.md. It is outdated and irrelevant for 3rd party custom applications.
-->
