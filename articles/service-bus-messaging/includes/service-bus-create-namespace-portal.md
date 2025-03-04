---
title: Datei einfügen
description: include file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 978e703285317c7a644f6d0c459a1f1d572b9179
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129801174"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Erstellen eines Namespace im Azure-Portal
Um mit der Verwendung von Service Bus-Nachrichtenentitäten in Azure beginnen zu können, müssen Sie zuerst einen Namespace mit einem in Azure eindeutigen Namen erstellen. Ein Namespace ist ein Bereichscontainer für die Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Namespace

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)
2. Wählen Sie im linken Navigationsbereich des Portals die Optionen **+ Ressource erstellen** > **Integration** > **Service Bus**.

    :::image type="content" source="./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png" alt-text="Abbildung: Auswählen von „Ressource erstellen“, „Integration“ und „Service Bus“ im Menü":::
3. Führen Sie auf der Seite **Namespace erstellen** auf der Registerkarte **Grundlagen** die folgenden Schritte aus: 
    1. Wählen Sie unter **Abonnement** ein Azure-Abonnement aus, in dem der Namespace erstellt werden soll.
    1. Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe für den Namespace aus, oder erstellen Sie eine neue Ressourcengruppe.      
    1. Geben Sie einen **Namen für den Namespace** ein. Das System überprüft sofort, ob dieser Name verfügbar ist. Eine Liste der Regeln für die Benennung von Namespaces finden Sie unter [REST-API zum Erstellen von Namespaces](/rest/api/servicebus/create-namespace).
    1. Wählen Sie unter **Standort** die Region aus, in der Ihr Namespace gehostet werden soll.
    1. Wählen Sie unter **Tarif** den Tarif (Basic, Standard oder Premium) für den Namespace aus. Wählen Sie für diesen Schnellstart die Option **Standard** aus. 
    
        Wenn Sie [Themen und Abonnements](../service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) nutzen möchten, wählen Sie entweder Standard oder Premium aus. Themen/Abonnements werden für den Basic-Tarif nicht unterstützt. 

        Wenn Sie den Tarif **Premium** auswählen, geben Sie die Anzahl von **Messagingeinheiten** an. Der Premium-Tarif bietet Ressourcenisolierung auf CPU- und Arbeitsspeicherebene, sodass die einzelnen Workloads voneinander isoliert ausgeführt werden. Dieser Ressourcencontainer wird als Messagingeinheit bezeichnet. Ein Premium-Namespace verfügt über mindestens eine Messagingeinheit. Sie können 1, 2 oder 4 Messagingeinheiten für jeden Service Bus Premium-Namespace erwerben. Weitere Informationen finden Sie unter [Service Bus Premium- und Standard-Preisstufe für Messaging](../service-bus-premium-messaging.md).
    1. Klicken Sie auf **Überprüfen + erstellen**. Ihr Dienstnamespace wird nun erstellt und aktiviert. Ggf. müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto durch das System bereitgestellt werden.
   
        :::image type="content" source="./media/service-bus-create-namespace-portal/create-namespace.png" alt-text="Abbildung: Seite „Namespace erstellen“":::
    1. Überprüfen Sie die Einstellungen auf der Seite **Überprüfen und erstellen**, und wählen Sie **Erstellen** aus. 
4. Wählen Sie auf der Bereitstellungsseite die Option **Zu Ressource wechseln** aus. 

    :::image type="content" source="./media/service-bus-create-namespace-portal/deployment-alert.png" alt-text="Abbildung: Seite „Die Bereitstellung war erfolgreich.“ mit dem Link „Zu Ressource wechseln“":::
6. Die Startseite für Ihren Service Bus-Namespace wird angezeigt. 

    :::image type="content" source="./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png" alt-text="Abbildung: Startseite des erstellten Service Bus-Namespace" :::

## <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge 
Beim Erstellen eines neuen Namespace wird automatisch eine anfängliche SAS-Richtlinie (Shared Access Signature) mit primären und sekundären Schlüsseln sowie primären und sekundären Verbindungszeichenfolgen generiert, mit denen Sie jeweils die volle Kontrolle über sämtliche Aspekte des Namespace haben. Unter [Service Bus-Authentifizierung und -Autorisierung](../service-bus-authentication-and-authorization.md) erfahren Sie, wie Sie Regeln mit stärker eingeschränkten Rechten für reguläre Absender und Empfänger erstellen. 

Führen Sie die folgenden Schritte aus, um die primäre Verbindungszeichenfolge für Ihren Namespace zu kopieren: 

1. Wählen Sie auf der Seite **Service Bus-Namespace** im linken Menü die Option **Freigegebene Zugriffsrichtlinien** aus.
3. Wählen Sie auf der Seite **Freigegebene Zugriffsrichtlinien** die Option **RootManageSharedAccessKey** aus.
   
    :::image type="content" source="./media/service-bus-create-namespace-portal/connection-info.png" alt-text="Screenshot: Fenster „Richtlinien für gemeinsamen Zugriff“ mit einer hervorgehobenen Richtlinie":::
4. Klicken Sie im Fenster **Richtlinie: RootManageSharedAccessKey** neben **Primäre Verbindungszeichenfolge** auf die Schaltfläche „Kopieren“, um die Verbindungszeichenfolge zur späteren Verwendung in die Zwischenablage zu kopieren. Fügen Sie diesen Wert in den Editor oder an einem anderen temporären Speicherort ein.
   
    :::image type="content" source="./media/service-bus-create-namespace-portal/connection-string.png" alt-text="Screenshot: SAS-Richtlinie namens „RootManageSharedAccessKey“, die Schlüssel und Verbindungszeichenfolgen enthält":::

    Auf dieser Seite können Sie den Primärschlüssel, den sekundären Schlüssel und die sekundäre Verbindungszeichenfolge kopieren. 
