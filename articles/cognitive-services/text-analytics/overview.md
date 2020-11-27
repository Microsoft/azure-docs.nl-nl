---
title: Tekstanalyse met de Text Analytics-API voor Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Meer informatie over tekstanalyse met de Text Analytics-API. Gebruik het voor sentimentanalyse, taaldetectie en andere vormen van natuurlijke taalverwerking.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/17/2020
ms.author: aahi
keywords: tekstanalyse, sentimentanalyse, tekst analytics
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 14dfa6111bbda8750b175c8c9b2a96321e1af2c5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95519401"
---
# <a name="what-is-the-text-analytics-api"></a>Wat is Text Analytics-API?

De Text Analytics-API is een op de cloud gebaseerde service die NLP-functies (natuurlijke taalverwerking) biedt voor tekstanalyse, waaronder: sentimentanalyse, meninganalyse, sleuteltermextractie, taaldetectie en herkenning van benoemde entiteiten.

De API maakt deel uit van [Azure Cognitive Services](../index.yml), een verzameling van machine learning- en AI-algoritmen in de cloud, die kunnen worden gebruikt in uw ontwikkelprojecten. U kunt deze functies gebruiken met de [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) of met de [clientbibliotheek](quickstarts/text-analytics-sdk.md).

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

## <a name="sentiment-analysis"></a>Sentimentanalyse

Gebruik [sentimentanalyse](how-tos/text-analytics-how-to-sentiment-analysis.md) en kom erachter wat klanten van uw merk of onderwerp vinden door de tekst te analyseren op aanwijzingen over positieve of negatieve indrukken. 

De functie biedt sentimentlabels (zoals 'negatief', 'neutraal' en 'positief') op basis van de hoogste betrouwbaarheidsscore die door de service op zin- het documentniveau is gevonden. Met deze functie worden ook betrouwbaarheidsscores geretourneerd tussen 0 en 1 voor elk document en voor alle zinnen in dat document, voor positief, neutraal en negatief sentiment. U kunt de service ook on-premises uitvoeren [met behulp van een container](how-tos/text-analytics-how-to-install-containers.md).

Vanaf v.3.1-preview is meninganalyse een functie van Sentimentanalyse. Deze functie wordt ook wel op aspecten gebaseerde sentimentanalyse in natuurlijke taalverwerking (NLP) en biedt gedetailleerde informatie over de meningen over bepaalde aspecten (zoals de kenmerken van producten of diensten) in de tekst.

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

Gebruik [sleuteltermextractie](how-tos/text-analytics-how-to-keyword-extraction.md) om snel de belangrijkste concepten in de tekst te identificeren. Bijvoorbeeld, voor de tekst 'het eten was heerlijk en de bediening fantastisch' retourneert de sleuteltermextractie de belangrijkste gespreksonderwerpen: 'eten' en 'bediening fantastisch'.

## <a name="language-detection"></a>Taaldetectie

Taaldetectie kan [detecteren in welke taal de ingevoerde tekst is geschreven](how-tos/text-analytics-how-to-language-detection.md) en één taalcode rapporteren voor elk document dat is ingediend bij de aanvraag in veel verschillende talen, varianten, dialecten en enkele regionale/culturele talen. De taalcode wordt gekoppeld aan een betrouwbaarheidsscore.

## <a name="named-entity-recognition"></a>Herkenning van tekeneenheden

Herkenning van benoemde entiteiten (NER) kan entiteiten in uw tekst [identificeren en categoriseren](how-tos/text-analytics-how-to-entity-linking.md) als mensen, plaatsen, organisaties of hoeveelheden. Bekende entiteiten worden ook herkend en gekoppeld aan informatie op het web.

## <a name="deploy-on-premises-using-docker-containers"></a>On-premises implementeren met behulp van Docker-containers

[Gebruik Text Analytics-containers](how-tos/text-analytics-how-to-install-containers.md) om API-functies on-premises te implementeren. Deze Docker-containers stellen u in staat om de service dichter bij uw gegevens te brengen voor naleving, beveiliging en andere operationele redenen. Text Analytics biedt de volgende containers:

* sentimentanalyse
* sleuteltermextractie (preview)
* taaldetectie (preview)
* Text Analytics voor status (preview)

## <a name="asynchronous-operations"></a>Asynchrone bewerkingen

Het eindpunt `/analyze` stelt u in staat om geselecteerde functies van de Text Analytics-API [asynchroon](how-tos/text-analytics-how-to-call-api.md) te gebruiken, zoals NER en sleuteltermextractie.

## <a name="typical-workflow"></a>Standaardwerkstroom

De werkstroom is eenvoudig: u dient gegevens in die u wilt analyseren en verwerkt de uitvoer in uw code. Analyseprogramma's worden in de huidige staat gebruikt, zonder extra configuratie of aanpassing.

1. [Maak een Azure-resource](../cognitive-services-apis-create-account.md) voor Text Analytics. Daarna kunt u [de sleutel ophalen](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) die voor u is gegenereerd om uw aanvragen te verifiëren.

2. [Formuleer een aanvraag](how-tos/text-analytics-how-to-call-api.md#json-schema) in JSON, die uw gegevens bevat als onbewerkte tekst.

3. Publiceer de aanvraag aan het eindpunt dat is gemaakt tijdens de registratie, en voeg de gewenste resource toe: sentimentanalyse, sleuteltermextractie, taaldetectie of herkenning van benoemde entiteiten.

4. U kunt het antwoord streamen of lokaal opslaan. Afhankelijk van de aanvraag bestaan de resultaten uit een gevoelsscore, een verzameling geëxtraheerde sleuteltermen of een taalcode.

Uitvoer wordt geretourneerd als één JSON-document met de resultaten van elk tekstdocument dat u hebt geplaatst, op basis van de id. U kunt de resultaten vervolgens analyseren, visualiseren of categoriseren in inzichten waarvoor een actie kan worden uitgevoerd.

Gegevens worden niet opgeslagen in uw account. Bewerkingen die door de Text Analytics-API worden uitgevoerd zijn staatloos. Dat betekent dat de tekst die u opgeeft, wordt verwerkt en de resultaten direct worden geretourneerd.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Text Analytics voor meerdere niveaus van programmeerervaring

U kunt de Text Analytics-API in uw processen gaan gebruiken, ook als u niet veel ervaring hebt met programmeren. Gebruik deze zelfstudies om te leren hoe u de API kunt gebruiken om tekst op verschillende manieren te analyseren, zodat deze past bij uw ervaringsniveau. 

* Minimaal programmeren vereist:
    * [Informatie extraheren in Excel met behulp van Text Analytics en Power Automate](tutorials/extract-excel-information.md)
    * [De Text Analytics-API en MS Flow gebruiken om het sentiment van opmerkingen in een Yammer-groep te identificeren](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%252f%252fazure%252fbread%252ftoc.json&toc=%252f%252fazure%252fcognitive-services%252ftext-analytics%252ftoc.json)
    * [Power BI integreren met de Text Analytics-API voor het analyseren van feedback van klanten](tutorials/tutorial-power-bi-key-phrases.md)
* Aanbevolen programmeerervaring:
    * [Sentimentanalyse over het streamen van gegevens met behulp van Azure Databricks](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%252f%252fazure%252fbread%252ftoc.json&toc=%252f%252fazure%252fcognitive-services%252ftext-analytics%252ftoc.json)
    * [Een Flask-app maken om tekst te vertalen, gevoel te analyseren en spraak na te bootsen](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%252f%252fazure%252fbread%252ftoc.json&toc=%252f%252fazure%252fcognitive-services%252ftext-analytics%252ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Ondersteunde talen

Deze sectie is voor betere zichtbaarheid verplaatst naar een afzonderlijk artikel. Raadpleeg [Ondersteunde talen in de Text Analytics-API](./language-support.md) voor deze inhoud.

<a name="data-limits"></a>

## <a name="data-limits"></a>Gegevenslimieten

Alle eindpunten van de Text Analytics-API accepteren onbewerkte tekstgegevens. Zie het artikel [Gegevenslimieten](concepts/data-limits.md) voor meer informatie.

## <a name="unicode-encoding"></a>Unicode-codering

De Text Analytics-API maakt gebruik van Unicode-codering voor tekstweergave en het berekenen van het aantal tekens. Aanvragen kunnen worden ingediend in UTF-8 en UTF-16, zonder meetbare verschillen in het aantal tekens. Unicode-codepunten worden gebruikt als de heuristiek voor tekenlengte en worden wat de gegevenslimieten voor tekstanalyse betreft als gelijkwaardig beschouwd. Als u [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements) gebruikt om het aantal tekens te verkrijgen, gebruikt u dezelfde methode als voor het meten van de gegevensgrootte.

## <a name="next-steps"></a>Volgende stappen

+ [Maak een Azure-resource](../cognitive-services-apis-create-account.md) voor Text Analytics om een sleutel en eindpunt voor uw toepassingen te krijgen.

+ Gebruik de [quickstart](quickstarts/text-analytics-sdk.md) om API-aanroepen te verzenden. Informatie over het indienen van tekst, het kiezen van een analyse en het bekijken van de resultaten met minimale code.

+ Zie [wat er nieuw is in de Text Analytics-API](whats-new.md) voor meer informatie over nieuwe releases en functies.

+ U kunt meer leren met deze [zelfstudie over sentimentanalyse](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services) met behulp van Azure Databricks.

+ Bekijk onze lijst met blogposts en meer video's over hoe u de Text Analytics-API gebruikt met andere hulpprogramma's en technologieën in onze [pagina Externe en community-inhoud](text-analytics-resource-external-community.md).