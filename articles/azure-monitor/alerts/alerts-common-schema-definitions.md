---
title: Warnungsschemadefinitionen in Azure Monitor
description: Informationen zu Definitionen des allgemeinen Warnungsschemas für Azure Monitor
author: ofirmanor
ms.topic: conceptual
ms.date: 07/20/2021
ms.openlocfilehash: 784106dab7b95f63957abe1de44dfbf5734dd5f4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744487"
---
# <a name="common-alert-schema-definitions"></a>Definitionen des allgemeinen Warnungsschemas

In diesem Artikel werden die [Definitionen des allgemeinen Warnungsschemas](./alerts-common-schema.md) für Azure Monitor beschrieben. Dazu gehören die Definitionen für Webhooks, Azur Logic Apps, Azure Functions und Azure Automation Runbooks. 

Jede Warnungsinstanz beschreibt die betroffene Ressource und die Ursache der Warnung. Diese Instanzen werden im allgemeinen Schema in den folgenden Abschnitten beschrieben:
* **Zusammenfassung**: Eine Gruppe standardisierter Felder (für alle Warnungstypen gleich), die beschreiben, auf welcher Ressource sich die Warnung befindet, sowie zusätzliche allgemeine Warnungsmetadaten (z.B. Schweregrad oder Beschreibung). Definitionen des Schweregrads finden Sie in der [Übersicht der Warnungen](alerts-overview.md#overview). 
* **Warnungskontext**: Eine Gruppe von Feldern, mit denen die Ursache der Warnung beschrieben wird. Die Felder variieren basierend auf dem Warnungstyp. Eine Metrikwarnung enthält im Warnungskontext beispielsweise Felder wie den Metriknamen und -wert, während eine Aktivitätsprotokollwarnung Informationen zum Ereignis enthält, von dem die Warnung generiert wurde. 

**Beispielnutzlast einer Warnung**
```json
{
  "schemaId": "azureMonitorCommonAlertSchema",
  "data": {
    "essentials": {
      "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
      "alertRule": "WCUS-R2-Gen2",
      "severity": "Sev3",
      "signalType": "Metric",
      "monitorCondition": "Resolved",
      "monitoringService": "Platform",
      "alertTargetIDs": [
        "/subscriptions/<subscription ID>/resourcegroups/pipelinealertrg/providers/microsoft.compute/virtualmachines/wcus-r2-gen2"
      ],
      "configurationItems": [
        "wcus-r2-gen2"
      ],
      "originAlertId": "3f2d4487-b0fc-4125-8bd5-7ad17384221e_PipeLineAlertRG_microsoft.insights_metricAlerts_WCUS-R2-Gen2_-117781227",
      "firedDateTime": "2019-03-22T13:58:24.3713213Z",
      "resolvedDateTime": "2019-03-22T14:03:16.2246313Z",
      "description": "",
      "essentialsVersion": "1.0",
      "alertContextVersion": "1.0"
    },
    "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 7.727
          }
        ]
      }
    }
  }
}
```

## <a name="essentials"></a>Essentials

| Feld | BESCHREIBUNG|
|:---|:---|
| alertId | Die eindeutige Ressourcen-ID, die die Warnungsinstanz identifiziert. |
| alertRule | Der Name der Warnungsregel, die die Warnungsinstanz generiert hat. |
| severity | Der Schweregrad der Warnung. Mögliche Werte: Sev0, Sev1, Sev2, Sev3 oder Sev4. |
| signalType | Identifiziert das Signal, für das die Warnungsregel definiert wurde. Mögliche Werte: Metrik, Protokoll oder Aktivitätsprotokoll. |
| monitorCondition | Wenn eine Warnung ausgelöst wird, wird die Überwachungsbedingung der Warnung auf **Ausgelöst** festgelegt. Wenn die zugrunde liegende Bedingung gelöscht wurde, die die Warnung ausgelöst hat, wird die Überwachungsbedingung auf **Behoben** festgelegt.   |
| monitoringService | Der Überwachungsdienst oder die Lösung, von dem bzw. der die Warnung generiert wurde. Die Felder für den Warnungskontext werden vom Überwachungsdienst vorgegeben. |
| alertTargetIds | Die Liste der Azure Resource Manager-IDs, die betroffene Ziele einer Warnung sind. Für eine Protokollwarnung, die in einem Log Analytics-Arbeitsbereich oder einer Application Insights-Instanz definiert ist, ist es der jeweilige Arbeitsbereich bzw. die jeweilige Anwendung. |
| configurationItems | Die Liste der von einer Warnung betroffenen Ressourcen. Die Konfigurationselemente können sich mitunter von den Warnungszielen unterscheiden, z. B. bei einer Metrik für ein Protokoll oder Protokollwarnungen, die in einem Log Analytics-Arbeitsbereich definiert sind (in diesem Fall sind die tatsächlichen Ressourcen, die die Telemetriedaten senden, die Konfigurationselemente und nicht der Arbeitsbereich). Dieses Feld wird von ITSM-Systemen verwendet, um Warnungen mit Ressourcen in einer Konfigurationsverwaltungsdatenbank (Configuration Management Database, CMDB) zu korrelieren. |
| originAlertId | Die ID der Warnungsinstanz, wie sie vom überwachenden Dienst generiert wird, der sie generiert. |
| firedDateTime | Das Datum und die Uhrzeit, zu der die Warnungsinstanz ausgelöst wurde, in koordinierter Weltzeit (UTC). |
| resolvedDateTime | Das Datum und die Uhrzeit, zu der die Überwachungsbedingung für die Warnungsinstanz auf **Behoben** festgelegt wurde, in UTC. Gilt derzeit nur für Metrikwarnungen.|
| description | Die Beschreibung entsprechend der Definition in der Warnungsregel. |
|essentialsVersion| Die Versionsnummer für den Abschnitt „essentials“.|
|alertContextVersion | Die Versionsnummer für den Abschnitt `alertContext`. |

**Beispielwerte**
```json
{
  "essentials": {
    "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
    "alertRule": "Contoso IT Metric Alert",
    "severity": "Sev3",
    "signalType": "Metric",
    "monitorCondition": "Fired",
    "monitoringService": "Platform",
    "alertTargetIDs": [
      "/subscriptions/<subscription ID>/resourceGroups/aimon-rg/providers/Microsoft.Insights/components/ai-orion-int-fe"
    ],
    "originAlertId": "74ff8faa0c79db6084969cf7c72b0710e51aec70b4f332c719ab5307227a984f",
    "firedDateTime": "2019-03-26T05:25:50.4994863Z",
    "description": "Test Metric alert",
    "essentialsVersion": "1.0",
    "alertContextVersion": "1.0"
  }
}
```

## <a name="alert-context"></a>Warnungskontext

### <a name="metric-alerts---static-threshold"></a>Metrikwarnungen – statischer Schwellwert

#### <a name="monitoringservice--platform"></a>`monitoringService` = `Platform`

**Beispielwerte**
```json
{
  "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 31.1105
          }
        ],
        "windowStartTime": "2019-03-22T13:40:03.064Z",
        "windowEndTime": "2019-03-22T13:45:03.064Z"
      }
    }
}
```

### <a name="metric-alerts---dynamic-threshold"></a>Metrikwarnungen – dynamischer Schwellwert

#### <a name="monitoringservice--platform"></a>`monitoringService` = `Platform`

**Beispielwerte**
```json
{
  "alertContext": {
      "properties": null,
      "conditionType": "DynamicThresholdCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "alertSensitivity": "High",
            "failingPeriods": {
              "numberOfEvaluationPeriods": 1,
              "minFailingPeriodsToAlert": 1
            },
            "ignoreDataBefore": null,
            "metricName": "Egress",
            "metricNamespace": "microsoft.storage/storageaccounts",
            "operator": "GreaterThan",
            "threshold": "47658",
            "timeAggregation": "Total",
            "dimensions": [],
            "metricValue": 50101
          }
        ],
        "windowStartTime": "2021-07-20T05:07:26.363Z",
        "windowEndTime": "2021-07-20T05:12:26.363Z"
      }
    }
}
```

### <a name="metric-alerts---availability-tests"></a>Metrikwarnungen – Verfügbarkeitstests

#### <a name="monitoringservice--platform"></a>`monitoringService` = `Platform`

**Beispielwerte**
```json
{
  "alertContext": {
      "properties": null,
      "conditionType": "WebtestLocationAvailabilityCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Failed Location",
            "metricNamespace": null,
            "operator": "GreaterThan",
            "threshold": "2",
            "timeAggregation": "Sum",
            "dimensions": [],
            "metricValue": 5,
            "webTestName": "myAvailabilityTest-myApplication"
          }
        ],
        "windowStartTime": "2019-03-22T13:40:03.064Z",
        "windowEndTime": "2019-03-22T13:45:03.064Z"
      }
    }
}
```

### <a name="log-alerts"></a>Protokollwarnungen

> [!NOTE]
> Für Protokollwarnungen mit benutzerdefiniertem E-Mail-Betreff und/oder benutzerdefinierter JSON-Nutzlast wird das E-Mail-Betreff- und/oder Nutzlastschema durch die Aktivierung des allgemeinen Schemas wie unten beschrieben zurückgesetzt. Das bedeutet: Wenn Sie eine benutzerdefinierte JSON-Nutzlast definieren möchten, kann der Webhook das allgemeine Warnungsschema nicht verwenden. Für Warnungen mit aktiviertem allgemeinem Schema gilt für die Größe ein oberer Grenzwert von 256 KB pro Warnung. Suchergebnisse werden nicht in die Nutzlast der Protokollwarnungen eingebettet, wenn sie bewirken, dass die Warnungsgröße diesen Schwellenwert überschreitet. Sie können dies feststellen, indem Sie das Flag `IncludedSearchResults` überprüfen. Wenn die Suchergebnisse nicht enthalten sind, sollten Sie unter Verwendung von `LinkToFilteredSearchResultsAPI` oder `LinkToSearchResultsAPI` mit der [Log Analytics-API](/rest/api/loganalytics/dataaccess/query/get) auf die Suchergebnisse zugreifen.

#### <a name="monitoringservice--log-analytics"></a>`monitoringService` = `Log Analytics`

**Beispielwerte**
```json
{
  "alertContext": {
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "3/22/2019 1:36:31 PM",
    "SearchIntervalEndtimeUtc": "3/22/2019 1:51:31 PM",
    "ResultCount": 2,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "SeverityDescription": "Warning",
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "SearchIntervalDurationMin": "15",
    "AffectedConfigurationItems": [
      "INC-Gen2Alert"
    ],
    "SearchIntervalInMinutes": "15",
    "Threshold": 10000,
    "Operator": "Less Than",
    "Dimensions": [
      {
        "name": "Computer",
        "value": "INC-Gen2Alert"
      }
    ],
    "SearchResults": {
      "tables": [
        {
          "name": "PrimaryResult",
          "columns": [
            {
              "name": "$table",
              "type": "string"
            },
            {
              "name": "Computer",
              "type": "string"
            },
            {
              "name": "TimeGenerated",
              "type": "datetime"
            }
          ],
          "rows": [
            [
              "Fabrikam",
              "33446677a",
              "2018-02-02T15:03:12.18Z"
            ],
            [
              "Contoso",
              "33445566b",
              "2018-02-02T15:16:53.932Z"
            ]
          ]
        }
      ]
    },
    "dataSources": [
      {
        "resourceId": "/subscriptions/a5ea55e2-7482-49ba-90b3-60e7496dd873/resourcegroups/test/providers/microsoft.operationalinsights/workspaces/test",
        "tables": [
          "Heartbeat"
        ]
      }
    ],
  "IncludedSearchResults": "True",
  "AlertType": "Metric measurement"
  }
}
```

#### <a name="monitoringservice--application-insights"></a>`monitoringService` = `Application Insights`

**Beispielwerte**
```json
{
  "alertContext": {
    "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
    "SearchIntervalStartTimeUtc": "3/22/2019 1:36:33 PM",
    "SearchIntervalEndtimeUtc": "3/22/2019 1:51:33 PM",
    "ResultCount": 2,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
    "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
    "SearchIntervalDurationMin": "15",
    "SearchIntervalInMinutes": "15",
    "Threshold": 10000.0,
    "Operator": "Less Than",
    "ApplicationId": "8e20151d-75b2-4d66-b965-153fb69d65a6",
    "Dimensions": [
      {
        "name": "IP",
        "value": "1.1.1.1"
      }
    ],
    "SearchResults": {
      "tables": [
        {
          "name": "PrimaryResult",
          "columns": [
            {
              "name": "$table",
              "type": "string"
            },
            {
              "name": "Id",
              "type": "string"
            },
            {
              "name": "Timestamp",
              "type": "datetime"
            }
          ],
          "rows": [
            [
              "Fabrikam",
              "33446677a",
              "2018-02-02T15:03:12.18Z"
            ],
            [
              "Contoso",
              "33445566b",
              "2018-02-02T15:16:53.932Z"
            ]
          ]
        }
      ],
      "dataSources": [
        {
          "resourceId": "/subscriptions/a5ea27e2-7482-49ba-90b3-52e7496dd873/resourcegroups/test/providers/microsoft.operationalinsights/workspaces/test",
          "tables": [
            "Heartbeat"
          ]
        }
      ]
    },
    "IncludedSearchResults": "True",
    "AlertType": "Metric measurement"
  }
}
```

#### <a name="monitoringservice--log-alerts-v2"></a>`monitoringService` = `Log Alerts V2`

> [!NOTE]
> Protokollwarnungsregeln der API-Version 2020-05-01 verwenden diesen Nutzdatentyp, der nur allgemeine Schemas unterstützt. Bei Verwendung dieser Version werden Suchergebnisse nicht in die Nutzdaten der Protokollwarnungen eingebettet. Verwenden Sie [Dimensionen](./alerts-unified-log.md#split-by-alert-dimensions), um Kontext für ausgelöste Warnungen bereitzustellen. Sie können auch `LinkToFilteredSearchResultsAPI` oder `LinkToSearchResultsAPI` verwenden, um mit der [Log Analytics-API](/rest/api/loganalytics/dataaccess/query/get) auf Abfrageergebnisse zuzugreifen. Wenn Sie die Ergebnisse einbetten müssen, verwenden Sie eine Logik-App mit den bereitgestellten Links, die benutzerdefinierte Nutzdaten generieren.

**Beispielwerte**
```json
{
  "alertContext": {
    "properties": null,
    "conditionType": "LogQueryCriteria",
    "condition": {
      "windowSize": "PT10M",
      "allOf": [
        {
          "searchQuery": "Heartbeat",
          "metricMeasure": null,
          "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
          "operator": "LowerThan",
          "threshold": "1",
          "timeAggregation": "Count",
          "dimensions": [
            {
              "name": "Computer",
              "value": "TestComputer"
            }
          ],
          "metricValue": 0.0,
          "failingPeriods": {
            "numberOfEvaluationPeriods": 1,
            "minFailingPeriodsToAlert": 1
          },
          "linkToSearchResultsUI": "https://portal.azure.com#@12345a-1234b-123c-123d-12345678e/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%212345a-1234b-123c-123d-12345678e%2FresourceGroups%2FContoso%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2FContoso%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
          "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12345a-1234b-123c-123d-12345678e/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%212345a-1234b-123c-123d-12345678e%2FresourceGroups%2FContoso%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2FContoso%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
          "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/Contoso/providers/Microsoft.Compute/virtualMachines/Contoso/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
          "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/Contoso/providers/Microsoft.Compute/virtualMachines/Contoso/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
        }
      ],
      "windowStartTime": "2020-07-07T13:54:34Z",
      "windowEndTime": "2020-07-09T13:54:34Z"
    }
  }
}
```

### <a name="activity-log-alerts"></a>Aktivitätsprotokollwarnungen

#### <a name="monitoringservice--activity-log---administrative"></a>`monitoringService` = `Activity Log - Administrative`

**Beispielwerte**
```json
{
  "alertContext": {
      "authorization": {
        "action": "Microsoft.Compute/virtualMachines/restart/action",
        "scope": "/subscriptions/<subscription ID>/resourceGroups/PipeLineAlertRG/providers/Microsoft.Compute/virtualMachines/WCUS-R2-ActLog"
      },
      "channels": "Operation",
      "claims": "{\"aud\":\"https://management.core.windows.net/\",\"iss\":\"https://sts.windows.net/12345a-1234b-123c-123d-12345678e/\",\"iat\":\"1553260826\",\"nbf\":\"1553260826\",\"exp\":\"1553264726\",\"aio\":\"42JgYNjdt+rr+3j/dx68v018XhuFAwA=\",\"appid\":\"e9a02282-074f-45cf-93b0-50568e0e7e50\",\"appidacr\":\"2\",\"http://schemas.microsoft.com/identity/claims/identityprovider\":\"https://sts.windows.net/12345a-1234b-123c-123d-12345678e/\",\"http://schemas.microsoft.com/identity/claims/objectidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.microsoft.com/identity/claims/tenantid\":\"12345a-1234b-123c-123d-12345678e\",\"uti\":\"v5wYC9t9ekuA2rkZSVZbAA\",\"ver\":\"1.0\"}",
      "caller": "9778283b-b94c-4ac6-8a41-d5b493d03aa3",
      "correlationId": "8ee9c32a-92a1-4a8f-989c-b0ba09292a91",
      "eventSource": "Administrative",
      "eventTimestamp": "2019-03-22T13:56:31.2917159+00:00",
      "eventDataId": "161fda7e-1cb4-4bc5-9c90-857c55a8f57b",
      "level": "Informational",
      "operationName": "Microsoft.Compute/virtualMachines/restart/action",
      "operationId": "310db69b-690f-436b-b740-6103ab6b0cba",
      "status": "Succeeded",
      "subStatus": "",
      "submissionTimestamp": "2019-03-22T13:56:54.067593+00:00"
    }
}
```

#### <a name="monitoringservice--activity-log---policy"></a>`monitoringService` = `Activity Log - Policy`

**Beispielwerte**
```json
{
  "alertContext": {
    "authorization": {
      "action": "Microsoft.Resources/checkPolicyCompliance/read",
      "scope": "/subscriptions/<GUID>"
    },
    "channels": "Operation",
    "claims": "{\"aud\":\"https://management.azure.com/\",\"iss\":\"https://sts.windows.net/<GUID>/\",\"iat\":\"1566711059\",\"nbf\":\"1566711059\",\"exp\":\"1566740159\",\"aio\":\"42FgYOhynHNw0scy3T/bL71+xLyqEwA=\",\"appid\":\"<GUID>\",\"appidacr\":\"2\",\"http://schemas.microsoft.com/identity/claims/identityprovider\":\"https://sts.windows.net/<GUID>/\",\"http://schemas.microsoft.com/identity/claims/objectidentifier\":\"<GUID>\",\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\":\"<GUID>\",\"http://schemas.microsoft.com/identity/claims/tenantid\":\"<GUID>\",\"uti\":\"Miy1GzoAG0Scu_l3m1aIAA\",\"ver\":\"1.0\"}",
    "caller": "<GUID>",
    "correlationId": "<GUID>",
    "eventSource": "Policy",
    "eventTimestamp": "2019-08-25T11:11:34.2269098+00:00",
    "eventDataId": "<GUID>",
    "level": "Warning",
    "operationName": "Microsoft.Authorization/policies/audit/action",
    "operationId": "<GUID>",
    "properties": {
      "isComplianceCheck": "True",
      "resourceLocation": "eastus2",
      "ancestors": "<GUID>",
      "policies": "[{\"policyDefinitionId\":\"/providers/Microsoft.Authorization/policyDefinitions/<GUID>/\",\"policySetDefinitionId\":\"/providers/Microsoft.Authorization/policySetDefinitions/<GUID>/\",\"policyDefinitionReferenceId\":\"vulnerabilityAssessmentMonitoring\",\"policySetDefinitionName\":\"<GUID>\",\"policyDefinitionName\":\"<GUID>\",\"policyDefinitionEffect\":\"AuditIfNotExists\",\"policyAssignmentId\":\"/subscriptions/<GUID>/providers/Microsoft.Authorization/policyAssignments/SecurityCenterBuiltIn/\",\"policyAssignmentName\":\"SecurityCenterBuiltIn\",\"policyAssignmentScope\":\"/subscriptions/<GUID>\",\"policyAssignmentSku\":{\"name\":\"A1\",\"tier\":\"Standard\"},\"policyAssignmentParameters\":{}}]"
    },
    "status": "Succeeded",
    "subStatus": "",
    "submissionTimestamp": "2019-08-25T11:12:46.1557298+00:00"
  }
}
```

#### <a name="monitoringservice--activity-log---autoscale"></a>`monitoringService` = `Activity Log - Autoscale`

**Beispielwerte**
```json
{
  "alertContext": {
    "channels": "Admin, Operation",
    "claims": "{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn\":\"Microsoft.Insights/autoscaleSettings\"}",
    "caller": "Microsoft.Insights/autoscaleSettings",
    "correlationId": "<GUID>",
    "eventSource": "Autoscale",
    "eventTimestamp": "2019-08-21T16:17:47.1551167+00:00",
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.Insights/AutoscaleSettings/Scaleup/Action",
    "operationId": "<GUID>",
    "properties": {
      "description": "The autoscale engine attempting to scale resource '/subscriptions/d<GUID>/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachineScaleSets/testVMSS' from 9 instances count to 10 instances count.",
      "resourceName": "/subscriptions/<GUID>/resourceGroups/voiceassistancedemo/providers/Microsoft.Compute/virtualMachineScaleSets/alexademo",
      "oldInstancesCount": "9",
      "newInstancesCount": "10",
      "activeAutoscaleProfile": "{\r\n  \"Name\": \"Auto created scale condition\",\r\n  \"Capacity\": {\r\n    \"Minimum\": \"1\",\r\n    \"Maximum\": \"10\",\r\n    \"Default\": \"1\"\r\n  },\r\n  \"Rules\": [\r\n    {\r\n      \"MetricTrigger\": {\r\n        \"Name\": \"Percentage CPU\",\r\n        \"Namespace\": \"microsoft.compute/virtualmachinescalesets\",\r\n        \"Resource\": \"/subscriptions/<GUID>/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachineScaleSets/testVMSS\",\r\n        \"ResourceLocation\": \"eastus\",\r\n        \"TimeGrain\": \"PT1M\",\r\n        \"Statistic\": \"Average\",\r\n        \"TimeWindow\": \"PT5M\",\r\n        \"TimeAggregation\": \"Average\",\r\n        \"Operator\": \"GreaterThan\",\r\n        \"Threshold\": 0.0,\r\n        \"Source\": \"/subscriptions/<GUID>/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachineScaleSets/testVMSS\",\r\n        \"MetricType\": \"MDM\",\r\n        \"Dimensions\": [],\r\n        \"DividePerInstance\": false\r\n      },\r\n      \"ScaleAction\": {\r\n        \"Direction\": \"Increase\",\r\n        \"Type\": \"ChangeCount\",\r\n        \"Value\": \"1\",\r\n        \"Cooldown\": \"PT1M\"\r\n      }\r\n    }\r\n  ]\r\n}",
      "lastScaleActionTime": "Wed, 21 Aug 2019 16:17:47 GMT"
    },
    "status": "Succeeded",
    "submissionTimestamp": "2019-08-21T16:17:47.2410185+00:00"
  }
}
```

#### <a name="monitoringservice--activity-log---security"></a>`monitoringService` = `Activity Log - Security`

**Beispielwerte**
```json
{
  "alertContext": {
    "channels": "Operation",
    "correlationId": "<GUID>",
    "eventSource": "Security",
    "eventTimestamp": "2019-08-26T08:34:14+00:00",
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.Security/locations/alerts/activate/action",
    "operationId": "<GUID>",
    "properties": {
      "threatStatus": "Quarantined",
      "category": "Virus",
      "threatID": "2147519003",
      "filePath": "C:\\AlertGeneration\\test.eicar",
      "protectionType": "Windows Defender",
      "actionTaken": "Blocked",
      "resourceType": "Virtual Machine",
      "severity": "Low",
      "compromisedEntity": "testVM",
      "remediationSteps": "[\"No user action is necessary\"]",
      "attackedResourceType": "Virtual Machine"
    },
    "status": "Active",
    "submissionTimestamp": "2019-08-26T09:28:58.3019107+00:00"
  }
}
```

#### <a name="monitoringservice--servicehealth"></a>`monitoringService` = `ServiceHealth`

**Beispielwerte**
```json
{
  "alertContext": {
    "authorization": null,
    "channels": 1,
    "claims": null,
    "caller": null,
    "correlationId": "f3cf2430-1ee3-4158-8e35-7a1d615acfc7",
    "eventSource": 2,
    "eventTimestamp": "2019-06-24T11:31:19.0312699+00:00",
    "httpRequest": null,
    "eventDataId": "<GUID>",
    "level": 3,
    "operationName": "Microsoft.ServiceHealth/maintenance/action",
    "operationId": "<GUID>",
    "properties": {
      "title": "Azure Synapse Analytics Scheduled Maintenance Pending",
      "service": "Azure Synapse Analytics",
      "region": "East US",
      "communication": "<MESSAGE>",
      "incidentType": "Maintenance",
      "trackingId": "<GUID>",
      "impactStartTime": "2019-06-26T04:00:00Z",
      "impactMitigationTime": "2019-06-26T12:00:00Z",
      "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"East US\"}],\"ServiceName\":\"Azure Synapse Analytics\"}]",
      "impactedServicesTableRows": "<tr>\r\n<td align='center' style='padding: 5px 10px; border-right:1px solid black; border-bottom:1px solid black'>Azure Synapse Analytics</td>\r\n<td align='center' style='padding: 5px 10px; border-bottom:1px solid black'>East US<br></td>\r\n</tr>\r\n",
      "defaultLanguageTitle": "Azure Synapse Analytics Scheduled Maintenance Pending",
      "defaultLanguageContent": "<MESSAGE>",
      "stage": "Planned",
      "communicationId": "<GUID>",
      "maintenanceId": "<GUID>",
      "isHIR": "false",
      "version": "0.1.1"
    },
    "status": "Active",
    "subStatus": null,
    "submissionTimestamp": "2019-06-24T11:31:31.7147357+00:00",
    "ResourceType": null
  }
}
```
#### <a name="monitoringservice--resource-health"></a>`monitoringService` = `Resource Health`

**Beispielwerte**
```json
{
  "alertContext": {
    "channels": "Admin, Operation",
    "correlationId": "<GUID>",
    "eventSource": "ResourceHealth",
    "eventTimestamp": "2019-06-24T15:42:54.074+00:00",
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
    "operationId": "<GUID>",
    "properties": {
      "title": "This virtual machine is stopping and deallocating as requested by an authorized user or process",
      "details": null,
      "currentHealthStatus": "Unavailable",
      "previousHealthStatus": "Available",
      "type": "Downtime",
      "cause": "UserInitiated"
    },
    "status": "Active",
    "submissionTimestamp": "2019-06-24T15:45:20.4488186+00:00"
  }
}
```


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [allgemeine Warnungsschema](./alerts-common-schema.md).
- Erfahren Sie, wie Sie eine [Logik-App erstellen, die das allgemeine Warnungsschema verwendet, um all Ihre Warnungen zu verarbeiten](./alerts-common-schema-integrations.md).
