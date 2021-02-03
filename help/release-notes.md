---
title: Versionshinweise zu  [!DNL Asset Compute Service]
description: Neue Funktionen, Verbesserungen und bekannte Probleme in  [!DNL Asset Compute Service].
translation-type: ht
source-git-commit: 95e384d2a298b3237d4f93673161272744e7f44a
workflow-type: ht
source-wordcount: '191'
ht-degree: 100%

---


# Versionshinweise zu [!DNL Asset Compute Service] {#release-notes}

Die neueste Version von [!DNL Asset Compute Service] wird am 30. Juli 2020 veröffentlicht.

<!--

To test your custom applications with the [developer tool](https://github.com/adobe/asset-compute-devtool), you need access to a [cloud storage container](https://github.com/adobe/asset-compute-devtool#prerequisites). Currently, Adobe supports Azure Blob Storage and AWS S3.

>[!NOTE]
>
>Cloud storage access is only required for using the developer tool. You can still create, test and deploy custom applications with out using the developer tool.
-->

## Neue Funktionen {#what-is-new}

Dies ist die erste Version von [!DNL Asset Compute Service]. Es handelt sich um einen skalierbaren und erweiterbaren Service von [!DNL Adobe Experience Cloud] zur Verarbeitung digitaler Assets. Sie können Bild-, Video-, Dokument- und andere Dateiformate in verschiedene Ausgabedarstellungen umwandeln, einschließlich Miniaturansichten, extrahiertem Text und Metadaten sowie Archiven.

Zurzeit kann [!DNL Asset Compute Service] nur in [!DNL Experience Manager] as a [!DNL Cloud Service] verwendet werden.

## Einschränkungen und bekannte Probleme {#known-limitations}

Um Ihr benutzerdefiniertes Programm mit dem [Entwickler-Tool](https://github.com/adobe/asset-compute-devtool) zu testen, benötigen Sie Zugriff auf einen [Cloud-Speicher-Container](https://github.com/adobe/asset-compute-devtool#prerequisites).

* Der Zugriff auf den Cloud-Speicher (anders als auf den [!DNL Experience Manager]-Blob-Speicher) ist nur für das Entwickler-Tool erforderlich. Sie können benutzerdefinierte Programme auch ohne das Entwickler-Tool erstellen, testen und bereitstellen.
* Es kann sich um einen freigegebenen Container handeln, der von mehreren Entwicklern in verschiedenen Projekten verwendet wird.

## Beitragen {#contribute-open-source}

Die Erweiterbarkeit von [!DNL Asset Compute Service] wird im Rahmen eines offenen Entwicklungsmodells auf [github.com/adobe](https://github.com/adobe) entwickelt, das Beiträge von Erweiterungsentwicklern begrüßt. Alle Komponenten, die für die Entwicklung, Erstellung, Prüfung und Bereitstellung von benutzerdefinierten Programmen relevant sind, sind Open Source. Erfahren Sie, [wie und wo Sie zu Compute Service beitragen können](contribute-to-compute-service.md).

<!-- **TBD:**
* Are we versioning the releases?
* Is there any compatibility information to be added? With Project Firefly versions, or AEMaaCS releases, or other offerings/integrations such as InDesign Server?
-->
