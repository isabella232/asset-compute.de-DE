---
title: Verstehen Sie, wie Sie erweitern [!DNL Asset Compute Service].
description: Wann und wie die Funktionalität [!DNL Asset Compute Service] für die Verarbeitung benutzerdefinierter Assets erweitert wird.
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '275'
ht-degree: 3%

---


# Einführung in die Erweiterbarkeit {#introduction-to-extensibilty}

Viele Darstellungsanforderungen, wie die Konvertierung in Formate und die Größenanpassung von Bildern, werden von [Verarbeitungswerkzeugen [!DNL Experience Manager] als Cloud Service](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/asset-microservices-overview.html)behandelt. Komplexere Geschäftsanforderungen erfordern möglicherweise eine benutzerdefinierte Lösung, die den Anforderungen eines Unternehmens entspricht. [!DNL Asset Compute Service] können erweitert werden, indem Sie benutzerdefinierte Anwendungen erstellen, die von &quot;Verarbeitungsanwendungen&quot;in [!DNL Experience Manager]aufgerufen werden. Diese benutzerdefinierten Anwendungen entsprechen den [unterstützten Anwendungsfällen](https://docs.adobe.com/content/help/de-DE/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html).

>[!NOTE]
>
>[!DNL Asset Compute Service] ist nur zur Verwendung mit [!DNL Experience Manager] als Cloud Service verfügbar.

Bei den benutzerdefinierten Anwendungen handelt es sich um kopflose [Project Firefly](https://github.com/AdobeDocs/project-firefly) -Apps. Die Erweiterung [!DNL Asset Compute Service] mit benutzerdefinierten Anwendungen wird durch die Werkzeuge [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk) und Project Firefly Developer vereinfacht. Dadurch können sich Entwickler auf Geschäftslogik konzentrieren. Das Erstellen benutzerdefinierter Anwendungen ist so einfach wie das Erstellen einer reinen serverllosen Adobe I/O Runtime-Aktion. Es handelt sich dabei um eine einzige JavaScript-Funktion von Node.js. Das [Beispiel](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) einer benutzerdefinierten Anwendung veranschaulicht dies.

## Voraussetzungen und Bereitstellungsanforderungen {#prerequisites-and-provisioning}

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Auf Ihrem Computer sind Project Firefly-Werkzeuge installiert.
* Eine [!DNL Experience Cloud] Organisation. Weitere Informationen [hier](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials).
* Die Experience Organisation muss [!DNL Experience Manager] als Cloud Service aktiviert sein.
* [!DNL Adobe Experience Cloud] ist Teil des Programms zur [!DNL Project Firefly] Vorschau von Entwicklern. Erfahren Sie, [wie Sie den Zugriff beantragen](https://github.com/AdobeDocs/project-firefly/blob/master/overview/getting_access.md).
* Stellen Sie sicher, dass die Rolle des Entwicklers oder die Administratorrechte im Unternehmen für den Entwickler gelten.
* Stellen Sie sicher, dass die [Adobe-I/O-CLI](https://github.com/adobe/aio-cli) lokal installiert ist.

<!-- TBD for later:

* What all accesses and licenses are required?
* What all permissions are required to create, debug, and deploy custom applications?
* How do developers get access and provision the required apps?
* What is repository management?
* Anything on security and data transfer?
* What about handling personal or sensitive information?
* Custom application SLA is dependent on SLAs of various services it depends on.
* Document how the devs can get to know the KPIs of their custom applications. The KPIs are dependent on the performance at Adobe's side, amongst other things.
-->
