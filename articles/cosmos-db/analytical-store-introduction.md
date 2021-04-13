---
title: Wat is analytische opslag van Azure Cosmos DB?
description: Meer informatie Azure Cosmos DB transactionele (op rijen gebaseerde) en analytische opslag (kolomgebaseerd). Voordelen van analytische opslag, impact op prestaties voor grootschalige workloads en automatische synchronisatie van gegevens van transactionele opslag naar analytische opslag
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: eaabc663ba243423bddf7ef6abfe41182e06b4f9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364601"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Wat is Azure Cosmos DB analytische opslag?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB analytische opslag is een volledig geïsoleerde kolomopslag voor het inschakelen van grootschalige analyses op basis van operationele gegevens in uw Azure Cosmos DB, zonder gevolgen voor uw transactionele workloads. 

Azure Cosmos DB transactionele opslag is schema-agnostisch en het stelt u in staat om uw transactionele toepassingen te itereren zonder dat u te maken hebt met schema- of indexbeheer. In tegenstelling tot dit wordt Azure Cosmos DB analytische opslag geschematiseerd om de prestaties van analytische query's te optimaliseren. In dit artikel wordt gedetailleerde informatie over analytische opslag beschreven.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Uitdagingen met grootschalige analyses van operationele gegevens

De operationele gegevens met meerdere modellen in Azure Cosmos DB container worden intern opgeslagen in een geïndexeerd' transactioneel opslag op rijbasis. De rijopslagindeling is ontworpen om snelle transactionele lees- en schrijf schrijftijden in de reactietijden van milliseconden en operationele query's toe te staan. Als uw gegevensset groter wordt, kunnen complexe analytische query's kostbaar zijn wat betreft inrichten van doorvoer op de gegevens die in deze indeling zijn opgeslagen. Een hoog verbruik van inrichtende doorvoer heeft op zijn beurt invloed op de prestaties van transactionele workloads die worden gebruikt door uw realtime toepassingen en services.

Normaal gezien worden voor het analyseren van grote hoeveelheden gegevens operationele gegevens geëxtraheerd uit Azure Cosmos DB transactionele opslag en opgeslagen in een afzonderlijke gegevenslaag. De gegevens worden bijvoorbeeld opgeslagen in een datawarehouse of data lake in een geschikte indeling. Deze gegevens worden later gebruikt voor grootschalige analyses en geanalyseerd met behulp van een berekeningsen engine zoals de Apache Spark clusters. Deze scheiding van analytische opslag- en rekenlagen van operationele gegevens resulteert in extra latentie, omdat de ETL-pijplijnen (Extraheren, Transformeren, Laden) minder vaak worden uitgevoerd om de mogelijke impact op uw transactionele workloads te minimaliseren.

De ETL-pijplijnen worden ook complex bij het verwerken van updates voor de operationele gegevens in vergelijking met het verwerken van alleen nieuw opgenomen operationele gegevens. 

## <a name="column-oriented-analytical-store"></a>Kolomgeoriënteerde analytische opslag

Azure Cosmos DB analytische opslag is een oplossing voor de complexiteit en latentie-uitdagingen die zich voordoen bij de traditionele ETL-pijplijnen. Azure Cosmos DB analytische opslag kunt uw operationele gegevens automatisch synchroniseren in een afzonderlijke kolomopslag. Kolomopslagindeling is geschikt voor grootschalige analytische query's die op een geoptimaliseerde manier kunnen worden uitgevoerd, waardoor de latentie van dergelijke query's wordt verbeterd.

Met Azure Synapse Link kunt u nu no-ETL HTAP-oplossingen bouwen door rechtstreeks te koppelen aan Azure Cosmos DB analytische opslag vanuit Azure Synapse Analytics. Hiermee kunt u bijna realtime grootschalige analyses uitvoeren op uw operationele gegevens.

## <a name="features-of-analytical-store"></a>Functies van analytische opslag 

Wanneer u analytische opslag in een Azure Cosmos DB inschakelen, wordt intern een nieuwe kolomopslag gemaakt op basis van de operationele gegevens in uw container. Deze kolomopslag wordt afzonderlijk van het rijgeoriënteerde transactionele opslag voor die container bewaard. De invoegingen, updates en verwijderen van uw operationele gegevens worden automatisch gesynchroniseerd met de analytische opslag. U hebt de wijzigingsfeed of ETL niet nodig om de gegevens te synchroniseren.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Kolomopslag voor analytische workloads op operationele gegevens

Analytische workloads omvatten doorgaans aggregaties en sequentiële scans van geselecteerde velden. Door de gegevens op te slaan in een kolom-grote volgorde, kan de analytische opslag een groep waarden voor elk veld samen serialiseren. Deze indeling vermindert de IOPS die nodig is om statistieken te scannen of te berekenen voor specifieke velden. Hiermee worden de reactietijden van query's voor scans voor grote gegevenssets aanzienlijk verbeterd. 

Als uw operationele tabellen bijvoorbeeld de volgende indeling hebben:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Voorbeeld van een operationele tabel" border="false":::

De bovenstaande gegevens worden in de rijopslag opgeslagen in een geseraliseerde indeling, per rij, op de schijf. Deze indeling maakt snellere transactionele lees-, schrijf- en operationele query's mogelijk, zoals 'Retourinformatie over Product1'. Als de gegevensset echter groter wordt en u complexe analytische query's op de gegevens wilt uitvoeren, kan dit kostbaar zijn. Als u bijvoorbeeld 'de verkooptrends voor een product onder de categorie Apparatuur' in verschillende bedrijfseenheden en maanden wilt zien, moet u een complexe query uitvoeren. Grote scans op deze gegevensset kunnen kostbaar worden in termen van inrichtende doorvoer en kunnen ook van invloed zijn op de prestaties van de transactionele workloads die uw realtime toepassingen en services aanstijven.

Analytische opslag, een kolomopslag, is beter geschikt voor dergelijke query's omdat deze vergelijkbare gegevensvelden samen serialiseert en de schijf-IOPS vermindert.

In de volgende afbeelding ziet u een transactionele rijopslag versus analytische kolomopslag in Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transactionele rijopslag versus analytische kolomopslag in Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Losgekoppelde prestaties voor analytische workloads

Analytische query's hebben geen invloed op de prestaties van uw transactionele workloads, omdat de analytische opslag gescheiden is van de transactionele opslag.  Voor analytische opslag hoeven geen afzonderlijke aanvraageenheden (AANVRAAGeenheden) te worden toegewezen.

### <a name="auto-sync"></a>Automatisch synchroniseren

Automatische synchronisatie verwijst naar de volledig beheerde mogelijkheid van Azure Cosmos DB waar de invoegingen, updates en verwijderen van operationele gegevens automatisch in bijna realtime vanuit een transactionele opslag worden gesynchroniseerd naar analytische opslag. Latentie voor automatische synchronisatie is doorgaans binnen twee minuten. In het geval van een gedeelde doorvoerdatabase met een groot aantal containers kan de latentie voor automatische synchronisatie van afzonderlijke containers hoger zijn en kan dit tot vijf minuten duren. We willen graag meer weten over hoe deze latentie in uw scenario's past. Neem dan contact op met het [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com).

De functie voor automatische synchronisatie samen met de analytische opslag biedt de volgende belangrijke voordelen:

### <a name="scalability--elasticity"></a>Schaalbaarheid & elasticiteit

Door horizontale partitionering te gebruiken, Azure Cosmos DB transactionele opslag de opslag en doorvoer elastisch schalen zonder uitvaltijd. Horizontale partitionering in de transactionele opslag biedt schaalbaarheid & elasticiteit bij automatische synchronisatie om ervoor te zorgen dat gegevens bijna in realtime worden gesynchroniseerd met de analytische opslag. De gegevenssynchronisatie vindt plaats ongeacht de doorvoer van transactioneel verkeer, ongeacht of het 1000 bewerkingen per seconde of 1 miljoen bewerkingen per seconde is, en heeft geen invloed op de inrichtende doorvoer in de transactionele opslag. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Automatisch schema-updates verwerken

Azure Cosmos DB transactionele opslag is schema-agnostisch en het stelt u in staat om uw transactionele toepassingen te itereren zonder dat u te maken hebt met schema- of indexbeheer. In tegenstelling tot dit wordt Azure Cosmos DB analytische opslag geschematiseerd om de prestaties van analytische query's te optimaliseren. Met de functie voor automatische synchronisatie Azure Cosmos DB de schemadeferentie over de meest recente updates van de transactionele opslag.  Het beheert ook de schemaweergave in de analytische opslag, inclusief het verwerken van geneste gegevenstypen.

Naarmate uw schema zich verder ontwikkelt en er in de loop van de tijd nieuwe eigenschappen worden toegevoegd, presenteert de analytische opslag automatisch een samen te voegen schema voor alle historische schema's in de transactionele opslag.

#### <a name="schema-constraints"></a>Schemabeperkingen

De volgende beperkingen zijn van toepassing op de operationele gegevens in Azure Cosmos DB als u analytische opslag in staat stelt om automatisch het schema af te afleiden en weer te geven:

* U kunt maximaal 1000 eigenschappen hebben op elk nestniveau in het schema en een maximale nestdiepte van 127.
  * Alleen de eerste 1000 eigenschappen worden weergegeven in de analytische opslag.
  * Alleen de eerste 127 geneste niveaus worden weergegeven in de analytische opslag.

* Hoewel JSON-documenten (en Cosmos DB verzamelingen/containers) hoofd- en hoofd- en uniek zijn, is analytische opslag dat niet.

  * **In hetzelfde document:** Eigenschappennamen op hetzelfde niveau moeten uniek zijn wanneer ze niet-case-ongevoelig zijn vergeleken. Het volgende JSON-document heeft bijvoorbeeld 'Naam' en 'naam' op hetzelfde niveau. Hoewel het een geldig JSON-document is, voldoet het niet aan de uniekheidsbeperking en wordt het daarom niet volledig weergegeven in de analytische opslag. In dit voorbeeld zijn 'Naam' en 'naam' hetzelfde wanneer ze worden vergeleken op een niet-casegevoelige manier. Alleen `"Name": "fred"` worden weergegeven in de analytische opslag, omdat dit de eerste keer is. En `"name": "john"` worden helemaal niet weergegeven.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **In verschillende documenten:** Eigenschappen op hetzelfde niveau en met dezelfde naam, maar in verschillende gevallen, worden weergegeven in dezelfde kolom, met behulp van de naamindeling van de eerste instantie. De volgende JSON-documenten hebben `"Name"` bijvoorbeeld en op hetzelfde `"name"` niveau. Aangezien de eerste documentindeling is, wordt dit gebruikt om de eigenschapsnaam `"Name"` in de analytische opslag weer te geven. Met andere woorden, de kolomnaam in de analytische opslag is `"Name"` . Zowel `"fred"` als worden weergegeven in de kolom `"john"` `"Name"` .


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* Het eerste document van de verzameling definieert het schema van de initiële analytische opslag.
  * Eigenschappen op het eerste niveau van het document worden weergegeven als kolommen.
  * Documenten met meer eigenschappen dan het oorspronkelijke schema genereren nieuwe kolommen in de analytische opslag.
  * Kolommen kunnen niet worden verwijderd.
  * Het verwijderen van alle documenten in een verzameling betekent niet dat het schema van de analytische opslag opnieuw wordt ingesteld.
  * Er is geen schemaversieversie. De laatste versie die uit de transactionele opslag is afgeleid, is wat u ziet in de analytische opslag.

* Momenteel wordt geen ondersteuning Azure Synapse spark-leeskolomnamen die lege tekst (spaties) bevatten.

* Verwacht ander gedrag met betrekking tot expliciete `null` waarden:
  * Spark-pools in Azure Synapse lezen deze waarden als `0` (nul).
  * Serverloze SQL-pools in Azure Synapse lezen deze waarden alsof het eerste document van de verzameling voor dezelfde eigenschap een waarde met een `NULL` `non-numeric` gegevenstype bevat.
  * Serverloze SQL-pools in Azure Synapse lezen deze waarden als (nul) als het eerste document van de verzameling voor dezelfde eigenschap een waarde met een `0` `numeric` gegevenstype bevat.

* Verwacht ander gedrag met betrekking tot ontbrekende kolommen:
  * Spark-pools in Azure Synapse worden deze kolommen weergegeven als `undefined` .
  * Serverloze SQL-pools in Azure Synapse worden deze kolommen weergegeven als `NULL` .

#### <a name="schema-representation"></a>Schemarepresentatie

Er zijn twee modi voor schemarepresentatie in de analytische opslag. Deze modi maken een afweging tussen de eenvoud van een kolomweergave, het behandelen van de polymorfe schema's en eenvoud van de query-ervaring:

* Goed gedefinieerde schemaweergave
* Schemaweergave van volledige betrouwbaarheid

> [!NOTE]
> Wanneer analytische opslag is ingeschakeld, is voor SQL (Core) API-accounts de standaardschemaweergave in de analytische opslag duidelijk gedefinieerd. Waar voor Azure Cosmos DB API voor MongoDB-accounts, is de standaardweergave van het schema in de analytische opslag een volledige betrouwbaarheidsschemaweergave. Als u scenario's hebt waarvoor een andere schemaweergave is vereist dan de standaardaanbieding voor elk van deze API's, kunt u contact op met het Azure Cosmos DB [om](mailto:cosmosdbsynapselink@microsoft.com) dit in te stellen.

**Goed gedefinieerde schemaweergave**

De goed gedefinieerde schemaweergave maakt een eenvoudige tabelweergave van de schema-agnostische gegevens in het transactionele opslag. De goed gedefinieerde schemaweergave heeft de volgende overwegingen:

* Een eigenschap heeft altijd hetzelfde type voor meerdere items.
* We staan slechts één typewijziging toe, van null in een ander gegevenstype. De eerste niet-null-instantie definieert het kolomgegevenstype.

  * heeft bijvoorbeeld geen goed gedefinieerd schema, omdat soms een `{"a":123} {"a": "str"}` `"a"` tekenreeks en soms een getal is. In dit geval registreert de analytische opslag het gegevenstype van als het gegevenstype van in het eerste item in de `"a"` `“a”` levensduur van de container. Het document wordt nog steeds opgenomen in de analytische opslag, maar items waar het gegevenstype `"a"` van verschilt, zijn dat niet.
  
    Deze voorwaarde is niet van toepassing op null-eigenschappen. Is bijvoorbeeld `{"a":123} {"a":null}` nog steeds goed gedefinieerd.

* Matrixtypen moeten één herhaald type bevatten.

  * is bijvoorbeeld geen goed gedefinieerd schema omdat de matrix een combinatie van gehele getallen en `{"a": ["str",12]}` tekenreekstypen bevat.

> [!NOTE]
> Als de Azure Cosmos DB analytische opslag de goed gedefinieerde schemaweergave volgt en de bovenstaande specificatie wordt geschonden door bepaalde items, worden deze items niet opgenomen in de analytische opslag.

* U kunt verschillende gedragingen verwachten met betrekking tot verschillende typen in een goed gedefinieerd schema:
  * Spark-pools in Azure Synapse vertegenwoordigen deze waarden als `undefined` .
  * Serverloze SQL-pools in Azure Synapse vertegenwoordigen deze waarden als `NULL` .


**Schemaweergave voor volledige betrouwbaarheid**

De schemaweergave voor volledige betrouwbaarheid is ontworpen voor het afhandelen van de volledige breedte van polymorfe schema's in de schema-agnostische operationele gegevens. In deze schemaweergave worden geen items uit de analytische opslag weggeslagen, zelfs niet als de goed gedefinieerde schemabeperkingen (die geen velden van het gegevenstype of gemengde gegevenstype matrices zijn) worden geschonden.

Dit wordt bereikt door de leaf-eigenschappen van de operationele gegevens om te zetten in de analytische opslag met afzonderlijke kolommen op basis van het gegevenstype van waarden in de eigenschap . De leaf-eigenschapsnamen worden uitgebreid met gegevenstypen als achtervoegsel in het schema van de analytische opslag, zodat ze zonder dubbelzinnigheid query's kunnen zijn.

Laten we bijvoorbeeld het volgende voorbeelddocument in de transactionele opslag nemen:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

De leaf-eigenschap binnen het geneste object wordt in het schema van de analytische opslag weergegeven `streetNo` `address` als een kolom `address.object.streetNo.int32` . Het gegevenstype wordt toegevoegd als achtervoegsel aan de kolom. Op deze manier, als er een ander document wordt toegevoegd aan de transactionele opslag waarbij de waarde van de leaf-eigenschap '123' is (let op dat het een tekenreeks is), verandert het schema van de analytische opslag automatisch zonder het type van een eerder geschreven kolom te `streetNo` wijzigen. Er wordt een nieuwe kolom toegevoegd aan de analytische `address.object.streetNo.string` opslag, waarbij deze waarde '123' wordt opgeslagen.

**Gegevenstype naar achtervoegselkaart**

Hier ziet u een overzicht van alle eigenschapsgegevenstypen en de weergaven van het achtervoegsel in de analytische opslag:

|Oorspronkelijk gegevenstype  |Achtervoegsel  |Voorbeeld  |
|---------|---------|---------|
| Dubbel |  ".float64" |    24.99|
| Matrix | ".array" |    ["a", "b"]|
|Binair | ".binary" |0|
|Booleaans    | ".bool"   |Waar|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Null   | ".null"   | null|
|Tekenreeks|    ".string" | "ABC"|
|Tijdstempel |    ".timestamp" |  Tijdstempel(0, 0)|
|DateTime   |".date"    | ISODate("2020-08-21T07:43:07.375Z")|
|ObjectId   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|Document   |".object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Rendabele archivering van historische gegevens

Gegevenslagen verwijst naar de scheiding van gegevens tussen opslaginfrastructuren die zijn geoptimaliseerd voor verschillende scenario's. Hierdoor worden de algehele prestaties en kosteneffectiviteit van de end-to-end gegevensstack verbeterd. Met analytische opslag ondersteunt Azure Cosmos DB nu automatische opslaglagen van gegevens uit de transactionele opslag naar analytische opslag met verschillende gegevensindelingen. Met analytische opslag geoptimaliseerd in termen van opslagkosten in vergelijking met de transactionele opslag, kunt u veel langere horizonken van operationele gegevens bewaren voor historische analyse.

Nadat de analytische opslag is ingeschakeld, kunt u op basis van de gegevensretentiebehoeften van de transactionele workloads de eigenschap Transactional Store Time to Live (Transactional TTL) configureren om records na een bepaalde periode automatisch te laten verwijderen uit het transactionele archief. Op dezelfde manier kunt u met 'Analytical Store Time To Live (Analytical TTL)' de levenscyclus beheren van gegevens die in de analytische opslag worden bewaard, onafhankelijk van de transactionele opslag. Door analytische opslag in te stellen en TTL-eigenschappen te configureren, kunt u de gegevensretentieperiode voor de twee winkels naadloos in lagen opslaan en definiëren.

### <a name="global-distribution"></a>Wereldwijde distributie

Als u een wereldwijd gedistribueerd Azure Cosmos DB account hebt en u analytische opslag voor een container hebt ingeschakeld, is het account beschikbaar in alle regio's van dat account.  Wijzigingen in operationele gegevens worden globaal gerepliceerd in alle regio's. U kunt analytische query's effectief uitvoeren op de dichtstbijzijnde regionale kopie van uw gegevens in Azure Cosmos DB.

### <a name="security"></a>Beveiliging

Verificatie met de analytische opslag is hetzelfde als de transactionele opslag voor een bepaalde database. U kunt primaire of alleen-lezen sleutels gebruiken voor verificatie. U kunt gebruikmaken van gekoppelde service in Synapse Studio te voorkomen dat de sleutels Azure Cosmos DB in de Spark-notebooks. Toegang tot deze gekoppelde service is beschikbaar voor iedereen die toegang heeft tot de werkruimte.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Ondersteuning voor meerdere Azure Synapse Analytics runtimes

De analytische opslag is geoptimaliseerd om schaalbaarheid, elasticiteit en prestaties te bieden voor analytische workloads zonder afhankelijk te zijn van de rekenrun times. De opslagtechnologie wordt zelf beheerd om uw analyseworkloads te optimaliseren zonder handmatige inspanningen.

Door het analytische opslagsysteem los te koppelen van het analytische rekensysteem, kunnen gegevens in de analytische opslag van Azure Cosmos DB tegelijkertijd worden opgevraagd vanuit de verschillende analyseruntimes die worden ondersteund door Azure Synapse Analytics. Vanaf nu biedt Azure Synapse Analytics ondersteuning Apache Spark en serverloze SQL-pool met Azure Cosmos DB analytische opslag.

> [!NOTE]
> U kunt alleen lezen uit de analytische opslag met Azure Synapse Analytics run time. U kunt de gegevens als een bedienende laag terugschrijven naar uw transactionele opslag.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Prijzen

Analytische opslag volgt een prijsmodel op basis van verbruik waarbij u kosten in rekening wordt gebracht:

* Opslag: het volume van de gegevens dat elke maand in de analytische opslag wordt bewaard, inclusief historische gegevens zoals gedefinieerd door analytische TTL.

* Analytische schrijfbewerkingen: de volledig beheerde synchronisatie van operationele gegevensupdates naar de analytische opslag vanuit de transactionele opslag (automatische synchronisatie)

* Analytische leesbewerkingen: de leesbewerkingen die worden uitgevoerd op de analytische opslag van Azure Synapse Analytics Spark-pool en serverloze SQL-poolrun times.

Prijzen voor analytische opslag staan los van het prijsmodel voor een transactieopslag. Er is geen concept van inrichtende RUs in de analytische opslag. Zie [Azure Cosmos DB pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/)voor volledige informatie over het prijsmodel voor analytische opslag.

Als u een schatting van de kosten op hoog niveau wilt maken om analytische opslag op een Azure Cosmos DB-container mogelijk te maken, kunt u [de Azure Cosmos DB Capacity Planner](https://cosmos.azure.com/capacitycalculator/) gebruiken om een schatting te krijgen van de kosten voor analytische opslag en schrijfbewerkingen. De kosten voor analytische leesbewerkingen zijn afhankelijk van de kenmerken van de analyseworkload, maar als een schatting op hoog niveau resulteert het scannen van 1 TB aan gegevens in de analytische opslag meestal in 130.000 analytische leesbewerkingen en resulteert dit in een kosten van $ 0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analytische time-to-live (TTL)

Analytical TTL geeft aan hoe lang gegevens voor een container moeten worden bewaard in uw analytische opslag. 

Als analytische opslag is ingeschakeld, worden invoegen, updates en verwijderen van operationele gegevens automatisch gesynchroniseerd van transactionele opslag naar analytische opslag, ongeacht de transactionele TTL-configuratie. De retentie van deze operationele gegevens in de analytische opslag kan worden beheerd door de analytische TTL-waarde op containerniveau, zoals hieronder wordt aangegeven:

Analytische TTL voor een container wordt ingesteld met behulp van de `AnalyticalStoreTimeToLiveInSeconds` eigenschap :

* Als de waarde is ingesteld op '0', ontbreekt (of is ingesteld op null): de analytische opslag is uitgeschakeld en er worden geen gegevens gerepliceerd van transactionele opslag naar analytische opslag

* Indien aanwezig en de waarde is ingesteld op '-1': de analytische opslag bewaart alle historische gegevens, ongeacht de bewaarperiode van de gegevens in de transactionele opslag. Deze instelling geeft aan dat de analytische opslag een oneindige retentie van uw operationele gegevens heeft

* Als aanwezig is en de waarde is ingesteld op een positief getal 'n': items verlopen uit de analytische opslag 'n' seconden na de laatste wijziging in de transactionele opslag. Deze instelling kan worden gebruikt als u uw operationele gegevens voor een beperkte periode in de analytische opslag wilt bewaren, ongeacht de retentie van de gegevens in de transactionele opslag

Enkele punten om in overweging te nemen:

*   Nadat de analytische opslag is ingeschakeld met een analytische TTL-waarde, kan deze later worden bijgewerkt naar een andere geldige waarde. 
*   Hoewel transactionele TTL kan worden ingesteld op container- of itemniveau, kan de analytische TTL momenteel alleen worden ingesteld op containerniveau.
*   U kunt uw operationele gegevens langer bewaren in de analytische opslag door analytische TTL->= transactionele TTL op containerniveau in te stellen.
*   De analytische opslag kan worden gemaakt om de transactionele opslag te spiegelen door analytische TTL = transactionele TTL in te stellen.

Wanneer u analytische opslag in een container inschakelen:

* Vanaf de Azure Portal is de optie analytische TTL ingesteld op de standaardwaarde -1. U kunt deze waarde wijzigen in 'n' seconden door te navigeren naar containerinstellingen onder Data Explorer. 
 
* Vanuit de Azure SDK, PowerShell of CLI kan de analytische TTL-optie worden ingeschakeld door deze in te stellen op -1 of 'n'. 

Zie Analytische TTL configureren op een container voor [meer informatie.](configure-synapse-link.md#create-analytical-ttl)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende documenten voor meer informatie:

* [Azure Synapse Link voor Azure Cosmos DB](synapse-link.md)

* [Aan de slag met Azure Synapse Link voor Azure Cosmos DB](configure-synapse-link.md)

* [Veelgestelde vragen over Synapse Link voor Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Use cases voor Azure Synapse Link voor Azure Cosmos DB](synapse-link-use-cases.md)
