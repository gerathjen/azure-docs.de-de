---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SKYSITE | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SKYSITE konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: 82ecf5566bd9ba8a207658bc42083f3e80aa2f88
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124752551"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-skysite"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SKYSITE

In diesem Tutorial erfahren Sie, wie Sie SKYSITE in Azure Active Directory (Azure AD) integrieren. Die Integration von SKYSITE in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SKYSITE hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SKYSITE anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SKYSITE-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SKYSITE unterstützt **IDP-initiiertes** einmaliges Anmelden.

* SKYSITE unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-skysite-from-the-gallery"></a>Hinzufügen von SKYSITE aus dem Katalog

Zum Konfigurieren der Integration von SKYSITE in Azure AD müssen Sie SKYSITE aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SKYSITE** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SKYSITE** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on-for-skysite"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SKYSITE

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SKYSITE mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SKYSITE eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SKYSITE die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SKYSITE](#configure-skysite-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SKYSITE-Testbenutzers](#create-skysite-test-user)** , um eine Entsprechung von B. Simon in SKYSITE zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SKYSITE** auf die Registerkarte **Eigenschaften**, und führen Sie die folgenden Schritte aus: 

    ![Eigenschaften für das einmalige Anmelden](./media/skysite-tutorial/config05.png)

    * Kopieren Sie die **URL für den Benutzerzugriff**. Diese muss im Abschnitt **Konfigurieren des einmaligen Anmeldens für SKYSITE** eingefügt werden. Dies wird später in diesem Tutorial erläutert.

1. Navigieren Sie auf der Anwendungsintegrationsseite für **SKYSITE** zu **Einmaliges Anmelden**.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **Grundlegende SAML-Konfiguration** ist die Anwendung im **IDP-initiierten** Modus vorkonfiguriert, und die erforderlichen URLs sind bereits mit Azure vorausgefüllt. Der Benutzer muss die Konfiguration speichern, indem er auf die Schaltfläche **Speichern** klickt.

1. Die SKYSITE-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld „Benutzerattribute“ zu öffnen.

    ![Screenshot: Benutzerattribute mit ausgewähltem Bearbeitungssymbol](common/edit-attribute.png)

1. Darüber hinaus wird von der SKYSITE-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Gruppenansprüche (Vorschau)** im Abschnitt **Benutzerattribute und Ansprüche** die folgenden Schritte aus:

    a. Klicken Sie auf den **Stift** neben **Im Anspruch zurückgegebene Gruppen**.

    ![Screenshot: Benutzeransprüche mit Option zum Hinzufügen eines neuen Anspruchs](./media/skysite-tutorial/config01.png)

    ![Screenshot des Dialogfelds „Benutzeransprüche verwalten“, in dem Sie die hier beschriebenen Werte eingeben können](./media/skysite-tutorial/config02.png)

    b. Wählen Sie **Alle Gruppen** aus der Optionsfeldliste aus.

    c. Wählen Sie **Quellattribut** von **Gruppen-ID** aus.

    d. Klicken Sie auf **Speichern**.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **SKYSITE einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SKYSITE gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SKYSITE** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-skysite-sso"></a>Konfigurieren des einmaligen Anmeldens für SKYSITE

1. Melden Sie sich in einem neuen Webbrowserfenster bei der SKYSITE-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Klicken Sie oben rechts auf der Seite auf **Settings** (Einstellungen), und navigieren Sie anschließend zu **Account setting** (Kontoeinstellung).

    ![Screenshot, in dem in den Einstellungen die Option „Kontoeinstellung“ ausgewählt ist.](./media/skysite-tutorial/config03.png)

5. Führen Sie auf der Registerkarte **Single sign on (SSO)** (Einmaliges Anmelden) die folgenden Schritte aus:

    ![Screenshot der Registerkarte „Einmaliges Anmelden“, auf der Sie die beschriebenen Werte eingeben können.](./media/skysite-tutorial/config04.png)

    a. Fügen Sie in das Textfeld **Identity Provider sign in URL** (Anmelde-URL des Identitätsanbieters) den Wert der **URL für den Benutzerzugriff** ein, den Sie im Azure-Portal auf der Registerkarte **Eigenschaften** kopiert haben.

    b. Klicken Sie auf **Upload certificate** (Zertifikat hochladen), um das Base64-codierte Zertifikat hochzuladen, das Sie aus dem Azure-Portal heruntergeladen haben.

    c. Klicken Sie auf **Speichern**.

### <a name="create-skysite-test-user"></a>Erstellen eines SKYSITE-Testbenutzers

In diesem Abschnitt wird in SKYSITE ein Benutzer mit dem Namen Britta Simon erstellt. SKYSITE unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in SKYSITE vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SKYSITE“ klicken, sollten Sie automatisch bei der SKYSITE-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [SKYSITE mit Azure AD ausprobieren](https://aad.portal.azure.com/)