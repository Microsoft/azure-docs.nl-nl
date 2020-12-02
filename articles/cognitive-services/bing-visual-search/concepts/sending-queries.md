---
title: Zoek query's naar de Bing Visual Search-API verzenden
titleSuffix: Azure Cognitive Services
description: In dit artikel worden de para meters en kenmerken van aanvragen beschreven die worden verzonden naar de Bing Visual Search-API, evenals het antwoord object.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: aahi
ms.openlocfilehash: 37d9352b6384ee2b5e95903f35d531bd672b25b1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490972"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>Zoek query's naar de Bing Visual Search-API verzenden

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

In dit artikel worden de para meters en kenmerken van aanvragen beschreven die worden verzonden naar de Bing Visual Search-API, evenals het antwoord object. 

U kunt op drie manieren inzichten over een afbeelding krijgen:

- Het gebruik van een Insights-token dat u van een installatie kopie ontvangt in een vorige aanroep van een van de [Bing afbeeldingen zoeken-API](/rest/api/cognitiveservices/bing-images-api-v7-reference) -eind punten.
- De URL van een afbeelding wordt verzonden.
- Een installatie kopie uploaden (in binaire indeling).

## <a name="bing-visual-search-requests"></a>Bing Visual Search aanvragen

Als u Visual Search een installatie kopie-token of-URL verzendt, wordt in het volgende code fragment het JSON-object weer gegeven dat u moet bevatten in de hoofd tekst van het bericht:

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

Het `imageInfo`-object moet het veld `url` of `imageInsightsToken` bevatten, maar niet beide. Stel het `url` veld in op de URL van een installatie kopie die toegankelijk is via internet. De maximale ondersteunde afbeeldingsgrootte is 1 MB.

De `imageInsightsToken` moet zijn ingesteld op een inzichttoken. Roep de Bing Image API aan om een inzichttoken te krijgen. Het antwoord bevat een lijst met `Image`-objecten. Elke `Image`-object bevat een `imageInsightsToken`-veld, dat het token bevat.

Het veld `cropArea` is optioneel. In het snij gebied wordt de linkerbovenhoek en rechter benedenhoek van een interesse gebied opgegeven. Geef waarden op in het bereik 0,0 t/m 1,0. De waarden zijn een percentage van de totale breedte of hoogte. In het bovenstaande voorbeeld wordt bijvoorbeeld de rechterhelft van de afbeelding gemarkeerd als het interessegebied. Neem dit op als u de inzichtaanvraag wilt beperken tot het interessegebied.

Het `filters`-object bevat een sitefilter (zie het veld `site`) dat u kunt gebruiken om de resultaten met vergelijkbare afbeeldingen en vergelijkbare producten te beperken tot een specifiek domein. Bijvoorbeeld, als de afbeelding van een Surface Book is, kunt u instellen `site` op `www.microsoft.com` .

Als u inzichten wilt krijgen over een lokale kopie van een afbeelding, uploadt u de afbeelding als binaire gegevens.

Zie [Typen inhoudformulieren](#content-form-types) voor informatie over het opnemen van deze opties in de hoofdtekst van de POST.

### <a name="search-endpoint"></a>Eind punt zoeken

Het eindpunt van Visual Search is: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Aanvragen mogen alleen worden verzonden als HTTP POST-aanvragen.

### <a name="query-parameters"></a>Queryparameters

Hier volgen de queryparameters die in uw aanvraag moeten worden opgegeven. U moet mini maal de `mkt` query parameter toevoegen:

| Naam | Waarde | Type | Vereist |
| --- | --- | --- | --- |
| <a name="cc"></a>cc  | Een land code van twee tekens die aangeeft waar de resultaten vandaan komen.<br /><br /> Als u deze parameter instelt, moet u ook de [Accept-Language](#acceptlanguage)-header opgeven. Bing gebruikt de eerste ondersteunde taal die wordt gevonden in de lijst met talen en combineert de taal met de landcode die u opgeeft om de markt te bepalen waaruit de resultaten moeten worden geretourneerd. Als de talenlijst geen ondersteunde taal bevat, vindt Bing de dichtstbijzijnde taal en markt die de aanvraag ondersteunen. Of het kan een geaggregeerde of standaardmarkt voor de resultaten gebruiken in plaats van degene die is opgegeven.<br /><br /> Gebruik deze queryparameter en de parameter `Accept-Language` alleen als u meerdere talen opgeeft; anders moet u de queryparameters `mkt` en `setLang` gebruiken.<br /><br /> Deze parameter en de parameter [mkt](#mkt) sluiten elkaar uit&mdash;geef ze niet beide op. | Tekenreeks | Nee       |
| <a name="mkt"></a>mkt   | De markt waaruit de resultaten afkomstig zijn. <br /><br /> **Opmerking:** U moet altijd de markt opgeven, indien bekend. Het specificeren van de markt helpt Bing de aanvraag te routeren en een passend en optimaal antwoord te geven.<br /><br /> Deze parameter en de parameter [cc](#cc) sluiten elkaar uit&mdash;geef ze niet beide op. | Tekenreeks | Ja      |
| <a name="safesearch"></a>safeSearch | Een filter voor inhoud voor volwassenen. Hier volgen de mogelijke niet-hoofdlettergevoelige filterwaarden.<br /><ul><li>Uit&mdash;Retourneer webpagina's met tekst of afbeeldingen voor volwassenen.<br /><br/></li><li>Gemiddeld&mdash;Retourneer webpagina's met tekst voor volwassenen, maar geen afbeeldingen voor volwassenen.<br /><br/></li><li>Strikt&mdash;Retourneer geen webpagina's met tekst of afbeeldingen voor volwassenen.</li></ul><br /> De standaardwaarde is Moderate.<br /><br /> **OPMERKING:** Als de aanvraag afkomstig is van een markt waar Bing's beleid voor volwassenen vereist dat `safeSearch` op Strikt wordt ingesteld, negeert Bing de waarde van `safeSearch` en gebruikt Strikt.<br/><br/>**Opmerking:** Als u de `site:` operator query gebruikt, is het mogelijk dat het antwoord inhoud voor volwassenen bevat, ongeacht de `safeSearch` query parameter is ingesteld op. Gebruik `site:` alleen als u zich bewust bent van de inhoud op de site en uw scenario de mogelijkheid van inhoud voor volwassenen ondersteunt.  | Tekenreeks | Nee       |
| <a name="setlang"></a>setLang  | De taal die moet worden gebruikt voor gebruikersinterfacetekenreeksen. Geef de taal op die gebruikmaakt van de ISO 639-1-taal code van twee letters. De taalcode voor Nederlands is bijvoorbeeld NL. De standaardwaarde is EN (Engels).<br /><br /> Hoewel dit optioneel is, moet u altijd de taal opgeven. Doorgaans stelt u `setLang` in op dezelfde taal die is opgegeven door `mkt`, tenzij de gebruiker wil dat gebruikersinterfacetekenreeksen in een andere taal worden weergegeven.<br /><br /> Deze parameter en de header [Accept-Language](#acceptlanguage) sluiten elkaar uit&mdash;geef ze niet beide op.<br /><br /> Een gebruikersinterfacetekenreeks is een tekenreeks die wordt gebruikt als label in een gebruikersinterface. Er zijn maar weinig gebruikersinterfacetekenreeksen in de JSON-antwoordobjecten. De opgegeven taal wordt ook toegepast op koppelingen naar Bing.com-eigenschappen in de antwoordobjecten. | Tekenreeks | Nee   |

## <a name="headers"></a>Kopteksten

Hier volgen de headers die in uw aanvraag moeten worden opgegeven. De `Content-Type` `Ocp-Apim-Subscription-Key` koptekst en zijn de enige vereiste headers, maar u moet ook `User-Agent` , `X-MSEdge-ClientID` `X-MSEdge-ClientIP` `X-Search-Location` , en.

| Header | Beschrijving |
| --- | --- |
| <a name="acceptlanguage"></a>Accept-Language  | Optionele aanvraagheader.<br /><br /> Een door komma's gescheiden lijst met talen die moet worden gebruikt voor gebruikersinterfacetekenreeksen. De lijst is in aflopende volgorde van voorkeur. Zie [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) voor meer informatie, waaronder de verwachte indeling.<br /><br /> Deze header en de queryparameter [setLang](#setlang) sluiten elkaar uit&mdash;geef ze niet beide op.<br /><br /> Als u deze header instelt, moet u ook de queryparameter [cc](#cc) opgeven. Om de markt te bepalen waarvoor resultaten moeten worden geretourneerd, gebruikt Bing de eerste ondersteunde taal die wordt gevonden in de lijst en combineert deze met de parameterwaarde `cc`. Als de lijst geen ondersteunde taal bevat, vindt Bing de dichtstbijzijnde taal en markt die de aanvraag ondersteunen, of gebruikt een geaggregeerde of standaardmarkt voor de resultaten. Zie de header als u wilt weten welke markt er door Bing is gebruikt `BingAPIs-Market` .<br /><br /> Gebruik deze header en de queryparameter `cc` alleen als u meerdere talen opgeeft. Gebruik anders de queryparameters [mkt](#mkt) en [setLang](#setlang).<br /><br /> Een gebruikersinterfacetekenreeks is een tekenreeks die wordt gebruikt als label in een gebruikersinterface. Er zijn maar weinig gebruikersinterfacetekenreeksen in de JSON-antwoordobjecten. De opgegeven taal wordt toegepast op koppelingen naar Bing.com-eigenschappen in de antwoordobjecten.  |
| <a name="contenttype"></a>Content-Type  |     |
| <a name="market"></a>BingAPIs-Market    | Antwoordheader.<br /><br /> De markt die wordt gebruikt door de aanvraag. Het formulier is \<languageCode\> - \<countryCode\> . Bijvoorbeeld: nl-NL.  |
| <a name="traceid"></a>BingAPIs-TraceId  | Antwoordheader.<br /><br /> De id van de logboekvermelding die de details van de aanvraag bevat. Registreer deze id wanneer er een fout optreedt. Als u het probleem niet kunt vaststellen en oplossen, neemt u deze id op bij de andere informatie die u aan het ondersteuningsteam verstrekt. |
| <a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key | Vereiste aanvraagheader.<br /><br /> De abonnementssleutel die u hebt ontvangen toen u zich hebt geregistreerd voor deze service in [Cognitive Services](https://www.microsoft.com/cognitive-services/). |
| <a name="pragma"></a>Pragma |   |
| <a name="useragent"></a>User-Agent  | Optionele aanvraagheader.<br /><br /> De gebruikersagent waarvan de aanvraag afkomstig is. Bing gebruikt de user-agent om mobiele gebruikers een geoptimaliseerde ervaring te bieden. Hoewel dit optioneel is, wordt u aangeraden deze header altijd op te geven.<br /><br /> De user-agent moet dezelfde tekenreeks zijn die elke veelgebruikte browser verzendt. Zie [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)voor meer informatie over gebruikers agents.<br /><br /> Hier volgen enkele voorbeelden van user-agent-tekenreeksen.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; nl-nl; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; zoals Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) zoals Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 zoals Mac OS X) AppleWebKit/537.51.1 (KHTML, zoals Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>      |
| <a name="clientid"></a>X-MSEdge-ClientID  | Optionele aanvraag- en antwoord-header.<br /><br /> Bing gebruikt deze header om gebruikers consistent gedrag te bieden bij Bing API-aanroepen. Bing introduceert nieuwe functies en verbeteringen vaak in flights, en gebruikt de client-ID als sleutel voor het toewijzen van verkeer aan verschillende flights. Als u niet dezelfde client-id gebruikt voor een gebruiker voor meerdere aanvragen, kan Bing de gebruiker toewijzen aan meerdere conflicterende flights. Toewijzing aan meerdere conflicterende flights kan leiden tot een inconsistente gebruikerservaring. Als de tweede aanvraag bijvoorbeeld een andere flighttoewijzing heeft dan de eerste, kan de ervaring onverwacht zijn. Bing kan de client-id ook gebruiken om webresultaten aan te passen aan de zoekgeschiedenis van die client-id, waardoor de gebruiker een rijkere ervaring krijgt.<br /><br /> Bing gebruikt deze header ook om de rangschikking van resultaten te verbeteren door de activiteit te analyseren die wordt gegenereerd door een client-id. De relevantie-verbeteringen helpen de kwaliteit van de resultaten die door Bing-API's worden geleverd te verbeteren, en maken op hun beurt hogere doorklikpercentages mogelijk voor de API-consument.<br /><br /> **BELANGRIJK:** Hoewel deze header optioneel is, dient u deze als verplicht te beschouwen. Door de client-id te behouden voor meerdere aanvragen voor dezelfde combinatie van eindgebruiker en apparaat, zorgt u voor 1) een consistente gebruikerservaring van de API-consument, en 2) hogere doorklikpercentages via een betere kwaliteit van de resultaten van de Bing-API's.<br /><br /> Hieronder volgen de basisgebruiksregels die van toepassing zijn op deze header.<br /><ul><li>Elke gebruiker die uw toepassing op het apparaat gebruikt, moet een unieke door Bing gegenereerde client-id hebben.<br /><br/>Als u deze header niet in de aanvraag opneemt, genereert Bing een id en retourneert deze in de X-MSEdge-ClientID-antwoordheader. De enige keer dat u deze header NIET in een aanvraag moet opnemen, is de eerste keer dat de gebruiker uw app op dat apparaat gebruikt.<br /><br/></li><li>**LET OP:** U moet ervoor zorgen dat deze client-id niet kan worden gekoppeld aan geverifieerde gebruikersaccountgegevens.</li><li>Gebruik de client-id voor elke Bing-API-aanvraag die uw app voor deze gebruiker op het apparaat doet.<br /><br/></li><li>Maak de client-id persistent. Gebruik in een browser-app een persistent HTTP-cookie om ervoor te zorgen dat de id in alle sessies wordt gebruikt. Gebruik geen sessiecookie. Voor andere apps, zoals mobiele apps, gebruikt u de persistente opslag van het apparaat om de id persistent te maken.<br /><br/>De volgende keer dat de gebruiker uw app op dat apparaat gebruikt, haalt u de client-id op die u persistent hebt gemaakt.</li></ul><br /> **OPMERKING:** Bing-antwoorden kunnen deze header al dan niet bevatten. Als het antwoord deze header bevat, registreert u de client-id en gebruikt u deze voor alle volgende Bing-aanvragen voor de gebruiker op dat apparaat.<br /><br /> **OPMERKING:** als u de X-MSEdge-ClientID opneemt, moet u geen cookies opnemen in de aanvraag. |
| <a name="clientip"></a>X-MSEdge-ClientIP   | Optionele aanvraagheader.<br /><br /> Het IPv4- of IPv6-adres van het clientapparaat. Het IP-adres wordt gebruikt voor het detecteren van de locatie van de gebruiker. Bing gebruikt de locatie-informatie om het gedrag van Veilig Zoeken te bepalen.<br /><br /> **OPMERKING:** Hoewel dit optioneel is, wordt u aangeraden deze header en de X-Search-Location-header altijd op te geven.<br /><br /> Verdoezel het adres niet (bijvoorbeeld door het laatste octet te wijzigen in 0). Wanneer u het adres verdoezelt, is de locatie totaal niet in de buurt van de werkelijke locatie van het apparaat, wat ertoe kan leiden dat Bing onjuiste resultaten geeft. |
| <a name="location"></a>X-Search-Location   | Optionele aanvraagheader.<br /><br /> Een met puntkomma's gescheiden lijst met sleutel-waardeparen die de geografische locatie van de client beschrijven. Bing gebruikt de locatie-informatie om het gedrag van Veilig Zoeken te bepalen en relevante lokale inhoud te retourneren. Geef het sleutel/waarde-paar op als \<key\> : \<value\> . Hier volgen de sleutels die u gebruikt om de locatie van de gebruiker op te geven.<br /><br /><ul><li>lat&mdash;Vereist. De breedtegraad van de locatie van de client, in graden. De breedtegraad moet groter dan of gelijk zijn aan -90.0 en kleiner dan of gelijk aan +90.0. Negatieve waarden geven zuidelijke breedtegraden aan, en positieve waarden noordelijke.<br /><br /></li><li>long&mdash;Vereist. De lengtegraad van de locatie van de client, in graden. De lengtegraad moet groter dan of gelijk zijn aan -180.0 en kleiner dan of gelijk aan +180.0. Negatieve waarden geven westelijke lengtegraden aan, en positieve waarden oostelijke.<br /><br /></li><li>re&mdash;Vereist. De straal, in meters, die de horizontale nauwkeurigheid van de coördinaten aangeeft. Geef de waarde door die wordt geretourneerd door de locatieservice van het apparaat. Typische waarden kunnen 22 m zijn voor GPS/Wi-Fi, 380 m voor triangulatie van een cel-toren en 18.000 m voor omgekeerde IP-Zoek opdrachten.<br /><br /></li><li>ts&mdash;Optioneel. Het UTC-UNIX-tijdstempel van wanneer de client op de locatie was. (Het UNIX-tijdstempel is het aantal seconden sinds 1 januari 1970.)<br /><br /></li><li>head&mdash;Optioneel. De relatieve koers of reisrichting van de client. Geef de reisrichting op als graden van 0 t/m 360, gerekend met de klok mee ten opzichte van het ware noorden. Geef deze sleutel alleen op als de `sp`-sleutel niet nul is.<br /><br /></li><li>sp&mdash;Optioneel. De horizontale snelheid, in meters per seconde, waarmee het clientapparaat reist.<br /><br /></li><li>alt&mdash;Optioneel. De hoogte van het clientapparaat, in meters.<br /><br /></li><li>are&mdash;Optioneel. De straal, in meters, die de verticale nauwkeurigheid van de coördinaten aangeeft. Geef deze sleutel alleen op als u de `alt`-sleutel opgeeft.<br /><br /></li></ul> **OPMERKING:** Hoewel veel van de sleutels optioneel zijn, geldt dat hoe meer informatie u verstrekt, hoe nauwkeuriger de locatieresultaten zijn.<br /><br /> **OPMERKING:** Hoewel dit optioneel is, wordt u aangeraden de geografische locatie van de gebruiker altijd op te geven. Het opgeven van de locatie is vooral belangrijk als het IP-adres van de client de fysieke locatie van de gebruiker niet nauwkeurig weergeeft (bijvoorbeeld als de client VPN gebruikt). Voor optimale resultaten moet u deze header en de header toevoegen `X-MSEdge-ClientIP` , maar u moet deze header echter wel toevoegen.       |

> [!NOTE]
> Houd er rekening mee dat de vereisten voor het gebruik van de [Bing Search-API en de weer gave](../../bing-web-search/use-display-requirements.md) moeten voldoen aan alle toepasselijke wetten, met inbegrip van het gebruik van deze headers. In bepaalde rechtsgebieden, zoals Europa, is het bijvoorbeeld vereist om toestemming van de gebruiker te verkrijgen voordat bepaalde traceringsapparaten op gebruikersapparaten wordt geplaatst.

<a name="content-form-types"></a>

### <a name="content-form-types"></a>Typen formulierinhoud

Elke aanvraag moet de `Content-Type` koptekst bevatten. De header moet worden ingesteld op: `multipart/form-data; boundary=\<boundary string\>` , waarbij \<boundary string\> is een unieke, dekkende teken reeks waarmee de grens van de formulier gegevens wordt geïdentificeerd. Bijvoorbeeld `boundary=boundary_1234-abcd`.

Als u Visual Search een afbeeldings token of URL verzendt, worden in het volgende code fragment de formulier gegevens weer gegeven die u in de hoofd tekst van het bericht moet bevatten. De formulier gegevens moeten de `Content-Disposition` koptekst bevatten en u moet de `name` para meter instellen op ' knowledgeRequest '. Zie de aanvraag voor meer informatie over het `imageInfo` object.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

U kunt eventueel het `enableEntityData` kenmerk in de koptekst instellen op `true` voor gedetailleerde informatie over de hoofd entiteit in de installatie kopie die u uploadt, inclusief koppelingen naar de web-en informatie over de toewijzing. Dit veld is `false` standaard.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
  "imageInfo" : {
      "url" : "https://contoso.com/2018/05/fashion/red.jpg"
  },
  "knowledgeRequest" : {
    "invokedSkillsRequestData" : {
        "enableEntityData" : "true"
    }
  }
}

--boundary_1234-abcd--
```

Als u een lokale installatie kopie uploadt, bevat het volgende code fragment de formulier gegevens die u moet bevatten in de hoofd tekst van het bericht. De formulier gegevens moeten de `Content-Disposition` koptekst bevatten. De parameter `name` moet worden ingesteld op "image" en de parameter `filename` kan op een willekeurige tekenreeks worden ingesteld. De `Content-Type` header kan worden ingesteld op een veelgebruikte MIME-afbeeldings type. De inhoud van het formulier is de binaire gegevens van de installatie kopie. De maximale afbeeldingsgrootte die u kunt uploaden is 1 MB. De grootste zijde van de afbeelding (breedte of hoogte) moet 1500 pixels of minder zijn.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Het volgende code fragment laat zien hoe u het belang van een geüploade afbeelding kunt opgeven:

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

### <a name="example-request"></a>Voorbeeldaanvraag

Het volgende code fragment toont een volledige image Insights-aanvraag die een afbeeldings token en een belang rijke regio doorgeeft. U krijgt het Insights-token van een vorige aanroep van/Images/Search:

```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```

## <a name="bing-visual-search-responses"></a>Bing Visual Search reacties


[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

Als er Inzichten beschikbaar zijn voor de afbeelding, bevat het antwoord een of meer `tags` die de Inzichten bevatten. Het `image` veld bevat het Insights-token voor de invoer installatie kopie:

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Het veld `tags` bevat een weergavenaam en een lijst met acties (inzichten). Een van de tags bevat een `displayName`-veld dat is ingesteld op een lege tekenreeks. Deze tag bevat de standaardinzichten, zoals webpagina's met de afbeelding, visueel vergelijkbare afbeeldingen en winkelbronnen voor in de afbeelding gevonden artikelen. Omdat de volledige afbeelding van belang is, bevatten de standaard Insights-Tags geen begrenzings vakken voor de gewenste regio's:

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Zie [standaard Insights-tag](../default-insights-tag.md)voor een lijst met standaard inzichten.

De overige tags bevatten andere inzichten die van belang kunnen zijn voor de gebruiker. Als de afbeelding bijvoorbeeld tekst bevat, kan een van de tags het inzicht TextResults bevatten, dat de herkende tekst bevat. Als Bing een entiteit (dat wil zeggen, een in de praktijk bekende/populaire persoon, plaats of ding) in de afbeelding herkent, kan een van de tags de entiteit identificeren. Visual Search retourneert ook een diverse reeks termen (tags) die zijn afgeleid van de ingevoerde afbeelding. Met deze tags kunnen gebruikers concepten verkennen die in de installatie kopie worden gevonden. Als de invoerafbeelding bijvoorbeeld een beroemde atleet bevat, kan een van de tags Sport zijn, die koppelingen naar afbeeldingen van sport bevat.

Elke tag bevat een weergavenaam die u kunt gebruiken om het inzicht te categoriseren, heen begrenzingsvak dat het interessegebied identificeert waarop het inzicht van toepassing is, de inzichten zelf, en een miniatuur van de afbeelding. Als de afbeelding bijvoorbeeld van een persoon is die een sporttrui draagt, kan een van de tags een begrenzingsvak bevatten dat de trui begrenst en VisualSearch- en ProductVisualSearch-inzichten bevatten. En een andere tag kan een ImageResults-inzicht bevatten met een URL voor een /images/search-API-aanvraag om afbeeldingen te krijgen die gerelateerd zijn aan het onderwerp of een zoek-URL van Bing.com die de gebruiker naar de Bing.com-zoekresultaten voor afbeeldingen brengt.

Alle andere tags dan de standaardinzichttag bevatten begrenzingsvakken die interessegebieden in de afbeelding identificeren. Als de afbeelding bijvoorbeeld meerdere herkende personen bevat, kunnen tags begrenzingsvakken voor elk van de mensen bevatten of als de afbeelding herkende kledingstukken bevat, kunnen tags begrenzingsvakken bevatten voor elk herkend kledingstuk. U kunt de begrenzingsvakken gebruiken om hotspots te maken op de afbeelding die, wanneer erop wordt geklikt, details bieden over de inhoud in dat deel van de afbeelding. U moet geen hotspots in een afbeelding opnemen voor begrenzingsvakken die de volledige afbeelding identificeren.

### <a name="text-recognition"></a>Tekstherkenning

Als de afbeelding tekst bevat die door de service wordt herkend, bevat een van de tags het inzicht TextResults (actie). Het inzicht `displayName` bevat de herkende tekst:

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Omdat het veld `displayName` van de tag ##TextRecognition bevat, moet u dit niet gebruiken als categorietitel in de UX. Dat geldt voor elke weergavenaam die begint met ##. Gebruik in plaats daarvan de weergave naam van de actie.

Tekstherkenning kan ook de contactgegevens op visitekaartjes herkennen, zoals telefoonnummers en e-mailadressen. Het selectiekader geeft de locatie van de contactgegevens op het kaartje aan.

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Als de installatie kopie een herkende entiteit bevat, zoals een cultuur bekende/populaire persoon, plaats of ding, kan een van de Tags een entiteit inzicht bevatten. De `mainEntity` `data` velden en zijn alleen beschikbaar als het `enableEntityData` kenmerk in de `Content-Type` kop is ingesteld op `true` .

```json
{
  "image" : {
    "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
  },
  "displayName" : "Statue of Liberty",
  "boundingBox" : {
    "queryRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    },
    "displayRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    }
  },
  "actions" : [
    {
      "_type" : "ImageEntityAction",
      "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
      "displayName" : "Statue of Liberty",
      "actionType" : "Entity",
      "mainEntity" : {
        "name" = "Statue of liberty",
        "bingId" : "..."
      },
      "data" : {
        "id" : "https://api.cognitive.microsoft.com/api/v7/entities/...",
        "readLink": "https://www.bingapis.com/api/v7/search?q=...",
        "readLinkPingSuffix": "...",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
                "name": "CC-BY-SA",
                "url": "http://creativecommons.org/licenses/by-sa/3.0/",
                "urlPingSuffix": "..."
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/...",
            "urlPingSuffix": "..."
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q=...",
        "webSearchUrlPingSuffix": "...",
        "name": "Statue of Liberty",
        "image": {
          "thumbnailUrl": "https://tse1.mm.bing.net/th?id=...",
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/...",
          "hostPageUrlPingSuffix": "...",
          "width": 50,
          "height": 50,
          "sourceWidth": 474,
          "sourceHeight": 598
        },
        "description" : "...",
        "bingId": "..."
        }
      }
  ]
}
```

## <a name="see-also"></a>Zie ook

- [Wat is de Bing Visual Search-API?](../overview.md)
- [Zelfstudie: Een Visual Search-web-app met één pagina maken](../tutorial-bing-visual-search-single-page-app.md)