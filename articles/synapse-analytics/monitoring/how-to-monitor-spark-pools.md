---
title: Überwachen von Apache Spark-Pools in Synapse Studio
description: Erfahren Sie, wie Sie Ihre Apache Spark-Pools mit Synapse Studio überwachen.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: c6148f2bd5d3b1555ae61d2da3e922c9cfe632cb
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539657"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Überwachen Ihrer Apache Spark-Pools mit Synapse Studio

Mit Azure Synapse Analytics können Sie mithilfe von Apache Spark Notebooks, Aufträge und andere Arten von Anwendungen in Apache Spark-Pools in Ihrem Arbeitsbereich ausführen.

In diesem Artikel wird erläutert, wie Sie Ihre Apache Spark-Pools überwachen, damit Sie den Status Ihrer Pools im Blick behalten, einschließlich der Anzahl von virtuellen Kernen, die von einzelnen Arbeitsbereichsbenutzern verwendet werden.

## <a name="access-apache-spark-pools-list"></a>Zugreifen auf die Apache Spark-Poolliste

Um die Liste der Apache Spark-Pools in Ihrem Arbeitsbereich anzuzeigen, [öffnen Sie zunächst Synapse Studio](https://web.azuresynapse.net/), und wählen Sie Ihren Arbeitsbereich aus.

![Anmelden beim Arbeitsbereich](./media/common/login-workspace.png)

Nachdem Sie Ihren Arbeitsbereich geöffnet haben, wählen Sie links den Abschnitt **Überwachen** aus.

![Auswählen des Hubs „Überwachen“](./media/common/left-nav.png)

Wählen Sie **Apache Spark-Pools** aus, um die Liste der Apache Spark-Pools anzuzeigen.

 ![Auswählen von Apache Spark-Pools](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-apache-spark-pools"></a>Filtern Ihrer Apache Spark-Pools

Sie können die Liste nach den Apache Spark-Pools filtern, die Sie interessieren. Mit den Filtern am oberen Rand des Bildschirms können Sie ein Feld angeben, nach dem Sie filtern möchten.

Beispielsweise können Sie die Ansicht so filtern, dass nur Apache Spark-Pools angezeigt werden, die den Namen „dataprep“ enthalten:

![Beispielfilter](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-pool"></a>Anzeigen von Details zu einem bestimmten Apache Spark-Pool

Um Details zu einem Ihrer Apache Spark-Pools anzuzeigen, wählen Sie den Apache Spark-Pool aus.

![Details zum Apache Spark-Pool](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Überwachen von Pipelineausführungen finden Sie im Artikel [Überwachen von Pipelineausführungen mithilfe von Synapse Studio](how-to-monitor-pipeline-runs.md). 

Weitere Informationen zum Überwachen von Apache Spark-Anwendungen finden Sie im Artikel [Überwachen von Apache Spark-Anwendungen in Synapse Studio](how-to-monitor-spark-applications.md).
