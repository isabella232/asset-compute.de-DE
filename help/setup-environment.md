---
title: Legen Sie die erforderliche Umgebung für [!DNL Asset Compute Service]die Entwicklung fest.
description: Entwickler-Umgebung für das Erstellen und Testen von benutzerdefiniertem Beginn [!DNL Asset Compute Service] einrichten.
translation-type: tm+mt
source-git-commit: 1c2a1dc41296bf26c432c51b5afa20cb07a4c5c5
workflow-type: tm+mt
source-wordcount: '376'
ht-degree: 1%

---


# Einrichten einer Entwickler-Umgebung {#create-dev-environment}

Um ein Setup zu erstellen, das die Entwicklung für ermöglicht, befolgen Sie [!DNL Asset Compute Service]die folgenden Anforderungen und Anweisungen.

1. [Erhalten Sie Zugriff und Anmeldeinformationen](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials) für Project Firefly.

1. [Richten Sie die lokale Umgebung](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#local-environment-set-up) und die erforderlichen Tools ein.

1. Einige weitere Tools, die Ihnen beim reibungslosen Entwickeln helfen, sind:

   * [Git](https://git-scm.com/).
   * [Docker Desktop](https://www.docker.com/get-started).
   * [NodeJS](https://nodejs.org) (Versionen 10 bis 12 LTS, ungerade Versionen werden nicht empfohlen) und [NPM](https://www.npmjs.com). Benutzer von OSX HomeBrew können beides `brew install node` installieren. Andernfalls laden Sie es von der [NodeJS-Downloadseite](https://nodejs.org/en/)herunter.
   * Eine IDE, die gut für NodeJS geeignet ist. Wir empfehlen [Visual Studio Code (VS Code)](https://code.visualstudio.com) , da es die unterstützte IDE für den Debugger ist. Sie können jede andere IDE als Code-Editor verwenden, aber die erweiterte Verwendung (z. B. Debugger) wird noch nicht unterstützt.
   * [AIO CLI](https://github.com/adobe/aio-cli) (`aio`) - Installation mit `npm install -g @adobe/aio-cli`.

1. Achten Sie darauf, die [Voraussetzungen](/help/understand-extensibility.md#prerequisites-and-provisioning)zu erfüllen.

## Firefly-Projekt einrichten {#create-firefly-project}

1. Sie erhalten Zugriff auf Systemadministrator oder Entwicklerrolle in der Experience Organisation. Dies kann von einem Systemadministrator in der [Admin Console](https://adminconsole.adobe.com/overview)festgelegt werden.

1. Melden Sie sich bei der [Adobe Developer Console](https://console.adobe.io/)an. Stellen Sie sicher, dass Sie zur gleichen Adobe Experience Cloud-Organisation gehören wie die AEM als Cloud Service-Integration. Weitere Informationen zur Adobe Developer Console finden Sie in der [Konsolendokumentation](https://www.adobe.io/apis/experienceplatform/console/docs.html).

1. [Erstellen Sie ein Firefly-Projekt](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md). Klicken Sie auf Neues Projekt **** erstellen > **[!UICONTROL Projekt aus Vorlage]**. Wählen Sie Firefly. Es wird ein neues Firefly-Projekt mit zwei Arbeitsbereichen erstellt: `Production` und `Stage`. hinzufügen zusätzliche Arbeitsbereiche, z. B. `Development`nach Bedarf.

1. Wählen Sie im Firefly-Projekt einen Arbeitsbereich aus und abonnieren Sie die Dienste, die für die Asset-Berechnung erforderlich sind. Klicken Sie auf **Hinzufügen zu Projekt** > **API** und fügen Sie `Asset Compute`, `IO Events`und `IO Events Management` Dienste hinzu. Beim Hinzufügen der ersten API wird aufgefordert, einen privaten Schlüssel zu erstellen. Speichern Sie diese Informationen auf Ihrem Computer, da Sie diesen Schlüssel zum Testen Ihrer benutzerdefinierten Anwendung mit dem Developer Tool benötigen.

## Nächster Schritt {#next-step}

Nachdem Sie Ihre Umgebung eingerichtet haben, können Sie eine benutzerdefinierte Anwendung [erstellen](develop-custom-application.md).

<!-- TBD items for later:
 
* Any steps in the beginning that lead to gotchas later should be called out for caution? For example,
  * don't change some defaults initially
  * know risks when deviating from standard path
  * naming conventions to follow
  * Retrieve and format credentials (YAML file details)
-->
