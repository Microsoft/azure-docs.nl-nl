---
title: 'ML Studio (klassiek): toepassings levenscyclus beheer-Azure'
description: Best practices voor toepassings levenscyclus beheer Toep assen in Azure Machine Learning Studio (klassiek)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 82d6f1fd250621ec34015970e1339ccfbd134675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518399"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Beheer van toepassings levenscyclus in Azure Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Azure Machine Learning Studio (klassiek) is een hulp programma voor het ontwikkelen van machine learning experimenten die operationeel zijn in het Azure-Cloud platform. Het is vergelijkbaar met de Visual Studio IDE en de schaal bare Cloud service die is samengevoegd met één platform. U kunt standaard ALM-procedures (Application Lifecycle Management) opnemen van het versie beheer van verschillende assets tot geautomatiseerde uitvoering en implementatie, in Azure Machine Learning Studio (klassiek). In dit artikel worden enkele van de opties en benaderingen besproken.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Experimenteren met versie beheer
Er zijn twee aanbevolen manieren voor het versie nummer van uw experimenten. U kunt vertrouwen op de ingebouwde uitvoerings geschiedenis of het experiment exporteren in een JSON-indeling, zodat het extern kan worden beheerd. Elke benadering wordt geleverd met zijn voor-en nadelen.

### <a name="experiment-snapshots-using-run-history"></a>Moment opnamen experimenteren met de uitvoerings geschiedenis
In het uitvoerings model van het Learning-experiment van Azure Machine Learning Studio (klassiek) wordt een onveranderlijke moment opname van het experiment verzonden naar de job scheduler wanneer u op **uitvoeren** klikt in de experiment editor. Als u deze lijst met moment opnamen wilt weer geven, klikt u op de opdracht balk in de weer gave experiment editor op **geschiedenis uitvoeren** .

![Knop Geschiedenis uitvoeren](./media/version-control/runhistory.png)

U kunt de moment opname vervolgens openen in de vergrendelde modus door te klikken op de naam van het experiment op het moment dat het experiment werd verzonden om te worden uitgevoerd en de moment opname is gemaakt. U ziet dat alleen het eerste item in de lijst, dat het huidige experiment vertegenwoordigt, zich in een Bewerk bare staat bevindt. U ziet ook dat elke moment opname in verschillende statussen kan worden opgenomen, zoals voltooide (gedeeltelijke uitvoering), mislukt, mislukt (gedeeltelijke uitvoering) of concept.

![Lijst met uitvoerings geschiedenis](./media/version-control/runhistorylist.png)

Nadat de app is geopend, kunt u het momentopname experiment opslaan als een nieuw experiment en dit vervolgens wijzigen. Als uw proef momentopname assets bevat, zoals getrainde modellen, trans formaties of gegevens sets die bijgewerkte versies hebben, behoudt de moment opname de verwijzingen naar de oorspronkelijke versie wanneer de moment opname werd gemaakt. Als u de vergrendelde moment opname als een nieuw experiment opslaat, detecteert Azure Machine Learning Studio (klassiek) het bestaan van een nieuwere versie van deze assets en worden ze in het nieuwe experiment automatisch bijgewerkt.

Als u het experiment verwijdert, worden alle moment opnamen van dat experiment verwijderd.

### <a name="exportimport-experiment-in-json-format"></a>Experiment exporteren/importeren in JSON-indeling
De moment opnamen van de uitvoerings geschiedenis blijven een onveranderbare versie van het experiment in Azure Machine Learning Studio (klassiek) elke keer dat deze wordt verzonden om uit te voeren. U kunt ook een lokale kopie van het experiment opslaan en deze controleren in uw favoriete bron beheersysteem, zoals Team Foundation Server, en later opnieuw een experiment maken op basis van het lokale bestand. U kunt de [Azure machine learning Power shell](https://aka.ms/amlps) Commandlets [*export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) en [*import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) gebruiken om dat te bereiken.

Het JSON-bestand is een tekst weergave van de experimentele grafiek, die mogelijk een verwijzing bevat naar assets in de werk ruimte, zoals een gegevensset of een getraind model. Het bevat geen geserialiseerde versie van de Asset. Als u het JSON-document weer in de werk ruimte probeert te importeren, moeten de activa waarnaar wordt verwezen al bestaan met dezelfde activa-Id's waarnaar in het experiment wordt verwezen. Anders hebt u geen toegang tot het geïmporteerde experiment.

## <a name="versioning-trained-model"></a>Getraind model voor versie beheer
Een getraind model in Azure Machine Learning Studio (klassiek) wordt geserialiseerd in een indeling die wordt aangeduid als een iLearner-bestand ( `.iLearner` ) en wordt opgeslagen in het Azure Blob Storage-account dat is gekoppeld aan de werk ruimte. Een manier om een kopie van het iLearner-bestand op te halen, is via de retraining-API. In [dit artikel](./retrain-machine-learning-model.md) wordt uitgelegd hoe de retraining-API werkt. De stappen op hoog niveau:

1. Stel uw trainings experiment in.
2. Voeg een webservice-uitvoer poort toe aan de module Train model of de module die het getrainde model produceert, zoals model afstemming afstemmen of R-model maken.
3. Voer uw trainings experiment uit en implementeer dit als een model training-webservice.
4. Roep het BES-eind punt van de training-webservice aan en geef de gewenste locatie van de iLearner-bestands naam en het Blob-opslag account op waar de service wordt opgeslagen.
5. Oogst het geproduceerde iLearner-bestand nadat de BES-aanroep is voltooid.

Een andere manier om het iLearner-bestand op te halen, is via de Power shell [*-Commandlet down load-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Dit kan handig zijn als u alleen een kopie wilt ophalen van het iLearner-bestand zonder dat u het model programmatisch opnieuw moet trainen.

Nadat u het iLearner-bestand met het getrainde model hebt, kunt u vervolgens uw eigen versie beheer strategie gebruiken. De strategie kan net zo eenvoudig zijn als het Toep assen van een pre-achtervoegsel als een naamgevings Conventie en het iLearner-bestand in Blob Storage te verlaten, of door het te kopiëren/importeren in uw versie beheersysteem.

Het opgeslagen iLearner-bestand kan vervolgens worden gebruikt voor het scoren via geïmplementeerde webservices.

## <a name="versioning-web-service"></a>Webservice voor versie beheer
U kunt twee soorten webservices implementeren vanuit een Azure Machine Learning Studio (klassiek)-experiment. De klassieke webservice is nauw gekoppeld aan het experiment en de werk ruimte. De nieuwe webservice gebruikt het Azure Resource Manager Framework en is niet meer gekoppeld aan het oorspronkelijke experiment of de werk ruimte.

### <a name="classic-web-service"></a>Klassieke webservice
Als u een klassieke webservice wilt maken, kunt u profiteren van de web service-eindpunt constructie. Hier volgt een typische stroom:

1. Vanuit uw voorspellende experiment implementeert u een nieuwe klassieke webservice, die een standaard eindpunt bevat.
2. U maakt een nieuw eind punt met de naam EP2, waarmee de huidige versie van het model voor experiment/getraind wordt weer gegeven.
3. U gaat terug en uw voorspellende experiment en getrainde model bijwerken.
4. U implementeert het voorspellende experiment, waarna het standaard eindpunt wordt bijgewerkt. Maar dit kan EP2 niet wijzigen.
5. U maakt een extra eind punt met de naam eP3, dat de nieuwe versie van het experiment en het getrainde model beschrijft.
6. Ga terug naar stap 3, indien nodig.

In de loop van de tijd zijn er mogelijk veel eind punten gemaakt in dezelfde webservice. Elk eind punt vertegenwoordigt een tijdgebonden kopie van het experiment dat de tijdgebonden versie van het getrainde model bevat. U kunt vervolgens externe logica gebruiken om te bepalen welk eind punt moet worden aangeroepen. Dit betekent effectief dat u een versie selecteert van het getrainde model voor de uitvoering van het score.

U kunt ook veel identieke webservice-eind punten maken en vervolgens verschillende versies van het iLearner-bestand naar het eind punt patchen om een vergelijkbaar effect te krijgen. In [dit artikel](create-models-and-endpoints-with-powershell.md) vindt u meer informatie over hoe u dit kunt doen.

### <a name="new-web-service"></a>Nieuwe webservice
Als u een nieuwe webservice op basis van Azure Resource Manager maakt, is de eindpunt constructie niet meer beschikbaar. In plaats daarvan kunt u WSD-bestanden (web service Definition) in JSON-indeling genereren vanuit uw voorspellende experiment met behulp van de Power shell [-Commandlet export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) of met behulp van de Power shell-commandlet [*export-AzMlWebservice*](/powershell/module/az.machinelearning/export-azmlwebservice) van een geïmplementeerde web service van Resource Manager.

Nadat u het WSD-bestand en de versie beheer ervan hebt geëxporteerd, kunt u de WSD ook als een nieuwe webservice implementeren in een ander abonnement in een andere Azure-regio. Zorg ervoor dat u de juiste configuratie voor het opslag account en de nieuwe abonnement-ID van de webservice opgeeft. Als u een patch in verschillende iLearner-bestanden wilt uitvoeren, kunt u het WSD-bestand wijzigen en de locatie verwijzing van het getrainde model bijwerken en implementeren als een nieuwe webservice.

## <a name="automate-experiment-execution-and-deployment"></a>Experimenteel uitvoeren en implementeren automatiseren
Een belang rijk aspect van ALM is om het uitvoerings-en implementatie proces van de toepassing te automatiseren. In Azure Machine Learning Studio (klassiek) kunt u dit doen met behulp van de [Power shell-module](https://aka.ms/amlps). Hier volgt een voor beeld van end-to-end-stappen die relevant zijn voor een standaard ALM voor automatische uitvoering/implementatie met behulp van de [Power shell-module Azure machine learning Studio (klassiek)](https://aka.ms/amlps). Elke stap is gekoppeld aan een of meer Power shell-Commandlets die u kunt gebruiken om deze stap uit te voeren.

1. [Upload een gegevensset](https://github.com/hning86/azuremlps#upload-amldataset).
2. Kopieer een Oefen experiment naar de werk ruimte vanuit een [werk ruimte](https://github.com/hning86/azuremlps#copy-amlexperiment) of vanuit de [Galerie](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)of [Importeer](https://github.com/hning86/azuremlps#import-amlexperimentgraph) een [geëxporteerd](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experiment vanaf een lokale schijf.
3. [Werk de gegevensset](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) bij in het trainings experiment.
4. [Voer het trainings experiment uit](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Promoot het getrainde model](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Kopieer een voorspellend experiment](https://github.com/hning86/azuremlps#copy-amlexperiment) naar de werk ruimte.
7. [Werk het getrainde model](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) in het voorspellende experiment bij.
8. [Voer het voorspellende experiment uit](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Implementeer een webservice](https://github.com/hning86/azuremlps#new-amlwebservice) van het voorspellende experiment.
10. Test de [bron records](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) van de webservice of het [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) -eind punt.

## <a name="next-steps"></a>Volgende stappen
* Down load de [Azure machine learning Studio (klassieke) Power shell](https://aka.ms/amlps) -module en start uw ALM-taken.
* Meer informatie over hoe u [een groot aantal ml modellen maakt en beheert met behulp van een enkel experiment](create-models-and-endpoints-with-powershell.md) via Power shell en retraining-API.
* Meer informatie over het [implementeren van Azure machine learning-webservices](deploy-a-machine-learning-web-service.md).