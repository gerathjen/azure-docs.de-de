---
title: Dashboard für Bewertungen und Rezensionen für den gewerblichen Markt
description: Erfahren Sie, wie Sie auf eine konsolidierte Ansicht des Kundenfeedbacks zu Ihren Angeboten in Microsoft AppSource und im Azure Marketplace zugreifen können.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: df8d3d610262d9d544b9161c14d790a54d00f3cc
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082636"
---
# <a name="ratings-and-reviews-dashboard-in-commercial-marketplace-analytics"></a>Dashboard für Bewertungen und Rezensionen in Commercial Marketplace Analytics

Dieser Artikel enthält Informationen über das Dashboard für Bewertungen und Rezensionen im Partner Center. Im Dashboard wird eine konsolidierte Ansicht des Kundenfeedbacks zu Angeboten bei Microsoft AppSource und im Azure Marketplace angezeigt. Beim Suchen, Durchsuchen und Erwerben von Angeboten in beiden Marketplaces können Kunden Bewertungen und Rezensionen für die von ihnen erworbenen Angebote abgeben.

- Kunden können eine neue Bewertung übermitteln oder eine vorhandene Bewertung oder Rezension, die sie bereits übermittelt haben, überprüfen, aktualisieren oder löschen. Kunden können nur Änderungen an den Bewertungen und Rezensionen vornehmen, deren Besitzer sie sind.  
- Rezensionen werden auf der Registerkarte „Rezensionen“ auf der Produktseite des Angebots im Azure Marketplace bzw. in AppSource veröffentlicht. Kunden können ihren Namen einschließen oder ihre Beiträge anonym veröffentlichen.  

>[!NOTE]
> Ausführliche Definitionen der Analyseterminologie finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen und Terminologie](analytics-faq.yml).

## <a name="access-the-ratings--reviews-dashboard"></a>Zugriff auf das Dashboard für Bewertungen und Rezensionen

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Ansicht Arbeitsbereiche](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie auf der Startseite die Kachel **Einblicke**.

    [ ![Veranschaulicht die Kachel Einblicke auf der Startseite des Partner Centers.](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. Wählen Sie im linken Menü **Bewertungen & Rezensionen**.

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

Erweitern Sie in Partner Center im [Dashboard des kommerziellen Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) den Abschnitt **[Analysieren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** , und wählen Sie **Bewertungen und Rezensionen** aus.

---

Im Dashboard wird eine grafische Darstellung der folgenden Kundenaktivität angezeigt:

- Ratings  
- Rezensionskommentare

Verwenden Sie die Registerkarten **Marketplace Insights**, um die Microsoft AppSource- und Microsoft Azure Marketplace-Metriken für Ihr Angebot getrennt zu betrachten. Wählen Sie das Angebot im Dropdownmenü der Angebote aus, um bestimmte Angebotsmetriken anzuzeigen.

### <a name="ratings-and-reviews-summary"></a>Zusammenfassung von Bewertungen und Rezensionen

Im Abschnitt "Zusammenfassung" werden die folgenden Metriken für einen ausgewählten Datumsbereich angezeigt:

- **Durchschnittliche Bewertung:** Gewichtete durchschnittliche Sternebewertung aller Bewertungen, die von Kunden für das ausgewählte Angebot gesendet wurden.
- **Aufschlüsselung der Bewertungen:** Aufschlüsselung der Sternebewertung nach Anzahl von Kunden, die Bewertungen gesendet haben. Im Balkendiagramm sind tatsächliche und überarbeitete Bewertungen (Anzahl aktualisierter Bewertungen) gestapelt.
- **Bewertungen gesamt:** Gesamtanzahl der gesendeten Bewertungen. Diese Anzahl umfasst auch Bewertungen mit und ohne Rezensionen.
- **Bewertungen mit Rezensionen:** Anzahl gesendeter Rezensionen.

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/analyze-ratings-summary.png" alt-text="Veranschaulicht die Zusammenfassung der Analyse von Bewertungen und Rezensionen in Partner Center." lightbox="media/marketplace-publisher-guide-rating-reviews/analyze-ratings-summary.png":::

### <a name="review-comments"></a>Rezensionskommentare

Rezensionen werden in chronologischer Reihenfolge nach Veröffentlichungsdatum angezeigt. In der Standardansicht werden alle Rezensionen angezeigt, und mit der Option **Bewertungsfilter** im Dropdownmenü können Sie die Rezensionen nach Sternebewertung filtern. Darüber hinaus können Sie nach Schlüsselwörtern suchen, die in der Rezension vorkommen.  

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/analyze-reviews.png" alt-text="Veranschaulicht die Analyse von Rezensionskommentaren in Partner Center." lightbox="media/marketplace-publisher-guide-rating-reviews/analyze-reviews.png":::

### <a name="respond-to-a-review"></a>Auf eine Bewertung antworten

Sie können auf Rezensionen von Benutzern reagieren. Die Antwort wird entweder in Azure Marketplace- oder AppSource-Storefronts angezeigt. Diese Funktionalität gilt für die folgenden Angebotstypen: Azure-Anwendung, Azure-Container, virtueller Azure-Computer, Dynamics 365 Business Central, Dynamics 365 Customer Engagement und Power Apps, Dynamics 365 Operations, IoT Edge-Modul, verwalteter Dienst, Power BI-App und Software-as-a-Service.

Führen Sie die folgenden Schritte aus, um auf eine Rezension zu reagieren:

#### <a name="workspaces-view"></a>[Ansicht Arbeitsbereiche](#tab/workspaces-view)

1. Wählen Sie auf der Seite **Bewertungen & Rezensionen** die Option **Azure Marketplace** oder **AppSource**. Sie können **Filter** auswählen, um die Liste der Bewertungen einzugrenzen und z. B. nur Bewertungen mit einer bestimmten Sternebewertung anzuzeigen.

    [![Illustriert die Seite mit den Bewertungen und Rezensionen.](media/marketplace-publisher-guide-rating-reviews/ratings-and-reviews-workspace.png)](media/marketplace-publisher-guide-rating-reviews/ratings-and-reviews-workspace.png#lightbox)

1. Wählen Sie den Link **Antworten** bei der Rezension aus, auf die Sie reagieren möchten, geben Sie Ihre Antwort in das **Textfeld** ein und wählen Sie dann **Antwort senden** aus.

Die Antwort wird unter dem Text der ursprünglichen Rezension auf der Produktdetailseite in der AppSource- und Azure Marketplace-Onlinestorefront angezeigt.

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Wählen Sie auf der Seite **Bewertungen & Rezensionen** die Option **Azure Marketplace** oder **AppSource**. Sie können **Filter** auswählen, um die Liste der Rezensionen einzugrenzen und z. B. nur Rezensionen mit einer bestimmten Sternebewertung anzuzeigen.

    :::image type="content" source="media/marketplace-publisher-guide-rating-reviews/ratings-and-reviews.png" alt-text="Veranschaulicht die Bewertungen und Rezensionen in AppSource." lightbox="media/marketplace-publisher-guide-rating-reviews/ratings-and-reviews.png":::

1. Wählen Sie den Link **Antworten** bei der Rezension aus, auf die Sie reagieren möchten, geben Sie Ihre Antwort in das **Textfeld** ein und wählen Sie dann **Antwort senden** aus.

Die Antwort wird unter dem Text der ursprünglichen Rezension auf der Produktdetailseite in der AppSource- und Azure Marketplace-Onlinestorefront angezeigt.

---

#### <a name="appsource"></a>AppSource

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/review-reply-appsource.png" alt-text="Veranschaulicht die Antwort auf die AppSource-Überprüfung" lightbox="media/marketplace-publisher-guide-rating-reviews/review-reply-appsource.png":::

#### <a name="azure-marketplace-online-store"></a>Azure Marketplace-Onlineshop

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/az-mp-online-store.png" alt-text="Veranschaulicht die Antwort im Azure Marketplace-Onlineshop." lightbox="media/marketplace-publisher-guide-rating-reviews/az-mp-online-store.png":::

### <a name="editing-or-deleting-a-response-to-a-review"></a>Bearbeiten oder Löschen einer Antwort auf eine Rezension

Sie können eine Antwort auf eine Rezension bearbeiten oder löschen, indem Sie **Bearbeiten** oder **Löschen** auswählen.

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/edit-or-delete-reply.png" alt-text="Veranschaulicht Optionen zum Bearbeiten oder Löschen einer Antwort.":::

### <a name="contacting-users-after-a-review-has-been-posted"></a>Kontaktieren von Benutzern nach der Veröffentlichung einer Rezension

Beim Veröffentlichen einer Rezension kann ein Benutzer seine Zustimmung geben, vom Herausgeber kontaktiert zu werden. Wenn ein Benutzer seine Einwilligung erteilt hat, wird am Anfang der Rezension in Partner Center eine Benachrichtigung angezeigt, und die E-Mail-Adresse des Benutzers, der die Rezension veröffentlicht hat, wird angezeigt.

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/contacting-consenting-customer.png" alt-text="Veranschaulicht die Kontaktaufnahme mit einem einwilligenden Kunden.":::

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu Diagrammen, Trends und Werten von aggregierten Daten, mit deren Hilfe Marketplace-Aktivitäten für Ihr Angebot zusammengefasst werden, finden Sie unter [Dashboard „Zusammenfassung“ in Analysen für den kommerziellen Marketplace](summary-dashboard.md).
- Informationen zu Ihren Aufträgen in einem grafischen und herunterladbaren Format finden Sie unter [Dashboard „Aufträge“ in Analysen für den kommerziellen Marketplace](orders-dashboard.md).
- Informationen zu Metriken zur Nutzung und zur getakteten Abrechnung für VM-Angebote finden Sie unter [Dashboard „Nutzung“ in Analysen für den kommerziellen Marketplace](usage-dashboard.md).
- Ausführliche Informationen zu Ihren Kunden, einschließlich Wachstumstrends, finden Sie unter [Dashboard „Kunde“ in Analysen für den kommerziellen Marketplace](customer-dashboard.md).
- Eine Liste Ihrer Downloadanforderungen in den letzten 30 Tagen finden Sie unter [Dashboard „Downloads“ in Analysen für den kommerziellen Marketplace](downloads-dashboard.md).
