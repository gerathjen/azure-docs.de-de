---
title: Verwalten von Berechtigungen und Sicherheit für Rollen in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) verwenden, um eine präzise Zugriffsverwaltung für Azure-Ressourcen zu erzielen.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 67f7076852ffe810e213fcc7d8cb6188d6db405d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057850"
---
# <a name="manage-role-permissions-and-security-in-automation"></a>Verwalten von Berechtigungen und Sicherheit für Rollen in Automation

Die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure-Ressourcen. Mithilfe der [rollenbasierten Zugriffssteuerung von Azure](../role-based-access-control/overview.md) können Sie Aufgaben innerhalb Ihres Teams verteilen sowie Benutzern, Gruppen und Anwendungen nur den Zugriff gewähren, den diese zur Ausführung ihrer Aufgaben benötigen. Sie können Benutzern über das Azure-Portal, über Azure-Befehlszeilentools oder über Azure-Verwaltungs-APIs rollenbasierten Zugriff gewähren.

## <a name="roles-in-automation-accounts"></a>Rollen in Automation-Konten

Der Zugriff wird in Azure Automation erteilt, indem den Benutzern, Gruppen und Anwendungen im Automation-Kontenbereich die passende Azure-Rolle zugewiesen wird. Die folgenden vordefinierten Rollen werden von Automation-Konten unterstützt:

| **Rolle** | **Beschreibung** |
|:--- |:--- |
| Besitzer |Die Rolle „Besitzer“ erlaubt den Zugriff auf alle Ressourcen und Aktionen innerhalb eines Automation-Kontos, z.B. den Zugriff auf andere Benutzer, Gruppen und Anwendungen, um das Automation-Konto zu verwalten. |
| Mitwirkender |Die Rolle „Mitwirkender“ erlaubt Ihnen, fast alles zu verwalten. Das Einzige, was Sie nicht können, ist das Ändern der Zugriffsberechtigungen für ein Automation-Konto anderer Benutzer. |
| Leser |Mit der Rolle „Leser“ können Sie alle Ressourcen in einem Automation-Konto anzeigen, aber keine Änderungen vornehmen. |
| Mitwirkender für Automatisierung | Mit der Rolle „Mitwirkender für Automatisierung“ können Sie alle Ressourcen im Automation-Konto verwalten, mit Ausnahme von Änderungen an den Zugriffsberechtigungen anderer Benutzer für ein Automation-Konto. |
| Operator für Automation |Die Rolle „Operator für Automation“ ermöglicht das Anzeigen des Namens und der Eigenschaften des Runbooks sowie das Erstellen und Verwalten von Aufträgen für alle Runbooks in einem Automation-Konto. Diese Rolle ist hilfreich, wenn Sie Ihre Automation-Konten-Ressourcen wie Anmeldeinformationsobjekte und Runbooks vor Einblicken und Änderungen schützen, Mitgliedern Ihrer Organisation aber dennoch ermöglichen möchten, diese Runbooks auszuführen. |
|Automation-Auftragsoperator|Die Rolle „Automation-Auftragsoperator“ ermöglicht das Erstellen und Verwalten von Aufträgen für alle Runbooks in einem Automation-Konto.|
|Automation-Runbookoperator|Die Rolle „Automation-Runbookoperator“ ermöglicht das Lesen des Namens und der Eigenschaften eines Runbooks.|
| Log Analytics-Mitwirkender | Die Rolle „Log Analytics-Mitwirkender“ erlaubt Ihnen, alle Überwachungsdaten zu lesen und Überwachungseinstellungen zu bearbeiten. Das Bearbeiten von Überwachungseinstellungen schließt folgende Aufgaben ein: Hinzufügen der VM-Erweiterung zu VMs, Lesen von Speicherkontoschlüsseln zum Konfigurieren von Protokollsammlungen aus Azure Storage, Erstellen und Konfigurieren von Automation-Konten, Hinzufügen von Azure Automation-Features und Konfigurieren der Azure-Diagnose für alle Azure-Ressourcen.|
| Log Analytics-Leser | Die Rolle „Log Analytics-Leser“ erlaubt Ihnen, alle Überwachungsdaten anzuzeigen und zu durchsuchen sowie Überwachungseinstellungen anzuzeigen. Dies schließt auch die Anzeige der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen ein. |
| Überwachungsmitwirkender | Die Rolle „Überwachungsmitwirkender“ erlaubt Ihnen, alle Überwachungsdaten zu lesen und Überwachungseinstellungen zu aktualisieren.|
| Überwachungsleser | Die Rolle „Überwachungsleser“ ermöglicht Ihnen, alle Überwachungsdaten zu lesen. |
| Benutzerzugriffsadministrator |Mit der Rolle „Benutzerzugriffsadministrator“ können Sie den Benutzerzugriff auf Azure Automation-Konten verwalten. |

## <a name="role-permissions"></a>Rollenberechtigungen

Die folgenden Tabellen beschreiben die spezifischen Berechtigungen der einzelnen Rollen. Dazu gehören „Aktionen“, die Berechtigungen erteilen, und „Keine Aktionen“, die sie einschränken.

### <a name="owner"></a>Besitzer

Ein Besitzer kann alles verwalten, einschließlich des Zugriffs. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|Aktionen|BESCHREIBUNG|
|---|---|
|Microsoft.Automation/automationAccounts/|Erstellen und Verwalten von Ressourcen aller Typen|

### <a name="contributor"></a>Mitwirkender

Ein Mitwirkender kann alles mit Ausnahme des Zugriffs verwalten. Die folgende Tabelle zeigt, welche Berechtigungen für die Rolle gewährt werden und welche nicht:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Erstellen und Verwalten von Ressourcen aller Typen|
|**Keine Aktionen**||
|Microsoft.Authorization/*/Delete| Löschen von Rollen und Rollenzuweisungen       |
|Microsoft.Authorization/*/Write     |  Erstellen von Rollen und Rollenzuweisungen       |
|Microsoft.Authorization/elevateAccess/Action    | Kein Zugriff auf die Option zum Erstellen eines Benutzerzugriffsadministrators       |

### <a name="reader"></a>Leser

Ein Leser kann alle Ressourcen in einem Automation-Konto anzeigen, aber keine Änderungen vornehmen.

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Anzeigen aller Ressourcen in ein Automation-Konto |

### <a name="automation-contributor"></a>Mitwirkender für Automatisierung

Ein Mitwirkender für Automatisierung kann alle Ressourcen im Automation-Konto mit Ausnahme des Zugriffs verwalten. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/*|Erstellen und Verwalten von Ressourcen aller Typen unter dem Automation-Konto|
|Microsoft.Authorization/*/read|Lesen von Rollen und Rollenzuweisungen|
|Microsoft.Resources/deployments/*|Erstellen und Verwalten von Ressourcengruppenbereitstellungen|
|Microsoft.Resources/subscriptions/resourceGroups/read|Lesen von Ressourcengruppenbereitstellungen|
|Microsoft.Support/*|Erstellen und Verwalten von Supporttickets|
|Microsoft.Insights/ActionGroups/*|Lesen/Schreiben/Löschen einer Aktionsgruppe|
|Microsoft.Insights/ActivityLogAlerts/*|Lesen/Schreiben/Löschen von Aktivitätsprotokollwarnungen|
|Microsoft.Insights/diagnosticSettings/*|Lesen/Schreiben/Löschen von Diagnoseeinstellungen.|
|Microsoft.Insights/MetricAlerts/*|Lesen/Schreiben/Löschen von Metrikwarnungen (nahezu in Echtzeit).|
|Microsoft.Insights/ScheduledQueryRules/*|Protokollwarnungen für Lesen, Schreiben und Löschen in Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Auflisten der Schlüssel für einen Log Analytics-Arbeitsbereich|

> [!NOTE]
> Die Rolle „Mitwirkender für Automatisierung“ kann verwendet werden, um mithilfe der verwalteten Identität, sofern entsprechende Berechtigungen für die Zielressource festgelegt sind, oder mithilfe eines ausführenden Kontos auf eine beliebige Ressource zuzugreifen. Ein ausführendes Automation-Konto ist standardmäßig mit den Rechten von Mitwirkenden für das Abonnement konfiguriert. Befolgen Sie das Prinzip der geringsten Berechtigung, und weisen Sie sorgfältig nur die Berechtigungen zu, die für die Ausführung Ihres Runbooks erforderlich sind. Beispiel: Wenn das Automatisierungskonto nur zum Starten oder Stoppen einer Azure-VM erforderlich ist, dann müssen die dem ausführenden Konto oder der verwalteten Identität zugewiesenen Berechtigungen nur zum Starten oder Beenden der VM dienen. Weisen Sie ebenso Lesezugriffsberechtigungen zu, wenn ein Runbook aus Blobspeicher liest.
> 
> Beim Zuweisen von Berechtigungen wird empfohlen, die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC) von Azure zu verwenden, die einer verwalteten Identität zugewiesen ist. Sehen Sie sich unsere Empfehlungen für den [besten Ansatz](../active-directory/managed-identities-azure-resources/managed-identity-best-practice-recommendations.md) zur Verwendung einer system- oder benutzerseitig zugewiesenen verwalteten Identität an, einschließlich Verwaltung und Governance während der Lebensdauer.

### <a name="automation-operator"></a>Operator für Automation

Ein Operator für Automation kann Aufträge erstellen und verwalten sowie Runbooknamen und -Eigenschaften für alle Runbooks in einem Automation-Konto lesen.

>[!NOTE]
>Wenn Sie den Operatorzugriff auf einzelne Runbooks steuern möchten, legen Sie diese Rolle nicht fest. Verwenden Sie stattdessen kombiniert die Rollen **Automation-Auftragsoperator** und **Automation-Runbookoperator**.

Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Authorization/*/read|Lesen von Autorisierungen|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Lesen von Hybrid Runbook Worker-Ressourcen|
|Microsoft.Automation/automationAccounts/jobs/read|Auflisten von Aufträgen des Runbooks|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Fortsetzen eines angehaltenen Auftrags|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Abbrechen eines Auftrags in Bearbeitung|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lesen der Auftragsdatenströme und -ausgabe|
|Microsoft.Automation/automationAccounts/jobs/output/read|Abrufen der Ausgabe eines Auftrags|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Anhalten eines Auftrags in Bearbeitung|
|Microsoft.Automation/automationAccounts/jobs/write|Erstellen von Aufträgen|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Abrufen eines Azure Automation-Auftragszeitplans|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Erstellen eines Azure Automation-Auftragszeitplans|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Abrufen des Arbeitsbereichs, der mit dem Automation-Konto verknüpft ist.|
|Microsoft.Automation/automationAccounts/read|Abrufen eines Azure Automation-Kontos|
|Microsoft.Automation/automationAccounts/runbooks/read|Abrufen eines Azure Automation-Runbooks|
|Microsoft.Automation/automationAccounts/schedules/read|Abrufen eines Azure Automation-Zeitplanassets|
|Microsoft.Automation/automationAccounts/schedules/write|Erstellen oder Aktualisieren eines Azure Automation-Zeitplanassets|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Lesen von Rollen und Rollenzuweisungen         |
|Microsoft.Resources/deployments/*      |Erstellen und Verwalten von Ressourcengruppenbereitstellungen         |
|Microsoft.Insights/alertRules/*      | Erstellen und Verwalten von Warnungsregeln        |
|Microsoft.Support/* |Erstellen und Verwalten von Supporttickets|

### <a name="automation-job-operator"></a>Automation-Auftragsoperator

Die Rolle „Automation-Auftragsoperator“ wird im Bereich des Automation-Kontos vergeben. So können mit Operatorberechtigungen Aufträge für alle Runbooks in dem Konto erstellt und verwaltet werden. Wenn der Rolle „Auftragsoperator“ Leseberechtigungen für die Ressourcengruppe mit dem Automation-Konto erteilt werden, können Mitglieder der Rolle Runbooks starten. Sie haben jedoch nicht die Möglichkeit, sie zu erstellen, zu bearbeiten oder zu löschen.

Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Authorization/*/read|Lesen von Autorisierungen|
|Microsoft.Automation/automationAccounts/jobs/read|Auflisten von Aufträgen des Runbooks|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Fortsetzen eines angehaltenen Auftrags|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Abbrechen eines Auftrags in Bearbeitung|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Lesen der Auftragsdatenströme und -ausgabe|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Anhalten eines Auftrags in Bearbeitung|
|Microsoft.Automation/automationAccounts/jobs/write|Erstellen von Aufträgen|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Lesen von Rollen und Rollenzuweisungen       |
|Microsoft.Resources/deployments/*      |Erstellen und Verwalten von Ressourcengruppenbereitstellungen         |
|Microsoft.Insights/alertRules/*      | Erstellen und Verwalten von Warnungsregeln        |
|Microsoft.Support/* |Erstellen und Verwalten von Supporttickets|

### <a name="automation-runbook-operator"></a>Automation-Runbookoperator

Die Rolle „Automation-Runbookoperator“ wird im Runbookbereich vergeben. Ein Automation-Runbookoperator kann den Namen und die Eigenschaften eines Runbooks anzeigen. Zusammen mit der Rolle **Automation-Auftragsoperator** ermöglicht diese Rolle dem Operator zudem das Erstellen und Verwalten von Aufträgen für das Runbook. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Auflisten der Runbooks        |
|Microsoft.Authorization/*/read      | Lesen von Autorisierungen        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Lesen von Rollen und Rollenzuweisungen         |
|Microsoft.Resources/deployments/*      | Erstellen und Verwalten von Ressourcengruppenbereitstellungen         |
|Microsoft.Insights/alertRules/*      | Erstellen und Verwalten von Warnungsregeln        |
|Microsoft.Support/*      | Erstellen und Verwalten von Supporttickets        |

### <a name="log-analytics-contributor"></a>Log Analytics-Mitwirkender

Ein Log Analytics-Mitwirkender kann alle Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Das Bearbeiten von Überwachungseinstellungen schließt folgende Aufgaben ein: Hinzufügen der VM-Erweiterung zu VMs, Lesen von Speicherkontoschlüsseln zum Konfigurieren von Protokollsammlungen aus Azure Storage, Erstellen und Konfigurieren von Automation-Konten, Hinzufügen von Features und Konfigurieren der Azure-Diagnose für alle Azure-Ressourcen. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|*/Lesen|Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel|
|Microsoft.Automation/automationAccounts/*|Verwalten von Automation-Konten|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Erstellen und Verwalten von VM-Erweiterungen|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Auflisten von klassischen Speicherkontoschlüsseln|
|Microsoft.Compute/virtualMachines/extensions/*|Erstellen und Verwalten von klassischen VM-Erweiterungen|
|Microsoft.Insights/alertRules/*|Lesen/Schreiben/Löschen von Warnungsregeln.|
|Microsoft.Insights/diagnosticSettings/*|Lesen/Schreiben/Löschen von Diagnoseeinstellungen.|
|Microsoft.OperationalInsights/*|Verwalten von Azure Monitor-Protokollen.|
|Microsoft.OperationsManagement/*|Verwalten von Azure Automation-Features in Arbeitsbereichen|
|Microsoft.Resources/deployments/*|Erstellen und Verwalten von Ressourcengruppenbereitstellungen|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Erstellen und Verwalten von Ressourcengruppenbereitstellungen|
|Microsoft.Storage/storageAccounts/listKeys/action|Auflisten von Speicherkontoschlüsseln|
|Microsoft.Support/*|Erstellen und Verwalten von Supporttickets|

### <a name="log-analytics-reader"></a>Log Analytics-Leser

Ein Log Analytics-Leser kann alle Überwachungsdaten anzeigen und durchsuchen sowie Überwachungseinstellungen anzeigen. Hierzu zählt auch die Anzeige der Konfiguration von Azure-Diagnosen für alle Azure-Ressourcen. Die folgende Tabelle zeigt, welche Berechtigungen für die Rolle gewährt werden und welche nicht:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|*/Lesen|Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Verwalten von Abfragen in Azure Monitor-Protokollen.|
|Microsoft.OperationalInsights/workspaces/search/action|Durchsuchen von Azure Monitor-Protokolldaten.|
|Microsoft.Support/*|Erstellen und Verwalten von Supporttickets|
|**Keine Aktionen**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Kein Lesezugriff auf freigegebene Zugriffsschlüssel|

### <a name="monitoring-contributor"></a>Überwachungsmitwirkender

Ein Überwachungsmitwirkender kann alle Überwachungsdaten lesen und Überwachungseinstellungen aktualisieren. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|*/Lesen|Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel|
|Microsoft.AlertsManagement/alerts/*|Verwalten von Warnungen|
|Microsoft.AlertsManagement/alertsSummary/*|Verwalten des Warnungsdashboards|
|Microsoft.Insights/AlertRules/*|Verwalten von Warnungsregeln|
|Microsoft.Insights/components/*|Verwalten von Application Insights-Komponenten|
|Microsoft.Insights/DiagnosticSettings/*|Verwalten von Diagnoseeinstellungen|
|Microsoft.Insights/eventtypes/*|Auflisten von Aktivitätsprotokollereignissen (Verwaltungsereignissen) in einem Abonnement. Diese Berechtigung gilt sowohl für den programmgesteuerten als auch für den Portalzugriff auf das Aktivitätsprotokoll.|
|Microsoft.Insights/LogDefinitions/*|Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf Aktivitätsprotokolle zugreifen müssen. Auflisten der Protokollkategorien im Aktivitätsprotokoll.|
|Microsoft.Insights/MetricDefinitions/*|Lesen von Metrikdefinitionen (Liste der verfügbaren Metriktypen für eine Ressource).|
|Microsoft.Insights/Metrics/*|Lesen von Metriken für eine Ressource.|
|Microsoft.Insights/Register/Action|Registriert den Microsoft.Insights-Anbieter.|
|Microsoft.Insights/webtests/*|Verwalten von Application Insights-Webtests|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Verwalten von Azure Monitor-Protokolllösungspaketen.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Verwalten von Azure Monitor-Protokollen: gespeicherte Suchen.|
|Microsoft.OperationalInsights/workspaces/search/action|Durchsuchen von Log Analytics-Arbeitsbereichen.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Auflisten der Schlüssel für einen Log Analytics-Arbeitsbereich.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Verwalten von Azure Monitor-Protokollen: Speichererkenntniskonfigurationen.|
|Microsoft.Support/*|Erstellen und Verwalten von Supporttickets|
|Microsoft.WorkloadMonitor/workloads/*|Verwalten von Workloads|

### <a name="monitoring-reader"></a>Überwachungsleser

Ein Überwachungsleser kann alle Überwachungsdaten lesen. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|*/Lesen|Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel|
|Microsoft.OperationalInsights/workspaces/search/action|Durchsuchen von Log Analytics-Arbeitsbereichen.|
|Microsoft.Support/*|Erstellen und Verwalten von Support-Tickets|

### <a name="user-access-administrator"></a>Benutzerzugriffsadministrator

Ein Benutzerzugriffsadministrator kann den Benutzerzugriff auf Azure-Ressourcen verwalten. Die folgende Tabelle zeigt die Berechtigungen für die Rolle:

|**Aktionen**  |**Beschreibung**  |
|---------|---------|
|*/Lesen|Lesen aller Ressourcen|
|Microsoft.Authorization/*|Verwalten der Autorisierung|
|Microsoft.Support/*|Erstellen und Verwalten von Support-Tickets|

## <a name="feature-setup-permissions"></a>Berechtigungen für die Featureeinrichtung

In den folgenden Abschnitten werden die minimal erforderlichen Berechtigungen beschrieben, die für das Aktivieren der Features Updateverwaltung und Änderungsnachverfolgung und Bestand benötigt werden.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Berechtigungen für das Aktivieren von Updateverwaltung und Änderungsnachverfolgung und Bestand für einen virtuellen Computer

|**Aktion**  |**Berechtigung**  |**Mindestumfang**  |
|---------|---------|---------|
|Neue Bereitstellung schreiben      | Microsoft.Resources/deployments/*          |Subscription          |
|Neue Ressourcengruppe schreiben      | Microsoft.Resources/subscriptions/resourceGroups/write        | Subscription          |
|Neuen Standardarbeitsbereich erstellen      | Microsoft.OperationalInsights/workspaces/write         | Resource group         |
|Neues Konto erstellen      |  Microsoft.Automation/automationAccounts/write        |Resource group         |
|Arbeitsbereich mit Konto verknüpfen      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Arbeitsbereich</br>Automation-Konto
|MMA-Erweiterung erstellen      | Microsoft.Compute/virtualMachines/write         | Virtual Machine         |
|Gespeicherten Suchvorgang erstellen      | Microsoft.OperationalInsights/workspaces/write          | Arbeitsbereich         |
|Bereichskonfiguration erstellen      | Microsoft.OperationalInsights/workspaces/write          | Arbeitsbereich         |
|Statusüberprüfung des Onboardings – Arbeitsbereich lesen      | Microsoft.OperationalInsights/workspaces/read         | Arbeitsbereich         |
|Statusüberprüfung des Onboardings – Eigenschaft des verknüpften Arbeitsbereichs von Konto lesen     | Microsoft.Automation/automationAccounts/read      | Automation-Konto        |
|Statusüberprüfung des Onboardings – Lösung lesen      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Lösung         |
|Statusüberprüfung des Onboardings – VM lesen      | Microsoft.Compute/virtualMachines/read         | Virtual Machine         |
|Statusüberprüfung des Onboardings – Konto lesen      | Microsoft.Automation/automationAccounts/read  |  Automation-Konto   |
| Arbeitsbereichsüberprüfung des Onboardings für die VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Subscription         |
| Registrieren des Log Analytics-Anbieters |Microsoft.Insights/register/action | Subscription|

<sup>1</sup> Diese Berechtigung ist für das Aktivieren der Features über die Benutzeroberfläche des VM-Portals erforderlich.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Berechtigungen für das Aktivieren von Updateverwaltung und Änderungsnachverfolgung und Bestand über ein Automation-Konto

|**Aktion**  |**Berechtigung** |**Mindestumfang**  |
|---------|---------|---------|
|Neue Bereitstellung erstellen     | Microsoft.Resources/deployments/*        | Subscription         |
|Erstellen einer neuen Ressourcengruppe     | Microsoft.Resources/subscriptions/resourceGroups/write         | Subscription        |
|AutomationOnboarding-Blatt – neuen Arbeitsbereich erstellen     |Microsoft.OperationalInsights/workspaces/write           | Resource group        |
|AutomationOnboarding-Blatt – verknüpften Arbeitsbereich lesen     | Microsoft.Automation/automationAccounts/read        | Automation-Konto       |
|AutomationOnboarding-Blatt – Lösung lesen     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Lösung        |
|AutomationOnboarding-Blatt – Arbeitsbereich lesen     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Arbeitsbereich        |
|Link für Arbeitsbereich und Konto erstellen     | Microsoft.OperationalInsights/workspaces/write        | Arbeitsbereich        |
|Konto für Shoebox schreiben      | Microsoft.Automation/automationAccounts/write        | Konto        |
|Gespeicherten Suchvorgang erstellen/bearbeiten     | Microsoft.OperationalInsights/workspaces/write        | Arbeitsbereich        |
|Bereichskonfiguration erstellen/bearbeiten     | Microsoft.OperationalInsights/workspaces/write        | Arbeitsbereich        |
| Registrieren des Log Analytics-Anbieters |Microsoft.Insights/register/action | Subscription|
|**Schritt 2: Aktivieren mehrerer virtueller Computer**     |         |         |
|VMOnboarding-Blatt – MMA-Erweiterung erstellen     | Microsoft.Compute/virtualMachines/write           | Virtual Machine        |
|Gespeicherten Suchvorgang erstellen/bearbeiten     | Microsoft.OperationalInsights/workspaces/write           | Arbeitsbereich        |
|Bereichskonfiguration erstellen/bearbeiten  | Microsoft.OperationalInsights/workspaces/write   | Arbeitsbereich|

## <a name="custom-azure-automation-contributor-role"></a>Benutzerdefinierte Rolle „Azure Automation-Mitwirkender“

Microsoft möchte die Rechte des Automation-Kontos aus der Rolle „Log Analytics-Mitwirkender“ entfernen. Derzeit kann die oben beschriebene integrierte Rolle [Log Analytics-Mitwirkender](#log-analytics-contributor) Berechtigungen an die Rolle [Mitwirkender](./../role-based-access-control/built-in-roles.md#contributor) des Abonnements eskalieren. Da für die ausführenden Konten des Automation-Kontos bereits die Rechte der Rolle „Mitwirkender“ für das Abonnement konfiguriert sind, kann es von einem Angreifer verwendet werden, um neue Runbooks zu erstellen und Code als Mitwirkender für das Abonnement auszuführen.

Aufgrund dieses Sicherheitsrisikos wird empfohlen, die Rolle „Log Analytics-Mitwirkender“ nicht zum Ausführen von Automation-Aufträgen zu verwenden. Erstellen Sie stattdessen die benutzerdefinierte Rolle „Azure Automation-Mitwirkender“, und verwenden Sie diese für Aktionen im Zusammenhang mit dem Automation-Konto. Führen Sie die folgenden Schritte aus, um diese benutzerdefinierte Rolle zu erstellen:

### <a name="create-using-the-azure-portal"></a>Erstellen mithilfe des Azure-Portals

Führen Sie die folgenden Schritte aus, um die benutzerdefinierte Azure Automation-Rolle im Azure-Portal zu erstellen. Weitere Informationen finden Sie unter [Benutzerdefinierte Azure-Rollen](./../role-based-access-control/custom-roles.md).

1. Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in eine Datei ein: Speichern Sie die Datei auf Ihrem lokalen Computer oder in einem Azure-Speicherkonto. Ersetzen Sie in der JSON-Datei den Wert für die **assignableScopes**-Eigenschaft durch die Abonnement-GUID.

   ```json
   {
    "properties": {
        "roleName": "Automation Account Contributor (Custom)",
        "description": "Allows access to manage Azure Automation and its resources",
        "assignableScopes": [
            "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Authorization/*/read",
                    "Microsoft.Insights/alertRules/*",
                    "Microsoft.Insights/metrics/read",
                    "Microsoft.Insights/diagnosticSettings/*",
                    "Microsoft.Resources/deployments/*",
                    "Microsoft.Resources/subscriptions/resourceGroups/read",
                    "Microsoft.Automation/automationAccounts/*",
                    "Microsoft.Support/*"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
      }
   }
   ```

1. Führen Sie die restlichen Schritte wie unter [Erstellen oder Aktualisieren von benutzerdefinierten Azure-Rollen über das Azure-Portal](../role-based-access-control/custom-roles-portal.md#start-from-json) beschrieben aus. Beachten Sie Folgendes für [Schritt 3:Grundlagen](../role-based-access-control/custom-roles-portal.md#step-3-basics):

    -  Geben Sie im Feld **Name der benutzerdefinierten Rolle** den Namen **Mitwirkender für Automation-Konto (benutzerdefiniert)** oder einen Namen ein, der Ihren Benennungsstandards entspricht.
    - Wählen Sie bei **Baselineberechtigungen** die Option **Von JSON aus starten** aus. Wählen Sie dann die benutzerdefinierte JSON-Datei aus, die Sie zuvor gespeichert haben.

1. Führen Sie die verbleibenden Schritte aus, und überprüfen und erstellen Sie dann die benutzerdefinierte Rolle. Es kann einige Minuten dauern, bis Ihre benutzerdefinierte Rolle überall angezeigt wird.

### <a name="create-using-powershell"></a>Erstellen mithilfe von PowerShell

Führen Sie die folgenden Schritte aus, um die benutzerdefinierte Azure Automation-Rolle mit PowerShell zu erstellen. Weitere Informationen finden Sie unter [Benutzerdefinierte Azure-Rollen](./../role-based-access-control/custom-roles.md).

1. Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in eine Datei ein: Speichern Sie die Datei auf Ihrem lokalen Computer oder in einem Azure-Speicherkonto. Ersetzen Sie in der JSON-Datei den Wert für die **AssignableScopes**-Eigenschaft durch die Abonnement-GUID.

   ```json
   { 
       "Name": "Automation account Contributor (custom)",
       "Id": "",
       "IsCustom": true,
       "Description": "Allows access to manage Azure Automation and its resources",
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Insights/alertRules/*",
           "Microsoft.Insights/metrics/read",
           "Microsoft.Insights/diagnosticSettings/*",
           "Microsoft.Resources/deployments/*",
           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Automation/automationAccounts/*",
           "Microsoft.Support/*"
       ],
       "NotActions": [],
       "AssignableScopes": [
           "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX"
       ] 
   } 
   ```

1. Führen Sie die restlichen Schritte wie unter [Erstellen oder Aktualisieren von benutzerdefinierten Azure-Rollen mithilfe von Azure PowerShell](./../role-based-access-control/custom-roles-powershell.md#create-a-custom-role-with-json-template) beschrieben aus. Es kann einige Minuten dauern, bis Ihre benutzerdefinierte Rolle überall angezeigt wird.

## <a name="update-management-permissions"></a>Berechtigungen für die Updateverwaltung

Die Updateverwaltung kann verwendet werden, um Updatebereitstellungen auf Computern in mehreren Abonnements im gleichen Azure Active Directory-Mandanten (Azure AD) oder mandantenübergreifend mithilfe von Azure Lighthouse zu planen. In der folgenden Tabelle sind die zum Verwalten von Bereitstellungen der Updateverwaltung benötigten Berechtigungen aufgelistet.

|**Ressource** |**Rolle** |**Umfang** |
|---------|---------|---------|
|Automation-Konto |[Benutzerdefinierte Rolle „Azure Automation-Mitwirkender“](#custom-azure-automation-contributor-role) |Automation-Konto |
|Automation-Konto |Mitwirkender von virtuellen Computern  |Ressourcengruppe für das Konto  |
|Log Analytics-Mitwirkender an Log Analytics-Arbeitsbereich|Log Analytics-Arbeitsbereich |
|Log Analytics-Arbeitsbereich |Log Analytics-Leser|Subscription|
|Lösung |Log Analytics-Mitwirkender |Lösung|
|Virtual Machine |Mitwirkender von virtuellen Computern |Virtual Machine |
|**Aktionen auf virtuellem Computer** | | |
|Anzeigen des Verlaufs der Ausführung des Updatezeitplans ([Ausführungen des Computers für die Softwareupdatekonfiguration](/rest/api/automation/softwareupdateconfigurationmachineruns)) |Leser |Automation-Konto |
|**Aktionen auf dem virtuellen Computer** |**Berechtigung** | |
|Erstellen eines Updatezeitplans [(Softwareupdatekonfigurationen](/rest/api/automation/softwareupdateconfigurations)) |Microsoft.Compute/virtualMachines/write |Für statische VM-Listen und Ressourcengruppen |
|Erstellen eines Updatezeitplans [(Softwareupdatekonfigurationen](/rest/api/automation/softwareupdateconfigurations)) |Microsoft.OperationalInsights/workspaces/analytics/query/action |Für die Arbeitsbereichsressourcen-ID bei Verwendung einer dynamischen Liste, die nicht aus Azure stammt.|

## <a name="configure-azure-rbac-for-your-automation-account"></a>Konfigurieren der rollenbasierten Zugriffssteuerung von Azure für Automation-Konten

Der folgende Abschnitt veranschaulicht das Konfigurieren der rollenbasierten Zugriffssteuerung von Azure für Automation-Konten über das [Azure-Portal](#configure-azure-rbac-using-the-azure-portal) und [PowerShell](#configure-azure-rbac-using-powershell).

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Konfigurieren der rollenbasierten Zugriffssteuerung von Azure über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und öffnen Sie auf der Seite „Automation-Konten“ Ihr Automation-Konto.
2. Klicken Sie auf **Zugriffssteuerung (IAM)** , um die Seite „Zugriffssteuerung (IAM)“ zu öffnen. Auf dieser Seite können Sie neue Benutzer, Gruppen und Anwendungen der Verwaltung Ihres Automation-Kontos hinzufügen und vorhandene Rollen anzeigen, die für das Automation-Konto konfiguriert werden können.
3. Klicken Sie auf die Registerkarte **Rollenzuweisungen**.

   ![Zugriffsschaltfläche](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Einen neuen Benutzer hinzufügen und eine Rolle zuweisen

1. Klicken Sie auf der Seite „Zugriffssteuerung (IAM)“ auf **+ Rollenzuweisung hinzufügen**. Mit dieser Aktion wird die Seite „Rollenzuweisung hinzufügen“ geöffnet, auf der Sie einen Benutzer, eine Gruppe oder eine Anwendung hinzufügen und eine entsprechende Rolle zuweisen können.

2. Wählen Sie eine Rolle aus der Liste der verfügbaren Rollen aus. Sie können aber jede der verfügbaren integrierten Rollen, die von Automation-Konten unterstützt werden, oder auch eine eigene, benutzerdefinierte Rolle auswählen.

3. Geben Sie im Feld **Auswählen** den Namen des Benutzers ein, dem Sie Berechtigungen erteilen möchten. Wählen Sie den Benutzer in der Liste aus, und klicken Sie auf **Speichern**.

   ![Hinzufügen von Benutzern](media/automation-role-based-access-control/automation-04-add-users.png)

   Der Benutzer sollte nun auf der Seite „Benutzer“ mit der zugewiesenen Rolle, die Sie ausgewählt haben, angezeigt werden.

   ![Benutzer auflisten](media/automation-role-based-access-control/automation-05-list-users.png)

   Sie können dem Benutzer die Rolle über die Seite „Rollen“ zuweisen.

4. Klicken Sie auf der Seite „Zugriffssteuerung (AIM)“ auf **Rollen**, um die Seite „Rollen“ zu öffnen. Auf dieser Seite können Sie den Namen der Rolle sowie die Anzahl von Benutzern und Gruppen anzeigen, denen diese Rolle zugewiesen wurde.

    ![Rolle über die Seite „Benutzer“ zuweisen](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Sie können die rollenbasierte Zugriffssteuerung nur im Automation-Kontobereich einrichten und nicht bei einer beliebigen Ressource unter dem Automation-Konto.

#### <a name="remove-a-user"></a>Benutzer entfernen

Sie können die Zugriffsberechtigung eines Benutzers, der selbst nicht das Automation-Konto verwaltet oder nicht mehr für die Organisation arbeitet, entfernen. Folgende Schritte müssen Sie durchführen, um einen Benutzer zu entfernen:

1. Wählen Sie auf der Seite „Zugriffssteuerung (AIM)“ den Benutzer aus, den Sie entfernen möchten, und klicken Sie dann auf **Entfernen**.
2. Klicken Sie auf der Seite mit den Zuweisungsdetails auf die Schaltfläche **Entfernen**.
3. Klicken Sie auf **Ja** , um die Entfernung zu bestätigen.

   ![Benutzer entfernen](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>Konfigurieren der rollenbasierten Zugriffssteuerung von Azure über PowerShell

Sie können den rollenbasierten Zugriff für ein Automation-Konto auch mit den folgenden [Azure PowerShell-Cmdlets](../role-based-access-control/role-assignments-powershell.md) konfigurieren:

[Get-AzRoleDefinition](/powershell/module/Az.Resources/Get-AzRoleDefinition) listet alle in Azure Active Directory verfügbaren Azure-Rollen auf. Sie können dieses Cmdlet mit dem Parameter `Name` verwenden, um alle Aktionen aufzulisten, die eine bestimmte Rolle ausführen kann.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

Im Folgenden sehen Sie die Beispielausgabe:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) listet die Azure-Rollenzuweisungen für den angegebenen Bereich auf. Ohne Parameter gibt dieses Cmdlet alle Rollenzuweisungen zurück, die in diesem Abonnement erstellt wurden. Verwenden Sie den `ExpandPrincipalGroups`-Parameter, um die Zugriffszuweisungen für den festgelegten Benutzer sowie die Gruppen, denen der Benutzer angehört, aufzulisten.

**Beispiel:** Verwenden Sie das folgende Cmdlet, um alle Benutzer innerhalb eines Automation-Kontos mit ihren Rollen aufzulisten.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Im Folgenden sehen Sie die Beispielausgabe:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

Verwenden Sie [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment), um Benutzern, Gruppen und Anwendungen die Zugriffsberechtigung für einen bestimmten Bereich zuzuweisen.

**Beispiel:** Verwenden Sie den folgenden Befehl, um einem Benutzer die Rolle „Operator für Automation“ im Geltungsbereich des Automation-Kontos zuzuweisen.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Im Folgenden sehen Sie die Beispielausgabe:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Verwenden Sie [Remove-AzRoleAssignment](/powershell/module/Az.Resources/Remove-AzRoleAssignment), um den Zugriff eines angegebenen Benutzers, einer Gruppe oder einer Anwendung für einen bestimmten Bereich zu entfernen.

**Beispiel:** Verwenden Sie den folgenden Befehl, um den Benutzer aus der Rolle „Operator für Automation“ im Geltungsbereich des Automation-Kontos zu entfernen.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Ersetzen Sie im vorherigen Beispiel `sign-in ID of a user you wish to remove`, `SubscriptionID`, `Resource Group Name` und `Automation account name` durch Ihre Kontodetails. Wählen Sie **Ja**, wenn Sie zum Bestätigen aufgefordert werden, bevor Sie mit dem Entfernen von Benutzerrollenzuweisungen fortfahren.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Berechtigungen der Rolle „Operator für Automation“ – Automation-Konto

Wenn ein Benutzer, dem im Automation-Kontobereich die Rolle „Operator für Automation“ zugewiesen wurde, das ihm zugewiesene Automation-Konto aufruft, kann er nur die Liste mit den Runbooks, Runbookaufträgen und Zeitplänen sehen, die im Automation-Konto erstellt wurden. Dieser Benutzer kann die Definitionen dieser Elemente nicht anzeigen. Der Benutzer kann den Runbookauftrag starten, beenden, unterbrechen, fortsetzen oder planen. Der Benutzer hat jedoch keinen Zugriff auf andere Automation-Ressourcen wie Konfigurationen, Hybrid Runbook Worker-Gruppen oder DSC-Knoten.

![Kein Zugriff auf Ressourcen](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Konfigurieren der rollenbasierten Zugriffssteuerung von Azure für Runbooks

Mit Azure Automation können Sie bestimmten Runbooks Azure-Rollen zuweisen. Führen Sie das folgende Skript aus, um einen Benutzer einem bestimmten Runbook hinzuzufügen. Das Skript kann von einem Automation-Kontoadministrator oder einem Mandantenadministrator ausgeführt werden.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Nach Ausführung des Skripts muss der Benutzer sich im Azure-Portal anmelden und **Alle Ressourcen** auswählen. In der Liste kann der Benutzer das Runbook sehen, für das er als Automation-Runbookoperator hinzugefügt wurde.

![Rollenbasierte Zugriffssteuerung von Azure für Runbooks über das Portal](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Berechtigungen der Rolle „Operator für Automation“ – Runbook

Wenn ein Benutzer, dem im Runbookbereich die Rolle „Operator für Automation“ zugewiesen wurde, ein ihm zugewiesenes Runbook aufruft, kann er nur das Runbook starten und die Runbookaufträge ansehen.

![Nur Startzugriff](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Azure RBAC mithilfe von PowerShell finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe von Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Ausführliche Informationen zu Runbooktypen finden Sie unter [Azure Automation-Runbooktypen](automation-runbook-types.md).
* Informationen zum Starten eines Runbooks finden Sie unter [Starten eines Runbooks in Azure Automation](start-runbooks.md).
