---
title: Erste Schritte mit benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc87628d8b47435012c3d20ec2e72ac186983555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189326"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Benutzerdefinierte Richtlinien](custom-policy-overview.md) sind Konfigurationsdateien, die das Verhalten Ihres Azure AD B2C-Mandanten (Azure Active Directory B2C) definieren. In diesem Artikel erfahren Sie, wie Sie eine benutzerdefinierte Richtlinie erstellen, die die Registrierung oder Anmeldung mit einem lokalen Konto über eine E-Mail-Adresse und ein Kennwort unterstützt. Außerdem bereiten Sie Ihre Umgebung für das Hinzufügen von Identitätsanbietern vor.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie noch nicht über einen Azure AD B2C-Mandanten verfügen, [erstellen Sie einen](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.
- [Registrieren Sie Ihre Anwendung](tutorial-register-applications.md) in dem Mandanten, den Sie erstellt haben, damit er mit Azure AD B2C kommunizieren kann.
- Führen Sie die Schritte unter [Einrichten der Registrierung und Anmeldung mit einem Facebook-Konto mithilfe von Azure Active Directory B2C](identity-provider-facebook.md) aus, um eine Facebook-Anwendung zu konfigurieren. Wenngleich für die Verwendung von benutzerdefinierten Richtlinien keine Facebook-Anwendung erforderlich ist, wird in dieser exemplarischen Vorgehensweise eine solche Anwendung verwendet, um die Aktivierung des Soziale Medien-Logins in einer benutzerdefinierten Richtlinie zu veranschaulichen.

## <a name="add-signing-and-encryption-keys"></a>Hinzufügen von Signatur- und Verschlüsselungsschlüsseln

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie auf der Übersichtsseite unter **Richtlinien** die Option **Identity Experience Framework** aus.

### <a name="create-the-signing-key"></a>Erstellen des Signaturschlüssels

1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Generate`.
1. Geben Sie unter **Name**`TokenSigningKeyContainer` ein. Das Präfix `B2C_1A_` wird möglicherweise automatisch hinzugefügt.
1. Wählen Sie **RSA** als **Schlüsseltyp** aus.
1. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.
1. Klicken Sie auf **Erstellen**.

### <a name="create-the-encryption-key"></a>Erstellen des Verschlüsselungsschlüssels

1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Generate`.
1. Geben Sie unter **Name**`TokenEncryptionKeyContainer` ein. Das Präfix `B2C_1A`_ wird möglicherweise automatisch hinzugefügt.
1. Wählen Sie **RSA** als **Schlüsseltyp** aus.
1. Wählen Sie für **Schlüsselverwendung** die Option **Verschlüsselung** aus.
1. Klicken Sie auf **Erstellen**.

### <a name="create-the-facebook-key"></a>Erstellen des Facebook-Schlüssels

Fügen Sie das [App-Geheimnis](identity-provider-facebook.md) der Facebook-Anwendung als Richtlinienschlüssel hinzu. Sie können das App-Geheimnis der Anwendung verwenden, das Sie unter „Voraussetzungen“ in diesem Artikel erstellt haben.

1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie unter **Name**`FacebookSecret` ein. Das Präfix `B2C_1A_` wird möglicherweise automatisch hinzugefügt.
1. Geben Sie unter **Geheimnis** das *App-Geheimnis* der Facebook-Anwendung aus developers.facebook.com ein. Bei diesem Wert handelt sich um das Geheimnis und nicht um die Anwendungs-ID.
1. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.
1. Klicken Sie auf **Erstellen**.

## <a name="register-identity-experience-framework-applications"></a>Registrieren von Identity Experience Framework-Anwendungen

Azure AD B2C erfordert, dass Sie zwei Anwendungen registrieren, die zur Registrierung und Anmeldung von Benutzern mit lokalen Konten verwendet werden: *IdentityExperienceFramework* (eine Web-API) und *ProxyIdentityExperienceFramework* (eine native App) mit delegierter Berechtigung für die IdentityExperienceFramework-App. Ihre Benutzer können sich mit einer E-Mail-Adresse oder einem Benutzernamen und einem Kennwort für den Zugriff auf Ihre mandantenregistrierten Anwendungen anmelden, wodurch ein „lokales Konto“ erstellt wird. Lokale Konten sind nur in Ihrem Azure AD B2C-Mandanten vorhanden.

Sie müssen diese beiden Anwendungen nur ein Mal in Ihrem Azure AD B2C-Mandanten registrieren.

### <a name="register-the-identityexperienceframework-application"></a>Registrieren der IdentityExperienceFramework-Anwendung

Zum Registrieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie die Benutzeroberfläche für **App-Registrierungen (Legacy)** oder unsere neue einheitliche Benutzeroberfläche **App-Registrierungen (Vorschau)** verwenden. [Weitere Informationen zur neuen Oberfläche](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Anwendungen](#tab/applications/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie im Azure-Portal nach **Azure Active Directory**, und wählen Sie es aus.
1. Wählen Sie im Übersichtsmenü **Azure Active Directory** unter **Verwalten** die Option **App-Registrierungen (Legacy)** aus.
1. Wählen Sie **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name**`IdentityExperienceFramework` ein.
1. Wählen Sie unter **Anwendungstyp** die Option **Web-App/API** aus.
1. Geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` als **Anmelde-URL** ein, wobei `your-tenant-name` für den Domänennamen Ihres Azure AD B2C-Mandanten steht. Alle URLs sollten jetzt [b2clogin.com](b2clogin.md) verwenden.
1. Klicken Sie auf **Erstellen**. Wenn der Bestellvorgang abgeschlossen ist, kopieren Sie die Anwendungs-ID und speichern Sie sie zur späteren Verwendung.

#### <a name="app-registrations-preview"></a>[App-Registrierungen (Vorschau)](#tab/app-reg-preview/)

1. Wählen Sie **App-Registrierungen (Vorschau)** und dann **Neue Registrierung** aus.
1. Geben Sie unter **Name**`IdentityExperienceFramework` ein.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis** aus.
1. Wählen Sie unter **Umleitungs-URI** die Option **Web** aus, und geben Sie dann `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` ein, wobei `your-tenant-name` für den Domänennamen des Azure AD B2C-Mandanten steht.
1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen *Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen*.
1. Wählen Sie **Registrieren**.
1. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt.

Stellen Sie nun die API bereit, indem Sie einen Bereich hinzufügen:

1. Wählen Sie unter **Verwalten** die Option **Eine API verfügbar machen** aus.
1. Wählen Sie **Bereich hinzufügen** und dann **Speichern und fortfahren** aus, um den URI der Standardanwendungs-ID zu akzeptieren.
1. Geben Sie die folgenden Werte ein, um einen Bereich zu erstellen, der die benutzerdefinierte Richtlinienausführung in Ihrem Azure AD B2C-Mandanten ermöglicht:
    * **Bereichsname**: `user_impersonation`
    * **Anzeigename der Administratoreinwilligung**: `Access IdentityExperienceFramework`
    * **Beschreibung der Administratoreinwilligung**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Wählen Sie **Bereich hinzufügen** aus.

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrieren der ProxyIdentityExperienceFramework-Anwendung

#### <a name="applications"></a>[Anwendungen](#tab/applications/)

1. Wählen Sie unter **App-Registrierungen (Legacy)** die Option **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name**`ProxyIdentityExperienceFramework` ein.
1. Wählen Sie **Nativ** als **Anwendungstyp** aus.
1. Geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` als **Umleitungs-URI** ein, wobei `your-tenant-name` für Ihren Azure AD B2C-Mandanten steht.
1. Klicken Sie auf **Erstellen**. Wenn der Bestellvorgang abgeschlossen ist, kopieren Sie die Anwendungs-ID und speichern Sie sie zur späteren Verwendung.
1. Wählen Sie **Einstellungen**, anschließend **Erforderliche Berechtigungen** und dann **Hinzufügen** aus.
1. Wählen Sie **API auswählen** aus, suchen Sie nach **IdentityExperienceFramework**, wählen Sie diese API aus, und klicken Sie anschließend auf **Auswählen**.
1. Aktivieren Sie das Kontrollkästchen neben **Access IdentityExperienceFramework** (Auf IdentityExperienceFramework zugreifen), und klicken Sie erst auf **Auswählen** und dann auf **Done** (Fertig).
1. Wählen Sie **Berechtigungen erteilen** aus, und bestätigen Sie dies anschließend durch Auswahl von **Ja**.

#### <a name="app-registrations-preview"></a>[App-Registrierungen (Vorschau)](#tab/app-reg-preview/)

1. Wählen Sie **App-Registrierungen (Vorschau)** und dann **Neue Registrierung** aus.
1. Geben Sie unter **Name**`ProxyIdentityExperienceFramework` ein.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis** aus.
1. Verwenden Sie die Dropdownliste unter **Umleitungs-URI**, um **Öffentlicher Client/nativ (mobil und Desktop)** auszuwählen.
1. Geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` als **Umleitungs-URI** ein, wobei `your-tenant-name` für Ihren Azure AD B2C-Mandanten steht.
1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen *Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen*.
1. Wählen Sie **Registrieren**.
1. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt.

Geben Sie nun an, dass die Anwendung als öffentlicher Client behandelt werden soll:

1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
1. Wählen Sie **Neue Benutzeroberfläche ausprobieren** aus (sofern die Option angezeigt wird).
1. Aktivieren Sie unter **Erweiterte Einstellungen** die Option **Anwendung als öffentlichen Client behandeln** aus (wählen Sie **Ja** aus).
1. Wählen Sie **Speichern** aus.

Erteilen Sie nun Berechtigungen für den API-Bereich, den Sie zuvor in der Registrierung *IdentityExperienceFramework* verfügbar gemacht haben:

1. Wählen Sie unter **Verwalten** die Option **API-Berechtigungen**.
1. Wählen Sie unter **Konfigurierte Berechtigungen** die Option **Berechtigung hinzufügen** aus.
1. Wählen Sie die Registerkarte **Meine APIs** aus, und wählen Sie dann die Anwendung **IdentityExperienceFramework** aus.
1. Wählen Sie unter **Berechtigung** den Bereich **user_impersonation** aus, den Sie zuvor definiert haben.
1. Wählen Sie **Berechtigungen hinzufügen** aus. Warten Sie einige Minuten, bevor Sie mit dem nächsten Schritt fortfahren.
1. Wählen Sie **Administratorzustimmung für (Name Ihres Mandanten) erteilen** aus.
1. Wählen Sie das derzeit angemeldete Administratorkonto aus, oder melden Sie sich mit einem Konto bei Ihrem Azure AD B2C-Mandanten an, dem mindestens die Rolle *Cloudanwendungsadministrator* zugewiesen wurde.
1. Wählen Sie **Akzeptieren** aus.
1. Wählen Sie **Aktualisieren** aus, und vergewissern Sie sich, dass für beide Bereiche unter **Status** der Status „Gewährt für...“ angezeigt wird. Es kann einige Minuten dauern, bis die Berechtigungen weitergegeben wurden.

* * *

## <a name="custom-policy-starter-pack"></a>Starter Pack für benutzerdefinierte Richtlinien

Benutzerdefinierte Richtlinien sind ein Satz von XML-Dateien, die Sie in den Azure AD B2C-Mandanten hochladen, um technische Profile und User Journeys zu definieren. Wir stellen Starter Packs mit mehreren vordefinierten Richtlinien bereit, damit Sie schnell loslegen können. Jedes dieser Starter Packs enthält die kleinste Anzahl von technischen Profilen sowie die zum Erreichen der beschriebenen Szenarien benötigten User Journeys:

- **LocalAccounts** ermöglicht die ausschließliche Nutzung von lokalen Konten.
- **SocialAccounts** ermöglicht die ausschließliche Nutzung von Konten sozialer Netzwerke (oder Verbundkonten).
- **SocialAndLocalAccounts** ermöglicht sowohl die Verwendung von lokalen Konten als auch von Konten für soziale Netzwerke.
- **SocialAndLocalAccountsWithMFA** ermöglicht lokale Optionen und Optionen für soziale Netzwerke sowie zur Multi-Factor Authentication.

Die einzelnen Starter Packs enthalten Folgendes:

- **Basisdatei**: Es sind einige Änderungen an der Basisdatei erforderlich. Beispiel: *TrustFrameworkBase.xml*
- **Erweiterungsdatei**: In dieser Datei werden die meisten Konfigurationsänderungen vorgenommen. Beispiel: *TrustFrameworkExtensions.xml*
- **Dateien der vertrauenden Seite**: aufgabenspezifische Dateien, die von Ihrer Anwendung aufgerufen werden. Beispiele: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

In diesem Artikel bearbeiten Sie die benutzerdefinierten XML-Richtliniendateien im Starter Pack **SocialAndLocalAccounts**. Wenn Sie einen XML-Editor benötigen, können Sie [Visual Studio Code](https://code.visualstudio.com/download), einen einfachen plattformübergreifenden Editor, ausprobieren.

### <a name="get-the-starter-pack"></a>Abrufen des Starter Packs

Holen Sie sich die Starter Packs für benutzerdefinierte Richtlinien-von GitHub, und aktualisieren Sie dann die XML-Dateien im Starter Pack „SocialAndLocalAccounts“ mit dem Namen Ihres Azure AD B2C-Mandanten.

1. [Laden Sie die ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip), oder klonen Sie das Repository:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Ersetzen Sie in allen Dateien im Verzeichnis **SocialAndLocalAccounts** die Zeichenfolge `yourtenant` durch den Namen Ihres Azure AD B2C-Mandanten.

    Wenn z. B. der Name des B2C-Mandanten *contosotenant* lautet, werden alle Instanzen von `yourtenant.onmicrosoft.com` in `contosotenant.onmicrosoft.com` geändert.

### <a name="add-application-ids-to-the-custom-policy"></a>Hinzufügen der Anwendungs-IDs zur benutzerdefinierten Richtlinie

Fügen Sie die Anwendung-ID zur Erweiterungsdatei *TrustFrameworkExtensions.xml* hinzu.

1. Öffnen Sie `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** , und suchen Sie das Element `<TechnicalProfile Id="login-NonInteractive">`.
1. Ersetzen Sie beide Instanzen von `IdentityExperienceFrameworkAppId` durch die Anwendungs-ID der IdentityExperienceFramework-Anwendung, die Sie zuvor erstellt haben.
1. Ersetzen Sie beide Instanzen von `ProxyIdentityExperienceFrameworkAppId` durch die Anwendungs-ID der ProxyIdentityExperienceFramework-Anwendung, die Sie zuvor erstellt haben.
1. Speichern Sie die Datei .

## <a name="upload-the-policies"></a>Hochladen der Richtlinien

1. Wählen Sie in Ihrem B2C-Mandanten im Azure-Portal das Menüelement **Identity Experience Framework** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus.
1. Laden Sie die Richtliniendateien in dieser Reihenfolge hoch:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Wenn Sie die Dateien hochladen, fügt Azure jeder Datei das Präfix `B2C_1A_` hinzu.

> [!TIP]
> Wenn Ihr XML-Editor Validierungen unterstützt, überprüfen Sie die Dateien anhand des XML-Schemas `TrustFrameworkPolicy_0.3.0.0.xsd` im Stammverzeichnis des Starter Packs. Durch die XML-Schemavalidierung werden vor dem Upload Fehler festgestellt.

## <a name="test-the-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie unter **Benutzerdefinierte Richtlinien** die Richtlinie **B2C_1A_signup_signin** aus.
1. Wählen Sie auf der Übersichtsseite der benutzerdefinierten Richtlinie unter **Anwendung auswählen** die Webanwendung namens *webapp1* aus, die Sie zuvor registriert haben.
1. Stellen Sie sicher, dass die **Antwort-URL**`https://jwt.ms` lautet.
1. Wählen Sie **Jetzt ausführen** aus.
1. Registrieren Sie sich mit einer E-Mail-Adresse.
1. Wählen Sie erneut **Jetzt ausführen** aus.
1. Melden Sie sich zur Bestätigung der richtigen Konfiguration mit demselben Konto an.

## <a name="add-facebook-as-an-identity-provider"></a>Hinzufügen von Facebook als Identitätsanbieter

Wie unter [Voraussetzungen](#prerequisites) erwähnt, ist Facebook *nicht* erforderlich, um benutzerdefinierte Richtlinien zu verwenden. Die Anwendung wird hier jedoch verwendet, um zu veranschaulichen, wie Sie die Soziale Medien-Verbundanmeldung in einer benutzerdefinierten Richtlinie aktivieren können.

1. Ersetzen Sie in der Datei `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** den Wert `client_id` durch die ID der Facebook-Anwendung:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Laden Sie die Datei *TrustFrameworkExtensions.xml* in Ihren Mandanten hoch.
1. Wählen Sie unter **Benutzerdefinierte Richtlinien** die Richtlinie **B2C_1A_signup_signin** aus.
1. Wählen Sie **Jetzt ausführen**, und wählen Sie „Facebook“ aus, um sich bei Facebook anzumelden und die benutzerdefinierte Richtlinie zu testen.

## <a name="next-steps"></a>Nächste Schritte

Fügen Sie als Nächstes Azure Active Directory (Azure AD) als Identitätsanbieter hinzu. Die in diesem Leitfaden für erste Schritte verwendete Basisdatei enthält bereits einige Inhalte, die Sie zum Hinzufügen anderer Identitätsanbieter, z. B. Azure AD, benötigen.

Weitere Informationen zur Einrichtung von Azure AD als Identitätsanbieter finden Sie unter [Einrichten der Anmeldung mit einem Azure Active Directory-Konto mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C](identity-provider-azure-ad-single-tenant-custom.md).
