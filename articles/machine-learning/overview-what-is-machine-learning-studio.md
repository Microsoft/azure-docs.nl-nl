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
adobe-target: true
ms.openlocfilehash: 48c4b2a73628ab2105e23054d747e28acc105d01
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563182"
---
# <a name="what-is-azure-machine-learning-studio"></a>What is Azure Machine Learning-studio?

In dit artikel vindt u informatie over Azure Machine Learning Studio, de webportal voor datawetenschappers en ontwikkelaars in [Azure Machine Learning](overview-what-is-azure-ml.md). In de studio worden geen-code en code-eerst ervaringen gecombineerd voor een allesomvattend datawetenschapsplatform.

In dit artikel leert u het volgende:
>[!div class="checklist"]
> - [Machine learning-projecten maken](#author-machine-learning-projects) in de studio.
> - Informatie over het [beheren van assets en resources](#manage-assets-and-resources) in de studio.
> - De verschillen tussen [Azure Machine Learning Studio en ML Studio (klassiek)](#ml-studio-classic-vs-azure-machine-learning-studio).

U wordt aangeraden om de meest recente browser te gebruiken die compatibel is met het besturingssysteem. De volgende browsers worden ondersteund:
  * Microsoft Edge (de nieuwe, meest recente versie van Microsoft Edge. Geen verouderde versie van Microsoft Edge)
  * Safari (meest recente versie, alleen Mac)
  * Chrome (meest recente versie)
  * Firefox (meest recente versie)

## <a name="author-machine-learning-projects"></a>Machine learning-projecten ontwerpen

De studio biedt meerdere ontwerpfuncties, afhankelijk van het type project en de gebruikerservaring.

+ **Notebooks**

  Uw eigen code schrijven en uitvoeren in beheerde [Jupyter Notebook-servers](how-to-run-jupyter-notebooks.md) die rechtstreeks zijn geïntegreerd in de studio. 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="Schermopname: code schrijven en uitvoeren in een notebook":::

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

**ML Studio (klassiek), uitgebracht in 2015,** was onze eerste machine learning-builder met fucntionaliteit voor slepen en neerzetten. 

**Ml Studio (klassiek)** is een zelfstandige service die alleen een visuele ervaring biedt. Studio (klassiek) werkt niet samen met Azure Machine Learning.

**Azure machine learning** is een afzonderlijke en moderne service die een volledig data Science-platform biedt. Het ondersteunt zowel code-eerst als weinig-code ervaringen.

**Azure Machine Learning Studio** is een webportal *in* Azure Machine Learning met opties voor weinig-code en geen-code voor het ontwerpen van projecten en het beheren van assets. 

We raden nieuwe gebruikers aan om **Azure Machine Learning** te kiezen in plaats van ML Studio (klassiek), voor het meest recente aanbod van data science-hulpprogramma's. Als u een bestaande ML Studio (klassieke) gebruiker bent, kunt u overwegen [om naar Azure machine learning te migreren](classic/migrate-overview.md).

Hier volgen enkele van de voor delen van het overschakelen naar Azure Machine Learning:

- Schaal bare reken clusters voor grootschalige training.
- Enter prise Security en governance.
- Compatibel met populaire open-source hulpprogram ma's.
- End-to-end-MLOps.

### <a name="feature-comparison"></a>Vergelijking van functies

[!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

## <a name="troubleshooting"></a>Problemen oplossen

* **Ontbrekende items van de Studio-gebruikersinterface**. In Azure kan op rollen gebaseerd toegangsbeheer worden gebruikt om de acties te beperken die u kunt uitvoeren met Azure Machine Learning. Door deze beperkingen kan het gebeuren dat bepaalde items van de gebruikersinterface niet worden weergegeven in Azure Machine Learning Studio. Als aan u bijvoorbeeld een rol is toegewezen op basis waarvan u geen rekenproces kunt maken, wordt de optie voor het maken van rekenprocessen niet weergegeven in Studio. Zie [Gebruikers en rollen beheren](how-to-assign-roles.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Ga naar de [studio](https://ml.azure.com)of leer meer over de verschillende ontwerpopties met deze zelfstudies:  

- + [Aan de slag gaan in uw eigen ontwikkelomgeving](tutorial-1st-experiment-sdk-setup-local.md)
  + [Jupyter-notebooks gebruiken in een rekenproces om modellen te trainen en te implementeren](tutorial-1st-experiment-sdk-setup.md)
  + [Geautomatiseerde machine learning gebruiken voor het trainen en implementeren van modellen](tutorial-first-experiment-automated-ml.md)  
  + [De ontwerpfunctie gebruiken om modellen te trainen en implementeren](tutorial-designer-automobile-price-train-score.md)
  + [Studio gebruiken in een beveiligd virtueel netwerk](how-to-enable-studio-virtual-network.md)