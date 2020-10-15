---
title: Architektur von [!DNL Asset Compute Service].
description: ' [!DNL Asset Compute Service] WieAPI, Anwendungen und SDK zusammenarbeiten, um einen Cloud-nativen Asset-Verarbeitungsdienst bereitzustellen.'
translation-type: tm+mt
source-git-commit: 0fb256f7d9f83fbae564d9fd52ee6b2f34c5d7e9
workflow-type: tm+mt
source-wordcount: '496'
ht-degree: 0%

---


# Architektur [!DNL Asset Compute Service] {#overview}

Der [!DNL Asset Compute Service] ist auf einer serverllosen Adobe I/O Runtime-Plattform aufgebaut. Es bietet Adobe Sensei Content Services-Unterstützung für Assets. Der aufrufende Client (nur [!DNL Experience Manager] da ein Cloud Service unterstützt wird) erhält die Adobe Sensei-generierten Informationen, die er für den Asset gesucht hat. Die zurückgegebenen Informationen haben das JSON-Format.

[!DNL Asset Compute Service] kann erweitert werden, indem benutzerdefinierte Anwendungen basierend auf [!DNL Project Firefly]. Diese benutzerdefinierten Anwendungen sind [!DNL Project Firefly] kopflose Apps und führen Aufgaben wie das Hinzufügen benutzerdefinierter Konvertierungstools oder den Aufruf externer APIs durch, um Bildvorgänge durchzuführen.

[!DNL Project Firefly] ist ein Framework zum Erstellen und Bereitstellen von benutzerdefinierten Webanwendungen zur [!DNL Adobe I/O] Laufzeit. Um benutzerdefinierte Anwendungen zu erstellen, können die Entwickler das [!DNL React Spectrum] (UI-Toolkit der Adobe) nutzen, Mikrodienste erstellen, benutzerdefinierte Ereignis erstellen und APIs für die Orchestrierung erstellen. Siehe [Dokumentation des Projekts Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).

Die Grundlage, auf der die Architektur basiert, umfasst:

* Die Modularität der Applikationen - die nur das enthalten, was für eine bestimmte Aufgabe benötigt wird - ermöglicht es, die Applikationen voneinander zu trennen und so gering wie möglich zu halten.

* Das serverllose Konzept von Adobe I/O Runtime bietet zahlreiche Vorteile: asynchrone, hochskalierbare, isolierte, auftragsbasierte Verarbeitung, die perfekt zur Asset-Verarbeitung geeignet ist.

* Die binäre Cloud-Datenspeicherung bietet die erforderlichen Funktionen zum Speichern und Zugriff auf Asset-Dateien und Darstellungen einzeln, ohne dass dafür Vollzugriff auf die Datenspeicherung erforderlich ist. Dazu werden vorab signierte URL-Verweise verwendet. Übertragungsbeschleunigung, CDN-Zwischenspeicherung und das gemeinsame Auffinden von Computeranwendungen mit Cloud-Datenspeicherung ermöglichen einen optimalen Zugriff auf Inhalte mit niedriger Latenz. AWS- und Azurblauer-Wolken werden unterstützt.

![Architektur des Asset Compute Service](assets/architecture-diagram.png)

*Abbildung: Architektur [!DNL Asset Compute Service] und Integration in [!DNL Experience Manager], Datenspeicherung und Verarbeitungsanwendungen.*

Die Architektur besteht aus folgenden Teilen:

* **Eine API- und Orchesterebene** empfängt Anforderungen (im JSON-Format), die den Dienst anweisen, ein Quellelement in mehrere Darstellungen umzuwandeln. Diese Anforderungen sind asynchron und werden mit einer Aktivierungen-ID (auch &quot;Job-ID&quot;genannt) zurückgegeben. Anweisungen sind rein deklarativ, und für alle Standardverarbeitungsvorgänge (z. B. Miniaturansichten, Textdarstellung) geben die Extraktionen nur das gewünschte Ergebnis an, nicht jedoch die Anwendungen, die bestimmte Darstellungen bearbeiten. Generische API-Funktionen wie Authentifizierung, Analyse, Ratenbegrenzung werden mit dem Adobe API Gateway vor dem Dienst verarbeitet und verwaltet alle Anfragen, die zur I/O-Laufzeit gehen. Das Routing der Anwendung wird dynamisch von der Orchestrierungsebene ausgeführt. Benutzerdefinierte Anwendungen können von Clients für bestimmte Darstellungen angegeben werden und benutzerdefinierte Parameter enthalten. Die Anwendungsausführung kann vollständig parallelisiert werden, da es sich um separate serverlesene Funktionen in der I/O-Laufzeit handelt.

* **Anwendungen zur Verarbeitung von Assets** , die auf bestimmte Dateiformate oder Darstellungen von Zielgruppen spezialisiert sind. Eine Anwendung ähnelt dem Unix-Pipe-Konzept: eine Eingabedatei in eine oder mehrere Ausgabedateien umgewandelt wird.

* **Eine [gängige Anwendungsbibliothek](https://github.com/adobe/asset-compute-sdk)** verarbeitet gängige Aufgaben wie das Herunterladen der Quelldatei, das Hochladen der Ausgabeformate, den Berichte von Fehlermeldungen, das Senden und Überwachen von Ereignissen. Dies wurde so konzipiert, dass die Entwicklung einer Anwendung so einfach wie möglich bleibt, entsprechend der serverllosen Idee, und auf lokale Dateisysteminteraktionen beschränkt werden kann.

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
