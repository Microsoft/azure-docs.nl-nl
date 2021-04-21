---
title: Architectuur & belangrijkste concepten
titleSuffix: Azure Machine Learning
description: In dit artikel krijgt u een algemeen inzicht in de architectuur, termen en concepten waar Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: f1eb7a5b4697801775d23091c610ab594b0b27ec
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813376"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Hoe Azure Machine Learning werkt: architectuur en concepten

Meer informatie over de architectuur en concepten [voor Azure Machine Learning](overview-what-is-azure-ml.md).  In dit artikel krijgt u een hoog begrip van de onderdelen en hoe deze samenwerken om u te helpen bij het bouwen, implementeren en onderhouden van machine learning modellen.

## <a name="workspace"></a><a name="workspace"></a> Werkruimte

Een [machine learning is](concept-workspace.md) de resource op het hoogste niveau voor Azure Machine Learning.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Diagram: Azure Machine Learning van een werkruimte en de onderdelen ervan":::

De werkruimte is de centrale plaats voor het volgende:

* Resources beheren die u gebruikt voor het trainen en implementeren van modellen, zoals [berekeningen](#compute-instance)
* Sla assets op die u maakt wanneer u Azure Machine Learning, waaronder:
  * [Omgevingen](#environments)
  * [Experimenten](#experiments)
  * [Pijplijnen](#ml-pipelines)
  * [Gegevenssets](#datasets-and-datastores)
  * [Modellen](#models)
  * [Eindpunten](#endpoints)

Een werkruimte bevat andere Azure-resources die worden gebruikt door de werkruimte:

+ [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/): registreert dockercontainers die u gebruikt tijdens de training en wanneer u een model implementeert. Om de kosten te minimaliseren, wordt ACR alleen gemaakt wanneer implementatie-installatie images worden gemaakt.
+ [Azure Storage account:](https://azure.microsoft.com/services/storage/)wordt gebruikt als het standaardgegevensstore voor de werkruimte.  Jupyter-notebooks die worden gebruikt met uw Azure Machine Learning reken-exemplaren worden hier ook opgeslagen.
+ [Azure-toepassing Insights: slaat bewakingsinformatie](https://azure.microsoft.com/services/application-insights/)over uw modellen op.
+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)slaat geheimen op die worden gebruikt door rekendoelen en andere gevoelige informatie die nodig is voor de werkruimte.

U kunt een werkruimte delen met anderen.

## <a name="computes"></a>Berekent

<a name="compute-targets"></a> Een [rekendoel](concept-compute-target.md) is een computer of set machines die u gebruikt om uw trainingsscript uit te voeren of uw service-implementatie te hosten. U kunt uw lokale computer of een externe rekenresource gebruiken als rekendoel.  Met rekendoelen kunt u beginnen met trainen op uw lokale computer en vervolgens uitschalen naar de cloud zonder uw trainingsscript te wijzigen.

Azure Machine Learning introduceert twee volledig beheerde virtuele machines (VM's) in de cloud die zijn geconfigureerd voor machine learning taken:

* <a name="compute-instance"></a>**Reken exemplaar:** een reken-exemplaar is een VM met meerdere hulpprogramma's en omgevingen die zijn geïnstalleerd voor machine learning. Het primaire gebruik van een reken-exemplaar is voor uw ontwikkelwerkstation.  U kunt beginnen met het uitvoeren van voorbeeldnote notebooks zonder installatie vereist. Een reken-exemplaar kan ook worden gebruikt als rekendoel voor trainings- en deferencingtaken.

* **Rekenclusters:** rekenclusters zijn een cluster van VM's met schaalmogelijkheden voor meerdere knooppunt. Rekenclusters zijn beter geschikt voor rekendoelen voor grote taken en productie.  Het cluster wordt automatisch omhoog geschaald wanneer een taak wordt verzonden.  Gebruik als een rekendoel voor training of voor de implementatie van dev/test.

Zie Rekendoelen trainen voor meer informatie over [trainingsrekendoelen.](concept-compute-target.md#train)  Zie Implementatiedoelen voor meer informatie over rekendoelen [voor implementaties.](concept-compute-target.md#deploy)

## <a name="datasets-and-datastores"></a>Gegevenssets en gegevensstores

[**Azure Machine Learning gegevenssets**](concept-data.md#datasets)  kunt u gemakkelijker toegang krijgen tot uw gegevens en persoonsgegevens gebruiken. Door een gegevensset te maken, maakt u een verwijzing naar de locatie van de gegevensbron samen met een kopie van de metagegevens. Omdat de gegevens op de bestaande locatie blijven, worden er geen extra opslagkosten in gebracht en loopt u geen risico op de integriteit van uw gegevensbronnen.

Zie Create [and register Azure Machine Learning Datasets (Gegevenssets maken en registreren) voor meer informatie.](how-to-create-register-datasets.md)  Zie de voorbeeldnotenotes voor meer voorbeelden van het gebruik [van gegevenssets.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)

Gegevenssets gebruiken [gegevensopslag om](concept-data.md#datastores) veilig verbinding te maken met uw Azure Storage-services. In gegevensopslag worden verbindingsgegevens opgeslagen zonder dat uw verificatiereferenties en de integriteit van uw oorspronkelijke gegevensbron risico lopen. Ze slaan verbindingsgegevens, zoals uw abonnements-id en tokenautorisatie, op in uw Key Vault die aan de werkruimte is gekoppeld, zodat u veilig toegang hebt tot uw opslag zonder dat u ze in uw script in code moet coden.

## <a name="environments"></a>Omgevingen

[Werkruimte](#workspace)  >  **Omgevingen**

Een [omgeving](concept-environments.md) is de inkapseling van de omgeving waarin het trainen of scoren van uw machine learning gebeurt. In de omgeving worden de Python-pakketten, omgevingsvariabelen en software-instellingen rond uw trainings- en scorescripts opgegeven.  

Zie de sectie Omgevingen beheren van Omgevingen gebruiken voor [codevoorbeelden.](how-to-use-environments.md#manage-environments)

## <a name="experiments"></a>Experimenten

[Werkruimte](#workspace)  >  **Experimenten**

Een experiment is een groepering van veel runs vanuit een opgegeven script. Deze hoort altijd bij een werkruimte. Wanneer u een run indient, geeft u een experimentnaam op. Informatie voor de run wordt opgeslagen onder dat experiment. Als de naam niet bestaat wanneer u een experiment indient, wordt er automatisch een nieuw experiment gemaakt.
  
Zie Zelfstudie: Uw eerste model trainen voor een voorbeeld van het gebruik [van een experiment.](tutorial-1st-experiment-sdk-train.md)

### <a name="runs"></a>Wordt uitgevoerd

[Werkruimte](#workspace)  >  [Experimenten](#experiments)  >  **Uitvoeren**

Een uitvoering is één uitvoering van een trainingsscript. Een experiment bevat doorgaans meerdere runs.

Azure Machine Learning registreert alle runs en slaat de volgende informatie op in het experiment:

* Metagegevens over de run (tijdstempel, duur, e.d.)
* Metrische gegevens die door uw script worden geregistreerd
* Uitvoerbestanden die automatisch worden opgehaald door het experiment of expliciet door u zijn geüpload
* Een momentopname van de map die uw scripts bevat, vóór de run

U produceert een uitvoering wanneer u een script voor het trainen van een model indient. Een run kan nul of meer onderliggende runs hebben. De run op het hoogste niveau kan bijvoorbeeld twee onderliggende runs hebben, die elk een eigen onderliggende run kunnen hebben.

### <a name="run-configurations"></a>Configuraties uitvoeren

[Werkruimte](#workspace)  >  [Experimenten](#experiments)  >  [Uitvoeren](#runs)  >  **Configuratie uitvoeren**

Een uitvoeringsconfiguratie definieert hoe een script moet worden uitgevoerd in een opgegeven rekendoel. U gebruikt de configuratie om het script, het rekendoel en de Azure ML-omgeving op te geven die moeten worden uitgevoerd, eventuele gedistribueerde taakspecifieke configuraties en enkele aanvullende eigenschappen. Zie [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)voor meer informatie over de volledige set configureerbare opties voor runs.

Een uitvoeringsconfiguratie kan worden opgeslagen in een bestand in de map die uw trainingsscript bevat.   Of het kan worden samengesteld als een in-memory object en worden gebruikt om een run te verzenden.

Zie Configure a training run (Een [trainingsrun configureren) voor bijvoorbeeld runconfiguraties.](how-to-set-up-training-targets.md)

### <a name="snapshots"></a>Momentopnamen

[Werkruimte](#workspace)  >  [Experimenten](#experiments)  >  [Uitvoeren](#runs)  >  **Momentopname**

Wanneer u een run verzendt, Azure Machine Learning de map met het script als zip-bestand gecomprimeerd en naar het rekendoel verzenden. Het zip-bestand wordt vervolgens geëxtraheerd en het script wordt daar uitgevoerd. Azure Machine Learning slaat het zip-bestand ook op als een momentopname als onderdeel van de runrecord. Iedereen met toegang tot de werkruimte kan door een runrecord bladeren en de momentopname downloaden.

### <a name="logging"></a>Logboekregistratie

Azure Machine Learning registreert automatisch standaard metrische gegevens voor uitvoeren voor u. U kunt echter ook de [Python SDK gebruiken om willekeurige metrische gegevens te loggen.](how-to-log-view-metrics.md)

Er zijn meerdere manieren om uw logboeken weer te geven: de uitvoeringsstatus in realtime bewaken of resultaten bekijken na voltooiing. Zie Ml-runlogboeken [bewaken en weergeven voor meer informatie.](how-to-log-view-metrics.md)


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git-tracering en -integratie

Wanneer u een trainingsrun start waarbij de bronmap een lokale Git-opslagplaats is, wordt informatie over de opslagplaats opgeslagen in de geschiedenis van de run. Dit werkt met uitvoeringen die zijn verzonden met behulp van een scriptrunconfiguratie of ML-pijplijn. Het werkt ook voor runs die zijn verzonden vanuit de SDK of Machine Learning CLI.

Zie Git-integratie voor [meer informatie Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="training-workflow"></a>Trainingswerkstroom

Wanneer u een experiment om een model te trainen, worden de volgende stappen uitgevoerd. Deze worden weergegeven in het onderstaande diagram van de trainingswerkstroom:

* Azure Machine Learning wordt aangeroepen met de momentopname-id voor de codemomentopname die in de vorige sectie is opgeslagen.
* Azure Machine Learning maakt een run-id (optioneel) en een Machine Learning service-token, die later worden gebruikt door rekendoelen zoals Machine Learning Compute/VM's om te communiceren met de Machine Learning service.
* U kunt een beheerd rekendoel (zoals Machine Learning Compute) of een niet-beheerd rekendoel (zoals VM's) kiezen om trainingstaken uit te voeren. Dit zijn de gegevensstromen voor beide scenario's:
   * VM's/HDInsight, toegankelijk via SSH-referenties in een sleutelkluis in het Microsoft-abonnement. Azure Machine Learning voert beheercode uit op het rekendoel dat:

   1. Bereidt de omgeving voor. (Docker is een optie voor VM's en lokale computers. Zie de volgende stappen voor meer Machine Learning Compute hoe het uitvoeren van experimenten op Docker-containers werkt.)
   1. Downloadt de code.
   1. Stelt omgevingsvariabelen en configuraties in.
   1. Voert gebruikersscripts uit (de codemomentopname die in de vorige sectie is vermeld).

   * Machine Learning Compute, toegankelijk via een door een werkruimte beheerde identiteit.
Omdat Machine Learning Compute een beheerd rekendoel is (dat wil zeggen, het wordt beheerd door Microsoft), wordt het uitgevoerd onder uw Microsoft-abonnement.

   1. Zo nodig wordt de externe Docker-constructie van start gaat.
   1. Beheercode wordt geschreven naar de Azure Files gebruiker.
   1. De container wordt gestart met een initiële opdracht. Dat wil zeggen, beheercode zoals beschreven in de vorige stap.

* Nadat de run is voltooid, kunt u query's uitvoeren op runs en metrische gegevens. In het onderstaande stroomdiagram vindt deze stap plaats wanneer het rekendoel van de training de metrische gegevens van de run terug schrijft naar Azure Machine Learning opslag in de Cosmos DB database. Clients kunnen een Azure Machine Learning. Machine Learning haalt op zijn beurt metrische gegevens op uit Cosmos DB database en retournt deze terug naar de client.

[![Trainingswerkstroom](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modellen

In de eenvoudigste plaats is een model een stuk code dat invoer gebruikt en uitvoer produceert. Het maken machine learning model omvat het selecteren van een algoritme, het leveren van gegevens en het [afstemmen van hyperparameters.](how-to-tune-hyperparameters.md) Training is een iteratief proces dat een getraind model produceert, waarin wordt ingekapseld wat het model tijdens het trainingsproces heeft geleerd.

U kunt een model gebruiken dat buiten het model is Azure Machine Learning. U kunt een model ook [](#runs) trainen door een uitvoering van een [experiment](#experiments) in te dienen bij een [rekendoel](#compute-targets) in Azure Machine Learning. Zodra u een model hebt, registreert [u het model](#register-model) in de werkruimte.

Azure Machine Learning is frameworkagnostisch. Wanneer u een model maakt, kunt u elk populair machine learning-framework gebruiken, zoals Scikit-learn, XGBoost, PyTorch, TensorFlow en Chainer.

Zie Zelfstudie: Een model voor afbeeldingsclassificatie trainen met behulp van Scikit-learn voor een voorbeeld van het trainen [van een model Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="model-registry"></a><a name="register-model"></a> Modelregister

[Werkruimte](#workspace)  >  **Modellen**

Met **het modelregister** kunt u alle modellen in uw werkruimte Azure Machine Learning bijhouden.

Modellen worden aangeduid met de naam en versie. Telkens wanneer u een model registreert met dezelfde naam als een bestaand model, gaat het register ervan uit dat het een nieuwe versie is. De versie wordt verhoogd en het nieuwe model wordt geregistreerd onder dezelfde naam.

Wanneer u het model registreert, kunt u aanvullende tags voor metagegevens opgeven en vervolgens de tags gebruiken wanneer u naar modellen zoekt.

> [!TIP]
> Een geregistreerd model is een logische container voor een of meer bestanden waar uw model uit bestaat. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u het model registreren als één model in Azure Machine Learning werkruimte. Na de registratie kunt u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.

U kunt een geregistreerd model dat wordt gebruikt door een actieve implementatie niet verwijderen.

Zie Train an image classification [model with Azure Machine Learning (Een](tutorial-train-models-with-aml.md)model voor afbeeldingsclassificatie trainen met Azure Machine Learning) voor een voorbeeld van het registreren Azure Machine Learning.

## <a name="deployment"></a>Implementatie

U implementeert [een geregistreerd model](#register-model) als een service-eindpunt. U hebt de volgende onderdelen nodig:

* **Omgeving**. Deze omgeving kapselt de afhankelijkheden in die nodig zijn om uw model uit te voeren voor de deferentie.
* **Scorecode**. Dit script accepteert aanvragen, scores van de aanvragen met behulp van het model en retourneert de resultaten.
* **De deferentieconfiguratie**. De deferentieconfiguratie geeft de omgeving, het invoerscript en andere onderdelen aan die nodig zijn om het model als een service uit te voeren.

Zie Modellen implementeren met Azure Machine Learning voor meer [informatie over Azure Machine Learning.](how-to-deploy-and-where.md)

### <a name="endpoints"></a>Eindpunten

[Werkruimte](#workspace)  >  **Eindpunten**

Een eindpunt is een instantiëring van uw model in een webservice die kan worden gehost in de cloud of een IoT-module voor geïntegreerde apparaatimplementaties.

#### <a name="web-service-endpoint"></a>Webservice-eindpunt

Wanneer u een model als een webservice implementeert, kan het eindpunt worden geïmplementeerd op Azure Container Instances, Azure Kubernetes Service of FPGA's. U maakt de service van uw model, script en bijbehorende bestanden. Deze worden in een basiscontainer-afbeelding geplaatst, die de uitvoeringsomgeving voor het model bevat. De afbeelding heeft een HTTP-eindpunt met load-balanced dat scoring-aanvragen ontvangt die naar de webservice worden verzonden.

U kunt de Application Insights of model-telemetrie inschakelen om uw webservice te bewaken. De telemetriegegevens zijn alleen voor u toegankelijk.  Het wordt opgeslagen in uw Application Insights en opslagaccounts. Als u automatisch schalen hebt ingeschakeld, schaalt Azure uw implementatie automatisch.

In het volgende diagram ziet u de deferentiewerkstroom voor een model dat is geïmplementeerd als een webservice-eindpunt:

Dit zijn de details:

* De gebruiker registreert een model met behulp van een client zoals de Azure Machine Learning SDK.
* De gebruiker maakt een afbeelding met behulp van een model, een scorebestand en andere modelafhankelijkheden.
* De Docker-afbeelding wordt gemaakt en opgeslagen in Azure Container Registry.
* De webservice wordt geïmplementeerd op het rekendoel (Container Instances/AKS) met behulp van de afbeelding die u in de vorige stap hebt gemaakt.
* Scoreaanvraagdetails worden opgeslagen in Application Insights, die zich in het abonnement van de gebruiker.
* Telemetrie wordt ook naar het Microsoft/Azure-abonnement pushen.

[![De deferentiewerkstroom](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Zie Deploy an image classification model in Azure Container Instances (Een afbeeldingsclassificatiemodel implementeren [in Azure Container Instances)](tutorial-deploy-models-with-aml.md)voor een voorbeeld van het implementeren van een model Azure Container Instances .

#### <a name="real-time-endpoints"></a>Realtime-eindpunten

Wanneer u een getraind model in de ontwerpfunctie implementeert, kunt u het [model implementeren als een realtime-eindpunt.](tutorial-designer-automobile-price-deploy.md) Een realtime-eindpunt ontvangt doorgaans één aanvraag via het REST-eindpunt en retourneert een voorspelling in realtime. Dit is in tegenstelling tot batchverwerking, waarbij meerdere waarden tegelijk worden verwerkt en de resultaten na voltooiing worden op slaat in een gegevensstore.

#### <a name="pipeline-endpoints"></a>Pijplijneindpunten

Met pijplijn-eindpunten kunt u uw [ML-pijplijnen](#ml-pipelines) programmatisch aanroepen via een REST-eindpunt. Met pijplijn-eindpunten kunt u uw pijplijnwerkstromen automatiseren.

Een pijplijn-eindpunt is een verzameling gepubliceerde pijplijnen. Met deze logische organisatie kunt u meerdere pijplijnen beheren en aanroepen met hetzelfde eindpunt. Voor elke gepubliceerde pijplijn in een pijplijn-eindpunt is versieversie beschikbaar. U kunt een standaardpijplijn voor het eindpunt selecteren of een versie opgeven in de REST-aanroep.
 

#### <a name="iot-module-endpoints"></a>Eindpunten van IoT-module

Een geïmplementeerd IoT-module-eindpunt is een Docker-container die uw model en bijbehorende script of toepassing en eventuele extra afhankelijkheden bevat. U implementeert deze modules met behulp van Azure IoT Edge op edge-apparaten.

Als u bewaking hebt ingeschakeld, verzamelt Azure telemetriegegevens van het model in Azure IoT Edge module. De telemetriegegevens zijn alleen toegankelijk voor u en worden opgeslagen in de instantie van uw opslagaccount.

Azure IoT Edge zorgt ervoor dat uw module wordt uitgevoerd en controleert het apparaat dat het host. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

De [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) is een uitbreiding op de Azure CLI, een platformoverschrijdende opdrachtregelinterface voor het Azure-platform. Deze extensie biedt opdrachten voor het automatiseren van uw machine learning activiteiten.

### <a name="ml-pipelines"></a>ML-pijplijnen

U gebruikt [machine learning pijplijnen om](concept-ml-pipelines.md) werkstromen te maken en beheren die machine learning samenbrengen. Een pijplijn kan bijvoorbeeld gegevensvoorbereiding, modeltraining, modelimplementatie en de deference/scoring-fasen bevatten. Elke fase kan meerdere stappen omvatten, die elk zonder toezicht kunnen worden uitgevoerd in verschillende rekendoelen. 

Pijplijnstappen kunnen opnieuw worden gebruikt en kunnen worden uitgevoerd zonder de vorige stappen opnieuw uit te voeren als de uitvoer van deze stappen niet is gewijzigd. U kunt een model bijvoorbeeld opnieuw trainen zonder kostbare stappen voor gegevensvoorbereiding opnieuw uit te voeren als de gegevens niet zijn gewijzigd. Met pijplijnen kunnen gegevenswetenschappers ook samenwerken terwijl ze aan afzonderlijke gebieden van een machine learning werken.

## <a name="monitoring-and-logging"></a>Bewaking en registratie

Azure Machine Learning biedt de volgende mogelijkheden voor bewaking en logboekregistratie:

* Voor __gegevenswetenschappers__ kunt u uw experimenten bewaken en gegevens van uw trainingstrainingen in een logboek opslaan. Raadpleeg voor meer informatie de volgende artikelen:
   * [Trainings runs starten, bewaken en annuleren](how-to-track-monitor-analyze-runs.md)
   * [Metrische gegevens registreren voor trainingsuitvoeringen](how-to-log-view-metrics.md)
   * [Experimenten bijhouden met MLflow](how-to-use-mlflow.md)
   * [Uitvoeringen visualiseren met TensorBoard](how-to-monitor-tensorboard.md)
* Voor __beheerders kunt u__ informatie over de werkruimte, gerelateerde Azure-resources en gebeurtenissen bewaken, zoals het maken en verwijderen van resources met behulp van Azure Monitor. Zie Voor meer informatie [How to monitor Azure Machine Learning](monitor-azure-machine-learning.md).
* Voor __DevOps__ of __MLOps__ kunt u informatie bewaken die wordt gegenereerd door modellen die zijn geïmplementeerd als webservices of IoT Edge-modules om problemen met de implementaties te identificeren en gegevens te verzamelen die naar de service zijn verzonden. Zie Modelgegevens [verzamelen](how-to-enable-data-collection.md) en Bewaken met Application Insights [voor meer Application Insights.](how-to-enable-app-insights.md)

## <a name="interacting-with-your-workspace"></a>Interactie met uw werkruimte

### <a name="studio"></a>Studio

[Azure Machine Learning-studio](overview-what-is-machine-learning-studio.md) biedt een webweergave van alle artefacten in uw werkruimte.  U kunt de resultaten en details van uw gegevenssets, experimenten, pijplijnen, modellen en eindpunten bekijken.  U kunt ook rekenbronnen en gegevensstores beheren in de studio.

In de studio hebt u ook toegang tot de interactieve hulpprogramma's die deel uitmaken van Azure Machine Learning:

+ [Azure Machine Learning om werkstroomstappen](concept-designer.md) uit te voeren zonder code te schrijven
+ Webervaring voor [geautomatiseerde machine learning](concept-automated-ml.md)
+ [Azure Machine Learning notebooks om](how-to-run-jupyter-notebooks.md) uw eigen code te schrijven en uit te voeren in geïntegreerde Jupyter Notebook-servers.
+ [Gegevenslabelprojecten voor het](how-to-create-labeling-projects.md) maken, beheren en bewaken van projecten om uw gegevens te labelen

### <a name="programming-tools"></a>Programmeerprogramma's

> [!IMPORTANT]
> Hulpprogramma's die hieronder zijn gemarkeerd (preview) zijn momenteel beschikbaar als openbare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

+  Interactie met de service in een Python-omgeving met [de Azure Machine Learning SDK voor Python.](/python/api/overview/azure/ml/intro)
+ Interactie met de service in een R-omgeving met [de Azure Machine Learning SDK voor R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (preview).
+ Gebruik [Azure Machine Learning designer om](concept-designer.md) de werkstroomstappen uit te voeren zonder code te schrijven. 
+ Gebruik [Azure Machine Learning CLI](./reference-azure-machine-learning-cli.md) voor automatisering.
+ De [Many Models Solution Accelerator](https://aka.ms/many-models) (preview) heeft Azure Machine Learning als basis en stelt u in staat om honderden, of zelfs duizenden machine Learning-modellen, te trainen, te gebruiken en te beheren.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt Azure Machine Learning, gaat u naar:

* [Wat is Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Een werkruimte Azure Machine Learning maken](how-to-manage-workspace.md)
* [Zelfstudie (deel 1): Een model trainen](tutorial-train-models-with-aml.md)