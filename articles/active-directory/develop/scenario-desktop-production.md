---
title: Verschieben einer Desktop-App, die Web-APIs aufruft, in die Produktion – Microsoft Identity Platform | Azure
description: Erfahren Sie, wie Sie eine Desktop-App in die Produktion verschieben, die Web-APIs aufruft.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c8a9cf0c05d8af14d52bb1efb536dc8bbe7db84d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79230574"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Desktop-App, die Web-APIs aufruft: Überführen in die Produktion

Dieser Artikel enthält Details zum Überführen Ihrer Desktop-App, die Web-APIs aufruft, in die Produktion.

## <a name="handle-errors-in-desktop-applications"></a>Behandeln von Fehlern in Desktopanwendungen

Sie haben anhand der unterschiedlichen Flows und Codeausschnitte gelernt, wie Sie Fehler für automatisch ausgeführte Flows behandeln. Außerdem haben Sie erfahren, dass in bestimmten Fällen eine Interaktion erforderlich ist, nämlich bei einer inkrementellen Einwilligung und bei bedingtem Zugriff.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Einholen der Benutzereinwilligung im Vorfeld für verschiedene Ressourcen

> [!NOTE]
> Das Einholen der Einwilligung für mehrere Ressourcen ist mit Microsoft Identity Platform, nicht aber mit Azure Active Directory B2C (Azure AD) möglich. Azure AD B2C unterstützt nur die Administrator- und nicht die Benutzereinwilligung.

Pber den Endpunkt von Microsoft Identity Platform (v2.0) können Sie kein Token für mehrere Ressourcen gleichzeitig abrufen. Der Parameter `scopes` kann nur Bereiche für eine einzelne Ressource enthalten. Mit dem Parameter `extraScopesToConsent` können Sie sicherstellen, dass der Benutzer vorab seine Einwilligung zu mehreren Ressourcen erteilt.

Angenommen, Sie verfügen über zwei Ressourcen mit jeweils zwei Bereichen:

- `https://mytenant.onmicrosoft.com/customerapi` mit den Bereichen `customer.read` und `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` mit den Bereichen `vendor.read` und `vendor.write`

In diesem Fall sollten Sie den `.WithAdditionalPromptToConsent`-Modifizierer verwenden, der über den Parameter `extraScopesToConsent` verfügt.

Beispiel:

### <a name="in-msalnet"></a>In MSAL.NET

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>In MSAL für iOS und macOS

Objective-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Durch diesen Aufruf wird ein Zugriffstoken für die erste Web-API aufgerufen.

Rufen Sie die `AcquireTokenSilent`-API auf, wenn Sie die zweite Web-API aufrufen müssen.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Persönliches Microsoft-Konto erfordert erneute Einwilligung bei jedem Ausführen der App

Für Benutzer eines persönlichen Microsoft-Kontos besteht das beabsichtigte Verhalten darin, bei jedem Aufruf des nativen Clients (Desktop oder mobile App) zur Autorisierung erneut eine Einwilligung einzuholen. Die Identität des nativen Clients ist im Gegensatz zur vertraulichen Identität der Clientanwendung grundsätzlich unsicher. Um ihre Identität nachzuweisen, tauschen vertrauliche Clientanwendungen ein Geheimnis mit Azure AD aus. Microsoft Identity Platform schränkt dieses Sicherheitsrisiko für Verbraucherdienste ein, indem der Benutzer bei jeder Anwendungsautorisierung seine Einwilligung geben muss.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
