---
title: Verwenden dynamischer Gruppen mit der Azure-Updateverwaltung
description: In diesem Artikel wird beschrieben, wie dynamische Gruppen mit der Azure Automation-Updateverwaltung funktionieren.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420393"
---
# <a name="use-dynamic-groups-with-update-management"></a>Verwenden dynamischer Gruppen mit der Updateverwaltung

Die Updateverwaltung bietet die Möglichkeit, eine dynamische Gruppe von virtuellen Azure-Computern oder Azure-fremden virtuellen Computern als Ziel für Updatebereitstellungen zu verwenden. Diese Gruppen werden zum Zeitpunkt der Bereitstellung ausgewertet, sodass Sie die Bereitstellung nicht bearbeiten müssen, um Computer hinzuzufügen.

## <a name="azure-machines"></a>Azure-Computer

Diese Gruppen werden durch eine Abfrage definiert. Wenn eine Updatebereitstellung beginnt, werden die Mitglieder dieser Gruppe ausgewertet. Dynamische Gruppen funktionieren nicht mit klassischen VMs. Wenn Sie Ihre Abfrage definieren, können die folgenden Elemente zusammen verwendet werden, um die dynamische Gruppe aufzufüllen:

* Subscription
* Ressourcengruppen
* Standorte
* `Tags`

![Auswählen von Gruppen](./media/automation-update-management/select-groups.png)

Um eine Vorschau der Ergebnisse einer dynamischen Gruppe anzuzeigen, klicken Sie auf die Schaltfläche **Vorschau**. Diese Vorschau zeigt die Gruppenmitgliedschaft zu diesem Zeitpunkt an. In diesem Beispiel suchen wir nach Computern, bei denen das Tag **Role** den Wert **BackendServer** aufweist. Wenn dieses Tag weiteren Computern hinzugefügt wird, werden sie bei zukünftigen Bereitstellungen dieser Gruppe hinzugefügt.

![Anzeigen einer Vorschau von Gruppen](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Azure-fremde Computer

Bei Azure-fremden Computern wird die dynamische Gruppe mithilfe von gespeicherten Suchen (auch als Computergruppen bezeichnet) erstellt. Informationen zum Erstellen einer gespeicherten Suche finden Sie unter [Erstellen einer Computergruppe](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Nachdem die Gruppe erstellt wurde, können Sie sie in der Liste der gespeicherten Suchen auswählen. Klicken Sie auf **Vorschau**, um die Vorschau der Computer in der gespeicherten Suche anzuzeigen.

![Auswählen von Gruppen](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie eine dynamische Gruppe erstellt haben, können Sie [eine Updatebereitstellung erstellen](automation-tutorial-update-management.md).
