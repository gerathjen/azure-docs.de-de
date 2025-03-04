---
title: 'Tutorial: Azure AD-SSO-Integration mit ScaleX Enterprise'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ScaleX Enterprise konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2021
ms.author: jeedes
ms.openlocfilehash: 05fa6eacd489a7071c0f1bcb3d5942e9230fe890
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308976"
---
# <a name="tutorial-azure-ad-sso-integration-with-scalex-enterprise"></a>Tutorial: Azure AD-SSO-Integration mit ScaleX Enterprise

In diesem Tutorial erfahren Sie, wie Sie ScaleX Enterprise in Azure Active Directory (Azure AD) integrieren. Die Integration von ScaleX Enterprise in Azure Active Directory ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf ScaleX Enterprise hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei ScaleX Enterprise anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein ScaleX Enterprise-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ScaleX Enterprise unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="add-scalex-enterprise-from-the-gallery"></a>Hinzufügen von ScaleX Enterprise aus dem Katalog

Zum Konfigurieren der Integration von ScaleX Enterprise in Azure AD müssen Sie ScaleX Enterprise aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ScaleX Enterprise** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ScaleX Enterprise** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-scalex-enterprise"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für ScaleX Enterprise

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ScaleX Enterprise mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ScaleX Enterprise eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ScaleX Enterprise die folgenden Schritte aus:
1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für ScaleX Enterprise](#configure-scalex-enterprise-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines ScaleX Enterprise-Testbenutzers](#create-scalex-enterprise-test-user)** , um eine Entsprechung von B. Simon in ScaleX Enterprise zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ScaleX Enterprise** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://platform.rescale.com/saml2/<company id>/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam von ScaleX Enterprise Client](https://about.rescale.com/contactus.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die ScaleX Enterprise-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **emailaddress** ist hier **user.mail** zugeordnet. Die ScaleX Enterprise-Anwendung erwartet, dass **emailaddress** der Wert **user.userprincipalname** zugeordnet ist. Sie müssen die Attributzuordnung daher entsprechend ändern, indem Sie auf das Symbol **Bearbeiten** klicken.

    ![image](common/edit-attribute.png)

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **ScaleX Enterprise einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ScaleX Enterprise gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **ScaleX Enterprise** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-scalex-enterprise-sso"></a>Konfigurieren des einmaligen Anmeldens für ScaleX Enterprise

1. Wenn Sie die Konfiguration in ScaleX Enterprise automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **ScaleX Enterprise einrichten**, um zur Anwendung ScaleX Enterprise weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei ScaleX Enterprise anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie ScaleX Enterprise manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der ScaleX Enterprise-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

1. Klicken Sie auf das Menü oben rechts, und wählen Sie **Contoso Administration** aus.

    > [!NOTE]
    > „Contoso“ ist nur ein Beispiel. Auf der Seite steht der tatsächliche Name Ihres Unternehmens.

    ![Screenshot: Im Menü in der Ecke oben rechts ausgewählter Beispielunternehmensname](./media/scalex-enterprise-tutorial/admin.png)

1. Wählen Sie aus dem oberen Menü die Option **Integrations** (Integrationen) und dann **Single Sign-On** (Einmaliges Anmelden) aus.

    ![Screenshot: Ausgewählte Option „Integrations“ (Integrationen) und ausgewählte Option „Single Sign-on“ (Einmaliges Anmelden) im Dropdownmenü](./media/scalex-enterprise-tutorial/menu.png) 

1. Füllen Sie das Formular wie folgt aus:

    ![Einmaliges Anmelden konfigurieren](./media/scalex-enterprise-tutorial/settings.png)

    a. Wählen Sie **Create any user who can authenticate with SSO** (Benutzer*innen erstellen, die sich mit SSO authentifizieren können) aus.

    b. **Service Provider saml** (Dienstanbieter-SAML): Fügen Sie den Wert **urn:oasis:names:tc:SAML:2.0:nameid-format:persistent** ein.

    c. **Name of Identity Provider email field in ACS response** (E-Mail-Feld für den Namen des Identitätsanbieters in der ACS-Antwort): Fügen Sie den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.

    d. **Identity Provider EntityDescriptor Entity ID** (EntityDescriptor-Entitäts-ID des Identitätsanbieters): Fügen Sie den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. **Identity Provider SingleSignOnService URL** (SingleSignOnService-URL des Identitätsanbieters): Fügen Sie die **Anmelde-URL** aus dem Azure-Portal ein.

    f. **Identity Provider public X509 certificate** (Öffentliches X.509-Zertifikat des Identitätsanbieters): Öffnen Sie das aus Azure heruntergeladene X.509-Zertifikat im Editor, und fügen Sie den Inhalt in dieses Feld ein. Stellen Sie sicher, dass das Zertifikat keine Zeilenumbrüche enthält.

    g. Aktivieren Sie die folgenden Kontrollkästchen: **Enabled**, „Encrypt NameID“ und „Sign AuthnRequests“.

    h. Klicken Sie auf **Update SSO Settings**, um die Einstellungen zu speichern.

### <a name="create-scalex-enterprise-test-user"></a>Erstellen eines ScaleX Enterprise-Testbenutzers

Damit sich Azure AD-Benutzer bei ScaleX Enterprise anmelden können, müssen sie in ScaleX Enterprise bereitgestellt werden. Bei ScaleX Enterprise erfolgt die Bereitstellung automatisch, sodass keine manuellen Schritte erforderlich sind. Alle Benutzer, die sich erfolgreich mit Anmeldeinformationen für das einmalige Anmelden authentifizieren können, werden automatisch auf der Seite von ScaleX bereitgestellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für ScaleX Enterprise weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die ScaleX Enterprise-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der ScaleX Enterprise-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „ScaleX Enterprise“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der ScaleX Enterprise-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von ScaleX Enterprise können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
