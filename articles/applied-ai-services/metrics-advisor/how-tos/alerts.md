---
title: Konfigurieren von Metrics Advisor-Warnungen
titleSuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie Ihre Metrics Advisor-Warnungen konfigurieren, indem Sie Hooks für E-Mail, Web und Azure DevOps verwenden.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: f63ea23cce0ab43dd5c9e0864619ff32d5dae50d
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341682"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Gewusst wie: Konfigurieren von Warnungen und Erhalten von Benachrichtigungen per Hook

Nachdem mit Metrics Advisor eine Anomalie erkannt wurde, wird basierend auf den Warnungseinstellungen über einen Hook eine Warnungsbenachrichtigung ausgelöst. Eine Warnungseinstellung kann mit unterschiedlichen Konfigurationen für die Erkennung verwendet werden, und es sind verschiedene Parameter für die Anpassung Ihrer Warnungsregel verfügbar.

## <a name="create-a-hook"></a>Erstellen eines Hooks

Metrics Advisor unterstützt vier Arten von Hooks: E-Mail, Teams, Webhook und Azure DevOps. Sie können das Verfahren wählen, das für Ihr jeweiliges Szenario am besten geeignet ist.      

### <a name="email-hook"></a>E-Mail-Hook

> [!Note]
> Metrics Advisor-Ressourcenadministratoren müssen die E-Mail-Einstellungen konfigurieren und **SMTP-bezogene Informationen** in Metrics Advisor eingeben, bevor Anomaliewarnungen gesendet werden können. Der Administrator der Ressourcengruppe oder der Abonnementadministrator muss mindestens eine Rolle des *Cognitive Services Metrics Advisor-Administrators* auf der Registerkarte zur Zugriffssteuerung der Metrics Advisor-Ressource zuweisen. [Erfahren Sie mehr über die Konfiguration der E-Mail-Einstellungen](../faq.yml#how-to-set-up-email-settings-and-enable-alerting-by-email-). 


Ein E-Mail-Hook ist der Kanal, über den Anomaliewarnungen an E-Mail-Adressen gesendet werden, die im Abschnitt **Email to** (E-Mail an) angegeben sind. Es werden zwei Arten von Warnungs-E-Mails versendet: Warnungen des Typs **Datenfeed nicht verfügbar** und **Incidentberichte**, die eine oder mehrere Anomalien enthalten. 

Für die Erstellung eines E-Mail-Hooks sind die folgenden Parameter verfügbar: 

|Parameter |BESCHREIBUNG  |
|---------|---------|
| name | Name des E-Mail-Hooks |
| Email to (E-Mail an)| E-Mail-Adressen zum Senden von Warnungen an|
| Externer Link | Optionales Feld, das eine angepasste Umleitung ermöglicht, z. B. für Hinweise zur Problembehandlung. |
| Titel der angepassten Anomaliewarnung | Die Titelvorlage unterstützt Folgendes: `${severity}`, `${alertSettingName}`, `${datafeedName}`, `${metricName}`, `${detectConfigName}`, `${timestamp}`, `${topDimension}`, `${incidentCount}`, `${anomalyCount}`

Wenn Sie **OK** auswählen, wird ein E-Mail-Hook erstellt. Sie können ihn für alle Warnungseinstellungen verwenden, um Anomaliewarnungen zu erhalten. Ausführliche Schritte finden Sie im Tutorial [Aktivieren von Anomaliebenachrichtigungen in Metrics Advisor](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp).

### <a name="teams-hook"></a>Teams-Hook

Ein Teams-Hook ist der Kanal für Anomaliewarnungen, die an einen Kanal in Microsoft Teams gesendet werden. Ein Teams-Hook wird über einen Connector für eingehende Webhooks implementiert. Möglicherweise müssen Sie einen Connector für eingehende Webhooks in Ihrem Teams-Zielkanal erstellen und eine zugehörige URL abrufen. Kehren Sie dann zu Ihrem Metrics Advisor-Arbeitsbereich zurück. 

Wählen Sie auf der linken Navigationsleiste die Registerkarte „Hooks“ und dann rechts oben auf der Seite die Schaltfläche „Hook erstellen“ aus. Wählen Sie den Hooktyp „Teams“ aus. Die folgenden Parameter werden bereitgestellt: 

|Parameter |BESCHREIBUNG  |
|---------|---------|
| Name | Name des Teams-Hook | 
| Connector-URL | Die URL, die soeben aus dem Connector für eingehende Webhooks kopiert wurde, der im Teams-Zielkanal erstellt wurde. |

Nachdem Sie **OK** ausgewählt haben, wird ein Teams-Hook erstellt. Sie können ihn in allen Warnungseinstellungen verwenden, um Warnungen zu Anomalien an den Teams-Zielkanal zu übermitteln. Ausführliche Schritte finden Sie im Tutorial [Aktivieren von Anomaliebenachrichtigungen in Metrics Advisor](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp).

### <a name="web-hook"></a>Webhook

Ein Webhook ist ein weiterer Benachrichtigungskanal, der einen vom Kunden bereitgestellten Endpunkt verwendet. Bei Anomalien, die in der Zeitreihe erkannt werden, erfolgt eine Benachrichtigung über einen Webhook. Es gibt mehrere Schritte zum Aktivieren eines Webhooks als Warnungsbenachrichtigungskanal in Metrics Advisor. 

**Schritt 1:**  Aktivieren der verwalteten Identität in Ihrer Metrics Advisor-Ressource

Eine systemseitig zugewiesene verwaltete Identität ist auf eine Ressource beschränkt und an den Lebenszyklus dieser Ressource gebunden. Sie können der verwalteten Identität mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) Berechtigungen erteilen. Die verwaltete Identität wird bei Azure AD authentifiziert, sodass Sie keine Anmeldeinformationen im Code speichern müssen. 

Wechseln Sie im Azure-Portal zur Ressource „Metrics Advisor“. Wählen Sie die Option „Identität“ aus, und legen Sie sie auf „Ein“ fest. Dann ist verwaltete Identität aktiviert. 

**Schritt 2:** Erstellen eines Webhooks im Metrics Advisor-Arbeitsbereich

Melden Sie sich bei Ihrem Arbeitsbereich an. Wählen Sie die Registerkarte „Hooks“ und dann die Schaltfläche „Hook erstellen“ aus. 


Zum Erstellen eines Webhooks müssen Sie die folgenden Informationen hinzufügen:

|Parameter |BESCHREIBUNG  |
|---------|---------|
|Endpunkt     | Die API-Adresse, die bei Auslösung einer Warnung aufgerufen werden soll. **MUSS HTTPS sein**.       |
|Benutzername und Kennwort | Dient zum Authentifizieren gegenüber der API-Adresse. Lassen Sie dies unverändert, falls keine Authentifizierung erforderlich ist.         |
|Header     | Benutzerdefinierte Header im API-Aufruf.        |
|Zertifikat-ID in Azure Key Vault| Wenn der Zugriff auf den Endpunkt mittels Zertifikat authentifiziert werden muss, muss das Zertifikat in Azure Key Vault gespeichert sein. Geben Sie die ID hier ein.

> [!Note]
> Wenn ein Webhook erstellt oder geändert wird, wird der Endpunkt testweise mit **einem leeren Anforderungstext** aufgerufen. Ihre API muss einen HTTP-Code im Bereich 2xx zurückgeben, um die erfolgreiche Überprüfung zu bestätigen.

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="Fenster für Webhookerstellung":::

- Anforderungsmethode ist **POST**
- Timeout 30s
- Wiederholen bei Fehler 5xx, sonstige Fehler ignorieren. Folgt nicht der Umleitungsanforderung 301/302.
- Anforderungstext: 
```
{
"value": [{
    "hookId": "b0f27e91-28cf-4aa2-aa66-ac0275df14dd",
    "alertType": "Anomaly",
    "alertInfo": {
        "anomalyAlertingConfigurationId": "1bc6052e-9a2a-430b-9cbd-80cd07a78c64",
        "alertId": "172536dbc00",
        "timestamp": "2020-05-27T00:00:00Z",
        "createdTime": "2020-05-29T10:04:45.590Z",
        "modifiedTime": "2020-05-29T10:04:45.590Z"
    },
    "callBackUrl": "https://kensho2-api.azurewebsites.net/alert/anomaly/configurations/1bc6052e-9a2a-430b-9cbd-80cd07a78c64/alerts/172536dbc00/incidents"
}]
}
```

**Schritt 3: (optional)** Speichern Sie Ihr Zertifikat in Azure Key Vault, und rufen Sie die ID wie erwähnt ab. Der Zugriff auf den Endpunkt muss mittels eines Zertifikats authentifiziert werden, das in Azure Key Vault gespeichert sein muss. 

- Lesen Sie [Festlegen und Abrufen eines Zertifikats aus Azure Key Vault über das Azure-Portal](../../../key-vault/certificates/quick-create-portal.md).
- Klicken Sie auf das Zertifikat, das Sie hinzugefügt haben. Anschließend können Sie die „Zertifikat-ID“ kopieren. 
- Wählen Sie dann „Zugriffsrichtlinien“ und „Zugriffsrichtlinie hinzufügen“ aus. Erteilen Sie für „Schlüsselberechtigungen“, „Geheimnisberechtigungen“ und „Zertifikatberechtigungen“ die Berechtigung „Abrufen“. Wählen Sie als Namen Ihrer Metrics Advisor-Ressource „Prinzipal“ aus. Wählen Sie auf der Seite „Zugriffsrichtlinien“ die Schaltflächen „Hinzufügen“ und „Speichern“ aus. 

**Schritt 4:** Empfangen von Anomaliebenachrichtigungen: Wenn eine Benachrichtigung über einen Webhook gepusht wird, können Sie Incidentdaten abrufen, indem Sie „callBackUrl“ in der Webhookanforderung aufrufen. Details zu dieser API:

-   [/alert/anomaly/configurations/{Konfiguratios-ID}/alerts/{Warnungs-ID}/incidents](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/operations/getIncidentsFromAlertByAnomalyAlertingConfiguration)

Durch die Verwendung von Webhooks und Azure Logic Apps ist es möglich, E-Mail-Benachrichtigungen zu versenden **, ohne einen SMTP-Server konfigurieren zu müssen**. Ausführliche Schritte finden Sie im Tutorial [Aktivieren von Anomaliebenachrichtigungen in Metrics Advisor](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp).

### <a name="azure-devops"></a>Azure DevOps

Metrics Advisor unterstützt auch die automatische Erstellung eines Arbeitselements in Azure DevOps, um bei Erkennung von Anomalien Probleme bzw. Fehler nachzuverfolgen. Alle Warnungen können über Azure DevOps-Hooks gesendet werden.

Zum Erstellen eines Azure DevOps-Hooks müssen Sie die unten angegebenen Informationen hinzufügen.

|Parameter |BESCHREIBUNG  |
|---------|---------|
| name | Ein Name für den Hook. |
| Organisation | Die Organisation, zu der Ihre DevOps-Instanz gehört. |
| Projekt | Das spezifische Projekt in DevOps. |
| Access Token |  Ein Token für die Authentifizierung bei DevOps. | 

> [!Note]
> Sie müssen Schreibberechtigungen gewähren, wenn Sie möchten, dass von Metrics Advisor Arbeitselemente basierend auf Anomaliewarnungen erstellt werden. Nach dem Erstellen der Hooks können Sie diese in Ihren gesamten Warnungseinstellungen verwenden. Verwalten Sie Ihre Hooks auf der Seite **Hook Settings** (Hookeinstellungen). 

## <a name="add-or-edit-alert-settings"></a>Hinzufügen oder Bearbeiten von Warnungseinstellungen

Navigieren Sie zur Seite mit den Metrikdetails, auf der Sie links unten den Abschnitt **Warnungseinstellungen** finden. Es sind alle Warnungseinstellungen aufgeführt, die zur ausgewählten Erkennungskonfiguration gehören. Wenn eine neue Erkennungskonfiguration erstellt wird, sind keine Warnungseinstellungen vorhanden, und es werden keine Warnungen gesendet.  
Sie können die Symbole **Hinzufügen**, **Bearbeiten** und **Löschen** verwenden, um Warnungseinstellungen zu ändern.

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Menüelement „Warnungseinstellungen“":::

Wählen Sie die Schaltfläche **Hinzufügen** oder **Bearbeiten** aus, um ein Fenster zum Hinzufügen bzw. Bearbeiten Ihrer Warnungseinstellungen anzuzeigen.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Hinzufügen oder Bearbeiten von Warnungseinstellungen":::

**Name der Warnungseinstellung**: der Name der Warnungseinstellung. Er wird im Titel der Benachrichtigungs-E-Mail angezeigt.

**Hooks**: Die Liste mit den Hooks, an die Warnungen gesendet werden sollen.

Der im obigen Screenshot gekennzeichnete Abschnitt enthält die Einstellungen für eine Erkennungskonfiguration. Sie können für die einzelnen Erkennungskonfigurationen unterschiedliche Warnungseinstellungen festlegen. Wählen Sie die Zielkonfiguration aus, indem Sie in diesem Fenster die dritte Dropdownliste verwenden. 

### <a name="filter-settings"></a>Filtereinstellungen 

Unten sind die Filtereinstellungen für eine Erkennungskonfiguration angegeben.

**Warnung für** umfasst vier Optionen zum Filtern von Anomalien:

* **Anomalies in all series** (Anomalien in allen Reihen): Alle Anomalien werden in die Warnung eingeschlossen.         
* **Anomalies in the series group** (Anomalien in der Reihengruppe): Filtert Reihen nach Dimensionswerten. Legen Sie bestimmte Werte für einige Dimensionen fest. Anomalien sind nur dann in der Warnung enthalten, wenn die Reihe mit dem angegebenen Wert übereinstimmt.       
* **Anomalies in favorite series** (Anomalien in bevorzugten Reihen): Nur die als Favorit gekennzeichneten Reihen sind in der Warnung enthalten.        |
* **Anomalies in top N of all series** (Oberste „n“ Anomalien aller Reihen): Bei diesem Filter geht es nur um die Reihe, deren Wert sich in der obersten N befindet. Metrics Advisor blickt auf vorherige Zeitstempel zurück und prüft, ob sich die Werte der Reihe bei diesen Zeitstempeln in den obersten N befinden. Wenn die Anzahl „in den obersten N“ größer als die angegebene Zahl ist, wird die Anomalie in eine Warnung eingeschlossen.        |

**Filter anomaly options (Anomalieoptionen filtern) ist ein weiterer Filter mit den folgenden Optionen**:

- **Schweregrad**: Die Anomalie wird nur dann eingeschlossen, wenn ihr Schweregrad innerhalb des angegebenen Bereichs liegt.
- **Zurückstellen**: Die Anzeige von Warnungen wird für die Anomalien der nächsten „n“ Punkte (Zeitraum) beendet, wenn diese im Rahmen einer Warnung ausgelöst werden.
    - **Typ der Zurückstellung**: Bei Festlegung auf **Series** (Reihe) wird bei einer ausgelösten Anomalie nur deren Reihe zurückgestellt. Bei **Metric** (Metrik) werden bei einer ausgelösten Anomalie alle Reihen der Metrik zurückgestellt.
    - **Snooze Number** (Anzahl für Zurückstellung): Gibt die Anzahl von Punkten (den Zeitraum) für die Zurückstellung an.
    - **Reset for non-successive** (Zurücksetzen, falls nicht aufeinanderfolgend):Wenn diese Option aktiviert ist, werden bei einer ausgelösten Anomalie nur die nächsten „N“ aufeinanderfolgenden Anomalien zurückgestellt. Falls es sich bei einem der folgenden Datenpunkte nicht um eine Anomalie handelt, wird die Zurückstellung ab diesem Punkt zurückgesetzt. Wenn diese Option nicht ausgewählt ist, werden bei einer ausgelösten Anomalie auch dann die nächsten „n“ Punkte (Zeitraum) zurückgestellt, wenn es sich bei den aufeinanderfolgenden Datenpunkten nicht um Anomalien handelt.
- **value** (Wert) (optional): Filtern Sie nach Wert. Nur bei Punktwerten, die die Bedingung erfüllen, wird die Anomalie berücksichtigt. Wenn Sie den entsprechenden Wert einer anderen Metrik verwenden, sollten die Dimensionsnamen der beiden Metriken konsistent sein.

Anomalien, die nicht herausgefiltert werden, werden als Teil einer Warnung gesendet.

### <a name="add-cross-metric-settings"></a>Hinzufügen von metrikübergreifenden Einstellungen

Wählen Sie auf der Seite mit den Warnungseinstellungen **+ Add cross-metric settings** (+ Metrikübergreifende Einstellungen hinzufügen) aus, um einen weiteren Abschnitt hinzuzufügen.

Die Auswahloption **Operator** stellt die logische Beziehung der einzelnen Abschnitte dar, um ermitteln zu können, ob jeweils eine Warnung gesendet wird.


|Operator  |Beschreibung  |
|---------|---------|
|UND     | Eine Warnung wird nur gesendet, wenn eine Reihe mit jedem Warnungsabschnitt übereinstimmt und es sich bei allen Datenpunkten um Anomalien handelt. Wenn die Metriken unterschiedliche Dimensionsnamen aufweisen, wird nie eine Warnung ausgelöst.         |
|oder     | Die Warnung wird gesendet, wenn mindestens ein Abschnitt Anomalien enthält.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Operator für Abschnitt mit mehreren Warnungseinstellungen":::

## <a name="next-steps"></a>Nächste Schritte

- [Anpassen der Anomalieerkennung anhand von Feedback](anomaly-feedback.md)
- [Diagnostizieren eines Incidents](diagnose-an-incident.md)
- [Konfigurieren von Metriken und Optimieren der Erkennungskonfiguration](configure-metrics.md)
