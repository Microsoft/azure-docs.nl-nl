---
title: Een zoekindex maken in de Azure-portal
titleSuffix: Azure Cognitive Search
description: In deze snelstartgids voor de Azure-portal gebruikt u de wizard Gegevens importeren om uw eerste zoekindex in Azure Cognitive Search te maken, te laden en op te vragen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/12/2020
ms.openlocfilehash: 1e9d63c88cf0cd6f65db99b2bc878797770d53cd
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368627"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Quickstart: Een Azure Cognitive Search-index maken in de Azure-portal

Maak uw eerste index met behulp van de wizard **Gegevens importeren** en een ingebouwde voorbeeldgegevensbron met fictieve hotelgegevens. De wizard begeleidt u bij het maken van een zoekindex (hotels-sample-index) zodat u binnen enkele minuten interessante query's kunt schrijven. 

Hoewel u de opties niet gebruikt in deze quickstart, bevat de wizard een pagina voor AI-verrijking. Hiermee kunt u tekst en structuur extraheren uit afbeeldingsbestanden en ongestructureerde tekst. Voor vergelijkbare stapsgewijze instructies voor AI-verrijking, raadpleegt u [Quickstart: Een cognitieve-vaardighedenset maken](cognitive-search-quickstart-blob.md).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

+ Een Azure Cognitive Search-service. [Maak een service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken. 

### <a name="check-for-space"></a>Controleren of er voldoende ruimte is

Veel klanten beginnen met de gratis service. Deze versie is beperkt tot drie indexen drie gegevensbronnen en drie indexeerfuncties. Zorg ervoor dat er voldoende ruimte is voor extra items voordat u begint. In deze zelfstudie wordt van elk object één exemplaar gemaakt.

Op secties in het servicedashboard wordt weergegeven hoeveel indexen, indexeerfuncties en gegevensbronnen u al hebt. 

:::image type="content" source="media/search-get-started-portal/tiles-indexers-datasources.png" alt-text="Lijsten met indexen, indexeerfuncties en gegevensbronnen":::

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Een index maken en gegevens laden

Zoekopdrachten worden herhaald over een [*index*](search-what-is-an-index.md) die doorzoekbare gegevens, metagegevens en aanvullende constructies bevat, die worden gebruikt om bepaald zoekgedrag te optimaliseren.

Voor deze zelfstudie gebruiken we een ingebouwde gegevensset die met behulp van een [*indexeerfunctie*](search-indexer-overview.md) kan worden verkend via de [**wizard** Gegevens importeren](search-import-data-portal.md). Een indexeerfunctie is een bronspecifieke verkenner die metagegevens en inhoud uit ondersteunde Azure-gegevensbronnen kan lezen. Normaal gesproken worden indexeerfuncties via een programma gebruikt, maar in de portal zijn ze toegankelijk via de wizard **Gegevens importeren**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Stap 1: de wizard Gegevens importeren starten en een gegevensbron maken

1. Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com/).

1. [Vind uw zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) en klik op de overzichtspagina op **Gegevens importeren** in de opdrachtbalk om een zoekindex te maken en te vullen.

   :::image type="content" source="media/search-get-started-portal/import-data-cmd.png" alt-text="Opdracht Gegevens importeren":::

1. Klik in de wizard op **Verbinding maken met uw gegevens** > **Voorbeelden** > **hotels-sample**. Deze gegevensbron is ingebouwd. Als u uw eigen gegevensbron zou maken, zou u een naam, het type en de verbindingsgegevens moeten opgeven. Zodra de gegevensbron is gemaakt, wordt deze een bestaande gegevensbron genoemd die opnieuw kan worden gebruikt voor andere bewerkingen.

   :::image type="content" source="media/search-get-started-portal/import-datasource-sample.png" alt-text="Voorbeeldgegevensset selecteren":::

1. Ga door naar de volgende pagina.

### <a name="step-2---skip-the-enrich-content-page"></a>Stap 2: De pagina ‘Inhoud verrijken’ overslaan

De wizard ondersteunt het maken van een [pijplijn voor AI-verrijking](cognitive-search-concept-intro.md) voor het opnemen van de AI-algoritmen van Cognitive Services bij het indexeren. 

Deze stap slaan we nu even over en we gaan naar **Doelindex aanpassen**.

   :::image type="content" source="media/search-get-started-portal/skip-cog-skill-step.png" alt-text="Stap voor cognitieve vaardigheden overslaan":::

> [!TIP]
> U kunt een voorbeeld van een AI-indexering doorlopen in een [Snelstartgids](cognitive-search-quickstart-blob.md) of [zelfstudie](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Stap 3: index configureren

Het maken van een index is meestal een op code gebaseerde oefening die is voltooid voordat gegevens worden geladen. Maar zoals deze zelfstudie laat zien, kan de wizard een basisindex genereren voor elke gegevensbron die kan worden verkend. Een index vereist minimaal een naam en een verzameling velden. Eén van de velden moet zijn gemarkeerd als de documentsleutel die elk document op unieke wijze identificeert. Bovendien kunt u taalanalyses of suggesters opgeven als u wilt dat automatisch aanvullen of het voorstellen van query's wordt gebruikt.

Velden bevatten gegevenstypen en kenmerken. De selectievakjes bovenaan zijn *indexkenmerken* die bepalen hoe het veld wordt gebruikt.

* **Ophalen mogelijk** betekent dat dit veld wordt weergegeven in de lijst met zoekresultaten. U kunt afzonderlijke velden markeren als ontoegankelijk voor zoekresultaten door dit selectievakje uit te schakelen, bijvoorbeeld voor velden die alleen in filterexpressies worden gebruikt.
* **Sleutel** is de unieke id van het document. Het is altijd een tekenreeks en deze is vereist.
* Met de kenmerken **Filterbaar**, **Sorteerbaar** en **Geschikt voor facetten** bepaalt u of velden in een filter, een sorteervolgorde of een facetnavigatiestructuur worden gebruikt.
* **Doorzoekbaar** betekent dat een veld is opgenomen in een zoekopdracht in volledige tekst. Tekenreeksen zijn doorzoekbaar. Numerieke velden en Booleaanse waarden zijn vaak gemarkeerd als niet doorzoekbaar.

Opslagvereisten verschillen niet als gevolg van uw selectie. Als u bijvoorbeeld het kenmerk **Ophalen mogelijk** instelt voor meerdere velden, nemen de opslagvereisten niet toe.

Standaard wordt met de wizard de gegevensbron gescand op unieke id's als basis voor het sleutelveld. *Tekenreeksen* hebben de kenmerken **Ophaalbaar** en **Doorzoekbaar**. *Gehele getallen* hebben de kenmerken **Ophaalbaar**, **Filterbaar**, **Sorteerbaar** en **Geschikt voor facetten**.

1. Accepteer de standaardwaarden.

   Als u de wizard opnieuw uitvoert met behulp van een bestaande gegevensbron met hotels, kan de index niet worden geconfigureerd met standaardkenmerken. Voor toekomstige importbewerkingen moet u de kenmerken handmatig selecteren. 

   :::image type="content" source="media/search-get-started-portal/hotelsindex.png" alt-text="Gegenereerde hotelindex":::

2. Ga door naar de volgende pagina.


### <a name="step-4---configure-indexer"></a>Stap 4: indexeerfunctie configureren

Klik in de wizard **Gegevens importeren** op **Indexeerfunctie** > **Naam** en typ een naam voor de indexeerfunctie.

Dit object definieert een uitvoerbaar proces. U kunt een terugkerend schema instellen, maar in dit geval gebruikt u de standaardoptie om de indexeerfunctie één keer meteen uit te voeren.

Klik op **Verzenden** om de indexeerfunctie te maken en tegelijkertijd uit te voeren.

  :::image type="content" source="media/search-get-started-portal/hotels-indexer.png" alt-text="indexeerfunctie voor hotels":::

## <a name="monitor-progress"></a>Voortgang controleren

De wizard leidt u naar de lijst met indexeerfuncties waar u de voortgang kunt controleren. Voor zelfnavigatie gaat u naar de overzichtspagina en klikt u op **Indexeerfuncties**.

Het kan een paar minuten duren voordat in de portal de pagina is bijgewerkt, maar u zou de zojuist gemaakte indexeerfunctie in de lijst moeten zien met de status die aangeeft dat deze wordt uitgevoerd of is geslaagd en met het aantal geïndexeerde documenten.

   :::image type="content" source="media/search-get-started-portal/indexers-inprogress.png" alt-text="Voortgangsbericht voor de indexeerfunctie":::

## <a name="view-the-index"></a>De index bekijken

De belangrijkste servicepagina bevat koppelingen naar de resources die in uw Azure Cognitive Search-service zijn gemaakt.  Als u de index wilt bekijken die u zojuist hebt gemaakt, klikt u op **Indexen** in de lijst met koppelingen. 

Wacht tot de portalpagina is vernieuwd. Na enkele minuten ziet u de index met het aantal documenten en de opslaggrootte.

   :::image type="content" source="media/search-get-started-portal/indexes-list.png" alt-text="Lijst met indexen op het servicedashboard":::

In deze lijst kunt u op de index *hotels-sample* klikken die u zojuist hebt gemaakt, het indexschema bekijken. en eventueel nieuwe velden toevoegen. 

Op het tabblad **Velden** wordt het indexschema getoond. Scroll naar de onderkant van de lijst om een nieuw veld in te voeren. In de meeste gevallen kunt u geen bestaande velden wijzigen. Bestaande velden hebben een fysieke weergave in Azure Cognitive Search en kunnen daarom niet worden gewijzigd, zelfs niet in code. Als u een bestaand veld fundamenteel wilt wijzigen, maakt u een nieuwe index en verwijdert u het oorspronkelijke veld.

   :::image type="content" source="media/search-get-started-portal/sample-index-def.png" alt-text="voorbeeld van indexdefinitie":::

Andere constructies, zoals scoreprofielen en CORS-opties, kunnen op elk gewenst moment worden toegevoegd.

Neem even de tijd voor het bekijken van de indexdefinitieopties om duidelijk te begrijpen wat u wel en niet kunt bewerken tijdens indexontwerp. Uitgegrijsde opties geven aan dat een waarde niet kan worden gewijzigd of verwijderd. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Query die Search Explorer gebruikt

U zou nu een zoekindex moeten hebben die klaar is om query’s uit te voeren met de ingebouwde querypagina [**Search Explorer**](search-explorer.md). Deze pagina biedt een zoekvak waarmee u willekeurige queryreeksen kunt testen.

**Search Explorer** kan alleen omgaan met [REST API-aanvragen](/rest/api/searchservice/search-documents), maar er wordt syntaxis geaccepteerd voor [eenvoudige querysyntaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) en [volledige Lucene-queryparser](/rest/api/searchservice/lucene-query-syntax-in-azure-search), plus alle zoekparameters die beschikbaar zijn in bewerkingen voor [Search-document-REST API](/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> De volgende stappen worden na 6 min 8 sec gedemonstreerd in de [Azure Cognitive Search-overzichtsvideo](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klik op **Search explorer** in de opdrachtbalk.

   :::image type="content" source="media/search-get-started-portal/search-explorer-cmd.png" alt-text="Opdracht Search Explorer":::

2. Kies in de vervolgkeuzelijst **Index** de *voorbeeldindex met hotels*. Klik op de vervolgkeuzelijst voor de **API-versie** om de REST-API's te zien die beschikbaar zijn. Gebruik voor onderstaande query's de algemeen beschikbare versie (2020-06-30).

   :::image type="content" source="media/search-get-started-portal/search-explorer-changeindex.png" alt-text="Index- en API-opdrachten":::

3. Plak in de zoekbalk de onderstaande querytekenreeksen en klik op **Zoeken**.

   :::image type="content" source="media/search-get-started-portal/search-explorer-query-string-example.png" alt-text="Querytekenreeks en knop Zoeken":::

## <a name="example-queries"></a>Voorbeelden van query's

U kunt termen en zinnen invoeren, vergelijkbaar met wat u in een zoekopdracht in Bing of Google doet, of u kunt volledig opgegeven query-expressies invoeren. Resultaten worden geretourneerd als uitgebreide JSON-documenten.

### <a name="simple-query-with-top-n-results"></a>Eenvoudige query met de bovenste N resultaten

#### <a name="example-string-query-searchspa"></a>Voorbeeld (tekenreeks): `search=spa`

* De parameter **search** wordt gebruikt om een zoekopdracht voor zoeken in volledige tekst in te voeren waarmee, in dit geval, hotelgegevens worden geretourneerd die *spa* bevatten in elk doorzoekbaar veld van het document.

* Met **Search Explorer** worden resultaten geretourneerd in JSON. Deze indeling is uitgebreid en moeilijk te lezen als documenten een compacte structuur hebben. Dit is opzettelijk; dat het hele document zichtbaar is, is belangrijk voor ontwikkeldoeleinden, vooral tijdens het testen. Voor een betere gebruikerservaring moet u code schrijven waarmee [zoekresultaten](search-pagination-page-layout.md) belangrijke elementen weergeven.

* Documenten bestaan uit alle velden die zijn gemarkeerd als ‘Ophalen mogelijk’ in de index. Als u in de portal indexkenmerken wilt weergeven, gaat u naar de lijst **Indexen** en klikt u op *hotels-sample*.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Voorbeeld (geparameteriseerde query): `search=spa&$count=true&$top=10`

* Het symbool **&** wordt gebruikt om zoekparameters toe te voegen. Deze kunnen in willekeurige volgorde worden opgegeven.

* De parameter **$count=true** retourneert het totale aantal geretourneerde documenten. Deze waarde verschijnt bovenaan de zoekresultaten. U kunt filterquery's controleren door de wijzigingen te controleren die door **$count=true** worden gerapporteerd. Lage aantallen geven aan dat uw filter werkt.

* De **$top=10** retourneert de 10 hoogst scorende documenten in dit totaal. Standaard retourneert Azure Cognitive Search de 50 beste resultaten. U kunt dit aantal vergroten of verkleinen via **$top**.

### <a name="filter-the-query"></a><a name="filter-query"></a>De query filteren

Filters zijn opgenomen in de zoekopdrachten wanneer u de parameter **$filter** toevoegt. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Voorbeeld (gefilterd): `search=beach&$filter=Rating gt 4`

* De parameter **$filter** retourneert resultaten die voldoen aan de criteria die u hebt opgegeven. In dit geval: beoordelingen die hoger zijn dan 4.

* Filtersyntaxis is een OData-constructie. Zie [OData-syntaxis filteren](/rest/api/searchservice/odata-expression-syntax-for-azure-search) voor meer informatie.

### <a name="facet-the-query"></a><a name="facet-query"></a>De query facetteren

Facetfilters zijn opgenomen in zoekopdrachten. U kunt de facetparameter gebruiken om een samengevoegd aantal documenten te retourneren die overeenkomen met een door u opgegeven facetwaarde.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Voorbeeld (gefacetteerd met bereikvermindering): `search=*&facet=Category&$top=2`

* **search=** _ is een lege zoekopdracht. Met een lege zoekopdracht wordt naar alles gezocht. Eén reden om een lege query in te dienen, is om de hele set documenten te filteren of als facet te gebruiken. U wilt bijvoorbeeld een facetnavigatiestructuur die bestaat uit alle hotels in de index.
_ **facet** retourneert een navigatiestructuur die u kunt doorgeven aan een UI-besturingselement. Deze retourneert categorieën en een aantal. In dit geval zijn categorieën gebaseerd op een veld dat gemakshalve *categorie* wordt genoemd. Er is geen aggregatie in Azure Cognitive Search, maar u kunt een geschatte aggregatie bepalen via `facet`, dat het aantal documenten in elke categorie retourneert.

* **$top=2** retourneert twee documenten, om te illustreren dat u `top` kunt gebruiken om het aantal resultaten te verlagen of te verhogen.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Voorbeeld (gefacetteerd op numerieke waarden): `search=spa&facet=Rating`

* Deze query gebruikt het facet beoordelingen in een tekstuele zoekopdracht naar *spa*. De term *Beoordeling* kan worden opgegeven als een facet, omdat het veld in de index is gemarkeerd als ophaalbaar, filterbaar en bruikbaar als facet. En de waarden die het bevat (numeriek, 1 tot en met 5), zijn geschikt voor het categoriseren van vermeldingen in groepen.

* Alleen filterbare velden kunnen als facet worden gebruikt. Alleen ophaalbare velden kunnen in de resultaten worden geretourneerd.

* Het veld *Beoordeling* is een drijvendekommagetal met dubbele precisie en de groepering vindt plaats op basis van de nauwkeurige waarde. Zie [How to implement faceted navigation in Azure Cognitive Search](./search-faceted-navigation.md#filter-based-on-a-range) (Gefaceteerde navigatie implementeren in Azure Cognitive Search) voor meer informatie over het groeperen op basis van interval (zoals ‘beoordelingen met 3 sterren’, ‘beoordelingen met 4 sterren’, enz.).


### <a name="highlight-search-results"></a><a name="highlight-query"></a> Zoekresultaten markeren

Markeren betekent het toevoegen van opmaak aan tekst die overeenkomt met het trefwoord, overeenkomsten die in een bepaald veld zijn aangetroffen. Als de zoekterm verborgen is in een beschrijving, kunt u de treffers markeren om deze makkelijker te vinden.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Voorbeeld (gemarkeerd): `search=beach&highlight=Description`

* In dit voorbeeld is het opgemaakte woord *strand* gemakkelijker te zien in het omschrijvingsveld.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Voorbeeld (taalkundige analyse): `search=beaches&highlight=Description`

* De functie voor zoeken in volledige tekst herkent basisvariaties in Word-formulieren. In dit geval bevatten de zoekresultaten gemarkeerde tekst voor ‘strand’ voor hotels waarbij dat woord zich in hun doorzoekbare velden bevindt, in reactie op een zoekopdracht met het trefwoord ‘stranden’. De resultaten kunnen verschillende vormen van hetzelfde woord bevatten vanwege taalkundige analyse. 

* Azure Cognitive Search ondersteunt 56 analyzers van Lucene en Microsoft.Azure Search ondersteunt 56 analyzers van Lucene en Microsoft. Standaard wordt Lucene Analyzer gebruikt voor Azure Cognitive Search.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Fuzzy zoekopdrachten uitproberen

Voor verkeerd gespelde zoektermen, zoals *seatle* voor ‘Seattle’, worden bij standaardzoekopdrachten geen overeenkomsten geretourneerd. Het volgende voorbeeld retourneert geen resultaten.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Voorbeeld (verkeerd gespelde term, niet verwerkt): `search=seatle`

U kunt fuzzy zoekopdrachten gebruiken om spelfouten te omzeilen. Fuzzy zoekopdrachten worden ingeschakeld wanneer u de volledige Lucene-querysyntaxis gebruikt, wat gebeurt wanneer u **queryType=full** voor de query instelt en de **~** aan de zoekreeks toevoegt.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Voorbeeld (verkeerd gespelde term, verwerkt): `search=seatle~&queryType=full`

Dit voorbeeld retourneert nu documenten die overeenkomsten met ‘Seattle’ bevatten.

Als **queryType** niet is opgegeven, wordt standaard de eenvoudige queryparser gebruikt. De eenvoudige queryparser is sneller, maar als u gebruik wilt maken van fuzzy zoeken, reguliere expressies, zoeken op nabijheid of andere geavanceerde typen query's, dan hebt u de volledige syntaxis nodig.

Fuzzy zoekopdrachten en zoekopdrachten met jokertekens hebben implicaties voor zoekresultaten. Taalkundige analyse wordt niet uitgevoerd op deze queryindelingen. Bekijk in [Hoe zoeken in volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) de sectie over uitzonderingen op lexicale analyse voordat u fuzzy zoekopdrachten en zoekopdrachten met jokertekens gebruikt.

Zie [Lucene-querysyntaxis in Azure Cognitive Search](/rest/api/searchservice/lucene-query-syntax-in-azure-search) voor meer informatie over queryscenario's op basis van de volledige queryparser.

### <a name="try-geospatial-search"></a><a name="geo-search"></a> Georuimtelijk zoeken uitproberen

Georuimtelijk zoeken wordt ondersteund door het [gegevenstype edm.GeographyPoint](/rest/api/searchservice/supported-data-types) voor een veld met coördinaten. Geosearch is een type filter dat wordt opgegeven bij [Filter OData-syntaxis](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Voorbeeld (filters voor geografische coördinaten): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Met dit voorbeeld worden alle resultaten voor positionele gegevens gefilterd die minder dan 5 kilometer zijn verwijderd van een opgegeven punt (opgegeven als coördinaten voor lengte- en breedtegraad). Door **$count** toe te voegen, kunt u zien hoeveel resultaten er worden geretourneerd als u de afstand of de coördinaten wijzigt.

Georuimtelijk zoeken is handig als uw zoektoepassing een functie 'in mijn buurt zoeken' heeft of gebruikmaakt van kaartnavigatie. Dit is echter niet een zoekopdracht in volledige tekst. Als uw gebruikers op naam naar steden of landen/regio’s willen zoeken, voegt u, naast coördinaten, ook velden met namen van steden of landen/regio’s toe.

## <a name="takeaways"></a>Opgedane kennis

Deze zelfstudie biedt een snelle introductie tot het gebruik van Azure Cognitive Search via Azure Portal.

U hebt geleerd hoe u een zoekindex maakt met de wizard **Gegevens importeren**. U hebt geleerd over [indexeerfuncties](search-indexer-overview.md), en over de basiswerkstroom voor indexontwerp, waaronder [ondersteunde wijzigingen in een gepubliceerde index](/rest/api/searchservice/update-index).

Met behulp van de **Search Explorer** in de Azure Portal hebt u wat querysyntaxis geleerd via praktijkvoorbeelden die belangrijke mogelijkheden hebben gedemonstreerd, zoals filters, resultaatmarkering, fuzzy zoekopdrachten en op geografische locaties zoeken.

U hebt ook geleerd hoe u in de portal kunt zoeken naar indexen, indexeerfuncties en gegevensbronnen. Met elke nieuwe gegevensbron in de toekomst kunt u de portal gebruiken om met een minimale inspanning snel de definities of de veldverzamelingen ervan te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Een portalwizard gebruiken om een kant-en-klare web-app te genereren die in een browser wordt uitgevoerd. U kunt deze wizard uitproberen op de kleine index die u zojuist hebt gemaakt, of een van de ingebouwde sets met voorbeeldgegevens gebruiken voor een uitgebreidere zoekervaring.

> [!div class="nextstepaction"]
> [Een demo-app maken in de portal](search-create-app-portal.md)