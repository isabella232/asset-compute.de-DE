---
title: Architektur von  [!DNL Asset Compute Service]
description: So arbeiten  [!DNL Asset Compute Service] -API, Anwendungen und SDK zusammen, um einen Cloud-nativen Asset-Verarbeitungs-Service bereitzustellen.
translation-type: tm+mt
source-git-commit: 95e384d2a298b3237d4f93673161272744e7f44a
workflow-type: tm+mt
source-wordcount: '485'
ht-degree: 82%

---


# Architektur von [!DNL Asset Compute Service] {#overview}

Das [!DNL Asset Compute Service] ist auf der Laufzeitplattform ohne Server [!DNL Adobe I/O] aufgebaut. Der Service bietet Unterstützung mit Adobe Sensei-Inhalts-Services für Assets. Der aufrufende Client (es wird nur [!DNL Experience Manager] as a [!DNL Cloud Service] unterstützt) erhält die von Adobe Sensei generierten Informationen, nach denen er für das Asset gesucht hat. Die zurückgegebenen Informationen liegen im JSON-Format vor.

[!DNL Asset Compute Service] kann erweitert werden, indem benutzerdefinierte Anwendungen basierend auf [!DNL Project Firefly] erstellt werden. Diese benutzerdefinierten Anwendungen sind Headless-[!DNL Project Firefly]-App und führen Aufgaben wie das Hinzufügen benutzerdefinierter Konvertierungs-Tools oder den Aufruf externer APIs durch, um Bildvorgänge auszuführen.

[!DNL Project Firefly] ist ein Framework zum Erstellen und Bereitstellen von benutzerdefinierten Web-Anwendungen zur [!DNL Adobe I/O]-Laufzeit. Um benutzerdefinierte Anwendungen zu erstellen, können die Entwickler [!DNL React Spectrum] (das Toolkit von Adobe für Benutzeroberflächen) nutzen, Microservices erstellen, benutzerdefinierte Ereignisse erstellen und APIs koordinieren. Weitere Informationen finden Sie in der [Dokumentation zu Project Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).

Die Architektur basiert auf dem Folgenden:

* Die Modularität der Anwendungen – sie enthalten nur das, was für eine bestimmte Aufgabe benötigt wird – erlaubt es, die Anwendungen voneinander zu entkoppeln und sie einfach zu halten.

* Das serverllose Konzept von [!DNL Adobe I/O] Runtime bietet zahlreiche Vorteile: asynchrone, hochskalierbare, isolierte, auftragsbasierte Verarbeitung, die perfekt zur Asset-Verarbeitung geeignet ist.

* Der binäre Cloud-Speicher bietet die erforderlichen Funktionen zum individuellen Speichern von und Zugreifen auf Asset-Dateien und -Ausgabedarstellungen, ohne dass volle Zugriffsberechtigungen auf den Speicher erforderlich sind, und zwar unter Verwendung von vorab signierten URL-Referenzen. Übertragungsbeschleunigung, CDN-Caching und die gemeinsame Speicherung von Compute-Anwendungen im Cloud-Speicher ermöglichen einen optimalen Zugriff auf Inhalte mit geringer Latenz. Es werden sowohl AWS- als auch Azure-Clouds unterstützt.

![Architektur von Asset Compute Service](assets/architecture-diagram.png)

*Abbildung: Architektur von [!DNL Asset Compute Service] und wie der Service mit [!DNL Experience Manager], Datenspeicherung und Verarbeitungsanwendungen zusammenarbeitet.*

Die Architektur besteht aus folgenden Teilen:

* **Eine API- und Orchestrierungsebene** empfängt Anforderungen (im JSON-Format), die den Service anweisen, ein Quellelement in mehrere Ausgabedarstellungen umzuwandeln. Die Anforderungen sind asynchron und werden mit einer Aktivierungen-ID zurückgegeben, d. h. mit einer Job-ID. Die Anweisungen sind rein deklarativ. Bei allen Standardverarbeitungsvorgängen (z. B. Miniaturansichten, Textdarstellung) geben die Verbraucher nur das gewünschte Ergebnis an, nicht jedoch die Anwendungen, die bestimmte Ausgabedarstellungen bearbeiten. Generische API-Funktionen wie Authentifizierung, Analyse, Ratenbegrenzung werden mit dem Adobe API Gateway vor dem Dienst verarbeitet und verwaltet alle Anfragen, die zur [!DNL Adobe I/O]-Laufzeit gehen. Das Anwendungs-Routing wird dynamisch von der Orchestrierungsebene ausgeführt. Benutzerdefinierte Anwendungen können von Clients für bestimmte Ausgabedarstellungen angegeben werden und benutzerdefinierte Parameter enthalten. Die Anwendungsausführung kann vollständig parallelisiert werden, da es sich um separate serverlesene Funktionen in der [!DNL Adobe I/O]-Laufzeit handelt.

* **Anwendungen zur Verarbeitung von Assets**, die auf bestimmte Dateiformate oder Zieldarstellungen spezialisiert sind. Eine Anwendung ähnelt dem Unix-Pipe-Konzept: eine Eingabedatei wird in eine oder mehrere Ausgabedateien umgewandelt.

* **Eine [gemeinsame Anwendungsbibliothek](https://github.com/adobe/asset-compute-sdk)** verarbeitet allgemeine Aufgaben wie das Herunterladen der Quelldatei, das Hochladen der Ausgabedarstellungen, die Fehlerberichterstellung, das Senden und Überwachen von Ereignissen. Dies wurde so entwickelt, dass die Entwicklung einer Anwendung so einfach wie möglich bleibt, der Server-losen Idee folgt und auf lokale Dateisysteminteraktionen beschränkt werden kann.

<!-- TBD:

* About the YAML file?
* See [https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#5-anatomy-of-a-project-firefly-application](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#5-anatomy-of-a-project-firefly-application).

* minimize description to custom applications
* remove all internal stuff (e.g. Photoshop application, API Gateway) from text and diagram
* update diagram to focus on 3rd party custom applications ONLY
* Explain important transactions/handshakes?
* Flow of assets/control? See the illustration on the Nui diagrams wiki.
* Illustrations. See the SVG shared by Alex.
* Exceptions? Limitations? Call-outs? Gotchas?
* Do we want to add what basic processing is not available currently, that is expected by existing AEM customers?
-->
