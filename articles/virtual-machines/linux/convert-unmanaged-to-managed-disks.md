---
title: Konvertieren einer Linux-VM von nicht verwalteten Datenträgern zu verwalteten Datenträgern
description: Konvertieren einer Linux-VM von nicht verwalteten Datenträgern zu verwalteten Datenträgern mithilfe der Azure-Befehlszeilenschnittstelle
author: roygara
ms.service: storage
ms.collection: linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 18d3b3c5717c5bec2044a05c7e5854222adb73b1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257934"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konvertieren einer Linux-VM von nicht verwalteten Datenträgern in verwaltete Datenträger

**Gilt für:** :heavy_check_mark: Linux-VMs 

Wenn Sie über vorhandene virtuelle Linux-Computer (VMs) verfügen, die nicht verwaltete Datenträger verwenden, können Sie die VMs konvertieren, sodass [verwaltete Azure-Datenträger](../managed-disks-overview.md) verwendet werden. Bei diesem Prozess werden sowohl der Betriebssystemdatenträger als auch alle anderen angefügten Datenträger konvertiert.

In diesem Artikel wird beschrieben, wie Sie VMs über die Azure-Befehlszeilenschnittstelle konvertieren. Wenn Sie die Befehlszeilenschnittstelle installieren oder aktualisieren müssen, finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) Informationen dazu. 

## <a name="before-you-begin"></a>Voraussetzungen
* Lesen Sie die [häufig gestellten Fragen zu Managed Disks](../faq-for-disks.yml).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Die ursprünglichen VHDs und das Speicherkonto, die vor der Konvertierung vom virtuellen Computer verwendet wurden, werden nicht gelöscht. Sie verursachen weiterhin Kosten. Um zu vermeiden, dass diese Artefakte in Rechnung gestellt werden, löschen Sie die ursprünglichen VHD-Blobs, nachdem Sie sichergestellt haben, dass die Konvertierung abgeschlossen ist. Wenn Sie nach diesen nicht angefügten Datenträgern suchen müssen, um sie zu löschen, lesen Sie den Artikel [Suchen und Löschen von nicht angefügten verwalteten und nicht verwalteten Azure-Datenträgern](find-unattached-disks.md).

## <a name="convert-single-instance-vms"></a>Konvertieren von Einzelinstanz-VMs
In diesem Abschnitt wird beschrieben, wie Sie für Einzelinstanz-VMs von Azure die Konvertierung von nicht verwalteten Datenträgern in verwaltete Datenträger durchführen. (Wenn Ihre VMs in einer Verfügbarkeitsgruppe enthalten sind, lesen Sie den nächsten Abschnitt.) Sie können diesen Prozess verwenden, um nicht verwaltete Premium-Datenträger (SSD) in verwaltete Premium-Datenträger oder nicht verwaltete Standard-Datenträger (HDD) in verwaltete Standard-Datenträger zu konvertieren.

1. Heben Sie die Zuordnung der VM mit [az vm deallocate](/cli/azure/vm) auf. Im folgenden Beispiel wird die Zuordnung für die VM `myVM` in der Ressourcengruppe `myResourceGroup` aufgehoben:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Führen Sie mit [az vm convert](/cli/azure/vm) die Konvertierung in verwaltete Datenträger durch. Mit dem folgenden Prozess wird die VM `myVM` konvertiert, einschließlich des Betriebssystemdatenträgers und aller Datenträger:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Starten Sie die VM nach der Konvertierung in verwaltete Datenträger mit [az vm start](/cli/azure/vm). Im folgenden Beispiel wird die VM `myVM` in der Ressourcengruppe `myResourceGroup` gestartet.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Konvertieren von virtuellen Computern in einer Verfügbarkeitsgruppe

Falls sich die VMs, die Sie in verwaltete Datenträger konvertieren möchten, in einer Verfügbarkeitsgruppe befinden, müssen Sie zuerst für die Verfügbarkeitsgruppe die Konvertierung in eine verwaltete Verfügbarkeitsgruppe durchführen.

Für alle VMs in der Verfügbarkeitsgruppe muss die Zuordnung aufgehoben werden, bevor Sie die Verfügbarkeitsgruppe konvertieren können. Planen Sie die Konvertierung aller VMs in verwaltete Datenträger, nachdem die Verfügbarkeitsgruppe selbst in eine verwaltete Verfügbarkeitsgruppe konvertiert wurde. Starten Sie anschließend alle VMs, und setzen Sie den normalen Betrieb fort.

1. Listen Sie alle VMs einer Verfügbarkeitsgruppe mit [az vm availability-set list](/cli/azure/vm/availability-set) auf. Im folgenden Beispiel werden alle VMs der Verfügbarkeitsgruppe `myAvailabilitySet` aufgelistet, die in der Ressourcengruppe `myResourceGroup` enthalten sind:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Verwenden Sie [az vm deallocate](/cli/azure/vm), um die Zuordnung aller VMs aufzuheben. Im folgenden Beispiel wird die Zuordnung für die VM `myVM` in der Ressourcengruppe `myResourceGroup` aufgehoben:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Konvertieren Sie die Verfügbarkeitsgruppe mit [az vm availability-set convert](/cli/azure/vm/availability-set). Im folgenden Beispiel wird die Verfügbarkeitsgruppe `myAvailabilitySet` aus der Ressourcengruppe `myResourceGroup` konvertiert:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Konvertieren Sie alle VMs in verwaltete Datenträger, indem Sie [az vm convert](/cli/azure/vm) verwenden. Mit dem folgenden Prozess wird die VM `myVM` konvertiert, einschließlich des Betriebssystemdatenträgers und aller Datenträger:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Starten Sie alle VMs nach der Konvertierung in verwaltete Datenträger mit [az vm start](/cli/azure/vm). Im folgenden Beispiel wird die VM `myVM` in der Ressourcengruppe `myResourceGroup` gestartet:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Konvertieren über das Azure-Portal

Sie haben auch die Möglichkeit, nicht verwaltete Datenträger über das Azure-Portal in verwaltete Datenträger zu konvertieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie die VM in der Liste der VMs im Portal aus.
3. Wählen Sie auf dem Blatt für die VM aus dem Menü die Option **Datenträger** aus.
4. Wählen Sie oben auf dem Blatt **Datenträger** die Option **Zu Managed Disks migrieren** aus.
5. Wenn sich Ihre VM in einer Verfügbarkeitsgruppe befindet, wird auf dem Blatt **Zu Managed Disks migrieren** eine Warnung angezeigt, die besagt, dass Sie die Verfügbarkeitsgruppe zuerst konvertieren müssen. Die Warnung sollte einen Link enthalten, über den Sie die Verfügbarkeitsgruppe konvertieren können. Sobald die Verfügbarkeitsgruppe konvertiert wurde, oder wenn sich Ihre VM nicht in einer Verfügbarkeitsgruppe befindet, klicken Sie auf **Migrieren**, um den Prozess der Migration Ihrer Datenträger zu Managed Disks zu starten.

Nach Abschluss der Migration wird die VM angehalten und neu gestartet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Speicheroptionen finden Sie in der [Übersicht über Managed Disks](../managed-disks-overview.md).
