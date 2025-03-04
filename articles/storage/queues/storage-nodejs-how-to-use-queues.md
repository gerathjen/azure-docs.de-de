---
title: Verwenden von Azure Queue Storage mit Node.js – Azure Storage
description: Erfahren Sie, wie Sie Warteschlangen mit Azure Queue Storage erstellen und löschen. Erfahren Sie, wie Sie Nachrichten mit Node.js einfügen, abrufen und löschen.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 29a621aa5221a0a4ee06c5c9a635af49320533c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638271"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Verwenden von Azure Queue Storage mit Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Übersicht

In diesem Leitfaden wird die Ausführung häufiger Szenarien mit Azure Queue Storage erläutert. Die Beispiele sind in C# geschrieben und verwenden die Node.js-API. Die erläuterten Szenarien umfassen das Einfügen, Einsehen, Abrufen und Löschen von Warteschlangennachrichten. Außerdem erfahren Sie, wie Warteschlangen erstellt und gelöscht werden.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung

Anleitungen zum Erstellen einer leeren Node.js-Anwendung mithilfe von PowerShell oder [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial) finden Sie unter [Erstellen einer Node.js-Web-App in Azure App Service](../../app-service/quickstart-nodejs.md) und [Erstellen und Bereitstellen einer Node.js-Anwendung in Azure Cloud Services](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurieren der Anwendung für den Speicherzugriff

Die [Azure Storage-Clientbibliothek für JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript) enthält eine Reihe von Bibliotheken, die mit den Storage-REST-Diensten kommunizieren.

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Verwenden von Node Package Manager (npm) zum Beziehen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle, z. B. PowerShell (Windows), Terminal (Mac) oder Bash (Unix), und navigieren Sie zu dem Ordner, in dem Sie Ihre Beispielanwendung erstellt haben.

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

1. Geben Sie im Befehlsfenster `npm install @azure/storage-queue` ein.

1. Überprüfen Sie, ob ein Ordner `node_modules` erstellt wurde. In diesem Ordner finden Sie das Paket `@azure/storage-queue` mit der Clientbibliothek, die Sie für den Zugriff auf den Speicher benötigen.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. Geben Sie im Befehlsfenster `npm install azure-storage` ein.

1. Überprüfen Sie, ob ein Ordner `node_modules` erstellt wurde. In diesem Ordner finden Sie das Paket `azure-storage` mit den Bibliotheken, die Sie für den Zugriff auf den Speicher benötigen.

---

### <a name="import-the-package"></a>Importieren des Pakets

Fügen Sie in Ihrem Code-Editor den folgenden Code am Anfang der JavaScript-Datei hinzu, in der Sie Warteschlangen verwenden möchten.

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Erstellen von Warteschlangen

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

Der folgende Code ruft den Wert der Umgebungsvariable `AZURE_STORAGE_CONNECTION_STRING` ab und verwendet ihn zum Erstellen eines [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient)-Objekts. Mit diesem Objekt wird dann ein [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient)-Objekts erstellt, das Ihnen das Arbeiten mit einer bestimmten Warteschlange ermöglicht.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Wenn die Warteschlange bereits vorhanden ist, wird eine Ausnahme ausgelöst.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Das Azure-Modul entnimmt den Umgebungsvariablen `AZURE_STORAGE_ACCOUNT` und `AZURE_STORAGE_ACCESS_KEY` oder `AZURE_STORAGE_CONNECTION_STRING` die erforderlichen Informationen zum Herstellen einer Verbindung mit Ihrem Azure Storage-Konto. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen beim Aufrufen von `createQueueService` angeben.

Durch folgenden Code wird ein `QueueService` -Objekt erstellt, das Ihnen das Arbeiten mit Warteschlangen ermöglicht.

```javascript
var queueSvc = azure.createQueueService();
```

Rufen Sie die Methode `createQueueIfNotExists` auf, um eine neue Warteschlange mit dem angegebenen Namen zu erstellen oder die Warteschlange zurückzugeben, wenn sie bereits vorhanden ist.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Wenn die Warteschlange erstellt wird, ist `result.created` "true". Wenn die Warteschlange bereits vorhanden ist, ist `result.created` "false".

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Einfügen einer Nachricht in eine Warteschlange

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

Wenn Sie einer Warteschlange eine Nachricht hinzufügen möchten, rufen Sie die [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-)-Methode auf.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Zum Einfügen einer Nachricht in eine Warteschlange rufen Sie die Methode `createMessage` auf, um eine neue Nachricht zu erstellen und zur Warteschlange hinzuzufügen.

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Einsehen der nächsten Nachricht

Sie können Nachrichten in der Warteschlange einsehen, ohne sie daraus zu entfernen. Rufen Sie dazu die Methode `peekMessages` auf.

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

Standardmäßig wird von [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) jeweils nur eine Nachricht eingesehen. Im folgenden Beispiel werden die ersten fünf Nachrichten in der Warteschlange eingesehen. Wenn weniger als fünf Nachrichten sichtbar sind, werden nur die sichtbaren Nachrichten zurückgegeben.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Standardmäßig wird von `peekMessages` jeweils nur eine Nachricht angeschaut.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Das `result` enthält die Nachricht.

---

Wenn die Warteschlange beim Aufrufen von `peekMessages` keine Nachrichten enthält, wird kein Fehler zurückgegeben. Es werden aber auch keine Nachrichten zurückgegeben.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Ändern des Inhalts einer Nachricht in der Warteschlange

Im folgenden Beispiel wird der Text einer Nachricht aktualisiert.

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

Sie ändern den Inhalt einer Nachricht vor Ort in der Warteschlange durch Aufrufen von [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-).

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Ändern Sie die Inhalte einer Nachricht direkt in der Warteschlange durch Aufrufen von `updateMessage`.

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Entfernen einer Nachricht aus der Warteschlange

Das Entfernen einer Nachricht aus der Warteschlange ist ein zweistufiger Prozess:

1. Abrufen der Nachricht.

1. Löschen der Nachricht.

Im folgenden Beispiel wird eine Nachricht abgerufen und dann gelöscht.

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

Wenn Sie eine Nachricht abrufen möchten, rufen Sie die [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-)-Methode auf. Durch diesen Aufruf werden die Nachrichten in der Warteschlange unsichtbar, sodass andere Clients sie nicht verarbeiten können. Nachdem Ihre Anwendung eine Nachricht verarbeitet hat, rufen Sie [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) auf, um sie aus der Warteschlange zu löschen.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Eine Nachricht wird standardmäßig nur 30 Sekunden lang ausgeblendet. Nach 30 Sekunden wird sie für andere Clients sichtbar. Sie können einen anderen Wert angeben, indem Sie [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) festlegen, wenn Sie `receiveMessages` aufrufen.

Wenn die Warteschlange beim Aufrufen von `receiveMessages` keine Nachrichten enthält, wird kein Fehler zurückgegeben. Es werden aber auch keine Nachrichten zurückgegeben.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Wenn Sie eine Nachricht abrufen möchten, rufen Sie die Methode `getMessages` auf. Durch diesen Aufruf werden die Nachrichten in der Warteschlange unsichtbar, sodass andere Clients sie nicht verarbeiten können. Sobald Ihre Anwendung eine Nachricht verarbeitet hat, rufen Sie `deleteMessage` auf, um sie aus der Warteschlange zu löschen.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Eine Nachricht wird standardmäßig nur 30 Sekunden lang ausgeblendet. Nach 30 Sekunden wird sie für andere Clients sichtbar. Sie können einen anderen Wert angeben, indem Sie `options.visibilityTimeout` mit `getMessages`verwenden.

Wenn die Warteschlange bei Verwendung von `getMessages` keine Nachrichten enthält, wird kein Fehler zurückgegeben. Es werden aber auch keine Nachrichten zurückgegeben.

---

## <a name="additional-options-for-dequeuing-messages"></a>Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können:

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages): Abrufen eines Nachrichtenbatches (bis zu 32).
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout): Festlegen eines längeren oder kürzeren Unsichtbarkeits-Zeitlimits.

Im folgenden Beispiel wird die Methode `receiveMessages` verwendet, um mit einem einzigen Aufruf fünf Nachrichten abzurufen. Anschließend wird jede Nachricht mithilfe einer `for`-Schleife verarbeitet. Zudem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten für alle Nachrichten festgelegt, die durch diese Methode zurückgegeben werden.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können:

- `options.numOfMessages`: Abrufen eines Nachrichtenbatches (bis zu 32).
- `options.visibilityTimeout`: Festlegen eines längeren oder kürzeren Unsichtbarkeits-Zeitlimits.

Im folgenden Beispiel wird die Methode `getMessages` verwendet, um mit einem Aufruf 15 Nachrichten abzurufen. Anschließend wird jede Nachricht mithilfe einer `for`-Schleife verarbeitet. Zudem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten für alle Nachrichten festgelegt, die durch diese Methode zurückgegeben werden.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>Abrufen der Warteschlangenlänge

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

Die [`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-)-Methode gibt Metadaten über die Warteschlange zurück, einschließlich der geschätzten Anzahl der in der Warteschlange wartenden Nachrichten.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Die Methode `getQueueMetadata` gibt Metadaten über die Warteschlange zurück, einschließlich der geschätzten Anzahl der in der Warteschlange wartenden Nachrichten.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Auflisten von Warteschlangen

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

Rufen Sie zum Abrufen einer Liste von Warteschlangen [`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) auf. Wenn Sie eine Liste abrufen möchten, die nach einem bestimmten Präfix gefiltert wurde, legen Sie in Ihrem Aufruf [options.prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) auf `listQueues` fest.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Verwenden Sie zum Aufrufen einer Liste an Warteschlangen `listQueuesSegmented`. Verwenden Sie `listQueuesSegmentedWithPrefix`, um eine Liste abzurufen, die nach einem bestimmten Präfix gefiltert ist.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Wenn nicht alle Warteschlangen zurückgegeben werden können, übergeben Sie `result.continuationToken` als ersten Parameter von `listQueuesSegmented` oder als zweiten Parameter von `listQueuesSegmentedWithPrefix`, um weitere Ergebnisse abzurufen.

---

## <a name="how-to-delete-a-queue"></a>Löschen von Warteschlangen

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-)-Methode für das `QueueClient`-Objekt auf.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Wenn Sie alle Nachrichten in einer Warteschlange löschen möchten, ohne diese selbst zu löschen, rufen Sie [`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) auf.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die `deleteQueue`-Methode für das Warteschlangenobjekt auf.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Wenn Sie alle Nachrichten in einer Warteschlange löschen möchten, ohne diese selbst zu löschen, rufen Sie `clearMessages` auf.

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Queue Storage vertraut gemacht haben, folgen Sie diesen Links, um mehr über komplexere Speicheraufgaben zu erfahren.

- Besuchen Sie den [Azure Storage-Teamblog](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog), um sich über Neuigkeiten zu informieren.
- Besuchen Sie das Repository [Azure Storage-Clientbibliothek für JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript) auf GitHub.
