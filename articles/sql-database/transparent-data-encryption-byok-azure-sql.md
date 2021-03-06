---
title: Kundenseitig verwaltete Transparent Data Encryption (TDE)
description: 'Transparent Data Encryption (TDE) mit Azure Key Vault mit Bring Your Own Key-Unterstützung (BYOK) für Azure SQL-Datenbank und Azure Synapse TDE mit BYOK: Überblick, Funktionsweise, Überlegungen und Empfehlungen'
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 462326fb16663a6f25ff4b51ea11791201086fd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528727"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL Transparent Data Encryption mithilfe eines kundenseitig verwalteten Schlüssels

Azure SQL [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) mit kundenseitig verwalteten Schlüsseln ermöglicht BYOK-Szenarien (Bring Your Own Key) für den Schutz von Daten im Ruhezustand und gibt Organisationen die Möglichkeit, Verwaltungsaufgaben von den Schlüsseln und Daten zu trennen. Bei der vom Kunden verwalteten Transparent Data Encryption ist der Kunde vollständig für die Verwaltung des Lebenszyklus (Schlüsselerstellung, -upload, -rotation, -löschung) und der Schlüsselnutzungsberechtigungen sowie für die Überwachung von Vorgängen für Schlüssel verantwortlich.

In diesem Szenario ist der Schlüssel, der für die Verschlüsselung des Datenbank-Verschlüsselungsschlüssels (Database Encryption Key, DEK) verwendet und auch als TDE-Schutzvorrichtung bezeichnet wird, ein vom Kunden verwalteter asymmetrischer Schlüssel, der in einer kundeneigenen und vom Kunden verwalteten [Azure Key Vault-Instanz (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) gespeichert ist, einem cloudbasierten externen Schlüsselverwaltungssystem. Key Vault bietet hochverfügbaren und skalierbaren sicheren Speicher für RSA-Kryptografieschlüssel, der optional von FIPS 140-2 Level 2-geprüften Hardwaresicherheitsmodulen (HSM) gesichert wird. Dabei wird kein direkter Zugriff auf einen gespeicherten Schlüssel zugelassen, sondern Verschlüsselung und Entschlüsselung werden mithilfe des Schlüssels für die autorisierten Entitäten bereitgestellt. Der Schlüssel kann vom Schlüsseltresor generiert, importiert oder von [einem lokalen HSM-Gerät auf den Schlüsseltresor übertragen werden](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

Bei Azure SQL-Datenbank und Azure Synapse ist die TDE-Schutzvorrichtung auf der logischen Serverebene festgelegt und wird von allen verschlüsselten Datenbanken geerbt, die diesem Server zugeordnet sind. Bei der verwalteten Azure SQL-Instanz ist die TDE-Schutzvorrichtung auf Instanzebene festgelegt und wird von allen verschlüsselten Datenbanken für diese Instanz geerbt. In diesem Dokument bezieht sich der Begriff *Server* sowohl auf den logischen SQL-Datenbank-Server als auch auf die Instanz (sofern nicht anders angegeben).

> [!IMPORTANT]
> Für diejenigen, die eine dienstseitig verwaltete TDE verwenden und auf eine kundenseitig verwalteten TDE umsteigen möchten, bleiben die Daten während der Umstellung verschlüsselt, und es kommt nicht zu Ausfallzeiten oder einer Neuverschlüsselung der Datenbankdateien. Der Wechsel von einem dienstseitig verwalteten Schlüssel zu einem kundenseitig verwalteten Schlüssel erfordert lediglich eine erneute Verschlüsselung des Datenbankverschlüsselungsschlüssels (DEK), die schnell und online durchzuführen ist.

## <a name="benefits-of-the-customer-managed-tde"></a>Vorteile der kundenseitig verwalteten TDE

Eine kundenseitig verwaltete TDE bietet Kunden die folgenden Vorteile:

- Vollständige und präzise Kontrolle über die Verwendung und Verwaltung der TDE-Schutzvorrichtung

- Transparenz der Verwendung der TDE-Schutzvorrichtung

- Möglichkeit der Trennung von Aufgaben bei der Verwaltung von Schlüsseln und Daten innerhalb der Organisation

- Möglichkeit des Widerrufs von Schlüsselzugriffsberechtigungen durch den Key Vault-Administrator, um den Zugriff auf die verschlüsselte Datenbank zu verhindern

- Zentrale Verwaltung von Schlüsseln in Azure Key Vault

- Mehr Vertrauen der Endkunden, da AKV so entworfen wurde, dass Microsoft die Verschlüsselungsschlüssel weder einsehen noch extrahieren kann

## <a name="how-customer-managed-tde-works"></a>Funktionsweise der kundenseitig verwalteten TDE

![Einrichtung und Funktionsweise der kundenseitig verwalteten TDE](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Damit der Server die in Azure Key Vault (AKV) gespeicherte TDE-Schutzvorrichtung für die Verschlüsselung des DEK verwenden kann, muss der Key Vault-Administrator dem Server die folgenden Zugriffsrechte über die eindeutige AAD-Identität gewähren:

- **get:** zum Abrufen des öffentlichen Teils und der Eigenschaften des Schlüssels in Key Vault

- **wrapKey:** zum Schützen (Verschlüsseln) des DEK

- **unwrapKey:** zum Aufheben des Schutzes (Verschlüsselung) des DEK

Der Key Vault-Administrator kann auch die [Protokollierung von Key Vault-Überwachungsereignissen aktivieren](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault), damit sie später überprüft werden können.

Wenn der Server erstmals für die Verwendung einer TDE-Schutzvorrichtung aus Azure Key Vault konfiguriert wird, sendet der Server den DEK jeder Datenbank mit TDE zur Verschlüsselung an Key Vault. Key Vault gibt den verschlüsselten DEK zurück, der in der Benutzerdatenbank gespeichert wird.

Bei Bedarf sendet der Server den geschützten DEK zur Entschlüsselung an den Schlüsseltresor.

Prüfer können Azure Monitor verwenden, um die AuditEvent-Protokolle von Key Vault zu überprüfen, sofern die Protokollierung aktiviert wurde.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Anforderungen an die Konfiguration der kundenseitig verwalteten TDE

### <a name="requirements-for-configuring-akv"></a>Anforderungen an die Konfiguration von AKV

- Der Schlüsseltresor und die (verwaltete) SQL-Datenbank-Instanz müssen demselben Azure Active Directory-Mandanten angehören. Mandantenübergreifende Interaktionen zwischen Schlüsseltresor und Server werden nicht unterstützt. Damit Ressourcen später verschoben werden können, muss die TDE mit AKV neu konfiguriert werden. Erfahren Sie mehr über das [Verschieben von Ressourcen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- Die Funktion zum [vorläufigen Löschen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) muss im Schlüsseltresor aktiviert sein, um bei einer versehentlichen Löschung des Schlüssels (oder Schlüsseltresors) Datenverluste zu vermeiden. Vorläufig gelöschte Ressourcen werden 90 Tage lang aufbewahrt, sofern sie nicht in der Zwischenzeit vom Kunden wiederhergestellt oder endgültig gelöscht werden. Den Aktionen *Wiederherstellen* und *Endgültig löschen* sind über Zugriffsrichtlinien für den Schlüsseltresor eigene Berechtigungen zugewiesen. Die Funktion für vorläufiges Löschen ist standardmäßig deaktiviert und kann über [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) oder die Befehlszeilenschnittstelle ([CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)) aktiviert werden. Über das Azure-Portal ist die Aktivierung nicht möglich.  

- Gewähren Sie dem SQL-Datenbank-Server oder der verwalteten Instanz über die Azure Active Directory-Identität Zugriff auf den Schlüsseltresor (get, wrapKey, unwrapKey). Wenn Sie das Azure-Portal verwenden, wird die Azure AD-Identität automatisch erstellt. Bei der Verwendung von PowerShell oder der Befehlszeilenschnittstelle muss die Azure AD-Identität explizit erstellt werden, und der Abschluss des Vorgangs sollte überprüft werden. Unter [Konfigurieren von TDE mit BYOK](transparent-data-encryption-byok-azure-sql-configure.md) und [Konfigurieren von TDE mit BYOK für verwaltete Instanz](https://aka.ms/sqlmibyoktdepowershell) finden Sie ausführliche Anleitungen für die Verwendung von PowerShell.

- Wenn Sie eine Firewall mit AKV verwenden, müssen Sie die Option *Vertrauenswürdige Microsoft-Dienste zulassen* aktivieren.

### <a name="requirements-for-configuring-tde-protector"></a>Anforderungen an die Konfiguration der TDE-Schutzvorrichtung

- Die TDE-Schutzvorrichtung darf nur ein asymmetrischer RSA 2048- oder RSA HSM 2048-Schlüssel sein.

- Das Schlüsselaktivierungsdatum (sofern festgelegt) muss ein Datum und eine Uhrzeit in der Vergangenheit sein. Das Ablaufdatum (sofern festgelegt) muss ein Datum und eine Uhrzeit in der Zukunft sein.

- Der Schlüssel muss sich im Zustand *Aktiviert* befinden.

- Wenn Sie einen vorhandenen Schlüssel in den Schlüsseltresor importieren, müssen Sie ihn in einem unterstützten Dateiformat (.pfx, .byok oder .backup) bereitstellen.

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Empfehlungen für die Konfiguration einer kundenseitig verwalteten TDE

### <a name="recommendations-when-configuring-akv"></a>Empfehlungen für die Konfiguration von AKV

- Ordnen Sie einem Schlüsseltresor in einem Einzelabonnement insgesamt höchstens 500 universelle oder 200 unternehmenskritische Datenbanken zu, um Hochverfügbarkeit sicherzustellen, wenn der Server auf die TDE-Schutzvorrichtung im Schlüsseltresor zugreift. Diese Zahlen basieren auf Erfahrungen und sind in den [Key Vault-Dienstgrenzwerten](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits) dokumentiert. Damit sollen Probleme nach einem Serverfailover verhindert werden, da dabei so viele Schlüsselvorgänge für den Tresor ausgeführt werden, wie Datenbanken auf diesem Server vorhanden sind.

- Legen Sie eine Ressourcensperre für den Schlüsseltresor fest, um zu steuern, wer diese wichtige Ressource löschen kann, und um ein versehentliches oder nicht autorisiertes Löschen zu verhindern. Erfahren Sie mehr über [Ressourcensperren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Aktivieren Sie die Überwachung und Berichterstellung für alle Verschlüsselungsschlüssel: Key Vault stellt Protokolle bereit, die sich problemlos in andere SIEM-Tools (Security Information & Event Management) einfügen lassen. [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) in Operations Management Suite ist ein Beispiel für einen Dienst, der bereits integriert ist.

- Verknüpfen Sie jeden Server mit zwei Schlüsseltresoren in unterschiedlichen Regionen, aber mit denselben Schlüsselmaterialien, um die Hochverfügbarkeit verschlüsselter Datenbanken zu gewährleisten. Markieren Sie nur den Schlüssel aus dem Schlüsseltresor in derselben Region als TDE-Schutzvorrichtung. Wenn es zu einem Ausfall kommt, der sich auf den Schlüsseltresor in derselben Region auswirkt, wechselt das System automatisch zu diesem Tresor.

### <a name="recommendations-when-configuring-tde-protector"></a>Empfehlungen für die Konfiguration der TDE-Schutzvorrichtung
- Bewahren Sie eine Kopie der TDE-Schutzvorrichtung an einem sicheren Ort auf, oder hinterlegen Sie sie bei einem entsprechenden Dienst.

- Wenn der Schlüssel im Schlüsseltresor generiert wird, erstellen Sie eine Schlüsselsicherung, bevor Sie den Schlüssel zum ersten Mal in AKV verwenden. Die Sicherung kann nur in einer Azure Key Vault-Instanz wiederhergestellt werden. Unter [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) erfahren Sie mehr zu diesem Befehl.

- Erstellen Sie immer eine neue Sicherung, wenn Änderungen am Schlüssel vorgenommen werden (z. B. Schlüsselattribute, Tags, ACLs).

- **Lassen Sie frühere Versionen** des Schlüssels beim Rotieren von Schlüsseln im Schlüsseltresor, damit ältere Datenbanksicherungen wiederhergestellt werden können. Wenn die TDE-Schutzvorrichtung für eine Datenbank geändert wird, werden alte Sicherungen der Datenbank **nicht aktualisiert**, um die aktuelle TDE-Schutzvorrichtung zu verwenden. Bei der Wiederherstellung ist für jede Sicherung die TDE-Schutzvorrichtung erforderlich, mit der sie zum Erstellungszeitpunkt verschlüsselt wurde. Schlüsselrotationen können anhand der Anweisungen unter [Rotieren der Transparent Data Encryption-Schutzvorrichtung mithilfe von PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md) durchgeführt werden.

- Bewahren Sie alle zuvor verwendeten Schlüssel in AKV auf, auch nachdem Sie zu dienstseitig verwalteten Schlüsseln gewechselt haben. Dadurch wird sichergestellt, dass Datenbanksicherungen mit den in AKV gespeicherten TDE-Schutzvorrichtungen wiederhergestellt werden können.  Mit Azure Key Vault erstellte TDE-Schutzvorrichtungen müssen beibehalten werden, bis alle gespeicherte Sicherungen mit dienstseitig verwalteten Schlüsseln erstellt wurden. Erstellen Sie mithilfe von [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) wiederherstellbare Sicherungskopien dieser Schlüssel.

- Zum Entfernen eines möglicherweise kompromittierten Schlüssels während eines Sicherheitsvorfalls ohne Risiko von Datenverlust führen Sie die Schritte unter [Entfernen eines möglicherweise kompromittierten Schlüssels](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) durch.

## <a name="inaccessible-tde-protector"></a>Nicht zugängliche TDE-Schutzvorrichtungen

Wenn Transparent Data Encryption für die Verwendung eines kundenseitig verwalteten Schlüssels konfiguriert ist, ist dauerhafter Zugriff auf die TDE-Schutzvorrichtung erforderlich, damit die Datenbank online bleiben kann. Wenn der Server keinen Zugriff mehr auf die kundenseitig verwaltete TDE-Schutzvorrichtung in AKV hat, beginnt die Datenbank nach etwa 10 Minuten, mit einer entsprechenden Fehlermeldung alle Verbindungen zu verweigern, und ändert den Status in *Kein Zugriff*. Die einzige zulässige Aktion für eine Datenbank im Zustand „Kein Zugriff“ ist das Löschen der Datenbank.

> [!NOTE]
> Wenn aufgrund eines vorübergehenden Netzwerkausfalls nicht auf die Datenbank zugegriffen werden kann, ist keine Aktion erforderlich, und die Datenbanken werden automatisch wieder online geschaltet.

Nachdem der Zugriff auf den Schlüssel wiederhergestellt wurde, erfordert die Wiederherstellung der Datenbank zusätzliche Zeit und Schritte, die je nach verstrichener Zeit ohne Zugriff auf den Schlüssel und die Größe der Daten in der Datenbank variieren kann:

- Wenn der Schlüsselzugriff innerhalb von 8 Stunden wiederhergestellt wird, erfolgt die automatische Reparatur der Datenbank innerhalb der nächsten Stunde.

- Wenn der Schlüsselzugriff nach mehr als 8 Stunden wiederhergestellt wird, ist keine automatische Reparatur möglich. Die erneute Aktivierung der Datenbank erfordert zusätzliche Schritte im Portal und kann abhängig von der Größe der Datenbank sehr lange dauern. Wenn die Datenbank wieder online ist, gehen zuvor konfigurierte Einstellungen auf Serverebene wie die Konfiguration der [Failovergruppe](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group), der Verlauf der Point-in-Time-Wiederherstellung und Tags **verloren**. Daher empfiehlt es sich, ein Benachrichtigungssystem zu implementieren, das es Ihnen ermöglicht, Probleme beim Zugriff auf zugrunde liegende Schlüssel innerhalb von 8 Stunden zu erkennen und zu beheben.

### <a name="accidental-tde-protector-access-revocation"></a>Versehentliches Sperren des Zugriffs auf die TDE-Schutzvorrichtung

Es kann vorkommen, dass ein Benutzer mit ausreichenden Zugriffsrechten für den Schlüsseltresor den Serverzugriff auf den Schlüssel versehentlich durch eine der folgende Aktionen deaktiviert:

- Widerrufen der Berechtigungen *get*, *wrapKey*, *unwrapKey* vom Server

- Löschen des Schlüssels

- Löschen des Schlüsseltresors

- Ändern von Firewallregeln des Schlüsseltresors

- Löschen der verwalteten Identität des Servers in Azure Active Directory

Erfahren Sie mehr über [häufige Ursachen für Zugriffsprobleme bei Datenbanken](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Überwachen der kundenseitig verwalteten TDE

Konfigurieren Sie die folgenden Azure-Features, um den Datenbankzustand zu überwachen und Warnungen bei Verlust des Zugriffs auf die TDE-Schutzvorrichtung zu aktivieren:
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview): Eine Datenbank, auf die nicht zugegriffen werden kann und die den Zugriff auf die TDE-Schutzvorrichtung verloren hat, wird als „Nicht verfügbar“ angezeigt, nachdem die erste Verbindung mit der Datenbank verweigert wurde.
- [Aktivitätsprotokoll](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications): Ist der Zugriff auf die TDE-Schutzvorrichtung im vom Kunden verwalteten Schlüsseltresor nicht möglich, werden dem Aktivitätsprotokoll entsprechende Einträge hinzugefügt.  Durch die Erstellung von Warnungen für diese Ereignisse können Sie den Zugriff schnellstmöglich wiederherstellen.
- [Aktionsgruppen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) können definiert werden, um Benachrichtigungen und Warnungen gemäß Ihren Präferenzen zu senden – also etwa per E-Mail/SMS/Pushbenachrichtigung/Sprachnachricht, per Logik-App, per Webhook, per ITSM oder per Automation-Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Datenbanksicherung und -wiederherstellung mit der kundenseitig verwalteten TDE

Nachdem eine Datenbank mithilfe eines Schlüssels aus Key Vault mit TDE verschlüsselt wurde, werden alle neu generierten Sicherungen ebenfalls mit der gleichen TDE-Schutzvorrichtung verschlüsselt. Wenn die TDE-Schutzvorrichtung geändert wird, werden alte Sicherungen der Datenbank **nicht aktualisiert**, um die aktuelle TDE-Schutzvorrichtung zu verwenden.

Zum Wiederherstellen einer Sicherung, die mit einer TDE-Schutzvorrichtung aus Key Vault verschlüsselt ist, stellen Sie sicher, dass das Schlüsselmaterial für den Zielserver verfügbar ist. Es wird daher empfohlen, alle alten Versionen der TDE-Schutzvorrichtung im Schlüsseltresor beizubehalten, damit Datenbanksicherungen wiederhergestellt werden können.

> [!IMPORTANT]
> Zu jedem Zeitpunkt kann nur eine TDE-Schutzvorrichtung für einen Server festgelegt werden. Dabei handelt es sich um den Schlüssel, der auf dem Blatt im Azure-Portal mit „Legen Sie den ausgewählten Schlüssel als TDE-Standardschutzvorrichtung fest“ gekennzeichnet ist. Es können jedoch mehrere zusätzliche Schlüssel mit einem Server verknüpft werden, ohne diese als TDE-Schutzvorrichtung zu kennzeichnen. Diese Schlüssel werden nicht zum Schutz des DEK verwendet. Sie können aber während der Wiederherstellung aus einer Sicherung verwendet werden, wenn die Sicherungsdatei mit dem Schlüssel mit dem entsprechenden Fingerabdruck verschlüsselt ist.

Wenn der für die Wiederherstellung einer Sicherung erforderliche Schlüssel nicht mehr für den Zielserver verfügbar ist, wird beim Wiederherstellungsversuch die folgende Fehlermeldung zurückgegeben: „Target server `<Servername>` does not have access to all AKV URIs created between \<Timestamp #1> and \<Timestamp #2>. Please retry operation after restoring all AKV URIs.“ (Zielserver „&lt;Servername&gt;“ kann nicht auf alle zwischen <Timestamp #1> und <Timestamp #2> erstellten AKV-URIs zugreifen. Wiederholen Sie den Vorgang, nachdem alle AKV-URIs wiederhergestellt wurden.)

Um dieses Problem zu umgehen, führen Sie das Cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) für den logischen SQL-Datenbank-Zielserver oder das Cmdlet [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) für eine verwaltete Zielinstanz aus, um die Liste der verfügbaren Schlüssel zurückzugeben und die fehlenden Schlüssel zu ermitteln. Um sicherzustellen, dass alle Sicherungen wiederhergestellt werden können, vergewissern Sie sich, dass der Zielserver für die Wiederherstellung auf alle erforderlichen Schlüssel zugreifen kann. Diese Schlüssel müssen nicht als TDE-Schutzvorrichtung gekennzeichnet sein.

Weitere Informationen zu Sicherungswiederherstellung für SQL-Datenbank finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank mit automatisierten Datenbanksicherungen](sql-database-recovery-using-backups.md). Weitere Informationen zur Sicherungswiederherstellung für SQL-Pools finden Sie unter [Wiederherstellen eines SQL-Pools](../synapse-analytics/sql-data-warehouse/backup-and-restore.md). Informationen zur nativen Sicherung/Wiederherstellung von SQL Server mit verwalteten Instanzen finden Sie unter [Schnellstart: Wiederherstellen einer Datenbank in einer verwalteten Instanz](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore).

Ein weiterer zu berücksichtigender Aspekt für Protokolldateien: Gesicherte Protokolldateien bleiben auch dann mit der ursprünglichen TDE-Schutzvorrichtung verschlüsselt, wenn diese rotiert wurde und die Datenbank jetzt eine neue TDE-Schutzvorrichtung verwendet.  Zur Wiederherstellungszeit werden beide Schlüssel benötigt, um die Datenbank wiederherzustellen.  Wenn die Protokolldatei eine in Azure Key Vault gespeicherte TDE-Schutzvorrichtung verwendet, wird dieser Schlüssel zur Wiederherstellungszeit auch dann benötigt, wenn die Datenbank geändert wurde und mittlerweile die dienstseitig verwaltete TDE verwendet.

## <a name="high-availability-with-customer-managed-tde"></a>Hochverfügbarkeit bei der kundenseitig verwalteten TDE

Selbst ohne konfigurierte Georedundanz wird dringend empfohlen, den Server für die Verwendung von zwei verschiedenen Schlüsseltresoren in zwei unterschiedlichen Regionen mit demselben Schlüsselmaterial zu konfigurieren. Dazu wird mithilfe der primären Key Vault-Instanz in der gleichen Region wie der Server eine TDE-Schutzvorrichtung erstellt, und der Schlüssel wird in einen Schlüsseltresor in einer anderen Azure-Region geklont. So hat der Server Zugriff auf einen zweiten Schlüsseltresor, falls in der primären Schlüsseltresorumgebung ein Ausfall auftritt, während die Datenbank in Betrieb ist.

Rufen Sie mit dem Cmdlet „Backup-AzKeyVaultKey“ den Schlüssel in einem verschlüsselten Format aus dem primären Schlüsseltresor ab, und verwenden Sie dann das Cmdlet „Restore-AzKeyVaultKey“ um einen Schlüsseltresor in der zweiten Region für das Klonen des Schlüssels anzugeben. Alternativ können Sie das Azure-Portal verwenden, um den Schlüssel zu sichern und wiederherzustellen. Der Schlüssel im sekundären Schlüsseltresor in der anderen Region sollte nicht als TDE-Schutzvorrichtung gekennzeichnet werden. Aus diesem Grund ist dies auch nicht zulässig.

 Nur bei einem Ausfall, der den primären Schlüsseltresor betrifft, wechselt das System automatisch zum anderen verknüpften Schlüssel mit demselben Fingerabdruck im sekundären Schlüsseltresor (sofern vorhanden). Beachten Sie, dass dieser Wechsel nicht erfolgt, wenn die TDE-Schutzvorrichtung aufgrund von gesperrten Zugriffsrechten nicht zugänglich ist oder wenn der Schlüssel oder der Schlüsseltresor gelöscht wurden, da dies auf eine Kundenabsicht hinweist, um den Zugriff des Servers auf den Schlüssel einzuschränken.

![Hochverfügbarkeit bei Einzelservern](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Georedundante Notfallwiederherstellung und kundenseitig verwaltete TDE

Sowohl in Szenarien mit [aktiver Georeplikation](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) als auch in solchen mit [Failovergruppen](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) benötigt jeder beteiligte Server einen separaten Schlüsseltresor, der sich zusammen mit dem Server in derselben Azure-Region befinden muss. Der Kunde ist dafür verantwortlich, das Schlüsselmaterial in den Schlüsseltresoren konsistent zu halten, sodass die georedundante sekundäre Datenbank synchron ist und bei der Übernahme der Aufgaben denselben Schlüssel aus dem lokalen Schlüsseltresor verwenden kann, wenn die primäre Datenbank aufgrund eines Ausfalls der Region nicht mehr verfügbar ist und ein Failover ausgelöst wird. Es können bis zu vier sekundäre Datenbanken konfiguriert werden, und Verkettung (sekundäre Datenbanken von sekundären Datenbanken) wird nicht unterstützt.

Um Probleme bei der Einrichtung oder während der Georeplikation aufgrund von unvollständigem Schlüsselmaterial zu vermeiden, sollten Sie die folgenden Regeln befolgen, wenn Sie die kundenseitig verwaltete TDE konfigurieren:

- Alle beteiligten Schlüsseltresore müssen die gleichen Eigenschaften und Zugriffsrechte für die jeweiligen Server aufweisen.

- Alle beteiligten Schlüsseltresore müssen identische Schlüsselmaterialien enthalten. Dies gilt nicht nur für die aktuelle TDE-Schutzvorrichtung, sondern für alle vorherigen TDE-Schutzvorrichtungen, die möglicherweise bei den Sicherungsdateien verwendet werden.

- Sowohl die Ersteinrichtung als auch die Rotation der TDE-Schutzvorrichtung muss zuerst in der sekundären Datenbank und erst danach in der primären Datenbank erfolgen.

![Failovergruppen und georedundante Notfallwiederherstellung](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Um ein Failover zu testen, führen Sie die Schritte in [Übersicht über die aktive Georeplikation](sql-database-geo-replication-overview.md) aus. Dies sollte in regelmäßigen Abständen erfolgen, um die ordnungsgemäße Verwaltung der Berechtigungen für SQL zum Zugreifen auf beide Schlüsseltresore zu bestätigen.

## <a name="next-steps"></a>Nächste Schritte

Möglicherweise sollten Sie sich auch die folgenden PowerShell-Beispielskripts für allgemeine Vorgänge mit der kundenseitig verwalteten TDE ansehen:

- [Rotieren der Transparent Data Encryption-Schutzvorrichtung für SQL-Datenbank mithilfe von PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Entfernen der Transparent Data Encryption-Schutzvorrichtung (TDE) für SQL-Datenbank mithilfe von PowerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Verwalten der Transparent Data Encryption in einer verwalteten Instanz mithilfe eines eigenen Schlüssels mit PowerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
