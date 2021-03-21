---
title: Image Insights ophalen-Bing Afbeeldingen zoeken-API
titleSuffix: Azure Cognitive Services
description: Informatie over het gebruik van de Bing Afbeeldingen zoeken-API om meer informatie over een installatie kopie te krijgen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 9dffc16a1d1171343fa10fce10fddba2a12c2167
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348242"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Image Insights ophalen met de Bing Afbeeldingen zoeken-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

> [!IMPORTANT]
> In plaats van het/images/Details-eind punt te gebruiken om image Insights op te halen, moet u [Visual Search](../bing-visual-search/overview.md) gebruiken, omdat het uitgebreidere inzichten bevat.


Elke installatie kopie bevat een Insights-token dat u kunt gebruiken om informatie over de installatie kopie op te halen. U kunt bijvoorbeeld een verzameling gerelateerde installatie kopieën, webpagina's die de installatie kopie bevatten of een lijst met verkopers weer geven waarin u het product kunt kopen dat in de installatie kopie wordt weer gegeven.  

Als u inzicht wilt krijgen in een afbeelding, legt u het [imageInsightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) -token van de installatie kopie vast in het antwoord.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Vervolgens roept u het eind punt voor de afbeeldings gegevens aan en stelt u de [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) -query parameter in op het token in `imageInsightsToken` .  

Stel de query-para meter in om de inzichten op te geven die u wilt ophalen `modules` . Als u alle inzichten wilt ophalen, stelt `modules` u in op `All` . Als u alleen het bijschrift en verzamel inzichten wilt ophalen, stelt `modules` u in op `Caption%2CCollection` . Zie [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested)voor een volledige lijst met mogelijke inzichten. Niet alle inzichten zijn beschikbaar voor alle installatie kopieën. Het antwoord bevat alle inzichten die u hebt aangevraagd, indien beschikbaar.

In het volgende voor beeld wordt alle beschik bare inzichten voor de voor gaande afbeelding opgevraagd.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Inzichten verkrijgen van een bekende installatie kopie

Als u de URL naar een afbeelding hebt waarvan u inzicht wilt krijgen, gebruikt u de query parameter [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) in plaats van de para meter [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) om de installatie kopie op te geven. Als u het afbeeldings bestand hebt, kunt u de binaire afbeelding van de installatie kopie verzenden naar de hoofd tekst van een POST-aanvraag. Als u een POST-aanvraag gebruikt, `Content-Type` moet de koptekst worden ingesteld op `multipart/data-form` . Met beide opties kan de grootte van de afbeelding niet groter zijn dan 1 MB.  

Als u een URL naar de afbeelding hebt, ziet u in het volgende voor beeld hoe u inzichten van een afbeelding aanvraagt.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Alle image Insights ophalen  

Als u alle inzichten van een installatie kopie wilt aanvragen, stelt u de query parameter voor [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) in op `All` . Om verwante Zoek opdrachten te krijgen, moet de aanvraag de query teken reeks van de gebruiker bevatten. In dit voor beeld ziet u hoe de [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) wordt gebruikt om de afbeelding op te geven.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Het object op het hoogste niveau is een [ImageInsightsResponse](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) -object in plaats van een [afbeeldings](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) object.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Entiteiten herkennen in een installatie kopie  

De functie voor entiteits herkenning identificeert entiteiten in een installatie kopie, momenteel alleen personen. Als u entiteiten in een installatie kopie wilt identificeren, stelt u de query parameter [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) in op `RecognizedEntities` .  

> [!NOTE]
> U mag deze module niet met een andere module opgeven. Als u deze module met andere modules opgeeft, bevat het antwoord geen herkende entiteiten.  

Hieronder ziet u hoe u de installatie kopie kunt opgeven met behulp van de para meter [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) . Let op dat URL de query parameters versleutelt.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Hieronder ziet u het antwoord op de vorige aanvraag. Omdat de afbeelding twee personen bevat, identificeert het antwoord een regio voor elke persoon. In dit geval zijn de mensen herkend in de groepen CelebrityAnnotations en CelebRecognitionAnnotations. Bing bevat een lijst met de personen in elke groep op basis van de waarschijnlijkheid dat ze overeenkomen met de persoon in de oorspronkelijke afbeelding. De lijst is in aflopende volg orde van vertrouwen. De groep CelebRecognitionAnnotations biedt het hoogste vertrouwens niveau dat de overeenkomst juist is.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

In het `region` veld wordt het gebied van de installatie kopie aangegeven waar Bing de entiteit heeft herkend. Voor personen staat de regio voor het gezicht van de persoon.  

De waarden van de rechthoek zijn relatief ten opzichte van de breedte en hoogte van de oorspronkelijke afbeelding en zijn in het bereik 0,0 tot en met 1,0. Als de afbeelding bijvoorbeeld 300x200 is en de bovenste, linkerhoek van het gebied op punt (10, 20) en de onderste, rechter hoek op punt (290, 150), is de genormaliseerde rechthoek:  

-   Links: 10/300 = 0,03333...  
-   Boven: 20/200 = 0,1  
-   Rechts: 290/300 = 0,9667...  
-   Onder: 150/200 = 0,75  

U kunt de regio gebruiken die door Bing wordt geretourneerd in volgende Insights-aanroepen. Als u bijvoorbeeld visueel vergelijk bare afbeeldingen van de herkende entiteit wilt ophalen. Zie afbeeldingen bijsnijden voor gebruik met visueel vergelijk bare en entiteits herkennings modules voor meer informatie. Hieronder ziet u de toewijzing tussen de regio velden en de query parameters die u gebruikt om afbeeldingen bij te snijden.  

-   Links wijst naar [CAL](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
-   Bovenaan wijst naar [kat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
-   Rechts wijst naar [auto](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
-   Onder toewijzen aan [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Visueel vergelijk bare afbeeldingen zoeken  

Als u afbeeldingen wilt zoeken die visueel lijken op de oorspronkelijke afbeelding, stelt u de query parameter [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) in op SimilarImages.  

De volgende aanvraag laat zien hoe u visueel vergelijk bare afbeeldingen krijgt. De aanvraag gebruikt de query parameter [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) om de oorspronkelijke afbeelding te identificeren. Als u de relevantie wilt verbeteren, moet u de query teken reeks van de gebruiker toevoegen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Hieronder ziet u het antwoord op de vorige aanvraag.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Afbeeldingen bijsnijden voor gebruik met visueel vergelijk bare modules voor entiteits herkenning  

Als u de regio van de afbeelding wilt opgeven die door Bing wordt gebruikt om te bepalen of afbeeldingen visueel vergelijkbaar zijn of om entiteit herkenning uit te voeren, gebruikt u de query parameters [CAL](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [kat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat), [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)en [auto](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) . Bing maakt standaard gebruik van de volledige afbeelding.  

De para meters geven de boven, linkerhoek en onderste, rechter hoek van de regio die Bing gebruikt voor vergelijking. Geef de waarden op als fracties van de breedte en hoogte van de oorspronkelijke afbeelding. De breuk waarden beginnen met (0,0, 0,0) bovenaan, linksboven en eindigen met (1,0, 1,0) in de rechter benedenhoek. Als u bijvoorbeeld wilt opgeven dat de bovenste, linkerhoek een kwart van de bovenkant en een vierde van de richting van de rechter kant begint, stelt `cal` u in op 0,25 en `cat` 0,25.  

In de volgende volg orde van aanroepen wordt het effect van het opgeven van het gebied voor bijsnijden weer gegeven. De eerste aanroep bevat geen bijsnijding en Bing herkent twee mensen aan de zijkant van de afbeelding.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

In het antwoord worden twee herkende entiteiten weer gegeven.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

Met de tweede aanroep wordt de afbeelding verticaal in het midden bijgesneden en wordt één persoon aan de rechter kant van de afbeelding herkend.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

In het antwoord wordt een herkende entiteit weer gegeven.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Visueel vergelijk bare producten zoeken  

Als u wilt zoeken naar installatie kopieën die producten bevatten die visueel lijken op de producten die zijn gevonden in de oorspronkelijke installatie kopie, stelt u de query parameter [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) in op SimilarProducts.  

De volgende aanvraag laat zien hoe u beelden van visueel vergelijk bare producten kunt ophalen. De aanvraag gebruikt de query parameter [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) om de oorspronkelijke afbeelding te identificeren die in een eerdere aanvraag is geretourneerd. Als u de relevantie wilt verbeteren, moet u de query teken reeks van de gebruiker toevoegen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Hieronder ziet u het antwoord op de vorige aanvraag. Het antwoord bevat een afbeelding van een vergelijkbaar product en geeft aan hoeveel verkopers het product online aanbieden, of er product beoordelingen zijn en de laagste prijs gevonden (Zie het `aggregateOffer` veld).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Als u een lijst wilt weer geven met de handelaars die het product online aanbieden (Zie het veld [offerCount](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) ), roept u de API opnieuw aan en stelt `modules` u deze in op ShoppingSources. Vervolgens stelt u de `insightsToken` query-para meter in op het token dat is gevonden in de product samenvattings afbeelding.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Hier volgt de reactie op de vorige aanvraag.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```