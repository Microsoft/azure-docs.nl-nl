---
title: HTTP-variabelen voor de engine van Azure CDN-regels | Microsoft Docs
description: Meer informatie over HTTP-variabelen, waarmee u HTTP-aanvraag-en respons meta gegevens kunt ophalen voor sommige functies van een regel engine. Meta gegevens gebruiken voor het wijzigen van een aanvraag/antwoord.
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
ms.date: 05/09/2018
ms.author: allensu
ms.openlocfilehash: a2d9fc98ba6f514afbd88e543a859a69e0fc6c6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192674"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>HTTP-variabelen voor Azure CDN-regel engine
HTTP-variabelen bieden de manier waarop u HTTP-aanvraag-en respons meta gegevens kunt ophalen. Deze meta gegevens kunnen vervolgens worden gebruikt om een aanvraag of antwoord dynamisch te wijzigen. Het gebruik van HTTP-variabelen is beperkt tot de volgende functies van de regel Engine:

- [Cache sleutel opnieuw schrijven](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm)
- [Header van client aanvraag wijzigen](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm)
- [Reactie header van client wijzigen](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm)
- [URL-omleiding](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm)
- [URL opnieuw schrijven](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm)

## <a name="definitions"></a>Definities
In de volgende tabel worden de ondersteunde HTTP-variabelen beschreven. Er wordt een lege waarde geretourneerd als GEO-meta gegevens (bijvoorbeeld post code) niet beschikbaar zijn voor een bepaalde aanvraag.


| Name | Variabele | Beschrijving | Voorbeeldwaarde |
| ---- | -------- | ----------- | ------------ |
| ASN (aanvrager) | % {geo_asnum} | Hiermee wordt het AS-nummer van de aanvrager aangegeven. <br /><br />**Afgeschaft:** % {virt_dst_asnum}. <br />Deze variabele is vervangen door% {geo_asnum}. Hoewel een regel die gebruikmaakt van deze afgeschafte variabele, blijft werken, moet u deze bijwerken om de nieuwe variabele te gebruiken. | AS15133 |
| Plaats (aanvrager) | % {geo_city} | Hiermee wordt de plaats van de aanvrager aangegeven. | Los Angeles |
| Continent (aanvrager) | % {geo_continent} | Geeft het continent van de aanvrager aan met de afkorting. <br />Geldige waarden zijn: <br />AF: Afrika<br />ALS: Azië<br />EU: Europa<br />N.V.T.: Noord-Amerika<br />OC: Oceania<br />SA: Zuid-Amerika<br /><br />**Afgeschaft:** % {virt_dst_continent}. <br />Deze variabele is vervangen door% {geo_continent}. <br />Hoewel een regel die gebruikmaakt van deze afgeschafte variabele, blijft werken, moet u deze bijwerken om de nieuwe variabele te gebruiken.| N.v.t. |
| Cookie waarde | % {cookie_Cookie} | Retourneert de waarde die overeenkomt met de cookie sleutel die wordt geïdentificeerd door de cookie term. | Voorbeeld gebruik: <br />% {cookie__utma}<br /><br />Voorbeeld waarde:<br />111662281.2.10.1222100123 |
| Land/regio (aanvrager) | % {geo_country} | Geeft het land of de regio van oorsprong van de aanvrager aan via het land-of regio nummer. <br />**Afgeschaft:** % {virt_dst_country}. <br /><br />Deze variabele is vervangen door% {geo_country}. Hoewel een regel die gebruikmaakt van deze afgeschafte variabele, blijft werken, moet u deze bijwerken om de nieuwe variabele te gebruiken. | VS |
| Aangewezen markt gebied (aanvrager) | % {geo_dma_code} |Geeft de media markt van de aanvrager aan op basis van de regio code. <br /><br />Dit veld is alleen van toepassing op aanvragen die afkomstig zijn van de Verenigde Staten.| 745 |
| HTTP-aanvraagmethode | % {request_method} | Hiermee wordt de HTTP-aanvraag methode aangegeven. | GET |
| HTTP-status code | % {status} | Hiermee wordt de HTTP-status code voor het antwoord aangegeven. | 200 |
| IP-adres (aanvrager) | % {virt_dst_addr} | Hiermee wordt het IP-adres van de aanvrager aangegeven. | 192.168.1.1 |
| Latitude (aanvrager) | % {geo_latitude} | Hiermee wordt de breedte van de aanvrager aangegeven. | 34,0995 |
| Lengte graad (aanvrager) | % {geo_longitude} | Hiermee wordt de lengte graad van de aanvrager aangegeven. | -118,4143 |
| Euro pees gebied voor statistische deel (aanvrager) | % {geo_metro_code} | Hiermee wordt het gebied in de buurt van de aanvrager aangegeven. <br /><br />Dit veld is alleen van toepassing op aanvragen die afkomstig zijn van de Verenigde Staten.<br />| 745 |
| Poort (aanvrager) | % {virt_dst_port} | Hiermee wordt de tijdelijke poort van de aanvrager aangegeven. | 55885 |
| Post code (aanvrager) | % {geo_postal_code} | Hiermee wordt de post code van de aanvrager aangegeven. | 90210 |
| Query reeks gevonden | % {is_args} | De waarde voor deze variabele is afhankelijk van het feit of de aanvraag een query reeks bevat.<br /><br />-Query reeks gevonden:?<br />-Geen query reeks: NULL | ? |
| Query teken reeks parameter gevonden | % {is_amp} | De waarde voor deze variabele is afhankelijk van het feit of de aanvraag ten minste één query teken reeks parameter bevat.<br /><br />-Para meter gevonden: &<br />-Geen para meters: NULL | & |
| Parameter waarde van de query reeks | % {arg_ &lt; para meter &gt; } | Retourneert de waarde die overeenkomt met de query teken reeks parameter geïdentificeerd door de &lt; parameter &gt; term. | Voorbeeld gebruik: <br />% {arg_language}<br /><br />Voorbeeld query teken reeks parameter: <br />? language = en<br /><br />Voorbeeld waarde: en |
| Query teken reeks waarde | % {query_string} | Hiermee wordt de volledige query teken reeks waarde aangegeven die in de aanvraag-URL is gedefinieerd. |Key1 = val1&Key2 = val2&Key3 = val3 |
| Verwijzend domein | % {referring_domain} | Hiermee wordt het domein aangegeven dat is gedefinieerd in de header van de verwijzings aanvraag. | <www.google.com> |
| Regio (aanvrager) | % {geo_region} | Geeft de regio van de aanvrager (bijvoorbeeld staat of provincie) aan met de alfanumerieke afkorting. | CA (consistentie en beschikbaarheid) |
| Waarde van aanvraag header | % {http_RequestHeader} | Retourneert de waarde die overeenkomt met de aanvraag header die wordt geïdentificeerd door de RequestHeader-term. <br /><br />Als de naam van de aanvraag header een streepje bevat (bijvoorbeeld user-agent), vervangt u dit door een onderstrepings teken (bijvoorbeeld User_Agent).| Voorbeeld gebruik:% {http_Connection}<br /><br />Voorbeeld waarde: Keep-Alive | 
| Host van aanvraag | % {host} | Hiermee wordt de host aangegeven die is gedefinieerd in de aanvraag-URL. | <www.mydomain.com> |
| Aanvraag Protocol | % {request_protocol} | Geeft het aanvraag protocol aan. | HTTP/1.1 |
| Aanvraag schema | % {schema} | Hiermee wordt het aanvraag schema aangegeven. |http |
| Aanvraag-URI (relatief) | % {request_uri} | Hiermee wordt het relatieve pad aangegeven, inclusief de query reeks, gedefinieerd in de aanvraag-URI. | /Marketing/foo.js? loggedin = True |
| URI van aanvraag (relatief zonder query teken reeks) | % {URI} | Hiermee wordt het relatieve pad naar de aangevraagde inhoud aangegeven. <br /><br/>Belang rijke informatie:<br />-Dit relatieve pad sluit de query reeks uit.<br />-Dit relatieve pad duidt op het herschrijven van URL'S. Een URL wordt onder de volgende omstandigheden opnieuw geschreven:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Functie voor het herschrijven van URL'S: met deze functie wordt het relatieve pad dat in de aanvraag-URI is gedefinieerd, opnieuw geschreven.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge CNAME URL: dit type aanvraag wordt herschreven naar de bijbehorende CDN-URL. |/800001/corigin/rewrittendir/foo.js |
| Aanvraag-URI | % {Request} | Hiermee wordt de aanvraag beschreven. <br />Syntaxis: &lt; http-protocol bij methode &gt; &lt; relatief pad &gt; &lt;&gt; | GET/marketing/foo.js? loggedin = True HTTP/1.1 |
| Waarde van de antwoord header | % {resp_ &lt; ResponseHeader &gt; } | Retourneert de waarde die overeenkomt met de antwoord header die wordt geïdentificeerd door de &lt; ResponseHeader- &gt; term. <br /><br />Als de naam van de antwoord header een streepje bevat (bijvoorbeeld user-agent), vervangt u dit door een onderstrepings teken (bijvoorbeeld User_Agent). | Voorbeeld gebruik:% {resp_Content_Length}<br /><br />Voorbeeld waarde: 100 |

## <a name="usage"></a>Gebruik
De volgende tabel beschrijft de juiste syntaxis voor het opgeven van een HTTP-variabele.


| Syntax | Voorbeeld | Beschrijving |
| ------ | -------- | ---------- |
| % { &lt; HTTPVariable &gt; } | % {host} | Gebruik deze syntaxis om de volledige waarde op te halen die overeenkomt met de opgegeven &lt; HTTPVariable &gt; . |
| % { &lt; HTTPVariableDelimiter &gt; } | % {host,} | Gebruik deze syntaxis om de case in te stellen voor de gehele waarde die overeenkomt met de opgegeven  &lt; HTTPVariableDelimiter &gt; . |
| % { &lt; HTTPVariableDelimiterExpression &gt; } | % {host/= ^ www \. ([^ \. ] +) \. ([^ \. :] +)/CDN. $2. $3:80} | Gebruik een reguliere expressie voor &lt; HTTPVariableDelimiterExpression &gt; om de waarde van een http-variabele te vervangen, te verwijderen of te bewerken. |

Namen van HTTP-variabelen ondersteunen alleen alfabetische tekens en onderstrepingen. Converteer niet-ondersteunde tekens naar onderstrepingen.

## <a name="delimiter-reference"></a>Verwijzing naar scheidings teken
Een scheidings teken kan worden opgegeven na een HTTP-variabele om een van de volgende effecten te bereiken:

- Transformeer de waarde die is gekoppeld aan de variabele.

     Voor beeld: Converteer de gehele waarde naar kleine letters.

- Verwijder de waarde die is gekoppeld aan de variabele.

- Bewerk de waarde die is gekoppeld aan de variabele.

     Voor beeld: reguliere expressies gebruiken om de waarde die is gekoppeld aan de HTTP-variabele te wijzigen.

De scheidings tekens worden in de volgende tabel beschreven.

| Scheidingsteken | Description |
| --------- | ----------- |
| := | Geeft aan dat er een standaard waarde wordt toegewezen aan de variabele wanneer het een van de volgende is: <br />-Ontbreekt <br />-Stel in op NULL. |
| :+ | Geeft aan dat er een standaard waarde wordt toegewezen aan de variabele wanneer hieraan een waarde is toegewezen. |
| : | Hiermee wordt aangegeven dat een subtekenreeks van de waarde die is toegewezen aan de variabele, wordt uitgevouwen. |
| # | Geeft aan dat het patroon dat is opgegeven na dit scheidings teken moet worden verwijderd wanneer het wordt gevonden aan het begin van de waarde die aan de variabele is gekoppeld. |
| % | Geeft aan dat het patroon dat is opgegeven na dit scheidings teken moet worden verwijderd wanneer het wordt gevonden aan het einde van de waarde die is gekoppeld aan de variabele. <br />Deze definitie is alleen van toepassing als het symbool% wordt gebruikt als scheidings teken. |
| / | Een HTTP-variabele of een patroon wordt beperkt. |
| // | Alle exemplaren van het opgegeven patroon zoeken en vervangen. |
| /= | Alle exemplaren van het opgegeven patroon zoeken, kopiëren en herschrijven. |
| , | Converteer de waarde die is gekoppeld aan de HTTP-variabele naar kleine letters. |
| ^ | Converteer de waarde die is gekoppeld aan de HTTP-variabele naar hoofd letters. |
| ,, | Converteer alle exemplaren van het opgegeven teken in de waarde die is gekoppeld aan de HTTP-variabele naar kleine letters. |
| ^^ | Converteer alle exemplaren van het opgegeven teken in de waarde die is gekoppeld aan de HTTP-variabele naar hoofd letters. |

## <a name="exceptions"></a>Uitzonderingen
In de volgende tabel worden de omstandigheden beschreven waaronder de opgegeven tekst niet wordt behandeld als een HTTP-variabele.

| Voorwaarde | Beschrijving | Voorbeeld |
| --------- | ----------- | --------|
| Escape-teken% | Het percentage symbool kan worden voorafgegaan door het gebruik van een back slash. <br />De voorbeeld waarde aan de rechter kant wordt beschouwd als een letterlijke waarde en niet als een HTTP-variabele.| \%hostsite |
| Onbekende variabelen | Een lege teken reeks wordt altijd geretourneerd voor onbekende variabelen. | % {unknown_variable} |
| Ongeldige tekens of syntaxis | Variabelen die ongeldige tekens of syntaxis bevatten, worden beschouwd als letterlijke waarden. <br /><br />Voor beeld #1: de opgegeven waarde bevat een ongeldig teken (bijvoorbeeld:). <br /><br />Voor beeld #2: de opgegeven waarde bevat een dubbele set accolades. <br /><br />Voor beeld #3: er ontbreekt een accolade sluiten in de opgegeven waarde.<br /> | Voor beeld #1:% {resp_user-agent} <br /><br />Voor beeld #2:% {host}} <br /><br />Voor beeld #3:% {host |
| Naam van variabele ontbreekt | Er wordt altijd een NULL-waarde geretourneerd als er geen variabele is opgegeven. | %{} |
| Navolgende tekens | Tekens die als Trail van een variabele worden beschouwd als letterlijke waarden. <br />De voorbeeld waarde aan de rechter kant bevat een accolade die wordt behandeld als een letterlijke waarde. | % {host}} |

## <a name="setting-default-header-values"></a>Standaard waarden voor headers instellen
Een standaard waarde kan worden toegewezen aan een header wanneer deze aan een van de volgende voor waarden voldoet:
- Ontbrekende/opheffen
- Ingesteld op NULL.

In de volgende tabel wordt beschreven hoe u een standaard waarde definieert.

| Voorwaarde | Syntax | Voorbeeld | Beschrijving |
| --------- | ------ | --------| ----------- |
| Stel een koptekst in op een standaard waarde wanneer deze aan een van de volgende voor waarden voldoet: <br /><br />-Ontbrekende header <br /><br />-Header-waarde is ingesteld op NULL.| % {Variable: = waarde} | % {http_referrer: = niet opgegeven} | De verwijzende header wordt alleen ingesteld op niet *opgegeven* als deze ontbreekt of als deze is ingesteld op null. Als deze is ingesteld, wordt er geen actie uitgevoerd. |
| Stel een koptekst in op een standaard waarde wanneer deze ontbreekt. | % {Variable = waarde} | % {http_referrer = niet opgegeven} | De verwijzende header wordt alleen ingesteld op niet *opgegeven* wanneer deze ontbreekt. Als deze is ingesteld, wordt er geen actie uitgevoerd. |
| Stel de koptekst in op een standaard waarde wanneer deze niet aan een van de volgende voor waarden voldoet: <br /><br />-Ontbreekt<br /><br /> -Stel in op NULL. | % {Variable: + value} | % {http_referrer: + niet opgegeven} | De verwijzende koptekst wordt alleen ingesteld op niet *opgegeven* wanneer er een waarde aan is toegewezen. Er wordt geen actie uitgevoerd als deze ontbreekt of als deze is ingesteld op NULL. |

## <a name="manipulating-variables"></a>Variabelen bewerken
Variabelen kunnen op de volgende manieren worden gemanipuleerd:

- Subtekenreeksen uitbreiden
- Patronen verwijderen

### <a name="substring-expansion"></a>Uitbrei ding subtekenreeks
Standaard wordt een variabele uitgebreid naar de volledige waarde. Gebruik de volgende syntaxis om alleen een subtekenreeks van de waarde van de variabele uit te breiden.

`%<Variable>:<Offset>:<Length>}`

Belang rijke informatie:

- De waarde die is toegewezen aan de offset-term bepaalt het begin teken van de subtekenreeks:

     - Positief: het begin teken van de subtekenreeks wordt berekend op basis van het eerste teken in de teken reeks.
     - Nul: het begin teken van de subtekenreeks is het eerste teken in de teken reeks.
     - Negatief: het begin teken van de subtekenreeks wordt berekend op basis van het laatste teken in de teken reeks.

- De lengte van de subtekenreeks wordt bepaald door de *lengte* termijn:

     - Wegge laten: het weglaten van de lengte term laat de subtekenreeks alle tekens bevatten tussen het begin teken en het einde van de teken reeks.
     - Positief: bepaalt de lengte van de subtekenreeks van het begin teken aan de rechter kant.
     - Negatief: bepaalt de lengte van de subtekenreeks van het begin teken tot de linkerkant.

#### <a name="example"></a>Voorbeeld:

In het volgende voor beeld wordt gebruikgemaakt van de volgende voorbeeld aanvraag-URL:

https: \/ /cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

De volgende teken reeks demonstreert verschillende methoden voor het bewerken van variabelen:

https: \/ /www%{HTTP_HOST: 3}/mobile/%{REQUEST_URI: 7:10}/% {REQUEST_URI:-5:-8}. htm

Op basis van de voor beeld-aanvraag-URL wordt met de bovenstaande variabele manipulatie de volgende waarde geproduceerd:

https: \/ /www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Patroon verwijderen
Tekst die overeenkomt met een specifiek patroon kan worden verwijderd uit het begin of het einde van de waarde van een variabele.

| Syntax | Bewerking |
| ------ | ------ |
| % {Variable # patroon} | Tekst verwijderen wanneer het opgegeven patroon wordt gevonden aan het begin van de waarde van een variabele. |
| % {Variabele% pattern} | Tekst verwijderen wanneer het opgegeven patroon wordt gevonden aan het einde van de waarde van een variabele. |

#### <a name="example"></a>Voorbeeld:

In dit voorbeeld scenario wordt de variabele *REQUEST_URI* ingesteld op:

`/800001/myorigin/marketing/product.html?language=en-US`

In de volgende tabel ziet u hoe deze syntaxis werkt.

| Voorbeeld syntaxis | Resultaten | Description |
| ------------- | ------- | --- |
| % {request_uri #/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html? language = nl-nl | Omdat de variabele begint met het patroon, is deze vervangen. |
| % {request_uri% html} htm | /800001/myorigin/marketing/product.html? language = nl-nl | Omdat de variabele niet eindigt met het patroon, is er geen wijziging.|

### <a name="find-and-replace"></a>Zoeken en vervangen
De syntaxis voor zoeken en vervangen wordt beschreven in de volgende tabel.

| Syntax | Bewerking |
| ------ | ------ |
| % {Variabele/zoeken/vervangen} | Het eerste exemplaar van het opgegeven patroon zoeken en vervangen. |
| % {Variable//zoeken/vervangen} | Zoek en vervang alle instanties van het opgegeven patroon. |
| % {Variable ^} |Zet de gehele waarde om in hoofd letters. |
| % {Variabele ^ zoeken} | Het eerste exemplaar van het opgegeven patroon converteren naar hoofd letters. |
| % {Variable,} | Zet de gehele waarde om in kleine letters. |
| % {Variable, zoeken} | Het eerste exemplaar van het opgegeven patroon converteren naar kleine letters. |

### <a name="find-and-rewrite"></a>Zoeken en herschrijven
Gebruik voor een variatie op zoeken en vervangen de tekst die overeenkomt met het opgegeven patroon wanneer u deze herschrijft. De syntaxis voor zoeken en herschrijven wordt beschreven in de volgende tabel.

| Syntax | Bewerking |
| ------ | ------ |
| % {Variable/= zoeken/herschrijven} | Alle exemplaren van het opgegeven patroon zoeken, kopiëren en herschrijven. |
| % {Variable/^ zoeken/herschrijven} | Zoeken, kopiëren en herschrijven van het opgegeven patroon wanneer dit plaatsvindt aan het begin van de variabele. |
| % {Variable/$Find/rewrite} | Zoeken, kopiëren en herschrijven van het opgegeven patroon wanneer dit plaatsvindt aan het einde van de variabele. |
| % {Variabele/zoeken} | Zoek en verwijder alle exemplaren van het opgegeven patroon. |

Belang rijke informatie:

- Vouw tekst uit die overeenkomt met het opgegeven patroon door een dollar teken op te geven gevolgd door een geheel getal (bijvoorbeeld $1).

- Er kunnen meerdere patronen worden opgegeven. De volg orde waarin het patroon wordt opgegeven, bepaalt het geheel getal dat hieraan wordt toegewezen. In het volgende voor beeld komt het eerste patroon overeen met ' www. ', het tweede patroon komt overeen met het domein van het tweede niveau en het derde patroon overeenkomt met het domein op het hoogste niveau:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- De herschrijfde waarde kan bestaan uit een wille keurige combi natie van tekst en deze tijdelijke aanduidingen.

    In het vorige voor beeld wordt de hostnaam opnieuw geschreven naar `cdn.$2.$3:80` (bijvoorbeeld CDN.mydomain.com:80).

- Het geval van een tijdelijke aanduiding voor een patroon (bijvoorbeeld $1) kan worden gewijzigd met behulp van de volgende vlaggen:
     - U: hoofd letters de uitgevouwen waarde.

         Voorbeeld van syntaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: kleine de uitgevouwen waarde.

         Voorbeeld van syntaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Er moet een operator worden opgegeven voor het patroon. De opgegeven operator bepaalt het gedrag van het vastleggen van patronen:

     - `=`: Hiermee wordt aangegeven dat alle exemplaren van het opgegeven patroon moeten worden vastgelegd en opnieuw moeten worden geschreven.
     - `^`: Geeft aan dat alleen tekst die begint met het opgegeven patroon wordt vastgelegd.
     - `$`: Geeft aan dat alleen tekst die eindigt op het opgegeven patroon wordt vastgelegd.
 
- Als u de waarde */rewrite* weglaat, wordt de tekst die overeenkomt met het patroon, verwijderd.


