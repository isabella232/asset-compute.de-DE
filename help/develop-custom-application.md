---
title: Entwickeln für [!DNL Asset Compute Service].
description: Erstellen Sie benutzerdefinierte Anwendungen mit [!DNL Asset Compute Service].
translation-type: tm+mt
source-git-commit: 127895cf1bab59546f9ba0be2b3b7a935627effb
workflow-type: tm+mt
source-wordcount: '1496'
ht-degree: 0%

---


# Entwickeln einer benutzerdefinierten Anwendung {#develop}

Bevor Sie mit der Entwicklung einer benutzerdefinierten Anwendung beginnen:

* Stellen Sie sicher, dass alle [Voraussetzungen](/help/understand-extensibility.md#prerequisites-and-provisioning) erfüllt sind.
* Installieren Sie die [erforderlichen Softwarewerkzeuge](/help/setup-environment.md#create-dev-environment).
* Siehe [Richten Sie Ihre Umgebung](setup-environment.md) ein, um sicherzustellen, dass Sie bereit sind, eine benutzerdefinierte Anwendung zu erstellen.

## Erstellen einer benutzerdefinierten Anwendung {#create-custom-application}

Stellen Sie sicher, dass die [Adobe-I/O-CLI](https://github.com/adobe/aio-cli) lokal installiert ist.

1. Um eine benutzerdefinierte Anwendung zu erstellen, [erstellen Sie eine Firefly-App](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#4-bootstrapping-new-app-using-the-cli). Führen Sie dazu im Terminal `aio app init <app-name>` aus.

   Wenn Sie sich noch nicht angemeldet haben, fordert Sie ein Browser auf, sich bei Ihrem Adobe ID bei der [Adobe Developer Console](https://console.adobe.io/) anzumelden. Weitere Informationen zum Anmelden von [der Anmeldung finden Sie](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli) hier.

   Adobe empfiehlt die Anmeldung. Wenn Probleme auftreten, befolgen Sie die Anweisungen [zum Erstellen einer App, ohne sich anzumelden](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#42-developer-is-not-logged-in-as-enterprise-organization-user).

1. Nachdem Sie sich angemeldet haben, befolgen Sie die Anweisungen in der CLI und wählen Sie die Optionen `Organization`, `Project`und `Workspace` für die Anwendung aus. Wählen Sie das Projekt und den Arbeitsbereich aus, die Sie beim [Einrichten der Umgebung](setup-environment.md)erstellt haben.

   ```sh
   $ aio app init <app-name>
   Retrieving information from Adobe I/O Console..
   ? Select Org My Adobe Org
   ? Select Project MyFireflyProject
   ? Select Workspace myworkspace
   create console.json
   ```

1. Wählen Sie bei Aufforderung `Which Adobe I/O App features do you want to enable for this project?`mindestens `Actions`:

   ```bash
   ? Which Adobe I/O App features do you want to enable for this project?
   select components to include (Press <space> to select, <a> to toggle all, <i> to invert selection)
   ❯◉ Actions: Deploy Runtime actions
   ◯ Events: Publish to Adobe I/O Events
   ◯ Web Assets: Deploy hosted static assets
   ◯ CI/CD: Include GitHub Actions based workflows for Build, Test and Deploy
   ```

1. Achten Sie bei Aufforderung `Which type of sample actions do you want to create?`auf die Auswahl `Adobe Asset Compute Worker`:

   ```bash
   ? Which type of sample actions do you want to create?
   Select type of actions to generate
   ❯◉ Adobe Asset Compute Worker
   ◯ Generic
   ```

1. Befolgen Sie die restlichen Anweisungen und öffnen Sie die neue Anwendung in Visual Studio Code (oder Ihrem Lieblings-Code-Editor). Es enthält die Gerüste und den Beispielcode für eine benutzerdefinierte Anwendung.

   Hier finden Sie Informationen zu den [Hauptkomponenten einer Firefly-App](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#5-anatomy-of-a-project-firefly-application).

   Die Vorlagenanwendung nutzt unser [Asset Compute-SDK](https://github.com/adobe/asset-compute-sdk#asset-compute-sdk) für das Hochladen, Herunterladen und Orchestrieren von Anwendungsdarstellungen, sodass Entwickler nur die benutzerdefinierte Anwendungslogik implementieren müssen. Innerhalb des `actions/<worker-name>` Ordners wird der benutzerdefinierte Anwendungscode in die `index.js` Datei eingefügt.

Beispiele und Ideen für benutzerdefinierte Anwendungen finden Sie unter [Beispiele für benutzerdefinierte Anwendungen](#try-sample) .

### hinzufügen {#add-credentials}

Während Sie sich beim Erstellen der Anwendung anmelden, werden die meisten Firefly-Anmeldeinformationen in Ihrer ENV-Datei erfasst. Die Verwendung des Developer Tools erfordert jedoch zusätzliche Anmeldeinformationen.

<!-- TBD: Check if manual setup of credentials is required.
Manual set up of credentials is removed from troubleshooting and best practices page. Link was broken.
If you did not log in, refer to our troubleshooting guide to [set up credentials manually](troubleshooting.md).
-->

#### Anmeldedaten für die Developer Tool-Datenspeicherung {#developer-tool-credentials}

Das Developer Tool zum Testen benutzerdefinierter Anwendungen mit dem tatsächlichen [!DNL Asset Compute service] erfordert einen Cloud-Datenspeicherung-Container zum Hosten von Testdateien und zum Empfangen und Anzeigen von Darstellungen, die von Anwendungen generiert wurden.

>[!NOTE]
>
>Dies ist von der Cloud-Datenspeicherung [!DNL Adobe Experience Manager] als Cloud Service getrennt. Es gilt nur für die Entwicklung und das Testen mit dem Asset Compute-Entwicklerwerkzeug.

Stellen Sie sicher, dass Sie Zugriff auf einen [unterstützten Cloud-Datenspeicherung-Container](https://github.com/adobe/asset-compute-devtool#prerequisites)haben. Dieser Container kann bei Bedarf von mehreren Entwicklern für verschiedene Projekte freigegeben werden.

#### Anmeldeinformationen für die ENV-Datei Hinzufügen {#add-credentials-env-file}

hinzufügen Sie die folgenden Anmeldeinformationen für das Developer Tool in die ENV-Datei im Stammordner Ihres Firefly-Projekts:

1. hinzufügen Sie den absoluten Pfad zur privaten Schlüsseldatei, die beim Hinzufügen von Diensten zu Ihrem Firefly-Projekt erstellt wurde:

   ```conf
   ASSET_COMPUTE_PRIVATE_KEY_FILE_PATH=
   ```

1. hinzufügen entweder S3- oder Azurblauer Datenspeicherung-Anmeldeinformationen. Sie benötigen nur Zugriff auf eine Cloud-Datenspeicherung.

   ```conf
   # S3 credentials
   S3_BUCKET=
   AWS_ACCESS_KEY_ID=
   AWS_SECRET_ACCESS_KEY=
   AWS_REGION=
   
   # Azure Storage credentials
   AZURE_STORAGE_ACCOUNT=
   AZURE_STORAGE_KEY=
   AZURE_STORAGE_CONTAINER_NAME=
   ```

## Anwendung ausführen {#run-custom-application}

Bevor Sie die Anwendung mit dem Asset Compute Developer Tool ausführen, müssen Sie die [Anmeldeinformationen](#developer-tool-credentials)ordnungsgemäß konfigurieren.

Um die Anwendung im Developer Tool auszuführen, verwenden Sie `aio app run` Befehl. Es stellt die Aktion für Adobe I/O Runtime bereit und Beginn das Entwicklungstool auf Ihrem lokalen Computer. Dieses Tool wird zum Testen von Anwendungsanforderungen während der Entwicklung verwendet. Im Folgenden finden Sie eine Beispielwiedergabeanforderung:

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg"
    }
]
```

>[!NOTE]
>
>Verwenden Sie nicht das `--local` Flag mit dem `run` Befehl. Es funktioniert nicht mit [!DNL Asset Compute] benutzerdefinierten Anwendungen und dem Asset Compute Developer-Tool. Benutzerdefinierte Anwendungen werden von der aufgerufen, [!DNL Asset Compute Service] die nicht auf Aktionen zugreifen können, die auf lokalen Computern des Entwicklers ausgeführt werden.

Sehen Sie [hier](test-custom-application.md) , wie Sie Ihre Anwendung testen und debuggen. Wenn Sie mit der Entwicklung Ihrer benutzerdefinierten Anwendung fertig sind, [stellen Sie Ihre benutzerdefinierte Anwendung](deploy-custom-application.md)bereit.

## Testen Sie die Beispielanwendung, die von der Adobe bereitgestellt wird {#try-sample}

Im Folgenden finden Sie Beispiele für benutzerdefinierte Anwendungen:

* [Worker-basic](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic)
* [Worker-Animal-images](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-animal-pictures)

### Benutzerdefinierte Vorlage {#template-custom-application}

Der [Worker-basic](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic) ist eine Vorlagenanwendung. Es generiert eine Darstellung, indem einfach die Quelldatei kopiert wird. Der Inhalt dieser Anwendung ist die Vorlage, die bei der Auswahl `Adobe Asset Compute` bei der Erstellung der App empfangen wird.

Die Anwendungsdatei [`worker-basic.js`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) lädt die Quelldatei [`asset-compute-sdk`](https://github.com/adobe/asset-compute-sdk#overview) herunter, orchestriert jede Darstellungsverarbeitung und lädt die resultierenden Darstellungen zurück in die Cloud-Datenspeicherung hoch.

Die [`renditionCallback`](https://github.com/adobe/asset-compute-sdk#rendition-callback-for-worker-required) Definition im Anwendungscode ist die Stelle, an der die gesamte Verarbeitungslogik der Anwendung ausgeführt werden soll. Der Rückruf bei der Darstellung in kopiert `worker-basic` einfach den Inhalt der Quelldatei in die Darstellungsdatei.

```javascript
const { worker } = require('@adobe/asset-compute-sdk');
const fs = require('fs').promises;

exports.main = worker(async (source, rendition) => {
    // copy source to rendition to transfer 1:1
    await fs.copyFile(source.path, rendition.path);
});
```

## Aufruf einer externen API {#call-external-api}

Im Anwendungscode können Sie externe API-Aufrufe durchführen, um die Anwendungsverarbeitung zu unterstützen. Nachfolgend finden Sie eine Beispielanwendungsdatei, die die externe API aufruft.

```javascript
exports.main = worker(async function (source, rendition) {

    const response = await fetch('https://adobe.com', {
        method: 'GET',
        Authorization: params.AUTH_KEY
    })
});
```

Beispielsweise [`worker-animal-pictures`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L46) stellt die Variable mithilfe der [`node-httptransfer`](https://github.com/adobe/node-httptransfer#node-httptransfer) Bibliothek eine Anfrage an eine statische URL von Wikimedia.

<!-- TBD: Revisit later to see if this note is required.
>[!NOTE]
>
>For extra authorization for these API calls, see [custom authorization checks](#custom-authorization-checks).
-->

### Übergeben benutzerdefinierter Parameter {#pass-custom-parameters}

Sie können benutzerdefinierte definierte Parameter über die Darstellungsobjekte übergeben. Sie können innerhalb der Anwendung in [`rendition` Anweisungen](https://github.com/adobe/asset-compute-sdk#rendition)referenziert werden. Ein Beispiel für ein Darstellungsobjekt ist:

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg",
        "my-custom-parameter": "my-custom-parameter-value"
    }
]
```

Ein Beispiel für eine Anwendungsdatei, die auf benutzerdefinierte Parameter zugreift:

```javascript
exports.main = worker(async function (source, rendition) {

    const customParam = rendition.instructions['my-custom-parameter'];
    console.log('Custom paramter:', customParam);
    // should print out `Custom parameter: "my-custom-parameter-value"`
});
```

Der `example-worker-animal-pictures` übergibt einen benutzerdefinierten Parameter, [`animal`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L39) um zu bestimmen, welche Datei von Wikimedia abgerufen werden soll.

## Unterstützung bei Authentifizierung und Autorisierung {#authentication-authorization-support}

Asset Compute-benutzerdefinierte Anwendungen werden standardmäßig mit Autorisierungs- und Authentifizierungsprüfungen für Firefly-Anwendungen geliefert. Dies wird aktiviert, indem Sie die `require-adobe-auth` Anmerkung `true` in der `manifest.yml`.

### Zugriff auf andere Adobe-APIs {#access-adobe-apis}

<!-- TBD: Revisit this section. Where do we document console workspace creation?
-->

hinzufügen Sie die API-Dienste in den im Setup erstellten [!DNL Asset Compute] Konsolenarbeitsbereich. Diese Dienste sind Teil des JWT-Zugriffstokens, das von [!DNL Asset Compute Service]erstellt wird. Auf das Token und andere Berechtigungen kann innerhalb des `params` Objekts für die Anwendungsaktion zugegriffen werden.

```javascript
const accessToken = params.auth.accessToken; // JWT token for Technical Account with entitlements from the console workspace to the API service
const clientId = params.auth.clientId; // Technical Account client Id
const orgId = params.auth.orgId; // Experience Cloud Organization
```

### Übergeben von Anmeldeinformationen für Drittanbietersysteme {#pass-credentials-for-tp}

Um Anmeldeinformationen für andere externe Dienste zu verarbeiten, übergeben Sie diese als Standardparameter für die Aktionen. Diese werden automatisch im Transit verschlüsselt. Weitere Informationen finden Sie unter [Erstellen von Aktionen im Laufzeitentwickler-Handbuch](https://www.adobe.io/apis/experienceplatform/runtime/docs.html#!adobedocs/adobeio-runtime/master/guides/creating_actions.md). Legen Sie sie dann während der Bereitstellung mithilfe von Umgebung-Variablen fest. Auf diese Parameter kann im `params` Objekt innerhalb der Aktion zugegriffen werden.

Legen Sie die Standardparameter in der `inputs` im `manifest.yml`:

```yaml
packages:
  __APP_PACKAGE__:
    actions:
      worker:
        function: 'index.js'
        runtime: 'nodejs:10'
        web: true
        inputs:
           secretKey: $SECRET_KEY
        annotations:
          require-adobe-auth: true
```

Der `$VAR` Ausdruck liest den Wert aus einer Umgebung mit dem Namen `VAR`.

Während der Entwicklung kann der Wert in der lokalen ENV-Datei festgelegt werden, da `aio` zusätzlich zu den in der invoking Shell festgelegten Variablen auch automatisch Umgebung aus ENV-Dateien gelesen werden. In diesem Beispiel sieht die ENV-Datei wie folgt aus:

```CONF
#...
SECRET_KEY=secret-value
```

Bei der Produktionsimplementierung kann man die Umgebung im CI-System einstellen, z. B. mithilfe von Geheimnissen in GitHub-Aktionen. Schließlich greifen Sie auf die Standardparameter in der Anwendung als solche zu:

```javascript
const key = params.secretKey;
```

## Größe von Anwendungen {#sizing-workers}

Eine Anwendung wird in einem Container in Adobe I/O Runtime mit [Einschränkungen](https://www.adobe.io/apis/experienceplatform/runtime/docs.html#!adobedocs/adobeio-runtime/master/guides/system_settings.md) ausgeführt, die über folgende Optionen konfiguriert werden können `manifest.yml`:

```yaml
    actions:
      myworker:
        function: /actions/myworker/index.js
        limits:
          timeout: 300000
          memorySize: 512
          concurrency: 1
```

Aufgrund der umfangreicheren Verarbeitung, die üblicherweise von Asset Compute-Anwendungen durchgeführt wird, müssen diese Grenzwerte eher für eine optimale Leistung (groß genug, um binäre Assets zu verarbeiten) und Effizienz (nicht wegen ungenutzten Container-Speichers zu verschwenden) angepasst werden.

Der Standard-Timeout für Aktionen in Laufzeit ist eine Minute, kann jedoch durch Festlegen des `timeout` Limit (in Millisekunden) erhöht werden. Wenn Sie mit der Verarbeitung größerer Dateien rechnen, erhöhen Sie diese Zeit. Berücksichtigen Sie die Gesamtdauer, die zum Herunterladen der Quelle, zum Verarbeiten der Datei und zum Hochladen der Darstellung erforderlich ist. Wenn eine Aktion eine Zeitüberschreitung aufweist, d. h. die Aktivierung nicht vor dem angegebenen Timeout-Grenzwert zurückgibt, verwirft die Laufzeit den Container und verwendet ihn nicht erneut.

Asset-Compute-Anwendungen sind naturgemäß eher an Netzwerk- und Disk-IO gebunden. Die Quelldatei muss zuerst heruntergeladen werden, die Verarbeitung ist häufig IO-groß und die resultierenden Darstellungen werden dann erneut hochgeladen.

Der für einen Action Container verfügbare Speicher wird in MB angegeben. `memorySize` Derzeit definiert dies auch, wie viel CPU-Zugriff der Container erhält, und vor allem ist dies ein Schlüsselelement der Kosten für die Verwendung von Runtime (größere Container kosten mehr). Verwenden Sie hier einen höheren Wert, wenn Ihre Verarbeitung mehr Arbeitsspeicher oder CPU benötigt, aber achten Sie darauf, keine Ressourcen zu verschwenden, da je größer die Container sind, desto niedriger der Gesamtdurchsatz ist.

Darüber hinaus ist es möglich, Aktionszeitgleichheit innerhalb eines Containers mithilfe der `concurrency` Einstellung zu steuern. Dies ist die Anzahl gleichzeitiger Aktivierungen, die ein Container (derselben Aktion) erhält. In diesem Modell ist der Action-Container wie ein Node.js-Server, der mehrere gleichzeitige Anforderungen bis zu diesem Grenzwert empfängt. Ist dies nicht der Fall, ist der Standardwert in Laufzeit 200, was für kleinere Firefly-Aktionen hervorragend ist, für Asset Compute-Anwendungen jedoch aufgrund ihrer intensiveren lokalen Verarbeitung und Disk-Aktivität in der Regel zu groß. Einige Anwendungen funktionieren je nach Implementierung möglicherweise auch bei gleichzeitiger Aktivität nicht gut. Das Asset Compute-SDK stellt sicher, dass Aktivierungen durch das Schreiben von Dateien in verschiedene eindeutige Ordner getrennt werden.

Testen Sie Anwendungen, um die optimalen Zahlen für `concurrency` und `memorySize`zu finden. Größere Container = höhere Speichergrenze könnte mehr Parallelität ermöglichen, könnte aber auch für geringeren Traffic verschwenderisch sein.
