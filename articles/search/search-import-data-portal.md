---
title: Gegevens importeren in een zoek index met behulp van Azure Portal
titleSuffix: Azure Cognitive Search
description: Meer informatie over het gebruik van de wizard gegevens importeren in de Azure Portal om Azure-gegevens te verkennen vanuit Cosmos DB, Blob Storage, Table Storage, SQL Database, SQL Managed instance en SQL Server op Azure Vm's.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cff009d5d1e187e8d0330fadca530b57b3e3d21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935208"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>De wizard gegevens importeren voor Azure Cognitive Search

De Azure Portal bevat een wizard **gegevens importeren** op het Azure Cognitive Search dash board voor het maken van prototypen en het laden van een index. In dit artikel worden de voor delen en beperkingen van het gebruik van de wizard, invoer en uitvoer en bepaalde gebruiks gegevens besproken. Zie de [Cognitive Search een Azure-index maken met behulp van de Azure Portal](search-get-started-portal.md) Quick start voor meer informatie over het stapsgewijs door lopen van de wizard met behulp van ingebouwde voorbeeld gegevens.

De bewerkingen die deze wizard uitvoert, zijn onder andere:

1: Maak verbinding met een ondersteunde Azure-gegevens bron.

2: Maak een index schema dat is afgeleid van de voorbeeld bron gegevens.

3: Voeg ook AI-verrijkingen toe om inhoud en structuur te extra heren of te genereren.

4: Voer de wizard uit om objecten te maken, gegevens te importeren, een planning en andere configuratie opties in te stellen.

De wizard voert een aantal objecten uit die zijn opgeslagen in uw zoek service, die u kunt gebruiken via een programma of in andere hulpprogram ma's.

## <a name="advantages-and-limitations"></a>Voor delen en beperkingen

Voordat u code schrijft, kunt u de wizard gebruiken voor het prototypen en testen van concepten. De wizard maakt verbinding met externe gegevens bronnen, voor beelden van de gegevens voor het maken van een initiële index en importeert vervolgens de gegevens als JSON-documenten in een index op Azure Cognitive Search. 

Steek proeven zijn het proces waarmee een index schema wordt afgeleid en er zijn enkele beperkingen. Wanneer de gegevens bron is gemaakt, kiest de wizard een voor beeld van documenten om te bepalen welke kolommen deel uitmaken van de gegevens bron. Niet alle bestanden worden gelezen, omdat dit mogelijk uren kan duren voor zeer grote gegevens bronnen. Op basis van een selectie van documenten, meta gegevens van de bron, zoals veld naam of type, wordt gebruikt voor het maken van een verzameling velden in een index schema. Afhankelijk van de complexiteit van de bron gegevens moet u het initiële schema mogelijk bewerken voor nauw keurigheid of het uitbreiden voor volledigheid. U kunt uw wijzigingen inline aanbrengen op de pagina met de index definitie.

Over het algemeen zijn de voor delen van het gebruik van de wizard duidelijk: als aan de vereisten wordt voldaan, kunt u binnen enkele minuten een query maken die kan worden geïndexeerd. Enkele van de complexiteit van het indexeren, zoals het opgeven van gegevens als JSON-documenten, worden verwerkt door de wizard.

Bekende beperkingen zijn als volgt:

+ De wizard biedt geen ondersteuning voor iteratie of hergebruik. Elke pass through-wizard maakt een nieuwe index, vaardig heden en Indexeer functie. Alleen gegevens bronnen kunnen worden bewaard en opnieuw worden gebruikt in de wizard. Als u andere objecten wilt bewerken of verfijnen, moet u de REST-Api's of .NET SDK gebruiken om de structuren op te halen en te wijzigen.

+ De bron inhoud moet zich in een ondersteunde Azure-gegevens bron bevinden.

+ Steek proeven zijn meer dan een subset van bron gegevens. Voor grote gegevens bronnen is het mogelijk dat de wizard velden mist. Het kan nodig zijn om het schema uit te breiden of de uitgestelde gegevens typen te corrigeren als er onvoldoende steek proeven zijn.

+ AI-verrijking, zoals weer gegeven in de portal, is beperkt tot een aantal ingebouwde vaardig heden. 

+ Een [kennis archief](knowledge-store-concept-intro.md)dat door de wizard kan worden gemaakt, is beperkt tot een aantal standaard projecties. Als u verrijkte documenten wilt opslaan die door de wizard zijn gemaakt, worden de BLOB-container en de tabellen met standaard namen en-structuur geleverd.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Invoer van gegevens bron

De wizard **gegevens importeren** maakt verbinding met een externe gegevens bron met behulp van de interne logica van Azure Cognitive Search Indexeer functies, die zijn uitgerust met een steek proef van de bron, het lezen van meta gegevens, het kraken van documenten voor het lezen van inhoud en structuur, en het serialiseren van inhoud als JSON voor de volgende import naar Azure Cognitive Search.

U kunt alleen importeren uit één tabel, database weergave of gelijkwaardige gegevens structuur, maar de structuur kan hiërarchische of geneste substructuren bevatten. Zie voor meer informatie [complex typen model leren](search-howto-complex-data-types.md).

U moet deze afzonderlijke tabel of weer gave maken voordat u de wizard uitvoert, en deze moet inhoud bevatten. Voor duidelijke redenen is het niet logisch om de wizard **gegevens importeren** uit te voeren op een lege gegevens bron.

|  Selectie | Beschrijving |
| ---------- | ----------- |
| **Bestaande gegevensbron** |Als u al Indexeer functies hebt gedefinieerd in uw zoek service, hebt u mogelijk een bestaande definitie van een gegevens bron die u opnieuw kunt gebruiken. In azure Cognitive Search worden gegevens bron objecten alleen gebruikt door Indexeer functies. U kunt een gegevens bron object maken via een programma of via de wizard **gegevens importeren** en ze indien nodig opnieuw gebruiken.|
| **Voorbeelden**| Azure Cognitive Search biedt twee ingebouwde voorbeeld gegevens bronnen die worden gebruikt in zelf studies en Snelstartgids: een onroerend goed SQL database en een Hotels-data base die wordt gehost op Cosmos DB. Zie voor een door loop op basis van het voor beeld van hotels het artikel [een index maken in de snelstartgids Azure Portal](search-get-started-portal.md) . |
| [**Azure SQL Database of SQL Managed instance**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |De servicenaam, referenties voor een databasegebruiker met leesmachtiging en de naam van een database kunnen worden opgegeven op de pagina of via een ADO.NET-verbindingsreeks. Kies de verbindingsreeksoptie om eigenschappen te bekijken of aan te passen. <br/><br/>De tabel of weergave die de rijenset bevat, moet worden opgegeven op de pagina. Deze optie wordt weergegeven nadat de verbinding tot stand is gebracht, waarna een vervolgkeuzelijst wordt weergegeven zodat u een selectie kunt maken.|
| **SQL Server op virtuele Azure-machine** |Geef een volledig gekwalificeerde service naam, gebruikers-ID en wacht woord en Data Base als connection string op. Voor het gebruik van deze gegevensbron moet u eerder een certificaat hebben geïnstalleerd in het lokale archief dat de verbinding versleutelt. Zie [SQL VM-verbinding met Azure Cognitive Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)voor instructies. <br/><br/>De tabel of weergave die de rijenset bevat, moet worden opgegeven op de pagina. Deze optie wordt weergegeven nadat de verbinding tot stand is gebracht, waarna een vervolgkeuzelijst wordt weergegeven zodat u een selectie kunt maken. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Vereisten zijn het account, de database en de verzameling. Alle documenten in de verzameling worden opgenomen in de index. U kunt een query definiëren om de rijenset samen te voegen of te filteren, of de query leeg laten. Er is geen query vereist in deze wizard.|
| [**Azure Blob Storage**](search-howto-indexing-azure-blob-storage.md) |Vereisten zijn het opslagaccount en een container. Als blob-namen een virtuele naamconventie voor groeperingsdoeleinden volgen, kunt u desgewenst het gedeelte van de virtuele map van de naam als een map onder de container opgeven. Zie [Blob Storage indexeren](search-howto-indexing-azure-blob-storage.md) voor meer informatie. |
| [**Azure Table Storage**](search-howto-indexing-azure-tables.md) |Vereisten zijn het opslagaccount en een tabelnaam. U kunt desgewenst een query opgeven om een subset van de tabellen op te halen. Zie [Table Storage indexeren](search-howto-indexing-azure-tables.md) voor meer informatie. |

## <a name="wizard-output"></a>Wizard-uitvoer

Achter de schermen worden de volgende objecten gemaakt, geconfigureerd en aangeroepen door de wizard. Nadat de wizard is uitgevoerd, kunt u de uitvoer ervan vinden in de portal pagina's. De overzichts pagina van uw service bevat lijsten met indexen, Indexeer functies, gegevens bronnen en vaardig heden. Index definities kunnen worden weer gegeven in volledige JSON in de portal. Voor andere definities kunt u de [rest API](/rest/api/searchservice/) gebruiken om specifieke objecten op te halen.

| Object | Beschrijving | 
|--------|-------------|
| [Gegevensbron](/rest/api/searchservice/create-data-source)  | Persistente verbindings gegevens naar bron gegevens, met inbegrip van referenties. Een gegevens bron object wordt uitsluitend gebruikt met Indexeer functies. | 
| [Index](/rest/api/searchservice/create-index) | Fysieke gegevens structuur die wordt gebruikt voor zoeken in volledige tekst en andere query's. | 
| [Vaardig heden](/rest/api/searchservice/create-skillset) | Een volledige set instructies voor het bewerken, transformeren en vorm geven van inhoud, met inbegrip van het analyseren en extra heren van informatie uit afbeeldings bestanden. Met uitzonde ring van eenvoudige en beperkte structuren bevat het een verwijzing naar een Cognitive Services resource die verrijking levert. Eventueel kan ook een definitie van een kennis archief bevatten.  | 
| [Indexeerfunctie](/rest/api/searchservice/create-indexer)  | Een configuratie object dat een gegevens bron, doel index, optionele vaardig heden, optionele planning en optionele configuratie-instellingen voor fout-en basis64-code ring opgeeft. |


## <a name="how-to-start-the-wizard"></a>De wizard starten

De wizard gegevens importeren wordt gestart vanaf de opdracht balk op de overzichts pagina van de service.

1. Open in de [Azure Portal](https://portal.azure.com)de pagina zoek service in het dash board of [Zoek uw service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in de lijst met Services.

2. Klik op de pagina overzicht van services bovenaan op **gegevens importeren**.

   ![De opdracht gegevens importeren in de portal](./media/search-import-data-portal/import-data-cmd2.png "De wizard Gegevens importeren starten")

U kunt ook **gegevens importeren** uit andere Azure-Services, waaronder Azure Cosmos DB, Azure SQL database, door SQL beheerd exemplaar en Azure Blob-opslag. Zoek naar **Azure Cognitive Search toevoegen** in het linkerdeel venster op de overzichts pagina van de service.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Een index schema bewerken of volt ooien in de wizard

De wizard genereert een onvolledige index, die wordt gevuld met documenten die zijn opgehaald uit de invoer gegevens bron. Voor een functionele index moet u de volgende elementen definiëren.

1. Is de lijst met velden voltooid? Voeg nieuwe velden toe die niet worden gesampled en verwijder de waarden die geen waarde toevoegen aan een zoek ervaring of die niet worden gebruikt in een [filter expressie](search-query-odata-filter.md) of [Score profiel](index-add-scoring-profiles.md).

1. Is het gegevens type dat geschikt is voor de inkomende gegevens? Azure Cognitive Search ondersteunt de [gegevens typen van de Entity Data Model (EDM)](/rest/api/searchservice/supported-data-types). Voor Azure SQL-gegevens is er een [toewijzings grafiek](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) die gelijkwaardige waarden bevat. Zie [veld Toewijzingen en trans formaties](search-indexer-field-mappings.md)voor meer achtergrond informatie.

1. Hebt u één veld dat kan dienen als de *sleutel*? Dit veld moet EDM. String zijn en moet een unieke identificatie vormen voor een document. Voor relationele gegevens kan deze worden toegewezen aan een primaire sleutel. Voor blobs is dit mogelijk de `metadata-storage-path` . Als veld waarden spaties of streepjes bevatten, moet u de optie **Base-64 Codeer sleutel** instellen in de stap **een Indexeer functie maken** onder **Geavanceerde opties** om de validatie controle voor deze tekens te onderdrukken.

1. Stel kenmerken in om te bepalen hoe dit veld wordt gebruikt in een index. 

   Neem even de tijd met deze stap omdat de kenmerken de fysieke expressie van velden in de index bepalen. Als u later de kenmerken wilt wijzigen, zelfs via een programma, moet u de index bijna altijd verwijderen en opnieuw bouwen. Kern kenmerken, zoals **doorzoekbaar** en **ophalen** , hebben een [Verwaarloos bare invloed op de opslag](search-what-is-an-index.md#index-size). Door filters in te scha kelen en Voorst Ellen te gebruiken, worden de opslag vereisten verhoogd. 
   
   + **Doorzoekbaar** maakt zoeken in volledige tekst mogelijk. Elk veld dat wordt gebruikt in vrije-vorm query's of in query-expressies moet dit kenmerk hebben. Er worden omgekeerde indexen gemaakt voor elk veld dat u als **doorzoekbaar** markeert.

   + **Ophalenable** retourneert het veld in de zoek resultaten. Elk veld dat inhoud aan Zoek resultaten levert, moet dit kenmerk hebben. Het instellen van dit veld heeft geen aanzienlijke gevolgen voor de index grootte.

   + Met **filterable** kan worden verwezen naar het veld in filter expressies. Elk veld dat in een **$filter**  expressie wordt gebruikt, moet dit kenmerk hebben. Filter expressies zijn voor exacte overeenkomsten. Omdat tekst teken reeksen intact blijven, is extra opslag ruimte vereist voor de Verbatim-inhoud.

   + **Facetable** schakelt het veld in voor facet navigatie. Alleen velden die ook als **filterbaar** zijn gemarkeerd, kunnen als **facetbaar** worden gemarkeerd.

   + Met **sorteerbaar** kunt u het veld in een sortering gebruiken. Elk veld dat in een **$OrderBy** expressie wordt gebruikt, moet dit kenmerk hebben.

1. Hebt u [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis)nodig? Voor EDM. String-velden die **doorzoekbaar** zijn, kunt u een **Analyzer** instellen als u de taal uitgebreid indexeren en query's wilt uitvoeren. 

   De standaard waarde is *standaard lucene* , maar u kunt *micro soft English* kiezen als u de analyse functie van micro soft voor geavanceerde lexicale verwerking wilt gebruiken, zoals het oplossen van onregelmatige zelfstandig-en verbale formulieren. Alleen taal analyse functies kunnen worden opgegeven in de portal. Het gebruik van een aangepaste analyse functie of een niet-taal analyse zoals sleutel woord, patroon, enzovoort moet via een programma worden uitgevoerd. Zie voor meer informatie over analyse functies [taal analysen toevoegen](search-language-support.md).

1. Hebt u de typeahead-functionaliteit nodig in de vorm van automatisch aanvullen of voorgestelde resultaten? Schakel het selectie vakje Voorst **Ellen** in om [typeahead-query suggesties en automatisch aanvullen](index-add-suggesters.md) voor geselecteerde velden in te scha kelen. Suggesties worden toegevoegd aan het aantal tokens in uw index en gebruiken daarom meer opslag.


## <a name="next-steps"></a>Volgende stappen

De beste manier om de voor delen en beperkingen van de wizard te begrijpen, is door deze stap te door lopen. De volgende Snelstartgids begeleidt u bij elke stap.

> [!div class="nextstepaction"]
> [Een Azure Cognitive Search-index maken met behulp van de Azure Portal](search-get-started-portal.md)