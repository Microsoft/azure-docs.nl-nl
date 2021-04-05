---
title: Gebeurtenissen in ML-werk stromen activeren (preview-versie)
titleSuffix: Azure Machine Learning
description: Met gebeurtenis gerichte toepassingen, processen of CI/CD-machine learning werk stromen instellen in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 9dc7c0a476f57048c7caefb6001918d314c991ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102209693"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Toepassingen, processen of CI/CD-werk stromen activeren op basis van Azure Machine Learning gebeurtenissen (preview)

In dit artikel leert u hoe u op gebeurtenissen gebaseerde toepassingen, processen of CI/CD-werk stromen kunt instellen op basis van Azure Machine Learning gebeurtenissen, zoals e-mail berichten over mislukte meldingen of MILLILITERs pijplijnen, wanneer bepaalde voor waarden worden gedetecteerd door [Azure Event grid](../event-grid/index.yml).

Azure Machine Learning beheert de volledige levens cyclus van machine learning proces, waaronder model training, model implementatie en bewaking. U kunt Event Grid gebruiken om te reageren op Azure Machine Learning gebeurtenissen, zoals het volt ooien van de uitvoering van de training, de registratie en implementatie van modellen en de detectie van gegevens drift, door gebruik te maken van moderne serverloze architecturen. U kunt vervolgens gebeurtenissen, zoals de uitvoerings status gewijzigd, het volt ooien van het registreren, model registratie, model implementatie en de detectie van gegevens drift in een werk ruimte, abonneren en gebruiken.

Wanneer u Event Grid moet gebruiken voor door gebeurtenis gestuurde acties:
* E-mail berichten verzenden wanneer de uitvoering is mislukt en de uitvoering is voltooid
* Een Azure-functie gebruiken nadat een model is geregistreerd
* Gebeurtenissen streamen van Azure Machine Learning naar verschillende eind punten
* Een ML-pijp lijn activeren als er een drift wordt gedetecteerd

## <a name="prerequisites"></a>Vereisten
Als u Event Grid wilt gebruiken, moet u Inzender of eigenaar toegang hebben tot de Azure Machine Learning-werk ruimte waarvoor u gebeurtenissen gaat maken.

## <a name="the-event-model--types"></a>De & typen van het gebeurtenis model

Azure Event Grid leest gebeurtenissen van bronnen, zoals Azure Machine Learning en andere Azure-Services. Deze gebeurtenissen worden vervolgens verzonden naar gebeurtenis-handlers zoals Azure Event Hubs, Azure Functions, Logic Apps en anderen. In het volgende diagram ziet u hoe Event Grid bronnen en handlers verbindt, maar dit is geen uitgebreide lijst met ondersteunde integraties.

![Azure Event Grid functionele model](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Zie [Wat is Event grid?](../event-grid/overview.md)voor meer informatie over gebeurtenis bronnen en gebeurtenis-handlers.

### <a name="event-types-for-azure-machine-learning"></a>Gebeurtenis typen voor Azure Machine Learning

Azure Machine Learning biedt gebeurtenissen in de verschillende punten van machine learning levenscyclus: 

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Deze gebeurtenis treedt op wanneer de uitvoering van een machine learning experiment is voltooid |
| `Microsoft.MachineLearningServices.ModelRegistered` | Deze gebeurtenis treedt op wanneer een machine learning model in de werk ruimte wordt geregistreerd |
| `Microsoft.MachineLearningServices.ModelDeployed` | Deze gebeurtenis treedt op wanneer een implementatie van een Afleidings service met een of meer modellen is voltooid |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Deze gebeurtenis treedt op wanneer een taak voor de detectie van gegevens drift voor twee gegevens sets is voltooid |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Deze gebeurtenis treedt op wanneer een uitvoerings status wordt gewijzigd. dit wordt momenteel alleen geactiveerd wanneer de uitvoerings status is ingesteld op mislukt |

### <a name="filter--subscribe-to-events"></a>& abonneren op gebeurtenissen

Deze gebeurtenissen worden gepubliceerd via Azure Event Grid. Met Azure Portal, Power shell of Azure CLI kunnen klanten eenvoudig zich abonneren op gebeurtenissen door [een of meer gebeurtenis typen op te geven en filter voorwaarden](../event-grid/event-filtering.md). 

Bij het instellen van uw gebeurtenissen kunt u filters toep assen op alleen triggers voor specifieke gebeurtenis gegevens. In het onderstaande voor beeld, voor gewijzigde gebeurtenissen, kunt u filteren op uitvoer typen. De gebeurtenis wordt alleen geactiveerd wanneer aan de criteria wordt voldaan. Raadpleeg het [Azure machine learning Event grid-schema](../event-grid/event-schema-machine-learning.md) voor meer informatie over gebeurtenis gegevens waarop u kunt filteren. 

Abonnementen voor Azure Machine Learning gebeurtenissen worden beveiligd door Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Alleen [Inzender of eigenaar](how-to-assign-roles.md#default-roles) van een werk ruimte kan gebeurtenis abonnementen maken, bijwerken en verwijderen.  Filters kunnen worden toegepast op gebeurtenis abonnementen tijdens het [maken](/cli/azure/eventgrid/event-subscription) van het gebeurtenis abonnement of op een later tijdstip. 


1. Ga naar de Azure Portal, selecteer een nieuw abonnement of een bestaande. 

1. Selecteer het tabblad Filters en schuif omlaag naar geavanceerde filters. Geef voor de **sleutel** en **waarde** de eigenschaps typen op waarop u wilt filteren. Hier kunt u zien dat de gebeurtenis alleen wordt geactiveerd wanneer het run-type een pijplijn uitvoering of pijplijn stap wordt uitgevoerd.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="gebeurtenissen filteren":::


+ **Filteren op gebeurtenis type:** Met een gebeurtenis abonnement kunt u een of meer Azure Machine Learning gebeurtenis typen opgeven.

+ **Filteren op gebeurtenis onderwerp:** Azure Event Grid ondersteunt onderwerps __filters op basis van__ en __eindigt met__ overeenkomsten, zodat gebeurtenissen met een overeenkomend onderwerp aan de abonnee worden geleverd. Verschillende machine learning gebeurtenissen hebben een andere onderwerps indeling.

  | Gebeurtenistype | Onderwerps indeling | Voorbeeld onderwerp |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Geavanceerd filteren**: Azure Event grid ondersteunt ook geavanceerde filtering op basis van het gepubliceerde gebeurtenis schema. Details van Azure Machine Learning gebeurtenis schema vindt u in [Azure Event grid-gebeurtenis schema voor Azure machine learning](../event-grid/event-schema-machine-learning.md).  Voor beelden van geavanceerde filters die u kunt uitvoeren zijn:

  Voor `Microsoft.MachineLearningServices.ModelRegistered` gebeurtenis: als u de label waarde van het model wilt filteren.

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Zie [gebeurtenissen filteren voor Event grid voor](../event-grid/how-to-filter-events.md)meer informatie over het Toep assen van filters.

## <a name="consume-machine-learning-events"></a>Machine Learning gebeurtenissen gebruiken

Toepassingen die Machine Learning gebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:

> [!div class="checklist"]
> * Omdat meerdere abonnementen kunnen worden geconfigureerd voor het routeren van gebeurtenissen naar dezelfde gebeurtenis-handler, is het belang rijk om te voor komen dat gebeurtenissen afkomstig zijn uit een bepaalde bron, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van de machine learning werk ruimte die u verwacht.
> * Controleer ook of de Event type is ingesteld als een voor bereiding op het proces en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de verwachte typen zijn.
> * Wanneer berichten in aflopende volg orde en na enige vertraging kunnen aankomen, gebruikt u de ETAG-velden om te begrijpen of uw informatie over objecten nog steeds actueel is.  Gebruik ook de sequencer-velden om de volg orde van gebeurtenissen op een bepaald object te begrijpen.
> * Velden negeren die u niet begrijpt. Met deze procedure kunt u de nieuwe functies die in de toekomst kunnen worden toegevoegd, flexibeler maken.
> * Mislukte of geannuleerde Azure Machine Learning bewerkingen activeren geen gebeurtenis. Als bijvoorbeeld een model implementatie mislukt, wordt micro soft. MachineLearningServices. ModelDeployed niet geactiveerd. Overweeg deze fout modus bij het ontwerpen van uw toepassingen. U kunt altijd Azure Machine Learning SDK, CLI of portal gebruiken om de status van een bewerking te controleren en inzicht te krijgen in de oorzaken van de fout.

Met Azure Event Grid kunnen klanten ongekoppelde bericht-handlers bouwen, die kunnen worden geactiveerd door Azure Machine Learning-gebeurtenissen. Enkele voor beelden van bericht-handlers zijn:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory pijp lijn
* Generieke webhooks, die kunnen worden gehost op het Azure-platform of elders

## <a name="set-up-in-azure-portal"></a>Ingesteld in Azure Portal

1. Open de [Azure Portal](https://portal.azure.com) en ga naar uw Azure machine learning-werk ruimte.

1. Selecteer in de linker balk __gebeurtenissen__ en selecteer vervolgens **gebeurtenis abonnementen**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Selecteer het gebeurtenis type dat u wilt gebruiken. De volgende scherm afbeelding is bijvoorbeeld geselecteerd __model geregistreerd__, __model geïmplementeerd__, __uitvoering voltooid__ en gegevensset- __drift gedetecteerd__:

    ![Add-Event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selecteer het eind punt waarnaar u de gebeurtenis wilt publiceren. In de volgende scherm afbeelding is __Event hub__ het geselecteerde eind punt:

    ![Scherm afbeelding toont het deel venster gebeurtenis abonnement maken met Select Event hub open.](./media/how-to-use-event-grid/select-event-handler.png)

Nadat u de selectie hebt bevestigd, klikt u op __maken__. Na de configuratie worden deze gebeurtenissen naar uw eind punt gepusht.


### <a name="set-up-with-the-cli"></a>Ingesteld met de CLI

U kunt de nieuwste versie van [Azure cli](/cli/azure/install-azure-cli)installeren of de Azure Cloud shell gebruiken die wordt meegeleverd als onderdeel van uw Azure-abonnement.

Als u de uitbrei ding Event Grid wilt installeren, gebruikt u de volgende opdracht vanuit de CLI:

```azurecli-interactive
az add extension --name eventgrid
```

In het volgende voor beeld ziet u hoe u een Azure-abonnement selecteert en e een nieuw gebeurtenis abonnement maakt voor Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Voorbeelden

### <a name="example-send-email-alerts"></a>Voor beeld: e-mail waarschuwingen verzenden

Gebruik [Azure Logic apps](../logic-apps/index.yml) voor het configureren van e-mail berichten voor al uw gebeurtenissen. Pas aan met voor waarden en geef ontvangers op om samen werking en bewustzijn in verschillende teams samen te stellen.

1. Ga in het Azure Portal naar uw werk ruimte Azure Machine Learning en selecteer het tabblad gebeurtenissen in de balk aan de linkerkant. Selecteer hier __Logic apps__. 

    ![Scherm afbeelding toont een Machine Learning pagina werkruimte gebeurtenissen met Logic Apps.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Meld u aan bij de gebruikers interface van de logische app en selecteer Machine Learning service als onderwerps type. 

    ![Scherm afbeelding toont het dialoog venster wanneer een bron gebeurtenis plaatsvindt met machine learning geselecteerd als resource type.](./media/how-to-use-event-grid/select-topic-type.png)

1. Selecteer voor welke gebeurtenis (en) u een melding wilt ontvangen. Bijvoorbeeld de volgende scherm afbeelding __RunCompleted__.

    ![Scherm afbeelding toont het dialoog venster wanneer een resource gebeurtenis plaatsvindt met een geselecteerd gebeurtenis type.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. U kunt de filter methode in de bovenstaande sectie gebruiken of filters toevoegen om alleen de logische app te activeren voor een subset van gebeurtenis typen. In de volgende scherm afbeelding wordt een __voor voegsel filter__ van __/datadriftID/runs/__ gebruikt.

    ![filter-gebeurtenissen](./media/how-to-use-event-grid/filtering-events.png)

1. Voeg vervolgens een stap toe om deze gebeurtenis te gebruiken en te zoeken naar e-mail. Er zijn verschillende e-mail accounts die u kunt gebruiken om gebeurtenissen te ontvangen. U kunt ook voor waarden configureren op wanneer u een e-mail bericht wilt verzenden.

    ![Scherm afbeelding toont het dialoog venster een actie kiezen met een e-mail adres dat is ingevoerd op de zoek regel.](./media/how-to-use-event-grid/select-email-action.png)

1. Selecteer __een E-mail verzenden__ en vul de para meters in. In het onderwerp kunt u het __gebeurtenis type__ en __onderwerp__ toevoegen om gebeurtenissen te filteren. U kunt ook een koppeling naar de werkruimte pagina toevoegen voor uitvoeringen in de hoofd tekst van het bericht. 

    ![Scherm afbeelding toont het dialoog venster een e-mail verzenden met het onderwerp en het gebeurtenis type dat is toegevoegd aan de onderwerpregel van de lijst aan de rechter kant.](./media/how-to-use-event-grid/configure-email-body.png)

1. Als u deze actie wilt opslaan, selecteert u **Opslaan als** in de linkerbovenhoek van de pagina. Bevestig het maken van deze actie vanaf de rechter balk die wordt weer gegeven.

    ![Scherm afbeelding toont de knoppen Opslaan als en maken in de Logic Apps Designer.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Voor beeld: het opnieuw trainen van gegevens drift wordt geactiveerd

Modellen gaan verouderd in de loop van de tijd en blijven niet nuttig in de context waarin deze wordt uitgevoerd in. Een manier om te bepalen of het model opnieuw moet worden getraind, is het detecteren van gegevens drift. 

In dit voor beeld ziet u hoe u Event grid gebruikt met een Azure Logic-app om de training te activeren. In het voor beeld wordt een Azure Data Factory pijp lijn geactiveerd wanneer gegevens overdracht plaatsvindt tussen de training van een model en het leveren van data sets.

Voordat u begint, moet u de volgende acties uitvoeren:

* Een gegevensset-monitor instellen voor het [detecteren van gegevens drift](how-to-monitor-datasets.md) in een werk ruimte
* Een gepubliceerde [Azure Data Factory pijp lijn](../data-factory/index.yml)maken.

In dit voor beeld wordt een eenvoudige Data Factory-pijp lijn gebruikt om bestanden te kopiëren naar een BLOB Store en een gepubliceerde Machine Learning-pijp lijn uit te voeren. Zie een [Machine Learning stap instellen in azure Data Factory](../data-factory/transform-data-machine-learning-service.md) voor meer informatie over dit scenario.

![Scherm afbeelding toont de trainings pijplijn in de fabrieks resources met Copy Data1 voeder M L Execute Pipeline1.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Begin met het maken van de logische app. Ga naar de [Azure Portal](https://portal.azure.com), zoek naar Logic apps en selecteer maken.

    ![Zoek logica-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Vul de vereiste gegevens in. Gebruik hetzelfde abonnement en dezelfde resource groep als uw Azure Data Factory pijp lijn en Azure Machine Learning werk ruimte om de ervaring te vereenvoudigen.

    ![Scherm afbeelding toont het deel venster voor het maken van logische apps.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Wanneer u de logische app hebt gemaakt, selecteert u __Wanneer een event grid resource gebeurtenis optreedt__. 

    ![Scherm afbeelding toont de Logic Apps Designer met beginnen met een algemene trigger opties, waaronder wanneer een Event Grid resource gebeurtenis optreedt.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Meld u aan en vul de Details voor de gebeurtenis in. Stel de __resource naam__ in op de naam van de werk ruimte. Stel het __gebeurtenis type__ in op __DatasetDriftDetected__.

    ![Scherm afbeelding laat zien hoe een resource gebeurtenis plaatsvindt waarbij een gebeurtenis type-item is geselecteerd.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Voeg een nieuwe stap toe en zoek naar __Azure Data Factory__. Selecteer __een pijplijn uitvoering maken__. 

    ![Scherm afbeelding toont het deel venster actie kiezen bij het maken van een pijplijn uitvoering geselecteerd.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Meld u aan en geef de gepubliceerde Azure Data Factory-pijp lijn op om uit te voeren.

    ![Scherm afbeelding toont het deel venster een pijplijn uitvoering maken met verschillende waarden.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Sla de logische app op en maak deze op met behulp van de knop **Opslaan** in de linkerbovenhoek van de pagina. Als u uw app wilt weer geven, gaat u naar uw werk ruimte in de [Azure Portal](https://portal.azure.com) en klikt u op **gebeurtenissen**.

    ![Scherm opname toont gebeurtenissen met de logische app gemarkeerd.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Nu wordt de data factory-pijp lijn geactiveerd wanneer drift optreedt. Bekijk details over uw data drift-uitvoering en machine learning pijp lijn op de [nieuwe werkruimte Portal](https://ml.azure.com). 

![Scherm opname toont pijp lijn-eind punten.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Voor beeld: een model implementeren op basis van Tags

Een Azure Machine Learning model object bevat para meters waarmee u implementaties kunt draaien, zoals model naam, versie, tag en eigenschap. De registratie gebeurtenis voor het model kan een eind punt activeren en u kunt een Azure-functie gebruiken om een model te implementeren op basis van de waarde van die para meters.

Voor een voor beeld raadpleegt u de [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) opslag plaats en volgt u de stappen in het **Leesmij** -bestand.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Event Grid en het geven van Azure Machine Learning gebeurtenissen een try:

- [Over Event Grid](../event-grid/overview.md)

- [Gebeurtenis schema voor Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
