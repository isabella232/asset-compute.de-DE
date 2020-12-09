---
title: Bereitstellen einer benutzerdefinierten  [!DNL Asset Compute Service] -Anwendung.
description: Bereitstellen einer benutzerdefinierten  [!DNL Asset Compute Service] -Anwendung.
translation-type: ht
source-git-commit: 78c1246f5fc42006013701a6cf4d375a1d8c9fd8
workflow-type: ht
source-wordcount: '183'
ht-degree: 100%

---


# Bereitstellen einer benutzerdefinierten Anwendung {#deploy-custom-application}

Verwenden Sie zum Bereitstellen der Anwendung den Befehl [aio app deploy](https://github.com/adobe/aio-cli#aio-appdeploy). Der Befehl zeigt im Terminal eine URL für den Zugriff auf die benutzerdefinierte Anwendung an. Die URL hat das Format `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]`.

Verwenden Sie den Befehl [`aio app get-url`](https://github.com/adobe/aio-cli#aio-appget-url-action), um die URL ohne erneute Bereitstellung der Anwendung abzurufen.

Verwenden Sie die URL in einem [Verarbeitungsprofil in  [!DNL Experience Manager]  as a  [!DNL Cloud Service]](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html?lang=de), um Ihre Anwendung mit [!DNL Experience Manager] as a [!DNL Cloud Service] zu integrieren.

Stellen Sie sicher, dass Ihr Firefly-Projekt und der Arbeitsbereich der Umgebung von [!DNL Experience Manager] as a [!DNL Cloud Service] entsprechen, in der Sie Ihre Aktion durchführen möchten. Es gibt verschiedene Umgebungen für Entwicklung, Staging und Produktion. Sie können die Umgebung überprüfen, indem Sie die `AIO_runtime_*`-Anmeldeinformationen überprüfen, die in Ihrer ENV-Datei im Stammverzeichnis Ihrer Firefly-Anwendung definiert sind. Um beispielsweise eine Bereitstellung in einem `Stage`-Arbeitsbereich vorzunehmen, hat `AIO_runtime_namespace` das Format `xxxxxx_xxxxxxxxx_stage`. Verwenden Sie zur Integration in eine Produktionsumgebung von [!DNL Experience Manager] as a [!DNL Cloud Service] die Anwendungs-URLs aus Ihrem Firefly-`Production`-Arbeitsbereich.

>[!CAUTION]
>
>Verwenden Sie keinen persönlichen Arbeitsbereich für wichtige [!DNL Experience Manager]-Umgebungen.

>[!MORELIKETHIS]
>
>* [Verstehen und Verwalten von Umgebungen in  [!DNL Experience Manager]  as a  [!DNL Cloud Service]](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/using-cloud-manager/manage-environments.html?lang=de).

