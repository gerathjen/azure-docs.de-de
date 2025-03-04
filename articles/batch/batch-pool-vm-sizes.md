---
title: Auswählen von VM-Größen und Images für Pools
description: 'Vorgehensweise: Auswahl aus den verfügbaren VM-Größen und Betriebssystemversionen für Computeknoten in Azure Batch-Pools'
ms.topic: conceptual
ms.date: 09/02/2021
ms.custom: seodec18
ms.openlocfilehash: 64dc4f11d5b80f0b493ca393f9a04521090c02cb
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437056"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Auswählen einer VM-Größe und eines Images für Computeknoten in einem Azure Batch-Pool

Wenn Sie eine Knotengröße für einen Azure Batch-Pool wählen, können Sie aus fast allen in Azure verfügbaren VM-Größen wählen. Azure bietet eine Reihe von Größen für virtuelle Linux- und Windows-Computer für verschiedene Workloads.

## <a name="supported-vm-series-and-sizes"></a>Unterstützte VM-Serien und -Größen

### <a name="pools-in-virtual-machine-configuration"></a>Pools in der Konfiguration des virtuellen Computers

Batch-Pools in der Konfiguration des virtuellen Computers unterstützen nahezu alle [VM-Größen](../virtual-machines/sizes.md). Die unterstützten VM-Größen in einer Region können über [Batchverwaltungs-APIs](batch-apis-tools.md#batch-management-apis) sowie über die [Befehlszeilentools](batch-apis-tools.md#batch-command-line-tools) (PowerShell-Cmdlets und Azure CLI) ermittelt werden.  Der [Azure Batch-CLI-Befehl](/cli/azure/batch/location#az_batch_location_list_skus) zum Auflisten der unterstützten VM-Größen in einer Region lautet beispielsweise wie folgt:

```azurecli-interactive
az batch location list-skus --location
                            [--filter]
                            [--maxresults]
                            [--subscription] 
```

Für jede VM-Serie wird in der folgenden Tabelle auch aufgeführt, ob die VM-Serie und die VM-Größen von Batch unterstützt werden.

| VM-Serie  | Unterstützte Größen |
|------------|---------|
| Basic A | Alle Größen *außer* Basic_A0 (A0) |
| Ein | Alle Größen *außer* Standard_A0, Standard_A8, Standard_A9, Standard_A10, Standard_A11 |
| Av2 | Alle Größen |
| B | Nicht unterstützt |
| DCsv2 | Alle Größen |
| Dv2, DSv2 | Alle Größen |
| Dv3, Dsv3 | Alle Größen |
| Dav4, Dasv4 | Alle Größen |
| Ddv4, Ddsv4 |  Alle Größen |
| Dv4, Dsv4 | Nicht unterstützt |
| Ev3, Esv3 | Alle Größen außer E64is_v3 |
| Eav4, Easv4 | Alle Größen |
| Edv4, Edsv4 | Alle Größen |
| Ev4, Esv4 | Nicht unterstützt |
| F, Fs | Alle Größen |
| Fsv2 | Alle Größen |
| FX<sup>1</sup> | Alle Größen |
| G, Gs | Alle Größen |
| H | Alle Größen |
| HB | Alle Größen |
| HBv2 | Alle Größen |
| HBv3 | Alle Größen |
| HC | Alle Größen |
| Ls | Alle Größen |
| Lsv2 | Alle Größen |
| M | Alle Größen |
| Mv2<sup>1</sup> | Alle Größen |
| NC | Alle Größen |
| NCv2 | Alle Größen |
| NCv3 | Alle Größen |
| NCasT4_v3 | Alle Größen |
| ND | Alle Größen |
| NDv4 | Alle Größen |
| NDv2 | Keine: Noch nicht verfügbar |
| NP | Alle Größen |
| SH | Alle Größen |
| NVv3 | Alle Größen |
| NVv4 | Alle Größen |
| SAP HANA | Nicht unterstützt |

<sup>1</sup> Diese VM-Serien können nur mit VM-Images der 2. Generation verwendet werden.

### <a name="using-generation-2-vm-images"></a>Verwenden von VM-Images der zweiten Generation

Einige VM-Serien (etwa [Mv2](../virtual-machines/mv2-series.md)) können nur mit [VM-Images der zweiten Generation](../virtual-machines/generation-2.md) verwendet werden. VM-Images der zweiten Generation werden genau wie andere VM-Images unter Verwendung der Eigenschaft „sku“ der Konfiguration [imageReference](/rest/api/batchservice/pool/add#imagereference) angegeben. Die SKU-Zeichenfolgen besitzen ein Suffix wie „-g2“ oder „-gen2“. Eine Liste mit von Batch unterstützten VM-Images (einschließlich Images der zweiten Generation) können Sie über die [API zum Auflisten der unterstützten Images](/rest/api/batchservice/account/listsupportedimages), über [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage) oder über die [Azure-Befehlszeilenschnittstelle](/cli/azure/batch/pool/supported-images) abrufen.

### <a name="pools-in-cloud-services-configuration"></a>Pools in der Cloud Services-Konfiguration

> [!WARNING]
> Cloud Services-Konfigurationspools sind [veraltet](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Verwenden Sie stattdessen VM-Konfigurationspools.

Batch-Pools in der Cloud Services-Konfiguration unterstützen alle [VM-Größen für Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **mit Ausnahme** der folgenden:

| VM-Serie  | Nicht unterstützte Größen |
|------------|-------------------|
| A-Serie   | Sehr klein       |
| Av2-Serie | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Überlegungen zu Größen

- **Anwendungsanforderungen**: Berücksichtigen Sie die Merkmale und Anforderungen der Anwendungen, die auf den Knoten ausgeführt werden sollen. Die Beantwortung der Fragen, ob es sich beispielsweise um eine Multithreadanwendung handelt und wie viel Arbeitsspeicher sie beansprucht, kann Ihnen dabei behilflich sein, die am besten geeignete und kostengünstigste Knotengröße zu bestimmen. Ziehen Sie für [MPI-Workloads](batch-mpi.md) mit mehreren Instanzen oder CUDA-Anwendungen spezielle [HPC](../virtual-machines/sizes-hpc.md)- bzw. [GPU-fähige ](../virtual-machines/sizes-gpu.md) VM-Größen in Betracht. Weitere Informationen finden Sie unter [Verwenden RDMA-fähiger oder GPU-fähiger Instanzen in Batch-Pools](batch-pool-compute-intensive-sizes.md).

- **Aufgaben pro Knoten**: Die Knotengröße wird normalerweise unter der Annahme ausgewählt, dass jeweils nur eine Aufgabe auf einem Knoten ausgeführt wird. Es kann jedoch von Vorteil sein, mehrere Aufgaben (und somit mehrere Anwendungsinstanzen) während der Auftragsausführung auf Computeknoten [parallel zu nutzen](batch-parallel-node-tasks.md). In diesem Fall wird häufig eine Knotengröße mit mehreren Kernen gewählt, um den höheren Bedarf an parallelen Aufgabenausführungen decken zu können.

- **Auslastungsgrade für verschiedene Aufgaben**: Alle Knoten in einem Pool haben die gleiche Größe. Wenn Sie Anwendungen mit unterschiedlichen Systemanforderungen bzw. Auslastungsgraden ausführen möchten, empfehlen wir Ihnen die Verwendung von separaten Pools.

- **Regionale Verfügbarkeit**: Eine VM-Serie oder -Größe ist in den Regionen, in denen Sie die Batch-Konten erstellen, möglicherweise nicht verfügbar. Informationen dazu, welche Größen verfügbar sind, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).

- **Kontingente**: Die [Kernkontingente](batch-quota-limit.md#resource-quotas) in Ihrem Batch-Konto können die Anzahl der Knoten einer bestimmten Größe beschränken, die Sie einem Batch-Pool hinzufügen können. Bei Bedarf können Sie eine [Kontingenterhöhung](batch-quota-limit.md#increase-a-quota) anfordern.

- **Poolkonfiguration:** Im Allgemeinen stehen Ihnen mehr VM-Größenoptionen zur Verfügung, wenn Sie einen Pool in der Konfiguration der VM anstatt in der Cloud Services-Konfiguration erstellen.

## <a name="supported-vm-images"></a>Unterstützte VM-Images

Verwenden Sie eine der folgenden APIs, um eine Liste mit Windows- und Linux-VM-Images zurückzugeben, die derzeit von Batch unterstützt werden, einschließlich der Knoten-Agent-SKU-IDs für jedes Image:

- Batch-Dienste-REST-API: [Liste unterstützter Images](/rest/api/batchservice/account/listsupportedimages)
- Mit PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI: [az batch pool supported-images](/cli/azure/batch/pool/supported-images)

Es wird dringend empfohlen, keine Bilder zu verwenden, die bald nicht mehr von Batch unterstützt werden. Die Datumsangaben für die Unterstützungseinstellung können Sie über die [`ListSupportedImages`-API](/rest/api/batchservice/account/listsupportedimages), [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage) oder über die [Azure CLI](/cli/azure/batch/pool/supported-images) ermitteln. Weitere Informationen zur Auswahl eines VM-Image für den Batch-Pool finden Sie im Leitfaden zu den [bewährten Methoden für Batch](best-practices.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Workflow des Batch-Diensts und primäre Ressourcen](batch-service-workflow-features.md) wie Pools, Knoten, Aufträge und Aufgaben.
- Informationen zur Verwendung von rechenintensiven VM-Größen finden Sie unter [Verwenden RDMA-fähiger oder GPU-fähiger Instanzen in Batch-Pools](batch-pool-compute-intensive-sizes.md).
