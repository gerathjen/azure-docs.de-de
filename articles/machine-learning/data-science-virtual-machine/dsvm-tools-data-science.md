---
title: Machine Learning- und Data Science-Tools
titleSuffix: Azure Data Science Virtual Machine
description: Erfahren Sie mehr über die Machine Learning-Tools und -Frameworks, die auf der Data Science Virtual Machine-Instanz vorinstalliert sind.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: 86cbac686c2f994dff4042ea2a227156d9e45472
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339813"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Tools für maschinelles Lernen und Data Science in virtuellen Azure-Data Science Virtual Machines
Azure Data Science Virtual Machine-Instanzen (DSVMs) verfügen über ein umfangreiches Angebot an Tools und Bibliotheken für maschinelles Lernen in gängigen Sprachen wie Python, R und Julia.

Im Anschluss sind einige der Machine Learning-Tools und -Bibliotheken auf DSVMs aufgeführt.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK für Python

Lesen Sie die vollständige Referenz zum [Azure Machine Learning SDK für Python](../overview-what-is-azure-machine-learning.md).

| Category | Wert |
| ------------- | ------------- |
| Was ist das?   |   Azure Machine Learning-Dienst ist ein Clouddienst, den Sie zum Entwickeln und Bereitstellen von Machine Learning-Modellen verwenden können. Sie können Ihre Modelle beim Erstellen, Trainieren, Skalieren und Verwalten mit dem Python SDK nachverfolgen. Stellen Sie Modelle als Container bereit, und führen Sie sie in der Cloud, lokal oder in Azure IoT Edge aus.   |
| Unterstützte Editionen     | Windows (Conda-Umgebung: AzureML), Linux (Conda-Umgebung: py36)    |
| Typische Verwendung      | Allgemeine Plattform für maschinelles Lernen      |
| Konfiguration/Installation      |  Installiert mit GPU-Unterstützung   |
| Verwendung/Ausführung      | Als Python SDK und in der Azure CLI. Aktivieren Sie die Conda-Umgebung `AzureML` für die Windows-Edition *oder*`py36` für die Linux-Edition.      |
| Link zu Beispielen      | Exemplarische Jupyter-Notebooks finden Sie im Verzeichnis `AzureML` unter „notebooks“.  |

## <a name="h2o"></a>H2O

| Category | Wert |
| ------------- | ------------- |
| Was ist das?   | Eine Open-Source-KI-Plattform für verteiltes, schnelles und skalierbares maschinelles In-Memory-Lernen.  |
| Unterstützte Versionen      | Linux   |
| Typische Verwendung      | Universelles verteiltes und skalierbares maschinelles Lernen   |
| Konfiguration/Installation      | H2O ist unter `/dsvm/tools/h2o` installiert.      |
| Verwendung/Ausführung      | Stellen Sie über X2Go eine Verbindung mit dem virtuellen Computer her. Starten Sie ein neues Terminal, und führen Sie `java -jar /dsvm/tools/h2o/current/h2o.jar` aus. Stellen Sie dann über einen Webbrowser eine Verbindung mit `http://localhost:54321` her.      |
| Link zu Beispielen      | Beispiele stehen auf dem virtuellen Computer in Jupyter im Verzeichnis `h2o` zur Verfügung.      |

Es gibt mehrere weitere Machine Learning-Bibliotheken auf DSVMs, z. B. das beliebte `scikit-learn`-Paket, das Teil der Anaconda Python-Distribution für DSVMs ist. Führen Sie die entsprechenden Paket-Manager aus, um sich die Liste mit den verfügbaren Paketen für Python, R und Julia anzusehen.

## <a name="lightgbm"></a>LightGBM

| Category | Wert |
| ------------- | ------------- |
| Was ist das?   | Ein schnelles, verteiltes Gradient Boosting-Hochleistungsframework (GBDT, GBRT, GBM oder MART) auf der Grundlage von Entscheidungsstrukturalgorithmen. Es wird für Rangfolgen, Klassifizierungen und viele andere Aufgaben im Bereich Machine Learning verwendet.    |
| Unterstützte Versionen      | Windows, Linux    |
| Typische Verwendung      | Allgemeines Gradient-Boosted-Framework      |
| Konfiguration/Installation      | Unter Windows ist LightGBM als Python-Paket installiert. Unter Linux befindet sich die über die Befehlszeile ausführbare Datei unter `/opt/LightGBM/lightgbm`. Außerdem sind das R-Paket und Python-Pakete installiert.     |
| Link zu Beispielen      | [Leitfaden zu LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |

## <a name="rattle"></a>Rattle
| Category | Wert |
| ------------- | ------------- |
| Was ist das?   |   Eine grafische Benutzeroberfläche für Data Mining unter Verwendung von R.   |
| Unterstützte Editionen     | Windows, Linux     |
| Typische Verwendung      | Allgemeines UI-basiertes Data Mining-Tool für R    |
| Verwendung/Ausführung      | Als Benutzeroberflächentool. Starten Sie unter Windows eine Eingabeaufforderung, und führen Sie R und anschließend `rattle()` (innerhalb von R) aus. Stellen Sie unter Linux eine Verbindung über X2Go her, starten Sie ein Terminal, führen Sie R aus, und führen Sie dann `rattle()` in R aus. |
| Link zu Beispielen      | [Rattle](https://togaware.com/onepager/) |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
| Category | Wert |
| ------------- | ------------- |
| Was ist das?   |   Eine schnelle Open-Source-basierte Out-of-Core-Lernsystembibliothek    |
| Unterstützte Editionen     | Windows, Linux     |
| Typische Verwendung      | Allgemeine Bibliothek für maschinelles Lernen      |
| Konfiguration/Installation      |  Windows: MSI-Installationsprogramm<br/>Linux: apt-get |
| Verwendung/Ausführung      | Als pfadbasiertes Befehlszeilentool (`C:\Program Files\VowpalWabbit\vw.exe` unter Windows, `/usr/bin/vw` unter Linux)    |
| Link zu Beispielen      | [Beispiele für VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |

## <a name="weka"></a>Weka
| Category | Wert |
| ------------- | ------------- |
| Was ist das?   |  Eine Sammlung von Machine Learning-Algorithmen für Data Mining-Aufgaben. Die Algorithmen können entweder direkt auf ein Dataset angewendet oder über Ihren eigenen Java-Code aufgerufen werden. Weka enthält Tools für Datenvorverarbeitung, Klassifizierung, Regression, Clustering, Zuordnungsregeln und Visualisierung. |
| Unterstützte Editionen     | Windows, Linux     |
| Typische Verwendung      | Allgemeines Machine Learning-Tool     |
| Verwendung/Ausführung      | Unter Windows finden Sie Weka im **Startmenü**. Unter Linux finden Sie Weka nach der Anmeldung mit X2Go unter **Anwendungen** > **Entwicklung** > **Weka**. |
| Link zu Beispielen      | [Beispiele für Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |

## <a name="xgboost"></a>XGBoost 
| Category | Wert |
| ------------- | ------------- |
| Was ist das?   |   Schnelle, portable und verteilte Gradient-Boosted-Bibliothek (GBDT, GBRT oder GBM) für Python, R, Java, Scala, C++ und mehr. Sie eignet sich für einen einzelnen Computer und in Apache Hadoop und Spark.    |
| Unterstützte Editionen     | Windows, Linux     |
| Typische Verwendung      | Allgemeine Bibliothek für maschinelles Lernen      |
| Konfiguration/Installation      |  Installiert mit GPU-Unterstützung   |
| Verwendung/Ausführung      | Als Python-Bibliothek (2.7 und 3.6 und höher ), R-Paket und pfadbasiertes Befehlszeilentool (`C:\dsvm\tools\xgboost\bin\xgboost.exe` für Windows und `/dsvm/tools/xgboost/xgboost` für Linux)    |
| Links zu Beispielen      | Beispiele stehen auf dem virtuellen Computer unter `/dsvm/tools/xgboost/demo` (Linux) bzw. unter `C:\dsvm\tools\xgboost\demo` (Windows) zur Verfügung.   |
