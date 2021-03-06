---
title: Auswählen einer Echtzeit- und Streamverarbeitungslösung in Azure
description: Erfahren Sie, wie Sie die richtige Technologie für Echtzeitanalysen und -Streamingverarbeitung auswählen, um Ihre Anwendung in Azure zu erstellen.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860247"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Auswählen einer Technologie für Echtzeitanalysen und -Streamingverarbeitung in Azure

Es stehen mehrere Dienste für Echtzeitanalysen und -Streamingverarbeitung in Azure zur Verfügung. Dieser Artikel enthält die Informationen, anhand derer Sie entscheiden können, welche Technologie sich am besten für Ihre Anwendung eignet.

## <a name="when-to-use-azure-stream-analytics"></a>Verwendung von Azure Stream Analytics

Azure Stream Analytics wird als Dienst für Streaminganalysen in Azure empfohlen. Der Dienst ist für eine Vielzahl von Szenarien konzipiert, unter anderem für folgende Szenarien:

* Dashboards für die Datenvisualisierung
* [Warnungen](stream-analytics-set-up-alerts.md) in Echtzeit zu temporalen und räumlichen Mustern oder Anomalien
* Extraktions-, Umwandlungs- und Ladeprozesse (Extract, Transform and Load, ETL)
* [Muster „Ereignissourcing“](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Das Hinzufügen eines Azure Stream Analytics-Auftrags in Ihrer Anwendung ist die schnellste Möglichkeit, Streaminganalysen unter Verwendung der bereits bekannten strukturierten Abfragesprache (SQL) in Azure auszuführen. Azure Stream Analytics ist ein Auftragsdienst, sodass Sie keine Zeit mit der Verwaltung von Clustern verbringen und sich nicht um Ausfallzeiten kümmern müssen, da der Dienst eine SLA mit einer Verfügbarkeit von 99,9 % auf Auftragsebene bietet. Auch die Abrechnung erfolgt auf Auftragsebene. Dadurch sind die Startkosten niedrig (eine Streamingeinheit), aber skalierbar (bis zu 192 Streamingeinheiten). Es ist sehr viel kostengünstiger, einige Stream Analytics-Aufträge auszuführen, als einen Cluster auszuführen und zu verwalten.

Azure Stream Analytics verfügt über eine umfangreiche direkt verwendungsbereite Umgebung. Sie können sofort ohne weitere Einrichtung die folgenden Funktionen nutzen:

* Integrierte temporale Operatoren, z. B. [Aggregate im Fenstermodus](stream-analytics-window-functions.md), temporale Verknüpfungen und temporale Analysefunktionen
* Native Azure-Adapter für [Eingabe](stream-analytics-add-inputs.md) und [Ausgabe](stream-analytics-define-outputs.md)
* Unterstützung für [Referenzdaten](stream-analytics-use-reference-data.md), die sich langsam ändern (auch als Nachschlagetabellen bezeichnet), z. B. Verknüpfung mit räumlichen Referenzdaten für Geofencing
* Integrierte Lösungen, z. B. [Anomalieerkennung](stream-analytics-machine-learning-anomaly-detection.md)
* Mehrere Zeitfenster in der gleichen Abfrage
* Möglichkeit, mehrere temporale Operatoren in beliebigen Sequenzen zu erstellen
* End-to-End-Latenz unter 100 ms von der Eingabe in Event Hubs bis zur Ausgabe in Event Hubs, einschließlich der Netzwerkverzögerung von und zu Event Hubs bei dauerhaft hohem Durchsatz

## <a name="when-to-use-other-technologies"></a>Verwendung anderer Technologien

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>UDFs, UDAs und benutzerdefinierte Deserialisierer in einer anderen Programmiersprache als JavaScript oder C#

Azure Stream Analytics unterstützt benutzerdefinierte Funktionen (UDF) oder benutzerdefinierte Aggregate (UDA) in JavaScript für Cloudaufträge und in C# für IoT Edge-Aufträge. Benutzerdefinierte C#-Deserialisierer werden auch unterstützt. Wenn Sie einen Deserialisierer, eine benutzerdefinierte Funktion oder ein benutzerdefiniertes Aggregat in anderen Programmiersprachen (z. B. Java oder Python) implementieren möchten, können Sie das strukturierte Spark-Streaming verwenden. Sie können außerdem den **EventProcessorHost** von Event Hubs auf Ihren virtuellen Computern ausführen, um eine beliebige Streamingverarbeitung durchzuführen.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Lösung in Umgebung mit mehreren Clouds oder einer lokalen Umgebung

Azure Stream Analytics ist die proprietäre Technologie von Microsoft und steht nur in Azure zur Verfügung. Wenn Ihre Lösung cloudübergreifend oder lokal portierbar sein soll, können Sie Open-Source-Technologien wie z. B. das strukturierte Spark-Streaming oder Storm verwenden.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio Code](quick-create-vs-code.md)
