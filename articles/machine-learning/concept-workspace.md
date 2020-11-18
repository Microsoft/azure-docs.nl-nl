---
title: Wat is een werk ruimte?
titleSuffix: Azure Machine Learning
description: De werk ruimte is de resource op het hoogste niveau voor Azure Machine Learning. Het houdt een geschiedenis bij van alle trainings runs, met Logboeken, metrische gegevens, uitvoer en een moment opname van uw scripts.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 7acb555ddde20d6512119af85a8db055bcd9eeed
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701650"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Wat is een Azure Machine Learning-werkruimte?

De werk ruimte is de resource op het hoogste niveau voor Azure Machine Learning, waardoor u een centrale locatie kunt gebruiken met alle artefacten die u maakt wanneer u Azure Machine Learning gebruikt.  De werk ruimte houdt een geschiedenis bij van alle uitvoeringen van de training, met inbegrip van Logboeken, metrische gegevens, uitvoer en een moment opname van uw scripts. U gebruikt deze informatie om te bepalen welke trainings uitvoering het beste model produceert.  

Wanneer u een model hebt dat u wilt, registreert u het bij de werk ruimte. Vervolgens gebruikt u de geregistreerde model-en Score scripts om te implementeren op Azure Container Instances, Azure Kubernetes-service of een veld-Programmeer bare poort matrix (FPGA) als een HTTP-eind punt op basis van de REST. U kunt het model ook implementeren op een Azure IoT Edge-apparaat als een module.

## <a name="taxonomy"></a>Taxonomie 

Een taxonomie van de werk ruimte wordt geïllustreerd in het volgende diagram:

[![Werkruimte taxonomie](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

In het diagram worden de volgende onderdelen van een werk ruimte weer gegeven:

+ Een werk ruimte kan [Azure machine learning reken instanties](concept-compute-instance.md)bevatten, cloud resources die zijn geconfigureerd met de python-omgeving die nodig is om Azure machine learning uit te voeren.

+ Met [gebruikers rollen](how-to-assign-roles.md) kunt u uw werk ruimte delen met andere gebruikers, teams of projecten.
+ [Reken doelen](concept-azure-machine-learning-architecture.md#compute-targets) worden gebruikt om uw experimenten uit te voeren.
+ Wanneer u de werk ruimte maakt, worden er ook [gekoppelde resources](#resources) voor u gemaakt.
+ [Experimenten](concept-azure-machine-learning-architecture.md#experiments) zijn trainings uitvoeringen die u gebruikt om uw modellen te bouwen.  
+ [Pijp lijnen](concept-azure-machine-learning-architecture.md#ml-pipelines) zijn herbruikbare werk stromen voor trainingen en trainingen van uw model.
+ Gegevens [sets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) bieden ondersteuning bij het beheer van de data die u gebruikt voor model training en het maken van pijp lijnen.
+ Zodra u een model hebt dat u wilt implementeren, maakt u een geregistreerd model.
+ Gebruik het geregistreerde model en een score script om een [implementatie-eind punt](concept-azure-machine-learning-architecture.md#endpoints)te maken.

## <a name="tools-for-workspace-interaction"></a>Hulp middelen voor werkruimte interactie

U kunt op de volgende manieren met uw werk ruimte werken:

> [!IMPORTANT]
> De hulpprogram ma's die zijn gemarkeerd (preview) zijn momenteel beschikbaar als open bare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

+ Op het web:
    + [Azure Machine Learning Studio ](https://ml.azure.com) 
    + [Azure Machine Learning-ontwerpprogramma](concept-designer.md) 
+ In een python-omgeving met de [Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
+ In elke R-omgeving met de [Azure machine learning SDK voor R (preview)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Op de opdracht regel met behulp van de Azure Machine Learning [cli-extensie](./reference-azure-machine-learning-cli.md)
+ [Azure Machine Learning VS code-uitbrei ding](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Machine learning met een werk ruimte

Machine learning-taken lezen en/of schrijven artefacten naar uw werk ruimte.

+ Voer een experiment uit om een model voor het schrijven van modellen te trainen in de werk ruimte.
+ Gebruik automatische ML om een model te trainen-schrijf de resultaten van de training naar de werk ruimte.
+ Registreer een model in de werk ruimte.
+ Een model implementeren: maakt gebruik van het geregistreerde model om een implementatie te maken.
+ Herbruikbare werk stromen maken en uitvoeren.
+ Machine learning artefacten weer geven, zoals experimenten, pijp lijnen, modellen en implementaties.
+ Modellen bijhouden en bewaken.

## <a name="workspace-management"></a>Werkruimte beheer

U kunt ook de volgende beheer taken voor werk ruimten uitvoeren:

| Werkruimte beheer taak   | Portal              | Studio | Python SDK/R SDK       | CLI        | VS-code
|---------------------------|---------|---------|------------|------------|------------|
| Een werkruimte maken        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Toegang tot de werk ruimte beheren    | **&check;**   || |  **&check;**    ||
| Reken resources maken en beheren    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Een VM voor een notebook maken |   | **&check;** | |     ||

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werk ruimte naar een ander abonnement of het verplaatsen van het abonnement dat eigenaar is naar een nieuwe Tenant, wordt niet ondersteund. Dit kan fouten veroorzaken.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Een werk ruimte maken

Er zijn meerdere manieren om een werk ruimte te maken:  

* Gebruik de [Azure Portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) voor een punt-en-klik-interface om elke stap stapsgewijs te door lopen.
* De [Azure machine learning SDK voor python](how-to-manage-workspace.md?tabs=python#create-a-workspace) gebruiken om een werk ruimte te maken in de vlucht vanuit Python-scripts of Jupyter-notebooks
* Gebruik een [Azure Resource Manager sjabloon](how-to-create-workspace-template.md) of de [Azure machine learning cli](reference-azure-machine-learning-cli.md) wanneer u het maken van de zakelijke beveiligings normen wilt automatiseren of aanpassen.
* Als u in Visual Studio code werkt, gebruikt u de [VS code-extensie](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> De naam van de werk ruimte is niet hoofdletter gevoelig.

## <a name="associated-resources"></a><a name="resources"></a> Gekoppelde resources

Wanneer u een nieuwe werk ruimte maakt, worden er automatisch verschillende Azure-resources gemaakt die worden gebruikt door de werk ruimte:

+ [Azure Storage account](https://azure.microsoft.com/services/storage/): wordt gebruikt als de standaard gegevens opslag voor de werk ruimte.  Jupyter-notebooks die worden gebruikt met uw Azure Machine Learning Reken instanties worden hier ook opgeslagen. 
  
  > [!IMPORTANT]
  > Het opslag account is standaard een v1-account voor algemeen gebruik. U kunt [deze upgrade uitvoeren naar de versie v2 voor algemeen gebruik](../storage/common/storage-account-upgrade.md) nadat de werk ruimte is gemaakt. Schakel de hiërarchische naam ruimte op het opslag account niet in na de upgrade naar de versie van het algemene doel v2.

  Als u een bestaand Azure Storage account wilt gebruiken, kan het geen Premium-account zijn (Premium_LRS en Premium_GRS). Het kan ook geen hiërarchische naam ruimte hebben (gebruikt met Azure Data Lake Storage Gen2). Geen enkele Premium-opslag of hiërarchische naam ruimten worden ondersteund met het _standaard_ opslag account van de werk ruimte. U kunt Premium-opslag of een hiërarchische naam ruimte gebruiken met _niet-standaard_ opslag accounts.
  
+ [Azure container Registry](https://azure.microsoft.com/services/container-registry/): registreert docker-containers die u tijdens de training gebruikt en wanneer u een model implementeert. Om de kosten te minimaliseren, wordt ACR **Lazy geladen** totdat implementatie installatie kopieën zijn gemaakt.

+ [Azure-toepassing Insights](https://azure.microsoft.com/services/application-insights/): slaat bewakings informatie over uw modellen op.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): slaat geheimen op die worden gebruikt door Compute-doelen en andere gevoelige informatie die nodig is voor de werk ruimte.

> [!NOTE]
> U kunt in plaats daarvan bestaande Azure-resource-instanties gebruiken wanneer u de werk ruimte met de [python-SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace), [R-sdk](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)of de Azure machine learning cli maakt [met behulp van een arm-sjabloon](how-to-create-workspace-template.md).

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Wat is er gebeurd met de Enter prise Edition?

Vanaf september 2020 zijn alle mogelijkheden die beschikbaar waren in Enter prise Edition-werk ruimten, nu ook beschikbaar in de Basic Edition-werk ruimten. Nieuwe ondernemings-werk ruimten kunnen niet meer worden gemaakt.  Alle SDK-, CLI-of Azure Resource Manager-aanroepen die gebruikmaken van de `sku` para meter blijven werken, maar er wordt een eenvoudige werk ruimte ingericht.

Vanaf 21 december worden alle Enter prise Edition-werk ruimten automatisch ingesteld op Basic Edition, dat dezelfde mogelijkheden heeft. Er wordt geen downtime uitgevoerd tijdens dit proces. Op 1 januari 2021 wordt Enter prise Edition formeel buiten gebruik gesteld. 

In beide edities zijn klanten verantwoordelijk voor de kosten van verbruikte Azure-resources en hoeven er geen extra kosten te worden betaald voor Azure Machine Learning. Raadpleeg de pagina met [prijzen voor Azure machine learning](https://azure.microsoft.com/pricing/details/machine-learning/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met Azure Machine Learning raadpleegt u:

+ [Overzicht van Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Een werk ruimte maken en beheren](how-to-manage-workspace.md)
+ [Zelf studie: aan de slag met Azure Machine Learning in uw ontwikkel omgeving](tutorial-1st-experiment-sdk-setup-local.md)
+ [Zelf studie: aan de slag met het maken van uw eerste ML-experiment op een reken instantie](tutorial-1st-experiment-sdk-setup.md)
+ [Zelf studie: aan de slag met Azure Machine Learning met de R SDK](tutorial-1st-r-experiment.md)
+ [Zelfstudie: Uw eerste classificatiemodel maken met geautomatiseerde machine learning](tutorial-first-experiment-automated-ml.md) 
+ [Zelfstudie: Autoprijzen voorspellen met de ontwerpfunctie](tutorial-designer-automobile-price-train-score.md)
