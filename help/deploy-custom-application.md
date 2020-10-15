---
title: Benutzerdefinierte Anwendung [!DNL Asset Compute Service] bereitstellen.
description: Benutzerdefinierte Anwendung [!DNL Asset Compute Service] bereitstellen.
translation-type: tm+mt
source-git-commit: 1c2a1dc41296bf26c432c51b5afa20cb07a4c5c5
workflow-type: tm+mt
source-wordcount: '201'
ht-degree: 8%

---


# Eine benutzerdefinierte Anwendung bereitstellen {#deploy-custom-application}

Verwenden Sie zum Bereitstellen der Anwendung den [Befehl &quot;App bereitstellen](https://github.com/adobe/aio-cli#aio-appdeploy) &quot;. Im Terminal zeigt der Befehl eine URL für den Zugriff auf die benutzerdefinierte Anwendung an. Die URL hat das Format `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]`.

Verwenden Sie den [`aio app get-url`](https://github.com/adobe/aio-cli#aio-appget-url-action) Befehl, um die URL ohne erneute Bereitstellung der Anwendung abzurufen.

Verwenden Sie die URL in einem [Verarbeitungs-Profil in Experience Manager als Cloud Service](https://docs.adobe.com/content/help/de-DE/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html) , um Ihre Anwendung in [!DNL Experience Manager] einen Cloud Service zu integrieren.

Stellen Sie sicher, dass Ihr Firefly-Projekt und Ihre Arbeitsfläche der Umgebung [!DNL Experience Manager] als Cloud Service entsprechen, in der Sie Ihre Aktion durchführen möchten. Es hat verschiedene Umgebung für Entwicklung, Staging und Produktion. Sie können die Umgebung überprüfen, indem Sie die `AIO_runtime_*` Anmeldeinformationen überprüfen, die in Ihrer ENV-Datei im Stammverzeichnis Ihrer Firefly-Anwendung definiert sind. Um beispielsweise eine Bereitstellung in einem `Stage` Arbeitsbereich vorzunehmen, hat der `AIO_runtime_namespace` Benutzer das Format `xxxxxx_xxxxxxxxx_stage`. Verwenden Sie zur Integration [!DNL Experience Manager] als Cloud Service Production-Umgebung Anwendungs-URLs aus Ihrem Firefly- `Production` Arbeitsbereich.

>[!CAUTION]
>
>Verwenden Sie keinen persönlichen Arbeitsbereich für kritische [!DNL Experience Manager] Umgebung.

>[!MORELIKETHIS]
>
>* [Verstehen und Verwalten von Umgebung in Experience Manager als Cloud Service](https://docs.adobe.com/content/help/de-DE/experience-manager-cloud-service/implementing/using-cloud-manager/manage-environments.html).

