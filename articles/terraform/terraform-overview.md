---
title: Verwenden von Terraform mit Azure
description: Einführung in die Verwendung von Terraform zur Versionserstellung und Bereitstellung für die Azure-Infrastruktur
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 05b92fdf8c0a0f84d2f29b4aa7479850b2721441
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472161"
---
# <a name="terraform-with-azure"></a>Terraform mit Azure

[Hashicorp Terraform](https://www.terraform.io/) ist ein Open Source-Tool für die Bereitstellung und Verwaltung von Cloudinfrastrukturen. Die Infrastruktur in Konfigurationsdateien, die die Topologie von Cloudressourcen beschreiben, wird kodifiziert. Zu diesen Ressourcen zählen virtuelle Computer, Speicherkonten und Netzwerkschnittstellen. Die Befehlszeilenschnittstelle von Terraform bietet einen einfachen Mechanismus für die Bereitstellung von Konfigurationsdateien und für ihre Versionserstellung in Azure.

In diesem Artikel werden die Vorteile bei der Verwendung von Terraform zum Verwalten der Azure-Infrastruktur beschrieben.

## <a name="automate-infrastructure-management"></a>Automatisieren der Infrastrukturverwaltung

Die vorlagenbasierten Konfigurationsdateien von Terraform ermöglichen es Ihnen, Azure-Ressourcen in wiederholbarer und vorhersagbarer Weise zu definieren, bereitzustellen und zu konfigurieren. Das Automatisieren der Infrastruktur hat mehrere Vorteile:

- Verringert die Wahrscheinlichkeit von menschlichem Fehlverhalten bei der Bereitstellung und Verwaltung der Infrastruktur.
- Stellt dieselbe Vorlage mehrmals bereit, um identische Entwicklungs-, Test- und Produktionsumgebungen zu erzeugen.
- Verringert die Kosten für Entwicklungs- und Testumgebungen, indem diese bei Bedarf erstellt werden.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Verstehen der Infrastrukturänderungen vor ihrer Anwendung

Wenn eine Ressourcentopologie komplex wird, kann es schwierig sein, die Bedeutung und Auswirkung von Infrastrukturänderungen zu verstehen.

Über die Terraform-Befehlszeilenschnittstelle können Benutzer Infrastrukturänderungen vor ihrer Anwendung überprüfen und in einer Vorschau anzeigen. Das Anzeigen von Infrastrukturänderungen auf sichere Weise in einer Vorschau hat mehrere Vorteile:
- Teammitglieder können effektiver zusammenarbeiten, indem sie vorgeschlagene Änderungen und deren Auswirkung schnell verstehen.
- Unbeabsichtigte Änderungen können frühzeitig im Entwicklungsprozess abgefangen werden.

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Bereitstellen der Infrastruktur in mehreren Clouds

Terraform kann eine Infrastruktur für mehrere Cloudanbieter bereitstellen. Terraform ermöglicht es Entwicklern, die einzelnen Infrastrukturdefinitionen mithilfe konsistenter Tools zu verwalten.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun eine Übersicht über Terraform und seine Vorteile haben, folgen hier die empfohlenen nächsten Schritte:

- Erste Schritte durch die [Installation von Terraform und seiner Konfiguration für die Verwendung von Azure](terraform-install-configure.md)
- [Erstellen eines virtuellen Azure-Computers mit Terraform](terraform-create-complete-vm.md)
- Untersuchen des [Azure Resource Manager-Moduls für Terraform](https://www.terraform.io/docs/providers/azurerm/) 