---
title: Erstellen und Verwenden einer verwalteten Identität
titleSuffix: Azure Cognitive Services
description: Informationen zum Erstellen und Verwenden einer verwalteten Identität im Azure-Portal
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 09/09/2021
ms.author: lajanuar
ms.openlocfilehash: b6a25b832c3acc65d6a7b343901cf23cdd17f98a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255785"
---
# <a name="create-and-use-managed-identity"></a>Erstellen und Verwenden einer verwalteten Identität

> [!IMPORTANT]
>
> Die verwaltete Identität für die Dokumentübersetzung ist derzeit in der globalen Region nicht verfügbar. Wenn Sie eine verwaltete Identität für Dokumentübersetzungsvorgänge verwenden möchten, [erstellen Sie Ihre Übersetzerressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) in einer nicht globalen Azure-Region.

## <a name="what-is-managed-identity"></a>Was ist eine verwaltete Identität?

 Eine verwaltete Azure-Identität ist ein Dienstprinzipal, der eine Azure Active Directory-Identität und bestimmte Berechtigungen für von Azure verwaltete Ressourcen erstellt. Sie können eine verwaltete Identität verwenden, um Zugriff auf eine beliebige Ressource zu gewähren, die die Azure AD-Authentifizierung unterstützt. Zum Gewähren des Zugriffs weisen Sie einer verwalteten Identität mithilfe der [rollenbasierten Zugriffssteuerung von Azure](../../../role-based-access-control/overview.md) (Azure RBAC) eine Rolle zu.  Es entstehen keine zusätzlichen Kosten für die Verwendung der verwalteten Identität in Azure.

Die verwaltete Identität unterstützt sowohl private als auch öffentlich zugängliche Azure Blob Storage-Konten.  Für Speicherkonten **mit öffentlichem Zugriff** können Sie eine Shared Access Signature (SAS) verwenden, um eingeschränkten Zugriff zu gewähren.  In diesem Artikel wird untersucht, wie Sie den Zugriff auf Übersetzungsdokumente in Ihrem Azure Blob Storage-Konto mithilfe der systemseitig zugewiesenen verwalteten Identität verwalten können.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein aktives [**Azure-Konto**](https://azure.microsoft.com/free/cognitive-services/) – Sollten Sie über kein aktives Konto verfügen, können Sie ein [**kostenloses Konto erstellen**](https://azure.microsoft.com/free/).

* Eine [**Übersetzerressource für einen einzelnen Dienst**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (keine Cognitive Services-Ressource für mehrere Dienste), die einer **nicht globalen** Region zugewiesen ist. Ausführliche Schritte _finden_ Sie unter [Erstellen einer Cognitive Services-Ressource mithilfe des Azure-Portals](../../cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows).

* Eine kurze Übersicht über die [**rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)**](../../../role-based-access-control/role-assignments-portal.md) über das Azure-Portal.

* Ein [**Azure Blob Storage-Konto**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) in derselben Region wie Ihre Übersetzerressource. Sie erstellen Container zum Speichern und Organisieren Ihrer Blobdaten unter Ihrem Speicherkonto. 

* **Wenn sich Ihr Speicherkonto hinter einer Firewall befindet, müssen Sie die folgende Konfiguration aktivieren**: </br>

  * Wählen Sie auf der Seite Ihres Speicherkontos im linken Menü **Sicherheit + Netzwerkbetrieb** → **Netzwerk** aus.
    :::image type="content" source="../media/managed-identities/security-and-networking-node.png" alt-text="Screenshot: Registerkarte „Sicherheit + Netzwerkbetrieb“.":::

  * Klicken Sie im Hauptfenster auf **Zugriff aus bestimmten Netzwerken zulassen**.
  :::image type="content" source="../media/managed-identities/firewalls-and-virtual-networks.png" alt-text="Screenshot: aktiviertes Optionsfeld „Ausgewählte Netzwerke“.":::

  * Navigieren Sie auf der Seite „Ausgewählte Netzwerke“ zur Kategorie **Ausnahmen**, und stellen Sie sicher, dass das Kontrollkästchen [**Azure-Diensten in der Liste der vertrauenswürdigen Dienste den Zugriff auf dieses Speicherkonto erlauben**](../../../storage/common/storage-network-security.md?tabs=azure-portal#manage-exceptions) aktiviert ist.

    :::image type="content" source="../media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="Screenshot: Kontrollkästchen für das Zulassen vertrauenswürdiger Dienste, Portalansicht":::

## <a name="managed-identity-assignments"></a>Zuweisungen verwalteter Identitäten

Es gibt zwei Arten von verwalteten Identitäten: **systemseitig** und **benutzerseitig** zugewiesene Identitäten.  Derzeit wird die Dokumentübersetzung durch eine systemseitig zugewiesene verwaltete Identität unterstützt. Eine systemseitig zugewiesene verwaltete Identität wird direkt für eine Dienstinstanz **aktiviert**. Sie ist nicht standardmäßig aktiviert. Sie müssen zu Ihrer Ressource wechseln und die Identitätseinstellung aktualisieren. Die systemseitig zugewiesene verwaltete Identität ist während des gesamten Lebenszyklus an Ihre Ressource gebunden. Wenn Sie Ihre Ressource löschen, wird auch die verwaltete Identität gelöscht.

In den folgenden Schritten aktivieren wir eine systemseitig zugewiesene verwaltete Identität und gewähren Ihrer Übersetzerressource begrenzten Zugriff auf Ihr Azure Blob Storage-Konto.

## <a name="enable-a-system-assigned-managed-identity-using-the-azure-portal"></a>Aktivieren einer systemseitig zugewiesenen verwalteten Identität im Azure-Portal

>[!IMPORTANT]
>
> Um eine systemseitig zugewiesene verwaltete Identität zu aktivieren, benötigen Sie **Microsoft.Authorization/roleAssignments/write**-Berechtigungen, z. B. [**Besitzer**](../../../role-based-access-control/built-in-roles.md#owner) oder [**Benutzerzugriffsadministrator**](../../../role-based-access-control/built-in-roles.md#user-access-administrator). Sie können einen Bereich auf vier Ebenen angeben: Verwaltungsgruppe, Abonnement, Ressourcengruppe oder Ressource.

1. Melden Sie sich mit einem Konto, das Ihrem Azure-Abonnement zugeordnet ist, beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu Ihrer **Übersetzer**-Ressourcenseite im Azure-Portal.

1. Wählen Sie in der linken Leiste die Option **Identität** aus der Liste **Ressourcenverwaltung** aus:

    :::image type="content" source="../media/managed-identities/resource-management-identity-tab.png" alt-text="Screenshot: Registerkarte „Ressourcenverwaltungsidentität“ im Azure-Portal":::

1. Schalten Sie im Hauptfenster die Registerkarte **Vom System zugewiesener Status** auf **Ein**.

1. Wählen Sie unter **Berechtigungen** die Option **Azure-Rollenzuweisungen** aus:

    :::image type="content" source="../media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="Screenshot: Aktivieren der systemseitig zugewiesenen verwalteten Identität im Azure-Portal":::

1. Eine Seite mit Azure-Rollenzuweisungen wird geöffnet. Wählen Sie Ihr Abonnement aus der Dropdownliste und dann die Option **&plus; Rollenzuweisung hinzufügen** aus.

    :::image type="content" source="../media/managed-identities/azure-role-assignments-page-portal.png" alt-text="Screenshot: Seite „Azure-Rollenzuweisungen“ im Azure-Portal":::

    >[!NOTE]
    >
    > Wenn Sie im Azure-Portal keine Rolle zuweisen können, weil die Option „Hinzufügen > Rollenzuweisung hinzufügen“ deaktiviert ist oder der Berechtigungsfehler „Sie verfügen nicht über die Berechtigung zum Hinzufügen einer Rollenzuweisung in diesem Bereich“ angezeigt wird, überprüfen Sie, ob Sie derzeit als Benutzer mit einer zugewiesenen Rolle angemeldet sind, die über Microsoft.Authorization/roleAssignments/write-Berechtigungen wie [**Besitzer**](../../../role-based-access-control/built-in-roles.md#owner) oder [**Benutzerzugriffsadministrator**](../../../role-based-access-control/built-in-roles.md#user-access-administrator) im Speicherbereich für die Speicherressource verfügt.

1. Als nächstes werden Sie Ihrer Übersetzerdienstressource eine **Mitwirkender an Storage-Blobdaten**-Rolle zuweisen. Füllen Sie im Popupfenster **Rollenzuweisung hinzufügen** die Felder wie folgt aus, und wählen Sie **Speichern** aus:

    | Feld | Wert|
    |------|--------|
    |**Bereich**| **_Storage_**|
    |**Abonnement**| **_Das Ihrer Speicherressource zugeordnete Abonnement_**.|
    |**Ressource**| **_Der Name Ihrer Speicherressource_**.|
    |**Rolle** | **_Mitwirkender an Storage-Blobdaten_**.|

     :::image type="content" source="../media/managed-identities/add-role-assignment-window.png" alt-text="Screenshot: Seite „Azure-Rollenzuweisungen hinzufügen“ im Azure-Portal":::

1. Nachdem Sie die Bestätigungsmeldung für die _hinzugefügte Rollenzuweisung_ erhalten haben, aktualisieren Sie die Seite, um die hinzugefügte Rollenzuweisung anzuzeigen. 

    :::image type="content" source="../media/managed-identities/add-role-assignment-confirmation.png" alt-text="Screenshot: Popupmeldung zur Bestätigung der hinzugefügten Rollenzuweisung":::

1. Wenn die Änderung nicht sofort angezeigt wird, warten Sie und versuchen Sie, die Seite noch einmal zu aktualisieren. Beim Zuweisen oder Entfernen von Rollenzuweisungen kann es bis zu 30 Minuten dauern, bis Änderungen wirksam werden.

    :::image type="content" source="../media/managed-identities/assigned-roles-window.png" alt-text="Screenshot: Fenster „Azure-Rollenzuweisungen“":::

 Sehr gut! Sie haben die Schritte zum Aktivieren einer systemseitig zugewiesenen verwalteten Identität abgeschlossen. Mit dieser Anmeldeinformation für diese Identität können Sie dem Übersetzer spezifische Zugriffsrechte auf Ihre Speicherressource gewähren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen zu verwalteten Identitäten für Azure-Ressourcen](../../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)

> [!div class="nextstepaction"]
>[Verwenden verwalteter Identitäten zum Abrufen von Zugriffstoken](../../../app-service/overview-managed-identity.md?tabs=dotnet#obtain-tokens-for-azure-resources)