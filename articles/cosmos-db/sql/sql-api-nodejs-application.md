---
title: 'Tutorial: Erstellen einer Node.js-Web-App mit dem Azure Cosmos DB JavaScript SDK zum Verwalten von SQL-API-Daten'
description: In diesem Node.js-Tutorial wird beschrieben, wie Sie mit Microsoft Azure Cosmos DB Daten aus einer in einem Web-App-Feature von Microsoft Azure App Service gehosteten Node.js Express-Webanwendung speichern und darauf zugreifen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/18/2021
ms.author: sngun
ms.custom: devx-track-js
ms.openlocfilehash: e9ee6ef04563466cb47f1bc6fe8f92929dd11950
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177553"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>Tutorial: Erstellen einer Node.js-Web-App mit dem JavaScript SDK zum Verwalten eines SQL API-Kontos in Azure Cosmos DB 
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Als Entwickler besitzen Sie möglicherweise Anwendungen, die NoSQL-Dokumentdaten verwenden. Sie können ein SQL-API-Konto in Azure Cosmos DB verwenden, um diese Dokumentdaten zu speichern und darauf zuzugreifen. In diesem Node.js-Tutorial erfahren Sie, wie Sie mithilfe einer in einem Web-Apps-Feature von Microsoft Azure App Service gehosteten Node.js Express-Anwendung Daten in einem SQL-API-Konto für Azure Cosmos DB speichern und daraus abrufen. Sie erstellen Sie eine webbasierte Anwendung (To-Do-App), mit der Sie Aufgaben erstellen, abrufen und abschließen können. Die Aufgaben werden als JSON-Dokumente in Azure Cosmos DB gespeichert. 

In diesem Tutorial erfahren Sie, wie Sie über das Azure-Portal ein SQL-API-Konto für Azure Cosmos DB erstellen. Anschließend erstellen Sie eine auf dem Node.js SDK basierende Web-App und führen sie aus, um eine Datenbank und einen Container zu erstellen und dem Container Elemente hinzuzufügen. In diesem Tutorial wird Version 3.0 des JavaScript SDK verwendet.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Erstellen eines Azure Cosmos DB-Kontos
> * Erstellen einer neuen Node.js-Anwendung
> * Herstellen einer Verbindung zwischen der Anwendung und Azure Cosmos DB
> * Ausführen und Bereitstellen der Anwendung in Azure

## <a name="prerequisites"></a><a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass Sie über die folgenden Ressourcen verfügen:

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js], Version 6.10 oder höher
* [Express Generator](https://www.expressjs.com/starter/generator.html) (Express kann über `npm install express-generator -g` installiert werden)
* Installieren Sie [Git][Git] auf Ihrer lokalen Arbeitsstation.

## <a name="create-an-azure-cosmos-db-account"></a><a name="create-account"></a>Erstellen eines Azure Cosmos DB-Kontos
Wir beginnen, indem wir ein Azure Cosmos DB-Konto erstellen. Falls Sie bereits ein Konto besitzen oder den Azure Cosmos DB-Emulator für dieses Tutorial verwenden, können Sie mit [Schritt 2: Erstellen einer neuen Node.js-Anwendung](#create-new-app) fortfahren.

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../includes/cosmos-db-keys.md)]

## <a name="create-a-new-nodejs-application"></a><a name="create-new-app"></a>Erstellen einer neuen Node.js-Anwendung
Nun erfahren Sie, wie Sie ein einfaches „Hallo Welt“-Node.js-Projekt mithilfe des Express -Frameworks erstellen.

1. Öffnen Sie Ihr bevorzugtes Terminal (beispielsweise die Node.js-Eingabeaufforderung).

1. Navigieren Sie zu dem Verzeichnis, in dem Sie die neue Anwendung speichern möchten.

1. Verwenden Sie den Express Generator, um eine neue Anwendung namens **todo** zu erstellen.

   ```bash
   express todo
   ```

1. Öffnen Sie das Verzeichnis **todo**, und installieren Sie die Abhängigkeiten.

   ```bash
   cd todo
   npm install
   ```

1. Führen Sie die neue Anwendung aus.

   ```bash
   npm start
   ```

1. Sie können die neue Anwendung anzeigen, indem Sie in Ihrem Browser zu „`http://localhost:3000`“ navigieren.
   
   :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-express.png" alt-text="Kennenlernen von Node.js – Screenshot der „Hello World“-Anwendung in einem Browserfenster":::

   Beenden Sie die Anwendung durch Drücken von STRG+C im Terminalfenster, und wählen Sie **J** aus, um den Batchauftrag zu beenden.

## <a name="install-the-required-modules"></a><a name="install-modules"></a>Installieren der erforderlichen Module

Die Datei **package.json** ist eine der im Stammverzeichnis des Projekts erstellten Dateien. Diese Datei enthält eine Liste weiterer Module, die für Ihre Node.js-Anwendung erforderlich sind. Wenn Sie diese Anwendung in Azure bereitstellen, wird anhand dieser Datei bestimmt, welche Module in Azure installiert werden müssen, um Ihre Anwendung zu unterstützen. Installieren Sie für dieses Tutorial zwei weitere Pakete.

1. Installieren Sie das **\@azure/cosmo** s-Modul über npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="connect-the-nodejs-application-to-azure-cosmos-db"></a><a name="connect-to-database"></a>Herstellen einer Verbindung zwischen der Node.js-Anwendung und Azure Cosmos DB
Sie haben die Ersteinrichtung und -konfiguration abgeschlossen und schreiben als Nächstes Code, der von der To-Do-Anwendung für die Kommunikation mit Azure Cosmos DB benötigt wird.

### <a name="create-the-model"></a>Erstellen des Modells
1. Erstellen Sie im Stamm des Projektverzeichnisses ein neues Verzeichnis namens **models**.  

2. Erstellen Sie im Verzeichnis **models** eine neue Datei namens **taskDao.js**. Diese Datei enthält den erforderlichen Code zum Erstellen der Datenbank und des Containers. Sie definiert auch Methoden zum Lesen, Aktualisieren, Erstellen und Finden von Aufgaben in Azure Cosmos DB. 

3. Kopieren Sie den folgenden Code in die Datei **taskDao.js**:

   ```javascript
    // @ts-check
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const debug = require('debug')('todo:taskDao')

    // For simplicity we'll set a constant partition key
    const partitionKey = undefined
    class TaskDao {
      /**
       * Manages reading, adding, and updating Tasks in Cosmos DB
       * @param {CosmosClient} cosmosClient
       * @param {string} databaseId
       * @param {string} containerId
       */
      constructor(cosmosClient, databaseId, containerId) {
        this.client = cosmosClient
        this.databaseId = databaseId
        this.collectionId = containerId

        this.database = null
        this.container = null
      }

      async init() {
        debug('Setting up the database...')
        const dbResponse = await this.client.databases.createIfNotExists({
          id: this.databaseId
        })
        this.database = dbResponse.database
        debug('Setting up the database...done!')
        debug('Setting up the container...')
        const coResponse = await this.database.containers.createIfNotExists({
          id: this.collectionId
        })
        this.container = coResponse.container
        debug('Setting up the container...done!')
      }

      async find(querySpec) {
        debug('Querying for items from the database')
        if (!this.container) {
          throw new Error('Collection is not initialized.')
        }
        const { resources } = await this.container.items.query(querySpec).fetchAll()
        return resources
      }

      async addItem(item) {
        debug('Adding an item to the database')
        item.date = Date.now()
        item.completed = false
        const { resource: doc } = await this.container.items.create(item)
        return doc
      }

      async updateItem(itemId) {
        debug('Update an item in the database')
        const doc = await this.getItem(itemId)
        doc.completed = true

        const { resource: replaced } = await this.container
          .item(itemId, partitionKey)
          .replace(doc)
        return replaced
      }

      async getItem(itemId) {
        debug('Getting an item from the database')
        const { resource } = await this.container.item(itemId, partitionKey).read()
        return resource
      }
    }

    module.exports = TaskDao
   ```
4. Speichern und schließen Sie die Datei **taskDao.js** .  

### <a name="create-the-controller"></a>Erstellen des Controllers

1. Erstellen Sie im Verzeichnis **routes** des Projekts eine neue Datei namens **tasklist.js**.  

2. Fügen Sie **tasklist.js** den folgenden Code hinzu. Dieser Code lädt die Module „CosmosClient“ und „async“, die von **tasklist.js** verwendet werden. Des Weiteren definiert er die Klasse **TaskList**, die als Instanz des zuvor definierten Objekts **TaskDao** übergeben wird:
   
   ```javascript
    const TaskDao = require("../models/TaskDao");
    
    class TaskList {
      /**
       * Handles the various APIs for displaying and managing tasks
       * @param {TaskDao} taskDao
       */
      constructor(taskDao) {
        this.taskDao = taskDao;
      }
      async showTasks(req, res) {
        const querySpec = {
          query: "SELECT * FROM root r WHERE r.completed=@completed",
          parameters: [
            {
              name: "@completed",
              value: false
            }
          ]
        };

        const items = await this.taskDao.find(querySpec);
        res.render("index", {
          title: "My ToDo List ",
          tasks: items
        });
      }

      async addTask(req, res) {
        const item = req.body;

        await this.taskDao.addItem(item);
        res.redirect("/");
      }

      async completeTask(req, res) {
        const completedTasks = Object.keys(req.body);
        const tasks = [];

        completedTasks.forEach(task => {
          tasks.push(this.taskDao.updateItem(task));
        });

        await Promise.all(tasks);

        res.redirect("/");
      }
    }

    module.exports = TaskList;
   ```

3. Speichern und schließen Sie die Datei **tasklist.js** .

### <a name="add-configjs"></a>Fügen Sie config.js hinzu.

1. Erstellen Sie im Stamm des Projektverzeichnisses eine neue Datei namens **config.js**. 

2. Fügen Sie der Datei **config.cs** den folgenden Code hinzu. Mit diesem Code werden die für Ihre Anwendung erforderlichen Konfigurationseinstellungen und Werte definiert.
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. Aktualisieren Sie in der Datei **config.js** die Werte für „HOST“ und „AUTH_KEY“ mit den Werten auf der Seite „Schlüssel“ Ihres Azure Cosmos DB-Kontos im [Azure-Portal](https://portal.azure.com). 

4. Speichern und schließen Sie die Datei **config.js** .

### <a name="modify-appjs"></a>Ändern von app.js

1. Öffnen Sie im Projektverzeichnis die Datei **app.js** . Diese Datei wurde bereits während der Erstellung der Express-Webanwendung erstellt.  

2. Fügen Sie der Datei **app.js** den folgenden Code hinzu. Dieser Code definiert die zu verwendende Konfigurationsdatei und lädt die Werte in einige Variablen, die in den nächsten Abschnitten verwendet werden. 
   
   ```javascript
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const config = require('./config')
    const TaskList = require('./routes/tasklist')
    const TaskDao = require('./models/taskDao')

    const express = require('express')
    const path = require('path')
    const logger = require('morgan')
    const cookieParser = require('cookie-parser')
    const bodyParser = require('body-parser')

    const app = express()

    // view engine setup
    app.set('views', path.join(__dirname, 'views'))
    app.set('view engine', 'jade')

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'))
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({ extended: false }))
    app.use(cookieParser())
    app.use(express.static(path.join(__dirname, 'public')))

    //Todo App:
    const cosmosClient = new CosmosClient({
      endpoint: config.host,
      key: config.authKey
    })
    const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId)
    const taskList = new TaskList(taskDao)
    taskDao
      .init(err => {
        console.error(err)
      })
      .catch(err => {
        console.error(err)
        console.error(
          'Shutting down because there was an error settinig up the database.'
        )
        process.exit(1)
      })

    app.get('/', (req, res, next) => taskList.showTasks(req, res).catch(next))
    app.post('/addtask', (req, res, next) => taskList.addTask(req, res).catch(next))
    app.post('/completetask', (req, res, next) =>
      taskList.completeTask(req, res).catch(next)
    )
    app.set('view engine', 'jade')

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      const err = new Error('Not Found')
      err.status = 404
      next(err)
    })

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message
      res.locals.error = req.app.get('env') === 'development' ? err : {}

      // render the error page
      res.status(err.status || 500)
      res.render('error')
    })

    module.exports = app
   ```

3. Speichern und schließen Sie abschließend die Datei **app.js**.

## <a name="build-a-user-interface"></a><a name="build-ui"></a>Erstellen einer Benutzeroberfläche

Als Nächstes erstellen wir die Benutzeroberfläche, damit Benutzer mit der Anwendung interagieren können. Die im vorherigen Abschnitt erstellte Express-Anwendung verwendet **Jade** als Anzeige-Engine.

1. Die Datei **layout.jade** im Verzeichnis **views** dient als globale Vorlage für andere **.jade**-Dateien. Sie wird in diesem Schritt bearbeitet, um Twitter Bootstrap zu verwenden – ein Toolkit für die Websitegestaltung.  

2. Öffnen Sie die Datei **layout.jade** (im Ordner **views**), und ersetzen Sie die Inhalte durch folgenden Code:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Dieser Code weist die **Jade-Engine** an, einige HTML-Elemente für unsere Anwendung darzustellen, und erstellt einen **Block** mit der Bezeichnung **content**, in dem wir das Layout für unsere Inhaltsseiten angeben können. Speichern und schließen Sie die Datei **layout.jade**.

3. Öffnen Sie nun die Datei **index.jade** (die Ansicht, die von der Anwendung verwendet wird), und ersetzen Sie den Inhalt der Datei durch den folgenden Code:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Dieser Code erweitert das Layout und stellt Inhalte für den Platzhalter **content** bereit, den wir zuvor in der Datei **layout.jade** gesehen haben. In diesem Layout haben wir zwei HTML-Formulare erstellt.

Das erste Formular enthält eine Tabelle für Ihre Daten sowie eine Schaltfläche, mit der die Elemente mittels Übermittlung an die Methode **/completeTask** des Controllers aktualisiert werden können.
    
Das zweite Formular enthält zwei Eingabefelder und eine Schaltfläche, mit der Sie mittels Übermittlung an die Methode **/addtask** des Controllers ein neues Element erstellen können. Damit haben wir alles, was wir für diese Anwendung benötigen.

## <a name="run-your-application-locally"></a><a name="run-app-locally"></a>Lokales Ausführen der Anwendung

Nachdem Sie die Anwendung erstellt haben, können Sie sie lokal ausführen, indem Sie die folgenden Schritte ausführen:  

1. Nun können Sie die Anwendung auf Ihrem lokalen Computer testen. Führen Sie dazu im Terminal `npm start` aus, um Ihre Anwendung zu starten, und aktualisieren Sie anschließend die Browserseite `http://localhost:3000`. Die Seite sollte wie im folgenden Screenshot aussehen:
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png" alt-text="Screenshot der Anwendung „Meine Aufgabenliste“ in einem Browserfenster":::

    > [!TIP]
    > Sollte eine Fehlermeldung mit einem Hinweis auf den Einzug in der Datei „layout.jade“ oder „index.jade“ ausgegeben werden, vergewissern Sie sich, dass die ersten beiden Zeilen der beiden Dateien ohne Leerzeichen linksbündig ausgerichtet sind. Sollten sich vor den ersten beiden Zeilen Leerzeichen befinden, entfernen Sie sie, speichern Sie beide Dateien, und aktualisieren Sie anschließend Ihr Browserfenster. 

2. Geben Sie unter Verwendung der Felder „Element“, „Elementname“ und „Kategorie“ eine neue Aufgabe ein, und wählen Sie anschließend **Element hinzufügen** aus. Dadurch wird in Azure Cosmos DB ein Dokument mit diesen Eigenschaften erstellt. 

3. Die Seite sollte nun das neu erstellte Element in der Aufgabenliste anzeigen.
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png" alt-text="Screenshot der Anwendung mit einem neuen Element in der Aufgabenliste":::

4. Um eine Aufgabe abzuschließen, aktivieren Sie das Kontrollkästchen in der Spalte „Abschließen“, und wählen Sie anschließend **Aufgaben aktualisieren** aus. Dadurch wird das bereits erstellte Dokument aktualisiert und aus der Ansicht entfernt.

5. Drücken Sie zum Beenden der Anwendung STRG+C im Terminalfenster, und wählen Sie anschließend **J** aus, um den Batchauftrag zu beenden.

## <a name="deploy-your-application-to-app-service"></a><a name="deploy-app"></a>Bereitstellen Ihrer Anwendung in App Service

Nachdem Ihre Anwendung lokal erfolgreich ausgeführt wurde, können Sie sie in Azure App Service bereitstellen. Stellen Sie im Terminal sicher, dass Sie sich im App-Verzeichnis *todo* befinden. Stellen Sie den Code in Ihrem lokalen Ordner (todo) mit dem folgenden Befehl vom Typ [az webapp up](/cli/azure/webapp?view=azure-cli-latest#az_webapp_up&preserve-view=true) bereit:

```azurecli
az webapp up --sku F1 --name <app-name>
```

Ersetzen Sie <app_name> durch einen Namen, der innerhalb von Azure eindeutig ist (gültige Zeichen: a-z, 0-9 und -). Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID. Weitere Informationen zur App-Bereitstellung finden Sie im Artikel [Erstellen einer Node.js-Web-App in Azure](../../app-service/quickstart-nodejs.md?tabs=linux&pivots=development-environment-cli#deploy-to-azure).

Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. Bei der Ausführung werden Meldungen zum Erstellen der Ressourcengruppe, dem App Service-Plan und der App-Ressource, zur Konfiguration der Protokollierung und zur ZIP-Bereitstellung angezeigt. Anschließend erhalten Sie eine URL zum Starten der App unter `http://<app-name>.azurewebsites.net`. Dies ist die URL der App in Azure.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe, das Azure Cosmos DB-Konto und die dazugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe für das Azure Cosmos DB-Konto und anschließend **Löschen** aus, und bestätigen Sie den Namen der zu löschenden Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
  * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
  * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)

> [!div class="nextstepaction"]
> [Erstellen von mobilen Anwendungen mit Xamarin und Azure Cosmos DB](mobile-apps-with-xamarin.md)

[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app