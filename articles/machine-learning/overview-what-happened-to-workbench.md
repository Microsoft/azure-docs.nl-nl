---
title: Wat is er gebeurd met Workbench?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning is een geïntegreerde oplossing voor gegevens wetenschap voor het model leren en implementeren van ML-toepassingen op Cloud schaal. De functie Workbench is buiten gebruik gesteld.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: larryfr
author: BlackMist
ms.date: 03/05/2020
ms.openlocfilehash: c800901ae139b16e75613b0cc8de3a9485186896
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521370"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Wat is er gebeurd met Azure Machine Learning Workbench?

De Azure Machine Learning Workbench-toepassing en enkele andere vroege functies zijn afgeschaft en vervangen in de release van **September 2018** om een verbeterde [architectuur](concept-azure-machine-learning-architecture.md)te maken.

De nieuwe versie bevat veel belangrijke updates op basis van feedback van klanten, om uw ervaring te verbeteren. De kernfunctionaliteit voor experimentele uitvoeringen tot modelimplementatie is niet gewijzigd. U kunt nu echter de robuuste <a href="/python/api/overview/azure/ml/intro" target="_blank">PYTHON SDK</a>, R SDK en de [Azure cli](reference-azure-machine-learning-cli.md) gebruiken om uw machine learning taken en pijp lijnen uit te voeren.

De meeste artefacten die zijn gemaakt in de eerdere versie van Azure Machine Learning worden opgeslagen in uw eigen lokale of Cloud opslag. Deze artefacten worden nooit verwijderd.

In dit artikel leest u wat er is veranderd en hoe dit van invloed is op uw bestaande werk met de Azure Machine Learning Workbench en de bijbehorende API's.

>[!Warning]
>Dit artikel is niet bedoeld voor gebruikers van Azure Machine Learning Studio, Het is voor Azure Machine Learning klanten die de workbench-toepassing (preview) hebben geïnstalleerd en/of een preview-account hebben voor experimenteren en model beheer.


## <a name="what-changed"></a>Wat is er veranderd?

De nieuwste versie van Azure Machine Learning bevat de volgende functies:
+ Een [vereenvoudigd Azure-resource model](concept-azure-machine-learning-architecture.md).
+ Een [nieuwe gebruikersinterface in de portal](how-to-track-experiments.md) voor het beheren van uw experimenten en compute-doelen.
+ Een nieuwe, uitgebreidere Python-<a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>.
+ De nieuwe, uitgebreide [Azure CLI-extensie](reference-azure-machine-learning-cli.md) voor machine learning.

De [architectuur](concept-azure-machine-learning-architecture.md) is vernieuwd voor meer gebruiksgemak. In plaats van meerdere Azure-resources en -accounts, hebt u alleen een [Azure Machine Learning-werkruimte](concept-workspace.md) nodig. U kunt snel werkruimten maken in de [Azure-portal](how-to-manage-workspace.md). Met een werkruimte kunnen meerdere gebruikers rekendoelen voor training en implementatie, modelexperimenten, Docker-installatiekopieën, geïmplementeerde modellen, enzovoort, opslaan.

De huidige versie bevat nieuwe en verbeterde CLI- en SDK-clients, maar de Workbench-bureaubladtoepassing zelf is afgeschaft. Experimenten kunnen in Azure Machine Learning Studio worden beheerd in het [dash board werk ruimte](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal). Gebruik het dashboard om uw experimentgeschiedenis op te halen, de compute-doelen die zijn gekoppeld aan uw werkruimte te beheren, uw modellen en Docker-installatiekopieën te beheren en zelfs om webservices te implementeren.

<a name="timeline"></a>

## <a name="support-timeline"></a>Ondersteuningstijdlijn

Op 9 januari 2019 wordt ondersteuning geboden voor Machine Learning Workbench, Azure Machine Learning Experimenten-en Modelbeheer-accounts, en de bijbehorende SDK en CLI zijn geëindigd.

Alle nieuwste mogelijkheden zijn beschikbaar via deze nieuwe <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>, de [CLI](reference-azure-machine-learning-cli.md) en de [portal](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>Hoe zit het met uitvoeringsgeschiedenissen?

Oudere uitvoeringsgeschiedenissen zijn niet meer toegankelijk, maar uw uitvoeringen zijn nog wel zichtbaar in de nieuwste versie.

Uitvoeringsgeschiedenissen heten voortaan **experimenten**. U kunt de experimenten van uw model verzamelen en deze verkennen met behulp van de SDK, de CLI of de Azure Machine Learning Studio.

Het werkruimtedashboard van de portal wordt alleen ondersteund in Microsoft Edge, Chrome en Firefox:

[![Online Portal](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Gebruik de nieuwe CLI en SDK om uw modellen te trainen en de uitvoeringsgeschiedenis bij te houden. U kunt lezen hoe u met de [zelf studie modellen traint met Azure machine learning](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Blijven projecten behouden?

U verliest geen code of werk. Projecten zijn in de oudere versie cloudentiteiten met een lokale map. In de nieuwste versie koppelt u lokale mappen aan de Azure Machine Learning-werk ruimte met behulp van een lokaal configuratie bestand. Bekijk een [diagram van de meest recente architectuur](concept-azure-machine-learning-architecture.md).

Veel van de projectinhoud staat al op uw lokale computer. U hoeft alleen een configuratiebestand in die map te maken en ernaar te verwijzen in uw code om het aan uw werkruimte te koppelen. Als u wilt door gaan met het gebruik van de lokale map met uw bestanden en scripts, geeft u de naam van de map op in de opdracht ' [experiment. Submit '](/python/api/azureml-core/azureml.core.experiment.experiment) van python of met de `az ml project attach` cli-opdracht.  Bijvoorbeeld:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Maak een werk ruimte](how-to-manage-workspace.md) om aan de slag te gaan.

## <a name="what-about-my-registered-models-and-images"></a>Hoe zit het met mijn geregistreerde modellen en installatiekopieën?

De modellen die u in uw oude modelregister hebt geregistreerd, moeten worden gemigreerd naar uw nieuwe werkruimte als u ze wilt blijven gebruiken. Voor het migreren van uw modellen downloadt u de modellen en registreert u deze opnieuw in uw nieuwe werkruimte.

De installatie kopieën die u in het oude installatie kopie register hebt gemaakt, kunnen niet rechtstreeks naar de nieuwe werk ruimte worden gemigreerd. In de meeste gevallen kan het model worden geïmplementeerd zonder dat er een installatie kopie hoeft te worden gemaakt. Als dat nodig is, kunt u een installatie kopie maken voor het model in de nieuwe werk ruimte. Zie [machine learning modellen beheren, registreren, implementeren en bewaken](concept-model-management-and-deployment.md)voor meer informatie.

## <a name="what-about-deployed-web-services"></a>Hoe zit het met geïmplementeerde webservices?

Aangezien de ondersteuning voor de oude CLI is beëindigd, kunt u modellen of webservices die u oorspronkelijk hebt geïmplementeerd met uw account voor modelbeheer niet meer opnieuw implementeren of beheren. Deze webservices blijven echter werken zolang Azure Container Service (ACS) wordt ondersteund.

In de nieuwste versie worden modellen als webservices geïmplementeerd in ACI-clusters (Azure Container Instances) of AKS-clusters (Azure Kubernetes Service). U kunt ook in FPGA's en Azure IoT Edge implementeren.

Ga voor meer informatie naar deze artikelen:
+ [Modellen implementeren met Azure Machine Learning Service](how-to-deploy-and-where.md)
+ [Zelf studie: modellen implementeren met Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [nieuwste architectuur voor Azure machine learning](concept-azure-machine-learning-architecture.md).

Lees [Wat is Azure machine learning?](overview-what-is-azure-ml.md)voor een overzicht van de service.

Maak uw eerste experiment met de methode van uw voorkeur:

  + [Uw eigen omgeving gebruiken](tutorial-1st-experiment-sdk-setup-local.md)
  + [Python-notebooks gebruiken](tutorial-1st-experiment-sdk-setup.md)
  + [R-prijs verlaging gebruiken](https://github.com/Azure/azureml-sdk-for-r) 
  + [Automatische machine learning gebruiken](tutorial-designer-automobile-price-train-score.md) 
  + [De Sleep mogelijkheden van de ontwerp functie voor het Designer-& gebruiken](tutorial-first-experiment-automated-ml.md) 
  + [De uitbrei ding voor ML van de CLI gebruiken](tutorial-train-deploy-model-cli.md)
