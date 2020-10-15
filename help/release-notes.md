---
title: Versionshinweise [!DNL Asset Compute Service].
description: Neue Funktionen, Verbesserungen und bekannte Probleme in [!DNL Asset Compute Service].
translation-type: tm+mt
source-git-commit: 68d910cd092fccb599c361f24daff80460129e1c
workflow-type: tm+mt
source-wordcount: '193'
ht-degree: 2%

---


# Versionshinweise zu [!DNL Asset Compute Service] {#release-notes}

Die neueste Version von [!DNL Asset Compute Service] wird am 30. Juli 2020 veröffentlicht.

<!--

To test your custom applications with the [developer tool](https://github.com/adobe/asset-compute-devtool), you need access to a [cloud storage container](https://github.com/adobe/asset-compute-devtool#prerequisites). Currently, Adobe supports Azure Blob Storage and AWS S3.

>[!NOTE]
>
>Cloud storage access is only required for using the developer tool. You can still create, test and deploy custom applications with out using the developer tool.
-->

## What is new {#what-is-new}

Dies ist die erste Version von [!DNL Asset Compute Service]. Es handelt sich um einen skalierbaren und erweiterbaren Dienst [!DNL Adobe Experience Cloud] zur Verarbeitung digitaler Assets. Sie können Bild-, Video-, Dokument- und andere Dateiformate in verschiedene Darstellungen umwandeln, einschließlich Miniaturansichten, extrahiertem Text und Metadaten sowie Archiven.

Zurzeit [!DNL Asset Compute Service] kann das nur in [!DNL Experience Manager] einem Cloud Service verwendet werden.

## Einschränkungen und bekannte Probleme {#known-limitations}

Um Ihre benutzerdefinierte Anwendung mit dem [Developer Tool](https://github.com/adobe/asset-compute-devtool)zu testen, benötigen Sie Zugriff auf einen [Cloud-Datenspeicherung-Container](https://github.com/adobe/asset-compute-devtool#prerequisites).

* Der Zugriff auf die Cloud-Datenspeicherung (anders als auf den [!DNL Experience Manager] Blob Store) ist nur für das Developer Tool erforderlich. Sie können benutzerdefinierte Anwendungen auch ohne das Developer Tool erstellen, testen und bereitstellen.
* Es kann sich um einen freigegebenen Container handeln, der von mehreren Entwicklern in verschiedenen Projekten verwendet wird.

## Beitragen {#contribute-open-source}

[!DNL Asset Compute Service] Erweiterbarkeit wird unter einem offenen Entwicklungsmodell auf github.com/adobe entwickelt, das Beiträge von Entwicklern von Erweiterungen [begrüßt](https://github.com/adobe) . Alle Komponenten, die für die Entwicklung, Erstellung, Prüfung und Bereitstellung von benutzerdefinierten Anwendungen relevant sind, sind Open Source. Erfahren Sie, [wie und wo Sie zum Compute Service](contribute-to-compute-service.md)beitragen können.

<!-- **TBD:**
* Are we versioning the releases?
* Is there any compatibility information to be added? With Project Firefly versions, or AEMaaCS releases, or other offerings/integrations such as InDesign Server?
-->
