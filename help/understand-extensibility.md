---
title: Lernen Sie, wie Sie  [!DNL Asset Compute Service] erweitern
description: Wann und wie die Funktionalität von [!DNL Asset Compute Service] für die Verarbeitung benutzerdefinierter Assets erweitert wird.
exl-id: 3b903364-34cc-44d5-9a03-24a0102cf85d
source-git-commit: 2dde177933477dc9ac2ff5a55af1fd2366e18359
workflow-type: tm+mt
source-wordcount: '260'
ht-degree: 78%

---

# Einführung in die Erweiterbarkeit {#introduction-to-extensibilty}

Viele Ausgabedarstellungsanforderungen, wie die Konvertierung in Formate und die Größenanpassung von Bildern, erfolgen über [Verarbeitungsprofile in  [!DNL Experience Manager]  as a  [!DNL Cloud Service]](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/asset-microservices-overview.html?lang=de). Komplexere Geschäftsanforderungen erfordern möglicherweise eine individuell erstellte Lösung, die den Anforderungen eines Unternehmens entspricht. [!DNL Asset Compute Service] kann erweitert werden, indem Sie benutzerdefinierte Programme erstellen, die von den Verarbeitungsprofilen in [!DNL Experience Manager] aufgerufen werden. Diese benutzerdefinierten Programme decken die [unterstützten Anwendungsfälle](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html?lang=de) ab.

>[!NOTE]
>
>[!DNL Asset Compute Service] ist nur zur Verwendung mit [!DNL Experience Manager] as a [!DNL Cloud Service] verfügbar.

Benutzerdefinierte Programme sind Headless [Adobe Developer App Builder](https://github.com/AdobeDocs/app-builder) Apps. Erweitern [!DNL Asset Compute Service] mit benutzerdefinierten Anwendungen durch die [asset compute SDK](https://github.com/adobe/asset-compute-sdk) und die Entwicklertools von Adobe Developer App Builder. Dadurch können sich Entwickler auf Geschäftslogik konzentrieren. Das Erstellen benutzerdefinierter Programme ist so einfach wie das Erstellen einer Server-losen [!DNL Adobe I/O] Runtime-Aktion. Es handelt sich dabei um eine einzige JavaScript-Funktion von Node.js. Dies wird durch das [grundlegende Beispiel eines benutzerdefinierten Programms](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) veranschaulicht.

## Voraussetzungen und Bereitstellungsanforderungen {#prerequisites-and-provisioning}

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Die Adobe Developer App Builder-Tools sind auf Ihrem Computer installiert.
* Eine [!DNL Experience Cloud]-Organisation. Weitere Informationen finden Sie [hier](https://developer.adobe.com/app-builder/docs/getting_started/#acquire-access-and-credentials).
* [!DNL Experience Manager] as a [!DNL Cloud Service] muss für die Experience-Organisation aktiviert sein.
* Die [!DNL Adobe Experience Cloud]-Organisation ist Teil des Developer Preview-Programms von [!DNL Adobe Developer App Builder]. Lernen Sie, [wie Sie den Zugriff beantragen](https://developer.adobe.com/app-builder/docs/overview/getting_access).
* Stellen Sie für den Entwickler eine Entwicklerrolle oder Administratorberechtigungen in der Organisation sicher.
* Stellen Sie sicher, dass [[!DNL Adobe I/O] CLI](https://github.com/adobe/aio-cli) lokal installiert ist.

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
