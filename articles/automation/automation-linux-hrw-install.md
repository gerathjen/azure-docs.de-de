---
title: Bereitstellen eines Agent-basierten Linux-Hybrid Runbook Workers in Automation
description: In diesem Artikel erfahren Sie, wie Sie einen Agent-basierten Hybrid Runbook Worker bereitstellen, mit dem Sie Runbooks auf Linux-Computern in Ihrem lokalen Rechenzentrum oder einer Cloudumgebung ausführen können.
services: automation
ms.subservice: process-automation
ms.date: 09/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9eff8a0605f3cebce7f85f9c6edb2a3009ec4901
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240393"
---
# <a name="deploy-an-agent-based-linux-hybrid-runbook-worker-in-automation"></a>Bereitstellen eines Agent-basierten Linux-Hybrid Runbook Workers in Automation

Sie können die Benutzerfunktion Hybrid Runbook Worker von Azure Automation verwenden, um Runbooks direkt auf dem Azure- oder Nicht-Azure-Computer auszuführen, einschließlich der Server, die bei [Servern mit Azure Arc-Aktivierung](../azure-arc/servers/overview.md) registriert sind. Von dem Computer oder Server aus, der die Rolle hostet, können Sie Runbooks direkt auf dem Computer ausführen, und mit Ressourcen in der Umgebung, um diese lokalen Ressourcen zu verwalten.

Linux Hybrid Runbook Worker führt Runbooks als spezieller Benutzer mit erhöhten Rechten aus, um Befehle ausführen zu können, für die erhöhte Rechte erforderlich sind. Azure Automation speichert und verwaltet Runbooks und übermittelt sie dann an dafür ausgewählte Computer. In diesem Artikel wird beschrieben, wie Sie den Hybrid Runbook Worker auf einem Linux-Computer installieren und wie Sie den Worker oder eine Hybrid Runbook Worker-Gruppe entfernen. Informationen zu benutzerbasierten Hybrid Runbook Workern finden Sie auch unter [Bereitstellen von erweiterungsbasierten Windows- oder Linux-Hybrid Runbook Workern in Automation](./extension-based-hybrid-runbook-worker-install.md).

Lesen Sie nach dem erfolgreichen Bereitstellen eines Runbook Workers [Running runbooks on a Hybrid Runbook Worker (Ausführen von Runbooks auf einem Hybrid Runbook Worker)](automation-hrw-run-runbooks.md), um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Datencenter oder in einer anderen Cloudumgebung konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie über Folgendes verfügen.

### <a name="a-log-analytics-workspace"></a>Einen Log Analytics-Arbeitsbereich.

Die „Hybrid Runbook Worker“-Rolle ist hinsichtlich ihrer Installation und Konfiguration von einem Azure Monitor Log Analytics-Arbeitsbereich abhängig. Sie können ihn über den [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) erstellen, mithilfe der [PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md?toc=%2fpowershell%2fmodule%2ftoc.json) oder im [Azure-Portal](../azure-monitor/logs/quick-create-workspace.md).

Wenn Sie über keinen Azure Monitor Log Analytics-Arbeitsbereich verfügen, lesen Sie zuerst die [Entwurfsanleitung für Azure Monitor-Protokolle](../azure-monitor/logs/design-logs-deployment.md), bevor Sie einen Arbeitsbereich erstellen.

### <a name="log-analytics-agent"></a>Log Analytics-Agent

Die „Hybrid Runbook Worker“-Rolle erfordert den [Log Analytics-Agent](../azure-monitor/agents/log-analytics-agent.md) für das unterstützte Linux-Betriebssystem. Für Server oder Computer, die außerhalb von Azure gehostet werden, können Sie den Log Analytics-Agent mit [Servern mit Azure Arc-Aktivierung](../azure-arc/servers/overview.md) installieren. Der Agent wird mit bestimmten Dienstkonten installiert, die Befehle ausführen, die root-Berechtigungen erfordern. Weitere Informationen finden Sie unter [Dienstkonten](./automation-hrw-run-runbooks.md#service-accounts).

### <a name="supported-linux-operating-systems"></a>Unterstützte Linux-Betriebssysteme

Die Hybrid Runbook Worker-Funktion unterstützt die folgenden Distributionen. Bei allen Betriebssystemen wird von einer x64-Architektur ausgegangen. x86 wird für kein Betriebssystem unterstützt.

* Amazon Linux 2012.09 bis 2015.09
* CentOS Linux 5, 6, 7 und 8
* Oracle Linux 6, 7 und 8
* Red Hat Enterprise Linux Server 5, 6, 7 und 8
* Debian GNU/Linux 6, 7 und 8
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS, 18.04 und 20.04 LTS
* SUSE Linux Enterprise Server 12, 15 und 15.1 (SUSE hat keine Versionen mit den Nummern 13 oder 14 veröffentlicht)

> [!IMPORTANT]
> Überprüfen Sie [hier](update-management/operating-system-requirements.md) die unterstützten Distributionen, bevor Sie das Updateverwaltungsfeature (abhängig von der Hybrid Runbook Worker-Systemrolle) aktivieren.

### <a name="minimum-requirements"></a>Mindestanforderungen

Die Mindestanforderungen für einen System- und Benutzer-Hybrid Runbook Worker unter Linux sind:

* Zwei Kerne
* 4 GB RAM
* Port 443 (ausgehend)

| **Erforderliches Paket** | **Beschreibung** | **Mindestversion**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-Bibliothek| 2.5-12 |
|Openssl| OpenSSL-Bibliotheken | 1.0 (TLS 1.1 und TLS 1.2 werden unterstützt)|
|Curl | cURL-Webclient | 7.15.5|
|Python-ctypes | Fremdfunktionsbibliothek für Python| Python 2.x oder Python 3.x erforderlich |
|PAM | Module für austauschbare Authentifizierung|

| **Optionale Pakete** | **Beschreibung** | **Mindestversion**|
|--------------------- | --------------------- | -------------------|
| PowerShell Core | Um PowerShell-Runbooks auszuführen, muss PowerShell Core installiert sein. Unter [Installieren von PowerShell Core unter Linux](/powershell/scripting/install/installing-powershell-core-on-linux) erfahren Sie, wie Sie PowerShell Core installieren. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Hinzufügen eines Computers zu einer Hybrid Runbook Worker-Gruppe

Sie können den Workercomputer einer Hybrid Runbook Worker-Gruppe einem Ihrer Automation-Konten hinzufügen. Computer, auf denen der von der Updateverwaltung verwaltete System-Hybrid Runbook Worker gehostet wird, können einer Hybrid Runbook Worker-Gruppe hinzugefügt werden. Sie müssen aber für die Updateverwaltung und die Mitgliedschaft in der Hybrid Runbook Worker-Gruppe dasselbe Automation-Konto verwenden.

> [!NOTE]
> Die [Updateverwaltung](./update-management/overview.md) von Azure Automation installiert automatisch den System-Hybrid Runbook Worker auf einem Azure- oder Nicht-Azure-Computer, der für die Updateverwaltung aktiviert ist. Es erfolgt jedoch keine Registrierung des Workers für Hybrid Runbook Worker-Gruppen in Ihrem Automation-Konto. Zum Ausführen der Runbooks auf diesen Computern müssen Sie sie einer Hybrid Runbook Worker-Gruppe hinzufügen. Führen Sie Schritt 4 im Abschnitt [Installieren eines Linux-Hybrid Runbook Workers](#install-a-linux-hybrid-runbook-worker) aus, um ihn einer Gruppe hinzuzufügen.

## <a name="supported-linux-hardening"></a>Unterstützte Linux-Härtung

Folgendes wird noch nicht unterstützt:

* CIS

## <a name="supported-runbook-types"></a>Unterstützte Runbooktypen

Linux-Hybrid Runbook Worker unterstützen eine begrenzte Gruppe von Runbooktypen in Azure Automation, die in der folgenden Tabelle beschrieben werden.

|Runbooktyp | Unterstützt |
|-------------|-----------|
|Python 3 (Vorschau)|Ja, nur für diese Distributionen erforderlich: SUSE LES 15, RHEL 8 und CentOS 8|
|Python 2 |Ja, für jede Distribution, die nicht Python 3 erfordert<sup>1</sup> |
|PowerShell |Ja<sup>2</sup> |
|PowerShell-Workflow |Nein |
|Grafisch |Nein |
|Grafischer PowerShell-Workflow |Nein |

<sup>1</sup>Siehe [Unterstützte Linux-Betriebssysteme](#supported-linux-operating-systems)

<sup>2</sup>PowerShell-Runbooks erfordern, dass PowerShell Core auf dem Linux-Computer installiert wird. Unter [Installieren von PowerShell Core unter Linux](/powershell/scripting/install/installing-powershell-core-on-linux) erfahren Sie, wie Sie PowerShell Core installieren.

### <a name="network-configuration"></a>Netzwerkkonfiguration

Netzwerkanforderungen für den Hybrid Runbook Worker finden Sie unter [Konfigurieren des Netzwerks](automation-hybrid-runbook-worker.md#network-planning).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Installieren eines Linux Hybrid Runbook Workers

Für die automatische Bereitstellung eines Hybrid Runbook Worker stehen zwei Methoden zur Verfügung. Sie können ein Runbook aus dem Runbookkatalog im Azure-Portal importieren und ausführen, oder Sie können eine Reihe von PowerShell-Befehlen manuell ausführen.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Importieren eines Runbooks aus dem Runbookkatalog

Eine ausführliche Beschreibung des Importverfahrens finden Sie unter [Importieren von Runbooks aus GitHub über das Azure-Portal](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal). Der Name des zu importierenden Runbooks lautet **Create Automation Linux HybridWorker**.

Von dem Runbook werden folgende Parameter verwendet.

| Parameter | Status | BESCHREIBUNG |
| ------- | ----- | ----------- |
| `Location` | Obligatorisch. | Der Speicherort für den Log Analytics-Arbeitsbereich. |
| `ResourceGroupName` | Obligatorisch. | Die Ressourcengruppe für Ihr Automation-Konto. |
| `AccountName` | Obligatorisch. | Der Name des Automation-Kontos, bei dem der Hybrid Runbook Worker registriert wird. |
| `CreateLA` | Obligatorisch. | Bei „true“ wird der Wert von `WorkspaceName` verwendet, um einen Log Analytics-Arbeitsbereich zu erstellen. Bei „false“ muss der Wert von `WorkspaceName` auf einen vorhandenen Arbeitsbereich verweisen. |
| `LAlocation` | Optional | Der Speicherort, an dem der Log Analytics-Arbeitsbereich erstellt wird oder an dem er bereits vorhanden ist. |
| `WorkspaceName` | Optional | Der Name des zu erstellenden oder zu verwendenden Log Analytics-Arbeitsbereichs. |
| `CreateVM` | Obligatorisch. | Bei „true“ wird der Wert von `VMName` als Name eines neuen virtuellen Computers verwendet. Bei „false“ wird `VMName` verwendet, um einen vorhandenen virtuellen Computer zu suchen und zu registrieren. |
| `VMName` | Optional | Der Name des virtuellen Computers, der entweder erstellt oder registriert wird (je nach Wert von `CreateVM`). |
| `VMImage` | Optional | Der Name des zu erstellenden VM-Images. |
| `VMlocation` | Optional | Der Speicherort des virtuellen Computers, der erstellt oder registriert wird. Ohne Angabe dieses Orts wird der Wert von `LAlocation` verwendet. |
| `RegisterHW` | Obligatorisch. | Bei „true“ wird der virtuelle Computer als Hybrid Worker registriert. |
| `WorkerGroupName` | Obligatorisch. | Der Name der Hybrid Worker-Gruppe. |

### <a name="manually-run-powershell-commands"></a>Manuelles Ausführen von PowerShell-Befehlen

Zum Installieren und Konfigurieren eines Linux Hybrid Runbook Workers führen Sie die folgenden Schritte durch.

1. Aktivieren Sie die Azure Automation-Lösung in Ihrem Log Analytics-Arbeitsbereich, indem Sie den folgenden Befehl an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten oder in der Cloud Shell im [Azure-Portal](https://portal.azure.com) ausführen:

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Stellen Sie den Log Analytics-Agent auf dem Zielcomputer bereit.

    - Für Azure-VMs installieren Sie den Log Analytics-Agent für Linux mithilfe der [VM-Erweiterung für Linux](../virtual-machines/extensions/oms-linux.md). Die Erweiterung installiert den Log Analytics-Agent auf virtuellen Azure-Computern und registriert virtuelle Computer in einem vorhandenen Log Analytics-Arbeitsbereich. Sie können eine Azure Resource Manager-Vorlage, die Azure CLI oder Azure Policy verwenden, um die integrierte Richtliniendefinition [Log Analytics-Agent für *Linux*- oder *Windows*-VMs bereitstellen](../governance/policy/samples/built-in-policies.md#monitoring) zuzuweisen. Nachdem der Agent installiert wurde, kann der Computer einer Hybrid Runbook Worker-Gruppe in Ihrem Automation-Konto hinzugefügt werden.

    - Für Nicht-Azure-Computer können Sie den Log Analytics-Agent mit [Servern mit Azure Arc-Unterstützung](../azure-arc/servers/overview.md) installieren. Azure Arc-aktivierte Server unterstützen die Bereitstellung des Log Analytics-Agenten mit den folgenden Methoden:

      - Verwenden des VM-Erweiterungen-Frameworks.

        Mit dieser Funktion in Servern mit Azure Arc-Unterstützung können Sie die VM-Erweiterung für den Log Analytics-Agent auf einem Nicht-Azure-Windows- und/oder Linux-Server bereitstellen. VM-Erweiterungen können mit den folgenden Methoden auf Ihren hybriden Computern oder Servern verwaltet werden, die von Azure Arc-aktivierten Servern verwaltet werden:

        - Das [Azure-Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
        - Die [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
        - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
        - [Azure Resource Manager-Vorlagen](../azure-arc/servers/manage-vm-extensions-template.md)

      - Verwenden von Azure Policy.

        Bei dieser Vorgehensweise verwenden Sie die integrierte Azure Policy-Richtliniendefinition zum [Bereitstellen des Log Analytics Agent auf Linux- oder Windows-Azure Arc-Computern](../governance/policy/samples/built-in-policies.md#monitoring), um zu überwachen, ob der Log Analytics-Agent auf dem Server mit Arc-Unterstützung installiert ist. Wenn der Agent nicht installiert ist, wird er automatisch über einen Wartungstask bereitgestellt. Wenn Sie die Überwachung der Computer mit Azure Monitor für VMs planen, verwenden Sie stattdessen die Initiative [Azure Monitor für VMs aktivieren](../governance/policy/samples/built-in-initiatives.md#monitoring), um den Log Analytics-Agent zu installieren und zu konfigurieren.

      Es wird empfohlen, den Log Analytics-Agent für Windows oder Linux mit Azure Policy zu installieren.

    > [!NOTE]
    > Um die Konfiguration der Computer zu verwalten, die die Hybrid Runbook Worker-Rolle mit DSC (Desired State Configuration) unterstützen, müssen Sie die Computer als DSC-Knoten hinzufügen.

    > [!NOTE]
    > Das Konto [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) mit den entsprechenden sudo-Berechtigungen muss bei der Installation des Linux-Hybrid Workers vorhanden sein. Wenn Sie versuchen, den Worker zu installieren, und das Konto nicht vorhanden ist oder nicht über die entsprechenden Berechtigungen verfügt, tritt bei der Installation ein Fehler auf.

3. Überprüfen Sie, ob der Agent an den Arbeitsbereich meldet.

    Der Log Analytics-Agent für Linux verbindet Computer mit einem Azure Monitor Log Analytics-Arbeitsbereich. Wenn Sie den Agent auf Ihrem Computer installieren und mit Ihrem Arbeitsbereich verbinden, werden automatisch die erforderlichen Komponenten für Hybrid Runbook Worker heruntergeladen.

    Nachdem der Agent nach einigen Minuten eine Verbindung mit dem Log Analytics-Arbeitsbereich hergestellt hat, können Sie über die folgende Abfrage überprüfen, ob er Heartbeatdaten an den Arbeitsbereich sendet.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    In den Suchergebnissen sollten Heartbeat-Datensätze für den Computer angezeigt werden, die angeben, dass dieser verbunden ist und Berichte an den Dienst übermittelt. Standardmäßig leitet jeder Agent einen Heartbeat-Datensatz an seinen zugewiesenen Arbeitsbereich weiter.

4. Führen Sie den folgenden Befehl aus, um den Computer zu einer Hybrid Runbook Worker-Gruppe hinzuzufügen, wobei Sie die Werte für die Parameter `-w`, `-k`, `-g` und `-e` angeben.

    Sie finden die für die Parameter `-k` und `-e` erforderlichen Informationen in Ihrem Automation-Konto auf der Seite **Schlüssel**. Wählen Sie im linken Bereich der Seite im Abschnitt **Kontoeinstellungen** die Option **Schlüssel** aus.

    ![Seite „Schlüssel verwalten“](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Kopieren Sie für den Parameter `-e` den Wert für **URL**.

    * Kopieren Sie für den Parameter `-k` den Wert für **PRIMÄRER ZUGRIFFSSCHLÜSSEL**.

    * Geben Sie für den Parameter `-g` den Namen der Hybrid Runbook Worker-Gruppe an, der der neue Linux Hybrid Runbook Worker beitreten soll. Wenn diese Gruppe bereits im Automation-Konto vorhanden ist, wird ihr der aktuelle Computer hinzugefügt. Wenn diese Gruppe nicht vorhanden ist, wird sie mit diesem Namen erstellt.

    * Geben Sie für den Parameter `-w` Ihre Log Analytics-Arbeitsbereichs-ID an.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Überprüfen Sie die Bereitstellung, nachdem das Skript abgeschlossen wurde. Auf der Seite **Hybrid-Runbook-Workergruppen** in Ihrem Automation-Konto werden unter der Registerkarte **Benutzer-Hybrid Runbook Worker-Gruppe** die neue oder vorhandene Gruppe sowie die Anzahl der Mitglieder angezeigt. Wenn die Gruppe bereits vorhanden ist, wird die Anzahl der Mitglieder erhöht. Sie können die Gruppe in der Liste auf der Seite auswählen. Wählen Sie im Menü auf der linken Seite **Hybrid Worker** aus. Auf der Seite **Hybrid Worker** werden die einzelnen Mitglieder der Gruppe aufgelistet.

    > [!NOTE]
    > Wenn Sie die Log Analytics-VM-Erweiterung für Linux für einen virtuellen Azure-Computer verwenden, empfehlen wir, `autoUpgradeMinorVersion` auf `false` festzulegen, da automatische Upgrades von Versionen Probleme mit dem Hybrid Runbook Worker verursachen können. Informationen zum manuellen Upgrade der Erweiterung finden Sie unter [Azure CLI-Bereitstellung ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Deaktivieren der Signaturüberprüfung

Standardmäßig erfordern Linux Hybrid Runbook Worker eine Überprüfung der Signatur. Wenn Sie ein unsigniertes Runbook für einen Worker ausführen, wird der Fehler `Signature validation failed` angezeigt. Um die Überprüfung der Signatur zu deaktivieren, führen Sie den folgenden Befehl aus. Ersetzen Sie den zweiten Parameter durch Ihre Log Analytics-Arbeitsbereichs-ID.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Entfernen des Hybrid Runbook Workers

Sie können den Befehl `ls /var/opt/microsoft/omsagent` auf dem Hybrid Runbook Worker verwenden, um die Arbeitsbereichs-ID abzurufen. Es wird ein Ordner mit der Arbeitsbereichs-ID als Name erstellt.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Mit diesem Skript wird der Log Analytics-Agent für Linux nicht vom Computer entfernt. Er entfernt nur die Funktionalität und Konfiguration der Hybrid Runbook Worker-Rolle.

## <a name="remove-a-hybrid-worker-group"></a>Entfernen einer Hybrid Worker-Gruppe

Um eine Hybrid Runbook Worker-Gruppe von Linux-Computern zu entfernen, befolgen Sie die gleichen Schritte wie für eine Hybrid Worker-Gruppe unter Windows. Weitere Informationen finden Sie unter [Entfernen einer Hybrid Worker-Gruppe](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Nächste Schritte

* Um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Rechenzentrum oder in einer anderen Cloudumgebung konfigurieren, lesen Sie sich [Ausführen von Runbooks auf einem Hybrid Runbook Worker](automation-hrw-run-runbooks.md) durch.

* Informationen zum Behandeln von Problemen mit Ihren Hybrid Runbook Workern finden Sie unter [Problembehandlung von Hybrid Runbook Workern – Linux](troubleshoot/hybrid-runbook-worker.md#linux).