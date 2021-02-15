---
title: 'Functies: actie en context-persoonlijker'
titleSuffix: Azure Cognitive Services
description: Personaler maakt gebruik van functies, informatie over acties en context, om betere suggesties te stellen. Functies kunnen zeer algemeen of specifiek voor een item zijn.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: f49abd4ca1cc1ccdcb7ba2b0fab3bad953dede5d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380539"
---
# <a name="features-are-information-about-actions-and-context"></a>Functies zijn informatie over acties en context

De Personaler service werkt door te leren wat uw toepassing moet weer geven aan gebruikers in een bepaalde context.

Personaler maakt gebruik van **functies**. Dit is informatie over de **huidige context** om de beste **actie** te kiezen. De functies vertegenwoordigen alle informatie die u kunt gebruiken om persoonlijke voor delen te krijgen. Functies kunnen zeer algemeen of specifiek voor een item zijn. 

U hebt bijvoorbeeld een **functie** over:

* De _gebruiker_ , zoals een `Sports_Shopper` . Dit mag geen afzonderlijke gebruikers-ID zijn. 
* De _inhoud_ , bijvoorbeeld als een video een `Documentary` , een `Movie` of een is `TV Series` , of een retail-item beschikbaar is in de Store.
* De _huidige_ tijds periode, zoals de dag van de week.

Personaler schrijft, beperkt of corrigeert de functies die u kunt verzenden voor acties en context:

* U kunt sommige functies voor sommige acties en niet voor anderen verzenden als u deze niet hebt. TV-reeksen kunnen bijvoorbeeld kenmerken van films hebben.
* Mogelijk zijn sommige functies slechts enkele keren beschikbaar. Een mobiele toepassing kan bijvoorbeeld meer informatie geven dan een webpagina. 
* Na verloop van tijd kunt u functies over context en acties toevoegen en verwijderen. Personaler gaat verder met de beschik bare informatie.
* Er moet ten minste één functie zijn voor de context. Personaler biedt geen ondersteuning voor een lege context. Als u elke keer een vaste context verzendt, kiest Personaler dan de actie voor de classificaties alleen met betrekking tot de functies in de acties.
* Voor categorische-functies hoeft u de mogelijke waarden niet te definiëren en hoeft u geen bereik vooraf in te stellen voor numerieke waarden.

## <a name="supported-feature-types"></a>Ondersteunde functie typen

Personaler ondersteunt functies van het type teken reeks, numeriek en Booleaanse waarde. Het is zeer waarschijnlijk dat uw toepassing gebruik maakt van teken reeks functies, met enkele uitzonde ringen.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Hoe de keuze van het functie type is van invloed op Machine Learning in Personaler

* **Teken reeksen**: voor teken reeks typen wordt elke combi natie van sleutel en waarde behandeld als een One-Hot functie (bijvoorbeeld genre: "ScienceFiction" en genre: "documentaire" maakt twee nieuwe invoer functies voor het machine learning model.
* **Numeriek**: u moet numerieke waarden gebruiken wanneer het getal een grootte is die proportioneel van invloed moet zijn op het personalisatie resultaat. Dit is een zeer afhankelijk scenario. In een vereenvoudigd voor beeld bijvoorbeeld: bij het personaliseren van een retail-ervaring kan NumberOfPetsOwned een functie zijn die numeriek is als u wilt dat mensen met 2 of 3 huis dieren twee keer zo veel mogelijk van invloed op het persoonlijke resultaat hebben of drie keer per. Functies die zijn gebaseerd op numerieke eenheden, maar waarbij de betekenis niet lineair is, zoals leeftijd, Tempe ratuur of persoons hoogte, worden het beste gecodeerd als teken reeksen. Bijvoorbeeld DayOfMonth zou een teken reeks zijn met ' 1 ', ' 2 '... ' 31 '. Als u veel categorieën hebt, kan de functie kwaliteit doorgaans worden verbeterd door gebruik te maken van bereiken. Leeftijd kan bijvoorbeeld worden gecodeerd als ' leeftijd ': ' 0-5 ', ' leeftijd ': ' 6-10 ', enzovoort.
* **Booleaanse** waarden die zijn verzonden met de waarde ' false ' fungeren alsof ze niet had zijn verzonden.

Functies die niet aanwezig zijn, moeten worden wegge laten uit de aanvraag. Vermijd het verzenden van functies met een null-waarde, omdat deze wordt verwerkt als bestaande en met de waarde ' null ' bij het trainen van het model.

## <a name="categorize-features-with-namespaces"></a>Functies categoriseren met naam ruimten

Personaler neemt functies die zijn ingedeeld in naam ruimten. U kunt in uw toepassing bepalen of naam ruimten worden gebruikt en wat ze moeten zijn. Naam ruimten worden gebruikt om functies te groeperen over een vergelijkbaar onderwerp of functies die afkomstig zijn van een bepaalde bron.

Hier volgen enkele voor beelden van onderdeel naam ruimten die worden gebruikt door toepassingen:

* User_Profile_from_CRM
* Tijd
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Weer
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

U kunt functie naam ruimten volgen volgens uw eigen conventies, zolang ze geldige JSON-sleutels zijn. Naam ruimten worden gebruikt om functies in verschillende sets te organiseren en om dubbel zinnigheid te maken met vergelijk bare namen. U kunt naam ruimten beschouwen als een voor voegsel die wordt toegevoegd aan functie namen. Naam ruimten kunnen niet worden genest.


In de volgende JSON, `user` , `state` en `device` zijn functie naam ruimten. 

> [!Note]
> We raden u op dit moment ten zeerste aan om namen te gebruiken voor functie naam ruimten die zijn gebaseerd op UTF-8 en beginnen met andere letters. Bijvoorbeeld, `user` `state` , en `device` begin met `u` , `s` , en `d` . Momenteel hebben naam ruimten met dezelfde eerste tekens kunnen leiden tot conflicten in indexen die worden gebruikt voor machine learning.

JSON-objecten kunnen geneste JSON-objecten en eenvoudige eigenschappen/waarden bevatten. Een matrix kan alleen worden opgenomen als de matrix items getallen zijn. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": ["47.6,-122.1"]
            }
        },
        {
            "environment": {
                "dayOfMonth": "28",
                "monthOfYear": "8",
                "timeOfDay": "13:00",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        },
        {
            "userActivity" : {
                "itemsInCart": 3,
                "cartValue": 250,
                "appliedCoupon": true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Beperkingen in teken sets voor naam ruimten

De teken reeks die u gebruikt voor het benoemen van de naam ruimte moet voldoen aan enkele beperkingen: 
* Dit kan niet Unicode zijn.
* U kunt sommige van de afdruk bare symbolen gebruiken met codes < 256 voor de namen van de naam ruimte. 
* U kunt geen symbolen gebruiken met codes < 32 (niet afdruk bare), 32 (spatie), 58 (dubbele punt), 124 (pijp) en 126 – 140.
* Het mag niet beginnen met een onderstrepings teken "_" of het onderdeel wordt genegeerd.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Functie sets effectiever maken voor persoonlijker

Een goede functieset helpt persoonlijker te leren hoe u de actie kunt voors pellen waarmee de hoogste beloning wordt gestimuleerd. 

Overweeg het verzenden van functies naar de Personaler Rank-API die deze aanbevelingen volgen:

* Gebruik categorische en string types voor functies die geen grootte hebben. 

* Er zijn voldoende functies om persoonlijke instellingen te verzorgen. Hoe preciezer de inhoud moet zijn, hoe meer functies nodig zijn.

* Er zijn voldoende functies voor verschillende *densiteit*. Een functie is zeer *dicht* als veel items zijn gegroepeerd in een paar buckets. Duizenden Video's kunnen bijvoorbeeld worden geclassificeerd als ' lang ' (meer dan 5 minuten lang) en ' korte ' (minder dan 5 minuten lang zijn). Dit is een *zeer compacte* functie. Aan de andere kant kunnen dezelfde duizenden items een kenmerk met de naam "title" hebben, wat bijna nooit van het ene naar het andere item is. Dit is een zeer niet-compacte of *sparse* functie.  

Met de functies van hoge dichtheid kan de persoonlijker leren van het ene naar het andere item. Maar als er maar een paar functies zijn en deze te dicht zijn, probeert de Personaler de inhoud nauw keurig te richten met slechts enkele buckets waaruit kan worden gekozen.

### <a name="improve-feature-sets"></a>Functie sets verbeteren 

Analyseer het gebruikers gedrag door een offline-evaluatie uit te voeren. Op die manier kunt u eerdere gegevens bekijken om te zien welke functies sterk bijdragen aan positieve beloningen en die minder bijdragen. U kunt zien welke functies u helpen, en uw toepassing helpt u bij het vinden van betere functies om de resultaten nog verder te verbeteren.

De volgende secties bevatten algemene procedures voor het verbeteren van functies die naar persoonlijke voor keuren worden verzonden.

#### <a name="make-features-more-dense"></a>Functies een compacter maken

Het is mogelijk om uw functie sets te verbeteren door ze te bewerken zodat deze groter en meer of minder compacte zijn.

Een time stamp voor het tweede is bijvoorbeeld een zeer verspreide functie. Dit kan een meer compacte (effectief) zijn door tijden te classificeren naar 's ochtends, rond middaguur, 's middags enzovoort.

Locatie gegevens hebben doorgaans voor delen ten opzichte van het maken van grotere classificaties. Bijvoorbeeld een Latitude-Longitude coördinaat zoals lat: 47,67402 ° N, Long: 122,12154 ° W is te nauw keurig en dwingt het model om de breedte graad en lengte graad als afzonderlijke dimensies te ontdekken. Wanneer u probeert te personaliseren op basis van locatie-informatie, is het handig om locatie gegevens te groeperen in grotere sectoren. Een eenvoudige manier om dat te doen, is door een geschikte Afrondings precisie te kiezen voor de Lat-Long getallen en de breedte graad en lengte graad te combi neren in ' gebieden ' door ze in één teken reeks te brengen. Een goede manier om 47,67402 ° N te vertegenwoordigen, lang: 122,12154 ° W in regio's ongeveer een paar kilo meters is ' locatie ': ' 34.3, 12,1 '.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Functie sets met extrapolatie gegevens uitvouwen

U kunt ook meer functies verkrijgen door te denken aan niet-Verken kenmerken die kunnen worden afgeleid van de gegevens die u al hebt. Bijvoorbeeld: in een fictieve persoonlijke weer geven van een film lijst is het mogelijk dat een weekend VS-weekdag een ander gedrag van gebruikers veroorzaakt? De tijd kan worden uitgevouwen om een ' weekend ' of ' weekdag-kenmerk ' te hebben. Zorgen de nationale vakantie dagen voor de aandacht voor bepaalde film typen? Een "Halloween"-kenmerk is bijvoorbeeld nuttig op plaatsen waar het relevant is. Is het mogelijk dat regen achtige weer een grote invloed heeft op de keuze van een film voor veel mensen? Met tijd en plaats kan een weer service die informatie leveren en kunt u deze toevoegen als een extra functie. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Vouw functie sets uit met kunst matige intelligentie en cognitieve Services

Kunst matige intelligentie en kant-en-klare Cognitive Services kunnen een zeer krachtige aanvulling zijn op de Personaler. 

Door de items voor te verwerken met kunst matige intelligentie Services, kunt u gegevens die waarschijnlijk relevant zijn voor persoonlijke instellingen, automatisch extra heren.

Bijvoorbeeld:

* U kunt een film bestand uitvoeren via [video indexer](https://azure.microsoft.com/services/media-services/video-indexer/) om scène-elementen, tekst, sentiment en vele andere kenmerken uit te pakken. Deze kenmerken kunnen vervolgens groter worden gemaakt om de kenmerken te zien die de meta gegevens van het oorspronkelijke item niet hebben. 
* Installatie kopieën kunnen worden uitgevoerd via object detectie, gezichten via sentiment, enzovoort.
* Informatie in de tekst kan worden uitgebreid door entiteiten uit te pakken, sentiment, entiteiten uit te breiden met Bing kennis Graph, enzovoort.

U kunt verschillende andere [Azure-Cognitive Services](https://www.microsoft.com/cognitive-services)gebruiken, zoals

* [Entiteiten koppelen](../text-analytics/index.yml)
* [Tekstanalyse](../text-analytics/overview.md)
* [Emotion](../face/overview.md)
* [Computer Vision](../computer-vision/overview.md)

## <a name="actions-represent-a-list-of-options"></a>Acties vertegenwoordigen een lijst met opties

Elke actie:

* Heeft een _gebeurtenis_ -id. Als u al een gebeurtenis-ID hebt, moet u dat doen. Als u geen gebeurtenis-ID hebt, mag er geen worden verzonden, wordt er voor u een persoonlijker gemaakt en wordt deze geretourneerd in het antwoord van de rang aanvraag. De ID is gekoppeld aan de positie van de gebeurtenis, niet aan de gebruiker. Als u een ID maakt, werkt een GUID het beste. 
* Bevat een lijst met functies.
* De lijst met functies kan groot (honderden) zijn, maar we raden u aan de effectiviteit van de functie te evalueren om functies te verwijderen die niet bijdragen aan het verkrijgen van beloningen. 
* De functies in de **acties** kunnen of mogelijk geen correlatie hebben met functies in de **context** die wordt gebruikt door personaler.
* Functies voor acties zijn mogelijk aanwezig in sommige acties en niet op andere. 
* Functies voor een bepaalde actie-ID zijn mogelijk één dag beschikbaar, maar later is niet meer beschikbaar. 

De machine learning-algoritmen van personalers worden beter wanneer er stabiele functie sets zijn, maar rang gesprekken mislukken niet als de functie is gewijzigd in de loop van de tijd.

Geen meer dan 50 acties verzenden bij het classificeren van acties. Dit kunnen dezelfde 50-acties zijn telkens, of ze kunnen veranderen. Als u bijvoorbeeld een product catalogus van 10.000 items voor een e-commerce-toepassing hebt, kunt u een aanbevelings-of filter engine gebruiken om te bepalen wat de top 40 van een klant kan zijn en kunt u Personaler gebruiken om de app te vinden waarmee de meeste beloning wordt gegenereerd (bijvoorbeeld wanneer de gebruiker aan het mandje wordt toegevoegd) voor de huidige context.


### <a name="examples-of-actions"></a>Voor beelden van acties

De acties die u naar de positie-API verzendt, zijn afhankelijk van wat u probeert te personaliseren.

Hier volgen enkele voorbeelden:

|Doel|Bewerking|
|--|--|
|Personaliseer welk artikel op een nieuws website is gemarkeerd.|Elke actie is een mogelijk nieuws artikel.|
|Optimaliseer de plaatsing van advertenties op een website.|Elke actie is een indeling of regels voor het maken van een indeling voor de advertenties (bijvoorbeeld bovenaan, kleine afbeeldingen, grote afbeeldingen).|
|Geef een persoonlijke volg orde van de aanbevolen items op een winkel website weer.|Elke actie is een specifiek product.|
|Stel gebruikers interface-elementen, zoals filters, voor op een specifieke foto.|Elke actie kan een ander filter zijn.|
|Kies een reactie op een chat-bot om de gebruikers intentie te verduidelijken of om een actie te suggereren.|Elke actie is een optie voor het interpreteren van het antwoord.|
|Kies wat u wilt weer geven boven aan een lijst met zoek resultaten|Elke actie is een van de meeste Zoek resultaten.|


### <a name="examples-of-features-for-actions"></a>Voor beelden van functies voor acties

Hier volgen enkele voor beelden van functies voor acties. Deze zijn afhankelijk van elke toepassing.

* Functies met kenmerken van de acties. Is het bijvoorbeeld een film of een tv-serie?
* Functies over hoe gebruikers in het verleden met deze actie kunnen werken. Deze film wordt meestal gezien door personen in demografische gegevens A of B, die meestal niet meer dan één keer worden afgespeeld.
* Functies over de kenmerken van de manier waarop de gebruiker de acties *ziet* . Bevat de poster voor de film die in de miniatuur wordt weer gegeven bijvoorbeeld gezichten, auto's of landschappen?

### <a name="load-actions-from-the-client-application"></a>Acties laden vanuit de client toepassing

Functies van acties kunnen doorgaans afkomstig zijn van Content Management Systems, catalogs en aanbevolen systemen. Uw toepassing is verantwoordelijk voor het laden van de informatie over de acties van de relevante data bases en systemen die u hebt. Als uw acties niet worden gewijzigd of als ze niet worden geladen elke keer dat er sprake is van een onnodige impact op de prestaties, kunt u logica in uw toepassing toevoegen om deze informatie in de cache op te slaan.

### <a name="prevent-actions-from-being-ranked"></a>Voor komen dat acties worden gerangschikt

In sommige gevallen zijn er acties die u niet wilt weer geven voor gebruikers. De beste manier om te voor komen dat een actie wordt geclassificeerd als een bovenste, is deze niet in de actie lijst opnemen in de positie-API in de eerste plaats.

In sommige gevallen kan het alleen later in uw bedrijfs logica worden bepaald als een resulterende _actie_ van een absolute API-aanroep moet worden weer gegeven aan een gebruiker. In deze gevallen moet u _inactieve gebeurtenissen_ gebruiken.

## <a name="json-format-for-actions"></a>JSON-indeling voor acties

Wanneer u positie aanroept, verzendt u meerdere acties waaruit u kunt kiezen:

JSON-objecten kunnen geneste JSON-objecten en eenvoudige eigenschappen/waarden bevatten. Een matrix kan alleen worden opgenomen als de matrix items getallen zijn. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Voor beelden van context informatie

Informatie over de _context_ is afhankelijk van elke toepassing en use-case, maar kan meestal informatie bevatten zoals:

* Demografische en profiel gegevens over uw gebruiker.
* Gegevens die worden geëxtraheerd uit HTTP-headers, zoals gebruikers agent, of die zijn afgeleid van HTTP-informatie, zoals reverse-geografische zoek acties op basis van IP-adressen.
* Informatie over de huidige tijd, zoals de dag van de week, weekend of niet, morgen of middag, kerst seizoen, enzovoort.
* Gegevens die worden geëxtraheerd uit mobiele toepassingen, zoals locatie, verplaatsing of accu niveau.
* Historische aggregaties van het gedrag van gebruikers, zoals wat zijn de film genres die deze gebruiker het meest heeft bekeken.

Uw toepassing is verantwoordelijk voor het laden van de informatie over de context van de relevante data bases, Sens oren en systemen die u mogelijk hebt. Als uw context informatie niet verandert, kunt u logica in uw toepassing toevoegen om deze informatie in de cache op te slaan voordat u deze naar de positie-API verzendt.

## <a name="json-format-for-context"></a>JSON-indeling voor context 

De context wordt uitgedrukt als een JSON-object dat wordt verzonden naar de positie-API:

JSON-objecten kunnen geneste JSON-objecten en eenvoudige eigenschappen/waarden bevatten. Een matrix kan alleen worden opgenomen als de matrix items getallen zijn. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

[Bekrachtigend leren](concepts-reinforcement-learning.md)
