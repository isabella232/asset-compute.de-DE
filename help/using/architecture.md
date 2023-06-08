---
title: Architektur von  [!DNL Asset Compute Service]
description: So arbeiten [!DNL Asset Compute Service] -API, Programme und SDK zusammen, um einen Cloud-nativen Asset-Verarbeitungs-Service bereitzustellen.
exl-id: 658ee4b7-5eb1-4109-b263-1b7d705e49d6
source-git-commit: 5257e091730f3672c46dfbe45c3e697a6555e6b1
workflow-type: ht
source-wordcount: '486'
ht-degree: 100%

---

# Architektur von [!DNL Asset Compute Service] {#overview}

[!DNL Asset Compute Service] basiert auf der Server-losen [!DNL Adobe I/O] Runtime-Plattform. Der Service bietet Unterstützung mit Adobe Sensei-Inhalts-Services für Assets. Der aufrufende Client (es wird nur [!DNL Experience Manager] as a [!DNL Cloud Service] unterstützt) erhält die von Adobe Sensei generierten Informationen, nach denen er für das Asset gesucht hat. Die zurückgegebenen Informationen liegen im JSON-Format vor.

[!DNL Asset Compute Service] kann erweitert werden, indem benutzerdefinierte Programme basierend auf [!DNL Project Adobe Developer App Builder] erstellt werden. Diese benutzerdefinierten Programme sind Headless-[!DNL Project Adobe Developer App Builder]-Apps und führen Aufgaben wie das Hinzufügen benutzerdefinierter Konvertierungs-Tools oder den Aufruf externer APIs durch, um Bildvorgänge auszuführen.

[!DNL Project Adobe Developer App Builder] ist ein Framework zum Erstellen und Bereitstellen von benutzerdefinierten Web-Anwendungen zur [!DNL Adobe I/O]-Laufzeit. Um benutzerdefinierte Programme zu erstellen, können die Entwickler [!DNL React Spectrum] (das Toolkit von Adobe für Benutzeroberflächen) nutzen, Microservices erstellen, benutzerdefinierte Ereignisse erstellen und APIs koordinieren. Siehe [Dokumentation zu Adobe Developer App Builder](https://developer.adobe.com/app-builder/docs/overview).

Die Architektur basiert auf dem Folgenden:

* Die Modularität der Programme – sie enthalten nur das, was für eine bestimmte Aufgabe benötigt wird – erlaubt es, die Programme voneinander zu entkoppeln und sie einfach zu halten.

* Das Server-lose Konzept von [!DNL Adobe I/O] Runtime bietet zahlreiche Vorteile: asynchrone, hochskalierbare, isolierte, vorgangsbasierte Verarbeitung, die sich ideal für die Asset-Verarbeitung eignet.

* Der binäre Cloud-Speicher bietet die erforderlichen Funktionen zum individuellen Speichern von und Zugreifen auf Asset-Dateien und -Ausgabedarstellungen, ohne dass volle Zugriffsberechtigungen auf den Speicher erforderlich sind, und zwar unter Verwendung von vorab signierten URL-Referenzen. Übertragungsbeschleunigung, CDN-Caching und die gemeinsame Speicherung von Compute-Programme im Cloud-Speicher ermöglichen einen optimalen Zugriff auf Inhalte mit geringer Latenz. Es werden sowohl AWS- als auch Azure-Clouds unterstützt.

![Architektur von Asset Compute Service](assets/architecture-diagram.png)

*Abbildung: Architektur von [!DNL Asset Compute Service] und wie der Service mit [!DNL Experience Manager], Datenspeicherung und Verarbeitungsprogrammen zusammenarbeitet.*

Die Architektur besteht aus folgenden Teilen:

* **Eine API- und Orchestrierungsebene** empfängt Anforderungen (im JSON-Format), die den Service anweisen, ein Quellelement in mehrere Ausgabedarstellungen umzuwandeln. Die Anfragen sind asynchron und werden mit einer Aktivierungs-ID, also einer Vorgangs-ID, zurückgegeben. Die Anweisungen sind rein deklarativ. Bei allen Standardverarbeitungsvorgängen (z. B. Miniaturansichten, Textdarstellung) geben die Verbraucher nur das gewünschte Ergebnis an, nicht jedoch die Programme, die bestimmte Ausgabedarstellungen bearbeiten. Generische API-Funktionen wie Authentifizierung, Analyse, Ratenbegrenzung werden über das Adobe API Gateway vor dem Service abgewickelt und verwalten alle Anfragen, die an [!DNL Adobe I/O] Runtime gesendet werden. Das Programm-Routing wird dynamisch von der Orchestrierungsebene ausgeführt. Benutzerdefinierte Programme können von Clients für bestimmte Ausgabedarstellungen angegeben werden und benutzerdefinierte Parameter enthalten. Die Programmausführung kann vollständig parallelisiert werden, da es sich um separate Server-lose Funktionen in [!DNL Adobe I/O] Runtime handelt.

* **Programme zur Verarbeitung von Assets**, die auf bestimmte Dateiformate oder Zieldarstellungen spezialisiert sind. Ein Programm ähnelt dem Unix-Pipe-Konzept: eine Eingabedatei wird in eine oder mehrere Ausgabedateien umgewandelt.

* **Eine [gemeinsame Programmbibliothek](https://github.com/adobe/asset-compute-sdk)** verarbeitet allgemeine Aufgaben wie das Herunterladen der Quelldatei, das Hochladen der Ausgabedarstellungen, die Fehlerberichterstellung, das Senden und Überwachen von Ereignissen. Dies wurde so entwickelt, dass die Entwicklung eines Programms so einfach wie möglich bleibt, der Server-losen Idee folgt und auf lokale Dateisysteminteraktionen beschränkt werden kann.

<!-- TBD:

* About the YAML file?
* minimize description to custom applications
* remove all internal stuff (e.g. Photoshop application, API Gateway) from text and diagram
* update diagram to focus on 3rd party custom applications ONLY
* Explain important transactions/handshakes?
* Flow of assets/control? See the illustration on the Nui diagrams wiki.
* Illustrations. See the SVG shared by Alex.
* Exceptions? Limitations? Call-outs? Gotchas?
* Do we want to add what basic processing is not available currently, that is expected by existing AEM customers?
-->
