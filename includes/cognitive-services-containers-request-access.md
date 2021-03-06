---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229325"
---
Füllen Sie das [Formular zum Anfordern von Cognitive Services-Containern für maschinelles Sehen](https://aka.ms/VisionContainersPreview) aus und senden Sie es anschließend, um Zugriff auf den Container anzufordern. Im Formular müssen Sie Informationen über Sie selbst, Ihr Unternehmen und das Benutzerszenario eintragen, für das Sie den Container verwenden möchten. Nach der Übermittlung des Formulars wird es vom Azure Cognitive Services-Team überprüft, um sicherzustellen, dass Sie die Kriterien für den Zugriff auf die private Containerregistrierung erfüllen.

> [!IMPORTANT]
> Sie müssen im Formular eine E-Mail-Adresse verwenden, die entweder einem Microsoft-Konto (MSA) oder einem Azure Active Directory-Konto (Azure AD) zugeordnet ist.

Wenn Ihre Anforderung genehmigt wurde, erhalten Sie eine E-Mail mit Anweisungen zum Abrufen Ihrer Anmeldeinformationen und zum Zugreifen auf die private Containerregistrierung.

## <a name="log-in-to-the-private-container-registry"></a>Anmelden bei der privaten Containerregistrierung

Es gibt verschiedene Möglichkeiten, sich bei der privaten Containerregistrierung für Cognitive Services-Container zu authentifizieren. Es wird empfohlen, die Befehlszeilenmethode mithilfe der [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) zu verwenden.

Verwenden Sie den Befehl [docker login](https://docs.docker.com/engine/reference/commandline/login/) wie im folgenden Beispiel gezeigt, um sich bei der privaten Containerregistrierung für Cognitive Services-Container (`containerpreview.azurecr.io`) anzumelden. Ersetzen Sie *\<username\>* durch den Benutzernamen und *\<password\>* durch das Kennwort. Diese Anmeldeinformationen wurden Ihnen vom Azure Cognitive Services-Team bereitgestellt.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Wenn Sie Ihre Anmeldeinformationen in einer Textdatei gesichert haben, können Sie den Inhalt dieser Textdatei mit dem Befehl `docker login` verketten. Verwenden Sie den Befehl `cat` wie im folgenden Beispiel. Ersetzen Sie *\<passwordFile\>* durch den Pfad und Namen der Textdatei, die das Kennwort enthält. Ersetzen Sie den *\<Benutzernamen\>* durch den in Ihren Anmeldeinformationen angegebenen Benutzernamen.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

