---
title: Over Azure Machine Learning omgevingen
titleSuffix: Azure Machine Learning
description: Meer informatie over machine learning-omgevingen, waarmee u reproduceer bare, bewerkbaar, & Portable machine learning-afhankelijkheids definities voor verschillende reken doelen kunt inschakelen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 11/16/2020
ms.openlocfilehash: 648dbe6b8d275c832f219cb6f3119ac0bc518a54
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508466"
---
# <a name="what-are-azure-machine-learning-environments"></a>Wat zijn Azure Machine Learning omgevingen?

Azure Machine Learning omgevingen zijn een inkapseling van de omgeving waarin uw machine learning-training zich voordoet. Hiermee worden de Python-pakketten, omgevings variabelen en software-instellingen voor uw trainings-en Score scripts opgegeven. Ze specificeren ook uitvoerings tijden (python, Spark of docker). De omgevingen zijn beheerde en geversiede entiteiten in uw Machine Learning-werk ruimte waarmee u reproduceer bare, audit bare en Portable machine learning werk stromen kunt maken in verschillende reken doelen.

U kunt een `Environment` object op uw lokale Compute gebruiken om het volgende te doen:
* Ontwikkel uw trainings script.
* Gebruik dezelfde omgeving op Azure Machine Learning Compute for model-training op schaal.
* Implementeer uw model met diezelfde omgeving.
* Ga naar de omgeving waarin een bestaand model is getraind.

In het volgende diagram ziet u hoe u één enkel object kunt gebruiken `Environment` in de configuratie van de uitvoering (voor training) en uw detrain-en implementatie configuratie (voor web service-implementaties).

![Diagram van een omgeving in machine learning werk stroom](./media/concept-environments/ml-environment.png)

De omgeving, de reken doel-en trainings script vormen samen de configuratie van de uitvoering: de volledige specificatie van een trainings uitvoering.

## <a name="types-of-environments"></a>Typen omgevingen

Omgevingen kunnen breed worden onderverdeeld in drie categorieën: met *curator*, door de *gebruiker beheerd* en door *het systeem beheerd*.

Gehoste omgevingen worden verzorgd door Azure Machine Learning en zijn standaard beschikbaar in uw werk ruimte. Deze zijn bedoeld om te worden gebruikt als zodanig dat ze verzamelingen van Python-pakketten en-instellingen bevatten die u helpen aan de slag te gaan met diverse machine learning frameworks. Deze vooraf gemaakte omgevingen bieden ook een snellere implementatie tijd. Zie het artikel over de gedocumenteerde [omgevingen](resource-curated-environments.md)voor een volledige lijst.

In door gebruikers beheerde omgevingen bent u verantwoordelijk voor het instellen van uw omgeving en het installeren van elk pakket dat uw trainings script nodig heeft voor het Compute-doel. Conda controleert uw omgeving niet en installeert niets voor u. Als u uw eigen omgeving definieert, moet u de `azureml-defaults` versie `>= 1.0.45` als een PIP-afhankelijkheid vermelden. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten.

U gebruikt door het systeem beheerde omgevingen als u wilt dat [Conda](https://conda.io/docs/) de python-omgeving en de script afhankelijkheden voor u beheert. Een nieuwe Conda-omgeving is gebouwd op basis van het Conda-afhankelijkheids object. De Azure Machine Learning-service veronderstelt dit type omgeving standaard, vanwege de bruikbaarheid op externe Compute-doelen die niet hand matig kunnen worden geconfigureerd.

## <a name="create-and-manage-environments"></a>Omgevingen maken en beheren

U kunt omgevingen maken op:

* Het definiëren `Environment` van nieuwe objecten, hetzij door gebruik te maken van een gecuratore omgeving of door uw eigen afhankelijkheden te definiëren.
* Bestaande `Environment` objecten uit uw werk ruimte gebruiken. Deze benadering maakt consistentie en reproduceer baarheid mogelijk met uw afhankelijkheden.
* Importeren uit een bestaande Anaconda-omgevings definitie.
* De Azure Machine Learning CLI gebruiken
* [De VS code-extensie gebruiken](how-to-manage-resources-vscode.md#create-environment)

Zie voor voor beelden van specifieke code de sectie ' een omgeving maken ' van het [gebruik van omgevingen](how-to-use-environments.md#create-an-environment). Omgevingen worden ook eenvoudig beheerd via uw werk ruimte. Ze bevatten de volgende functionaliteit:

* Omgevingen worden automatisch bij uw werk ruimte geregistreerd wanneer u een experiment verzendt. Ze kunnen ook hand matig worden geregistreerd.
* U kunt in uw werk ruimte omgevingen ophalen die u wilt gebruiken voor training of implementatie, of om wijzigingen aan te brengen in de omgevings definitie.
* Met versie beheer kunt u in de loop der tijd wijzigingen in uw omgevingen zien, waardoor de reproduceer baarheid gewaarborgd is.
* U kunt docker-installatie kopieën automatisch bouwen vanuit uw omgevingen.

Voor code voorbeelden, zie de sectie omgevingen beheren van het [gebruik van omgevingen](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Omgeving bouwen, in cache plaatsen en opnieuw gebruiken

De Azure Machine Learning-service bouwt omgevings definities in docker-installatie kopieën en Conda-omgevingen. Ook worden de omgevingen in de cache opgeslagen, zodat ze opnieuw kunnen worden gebruikt in volgende trainings uitvoeringen en implementaties van service-eind punten. Als u een trainings script op afstand uitvoert, moet u een docker-installatie kopie maken, terwijl een lokale uitvoering rechtstreeks een Conda-omgeving kan gebruiken. 

### <a name="submitting-a-run-using-an-environment"></a>Een run verzenden met een omgeving

Wanneer u een externe uitvoering voor het eerst met een omgeving verzendt, wordt door de Azure Machine Learning-service een [ACR-build-taak](../container-registry/container-registry-tasks-overview.md) aangeroepen in de Azure container Registry (ACR) die aan de werk ruimte is gekoppeld. De ingebouwde docker-installatie kopie wordt vervolgens in de cache opgeslagen in de werk ruimte ACR. Met een hoofd omgeving worden er back-ups gemaakt van docker-installatie kopieën die in de cache zijn opgeslagen in de globale ACR. Aan het begin van het uitvoeren van de uitvoering wordt de installatie kopie opgehaald door het berekenings doel van de relevante ACR.

Voor lokale uitvoeringen wordt een docker-of Conda-omgeving gemaakt op basis van de omgevings definitie. De scripts worden vervolgens uitgevoerd op de doel computer, een lokale runtime-omgeving of een lokale docker-engine.

### <a name="building-environments-as-docker-images"></a>Omgevingen bouwen als docker-installatie kopieën

Als de omgevings definitie nog niet bestaat in de werk ruimte ACR, wordt er een nieuwe installatie kopie gemaakt. De afbeeldings opbouw bestaat uit twee stappen:

 1. Een basis installatie kopie downloaden en alle docker-stappen uitvoeren
 2. Het bouwen van een Conda-omgeving op basis van Conda-afhankelijkheden die zijn opgegeven in de omgevings definitie.

De tweede stap wordt wegge laten als u door de [gebruiker beheerde afhankelijkheden](/python/api/azureml-core/azureml.core.environment.pythonsection)opgeeft. In dit geval bent u verantwoordelijk voor het installeren van Python-pakketten door ze op te nemen in uw basis installatie kopie of door aangepaste docker-stappen in de eerste stap op te geven. U bent ook verantwoordelijk voor het opgeven van de juiste locatie voor het uitvoer bare python-bestand. Het is ook mogelijk om een [aangepaste docker-basis installatie kopie](how-to-deploy-custom-docker-image.md)te gebruiken.

### <a name="image-caching-and-reuse"></a>Afbeeldingen in cache opslaan en opnieuw gebruiken

Als u dezelfde omgevings definitie gebruikt voor een andere uitvoering, gebruikt de Azure Machine Learning-service de in de cache opgeslagen afbeelding van de werk ruimte ACR. 

Gebruik [environment.get_image_details](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-) methode om de details van een afbeelding in de cache weer te geven.

Als u wilt bepalen of u een installatie kopie in cache opnieuw wilt gebruiken of een nieuwe afbeelding wilt maken, berekent de service [een hashwaarde](https://en.wikipedia.org/wiki/Hash_table) van de omgevings definitie en vergelijkt deze met de hashes van bestaande omgevingen. De hash is gebaseerd op:
 
 * Eigenschaps waarde basis afbeelding
 * Eigenschaps waarde voor aangepaste docker-stappen
 * Lijst met Python-pakketten in de Conda-definitie
 * Lijst met pakketten in Spark-definitie 

De hash is niet afhankelijk van de naam of versie van de omgeving: als u de naam van uw omgeving wijzigt of een nieuwe omgeving maakt met de exacte eigenschappen en pakketten van een bestaande, blijft de hash-waarde gelijk. Wijzigingen in de omgevings definities, zoals het toevoegen of verwijderen van een python-pakket of het wijzigen van de pakket versie, zorgen er echter voor dat de hash-waarde wordt gewijzigd. Het wijzigen van de volg orde van afhankelijkheden of kanalen in een omgeving resulteert in een nieuwe omgeving en daarom moet er een nieuwe installatie kopie worden gemaakt. Het is belang rijk te weten dat elke wijziging in een gecuratore omgeving de hash ongeldig maakt en een nieuwe ' niet-nagemaakte ' omgeving heeft.

De berekende hashwaarde wordt vergeleken met de waarden in de werk ruimte en de globale ACR (of op het reken doel voor lokale uitvoeringen). Als er een overeenkomst is, wordt de in de cache opgeslagen afbeelding opgehaald, anders wordt een installatie kopie-build geactiveerd. De duur van het ophalen van een in cache opgeslagen afbeelding bevat de download tijd, terwijl de duur voor het ophalen van een nieuw gebouwde afbeelding de build-tijd en de download tijd bevat. 

In het volgende diagram ziet u drie omgevings definities. Er zijn twee verschillende namen en versies, maar identieke basis installatie kopieën en Python-pakketten. Maar ze hebben dezelfde hash en komen dus overeen met dezelfde afbeelding in de cache. De derde omgeving heeft andere Python-pakketten en-versies en komt daarom overeen met een andere kopie in de cache.

![Diagram van het opslaan van omgevingen in de cache als docker-installatie kopieën](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Als u een omgeving met een niet-vastgemaakte pakket afhankelijkheid maakt, blijft ```numpy``` die omgeving de pakket versie gebruiken die is geïnstalleerd _op het moment dat de omgeving wordt gemaakt_. Daarnaast blijft de oude versie van alle toekomstige omgevingen met de overeenkomende definitie bewaard. 

Als u het pakket wilt bijwerken, moet u een versie nummer opgeven om het opnieuw samen stellen van de installatie kopie af te dwingen ```numpy==1.18.1``` . Er worden nieuwe afhankelijkheden, inclusief geneste items, geïnstalleerd die een eerder werkend scenario kunnen verstoren. 

> [!WARNING]
>  Met de methode [environment. build](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) wordt de installatie kopie in de cache opnieuw opgebouwd, met mogelijk neven effect van het bijwerken van losgemaakte pakketten en het verbreken van de reproduceer baarheid voor alle omgevings definities die overeenkomen met die in de cache opgeslagen afbeelding.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken en gebruiken van omgevingen](how-to-use-environments.md) in azure machine learning.
* Raadpleeg de documentatie van python SDK voor de [omgevings klasse](/python/api/azureml-core/azureml.core.environment%28class%29).
* Raadpleeg de naslag documentatie over R SDK voor [omgevingen](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).