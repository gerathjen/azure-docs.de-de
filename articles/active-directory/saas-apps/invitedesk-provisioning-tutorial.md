---
title: 'Tutorial: Konfigurieren von InviteDesk für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Benutzerkonten aus Azure AD für InviteDesk automatisch bereitstellen und die Bereitstellung wieder aufheben.
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: d3291257-0dc0-4ed7-ae21-29249ce664df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/11/2021
ms.author: thwimmer
ms.openlocfilehash: e38acc922667aca595a7cd80cd7039eff16b9e13
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122447043"
---
# <a name="tutorial-configure-invitedesk-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von InviteDesk für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in InviteDesk als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen für [InviteDesk](https://invitedesk.com/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in InviteDesk
> * Entfernen von Benutzern aus InviteDesk, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und InviteDesk
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in InviteDesk
> * [Einmaliges Anmelden (SSO)](../manage-apps/add-application-portal-setup-oidc-sso.md) bei InviteDesk (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein [InviteDesk](https://invitedesk.com/)-Mandant.
* Ein Benutzerkonto in InviteDesk mit Administratorberechtigungen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
1. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
1. Legen Sie fest, welche Daten [zwischen Azure AD und InviteDesk zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-invitedesk-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von InviteDesk für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich bei der [InviteDesk-Verwaltungskonsole](https://app.invitedesk.com/) an. Navigieren Sie zu **Settings > Active Directory** (Einstellungen > Active Directory).


      ![InviteDesk-Einstellungen](./media\invitedesk-provisioning-tutorial\invitedesk-settings.png)

1. Geben Sie die **Mandanten-ID für Azure** ein, und klicken Sie dann auf die Umschaltfläche, um den entsprechenden **Zugriffscode** zu generieren.

      ![Seite für InviteDesk-Token](./media\invitedesk-provisioning-tutorial\invitedesk-token-page.png)


1. Beim Klicken auf die Umschaltfläche wird der **Zugriffscode** generiert, der der **Mandanten-ID für Azure** entspricht. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ für Ihre InviteDesk-Anwendung in das Feld **Geheimes Token*** eingegeben. 

      ![InviteDesk-Token generieren](./media\invitedesk-provisioning-tutorial\invitedesk-token-generate.png)


## <a name="step-3-add-invitedesk-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von InviteDesk aus dem Azure AD-Anwendungskatalog

Fügen Sie InviteDesk aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in InviteDesk zu beginnen. Wenn Sie InviteDesk zuvor für einmaliges Anmelden (SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu InviteDesk müssen Sie eine andere Rolle als den **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-invitedesk"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für InviteDesk 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in InviteDesk auf Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-invitedesk-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für InviteDesk in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **InviteDesk** aus.

    ![InviteDesk-Link in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die **Mandanten-URL** und das **geheime Token** für InviteDesk ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit InviteDesk herstellen kann. Vergewissern Sie sich bei einem Verbindungsfehler, dass Ihr InviteDesk-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit InviteDesk synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die aus Azure AD mit InviteDesk synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in InviteDesk für Updatevorgänge verwendet. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die InviteDesk-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|Typ|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;
   |aktiv|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].country|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |externalId|String|
   |preferredLanguage|String|


1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit InviteDesk synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit InviteDesk synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in InviteDesk für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|Typ|Unterstützung für das Filtern|
   |---|---|---|
   |displayName|String|&check;
   |externalId|String|
   |members|Verweis|
   
1. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Um den Azure AD-Bereitstellungsdienst für InviteDesk zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

1. Legen Sie die Benutzer und/oder Gruppen fest, die in InviteDesk bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

1. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

* Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
* Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
* Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="more-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)