---
title: Gebeurtenissen activeren in ML-werkstromen (preview)
titleSuffix: Azure Machine Learning
description: Stel gebeurtenisgestuurde toepassingen, processen of CI/CD-machine learning in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 8294ecb255578305c868188575d5fa33e3b29b42
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884512"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Toepassingen, processen of CI/CD-werkstromen activeren op basis van Azure Machine Learning gebeurtenissen (preview)

In dit artikel leert u hoe u gebeurtenisgestuurde toepassingen, processen of CI/CD-werkstromen kunt instellen op basis van Azure Machine Learning-gebeurtenissen, zoals e-mails over foutmeldingen of ML-pijplijn-runs, wanneer bepaalde voorwaarden worden gedetecteerd door [Azure Event Grid](../event-grid/index.yml).

Azure Machine Learning beheert de volledige levenscyclus van machine learning proces, waaronder modeltraining, modelimplementatie en bewaking. U kunt Event Grid om te reageren op Azure Machine Learning-gebeurtenissen, zoals het voltooien van trainings runs, de registratie en implementatie van modellen en de detectie van gegevensdrift, met behulp van moderne serverloze architecturen. Vervolgens kunt u zich abonneren en gebeurtenissen gebruiken, zoals de status van de uitvoering gewijzigd, uitvoering voltooid, modelregistratie, modelimplementatie en detectie van gegevensdrift binnen een werkruimte.

Wanneer gebruikt u Event Grid voor gebeurtenisgestuurde acties:
* E-mailberichten verzenden bij mislukte uitvoering en voltooiing van uitvoering
* Een Azure-functie gebruiken nadat een model is geregistreerd
* Gebeurtenissen streamen van Azure Machine Learning naar verschillende eindpunten
* Een ML-pijplijn activeren wanneer er drift wordt gedetecteerd

## <a name="prerequisites"></a>Vereisten
Als u Event Grid wilt gebruiken, hebt u inzender- of eigenaarstoegang nodig tot Azure Machine Learning werkruimte voor wie u gebeurtenissen maakt.

## <a name="the-event-model--types"></a>Het gebeurtenismodel & typen

Azure Event Grid leest gebeurtenissen uit bronnen, zoals Azure Machine Learning en andere Azure-services. Deze gebeurtenissen worden vervolgens verzonden naar gebeurtenis-handlers zoals Azure Event Hubs, Azure Functions, Logic Apps en andere. Het volgende diagram toont hoe Event Grid bronnen en handlers verbindt, maar is geen uitgebreide lijst met ondersteunde integraties.

![Azure Event Grid functioneel model](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Zie Wat is Event Grid? voor meer informatie over [gebeurtenisbronnen en gebeurtenis-handlers.](../event-grid/overview.md)

### <a name="event-types-for-azure-machine-learning"></a>Gebeurtenistypen voor Azure Machine Learning

Azure Machine Learning biedt gebeurtenissen in de verschillende punten van machine learning levenscyclus: 

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Wordt verhoogd wanneer een machine learning experiment is voltooid |
| `Microsoft.MachineLearningServices.ModelRegistered` | Wordt gemeld wanneer een machine learning model wordt geregistreerd in de werkruimte |
| `Microsoft.MachineLearningServices.ModelDeployed` | Wordt verhoogd wanneer een implementatie van de deference-service met een of meer modellen is voltooid |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Wordt aangemaakt wanneer een taak voor gegevensdriftdetectie voor twee gegevenssets is voltooid |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Wordt verhoogd wanneer de status van een run is gewijzigd, momenteel alleen verhoogd wanneer de status van een run 'mislukt' is |

### <a name="filter--subscribe-to-events"></a>Filteren & abonneren op gebeurtenissen

Deze gebeurtenissen worden gepubliceerd via Azure Event Grid. Met Azure Portal, PowerShell of Azure CLI kunnen klanten zich eenvoudig abonneren op gebeurtenissen door een of meer gebeurtenistypen en [filtervoorwaarden op te geven.](../event-grid/event-filtering.md) 

Bij het instellen van uw gebeurtenissen kunt u filters toepassen om alleen te activeren op specifieke gebeurtenisgegevens. In het onderstaande voorbeeld kunt u voor gewijzigde gebeurtenissen van de runstatus filteren op run types. De gebeurtenis wordt alleen triggers wanneer aan de criteria wordt voldaan. Raadpleeg het event [grid Azure Machine Learning schema voor](../event-grid/event-schema-machine-learning.md) meer informatie over gebeurtenisgegevens die u kunt filteren. 

Abonnementen voor Azure Machine Learning worden beveiligd door op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC). Alleen [inzenders](how-to-assign-roles.md#default-roles) of eigenaren van een werkruimte kunnen gebeurtenisabonnementen maken, bijwerken en verwijderen.  Filters kunnen worden toegepast op gebeurtenisabonnementen tijdens het [maken](/cli/azure/eventgrid/event-subscription) van het gebeurtenisabonnement of op een later tijdstip. 


1. Ga naar de Azure Portal selecteer een nieuw abonnement of een bestaand abonnement. 

1. Selecteer het tabblad Filters en schuif omlaag naar Geavanceerde filters. Geef voor **De sleutel** **en waarde** de eigenschapstypen op die u wilt filteren. Hier ziet u dat de gebeurtenis alleen wordt getypt wanneer het runtype een pijplijn-run of pijplijnstap is.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="gebeurtenissen filteren":::


+ **Filteren op gebeurtenistype:** Een gebeurtenisabonnement kan een of meer typen Azure Machine Learning opgeven.

+ **Filteren op gebeurtenisonderwerp:** Azure Event Grid ondersteunt onderwerpfilters op basis  van begint __met__ en eindigt met overeenkomsten, zodat gebeurtenissen met een overeenkomend onderwerp aan de abonnee worden geleverd. Verschillende machine learning hebben een andere onderwerpindeling.

  | Gebeurtenistype | Onderwerpindeling | Voorbeeldonderwerp |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Geavanceerd filteren:** Azure Event Grid ook geavanceerde filters op basis van het gepubliceerde gebeurtenisschema. Azure Machine Learning gebeurtenisschemadetails vindt u in [Azure Event Grid gebeurtenisschema voor Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Enkele voorbeelden van geavanceerde filters die u kunt uitvoeren, zijn:

  Voor `Microsoft.MachineLearningServices.ModelRegistered` gebeurtenis, om de tagwaarde van het model te filteren:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Zie Gebeurtenissen filteren voor Event Grid voor meer [informatie over het toepassen van Event Grid.](../event-grid/how-to-filter-events.md)

## <a name="consume-machine-learning-events"></a>Gebeurtenissen Machine Learning verbruiken

Toepassingen die Machine Learning verwerken, moeten een aantal aanbevolen procedures volgen:

> [!div class="checklist"]
> * Omdat meerdere abonnementen kunnen worden geconfigureerd om gebeurtenissen naar dezelfde gebeurtenis-handler te sturen, is het belangrijk om niet ervan uit te gaan dat gebeurtenissen afkomstig zijn van een bepaalde bron, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van de machine learning-werkruimte die u verwacht.
> * Controleer op dezelfde manier of het eventType het type is dat u wilt verwerken en ga er niet van uit dat alle gebeurtenissen die u ontvangt de typen zijn die u verwacht.
> * Als berichten niet in de volgorde en na enige vertraging kunnen binnenkomen, gebruikt u de etag-velden om te begrijpen of uw informatie over objecten nog steeds up-to-date is.  Gebruik ook de sequencervelden om de volgorde van gebeurtenissen op een bepaald object te begrijpen.
> * Negeer velden die u niet begrijpt. Deze praktijk helpt u bestand te houden tegen nieuwe functies die in de toekomst kunnen worden toegevoegd.
> * Mislukt of geannuleerd Azure Machine Learning-bewerkingen activeren geen gebeurtenis. Als een modelimplementatie bijvoorbeeld mislukt, wordt Microsoft.MachineLearningServices.ModelDeployed niet geactiveerd. Houd rekening met een dergelijke foutmodus bij het ontwerpen van uw toepassingen. U kunt altijd Azure Machine Learning SDK, CLI of portal gebruiken om de status van een bewerking te controleren en inzicht te krijgen in de gedetailleerde oorzaken van fouten.

Azure Event Grid kunnen klanten ontkoppelde berichten-handlers bouwen, die kunnen worden geactiveerd door Azure Machine Learning gebeurtenissen. Enkele belangrijke voorbeelden van berichten-handlers zijn:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory pijplijn
* Algemene webhooks, die kunnen worden gehost op het Azure-platform of elders

## <a name="set-up-in-azure-portal"></a>Instellen in Azure Portal

1. Open de [Azure Portal](https://portal.azure.com) en ga naar uw Azure Machine Learning werkruimte.

1. Selecteer gebeurtenissen in de __linkerbalk__ en selecteer vervolgens **Gebeurtenisabonnementen.** 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Selecteer het gebeurtenistype dat u wilt gebruiken. In de volgende schermopname zijn bijvoorbeeld __Geregistreerd model,__ __Model geïmplementeerd,__ __Voltooid uitvoeren__ en __Gegevenssetdrift gedetecteerd:__

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selecteer het eindpunt om de gebeurtenis naar te publiceren. In de volgende schermafbeelding is __Event Hub__ het geselecteerde eindpunt:

    ![Schermopname van het deelvenster Gebeurtenisabonnement maken met Event Hub selecteren geopend.](./media/how-to-use-event-grid/select-event-handler.png)

Nadat u uw selectie hebt bevestigd, klikt u op __Maken.__ Na de configuratie worden deze gebeurtenissen naar uw eindpunt ge pusht.


### <a name="set-up-with-the-cli"></a>Instellen met de CLI

U kunt de nieuwste Versie van [Azure CLI](/cli/azure/install-azure-cli)installeren of de Azure Cloud Shell die is opgegeven als onderdeel van uw Azure-abonnement.

Als u de Event Grid wilt installeren, gebruikt u de volgende opdracht vanuit de CLI:

```azurecli-interactive
az add extension --name eventgrid
```

In het volgende voorbeeld wordt gedemonstreerd hoe u een Azure-abonnement selecteert en een nieuw gebeurtenisabonnement maakt voor Azure Machine Learning:

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

### <a name="example-send-email-alerts"></a>Voorbeeld: E-mailwaarschuwingen verzenden

Gebruik [Azure Logic Apps](../logic-apps/index.yml) om e-mailberichten te configureren voor al uw gebeurtenissen. Pas aan met voorwaarden en geef ontvangers op om samenwerking en bewustzijn mogelijk te maken tussen teams die samenwerken.

1. Ga in Azure Portal werkruimte naar Azure Machine Learning werkruimte en selecteer het tabblad Gebeurtenissen in de linkerbalk. Selecteer hier Logische __apps.__ 

    ![Schermopname toont een Machine Learning werkruimte Gebeurtenissen met Logic Apps.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Meld u aan bij de gebruikersinterface van de logische app en Machine Learning service als het onderwerptype. 

    ![Schermopname van het dialoogvenster Wanneer een resourcegebeurtenis plaatsvindt, machine learning geselecteerd als een resourcetype.](./media/how-to-use-event-grid/select-topic-type.png)

1. Selecteer voor welke gebeurtenis(s) u een melding wilt ontvangen. Bijvoorbeeld de volgende schermopname __RunCompleted.__

    ![Schermopname van het dialoogvenster Wanneer een resourcegebeurtenis plaatsvindt, met een gebeurtenistype geselecteerd.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. U kunt de filtermethode in de bovenstaande sectie gebruiken of filters toevoegen om de logische app alleen te activeren voor een subset van gebeurtenistypen. In de volgende schermopname wordt __een voorvoegselfilter__ __/datadriftID/runs/__ gebruikt.

    ![filtergebeurtenissen](./media/how-to-use-event-grid/filtering-events.png)

1. Voeg vervolgens een stap toe om deze gebeurtenis te gebruiken en te zoeken naar e-mail. Er zijn verschillende e-mailaccounts die u kunt gebruiken om gebeurtenissen te ontvangen. U kunt ook voorwaarden configureren voor het verzenden van een e-mailwaarschuwing.

    ![Schermopname van het dialoogvenster Een actie kiezen met e-mail ingevoerd in de zoekregel.](./media/how-to-use-event-grid/select-email-action.png)

1. Selecteer __Een e-mail verzenden__ en vul de parameters in. In het onderwerp kunt u het gebeurtenistype en __onderwerp__ opnemen __om__ gebeurtenissen te filteren. U kunt ook een koppeling naar de werkruimtepagina opnemen voor de runs in de hoofd bericht. 

    ![Schermopname van het dialoogvenster Een e-mail verzenden met Onderwerp en Gebeurtenistype toegevoegd aan de onderwerpregel in de lijst aan de rechterkant.](./media/how-to-use-event-grid/configure-email-body.png)

1. Als u deze actie wilt opslaan, **selecteert u** Opslaan als in de linkerhoek van de pagina. Bevestig in de rechterbalk die wordt weergegeven dat deze actie is gemaakt.

    ![Schermopname van de knoppen Opslaan als en Maken in Logic Apps Designer.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Voorbeeld: Gegevensdrifttriggers opnieuw trainen

Modellen gaan in de loop van de tijd verouderd en blijven niet nuttig in de context waarin ze worden uitgevoerd. Een manier om te zien of het tijd is om het model opnieuw te trainen, is het detecteren van gegevensdrift. 

In dit voorbeeld ziet u hoe u Event Grid gebruikt met een logische Azure-app om opnieuw trainen te activeren. Het voorbeeld activeert een Azure Data Factory pijplijn wanneer gegevensdrift plaatsvindt tussen de trainings- en gegevenssets van een model.

Voordat u begint, moet u de volgende acties uitvoeren:

* Een gegevenssetmonitor instellen om [gegevensdrift](how-to-monitor-datasets.md) in een werkruimte te detecteren
* Maak een gepubliceerde [Azure Data Factory pijplijn](../data-factory/index.yml).

In dit voorbeeld wordt een eenvoudige Data Factory gebruikt om bestanden te kopiëren naar een blob-archief en een gepubliceerde Machine Learning uitvoeren. Zie Voor meer informatie over dit scenario een Machine Learning [instellen in Azure Data Factory](../data-factory/transform-data-machine-learning-service.md)

![Schermopname van de trainingspijplijn in Factory-resources met Copy data1 en M L Execute Pipeline1.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Begin met het maken van de logische app. Ga naar de [Azure Portal,](https://portal.azure.com)zoek naar Logic Apps en selecteer Maken.

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Vul de gevraagde gegevens in. Om de ervaring te vereenvoudigen, gebruikt u hetzelfde abonnement en dezelfde resourcegroep als uw Azure Data Factory Pipeline en Azure Machine Learning werkruimte.

    ![Schermopname van het deelvenster Maken van logische app.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Nadat u de logische app hebt gemaakt, selecteert __u Wanneer een Event Grid resourcegebeurtenis plaatsvindt.__ 

    ![Schermopname toont de Logic Apps Designer met Start with a common trigger options,inclusief Wanneer een Event Grid resourcegebeurtenis optreedt.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Meld u aan en vul de details voor de gebeurtenis in. Stel __resourcenaam in__ op de naam van de werkruimte. Stel het __Gebeurtenistype in__ __op DatasetDriftDetected.__

    ![Schermopname toont wanneer een resourcegebeurtenis plaatsvindt met een gebeurtenistype-item geselecteerd.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Voeg een nieuwe stap toe en zoek naar __Azure Data Factory__. Selecteer __Een pijplijn uitvoeren maken.__ 

    ![Schermopname van het deelvenster Een actie kiezen met Een pijplijnuitleiding maken geselecteerd.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Meld u aan en geef de gepubliceerde Azure Data Factory pijplijn op die moet worden uitgevoerd.

    ![Schermopname van het deelvenster Een pijplijnuitleiding maken met verschillende waarden.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Sla de logische app op en maak deze **met behulp van de** knop Opslaan linksboven op de pagina. Als u uw app wilt weergeven, gaat u naar uw werkruimte in [Azure Portal](https://portal.azure.com) klikt u op **Gebeurtenissen**.

    ![Schermopname van gebeurtenissen met de logische app gemarkeerd.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Nu wordt data factory pijplijn geactiveerd wanneer er drift optreedt. Bekijk details over uw gegevensdrift-run en machine learning pijplijn in de [nieuwe werkruimteportal.](https://ml.azure.com) 

![Schermopname van pijplijn-eindpunten.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Voorbeeld: Een model implementeren op basis van tags

Een Azure Machine Learning-modelobject bevat parameters waar u implementaties op kunt draaien, zoals modelnaam, versie, tag en eigenschap. De modelregistratiegebeurtenis kan een eindpunt activeren en u kunt een Azure-functie gebruiken om een model te implementeren op basis van de waarde van deze parameters.

Zie de opslagplaats voor een voorbeeld [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) en volg de stappen in het **leesmij-bestand.**

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Event Grid en Azure Machine Learning gebeurtenissen eens proberen:

- [Over Event Grid](../event-grid/overview.md)

- [Gebeurtenisschema voor Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
