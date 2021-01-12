---
title: 'MLOps: ML model beheer'
titleSuffix: Azure Machine Learning
description: 'Meer informatie over model beheer met Azure Machine Learning (MLOps). Implementeer, beheer en bewaak uw modellen om ze continu te verbeteren. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 8813b2cad5800bd4ede6e64119117df57c230fdc
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071218"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: model beheer, implementatie en bewaking met Azure Machine Learning

In dit artikel vindt u informatie over het gebruik van Azure Machine Learning voor het beheren van de levens cyclus van uw modellen. Azure Machine Learning maakt gebruik van een MLOps-benadering (Machine Learning Operations). MLOps verbetert de kwaliteit en consistentie van uw machine learning oplossingen. 

## <a name="what-is-mlops"></a>Wat is MLOps?

Machine Learning Operations (MLOps) is gebaseerd op [DevOps](https://azure.microsoft.com/overview/what-is-devops/) -principes en-procedures die de efficiëntie van werk stromen verg Roten. Bijvoorbeeld continue integratie, levering en implementatie. MLOps past deze principes toe op het machine learning proces, met als doel:

* Sneller experimenteren en ontwikkelen van modellen
* Snellere implementatie van modellen in productie
* Kwaliteitscontrole

Azure Machine Learning biedt de volgende MLOps mogelijkheden:

- **Maak reproduceer bare ml-pijp lijnen**. Met Machine Learning pijp lijnen kunt u Herhaal bare en herbruikbare stappen definiëren voor uw gegevens voorbereiding, training en Score processen.
- **Herbruikbare software omgevingen maken** voor trainings-en implementatie modellen.
- **Registreer, verpak en implementeer modellen vanaf elke locatie**. U kunt ook gekoppelde meta gegevens volgen die vereist zijn voor het gebruik van het model.
- **Leg de governance-gegevens vast voor de end-to-end ml-levens cyclus**. De vastgelegde informatie kan bestaan uit wie modellen publiceert, waarom wijzigingen zijn aangebracht en wanneer modellen zijn geïmplementeerd of gebruikt voor productie.
- Meld u **aan en waarschuw op gebeurtenissen in de levens cyclus van ml**. Voor beeld: voltooiing van het experiment, model registratie, model implementatie en detectie van gegevens drift.
- **Bewaak ml-toepassingen voor operationele en ml-gerelateerde problemen**. Vergelijk model invoer tussen training en demijnen, verken specifieke metrische gegevens en geef bewaking en waarschuwingen op uw ML-infra structuur.
- **Automatiseer de end-to-end ml-levens cyclus met Azure machine learning en Azure-pijp lijnen**. Met behulp van pijp lijnen kunt u vaak modellen bijwerken, nieuwe modellen testen en voortdurend nieuwe ML-modellen implementeren naast uw andere toepassingen en services.

## <a name="create-reproducible-ml-pipelines"></a>Reproduceer bare ML-pijp lijnen maken

Gebruik ML-pijp lijnen van Azure Machine Learning om alle stappen in uw model trainings proces samen te voegen.

Een ML-pijp lijn kan stappen bevatten van gegevens voorbereiding voor het uitpakken van de functie voor het afstemmen van de model evaluatie. Zie [ml-pijp lijnen](concept-ml-pipelines.md)voor meer informatie.

Als u de [Designer](concept-designer.md) gebruikt om uw ml-pijp lijnen te maken, kunt u op elk gewenst moment klikken op **'... '** in de rechter bovenhoek van de ontwerp pagina en vervolgens **klonen** selecteren. Door uw pijp lijn te klonen, kunt u het ontwerp van de pijp lijn herhalen zonder dat uw oude versies verloren gaan.  

## <a name="create-reusable-software-environments"></a>Herbruikbare software omgevingen maken

Met Azure Machine Learning omgevingen kunt u de software afhankelijkheden van uw projecten bijhouden en reproduceren tijdens het ontwikkelen. Met omgevingen kunt u ervoor zorgen dat builds worden verreproduceerd zonder hand matige software configuraties.

Omgevingen beschrijven de PIP-en Conda-afhankelijkheden voor uw projecten en kunnen worden gebruikt voor zowel trainingen als implementatie van modellen. Zie [Wat zijn Azure machine learning omgevingen](concept-environments.md)voor meer informatie.

## <a name="register-package-and-deploy-models-from-anywhere"></a>Modellen vanaf elke locatie registreren, inpakken en implementeren

### <a name="register-and-track-ml-models"></a>Modellen voor MILLILITERs registreren en volgen

Met model registratie kunt u uw modellen in de Azure-Cloud opslaan en versie, in uw werk ruimte. Het model register maakt het eenvoudig om uw getrainde modellen te organiseren en bij te houden.

> [!TIP]
> Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in uw Azure Machine Learning-werk ruimte. Na de registratie kunt u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.

Geregistreerde modellen worden geïdentificeerd met naam en versie. Telkens wanneer u een model registreert met dezelfde naam als een bestaand model, wordt de versie in het register verhoogd. Er kunnen extra meta gegevenslabels worden gegeven tijdens de registratie. Deze tags worden vervolgens gebruikt bij het zoeken naar een model. Azure Machine Learning ondersteunt elk model dat kan worden geladen met python 3.5.2 of hoger.

> [!TIP]
> U kunt ook modellen die zijn getraind buiten Azure Machine Learning registreren.

U kunt een geregistreerd model dat wordt gebruikt in een actieve implementatie, niet verwijderen.
Zie de sectie model registreren van [modellen implementeren](how-to-deploy-and-where.md#registermodel)voor meer informatie.

> [!IMPORTANT]
> Bij gebruik van `Tags` de optie filteren op de pagina modellen van Azure machine learning Studio in plaats van gebruik te maken van `TagName : TagValue` klanten `TagName=TagValue` (zonder ruimte)

### <a name="profile-models"></a>Profielmodellen

Azure Machine Learning kunt u meer inzicht krijgen in de vereisten voor de CPU en het geheugen van de service die wordt gemaakt wanneer u uw model implementeert. Profile ring test de service die uw model uitvoert en retourneert informatie zoals het CPU-gebruik, het geheugen gebruik en de reactie latentie. Het biedt ook een aanbeveling van CPU en geheugen op basis van het resource gebruik.
Zie de sectie Profiler van [modellen implementeren](how-to-deploy-profile-model.md)voor meer informatie.

### <a name="package-and-debug-models"></a>Pakket-en fout opsporings modellen

Voordat u een model in productie implementeert, wordt het verpakt in een docker-installatie kopie. In de meeste gevallen gebeurt het maken van afbeeldingen automatisch op de achtergrond tijdens de implementatie. U kunt de installatie kopie hand matig opgeven.

Als u problemen met de implementatie ondervindt, kunt u in uw lokale ontwikkel omgeving implementeren voor probleem oplossing en fout opsporing.

Zie [modellen implementeren](how-to-deploy-and-where.md#registermodel) en [implementaties oplossen](how-to-troubleshoot-deployment.md)voor meer informatie.

### <a name="convert-and-optimize-models"></a>Modellen converteren en optimaliseren

Als u uw model converteert naar [Open Neural Network Exchange](https://onnx.ai) (ONNX), kunnen de prestaties worden verbeterd. Gemiddeld kan het converteren naar ONNX resulteren in een 2x-prestatie verhoging.

Voor meer informatie over ONNX met Azure Machine Learning raadpleegt u het artikel ' [ml-modellen maken en versnellen](concept-onnx.md) '.

### <a name="use-models"></a>Modellen gebruiken

Getrainde machine learning modellen worden geïmplementeerd als webservices in de Cloud of lokaal. U kunt modellen ook implementeren op Azure IoT Edge apparaten. Implementaties gebruiken CPU, GPU of veld-Programmeer bare poort matrices (FPGA) voor het afvoeren. U kunt ook modellen van Power BI gebruiken.

Wanneer u een model als een webservice of IoT Edge-apparaat gebruikt, geeft u de volgende items op:

* De modellen die worden gebruikt voor het bepalen van de gegevens die naar de service/het apparaat worden verzonden.
* Een invoerscript. Dit script accepteert aanvragen, maakt gebruik van de model (en) om de gegevens te beoordelen en retourneert een antwoord.
* Een Azure Machine Learning omgeving waarin de PIP-en Conda-afhankelijkheden worden beschreven die vereist zijn voor de model (len) en het vermeldings script.
* Alle extra assets, zoals tekst, gegevens, enzovoort, die vereist zijn voor de model (len) en het vermeldings script.

U geeft ook de configuratie van het doelimplementatieplatform op. Bijvoorbeeld, het type van de VM-familie, het beschik bare geheugen en het aantal kernen bij het implementeren naar Azure Kubernetes service.

Wanneer de installatiekopie is gemaakt, worden ook de onderdelen toegevoegd die worden vereist door Azure Machine Learning. Bijvoorbeeld assets die nodig zijn om de webservice uit te voeren en te communiceren met IoT Edge.

#### <a name="batch-scoring"></a>Batchscore
Batch scores worden ondersteund via ML-pijp lijnen. Zie [batch voorspellingen op Big Data](./tutorial-pipeline-batch-scoring-classification.md)voor meer informatie.

#### <a name="real-time-web-services"></a>Real-time webservices

U kunt uw modellen in **Web Services** gebruiken met de volgende Compute-doelen:

* Azure Container Instance
* Azure Kubernetes Service
* Lokale ontwikkel omgeving

Als u het model als een webservice wilt implementeren, moet u de volgende items opgeven:

* Het model of de ensemble van modellen.
* Afhankelijkheden die zijn vereist voor het gebruik van het model. Bijvoorbeeld een script dat aanvragen accepteert en het model, Conda afhankelijkheden, enzovoort aanroept.
* Implementatie configuratie die beschrijft hoe en waar het model moet worden geïmplementeerd.

Zie [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie.

#### <a name="controlled-rollout"></a>Gecontroleerde implementatie

Wanneer u implementeert in azure Kubernetes service, kunt u een gecontroleerde implementatie gebruiken om de volgende scenario's in te scha kelen:

* Meerdere versies van een eind punt maken voor een implementatie
* Voer een/B-test uit door verkeer naar verschillende versies van het eind punt te routeren.
* Scha kelen tussen eindpunt versies door het verkeers percentage in de eindpunt configuratie bij te werken.

Zie [gecontroleerde implementatie van ml modellen](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)voor meer informatie.

#### <a name="iot-edge-devices"></a>Apparaten IoT Edge

U kunt modellen met IoT-apparaten gebruiken via **Azure IOT Edge-modules**. IoT Edge-modules worden op het apparaat geïmplementeerd op een hardwareapparaat, waarmee de functie voor inschakeling of het model kan worden Gescored.

Zie [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie.

### <a name="analytics"></a>Analyse

Micro soft Power BI ondersteunt het gebruik van machine learning modellen voor gegevens analyse. Zie [Azure machine learning integration in Power bi (preview)](/power-bi/service-machine-learning-integration)voor meer informatie.

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Vastleggen van de governance-gegevens die nodig zijn voor het vastleggen van de end-to-end ML levenscyclus

Azure ML biedt u de mogelijkheid om de end-to-end-audittrail van al uw ML-assets bij te houden met behulp van meta gegevens.

- Azure ML [integreert met git](how-to-set-up-training-targets.md#gitintegration) om informatie bij te houden over de opslag plaats/vertakking/het door voeren van uw code.
- Met de [Azure ml](how-to-create-register-datasets.md) -gegevens sets kunt u gegevens bijhouden, profiel en versie.
- Door te [interpreteren](how-to-machine-learning-interpretability.md) kunt u uw modellen uitleggen, voldoen aan de naleving van regelgeving en inzicht krijgen in de manier waarop modellen aan een resultaat van een gegeven invoer komen.
- In de geschiedenis van Azure ML run wordt een moment opname van de code, gegevens en berekeningen opgeslagen waarmee een model wordt getraind.
- In het REGI ster van Azure ML-model worden alle meta gegevens vastgelegd die aan uw model zijn gekoppeld (waarbij experiment wordt getraind, waar het wordt geïmplementeerd als de implementaties in orde zijn).
- Dankzij de [integratie met Azure](how-to-use-event-grid.md) kunt u op gebeurtenissen reageren in de levens cyclus van ml. Bijvoorbeeld model registratie, implementatie, gegevens drift en training (run)-gebeurtenissen.

> [!TIP]
> Hoewel er automatisch een aantal informatie over modellen en gegevens sets wordt vastgelegd, kunt u extra informatie toevoegen met behulp van __Tags__. Wanneer u op zoek bent naar geregistreerde modellen en gegevens sets in uw werk ruimte, kunt u Tags gebruiken als filter.
>
> Een gegevensset koppelen aan een geregistreerd model is een optionele stap. Zie de verwijzing naar [model](/python/api/azureml-core/azureml.core.model%28class%29?preserve-view=true&view=azure-ml-py) klassen voor informatie over het verwijzen naar een gegevensset bij het registreren van een model.


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Meldingen, automatiseren en waarschuwen voor gebeurtenissen in de levens cyclus van ML
Azure ML publiceert belang rijke gebeurtenissen in azure EventGrid, die kunnen worden gebruikt voor het melden en automatiseren van gebeurtenissen in de levens cyclus van ML. Raadpleeg [dit document](how-to-use-event-grid.md)voor meer informatie.


## <a name="monitor-for-operational--ml-issues"></a>Controleren op problemen met operationele & ML

Met bewaking kunt u begrijpen welke gegevens worden verzonden naar uw model en welke voor spellingen worden geretourneerd.

Deze informatie helpt u inzicht te krijgen in het gebruik van uw model. De verzamelde invoer gegevens kunnen ook nuttig zijn bij het trainen van toekomstige versies van het model.

Zie [How to Enable model Data Collection](how-to-enable-data-collection.md)(Engelstalig) voor meer informatie.

## <a name="retrain-your-model-on-new-data"></a>Uw model opnieuw trainen op nieuwe gegevens

Het is vaak verstandig uw model te valideren, het bij te werken of het helemaal opnieuw te trainen, terwijl u nieuwe informatie ontvangt. Soms is het ontvangen van nieuwe gegevens een verwacht deel van het domein. Andere keren, zoals beschreven in [gegevens drift detecteren (preview) in gegevens sets](how-to-monitor-datasets.md), kunnen model prestaties afnemen in het vlak van dergelijke zaken als wijzigingen in een bepaalde sensor, natuurlijke gegevens wijzigingen zoals seizoen effecten of functies die in hun relatie met andere functies verschuivingen. 

Er is geen universeel antwoord op ' Hoe kan ik weet of ik opnieuw moet trainen? ' Azure ML-hulpprogram ma's voor gebeurtenis en controle die eerder zijn besproken, zijn goede start punten voor automatisering. Wanneer u hebt besloten om opnieuw te trainen, moet u het volgende doen: 

- Uw gegevens voorverwerken met een herhaalbaar, geautomatiseerd proces
- Uw nieuwe model trainen
- Vergelijk de uitvoer van uw nieuwe model met die van uw oude model
- Vooraf gedefinieerde criteria gebruiken om te kiezen of u het oude model wilt vervangen 

Een thema van de bovenstaande stappen is dat uw retraining automatisch moet worden geautomatiseerd, niet voor ad-hoc. [Azure machine learning pijp lijnen](concept-ml-pipelines.md) zijn een goed antwoord voor het maken van werk stromen met betrekking tot de voor bereiding van gegevens, training, validatie en implementatie. Lees opnieuw [Train modellen met Azure machine learning Designer](how-to-retrain-designer.md) om te zien hoe pijp lijnen en de Azure machine learning designer in een retrainings scenario passen. 

## <a name="automate-the-ml-lifecycle"></a>De levens cyclus van ML automatiseren 

U kunt GitHub en Azure-pijp lijnen gebruiken om een doorlopend integratie proces te maken dat een model traint. Wanneer een gegevens wetenschapper een wijziging in de Git-opslag plaats voor een project controleert, wordt in een typisch scenario een trainings uitvoering gestart. De resultaten van de uitvoering kunnen vervolgens worden gecontroleerd om de prestatie kenmerken van het getrainde model te bekijken. U kunt ook een pijp lijn maken die het model als een webservice implementeert.

De [uitbrei ding Azure machine learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) maakt het gemakkelijker om te werken met Azure-pijp lijnen. Het biedt de volgende uitbrei dingen voor Azure-pijp lijnen:

* Hiermee schakelt u de werkruimte selectie in bij het definiëren van een service verbinding.
* Maakt het mogelijk om release pijplijnen te activeren door getrainde modellen die zijn gemaakt in een trainings pijplijn.

Raadpleeg de volgende koppelingen voor meer informatie over het gebruik van Azure-pijp lijnen met Azure Machine Learning:

* [Continue integratie en implementatie van ML-modellen met Azure-pijp lijnen](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure machine learning MLOps](https://aka.ms/mlops) -opslag plaats.
* [Azure machine learning MLOpsPython](https://github.com/Microsoft/MLOpspython) -opslag plaats.

U kunt Azure Data Factory ook gebruiken om een pijp lijn voor gegevens opname te maken waarmee gegevens worden voor bereid voor gebruik met training. Zie [pijp lijn voor gegevens opname](how-to-cicd-data-ingestion.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het lezen en verkennen van de volgende resources:

+ [Hoe & implementeren van modellen](how-to-deploy-and-where.md) met Azure machine learning

+ [Zelf studie: een classificatie model voor een installatie kopie implementeren in ACI](tutorial-deploy-models-with-aml.md).

+ [End-to-end MLOps-voor beelden opslag plaats](https://github.com/microsoft/MLOps)

+ [CI/CD van ML modellen met Azure-pijp lijnen](/azure/devops/pipelines/targets/azure-machine-learning)

+ Clients maken die [een geïmplementeerd model gebruiken](how-to-consume-web-service.md)

+ [Machine learning op schaal](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI-referentie architecturen & best practices rep](https://github.com/microsoft/AI)