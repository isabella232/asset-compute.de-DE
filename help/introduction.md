---
title: Einführung in die [!DNL Asset Compute Service].
description: '[!DNL Asset Compute Service] ist ein Cloud-nativer Asset-Verarbeitungsdienst, der die Komplexität reduziert und die Skalierbarkeit verbessert.'
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '332'
ht-degree: 2%

---


# Übersicht über [!DNL Asset Compute Service] {#overview}

[!DNL Asset Compute Service] ist ein skalierbarer und erweiterbarer Dienst [!DNL Adobe Experience Cloud] zur Verarbeitung digitaler Assets. Sie können Bild-, Video-, Dokument- und andere Dateiformate in verschiedene Darstellungen umwandeln, einschließlich Miniaturansichten, extrahiertem Text und Metadaten sowie Archiven.

Entwickler können benutzerdefinierte Asset-Anwendungen (auch als benutzerdefinierte Mitarbeiter bezeichnet) hinzufügen, um benutzerdefinierte Anwendungsfälle zu bearbeiten. Der Dienst funktioniert zur [!DNL Adobe I/O] Laufzeit. Es kann durch [!DNL Project Firefly] kopflose Apps in Node.js erweitert werden. Diese können benutzerdefinierte Vorgänge ausführen, z. B. externe APIs aufrufen, um Bildvorgänge durchzuführen oder die [!DNL Adobe Sensei] Unterstützung zu nutzen.

[!DNL Project Firefly] ist ein Framework zum Erstellen und Bereitstellen benutzerdefinierter Webanwendungen zur [!DNL Adobe I/O] Laufzeit, um Adobe Experience Cloud-Lösungen zu erweitern. Um benutzerdefinierte Anwendungen zu erstellen, können die Entwickler das [!DNL React Spectrum] (UI-Toolkit der Adobe) nutzen, Mikrodienste erstellen, benutzerdefinierte Ereignis erstellen und APIs für die Orchestrierung erstellen. Siehe [Dokumentation des Projekts Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).

>[!NOTE]
>
>Zurzeit [!DNL Asset Compute Service] kann das nur über [!DNL Experience Manager] einen Cloud Service verwendet werden. Administratoren erstellen verarbeitende Profil, die die aufrufen können, [!DNL Asset Compute Service] um Assets zur Verarbeitung zu übergeben. Siehe [Verwenden von Asset-Mikrodiensten und Verarbeiten von Profilen](https://docs.adobe.com/content/help/de-DE/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html).

## Unterstützte Anwendungsfälle von [!DNL Asset Compute Service] {#possible-use-cases-benefits}

[!DNL Asset Compute Service] unterstützt einige gängige Geschäftsanwendungsfälle wie die grundlegende Bildverarbeitung; anwendungsspezifische Umrechnungen der Adobe; und benutzerdefinierte Anwendungen erstellen, die komplexe Geschäftsanforderungen koordinieren.

Sie können den [!DNL Asset Compute] Webdienst verwenden, um Miniaturansichten für verschiedene Dateitypen zu generieren. Hochwertige Bildwiedergaben für die [unterstützten Dateiformate](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html). Siehe [Anwendungsfälle, die von der benutzerdefinierten Konfiguration](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html#custom-config)unterstützt werden.

>[!NOTE]
>
>Der Dienst stellt keine Asset-Datenspeicherung bereit. Die Benutzer stellen die Datei bereit und geben Verweise auf die Speicherorte der Quell- und Darstellungsdateien in der Cloud-Datenspeicherung an.

<!-- TBD: Should this be mentioned in the docs?

|Asset Compute Service does not do this|Expectations from implementing client|
|---|---|
| Binary uploads or API-based asset ingestion. | Use other methods to ingest assets. |
| Store binaries or any persisted data across processing requests.| Each request is independent so treat it as a standalone request by sharing binary and processing instructions. |
| Store any configurations such as processing rules or settings for a user or an organization's account. | Add processing request to each request/instruction. |
| Direct event handling of asset creation events from storage systems and processing completed notifications, and errors. | Use Adobe I/O Events and other methods. |

-->

>[!MORELIKETHIS]
>
>* [Übersicht über die Asset-Verarbeitung mit Asset Microservices in Adobe Experience Manager als Cloud Service](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/asset-microservices-overview.html).
>* [Dokumentation des Projekts Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).
>* [Unterstützte Dateiformate für die Verarbeitung](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html).
>* [Versionshinweise zum Asset Compute-Dienst](release-notes.md)


<!-- **TBD:**
* Clarify the service can only be used within AEM as Cloud Service. The docs provided as context for custom application developers. Not to be used as a standalone service.
  ** and API as that plays a role in custom applications (accepting standard params, invoking Nui itself in the future, etc. (this is an outlook))

* link to aem as cloud service docs on asset ingestion and customization with processing profiles.
-->
