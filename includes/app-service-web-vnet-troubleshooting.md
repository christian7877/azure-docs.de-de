---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671490"
---
Die Funktion ist zwar einfach einzurichten, aber dies bedeutet nicht, dass für Ihre Benutzeroberfläche keinerlei Probleme auftreten. Falls beim Zugreifen auf den gewünschten Endpunkt Probleme auftreten, können Sie einige Hilfsprogramme verwenden, um die Verbindung über die App-Konsole zu testen. Sie können zwei Konsolen verwenden. Eine ist die Kudu-Konsole, und die andere ist die Konsole im Azure-Portal. Greifen Sie in der App auf „Tools -> Kudu“ zu, um zur Kudu-Konsole zu gelangen. Sie können auch die Kudo-Konsole unter „[sitename].scmn.azurewebsites.net“ erreichen. Wechseln Sie nach dem Laden der Website zur Konsolenregisterkarte „Debuggen“. Um auf die über das Azure-Portal gehostete Konsole zuzugreifen, greifen Sie in der App auf „Tools“ -> „Konsole“ zu. 

#### <a name="tools"></a>Tools
Die Tools **ping**, **nslookup** und **tracert** funktionieren aufgrund von Sicherheitseinschränkungen nicht über die Konsole. Es wurden zwei separate Tools hinzugefügt, um diese Lücke zu füllen. Zum Testen der DNS-Funktionalität haben wir ein Tool mit dem Namen „nameresolver.exe“ hinzugefügt. Die Syntax ist:

    nameresolver.exe hostname [optional: DNS Server]

Sie können **nameresolver** verwenden, um die Hostnamen zu überprüfen, von denen Ihre App abhängig ist. So können Sie testen, ob für das DNS etwas falsch konfiguriert ist oder ob ggf. kein Zugriff auf Ihren DNS-Server besteht. Den von Ihrer App verwendeten DNS-Server können Sie in der Konsole in den Umgebungsvariablen WEBSITE_DNS_SERVER und WEBSITE_DNS_ALT_SERVER einsehen.

Mit dem nächsten Tool können Sie die TCP-Verbindung mit einer Host/Port-Kombination testen. Dieses Tool hat den Namen **tcpping** und die folgende Syntax:

    tcpping.exe hostname [optional: port]

Das **tcpping**-Hilfsprogramm teilt Ihnen mit, ob Sie einen bestimmten Host und Port erreichen können. Es kann nur unter folgenden Bedingungen erfolgreich ausgeführt werden: Eine Anwendung lauscht auf der Host- und Portkombination, und von Ihrer App aus ist Netzwerkzugriff auf den angegebenen Host und Port möglich.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Debuggen des Zugriffs auf im VNET gehostete Ressourcen
Es kann verschiedene Ursachen haben, warum Ihre App einen bestimmten Host und Port nicht erreichen kann. In den meisten Fällen liegt eine der drei folgenden Ursachen vor:

* **Eine Firewall.** Falls eine Firewall den Zugriff verhindert, wird das TCP-Timeout ausgelöst. Das TCP-Timeout entspricht hier 21 Sekunden. Überprüfen Sie die Verbindung mithilfe des **tcpping**-Tools. TCP-Timeouts können zwar auch zahlreiche andere Ursachen haben, es empfiehlt sich allerdings, bei der Firewall zu beginnen. 
* **Kein DNS-Zugriff.** Das DNS-Timeout beträgt drei Sekunden pro DNS-Server. Wenn Sie zwei DNS-Server besitzen, beträgt das Timeout 6 Sekunden. Überprüfen Sie mithilfe des nameresolver-Tools, ob DNS funktioniert. Zur Erinnerung: Das nslookup-Tool kann nicht verwendet werden, da es nicht das DNS verwendet, mit dem Ihr VNET konfiguriert ist. Dieses Problem kann darauf zurückzuführen sein, dass eine Firewall oder Netzwerksicherheitsgruppe den Zugriff auf das DNS blockiert.

Sollte das Problem weiterhin bestehen, überprüfen Sie zunächst Folgendes: 

**Regionale VNET-Integration**
* Ist Ihr Ziel eine RFC 1918-fremde Adresse, und Sie haben WEBSITE_VNET_ROUTE_ALL nicht auf 1 festgelegt?
* Blockiert eine Netzwerksicherheitsgruppe ausgehenden Datenverkehr aus Ihrem Integrationssubnetz?
* Bei einer Verbindung über ExpressRoute oder ein VPN: Ist Ihr lokales Gateway zum Zurückleiten von Datenverkehr an Azure konfiguriert? Wenn Sie die Endpunkte in Ihrem VNET erreichen können, aber nicht lokal, überprüfen Sie Ihre Routen.
* Verfügen Sie über ausreichende Berechtigungen, um Delegierung für das Integrationssubnetz festzulegen? Während der Konfiguration der regionalen VNET-Integration wird das Integrationssubnetz an Microsoft.Web delegiert. Das Subnetz wird von der Benutzeroberfläche der VNet-Integration automatisch an Microsoft.Web delegiert. Wenn Ihr Konto nicht über ausreichende Netzwerkberechtigungen verfügt, um Delegierung festzulegen, benötigen Sie eine Person, die in Ihrem Integrationssubnetz Attribute festlegen kann, um das Subnetz zu delegieren. Wenn Sie das Subnetz für die Integration manuell delegieren möchten, wechseln Sie zur Benutzeroberfläche des Azure Virtual Network-Subnetzes, und legen Sie Delegierung für Microsoft.Web fest. 

**VNET-Integration, die ein Gateway erfordert**
* Liegt der Point-to-Site-Adressbereich in den RFC 1918-Bereichen (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Wird im Portal angezeigt, dass das Gateway ausgeführt wird? Fahren Sie das Gateway hoch, wenn es heruntergefahren ist.
* Werden Zertifikate als synchronisiert angezeigt, oder vermuten Sie, dass die Netzwerkkonfiguration geändert wurde?  Wenn Ihre Zertifikate nicht synchronisiert sind oder Sie vermuten, dass an Ihrer VNET-Konfiguration eine Änderung vorgenommen wurde, die nicht mit Ihren ASPs synchronisiert wurde, klicken Sie auf „Netzwerk synchronisieren“.
* Bei einer Verbindung über ein VPN: Ist Ihr lokales Gateway zum Zurückleiten von Datenverkehr an Azure konfiguriert? Wenn Sie die Endpunkte in Ihrem VNET erreichen können, aber nicht lokal, überprüfen Sie Ihre Routen.
* Versuchen Sie, ein Koexistenzgateway zu verwenden, das sowohl Point-to-Site als auch ExpressRoute unterstützt? Koexistenzgateways werden bei der VNET-Integration nicht unterstützt.

Das Debuggen von Netzwerkproblemen ist eine Herausforderung, da Sie nicht sehen können, was den Zugriff auf eine bestimmte Host:Port-Kombination blockiert. Mögliche Ursachen sind:

* Aktivierte Firewall auf Ihrem Host, die den Zugriff auf den Anwendungsport über Ihren Point-to-Site-IP-Adressbereich verhindert Gegebenenfalls erforderlicher öffentlicher Zugriff für die Durchquerung von Subnetzen
* Zielhost ausgefallen
* Anwendung nicht verfügbar
* Falsche IP-Adresse oder falscher Hostname
* Die Anwendung lauscht über einen anderen Port als erwartet. Sie können Ihre Prozess-ID auf den lauschenden Port festlegen, indem Sie auf dem Endpunkthost „netstat -aon“ verwenden. 
* Netzwerksicherheitsgruppen sind so konfiguriert, dass der Zugriff auf Ihren Anwendungshost und -port aus Ihrem Point-to-Site-IP-Adressbereich verhindert wird.

Denken Sie daran, dass Sie nicht wissen, welche Adresse Ihre App tatsächlich verwendet. Es kann sich um eine beliebige Adresse im Integrationssubnetz oder Point-to-Site-Adressbereich handeln. Sie müssen daher den Zugriff vom gesamten Adressbereich zulassen. 

Weitere Debugschritte:

* Stellen Sie eine Verbindung mit einer VM im VNET her, und versuchen Sie, die Ressource host:port von dort aus zu erreichen. Um den TCP-Zugriff zu testen, verwenden Sie den PowerShell-Befehl **test-netconnection**. Die Syntax ist:

      test-netconnection hostname [optional: -Port]

* Rufen Sie eine Anwendung auf einer VM auf, und testen Sie den Zugriff auf den jeweiligen Host und Port über die Konsole der App mit **tcpping**.

#### <a name="on-premises-resources"></a>Lokale Ressourcen ####

Wenn Ihre App eine Ressource lokal nicht erreichen kann, überprüfen Sie, ob Sie die Ressource über Ihr VNET erreichen können. Verwenden Sie den PowerShell-Befehl **test-netconnection**, um den TCP-Zugriff zu überprüfen. Wenn Ihre VM die lokale Ressource nicht erreichen kann, ist Ihre VPN- oder ExpressRoute-Verbindung möglicherweise nicht richtig konfiguriert.

Wenn die im VNET gehostete VM auf ein lokales System zugreifen kann, die App jedoch nicht, trifft wahrscheinlich einer der folgenden Gründe zu:

* Die Routen sind in Ihrem lokalen Gateway nicht mit Ihren Subnetz- oder Point-to-Site-Adressbereichen konfiguriert.
* Die Netzwerksicherheitsgruppen blockieren den Zugriff auf den Point-to-Site-IP-Adressbereich.
* Die lokalen Firewalls blockieren den Datenverkehr des Point-to-Site-IP-Adressbereichs.
* Sie versuchen, eine RFC 1918-fremde Adresse mit der Funktion für die regionale VNET-Integration zu erreichen.