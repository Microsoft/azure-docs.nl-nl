---
title: Wat is een werkruimte?
titleSuffix: Azure Machine Learning
description: De werkruimte is de resource op het hoogste niveau voor Azure Machine Learning. Het houdt een geschiedenis bij van alle trainingsuitvoer, met logboeken, metrische gegevens, uitvoer en een momentopname van uw scripts.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 215da0e38045a2e66a4a11b54204c26e7720815c
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719057"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Wat is een Azure Machine Learning-werkruimte?

De werkruimte is de resource op het hoogste Azure Machine Learning en biedt een centrale plaats om te werken met alle artefacten die u maakt wanneer u Azure Machine Learning.  De werkruimte houdt een geschiedenis bij van alle trainingsuitvoer, inclusief logboeken, metrische gegevens, uitvoer en een momentopname van uw scripts. U gebruikt deze informatie om te bepalen welke trainingsuitloop het beste model produceert.  

Zodra u een model hebt dat u wilt, registreert u het bij de werkruimte. Vervolgens gebruikt u het geregistreerde model en scorescripts om te implementeren naar Azure Container Instances, Azure Kubernetes Service of een field-programmable gate array (FPGA) als een OP REST gebaseerd HTTP-eindpunt. U kunt het model ook implementeren op Azure IoT Edge apparaat als module.

## <a name="taxonomy"></a>Taxonomie 

Een taxonomie van de werkruimte wordt geïllustreerd in het volgende diagram:

[![Taxonomie van werkruimte](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

In het diagram ziet u de volgende onderdelen van een werkruimte:

+ Een werkruimte kan een [Azure Machine Learning bevatten,](concept-compute-instance.md)cloudbronnen die zijn geconfigureerd met de Python-omgeving die nodig is om de Azure Machine Learning.

+ [Met gebruikersrollen](how-to-assign-roles.md) kunt u uw werkruimte delen met andere gebruikers, teams of projecten.
+ [Rekendoelen worden](concept-azure-machine-learning-architecture.md#compute-targets) gebruikt om uw experimenten uit te voeren.
+ Wanneer u de werkruimte maakt, [worden ook gekoppelde resources](#resources) voor u gemaakt.
+ [Experimenten](concept-azure-machine-learning-architecture.md#experiments) zijn trainings runs die u gebruikt om uw modellen te bouwen.  
+ [Pijplijnen](concept-azure-machine-learning-architecture.md#ml-pipelines) zijn herbruikbare werkstromen voor het trainen en opnieuw trainen van uw model.
+ [Gegevenssets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) helpen bij het beheer van de gegevens die u gebruikt voor het trainen van modellen en het maken van pijplijnen.
+ Zodra u een model hebt dat u wilt implementeren, maakt u een geregistreerd model.
+ Gebruik het geregistreerde model en een scorescript om een [implementatie-eindpunt te maken.](concept-azure-machine-learning-architecture.md#endpoints)

## <a name="tools-for-workspace-interaction"></a>Hulpprogramma's voor werkruimte-interactie

U kunt op de volgende manieren met uw werkruimte werken:

> [!IMPORTANT]
> Hulpprogramma's die hieronder zijn gemarkeerd (preview) zijn momenteel beschikbaar als openbare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

+ Op internet:
    + [Azure Machine Learning-studio ](https://ml.azure.com) 
    + [Azure Machine Learning-ontwerpprogramma](concept-designer.md) 
+ In een Python-omgeving met de [Azure Machine Learning SDK voor Python.](/python/api/overview/azure/ml/intro)
+ In een R-omgeving met de [Azure Machine Learning SDK voor R (preview)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Op de opdrachtregel met behulp van de [cli Azure Machine Learning extensie](./reference-azure-machine-learning-cli.md)
+ [Azure Machine Learning VS Code-extensie](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Machine learning met een werkruimte

Machine learning-taken lezen en/of schrijven artefacten naar uw werkruimte.

+ Een experiment uitvoeren om een model te trainen: schrijft de resultaten van de experimentuit voeren naar de werkruimte.
+ Geautomatiseerde ML gebruiken om een model te trainen: schrijft trainingsresultaten naar de werkruimte.
+ Registreer een model in de werkruimte.
+ Een model implementeren: maakt gebruik van het geregistreerde model om een implementatie te maken.
+ Herbruikbare werkstromen maken en uitvoeren.
+ Bekijk machine learning zoals experimenten, pijplijnen, modellen, implementaties.
+ Modellen bijhouden en bewaken.

## <a name="workspace-management"></a>Werkruimtebeheer

U kunt ook de volgende werkruimtebeheertaken uitvoeren:

| Werkruimtebeheertaak   | Portal              | Studio | Python SDK/R SDK       | CLI        | VS-code
|---------------------------|---------|---------|------------|------------|------------|
| Een werkruimte maken        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Toegang tot werkruimten beheren    | **&check;**   || |  **&check;**    ||
| Rekenbronnen maken en beheren    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Een notebook-VM maken |   | **&check;** | |     ||

> [!WARNING]
> Het verplaatsen Azure Machine Learning werkruimte naar een ander abonnement of het verplaatsen van het abonnement dat eigenaar is naar een nieuwe tenant, wordt niet ondersteund. Dit kan fouten veroorzaken.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Een werkruimte maken

Er zijn meerdere manieren om een werkruimte te maken:  

* Gebruik de [Azure Portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) voor een punt-en-klik-interface om u door elke stap te helpen.
* Gebruik de [Azure Machine Learning-SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace) voor Python om op het eerste moment een werkruimte te maken met Python-scripts of Jupyter-notebooks
* Gebruik een [Azure Resource Manager of](how-to-create-workspace-template.md) de [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) wanneer u het maken moet automatiseren of aanpassen met bedrijfsbeveiligingsstandaarden.
* Als u in Visual Studio Code werkt, gebruikt u de [VS Code-extensie](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> De naam van de werkruimte is niet-gevoelig.

## <a name="associated-resources"></a><a name="resources"></a> Gekoppelde resources

Wanneer u een nieuwe werkruimte maakt, worden automatisch verschillende Azure-resources gemaakt die door de werkruimte worden gebruikt:

+ [Azure Storage account:](https://azure.microsoft.com/services/storage/)wordt gebruikt als het standaardgegevensstore voor de werkruimte.  Jupyter-notebooks die worden gebruikt met uw Azure Machine Learning reken-exemplaren worden hier ook opgeslagen. 
  
  > [!IMPORTANT]
  > Het opslagaccount is standaard een v1-account voor algemeen gebruik. U kunt [deze upgraden naar algemeen gebruik v2](../storage/common/storage-account-upgrade.md) nadat de werkruimte is gemaakt. Schakel hiërarchische naamruimte in het opslagaccount niet in na een upgrade naar algemeen gebruik v2.

  Als u een bestaand Azure Storage account wilt gebruiken, kan het niet van het type BlobStorage of een Premium-account (Premium_LRS en Premium_GRS). Het kan ook geen hiërarchische naamruimte hebben (gebruikt met Azure Data Lake Storage Gen2). Premium-opslag of hiërarchische naamruimten worden niet ondersteund met het _standaardopslagaccount_ van de werkruimte. U kunt Premium Storage of hiërarchische naamruimte gebruiken met _niet-standaard_ opslagaccounts.
  
+ [Azure Container Registry:](https://azure.microsoft.com/services/container-registry/)Registreert Docker-containers die u gebruikt tijdens de training en wanneer u een model implementeert. Om de kosten te minimaliseren, wordt ACR **lui geladen** totdat er implementatie-installatie afbeeldingen worden gemaakt.

+ [Azure-toepassing Insights: slaat bewakingsinformatie](https://azure.microsoft.com/services/application-insights/)over uw modellen op.

+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)slaat geheimen op die worden gebruikt door rekendoelen en andere gevoelige informatie die nodig is voor de werkruimte.

> [!NOTE]
> U kunt in plaats daarvan bestaande Azure-resource-exemplaren gebruiken wanneer u de werkruimte maakt met de [Python SDK,](how-to-manage-workspace.md?tabs=python#create-a-workspace) [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)of de Azure Machine Learning CLI met behulp van een [ARM-sjabloon](how-to-create-workspace-template.md).

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Wat is er gebeurd met de Enterprise-editie?

Vanaf september 2020 zijn alle mogelijkheden die beschikbaar waren in werkruimten in de Enterprise-editie ook beschikbaar in werkruimten van de Basic-editie. Nieuwe Enterprise-werkruimten kunnen niet meer worden gemaakt.  Alle SDK-, CLI- of Azure Resource Manager die gebruikmaken van de parameter blijven werken, maar er wordt een `sku` Basic-werkruimte ingericht.

Vanaf 21 december worden alle Enterprise Edition automatisch ingesteld op Basic Edition, die dezelfde mogelijkheden heeft. Er treedt geen downtime op tijdens dit proces. Op 1 januari 2021 wordt Enterprise Edition formeel ingetrokken. 

In beide edities zijn klanten verantwoordelijk voor de kosten van verbruikte Azure-resources en hoeven ze geen extra kosten te betalen voor Azure Machine Learning. Raadpleeg de pagina Azure Machine Learning [prijzen voor](https://azure.microsoft.com/pricing/details/machine-learning/) meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt Azure Machine Learning, gaat u naar:

+ [Azure Machine Learning overzicht](overview-what-is-azure-ml.md)
+ [Een werkruimte maken en beheren](how-to-manage-workspace.md)
+ [Zelfstudie: Aan de slag Azure Machine Learning in uw ontwikkelomgeving](tutorial-1st-experiment-sdk-setup-local.md)
+ [Zelfstudie: Aan de slag met het maken van uw eerste ML-experiment op een rekenexement](tutorial-1st-experiment-sdk-setup.md)
+ [Zelfstudie: Uw eerste classificatiemodel maken met geautomatiseerde machine learning](tutorial-first-experiment-automated-ml.md) 
+ [Zelfstudie: Autoprijzen voorspellen met de ontwerpfunctie](tutorial-designer-automobile-price-train-score.md)
