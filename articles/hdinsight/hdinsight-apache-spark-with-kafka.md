---
title: Apache Spark-Streaming mit Apache Kafka – Azure HDInsight
description: Erfahren Sie, wie Sie Apache Spark verwenden, um Daten mithilfe von DStreams in oder aus Apache Kafka zu streamen. In diesem Beispiel streamen Sie Daten mithilfe eines Jupyter Notebooks aus Spark auf HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 370549811fd0f9dd8a0ff9b41e36acfb5999c61a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282581"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Beispiel für Apache Spark-Streaming (DStream) mit Apache Kafka in HDInsight

Erfahren Sie, wie Sie [Apache Spark](https://spark.apache.org/) verwenden, um Daten mithilfe von [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html) in oder aus [Apache Kafka](https://kafka.apache.org/) in HDInsight zu streamen. In diesem Beispiel wird ein auf dem Spark-Cluster ausgeführtes [Jupyter Notebook](https://jupyter.org/) verwendet.

> [!NOTE]  
> Mit den in diesem Dokument beschriebenen Schritten wird eine Azure-Ressourcengruppe erstellt, die jeweils einen Spark- und einen Kafka-Cluster in HDInsight beinhaltet. Die Cluster befinden sich innerhalb eines virtuellen Azure-Netzwerks, wodurch Spark- und Kafka-Cluster direkt miteinander kommunizieren können.
>
> Denken Sie nach dem Ausführen der Schritte in diesem Dokument daran, die Cluster zu löschen, um das Anfallen von Gebühren zu verhindern.

> [!IMPORTANT]  
> Dieses Beispiel verwendet DStreams, also eine ältere Spark-Streamingtechnologie. Ein Beispiel, das neuere Spark-Streamingfeatures verwendet, finden Sie im Dokument [Verwenden von strukturiertem Spark-Streaming mit Apache Kafka in HDInsight](hdinsight-apache-kafka-spark-structured-streaming.md).

## <a name="create-the-clusters"></a>Erstellen von Clustern

Apache Kafka in HDInsight ermöglicht keinen Zugriff auf die Kafka-Broker über das öffentliche Internet. Komponenten, die mit Kafka kommunizieren, müssen sich jeweils im selben virtuellen Azure-Netzwerk befinden wie die Knoten im Kafka-Cluster. Für dieses Beispiel sind die Kafka- und Spark-Cluster in einem virtuellen Azure-Netzwerk angeordnet. Im folgenden Diagramm ist dargestellt, wie der Kommunikationsfluss zwischen den Clustern abläuft:

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png" alt-text="Diagramm der Spark- und Kafka-Cluster in einem virtuellen Azure-Netzwerk" border="false":::

> [!NOTE]  
> Kafka selbst ist zwar auf die Kommunikation innerhalb des virtuellen Netzwerks beschränkt, aber auf andere Dienste im Cluster, z.B. SSH und Ambari, kann über das Internet zugegriffen werden. Weitere Informationen zu den öffentlichen Ports, die für HDInsight verfügbar sind, finden Sie unter [Von HDInsight verwendete Ports und URIs](hdinsight-hadoop-port-settings-for-services.md).

Es ist zwar möglich, ein virtuelles Azure-Netzwerk, einen Kafka-Cluster und einen Spark-Cluster manuell zu erstellen, aber mit einer Azure Resource Manager-Vorlage ist dies erheblich einfacher. Führen Sie die folgenden Schritte aus, um ein virtuelles Azure-Netzwerk, Kafka- und Spark-Cluster für Ihr Azure-Abonnement bereitzustellen.

1. Verwenden Sie die folgende Schaltfläche, um sich bei Azure anzumelden, und öffnen Sie die Vorlage im Azure-Portal.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

   Die Azure Resource Manager-Vorlage finden Sie unter **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

   > [!WARNING]
   > Um die Verfügbarkeit von Kafka in HDInsight zu gewährleisten, muss der Cluster mindestens drei Workerknoten enthalten. Diese Vorlage erstellt einen Kafka-Cluster, der drei Workerknoten enthält.

   Mit dieser Vorlage wird ein HDInsight 3.6-Cluster für Kafka und Spark erstellt.

1. Verwenden Sie die folgenden Informationen, um die Einträge auf dem Abschnitt **Benutzerdefinierte Bereitstellung** aufzufüllen:

   |Eigenschaft |Wert |
   |---|---|
   |Resource group|Erstellen Sie eine Gruppe, oder wählen Sie eine vorhandene Gruppe aus.|
   |Location|Wählen Sie einen Standort in Ihrer Nähe aus.|
   |Basisclustername|Dieser Wert wird als Basisname für Spark- und Kafka-Cluster verwendet. Wenn Sie beispielsweise **hdistreaming** eingeben, werden ein Spark-Cluster mit dem Namen __spark-hdistreaming__ und ein Kafka-Cluster mit dem Namen **kafka-hdistreaming** erstellt.|
   |Benutzername für Clusteranmeldung|Der Administratorbenutzername für die Spark- und Kafka-Cluster.|
   |Kennwort für Clusteranmeldung|Das Administratorbenutzerkennwort für die Spark- und Kafka-Cluster.|
   |SSH-Benutzername|SSH-Benutzer, der für die Spark- und Kafka-Cluster erstellt wird.|
   |SSH-Kennwort|Kennwort für den SSH-Benutzer für die Spark- und Kafka-Cluster.|

   :::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png" alt-text="HDInsight – Benutzerdefinierte Bereitstellungsparameter":::

1. Lesen Sie die **Geschäftsbedingungen**, und wählen Sie anschließend die Option **Ich stimme den oben genannten Geschäftsbedingungen zu**.

1. Wählen Sie abschließend **Kaufen** aus. Das Erstellen der Cluster dauert ca. 20 Minuten.

Sobald die Ressourcen erstellt wurden, wird eine Zusammenfassungsseite angezeigt.

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png" alt-text="Ressourcengruppenzusammenfassung für VNET und Cluster":::

> [!IMPORTANT]  
> Beachten Sie, dass die Namen der HDInsight-Cluster **spark-BASENAME** und **kafka-BASENAME** lauten, wobei BASENAME der Name ist, den Sie für die Vorlage angegeben haben. Sie verwenden diese Namen in späteren Schritten, wenn Sie eine Verbindung mit den Clustern herstellen.

## <a name="use-the-notebooks"></a>Verwenden der Notebooks

Den Code für das in diesem Dokument beschriebene Beispiel finden Sie unter [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

## <a name="delete-the-cluster"></a>Löschen des Clusters

[!INCLUDE [delete-cluster-warning](includes/hdinsight-delete-cluster-warning.md)]

Da mit den Schritten in diesem Dokument beide Cluster in derselben Azure-Ressourcengruppe erstellt werden, können Sie die Ressourcengruppe im Azure-Portal löschen. Durch das Löschen der Gruppe werden alle beim Durcharbeiten dieses Dokuments erstellten Ressourcen sowie das virtuelle Azure-Netzwerk und das von den Clustern verwendete Speicherkonto entfernt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Beispiel haben Sie erfahren, wie Spark verwendet wird, um in Kafka Lese- und Schreibvorgänge auszuführen. Verwenden Sie die folgenden Links, um weitere Möglichkeiten zur Arbeit mit Kafka kennenzulernen:

* [Erste Schritte mit Apache Kafka in HDInsight](kafka/apache-kafka-get-started.md)
* [Verwenden von MirrorMaker zum Replizieren von Apache Kafka in HDInsight](kafka/apache-kafka-mirroring.md)
* [Verwenden von Apache Storm mit Apache Kafka in HDInsight](hdinsight-apache-storm-with-kafka.md)
