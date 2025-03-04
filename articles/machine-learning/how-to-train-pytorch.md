---
title: Trainieren von Deep Learning-PyTorch-Modellen
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Ihre PyTorch-Trainingsskripts im Unternehmensumfang mit Azure Machine Learning ausführen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: how-to
ms.openlocfilehash: 6891da994955359dcc10b8e994d8c2dd05436fce
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606763"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Bedarfsgerechtes Trainieren von PyTorch-Modellen mit Azure Machine Learning

In diesem Artikel erfahren Sie, wie Sie Ihre [PyTorch](https://pytorch.org/)-Trainingsskripts im Unternehmensumfang mit Azure Machine Learning ausführen.

Die Beispielskripts in diesem Artikel werden dazu verwendet, Hühner- und Truthahnbilder zu klassifizieren, um ein neuronales Deep Learning-Netz (DNN) aufzubauen, basierend auf dem [Tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) zum Transferlernen von PyTorch. Lerntransfer ist ein Verfahren, bei dem das bei der Lösung eines Problems gewonnene Wissen auf ein anderes, aber verwandtes Problem angewandt wird. Aufgrund des damit geringeren Aufwands an Daten, Zeit und Computeressourcen wird der Trainingsprozess rationalisiert. Weitere Informationen zum Lerntransfer finden Sie im Artikel [Deep Learning im Vergleich zu maschinellem Lernen](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning).

Unabhängig davon, ob Sie ein PyTorch-Deep Learning-Modell von Grund auf trainieren, oder ob Sie ein vorhandenes Modell in die Cloud bringen, können Sie Azure Machine Learning zum Aufskalieren von Open-Source-Trainingsaufträgen mithilfe elastischer Cloud-Computeressourcen verwenden. Sie können produktionsgeeignete Modelle mit Azure Machine Learning erstellen, bereitstellen, überwachen sowie die Versionen verwalten. 

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie diesen Code in einer dieser Umgebungen aus:

- Azure Machine Learning-Compute-Instanz: keine Downloads oder Installationen erforderlich

    - Schließen Sie [Schnellstart: Erste Schritte mit Azure Machine Learning](quickstart-create-resources.md) ab, um einen dedizierten Notebook-Server zu erstellen, der mit dem SDK und dem Beispiel-Repository vorab geladen wurde.
    - Suchen Sie im Deep Learning-Beispielordner auf dem Notebookserver ein fertiges und erweitertes Notebook. Dazu navigieren Sie zum folgenden Verzeichnis: **how-to-use-azureml > ml-frameworks > pytorch > train-hyperparameter-tune-deploy-with-pytorch**. 
 
 - Ihr eigener Jupyter Notebook-Server
    - [Installieren des Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (1.15.0 oder höher).
    - [Erstellen Sie eine Konfigurationsdatei für den Arbeitsbereich.](how-to-configure-environment.md#workspace)
    - [Herunterladen der Beispielskriptdateien](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Auf der GitHub-Seite mit Beispielen finden Sie außerdem eine fertige [Jupyter Notebook-Version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) dieser Anleitung. Das Notebook umfasst erweiterte Abschnitte, in denen die intelligente Hyperparameteroptimierung, die Modellimplementierung und Notebook-Widgets behandelt werden.

## <a name="set-up-the-experiment"></a>Einrichten des Experiments

In diesem Abschnitt wird das Trainingsexperiment eingerichtet, indem die erforderlichen Python-Pakete geladen, ein Arbeitsbereich initialisiert, das Computeziel erstellt und die Trainingsumgebung definiert wird.

### <a name="import-packages"></a>Importieren von Paketen

Importieren Sie zunächst die erforderlichen Python-Bibliotheken.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Initialisieren eines Arbeitsbereichs

Der [Azure Machine Learning-Arbeitsbereich](concept-workspace.md) ist die Ressource der obersten Ebene für den Dienst. Er stellt den zentralen Ort für die Arbeit mit allen erstellten Artefakten dar. Im Python SDK können Sie auf die Arbeitsbereichsartefakte zugreifen, indem Sie ein [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace)-Objekt erstellen.

Erstellen Sie ein Arbeitsbereichsobjekt aus der Datei `config.json`, die im [Abschnitt „Voraussetzungen“](#prerequisites) erstellt wurde.

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Abrufen von Daten

Das Dataset besteht aus etwa 120 Trainingsbildern, jeweils von Puten und Hühnern, mit 100 Validierungsbildern für jede Klasse. Im Rahmen unseres Trainingsskripts `pytorch_train.py` laden wir das Dataset herunter und extrahieren es. Die Bilder sind eine Teilmenge des [Open Images v5 Dataset](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-script"></a>Vorbereiten des Trainingsskripts

In diesem Tutorial ist das Trainingsskript `pytorch_train.py` bereits bereitgestellt. In der Praxis können Sie jedes benutzerdefinierte Trainingsskript, wie es ist, verwenden und mit Azure Machine Learning ausführen.

Erstellen Sie einen Ordner für Ihre Trainingsskripts.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Erstellen eines Computeziels

Erstellen Sie ein Computeziel, auf dem der PyTorch-Auftrag ausgeführt werden soll. In diesem Beispiel erstellen Sie einen GPU-fähigen Azure Machine Learning-Computercluster.

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Weitere Informationen zu Computezielen finden Sie im Artikel [Was ist ein Computeziel?](concept-compute-target.md).

### <a name="define-your-environment"></a>Definieren der Umgebung

Um die Azure ML-[Umgebung](concept-environments.md) zu definieren, die die Abhängigkeiten Ihres Trainingsskripts kapselt, können Sie entweder eine benutzerdefinierte Umgebung definieren oder eine von Azure ML zusammengestellte Umgebung verwenden.

#### <a name="use-a-curated-environment"></a>Verwenden einer zusammengestellten Umgebung
Azure ML bietet vorgefertigte, zusammengestellte Umgebungen, falls Sie keine eigene Umgebung definieren möchten. Azure ML verfügt über mehrere unterschiedliche CPU- und GPU-Umgebungen für PyTorch, die verschiedenen Versionen von PyTorch entsprechen. Weitere Informationen finden Sie [hier](resource-curated-environments.md).

Wenn Sie eine zusammengestellte Umgebung verwenden möchten, können Sie stattdessen den folgenden Befehl ausführen:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Um die Pakete anzuzeigen, die in der zusammengestellten Umgebung enthalten sind, können Sie die Conda-Abhängigkeiten auf den Datenträger schreiben:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Stellen Sie sicher, dass die zusammengestellte Umgebung alle Abhängigkeiten enthält, die von Ihrem Trainingsskript benötigt werden. Wenn dies nicht der Fall ist, müssen Sie die Umgebung so ändern, dass die fehlenden Abhängigkeiten eingeschlossen werden. Beachten Sie Folgendes: Wenn die Umgebung geändert wird, müssen Sie ihr einen neuen Namen geben, da das Präfix „AzureML“ für zusammengestellte Umgebungen reserviert ist. Wenn Sie die YAML-Datei für Conda-Abhängigkeiten geändert haben, können Sie daraus eine neue Umgebung mit einem neuen Namen erstellen, z. B.:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Wenn Sie das Objekt der zusammengestellten Umgebung stattdessen direkt geändert haben, können Sie diese Umgebung mit einem neuen Namen klonen:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
```

#### <a name="create-a-custom-environment"></a>Erstellen einer benutzerdefinierten Umgebung

Sie können auch eine eigene Azure ML-Umgebung erstellen, die die Abhängigkeiten Ihres Trainingsskripts kapselt.

Definieren Sie zunächst Ihre Conda-Abhängigkeiten in einer YAML-Datei. In diesem Beispiel trägt die Datei den Namen `conda_dependencies.yml`.

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Erstellen Sie anhand dieser Spezifikation der Conda-Umgebung eine Azure ML-Umgebung. Die Umgebung wird zur Laufzeit in einen Docker-Container gepackt.

Wenn kein Basisimage angegeben wird, verwendet Azure ML standardmäßig ein CPU-Image `azureml.core.environment.DEFAULT_CPU_IMAGE` als Basisimage. Da das Training in diesem Beispiel auf einem GPU-Cluster ausgeführt wird, müssen Sie ein GPU-Basisimage mit den erforderlichen GPU-Treibern und -Abhängigkeiten angeben. Azure ML verwaltet eine Reihe von Basisimages, die für Microsoft Container Registry (MCR) veröffentlicht werden, die Sie verwenden können. Weitere Informationen finden Sie im GitHub-Repository [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers).

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Optional können Sie einfach alle ihre Abhängigkeiten direkt in einem benutzerdefinierten Docker-Image oder in einer Dockerfile-Datei erfassen und Ihre Umgebung daraus erstellen. Weitere Informationen finden Sie unter [Trainieren mit einem benutzerdefinierten Image](how-to-train-with-custom-image.md).

Weitere Informationen zum Erstellen und Verwenden von Umgebungen finden Sie unter [Erstellen und Verwenden von Softwareumgebungen in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurieren und Übermitteln Ihrer Trainingsausführung

### <a name="create-a-scriptrunconfig"></a>Erstellen eines ScriptRunConfig-Elements

Erstellen Sie ein [ScriptRunConfig-](/python/api/azureml-core/azureml.core.scriptrunconfig) Objekt, um die Konfigurationsdetails Ihres Trainingsauftrags anzugeben, einschließlich Ihres Trainingsskripts, der zu verwendenden Umgebung und des Computeziels für die Ausführung. Alle Argumente des Trainingsskripts werden über die Befehlszeile übergeben, wenn sie im `arguments` Parameter angegeben sind. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Zum Ausführen von Trainingsskripts wird von Azure Machine Learning das gesamte Quellverzeichnis kopiert. Sind vertrauliche Daten vorhanden, die nicht hochgeladen werden sollen, verwenden Sie die [IGNORE-Datei](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots), oder platzieren Sie sie nicht im Quellverzeichnis. Greifen Sie stattdessen mit einem Azure ML-[Dataset](how-to-train-with-datasets.md) auf Ihre Daten zu.

Weitere Informationen zum Konfigurieren von Aufträgen mit ScriptRunConfig finden Sie unter [Konfigurieren und Übermitteln von Trainingsausführungen](how-to-set-up-training-targets.md).

> [!WARNING]
> Wenn Sie zuvor den PyTorch-Schätzer zum Konfigurieren Ihrer PyTorch-Trainingsaufträge verwendet haben, beachten Sie, dass der Schätzer mit der Veröffentlichung von Release 1.19.0 des SDK als veraltet eingestuft wurde. Mit dem Azure Machine Learning SDK 1.15.0 oder höher ist ScriptRunConfig die empfohlene Vorgehensweise zum Konfigurieren von Trainingsaufträgen, einschließlich derjenigen, die Deep Learning-Frameworks verwenden. Allgemeine Fragen zur Migration finden Sie im [Leitfaden zur Migration vom Schätzer zu ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

## <a name="submit-your-run"></a>Übermitteln Ihrer Ausführung

Das [Run-Objekt](/python/api/azureml-core/azureml.core.run%28class%29) bildet die Schnittstelle zum Ausführungsverlauf, während der Auftrag ausgeführt wird und nachdem er abgeschlossen wurde.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Was passiert während der Ausführung
Die Ausführung durchläuft die folgenden Phasen:

- **Vorbereitung**: Ein Docker-Image wird entsprechend der definierten Umgebung erstellt. Das Image wird in die Containerregistrierung des Arbeitsbereichs hochgeladen und für spätere Ausführungen zwischengespeichert. Darüber hinaus werden Protokolle in den Ausführungsverlauf gestreamt, mit deren Hilfe der Status überwacht werden kann. Wenn stattdessen eine zusammengestellte Umgebung angegeben wird, wird das zwischengespeicherte Image verwendet, das diese zusammengestellte Umgebung unterstützt.

- **Skalierung**: Der Cluster versucht ein Hochskalieren, wenn der Batch KI-Cluster mehr Knoten zur Ausführung benötigt, als derzeit verfügbar sind.

- **Wird ausgeführt:** Alle Skripts im Skriptordner werden auf das Computeziel hochgeladen, Datenspeicher werden bereitgestellt oder kopiert, und `script` wird ausgeführt. Ausgaben aus „stdout“ und dem Ordner **./logs** werden in den Ausführungsverlauf gestreamt und können zur Überwachung der Ausführung verwendet werden.

- **Nachbearbeitung**: Der Ordner **./outputs** der Ausführung wird in den Ausführungsverlauf kopiert.

## <a name="register-or-download-a-model"></a>Registrieren oder Herunterladen eines Modells

Sobald Sie das Modell trainiert haben, können Sie es in Ihrem Arbeitsbereich registrieren. Die Modellregistrierung bietet die Möglichkeit, Ihre Modelle in Ihrem Arbeitsbereich zu speichern und zu versionieren, um die [Modellverwaltung und -bereitstellung](concept-model-management-and-deployment.md) zu vereinfachen.

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> Die Schrittanleitung zur Bereitstellung enthält einen Abschnitt zur Registrierung von Modellen, aber Sie können direkt zu [Erstellen eines Computeziels](how-to-deploy-and-where.md#choose-a-compute-target) für die Bereitstellung springen, da Sie bereits über ein registriertes Modell verfügen.

Mit dem Run-Objekt können Sie auch eine lokale Kopie des Modells herunterladen. Im Trainingsskript `pytorch_train.py` wird das Modell durch ein Speicherobjekt von PyTorch persistent in einem lokalen Ordner (lokal für das Computeziel) gespeichert. Sie können das Run-Objekt verwenden, um eine Kopie herunterzuladen.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Verteiltes Training

Azure Machine Learning unterstützt auch verteilte PyTorch-Aufträge auf mehreren Knoten, sodass Sie Ihre Trainingworkloads skalieren können. Sie können ganz einfach verteilte PyTorch-Aufträge ausführen. Azure ML verwaltet die Orchestrierung für Sie.

Azure ML unterstützt die Ausführung verteilter PyTorch-Aufträge sowohl mit Horovod als auch mit dem integrierten DistributedDataParallel-Modul von PyTorch.

Weitere Informationen zum verteilten Training finden Sie im [Leitfaden für das verteilte GPU-Training](how-to-train-distributed-gpu.md).

## <a name="export-to-onnx"></a>Exportieren nach ONNX

Um Rückschlüsse mit der [ONNX-Runtime](concept-onnx.md) zu optimieren, konvertieren Sie Ihr trainiertes PyTorch-Modell in dass ONNX-Format. Rückschlüsse oder Modellbewertungen stellen die Phase dar, in der das bereitgestellte Modell für die Vorhersage verwendet wird (meist für Produktionsdaten). Ein Beispiel finden Sie im [Tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie ein neuronales Deep Learning-Netz mithilfe von PyTorch in Azure Machine Learning trainiert und registriert. Um zu erfahren, wie Sie ein Modell bereitstellen, fahren Sie mit unserem Artikel zur Modellbereitstellung fort.

* [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md)
* [Erfassen einer Ausführungsmetrik während des Trainings](how-to-log-view-metrics.md)
* [Optimieren von Hyperparametern](how-to-tune-hyperparameters.md)
* [Bereitstellen eines trainierten Modells](how-to-deploy-and-where.md)
* [Verteiltes Trainieren von Deep Learning-Modellen in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
