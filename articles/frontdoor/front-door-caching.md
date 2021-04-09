---
title: Voor deur van Azure front-caching | Microsoft Docs
description: Dit artikel helpt u bij het begrijpen van het gedrag voor de voor deur met routerings regels die caching hebben ingeschakeld.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: d001a7a24d44c46a19bde08051e21d3ae3c5acb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538048"
---
# <a name="caching-with-azure-front-door"></a>Caching met de voor deur van Azure
In het volgende document worden gedragingen voor de voor deur opgegeven met routerings regels waarvoor caching is ingeschakeld. Front deur is een modern Content Delivery Network (CDN) met dynamische site versnelling en taak verdeling, maar biedt ook ondersteuning voor het opslaan in cache, net als bij andere CDN.

## <a name="delivery-of-large-files"></a>Levering van grote bestanden
Azure front-deur levert grote bestanden zonder een limiet voor de bestands grootte. De voor deur maakt gebruik van een techniek die object Chunking wordt genoemd. Als er een groot bestand wordt aangevraagd, haalt Front Door kleine onderdelen van het bestand op uit de back-end. Na ontvangst van een volledige of byte-Range-aanvraag vraagt het bestand van de back-end in de segmenten van 8 MB.

Nadat het segment in de front-deur omgeving arriveert, wordt het in de cache geplaatst en direct aan de gebruiker geleverd. De voor deur haalt vervolgens het volgende segment parallel op. Met deze vooraf opgehaalde, zorgt u ervoor dat de inhoud één segment vóór de gebruiker blijft, waardoor de latentie wordt verminderd. Dit proces wordt voortgezet totdat het hele bestand wordt gedownload (indien aangevraagd) of de client sluit de verbinding.

Lees [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)voor meer informatie over de aanvraag voor byte bereik.
Bij de voor deur worden eventuele segmenten in de cache opgeslagen, zodat het hele bestand niet in de cache hoeft te worden opgeslagen in de front-deur cache. Aanvragen voor het bestand of de byte bereiken worden verwerkt vanuit de cache. Als de segmenten niet in de cache zijn opgeslagen, wordt vooraf ophalen gebruikt om segmenten van de back-end op te vragen. Deze optimalisatie is afhankelijk van de mogelijkheid van de back-end om aanvragen voor byte bereik te ondersteunen. Als de back-end geen aanvragen voor byte bereik ondersteunt, is deze optimalisatie niet effectief.

## <a name="file-compression"></a>Bestandscompressie
De voor deur kan inhoud op de rand dynamisch comprimeren, wat resulteert in een kleinere en snellere reactie tijd voor uw clients. Om een bestand in aanmerking te laten komen voor compressie, moet caching zijn ingeschakeld en moet het bestand een MIME-type zijn om in aanmerking te komen voor compressie. Op dit moment mag deze lijst niet worden gewijzigd. De huidige lijst is:
- ' Application/EOT '
- toepassing/letter type
- "toepassing/letter type-sfnt"
- "toepassing/Java script"
- 'application/json'
- toepassing/open type
- ' Application/otf '
- ' application/pkcs7-MIME '
- "toepassing/True Type"
- ' Application/ttf ',
- ' application/vnd. MS-fontobject '
- "toepassing/XHTML en XML"
- ' Application/XML '
- ' Application/XML + RSS '
- ' Application/x: font-open type '
- ' Application/x: font-True Type '
- ' Application/x: font-ttf '
- "Application/x-httpd-cgi"
- ' Application/x-mpegurl '
- ' Application/x-open type '
- ' Application/x-otf '
- ' Application/x-perl '
- ' Application/x-ttf '
- ' Application/x-java script '
- "font/EOT"
- "font/ttf"
- "font/otf"
- "letter type/open type"
- "afbeelding/SVG + XML"
- "tekst/css"
- "tekst/CSV"
- "tekst/html"
- "tekst/java script"
- "tekst/js", "tekst/zonder opmaak"
- "tekst/RTF"
- "tekst/door tabs gescheiden waarden"
- "tekst/XML"
- "tekst/x-script"
- "tekst/x-onderdeel"
- "tekst/x-Java-bron"

Daarnaast moet het bestand ook tussen 1 KB en 8 MB groot zijn.

Deze profielen ondersteunen de volgende compressie coderingen:
- [Gzip (GNU-zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Als een aanvraag gzip en Brotli-compressie ondersteunt, heeft Brotli-compressie prioriteit.</br>
Wanneer een aanvraag voor een Asset compressie specificeert en de aanvraag resulteert in een Missing in een cache, wordt de Asset door de voor deur rechtstreeks op de POP-server gecomprimeerd. Daarna wordt het gecomprimeerde bestand vanuit de cache verwerkt. Het resulterende item wordt geretourneerd met een overdracht-Encoding: gesegmenteerd.

## <a name="query-string-behavior"></a>Query teken reeks gedrag
Met de voor deur kunt u bepalen hoe bestanden in de cache worden opgeslagen voor een webaanvraag die een query reeks bevat. In een webaanvraag met een query reeks is de query reeks het gedeelte van de aanvraag dat wordt uitgevoerd na een vraag teken (?). Een query reeks kan een of meer sleutel-waardeparen bevatten, waarbij de veld naam en de waarde ervan gescheiden worden door een gelijkteken (=). Elk sleutel-waardepaar wordt gescheiden door een en-teken (&). Bijvoorbeeld `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Als er meer dan één sleutel/waarde-paar in een query reeks van een aanvraag is, is de volg orde hiervan niet van belang.
- **Query reeksen negeren**: in deze modus geeft de voor deur de query teken reeksen van de aanvrager door aan de back-end van de eerste aanvraag en slaat de Asset op in de cache. Alle aanvragen voor de activa die worden verwerkt vanuit de voor deur, negeren de query teken reeksen totdat het activum in de cache verloopt.

- **Elke unieke URL in de cache opslaan**: in deze modus wordt elke aanvraag met een unieke URL, inclusief de query reeks, behandeld als een unieke Asset met een eigen cache. Het antwoord van de back-end voor een aanvraag voor `www.example.ashx?q=test1` wordt bijvoorbeeld opgeslagen in de cache van de front-deur omgeving en geretourneerd voor het aanbrengen van caches met dezelfde query reeks. Een aanvraag voor `www.example.ashx?q=test2` wordt in de cache opgeslagen als een afzonderlijk activum met een eigen time-to-Live-instelling.

## <a name="cache-purge"></a>Cache opschonen

Met de voor deur wordt activa in de cache geplaatst totdat de TTL (time-to-Live) van het activum verloopt. Wanneer een client een Asset aanvraagt met verlopen TTL, haalt de front-deur omgeving een nieuwe bijgewerkte kopie van de Asset op om de aanvraag te leveren en slaat vervolgens de vernieuwde cache op.

De best practice om ervoor te zorgen dat uw gebruikers altijd de nieuwste kopie van uw assets verkrijgen, is om uw assets voor elke update te maken en ze als nieuwe Url's te publiceren. Met de voor deur worden onmiddellijk de nieuwe assets opgehaald voor de volgende client aanvragen. Soms wilt u in de cache opgeslagen inhoud uit alle Edge-knoop punten verwijderen en alle nieuwe bijgewerkte assets laten afdwingen. De reden hiervoor is dat er updates voor uw webtoepassing zijn of dat u snel assets kunt bijwerken die onjuiste informatie bevatten.

Selecteer de activa die u wilt verwijderen uit de Edge-knoop punten. Als u alle assets wilt wissen, selecteert u **Alles opschonen**. Als dat niet het geval is, voert u in het **pad** het pad in van elk activum dat u wilt leegmaken.

Deze indelingen worden ondersteund in de lijsten met te verwijderen paden:

- **Eén pad leegmaken**: afzonderlijke assets opschonen door het volledige pad van de asset (zonder het protocol en domein) op te geven, met de bestands extensie, bijvoorbeeld/pictures/strasbourg.png;
- **Joker tekens opschonen**: sterretje ( \* ) kan worden gebruikt als Joker teken. Verwijder alle mappen, submappen en bestanden onder een eind punt met/ \* in het pad of verwijder alle submappen en bestanden in een specifieke map door de map op te geven gevolgd door/ \* , bijvoorbeeld/pictures/ \* .
- **Basis domein opschonen**: de hoofdmap van het eind punt met '/' in het pad opschonen.

> [!NOTE]
> **Domein met Joker tekens verwijderen**: opgeven van in cache opgeslagen paden voor het verwijderen zoals beschreven in deze sectie zijn niet van toepassing op joker tekens domeinen die aan de voor deur zijn gekoppeld. Het is momenteel niet mogelijk om Joker teken domeinen direct te verwijderen. U kunt paden uit specifieke subdomeinen verwijderen door het specifieke-subdomein en het opschoon pad op te geven. Als ik bijvoorbeeld mijn front `*.contoso.com` -deur heb, kan ik assets van mijn subdomein verwijderen `foo.contoso.com` door te typen `foo.contoso.com/path/*` . Op dit moment is het opgeven van hostnamen in het pad voor het opschonen van inhoud imited naar subdomeinen van joker tekens domeinen, indien van toepassing.
>

Cache verwijderingen op de voor deur zijn hoofdletter gevoelig. Daarnaast zijn ze query teken reeks neutraal, wat betekent dat als u een URL opschoont, alle wijzigingen in de query teken reeks worden verwijderd. 

## <a name="cache-expiration"></a>Verval datum van cache
De volgende volg orde van koppen wordt gebruikt om te bepalen hoe lang een item wordt opgeslagen in de cache:</br>
1. Cache-Control: s-maxAge =\<seconds>
2. Cache-Control: Max-Age =\<seconds>
3. Verstreken \<http-date>

Cache-Control antwoord headers die aangeven dat het antwoord niet in de cache moet worden opgeslagen, zoals het beheer van de cache: persoonlijk, cache-Control: no-cache en Cache-Control: No-Store worden nageleefd.  Als er geen Cache-Control aanwezig is, is het standaard gedrag dat de resource voor de X-tijd in de cache plaatst waarbij X wille keurig wordt gekozen tussen 1 en 3 dagen.

## <a name="request-headers"></a>Aanvraagheaders

De volgende aanvraag headers worden niet doorgestuurd naar een back-end wanneer caching wordt gebruikt.
- Content-Length
- Transfer-Encoding

## <a name="cache-duration"></a>Cacheduur

De duur van de cache kan worden geconfigureerd in zowel de voor deur Designer als in de regel engine. De cache duur die in de voor deur Designer is ingesteld, is de minimale cache duur. Deze onderdrukking werkt niet als de header van het cache besturings element van de oorsprong een grotere TTL heeft dan de onderdrukkings waarde. 

De engine voor het instellen van de cache duur via regels is een werkelijke cache-overschrijving, wat inhoudt dat de onderdrukkings waarde wordt gebruikt, ongeacht de oorspronkelijke antwoord header.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
