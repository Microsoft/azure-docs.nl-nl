---
title: Aanbevolen procedures voor het Azure Maps van Search Service | Microsoft Azure kaarten
description: Meer informatie over het Toep assen van de aanbevolen procedures voor het gebruik van de Search Service van Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 11c1938c3c1ccba533f52336fad81ebeaae53b24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895474"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Aanbevolen procedures voor het Azure Maps van Search Service

Azure Maps [Search service](/rest/api/maps/search) bevat api's die verschillende mogelijkheden bieden om ontwikkel aars te helpen bij het zoeken naar adressen, plaatsen, bedrijfs vermeldingen op naam of categorie en andere geografische informatie. Met[fuzzy Search-API](/rest/api/maps/search/getsearchfuzzy) kunnen gebruikers bijvoorbeeld zoeken naar een adres of een POI.

In dit artikel wordt uitgelegd hoe u geluids procedures toepast wanneer u gegevens aanroept vanuit Azure Maps Search Service. U leert het volgende:
> [!div class="checklist"]
> * Query's bouwen om relevante overeenkomsten te retour neren
> * Zoek resultaten beperken
> * Meer informatie over de verschillen tussen resultaat typen
> * Lees de adres structuur voor zoeken en antwoorden

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.

In dit artikel wordt gebruikgemaakt van de [postman-app](https://www.postman.com/downloads/) om rest-aanroepen te bouwen, maar u kunt elke API-ontwikkel omgeving kiezen.

## <a name="best-practices-to-geocode-addresses"></a>Aanbevolen procedures voor het geocoderen van adressen

Wanneer u zoekt naar een volledig of gedeeltelijk adres met behulp van Azure Maps Search Service, leest de API tref woorden uit uw zoek opdracht. Vervolgens worden de lengte-en breedte coördinaten van het adres geretourneerd. Dit proces wordt *geocodering* genoemd.

De mogelijkheid tot Geocode in een land/regio is afhankelijk van de beschik baarheid van weggegevens en de nauw keurigheid van de geocoderings service. Zie [geocoderings dekking](./geocoding-coverage.md)voor meer informatie over Azure Maps mogelijkheden voor geocodering per land of regio.

### <a name="limit-search-results"></a>Zoek resultaten beperken

 U kunt met behulp van Azure Maps Search-API de zoek resultaten op de juiste manier beperken. U beperkt de resultaten zodat u relevante gegevens kunt weer geven voor uw gebruikers.

> [!NOTE]
> De zoek-Api's ondersteunen meer para meters dan alleen de waarden die in dit artikel worden beschreven.

#### <a name="geobiased-search-results"></a>Geoverte kende Zoek resultaten

Als u de resultaten van de gebruiker wilt geobias, voegt u altijd zoveel mogelijk locatie gegevens toe. U kunt de zoek resultaten beperken door enkele invoer typen op te geven:

* Stel de `countrySet` para meter in. U kunt dit bijvoorbeeld instellen op `US,FR` . Standaard doorzoekt de API de hele wereld, zodat deze onnodige resultaten kan retour neren. Als uw query geen `countrySet` para meter heeft, kan de zoek opdracht mogelijk onnauwkeurige resultaten retour neren. Een zoek opdracht voor een stad met de naam *Bellevue* retourneert bijvoorbeeld resultaten van de Verenigde Staten en Frank rijk, omdat beide landen/regio's een stad met de naam *Bellevue* bevatten.

* U kunt de `btmRight` `topleft` para meters en gebruiken om het begrenzingsvak in te stellen. Met deze para meters wordt de zoek opdracht beperkt tot een specifiek gebied op de kaart.

* Als u het relevantie gebied voor de resultaten wilt beïnvloeden, definieert u de `lat` `lon` para meters en coördineert u. Gebruik de `radius` para meter om de RADIUS van het zoek gebied in te stellen.


#### <a name="fuzzy-search-parameters"></a>Zoek parameters voor fuzzy

U wordt aangeraden de Azure Maps [Zoek actie voor fuzzy](/rest/api/maps/search/getsearchfuzzy) te gebruiken wanneer u de invoer van uw gebruikers niet kent voor een zoek opdracht. De invoer van de gebruiker kan bijvoorbeeld een adres zijn of het type van een punt (POI), zoals *winkel centrum*. De API combineert POI-zoek acties en geocodering in een canonieke *Zoek opdracht op één regel*: 

* De `minFuzzyLevel` `maxFuzzyLevel` para meters en kunnen relevante overeenkomsten retour neren, zelfs wanneer de query parameters niet exact overeenkomen met de informatie die de gebruiker wil. Stel Zoek query's in op standaard waarden van en om de prestaties te maximaliseren en ongebruikelijke resultaten te verminderen `minFuzzyLevel=1` `maxFuzzyLevel=2` . 

    Als de `maxFuzzyLevel` para meter bijvoorbeeld is ingesteld op 2, wordt de zoek term *restrant* gekoppeld aan het *restaurant*. U kunt de standaard niveaus voor fuzzy negeren wanneer dat nodig is. 

* Gebruik de `idxSet` para meter om de prioriteit van de exacte set resultaat typen te bepalen. Als u een exacte set resultaten wilt priori teren, kunt u een door komma's gescheiden lijst met indexen verzenden. In uw lijst is de volg orde van de items niet van belang. Azure Maps ondersteunt de volgende indexen:

* `Addr` - **Adresbereiken: adres** punten die worden geïnterpoleerd vanaf het begin en het einde van de straat. Deze punten worden weer gegeven als adresbereiken.
* `Geo` - **Geografi**: Administratieve divisies van grond. Een geografie kan bijvoorbeeld een land/regio, staat of plaats zijn.
* `PAD` - **Punt adressen**: adressen die een straat naam en-nummer bevatten. Punt adressen vindt u in een index. Een voor beeld is *Soquel Dr 2501*. Een punt adres biedt het hoogste nauwkeurigheids niveau dat beschikbaar is voor adressen.  
* `POI` - Interessante **punten**: punten op een kaart die worden beschouwd als aandacht of die interessant kunnen zijn. De [Zoek adres-API](/rest/api/maps/search/getsearchaddress) retourneert geen poi's.  
* `Str` - **Streets**: Streets op de kaart.
* `XStr` - **Kruis straten of snij punten**: koppelingen of plaatsen waar twee straten INTERSECT.


#### <a name="usage-examples"></a>Gebruiksvoorbeelden

* `idxSet=POI` -Alleen naar Poi's zoeken. 

* `idxSet=PAD,Addr` -Alleen zoeken naar adressen. `PAD` geeft het adres van het punt aan en `Addr` geeft het adres bereik aan.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Reverse-Geocode en filter voor het entiteits type Geografie

Wanneer u een omgekeerde Geocode zoekt in de [Reverse API voor zoeken](/rest/api/maps/search/getsearchaddressreverse), kan de service veelhoeken retour neren voor administratieve gebieden. U kunt bijvoorbeeld de gebieds veelhoek voor een stad ophalen. Als u de zoek opdracht wilt beperken tot specifieke typen geografie entiteiten, neemt u de `entityType` para meter op in uw aanvragen. 

De resulterende reactie bevat de geografie-ID en het entiteits type dat overeenkomt. Als u meer dan één entiteit opgeeft, wordt met het eind punt de *kleinste beschik bare entiteit* geretourneerd. U kunt de geretourneerde geometrie-ID gebruiken om de geometrie van het geografie te verkrijgen via de [Search veelhoek-service](/rest/api/maps/search/getsearchpolygon).

#### <a name="sample-request"></a>Voorbeeld aanvraag

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Antwoord

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="set-the-results-language"></a>De taal van de resultaten instellen

Gebruik de `language` para meter om de taal voor de geretourneerde Zoek resultaten in te stellen. Als de aanvraag de taal niet heeft ingesteld, gebruikt Search Service standaard de meest voorkomende taal in het land of de regio. Als er geen gegevens beschikbaar zijn in de opgegeven taal, wordt de standaard taal gebruikt. 

Zie [Azure Maps ondersteunde talen](./supported-languages.md)voor meer informatie.


### <a name="use-predictive-mode-automatic-suggestions"></a>Voorspellings modus gebruiken (automatische suggesties)

Als u meer overeenkomsten wilt vinden voor gedeeltelijke query's, stelt `typeahead` u de para meter in op `true` . Deze query wordt geïnterpreteerd als gedeeltelijke invoer en de zoek opdracht wordt voorspellende modus ingevoerd. Als u de `typeahead` para meter niet instelt op `true` , wordt ervan uitgegaan dat alle relevante gegevens zijn door gegeven in.

In de volgende voorbeeld query wordt de service Search Address opgevraagd voor *Microsoft*. Hier `typeahead` stelt u de para meter in op `true` . In het antwoord ziet u dat de zoek service de query als gedeeltelijke query heeft geïnterpreteerd. Het antwoord bevat resultaten voor een automatisch voorgestelde query.

#### <a name="sample-query"></a>Voorbeeldquery

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Antwoord

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>Een URI coderen voor het verwerken van speciale tekens 

U moet de URI coderen voor het afhandelen van speciale tekens in het adres om Cross Street-adressen te vinden. Bekijk dit adres voor beeld: *1e stap & vakbonds straat Seattle*. Codeer hier het & teken ( `&` ) voordat u de aanvraag verzendt. 

Het is raadzaam om teken gegevens te coderen in een URI. In een URI Codeer u alle tekens met behulp van een procent teken ( `%` ) en een hexadecimale waarde van twee tekens die overeenkomt met de tekens UTF-8.

#### <a name="usage-examples"></a>Gebruiksvoorbeelden

Beginnen met dit adres:

```
query=1st Avenue & E 111th St, New York
```

Het adres coderen:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

U kunt de volgende methoden gebruiken.

Java script of type script:
```javascript
encodeURIComponent(query)
```

C# of Visual Basic:
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP
```PHP
urlencode(query)
```

Ruby
```Ruby
CGI::escape(query) 
```

Swift
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Gebleven
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Aanbevolen procedures voor het zoeken naar POI

In een POI-zoek opdracht kunt u POI-resultaten aanvragen op naam. U kunt bijvoorbeeld zoeken naar een bedrijf op naam. 

We raden u ten zeerste `countrySet` aan de para meter te gebruiken om landen/regio's op te geven waar uw toepassing behoefte heeft. Het standaard gedrag is het doorzoeken van de hele wereld. Deze brede zoek opdracht kan onnodig resultaten retour neren en de zoek opdracht kan enige tijd duren.

### <a name="brand-search"></a>Merk zoeken

Ter verbetering van de relevantie van de resultaten en de informatie in het antwoord, bevat een POI Zoek antwoord merk gegevens. U kunt deze informatie gebruiken om het antwoord verder te parseren.

In een aanvraag kunt u een door komma's gescheiden lijst met merk namen verzenden. Gebruik de lijst om de resultaten te beperken tot specifieke merken door de `brandSet` para meter in te stellen. In uw lijst is de volg orde van items niet van belang. Wanneer u meerdere merk lijsten opgeeft, moeten de geretourneerde resultaten tot ten minste één van de lijsten behoren.

We gaan een zoek aanvraag voor een [POI categorie](/rest/api/maps/search/getsearchpoicategory) maken om het zoeken naar merken te verkennen. In het volgende voor beeld zoeken we naar benzine stations in de buurt van micro soft campus in Redmond, Washington. In het antwoord worden de merk gegevens weer gegeven voor elk POI dat is geretourneerd.

#### <a name="sample-query"></a>Voorbeeldquery

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Antwoord

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Vlieg veld zoeken

Met de Search POI-API kunt u op lucht havens zoeken met behulp van hun officiële code. U kunt bijvoorbeeld *Sea* gebruiken om de Seattle-Tacoma internationale lucht haven te vinden: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>In de buurt zoeken

Als u POI resultaten rond een specifieke locatie wilt ophalen, kunt u proberen de [Zoek opdracht](/rest/api/maps/search/getsearchnearby)in de buurt te gebruiken. Het eind punt retourneert alleen POI resultaten. De zoek opdracht bevat geen query parameter. 

Als u de resultaten wilt beperken, raden we u aan om de RADIUS in te stellen.

## <a name="understanding-the-responses"></a>Meer informatie over de antwoorden

We gaan een adres vinden in Seattle door een aanvraag voor het zoeken naar het Azure Maps Search Service te maken. In de volgende aanvraag-URL stellen we de `countrySet` para meter in op `US` om te zoeken naar het adres in de Verenigde Staten.

### <a name="sample-query"></a>Voorbeeldquery

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Ondersteunde typen resultaten

* **Punt adres**: punten op een kaart die een specifiek adres met een straat naam en nummer hebben. Punt adres biedt het hoogste nauwkeurigheids niveau voor adressen. 

* **Adres bereik**: het adres bereik dat wordt geïnterpoleerd vanaf het begin en het einde van de straat.  

* **Geografie**: gebieden op een kaart die administratieve divisies van een land vertegenwoordigen, bijvoorbeeld land/regio, staat of plaats. 

* **POI**: punten op een kaart die aandacht best Eden en die mogelijk interessant zijn.

* **Street**: straten op de kaart. Adressen worden omgezet naar de breedte-en lengte coördinaten van de straat die het adres bevat. Het huis nummer wordt mogelijk niet verwerkt. 

* **Kruis straat**: snij punten. Kruis straten vertegenwoordigen verbindings lijnen waarbij twee straten INTERSECT.

### <a name="response"></a>Antwoord

Laten we eens kijken naar de structuur van de reactie. In het antwoord dat volgt, zijn de typen resultaat objecten verschillend. Als u zorgvuldig zoekt, ziet u drie typen resultaat objecten:

* Punt adres
* Straat
* Kruis adres

U ziet dat de adres zoekopdracht geen Poi's retourneert.  

De `Score` para meter voor elk reactie object geeft aan hoe de overeenkomende Score in verhouding staat tot de scores van andere objecten in hetzelfde antwoord. Zie [Zoek adres ophalen](/rest/api/maps/search/getsearchaddress)voor meer informatie over de para meters voor reactie objecten.

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometrie

Een antwoord type van *geometrie* kan de geometrie-id bevatten die wordt geretourneerd in het `dataSources` object onder `geometry` en `id` . U kunt bijvoorbeeld de [Search veelhoek-service](/rest/api/maps/search/getsearchpolygon) gebruiken om de geometrie gegevens aan te vragen in een geojson-indeling. Met deze indeling kunt u een overzicht van de stad of de lucht haven voor een set entiteiten ophalen. U kunt deze grens gegevens vervolgens gebruiken voor het [instellen van een geofence](./tutorial-geofence.md) of [het zoeken naar poi's binnen de geometrie](/rest/api/maps/search/postsearchinsidegeometry).


Reacties voor de API voor [Zoek adressen](/rest/api/maps/search/getsearchaddress) of de [Zoek fuzzy](/rest/api/maps/search/getsearchfuzzy) -API kunnen de geometrie-id bevatten die wordt geretourneerd in het `dataSources` object onder `geometry` en `id` :


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie naar:

> [!div class="nextstepaction"]
> [Azure Maps Search Service-aanvragen samen stellen](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Documentatie over Search Service-API](/rest/api/maps/search)