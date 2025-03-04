---
title: Sichern von Azure-Datenträgern mit der Azure Data Protection-REST-API.
description: In diesem Artikel erfahren Sie, wie Sie Sicherungsvorgänge von Azure-Datenträgern mithilfe der REST-API konfigurieren, initiieren und verwalten.
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: 6050a941-89d7-4b27-9976-69898cc34cde
ms.openlocfilehash: e01a0e528c3274ac6ddefe000311e2db7523bb5e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264710"
---
# <a name="back-up-azure-disks-using-azure-data-protection-via-rest-api"></a>Sichern von Azure-Datenträgern mit Azure Data Protection über die REST-API

In diesem Artikel wird beschrieben, wie Sicherungen für Azure-Datenträger über die REST-API verwaltet werden.

Informationen zur regionalen Verfügbarkeit von Azure Disk Backup, zu unterstützten Szenarien und zu Einschränkungen finden Sie in der [Supportmatrix](disk-backup-support-matrix.md).

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen eines Sicherungstresors](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Erstellen einer Sicherungsrichtlinie für Datenträger](backup-azure-dataprotection-use-rest-api-create-update-disk-policy.md)

## <a name="configure-backup"></a>Konfigurieren der Sicherung

### <a name="key-entities-involved"></a>Wichtige involvierte Entitäten

Nach dem Erstellen des Tresors und der Richtlinie gibt es drei wichtige Punkte, die Sie für den Schutz eines Azure-Datenträgers beachten müssen.

#### <a name="disk-to-be-protected"></a>Zu schützender Datenträger

Notieren Sie sich die ARM-ID und den Speicherort des zu schützenden Datenträgers. Dies dient als Bezeichner des Datenträgers.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup"
```

#### <a name="snapshot-resource-group"></a>Ressourcengruppe für Momentaufnahme

Die Datenträgermomentaufnahmen werden in einer Ressourcengruppe innerhalb Ihres Abonnements gespeichert. Als Richtlinie empfehlen wir, eine dedizierte Ressourcengruppe als Momentaufnahmen-Datenspeicher für den Azure Backup-Dienst zu erstellen. Mit einer dedizierten Ressourcengruppe lassen sich die Zugriffsrechte auf die Ressourcengruppe beschränken, was Sicherheit gewährleistet und eine einfache Verwaltung der Sicherungsdaten ermöglicht. Notieren Sie sich die ARM-ID für die Ressourcengruppe, in der Sie die Datenträger-Momentaufnahmen platzieren möchten.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
```

#### <a name="backup-vault"></a>Sicherungstresor

Der Sicherungstresor erfordert Berechtigungen für den Datenträger, um Sicherungen zu ermöglichen. Die vom System zugewiesene verwaltete Identität des Tresors wird zum Zuweisen solcher Berechtigungen verwendet.

### <a name="assign-permissions"></a>Zuweisen von Berechtigungen

Sie müssen dem Tresor (dargestellt durch die Tresor-MSI) und dem relevanten Datenträger und/oder der Datenträgerressourcengruppe über die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) einige Berechtigungen zuweisen. Dazu können Sie das Azure-Portal oder die Befehlszeilenschnittstelle verwenden. Informationen zum Zuweisen verwandter Berechtigungen finden Sie unter [Voraussetzungen zum Konfigurieren der Sicherung verwalteter Datenträger](./backup-managed-disks-ps.md#assign-permissions).

### <a name="prepare-the-request-to-configure-backup"></a>Vorbereiten der Anforderung zum Konfigurieren der Sicherung

Sobald die entsprechenden Berechtigungen für den Tresor und den Datenträger festgelegt und der Tresor und die Richtlinie konfiguriert sind, kann die Anforderung zum Konfigurieren der Sicherung vorbereitet werden. Im Folgenden sehen Sie den Anforderungstext zum Konfigurieren der Sicherung für einen Azure-Datenträger. Die Azure Resource Manager-ID (ARM-ID) des Azure-Datenträgers und die zugehörigen Details werden im Abschnitt _datasourceinfo_ genannt, und die Richtlinieninformationen sind im Abschnitt _policyinfo_ enthalten, wo die Momentaufnahmeressourcengruppe als einer der Richtlinienparameter angegeben ist.

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      }
    },
    "objectType": "BackupInstance"
  }
}
```

### <a name="validate-the-request-to-configure-backup"></a>Überprüfen der Anforderung zum Konfigurieren der Sicherung

Verwenden Sie die [API zum Überprüfen auf Sicherung](/rest/api/dataprotection/backup-instances/validate-for-backup), um zu überprüfen, ob die Anforderung zum Konfigurieren der Sicherung erfolgreich ausgeführt wird. Sie können die Antwort verwenden, um die erforderlichen Voraussetzungen auszuführen, und dann die Konfiguration für die Sicherungsanforderung übermitteln.

Die Anforderung zum Überprüfen auf Sicherung ist ein _POST_-Vorgang, und der URI enthält die Parameter `{subscriptionId}`, `{vaultName}` und `{vaultresourceGroupName}`.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.DataProtection/backupVaults/{backupVaultName}/validateForBackup?api-version=2021-01-01
```

In unserem Beispiel ergibt die API Folgendes:

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/validateForBackup?api-version=2021-01-01
```

Der zuvor erstellte [Anforderungstext](#prepare-the-request-to-configure-backup) wird verwendet, um die Details des zu schützenden Azure Datenträgers anzugeben.

#### <a name="example-request-body"></a>Beispiel für Anforderungstext

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      }
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-for-backup-request-validation"></a>Antworten auf eine Anforderung zum Überprüfen auf Sicherung

Die Anforderung zum Überprüfen auf Sicherung ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Somit wird in diesem Vorgang ein anderer Vorgang erstellt, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |Typ  |BESCHREIBUNG  |
|---------|---------|---------|
|202 – Akzeptiert     |         |  Der Vorgang wird asynchron abgeschlossen.      |
|200 – OK     |   [OperationJobExtendedInfo](/rest/api/dataprotection/backup-instances/validate-for-backup#operationjobextendedinfo)      |     Akzeptiert    |
| Andere Statuscodes |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    Fehlerantwort mit Beschreibung des Grunds für den Fehler    |

##### <a name="example-responses-for-validate-backup-request"></a>Beispielantworten auf eine Anforderung zum Überprüfen auf Sicherung

###### <a name="error-response"></a>Fehlerantwort

Wenn der angegebene Datenträger bereits geschützt ist, wird die Antwort „HTTP 400 (Ungültige Anforderung)“ zurückgegeben, die angibt, dass der angegebene Datenträger in einem Sicherungstresor geschützt ist, und nennt Details.

```http
HTTP/1.1 400 BadRequest
Content-Length: 1012
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 0c99ff0f-6c26-4ec7-899f-205435e89894
x-ms-routing-request-id: CENTRALUSEUAP:20210830T142949Z:0be72802-02ad-485d-b91f-4aadd92c059c
Cache-Control: no-cache
Date: Mon, 30 Aug 2021 14:29:49 GMT
X-Powered-By: ASP.NET

{
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
          "recommendedAction": [
            "Delete the backup instance SharedDataDisk from the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault to re-protect the datasource in any other vault."
          ],
          "details": null,
          "code": "UserErrorDppDatasourceAlreadyProtected",
          "target": "",
          "innerError": null,
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "0c99ff0f-6c26-4ec7-899f-205435e89894"
          }
        }
      }
    ],
    "code": "UserErrorDppDatasourceAlreadyProtected",
    "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
    "target": null,
    "details": null
  }
}
```

###### <a name="tracking-response"></a>Nachverfolgen der Antwort

Wenn die Datenquelle nicht geschützt ist, fährt die API mit weiteren Überprüfungen fort und erstellt einen Nachverfolgungsvorgang.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 3e7cacb3-65cd-4b3c-8145-71fe90d57327
x-ms-routing-request-id: CENTRALUSEUAP:20210707T124850Z:105f2105-6db1-44bf-8a34-45972a8ba861
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:48:50 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Verfolgen Sie den resultierenden Vorgang mithilfe des _Azure-AsyncOperation_-Headers mit einem einfachen *GET*-Befehl nach.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Inprogress",
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "0001-01-01T00:00:00"
}
```

Nach Abschluss des Vorgangs wird „200 (OK)“ zurückgegeben, und im Antworttext werden weitere Anforderungen aufgeführt, die erfüllt werden müssen, z. B. Berechtigungen.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Failed",
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Appropriate permissions to perform the operation is missing.",
          "recommendedAction": [
            "Grant appropriate permissions to perform this operation as mentioned at https://aka.ms/UserErrorMissingRequiredPermissions and retry the operation."
          ],
          "code": "UserErrorMissingRequiredPermissions",
          "target": "",
          "innerError": {
            "code": "UserErrorMissingRequiredPermissions",
            "additionalInfo": {
              "DetailedNonLocalisedMessage": "Validate for Protection failed. Exception Message: The client 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' with object id 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup/providers/Microsoft.Authorization' or the scope is invalid. If access was recently granted, please refresh your credentials."
            }
          },
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "3e7cacb3-65cd-4b3c-8145-71fe90d57327"
          }
        }
      }
    ],
    "code": "UserErrorMissingRequiredPermissions",
    "message": "Appropriate permissions to perform the operation is missing."
  },
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "2021-07-07T12:49:22Z"
}
```

Wenn Sie alle Berechtigungen erteilen, übermitteln Sie die Überprüfungsanforderung erneut, und verfolgen Sie den resultierenden Vorgang nach. Es wird „200 (OK)“ (erfolgreich) zurückgegeben, wenn alle Bedingungen erfüllt sind.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "status": "Succeeded",
  "startTime": "2021-07-07T13:03:54.8627251Z",
  "endTime": "2021-07-07T13:04:06Z"
}
```

### <a name="configure-backup-request"></a>Anforderung zum Konfigurieren der Sicherung

Nachdem die Anforderung überprüft wurde, können Sie sie an die [API zum Erstellen einer Sicherungsinstanz](/rest/api/dataprotection/backup-instances/create-or-update) übermitteln. Eine Sicherungsinstanz stellt ein Element dar, das mit dem Datenschutzdienst von Azure Backup innerhalb des Sicherungstresors geschützt ist. Hier ist der Azure-Datenträger die Sicherungsinstanz, und Sie können denselben Anforderungstext, der oben überprüft wurde, mit geringfügigen Ergänzungen verwenden.

Verwenden Sie einen eindeutigen Namen für die Sicherungsinstanz. Wir empfehlen Ihnen daher, eine Kombination aus dem Ressourcennamen und einem eindeutigen Bezeichner zu verwenden. Im folgenden Vorgang verwenden wir beispielsweise _msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed_ und markieren es als Namen der Sicherungsinstanz.

Verwenden Sie den folgenden ***PUT***-Vorgang, um eine Sicherungsinstanz zu erstellen oder zu aktualisieren.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/{BkpvaultName}/backupInstances/{UniqueBackupInstanceName}?api-version=2021-01-01
```

In unserem Beispiel ergibt die API Folgendes:

```http
 PUT https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed?api-version=2021-01-01
```

#### <a name="create-the-request-for-configure-backup"></a>Erstellen der Anforderung zum Konfigurieren der Sicherung

Zum Erstellen einer Sicherungsinstanz werden die folgenden Komponenten des Anforderungstexts verwendet:

|Name  |Typ  |BESCHREIBUNG  |
|---------|---------|---------|
|properties     |  [BackupInstance](/rest/api/dataprotection/backup-instances/create-or-update#backupinstance)       |     BackupInstanceResource-Eigenschaften    |

##### <a name="example-request-for-configure-backup"></a>Beispielanforderung zum Konfigurieren der Sicherung

Es wird derselbe Anforderungstext wie zum Überprüfen der Sicherungsanforderung verwendet, jedoch mit einem eindeutigen Namen, wie [oben](#configure-backup) erwähnt.

```json
{
  "name": "msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
  "type": "Microsoft.DataProtection/backupvaults/backupInstances",
  "properties": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      }
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-to-configure-backup-request"></a>Antworten auf eine Anforderung zum Konfigurieren der Sicherung

Die _Anforderung zum Erstellen einer Sicherungsinstanz_ ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Somit wird in diesem Vorgang ein anderer Vorgang erstellt, der separat nachverfolgt werden muss.

Es werden zwei Antworten zurückgegeben: „201 (Erstellt)“, wenn die Sicherungsinstanz erstellt wurde und der Schutz konfiguriert wird, und anschließend „200 (OK)“, wenn diese Konfiguration abgeschlossen ist.

|Name  |Typ  |BESCHREIBUNG  |
|---------|---------|---------|
|201 – Erstellt   |   [Sicherungsinstanz](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)      |  Die Sicherungsinstanz wurde erstellt, und der Schutz wird konfiguriert.      |
|200 – OK     |    [Sicherungsinstanz](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)     |     Der Schutz ist konfiguriert.    |
| Andere Statuscodes |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    Fehlerantwort mit Beschreibung des Grunds für den Fehler    |

##### <a name="example-responses-to-configure-backup-request"></a>Beispielantworten auf eine Anforderung zum Konfigurieren der Sicherung

Nachdem Sie die *PUT*-Anforderung zum Erstellen einer Sicherungsinstanz übermittelt haben, lautet die erste Antwort „201 (Erstellt)“ mit einem Azure-asyncOperation-Header. Beachten Sie, dass der Anforderungstext alle Eigenschaften der Sicherungsinstanz enthält.

```http
HTTP/1.1 201 Created
Content-Length: 1149
Content-Type: application/json
Expires: -1
Pragma: no-cache
Retry-After: 15
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 5d9ccf1b-7ac1-456d-8ae3-36c93c0d2427
x-ms-routing-request-id: CENTRALUSEUAP:20210707T170219Z:9e897266-5d86-4d13-b298-6561c60cf043
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 17:02:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
  "name": "msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances",
  "properties": {
    "friendlyName": "msdiskbackup",
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      },
    "protectionStatus": {
      "status": "ConfiguringProtection"
    },
    "currentProtectionState": "ConfiguringProtection",
    "provisioningState": "Provisioning",
    "objectType": "BackupInstance"
  }
}
```

Verfolgen Sie dann den resultierenden Vorgang mithilfe des _Azure-AsyncOperation_-Headers mit einem einfachen *GET*-Befehl nach.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
```

Sobald der Vorgang abgeschlossen ist, wird „200 (OK)“ mit der Erfolgsmeldung im Antworttext zurückgegeben.

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "status": "Succeeded",
  "startTime": "2021-07-07T17:02:19.0611871Z",
  "endTime": "2021-07-07T17:02:20Z"
}
```

### <a name="stop-protection-and-delete-data"></a>Beenden des Schutzes und Löschen der Daten

Um den Schutz für einen Azure-Datenträger zu entfernen und auch die Sicherungsdaten zu löschen, führen Sie einen [Löschvorgang](/rest/api/dataprotection/backup-instances/delete) durch.

Das Beenden des Schutzes und das Löschen der Daten ist ein *DELETE*-Vorgang.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}?api-version=2021-01-01
```

In unserem Beispiel ergibt die API Folgendes:

```http
DELETE "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed?api-version=2021-01-01"
```

#### <a name="responses-for-delete-protection"></a>Antworten auf einen Vorgang zum Beenden des Schutzes und Löschen der Daten

Der *DELETE*-Vorgang für den Schutz ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Somit wird in diesem Vorgang ein anderer Vorgang erstellt, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |Typ  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |         |  Status der Löschanforderung       |
|202 – Akzeptiert     |         |     Zulässig    |

##### <a name="example-responses-for-delete-protection"></a>Beispielantworten auf einen Vorgang zum Beenden des Schutzes und Löschen der Daten

Sobald Sie die *DELETE*-Anforderung übermittelt haben, lautet die erste Antwort „202 (akzeptiert) mit einem Azure-asyncOperation-Header.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-deletes: 14999
x-ms-correlation-request-id: fee7a361-b1b3-496d-b398-60fed030d5a7
x-ms-routing-request-id: CENTRALUSEUAP:20210708T071330Z:5c3a9f3e-53aa-4d5d-bf9a-20de5601b090
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 07:13:29 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Verfolgen Sie den _Azure-AsyncOperation_-Header mit einer einfachen *GET*-Anforderung nach. Wenn die Anforderung erfolgreich ist, wird „200 OK“ mit einem Erfolgsstatus als Antwort zurückgegeben.

```http
GET "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01"

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "status": "Succeeded",
  "startTime": "2021-07-08T07:13:30.23815Z",
  "endTime": "2021-07-08T07:13:46Z"
}
```

## <a name="next-steps"></a>Nächste Schritte

[Wiederherstellen von Daten von einem Azure-Datenträger](backup-azure-arm-userestapi-restoreazurevms.md)

Weitere Informationen zu den Azure Backup-REST-APIs finden Sie in den folgenden Artikeln:

- [REST-API des Azure-Datenschutzanbieters](/rest/api/dataprotection/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)