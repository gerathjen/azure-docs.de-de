---
title: Back-End-Integrität und Diagnoseprotokolle
titleSuffix: Azure Application Gateway
description: Erfahren Sie, wie Sie Zugriffs- und Leistungsprotokolle für Azure Application Gateway aktivieren und verwalten.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 10/18/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2ed550d36dca7e9a7043dfca91e468527982164
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138085"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>Back-End-Integrität und Diagnoseprotokolle für Application Gateway

Sie können Azure Application Gateway-Ressourcen auf die folgenden Arten überwachen:

* [Back-End-Integrität:](#back-end-health) Application Gateway bietet über das Azure-Portal sowie mithilfe von PowerShell die Möglichkeit zur Überwachung der Integrität der Server in den Back-End-Pools. Sie können die Integrität der Back-End-Pools auch über die Leistungsdiagnoseprotokolle ermitteln.

* [Protokolle:](#diagnostic-logging) Protokolle ermöglichen das Speichern und Nutzen von Leistungs-, Zugriffs- und anderen Daten einer Ressource zu Überwachungszwecken.

* [Metriken](application-gateway-metrics.md): Application Gateway verfügt über mehrere Metriken, mit denen Sie sicherstellen können, dass das System die erwartete Leistung aufweist.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Back-End-Integrität

Application Gateway bietet über das Portal sowie mithilfe von PowerShell oder die Befehlszeilenschnittstelle (CLI) die Möglichkeit zur Überwachung der Integrität einzelner Mitglieder der Back-End-Pools. Eine Zusammenfassung der Integrität von Back-End-Pools können Sie auch über die Leistungsdiagnoseprotokolle ermitteln.

Der Back-End-Integritätsbericht spiegelt die Ausgabe des Application Gateway-Integritätstests an die Back-End-Instanzen wider. Wenn der Testvorgang erfolgreich ist und das Back-End Datenverkehr empfangen kann, wird es als fehlerfrei angesehen. Andernfalls wird es als nicht fehlerfrei eingestuft.

> [!IMPORTANT]
> Wenn sich eine Netzwerksicherheitsgruppe (NSG) in einem Application Gateway-Subnetz befindet, sollten Sie die Portbereiche 65503 bis 65534 für v1-SKUs und 65200 bis 65535 für v2-SKUs im Application Gateway-Subnetz für eingehenden Datenverkehr öffnen. Dieser Portbereich ist für die Kommunikation mit der Azure-Infrastruktur erforderlich. Sie werden von Azure-Zertifikaten geschützt (gesperrt). Ohne entsprechende Zertifikate können externe Entitäten, einschließlich der Kunden dieser Gateways, keine Änderungen an diesen Endpunkten vornehmen.


### <a name="view-back-end-health-through-the-portal"></a>Anzeigen der Back-End-Integrität über das Portal

Im Portal wird die Back-End-Integrität automatisch bereitgestellt. Wählen Sie in einem vorhandenen Anwendungsgateway die Option **Überwachung** > **Back-End-Integrität**.

Jedes Mitglied im Back-End-Pool (ob NIC, IP-Adresse oder FQDN) ist auf dieser Seite aufgeführt. Der Name des Back-End-Pools, der Port, der Name der Back-End-HTTP-Einstellungen und der Integritätsstatus werden angezeigt. Gültige Werte für den Integritätsstatus sind **Fehlerfrei**, **Fehlerhaft** und **Unbekannt**.

> [!NOTE]
> Stellen Sie beim Back-End-Integritätsstatus **Unbekannt** sicher, dass der Zugriff auf das Back-End nicht durch eine NSG-Regel, eine benutzerdefinierte Route (UDR) oder ein benutzerdefiniertes DNS im virtuellen Netzwerk blockiert ist.

![Back-End-Integrität][10]

### <a name="view-back-end-health-through-powershell"></a>Anzeigen der Back-End-Integrität mit PowerShell

Der folgende PowerShell-Code veranschaulicht, wie Sie die Back-End-Integrität mit dem `Get-AzApplicationGatewayBackendHealth`-Cmdlet anzeigen:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Anzeigen der Back-End-Integrität mit der Azure CLI

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Ergebnisse

Der folgende Codeausschnitt enthält ein Beispiel für die Antwort:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnoseprotokolle

Sie können in Azure verschiedene Protokolltypen verwenden, um Anwendungsgateways zu verwalten und eventuelle Fehler zu beheben. Sie können auf einige dieser Protokolle über das Portal zugreifen. Alle Protokolle können aus Azure Blob Storage extrahiert und in anderen Tools wie [Azure Monitor-Protokollen](../azure-monitor/insights/azure-networking-analytics.md), Excel und Power BI angezeigt werden. In der folgenden Liste finden Sie weitere Informationen über die verschiedenen Typen von Protokollen:

* **Aktivitätsprotokoll:** Mit dem Feature [Azure-Aktivitätsprotokolle](../azure-monitor/essentials/activity-log.md) (ehemals Betriebs- und Überwachungsprotokolle) können Sie sämtliche an Ihr Azure-Abonnement übermittelten Vorgänge sowie deren Status anzeigen. Aktivitätsprotokolleinträge werden standardmäßig gesammelt und können im Azure-Portal angezeigt werden.
* **Zugriffsprotokoll:** Mithilfe dieses Protokolls können Sie Application Gateway-Zugriffsmuster anzeigen und wichtige Informationen analysieren. Dazu gehören die IP des Aufrufers, die angeforderte URL, die Antwortlatenz, der Rückgabecode sowie die ein- und ausgehenden Bytes. Ein Zugriffsprotokoll wird alle 60 Sekunden erstellt. Dieses Protokoll enthält einen Datensatz pro Instanz von Application Gateway. Die Application Gateway-Instanz wird anhand der InstanceId-Eigenschaft identifiziert.
* **Leistungsprotokoll:** Mithilfe dieses Protokolls können Sie die Leistung von Application Gateway-Instanzen anzeigen. In diesem Protokoll werden Leistungsinformationen für jede Instanz erfasst, z.B. insgesamt bereitgestellte Anforderungen, Durchsatz in Byte, Anzahl von Anforderungen mit Fehlern und die Anzahl von fehlerfreien und fehlerhaften Back-End-Instanzen. Ein Leistungsprotokoll wird alle 60 Sekunden erstellt. Das Leistungsprotokoll ist nur für die v1-SKU verfügbar. Verwenden Sie für die v2-SKU [Metriken](application-gateway-metrics.md) für die Leistungsdaten.
* **Firewallprotokoll:** Mithilfe dieses Protokolls können Sie die Anforderungen anzeigen, die entweder über den Erkennungs- oder über den Schutzmodus eines Anwendungsgateways protokolliert werden, das mit der Web Application Firewall konfiguriert wurde. Firewallprotokolle werden alle 60 Sekunden erfasst. 

> [!NOTE]
> Protokolle sind nur für Ressourcen verfügbar, die über das Azure Resource Manager-Bereitstellungsmodell bereitgestellt werden. Sie können Protokolle nicht für Ressourcen im klassischen Bereitstellungsmodell verwenden. Ein besseres Verständnis der beiden Modelle können Sie entwickeln, indem Sie den Artikel [Grundlegendes zur Bereitstellung über den Resource Manager im Vergleich zur klassischen Bereitstellung](../azure-resource-manager/management/deployment-models.md) lesen.

Sie haben drei Möglichkeiten, um Ihre Protokolle zu speichern:

* **Speicherkonto**: Speicherkonten eignen sich am besten für Protokolle, die eine längere Zeit gespeichert und bei Bedarf überprüft werden.
* **Event Hubs:** Event Hubs-Instanzen sind eine hervorragende Möglichkeit für die Integration in andere SIEM-Tools (Security Information and Event Management), um Warnungen für Ihre Ressourcen zu erhalten.
* **Azure Monitor-Protokolle:** Azure Monitor-Protokolle eignen sich am besten für eine allgemeine Echtzeitüberwachung Ihrer Anwendung oder zum Beobachten von Trends.

### <a name="enable-logging-through-powershell"></a>Ermöglichen der Protokollierung mit PowerShell

Die Aktivitätsprotokollierung ist automatisch für alle Resource Manager-Ressourcen aktiviert. Sie müssen die Zugriffs- und Leistungsprotokollierung aktivieren, um mit der Erfassung von Daten aus diesen Protokollen zu beginnen. Führen Sie zum Aktivieren der Protokollierung die folgenden Schritte aus:

1. Notieren Sie sich die Ressourcen-ID Ihres Speicherkontos, unter dem die Protokolldaten gespeichert werden. Dieser Wert hat das folgende Format: /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>. Sie können jedes Speicherkonto Ihres Abonnements verwenden. Sie können das Azure-Portal verwenden, um nach diesen Informationen zu suchen.

    ![Portal: Ressourcen-ID für Speicherkonto](./media/application-gateway-diagnostics/diagnostics1.png)

2. Notieren Sie sich die Ressourcen-ID Ihres Anwendungsgateways, für das die Protokollierung aktiviert ist. Dieser Wert hat das folgende Format: /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/applicationGateways/\<application gateway name\>. Sie können das Portal verwenden, um nach diesen Informationen zu suchen.

    ![Portal: Ressourcen-ID für das Anwendungsgateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Aktivieren Sie die Diagnoseprotokollierung mit dem folgenden PowerShell-Cmdlet:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Für Aktivitätsprotokolle ist kein separates Speicherkonto erforderlich. Für die Nutzung des Speichers für die Zugriffs- und Leistungsprotokollierung fallen Dienstgebühren an.

### <a name="enable-logging-through-the-azure-portal"></a>Ermöglichen der Protokollierung über das Azure-Portal

1. Suchen Sie im Azure-Portal nach Ihrer Ressource, und wählen Sie **Diagnoseeinstellungen** aus.

   Für Application Gateway sind drei Protokolle verfügbar:

   * Zugriffsprotokoll
   * Leistungsprotokoll
   * Firewallprotokoll

2. Wählen Sie **Diagnose aktivieren** aus, um die Erfassung von Daten zu starten.

   ![Aktivieren der Diagnose][1]

3. Auf der Seite **Diagnoseeinstellungen** befinden sich die Einstellungen für die Diagnoseprotokolle. In diesem Beispiel werden die Protokolle in Log Analytics gespeichert. Sie können auch Event Hubs und ein Speicherkonto verwenden, um die Diagnoseprotokolle zu speichern.

   ![Starten des Konfigurationsprozesses][2]

5. Geben Sie einen Namen für die Einstellungen ein, bestätigen Sie die Einstellungen, und wählen Sie **Speichern** aus.

### <a name="activity-log"></a>Aktivitätsprotokoll

Das Aktivitätsprotokoll wird von Azure standardmäßig generiert. Die Protokolle werden 90 Tage lang im Azure-Ereignisprotokollspeicher aufbewahrt. Weitere Informationen zu diesen Protokollen finden Sie im Artikel [Anzeigen von Ereignis- und Aktivitätsprotokollen](../azure-monitor/essentials/activity-log.md).

### <a name="access-log"></a>Zugriffsprotokoll

Das Zugriffsprotokoll wird nur generiert, wenn Sie es auf jeder Application Gateway-Instanz gemäß den obigen Schritten aktiviert haben. Die Daten werden in dem Speicherkonto gespeichert, das Sie beim Aktivieren der Protokollierung angegeben haben. Jeder Application Gateway-Zugriff wird wie unten gezeigt im JSON-Format protokolliert. 

#### <a name="for-application-gateway-standard-and-waf-sku-v1"></a>Für die SKU Application Gateway Standard und WAF (v1)

|Wert  |BESCHREIBUNG  |
|---------|---------|
|instanceId     | Application Gateway-Instanz, von der die Anforderung bereitgestellt wurde        |
|clientIP     | Ursprungs-IP für die Anforderung        |
|clientPort     | Ursprungsport für die Anforderung       |
|httpMethod     | Von der Anforderung verwendete HTTP-Methode       |
|requestUri     | URI der empfangenen Anforderung        |
|RequestQuery     | **Server-Routed:** Back-End-Poolinstanz, an die die Anforderung gesendet wurde.</br>**X-AzureApplicationGateway-LOG-ID:** Korrelations-ID, die für die Anforderung verwendet wurde. Kann für die Behandlung von Datenverkehrsproblemen auf den Back-End-Servern verwendet werden. </br>**SERVER-STATUS:** HTTP-Antwortcode, den Application Gateway vom Back-End empfangen hat.       |
|UserAgent     | Benutzer-Agent aus dem HTTP-Anforderungsheader        |
|httpStatus     | HTTP-Statuscode, der vom Application Gateway an den Client zurückgegeben wurde       |
|httpVersion     | HTTP-Version der Anforderung        |
|receivedBytes     | Größe des empfangenen Pakets in Byte        |
|sentBytes| Größe des gesendeten Pakets in Byte|
|timeTaken| Dauer (in Millisekunden), bis eine Anforderung verarbeitet und die dazugehörige Antwort gesendet wurde. Dies wird als Intervall zwischen dem Zeitpunkt, zu dem Application Gateway das erste Byte einer HTTP-Anforderung empfängt, bis zu dem Zeitpunkt berechnet, zu dem der Vorgang zum Senden der Antwort abgeschlossen ist. Hierbei ist der Hinweis wichtig, dass das Feld „Time-Taken“ normalerweise die Zeitdauer enthält, die von den Anforderungs- und Antwortpaketen für die Übermittlung über das Netzwerk benötigt wird. |
|sslEnabled| Gibt an, ob für die Kommunikation mit den Back-End-Pools TLS/SSL verwendet wurde. Gültige Werte sind „on“ und „off“.|
|host| Der Hostname, mit dem die Anforderung an den Back-End-Server gesendet wurde. Wenn der Back-End-Hostname überschrieben wird, zeigt sich dies in diesem Namen.|
|originalHost| Der Hostname, mit dem die Anforderung vom Client von der Application Gateway-Instanz empfangen wurde.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
#### <a name="for-application-gateway-and-waf-v2-sku"></a>Für die SKU Application Gateway und WAF v2

|Wert  |BESCHREIBUNG  |
|---------|---------|
|instanceId     | Application Gateway-Instanz, von der die Anforderung bereitgestellt wurde        |
|clientIP     | Ursprungs-IP für die Anforderung        |
|httpMethod     | Von der Anforderung verwendete HTTP-Methode       |
|requestUri     | URI der empfangenen Anforderung        |
|UserAgent     | Benutzer-Agent aus dem HTTP-Anforderungsheader        |
|httpStatus     | HTTP-Statuscode, der vom Application Gateway an den Client zurückgegeben wurde       |
|httpVersion     | HTTP-Version der Anforderung        |
|receivedBytes     | Größe des empfangenen Pakets in Byte        |
|sentBytes| Größe des gesendeten Pakets in Byte|
|timeTaken| Dauer (in **Sekunden**), bis eine Anforderung verarbeitet und die dazugehörige Antwort gesendet wurde. Dies wird als Intervall zwischen dem Zeitpunkt, zu dem Application Gateway das erste Byte einer HTTP-Anforderung empfängt, bis zu dem Zeitpunkt berechnet, zu dem der Vorgang zum Senden der Antwort abgeschlossen ist. Hierbei ist der Hinweis wichtig, dass das Feld „Time-Taken“ normalerweise die Zeitdauer enthält, die von den Anforderungs- und Antwortpaketen für die Übermittlung über das Netzwerk benötigt wird. |
|sslEnabled| Gibt an, ob für die Kommunikation mit den Back-End-Pools TLS verwendet wurde. Gültige Werte sind „on“ und „off“.|
|sslCipher| Verschlüsselungssammlung, die für die TLS-Kommunikation verwendet wird (sofern TLS aktiviert ist).|
|sslProtocol| Das verwendete SSL-/TLS-Protokoll (sofern TLS aktiviert ist)|
|serverRouted| Back-End-Server, an den das Anwendungsgateway die Anforderung weiterleitet|
|serverStatus| HTTP-Statuscode des Back-End-Servers|
|serverResponseLatency| Wartezeit für die Antwort (in **Sekunden**) vom Back-End-Server.|
|host| Adresse im host-Header der Anforderung Wenn dieses Feld beim erneuten Generieren von Headern neu geschrieben wird, enthält es den aktualisierten Hostnamen.|
|originalRequestUriWithArgs| Dieses Feld enthält die ursprüngliche Anforderungs-URL. |
|requestUri| Dieses Feld enthält die URL nach dem Vorgang zur erneuten Generierung in Application Gateway. |
|originalHost| Dieses Feld enthält den Hostnamen der ursprüngliche Anforderung.
```json
{
    "timeStamp": "2021-10-14T22:17:11+00:00",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "listenerName": "HTTP-Listener",
    "ruleName": "Storage-Static-Rule",
    "backendPoolName": "StaticStorageAccount",
    "backendSettingName": "StorageStatic-HTTPS-Setting",
    "operationName": "ApplicationGatewayAccess",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_2",
        "clientIP": "185.42.129.24",
        "clientPort": 45057,
        "httpMethod": "GET",
        "originalRequestUriWithArgs": "\/",
        "requestUri": "\/",
        "requestQuery": "",
        "userAgent": "Mozilla\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/52.0.2743.116 Safari\/537.36",
        "httpStatus": 200,
        "httpVersion": "HTTP\/1.1",
        "receivedBytes": 184,
        "sentBytes": 466,
        "timeTaken": 0.034,
        "transactionId": "592d1649f75a8d480a3c4dc6a975309d",
        "sslEnabled": "on",
        "sslCipher": "ECDHE-RSA-AES256-GCM-SHA384",
        "sslProtocol": "TLSv1.2",
        "sslClientVerify": "NONE",
        "sslClientCertificateFingerprint": "",
        "sslClientCertificateIssuerName": "",
        "serverRouted": "52.239.221.65:443",
        "serverStatus": "200",
        "serverResponseLatency": "0.028",
        "originalHost": "20.110.30.194",
        "host": "20.110.30.194"
    }
}
```

### <a name="performance-log"></a>Leistungsprotokoll

Das Leistungsprotokoll wird nur generiert, wenn Sie es auf jeder Application Gateway-Instanz gemäß den obigen Schritten aktiviert haben. Die Daten werden in dem Speicherkonto gespeichert, das Sie beim Aktivieren der Protokollierung angegeben haben. Die Daten für das Leistungsprotokoll werden in Intervallen von einer Minute generiert. Diese Funktion ist nur für die v1-SKU verfügbar. Verwenden Sie für die v2-SKU [Metriken](application-gateway-metrics.md) für die Leistungsdaten. Die folgenden Daten werden protokolliert:


|Wert  |BESCHREIBUNG  |
|---------|---------|
|instanceId     |  Application Gateway-Instanz, für die Leistungsdaten generiert werden. Für ein Anwendungsgateway mit mehreren Instanzen ist eine Zeile pro Instanz vorhanden.        |
|healthyHostCount     | Anzahl von fehlerfreien Hosts im Back-End-Pool        |
|unHealthyHostCount     | Anzahl von fehlerhaften Hosts im Back-End-Pool        |
|requestCount     | Anzahl von bereitgestellten Anforderungen        |
|latency | Durchschnittliche Wartezeit (in Millisekunden) für Anforderungen von der Instanz an das Back-End, das die Anforderungen verarbeitet. |
|failedRequestCount| Anzahl von fehlerhaften Anforderungen|
|throughput| Durchschnittlicher Durchsatz seit dem letzten Protokoll, gemessen in Bytes pro Sekunde.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Latenz wird von dem Zeitpunkt, zu dem das erste Byte der HTTP-Anforderung empfangen wird, bis zu dem Zeitpunkt berechnet, zu dem das letzte Byte der HTTP-Antwort gesendet wird. Dies ist die Summe der Application Gateway-Verarbeitungszeit plus Netzwerkkosten zum Back-End plus die Zeit, die das Back-End zum Verarbeiten der Anforderung benötigt.

### <a name="firewall-log"></a>Firewallprotokoll

Das Firewallprotokoll wird nur generiert, wenn Sie es für jedes Anwendungsgateway gemäß den obigen Schritten aktiviert haben. Für dieses Protokoll muss zudem die Web Application Firewall auf einem Anwendungsgateway konfiguriert sein. Die Daten werden in dem Speicherkonto gespeichert, das Sie beim Aktivieren der Protokollierung angegeben haben. Die folgenden Daten werden protokolliert:


|Wert  |BESCHREIBUNG  |
|---------|---------|
|instanceId     | Application Gateway-Instanz, für die Firewalldaten generiert werden. Für ein Anwendungsgateway mit mehreren Instanzen ist eine Zeile pro Instanz vorhanden.         |
|clientIp     |   Ursprungs-IP für die Anforderung      |
|clientPort     |  Ursprungsport für die Anforderung       |
|requestUri     | URI der empfangenen Anforderung       |
|ruleSetType     | Regelsatztyp: Der verfügbare Wert ist OWASP.        |
|ruleSetVersion     | Verwendete Regelsatzversion. Verfügbare Werte sind 2.2.9 und 3.0.     |
|ruleId     | Regel-ID des auslösenden Ereignisses        |
|message     | Benutzerfreundliche Meldung für das auslösende Ereignis. Weitere Details im Abschnitt „Details“.        |
|action     |  Aktion, die für die Anforderung durchgeführt wird. Verfügbare Werte sind „Blocked“ (Blockiert) und „Allowed“ (Zulässig) für benutzerdefinierte Regeln, „Matched“ (Übereinstimmend), wenn eine Regel mit einem Teil der Anforderung übereinstimmt, und „Detected“ (Erkannt) und „Blocked“ (Blockiert), beide für obligatorische Regeln, je nachdem, ob sich die WAF im Erkennungs- oder Präventionsmodus befindet.      |
|site     | Standort, für den das Protokoll generiert wurde. Derzeit ist nur „Global“ aufgeführt, da Regeln global sind.|
|Details     | Details zum auslösenden Ereignis        |
|details.message     | Beschreibung der Regel        |
|details.data     | Spezifische Daten in der Anforderung, die eine Übereinstimmung mit der Regel ergeben         |
|details.file     | Konfigurationsdatei, die die Regel enthalten hat        |
|details.line     | Nummer der Zeile in der Konfigurationsdatei, die das Ereignis ausgelöst hat       |
|hostname   | Hostname oder IP-Adresse für Application Gateway.    |
|transactionId  | Eindeutige ID für eine bestimmte Transaktion, die Sie beim Gruppieren mehrerer Regelverstöße innerhalb derselben Anforderung unterstützt.   |

```json
{
    "timeStamp": "2021-10-14T22:17:11+00:00",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayFirewall",
    "category": "ApplicationGatewayFirewallLog",
    "properties": {
        "instanceId": "appgw_2",
        "clientIp": "185.42.129.24",
        "clientPort": "",
        "requestUri": "\/",
        "ruleSetType": "OWASP_CRS",
        "ruleSetVersion": "3.0.0",
        "ruleId": "920350",
        "message": "Host header is a numeric IP address",
        "action": "Matched",
        "site": "Global",
        "details": {
            "message": "Warning. Pattern match \\\"^[\\\\d.:]+$\\\" at REQUEST_HEADERS:Host .... ",
            "data": "20.110.30.194:80",
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
            "line": "791"
        },
        "hostname": "20.110.30.194:80",
        "transactionId": "592d1649f75a8d480a3c4dc6a975309d",
        "policyId": "default",
        "policyScope": "Global",
        "policyScopeName": "Global"
    }
}
```

### <a name="view-and-analyze-the-activity-log"></a>Anzeigen und Analysieren des Aktivitätsprotokolls

Mit einer der folgenden Methoden können Sie die Aktivitätsprotokolldaten anzeigen und analysieren:

* **Azure-Tools:** Rufen Sie Informationen aus dem Aktivitätsprotokoll über Azure PowerShell, über die Azure-Befehlszeilenschnittstelle, mithilfe der Azure-REST-API oder über das Azure-Portal ab. Detaillierte Anleitungen für die einzelnen Methoden finden Sie im Artikel [Überwachen von Vorgängen mit dem Ressourcen-Manager](../azure-monitor/essentials/activity-log.md) .
* **Power BI**: Falls Sie noch kein [Power BI](https://powerbi.microsoft.com/pricing)-Konto besitzen, können Sie es kostenlos testen. Mithilfe der [Power BI-Vorlagen-Apps](/power-bi/service-template-apps-overview) können Sie Ihre Daten analysieren.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Anzeigen und Analysieren der Zugriffs-, Leistungs- und Firewallprotokolle

[Azure Monitor-Protokolle](../azure-monitor/insights/azure-networking-analytics.md) können die Indikator- und Ereignisprotokolldateien aus Ihrem Blobspeicherkonto erfassen. Die Anwendung umfasst Visualisierungen und leistungsfähige Suchfunktionen zum Analysieren Ihrer Protokolle.

Sie können auch eine Verbindung mit Ihrem Speicherkonto herstellen und die JSON-Protokolleinträge für Zugriffs- und Leistungsprotokolle abrufen. Nachdem Sie die JSON-Dateien heruntergeladen haben, können Sie diese in das CSV-Format konvertieren oder in Excel, Power BI oder einem anderen Datenvisualisierungstool anzeigen.

> [!TIP]
> Wenn Sie mit Visual Studio und den grundlegenden Konzepten zum Ändern der Werte für Konstanten und Variablen in C# vertraut sind, können Sie die [Protokollkonvertierungstools](https://github.com/Azure-Samples/networking-dotnet-log-converter) von GitHub verwenden.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analysieren der Zugriffsprotokolle mit GoAccess

Wir haben eine Resource Manager-Vorlage veröffentlicht, die die beliebte [GoAccess](https://goaccess.io/)-Protokollanalyse für Application Gateway-Zugriffsprotokolle installiert und ausführt. GoAccess stellt wertvolle HTTP-Datenverkehrsstatistiken bereit, z.B. eindeutige Besucher, angeforderte Dateien, Hosts, Betriebssysteme, Browser, HTTP-Statuscodes und vieles mehr. Weitere Informationen finden Sie in der [Infodatei im Resource Manager-Vorlagenordner auf GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/application-gateway-logviewer-goaccess).

## <a name="next-steps"></a>Nächste Schritte

* Visualisieren von Indikator- und Ereignisprotokollen mit [Azure Monitor-Protokollen](../azure-monitor/insights/azure-networking-analytics.md)
* Blogbeitrag [Visualize your Azure activity log with Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) (Visualisieren von Azure-Aktivitätsprotokollen mit Power BI)
* Blogbeitrag [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Anzeigen und Analysieren von Azure-Aktivitätsprotokollen in Power BI und mehr)

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
