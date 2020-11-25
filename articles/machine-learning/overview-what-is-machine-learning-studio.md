---
title: What is Azure Machine Learning-studio?
description: Studio is een webportal voor Azure Machine Learning-werkruimten. In de studio worden geen-code en code-eerst ervaringen gecombineerd voor een allesomvattend datawetenschapsplatform.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
ms.openlocfilehash: 018a0e3a244d1f0e36ce638d646ab05861c10e38
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565905"
---
# <a name="what-is-azure-machine-learning-studio"></a>What is Azure Machine Learning-studio?

In dit artikel vindt u informatie over Azure Machine Learning Studio, de webportal voor datawetenschappers en ontwikkelaars in [Azure Machine Learning](overview-what-is-azure-ml.md). In de studio worden geen-code en code-eerst ervaringen gecombineerd voor een allesomvattend datawetenschapsplatform.

In dit artikel leert u het volgende:
>[!div class="checklist"]
> - [Machine learning-projecten maken](#author-machine-learning-projects) in de studio.
> - Informatie over het [beheren van assets en resources](#manage-assets-and-resources) in de studio.
> - De verschillen tussen [Azure Machine Learning Studio en ML Studio (klassiek)](#ml-studio-classic-vs-azure-machine-learning-studio).


## <a name="author-machine-learning-projects"></a>Machine learning-projecten ontwerpen

De studio biedt meerdere ontwerpfuncties, afhankelijk van het type project en de gebruikerservaring.

+ **Notebooks**

  Uw eigen code schrijven en uitvoeren in beheerde [Jupyter Notebook-servers](how-to-run-jupyter-notebooks.md) die rechtstreeks zijn geïntegreerd in de studio. 

+ **Azure Machine Learning-ontwerpprogramma**

  Gebruik de Designer om Machine learning-modellen te trainen en te implementeren zonder code te schrijven. U kunt gegevenssets en modules slepen en neerzetten om ML-pijplijnen te maken. Probeer de [zelfstudie over de ontwerpfunctie](tutorial-designer-automobile-price-train-score.md).

    ![Voorbeeld van ontwerpfunctie van Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

+ **Gebruikersinterface voor geautomatiseerde machine learning**

  Leer hoe u [geautomatiseerde ML-experimenten](tutorial-first-experiment-automated-ml.md) maakt met een gebruiksvriendelijke interface. 

  [![Navigatievenster van Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Gegevens labelen**

    Gebruik [Azure Machine Learning Gegevens labelen](how-to-create-labeling-projects.md) om gegevenslabelprojecten op een efficiënte manier te coördineren.

## <a name="manage-assets-and-resources"></a>Assets en resources beheren

Beheer uw machine learning-assets rechtstreeks in uw browser. Assets worden in dezelfde werkruimte gedeeld tussen de SDK en de studio voor een naadloze ervaring. Gebruik de studio om het volgende te beheren:

- Modellen
- Gegevenssets
- Gegevensarchieven
- Rekenresources
- Notebooks
- Experimenten
- Uitvoeringslogboeken
- Pipelines 
- Pijplijneindpunten

Zelfs als u een ervaren ontwikkelaar bent, kunt u met de studio eenvoudiger bepalen hoe u werkruimte-resources beheert.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (classic) vs Azure Machine Learning studio

**ML Studio (klassiek), uitgebracht in 2015,** was onze eerste machine learning-builder met fucntionaliteit voor slepen en neerzetten. Het is een zelfstandige service die alleen een visuele ervaring biedt. Studio (klassiek) werkt niet samen met Azure Machine Learning.

**Azure Machine Learning** is een afzonderlijke en moderne service die een volledig data science-platform biedt. Het ondersteunt zowel code-eerst als weinig-code ervaringen.

**Azure Machine Learning Studio** is een webportal *in* Azure Machine Learning met opties voor weinig-code en geen-code voor het ontwerpen van projecten en het beheren van assets. 

We raden nieuwe gebruikers aan om **Azure Machine Learning** te kiezen in plaats van ML Studio (klassiek), voor het meest recente aanbod van data science-hulpprogramma's.

### <a name="feature-comparison"></a>Vergelijking van functies

De volgende tabel bevat een overzicht van enkele van de belangrijkste verschillen tussen ML Studio (klassiek) en Azure Machine Learning.

| Functie | ML Studio (klassiek) | Azure Machine Learning |
|---| --- | --- |
| Interface met slepen en neerzetten | Klassieke ervaring | Bijgewerkte ervaring - [Azure Machine Learning Designer](concept-designer.md)| 
| Code-SDK's | Niet ondersteund | Volledig geïntegreerd met [Azure Machine Learning Python](/python/api/overview/azure/ml/) en [R](tutorial-1st-r-experiment.md) SDK's |
| Experiment | Schaalbaar (max. 10 GB aan trainingsgegevens) | Schalen met rekendoel |
| Rekendoelen voor training | Eigen rekendoel, alleen CPU-ondersteuning | Breed scala aan aanpasbare [rekendoelen voor training](concept-compute-target.md#train). Inclusief GPU- en CPU-ondersteuning | 
| Rekendoelen voor implementatie | Bedrijfseigen webservice-indeling, niet aanpasbaar | Breed scala aan aanpasbare [rekendoelen voor implementatie](concept-compute-target.md#deploy). Inclusief GPU- en CPU-ondersteuning |
| ML-pijplijn | Niet ondersteund | Bouw flexibele, modulaire [pijplijnen](concept-ml-pipelines.md) om werkstromen te automatiseren |
| MLOps | Eenvoudig modellen beheren en implementeren; alleen voor CPU-implementaties | Versiebeheer voor entiteiten (model, gegevens, werkstromen), werkstroomautomatisering, integratie met CICD-hulpprogramma, CPU- en GPU-implementaties [en meer](concept-model-management-and-deployment.md) |
| Modelindeling | Eigen indeling, alleen Studio (klassiek) | Meerdere ondersteunde indelingen, afhankelijk van het type trainingstaak |
| Geautomatiseerde modeltraining en afstemming van hyperparameters |  Niet ondersteund | [Ondersteund](concept-automated-ml.md). Opties voor code-eerst en geen-code. | 
| Gegevensdriftdetectie | Niet ondersteund | [Ondersteund](how-to-monitor-datasets.md) |
| Projecten voor gegevenslabels | Niet ondersteund | [Ondersteund](how-to-create-labeling-projects.md) |


## <a name="next-steps"></a>Volgende stappen

Ga naar de [studio](https://ml.azure.com)of leer meer over de verschillende ontwerpopties met deze zelfstudies:  

- + [Aan de slag gaan in uw eigen ontwikkelomgeving](tutorial-1st-experiment-sdk-setup-local.md)
  + [Jupyter-notebooks gebruiken in een rekenproces om modellen te trainen en te implementeren](tutorial-1st-experiment-sdk-setup.md)
  + [Geautomatiseerde machine learning gebruiken voor het trainen en implementeren van modellen](tutorial-first-experiment-automated-ml.md)  
  + [De ontwerpfunctie gebruiken om modellen te trainen en implementeren](tutorial-designer-automobile-price-train-score.md)
  + [Studio gebruiken in een beveiligd virtueel netwerk](how-to-enable-studio-virtual-network.md)