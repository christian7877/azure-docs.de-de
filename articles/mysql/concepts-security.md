---
title: Sicherheit – Azure Database for MySQL
description: Eine Übersicht über die Sicherheitsfeatures in Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: aac2641913331095550c0e19cc587257a996fcce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537022"
---
# <a name="security-in-azure-database-for-mysql"></a>Sicherheit in Azure Database for MySQL

Zum Schutz der Daten auf dem Azure Database for MySQL-Server stehen mehrere Sicherheitsebenen zur Verfügung. In diesem Artikel werden diese Sicherheitsoptionen beschrieben.

## <a name="information-protection-and-encryption"></a>Informationsschutz und -verschlüsselung

### <a name="in-transit"></a>Während der Übertragung
Mit Azure Database for MySQL werden Ihre Daten durch Verschlüsselung während der Übertragung mit Transport Layer Security geschützt. Die Verschlüsselung (SSL/TLS) wird standardmäßig erzwungen.

### <a name="at-rest"></a>Ruhende Daten
Der Azure Database for MySQL-Dienst nutzt das FIPS 140-2-zertifizierte Kryptografiemodul für die Speicherverschlüsselung ruhender Daten. Daten, einschließlich Sicherungen, werden auf dem Datenträger verschlüsselt (mit Ausnahme von temporären Dateien, die während der Ausführung von Abfragen erstellt werden). Der Dienst verwendet das in der Azure Storage-Verschlüsselung enthaltene AES-256-Bit-Verschlüsselungsverfahren, und die Schlüssel werden vom System verwaltet. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden.


## <a name="network-security"></a>Netzwerksicherheit
Verbindungen mit einem Azure Database for MySQL-Server werden zunächst über ein regionales Gateway geleitet. Das Gateway verfügt über eine öffentlich zugängliche IP-Adresse, während die IP-Adressen des Servers geschützt sind. Weitere Informationen zum Gateway finden Sie im Artikel [Verbindungsarchitektur](concepts-connectivity-architecture.md).  

Ein neu erstellter Azure Database for MySQL-Server verfügt über eine Firewall, die alle externen Verbindungen blockiert. Sie erreichen zwar das Gateway, dürfen aber keine Verbindung mit dem Server herstellen. 

### <a name="ip-firewall-rules"></a>IP-Firewallregeln
IP-Firewallregeln gewähren Serverzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung. Weitere Informationen finden Sie in der [Übersicht über Firewallregeln](concepts-firewall-rules.md).

### <a name="virtual-network-firewall-rules"></a>Firewallregeln für virtuelle Netzwerke
Mit VNET-Dienstendpunkten wird die Konnektivität virtueller Netzwerke über den Azure-Backbone erweitert. Mithilfe von VNET-Regeln können Sie Ihren Azure Database for MySQL-Server so konfigurieren, dass Verbindungen von ausgewählten Subnetzen in einem virtuellen Netzwerk zugelassen werden. Weitere Informationen finden Sie in der [Übersicht über VNET-Dienstendpunkte](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>Private IP-Adresse
Private Link ermöglicht das Herstellen von Verbindungen mit Ihrer Azure Database for MySQL-Instanz in Azure über einen privaten Endpunkt. im Prinzip macht Azure Private Link Azure-Dienste in Ihrem privaten virtuellen Netzwerk (VNET) verfügbar. Der Zugriff auf die PaaS-Ressourcen erfolgt wie bei anderen Ressourcen im VNET auch über die private IP-Adresse. Weitere Informationen finden Sie in der [Übersicht über Private Link](concepts-data-access-security-private-link.md).

## <a name="access-management"></a>Zugriffsverwaltung

Beim Erstellen des Azure Database for MySQL-Servers geben Sie Anmeldeinformationen für einen Administratorbenutzer ein. Mit diesem Administrator können Sie zusätzliche MySQL-Benutzer erstellen.


## <a name="threat-protection"></a>Bedrohungsschutz

Sie können [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) verwenden. Diese Lösung erkennt anormale Aktivitäten, die darauf hindeuten, dass auf ungewöhnliche und potenziell schädliche Weise versucht wird, auf Server zuzugreifen oder diese auszunutzen.

Aktivitäten in Ihren Datenbanken können mithilfe der [Überwachungsprotokollierung](concepts-audit-logs.md) nachverfolgt werden. 


## <a name="next-steps"></a>Nächste Schritte
- Aktivieren von Firewallregeln für [IP-Adressen](concepts-firewall-rules.md) oder [virtuelle Netzwerke](concepts-data-access-and-security-vnet.md)