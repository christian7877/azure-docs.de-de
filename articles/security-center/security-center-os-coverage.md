---
title: Von Azure Security Center unterstützte Plattformen | Microsoft-Dokumentation
description: Dieses Dokument enthält eine Liste der von Azure Security Center unterstützten Plattformen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: e13149ba802f0f8b9a565e0aabd86ae05167f18b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208816"
---
# <a name="supported-platforms"></a>Unterstützte Plattformen 

## <a name="virtual-machines--servers"></a>Virtuelle Computer/Server <a name="vm-server"></a>

Security Center unterstützt virtuelle Computer/Server in unterschiedlichen Hybridumgebungen:

* Nur Azure
* Azure und lokal
* Azure und andere Clouds
* Azure, andere Clouds und lokal

Für eine unter einem Azure-Abonnement aktivierte Azure-Umgebung werden von Azure Security Center automatisch IaaS-Ressourcen ermittelt, die unter dem Abonnement bereitgestellt werden.

> [!NOTE]
> Um die vollständigen Sicherheitsfeatures nutzen zu können, muss der von Azure Security Center verwendete [Log Analytics-Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent) installiert und [ordnungsgemäß konfiguriert sein, damit Daten an Azure Security Center gesendet werden](security-center-enable-data-collection.md#manual-agent).

In den folgenden Abschnitten werden die unterstützten Serverbetriebssysteme aufgelistet, unter denen der von Azure Security Center verwendete [Log Analytics-Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent) ausgeführt werden kann.

### <a name="windows-server-operating-systems"></a>Windows Server-Betriebssysteme <a name="os-windows"></a>

|OS|Unterstützt durch Azure Security Center|Unterstützung für die Integration in Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Weitere Informationen zu den unterstützten Features für die oben aufgeführten Windows-Betriebssysteme finden Sie unter [Virtuelle Computer/Server – unterstützte Features](security-center-services.md#vm-server-features).

### <a name="windows-operating-systems"></a>Windows-Betriebssysteme <a name="os-windows (non-server)"></a>

Azure Security Center wird zur Überwachung und zum Schutz Ihrer Windows-basierten virtuellen Computer in Azure-Dienste integriert.

### <a name="linux-operating-systems"></a>Linux-Betriebssysteme <a name="os-linux"></a>

64 Bit

* CentOS 6 und 7
* Amazon Linux 2017.09
* Oracle Linux 6 und Oracle Linux 7
* Red Hat Enterprise Linux Server 6 und 7
* Debian GNU/Linux 8 und 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS und 18.04 LTS
* SUSE Linux Enterprise Server 12

32 Bit
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 und 9
* Ubuntu Linux 14.04 LTS und 16.04 LTS

> [!NOTE]
> Die Liste der unterstützten Linux-Betriebssysteme ändert sich ständig. Bei Bedarf können Sie [hier](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) klicken, um die aktuelle Liste der unterstützten Versionen anzuzeigen, falls seit der letzten Veröffentlichung in diesem Thema Änderungen vorgenommen wurden.

Weitere Informationen zu den unterstützten Features für die oben aufgeführten Linux-Betriebssysteme finden Sie unter [Virtuelle Computer/Server – unterstützte Features](security-center-services.md#vm-server-features).

### <a name="managed-virtual-machine-services"></a>Verwaltete Dienste für virtuelle Computer <a name="virtual-machine"></a>

Unter einem Kundenabonnement werden virtuelle Computer auch im Rahmen einiger verwalteter Azure-Dienste erstellt, z. B. Azure Kubernetes (AKS), Azure Databricks usw. Diese virtuellen Computer werden ebenfalls von Azure Security Center ermittelt. Der Log Analytics-Agent kann unter den oben aufgeführten unterstützten [Windows-/Linux-Betriebssystemen](#os-windows) installiert und konfiguriert werden.

### <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

Virtuelle Computer, die in einem Clouddienst ausgeführt werden, werden ebenfalls unterstützt. Es werden nur Clouddienst-Webrollen und -Workerrollen überwacht, die in Produktionsslots ausgeführt werden. Weitere Informationen zu Clouddiensten finden Sie unter [Übersicht zu Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

Der Schutz für virtuelle Computer, die sich in Azure Stack befinden, wird ebenfalls unterstützt. Weitere Informationen zur Integration des Security Centers in Azure Stack finden Sie unter [Integrieren Ihrer Azure Stack-VMs in Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Datenerfassung in Security Center und den Log Analytics-Agent](security-center-enable-data-collection.md).
- Erfahren Sie, wie [Daten von Security Center verwaltet und geschützt werden](security-center-data-security.md).
- Hier erfahren Sie, wie Sie [die Entwurfsaspekte in Bezug auf die Einführung von Azure Security Center planen und verstehen](security-center-planning-and-operations-guide.md).
- Erfahren Sie mehr über die [für die verschiedenen Cloudumgebungen verfügbaren Features](security-center-services.md).
- Erfahren Sie mehr zum [Bedrohungsschutz für Windows-und Linux-Computer in Azure Security Center](threat-protection.md#windows-machines).
