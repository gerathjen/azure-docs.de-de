---
title: Erstellen eines Triggers für ein rollierendes Fenster
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie in Azure Data Factory oder Azure Synapse Analytics einen Trigger erstellen, der eine Pipeline für ein rollierendes Fenster ausführt.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 6df2a24ca029f14a4743b28641b8ac4c2b88e851
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223781"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Erstellen eines Triggers zum Ausführen einer Pipeline für ein rollierendes Fenster

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält die Schritte zum Erstellen, Starten und Überwachen eines Triggers für rollierende Fenster. Allgemeine Informationen zu Triggern und unterstützten Triggertypen finden Sie unter [Pipelineausführung und Trigger in Azure Data Factory](concepts-pipeline-execution-triggers.md).

Trigger für ein rollierendes Fenster werden ab einem angegebenen Startzeitpunkt in regelmäßigen Zeitintervallen ausgelöst, während der Zustand beibehalten wird. Bei rollierenden Fenstern handelt es sich um eine Reihe von nicht überlappenden, aneinandergrenzenden Zeitintervallen mit einer festen Größe. Ein Trigger für ein rollierendes Fenster hat eine 1:1-Beziehung zu einer Pipeline und kann nur auf eine einzelne Pipeline verweisen. Der Trigger für ein rollierendes Fenster ist eine leistungsstärkere Alternative für einen Zeitplantrigger und verfügt über eine Suite mit Funktionen für komplexe Szenarien ([Abhängigkeit von anderen Triggern für ein rollierendes Fenster](#tumbling-window-trigger-dependency), [Erneutes Ausführen eines fehlgeschlagenen Auftrags](tumbling-window-trigger-dependency.md#monitor-dependencies) und [Festlegen der Benutzerwiederholung für Pipelines](#user-assigned-retries-of-pipelines)). Weitere Informationen zum Unterschied zwischen dem Zeitplantrigger und dem Trigger für ein rollierendes Fenster finden Sie [hier](concepts-pipeline-execution-triggers.md#trigger-type-comparison).

## <a name="azure-data-factory-and-synapse-portal-experience"></a>Azure Data Factory und Synapse-Portal

1. Zum Erstellen eines Triggers für ein rollierendes Fenster im Azure-Portal wählen Sie die Registerkarte **Trigger** und dann **Neu** aus. 
1. Nachdem der Bereich für die Triggerkonfiguration geöffnet wurde, wählen Sie **Rollierendes Fenster** aus und definieren dann die Triggereigenschaften des rollierenden Fensters. 
1. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png" alt-text="Erstellen eines Triggers für ein rollierendes Fenster im Azure-Portal":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
:::image type="content" source="media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger-synapse.png" alt-text="Erstellen eines Triggers für ein rollierendes Fenster im Azure-Portal":::

---

## <a name="tumbling-window-trigger-type-properties"></a>Triggertypeigenschaften eines rollierenden Fensters

Ein rollierendes Fenster weist die folgenden Triggertypeigenschaften auf:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

Die folgende Tabelle enthält eine allgemeine Übersicht über die wichtigsten JSON-Elemente im Zusammenhang mit der Wiederholung und Zeitplanung eines Triggers für rollierende Fenster:

| JSON-Element | BESCHREIBUNG | type | Zulässige Werte | Erforderlich |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Der Typ des Triggers. Der Typ ist der feste Wert „TumblingWindowTrigger“. | String | „TumblingWindowTrigger“ | Ja |
| **runtimeState** | Der aktuelle Status der Triggerausführungszeit.<br/>**Hinweis**: Dieses Element ist \<readOnly>. | String | „Started“, „Stopped“, „Disabled“ | Ja |
| **frequency** | Eine Zeichenfolge für die Einheit der Häufigkeit (Minuten oder Stunden), mit der der Trigger wiederholt wird. Wenn die **startTime**-Datumswerte granularer sind als der **frequency**-Wert, werden die **startTime**-Datumsangaben bei der Berechnung der Fenstergrenzen berücksichtigt. Beispiel: Wenn der **frequency**-Wert stündlich ist und der **startTime**-Wert „2017-09-01T10:10:10Z“ lautet, ist das erste Fenster „(2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z)“. | String | „minute“, „hour“  | Ja |
| **interval** | Eine positive ganze Zahl, die das Intervall für den **frequency**-Wert angibt, der bestimmt, wie oft der Trigger ausgeführt wird. Ist **interval** also beispielsweise auf „3“ und **frequency** auf „hour“ festgelegt, wird der Trigger alle drei Stunden ausgeführt. <br/>**Hinweis**: Das minimale Fensterintervall beträgt 5 Minuten. | Integer | Eine positive ganze Zahl | Ja |
| **startTime**| Das erste Vorkommen, das in der Vergangenheit liegen kann. Das erste Triggerintervall ist (**startTime**, **startTime** + **interval**). | Datetime | Ein DateTime-Wert | Ja |
| **endTime**| Das letzte Vorkommen, das in der Vergangenheit liegen kann. | Datetime | Ein DateTime-Wert | Ja |
| **delay** | Der Zeitraum, in dem der Beginn der Datenverarbeitung für das Fenster verzögert wird. Die Ausführung der Pipeline wird nach der erwarteten Ausführungszeit sowie dem **delay**-Wert gestartet. **delay** legt fest, wie lange der Trigger nach Ablauf der fälligen Zeit wartet, bevor er eine neue Ausführung auslöst. Bei **delay** wird nicht das Fenster **startTime** geändert. Ein **delay**-Wert von 00:10:00 impliziert beispielsweise eine Verzögerung von 10 Minuten. | Timespan<br/>(hh:mm:ss)  | Ein Zeitraumwert, wobei der Standardwert 00:00:00 ist. | Nein |
| **maxConcurrency** | Die Anzahl der gleichzeitigen Triggerausführungen, die für bereite Fenster ausgelöst werden. Dies gilt beispielsweise für das Abgleichen stündlicher Ausführungen für die gestrigen Ergebnisse in 24 Fenstern. Wenn **maxConcurrency** = 10, werden Triggerereignisse nur für die ersten 10 Fenster ausgelöst (00:00-01:00 – 09:00-10:00). Nachdem die ersten 10 ausgelösten Pipelineausführungen erfolgt sind, werden Triggerausführungen für die nächsten 10 Fenster (10:00-11:00 – 19:00-20:00) ausgelöst. Wenn Sie mit dem Beispiel **maxConcurrency** = 10 fortfahren, erfolgen 10 Pipelineausführungen, sobald 10 Fenster bereit sind. Wenn nur ein Fenster bereit ist, erfolgt nur eine Pipelineausführung. | Integer | Eine ganze Zahl zwischen 1 und 50. | Ja |
| **retryPolicy: Count** | Die Anzahl der Wiederholungen, bevor die Ausführung der Pipeline als „Failed“ markiert wird  | Integer | Eine ganze Zahl, wobei der Standardwert 0 ist (keine Wiederholungen) | Nein |
| **retryPolicy: intervalInSeconds** | Die Verzögerung zwischen den in Sekunden angegebenen Wiederholungsversuchen | Integer | Die Anzahl der Sekunden, wobei der Standardwert 30 ist | Nein |
| **dependsOn: type** | Der Typ von TumblingWindowTriggerReference. Erforderlich, wenn eine Abhängigkeit festgelegt ist. | String |  „TumblingWindowTriggerDependencyReference“, „SelfDependencyTumblingWindowTriggerReference“ | Nein |
| **dependsOn: size** | Die Größe des abhängigen rollierenden Fensters. | Timespan<br/>(hh:mm:ss)  | Ein positiver Zeitspannenwert, wobei der Standardwert die Fenstergröße des untergeordneten Triggers ist.  | Nein |
| **dependsOn: offset** | Der Offset des Abhängigkeitstriggers. | Timespan<br/>(hh:mm:ss) |  Ein Zeitspannenwert, der bei einer Selbstabhängigkeit negativ sein muss. Wenn kein Wert angegeben wird, ist das Fenster identisch mit dem Trigger. | Selbstabhängigkeit: Ja<br/>Sonstiges: Nein  |

> [!NOTE]
> Nach der Veröffentlichung eines Triggers für rollierende Fenster können **Intervall** und **Häufigkeit** nicht mehr bearbeitet werden.

### <a name="windowstart-and-windowend-system-variables"></a>Systemvariablen „WindowStart“ und „WindowEnd“

Sie können die Systemvariablen **WindowStart** und **WindowEnd** des Triggers für rollierende Fenster in Ihrer **Pipeline**-Definition (d.h. für einen Teil einer Abfrage) verwenden. Übergeben Sie die Systemvariablen als Parameter an Ihre Pipeline in der **Trigger**-Definition. Im folgenden Beispiel wird gezeigt, wie diese Variablen als Parameter übergeben werden:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Um die Systemvariablen **WindowStart** und **WindowEnd** in der Pipelinedefinition zu nutzen, verwenden Sie die Parameter „MyWindowStart“ und „MyWindowEnd“ entsprechend.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Ausführungsreihenfolge von Fenstern in einem Abgleichsszenario

Wenn „startTime“ für den Trigger in der Vergangenheit liegt, generiert der Trigger basierend auf der Formel „M=(CurrentTime - TriggerStartTime)/TumblingWindowSize“ parallel Ausführungen vom Typ „{M} backfill(past)“. Hierbei wird die Triggerparallelität berücksichtigt, bevor die zukünftigen Ausführungen erfolgen. Die Ausführungsreihenfolge für Fenster ist deterministisch, und die ältesten Intervalle werden zuerst ausgeführt. Dieses Verhalten kann derzeit nicht geändert werden.

### <a name="existing-triggerresource-elements"></a>Vorhandene TriggerResource-Elemente

Die folgenden Punkte gelten für Updates von vorhandenen **TriggerResource**-Elementen:

* Der Wert für das **frequency**-Element (bzw. die Fenstergröße) des Triggers und das **interval**-Element können nicht mehr geändert werden, nachdem der Trigger erstellt wurde. Dies ist für die richtige Funktionsweise von erneuten Ausführungen von triggerRun und für Abhängigkeitsauswertungen erforderlich.
* Wenn der Wert für das **endTime**-Element des Triggers geändert wird (durch Hinzufügen oder Aktualisieren), wird der Status der bereits verarbeiteten Fenster *nicht* zurückgesetzt. Der Trigger berücksichtigt den neuen **endTime**-Wert. Wenn der neue **endTime**-Wert vor den bereits ausgeführten Fenstern liegt, wird der Trigger angehalten. Andernfalls wird der Trigger angehalten, sobald der neue **endTime**-Wert erreicht wird.

### <a name="user-assigned-retries-of-pipelines"></a>Vom Benutzer zugewiesene Pipelinewiederholungen

Bei Pipelineausfällen kann der Trigger für ein rollierendes Fenster versuchen, die Ausführung der referenzierten Pipeline automatisch ohne Benutzereingriff zu wiederholen, indem die gleichen Eingabeparameter verwendet werden. Dies kann mit der Eigenschaft „retryPolicy“ in der Triggerdefinition angegeben werden.

### <a name="tumbling-window-trigger-dependency"></a>Triggerabhängigkeit für ein rollierendes Fenster

Wenn Sie sicherstellen möchten, dass ein Trigger für ein rollierendes Fenster erst nach der erfolgreichen Ausführung eines anderen Triggers für das rollierende Fenster ausgeführt wird, [erstellen Sie eine Triggerabhängigkeit für das rollierende Fenster](tumbling-window-trigger-dependency.md) in der Data Factory.

### <a name="cancel-tumbling-window-run"></a>Abbrechen der Ausführung eines rollierenden Fensters

Sie können Ausführungen für einen Trigger für rollierende Fenster abbrechen, wenn sich das Fenster im Zustand _Warten_, _Auf Abhängigkeit warten_ oder _Wird ausgeführt_ befindet.

* Wenn sich das Fenster im Zustand **Wird ausgeführt** befindet, brechen Sie die zugehörige _Pipelineausführung_ ab. Danach wird die Triggerausführung als _Abgebrochen_ markiert.
* Wenn sich das Fenster im Zustand **Warten** oder im Zustand **Auf Abhängigkeit warten** befindet, können Sie das Fenster über die Überwachung abbrechen:

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger.png" alt-text="Abbrechen eines Triggers für rollierende Fenster über die Seite „Überwachung“":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger-synapse.png" alt-text="Abbrechen eines Triggers für rollierende Fenster über die Seite „Überwachung“":::

---

Sie können ein abgebrochenes Fenster auch erneut ausführen. Bei der erneuten Ausführung werden die _letzten_ veröffentlichten Definitionen des Triggers verwendet, und Abhängigkeiten für das angegebene Fenster werden bei der erneuten Ausführung _erneut ausgewertet_.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger.png" alt-text="Erneute Ausführung eines Triggers für rollierende Fenster für zuvor abgebrochene Ausführungen":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger-synapse.png" alt-text="Erneute Ausführung eines Triggers für rollierende Fenster für zuvor abgebrochene Ausführungen":::

---

## <a name="sample-for-azure-powershell-and-azure-cli"></a>Beispiel für Azure PowerShell und Azure-Befehlszeilenschnittstelle

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

In diesem Abschnitt erfahren Sie, wie Sie mit Azure PowerShell einen Trigger erstellen, starten und überwachen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="prerequisites"></a>Voraussetzungen

- **Azure-Abonnement**. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. 

- **Azure PowerShell**. Befolgen Sie die Anweisungen unter [Installieren von Azure PowerShell unter Windows mit PowerShellGet](/powershell/azure/install-az-ps). 

- **Azure Data Factory** Führen Sie die Anleitungen in [Erstellen einer Azure Data Factory mithilfe von PowerShell](./quickstart-create-data-factory-powershell.md) aus, um eine Data Factory und eine Pipeline zu erstellen.

### <a name="sample-code"></a>Beispielcode

1. Erstellen Sie im Ordner „C:\ADFv2QuickStartPSH“ eine JSON-Datei mit dem Namen **MyTrigger.json** und dem folgenden Inhalt:

    > [!IMPORTANT]
    > Legen Sie vor dem Speichern der JSON-Datei den Wert des **startTime**-Elements auf die aktuelle UTC-Zeit fest. Legen Sie den Wert des **endTime**-Elements auf eine Stunde nach der aktuellen UTC-Zeit fest.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "endTime" : "2017-09-08T06:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Erstellen Sie mit dem Cmdlet [Set-AzDataFactoryV2Trigger](/powershell/module/az.datafactory/set-azdatafactoryv2trigger) einen Trigger:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Vergewissern Sie sich, dass der Status des Triggers **Beendet** lautet, indem Sie das Cmdlet [Get-AzDataFactoryV2Trigger](/powershell/module/az.datafactory/get-azdatafactoryv2trigger) verwenden:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Starten Sie den Trigger mithilfe des Cmdlets [Start-AzDataFactoryV2Trigger](/powershell/module/az.datafactory/start-azdatafactoryv2trigger):

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Vergewissern Sie sich, dass der Status des Triggers **Gestartet** lautet, indem Sie das Cmdlet [Get-AzDataFactoryV2Trigger](/powershell/module/az.datafactory/get-azdatafactoryv2trigger) verwenden:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Rufen Sie Triggerausführungen in Azure PowerShell mit dem Cmdlet [Get-AzDataFactoryV2TriggerRun](/powershell/module/az.datafactory/get-azdatafactoryv2triggerrun) ab. Führen Sie in regelmäßigen Abständen den folgenden Befehl aus, um die Informationen zu den Triggerausführungen abzurufen. Aktualisieren Sie die Werte **TriggerRunStartedAfter** und **TriggerRunStartedBefore** entsprechend den Werten in Ihrer Triggerdefinition:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

In diesem Abschnitt erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) einen Trigger erstellen, starten und überwachen können.

### <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Führen Sie die Anleitungen in [Erstellen einer Azure Data Factory mithilfe der Azure-Befehlszeilenschnittstelle](./quickstart-create-data-factory-azure-cli.md) aus, um eine Data Factory und eine Pipeline zu erstellen.

### <a name="sample-code"></a>Beispielcode

1. Erstellen Sie in Ihrem Arbeitsverzeichnis die JSON-Datei **MyTrigger.json** mit den Eigenschaften für den Trigger. Verwenden Sie für dieses Beispiel den folgenden Inhalt:

    > [!IMPORTANT]
    > Bevor Sie die JSON-Datei speichern, legen Sie den Wert von **referenceName** auf den Namen Ihrer Pipeline fest. Legen Sie den Wert des Elements **startTime** auf die aktuelle UTC-Zeit fest. Legen Sie den Wert des **endTime**-Elements auf eine Stunde nach der aktuellen UTC-Zeit fest.

    ```json
    {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-12-08T00:00:00Z",
          "endTime": "2017-12-08T01:00:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
    }
    ```

2. Erstellen Sie einen Trigger mit dem Befehl [az datafactory trigger create](/cli/azure/datafactory/trigger#az_datafactory_trigger_create):

    > [!IMPORTANT]
    > Ersetzen Sie `ResourceGroupName` bei diesem Schritt und allen nachfolgenden Schritten durch den Namen Ihrer Ressourcengruppe. Ersetzen Sie `DataFactoryName` durch den Namen Ihrer Data Factory.

    ```azurecli
    az datafactory trigger create --resource-group "ResourceGroupName" --factory-name "DataFactoryName"  --name "MyTrigger" --properties @MyTrigger.json  
    ```

3. Vergewissern Sie sich mithilfe des Befehls [az datafactory trigger show](/cli/azure/datafactory/trigger#az_datafactory_trigger_show), dass der Status des Triggers **Beendet** lautet:

    ```azurecli
    az datafactory trigger show --resource-group "ResourceGroupName" --factory-name "DataFactoryName" --name "MyTrigger" 
    ```

4. Starten Sie den Trigger mit dem Befehl [az datafactory trigger start](/cli/azure/datafactory/trigger#az_datafactory_trigger_start):

    ```azurecli
    az datafactory trigger start --resource-group "ResourceGroupName" --factory-name "DataFactoryName" --name "MyTrigger" 
    ```

5. Vergewissern Sie sich mithilfe des Befehls [az datafactory trigger show](/cli/azure/datafactory/trigger#az_datafactory_trigger_show), dass der Status des Triggers **Gestartet** lautet:

    ```azurecli
    az datafactory trigger show --resource-group "ResourceGroupName" --factory-name "DataFactoryName" --name "MyTrigger" 
    ```

6. Rufen Sie die Triggerausführungen in der Azure-Befehlszeilenschnittstelle mit dem Befehl [az datafactory trigger-run query-by-factory](/cli/azure/datafactory/trigger-run#az_datafactory_trigger_run_query_by_factory) ab. Führen Sie in regelmäßigen Abständen den folgenden Befehl aus, um die Informationen zu den Triggerausführungen abzurufen. Aktualisieren Sie die Werte **last-updated-after** und **last-updated-before**, damit sie den Werten in Ihrer Triggerdefinition entsprechen:

    ```azurecli
    az datafactory trigger-run query-by-factory --resource-group "ResourceGroupName" --factory-name "DataFactoryName" --filters operand="TriggerName" operator="Equals" values="MyTrigger" --last-updated-after "2017-12-08T00:00:00Z" --last-updated-before "2017-12-08T01:00:00Z"
    ```
---

Informationen zum Überwachen von Trigger- bzw. Pipelineausführungen im Azure-Portal finden Sie unter [Überwachen der Pipelineausführungen](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Nächste Schritte

* Detaillierte Informationen zu Triggern finden Sie unter [Pipelineausführung und -trigger](concepts-pipeline-execution-triggers.md#trigger-execution).
* [Erstellen einer Triggerabhängigkeit für ein rollierendes Fenster](tumbling-window-trigger-dependency.md)
* Informationen zum Verweisen auf Triggermetadaten in der Pipeline finden Sie unter [Verweisen auf Triggermetadaten in Pipelineausführungen](how-to-use-trigger-parameterization.md).