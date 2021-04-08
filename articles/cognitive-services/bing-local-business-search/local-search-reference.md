---
title: Naslag informatie Bing Local Business Search API V7
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat technische details over de reactie objecten en de query parameters en-headers die van invloed zijn op de zoek resultaten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 9791d99598fe3d043c42a37e2f4993edd6c5b3ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96487130"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Naslag informatie Bing Local Business Search API V7

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

De lokale Business Search-API stuurt een zoek query naar Bing om resultaten te verkrijgen die restaurants, Hotels of andere lokale bedrijven bevatten. Voor plaatsen kan de query de naam van de lokale onderneming of een categorie opgeven (bijvoorbeeld restaurants dichtbij). Entiteitsresultaten zijn personen, plaatsen of dingen. Plaats in deze context zijn bedrijfs entiteiten, Staten, landen/regio's enz.  

Deze sectie bevat technische details over de reactie objecten en de query parameters en-headers die van invloed zijn op de zoek resultaten. Voor voor beelden die laten zien hoe u aanvragen doet, raadpleegt u [Local Business Search C# Quick](quickstarts/local-quickstart.md) start of [Local Business Search Java Quick](quickstarts/local-search-java-quickstart.md)start. 
  
Zie [kopteksten](#headers)voor informatie over kopteksten die aanvragen moeten bevatten.  
  
Zie [query parameters](#query-parameters)voor meer informatie over query parameters die aanvragen moeten bevatten.  
  
Zie [Response Objects](#response-objects)(Engelstalig) voor meer informatie over de JSON-objecten die het antwoord bevat.

Zie [vereisten voor gebruik en weer gave](../bing-web-search/use-display-requirements.md)voor meer informatie over het toegestane gebruik en de weer gave van resultaten.


  
## <a name="endpoint"></a>Eindpunt  
Als u lokale bedrijfs resultaten wilt aanvragen, stuurt u een GET-aanvraag naar: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
Voor de aanvraag moet het HTTPS-protocol worden gebruikt.  
  
> [!NOTE]
> De maximale URL-lengte is 2.048 tekens. Om ervoor te zorgen dat de lengte van de URL de limiet niet overschrijdt, moet de maximum lengte van de query parameters kleiner zijn dan 1.500 tekens. Als de URL langer is dan 2.048 tekens, retourneert de server 404 niet gevonden.  
  
  
## <a name="headers"></a>Kopteksten  
Hieronder ziet u de kopteksten die een aanvraag en antwoord kan bevatten.  
  
|Header|Description|  
|------------|-----------------|  
|Accepteren|Optionele aanvraagheader.<br /><br /> Het standaard media type is Application/JSON. Als u wilt opgeven dat het antwoord [JSON-LD](https://json-ld.org/)gebruikt, stelt u de Accept-header in op Application/LD + JSON.|  
|<a name="acceptlanguage"></a>Accept-Language|Optionele aanvraagheader.<br /><br /> Een door komma's gescheiden lijst met talen die moet worden gebruikt voor gebruikersinterfacetekenreeksen. De lijst is in aflopende volgorde van voorkeur. Zie [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) voor meer informatie, waaronder de verwachte indeling.<br /><br /> Deze header en de queryparameter [setLang](#setlang) sluiten elkaar uit&mdash;geef ze niet beide op.<br /><br /> Als u deze header instelt, moet u ook de queryparameter cc opgeven. Om de markt te bepalen waarvoor resultaten moeten worden geretourneerd, gebruikt Bing de eerste ondersteunde taal die wordt gevonden in de lijst en combineert deze met de parameterwaarde `cc`. Als de lijst geen ondersteunde taal bevat, vindt Bing de dichtstbijzijnde taal en markt die de aanvraag ondersteunen, of gebruikt een geaggregeerde of standaardmarkt voor de resultaten. Zie de header BingAPIs-Market om de markt te bepalen die Bing heeft gebruikt.<br /><br /> Gebruik deze header en de queryparameter `cc` alleen als u meerdere talen opgeeft. Gebruik anders de queryparameters [mkt](#mkt) en [setLang](#setlang).<br /><br /> Een gebruikersinterfacetekenreeks is een tekenreeks die wordt gebruikt als label in een gebruikersinterface. Er zijn maar weinig gebruikersinterfacetekenreeksen in de JSON-antwoordobjecten. De opgegeven taal wordt toegepast op koppelingen naar Bing.com-eigenschappen in de antwoordobjecten.|  
|<a name="market"></a>BingAPIs-Market|Antwoordheader.<br /><br /> De markt die wordt gebruikt door de aanvraag. Het formulier is \<languageCode\> - \<countryCode\> . Bijvoorbeeld: nl-NL.|  
|<a name="traceid"></a>BingAPIs-TraceId|Antwoordheader.<br /><br /> De id van de logboekvermelding die de details van de aanvraag bevat. Registreer deze id wanneer er een fout optreedt. Als u het probleem niet kunt vaststellen en oplossen, neemt u deze id op bij de andere informatie die u aan het ondersteuningsteam verstrekt.|  
|<a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key|Vereiste aanvraagheader.<br /><br /> De abonnementssleutel die u hebt ontvangen toen u zich hebt geregistreerd voor deze service in [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma"></a>Pragma|Optionele aanvraagheader<br /><br /> Bing retourneert standaard cache-inhoud, indien beschikbaar. Om te voorkomen dat Bing inhoud uit de cache retourneert, stelt u de Pragma-header in op no-cache (bijvoorbeeld: Pragma: no-cache).
|<a name="useragent"></a>User-Agent|Optionele aanvraagheader.<br /><br /> De gebruikersagent waarvan de aanvraag afkomstig is. Bing gebruikt de user-agent om mobiele gebruikers een geoptimaliseerde ervaring te bieden. Hoewel dit optioneel is, wordt u aangeraden deze header altijd op te geven.<br /><br /> De user-agent moet dezelfde tekenreeks zijn die elke veelgebruikte browser verzendt. Zie [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) voor meer informatie over gebruikersagenten.<br /><br /> Hier volgen enkele voorbeelden van user-agent-tekenreeksen.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; nl-nl; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; zoals Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) zoals Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 zoals Mac OS X) AppleWebKit/537.51.1 (KHTML, zoals Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid"></a>X-MSEdge-ClientID|Optionele aanvraag- en antwoord-header.<br /><br /> Bing gebruikt deze header om gebruikers consistent gedrag te bieden bij Bing API-aanroepen. Bing introduceert nieuwe functies en verbeteringen vaak in flights, en gebruikt de client-ID als sleutel voor het toewijzen van verkeer aan verschillende flights. Als u niet dezelfde client-id gebruikt voor een gebruiker voor meerdere aanvragen, kan Bing de gebruiker toewijzen aan meerdere conflicterende flights. Toewijzing aan meerdere conflicterende flights kan leiden tot een inconsistente gebruikerservaring. Als de tweede aanvraag bijvoorbeeld een andere flighttoewijzing heeft dan de eerste, kan de ervaring onverwacht zijn. Bing kan de client-id ook gebruiken om webresultaten aan te passen aan de zoekgeschiedenis van die client-id, waardoor de gebruiker een rijkere ervaring krijgt.<br /><br /> Bing gebruikt deze header ook om de rangschikking van resultaten te verbeteren door de activiteit te analyseren die wordt gegenereerd door een client-id. De relevantie-verbeteringen helpen de kwaliteit van de resultaten die door Bing-API's worden geleverd te verbeteren, en maken op hun beurt hogere doorklikpercentages mogelijk voor de API-consument.<br /><br /> **BELANGRIJK:** Hoewel deze header optioneel is, dient u deze als verplicht te beschouwen. Door de client-id te behouden voor meerdere aanvragen voor dezelfde combinatie van eindgebruiker en apparaat, zorgt u voor 1) een consistente gebruikerservaring van de API-consument, en 2) hogere doorklikpercentages via een betere kwaliteit van de resultaten van de Bing-API's.<br /><br /> Hieronder volgen de basisgebruiksregels die van toepassing zijn op deze header.<br /><ul><li>Elke gebruiker die uw toepassing op het apparaat gebruikt, moet een unieke door Bing gegenereerde client-id hebben.<br /><br/>Als u deze header niet in de aanvraag opneemt, genereert Bing een id en retourneert deze in de X-MSEdge-ClientID-antwoordheader. De enige keer dat u deze header NIET in een aanvraag moet opnemen, is de eerste keer dat de gebruiker uw app op dat apparaat gebruikt.<br /><br/></li><li>Gebruik de client-id voor elke Bing-API-aanvraag die uw app voor deze gebruiker op het apparaat doet.<br /><br/></li><li>**Attentie:** U moet ervoor zorgen dat deze client-ID niet kan worden gekoppeld aan de gegevens van een authenticatable-gebruikers account.</li><br/><li>Maak de client-id persistent. Gebruik in een browser-app een persistent HTTP-cookie om ervoor te zorgen dat de id in alle sessies wordt gebruikt. Gebruik geen sessiecookie. Voor andere apps, zoals mobiele apps, gebruikt u de persistente opslag van het apparaat om de id persistent te maken.<br /><br/>De volgende keer dat de gebruiker uw app op dat apparaat gebruikt, haalt u de client-id op die u persistent hebt gemaakt.</li></ul><br /> **OPMERKING:** Bing-antwoorden kunnen deze header al dan niet bevatten. Als het antwoord deze header bevat, registreert u de client-id en gebruikt u deze voor alle volgende Bing-aanvragen voor de gebruiker op dat apparaat.<br /><br /> **OPMERKING:** als u de X-MSEdge-ClientID opneemt, moet u geen cookies opnemen in de aanvraag.|  
|<a name="clientip"></a>X-MSEdge-ClientIP|Optionele aanvraagheader.<br /><br /> Het IPv4- of IPv6-adres van het clientapparaat. Het IP-adres wordt gebruikt voor het detecteren van de locatie van de gebruiker. Bing gebruikt de locatie-informatie om het gedrag van Veilig Zoeken te bepalen.<br /><br /> **OPMERKING:** Hoewel dit optioneel is, wordt u aangeraden deze header en de X-Search-Location-header altijd op te geven.<br /><br /> Verdoezel het adres niet (bijvoorbeeld door het laatste octet te wijzigen in 0). Wanneer u het adres verdoezelt, is de locatie totaal niet in de buurt van de werkelijke locatie van het apparaat, wat ertoe kan leiden dat Bing onjuiste resultaten geeft.|  
|<a name="location"></a>X-Search-Location|Optionele aanvraagheader.<br /><br /> Een met puntkomma's gescheiden lijst met sleutel-waardeparen die de geografische locatie van de client beschrijven. Bing gebruikt de locatie-informatie om het gedrag van Veilig Zoeken te bepalen en relevante lokale inhoud te retourneren. Geef het sleutel/waarde-paar op als \<key\> : \<value\> . Hier volgen de sleutels die u gebruikt om de locatie van de gebruiker op te geven.<br /><br /><ul><li>lat &mdash; de breedte graad van de client locatie, in graden. De breedtegraad moet groter dan of gelijk zijn aan -90.0 en kleiner dan of gelijk aan +90.0. Negatieve waarden geven zuidelijke breedtegraden aan, en positieve waarden noordelijke.<br /><br /></li><li>lang &mdash; de lengte graad van de locatie van de client, in graden. De lengtegraad moet groter dan of gelijk zijn aan -180.0 en kleiner dan of gelijk aan +180.0. Negatieve waarden geven westelijke lengtegraden aan, en positieve waarden oostelijke.<br /><br /></li><li>&mdash;de RADIUS opnieuw, in meters, waarmee de horizontale nauw keurigheid van de coördinaten wordt opgegeven. Geef de waarde door die wordt geretourneerd door de locatieservice van het apparaat. Typische waarden zijn bijvoorbeeld 22 m voor GPS/Wi-Fi, 380 m voor triangulatie op basis van gsm-masten en 18.000 m voor reverse IP-lookup.<br /><br /></li><li>TS &mdash; de UTC-time stamp van een UNIX van wanneer de client zich op de locatie bevond. (Het UNIX-tijdstempel is het aantal seconden sinds 1 januari 1970.)<br /><br /></li><li>head&mdash;Optioneel. De relatieve koers of reisrichting van de client. Geef de reisrichting op als graden van 0 t/m 360, gerekend met de klok mee ten opzichte van het ware noorden. Geef deze sleutel alleen op als de `sp`-sleutel niet nul is.<br /><br /></li><li>SP &mdash; de horizontale snelheid (snelheid), in meters per seconde, die het client apparaat reist.<br /><br /></li><li>ALT &mdash; de hoogte van het client apparaat, in meters.<br /><br /></li><li>are&mdash;Optioneel. De straal, in meters, die de verticale nauwkeurigheid van de coördinaten aangeeft. RADIUS wordt standaard ingesteld op 50 kilo meters. Geef deze sleutel alleen op als u de `alt`-sleutel opgeeft.<br /><br /></li></ul> **Opmerking:** Hoewel deze sleutels optioneel zijn, hoe meer informatie die u verstrekt, hoe nauw keuriger de resultaten van de locatie zijn.<br /><br /> **Opmerking:** U wordt geadviseerd om altijd de geografische locatie van de gebruiker op te geven. Het opgeven van de locatie is vooral belangrijk als het IP-adres van de client de fysieke locatie van de gebruiker niet nauwkeurig weergeeft (bijvoorbeeld als de client VPN gebruikt). Voor optimale resultaten moet u zowel deze header als de X-MSEdge-ClientIP-header opnemen, maar neem minimaal deze header op.|

> [!NOTE] 
> Vergeet niet dat de gebruiksvoorwaarden naleving van alle toepasselijke wetgeving vereisen, inclusief die over het gebruik van deze headers. In bepaalde rechtsgebieden, zoals Europa, is het bijvoorbeeld vereist om toestemming van de gebruiker te verkrijgen voordat bepaalde traceringsapparaten op gebruikersapparaten wordt geplaatst.
  

## <a name="query-parameters"></a>Queryparameters  
De aanvraag kan de volgende query parameters bevatten. Zie de vereiste kolom voor de vereiste para meters. U moet de URL van de query parameters coderen.  
  
  
|Name|Waarde|Type|Vereist|  
|----------|-----------|----------|--------------|
|<a name="count"></a>count|Het aantal resultaten dat moet worden geretourneerd, te beginnen met de index die is opgegeven door de `offset` para meter.|Tekenreeks|No|   
|<a name="localCategories"></a>localCategories|Lijst met opties waarmee zoek acties op bedrijfs categorie worden gedefinieerd.  Zie [lokale bedrijfs categorieën zoeken](local-categories.md)|Tekenreeks|No|  
|<a name="mkt"></a>mkt|De markt waaruit de resultaten afkomstig zijn. <br /><br />Zie markt codes voor een lijst met mogelijke markt waarden.<br /><br /> **Opmerking:** De lokale Business Search-API ondersteunt momenteel alleen de markt en taal van de VS.<br /><br />|Tekenreeks|Ja|
|<a name="offset"></a>offset|De index voor het starten van de resultaten die zijn opgegeven met de `count` para meter.|Geheel getal|No|  
|<a name="query"></a>Nils|De zoek term van de gebruiker.|Tekenreeks|No|  
|<a name="responseformat"></a>responseFormat|Het media type dat voor het antwoord moet worden gebruikt. Hier volgen de mogelijke niet-hoofdletter gevoelige waarden.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> De standaard waarde is JSON. Zie [Response Objects](#response-objects)(Engelstalig) voor informatie over de JSON-objecten die het antwoord bevat.<br /><br />  Als u JsonLd opgeeft, bevat de antwoord tekst JSON-LD objecten die de zoek resultaten bevatten. Zie [JSON-LD](https://json-ld.org/)voor meer informatie over de JSON-ld.|Tekenreeks|No|  
|<a name="safesearch"></a>safeSearch|Een filter dat wordt gebruikt voor het filteren van inhoud voor volwassenen. Hier volgen de mogelijke niet-hoofdlettergevoelige filterwaarden.<br /><ul><li>Off &mdash; webpagina's retour neren met tekst, afbeeldingen of Video's voor volwassenen.<br /><br/></li><li>Gemiddeld &mdash; webpaginas retour neren met volwassene tekst, maar geen installatie kopieën of Video's voor volwassenen.<br /><br/></li><li>&mdash;Geen webpagina's retour neren met tekst, afbeeldingen of Video's voor volwassenen.</li></ul><br /> De standaardwaarde is Moderate.<br /><br /> **Opmerking:** Als de aanvraag afkomstig is van een markt waarvoor het volwassen beleid van Bing is `safeSearch` ingesteld op strikt, negeert Bing de `safeSearch` waarde en wordt strikt gebruikt.<br/><br/>**OPMERKING:** Als u de zoekoperator `site:` gebruikt, is het mogelijk dat het antwoord inhoud voor volwassenen bevat, ongeacht de instelling van de queryparameter `safeSearch`. Gebruik `site:` alleen als u zich bewust bent van de inhoud op de site en uw scenario de mogelijkheid van inhoud voor volwassenen ondersteunt. |Tekenreeks|No|  
|<a name="setlang"></a>setLang|De taal die moet worden gebruikt voor gebruikersinterfacetekenreeksen. Geef de taal op met behulp van de tweeletterige ISO 639-1 taalcode. De taalcode voor Nederlands is bijvoorbeeld NL. De standaardwaarde is EN (Engels).<br /><br /> Hoewel dit optioneel is, moet u altijd de taal opgeven. Doorgaans stelt u `setLang` in op dezelfde taal die is opgegeven door `mkt`, tenzij de gebruiker wil dat gebruikersinterfacetekenreeksen in een andere taal worden weergegeven.<br /><br /> Deze parameter en de header [Accept-Language](#acceptlanguage) sluiten elkaar uit&mdash;geef ze niet beide op.<br /><br /> Een gebruikersinterfacetekenreeks is een tekenreeks die wordt gebruikt als label in een gebruikersinterface. Er zijn maar weinig gebruikersinterfacetekenreeksen in de JSON-antwoordobjecten. De opgegeven taal wordt ook toegepast op koppelingen naar Bing.com-eigenschappen in de antwoordobjecten.|Tekenreeks|No| 


## <a name="response-objects"></a>Antwoord objecten  
Hieronder ziet u de JSON-antwoord objecten die het antwoord kan bevatten. Als de aanvraag is voltooid, is het object op het hoogste niveau in het antwoord het [SearchResponse](#searchresponse) -object. Als de aanvraag mislukt, is het object op het hoogste niveau het [ErrorResponse](#errorresponse) -object.


|Object|Description|  
|------------|-----------------|  
|[Brengen](#place)|Definieert informatie over een lokale onderneming, zoals een restaurant of Hotel.|  

  
### <a name="error"></a>Fout  
Hiermee definieert u de fout die is opgetreden.  
  
|Element|Beschrijving|Type|  
|-------------|-----------------|----------|  
|<a name="error-code"></a>gecodeerd|De fout code waarmee de fout categorie wordt aangeduid. Zie [fout codes](#error-codes)voor een lijst met mogelijke codes.|Tekenreeks|  
|<a name="error-message"></a>message|Een beschrijving van de fout.|Tekenreeks|  
|<a name="error-moredetails"></a>moreDetails|Een beschrijving die extra informatie biedt over de fout.|Tekenreeks|  
|<a name="error-parameter"></a>bepaalde|De query parameter in de aanvraag die de fout heeft veroorzaakt.|Tekenreeks|  
|<a name="error-subcode"></a>subCode|De fout code die de fout identificeert. Als bijvoorbeeld `code` InvalidRequest is, `subCode` kan ParameterInvalid of ParameterInvalidValue zijn. |Tekenreeks|  
|<a name="error-value"></a>Value|De waarde van de query parameter die ongeldig is.|Tekenreeks|  
  

### <a name="errorresponse"></a>ErrorResponse  
Het object op het hoogste niveau dat het antwoord bevat wanneer de aanvraag is mislukt.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|_type|Type hint.|Tekenreeks|  
|<a name="errors"></a>bufferoverschrijdingsfouten|Een lijst met fouten die de redenen beschrijven waarom de aanvraag is mislukt.|[Fout](#error)[]|  

  
  
### <a name="license"></a>Licentie  
Hiermee wordt de licentie gedefinieerd waarmee de tekst of foto kan worden gebruikt.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|naam|De naam van de licentie.|Tekenreeks|  
|url|De URL naar een website waar de gebruiker meer informatie over de licentie kan krijgen.<br /><br /> Gebruik de naam en URL om een Hyper link te maken.|Tekenreeks|  


### <a name="link"></a>Koppeling  
Hiermee worden de onderdelen van een Hyper Link gedefinieerd.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|_type|Type hint.|Tekenreeks|  
|tekst|De weergave tekst.|Tekenreeks|  
|url|Een URL. Gebruik de URL en weergave tekst om een Hyper link te maken.|Tekenreeks|  
  


  
### <a name="organization"></a>Organisatie  
Hiermee wordt een uitgever gedefinieerd.  
  
Houd er rekening mee dat een uitgever hun naam of hun website of beide kan opgeven.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|naam|De naam van de uitgever.|Tekenreeks|  
|url|De URL naar de website van de uitgever.<br /><br /> Houd er rekening mee dat de uitgever mogelijk geen website levert.|Tekenreeks|  
  
  

### <a name="place"></a>Locatie  
Definieert informatie over een lokale onderneming, zoals een restaurant of Hotel.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|_type|Type hint, die kan worden ingesteld op een van de volgende opties:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restaurant</ul><li>|Tekenreeks|  
|adres|Het post adres van de locatie waar de entiteit zich bevindt.|PostalAddress|  
|entityPresentationInfo|Aanvullende informatie over de entiteit, zoals hints, die u kunt gebruiken om het type van de entiteit te bepalen. Bijvoorbeeld of het nu gaat om een restaurant of Hotel. Het `entityScenario` veld wordt ingesteld op lijst item.|EntityPresentationInfo|  
|naam|De naam van de entiteit.|Tekenreeks|  
|telefoon|Het telefoon nummer van de entiteit.|Tekenreeks|  
|url|De URL naar de website van de entiteit.<br /><br /> Gebruik deze URL samen met de naam van de entiteit om een Hyper link te maken waarop wordt geklikt op de gebruiker van de website van de entiteit.|Tekenreeks|  
|webSearchUrl|De URL naar het Zoek resultaat van Bing voor deze locatie.|Tekenreeks| 
  
  
### <a name="querycontext"></a>QueryContext  
Hiermee wordt de query context gedefinieerd die door Bing voor de aanvraag wordt gebruikt.  
  
|Element|Beschrijving|Type|  
|-------------|-----------------|----------|  
|adultIntent|Een Booleaanse waarde die aangeeft of de opgegeven query volwassen intentie heeft. De waarde is **True** als de query volwassen intentie heeft; anders **False**.|Booleaans|  
|alterationOverrideQuery|De query teken reeks die moet worden gebruikt om Bing te dwingen de oorspronkelijke teken reeks te gebruiken. Als de query reeks bijvoorbeeld *Saling Downwind* is, is de query reeks opheffen *+ Saling Downwind*. Vergeet niet om de query teken reeks te coderen die resulteert in *% 2Bsaling + Downwind*.<br /><br /> Dit veld wordt alleen opgenomen als de oorspronkelijke query reeks een spel fout bevat.|Tekenreeks|  
|alteredQuery|De query teken reeks die door Bing wordt gebruikt om de query uit te voeren. Bing gebruikt de gewijzigde query reeks als de oorspronkelijke query reeks spel fouten bevat. Als de query reeks bijvoorbeeld is `saling downwind` , wordt de gewijzigde query reeks `sailing downwind` .<br /><br /> Dit veld wordt alleen opgenomen als de oorspronkelijke query reeks een spel fout bevat.|Tekenreeks|  
|askUserForLocation|Een Booleaanse waarde die aangeeft of Bing de locatie van de gebruiker nodig heeft om nauw keurige resultaten te leveren. Als u de locatie van de gebruiker hebt opgegeven met behulp van de [x-MSEdge-client](#clientip) en [x-Search-locatie](#location) headers, kunt u dit veld negeren.<br /><br /> Voor locatie bewuste query's, zoals ' nu weer geven ' of ' restaurants vlakbij ' waarvoor de locatie van de gebruiker voor nauw keurige resultaten nodig is, wordt dit veld ingesteld op **True**.<br /><br /> Voor locatie bewuste query's die de locatie bevatten (bijvoorbeeld ' Seattle weer '), is dit veld ingesteld op **Onwaar**. Dit veld wordt ook ingesteld op **Onwaar** voor query's die geen locatie ondervinden, zoals ' best verkochte '.|Booleaans|  
|originalQuery|De query reeks zoals opgegeven in de aanvraag.|Tekenreeks|  

### <a name="identifiable"></a>Persoonlijke

|Name|Waarde|Type|  
|-------------|-----------------|----------|
|id|Een resource-id|Tekenreeks|
 
### <a name="rankinggroup"></a>RankingGroup
Hiermee wordt een groep met zoek resultaten gedefinieerd, zoals Mainline.

|Name|Waarde|Type|  
|-------------|-----------------|----------|
|vermeldingen|Een lijst met zoek resultaten die in de groep moeten worden weer gegeven.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Hiermee wordt een item in de zoek resultaten gedefinieerd dat moet worden weer gegeven.

|Name|Waarde|Type|  
|-------------|-----------------|----------|
|resultIndex|Een op nul gebaseerde index van het item in het antwoord dat moet worden weer gegeven. Als het item dit veld niet bevat, geeft u alle items in het antwoord weer. U kunt bijvoorbeeld alle nieuws artikelen in het nieuws antwoord weer geven.|Geheel getal|
|answerType|Het antwoord dat het item bevat dat moet worden weer gegeven. Bijvoorbeeld nieuws.<br /><br />Gebruik het type om het antwoord te vinden in het SearchResponse-object. Het type is de naam van een SearchResponse-veld.<br /><br /> Gebruik echter alleen het antwoord type als dit object het veld waarde bevat. anders negeert u deze.|Tekenreeks|
|textualIndex|De index van het antwoord in textualAnswers dat moet worden weer gegeven.| Niet-ondertekend geheel getal|
|waarde|De ID waarmee een antwoord wordt weer gegeven of een item van een antwoord dat moet worden weer gegeven. Als de ID een antwoord identificeert, geeft u alle items van het antwoord weer.|Persoonlijke|

### <a name="rankingresponse"></a>RankingResponse  
Definieert waar de inhoud van de pagina met zoek resultaten moet worden geplaatst en in welke volg orde.  
  
|Name|Waarde|  
|----------|-----------|  
|<a name="ranking-mainline"></a>mainline|De zoek resultaten worden weer gegeven in de Mainline.|  
|<a name="ranking-pole"></a>pool|De zoek resultaten die de meest zicht bare behandeling moeten worden geboden (bijvoorbeeld weer gegeven boven de mainline en Sidebar).|  
|<a name="ranking-sidebar"></a>zijbalk|De zoek resultaten die in de zijbalk worden weer gegeven.| 

### <a name="searchresponse"></a>SearchResponse  
Hiermee definieert u het object op het hoogste niveau dat het antwoord bevat wanneer de aanvraag is voltooid.  
  
Houd er rekening mee dat als de service een DOS-aanval (Denial of service) vermoedt, de aanvraag slaagt (HTTP-status code is 200 OK). de hoofd tekst van de reactie is echter leeg.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|_type|Type hint, die is ingesteld op SearchResponse.|Tekenreeks|  
|plaatst|Een lijst met entiteiten die relevant zijn voor de zoek query.|JSON-object|  
|queryContext|Een object dat de query reeks bevat die Bing heeft gebruikt voor de aanvraag.<br /><br /> Dit object bevat de query reeks die is ingevoerd door de gebruiker. Het kan ook een gewijzigde query reeks bevatten die Bing heeft gebruikt voor de query als de query reeks een spel fout bevat.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Foutcodes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert.  
  
|Statuscode|Description|  
|-----------------|-----------------|  
|200|Voltooid.|  
|400|Een van de queryparameters ontbreekt of is ongeldig.|  
|401|De abonnements sleutel ontbreekt of is ongeldig.|  
|403|De gebruiker is geverifieerd (bijvoorbeeld omdat er een geldige abonnements sleutel is gebruikt), maar ze hebben geen machtiging voor de aangevraagde resource.<br /><br /> Bing kan ook deze status retour neren als de aanroeper het quotum voor query's per maand heeft overschreden.|  
|410|De aanvraag heeft HTTP gebruikt in plaats van het HTTPS-protocol. HTTPS is het enige ondersteunde protocol.|  
|429|De aanroeper heeft het quotum van de query's per seconde overschreden.|  
|500|Onverwachte serverfout.|

Als de aanvraag mislukt, bevat het antwoord een [ErrorResponse](#errorresponse) -object, dat een lijst bevat met [fout](#error) objecten die beschrijven wat de oorzaak van het probleem is. Als de fout is gerelateerd aan een para meter, `parameter` identificeert het veld de para meter die het probleem vormt. En als de fout is gerelateerd aan een parameter waarde, `value` identificeert het veld de waarde die niet geldig is.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Hier volgen de mogelijke fout code en waarden voor de subfout code.

|Code|SubCode|Description
|-|-|-
|Server Error|UnexpectedError<br/>ResourceError<br/>Niet geïmplementeerd|HTTP-status code is 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Geblokkeerd|Bing retourneert InvalidRequest wanneer een deel van de aanvraag ongeldig is. Een vereiste para meter ontbreekt bijvoorbeeld of een parameter waarde is niet geldig.<br/><br/>Als de fout ParameterMissing of ParameterInvalidValue is, is de HTTP-status code 400.<br/><br/>Als u het HTTP-protocol gebruikt in plaats van HTTPS, retourneert Bing HttpNotAllowed en de HTTP-status code is 410.
|RateLimitExceeded|Geen subcodes|Bing retourneert RateLimitExceeded wanneer u het quotum voor query's per seconde (QPS) of query's per maand (QPM) overschrijdt.<br/><br/>Als u QPS overschrijdt, retourneert Bing de HTTP-status code 429 en als u QPM overschrijdt, retourneert Bing 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing retourneert InvalidAuthorization wanneer Bing de oproepende functie niet kan verifiëren. De `Ocp-Apim-Subscription-Key` koptekst ontbreekt bijvoorbeeld of de abonnements sleutel is niet geldig.<br/><br/>Redundantie treedt op als u meer dan één verificatie methode opgeeft.<br/><br/>Als de fout InvalidAuthorization is, is de HTTP-status code 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing retourneert InsufficientAuthorization wanneer de aanroeper geen machtigingen heeft voor toegang tot de resource. Dit kan gebeuren als de abonnements sleutel is uitgeschakeld of is verlopen. <br/><br/>Als de fout InsufficientAuthorization is, is de HTTP-status code 403.

## <a name="next-steps"></a>Volgende stappen
- [Quickstart over Business Search](quickstarts/local-quickstart.md)
- [Quickstart over Bing Local Business Search met Java](quickstarts/local-search-java-quickstart.md)
- [Quick start voor lokaal bedrijfs Zoek knooppunt](quickstarts/local-search-node-quickstart.md)
- [Quickstart over zoeken naar lokale bedrijven met Python](quickstarts/local-search-python-quickstart.md)