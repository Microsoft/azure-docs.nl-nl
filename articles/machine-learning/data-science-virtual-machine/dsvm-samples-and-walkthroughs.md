---
title: Voorbeeld Programma's & ML-scenario's
titleSuffix: Azure Data Science Virtual Machine
description: In deze voor beelden en instructies leert u hoe u veelvoorkomende taken en scenario's kunt verwerken met de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: cda5dfd936243602775e1f4f965032b9d746b0b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519759"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Voor beelden van Azure data Science Virtual Machines

Azure data Science Virtual Machines (Dsvm) bevat een uitgebreide set voorbeeld code. Dit zijn voor beelden van Jupyter-notebooks en-scripts in talen als python en R.
> [!NOTE]
> Zie de sectie [toegangs Jupyter](#access-jupyter) voor meer informatie over het uitvoeren van Jupyter-notebooks op uw virtuele machines voor data technologie.

## <a name="prerequisites"></a>Vereisten

Als u deze voor beelden wilt uitvoeren, moet u een Ubuntu- [Data Science virtual machine](./dsvm-ubuntu-intro.md)hebben ingericht.

## <a name="available-samples"></a>Beschikbare voorbeelden
| Categorie voor beelden | Beschrijving | Locaties |
| ------------- | ------------- | ------------- |
| R-taal  | Voor beelden illustreren scenario's zoals het maken van verbinding met gegevens archieven op basis van Azure en het vergelijken van open source R en Microsoft Machine Learning Server. Er wordt ook uitgelegd hoe u operationeel maken modellen op Microsoft Machine Learning Server en SQL Server. <br/> [R-taal](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python-taal  | Voor beelden van scenario's zoals het maken van verbinding met gegevens archieven op basis van Azure en het werken met Azure Machine Learning.  <br/> [Python-taal](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia taal  | Hier vindt u een gedetailleerde beschrijving van het uitzetten en diep gaande leren in Julia. Ook wordt uitgelegd hoe u C en python aanroept vanuit Julia. <br/> [Julia taal](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Illustreert hoe u machine learning-en diep gaande modellen bouwt met Machine Learning. Implementeer modellen overal. Gebruik geautomatiseerde machine learning en intelligent afstemming tuning. Gebruik ook model beheer en gedistribueerde training. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch-notebooks  | Voor beelden van diep gaande lessen die gebruikmaken van op PyTorch gebaseerde Neural netwerken. Laptops variëren van beginners tot geavanceerde scenario's.  <br/> [PyTorch-notebooks](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Diverse Neural-netwerk voorbeelden en-technieken die worden geïmplementeerd met behulp van het tensor flow-Framework. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Voor beelden van diep gaande lessen die door het Cognitive Toolkit-team bij micro soft worden gepubliceerd.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Voor beelden van diep gaande lessen die gebruikmaken van op Caffe2 gebaseerde Neural netwerken. Met verschillende notebooks worden gebruikers met Caffe2 vertrouwd en wordt uitgelegd hoe u het effectief kunt gebruiken. Voor beelden zijn onder andere afbeeldings voorverwerken en het maken van gegevensset. Ze omvatten ook regressie en het gebruik van voortrainde modellen. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Voor beelden op basis van python die H2O gebruiken voor scenario's met een praktijk probleem. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML taal  | Voor beelden die gebruikmaken van functies van de Apache Spark MLLib Toolkit via pySpark en MMLSpark: Micro Soft Machine Learning voor Apache Spark op Apache Spark 2. x.  <br/> [SparkML taal](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standaard voor beelden van machine learning in XGBoost voor scenario's zoals classificatie en regressie. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Toegang tot Jupyter 

Om toegang te krijgen tot Jupyter, selecteert u het pictogram **Jupyter** in het menu bureau blad of toepassing. U hebt ook toegang tot Jupyter op een Linux-editie van een DSVM. Als u extern vanuit een webbrowser wilt openen, gaat u naar `https://<Full Domain Name or IP Address of the DSVM>:8000` op Ubuntu.

Gebruik de volgende richt lijnen om uitzonde ringen toe te voegen en de toegang tot Jupyter via een browser beschikbaar te maken:


![Jupyter-uitzonde ring inschakelen](./media/ubuntu-jupyter-exception.png)


Meld u aan met het wacht woord dat u gebruikt om u aan te melden bij de Data Science Virtual Machine.
<br/>

**Jupyter-start pagina**
<br/>![Jupyter-start pagina](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R-taal 
<br/>![R-voor beelden](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python-taal
<br/>![Python-voorbeelden](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia taal 
<br/>![Julia-voor beelden](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure Machine Learning-voor beelden](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch-voor beelden](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Tensor flow-voor beelden](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK-voor beelden](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2-voor beelden](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O-voor beelden](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML-voor beelden](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost-voor beelden](./media/xgboost-samples.png)<br/>

