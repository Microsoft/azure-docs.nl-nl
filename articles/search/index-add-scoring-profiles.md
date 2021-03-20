---
title: Zoek classificatie verhogen met behulp van Score profielen
titleSuffix: Azure Cognitive Search
description: Verhoog de zoek positie scores voor Azure Cognitive Search resultaten door Score profielen toe te voegen.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 97797e309c32c6ea996d5ae1901b9a266a683173
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537630"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Scoreprofielen toevoegen aan een Azure Cognitive Search-index

*Score* berekent een zoek score voor elk item in een gerangschikte resultatenset. Aan elk item in een zoek resultatenset wordt een zoek Score toegewezen en vervolgens het hoogste niveau van de laagste waarde.

 In azure Cognitive Search wordt gebruikgemaakt van de standaard score voor het berekenen van een eerste score, maar u kunt de berekening aanpassen via een *Score profiel*. Score profielen geven u meer controle over de rang schikking van items in Zoek resultaten. U kunt bijvoorbeeld items verhogen op basis van hun omzet potentieel, nieuwere items promoten of mogelijk objecten verhogen die in de voor Raad te lang zijn.  

 Het volgende video segment wordt snel doorgestuurd naar de manier waarop Score profielen werken in azure Cognitive Search.
 
> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="scoring-profile-definitions"></a>Score profiel definities

 Een score profiel maakt deel uit van de definitie van de index, die bestaat uit gewogen velden, functies en para meters.  

 Het volgende voor beeld toont een eenvoudig profiel met de naam ' Geo ' om u een idee te geven van hoe een score profiel eruit ziet. Hiermee worden items met de zoek term in het veld naam **Hotel** verhoogd. De functie wordt ook gebruikt `distance` om te voor komen dat items binnen tien kilo meter van de huidige locatie. Als iemand zoekt op de term ' Inn ' en ' Inn ' zich in de naam van het hotel bevinden, worden documenten die hotels bevatten met ' Inn ' binnen een RADIUS van 10 KM van de huidige locatie hoger weer gegeven in de zoek resultaten.  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 Als u dit Score profiel wilt gebruiken, wordt uw query geformuleerd om het profiel op te geven in de query teken reeks. In de onderstaande query ziet u de query parameter `scoringProfile=geo` in de aanvraag.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2020-06-30 
```  

 Met deze query wordt gezocht naar de term ' Inn ' en wordt de huidige locatie door gegeven. U ziet dat deze query andere para meters bevat, zoals `scoringParameter` . Query parameters worden beschreven in [Zoek documenten &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents).  

 Klik op [voor beeld](#bkmk_ex) om een gedetailleerdere voor beeld van een score profiel te bekijken.  

## <a name="what-is-default-scoring"></a>Wat is standaard Score?  
 Score berekent een zoek score voor elk item in een gerangschikte resultatenset. Aan elk item in een zoek resultatenset wordt een zoek Score toegewezen en vervolgens het hoogste niveau van de laagste waarde. Items met de hogere scores worden geretourneerd naar de toepassing. Standaard wordt de bovenste 50 geretourneerd, maar u kunt de `$top` para meter gebruiken om een kleiner of groter aantal items te retour neren (Maxi maal 1000 in één antwoord).  

De zoek score wordt berekend op basis van de statistische eigenschappen van de gegevens en de query. Azure Cognitive Search zoekt documenten die de zoek termen bevatten in de query reeks (sommige of alle, afhankelijk van `searchMode` ), om te voor komen dat documenten met veel exemplaren van de zoek term worden gevonden. De zoek score gaat zelfs hoger omhoog als de term zelden voor komt in de gegevens index, maar gebruikelijk is binnen het document. De basis voor deze aanpak voor het berekenen van de relevantie is de [IDF TF-](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) of term frequentie-inverse document frequentie.  

 Ervan uitgaande dat er geen aangepaste sortering is, worden de resultaten vervolgens gerangschikt op de zoek Score voordat ze worden geretourneerd naar de aanroepende toepassing. Als $top niet is opgegeven, worden 50 items met de hoogste Zoek Score geretourneerd.  

 Zoek Score waarden kunnen worden herhaald in een resultatenset. U kunt bijvoorbeeld 10 items hebben met een Score van 1,2, 20 items met een Score van 1,0 en 20 items met een Score van 0,5. Wanneer meerdere treffers dezelfde Zoek score hebben, wordt de volg orde van dezelfde gescoorde items niet gedefinieerd en is deze niet stabiel. Voer de query opnieuw uit en mogelijk ziet u de positie van items verschuiving. Als er twee items met een identieke Score worden opgegeven, is er geen garantie dat er eerst een wordt weer gegeven.  

## <a name="when-to-add-scoring-logic"></a>Wanneer u Score logica wilt toevoegen 
 U moet een of meer Score profielen maken wanneer het standaard gedrag van de positie niet ver genoeg in de buurt komt van uw bedrijfs doelstellingen. U kunt bijvoorbeeld bepalen dat de relevantie van de zoek actie moet worden aangeraden aan nieuw toegevoegde items. Op dezelfde manier kunt u een veld hebben dat winst marge bevat, of een ander veld dat de potentiële omzet aangeeft. Het verhogen van treffers die voor delen voor uw bedrijf bieden, kan een belang rijke factor zijn bij het kiezen van Score profielen.  

 Volg orde op basis van relevancy wordt ook geïmplementeerd via Score profielen. Bekijk de pagina's met zoek resultaten die u in het verleden hebt gebruikt, waarmee u kunt sorteren op prijs, datum, waardering of relevantie. In azure Cognitive Search worden met Score profielen de optie relevantie gestationeerd. De definitie van relevantie wordt bepaald door u, een predikaat voor zakelijke doel stellingen en het type Zoek ervaring dat u wilt leveren.  

##  <a name="example"></a><a name="bkmk_ex"></a> Hierbij  
 Zoals eerder vermeld, wordt aangepaste Score geïmplementeerd via een of meer Score profielen die in een index schema zijn gedefinieerd.  

 In dit voor beeld wordt het schema van een index met twee Score profielen ( `boostGenre` ,) weer gegeven `newAndHighlyRated` . Elke query op basis van deze index die een van beide profielen als een query parameter bevat, gebruikt het profiel voor het beoordelen van de resultatenset.  

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Werkstroom  
 Als u een aangepast Score patroon wilt implementeren, voegt u een score profiel toe aan het schema dat de index definieert. U kunt Maxi maal 100 score profielen binnen een index hebben (Zie [service limieten](search-limits-quotas-capacity.md)), maar u kunt slechts één profiel tegelijk in een bepaalde query opgeven.  

 Begin met de [sjabloon](#bkmk_template) die in dit onderwerp wordt beschreven.  

 Geef een naam op. Score profielen zijn optioneel, maar als u er een toevoegt, is de naam vereist. Zorg ervoor dat u de naam conventies voor velden volgt (begint met een letter, Vermijd speciale tekens en gereserveerde woorden). Zie de [naamgevings regels &#40;Azure Cognitive Search&#41;](/rest/api/searchservice/naming-rules) voor de volledige lijst.  

 De hoofd tekst van het Score profiel wordt samengesteld op basis van de gewogen velden en functies.  

|||  
|-|-|  
|**Gewicht**|Geef naam/waarde-paren op waarmee een relatief gewicht wordt toegewezen aan een veld. In het [voor beeld](#bkmk_ex)zijn de velden albumTitle, genre en kunstenaarnaam respectievelijk 1,5, 5 en 2. Waarom is het genre nog veel hoger dan het andere? Als de zoek opdracht wordt uitgevoerd op gegevens die enigszins homo geen zijn (zoals in het geval van ' genre ' in de `musicstoreindex` ), moet u mogelijk een grotere afwijking hebben in de relatieve gewichten. In de `musicstoreindex` ' Rock ' wordt bijvoorbeeld weer gegeven als een genre en in beschrijvingen van genres die identiek zijn. Als u een genre beschrijving van genre wilt hebben, heeft het veld genre een veel hoger relatief gewicht nodig.|  
|**Functies**|Wordt gebruikt wanneer aanvullende berekeningen zijn vereist voor specifieke contexten. Geldige waarden zijn `freshness`, `magnitude`, `distance` en `tag`. Elke functie heeft para meters die uniek zijn.<br /><br /> -   `freshness` moet worden gebruikt als u wilt verhogen hoe nieuw of oud een item is. Deze functie kan alleen worden gebruikt met `datetime` velden (EDM. DataTimeOffset). U ziet `boostingDuration` dat het kenmerk alleen wordt gebruikt in combi natie met de `freshness` functie.<br />-   `magnitude` moet worden gebruikt als u wilt verhogen op basis van de maximale of lage waarde van een getal. Scenario's waarbij deze functie wordt aangeroepen, zijn onder andere versterking van winst marge, hoogste prijs, laagste prijs of aantal down loads. Deze functie kan alleen worden gebruikt met dubbele en gehele velden.<br />     Voor de `magnitude` functie kunt u het bereik, hoog naar laag, terugdraaien als u het omgekeerde patroon wilt gebruiken (bijvoorbeeld om minder geprijsde items meer dan hogere items te verhogen). Op basis van een prijs bereik van $100 tot $1, stelt u `boostingRangeStart` 100 en `boostingRangeEnd` op 1 in om de items met een lagere prijs te verhogen.<br />-   `distance` moet worden gebruikt als u wilt verhogen op basis van nabijheid of geografische locatie. Deze functie kan alleen worden gebruikt met `Edm.GeographyPoint` velden.<br />-   `tag` moet worden gebruikt als u de algemene labels tussen documenten en zoek query's wilt verhogen. Deze functie kan alleen worden gebruikt met `Edm.String` en- `Collection(Edm.String)` velden.<br /><br /> **Regels voor het gebruik van functies**<br /><br /> Het functie type ( `freshness` , `magnitude` , `distance` ) `tag` moet kleine letters zijn.<br /><br /> Functies mogen geen null of lege waarden bevatten. Als u veld naam opneemt, moet u deze instellen op iets.<br /><br /> Functions kan alleen worden toegepast op filter bare velden. Zie [Create Index &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/create-index) voor meer informatie over filter bare velden.<br /><br /> Functions kan alleen worden toegepast op velden die zijn gedefinieerd in de verzameling velden van een index.|  

 Nadat de index is gedefinieerd, bouwt u de index op door het index schema te uploaden, gevolgd door documenten. Zie [index maken &#40;azure Cognitive Search REST API&#41;](/rest/api/searchservice/create-index) en [documenten toevoegen, bijwerken of verwijderen &#40;Azure Cognitive Search rest API ](/rest/api/searchservice/addupdate-or-delete-documents)&#41;voor instructies voor deze bewerkingen. Zodra de index is gemaakt, moet u een functioneel Score profiel hebben dat geschikt is voor uw Zoek gegevens.  

##  <a name="template"></a><a name="bkmk_template"></a> Sjabloon  
 In deze sectie worden de syntaxis en sjabloon voor Score profielen weer gegeven. Raadpleeg [index Attributes Reference](#bkmk_indexref) in de volgende sectie voor beschrijvingen van de kenmerken.  

```  
. . .   
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "...",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a> Naslag informatie over index kenmerken  

> [!NOTE]  
>  Een score functie kan alleen worden toegepast op velden die kunnen worden gefilterd.  

|Kenmerk|Beschrijving|  
|---------------|-----------------|  
|`name`|Vereist. Dit is de naam van het Score profiel. Het volgt dezelfde naam conventies van een veld. De naam moet beginnen met een letter, mag geen punten, dubbele punten of @ symbolen bevatten en mag niet beginnen met de zin ' azureSearch ' (hoofdletter gevoelig).|  
|`text`|Bevat de eigenschap Weights.|  
|`weights`|Optioneel. Bevat naam/waarde-paren die elke veld naam en relatief gewicht opgeven. Relatief gewicht moet een positief geheel getal zijn of een getal met een drijvende komma.<br /><br /> Het gewicht wordt gebruikt om het belang van één Doorzoek bare veld ten opzichte van een ander aan te geven.|  
|`functions`|Optioneel. Een score functie kan alleen worden toegepast op velden die kunnen worden gefilterd.|  
|`type`|Vereist voor Score functies. Hiermee wordt het type functie aangegeven dat moet worden gebruikt. Geldige waarden zijn grootte, versheid, afstand en label. U kunt in elk Score profiel meer dan één functie gebruiken. De functie naam moet in kleine letters worden gebruikt.|  
|`boost`|Vereist voor Score functies. Een positief getal dat wordt gebruikt als vermenigvuldiger voor de onbewerkte Score. De waarde mag niet gelijk zijn aan 1.|  
|`fieldname`|Vereist voor Score functies. Een score functie kan alleen worden toegepast op velden die deel uitmaken van de veld verzameling van de index en die kunnen worden gefilterd. Daarnaast introduceert elk functie type extra beperkingen (de versheid wordt gebruikt met datum-en tijd velden, een grootte met gehele getallen of dubbele velden en de afstand met locatie velden). U kunt slechts één veld per functie definitie opgeven. Als u bijvoorbeeld twee keer in hetzelfde profiel een bepaalde grootte wilt gebruiken, moet u twee definities per veld hebben.|  
|`interpolation`|Vereist voor Score functies. Definieert de helling waarvoor de score wordt verhoogd van het begin van het bereik tot het einde van het bereik. Geldige waarden zijn onder andere lineair (standaard), constante, kwadratisch en logaritmisch. Zie [interpolatie instellen](#bkmk_interpolation) voor meer informatie.|  
|`magnitude`|De functie magnitude wordt gebruikt om de classificaties te wijzigen op basis van het bereik van waarden voor een numeriek veld. Enkele van de meest voorkomende gebruiks voorbeelden hiervan zijn:<br /><br /> -   **Ster classificaties:** Wijzig de score op basis van de waarde in het veld ster waardering. Wanneer twee items relevant zijn, wordt het item met de hogere classificatie eerst weer gegeven.<br />-   **Marge:** Wanneer twee documenten relevant zijn, kan een detail handelaar documenten met hogere marges eerst stimuleren.<br />-   **Klik op aantal:** Voor toepassingen die volgen op acties door te klikken, kunt u de grootte gebruiken om items te verhogen die meestal het grootste verkeer krijgen.<br />-   **Download aantallen:** Voor toepassingen die down loads volgen, kunt u met de functie magnitude items met de meeste down loads verhogen.|  
|`magnitude` &#124; `boostingRangeStart`|Hiermee wordt de begin waarde ingesteld van het bereik waarvan de grootte wordt gescoord. De waarde moet een geheel getal zijn of een getal met een drijvende komma. Voor ster waarderingen van 1 tot en met 4 zou dit 1 zijn. Voor marges van meer dan 50% zou dit 50 zijn.|  
|`magnitude` &#124; `boostingRangeEnd`|Hiermee wordt de eind waarde van het bereik ingesteld waarvan de grootte wordt gescoord. De waarde moet een geheel getal zijn of een getal met een drijvende komma. Voor ster waarderingen van 1 tot en met 4 zou dit 4 zijn.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Geldige waarden zijn True of False (standaard). Als deze optie is ingesteld op True, blijft de volledige boost van toepassing op documenten met een waarde voor het doel veld die hoger is dan het bovenste einde van het bereik. Indien onwaar, wordt de boost van deze functie niet toegepast op documenten met een waarde voor het doel veld die buiten het bereik valt.|  
|`freshness`|De functie versheids score wordt gebruikt om de classificatie scores voor items te wijzigen op basis van waarden in `DateTimeOffset` velden. Een item met een recentere datum kan bijvoorbeeld hoger zijn dan oudere items.<br /><br /> Het is ook mogelijk om items zoals Agenda gebeurtenissen te rangschikken met toekomstige datums, zodat items die dichter bij de huidige aanwezig zijn, hoger kunnen worden gerangschikt dan items in de toekomst.<br /><br /> In de huidige service release wordt één einde van het bereik vastgesteld op de huidige tijd. De andere kant is een tijd in het verleden op basis van de `boostingDuration` . Gebruik een negatieve waarde om een reeks keren in de toekomst te stimuleren `boostingDuration` .<br /><br /> De snelheid waarmee de versterking van een maximum-en minimum bereik wordt bepaald door de interpolatie die wordt toegepast op het Score Profiel (Zie de afbeelding hieronder). Als u de verg Roten factor wilt omkeren, kiest u een boost factor kleiner dan 1.|  
|`freshness` &#124; `boostingDuration`|Hiermee stelt u een verval periode in waarna versterking van een bepaald document wordt gestopt. Zie [set boostingDuration](#bkmk_boostdur) in de volgende sectie voor de syntaxis en voor beelden.|  
|`distance`|De functie voor afstands bepaling wordt gebruikt om de Score van documenten te beïnvloeden op basis van hoe dicht of ten opzichte van de geografische locatie van een referentie. De referentie locatie wordt gegeven als onderdeel van de query in een para meter (met de `scoringParameterquery` teken reeks optie) als een Lon-argument.|  
|`distance` &#124; `referencePointParameter`|Een para meter die moet worden door gegeven in query's die als referentie locatie moeten worden gebruikt. `scoringParameter` is een query parameter. Zie [zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents) voor beschrijvingen van query parameters.|  
|`distance` &#124; `boostingDistance`|Een getal dat de afstand in kilo meters aangeeft van de referentie locatie waar het Boosting-bereik eindigt.|  
|`tag`|De tag Score functie wordt gebruikt om de Score van documenten te beïnvloeden op basis van tags in documenten en zoek query's. Documenten met een gemeen schappelijke label met de zoek query worden verhoogd. De labels voor de zoek query worden als een score parameter in elk zoek aanvraag gegeven (met de `scoringParameterquery` optie teken reeks).|  
|`tag` &#124; `tagsParameter`|Een para meter die in query's moet worden door gegeven om labels voor een bepaalde aanvraag op te geven. `scoringParameter` is een query parameter. Zie [zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents) voor beschrijvingen van query parameters.|  
|`functionAggregation`|Optioneel. Is alleen van toepassing wanneer de functies zijn opgegeven. Geldige waarden zijn: Sum (standaard), Average, minimum, maximum en firstMatching. Een zoek Score is één waarde die wordt berekend op basis van meerdere variabelen, met inbegrip van meerdere functies. Dit kenmerk geeft aan hoe de boosts van alle functies worden gecombineerd tot één statistische boost die vervolgens wordt toegepast op de basis document Score. De basis score is gebaseerd op de waarde [TF-IDF](http://www.tfidf.com/) die is berekend op basis van het document en de zoek query.|  
|`defaultScoringProfile`|Bij het uitvoeren van een zoek opdracht als er geen score profiel is opgegeven, wordt de standaard Score gebruikt ([TF-IDF](http://www.tfidf.com/) only).<br /><br /> Een standaard naam voor het Score profiel kan hier worden ingesteld, waardoor Azure Cognitive Search dat profiel gebruiken wanneer er geen specifiek profiel is opgegeven in de zoek opdracht.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a> Interpolatie instellen  
 Met interpolatie kunt u de vorm instellen van de helling die wordt gebruikt voor scores. Omdat score hoog naar laag is, is de helling altijd afnemen, maar de interpolatie bepaalt de curve van de neerwaartse helling. De volgende interpolatie kunnen worden gebruikt:  

| Interpolatie | Beschrijving |  
|-|-|  
|`linear`|Voor items die binnen het bereik Max en min zijn, wordt de Boost toegepast op het item in een voortdurend dalende hoeveelheid. Lineair is de standaard interpolatie voor een score profiel.|  
|`constant`|Voor items die zich binnen het begin-en eind bereik bevinden, wordt een constante Boost toegepast op de positie resultaten.|  
|`quadratic`|In vergelijking met een lineaire interpolatie die een voortdurend dalende Boost heeft, wordt in eerste instantie de kwadratische afname kleiner naarmate het eind bereik wordt gereduceerd. Deze interpolatie optie is niet toegestaan in Tags Score functies.|  
|`logarithmic`|In vergelijking met een lineaire interpolatie die een voortdurend dalende Boost heeft, wordt het logaritmische eerst op een hoger tempo en vervolgens op het einde van het eind bereik verkleind. Deze interpolatie optie is niet toegestaan in Tags Score functies.|  

 ![Constante, lineaire, kwadratische, log10-lijnen in de grafiek](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a> BoostingDuration instellen  
 `boostingDuration` is een kenmerk van de `freshness` functie. U gebruikt deze om een verval periode in te stellen waarna versterking van een bepaald document wordt gestopt. Als u bijvoorbeeld een product lijn of merk voor een promotie periode van tien dagen wilt verhogen, geeft u de periode van 10 dagen op als ' P10D ' voor deze documenten.  

 `boostingDuration` moet zijn opgemaakt als een XSD ' dayTimeDuration-waarde (een beperkte subset van een ISO 8601 duration-waarde). Het patroon hiervoor is: "P [nD] [T [nH] [nM] [nS]]".  

 De volgende tabel bevat enkele voor beelden.  

|Duur|boostingDuration|  
|--------------|----------------------|  
|1 dag|"P1D"|  
|2 dagen en 12 uur|"P2DT12H"|  
|15 minuten|"PT15M"|  
|30 dagen, 5 uur, 10 minuten en 6,334 seconden|"P30DT5H10M 6.334 S"|  

 Zie [XML-schema: data types (w3.org-website)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)voor meer voor beelden.  

## <a name="see-also"></a>Zie ook  

+ [REST API referentie](/rest/api/searchservice/)
+ [Index-API maken](/rest/api/searchservice/create-index)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search?)