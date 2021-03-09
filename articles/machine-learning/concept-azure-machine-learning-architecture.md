---
title: Belangrijkste concepten van architectuur &
titleSuffix: Azure Machine Learning
description: In dit artikel vindt u meer informatie over de architectuur, termen en concepten van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: dc1954c97da0d7f40deaf0f4efa7ca99793107bb
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503688"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Hoe Azure Machine Learning werkt: architectuur en concepten

Meer informatie over de architectuur en concepten voor [Azure machine learning](overview-what-is-azure-ml.md).  In dit artikel vindt u meer informatie over de onderdelen en hoe ze samen werken om het proces van het bouwen, implementeren en onderhouden van machine learning modellen te helpen.

## <a name="workspace"></a><a name="workspace"></a> Werk ruimte

Een [machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor Azure machine learning.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Diagram: Azure Machine Learning architectuur van een werk ruimte en de bijbehorende onderdelen":::

De werk ruimte is de centrale locatie waar u het volgende kunt doen:

* Resources beheren die u gebruikt voor de training en implementatie van modellen, zoals [berekeningen](#compute-instance)
* Store-assets die u maakt wanneer u Azure Machine Learning gebruikt, zoals:
  * [Omgevingen](#environments)
  * [Experimenten](#experiments)
  * [Pijplijnen](#ml-pipelines)
  * [Gegevenssets](#datasets-and-datastores)
  * [Modellen](#models)
  * [Eindpunten](#endpoints)

Een werk ruimte bevat andere Azure-resources die worden gebruikt door de werk ruimte:

+ [Azure container Registry (ACR)](https://azure.microsoft.com/services/container-registry/): registreert docker-containers die u tijdens de training gebruikt en wanneer u een model implementeert. Om de kosten te minimaliseren, wordt ACR alleen gemaakt wanneer implementatie kopieën worden gemaakt.
+ [Azure Storage account](https://azure.microsoft.com/services/storage/): wordt gebruikt als de standaard gegevens opslag voor de werk ruimte.  Jupyter-notebooks die worden gebruikt met uw Azure Machine Learning Reken instanties worden hier ook opgeslagen.
+ [Azure-toepassing Insights](https://azure.microsoft.com/services/application-insights/): slaat bewakings informatie over uw modellen op.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): slaat geheimen op die worden gebruikt door Compute-doelen en andere gevoelige informatie die nodig is voor de werk ruimte.

U kunt een werk ruimte delen met anderen.

## <a name="computes"></a>Berekeningen

<a name="compute-targets"></a> Een [Compute-doel](concept-compute-target.md) is een computer of set machines die u gebruikt om uw trainings script uit te voeren of uw service-implementatie te hosten. U kunt uw lokale computer of een externe Compute-resource als reken doel gebruiken.  Met Compute-doelen kunt u training starten op uw lokale machine en uitschalen naar de Cloud zonder uw trainings script te wijzigen.

Azure Machine Learning introduceert twee volledig beheerde virtuele machines in de Cloud (VM) die zijn geconfigureerd voor machine learning taken:

* <a name="compute-instance"></a>**Reken instantie**: een reken instantie is een VM met meerdere hulpprogram ma's en omgevingen die zijn geïnstalleerd voor machine learning. Het primaire gebruik van een reken instantie is voor uw ontwikkel werkstation.  U kunt voorbeeld notitieblokken starten zonder dat Setup is vereist. Een reken instantie kan ook worden gebruikt als een reken doel voor trainings-en detrainings taken.

* **Reken clusters**: reken clusters zijn een cluster met virtuele machines met schaal mogelijkheden voor meerdere knoop punten. Reken clusters zijn beter geschikt voor reken doelen voor grote taken en productie.  Het cluster wordt automatisch geschaald wanneer een taak wordt verzonden.  Gebruik als een trainings berekenings doel of voor een dev/test-implementatie.

Zie [trainings Compute-doelen](concept-compute-target.md#train)voor meer informatie over de doelen van de trainings compute.  Zie [implementatie doelen](concept-compute-target.md#deploy)voor meer informatie over Compute-doelen voor de implementatie.

## <a name="datasets-and-datastores"></a>Gegevens sets en gegevens opslag

Met [**Azure machine learning gegevens sets**](concept-data.md#datasets) kunt u eenvoudig toegang krijgen tot uw gegevens en ermee werken. Door een gegevensset te maken, maakt u een verwijzing naar de locatie van de gegevens bron samen met een kopie van de meta gegevens ervan. Omdat de gegevens zich op de bestaande locatie blijven, ontstaan er geen extra opslag kosten en wordt de integriteit van uw gegevens bronnen niet bedreigd.

Zie [Azure machine learning gegevens sets maken en registreren](how-to-create-register-datasets.md)voor meer informatie.  Raadpleeg de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)voor meer voor beelden met behulp van gegevens sets.

Gegevens sets gebruiken [data stores](concept-data.md#datastores) om veilig verbinding te maken met uw Azure Storage-services. Data stores slaan verbindings gegevens zonder uw verificatie referenties en de integriteit van de oorspronkelijke gegevens bron risico. Er worden verbindings gegevens opgeslagen, zoals uw abonnements-ID en Token autorisatie in uw Key Vault die aan de werk ruimte zijn gekoppeld, zodat u veilig toegang kunt krijgen tot uw opslag zonder dat u deze in uw script hoeft vast te geven.

## <a name="environments"></a>Omgevingen

[Werk ruimte](#workspace)  >  **Omgevingen**

Een [omgeving](concept-environments.md) is de inkapseling van de omgeving waar de training of het scoren van uw machine learning model gebeurt. De omgeving bevat de Python-pakketten, omgevings variabelen en software-instellingen rond uw trainings-en Score scripts.  

Voor code voorbeelden, zie de sectie omgevingen beheren van het [gebruik van omgevingen](how-to-use-environments.md#manage-environments).

## <a name="experiments"></a>Experimenten

[Werk ruimte](#workspace)  >  **Experimenten**

Een experiment is een groepering van veel uitvoeringen van een opgegeven script. Deze behoort altijd tot een werk ruimte. Wanneer u een uitvoering verzendt, geeft u een naam op voor het experiment. Informatie over de uitvoering is opgeslagen onder dat experiment. Als de naam niet bestaat wanneer u een experiment verzendt, wordt automatisch een nieuw experiment gemaakt.
  
Zie [zelf studie: uw eerste model trainen](tutorial-1st-experiment-sdk-train.md)voor een voor beeld van het gebruik van een experiment.

### <a name="runs"></a>Wordt uitgevoerd

[Werk ruimte](#workspace)  >  [Experimenten](#experiments)  >  **Uitvoeren**

Een uitvoering is één uitvoering van een trainings script. Een experiment bevat normaal gesp roken meerdere uitvoeringen.

Azure Machine Learning registreert alle uitvoeringen en slaat de volgende informatie op in het experiment:

* Meta gegevens over de uitvoering (tijds tempel, duur, enzovoort)
* Metrische gegevens die door het script worden geregistreerd
* Uitvoer bestanden die door het experiment worden verzameld of expliciet door u worden geüpload
* Een moment opname van de map die uw scripts bevat vóór de uitvoering

U produceert een uitvoering wanneer u een script voor het trainen van een model verzendt. Een run kan nul of meer onderliggende uitvoeringen hebben. De uitvoering op het hoogste niveau kan bijvoorbeeld twee onderliggende uitvoeringen hebben, waarvan elk een eigen onderliggend item kan hebben.

### <a name="run-configurations"></a>Configuraties uitvoeren

[Werk ruimte](#workspace)  >  [Experimenten](#experiments)  >  [Uitvoeren](#runs)  >  **Configuratie uitvoeren**

Een uitvoerings configuratie definieert hoe een script moet worden uitgevoerd in een opgegeven Compute-doel. U gebruikt de configuratie om het script, het reken doel en de Azure ML-omgeving op te geven die moeten worden uitgevoerd, alle gedistribueerde taak configuraties en enkele aanvullende eigenschappen. Zie [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)voor meer informatie over de volledige set Configureer bare opties voor uitvoeringen.

Een uitvoerings configuratie kan worden opgeslagen in een bestand in de map die uw trainings script bevat.   Of kan worden geconstrueerd als een in-memory-object en worden gebruikt voor het verzenden van een run.

Zie [een training configureren voor een](how-to-set-up-training-targets.md)voor beeld van het uitvoeren van configuraties.

### <a name="snapshots"></a>Momentopnamen

[Werk ruimte](#workspace)  >  [Experimenten](#experiments)  >  [Uitvoeren](#runs)  >  **Moment opname**

Wanneer u een uitvoering verzendt, comprimeert Azure Machine Learning de map die het script bevat als een zip-bestand en verzendt het naar het Compute-doel. Het zip-bestand wordt vervolgens geëxtraheerd en het script wordt uitgevoerd. Azure Machine Learning slaat het zip-bestand ook als een moment opname op als onderdeel van de uitvoerings record. Iedereen met toegang tot de werk ruimte kan bladeren in een uitvoerings record en de moment opname downloaden.

### <a name="logging"></a>Logboekregistratie

Azure Machine Learning registreert automatisch standaard waarden voor de uitvoering. U kunt echter ook [de python-SDK gebruiken om wille keurige metrieken te registreren](how-to-track-experiments.md).

Er zijn meerdere manieren om uw logboeken weer te geven: uitvoerings status van bewaking in realtime of resultaten weer geven na voltooiing. Zie voor meer informatie [monitors en weer geven van ml run logs](how-to-monitor-view-training-logs.md).


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git-tracking en-integratie

Wanneer u begint met het uitvoeren van een training waarbij de bronmap een lokale Git-opslag plaats is, wordt informatie over de opslag plaats opgeslagen in de uitvoerings geschiedenis. Dit werkt met uitvoeringen die zijn verzonden met behulp van een script configuratie of ML-pijp lijn. Het werkt ook voor uitvoeringen die zijn verzonden vanuit de SDK of Machine Learning CLI.

Zie [Git-integratie voor Azure machine learning](concept-train-model-git-integration.md)voor meer informatie.

### <a name="training-workflow"></a>Werk stroom voor training

Wanneer u een experiment uitvoert om een model te trainen, gebeurt de volgende stappen. Deze worden geïllustreerd in het werk stroom diagram training hieronder:

* Azure Machine Learning wordt aangeroepen met de moment opname-ID voor de code momentopname die is opgeslagen in de vorige sectie.
* Azure Machine Learning maakt een run-ID (optioneel) en een Machine Learning-service token, dat later wordt gebruikt door Compute-doelen als Machine Learning Compute/Vm's om te communiceren met de Machine Learning-service.
* U kunt kiezen voor een beheerde Compute-doel (zoals Machine Learning Compute) of een niet-beheerd reken doel (zoals Vm's) om trainings taken uit te voeren. Dit zijn de gegevens stromen voor beide scenario's:
   * Vm's/HDInsight, toegankelijk via SSH-referenties in een sleutel kluis in het micro soft-abonnement. Azure Machine Learning voert beheer code uit op het berekenings doel dat:

   1. Bereidt de omgeving voor. (Docker is een optie voor Vm's en lokale computers. Raadpleeg de volgende stappen voor Machine Learning Compute om te begrijpen hoe het uitvoeren van experimenten op docker-containers werkt.)
   1. Hiermee downloadt u de code.
   1. Hiermee stelt u omgevings variabelen en configuraties in.
   1. Voert gebruikers scripts uit (de code momentopname die in de vorige sectie is vermeld).

   * Machine Learning Compute, toegankelijk via een door een werk ruimte beheerde identiteit.
Omdat Machine Learning Compute een beheerd reken doel is (dat wil zeggen, wordt het beheerd door micro soft), wordt het uitgevoerd onder uw micro soft-abonnement.

   1. De externe docker-constructie wordt zo nodig gestart.
   1. De beheer code wordt geschreven naar de Azure Files share van de gebruiker.
   1. De container wordt gestart met een eerste opdracht. Dat wil zeggen, beheer code zoals beschreven in de vorige stap.

* Nadat de uitvoering is voltooid, kunt u query's uitvoeren en metrische gegevens uitvoeren. In het onderstaande stroom diagram treedt deze stap op wanneer het doel voor het berekenen van de training de metrische uitvoerings waarden terugschrijft naar Azure Machine Learning van opslag in de Cosmos DB-Data Base. Clients kunnen Azure Machine Learning aanroepen. Met Machine Learning worden de metrische gegevens van de Cosmos DB-Data Base opgehaald en terug naar de client geretourneerd.

[![Werk stroom voor training](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modellen

Een model is de eenvoudigste is een stukje code dat een invoer maakt en uitvoer produceert. Het maken van een machine learning model bestaat uit het selecteren van een algoritme, het leveren van gegevens en het [afstemmen van Hyper parameters](how-to-tune-hyperparameters.md). Training is een iteratief proces dat een getraind model produceert dat het model dat u tijdens het trainings proces hebt geleerd.

U kunt een model maken dat buiten Azure Machine Learning is getraind. Of u kunt een model trainen door een [uitvoering](#runs) van een [experiment](#experiments) te verzenden naar een [compute-doel](#compute-targets) in azure machine learning. Zodra u een model hebt, [registreert u het model](#register-model) in de werk ruimte.

Azure Machine Learning is Framework neutraal. Wanneer u een model maakt, kunt u een populair machine learning Framework gebruiken, zoals Scikit-learn, XGBoost, PyTorch, tensor flow en Chainer.

Voor een voor beeld van een model trainen met Scikit-learn, Zie [zelf studie: een classificatie model voor een installatie kopie trainen met Azure machine learning](tutorial-train-models-with-aml.md).


### <a name="model-registry"></a><a name="register-model"></a> Model register

[Werk ruimte](#workspace)  >  **Modellen**

Met het **model register** kunt u alle modellen in uw Azure machine learning-werk ruimte bijhouden.

Modellen worden geïdentificeerd op naam en versie. Telkens wanneer u een model met dezelfde naam registreert als een bestaande, wordt ervan uitgegaan dat het een nieuwe versie is. De versie wordt verhoogd en het nieuwe model wordt geregistreerd onder dezelfde naam.

Wanneer u het model registreert, kunt u extra labels voor meta gegevens opgeven en vervolgens de tags gebruiken wanneer u naar modellen zoekt.

> [!TIP]
> Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in uw Azure Machine Learning-werk ruimte. Na de registratie kunt u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.

U kunt een geregistreerd model dat wordt gebruikt door een actieve implementatie niet verwijderen.

Zie voor een voor beeld van het registreren van een model [een afbeeldings classificatie model trainen met Azure machine learning](tutorial-train-models-with-aml.md).

## <a name="deployment"></a>Implementatie

U implementeert een [geregistreerd model](#register-model) als een service-eind punt. U hebt de volgende onderdelen nodig:

* **Omgeving**. Deze omgeving omvat de afhankelijkheden die nodig zijn voor het uitvoeren van uw model.
* **Score code**. Met dit script worden aanvragen geaccepteerd, worden de aanvragen met behulp van het model gescoord en worden de resultaten geretourneerd.
* **Configuratie** afleiding. De configuratie voor afwijzen specificeert de omgeving, het invoer script en andere onderdelen die nodig zijn om het model als een service uit te voeren.

Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over deze onderdelen.

### <a name="endpoints"></a>Eindpunten

[Werk ruimte](#workspace)  >  **Eind punten**

Een eind punt is een instantie van uw model in een webservice die kan worden gehost in de Cloud of een IoT-module voor geïntegreerde implementaties van apparaten.

#### <a name="web-service-endpoint"></a>Webservice-eind punt

Bij het implementeren van een model als een webservice, kan het eind punt worden geïmplementeerd op Azure Container Instances, Azure Kubernetes service of Fpga's. U maakt de service vanuit uw model, script en de bijbehorende bestanden. Deze worden in een basis container installatie kopie geplaatst, die de uitvoerings omgeving voor het model bevat. De afbeelding heeft een HTTP-eind punt met gelijke taak verdeling dat Score aanvragen ontvangt die naar de webservice worden verzonden.

U kunt Application Insights telemetrie of model-telemetrie inschakelen om uw webservice te bewaken. De telemetriegegevens zijn alleen toegankelijk voor u.  Deze wordt opgeslagen in uw Application Insights-en opslag account-exemplaren. Als u automatisch schalen hebt ingeschakeld, wordt uw implementatie automatisch door Azure geschaald.

In het volgende diagram ziet u de werk stroom voor het afwijzen van een model dat is geïmplementeerd als een webservice-eind punt:

Dit zijn de details:

* De gebruiker registreert een model met behulp van een-client zoals de Azure Machine Learning SDK.
* De gebruiker maakt een installatie kopie met behulp van een model, een score bestand en andere model afhankelijkheden.
* De docker-installatie kopie wordt gemaakt en opgeslagen in Azure Container Registry.
* De webservice wordt geïmplementeerd op het Compute-doel (Container Instances/AKS) met behulp van de installatie kopie die u in de vorige stap hebt gemaakt.
* Details van Score aanvragen worden opgeslagen in Application Insights, dat zich in het abonnement van de gebruiker bevindt.
* Telemetrie wordt ook gepusht naar het micro soft/Azure-abonnement.

[![Werk stroom afwijzen](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Voor een voor beeld van het implementeren van een model als een webservice raadpleegt u [een installatie kopie classificatie model implementeren in azure container instances](tutorial-deploy-models-with-aml.md).

#### <a name="real-time-endpoints"></a>Realtime-eind punten

Wanneer u een getraind model in de ontwerp functie implementeert, kunt u [het model als een real-time-eind punt implementeren](tutorial-designer-automobile-price-deploy.md). Een real-time eind punt ontvangt doorgaans één aanvraag via het REST-eind punt en retourneert een voor spelling in realtime. Dit is in tegens telling tot batch verwerking, waarbij meerdere waarden tegelijk worden verwerkt en de resultaten worden opgeslagen na voltooiing naar een gegevens opslag.

#### <a name="pipeline-endpoints"></a>Pijplijneindpunten

Met pijplijn eindpunten kunt u uw [ml-pijp lijnen](#ml-pipelines) via een rest-eind punt aanroepen. Met pijplijn eindpunten kunt u uw pijplijn werk stromen automatiseren.

Een pijplijn eindpunt is een verzameling gepubliceerde pijp lijnen. Met deze logische organisatie kunt u meerdere pijp lijnen beheren en aanroepen met behulp van hetzelfde eind punt. Elke gepubliceerde pijp lijn in een pijp lijn-eind punt heeft een versie nummer. U kunt een standaard pijplijn selecteren voor het eind punt of een versie opgeven in de REST-aanroep.
 

#### <a name="iot-module-endpoints"></a>IoT-module-eind punten

Een geïmplementeerd IoT-module-eind punt is een docker-container die uw model en het bijbehorende script of de gekoppelde toepassing en eventuele extra afhankelijkheden bevat. U implementeert deze modules met behulp van Azure IoT Edge op edge-apparaten.

Als u bewaking hebt ingeschakeld, verzamelt Azure telemetriegegevens van het model in de module Azure IoT Edge. De telemetriegegevens zijn alleen toegankelijk voor u en worden opgeslagen in uw opslag account-exemplaar.

Azure IoT Edge zorgt ervoor dat de module wordt uitgevoerd en controleert het apparaat waarop het wordt gehost. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

De [Azure machine learning cli](reference-azure-machine-learning-cli.md) is een uitbrei ding van Azure CLI, een platformoverschrijdende opdracht regel interface voor het Azure-platform. Deze uitbrei ding bevat opdrachten om uw machine learning activiteiten te automatiseren.

### <a name="ml-pipelines"></a>ML-pijp lijnen

U gebruikt [machine learning pijp lijnen](concept-ml-pipelines.md) voor het maken en beheren van werk stromen die samen een machine learning fasen opdoen. Een pijp lijn kan bijvoorbeeld bestaan uit gegevens voorbereiding, model training, model implementatie en fase ring/scores. Elke fase kan meerdere stappen omvatten, die elk zonder toezicht kunnen worden uitgevoerd in verschillende reken doelen. 

U kunt de stappen van de pijp lijn herbruikbaresen en uitvoeren zonder de vorige stappen opnieuw uit te voeren als de uitvoer van deze stappen niet is gewijzigd. U kunt bijvoorbeeld een model opnieuw trainen zonder de stappen voor het voorbereiden van de kosten voor het opnieuw uitvoeren van gegevens als de gegevens niet zijn gewijzigd. Door pijp lijnen kunnen gegevens wetenschappers ook samen werken terwijl ze werken op verschillende gebieden van een machine learning werk stroom.

## <a name="monitoring-and-logging"></a>Bewaking en registratie

Azure Machine Learning biedt de volgende mogelijkheden voor bewaking en logboek registratie:

* Voor __gegevens wetenschappers__ kunt u uw experimenten en logboek gegevens van uw trainings uitvoeringen bewaken. Raadpleeg voor meer informatie de volgende artikelen:
   * [Trainings uitvoeringen starten, controleren en annuleren](how-to-manage-runs.md)
   * [Metrische gegevens registreren voor trainingsuitvoeringen](how-to-track-experiments.md)
   * [Experimenten bijhouden met MLflow](how-to-use-mlflow.md)
   * [Uitvoeringen visualiseren met TensorBoard](how-to-monitor-tensorboard.md)
* Voor __beheerders__ kunt u informatie over de werk ruimte, verwante Azure-resources en gebeurtenissen, zoals het maken en verwijderen van resources, bewaken met behulp van Azure monitor. Zie [Azure machine learning bewaken](monitor-azure-machine-learning.md)voor meer informatie.
* Voor __DevOps__ of __MLOps__ kunt u gegevens controleren die zijn gegenereerd door modellen die zijn geïmplementeerd als webservices of IOT Edge modules om problemen met de implementaties te identificeren en gegevens te verzamelen die naar de service worden verzonden. Zie voor meer informatie [model gegevens verzamelen](how-to-enable-data-collection.md) en [bewaken met Application Insights](how-to-enable-app-insights.md).

## <a name="interacting-with-your-workspace"></a>Interactie met uw werk ruimte

### <a name="studio"></a>Studio

[Azure machine learning Studio](overview-what-is-machine-learning-studio.md) biedt een webweergave van alle artefacten in uw werk ruimte.  U kunt de resultaten en Details van uw gegevens sets, experimenten, pijp lijnen, modellen en eind punten weer geven.  U kunt ook reken resources en gegevens bronnen beheren in de Studio.

De studio heeft ook toegang tot de interactieve hulp middelen die deel uitmaken van Azure Machine Learning:

+ [Azure machine learning Designer](concept-designer.md) om werk stroom stappen uit te voeren zonder code te schrijven
+ Webervaring voor [automatische machine learning](concept-automated-ml.md)
+ [Azure machine learning notitie blokken](how-to-run-jupyter-notebooks.md) om uw eigen code te schrijven en uit te voeren in geïntegreerde Jupyter-notebook servers.
+ [Gegevens label projecten](how-to-create-labeling-projects.md) voor het maken, beheren en bewaken van projecten voor het labelen van uw gegevens

### <a name="programming-tools"></a>Programmeer hulpprogramma's

> [!IMPORTANT]
> De hulpprogram ma's die zijn gemarkeerd (preview) zijn momenteel beschikbaar als open bare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

+  Communiceer met de service in een python-omgeving met de [Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro).
+ Communiceer met de service in een wille keurige R-omgeving met de [Azure machine learning SDK voor R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (preview).
+ Gebruik [Azure machine learning Designer](concept-designer.md) om de werk stroom stappen uit te voeren zonder code te schrijven. 
+ Gebruik [Azure machine learning cli](./reference-azure-machine-learning-cli.md) voor Automation.
+ De [Many Models Solution Accelerator](https://aka.ms/many-models) (preview) heeft Azure Machine Learning als basis en stelt u in staat om honderden, of zelfs duizenden machine Learning-modellen, te trainen, te gebruiken en te beheren.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met Azure Machine Learning raadpleegt u:

* [Wat is Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Een Azure Machine Learning-werk ruimte maken](how-to-manage-workspace.md)
* [Zelf studie (deel 1): een model trainen](tutorial-train-models-with-aml.md)