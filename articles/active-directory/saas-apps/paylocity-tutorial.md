---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Paylocity | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Paylocity konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 83d8290555bfc13e987e019e62e635d11ca3e136
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333996"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-paylocity"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Paylocity

In diesem Tutorial erfahren Sie, wie Sie Paylocity in Azure Active Directory (Azure AD) integrieren. Die Integration von Paylocity in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Paylocity hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Paylocity anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Paylocity-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Paylocity unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="add-paylocity-from-the-gallery"></a>Hinzufügen von Paylocity aus dem Katalog

Zum Konfigurieren der Integration von Paylocity in Azure AD müssen Sie Paylocity aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Paylocity** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Paylocity** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-paylocity"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Paylocity

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Paylocity mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Paylocity eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Paylocity die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Paylocity](#configure-paylocity-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Paylocity-Testbenutzers](#create-paylocity-test-user)** , um eine Entsprechung von B. Simon in Paylocity zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Paylocity** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://access.paylocity.com/`.

1. Klicken Sie auf **Speichern**.

1. Die Paylocity-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Paylocity-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie müssen sie jedoch durch echte Werte ersetzen.

    | Name |  Quellattribut|
    | ---------------| --------------- |
    | PartnerID | `P8000010` |
    | PaylocityUser | `user.mail`|
    | PaylocityEntity | < `PaylocityEntity` > |

    > [!NOTE]
    > „PaylocityEntity“ ist die Paylocity-Unternehmens-ID.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf das **Bearbeitungssymbol**.

    ![Screenshot: „SAML-Signaturzertifikat“ mit Auswahl der Aktion „Herunterladen“ unter „Verbundmetadaten-XML“](./media/paylocity-tutorial/edit-samlassertion.png)

1. Wählen Sie für **Signaturoption** die Option **SAML-Antwort und -Assertion signieren** aus, und klicken Sie auf **Speichern**.

    ![Bearbeiten des SAML-Signaturzertifikats](./media/paylocity-tutorial/saml-assertion.png)

1. Kopieren Sie im Abschnitt **Paylocity einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Paylocity gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Paylocity** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-paylocity-sso"></a>Konfigurieren des einmaligen Anmeldens für Paylocity

Führen Sie zum Konfigurieren des einmaligen Anmeldens aufseiten von **Paylocity** die folgenden Schritte aus:

1. Laden Sie die **Verbundmetadaten-XML** herunter.
1. Navigieren Sie in Paylocity zu **HR & Payroll** > **User Access** > **SSO Configuration** (Personalverwaltung und Lohnbuchhaltung > Benutzerzugriff > SSO-Konfiguration).
1. Wählen Sie unter **SSO Integrations** (SSO-Integrationen) die Option **Add SSO Integration** (SSO-Integration hinzufügen) aus. Daraufhin wird ein neuer Bereich geöffnet.
1. Wählen Sie in der Dropdownliste den SSO-Anbieter **Microsoft Azure** aus.
1. Wählen Sie in der Dropdownliste den Eintrag **Status** aus.
1. Ziehen Sie die Metadatendatei per Drag & Drop in den Ablegebereich. Paylocity versucht, die Aussteller-, Post/Redirect- und Binding-URLs sowie das/die Sicherheitszertifikat(e) zu analysieren.
1. Wählen Sie **Speichern** aus, um die Änderungen zu bestätigen. Die Integration sollte unter **SSO Integrations** (SSO-Integrationen) angezeigt werden.

### <a name="create-paylocity-test-user"></a>Erstellen eines Paylocity-Testbenutzers

In diesem Abschnitt erstellen Sie in Paylocity einen Benutzer namens B. Simon. Zum Hinzufügen der Benutzer auf der Paylocity-Plattform wenden Sie sich an das [Supportteam von Paylocity](mailto:service@paylocity.com). Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Paylocity weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Paylocity-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Paylocity-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Paylocity“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Paylocity-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Paylocity können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
