---
title: Legen Sie die erforderliche Entwicklungsumgebung für  [!DNL Asset Compute Service] fest
description: Einrichten einer Entwicklungsumgebung für [!DNL Asset Compute Service] , um benutzerdefinierten Code zu erstellen und zu testen.
exl-id: 91c12889-01d8-4757-9bdd-f73c491cd9d5
source-git-commit: 9404ffcc66a3b6ba206155d1b1a5c16a43e22a39
workflow-type: tm+mt
source-wordcount: '370'
ht-degree: 96%

---

# Einrichten einer Entwicklungsumgebung {#create-dev-environment}

Befolgen Sie diese Anforderungen und Anweisungen, um eine Entwicklungsumgebung für [!DNL Asset Compute Service] einzurichten.

1. [Erhalten Sie Zugriff auf und Anmeldeinformationen](https://www.adobe.io/project-firefly/docs/getting_started/#acquire-access-and-credentials) für [!DNL Project Firefly].

1. [Richten Sie die lokale Umgebung](https://www.adobe.io/project-firefly/docs/getting_started/#local-environment-set-up) und die erforderlichen Tools ein.

1. Einige weitere Tools, die Ihnen den reibungslosen Einstieg in die Entwicklung erleichtern, sind:

   * [Git](https://git-scm.com/).
   * [Docker Desktop](https://www.docker.com/get-started).
   * [NodeJS](https://nodejs.org) (Versionen 10 bis 12 LTS, ungerade Versionen werden nicht empfohlen) und [NPM](https://www.npmjs.com). Benutzer von OSX HomeBrew können `brew install node` ausführen, um beide zu installieren. Andernfalls laden Sie das Tool von der [NodeJS-Download-Seite](https://nodejs.org/de/) herunter.
   * Als IDE, die für NodeJS geeignet ist, empfehlen wir [Visual Studio Code (VS Code)](https://code.visualstudio.com), da dies die unterstützte IDE für den Debugger ist. Sie können jede andere IDE als Code-Editor verwenden, die erweiterte Verwendung (z. B. Debugger) wird jedoch noch nicht unterstützt.
   * [[!DNL Adobe I/O] CLI](https://github.com/adobe/aio-cli) (`aio`) – Installieren Sie mithilfe von `npm install -g @adobe/aio-cli@7.1.0`.

1. Stellen Sie sicher, dass Sie die [Voraussetzungen](/help/understand-extensibility.md#prerequisites-and-provisioning) erfüllen.

>[!NOTE]
>
>Verwenden Sie zunächst [!DNL Adobe I/O] CLI v7.1.0 von und verwenden Sie nicht [!DNL Adobe I/O] CLI v8.

## Einrichten eines Firefly-Projekts {#create-firefly-project}

1. Stellen Sie sicher, dass Sie über die Systemadministrator- oder Entwicklerrolle in der [!DNL Experience Cloud] -Organisation verfügen. Diese wird von einem Systemadministrator in der [Admin Console](https://adminconsole.adobe.com/overview) eingerichtet.

1. Melden Sie sich bei der [Adobe Developer Console](https://console.adobe.io/) an. [!DNL Experience Cloud]Stellen Sie sicher, dass Sie Teil derselben Organisation sind wie die [!DNL Experience Manager] as a [!DNL Cloud Service]-Integration. Weitere Informationen zur Adobe Developer Console finden Sie in der [Dokumentation zur Console](https://www.adobe.io/apis/experienceplatform/console/docs.html).

1. [Erstellen Sie ein Firefly-Projekt](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md). Klicken Sie auf **[!UICONTROL Create new project]** > **[!UICONTROL Project from Template]**. Wählen Sie „Firefly“ aus. Ein neues Firefly-Projekt mit zwei Arbeitsbereichen wird erstellt: `Production` und `Stage`. Fügen Sie bei Bedarf zusätzliche Arbeitsbereiche hinzu, z. B. `Development`.

1. Wählen Sie im Firefly-Projekt einen Arbeitsbereich aus und abonnieren Sie die Services, die für Asset Compute erforderlich sind. Klicken Sie auf **Add to Project** > **API** und fügen Sie die Services `Asset Compute`, `IO Events` und `IO Events Management` hinzu. Beim Hinzufügen der ersten API werden Sie aufgefordert, einen privaten Schlüssel zu erstellen. Speichern Sie diese Informationen auf Ihrem Computer, da Sie diesen Schlüssel zum Testen Ihrer benutzerdefinierten Programme mit dem Entwickler-Tool benötigen.

## Nächster Schritt {#next-step}

Nachdem Sie Ihre Umgebung eingerichtet haben, können Sie [ein benutzerdefiniertes Programm erstellen](develop-custom-application.md).

<!-- More ideas:
 
* Any steps in the beginning that lead to gotchas later should be called out for caution? For example,
  * don't change some defaults initially
  * know risks when deviating from standard path
  * naming conventions to follow
  * Retrieve and format credentials (YAML file details)

TBD: When aio-cli v8 bugs are resolved, update the AIO CLI install command to remove v7.x reference and instruct users to use the latest version. See CQDOC-18346.

-->
