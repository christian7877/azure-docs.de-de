---
title: Initialisieren von MSAL.js-Client-Apps | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Initialisierung von Clientanwendungen mithilfe der Microsoft Authentication Library für JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a0a2c5fc971c3f1f3283d95c5617bdf1e88a6a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084042"
---
# <a name="initialize-client-applications-using-msaljs"></a>Initialisieren von Clientanwendungen mithilfe von MSAL.js
Dieser Artikel beschreibt die Initialisierung der Microsoft Authentication Library für JavaScript (MSAL.js) mit einer Instanz einer Benutzer-Agent-Anwendung. Die Benutzer-Agent-Anwendung ist eine Form der öffentlichen Clientanwendung, bei der der Clientcode in einem Benutzer-Agent wie beispielsweise einem Webbrowser ausgeführt wird. Diese Clients speichern keine Geheimnisse, da der Browserkontext offen zugänglich ist. Um mehr über die Clientanwendungstypen und Anwendungskonfigurationsoptionen zu erfahren, lesen Sie die [Übersicht](msal-client-applications.md).

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie eine Anwendung initialisieren, müssen Sie sie zunächst [im Azure-Portal](scenario-spa-app-registration.md) registrieren, damit Ihre Anwendung in die Microsoft Identity Platform integriert werden kann. Nach der Registrierung benötigen Sie unter Umständen die folgenden Informationen (die Sie im Azure-Portal finden können):

- Die Client-ID (eine Zeichenfolge, die eine GUID für die Anwendung darstellt)
- Die URL des Identitätsanbieters (Namensgeber der Instanz) und die Anmeldezielgruppe für Ihre Anwendung. Diese beiden Parameter werden zusammen als Autorität bezeichnet.
- Die Mandanten-ID, wenn Sie eine Branchenanwendung ausschließlich für Ihre Organisation schreiben (auch Einzelmandantenanwendung genannt).
- Bei Webanwendungen müssen Sie auch den redirectUri einstellen, zu dem der Identitätsanbieter mit den Sicherheitstoken zu Ihrer Anwendung zurückkehrt.

## <a name="initializing-applications"></a>Initialisieren von Anwendungen

Sie können MSAL.js wie folgt in einer einfachen JavaScript/Typescript-Anwendung verwenden. Initialisieren Sie den MSAL-Authentifizierungskontext, indem Sie `UserAgentApplication` mit einem Konfigurationsobjekt instanziieren. Die für die Initialisierung von MSAL.js mindestens erforderliche Konfiguration ist die ClientID Ihrer Anwendung, die Sie vom Portal zur Anwendungsregistrierung erhalten.

Für Authentifizierungsmethoden mit Umleitungsflows (`loginRedirect` und `acquireTokenRedirect`) müssen Sie einen Rückruf über die `handleRedirectCallback()`-Methode explizit für Erfolg oder Fehler registrieren. Dies ist notwendig, da Umleitungsflows keine Zusagen zurückgeben, wie es bei Methoden mit einer Popup der Fall ist.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL.js ist so konzipiert, dass es eine einzige Instanz und Konfiguration der `UserAgentApplication` hat, um einen einzigen Authentifizierungskontext darzustellen. Mehrere Instanzen werden nicht empfohlen, da sie zu widersprüchlichen Cacheeinträgen und -verhaltensweisen im Browser führen.

## <a name="configuration-options"></a>Konfigurationsoptionen

MSAL.js besitzt ein unten dargestelltes Konfigurationsobjekt, das eine Gruppierung von konfigurierbaren Optionen zum Erstellen einer Instanz der `UserAgentApplication` bereitstellt.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Nachfolgend finden Sie alle konfigurierbaren Optionen, die derzeit im Konfigurationsobjekt unterstützt werden:

- **clientID**: Erforderlich. Die ClientID Ihrer Anwendung, diese erhalten Sie über das Portal zur Anwendungsregistrierung.

- **authority**: Optional. Eine URL, die ein Verzeichnis angibt, von dem MSAL Token anfordern kann. Der Standardwert ist `https://login.microsoftonline.com/common`.
    * In Azure AD hat die URL die Form https://&lt;instance&gt;/&lt;audience&gt;, wobei &lt;instance&gt; die Identitätsanbieterdomäne (z.B. `https://login.microsoftonline.com`) ist und &lt;audiance&gt; ein Identifikator ist, der die angemeldeten Zielgruppe repräsentiert. Mögliche Werte sind:
        * `https://login.microsoftonline.com/<tenant>` ist eine dem Mandanten zugeordnete Domäne, wie contoso.onmicrosoft.com, oder die GUID, die die `TenantID`-Eigenschaft des Verzeichnisses repräsentiert, die nur zur Anmeldung von Benutzern einer bestimmten Organisation verwendet wird.
        * `https://login.microsoftonline.com/common` wird verwendet, um Benutzer mit Geschäfts-, Schul- und Unikonten oder einem persönlichen Microsoft-Konto anzumelden.
        * `https://login.microsoftonline.com/organizations/` wird verwendet, um Benutzer mit Geschäfts-, Schul- oder Unikonten anzumelden.
        * `https://login.microsoftonline.com/consumers/` wird verwendet, um Benutzer mit nur persönliche Microsoft-Konto (live) anzumelden.
    * In Azure AD B2C hat die URL das Format `https://<instance>/tfp/<tenant>/<policyName>/`, wobei „instance“ die Azure AD B2C-Domäne ({Ihr Mandantenname}.b2clogin.com), „tenant“ der Name des Azure AD B2C-Mandanten ({Ihr Mandantenname}.onmicrosoft.com) und „policyName“ der Name der anzuwendenden B2C-Richtlinie ist.


- **validateAuthority**: Optional.  Validieren Sie den Aussteller des Tokens. Der Standardwert ist `true`. Da der Autoritätswert bekannt ist und je nach Richtlinie unterschiedlich sein kann, funktioniert die Autoritätsvalidierung bei B2C-Anwendungen nicht und muss auf `false` festgelegt werden.

- **redirectUri**: Optional.  Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einem der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben. Der Standardwert lautet `window.location.href`.

- **postLogoutRedirectUri**: Optional.  Leitet den Benutzer nach der Abmeldung zu `postLogoutRedirectUri` um. Der Standardwert lautet `redirectUri`.

- **navigateToLoginRequestUrl**: Optional. Die Möglichkeit, die Standardnavigation zur Startseite nach der Anmeldung zu deaktivieren. Der Standardwert ist "true". Dies wird nur für die Umleitungflows verwendet.

- **cacheLocation**: Optional.  Legt den Browserspeicher entweder auf `localStorage` oder `sessionStorage` fest. Der Standardwert lautet `sessionStorage`.

- **storeAuthStateInCookie**: Optional.  Dieses Flag wurde in MSAL.js v0.2.2 als Korrektur für die [Probleme mit der Authentifizierungsschleife](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) im Microsoft Internet Explorer und Microsoft Edge eingeführt. Setzen Sie das Flag `storeAuthStateInCookie` auf „true“, um diese Korrektur zu nutzen. Anschließend speichert MSAL.js den Authentifizierungsanforderungsstatus, der für die Überprüfung der Authentifizierungsflows erforderlich ist, in den Browsercookies. Standardmäßig ist dieses Flag auf `false` festgelegt.

- **logger**: Optional.  Ein Protokollierungsobjekt mit einer Rückrufinstanz, die vom Entwickler bereitgestellt werden kann, um Protokolle auf benutzerdefinierte Weise zu verarbeiten und zu veröffentlichen. Weitere Informationen zur Übergabe des Protokollierungsobjekts finden Sie unter [Protokollierung mit msal.js](msal-logging.md).

- **loadFrameTimeout**: Optional.  Die Anzahl der Millisekunden an Inaktivität, bevor für eine Antwort zur Tokenerneuerung von Azure AD eine Zeitüberschreitung angenommen wird. Der Standardwert ist 6 Sekunden.

- **tokenRenewalOffsetSeconds**: Optional. Die Anzahl der Millisekunden, die das Offsetfenster festlegt, das benötigt wird, um das Token vor Ablauf zu erneuern. Der Standardwert ist 300 Millisekunden.

- **navigateFrameWait**: Optional. Die Anzahl der Millisekunden für die Wartezeit, bevor ausgeblendete iFrames zu ihrem Ziel navigieren. Der Standardwert ist 500 Millisekunden.

Diese gelten nur für die Weitergabe aus der MSAL Angular-Wrapperbibliothek:
- **unprotectedResources**: Optional.  Array von URIs, die nicht geschützten Ressourcen sind. MSAL hängt kein Token an ausgehende Anforderungen an, die diesen URI enthalten. Der Standardwert lautet `null`.

- **protectedResourceMap**: Optional.  Dies ist die Zuordnung von Ressourcen zu Bereichen, die von MSAL für das automatische Anhängen von Zugriffstoken in Web-API-Aufrufen verwendet werden. Für die Ressource wird ein einzelnes Zugriffstoken abgerufen. Sie können einen bestimmten Ressourcenpfad wie folgt zuordnen: {"https://graph.microsoft.com/v1.0/me", ["user.read"]}, oder die App-URL der Ressource als: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Dies ist für CORS-Aufrufe erforderlich. Der Standardwert lautet `null`.
