---
title: Übersicht über die Architektur
description: Übersicht über die Architektur, die Komponenten und die Prozesse des Azure Backup-Diensts.
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 8b4ea499766349b3a5ebee340f17f5584303ae16
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361848"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup-Architektur und -Komponenten

Sie können den [Azure Backup-Dienst](backup-overview.md) verwenden, um Daten auf der Microsoft Azure-Cloudplattform zu sichern. In diesem Artikel werden die Architektur, die Komponenten und die Prozesse von Azure Backup zusammengefasst.

## <a name="what-does-azure-backup-do"></a>Welche Funktion hat Azure Backup?

Azure Backup sichert die Daten, den Computerzustand und die Workloads, die auf lokalen Computern und Instanzen virtueller Azure-Computer ausgeführt werden. Der Dienst kann in verschiedenen Szenarien eingesetzt werden:

## <a name="how-does-azure-backup-work"></a>Wie funktioniert Azure Backup?

Sie können Computer und Daten mithilfe verschiedener Methoden sichern:

- **Sichern lokaler Computer:**
  - Sie können lokale Windows-Computer mit dem Microsoft Azure Recovery Services-Agent (MARS-Agent) von Azure Backup direkt in Azure sichern. Linux-Computer werden nicht unterstützt.
  - Sie können lokale Computer auf einem Sicherungsserver sichern – entweder auf System Center Data Protection Manager (DPM) oder Microsoft Azure Backup Server (MABS). Anschließend können Sie den Sicherungsserver in einem Recovery Services-Tresor in Azure sichern.

- **Sichern virtueller Azure-Computer:**
  - Azure-VMs können direkt gesichert werden. Azure Backup installiert eine Sicherungserweiterung für den Azure-VM-Agent, der auf dem virtuellen Computer ausgeführt wird. Diese Erweiterung sichert die gesamte VM.
  - Sie können bestimmte Dateien und Ordner auf der Azure-VM sichern, indem Sie den MARS-Agent ausführen.
  - Sie können Azure-VMs auf dem in Azure ausgeführten MABS sichern, und dann können Sie den MABS in einem Recovery Services-Tresor sichern.

Erfahren Sie mehr über die [sicherbaren Elemente](backup-overview.md) und über [unterstützte Sicherungsszenarien](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Wo werden die Daten gesichert?

Azure Backup speichert gesicherte Daten in Tresoren: in Recovery Services-Tresoren und Azure Backup-Tresoren. Ein Tresor ist eine Onlinespeicherentität in Azure, die zum Speichern von Daten wie Sicherungskopien, Wiederherstellungspunkten und Sicherungsrichtlinien verwendet wird.

Tresore bieten die folgenden Features:

- Tresore vereinfachen die Organisation Ihrer Sicherungsdaten und minimieren gleichzeitig den Verwaltungsaufwand.
- Sie können gesicherte Elemente einschließlich virtueller Azure-Computer und lokaler Computer in einem Tresor überwachen.
- Der Zugriff auf den Tresor kann mithilfe der [rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/role-assignments-portal.md) verwaltet werden.
- Sie können angeben, wie die Daten im Tresor repliziert werden sollen, um für Redundanz zu sorgen:
  - **Lokal redundanter Speicher (LRS):** Sie können LRS zum Schutz vor Ausfällen in einem Datencenter verwenden. LRS repliziert Daten in einer Speicherskalierungseinheit. [Weitere Informationen](../storage/common/storage-redundancy.md#locally-redundant-storage)
  - **Georedundanter Speicher (Geo-Redundant Storage, GRS)** : Sie können GRS zum Schutz vor regionsweiten Ausfällen verwenden. GRS repliziert Ihre Daten in einer sekundären Region. [Weitere Informationen](../storage/common/storage-redundancy.md#geo-redundant-storage)
  - **Zonenredundanter Speicher (ZRS)** repliziert Ihre Daten in [Verfügbarkeitszonen](../availability-zones/az-overview.md#availability-zones) und gewährleistet dadurch Data Residency und Resilienz in derselben Region. [Weitere Informationen](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Recovery Services-Tresore verwenden standardmäßig GRS.

Recovery Services-Tresore bieten die folgenden zusätzlichen Features:

- In jedem Azure-Abonnement können bis zu 500 Tresore erstellt werden.

## <a name="backup-agents"></a>Sicherungs-Agents

Azure Backup stellt je nach Typ des zu sichernden Computers verschiedene Sicherungs-Agents bereit:

**Agent** | **Details**
--- | ---
**MARS-Agent** | <ul><li>Wird auf einzelnen lokalen Windows Server-Computern ausgeführt, um Dateien, Ordner und den Systemstatus zu sichern.</li> <li>Wird auf Azure-VMs ausgeführt, um Dateien, Ordner und den Systemstatus zu sichern.</li> <li>Wird auf DPM-/MABS-Servern ausgeführt, um den lokalen DPM-/MABS-Speicherdatenträger in Azure zu sichern.</li></ul>
**Azure-VM-Erweiterung** | Wird auf Azure-VMs ausgeführt, um sie in einem Tresor zu sichern.

## <a name="backup-types"></a>Sicherungstypen

In der folgenden Tabelle werden die verschiedenen Sicherungstypen und deren Verwendung erläutert:

**Sicherungstyp** | **Details** | **Verwendung**
--- | --- | ---
**Vollständig** | Eine vollständige Sicherung enthält die gesamte Datenquelle. Sie benötigt mehr Netzwerkbandbreite als differenzielle oder inkrementelle Sicherungen. | Wird für die erste Sicherung verwendet.
**Differenziell** |  Eine differenzielle Sicherung speichert die Blöcke, die seit der ersten vollständigen Sicherung geändert wurden. Sie beansprucht weniger Netzwerk- und Speicherressourcen und speichert keine redundanten Kopien von Daten, die nicht geändert wurden.<br/><br/> Ineffizient, da auch Datenblöcke übertragen und gespeichert werden, die in später erfolgten Sicherungen nicht geändert wurden. | Wird von Azure Backup nicht verwendet.
**Inkrementell** | Eine inkrementelle Sicherung speichert nur Datenblöcke, die seit der vorherigen Sicherung geändert wurden. Hohe Speicher- und Netzwerkeffizienz. <br/><br/> Bei der inkrementellen Sicherung müssen keine ergänzenden vollständigen Sicherungen erstellt werden. | Wird von DPM/MABS für Datenträgersicherungen sowie bei allen Sicherungen in Azure verwendet. Wird für eine SQL Server-Sicherung nicht verwendet.

## <a name="sql-server-backup-types"></a>Typen von SQL Server-Sicherungen

In der folgenden Tabelle werden die verschiedenen für SQL Server-Datenbanken verwendeten Sicherungstypen und die Häufigkeit von deren Verwendung erläutert:

**Sicherungstyp** | **Details** | **Verwendung**
--- | --- | ---
**Vollständige Sicherung** | Die gesamte Datenbank wird gesichert. Eine vollständige Datenbanksicherung enthält alle Daten in einer bestimmten Datenbank oder in einem bestimmten Satz von Dateigruppen oder Dateien. Außerdem enthält eine vollständige Sicherung genügend Protokolle, um diese Daten wiederherzustellen. | Pro Tag kann höchstens eine vollständige Sicherung ausgelöst werden.<br/><br/> Sie können auswählen, ob eine vollständige Sicherung täglich oder wöchentlich ausgeführt werden soll.
**Differenzielle Sicherung** | Eine differenzielle Sicherung basiert auf der letzten vollständigen Datensicherung.<br/><br/> Damit werden nur die Daten erfasst, die seit der vollständigen Sicherung geändert wurden. |  Pro Tag kann höchstens eine differenzielle Sicherung ausgelöst werden.<br/><br/> Sie können eine vollständige Sicherung und eine differenzielle Sicherung nicht am gleichen Tag konfigurieren.
**Sicherung des Transaktionsprotokolls** | Eine Zeitpunktwiederherstellung ist bis zu einer bestimmten Sekunde möglich. | Transaktionsprotokollsicherungen können höchstens alle 15 Minuten durchgeführt werden.

## <a name="sap-hana-backup-types"></a>SAP HANA-Sicherungstypen

In der folgenden Tabelle werden die verschiedenen für SAP HANA-Datenbanken verwendeten Sicherungstypen und die Häufigkeit von deren Verwendung erläutert:

| Sicherungstyp | Details | Verwendung |
| --- | --- | --- |
| **Vollständige Sicherung** | Die gesamte Datenbank wird gesichert. Dieser Sicherungstyp kann einzeln zum Wiederherstellen eines bestimmten Zeitpunkts verwendet werden. | Pro Tag kann höchstens eine vollständige Sicherung geplant werden. <br><br> Sie können auswählen, ob eine vollständige Sicherung täglich oder wöchentlich ausgeführt werden soll. |
| **Differenzielle Sicherung** | Eine differenzielle Sicherung basiert auf der letzten vollständigen Datensicherung. <br><br> Damit werden nur die Daten erfasst, die seit der letzten vollständigen Sicherung geändert wurden. | Pro Tag kann höchstens eine differenzielle Sicherung geplant werden.  <br><br> Sie können eine vollständige Sicherung und eine differenzielle Sicherung nicht am gleichen Tag konfigurieren. |
| **Inkrementelle Sicherung** | Eine inkrementelle Sicherung basiert auf der letzten vollständigen/differenziellen/inkrementellen Datensicherung. <br><br> Damit werden nur die Daten erfasst, die seit der letzten Datensicherung geändert wurden. | Pro Tag kann höchstens eine inkrementelle Sicherung geplant werden. <br><br> Für eine Datenbank können nicht gleichzeitig differenzielle und inkrementelle Sicherungen geplant werden, sondern nur Deltasicherungen. <br><br> Sie können eine vollständige Sicherung und eine differenzielle Sicherung nicht am gleichen Tag konfigurieren. |
| **Sicherung des Transaktionsprotokolls** | Eine Zeitpunktwiederherstellung ist bis zu einer bestimmten Sekunde möglich. | Transaktionsprotokollsicherungen können höchstens alle 15 Minuten durchgeführt werden. |

## <a name="comparison-of-backup-types"></a>Vergleich von Sicherungstypen

Speicherverbrauch, RTO (Recovery Time Objective) und Netzwerkauslastung variieren je nach Art der Sicherung. In der folgenden Abbildung ist ein Vergleich der Sicherungstypen dargestellt:

- Die Datenquelle A besteht aus 10 Speicherblöcken (A1–A10), die monatlich gesichert werden.
- Die Blöcke A2, A3, A4 und A9 ändern sich im ersten Monat, der Block A5 ändert sich im nächsten Monat.
- Bei differenziellen Sicherungen werden im zweiten Monat die geänderten Blöcke A2, A3, A4 und A9 gesichert. Im dritten Monat werden die gleichen Blöcke erneut gesichert – zusammen mit dem geänderten Block A5. Die geänderten Blöcke werden bis zur nächsten vollständigen Sicherung immer wieder gesichert.
- Bei inkrementellen Sicherungen werden die Blöcke A2, A3, A4 und A9 im zweiten Monat als geändert gekennzeichnet und übertragen. Im dritten Monat wird nur der geänderte Block A5 gekennzeichnet und übertragen.

![Das Diagramm zeigt, dass der Speicherverbrauch, RTO (Recovery Time Objective) und Netzwerkauslastung je nach Art des Sicherungstyps variieren.](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Sicherungsfeatures

Die folgende Tabelle enthält eine Zusammenfassung der für die verschiedenen Sicherungstypen unterstützten Features:

**Feature** | **Direkte Sicherung von Dateien und Ordnern (mit dem MARS-Agent)** | **Azure-VM-Sicherung** | **Computer oder Apps mit DPM/MABS**
--- | --- | --- | ---
Sicherung in einem Tresor | ![Ja][green] | ![Ja][green] | ![Ja][green]
Sicherung auf DPM-/MABS-Datenträgern und dann in Azure | | | ![Ja][green]
Komprimierung der gesendeten Daten für die Sicherung | ![Ja][green] | Daten werden unkomprimiert übertragen. Etwas höherer Speicherbedarf, aber schnellere Wiederherstellung.  | ![Ja][green]
Inkrementelle Sicherung |![Ja][green] |![Ja][green] |![Ja][green]
Sicherung deduplizierter Datenträger | | | ![Teilweise][yellow]<br/><br/> Nur für lokal bereitgestellte DPM-/MABS-Server

![Tabellenschlüssel](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Grundlagen zu Sicherungsrichtlinien

- Eine Sicherungsrichtlinie wird pro Tresor erstellt.
- Eine Sicherungsrichtlinie kann für die Sicherung der folgenden Workloads erstellt werden: Azure-VMs, SQL in Azure-VMs, SAP HANA in Azure-VMs und Azure-Dateifreigaben. Die Richtlinie für Datei- und Ordnersicherungen mit dem MARS-Agent wird in der MARS-Konsole angegeben.
  - Azure-Dateifreigabe
- Eine Richtlinie kann einer Vielzahl von Ressourcen zugewiesen werden. Eine Azure-VM-Sicherungsrichtlinie kann verwendet werden, um eine Vielzahl von Azure-VMs zu schützen.
- Eine Richtlinie besteht aus zwei Komponenten.
  - Zeitplan: Diese Komponente gibt an, wann eine Sicherung durchgeführt werden soll.
  - Aufbewahrung: Diese Komponente gibt an, wie lange jede einzelne Sicherung beibehalten werden soll.
- Der Zeitplan kann als „täglich“ oder „wöchentlich“ mit einem bestimmten Zeitpunkt definiert werden.
- Die Aufbewahrung kann für die Sicherungspunkte „täglich“, „wöchentlich“, „monatlich“ oder „jährlich“ definiert werden.
  - „Wöchentlich“ bezieht sich auf eine Sicherung an einem bestimmten Tag der Woche.
  - „Monatlich“ bezieht sich auf eine Sicherung an einem bestimmten Tag des Monats.
  - „Jährlich“ bezieht sich auf eine Sicherung an einem bestimmten Tag des Jahres.
- Die Aufbewahrung für die Sicherungspunkte „monatlich“ und „jährlich“ wird als „Langzeitaufbewahrung“ (Long Term Retention, LTR) bezeichnet.
- Wenn ein Tresor erstellt wurde, wird auch eine Standardrichtlinie mit dem Namen „DefaultPolicy“ erstellt. Diese kann zum Sichern von Ressourcen verwendet werden.
- Jede Änderung der Aufbewahrungsdauer für eine Sicherungsrichtlinie wird nicht nur auf neue Wiederherstellungspunkte, sondern auch rückwirkend auf alle älteren Wiederherstellungspunkte angewendet.

### <a name="impact-of-policy-change-on-recovery-points"></a>Auswirkung von Richtlinienänderungen auf Wiederherstellungspunkte

- **Erhöhte/gesenkte Aufbewahrungsdauer:** Wenn die Aufbewahrungsdauer geändert wird, wird die neue Aufbewahrungsdauer auch auf die vorhandenen Wiederherstellungspunkte angewendet. Das resultiert darin, dass einige Wiederherstellungspunkte bereinigt werden. Wenn der Aufbewahrungszeitraum erhöht wird, wird auch die Aufbewahrung der vorhandenen Wiederherstellungspunkte verlängert.
- **Änderung von täglich in wöchentlich:** Wenn die geplanten Sicherungen von täglich in wöchentlich geändert werden, werden die vorhandenen täglichen Wiederherstellungspunkte bereinigt.
- **Änderung von wöchentlich in täglich:** Die vorhandenen wöchentlichen Sicherungen werden entsprechend der aktuellen Aufbewahrungsrichtlinie basierend auf der Anzahl der verbleibenden Tage beibehalten.

### <a name="additional-reference"></a>Zusätzliche Referenz

- Virtuelle Azure-Computer: Informationen zum [Erstellen](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) und [Ändern](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) von Richtlinien
- SQL Server-Datenbank auf einem virtuellen Azure-Computer: Informationen zum [Erstellen](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) und [Ändern](./manage-monitor-sql-database-backup.md#modify-policy) von Richtlinien
- Azure-Dateifreigabe: Informationen zum [Erstellen](./backup-afs.md) und [Ändern](./manage-afs-backup.md#modify-policy) von Richtlinien
- SAP HANA: Informationen zum [Erstellen](./backup-azure-sap-hana-database.md#create-a-backup-policy) und [Ändern](./sap-hana-db-manage.md#change-policy) von Richtlinien
- MARS: Informationen zum [Erstellen](./backup-windows-with-mars-agent.md#create-a-backup-policy) und [Ändern](./backup-azure-manage-mars.md#modify-a-backup-policy) von Richtlinien
- [Gibt es Beschränkungen bei der Planung der Sicherung?](./backup-azure-backup-faq.yml#are-there-limits-on-backup-scheduling-)
- [Was passiert, wenn ich meine Sicherungsrichtlinie ändere?](./backup-azure-backup-faq.yml#what-happens-when-i-change-my-backup-policy-)

## <a name="architecture-built-in-azure-vm-backup"></a>Architektur: Integrierte Azure-VM-Sicherung

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architektur: Direkte Sicherung von lokalen Windows Server-Computern oder Azure-VM-Dateien oder -Ordnern

1. Um das Szenario einzurichten, müssen Sie den MARS-Agent herunterladen und auf dem Computer installieren. Wählen Sie dann aus, welche Komponenten gesichert werden sollen, wann die Sicherungen ausgeführt und wie lange sie in Azure aufbewahrt werden sollen.
1. Die erste Sicherung wird gemäß Ihren Sicherungseinstellungen ausgeführt.
1. Der MARS-Agent erstellt mit VSS eine Momentaufnahme der für die Sicherung ausgewählten Volumes.
    - Der MARS-Agent verwendet zum Erstellen der Momentaufnahme nur den Schreibvorgang des Windows-Systems.
    - Weil der Agent keine anwendungsspezifischen VSS Writer verwendet, erfasst er keine App-konsistenten Momentaufnahmen.
1. Nach der Erstellung der Momentaufnahme mit VSS erstellt der MARS-Agent eine virtuelle Festplatte (Virtual Hard Disk, VHD) in dem Cacheordner, den Sie beim Konfigurieren der Sicherung angegeben haben. Außerdem speichert der Agent Prüfsummen für die einzelnen Datenblöcke. Diese werden später verwendet, um bei nachfolgenden inkrementellen Sicherungen geänderte Blöcke zu erkennen.
1. Inkrementelle Sicherungen werden gemäß dem angegebenen Zeitplan ausgeführt, sofern Sie keine bedarfsgesteuerte Sicherung ausführen.
1. Bei inkrementellen Sicherungen werden geänderte Dateien identifiziert, und eine neue virtuelle Festplatte wird erstellt. Die VHD wird komprimiert und verschlüsselt und dann an den Tresor gesendet.
1. Nach Abschluss der inkrementellen Sicherung wird die neue VHD mit der VHD zusammengeführt, die nach der ersten Replikation erstellt wurde. Durch diese zusammengeführte VHD erhalten Sie den aktuellen Zustand zum Vergleich für die laufende Sicherung.

![Sicherung lokaler Windows Server-Computer mit dem MARS-Agent](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architektur: Sicherung mit DPM/MABS

1. Sie installieren den DPM- oder MABS-Schutz-Agent auf den Computern, die Sie schützen möchten. Dann fügen Sie die Computer einer DPM-Schutzgruppe hinzu.
    - Zum Schutz von lokalen Computern muss sich der DPM- oder MABS-Server in Ihrer lokalen Umgebung befinden.
    - Zum Schutz von Azure-VMs muss sich der MABS-Server in Azure befinden und als Azure-VM ausgeführt werden.
    - Mit DPM/MABS können Sie Sicherungsvolumes, Freigaben, Dateien und Ordner schützen. Sie können auch den Systemstatus eines Computers (durch Bare-Metal-Sicherung) sowie spezifische Apps mit App-fähigen Sicherungseinstellungen schützen.
1. Wenn Sie den Schutz für einen Computer oder eine App in DPM/MABS einrichten, wählen Sie aus, dass eine kurzzeitige Sicherung auf dem lokalen DPM-/MABS-Datenträger und eine Sicherung in Azure für den Onlineschutz erstellt werden sollen. Außerdem geben Sie an, wann die Sicherung im lokalen DPM-/MABS-Speicher und wann die Onlinesicherung in Azure ausgeführt werden soll.
1. Der Datenträger der geschützten Workload wird gemäß dem angegebenen Zeitplan auf den lokalen MABS-/DPM-Datenträgern gesichert.
1. Die DPM-/MABS-Datenträger werden von dem auf dem DPM-/MABS-Server ausgeführten MARS-Agent im Tresor gesichert.

![Sicherung von Computern und Workloads mit DPM- oder MABS-Schutz](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure-VM-Speicher

Betriebssystem, Apps und Daten von virtuellen Azure-Computern werden auf Datenträgern gespeichert. Jeder virtuelle Azure-Computer verfügt über mindestens zwei Datenträger: einen Datenträger für das Betriebssystem und einen temporären Datenträger. Darüber hinaus können Azure-VMs auch über Datenträger für App-Daten verfügen. Datenträger werden als VHDs gespeichert.

- VHDs werden als Seitenblobs in Azure-Speicherkonten (Standard oder Premium) gespeichert:
  - **Storage Standard:** Zuverlässige, kostengünstige Datenträgerunterstützung für virtuelle Computer ohne wartezeitempfindliche Workloads. Storage Standard kann Datenträger vom Typ SSD Standard oder HDD Standard verwenden.
  - **Storage Premium:** Unterstützung von Hochleistungsdatenträgern. Verwendet SSD Premium-Datenträger.
- Für Datenträger sind verschiedene Leistungsstufen verfügbar:
  - **HDD Standard-Datenträger:** Basiert auf Festplattenlaufwerken und ermöglicht eine kostengünstige Speicherung.
  - **SSD Standard-Datenträger:** Kombiniert Elemente von SSD Premium- und HDD Standard-Datenträgern. Bietet eine konsistentere Leistung und Zuverlässigkeit als HDD, ist aber dennoch kostengünstig.
  - **SSD Premium-Datenträger:** Durch SSDs gestützt; bietet hohe Leistung und geringe Wartezeit für VMs, auf denen E/A-intensive Workloads ausgeführt werden.
- Datenträger können verwaltet oder nicht verwaltet sein:
  - **Nicht verwaltete Datenträger:** Herkömmliche Datenträgertypen, die von VMs verwendet werden. Bei diesen Datenträgern erstellen Sie Ihr eigenes Speicherkonto und geben es beim Erstellen des Datenträgers an. Dann müssen Sie herausfinden, wie Sie die Speicherressourcen für Ihre VMs maximieren können.
  - **Verwaltete Datenträger**: Azure erstellt und verwaltet die Speicherkonten für Sie. Sie geben die Datenträgergröße und die Leistungsstufe an, und Azure erstellt verwaltete Datenträger für Sie. Während Sie Datenträger hinzufügen und VMs skalieren, kümmert sich Azure um die Speicherkonten.

Weitere Informationen zu Datenträgerspeicher und den verfügbaren Datenträgertypen für VMs finden Sie in den folgenden Artikeln:

- [Verwaltete Azure-Datenträger für Linux-VMs](../virtual-machines/managed-disks-overview.md)
- [Verfügbare Datenträgertypen für VMs](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Sichern und Wiederherstellen von Azure-VMs mit Storage Premium

Sie können virtuelle Azure-Computer mithilfe von Storage Premium mit Azure Backup sichern:

- Während des Vorgangs zum Sichern virtueller Computer mit Storage Premium erstellt der Backup-Dienst im Speicherkonto einen temporären Stagingspeicherort mit dem Namen *AzureBackup-* . Die Größe des Stagingspeicherorts entspricht der Größe der Momentaufnahme des Wiederherstellungspunkts.
- Stellen Sie sicher, dass das Premium-Speicherkonto über genügend freien Speicherplatz für den temporären Stagingspeicherort verfügt. Weitere Informationen finden Sie unter [Skalierbarkeitsziele für Seitenblobspeicher mit Premium-Leistung](../storage/blobs/scalability-targets-premium-page-blobs.md). Ändern Sie den Stagingspeicherort nicht.
- Nach Abschluss des Sicherungsauftrags wird der Stagingspeicherort gelöscht.
- Die Kosten für den Speicher, der für den Stagingspeicherort genutzt wird, entsprechen den [Preisen für Storage Premium](../virtual-machines/disks-types.md#billing).

Virtuelle Azure-Computer mit Storage Premium können in Storage Premium oder Storage Standard wiederhergestellt werden. In der Regel werden die VMs in Storage Premium wiederhergestellt. Wenn Sie jedoch nur eine Teilmenge der Dateien des virtuellen Computers benötigen, kann die Wiederherstellung in Storage Standard kostengünstiger sein.

### <a name="back-up-and-restore-managed-disks"></a>Sichern und Wiederherstellen von verwalteten Datenträgern

Sie können virtuelle Azure-Computer mit verwalteten Datenträgern sichern:

- Virtuelle Computer mit verwalteten Datenträgern werden auf die gleiche Weise gesichert wie andere virtuelle Azure-Computer. Sie können den virtuellen Computer direkt über die Einstellungen des virtuellen Computers sichern oder die Sicherung für virtuelle Computer im Recovery Services-Tresor aktivieren.
- Virtuelle Computer können auf verwalteten Datenträgern über RestorePoint-Sammlungen gesichert werden, die auf verwalteten Datenträgern aufbauen.
- Azure Backup unterstützt auch das Sichern von virtuellen Computern mit verwalteten Datenträgern, die mithilfe von Azure Disk Encryption verschlüsselt wurden.

Virtuelle Computer mit verwalteten Datenträgern können als vollständiger virtueller Computer mit verwalteten Datenträgern oder in einem Speicherkonto wiederhergestellt werden:

- Während des Wiederherstellungsvorgangs werden die verwalteten Datenträger von Azure verwaltet. Bei Verwendung der Speicherkontooption verwalten Sie das Speicherkonto, das während des Wiederherstellungsvorgangs erstellt wird.
- Wenn Sie einen verschlüsselten verwalteten VM wiederherstellen, müssen Sie vor dem Starten des Wiederherstellungsvorgangs sicherstellen, dass die Schlüssel und Geheimnisse für den virtuellen Computer im Schlüsseltresor vorhanden sind.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Supportmatrix](backup-support-matrix.md) an, um Informationen zu unterstützten Features und zu Einschränkungen für Sicherungsszenarien zu erhalten.
- Richten Sie die Sicherung für eines der folgenden Szenarien ein:
  - [Sichern virtueller Azure-Computer](backup-azure-arm-vms-prepare.md)
  - [Direktes Sichern von Windows-Computern](tutorial-backup-windows-server-to-azure.md) (ohne Sicherungsserver)
  - [Einrichten von MABS](backup-azure-microsoft-azure-backup.md) für die Sicherung in Azure und anschließendes Sichern von Workloads per MABS
  - [Einrichten von DPM](backup-azure-dpm-introduction.md) für die Sicherung in Azure und anschließendes Sichern von Workloads per DPM

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
