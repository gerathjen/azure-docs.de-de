---
title: 'Node.js: Angular-App mit Azure Cosmos DB-API für MongoDB (Teil 1)'
description: In dieser videobasierten Tutorialreihe erfahren Sie, wie Sie in Azure Cosmos DB eine MongoDB-App mit Angular und Node erstellen und dabei die gleichen APIs verwenden wie für MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: ec1275027c0a145da59e44017d596229d04e1298
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038187"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb"></a>Erstellen einer Angular-App mit der API für MongoDB von Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Dieses mehrteilige Tutorial zeigt, wie Sie eine in Node.js geschriebene neue App mit Express und Angular erstellen und mit Ihrem [Cosmos-Konto verbinden, das mit der API für MongoDB von Cosmos DB](mongodb-introduction.md) konfiguriert wurde.

Azure Cosmos DB ist die schnelle NoSQL-Datenbank von Microsoft mit offenen APIs für jede Größenordnung. Hiermit können Sie moderne Apps mit SLA-gestützter Geschwindigkeit und Verfügbarkeit, automatischer und sofortiger Skalierbarkeit sowie Open Source-APIs für viele NoSQL-Engines entwickeln.

Dieses mehrteilige Tutorial umfasst folgende Aufgaben:

> [!div class="checklist"]
> * [Erstellen einer Node.js-Express-App mithilfe der Angular-Befehlszeilenschnittstelle](tutorial-develop-nodejs-part-2.md)
> * [Erstellen der Benutzeroberfläche mit Angular](tutorial-develop-nodejs-part-3.md)
> * [Erstellen eines Azure Cosmos DB-Kontos mithilfe der Azure-Befehlszeilenschnittstelle](tutorial-develop-nodejs-part-4.md) 
> * [Herstellen einer Verbindung mit Azure Cosmos DB mithilfe von Mongoose](tutorial-develop-nodejs-part-5.md)
> * [Hinzufügen von Post-, Put- und Delete-Funktionen zur App](tutorial-develop-nodejs-part-6.md)

Diese App kann auch mit React erstellt werden. Die Tutorialvideoreihe für React finden Sie [hier](tutorial-develop-mongodb-react.md).

## <a name="video-walkthrough"></a>Exemplarische Vorgehensweise per Video

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>Fertiges Projekt 

In diesem Tutorial wird die Anwendungserstellung Schritt für Schritt erläutert. Für den Fall, dass Sie das fertige Projekt herunterladen möchten, steht die fertige Anwendung auf GitHub im [Repository „angular-cosmosdb“](https://github.com/Azure-Samples/angular-cosmosdb) zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Verschaffen eines Überblicks über die Schritte zum Erstellen einer MEAN.js-App mit Azure Cosmos DB 

Im nächsten Teil des Tutorials wird die Node.js-Express-App erstellt.

> [!div class="nextstepaction"]
> [Erstellen einer Node.js-Express-App mithilfe der Angular-Befehlszeilenschnittstelle](tutorial-develop-nodejs-part-2.md)

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md). 
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md).
