---
title: 'Tutorial: Konfigurieren von askSpoke für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten aus Azure AD für askSpoke automatisch bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: f9458aac-f576-49ce-aba4-fc8302ed6360
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/02/2021
ms.author: thwimmer
ms.openlocfilehash: d458ad311b5338e7e559e7df89d9fcb8615648ee
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326886"
---
# <a name="tutorial-configure-askspoke-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von askSpoke für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in askSpoke als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen in [askSpoke](https://www.askspoke.com/) bereit bzw. hebt die Bereitstellung von Benutzern und Gruppen auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Unterstützte Funktionen

> [!div class="checklist"]
>
> -  Erstellen von Benutzern in askSpoke
> -  Entfernen von Benutzern aus askSpoke, wenn diese keinen Zugriff mehr benötigen
> -  Synchronisieren von Benutzerattributen zwischen Azure AD und askSpoke
> -  Bereitstellen von Gruppen und Gruppenmitgliedschaften in askSpoke
> -  [Einmaliges Anmelden bei](./askspoke-tutorial.md) askSpoke (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

-  [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md)
-  Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator).
-  Ein Benutzerkonto in askSpoke mit Administratorrechten

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung

1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und askSpoke zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-askspoke-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von askSpoke für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich bei Ihrer askSpoke-Verwaltungskonsole an.

2. Navigieren zu **Einstellungen**.

3. Klicken Sie auf die Registerkarte **Integrations** .

4. Scrollen Sie zur SCIM-Karte. Klicken Sie auf **Verbinden**.

   ![Bearbeiten](media/askspoke-provisioning-tutorial/connection.png)

5. Klicken Sie auf **Enable SCIM** (SCIM aktivieren).

6. Kopieren und speichern Sie das **API-Token**. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ der askSpoke-Anwendung in das Feld **Geheimes Token** eingegeben.

   ![API](media/askspoke-provisioning-tutorial/scim.png)

7. Die Mandanten-URL ist Ihre askSpoke-URL gefolgt von **/scim/v2**. Beispiel: `https://example.askspoke.com/scim/v2`. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ der askSpoke-Anwendung in das Feld **Mandanten-URL** eingegeben.

## <a name="step-3-add-askspoke-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von askSpoke aus dem Azure AD-Anwendungskatalog

Fügen Sie askSpoke aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in askSpoke zu beginnen. Wenn Sie askSpoke zuvor für das einmalige Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden.

-  Beim Zuweisen von Benutzern und Gruppen zu askSpoke müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen.

-  Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben.

## <a name="step-5-configure-automatic-user-provisioning-to-askspoke"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in askSpoke

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in ServiceNow auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-askspoke-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für askSpoke in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

   ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **askSpoke** aus.

   ![askSpoke-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

   ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

   ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

5. Fügen Sie im Abschnitt **Administratoranmeldeinformationen** Ihre askSpoke-Mandanten-URL und das geheime Token ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit askSpoke herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr askSpoke-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch mal.

   ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

   ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit askSpoke synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit askSpoke synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in askSpoke für Updatevorgänge verwendet werden. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die askSpoke-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   | attribute                                                             | type      | Unterstützung für das Filtern |
   | --------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                              | String    | &check;                 |
   | emails[type eq "work"].value                                          | String    |
   | aktiv                                                                | Boolean   |
   | title                                                                 | String    |
   | name.givenName                                                        | String    |
   | name.familyName                                                       | String    |
   | name.formatted                                                        | String    |
   | addresses[type eq "work"].locality                                    | String    |
   | addresses[type eq "work"].country                                     | String    |
   | addresses[type eq "work"].region                                      | String    |
   | externalId                                                            | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager    | Verweis |
   | urn:ietf:params:scim:schemas:extension:SpokeCustom:2.0:User:startDate | String    |

10.   Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit askSpoke synchronisieren** aus.

11.   Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit askSpoke synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden verwendet, um die Gruppen in askSpoke für Updatevorgänge abzugleichen. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

      | attribute   | type      | Unterstützung für das Filtern |
      | ----------- | --------- | ----------------------- |
      | displayName | String    | &check;                 |
      | members     | Verweis |

12.   Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13.   Um den Azure AD-Bereitstellungsdienst für askSpoke zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

      ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14.   Legen Sie die Benutzer und/oder Gruppen fest, die in askSpoke bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

      ![Bereitstellungsbereich](common/provisioning-scope.png)

15.   Wenn Sie fertig sind, klicken Sie auf **Speichern**.

      ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung

Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

-  [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
-  [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

-  [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)