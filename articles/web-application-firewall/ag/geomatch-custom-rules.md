---
title: Aangepaste regels voor de geoovereenkomst van Azure Web Application firewall (WAF)
description: Dit artikel bevat een overzicht van WAF (Web Application firewall) die voldoet aan aangepaste regels op Azure-toepassing gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: bdb115cf6d0f0aa2c55f6143015a323df6faa114
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83744303"
---
# <a name="geomatch-custom-rules-preview"></a>Aangepaste regels voor geomatching (preview-versie)

Aangepaste regels bieden u de mogelijkheid om op maat gemaakte regels te maken voor de exacte behoeften van uw toepassingen en beveiligings beleid. Nu kunt u de toegang tot uw webtoepassingen beperken op basis van land/regio. Net als bij alle aangepaste regels kan deze logica worden samengesteld met andere regels om te voldoen aan de behoeften van uw toepassing.

Als u een aangepaste regel voor geografisch filteren wilt maken, selecteert u *geo-locatie* als het type overeenkomst en selecteert u vervolgens het land/de regio of landen/regio's die u wilt toestaan of blok keren vanuit uw toepassing. Zie [aangepaste regels maken in Power shell](configure-waf-custom-rules.md) en meer voor beelden van aangepaste regels (Create-Custom-waf-rules.MD) voor meer informatie.

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="countryregion-codes"></a>Land-/regiocodes

Als u de operator geomatch gebruikt, kunnen de selecters een van de volgende land-of regio codes van twee cijfers zijn. 

|Land-/regiocode | Naam van land/regio |
| ----- | ----- |
| AD | Andorra |
| AE | Verenigde Arabische Emiraten|
| AF | Afghanistan|
| AG | Antigua en Barbuda|
| AL | Albanië|
| AM | Armenië|
| AO | Angola|
| AR | Argentinië|
| AS | Amerikaans-Samoa|
| AT | Oostenrijk|
| AU | Australië|
| AZ | Azerbeidzjan|
| BA | Bosnië en Herzegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | België|
| BF | Burkina Faso|
| BG | Bulgarije|
| BH | Bahrein|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthélemy|
| BN | Brunei Darussalam|
| BO | Bolivia|
| BR | Brazilië|
| BS | Bahama's|
| BT | Bhutan|
| BW | Botswana|
| BY | Belarus|
| BZ | Belize|
| CA (consistentie en beschikbaarheid) | Canada|
| CD | Democratische Republiek Congo|
| CF | Centraal-Afrikaanse Republiek|
| CH | Zwitserland|
| CI | Cote d'Ivoire|
| CL | Chili|
| CM | Kameroen|
| CN | China|
| CO | Colombia|
| CR | Costa Rica|
| CU | Cuba|
| CV | Cabo Verde|
| CY | Cyprus|
| CZ | Tsjechische Republiek|
| DE | Duitsland|
| DK | Denemarken|
| DO | Dominicaanse Republiek|
| DZ | Algerije|
| EC | Ecuador|
| EE | Estland|
| EG | Egypte|
| ES | Spanje|
| ET | Ethiopië|
| FI | Finland|
| FJ | Fiji|
| FM | Micronesië, Federale Staten van|
| FR | Frankrijk|
| GB | Verenigd Koninkrijk|
| GE | Georgië|
| GF | Frans-Guyana|
| GH | Ghana|
| GN | Guinee|
| GP | Guadeloupe|
| GR | Griekenland|
| GT | Guatemala|
| GY | Guyana|
| HK | Hongkong SAR|
| HN | Honduras|
| HR | Kroatië|
| HT | Haïti|
| HU | Hongarije|
| Id | Indonesië|
| IE | Ierland|
| IL | Israël|
| IN | India|
| IQ | Irak|
| IR | Iran, Islamitische Republiek|
| IS | IJsland|
| IT | Italië|
| JM | Jamaica|
| JO | Jordanië|
| JP | Japan|
| KE | Kenia|
| KG | Kirgistan|
| KH | Cambodja|
| KI | Kiribati|
| KN | Saint Kitts en Nevis|
| KP | Noord-Korea|
| KR | Zuid-Korea|
| KW | Koeweit|
| KY | Kaaimaneilanden|
| KZ | Kazachstan|
| LA | Laos|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litouwen|
| LU | Luxemburg|
| LV | Letland|
| LY | Libië |
| MA | Marokko|
| MD | Moldavië, Republiek|
| MG | Madagaskar|
| MK | Noord-Macedonië|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolië|
| MO | Macau SAR|
| MQ | Martinique|
| MR | Mauritanië|
| MT | Malta|
| MV | Maldiven|
| MW | Malawi|
| MX | Mexico|
| MY | Maleisië|
| MZ | Mozambique|
| NA | Namibië|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Nederland|
| NO | Noorwegen|
| NP | Nepal|
| NR | Nauru|
| NZ | Nieuw-Zeeland|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Filipijnen|
| PK | Pakistan|
| PL | Polen|
| PR | Puerto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguay|
| QA | Qatar|
| RE | Réunion|
| RO | Roemenië|
| RS | Servië|
| RU | Russische Federatie|
| RW | Rwanda|
| SA | Saoedi-Arabië|
| SD | Soedan|
| SE | Zweden|
| SG | Singapore|
| SI | Slovenië|
| SK | Slowakije|
| SN | Senegal|
| SO | Somalië|
| SR | Suriname|
| SS | Zuid-Soedan|
| SV | El Salvador|
| SY | Arabische Republiek Syrië|
| SZ | Swaziland|
| TC | Turks- en Caicos-eilanden|
| TG | Togo|
| TH | Thailand|
| TN | Tunesië|
| TR | Turkije|
| TT | Trinidad en Tobago|
| TW | Taiwan|
| TZ | Tanzania, Verenigde Republiek|
| UA | Oekraïne|
| UG | Oeganda|
| VS | Verenigde Staten|
| UY | Uruguay|
| UZ | Oezbekistan|
| VC | Saint Vincent en de Grenadines|
| VE | Venezuela|
| VG | Britse Maagdeneilanden|
| VI | Amerikaanse Maagdeneilanden|
| VN | Vietnam|
| ZA | Zuid-Afrika|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Volgende stappen

Nadat u meer informatie over aangepaste regels hebt [gemaakt, maakt u uw eigen aangepaste regels](create-custom-waf-rules.md).
