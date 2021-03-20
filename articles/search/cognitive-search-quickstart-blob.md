---
title: Een vaardighedenset maken in de Azure-portal
titleSuffix: Azure Cognitive Search
description: In deze portal-quickstart leert u hoe u de wizard Gegevens importeren gebruikt om cognitieve vaardigheden toe te voegen aan een indexeringspijplijn in Azure Cognitive Search. Vaardigheden zijn onder andere OCR (optische tekenherkenning) en verwerking van natuurlijke taal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: d07b52d8abeab34d565ebde4bac58eec66780dce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98179262"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Quickstart: Een cognitieve vaardighedenset van Azure Cognitive Search maken in de Azure-portal

Een vaardighedenset is een AI-functie waarmee met behulp van Deep Learning-modellen informatie en structuur wordt geëxtraheerd uit grote, niet-gedifferentieerde tekst- en afbeeldingsbestanden. De inhoud kan vervolgens worden geïndexeerd en doorzocht in Azure Cognitive Search. 

In deze quickstart combineert u services en gegevens in de Azure-cloud om de vaardighedenset te maken. Zodra alles gereed is, voert u de wizard **Gegevens importeren** uit in de Azure-portal om alles samen op te halen. Het eindresultaat is een doorzoekbare index gevuld met gegevens die zijn gemaakt met AI-verwerking, en die u in de portal kunt doorzoeken ([Search Explorer](search-explorer.md)).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u beschikken over de volgende vereisten:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

+ Een Azure Cognitive Search-service. [Maak een service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken. 

+ Een Azure Storage-account met [Blob-opslag](../storage/blobs/index.yml).

> [!NOTE]
> Deze quickstart maakt ook gebruik van [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) voor de AI. Omdat de workload zo klein is, wordt de gratis verwerking (maximaal 20 transacties) van Cognitive Services achter de schermen gebruikt. Dit betekent dat u deze oefening kunt doen zonder dat u een nieuwe Cognitive Services-resource moet aanmaken.

## <a name="set-up-your-data"></a>Uw gegevens voorbereiden

Volg deze stappen om een blobcontainer in te stellen in Azure Storage om heterogene inhoudsbestanden op te slaan.

1. [Download de voorbeeldgegevens](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) die bestaan uit een kleine set van verschillende typen bestanden. Pak de bestanden uit.

1. [Een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal) of [een bestaand account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) zoeken. 

   + Kies dezelfde regio als Azure Cognitive Search om bandbreedtekosten te voorkomen. 

   + Kies het accounttype StorageV2 (algemeen gebruikVv2) als u de functie voor het kennisarchief later wilt uitproberen, in andere stapsgewijze instructies. Kies anders een willekeurig ander type.

1. Open de Blob-servicepagina's en maak een container. U kunt het standaardniveau voor openbare toegang gebruiken. 

1. Klik in de container op **Uploaden** om de voorbeeldbestanden te uploaden die u in de eerste stap hebt gedownload. Zoals u ziet, hebt u een breed scala aan inhoudstypen, waaronder afbeeldingen en toepassingsbestanden waarvoor zoeken in volledige tekst niet mogelijk is in de oorspronkelijke indeling.

   :::image type="content" source="media/cognitive-search-quickstart-blob/sample-data.png" alt-text="Bronbestanden in Azure-blobopslag" border="false":::

U kunt nu verdergaan met de wizard Gegevens importeren.

## <a name="run-the-import-data-wizard"></a>De wizard Gegevens importeren uitvoeren

1. Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com/).

1. [Ga naar uw zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) en klik op de overzichtspagina op **Gegevens importeren** in de opdrachtbalk om in vier stappen cognitieve verrijking in te stellen.

   :::image type="content" source="media/cognitive-search-quickstart-blob/import-data-cmd2.png" alt-text="Opdracht Gegevens importeren" border="false":::

### <a name="step-1---create-a-data-source"></a>Stap 1: een gegevensbron maken

1. Kies in **Verbinden met uw gegevens** de optie **Azure Blob-opslag**, en selecteer het account en de container die u hebt gemaakt. Geef een naam op voor de gegevensbron en gebruik standaardwaarden voor de rest. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/blob-datasource.png" alt-text="Azure-blobconfiguratie" border="false":::

    Ga door naar de volgende pagina.

### <a name="step-2---add-cognitive-skills"></a>Stap 2: cognitieve vaardigheden toevoegen

Configureer vervolgens AI-verrijking om OCR, afbeeldingsanalyse en verwerking van natuurlijke taal aan te roepen. 

1. Voor deze quickstart gebruiken we de **gratis** Cognitive Services-resource. De voorbeeldgegevens bestaan uit 14 bestanden. De gratis service-eenheid van 20 transacties op Cognitive Services is dus voldoende voor deze quickstart. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/cog-search-attach.png" alt-text="Cognitive Services koppelen: basisservice koppelen" border="false":::

1. Vouw **Verrijkingen toevoegen** uit en maak vier selecties. 

   Schakel OCR in om de vaardigheden voor afbeeldingsanalyse toe te voegen aan de wizardpagina.

   Stel granulariteit in op Pagina's om tekst op te splitsen in kleinere segmenten. Verschillende tekstvaardigheden zijn beperkt tot 5 kB invoer.

   Kies vaardigheden voor entiteitsherkenning (personen, organisaties en locaties) en afbeeldingsanalyse.

   :::image type="content" source="media/cognitive-search-quickstart-blob/skillset.png" alt-text="Cognitive Services koppelen: services voor vaardighedenset selecteren" border="false":::

   Ga door naar de volgende pagina.

### <a name="step-3---configure-the-index"></a>Stap 3: de index configureren

Een index bevat uw doorzoekbare inhoud, en met de wizard **Gegevens importeren** kan doorgaans het schema voor u worden gemaakt door een steekproef van de gegevensbron te nemen. In deze stap bekijkt u het gegenereerde schema en kunt u desgewenst instellingen wijzigen. Hieronder ziet u het standaardschema dat is gemaakt voor de demo-Blob-gegevensset.

De wizard biedt goede standaardinstellingen voor deze snelstart:  

+ Standaardvelden zijn gebaseerd op eigenschappen voor bestaande blobs plus nieuwe velden die een verrijkingsuitvoer bevatten (bijvoorbeeld `people`, `organizations`, `locations`). Gegevenstypen worden afgeleid van metagegevens en steekproeven van gegevens.

+ Standaarddocumentsleutel is *metadata_storage_path* (geselecteerd omdat het veld unieke waarden bevat).

+ **Ophaalbaar** en **Doorzoekbaar** zijn standaardkenmerken. **Doorzoekbaar** maakt zoeken in de volledige tekst van een veld mogelijk. **Ophaalbaar** betekent dat veldwaarden kunnen worden geretourneerd in resultaten. De wizard gaat ervan uit dat deze velden ophaalbaar en doorzoekbaar moeten zijn omdat u ze hebt gemaakt via een set vaardigheden.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields.png" alt-text="Indexvelden" border="false":::

Let op het doorgehaalde vinkje en het vraagteken in de kolom **Ophaalbaar** voor het veld `content`. Voor blob-documenten met veel tekst bevat het veld `content` het grootste deel van het bestand, mogelijk wel duizenden regels. Een veld zoals dit is onpraktisch in zoekresultaten en u moet dit veld uitsluiten voor deze demo. 

Als u de inhoud van een bestand wilt doorgeven aan clientcode, moet **Ophaalbaar** geselecteerd blijven. Anders kunt u dit kenmerk uitschakelen voor `content` als de uitgepakte elementen (zoals `people`, `organizations`, `locations`, enzovoort) voldoende zijn.

Als u een veld markeert als **Ophaalbaar**, betekent niet dat het veld aanwezig *moet* zijn in de lijst met zoekresultaten. U kunt de samenstelling van zoekresultaten nauwkeurig beheren met behulp van de queryparameter **$select** door op te geven welke velden u wilt opnemen. Voor velden met veel tekst zoals `content`, is de parameter **$select** de oplossing voor het aanbieden van beheersbare zoekresultaten aan de gebruikers van uw toepassing, terwijl clientcode via het kenmerk **Ophaalbaar** toegang heeft tot alle gegevens die nodig zijn.
  
Ga door naar de volgende pagina.

### <a name="step-4---configure-the-indexer"></a>Stap 4: de indexeerfunctie configureren

De indexeerfunctie is een belangrijke resource die het indexeerproces aandrijft. Hiermee specificeert u de naam van de gegevensbron, een doelindex en de uitvoerfrequentie. Met de wizard **Gegevens importeren** worden verschillende objecten gemaakt, en één hiervan is altijd een indexeerfunctie die u herhaaldelijk kunt uitvoeren.

1. Op de pagina **Indexeerfunctie** kunt u de standaardnaam accepteren en op de schemaoptie **Eén keer** klikken om de indexeerfunctie direct uit te voeren. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-def.png" alt-text="Definitie van de indexeerfunctie" border="false":::

1. Klik op **Verzenden** om de indexeerfunctie te maken en tegelijkertijd uit te voeren.

## <a name="monitor-status"></a>Status controleren

Indexering van de cognitieve vaardigheden vergt meer tijd dan gebruikelijke indexering op basis van tekst met name OCR en afbeeldingsanalyse. Als u de voortgang wilt bewaken, gaat u naar de overzichtspagina en klikt u op **Indexeerfuncties** in het midden van de pagina.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-notification.png" alt-text="Azure Cognitive Search-melding" border="false":::

Waarschuwingen krijgen normaal gesproken het brede scala aan inhoudstypen. Sommige inhoudstypen zijn niet geldig voor bepaalde vaardigheden en bij lagere lagen zijn [indexeringslimieten](search-limits-quotas-capacity.md#indexer-limits) gebruikelijk. Meldingen over afkappingen van 32.000 tekens zijn bijvoorbeeld een indexeringslimiet in de Gratis laag. Als u deze demo hebt uitgevoerd in een hogere laag, verdwijnen veel waarschuwingen over afkappingen.

Als u waarschuwingen of fouten wilt controleren, klikt u op Waarschuwingsstatus in de lijst Indexeerfuncties om de pagina Uitvoeringsgeschiedenis te openen.

Klik op deze pagina nogmaals op de Waarschuwingsstatus om een lijst met waarschuwingen te zien, vergelijkbaar met de lijst hieronder. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-warnings.png" alt-text="Lijst met indexeerfunctiewaarschuwingen" border="false":::

Details worden weergegeven wanneer u op een specifieke statusregel klikt. Deze waarschuwing geeft aan dat het samenvoegen is gestopt na het bereiken van een maximumdrempel (deze specifieke PDF is groot).

  :::image type="content" source="media/cognitive-search-quickstart-blob/warning-detail.png" alt-text="Waarschuwingsdetails" border="false":::

## <a name="query-in-search-explorer"></a>Query uitvoeren in Search Explorer

Nadat een index is gemaakt, kunt u query's uitvoeren om resultaten te retourneren. Gebruik **Search Explorer** in de portal voor deze taak. 

1. Klik op de dashboardpagina van de zoekservice op **Search Explorer** in de opdrachtbalk.

1. Selecteer bovenaan **Index wijzigen** om de index die u hebt gemaakt te selecteren.

1. Voer een zoekopdracht in om een query op de index toe te passen, zoals `search=Microsoft&$select=people,organizations,locations,imageTags`.

Resultaten worden geretourneerd als JSON, wat uitgebreid en moeilijk te lezen kan zijn, met name in grote documenten die afkomstig zijn van Azure-blobs. Enkele tips voor zoeken in dit hulpprogramma zijn onder andere de volgende technieken:

+ Voeg `$select` toe om op te geven welke velden moeten worden meegenomen in de resultaten. 
+ Gebruik CTRL-F om in de JSON te zoeken naar specifieke eigenschappen of termen.

Queryreeksen zijn hoofdlettergevoelig, dus als u het bericht 'onbekend veld' ontvangt, controleert u **Velden** of **Indexdefinitie (JSON)** om naam en aanvraag te controleren. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer.png" alt-text="Voorbeeld Search Explorer" border="false":::

## <a name="takeaways"></a>Opgedane kennis

U hebt nu uw eerste vaardighedenset gemaakt en belangrijke concepten geleerd die nuttig zijn om een prototype te maken van een verrijkte zoekoplossing met behulp van uw eigen gegevens.

Een van de belangrijke concepten die we wilden overbrengen, is de afhankelijkheid van Azure-gegevensbronnen. Een vaardighedenset is gebonden aan een indexeerfunctie, en indexeerfuncties zijn Azure- en bronspecifiek. Hoewel deze snelstart Azure Blob-opslag gebruikt, zijn andere Azure-gegevensbronnen ook mogelijk. Zie [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md) voor meer informatie. 

Een ander belangrijk concept is dat vaardigheden werken via inhoudstypen, en wanneer ze met heterogene inhoud werken, wordt sommige invoer overgeslagen. Bovendien kunnen grote bestanden of velden de indexeerfunctielimieten van uw servicelaag overschrijden. Het is normaal dat er waarschuwingen worden weergegeven wanneer deze gebeurtenissen optreden. 

Uitvoer wordt naar een zoekindex geleid, en er is een toewijzing tussen naam/waarde-paren die zijn gemaakt tijdens het indexeren, en afzonderlijke velden in uw index. Intern stelt de portal [aantekeningen](cognitive-search-concept-annotations-syntax.md) in en definieert een [set vaardigheden](cognitive-search-defining-skillset.md), waarmee de volgorde van bewerkingen en de algemene stroom wordt bepaald. Deze stappen zijn verborgen in de portal, maar wanneer u begint met het schrijven van code worden deze concepten belangrijk.

Ten slotte hebt u geleerd dat inhoud kan worden gecontroleerd door de index te doorzoeken. Azure Cognitive Search biedt eigenlijk een doorzoekbare index, waarin u query's kunt uitvoeren met behulp van de [eenvoudige](/rest/api/searchservice/simple-query-syntax-in-azure-search) of [volledig uitgebreide querysyntaxis](/rest/api/searchservice/lucene-query-syntax-in-azure-search). Een index met verrijkte velden werkt zoals elke andere index. Als u standaardanalyse of [aangepaste analyse](search-analyzers.md), [scoreprofielen](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synoniemen](search-synonyms.md), [meervoudige filters](search-filters-facets.md), geografisch zoeken of een andere functie van Azure Cognitive Search wilt opnemen, kunt u dat gewoon doen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

U kunt vaardighedensets maken met behulp van de portal, .NET SDK of REST API. Probeer REST API met behulp van Postman en meer voorbeeldgegevens om uw kennis te verfijnen.

> [!div class="nextstepaction"]
> [Zelfstudie: Tekst en structuur extraheren uit JSON-blobs met behulp van REST API's ](cognitive-search-tutorial-blob.md)

> [!Tip]
> Als u deze oefening wilt herhalen of een ander AI-verrijkingsscenario wilt uitproberen, verwijdert u de indexeerfunctie in de portal. Door de indexeerfunctie te verwijderen komt de teller van het aantal gratis dagelijkse transacties weer op nul te staan voor Cognitive Services-verwerking.