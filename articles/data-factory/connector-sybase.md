---
title: Kopieren von Daten aus Sybase
description: Erfahren Sie, wie Daten aus Sybase mithilfe einer Copy-Aktivität in einer Azure Data Factory- oder Synapse Analytics-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: d487a862c6079272c400db964d2a8e898d3121a6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820095"
---
# <a name="copy-data-from-sybase-using-azure-data-factory-or-synapse-analytics"></a>Kopieren von Daten aus Sybase mithilfe von Azure Data Factory oder Synapse Analytics
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-onprem-sybase-connector.md)
> * [Aktuelle Version](connector-sybase.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Copy-Aktivität in Azure Data Factory- oder Azure Synapse Analytics-Pipelines verwenden, um Daten aus einer Sybase-Datenbank zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der Sybase-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer Sybase-Datenbank in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Sybase-Connector unterstützt insbesondere Folgendes:

- SAP Sybase SQL Anywhere (ASA), **Version 16 und höher**.
- Kopieren von Dateien unter Verwendung der **Standard**- oder **Windows**-Authentifizierung

Sybase IQ und ASE werden nicht unterstützt. Stattdessen können Sie den generischen ODBC-Connector mit dem Sybase-Treiber verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie zum Verwenden dieses Sybase-Connectors diese Schritte aus:

- Richten Sie eine selbstgehostete Integration Runtime ein. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.
- Installieren Sie den [Datenanbieter für Sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 oder höher auf dem Computer mit der Integration Runtime.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-sybase-using-ui"></a>Erstellen eines verknüpften Diensts mit Sybase über die Benutzeroberfläche

Verwenden Sie die folgenden Schritte, um einen verknüpften Dienst mit Sybase auf der Azure-Portal Benutzeroberfläche zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Erstellen Sie einen neuen verknüpften Dienst mithilfe der Azure Data Factory-Benutzeroberfläche.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Erstellen Sie einen neuen verknüpften Dienst mithilfe der Azure Synapse Benutzeroberfläche.":::

2. Suchen Sie nach Sybase, und wählen Sie den Sybase-Connector aus.

   :::image type="content" source="media/connector-sybase/sybase-connector.png" alt-text="Wählen Sie den Sybase-Connector aus.":::    


1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung und erstellen Sie den neuen verknüpften Dienst.

   :::image type="content" source="media/connector-sybase/configure-sybase-linked-service.png" alt-text="Konfigurieren Sie einen verknüpften Dienst für Sybase.":::

## <a name="connector-configuration-details"></a>Details zur Connector-Konfiguration

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Sybase-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Sybase verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Sybase** | Ja |
| server | Name des Sybase-Servers. |Ja |
| database | Name der Sybase-Datenbank. |Ja |
| authenticationType | Typ der Authentifizierung für die Verbindung mit der Sybase-Datenbank.<br/>Zulässige Werte sind: **Standard** und **Windows** |Ja |
| username | Geben Sie einen Benutzernamen für das Herstellen der Verbindung mit der Sybase-Datenbank an. |Ja |
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. Markieren Sie dieses Feld als einen „SecureString“, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Eine selbstgehostete Integrationslaufzeit ist erforderlich, wie unter [Voraussetzungen](#prerequisites) erwähnt wird. |Ja |

**Beispiel:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Dataset „Sybase“ unterstützt werden.

Zum Kopieren von Daten aus Sybase werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **SybaseTable** | Ja |
| tableName | Name der Tabelle in der Sybase-Datenbank. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Wenn Sie das Datenset vom Typ `RelationalTable` verwenden, wird es weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Quelle „Sybase“ unterstützt werden.

### <a name="sybase-as-source"></a>Sybase als Quelle

Beim Kopieren von Daten aus Sybase werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **SybaseSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Wenn Sie eine Quelle vom Typ `RelationalSource` verwenden, wird sie weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

## <a name="data-type-mapping-for-sybase"></a>Datentypzuordnung für Sybase

Beim Kopieren von Daten aus Sybase werden die folgenden Zuordnungen von Sybase-Datentypen zu den vom Dienst intern verwendeten Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

Sybase unterstützt T-SQL-Typen. Informationen zu einer Tabellen mit Zuordnungen von SQL-Typen zu Zwischendatentypen des Diensts finden Sie im Abschnitt [Azure SQL-Datenbank-Connector: Datentypzuordnung](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database).

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).



## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quelles und Senken für die Kopieraktivität unterstützt werden, finden Sie in der Dokumentation für [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
