---
title: De werking van caching | Microsoft Docs
description: Caching is het proces van het lokaal opslaan van gegevens zodat toekomstige aanvragen voor die gegevens sneller kunnen worden geopend.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: allensu
ms.openlocfilehash: a226682c2580a871e1b2fc4db71f369f3bcc3abb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010160"
---
# <a name="how-caching-works"></a>Hoe caching werkt

In dit artikel vindt u een overzicht van algemene cache concepten en hoe u in de cache van [Azure Content Delivery Network (CDN)](cdn-overview.md) caching kunt gebruiken om de prestaties te verbeteren. Als u meer wilt weten over het aanpassen van het cache gedrag voor uw CDN-eind punt, raadpleegt u [beheer Azure CDN caching met caching Rules](cdn-caching-rules.md) en [Control Azure CDN caching behavior with query strings](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Inleiding tot caching

Caching is het proces van het lokaal opslaan van gegevens zodat toekomstige aanvragen voor die gegevens sneller kunnen worden geopend. In het meest voorkomende type caching, webbrowser cache, slaat een webbrowser kopieën van statische gegevens lokaal op een lokale vaste schijf op. Door gebruik te maken van caching, kan de webbrowser voor komen dat er meerdere Retouren naar de server worden gemaakt en wordt in plaats daarvan lokaal toegang tot dezelfde gegevens gegeven, waardoor tijd en resources worden bespaard. Caching is heel geschikt voor het lokaal beheren van kleine, statische gegevens zoals statische afbeeldingen, CSS-bestanden en Java script-bestanden.

Op dezelfde manier wordt caching gebruikt door een Content Delivery Network op Edge-servers dicht bij de gebruiker om te voor komen dat aanvragen worden teruggestuurd naar de oorsprong en de latentie van eind gebruikers te verminderen. In tegens telling tot een webbrowser cache, die alleen voor één gebruiker wordt gebruikt, heeft het CDN een gedeelde cache. In een gedeelde CDN-cache is een bestand dat door een gebruiker is aangevraagd, later toegankelijk door andere gebruikers, waardoor het aantal aanvragen voor de oorspronkelijke server aanzienlijk wordt verkleind.

Dynamische bronnen die regel matig worden gewijzigd of die uniek zijn voor een afzonderlijke gebruiker, kunnen niet in de cache worden opgeslagen. Deze typen resources kunnen echter profiteren van de optimalisatie van dynamische site versnelling (DSA) op de Azure-Content Delivery Network voor betere prestaties.

Caching kan plaatsvinden op meerdere niveaus tussen de oorspronkelijke server en de eind gebruiker:

- Webserver: gebruikt een gedeelde cache (voor meerdere gebruikers).
- Content Delivery Network: maakt gebruik van een gedeelde cache (voor meerdere gebruikers).
- Internet serviceprovider (ISP): maakt gebruik van een gedeelde cache (voor meerdere gebruikers).
- Webbrowser: maakt gebruik van een persoonlijke cache (voor één gebruiker).

Elke cache beheert de eigen bron versheid en voert een validatie uit wanneer een bestand is verouderd. Dit gedrag wordt gedefinieerd in de HTTP-cache specificatie [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Bron versheid

Omdat een bron in de cache mogelijk verouderd is of verouderd is (vergeleken met de bijbehorende resource op de bron server), is het belang rijk om een cache mechanisme te gebruiken om te bepalen wanneer de inhoud wordt vernieuwd. Om tijd-en bandbreedte gebruik te besparen, wordt een resource in de cache niet vergeleken met de versie op de oorspronkelijke server telkens wanneer deze wordt geopend. In plaats daarvan wordt ervan uitgegaan dat een resource in de cache de meest recente versie is en rechtstreeks naar de client wordt verzonden. Een resource in de cache wordt beschouwd als vers wanneer de leeftijd kleiner is dan de leeftijd of periode die is gedefinieerd door een cache-instelling. Wanneer bijvoorbeeld een browser een webpagina opnieuw laadt, wordt gecontroleerd of elke bron in de cache op de vaste schijf is vernieuwd en wordt geladen. Als de resource niet nieuw is (verouderd), wordt een bijgewerkte kopie van de server geladen.

### <a name="validation"></a>Validatie

Als een resource als verouderd wordt beschouwd, wordt de oorspronkelijke server gevraagd deze te valideren. zo weet u of de gegevens in de cache nog steeds overeenkomen met wat er op de oorspronkelijke server staat. Als het bestand op de oorspronkelijke server is gewijzigd, wordt de versie van de resource door de cache bijgewerkt. Als de resource echter nieuw is, worden de gegevens rechtstreeks vanuit de cache bezorgd zonder eerst te valideren.

### <a name="cdn-caching"></a>CDN-caching

Caching is integraal naar de manier waarop een CDN kan worden geleverd om de levering te versnellen en de belasting van statische activa zoals afbeeldingen, letter typen en Video's te verminderen. In CDN-caching worden statische resources selectief opgeslagen op strategisch geplaatste servers die meer lokaal zijn voor een gebruiker en bieden de volgende voor delen:

- Omdat het meeste webverkeer statisch is (bijvoorbeeld installatie kopieën, letter typen en Video's), vermindert CDN-caching de netwerk latentie door inhoud dichter naar de gebruiker te verplaatsen, waardoor de afstand die gegevens worden verplaatst, wordt verminderd.

- Door het offloaden van een werk naar een CDN kan het netwerk verkeer en de belasting van de oorspronkelijke server verminderen. Hierdoor worden de kosten en de resource vereisten voor de toepassing verminderd, zelfs wanneer er grote aantallen gebruikers zijn.

Net als in de manier waarop caching wordt geïmplementeerd in een webbrowser, kunt u bepalen hoe caching in een CDN wordt uitgevoerd door cache-instructie headers te verzenden. Cache-instructie headers zijn HTTP-headers, die meestal worden toegevoegd door de oorspronkelijke server. Hoewel de meeste van deze headers oorspronkelijk zijn ontworpen om caching in client browsers aan te pakken, worden ze nu ook gebruikt door alle tussenliggende caches, zoals Cdn's. 

Twee kopteksten kunnen worden gebruikt voor het definiëren van cache versheid: `Cache-Control` en `Expires` . `Cache-Control` is meer actueel en heeft voor rang op `Expires` , als beide bestaan. Er worden ook twee typen kopteksten gebruikt voor validatie (met de naam Validators): `ETag` en `Last-Modified` . `ETag` is recurrent en heeft voor rang op `Last-Modified` , als beide zijn gedefinieerd.  

## <a name="cache-directive-headers"></a>Cache-instructie headers

> [!IMPORTANT]
> Een Azure CDN-eind punt dat is geoptimaliseerd voor DSA, negeert standaard cache-instructie headers en bypass caching. Voor **Azure CDN Standard van Verizon** en **Azure CDN Standard van Akamai** -profielen, kunt u aanpassen hoe een Azure CDN-eind punt deze headers behandelt door gebruik te maken van [regels voor CDN-caching](cdn-caching-rules.md) om caching in te scha kelen. Voor **Azure CDN Premium van Verizon** -profielen gebruikt u de [regel engine](./cdn-verizon-premium-rules-engine.md) om caching in te scha kelen.

Azure CDN ondersteunt de volgende HTTP-cache-instructie headers, waarmee de cache duur en het delen van de cache worden gedefinieerd.

**Cache-Control:**
- Geïntroduceerd in HTTP 1,1 om webpublicaties meer controle te geven over de inhoud en om de beperkingen van de header te kunnen aanpakken `Expires` .
- Hiermee wordt de header overschreven `Expires` als beide en `Cache-Control` worden gedefinieerd.
- Bij gebruik in een HTTP-aanvraag van de client naar de CDN-POP, `Cache-Control` wordt standaard door alle Azure CDN profielen genegeerd.
- Bij gebruik in een HTTP-antwoord van de client naar de CDN-POP:
     - **Azure CDN Standard/Premium van Verizon** en **Azure CDN Standard van micro soft** ondersteunen alle `Cache-Control` richt lijnen.
     - **Azure CDN standaard van Akamai** ondersteunt alleen de volgende `Cache-Control` richt lijnen. alle andere worden genegeerd:
         - `max-age`: Een cache kan de inhoud voor het opgegeven aantal seconden opslaan. Bijvoorbeeld `Cache-Control: max-age=5`. Deze richt lijn geeft de maximale hoeveelheid tijd aan die de inhoud als vernieuwd wordt beschouwd.
         - `no-cache`: De inhoud in de cache opslaan, maar de inhoud elke keer valideren voordat deze vanuit de cache wordt afgeleverd. Gelijk aan `Cache-Control: max-age=0` .
         - `no-store`: De inhoud nooit in de cache opslaan. Verwijder inhoud als deze eerder is opgeslagen.

**Verstreken**
- Verouderde header geïntroduceerd in HTTP 1,0; ondersteund voor achterwaartse compatibiliteit.
- Maakt gebruik van een verval tijd op basis van een datum met de tweede precisie. 
- Vergelijkbaar met `Cache-Control: max-age` .
- Wordt gebruikt wanneer `Cache-Control` niet bestaat.

**Pragma**
   - Standaard niet gehonoreerd door Azure CDN.
   - Verouderde header geïntroduceerd in HTTP 1,0; ondersteund voor achterwaartse compatibiliteit.
   - Wordt gebruikt als een aanvraag header van de client met de volgende instructie: `no-cache` . Met deze instructie wordt de server geïnstrueerd een nieuwe versie van de resource te leveren.
   - `Pragma: no-cache` is het equivalent van `Cache-Control: no-cache`.

## <a name="validators"></a>Controles

Wanneer de cache verouderd is, worden validaties van de HTTP-cache gebruikt voor het vergelijken van de versie van een bestand in de cache met de versie op de oorspronkelijke server. **Azure CDN Standard/Premium van Verizon** ondersteunt zowel `ETag` als `Last-Modified` validatie functie standaard, terwijl **Azure CDN standaard van micro soft** en **Azure CDN standaard van Akamai** alleen worden ondersteund `Last-Modified` .

**ETAG**
- **Azure CDN Standard/Premium van Verizon** ondersteunt `ETag` standaard, terwijl **Azure CDN standaard van micro soft** en **Azure CDN Standard van Akamai** niet.
- `ETag` Hiermee wordt een teken reeks gedefinieerd die uniek is voor elk bestand en elke versie van een bestand. Bijvoorbeeld `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Is geïntroduceerd in HTTP 1,1 en is meer actueel dan `Last-Modified` . Handig wanneer de datum van laatste wijziging moeilijk te bepalen is.
- Ondersteunt zowel sterke validatie als zwakke validatie; Azure CDN ondersteunt echter alleen sterke validatie. Voor een sterke validatie moeten de twee resource representaties byte-voor-byte identiek zijn. 
- Een cache valideert een bestand dat wordt gebruikt `ETag` door een `If-None-Match` header met een of meer `ETag` validatie functies in de aanvraag te verzenden. Bijvoorbeeld `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Als de versie van de server overeenkomt met een `ETag` validatie functie in de lijst, verzendt deze de status code 304 (niet gewijzigd) in de reactie. Als de versie anders is, reageert de server met de status code 200 (OK) en de bijgewerkte resource.

**Laatst gewijzigd:**
- Alleen voor **Azure CDN Standard/Premium van Verizon** `Last-Modified` wordt gebruikt als `ETag` dit geen onderdeel is van het HTTP-antwoord. 
- Hiermee geeft u de datum en tijd op waarop de oorspronkelijke server heeft bepaald dat de bron voor het laatst is gewijzigd. Bijvoorbeeld `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Een cache valideert een bestand met behulp `Last-Modified` van door een `If-Modified-Since` header met een datum en tijd in de aanvraag te verzenden. De oorspronkelijke server vergelijkt die datum met de `Last-Modified` koptekst van de laatste resource. Als de resource sinds de opgegeven tijd niet is gewijzigd, retourneert de server de status code 304 (niet gewijzigd) in het antwoord. Als de resource is gewijzigd, retourneert de server status code 200 (OK) en de bijgewerkte resource.

## <a name="determining-which-files-can-be-cached"></a>Bepalen welke bestanden in de cache kunnen worden opgeslagen

Niet alle resources kunnen in de cache worden opgeslagen. In de volgende tabel ziet u welke bronnen kunnen worden opgeslagen in de cache, op basis van het type HTTP-antwoord. Resources die niet aan al deze voor waarden voldoen, kunnen niet in de cache worden opgeslagen. Voor **Azure CDN Premium van Verizon** kunt u de regel engine gebruiken om enkele van deze voor waarden aan te passen.

|                       | Azure CDN van micro soft          | Azure CDN van Verizon | Azure CDN van Akamai        |
|-----------------------|-----------------------------------|------------------------|------------------------------|
| **HTTP-statuscode** | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| **HTTP-methoden**      | GET, HEAD                         | GET                    | GET                          |
| **Maximale bestands grootte**  | 300 GB                            | 300 GB                 | -Algemene optimalisatie van webleveringen: 1,8 GB<br />-Optimalisatie van mediastreaming: 1,8 GB<br />-Optimalisatie van grote bestanden: 150 GB |

Voor **Azure CDN standaard van micro soft** caching om aan een resource te werken, moet de oorspronkelijke server een kop ondersteunen en HTTP-aanvragen ontvangen en moeten de waarden voor de inhouds lengte hetzelfde zijn voor elke kop en HTTP-antwoorden voor de Asset ontvangen. Voor een HEAD-aanvraag moet de oorspronkelijke server de HEAD-aanvraag ondersteunen en moet deze reageren met dezelfde headers alsof er een GET-aanvraag is ontvangen.

## <a name="default-caching-behavior"></a>Standaard gedrag bij cache

In de volgende tabel wordt het standaard gedrag voor caching voor de Azure CDN producten en hun optimalisaties beschreven.

|    | Micro soft: algemene Internet levering | Verizon: algemene Internet levering | Verizon: DSA | Akamai: algemene Internet levering | Akamai: DSA | Akamai: grote bestanden downloaden | Akamai: algemeen of VOD mediastreaming |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Naleven**       | Ja    | Ja   | Nee   | Ja    | Nee   | Ja   | Ja    |
| **CDN-cache duur** | 2 dagen |7 dagen | Geen | 7 dagen | Geen | 1 dag | 1 jaar |

Nagaan van de **oorsprong**: Hiermee geeft u aan of de ondersteunde cache-instructie headers moeten worden nageleefd als deze bestaan in het HTTP-antwoord van de oorspronkelijke server.

**CDN-cache duur**: Hiermee geeft u de hoeveelheid tijd op waarvoor een resource in de cache wordt opgeslagen op de Azure CDN. Als navraag van de **oorsprong** Ja is en het HTTP-antwoord van de oorspronkelijke server de header van de cache-instructie bevat `Expires` of `Cache-Control: max-age` , Azure CDN gebruikt de duur waarde die door de header is opgegeven. 

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het aanpassen en negeren van het standaard gedrag bij het opslaan van caches in het CDN door de regels voor caching regelt u het [gedrag van Azure CDN caching met cache regels](cdn-caching-rules.md). 
- Zie voor meer informatie over het gebruik van query reeksen voor het beheren [van cache gedrag Control-Azure CDN in de cache opslaan met query reeksen](cdn-query-string.md).