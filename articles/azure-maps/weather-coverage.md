---
title: Dekking van Microsoft Azure Maps-Services (preview)
description: Meer informatie over de dekking van Microsoft Azure Maps-Services (preview)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: references_regions
manager: philmea
ms.openlocfilehash: 6c4e9eb765a72b7a0b495f81a954b484ef6aa2b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905482"
---
# <a name="azure-maps-weather-services-preview-coverage"></a>Dekking van Azure Maps weer Services (preview)

> [!IMPORTANT]
> Azure Maps Weather-services zijn momenteel beschikbaar in de openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


In dit artikel vindt u informatie over de dekking van Azure Maps [weer Services](/rest/api/maps/weather). Azure Maps weer gegevens Services retourneert details zoals radar tegels, huidige weers omstandigheden, weers verwachtingen en het weers bericht langs een route.

Azure Maps heeft niet hetzelfde niveau van informatie en nauw keurigheid voor alle landen en regio's.

De volgende tabel bevat informatie over wat voor soort informatie u kunt aanvragen van elk land/elke regio.

| Symbool | Betekenis |
|--------|---------|
|*       |Behandelt de huidige voor waarden, uurprognose per uur, prognose van kwartaal dagen, dagelijkse prognose, weers gerichte route en dagelijkse indices. |


## <a name="americas"></a>Noord- en Zuid-Amerika

| Land/regio              |  Satelliet tegels | Minuut prognose, radar tegels | Ernstige weer Meldingen | Daarenteg |
|-----------------------------|:----------------:|:-----------------:|:-----------------:|:--------:|  
| Anguilla                                 | ✓ |   | |  ✓| 
| Antarctica                               | ✓ |   | |✓|
| Antigua en Barbuda                      | ✓ |   | |✓| 
| Argentinië                                | ✓ |   | |✓| 
| Aruba                                    | ✓ |   | |✓| 
| Bahama's                                  | ✓ |   | |✓| 
| Barbados                                 | ✓ |   | |✓| 
| Belize                                   | ✓ |   | |✓| 
| Bermuda                                  | ✓ |   | |✓| 
| Bolivia                                  | ✓ |   | |✓| 
| Bonaire                                  | ✓ |   | |✓| 
| Brazilië                                   | ✓ |   | ✓ |✓| 
| Britse Maagdeneilanden                   | ✓ |   | |✓| 
| Canada                                   | ✓ | ✓ | ✓ | ✓| 
| Kaaimaneilanden                           | ✓ |   | |✓| 
| Chili                                    | ✓ |   | |✓| 
| Colombia                                 | ✓ |   | |✓| 
| Costa Rica                               | ✓ |   | |✓| 
| Cuba                                     | ✓ |   | |✓| 
| Curaçao                                  | ✓ |   | |✓| 
| Dominica                                 | ✓ |   | |✓| 
| Dominicaanse Republiek                       | ✓ |   | |✓| 
| Ecuador                                  | ✓ |   | |✓| 
| El Salvador                              | ✓ |   | |✓| 
| Falklandeilanden                         | ✓ |   | |✓| 
| Frans-Guyana                            | ✓ |   | |✓| 
| Groenland                                | ✓ |   | |✓| 
| Grenada                                  | ✓ |   | |✓| 
| Guadeloupe                               | ✓ |   | |✓| 
| Guatemala                                | ✓ |   | |✓| 
| Guyana                                   | ✓ |   | |✓| 
| Haïti                                    | ✓ |   | |✓| 
| Honduras                                 | ✓ |   | |✓| 
| Jamaica                                  | ✓ |   | |✓| 
| Martinique                               | ✓ |   | |✓| 
| Mexico                                   | ✓ |   | |✓| 
| Montserrat                               | ✓ |   | |✓| 
| Nicaragua                                | ✓ |   | |✓| 
| Panama                                   | ✓ |   | |✓| 
| Paraguay                                 | ✓ |   | |✓| 
| Peru                                     | ✓ |   | |✓| 
| Puerto Rico                              | ✓ |   | ✓ |✓| 
| Saint Barthélemy                         | ✓ |   | |✓| 
| Saint Kitts en Nevis                    | ✓ |   | |✓| 
| Saint Lucia                              | ✓ |   | |✓| 
| Saint-Martin                             | ✓ |   | |✓| 
| Saint-Pierre en Miquelon                | ✓ |   | |✓| 
| Saint Vincent en de Grenadines         | ✓ |   | |✓| 
| Sint Eustatius                           | ✓ |   | |✓|  
| Sint Maarten                             | ✓ |   | |✓| 
| Zuid-Georgië en de Zuidelijke Sandwicheilanden | ✓ |   | |✓| 
| Suriname                                 | ✓ |   | |✓| 
| Trinidad en Tobago                      | ✓ |   | |✓| 
| Turks- en Caicos-eilanden                 | ✓ |   | |✓| 
| Amerikaanse ondergeschikte afgelegen eilanden                    | ✓ |   | |✓| 
| Amerikaanse Maagden eilanden                      | ✓ |   | ✓|✓| 
| Verenigde Staten                            | ✓ | ✓ | ✓| ✓| 
| Uruguay                                  | ✓ |   | |✓| 
| Venezuela                                | ✓ |   | |✓| 


## <a name="middle-east-and-africa"></a>Midden-Oosten en Afrika

| Land/regio              |  Satelliet tegels | Minuut prognose, radar tegels | Ernstige weer Meldingen | Daarenteg | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:| 
| Algerije                     | ✓               |                              |     |  ✓| 
| Angola                      | ✓               |                              |     |  ✓| 
| Bahrein                     | ✓               |                              |     |  ✓| 
| Benin                       | ✓               |                              |     |  ✓| 
| Botswana                    | ✓               |                              |     |  ✓| 
| Bouveteiland               | ✓               |                              |     |  ✓| 
| Burkina Faso                | ✓               |                              |     |  ✓| 
| Burundi                     | ✓               |                              |     |  ✓| 
| Kameroen                    | ✓               |                              |     |  ✓| 
| Cabo Verde                  | ✓               |                              |     |  ✓| 
| Centraal-Afrikaanse Republiek    | ✓               |                              |     |  ✓| 
| Tsjaad                        | ✓               |                              |     |  ✓| 
| Comoren                     | ✓               |                              |     |  ✓| 
| Congo (DRC)                 | ✓               |                              |     |  ✓|
| Cote d'Ivoire               | ✓               |                              |     |  ✓| 
| Djibouti                    | ✓               |                              |     |  ✓| 
| Egypte                       | ✓               |                              |     |  ✓| 
| Equatoriaal-Guinea           | ✓               |                              |     |  ✓| 
| Eritrea                     | ✓               |                              |     |  ✓| 
| eSwatini                    | ✓               |                              |     |  ✓| 
| Ethiopië                    | ✓               |                              |     |  ✓| 
| Franse Gebieden in de zuidelijke Indische Oceaan | ✓               |                              |     |  ✓| 
| Gabon                       | ✓               |                              |     |  ✓| 
| Gambia                      | ✓               |                              |     |  ✓| 
| Ghana                       | ✓               |                              |     |  ✓| 
| Guinee                      | ✓               |                              |     |  ✓| 
| Guinee-Bissau               | ✓               |                              |     |  ✓| 
| Iran                        | ✓               |                              |     |  ✓| 
| Irak                        | ✓               |                              |     |  ✓| 
| Israël                      | ✓               |                              |   ✓   |  ✓| 
| Jordanië                      | ✓               |                              |     |  ✓| 
| Kenia                       | ✓               |                              |     |  ✓| 
| Koeweit                      | ✓               |                              |     |  ✓| 
| Libanon                     | ✓               |                              |     |  ✓| 
| Lesotho                     | ✓               |                              |     |  ✓| 
| Liberia                     | ✓               |                              |     |  ✓| 
| Libië                       | ✓               |                              |     |  ✓| 
| Madagaskar                  | ✓               |                              |     |  ✓| 
| Malawi                      | ✓               |                              |     |  ✓| 
| Mali                        | ✓               |                              |     |  ✓| 
| Mauritanië                  | ✓               |                              |     |  ✓| 
| Mauritius                   | ✓               |                              |     |  ✓| 
| Mayotte                     | ✓               |                              |     |  ✓| 
| Marokko                     | ✓               |                              |     |  ✓| 
| Mozambique                  | ✓               |                              |     |  ✓| 
| Namibië                     | ✓               |                              |     |  ✓| 
| Niger                       | ✓               |                              |     |  ✓| 
| Nigeria                     | ✓               |                              |     |  ✓| 
| Oman                        | ✓               |                              |     |  ✓| 
| Palestijnse Autoriteit       | ✓               |                              |     |  ✓| 
| Qatar                       | ✓               |                              |     |  ✓| 
| Réunion                     | ✓               |                              |     |  ✓| 
| Rwanda                      | ✓               |                              |     |  ✓| 
| Sint-Helena, Ascension en Tristan da Cunha        | ✓               |            |     |  ✓| 
| Sao Tomé en principe       | ✓               |                              |     |  ✓| 
| Saoedi-Arabië                | ✓               |                              |     |  ✓| 
| Senegal                     | ✓               |                              |     |  ✓| 
| Seychellen                  | ✓               |                              |     |  ✓| 
| Sierra Leone                | ✓               |                              |     |  ✓| 
| Somalië                     | ✓               |                              |     |  ✓| 
| Zuid-Afrika                | ✓               |                              |     |  ✓| 
| Zuid-Soedan                 | ✓               |                              |     |  ✓| 
| Soedan                       | ✓               |                              |     |  ✓| 
| Syrië                       | ✓               |                              |     |  ✓| 
| Tanzania                    | ✓               |                              |     |  ✓| 
| Togo                        | ✓               |                              |     |  ✓| 
| Tunesië                     | ✓               |                              |     |  ✓| 
| Oeganda                      | ✓               |                              |     |  ✓| 
| Verenigde Arabische Emiraten        | ✓               |                              |     |  ✓| 
| Jemen                       | ✓               |                              |     |  ✓| 
| Zambia                      | ✓               |                              |     |  ✓| 
| Zimbabwe                    | ✓               |                              |     |  ✓| 


## <a name="asia-pacific"></a>Azië en Stille Oceaan

| Land/regio              |  Satelliet tegels | Minuut prognose, radar tegels | Ernstige weer Meldingen | Daarenteg |
|-----------------------------|:----------------:|:-----------------:|:--------:|  :--------:|
| Afghanistan                       | ✓ |   | | ✓| 
| Amerikaans-Samoa                    | ✓ |   |  ✓| ✓| 
| Australië                         | ✓ | ✓ |  ✓ | ✓| 
| Bangladesh                        | ✓ |   | | ✓| 
| Bhutan                            | ✓ |   | | ✓| 
| Brits Territorium in de Indische Oceaan    | ✓ |   | | ✓| 
| Brunei                            | ✓ |   | | ✓| 
| Cambodja                          | ✓ |   | | ✓| 
| China                             | ✓ | ✓ |  ✓ | ✓| 
| Christmaseiland                  | ✓ |   | | ✓| 
| Cocoseilanden           | ✓ |   | | ✓| 
| Cookeilanden                      | ✓ |   | | ✓| 
| Fiji                              | ✓ |   | | ✓| 
| Frans-Polynesië                  | ✓ |   | | ✓| 
| Guam                              | ✓ |   |  ✓| ✓| 
| Heard- en McDonald-eilanden | ✓ |   | | ✓| 
| Hongkong SAR                     | ✓ |   | | ✓| 
| India                             | ✓ |   | | ✓| 
| Indonesië                         | ✓ |   | | ✓| 
| Japan                             | ✓ | ✓ |  ✓| ✓| 
| Kazachstan                        | ✓ |   | | ✓| 
| Kiribati                          | ✓ |   | | ✓| 
| Korea                             | ✓ | ✓ | ✓ |  ✓| 
| Kirgistan                        | ✓ |   | | ✓| 
| Laos                              | ✓ |   | | ✓| 
| Macau SAR                         | ✓ |   | | ✓| 
| Maleisië                          | ✓ |   | | ✓| 
| Maldiven                          | ✓ |   | | ✓| 
| Marshalleilanden                  | ✓ |   | ✓ | ✓| 
| Micronesia                        | ✓ |   | ✓ | ✓| 
| Mongolië                          | ✓ |   | | ✓| 
| Myanmar                           | ✓ |   | | ✓| 
| Nauru                             | ✓ |   | | ✓| 
| Nepal                             | ✓ |   | | ✓| 
| Nieuw-Caledonië                     | ✓ |   | | ✓| 
| Nieuw-Zeeland                       | ✓ |   | ✓ | ✓| 
| Niue                              | ✓ |   | | ✓| 
| Norfolk                    | ✓ |   | | ✓| 
| Noord-Korea                       | ✓ |   | | ✓| 
| Noordelijke Marianen          | ✓ |   | ✓ | ✓| 
| Pakistan                          | ✓ |   | | ✓| 
| Palau                             | ✓ |   | ✓ | ✓| 
| Papoea-Nieuw-Guinea                  | ✓ |   | | ✓| 
| Filipijnen                       | ✓ |   | ✓ | ✓| 
| Pitcairneilanden                  | ✓ |   | | ✓| 
| Samoa                             | ✓ |   | | ✓| 
| Singapore                         | ✓ |   | | ✓| 
| Salomonseilanden                   | ✓ |   | | ✓| 
| Sri Lanka                         | ✓ |   | | ✓| 
| Taiwan                            | ✓ |   | | ✓| 
| Tadzjikistan                        | ✓ |   | | ✓| 
| Thailand                          | ✓ |   | | ✓| 
| Timor-Leste                       | ✓ |   | | ✓| 
| Tokelau                           | ✓ |   | | ✓| 
| Tonga                             | ✓ |   | | ✓| 
| Turkmenistan                      | ✓ |   | | ✓| 
| Tuvalu                            | ✓ |   | | ✓| 
| Oezbekistan                        | ✓ |   | | ✓| 
| Vanuatu                           | ✓ |   | | ✓| 
| Vietnam                           | ✓ |   | | ✓| 
| Wallis en Futuna                 | ✓ |   | | ✓| 


## <a name="europe"></a>Europa

| Land/regio              |  Satelliet tegels | Minuut prognose, radar tegels | Ernstige weer Meldingen | Daarenteg | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:|
| Albanië                | ✓ |   | | ✓| 
| Andorra                | ✓ |   | ✓ | ✓| 
| Armenië                | ✓ |   | | ✓| 
| Oostenrijk                | ✓ | ✓ | ✓ | ✓|
| Azerbeidzjan             | ✓ |   | | ✓| 
| Belarus                | ✓ |   | | ✓| 
| België                | ✓ | ✓ |  ✓| ✓| 
| Bosnië en Herzegovina | ✓ | ✓ | ✓ | ✓| 
| Bulgarije               | ✓ |   |  ✓| ✓| 
| Kroatië                | ✓ | ✓ |  ✓| ✓| 
| Cyprus                 | ✓ |   | ✓ | ✓| 
| Czechia                | ✓ | ✓ | ✓ | ✓| 
| Denemarken                | ✓ | ✓ | ✓ | ✓| 
| Estland                | ✓ | ✓ |  ✓| ✓| 
| Faröer          | ✓ |   | | ✓| 
| Finland                | ✓ | ✓ | ✓ | ✓| 
| Frankrijk                 | ✓ | ✓ | ✓ | ✓| 
| Georgië                | ✓ |   | | ✓| 
| Duitsland                | ✓ | ✓ | ✓ | ✓| 
| Gibraltar              | ✓ | ✓ | | ✓| 
| Griekenland                 | ✓ |   |  ✓| ✓| 
| Guernsey               | ✓ |   | | ✓| 
| Hongarije                | ✓ | ✓ |  ✓| ✓| 
| IJsland                | ✓ |   | ✓ | ✓| 
| Ierland                | ✓ | ✓ |  ✓| ✓| 
| Italië                  | ✓ |   |  ✓| ✓|
| Isle of Man            | ✓ |   | | ✓| 
| Jan Mayen              | ✓ |   | | ✓| 
| Jersey                 | ✓ |   | | ✓| 
| Kosovo                 | ✓ |   |  ✓| ✓| 
| Letland                 | ✓ |   | ✓ | ✓| 
| Liechtenstein          | ✓ | ✓ |  ✓| ✓| 
| Litouwen              | ✓ |   | ✓ | ✓| 
| Luxemburg             | ✓ | ✓ |  ✓| ✓| 
| Noord-Macedonië        | ✓ |   | ✓ | ✓| 
| Malta                  | ✓ |   | ✓ | ✓| 
| Moldavië                | ✓ | ✓ | ✓ | ✓| 
| Monaco                 | ✓ | ✓ |  ✓| ✓| 
| Montenegro             | ✓ | ✓ |  ✓| ✓| 
| Nederland            | ✓ | ✓ |  ✓| ✓| 
| Noorwegen                 | ✓ | ✓ |  ✓| ✓| 
| Polen                 | ✓ | ✓ |  ✓| ✓| 
| Portugal               | ✓ | ✓ |  ✓| ✓| 
| Roemenië                | ✓ | ✓ |  ✓| ✓| 
| Rusland                 | ✓ |   |  ✓| ✓| 
| San Marino             | ✓ |   |  ✓| ✓| 
| Servië                 | ✓ | ✓ |  ✓| ✓| 
| Slowakije               | ✓ | ✓ |  ✓| ✓| 
| Slovenië               | ✓ | ✓ |  ✓| ✓| 
| Spanje                  | ✓ | ✓ |  ✓| ✓| 
| Svalbard               | ✓ |   | | ✓|
| Zweden                 | ✓ | ✓ |  ✓| ✓| 
| Zwitserland            | ✓ | ✓ | ✓| ✓| 
| Turkije                 | ✓ |   | | ✓| 
| Oekraïne                | ✓ |   | | ✓| 
| Verenigd Koninkrijk         | ✓ | ✓ | ✓| ✓| 
| Vaticaanstad           | ✓ |   |✓ | ✓|