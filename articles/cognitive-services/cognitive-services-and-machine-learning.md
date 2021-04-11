---
title: Cognitive Services en Machine Learning
titleSuffix: Azure Cognitive Services
description: Ontdek hoe Azure Cognitive Services past bij andere Azure-aanbiedingen voor machine learning.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: 7946bc23f766eaf99860fa764ee7b542036b5109
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075777"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services en machine learning

Cognitive Services biedt machine learning mogelijkheden om algemene problemen op te lossen, zoals het analyseren van tekst voor emotioneel sentiment of het analyseren van installatie kopieën om objecten of gezichten te herkennen. U hebt geen speciale machine learning- of data science-kennis nodig om deze services te kunnen gebruiken. 

[Cognitive Services](./what-are-cognitive-services.md) is een groep services die elk verschillende gegeneraliseerde Voorspellings mogelijkheden ondersteunt. De services zijn onderverdeeld in verschillende categorieën om u te helpen de juiste service te vinden. 

|Service categorie|Doel|
|--|--|
|[Besluit](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Bouw apps die aanbevelingen opleveren voor geïnformeerde en efficiënte besluitvorming.|
|[Taal](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Laat uw apps natuurlijke taal verwerken met vooraf gemaakte scripts, sentiment evalueren en leren herkennen wat gebruikers willen.|
|[Zoeken](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Voeg Bing Search-API's toe aan uw apps en benut de mogelijkheid om miljarden webpagina's, afbeeldingen, video's en nieuwsitems met één API-aanroep te doorzoeken.|
|[Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Converteer spraak naar tekst en tekst naar spraak met natuurlijke uitspraak. Vertaal teksten van de ene naar de andere taal en schakel sprekercontrole en -herkenning in.|
|[Vision](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Herken, identificeer, indexeer en wijzig uw afbeeldingen, video's en digitale inkt en voorzie deze van ondertitels.|

Gebruik Cognitive Services wanneer u:

* Kan gebruikmaken van een algemene oplossing.
* Toegang tot de oplossing vanuit een programmeer REST API of-SDK. 

Gebruik een andere oplossing voor machine learning wanneer u:

* Het algoritme moet worden gekozen en moet worden getraind op zeer specifieke gegevens.

## <a name="what-is-machine-learning"></a>Wat is machine learning?

Machine learning is een concept waarbij u gegevens en een algoritme samenvoegt om een specifieke behoefte op te lossen. Zodra de gegevens en het algoritme zijn getraind, is de uitvoer een model dat u opnieuw kunt gebruiken met andere gegevens. Het getrainde model bevat inzichten op basis van de nieuwe gegevens. 

Het proces van het bouwen van een machine learning systeem vereist enige kennis van machine learning of Data Science.

Machine learning wordt gegeven met behulp van [Azure machine learning (AML)-producten en-services](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure%2fmachine-learning%2fstudio%2fcontext%2fml-context).

## <a name="what-is-a-cognitive-service"></a>Wat is een cognitieve service?

Een cognitieve service biedt deel of alle onderdelen in een machine learning oplossing: gegevens, algoritme en getraind model. Deze services zijn bedoeld om algemene kennis van uw gegevens te vereisen zonder dat u ervaring hoeft te hebben met machine learning of gegevens wetenschap. Deze services bieden zowel REST API (s) als Sdk's op basis van taal. Als gevolg hiervan moet u kennis van programmeer talen hebben om de services te kunnen gebruiken.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Hoe worden Cognitive Services en Azure Machine Learning (AML) vergelijkbaar?

Beide hebben de eind doelen van het Toep assen van kunst matige intelligentie (AI) voor het verbeteren van bedrijfs activiteiten, maar hoe elk deze in de respectieve aanbiedingen levert, is anders. 

Over het algemeen zijn de doel groepen verschillend:

* Cognitive Services zijn voor ontwikkel aars zonder machine learning-ervaring.
* Azure Machine Learning is afgestemd op gegevens wetenschappers. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Hoe wijkt een cognitieve service af van machine learning?

Een cognitieve service biedt een getraind model voor u. Dit brengt gegevens en een algoritme samen die beschikbaar zijn via een REST API (s) of SDK. U kunt deze service binnen enkele minuten implementeren, afhankelijk van uw scenario.  Een cognitieve service biedt antwoorden op algemene problemen, zoals sleutel zinnen in tekst-of item identificatie in afbeeldingen. 

Machine learning is een proces dat doorgaans een langere periode nodig heeft om te implementeren. Deze tijd wordt besteed aan het verzamelen van gegevens, het opschonen, transformeren, het selecteren van algoritmen, model trainingen en implementaties om te profiteren van hetzelfde functionaliteits niveau dat wordt verschaft door een cognitieve service. Met machine learning kunt u antwoorden geven op zeer gespecialiseerde en/of specifieke problemen. Voor machine learning-problemen moet u vertrouwd zijn met het specifieke onderwerp en de gegevens van het probleem dat wordt onderzocht, evenals de expertise van de gegevens wetenschap.

## <a name="what-kind-of-data-do-you-have"></a>Wat voor soort gegevens hebt u?

Cognitive Services kan, als een groep services, geen, sommige of alle aangepaste gegevens voor het getrainde model vereisen. 

### <a name="no-additional-training-data-required"></a>Geen extra trainings gegevens vereist

Services die een volledig getraind model bieden, kunnen worden behandeld als een _dekkend vak_. U hoeft niet te weten hoe ze werken of welke gegevens er zijn gebruikt om ze te trainen. U brengt uw gegevens naar een volledig getraind model om een voor spelling te krijgen. 

### <a name="some-or-all-training-data-required"></a>Sommige of alle trainings gegevens zijn vereist

Met sommige services kunt u uw eigen gegevens meenemen en vervolgens een model trainen. Hierdoor kunt u het model uitbreiden met behulp van de gegevens en het algoritme van de service met uw eigen gegevens. De uitvoer komt overeen met uw behoeften. Wanneer u uw eigen gegevens meebrengt, moet u mogelijk de gegevens labelen op een manier die specifiek is voor de service. Als u bijvoorbeeld een model traint om bloemen aan te duiden, kunt u een catalogus van bloem afbeeldingen samen met de locatie van de bloem in elke afbeelding opgeven om het model te trainen. 

Met een service _kunt u gegevens_ leveren om de eigen gegevens te verbeteren. Voor een service _moet_ u mogelijk gegevens opgeven. 

### <a name="real-time-or-near-real-time-data-required"></a>Realtime of bijna realtime gegevens vereist

Een service heeft mogelijk realtime of bijna realtime gegevens nodig om een effectief model te bouwen. Deze services verwerken aanzienlijke hoeveel heden model gegevens. 

## <a name="service-requirements-for-the-data-model"></a>Service vereisten voor het gegevens model

In de volgende gegevens wordt elke service gecategoriseerd op basis van het type gegevens dat is toegestaan of vereist.

|Cognitieve service|Geen trainings gegevens vereist|U geeft enkele of alle trainings gegevens op|Real-time of bijna realtime gegevens verzameling|
|--|--|--|--|
|[Anomaliedetectie](./Anomaly-Detector/overview.md)|x|x|x|
|Bing Search |x|||
|[Computer Vision](./computer-vision/overview.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./custom-vision-service/overview.md)||x||
|[Face](./Face/Overview.md)|x|x||
|[Form Recognizer](./form-recognizer/overview.md)||x||
|[Immersive Reader](./immersive-reader/overview.md)|x|||
|[Ink Recognizer](/previous-versions/azure/cognitive-services/Ink-Recognizer/overview)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizer](./personalizer/what-is-personalizer.md)|BxDxH|BxDxH|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Spreker-herkenning](./speech-service/speaker-recognition-overview.md)||x||
|[Spraak tekst naar spraak (TTS)](speech-service/text-to-speech.md)|x|x||
|[Spraak-naar-tekst (STT)](speech-service/speech-to-text.md)|x|x||
|[Speech Translation](speech-service/speech-translation.md)|x|||
|[Tekstanalyse](./text-analytics/overview.md)|x|||
|[Translator](./translator/translator-info-overview.md)|x|||
|[Translator-aangepaste vertaler](./translator/custom-translator/overview.md)||x||

* Personaler vereist alleen trainings gegevens die door de service worden verzameld (zoals deze in realtime actief zijn) om uw beleid en gegevens te evalueren. Personaler heeft geen grote historische gegevens sets nodig voor de voor-en hands-training. 

## <a name="where-can-you-use-cognitive-services"></a>Waar kunt u Cognitive Services gebruiken?
 
De services worden gebruikt in een toepassing die REST API (en) of SDK-aanroepen kan maken. Voor beelden van toepassingen zijn websites, bots, virtuele of gemengde realiteit, desktop-en mobiele toepassingen. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Hoe zijn Azure Cognitive Search gerelateerd aan Cognitive Services?

[Azure Cognitive Search](../search/search-what-is-azure-search.md) is een afzonderlijke service voor het zoeken in de cloud die optioneel Cognitive Services gebruikt voor het toevoegen van een installatie kopie en natuurlijke taal verwerking voor het indexeren van werk belastingen. Cognitive Services wordt weer gegeven in azure Cognitive Search via [ingebouwde vaardig heden](../search/cognitive-search-predefined-skills.md) waarmee afzonderlijke api's worden verpakt. U kunt een gratis resource gebruiken voor scenario's, maar plan het maken en koppelen van een [factureer bare resource](../search/cognitive-search-attach-cognitive-services.md) voor grotere volumes.

## <a name="how-can-you-use-cognitive-services"></a>Hoe kunt u Cognitive Services gebruiken?

Elke service biedt informatie over uw gegevens. U kunt Services combi neren om oplossingen te koppelen, zoals het omzetten van spraak (audio) naar tekst, het vertalen van de tekst in veel talen, en het gebruik van de vertaalde talen om antwoorden te krijgen van een Knowledge Base. Hoewel Cognitive Services kan worden gebruikt om intelligente oplossingen te maken, kunnen ze ook worden gecombineerd met traditionele machine learning projecten om modellen aan te vullen of het ontwikkel proces te versnellen. 

Cognitive Services die export modellen bieden voor andere machine learning-hulpprogram ma's:

|Cognitieve service|Model gegevens|
|--|--|
|[Custom Vision](./custom-vision-service/overview.md)|[Exporteren](./custom-vision-service/export-model-python.md) voor tensor flow voor Android, CoreML voor IOS11, ONNX voor Windows ml|

## <a name="learn-more"></a>Lees meer

* [Architectuur handleiding-wat zijn de machine learning producten van micro soft?](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Machine learning-Inleiding tot diep gaande informatie versus machine learning](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Volgende stappen

* Maak uw cognitieve service account in de [Azure Portal](cognitive-services-apis-create-account.md) of met [Azure cli](./cognitive-services-apis-create-account-cli.md).
* Meer informatie over het [verifiëren](authentication.md) van een cognitieve service.
* [Diagnostische logboek registratie](diagnostic-logging.md) gebruiken voor het identificeren van problemen en het opsporen van fouten. 
* Implementeer een cognitieve service in een docker- [container](cognitive-services-container-support.md).
* Blijf up-to-date met [service-updates](https://azure.microsoft.com/updates/?product=cognitive-services).
