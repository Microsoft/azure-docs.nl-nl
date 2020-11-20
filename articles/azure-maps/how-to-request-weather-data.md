---
title: Real-time en geraamde weers gegevens aanvragen met behulp van Azure Maps weer Services
description: Meer informatie over het aanvragen van real-time (huidige) en geraamde (minuut, uur, dagelijks) weer gegeven gegevens met Microsoft Azure kaarten weer Services
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/27/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3e0dce10d582006ab8c1dabf6d4b3efc82d8f39f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957109"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services"></a>Real-time en geraamde weers gegevens aanvragen met behulp van Azure Maps weer Services

De Azure Maps- [weer service](https://docs.microsoft.com/rest/api/maps/weather) is een reeks rest api's waarmee ontwikkel aars zeer dynamische historische, realtime en geraamde weers gegevens en visualisaties in hun oplossingen kunnen integreren. In dit artikel wordt uitgelegd hoe u realtime en geraamde weers gegevens kunt opvragen.

In dit artikel leert u het volgende:

* Vraag real-time (huidige) gegevens weer met de [API voor het ophalen van huidige voor waarden](https://docs.microsoft.com/rest/api/maps/weather/getcurrentconditionspreview).
* Vraag hevige weers meldingen [aan met de API Get ingrijpende weer waarschuwingen](https://docs.microsoft.com/rest/api/maps/weather/getsevereweatheralertspreview).
* Vraag dagelijkse prognoses op met behulp van de [down load Daily Forecast API](https://docs.microsoft.com/rest/api/maps/weather/getdailyforecastpreview).
* Vraag per uur prognoses aan met de API voor het ophalen van een [uur](https://docs.microsoft.com/rest/api/maps/weather/gethourlyforecastpreview).
* Vraag minuut per minuut prognoses met behulp van de API voor het [ophalen van minuten](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview).

In deze video vindt u een overzicht van de weers-Services in de Azure Maps met voor beelden voor REST-oproep.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player" width="960" height="540" allowFullScreen frameBorder="0" title="Azure Maps weer Services voor ontwikkel aars-micro soft Channel 9 video"></iframe>

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel. Zie [Verificatie beheren in Azure Maps](./how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

    >[!IMPORTANT]
    >De prijs categorie voor het berekenen van een [minuut](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview) is vereist voor de sleutel van de prijslaag. Alle andere Api's vereisen een S0 prijs categorie sleutel.

In deze zelfstudie wordt gebruikgemaakt van de [Postman](https://www.postman.com/)-toepassing, maar u kunt ook een andere API-ontwikkelomgeving kiezen.

## <a name="request-real-time-weather-data"></a>Real-time gegevens opvragen

De [API voor het ophalen van huidige voor waarden](https://docs.microsoft.com/rest/api/maps/weather/getcurrentconditionspreview) retourneert gedetailleerde weers omstandigheden, zoals precipitatie, Tempe ratuur en wind, voor een bepaalde coördinaten locatie. Ook kunnen waarnemingen van de afgelopen 6 of 24 uur voor een bepaalde locatie worden opgehaald. Het antwoord bevat details, zoals observatie datum en-tijd, een korte beschrijving van de weers omstandigheden, het pictogram weer, de indicator vlaggen voor precipitatie en de Tempe ratuur. RealFeel™ Tempe ratuur-en ultraviolet (UV)-index worden ook geretourneerd.

In dit voor beeld gebruikt u de [API Get huidige voor waarden](https://docs.microsoft.com/rest/api/maps/weather/getcurrentconditionspreview) om actuele weers omstandigheden op te halen die zich bevinden in Seattle, WA.

1. Open de Postman-app. Selecteer **New** (Nieuw) bovenaan de Postman-app. Selecteer **Collection** (Verzameling) in het venster **Create New** (Nieuwe maken).  Geef de verzameling een naam en selecteer de knop **Create** (Maken). U gebruikt deze verzameling voor de rest van de voor beelden in dit document.

2. Selecteer nogmaals **New** (Nieuw) om de aanvraag te maken. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt en selecteer **Save** (Opslaan).

3. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Klik op de knop voor blauwe **verzen ding** . De antwoord tekst bevat actuele weers informatie.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>Hevige weer Meldingen aanvragen

[Azure Maps ingrijpende weer waarschuwingen API](https://docs.microsoft.com/rest/api/maps/weather/getsevereweatheralertspreview) -bestand retourneert de ernstige weers waarschuwingen die wereld wijd beschikbaar zijn van zowel de officiële overheids instellingen als de wereld wijde waarschuwings providers voor regionale weers omstandigheden. De service kan Details retour neren zoals het type waarschuwing, categorie, niveau en gedetailleerde beschrijvingen van de actieve ernstige waarschuwingen voor de aangevraagde locatie, zoals orkanen, thunderstorms, bliksem, hitte golven of bosbranden. Logistiek managers kunnen bijvoorbeeld ernstige weers omstandigheden op een kaart, samen met bedrijfs locaties en geplande routes, visualiseren en verder coördineren met stuur Programma's en lokale werk rollen.

In dit voor beeld gebruikt u de [API Get ingrijpende waarschuwingen](https://docs.microsoft.com/rest/api/maps/weather/getsevereweatheralertspreview) om actuele weers omstandigheden op te halen die zich bevinden in CHEYENNE, WY.

>[!NOTE]
>In dit voor beeld worden ernstige weer Meldingen opgehaald op het moment van schrijven. Waarschijnlijk zijn er geen ernstige weer waarschuwingen meer op de aangevraagde locatie. Als u de werkelijke ernstige waarschuwings gegevens wilt ophalen wanneer u dit voor beeld uitvoert, moet u gegevens op een andere coördinaten locatie ophalen.

1. Open de Postman-app, klik op **Nieuw** en selecteer **aanvraag**. Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u hebt gemaakt in de vorige sectie of maak een nieuwe, en selecteer vervolgens **Opslaan**.

2. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klik op de knop voor blauwe **verzen ding** . Als er geen ernstige weer waarschuwingen zijn, bevat de antwoord tekst een lege `results[]` matrix. Als er sprake is van ernstige weer Meldingen, bevat de antwoord tekst iets zoals de volgende JSON-reactie:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>Dagelijkse weers prognose gegevens aanvragen

De [down load Daily Forecast API](https://docs.microsoft.com/rest/api/maps/weather/getdailyforecastpreview) retourneert gedetailleerde dagelijkse weers voorspelling zoals Tempe ratuur en wind. De aanvraag kan opgeven hoeveel dagen moeten worden geretourneerd: 1, 5, 10, 15, 25 of 45 dagen voor een bepaalde coördinaten locatie. Het antwoord bevat details zoals de Tempe ratuur, wind, precipitatie, lucht kwaliteit en UV-index.  In dit voor beeld vragen we vijf dagen per instelling `duration=5` .

>[!IMPORTANT]
>In de prijs categorie S0 kunt u een dagelijkse prognose aanvragen voor de volgende 1, 5, 10 en 15 dagen. In de prijs categorie S1 kunt u ook dagelijks een prognose aanvragen voor de volgende 25 dagen en 45 dagen.

In dit voor beeld gebruikt u de [down load Daily Forecast API](https://docs.microsoft.com/rest/api/maps/weather/getdailyforecastpreview) om de weers voorspelling van vijf dagen op te halen voor coördinaten in Seattle, WA.

1. Open de Postman-app, klik op **Nieuw** en selecteer **aanvraag**. Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u hebt gemaakt in de vorige sectie of maak een nieuwe, en selecteer vervolgens **Opslaan**.

2. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klik op de knop voor blauwe **verzen ding** . De antwoord tekst bevat de vijf dagen weers prognose gegevens. Voor het omwille van de boog is in het onderstaande JSON-antwoord de prognose voor de eerste dag weer gegeven.
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>Gegevens per uur voor weers pellen aanvragen

De [Forecast-API ophalen](https://docs.microsoft.com/rest/api/maps/weather/gethourlyforecastpreview) retourneert een gedetailleerde weers verwachting per uur voor de volgende 1, 12, 24 (1 dag), 72 (3 dagen), 120 (5 dagen) en 240 uur (10 dagen) voor de gegeven coördinaten locatie. De API retourneert details zoals Tempe ratuur, vochtigheids graad, wind, precipitatie en UV-index.

>[!IMPORTANT]
>In de prijs categorie S0 kunt u een prognose voor elk uur aanvragen voor de komende 1, 12, 24 uur (1 dag) en 72 uur (3 dagen). In de prijs categorie S1 kunt u ook een uurprognose aanvragen voor de volgende 120 (5 dagen) en 240 uur (10 dagen).

In dit voor beeld gebruikt u de [API ontvang per uur](https://docs.microsoft.com/rest/api/maps/weather/gethourlyforecastpreview) om de weers verwachting per uur op te halen voor de komende 12 uur op coördinaten in Seattle, WA.

1. Open de Postman-app, klik op **Nieuw** en selecteer **aanvraag**. Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u hebt gemaakt in de vorige sectie of maak een nieuwe, en selecteer vervolgens **Opslaan**.

2. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klik op de knop voor blauwe **verzen ding** . De antwoord tekst bevat weer gegeven voor de komende 12 uur. Voor het omwille van de boog beziet u in het onderstaande JSON-antwoord de prognose voor het eerste uur.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>Een weers verwachting van minuut per minuut om gegevens te voors pellen

 De voor waarde van de [prognose Get Minute](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview) retourneert een aantal minuten per minuut voor een bepaalde locatie voor de komende 120 minuten. Gebruikers kunnen weers verwachtingen aanvragen met intervallen van 1, 5 en 15 minuten. Het antwoord bevat details zoals het soort precipitatie (inclusief regen, sneeuw of een combi natie van beide), begin tijd en waarde voor precipitatie intensiteit (dBZ).

In dit voor beeld gebruikt u de [API Get Minute](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview) voor het ophalen van de weers verwachting per minuut op coördinaten in Seattle, WA. De weers voorspelling wordt de komende 120 minuten gegeven. In onze query wordt gevraag dat de prognose wordt weer gegeven met intervallen van 15 minuten, maar u kunt de para meter instellen op 1 of 5 minuten.

1. Open de Postman-app, klik op **Nieuw** en selecteer **aanvraag**. Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u hebt gemaakt in de vorige sectie of maak een nieuwe, en selecteer vervolgens **Opslaan**.

2. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Klik op de knop voor blauwe **verzen ding** . De antwoord tekst bevat weers verwachtingen voor de komende 120 minuten, in intervallen van 15 minuten.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Concepten van Azure Maps weer service](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts)

> [!div class="nextstepaction"]
> [Azure Maps weer service REST API](https://docs.microsoft.com/rest/api/maps/weather
)
