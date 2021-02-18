---
title: 'Azure front deur: caching'
description: Dit artikel helpt u bij het begrijpen van het gedrag van Azure front-deur Standard/Premium met routerings regels waarvoor caching is ingeschakeld.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 73b2e8e59774e12ddb9aa684382510d1f2c151b8
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099240"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Opslaan in cache met Azure front deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

In dit artikel leert u hoe front deur Standard/Premium (preview) routes en regelset gedraagt wanneer u caching hebt ingeschakeld. Azure front-deur is een modern Content Delivery Network (CDN) met dynamische site versnelling en taak verdeling.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="delivery-of-large-files"></a>Levering van grote bestanden

De voor deur standaard/Premium (preview) levert grote bestanden zonder een limiet voor de bestands grootte. De voor deur maakt gebruik van een techniek die object Chunking wordt genoemd. Wanneer een groot bestand wordt aangevraagd, haalt de voor deur kleinere delen van het bestand op van de oorsprong. Na ontvangst van een volledige of byte-Range-aanvraag vraagt de front-deur omgeving het bestand aan bij de oorsprong in segmenten van 8 MB.

Nadat het segment in de front-deur omgeving arriveert, wordt het in de cache geplaatst en direct aan de gebruiker geleverd. De voor deur haalt vervolgens het volgende segment parallel op. Met deze vooraf opgehaalde, zorgt u ervoor dat de inhoud één segment vóór de gebruiker blijft, waardoor de latentie wordt verminderd. Dit proces wordt voortgezet totdat het hele bestand wordt gedownload (indien aangevraagd) of de client sluit de verbinding.

Lees [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)voor meer informatie over de aanvraag voor byte bereik.
Bij de voor deur worden eventuele segmenten in de cache opgeslagen, zodat het hele bestand niet in de cache hoeft te worden opgeslagen in de front-deur cache. Aanvragen voor het bestand of de byte bereiken worden verwerkt vanuit de cache. Als de segmenten niet in de cache zijn opgeslagen, wordt vooraf ophalen gebruikt om segmenten van de back-end op te vragen. Deze optimalisatie is afhankelijk van de capaciteit van de oorsprong voor het ondersteunen van aanvragen voor byte bereik. Als de oorsprong geen aanvragen voor byte bereik ondersteunt, is deze optimalisatie niet effectief.

## <a name="file-compression"></a>Bestandscompressie

Raadpleeg de prestaties verbeteren door bestanden in azure front-deur te comprimeren.

## <a name="query-string-behavior"></a>Query teken reeks gedrag

Met de voor deur kunt u bepalen hoe bestanden in de cache worden opgeslagen voor een webaanvraag die een query reeks bevat. In een webaanvraag met een query reeks is de query reeks het gedeelte van de aanvraag dat wordt uitgevoerd na een vraag teken (?). Een query reeks kan een of meer sleutel-waardeparen bevatten, waarbij de veld naam en de waarde ervan gescheiden worden door een gelijkteken (=). Elk sleutel-waardepaar wordt gescheiden door een en-teken (&). Bijvoorbeeld `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Als er meer dan één sleutel/waarde-paar in een query reeks van een aanvraag is, is de volg orde hiervan niet van belang.

* **Query reeksen negeren**: in deze modus geeft de voor deur de query teken reeksen van de aanvrager door aan de oorsprong van de eerste aanvraag en slaat de Asset op in de cache. Alle aanvragen voor de activa die worden verwerkt vanuit de voor deur, negeren de query teken reeksen totdat het activum in de cache verloopt.

* **Elke unieke URL in de cache opslaan**: in deze modus wordt elke aanvraag met een unieke URL, inclusief de query reeks, behandeld als een unieke Asset met een eigen cache. Het antwoord van de oorsprong voor een aanvraag voor `www.example.ashx?q=test1` wordt bijvoorbeeld opgeslagen in de cache van de front-deur omgeving en wordt geretourneerd voor het aanbrengen van caches met dezelfde query reeks. Een aanvraag voor `www.example.ashx?q=test2` wordt in de cache opgeslagen als een afzonderlijk activum met een eigen time-to-Live-instelling.
* U kunt ook regel instellingen gebruiken om het gedrag van de **query teken reeks voor de cache sleutel** op te geven, of om opgegeven para meters uit te sluiten wanneer de cache sleutel wordt gegenereerd. De standaard cache sleutel is bijvoorbeeld:/Foo/image/asset.html en de voorbeeld aanvraag is `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` . Er is een regel reeks regel voor het uitsluiten van de query teken reeks ' userid '. Vervolgens zou de query reeks cache sleutel worden `/foo/image/asset.html?language=EN&sessionid=200` .

## <a name="cache-purge"></a>Cache opschonen

Raadpleeg Cache opschonen.

## <a name="cache-expiration"></a>Verval datum van cache
De volgende volg orde van koppen wordt gebruikt om te bepalen hoe lang een item wordt opgeslagen in de cache:</br>
1. Cache-Control: s-maxAge =\<seconds>
2. Cache-Control: Max-Age =\<seconds>
3. Verstreken \<http-date>

Cache-Control antwoord headers die aangeven dat het antwoord niet in de cache moet worden opgeslagen, zoals het beheer van de cache: persoonlijk, cache-Control: no-cache en Cache-Control: No-Store worden nageleefd.  Als er geen Cache-Control aanwezig is, is het standaard gedrag dat de resource voor de tijd in de cache wordt opgeslagen. Waarbij X wille keurig wordt gekozen tussen 1 en 3 dagen.

## <a name="request-headers"></a>Aanvraagheaders

De volgende aanvraag headers worden niet doorgestuurd naar een oorsprong wanneer caching wordt gebruikt.
* Content-Length
* Transfer-Encoding

## <a name="cache-duration"></a>Cacheduur

De cache duur kan worden geconfigureerd in de regel set. De instelling voor de cache duur die via de regel is ingesteld, is een werkelijke cache-overschrijving. Dit betekent dat de onderdrukkings waarde wordt gebruikt, ongeacht de oorspronkelijke antwoord header.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [voor waarden voor de regel instellingen](concept-rule-set-match-conditions.md)
* Meer informatie over [acties voor regel sets](concept-rule-set-actions.md)