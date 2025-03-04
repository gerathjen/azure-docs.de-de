---
title: Exportieren aus Application Insights nach SQL | Microsoft-Dokumentation
description: Exportiere Sie Application Insights-Daten kontinuierlich in SQL mit Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.service: stream-analytics
ms.openlocfilehash: bcf3bc5995a6d9c8c37ab92e303b7d39b0b132fe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598493"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Exemplarische Vorgehensweise: Exportieren aus Application Insights in SQL mit Stream Analytics
In diesem Artikel wird veranschaulicht, wie Sie Telemetriedaten aus [Azure Application Insights][start] mithilfe von [fortlaufendem Export][export] und [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) in Azure SQL-Datenbank verschieben. 

Beim fortlaufenden Export werden die Telemetriedaten im JSON-Format in Azure Storage verschoben. Wir analysieren die JSON-Objekte mithilfe von Azure Stream Analytics und erstellen Zeilen in einer Datenbanktabelle.

(Allgemeiner ausgedrückt, bietet der fortlaufende Export Ihnen die Möglichkeit, selbst eine Analyse der Telemetriedaten durchzuführen, die Ihre Apps an Application Insights senden. Sie können dieses Codebeispiel für andere Aufgaben mit den exportierten Telemetriedaten, wie beispielsweise die Aggregation der Daten, anpassen.)

Zu Beginn gehen wir davon aus, dass Sie bereits über die App verfügen, die Sie überwachen möchten.

In diesem Beispiel verwenden wir die Seitenzugriffsdaten. Dasselbe Muster kann jedoch problemlos auf andere Datentypen, wie z. B. benutzerdefinierte Ereignisse und Ausnahmen, übertragen werden. 

> [!IMPORTANT]
> Der fortlaufende Export ist veraltet und wird nur für klassische Application Insights-Ressourcen unterstützt. [Migrieren Sie zu einer arbeitsbereichsbasierten Application Insights-Ressource](../azure-monitor/app/convert-classic-resource.md), um [Diagnoseeinstellungen](../azure-monitor/app/export-telemetry.md#diagnostic-settings-based-export) zum Exportieren von Telemetriedaten zu verwenden.

## <a name="add-application-insights-to-your-application"></a>Hinzufügen von Application Insights zu Ihrer Anwendung 
Erste Schritte:

1. [Richten Sie Application Insights für Ihre Webseiten ein](../azure-monitor/app/javascript.md). 
   
    (In diesem Beispiel konzentrieren wir uns auf die Verarbeitung von Seitenansichtsdaten von Clientbrowsern, allerdings könnten Sie Application Insights auch für die Serverseite Ihrer [Java](../azure-monitor/app/java-in-process-agent.md)- oder [ASP.NET](../azure-monitor/app/asp-net.md)-App einrichten und Anforderung, Abhängigkeit und andere Servertelemetrie verarbeiten.)
2. Veröffentlichen Sie Ihre App, und beobachten Sie die Telemetriedaten, die in Ihrer Application Insights-Ressource angezeigt werden.

## <a name="create-storage-in-azure"></a>Erstellen von Speicher in Azure
Durch fortlaufende Exportaktivitäten werden Daten an ein Azure-Speicherkonto übertragen, daher müssen Sie zuerst Speicher erstellen.

1. Erstellen Sie ein Speicherkonto in Ihrem Abonnement im [Azure-Portal][portal].
   
    ![Screenshot des Azure-Portals: Wählen Sie „Neu“, „Daten“, „Speicher“ und dann „Klassisch“ und „Erstellen“ aus. Geben Sie anschließend einen Speichernamen an.](./media/app-insights-export-sql-stream-analytics/040-store.png)
2. Erstellen eines Containers
   
    ![Screenshot des neuen Speichers: Wählen Sie „Container“ und dann die Kachel „Container“ sowie „Hinzufügen“ aus.](./media/app-insights-export-sql-stream-analytics/050-container.png)
3. Kopieren Sie den Speicherzugriffsschlüssel.
   
    Sie benötigen ihn bald, um die Eingabe für den Stream Analytics-Dienst einzurichten.
   
    ![ Screenshot des Speichers: Öffnen Sie die Optionen „Einstellungen“, „Schlüssel“, und kopieren Sie den primären Zugriffsschlüssel.](./media/app-insights-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Starten des fortlaufenden Exports in den Azure-Speicher
1. Navigieren Sie im Azure-Portal zu der Application Insights-Ressource, die Sie für Ihre Anwendung erstellt haben.
   
    ![Screenshot des Azure-Portals: Wählen Sie „Durchsuchen“, „Anwendung Insights“ und dann Ihre Anwendung aus.](./media/app-insights-export-sql-stream-analytics/060-browse.png)
2. Erstellen Sie einen fortlaufenden Export.
   
    ![Screenshot der Application Insights-Ressource: Wählen Sie „Einstellungen“, „Fortlaufender Export“ und dann „Hinzufügen“ aus.](./media/app-insights-export-sql-stream-analytics/070-export.png)

    Wählen Sie das zuvor erstellte Speicherkonto aus:

    ![Screenshot: Fortlaufender Export und anschließende Auswahl von „Hinzufügen“.](./media/app-insights-export-sql-stream-analytics/080-add.png)

    Legen Sie die Ereignistypen fest, die Sie anzeigen möchten:

    ![Screenshot: Hinzufügen von fortlaufendem Export und Auswählen von Ereignistypen.](./media/app-insights-export-sql-stream-analytics/085-types.png)


1. Warten Sie, bis sich einige Daten angesammelt haben. Lehnen Sie sich zurück, und lassen Sie Ihre Benutzer die Anwendung eine Weile lang verwenden. Telemetriedaten gehen ein, und Sie sehen statistische Diagramme im [Metrik-Explorer](../azure-monitor/essentials/metrics-charts.md) sowie einzelne Ereignisse in der [Diagnosesuche](../azure-monitor/app/diagnostic-search.md). 
   
    Darüber hinaus werden die Daten in Ihren Speicher exportiert. 
2. Überprüfen Sie die exportierten Daten, entweder im Portal (wählen Sie **Durchsuchen**, anschließend Ihr Speicherkonto und dann **Container** aus) oder in Visual Studio. Wählen Sie in Visual Studio **Anzeigen/Cloud Explorer**, und öffnen Sie „Azure/Storage“. (Wenn diese Menüoption nicht verfügbar ist, müssen Sie das Azure SDK installieren: Öffnen Sie das Dialogfeld „Neues Projekt“ und anschließend „Visual C#/Cloud/Microsoft Azure SDK für .NET abrufen“.)
   
    ![Screenshot von Visual Studio: Öffnen Sie in Visual Studio den „Server-Browser“, „Azure“ und „Storage“.](./media/app-insights-export-sql-stream-analytics/087-explorer.png)
   
    Notieren Sie sich den gemeinsamen Teil des Pfadnamens, der sich vom Anwendungsnamen und vom Instrumentierungsschlüssel ableitet. 

Die Ereignisse werden in Blobdateien im JSON-Format geschrieben. Jede Datei kann ein oder mehrere Ereignisse enthalten. Daher möchten wir die Ereignisdaten lesen und die gewünschten Felder herausfiltern. Es gibt viele verschiedene Möglichkeiten zur Nutzung der Daten, aber unser Plan besteht darin, Stream Analytics zu verwenden, um die Daten in SQL-Datenbank zu verschieben. Auf diese Weise können wir ganz einfach viele interessante Abfragen ausführen.

## <a name="create-an-azure-sql-database"></a>Erstellen einer Azure-SQL-Datenbank
Beginnen Sie erneut bei Ihrem Abonnement im [Azure-Portal][portal], und erstellen Sie die Datenbank (und einen neuen Server, sofern noch keiner verfügbar ist), in die die Daten geschrieben werden sollen.

![Screenshot des Azure-Portals: „Erstellen“, „Daten und Speicher“ und „SQL-Datenbank“.](./media/app-insights-export-sql-stream-analytics/090-sql.png)

Stellen Sie sicher, dass der Server Zugriff auf Azure-Dienste ermöglicht:

![Screenshot von SQL-Servern in den Einstellungen für Azure-Portal und Firewall](./media/app-insights-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Erstellen einer Tabelle in Azure SQL-Datenbank
Stellen Sie über Ihr bevorzugtes Verwaltungstool eine Verbindung mit der Datenbank her, die Sie im vorherigen Abschnitt erstellt haben. In dieser exemplarischen Vorgehensweise verwenden wir die [SQL Server-Verwaltungstools](/sql/ssms/sql-server-management-studio-ssms) .

![Screenshot: „Verbindung mit Azure SQL-Datenbank herstellen“.](./media/app-insights-export-sql-stream-analytics/31-sql-table.png)

Erstellen Sie eine neue Abfrage, und führen Sie folgende T-SQL-Anweisung aus:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![Screenshot: Erstellen von PageViewsTable in SQL Server Management Studio.](./media/app-insights-export-sql-stream-analytics/34-create-table.png)

In diesem Beispiel verwenden wir Daten aus Seitenaufrufen. Überprüfen Sie zum Anzeigen der anderen verfügbaren Daten die JSON-Ausgabe, und sehen Sie sich das [Exportdatenmodell](../azure-monitor/app/export-data-model.md)an.

## <a name="create-an-azure-stream-analytics-instance"></a>Erstellen einer Azure Stream Analytics-Instanz
Wählen Sie im [Azure-Portal](https://portal.azure.com/) den Azure Stream Analytics-Dienst aus, und erstellen Sie einen neuen Stream Analytics-Auftrag:

![Screenshot der Seite für den Stream Analytics-Auftrag mit hervorgehobener Schaltfläche „Erstellen“](./media/app-insights-export-sql-stream-analytics/001.png)

![Screenshot: Neuer Stream Analytics-Auftrag.](./media/app-insights-export-sql-stream-analytics/002.png)

Wählen Sie nach dem Erstellen des neuen Auftrags die Option **Zu Ressource wechseln** aus.

![Screenshot der Meldung „Bereitstellung erfolgreich“ und der Schaltfläche „Zu Ressource wechseln“](./media/app-insights-export-sql-stream-analytics/003.png)

#### <a name="add-a-new-input"></a>Hinzufügen einer neuen Eingabe

![Screenshot der Seite „Eingaben“ mit ausgewählter Schaltfläche „Hinzufügen“](./media/app-insights-export-sql-stream-analytics/004.png)

Legen Sie den Auftrag so fest, dass er Eingaben vom Blob für den fortlaufenden Export erhält:

![Screenshot des Fensters „Neue Eingabe“ mit ausgewählten Optionen aus den Dropdownmenüs für „Eingabealias“, „Quelle“ und „Speicherkonto“](./media/app-insights-export-sql-stream-analytics/0005.png)

Jetzt benötigen Sie den primären Zugriffsschlüssel aus Ihrem Speicherkonto, den Sie zuvor notiert haben. Legen Sie diesen als Speicherkontoschlüssel fest.

#### <a name="set-path-prefix-pattern"></a>Festlegen des Präfixmusters des Pfads

**Achten Sie darauf, dass das Datum das Format „JJJJ-MM-TT“ (mit Bindestrichen) aufweist.**

Das Präfixmuster des Pfads gibt an, wie Stream Analytics die Eingabedateien im Speicher ermittelt. Sie müssen es so einstellen, dass es der Speicherung der Daten durch den fortlaufenden Export entspricht. Legen Sie ihn wie folgt fest:

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

In diesem Beispiel:

* `webapplication27` ist der Name der Application Insights-Ressource in **Kleinbuchstaben**. 
* `1234...` ist der Instrumentierungsschlüssel der Application Insights-Ressource **ohne Bindestriche**. 
* `PageViews` ist die Art der zu analysierenden Daten. Die verfügbaren Typen sind abhängig von dem Filter, den Sie im fortlaufenden Export festlegen. Untersuchen Sie die exportierten Daten, um die anderen verfügbaren Typen anzuzeigen, und sehen Sie sich das [Exportdatenmodell](../azure-monitor/app/export-data-model.md)an.
* `/{date}/{time}` ist ein als Literal geschriebenes Muster.

Um den Namen und iKey Ihrer Application Insights-Ressource zu erhalten, öffnen Sie auf der Seite "Übersicht" den Eintrag "Essentials", oder öffnen Sie "Einstellungen".

> [!TIP]
> Verwenden Sie die Beispielfunktion um zu überprüfen, ob der Eingabepfad ordnungsgemäß festgelegt wurde. Bei einem Fehler: Überprüfen Sie, ob sich für den ausgewählten Beispielzeitraum Daten im Speicher befinden. Bearbeiten Sie die Eingabedefinition, und überprüfen Sie, ob die Einstellungen für Speicherkonto, Pfadpräfix und Datumsformat richtig sind.

 
## <a name="set-query"></a>Festlegen der Abfrage
Öffnen Sie den Abfrageabschnitt:

Ersetzen Sie die Standardabfrage durch folgende:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Beachten Sie, dass die ersten Eigenschaften sich speziell auf die Seitenzugriffsdaten beziehen. Exporte anderer Telemetrietypen verfügen über andere Eigenschaften. Weitere Informationen finden Sie in der [detaillierten Datenmodellreferenz für die Eigenschaftstypen und -werte.](../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Einrichten der Ausgabe an die Datenbank
Wählen Sie SQL als Ausgabe.

![Screenshot: Stream Analytics mit ausgewählter Option „Ausgaben“.](./media/app-insights-export-sql-stream-analytics/006.png)

Geben Sie die Datenbank an.

![Screenshot der neuen Ausgabe mit Details zur Datenbank.](./media/app-insights-export-sql-stream-analytics/007.png)

Schließen Sie den Assistenten, und warten Sie auf eine Benachrichtigung, dass die Ausgabe eingerichtet wurde.

## <a name="start-processing"></a>Starten der Verarbeitung
Starten Sie den Auftrag über die Aktionsleiste:

![Screenshot: Stream Analytics mit ausgewählter Option „Starten“.](./media/app-insights-export-sql-stream-analytics/008.png)

Sie können auswählen, ob die Daten ab sofort verarbeitet werden oder ob Sie mit früheren Daten beginnen möchten. Letzteres ist nützlich, wenn der fortlaufende Export bereits seit einer Weile ausgeführt wurde.

Nach einigen Minuten wechseln Sie zurück zu den SQL Server-Verwaltungstools, und beobachten Sie den Eingang der Daten. Verwenden Sie beispielsweise eine Abfrage ähnlich der folgenden:

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="next-steps"></a>Nächste Schritte
* [Exportieren in Power BI mit Stream Analytics](../azure-monitor/app/export-power-bi.md)
* [Detaillierte Datenmodellreferenz für die Eigenschaftstypen und -werte.](../azure-monitor/app/export-data-model.md)
* [Fortlaufender Export in Application Insights](../azure-monitor/app/export-telemetry.md)

<!--Link references-->

[diagnostic]: ../azure-monitor/app/diagnostic-search.md
[export]: ../azure-monitor/app/export-telemetry.md
[metrics]: ../azure-monitor/essentials/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ../azure-monitor/app/app-insights-overview.md

