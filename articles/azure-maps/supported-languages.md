---
title: Ondersteuning voor lokalisatie met Microsoft Azure Maps
description: Zie welke regio's Azure Maps ondersteunt met services zoals kaarten, zoek functies, route ring, weer en verkeers incidenten. Meer informatie over het instellen van de weergave parameter.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 50e5d0721eb14d1fcdfad26aaf081bfa370e954e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904513"
---
# <a name="localization-support-in-azure-maps"></a>Ondersteuning voor lokalisatie in Azure Maps

Azure Maps ondersteunt diverse talen en weer gaven op basis van land/regio. In dit artikel vindt u de ondersteunde talen en weer gaven om uw Azure Maps-implementatie te begeleiden.


## <a name="azure-maps-supported-languages"></a>Azure Maps ondersteunde talen

Azure Maps zijn gelokaliseerd in de verschillende talen van alle services. De volgende tabel bevat de ondersteunde taal codes voor elke service.  
  

| Id         | Name                   |  Maps | Zoeken | Routering | Weer | Verkeers incidenten | JS-toewijzings beheer |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabisch                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bengaals (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bengaals (India)         |       |       |         |     ✓    |                   |                |
| bs-BA      | Bosnisch                 |       |       |         |     ✓    |                   |                |
| EU-ES      | Baskisch                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bulgaars              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Catalaans                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chinees (Vereenvoudigd)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chinees (Hongkong SAR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chinees (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Kroatisch               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Tsjechisch                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Deens                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-worden      | Nederlands (België)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Nederlands (Nederland)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | Engels (Australië)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | Engels (Nieuw-Zeeland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Engels (Groot-Brittannië) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-US      | Engels (Verenigde Staten)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Ests               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Fins                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Frans                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Frans (Canada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galicisch               |       |    ✓   |         |         |                   |                |
| de-DE      | Duits                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Grieks                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| Gu-IN      | Gujarati                |       |       |         |     ✓    |                   |                |
| he IL      | Hebreeuws                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Hongaars              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | IJslands              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonesisch             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italiaans                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japans               |       |        |         |     ✓    |                   |                |
| KN-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazachs                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreaans                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Latijns-Amerikaans-Spaans |       |    ✓   |         |         |                   |                |
| lv-LV      | Lets                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litouws             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| MK-MK      | Macedonisch             |       |       |         |     ✓    |                   |                |
| ms-MY      | Maleis (Latijns)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| Mr-IN      | Mahrati                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Noors - Bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutrale waarheid-officiële talen voor alle regio's in lokale scripts indien beschikbaar |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutrale waarheid-Latijns exonyms. Latijns script wordt gebruikt indien beschikbaar |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Pools                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugees (Brazilië)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugees (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA-IN      | Punjabi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Roemeens               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russisch                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Servisch (Cyrillisch)     |       |   SR-RS  |         |    SR-RS     |                   |                |
| sr-Latn-RS | Servisch (Latijns)        |       |       |         |     sr-Latn    |                   |                |
| sk-SK      | Slowaaks             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| SL-SL      | Sloveens              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Spaans                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Spaans (Mexico)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Zweeds                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | Tamil (India)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (India)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thai                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turks                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Oekraïens               |       |    ✓   |         |     ✓    |                   |                |
| uw-PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Oezbeeks                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamees             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Ondersteunde weer gaven Azure Maps

> [!Note]
> Op 1 augustus 2019 is Azure Maps uitgebracht in de volgende landen/regio's:
>  * Argentinië
>  * India
>  * Marokko
>  * Pakistan
>
> Na 1 augustus 2019 wordt met de **weer gave** -para meter de geretourneerde kaart inhoud voor de hierboven genoemde nieuwe regio's/landen gedefinieerd. Azure Maps **weer gave** -para meter (ook wel ' para meter gebruikers regio ' genoemd) is een ISO-3166-land code van twee letters waarmee de juiste toewijzingen voor dat land of deze regio worden weer gegeven waarmee wordt aangegeven welke set van geopolitieke inhoud wordt geretourneerd via Azure Maps Services, met inbegrip van randen en labels die op de kaart worden weer gegeven. 

Zorg ervoor dat u de **weer gave** -para meter hebt ingesteld zoals vereist voor de rest api's en de sdk's, die door uw services worden gebruikt.
  

### <a name="rest-apis"></a>Rest-Api's
  
Zorg ervoor dat u de weer gave-para meter hebt ingesteld zoals vereist. Met de weer gave-para meter geeft u op welke set van geopolitieke, betwiste inhoud wordt geretourneerd via Azure Maps Services. 

Betrokken Azure Maps REST-services:
    
 * Kaart tegel ophalen
 * Kaart afbeelding ophalen 
 * Zoek actie ophalen
 * Zoek POI ophalen
 * Categorie Zoek POI ophalen
 * Zoek in de buurt ophalen
 * Zoek adres ophalen
 * Gestructureerd Zoek adres ophalen
 * Zoekadres omkeren
 * Adres omgekeerde cross-straat ophalen
 * Post-zoekopdracht binnen geometrie
 * Post Search-adres batch
 * Batch voor het terugdraaien van Zoek adressen
 * Zoek opdracht op route plaatsen
 * Zoeken in zoek actie batch

 
### <a name="sdks"></a>SDK's

Zorg ervoor dat u de **weer gave** -para meter hebt ingesteld als vereist en u de meest recente versie van de Web-SDK en ANDROID-SDK hebt. Betrokken Sdk's:

 * Azure Maps Web-SDK
 * Azure Maps Android-SDK

De weer gave-para meter is standaard ingesteld op **Unified**, zelfs als u deze niet in de aanvraag hebt gedefinieerd. Bepaal de locatie van uw gebruikers. Stel vervolgens de **weer gave** -para meter op de juiste manier in voor die locatie. U kunt ook ' weer gave = automatisch ' instellen, waardoor de kaart gegevens worden geretourneerd op basis van het IP-adres van de aanvraag.  De **weer gave** -para meter in azure Maps moet worden gebruikt in overeenstemming met de toepasselijke wetgeving, waaronder die wetten over de toewijzing van het land/de regio waar kaarten, afbeeldingen en andere gegevens en inhoud van derden waartoe u toegang wilt krijgen via Azure Maps beschikbaar worden gesteld.


De volgende tabel bevat ondersteunde weer gaven.

| Weergave         | Beschrijving                            |  Maps | Zoeken | JS-Map Control |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Verenigde Arabische Emiraten (Arabische weer gave)    |   ✓   |        |     ✓          |
| AR           | Argentinië (Argentijnse weer gave)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (Arabische weer gave)                 |   ✓   |        |     ✓          |
| IN           | India (Indiase weer gave)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (Arabische weer gave)                    |   ✓   |        |     ✓          |
| JO           | Jordanië (Arabische weer gave)                  |   ✓   |        |     ✓          |
| KW           | Koeweit (Arabische weer gave)                  |   ✓   |        |     ✓          |
| LB           | Libanon (Arabische weer gave)                 |   ✓   |        |     ✓          |
| MA           | Marokko (Marokko-weer gave)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (Arabische weer gave)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (Pakistaanse-weer gave)              |   ✓   |    ✓    |     ✓          |
| PS           | Palestijnse autoriteit (Arabische weer gave)    |   ✓   |        |     ✓          |
| QA           | Qatar (Arabische weer gave)                   |   ✓   |        |     ✓          |
| SA           | Saudi-Arabië (Arabische weer gave)            |   ✓   |        |     ✓          |
| SY           | Syrië (Arabische weer gave)                   |   ✓   |        |     ✓          |
| TEZAM           | Jemen (Arabische weer gave)                   |   ✓   |        |     ✓          |
| Automatisch         | De kaart gegevens retour neren op basis van het IP-adres van de aanvraag.|   ✓   |    ✓   |     ✓          |
| Enkele      | Unified View (overige)                  |   ✓   |   ✓     |     ✓          |
