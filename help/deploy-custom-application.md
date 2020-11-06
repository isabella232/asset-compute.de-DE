---
title: Bereitstellen einer benutzerdefinierten  [!DNL Asset Compute Service] -Anwendung.
description: Bereitstellen einer benutzerdefinierten  [!DNL Asset Compute Service] -Anwendung.
translation-type: ht
source-git-commit: 1c2a1dc41296bf26c432c51b5afa20cb07a4c5c5
workflow-type: ht
source-wordcount: '201'
ht-degree: 100%

---


# Bereitstellen einer benutzerdefinierten Anwendung {#deploy-custom-application}

Verwenden Sie zum Bereitstellen der Anwendung den Befehl [aio app deploy](https://github.com/adobe/aio-cli#aio-appdeploy). Der Befehl zeigt im Terminal eine URL für den Zugriff auf die benutzerdefinierte Anwendung an. Die URL hat das Format `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]`.

Verwenden Sie den Befehl [`aio app get-url`](https://github.com/adobe/aio-cli#aio-appget-url-action), um die URL ohne erneute Bereitstellung der Anwendung abzurufen.

Verwenden Sie die URL in einem [Verarbeitungsprofil in Experience Manager as a Cloud Service](https://docs.adobe.com/content/help/de-DE/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html), um Ihre Anwendung in [!DNL Experience Manager] as a Cloud Service zu integrieren.

Stellen Sie sicher, dass Ihr Firefly-Projekt und der Arbeitsbereich der Umgebung von [!DNL Experience Manager] as a Cloud Service entsprechen, in der Sie Ihre Aktion durchführen möchten. Es gibt verschiedene Umgebungen für Entwicklung, Staging und Produktion. Sie können die Umgebung überprüfen, indem Sie die `AIO_runtime_*`-Anmeldeinformationen überprüfen, die in Ihrer ENV-Datei im Stammverzeichnis Ihrer Firefly-Anwendung definiert sind. Um beispielsweise eine Bereitstellung in einem `Stage`-Arbeitsbereich vorzunehmen, hat `AIO_runtime_namespace` das Format `xxxxxx_xxxxxxxxx_stage`. Verwenden Sie zur Integration in eine Produktionsumgebung von [!DNL Experience Manager] as a Cloud Service die Anwendungs-URLs aus Ihrem Firefly-`Production`-Arbeitsbereich.

>[!CAUTION]
>
>Verwenden Sie keinen persönlichen Arbeitsbereich für wichtige [!DNL Experience Manager]-Umgebungen.

>[!MORELIKETHIS]
>
>* [Verstehen und Verwalten von Umgebungen in Experience Manager as a Cloud Service](https://docs.adobe.com/content/help/de-DE/experience-manager-cloud-service/implementing/using-cloud-manager/manage-environments.html).

