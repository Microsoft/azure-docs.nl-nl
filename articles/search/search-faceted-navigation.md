---
title: Een facet navigatie categorie hiërarchie toevoegen
titleSuffix: Azure Cognitive Search
description: Voeg facet navigatie toe voor Self-gerichte filters in zoek toepassingen die worden geïntegreerd met Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 05be5295ae5f8c73c916a21bba7dbc98ab0c5e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002789"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>Facet navigatie in azure Cognitive Search implementeren

Facet navigatie is een filter mechanisme dat zelf gestuurde DrillDown-navigatie biedt in zoek toepassingen. De term ' facet navigatie ' is mogelijk onbekend, maar u hebt deze waarschijnlijk al eerder gebruikt. Zoals in het volgende voor beeld wordt getoond, is facet navigatie niets meer dan de categorieën die worden gebruikt om de resultaten te filteren.

 ![Demo van Azure Cognitive Search-taak Portal](media/search-faceted-navigation/azure-search-faceting-example.png "Demo van Azure Cognitive Search-taak Portal")

Facet navigatie is een alternatief invoer punt om te zoeken. Het biedt een handig alternatief om complexe Zoek expressies hand matig te typen. Facetten kunnen u helpen bij het vinden van wat u zoekt, terwijl u er zeker van bent dat u geen nul resultaten krijgt. Als ontwikkelaar kunt u met facetten de meest nuttige zoek criteria voor het navigeren door uw zoek index weer geven. In online retail toepassingen is facet navigatie vaak gebaseerd op merken, afdelingen (schoenen en kinderen), grootte, prijs, populariteit en Beoordelingen. 

De implementatie van de facet navigatie wijkt af van de verschillende Zoek technologieën. In azure Cognitive Search is facet navigatie gebaseerd op de query tijd, met behulp van velden die u eerder in uw schema hebt kenmerken.

-   In de query's die uw toepassing bouwt, moet een query *facet query parameters* verzenden om de beschik bare facet filter waarden voor die document resultaten te verkrijgen.

-   Als u de resultatenset van het document daad werkelijk wilt bijsnijden, moet de toepassing ook een expressie Toep assen `$filter` .

Bij het ontwikkelen van uw toepassing is het schrijven van code voor het maken van query's het meren deel van het werk. Veel van de toepassings gedragingen die u verwacht van facet navigatie, worden geleverd door de service, inclusief ingebouwde ondersteuning voor het definiëren van bereiken en het ophalen van aantallen voor facet resultaten. De service omvat ook redelijke standaard instellingen waarmee u onhandige navigatie structuren kunt voor komen. 

## <a name="sample-code-and-demo"></a>Voorbeeld code en demo
In dit artikel wordt een portal voor taak zoekopdrachten als voor beeld gebruikt. Het voor beeld wordt geïmplementeerd als een ASP.NET MVC-toepassing.

- Bekijk en test de werk demonstratie online in [Azure Cognitive Search Job Portal-demo](https://aka.ms/azjobsdemo).

- Down load de code van de [Azure-samples opslag plaats op github](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Aan de slag
Als u niet bekend bent met het ontwikkelen van de zoek functie, is het de beste manier om te zien wat de facet navigatie is, dat de mogelijkheden voor zelf zoeken worden weer gegeven. Het is een type inzoom ervaring op basis van vooraf gedefinieerde filters, waarmee u snel Zoek resultaten kunt beperken met behulp van Point-and-click-acties. 

### <a name="interaction-model"></a>Interactie model

De zoek ervaring voor facet navigatie is iteratief, dus laten we het eerst weten als een reeks query's die in reactie op gebruikers acties worden uitgevouwen.

Het begin punt is een toepassings pagina die facet navigatie biedt, die meestal in de omtrek wordt geplaatst. Facet navigatie is vaak een boom structuur, met selectie vakjes voor elke waarde of klik bare tekst. 

1. Met een query die wordt verzonden naar Azure Cognitive Search wordt de facet navigatie structuur opgegeven via een of meer facet query parameters. Zo kan de query bijvoorbeeld bevatten `facet=Rating` , mogelijk met een `:values` of- `:sort` optie om de presentatie verder te verfijnen.
2. De presentatielaag geeft een zoek pagina weer met facet navigatie, met behulp van de facetten die zijn opgegeven in de aanvraag.
3. Gezien een facet navigatie structuur die classificatie bevat, klikt u op 4 om aan te geven dat alleen producten met een classificatie van 4 of hoger moeten worden weer gegeven. 
4. Als antwoord wordt met de toepassing een query verzonden met daarin `$filter=Rating ge 4` 
5. De laag van de presentatie werkt de pagina bij, met een beperkte resultatenset, die alleen de items bevat die voldoen aan de nieuwe criteria (in dit geval de producten met de waarde 4 en up).

Een facet is een query parameter, maar verwar het niet met query-invoer. Het wordt nooit gebruikt als selectie criterium in een query. U kunt in plaats daarvan facet query parameters beschouwen als invoer voor de navigatie structuur die in het antwoord wordt weer gegeven. Voor elke facet query parameter die u opgeeft, wordt door Azure Cognitive Search geëvalueerd hoeveel documenten de gedeeltelijke resultaten voor elke facet waarde hebben.

Let `$filter` op de in stap 4. Het filter is een belang rijk aspect van facet navigatie. Hoewel facetten en filters onafhankelijk zijn in de API, hebt u beide nodig om de ervaring te leveren die u wilt bieden. 

### <a name="app-design-pattern"></a>App-ontwerp patroon

In toepassings code moet het patroon de facet query parameters gebruiken om de facet navigatie structuur te retour neren samen met facet resultaten, plus een $filter expressie.  Met de filter expressie wordt de gebeurtenis Click voor de facet waarde verwerkt. `$filter`U kunt de expressie beschouwen als de code achter de werkelijke bijsnijden van de zoek resultaten die worden geretourneerd naar de presentatielaag. Op basis van een kleuren facet wordt het klikken op de kleur rood uitgevoerd via een `$filter` expressie waarmee alleen de items worden geselecteerd die de kleur rood hebben. 

### <a name="query-basics"></a>Basis informatie over query's

In azure Cognitive Search wordt een aanvraag opgegeven via een of meer query parameters (Zie [documenten zoeken](/rest/api/searchservice/Search-Documents) voor een beschrijving van elk van beide). Geen van de query parameters is vereist, maar u moet ten minste één om een query geldig te maken.

Nauw keurigheid, gezien als de mogelijkheid om irrelevante treffers te filteren, wordt bereikt door middel van een of beide van de volgende expressies:

-   **Search =**  
    De waarde van deze para meter vormt de zoek expressie. Het kan een enkel tekst fragment zijn of een complexe Zoek expressie die meerdere voor waarden en Opera tors bevat. Op de-server wordt een zoek expressie gebruikt voor Zoek opdrachten in volledige tekst, query's uitvoeren op Doorzoek bare velden in de index voor overeenkomende termen, waarbij resultaten worden geretourneerd in rang orde. Als u instelt `search` op NULL, wordt de uitvoering van de query ten opzichte van de volledige index (dat wil zeggen `search=*` ). In dit geval zijn andere elementen van de query, zoals een `$filter` of Score profiel, de primaire factoren die van invloed zijn op welke documenten worden geretourneerd `($filter` ) en in welke volg orde ( `scoringProfile` of `$orderby` ).

-   **$filter =**  
    Een filter is een krachtig mechanisme voor het beperken van de grootte van de zoek resultaten op basis van de waarden van specifieke document kenmerken. A `$filter` wordt eerst geëvalueerd, gevolgd door de facet logica waarmee de beschik bare waarden en de bijbehorende aantallen voor elke waarde worden gegenereerd

Complexe Zoek expressies verminderen de prestaties van de query. Gebruik waar mogelijk goed opgebouwde filter expressies om de nauw keurigheid te verhogen en de query prestaties te verbeteren.

Als u beter inzicht wilt krijgen in hoe een filter nauw keuriger wordt toegevoegd, kunt u een complexe Zoek expressie vergelijken met een uitdrukking die een filter expressie bevat:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Beide query's zijn geldig, maar de tweede is een superieure als u op zoek bent naar niet-motels met parkeren in Seattle.
-   De eerste query is afhankelijk van de specifieke woorden die worden genoemd of die niet worden vermeld in de teken reeks velden, zoals naam, beschrijving en een ander veld dat Doorzoek bare gegevens bevat.
-   De tweede query zoekt naar nauw keurige overeenkomsten op gestructureerde gegevens en is waarschijnlijk veel nauw keuriger.

In toepassingen die facet navigatie bevatten, moet u ervoor zorgen dat elke gebruikers actie over een facet navigatie structuur vergezeld gaat van een kleiner aantal Zoek resultaten. Gebruik een filter expressie om de resultaten te beperken.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Een facet navigatie-app bouwen
U implementeert facet navigatie met Azure Cognitive Search in de code van uw toepassing die de zoek aanvraag bouwt. De facet navigatie is afhankelijk van elementen in het schema dat u eerder hebt gedefinieerd.

Vooraf gedefinieerd in uw zoek index is het `Facetable [true|false]` index kenmerk, ingesteld op geselecteerde velden om het gebruik in een facet navigatie structuur in of uit te scha kelen. Zonder `"Facetable" = true` een veld kan niet worden gebruikt in facet navigatie.

De presentatielaag in uw code biedt de gebruikers ervaring. Het moet een lijst zijn met de onderdelen van de facet navigatie, zoals de labels, waarden, selectie vakjes en de telling. De Azure Cognitive Search REST API is platform neutraal. gebruik dus elke taal en elk platform dat u wilt. Het is belang rijk dat u UI-elementen opneemt die incrementeel vernieuwen ondersteunen, met de bijgewerkte status van de gebruikers interface, terwijl elk extra facet wordt geselecteerd. 

Tijdens het uitvoeren van de query wordt met de code van de toepassing een aanvraag gemaakt die een `facet=[string]` aanvraag parameter bevat waarmee het veld kan worden gefacett door. Een query kan meerdere facetten hebben, zoals `&facet=color&facet=category&facet=rating` elk, gescheiden door een en-teken (&).

Toepassings code moet ook een `$filter` expressie maken voor het afhandelen van de klik gebeurtenissen in facet navigatie. Een `$filter` vermindert de zoek resultaten met behulp van de facet waarde als filter criterium.

Azure Cognitive Search retourneert de zoek resultaten op basis van een of meer voor waarden die u invoert, samen met updates voor de facet navigatie structuur. In azure Cognitive Search is facet navigatie een constructie met één niveau, met facet waarden en aantallen van het aantal resultaten dat voor elk item is gevonden.

In de volgende secties ziet u hoe u elk onderdeel kunt bouwen.

<a name="buildindex"></a>

## <a name="build-the-index"></a>De index maken
Facetatie is ingeschakeld voor een veld per veld in de index via dit index kenmerk: `"Facetable": true` .  
Alle veld typen die mogelijk in facet navigatie kunnen worden gebruikt, zijn `Facetable` standaard. Dergelijke veld typen bevatten `Edm.String` , `Edm.DateTimeOffset` , en alle numerieke veld typen (in feite zijn alle veld typen kunnen alleen `Edm.GeographyPoint` worden gebruikt in facet navigatie). 

Bij het bouwen van een index is een best practice voor facet navigatie het expliciet uitschakelen van facetten uitschakelen voor velden die nooit als een facet moeten worden gebruikt.  In het bijzonder moeten teken reeks velden voor Singleton waarden, zoals een ID of product naam, worden ingesteld op `"Facetable": false` om te voor komen dat ze per ongeluk (en oneffectief) gebruikmaken van facet navigatie. Als u de facet ring uitschakelt en u deze niet nodig hebt, blijft de grootte van de index klein en worden de prestaties meestal verbeterd.

Hieronder volgt een deel van het schema voor de voorbeeld toepassing van de job Portal-demo, met een aantal kenmerken om de grootte te verkleinen:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { "name": "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { "name": "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { "name": "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Zoals u kunt zien in het voorbeeld schema, `Facetable` is uitgeschakeld voor teken reeks velden die niet als facetten mogen worden gebruikt, zoals ID-waarden. Als u de facet ring uitschakelt en u deze niet nodig hebt, blijft de grootte van de index klein en worden de prestaties meestal verbeterd.

> [!TIP]
> Neem als best practice de volledige set index kenmerken voor elk veld op. Hoewel `Facetable` de standaard instelling is ingeschakeld voor bijna alle velden, kunt u elk kenmerk zo instellen dat u rekening moet houden met de implicaties van elk schema besluit. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Controleer de gegevens
De kwaliteit van uw gegevens heeft direct invloed op of de facet navigatie structuur resultatenset zoals u verwacht. Dit is ook van invloed op het gemak van de aanmaak filters om de resultatenset te verminderen.

Als u wilt facetten op merk of prijs, moet elk document waarden bevatten voor *brandname* en *ProductPrice* die geldig, consistent en productief zijn als een filter optie.

Hier volgen enkele herinneringen van wat u kunt reinigen voor:

* Voor elk veld waarop u wilt facetten, vraagt u zich af of het waarden bevat die geschikt zijn als filters in een zelf gerichte zoek opdracht. De waarden moeten korte, beschrijvende en voldoende onderscheidend zijn om een duidelijke keuze te bieden tussen concurrerende opties.
* Spel fouten of bijna overeenkomende waarden. Als u facetten kiest voor kleur en veld waarden zijn oranje en Ornage (een spel fout), wordt een facet dat is gebaseerd op het veld kleur, beide opgenomen.
* De tekst van een gemengde case kan ook wreak vernielen in facet navigatie, met oranje en oranje worden weer gegeven als twee verschillende waarden. 
* Enkele en meervoude versies van dezelfde waarde kunnen resulteren in een afzonderlijk facet voor elke.

Zo kunt u zich Voorst Ellen dat het voorbereiden van de gegevens belang rijk is voor doel treffende facet navigatie.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>De gebruikersinterface bouwen
Als u van de presentatielaag terugkeert, kunt u aan de slag met vereisten die mogelijk niet worden gemist, en begrijpen welke mogelijkheden essentieel zijn voor de zoek ervaring.

In termen van facet navigatie wordt in uw web-of toepassings pagina de facet navigatie structuur weer gegeven, wordt de gebruikers invoer op de pagina gedetecteerd en worden de gewijzigde elementen ingevoegd. 

Voor webtoepassingen wordt AJAX doorgaans gebruikt in de presentatielaag, omdat u hiermee incrementele wijzigingen kunt vernieuwen. U kunt ook ASP.NET MVC of een ander visualisatie platform gebruiken dat verbinding kan maken met een Azure Cognitive Search-service via HTTP. De voorbeeld toepassing waarnaar wordt verwezen in dit artikel--de demo van de **Azure Cognitive Search-taak Portal** – gebeurt als een ASP.NET MVC-toepassing.

In het voor beeld is facet navigatie ingebouwd in de pagina met zoek resultaten. In het volgende voor beeld, dat afkomstig is uit het `index.cshtml` bestand van de voorbeeld toepassing, wordt de statische HTML-structuur weer gegeven voor het weer geven van facet navigatie op de pagina met zoek resultaten. De lijst met facetten wordt dynamisch gebouwd of opnieuw opgebouwd wanneer u een zoek term verzendt, of u kunt een facet selecteren of wissen.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

Het volgende code fragment van de `index.cshtml` pagina bouwt de HTML dynamisch op om het eerste facet, de zakelijke titel, weer te geven. Met soort gelijke functies worden de HTML dynamisch voor de andere facetten gemaakt. Elk facet heeft een label en een aantal, waarin het aantal items wordt weer gegeven dat voor dat facet resultaat is gevonden.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Wanneer u de pagina met zoek resultaten ontwerpt, moet u een mechanisme voor het wissen van facetten toevoegen. Als u selectie vakjes toevoegt, kunt u eenvoudig zien hoe u de filters wist. Voor andere indelingen hebt u mogelijk een brood patroon of een andere creatieve benadering nodig. U kunt bijvoorbeeld in de voorbeeld toepassing Job Search Portal op het `[X]` na een geselecteerd facet klikken om het facet te wissen.

<a name="buildquery"></a>

## <a name="build-the-query"></a>De query maken
Met de code die u voor het maken van query's schrijft, moeten alle delen van een geldige query worden opgegeven, inclusief Zoek expressies, facetten, filters, Score profielen, waarmee alles wordt gebruikt om een aanvraag te formuleren. In deze sectie verkennen we waar facetten in een query passen en hoe filters worden gebruikt met facetten om een gereduceerde resultatenset te leveren.

U ziet dat facetten integraal zijn in deze voorbeeld toepassing. De zoek ervaring in de job Portal-demo is ontworpen rond de facet navigatie en filters. De prominente plaatsing van facet navigatie op de pagina laat zien hoe belang rijk is. 

Een voor beeld is vaak een goede plaats om te beginnen. In het volgende voor beeld, dat uit het bestand is gehaald `JobsSearch.cs` , wordt een aanvraag gebouwd waarmee facet navigatie wordt gemaakt op basis van de zakelijke titel, locatie, boekings type en mini maal salaris. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Een facet query-para meter is ingesteld op een veld en afhankelijk van het gegevens type, kan door komma's gescheiden lijst met `count:<integer>` , `sort:<>` , `interval:<integer>` , en worden uitgebreid met para meters `values:<list>` . Een lijst met waarden wordt ondersteund voor numerieke gegevens bij het instellen van bereiken. Zie [zoeken naar documenten (Azure Cognitive Search-API)](/rest/api/searchservice/Search-Documents) voor gebruiks gegevens.

Naast de facetten moet de aanvraag die is geformuleerd door uw toepassing ook filters maken om de set met kandidaten documenten te beperken op basis van de selectie van een facet waarde. Voor een fiets winkel biedt facet navigatie aanwijzingen voor vragen, zoals *welke kleuren, fabrikanten en soorten fietsen er beschikbaar zijn?*. Het filteren van antwoorden op vragen *, zoals de exacte fietsen rood, Mountain Bikes, in dit prijs bereik?*. Wanneer u op ' rood ' klikt om aan te geven dat alleen rode producten moeten worden weer gegeven, bevat de volgende query die door de toepassing wordt verzonden `$filter=Color eq 'Red'` .

Het volgende code fragment van de `JobsSearch.cs` pagina voegt de geselecteerde zakelijke titel toe aan het filter als u een waarde selecteert in het facet van de zakelijke titel.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Tips en best practices

### <a name="indexing-tips"></a>Indexerings tips
**De efficiëntie van de index verbeteren als u geen zoekvak gebruikt**

Als uw toepassing uitsluitend gebruikmaakt van facet navigatie (dat wil zeggen, geen zoekvak), kunt u het veld markeren als `searchable=false` , `facetable=true` om een compactere index te maken. Bovendien vindt het indexeren alleen plaats op de gehele facet waarden, zonder het woord-of index nummer van de onderdeel onderdelen van een waarde met meerdere woorden.

**Opgeven welke velden kunnen worden gebruikt als facetten**

U kunt het schema van de index gebruiken om te bepalen welke velden beschikbaar zijn voor gebruik als een facet. Ervan uitgaande dat een veld facetbaar is, geeft de query aan welke velden moeten worden gefacett door. Het veld waarover u facetten levert de waarden die onder het label worden weer gegeven. 

De waarden onder elk label worden opgehaald uit de index. Als het facet veld bijvoorbeeld *kleur* is, zijn de waarden die beschikbaar zijn voor aanvullende filters de waarden voor dat veld-rood, zwart, enzovoort.

Alleen voor numerieke en datum/tijd-waarden kunt u expliciet waarden instellen voor het facet veld (bijvoorbeeld `facet=Rating,values:1|2|3|4|5` ). Een waarden lijst is toegestaan voor deze veld typen om de schei ding van facet resultaten te vereenvoudigen in aaneengesloten bereiken (bereiken op basis van numerieke waarden of tijds perioden). 

**Standaard kunt u slechts één niveau van facet navigatie hebben** 

Zoals genoteerd, is er geen rechtstreekse ondersteuning voor het nesten van facetten in een hiërarchie. Facet navigatie in azure Cognitive Search ondersteunt standaard slechts één filter niveau. Er zijn echter tijdelijke oplossingen. U kunt een hiërarchische facet structuur coderen in een `Collection(Edm.String)` met één ingangs punt per hiërarchie. Het implementeren van deze tijdelijke oplossing valt buiten het bereik van dit artikel. 

### <a name="querying-tips"></a>Query's uitvoeren op tips
**Velden valideren**

Als u de lijst met facetten dynamisch bouwt op basis van niet-vertrouwde gebruikers invoer, controleert u of de namen van de facet velden geldig zijn. U kunt ook de namen weglaten wanneer u Url's bouwt met behulp `Uri.EscapeDataString()` van in .net of op basis van het platform van uw keuze.

### <a name="filtering-tips"></a>Tips filteren
**De zoek precisie verg Roten met filters**

Filters gebruiken. Als u alleen zoek expressies vertrouwt, kan dit ertoe leiden dat een document wordt geretourneerd dat niet de exacte facet waarde in een van de velden heeft.

**Verbeter de zoek prestaties met filters**

Filters beperken de set met kandidaten documenten voor Zoek opdrachten en sluiten deze uit van de rang schikking. Als u een grote set documenten hebt, biedt het gebruik van een selectief facet inzoomen vaak betere prestaties.
  
**Alleen de facet velden filteren**

In facet inzoomen wilt u meestal alleen documenten met de facet waarde in een specifiek (beperkt) veld toevoegen, en niet overal in alle Doorzoek bare velden. Door een filter toe te voegen, wordt het doel veld versterkt door de service alleen te laten zoeken in het veld facet voor een overeenkomende waarde.

**Facet resultaten knippen met meer filters**

Facet resultaten zijn documenten gevonden in de zoek resultaten die overeenkomen met een facet term. In het volgende voor beeld, in Zoek resultaten voor *Cloud Computing*, hebben 254-items ook een *interne specificatie* als een inhouds type. Items zijn niet noodzakelijkerwijs wederzijds exclusief. Als een item voldoet aan de criteria van beide filters, wordt dit in elk filter geteld. Deze duplicatie kan worden gebruikt wanneer facetten op `Collection(Edm.String)` velden, die vaak worden toegepast voor het implementeren van document tagging.

```output
Search term: "cloud computing"
Content type
   Internal specification (254)
   Video (10)
```

In het algemeen is het raadzaam om meer filters toe te voegen, als u merkt dat facet resultaten te groot zijn. u kunt er ook voor kiezen om de Zoek opdrachten te beperken.

### <a name="tips-about-result-count"></a>Tips voor het aantal resultaten

**Het aantal items in de facet navigatie beperken**

Voor elk facet veld in de navigatie structuur geldt een standaard limiet van 10 waarden. Deze standaard instelling is zinvol voor navigatie structuren, omdat de waarden lijst een beheer bare grootte behoudt. U kunt de standaard instelling onderdrukken door een waarde toe te wijzen die moet worden geteld.

* `&facet=city,count:5` Hiermee geeft u op dat alleen de eerste vijf steden in de bovenste gerangschikte resultaten worden geretourneerd als een facet resultaat. Bekijk een voorbeeld query met de zoek term ' lucht haven ' en 32 overeenkomsten. Als met de query wordt opgegeven `&facet=city,count:5` , worden alleen de eerste vijf unieke steden met de meeste documenten in de zoek resultaten opgenomen in de facet resultaten.

Let op het onderscheid tussen facet resultaten en zoek resultaten. Zoek resultaten zijn alle documenten die overeenkomen met de query. Facet resultaten zijn de overeenkomsten voor elke facet waarde. In het voor beeld omvatten Zoek resultaten plaats namen die niet voor komen in de facet classificatie lijst (5 in ons voor beeld). Resultaten die worden gefilterd via facet navigatie, worden zichtbaar wanneer u facetten wist of andere facetten kiezen naast plaats. 

> [!NOTE]
> Bespreken `count` wanneer er meer dan een type verwarrend kan zijn. De volgende tabel bevat een korte samen vatting van hoe de term wordt gebruikt in azure Cognitive Search API, voorbeeld code en documentatie. 

* `@colorFacet.count`<br/>
  In presentatie code ziet u een aantal para meters in het facet, dat wordt gebruikt om het aantal facet resultaten weer te geven. In facet resultaten geeft het aantal documenten aan dat overeenkomt met de facet term of het bereik.
* `&facet=City,count:12`<br/>
  In een facet query kunt u Count instellen op een waarde.  De standaard waarde is 10, maar u kunt deze hoger of lager instellen. Instelling `count:12` haalt de Top 12 overeenkomsten op in facet resultaten per document telling.
* "`@odata.count`"<br/>
  In het antwoord van de query geeft deze waarde het aantal overeenkomende items in de zoek resultaten aan. Gemiddeld is het groter dan de som van alle facet resultaten gecombineerd, vanwege de aanwezigheid van items die overeenkomen met de zoek term, maar waarvoor geen facet waarde overeenkomt.

**Aantallen ophalen in facet resultaten**

Wanneer u een filter aan een facet query toevoegt, wilt u mogelijk de facet instructie behouden (bijvoorbeeld `facet=Rating&$filter=Rating ge 4` ). Technisch, facet = classificatie is niet nodig, maar blijft het aantal facet waarden voor beoordelingen 4 en hoger retour neren. Als u bijvoorbeeld op 4 klikt en de query een filter bevat dat groter is dan of gelijk is aan 4, worden de aantallen geretourneerd voor elke classificatie van 4 en hoger.  

**Zorg ervoor dat u nauw keurige facet aantallen krijgt**

Onder bepaalde omstandigheden is het mogelijk dat facet aantallen niet overeenkomen met de resultaten sets (Zie [facet navigatie in Azure Cognitive Search (micro soft Q&een vraag pagina)](/answers/topics/azure-cognitive-search.html)).

De facet aantallen kunnen onnauwkeurig zijn als gevolg van de sharding-architectuur. Elke zoek index heeft meerdere Shards en elke Shard rapporteert de bovenste N facetten per document telling, die vervolgens in één resultaat wordt gecombineerd. Als sommige Shards veel overeenkomende waarden hebben, terwijl anderen minder hebben, is het mogelijk dat sommige facet waarden ontbreken of worden ondergeteld in de resultaten.

Hoewel dit gedrag op elk gewenst moment kan worden gewijzigd, kunt u dit probleem omzeilen door het aantal: \<number> naar een groot aantal te deruimen om de volledige rapportage van elke Shard af te dwingen. Als de waarde van Count: groter is dan of gelijk is aan het aantal unieke waarden in het veld, worden nauw keurige resultaten gegarandeerd. Maar wanneer het aantal documenten hoog is, is er sprake van prestatie vermindering. Gebruik deze optie daarom verstandig.

### <a name="user-interface-tips"></a>Tips voor de gebruikers interface
**Labels voor elk veld in de facet navigatie toevoegen**

Labels worden meestal gedefinieerd in de HTML of het formulier ( `index.cshtml` in de voorbeeld toepassing). Er is geen API in azure Cognitive Search voor facet navigatie labels of andere meta gegevens.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filteren op basis van een bereik
Facet overschrijding van bereik waarden is een algemene vereiste voor het zoeken van toepassingen. Bereiken worden ondersteund voor numerieke gegevens en datum/tijd-waarden. Meer informatie over elke benadering vindt u in [documenten zoeken (Azure Cognitive Search-API)](/rest/api/searchservice/Search-Documents).

Azure Cognitive Search vereenvoudigt de bereik constructie door twee benaderingen te bieden voor het berekenen van een bereik. Voor beide benaderingen maakt Azure Cognitive Search de juiste bereiken op basis van de invoer die u hebt opgegeven. Als u bijvoorbeeld de bereik waarden 10 | 20 | 30 opgeeft, worden er automatisch bereiken van 0-10, 10-20, 20-30 gemaakt. Uw toepassing kan eventueel alle intervallen verwijderen die leeg zijn. 

**Benadering 1: de para meter interval gebruiken**  
Als u prijs facetten in $10 stappen wilt instellen, geeft u het volgende op: `&facet=price,interval:10`

**Benadering 2: een lijst met waarden gebruiken**  
Voor numerieke gegevens kunt u een lijst met waarden gebruiken.  Houd rekening met het facet bereik voor een `listPrice` veld dat als volgt wordt weer gegeven:

  ![Lijst met voorbeeld waarden](media/search-faceted-navigation/Facet-5-Prices.PNG "Lijst met voorbeeld waarden")

Als u een facet bereik wilt opgeven zoals in de vorige scherm afbeelding, gebruikt u een lijst met waarden:

> `facet=listPrice,values:10|25|100|500|1000|2500`

Elk bereik is gebouwd op basis van 0 als uitgangs punt, een waarde uit de lijst als een eind punt en wordt vervolgens bijgesneden van het vorige bereik om discrete intervallen te maken. Azure Cognitive Search doet dit als onderdeel van facet navigatie. U hoeft geen code te schrijven voor het structureren van elk interval.

### <a name="build-a-filter-for-a-range"></a>Een filter maken voor een bereik
Als u documenten wilt filteren op basis van een bereik dat u hebt geselecteerd, kunt u de `"ge"` `"lt"` Opera tors en filteren in een expressie met twee delen waarmee de eind punten van het bereik worden gedefinieerd. Als u bijvoorbeeld het bereik 10-25 voor een `listPrice` veld kiest, zou het filter zijn `$filter=listPrice ge 10 and listPrice lt 25` . In de voorbeeld code gebruikt de filter expressie **priceFrom** -en **priceTo** -para meters om de eind punten in te stellen. 

  ![Query voor een reeks waarden](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Query voor een reeks waarden")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filteren op basis van afstand
Het is gebruikelijk om filters te zien die u helpen bij het kiezen van een Store, restaurant of doel op basis van de nabijheid van uw huidige locatie. Hoewel dit type filter eruit kan zien als facet navigatie, is het een filter. Dit wordt hier vermeld voor degenen die specifiek op zoek zijn naar het implementatie advies voor het desbetreffende ontwerp probleem.

Er zijn twee georuimtelijke functies in azure Cognitive Search, **geo. Distance** en **geo. intersects**.

* De functie **geo. Distance** retourneert de afstand in kilo meters tussen twee punten. Een punt is een veld en een ander is een constante die wordt door gegeven als onderdeel van het filter. 
* De functie **geo. intersects** retourneert True als een bepaald punt zich in een bepaalde veelhoek bevindt. Het punt is een veld en de veelhoek wordt opgegeven als een constante lijst met coördinaten die worden door gegeven als onderdeel van het filter.

U kunt filter voorbeelden vinden in de [OData-expressie syntaxis (Azure Cognitive Search)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Probeer de demo
De demo van de Azure Cognitive Search Job Portal bevat de voor beelden waarnaar in dit artikel wordt verwezen.

-   Bekijk en test de werk demonstratie online in [Azure Cognitive Search Job Portal-demo](https://aka.ms/azjobsdemo).

-   Down load de code van de [Azure-samples opslag plaats op github](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Bekijk bij het werken met zoek resultaten de URL voor wijzigingen in de bouw van query's. Deze toepassing wordt uitgevoerd om facetten toe te voegen aan de URI wanneer u deze selecteert.

1. Als u de toewijzings functionaliteit van de demo-app wilt gebruiken, haalt u een Bing Kaarten-sleutel op uit het [Bing Maps dev Center](https://www.bingmapsportal.com/). Plak het over de bestaande sleutel op de `index.cshtml` pagina. De `BingApiKey` instelling in het `Web.config` bestand wordt niet gebruikt. 

2. Voer de toepassing uit. Neem de optionele rond leiding of sluit het dialoog venster.
   
3. Voer een zoek term in, zoals ' analist ', en klik op het zoek pictogram. De query wordt snel uitgevoerd.
   
   Er wordt ook een facet navigatie structuur geretourneerd met de zoek resultaten. In de zoek resultaten pagina bevat de facet navigatie structuur de aantallen voor elk facet resultaat. Er zijn geen facetten geselecteerd, dus alle overeenkomende resultaten worden geretourneerd.
   
   ![Zoek resultaten voordat u facetten selecteert](media/search-faceted-navigation/faceted-search-before-facets.png "Zoek resultaten voordat u facetten selecteert")

4. Klik op de titel, locatie of het minimale salaris van een onderneming. Facetten zijn null bij de eerste zoek opdracht, maar wanneer ze op waarden worden uitgevoerd, worden de zoek resultaten afgekapt van items die niet meer overeenkomen.
   
   ![Resultaten zoeken na het selecteren van facetten](media/search-faceted-navigation/faceted-search-after-facets.png "Resultaten zoeken na het selecteren van facetten")

5. Als u de facet query wilt wissen zodat u verschillende query gedrag kunt proberen, klikt u op de `[X]` na de geselecteerde facetten om de facetten te wissen.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Lees meer
Bekijk [Azure Cognitive Search diep gaande](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Bij 45:25 is er een demo over het implementeren van facetten.

Voor meer informatie over ontwerp principes voor facet navigatie raden wij de volgende koppelingen aan:

* [Ontwerp patronen: facet navigatie](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Front-end-overwegingen bij het implementeren van facet zoeken – deel 1](https://articles.uie.com/faceted_search2/)