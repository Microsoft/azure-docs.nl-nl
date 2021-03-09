---
title: ML-modellen bouwen met de ontwerper
titleSuffix: Azure Machine Learning
description: Meer informatie over de termen, concepten en werk stromen die samen de ontwerper vormen voor Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: e6738bf944c5a80d0cb54432ade7555ebdcfbd51
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503537"
---
# <a name="what-is-azure-machine-learning-designer"></a>Wat is de Azure Machine Learning-ontwerpfunctie? 


Met Azure Machine Learning Designer kunt u [gegevens sets](#datasets) en [modules](#module) op een interactief canvas visueel verbinden om machine learning modellen te maken. Voor meer informatie over hoe u aan de slag kunt gaan met de ontwerp functie, Zie [zelf studie: prijs van auto Mobile voors pellen met de ontwerper](tutorial-designer-automobile-price-train-score.md)

![Voorbeeld van ontwerpfunctie van Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

De ontwerp functie maakt gebruik van uw Azure Machine Learning- [werk ruimte](concept-workspace.md) voor het organiseren van gedeelde resources zoals:

+ [Pijplijnen](#pipeline)
+ [Gegevenssets](#datasets)
+ [Reken resources](#compute)
+ [Geregistreerde modellen](concept-azure-machine-learning-architecture.md#models)
+ [Gepubliceerde pijp lijnen](#publish)
+ [Realtime-eind punten](#deploy)

## <a name="model-training-and-deployment"></a>Model training en-implementatie

De Designer biedt u een visueel canvas om machine learning modellen te bouwen, te testen en te implementeren. Met de ontwerp functie kunt u het volgende doen:

+ [Gegevens sets](#datasets) en [modules](#module) slepen en neerzetten op het canvas.
+ Verbind de modules om een [pijp lijn concept](#pipeline-draft)te maken.
+ Een [pijplijn uitvoering](#pipeline-run) verzenden met behulp van de reken resources in uw Azure machine learning-werk ruimte.
+ Converteer uw **trainings pijplijnen** om **pijp lijnen** af te leiden.
+ [Publiceer](#publish) uw pijp lijnen naar een rest **pijplijn-eind punt** om een nieuwe pijp lijn te verzenden die wordt uitgevoerd met verschillende para meters en gegevens sets.
    + Publiceer een **trainings pijplijn** om één pijp lijn te gebruiken voor het trainen van meerdere modellen tijdens het wijzigen van para meters en gegevens sets.
    + Publiceer een batch-uitstel **pijp lijn** om voor spellingen te maken op nieuwe gegevens met behulp van een eerder getraind model.
+ [Implementeer](#deploy) een **real-time pipeline-pijp lijn** naar een realtime-eind punt om voor spellingen te maken op nieuwe gegevens in realtime.

![Werk stroom diagram voor training, batch deinterferentie en real-time detrainer in de ontwerp functie](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pijplijn

Een [pijp lijn](concept-azure-machine-learning-architecture.md#ml-pipelines) bestaat uit gegevens sets en analytische modules, waarmee u verbinding maakt. Pijp lijnen hebben veel gebruiks mogelijkheden: u kunt een pijp lijn maken die één model traint of één waarmee meerdere modellen worden getraind. U kunt een pijp lijn maken die voor spellingen in realtime of in batch maakt, of een pijp lijn maken die alleen gegevens opschoont. Met pijp lijnen kunt u uw werk opnieuw gebruiken en uw projecten ordenen.

### <a name="pipeline-draft"></a>Pijp lijn concept

Wanneer u een pijp lijn in de ontwerp functie bewerkt, wordt uw voortgang opgeslagen als een **pijp lijn concept**. U kunt een pijp lijn concept op elk gewenst moment bewerken door modules toe te voegen of te verwijderen, reken doelen te configureren, para meters te maken, enzovoort.

Een geldige pijp lijn heeft de volgende kenmerken:

* Gegevens sets kunnen alleen verbinding maken met modules.
* Modules kunnen alleen verbinding maken met gegevens sets of andere modules.
* Alle invoer poorten voor modules moeten een verbinding met de gegevens stroom hebben.
* Alle vereiste para meters voor elke module moeten worden ingesteld.

Wanneer u klaar bent voor het uitvoeren van uw pijp lijn concept, verzendt u een pijplijn uitvoering.

### <a name="pipeline-run"></a>Pijplijnuitvoering

Telkens wanneer u een pijp lijn uitvoert, worden de configuratie van de pijp lijn en de resultaten ervan opgeslagen in uw werk ruimte als een **pijplijn uitvoering**. U kunt teruggaan naar elke pijplijn uitvoering om deze te controleren op probleem oplossing of controle doeleinden. Een pijplijn uitvoering **klonen** om een nieuw pijp lijn concept te maken dat u kunt bewerken.

Pijplijn uitvoeringen worden in [experimenten](concept-azure-machine-learning-architecture.md#experiments) gegroepeerd om de uitvoerings geschiedenis te organiseren. U kunt het experiment instellen voor elke pijplijn uitvoering. 

## <a name="datasets"></a>Gegevenssets

Met een machine learning-gegevensset kunt u eenvoudig toegang krijgen tot uw gegevens en deze gebruiken. De ontwerper bevat een aantal voor beelden van gegevens sets waarmee u kunt experimenteren. U kunt meer gegevens sets [registreren](how-to-create-register-datasets.md) wanneer u ze nodig hebt.

## <a name="module"></a>Module

Een module is een algoritme dat u met uw gegevens kunt uitvoeren. De designer heeft verschillende modules die variëren van functies voor het inkomen van gegevens in de processen training, Score en validatie.

Een module kan een reeks parameters hebben waarmee u de interne algoritmen van de module kunt configureren. Wanneer u een module op het canvas selecteert, worden de parameters van de module weergegeven in het deelvenster Properties, rechts van het canvas. U kunt de parameters in dit deelvenster wijzigen om het model af te stemmen. U kunt de reken resources voor afzonderlijke modules in de ontwerp functie instellen. 

:::image type="content" source="./media/concept-designer/properties.png" alt-text="Eigenschappen van module":::


Zie voor meer informatie over de beschik bare bibliotheek met machine learning algoritmen [overzicht van algoritme & module](algorithm-module-reference/module-reference.md). Zie het overzicht van [Azure machine learning Algorithm Cheat](algorithm-cheat-sheet.md)voor meer informatie over het kiezen van een algoritme.

## <a name="compute-resources"></a><a name="compute"></a> Reken resources

Gebruik reken resources van uw werk ruimte om uw pijp lijn uit te voeren en uw geïmplementeerde modellen als realtime-eind punten of pijplijn eindpunten te hosten (voor batch-deinterferentie). De ondersteunde Compute-doelen zijn:

| Rekendoel | Training | Implementatie |
| ---- |:----:|:----:|
| Azure Machine Learning compute | ✓ | |
| Azure Machine Learning-rekeninstantie | ✓ | |
| Azure Kubernetes Service | | ✓ |

Reken doelen zijn gekoppeld aan uw [Azure machine learning-werk ruimte](concept-workspace.md). U beheert uw reken doelen in uw werk ruimte in de [Azure machine learning Studio](https://ml.azure.com).

## <a name="deploy"></a>Implementeren

Als u real-time-interferentie wilt uitvoeren, moet u een pijp lijn als een **real-time-eind punt** implementeren. Het real-time eind punt maakt een interface tussen een externe toepassing en uw score model. Een aanroep van een real-time-eind punt retourneert Voorspellings resultaten voor de toepassing in realtime. Als u een real-time eind punt wilt aanroepen, geeft u de API-sleutel door die is gemaakt tijdens het implementeren van het eind punt. Het eind punt is gebaseerd op REST, een populaire architectuur keuze voor webprogrammeer projecten.

Realtime-eind punten moeten worden geïmplementeerd in een Azure Kubernetes-service cluster.

Zie [zelf studie: een machine learning model implementeren met de ontwerp functie](tutorial-designer-automobile-price-deploy.md)voor meer informatie over het implementeren van uw model.

## <a name="publish"></a>Publiceren

U kunt ook een pijp lijn publiceren naar een **pijplijn eindpunt**. Net als bij een real-time-eind punt kunt u met een pijplijn eindpunt nieuwe pijp lijn uitvoeringen vanuit externe toepassingen verzenden met behulp van REST-aanroepen. U kunt echter geen gegevens in realtime verzenden of ontvangen met behulp van een pijplijn eindpunt.

Gepubliceerde pijp lijnen zijn flexibel, ze kunnen worden gebruikt om modellen te trainen of opnieuw te trainen, een batch-detraining [uit te voeren](how-to-run-batch-predictions-designer.md), nieuwe gegevens te verwerken en nog veel meer. U kunt meerdere pijp lijnen naar één pijp lijn-eind punt publiceren en opgeven welke pijplijn versie moet worden uitgevoerd.

Een gepubliceerde pijp lijn wordt uitgevoerd op de reken resources die u in de pijp lijn concept voor elke module definieert.

De Designer maakt hetzelfde [PublishedPipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline) -object als de SDK.

## <a name="next-steps"></a>Volgende stappen

* Leer de basisprincipes van predictive analytics en machine learning met [Zelfstudie: Autoprijzen voorspellen met de ontwerpfunctie](tutorial-designer-automobile-price-train-score.md)
* Meer informatie over het wijzigen van bestaande [ontwerp voorbeelden](samples-designer.md) om ze aan te passen aan uw behoeften.