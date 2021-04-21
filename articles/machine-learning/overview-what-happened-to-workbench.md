---
title: Wat is er gebeurd met Workbench?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning is een geïntegreerde data science-oplossing voor het modelleren en implementeren van ML-toepassingen op cloudschaal. De workbench-functie is uit bedrijf genomen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: larryfr
author: BlackMist
ms.date: 03/05/2020
ms.openlocfilehash: 4c680be897c4c1bf2ccf20df1d34ab6f59f559f2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816148"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Wat is er gebeurd met Azure Machine Learning Workbench?

De Azure Machine Learning Workbench-toepassing en een aantal andere vroege functies zijn afgeschaft en vervangen in de release van september **2018** om plaats te maken voor een verbeterde [architectuur.](concept-azure-machine-learning-architecture.md)

De nieuwe versie bevat veel belangrijke updates op basis van feedback van klanten, om uw ervaring te verbeteren. De kernfunctionaliteit voor experimentele uitvoeringen tot modelimplementatie is niet gewijzigd. U kunt nu echter de robuuste <a href="/python/api/overview/azure/ml/intro" target="_blank">Python SDK, R SDK</a>en [de Azure CLI](reference-azure-machine-learning-cli.md) gebruiken om uw machine learning en pijplijnen uit te voeren.

De meeste artefacten die zijn gemaakt in de eerdere versie van Azure Machine Learning worden opgeslagen in uw eigen lokale opslag of cloudopslag. Deze artefacten worden nooit verwijderd.

In dit artikel leest u wat er is veranderd en hoe dit van invloed is op uw bestaande werk met de Azure Machine Learning Workbench en de bijbehorende API's.

>[!Warning]
>Dit artikel is niet bedoeld voor gebruikers van Azure Machine Learning Studio, Het is voor Azure Machine Learning die de Workbench-toepassing (preview) hebben geïnstalleerd en/of preview-accounts voor experimenten en modelbeheer hebben.


## <a name="what-changed"></a>Wat is er veranderd?

De nieuwste versie van Azure Machine Learning bevat de volgende functies:
+ Een [vereenvoudigd Azure-resourcesmodel.](concept-azure-machine-learning-architecture.md)
+ Een [nieuwe gebruikersinterface in de portal](how-to-log-view-metrics.md) voor het beheren van uw experimenten en compute-doelen.
+ Een nieuwe, uitgebreidere Python-<a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>.
+ De nieuwe, uitgebreide [Azure CLI-extensie](reference-azure-machine-learning-cli.md) voor machine learning.

De [architectuur](concept-azure-machine-learning-architecture.md) is vernieuwd voor meer gebruiksgemak. In plaats van meerdere Azure-resources en -accounts, hebt u alleen een [Azure Machine Learning-werkruimte](concept-workspace.md) nodig. U kunt snel werkruimten maken in de [Azure-portal](how-to-manage-workspace.md). Met een werkruimte kunnen meerdere gebruikers rekendoelen voor training en implementatie, modelexperimenten, Docker-installatiekopieën, geïmplementeerde modellen, enzovoort, opslaan.

De huidige versie bevat nieuwe en verbeterde CLI- en SDK-clients, maar de Workbench-bureaubladtoepassing zelf is afgeschaft. Experimenten kunnen worden beheerd in het [werkruimtedashboard in Azure Machine Learning-studio](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal). Gebruik het dashboard om uw experimentgeschiedenis op te halen, de compute-doelen die zijn gekoppeld aan uw werkruimte te beheren, uw modellen en Docker-installatiekopieën te beheren en zelfs om webservices te implementeren.

<a name="timeline"></a>

## <a name="support-timeline"></a>Ondersteuningstijdlijn

Op 9 januari 2019 is de ondersteuning voor Machine Learning Workbench, Azure Machine Learning Experimenten en Modelbeheer-accounts en de bijbehorende SDK en CLI beëindigd.

Alle nieuwste mogelijkheden zijn beschikbaar via deze nieuwe <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>, de [CLI](reference-azure-machine-learning-cli.md) en de [portal](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>Hoe zit het met uitvoeringsgeschiedenissen?

Oudere uitvoeringsgeschiedenissen zijn niet meer toegankelijk, maar uw uitvoeringen zijn nog wel zichtbaar in de nieuwste versie.

Uitvoeringsgeschiedenissen heten voortaan **experimenten**. U kunt de experimenten van uw model verzamelen en deze verkennen met behulp van de SDK, de CLI of de Azure Machine Learning-studio.

Het werkruimtedashboard van de portal wordt alleen ondersteund in Microsoft Edge, Chrome en Firefox:

[![Online portal](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Gebruik de nieuwe CLI en SDK om uw modellen te trainen en de uitvoeringsgeschiedenis bij te houden. U leert hoe u met de [Zelfstudie: modellen trainen met Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Blijven projecten behouden?

U verliest geen code of werk. Projecten zijn in de oudere versie cloudentiteiten met een lokale map. In de nieuwste versie koppelt u lokale mapjes aan de Azure Machine Learning werkruimte met behulp van een lokaal configuratiebestand. Bekijk een [diagram van de meest recente architectuur](concept-azure-machine-learning-architecture.md).

Veel van de projectinhoud staat al op uw lokale computer. U hoeft alleen een configuratiebestand in die map te maken en ernaar te verwijzen in uw code om het aan uw werkruimte te koppelen. Als u de lokale map met uw bestanden en scripts wilt blijven gebruiken, geeft u de naam van de map op in de Python-opdracht [experiment.submit](/python/api/azureml-core/azureml.core.experiment.experiment) of met behulp van de `az ml project attach` CLI-opdracht.  Bijvoorbeeld:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Maak een werkruimte om](how-to-manage-workspace.md) aan de slag te gaan.

## <a name="what-about-my-registered-models-and-images"></a>Hoe zit het met mijn geregistreerde modellen en installatiekopieën?

De modellen die u in uw oude modelregister hebt geregistreerd, moeten worden gemigreerd naar uw nieuwe werkruimte als u ze wilt blijven gebruiken. Voor het migreren van uw modellen downloadt u de modellen en registreert u deze opnieuw in uw nieuwe werkruimte.

De afbeeldingen die u in het oude register met afbeeldingen hebt gemaakt, kunnen niet rechtstreeks naar de nieuwe werkruimte worden gemigreerd. In de meeste gevallen kan het model worden geïmplementeerd zonder dat u een afbeelding moet maken. Indien nodig kunt u een afbeelding voor het model maken in de nieuwe werkruimte. Zie [Manage, register, deploy, and monitor machine learning models (Beheren, registreren, implementeren machine learning bewaken) voor meer informatie.](concept-model-management-and-deployment.md)

## <a name="what-about-deployed-web-services"></a>Hoe zit het met geïmplementeerde webservices?

Aangezien de ondersteuning voor de oude CLI is beëindigd, kunt u modellen of webservices die u oorspronkelijk hebt geïmplementeerd met uw account voor modelbeheer niet meer opnieuw implementeren of beheren. Deze webservices blijven echter werken zolang Azure Container Service (ACS) wordt ondersteund.

In de nieuwste versie worden modellen als webservices geïmplementeerd in ACI-clusters (Azure Container Instances) of AKS-clusters (Azure Kubernetes Service). U kunt ook in FPGA's en Azure IoT Edge implementeren.

Ga voor meer informatie naar deze artikelen:
+ [Modellen implementeren met Azure Machine Learning Service](how-to-deploy-and-where.md)
+ [Zelfstudie: Modellen implementeren met Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [de nieuwste architectuur voor Azure Machine Learning.](concept-azure-machine-learning-architecture.md)

Lees Voor een overzicht van de service [Wat is Azure Machine Learning?](overview-what-is-azure-ml.md).

Maak uw eerste experiment met de methode van uw voorkeur:

  + [Uw eigen omgeving gebruiken](tutorial-1st-experiment-sdk-setup-local.md)
  + [Python-notebooks gebruiken](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown gebruiken](https://github.com/Azure/azureml-sdk-for-r) 
  + [Geautomatiseerde machine learning](tutorial-designer-automobile-price-train-score.md) 
  + [Gebruik de mogelijkheden voor slepen en neerzetten & ontwerpfunctie](tutorial-first-experiment-automated-ml.md) 
  + [De ML-extensie gebruiken voor de CLI](tutorial-train-deploy-model-cli.md)
