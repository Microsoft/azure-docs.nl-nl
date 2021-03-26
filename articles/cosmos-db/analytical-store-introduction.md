---
title: Wat is Azure Cosmos DB Analytical Store?
description: Meer informatie over Azure Cosmos DB transactionele (op rijen gebaseerde) en analytische (op kolommen gebaseerde) opslag. Voor delen van de analytische opslag, invloed op de prestaties voor grootschalige workloads en automatische synchronisatie van gegevens van transactionele opslag naar een analytische opslag
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: 450514541a90a01ea6b70f77491f116adb404887
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046209"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Wat is Azure Cosmos DB Analytical Store?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB Analytical Store is een volledig geïsoleerde column Store voor het inschakelen van grootschalige analyses op basis van operationele gegevens in uw Azure Cosmos DB, zonder dat dit invloed heeft op uw transactionele werk belastingen. 

Azure Cosmos DB transactionele Store is schema-neutraal en biedt u de mogelijkheid om uw transactionele toepassingen te herhalen zonder schema-of index beheer te hoeven afhandelen. Azure Cosmos DB Analytical Store daarentegen is geschematiseerde om te optimaliseren voor analytische query prestaties. In dit artikel wordt gedetailleerde informatie over analytische opslag beschreven.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Uitdagingen met grootschalige analyses op operationele gegevens

De Bedrijfs gegevens met meerdere modellen in een Azure Cosmos DB container worden intern opgeslagen in een geïndexeerde rij transactionele Store. De indeling van een rij-archief is ontworpen om snelle transactionele Lees-en schrijf bewerkingen toe te staan in de reactie tijden van de milliseconden en operationele query's. Als uw gegevensset erg groot wordt, kunnen complexe analytische query's kostbaar zijn in termen van ingerichte door Voer voor de gegevens die zijn opgeslagen in deze indeling. Hoog verbruik van ingerichte door Voer is op zijn beurt van invloed op de prestaties van transactionele workloads die worden gebruikt door uw realtime-toepassingen en-services.

Traditioneel worden operationele gegevens geëxtraheerd uit de transactionele Store van Azure Cosmos DB en opgeslagen in een afzonderlijke gegevenslaag, om grote hoeveel heden gegevens te analyseren. De gegevens worden bijvoorbeeld in een Data Warehouse of Data Lake opgeslagen in een geschikte indeling. Deze gegevens worden later gebruikt voor grootschalige analyses en geanalyseerd met behulp van Compute engine, zoals de Apache Spark clusters. Deze schei ding van analytische opslag en reken lagen van operationele gegevens resulteert in extra latentie, omdat de ETL-pijp lijnen (extract, trans formatie, Load) minder vaak worden uitgevoerd om de potentiële impact op uw transactionele workloads te beperken.

De ETL-pijp lijnen worden ook complex wanneer er updates worden verwerkt in de operationele gegevens in vergelijking met het verwerken van alleen nieuwe opgenomen operationele gegevens. 

## <a name="column-oriented-analytical-store"></a>Kolom gerichte analytische opslag

Azure Cosmos DB Analytical Store verhelpt de complexiteits-en latentie uitdagingen die zich voordoen met de traditionele ETL-pijp lijnen. Met Azure Cosmos DB Analytical Store kunnen uw operationele gegevens automatisch worden gesynchroniseerd in een afzonderlijk kolom archief. De indeling van Column Store is geschikt voor grootschalige analytische query's die op een geoptimaliseerde manier worden uitgevoerd, wat resulteert in het verbeteren van de latentie van dergelijke query's.

Met de koppeling Azure Synapse kunt u nu geen ETL-HTAP-oplossingen bouwen door rechtstreeks een koppeling te maken met Azure Cosmos DB Analytical Store vanuit Azure Synapse Analytics. Zo kunt u bijna realtime grootschalige analyses uitvoeren op uw operationele gegevens.

## <a name="features-of-analytical-store"></a>Functies van de analytische opslag 

Wanneer u het analytische archief in een Azure Cosmos DB container inschakelt, wordt een nieuwe kolom opgeslagen die intern wordt gemaakt op basis van de operationele gegevens in de container. Dit kolom archief wordt afzonderlijk van het rij-georiënteerde transactionele Archief voor die container bewaard. De toevoegingen, updates en verwijderingen voor uw operationele gegevens worden automatisch gesynchroniseerd naar de analytische opslag. U hebt de wijzigings feed of ETL niet nodig om de gegevens te synchroniseren.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Kolom opslag voor analytische werk belastingen op operationele gegevens

Analytische werk belastingen omvatten doorgaans aggregaties en sequentiële scans van geselecteerde velden. Door de gegevens op te slaan in een kolom-primaire volg orde, kan de analytische opslag een groep waarden voor elk veld met elkaar worden geserialiseerd. Deze indeling vermindert de IOPS die nodig is om statistieken te scannen of te berekenen ten opzichte van specifieke velden. De reactie tijden van query's voor scans in grote gegevens sets worden aanzienlijk verbeterd. 

Als uw operationele tabellen bijvoorbeeld de volgende indeling hebben:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Voor beeld van operationele tabel" border="false":::

In het rij-archief worden de bovenstaande gegevens opgeslagen in een geserialiseerde indeling, per rij, op de schijf. Met deze indeling kunnen transactionele Lees bewerkingen, schrijf bewerkingen en operationele query's sneller worden uitgevoerd, zoals ' retour informatie over Product1 '. Omdat de gegevensset echter groot groeit en als u complexe analytische query's wilt uitvoeren op de gegevens, kan het kostbaar zijn. Als u bijvoorbeeld ' de verkoop trends voor een product onder de categorie ' apparatuur ' over verschillende bedrijfs eenheden en maanden wilt krijgen, moet u een complexe query uitvoeren. Grote scans op deze gegevensset kunnen duur verkrijgen in termen van ingerichte door Voer en kunnen ook van invloed zijn op de prestaties van de transactionele workloads die uw realtime-toepassingen en-services inschakelen.

Analytische opslag, een column Store, is beter geschikt voor dergelijke query's, omdat deze soort gelijke velden met gegevens aan elkaar heeft geserial en de schijf-IOPS vermindert.

De volgende afbeelding toont transactionele rijen Store versus analytisch kolom archief in Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transactionele rij-archief versus analytisch kolom archief in Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Ontkoppelde prestaties voor analytische werk belastingen

Er is geen invloed op de prestaties van uw transactionele workloads vanwege analytische query's, omdat het analytische archief gescheiden is van het transactionele archief.  Voor de analytische opslag zijn geen afzonderlijke aanvraag eenheden (RUs) nodig om te worden toegewezen.

### <a name="auto-sync"></a>Automatische synchronisatie

Automatische synchronisatie verwijst naar de volledig beheerde mogelijkheid van Azure Cosmos DB waarbij de toevoegingen, updates en verwijderingen van de operationele gegevens automatisch worden gesynchroniseerd vanuit transactioneel archief naar een analytische opslag in de nabije real-time. Latentie van automatische synchronisatie is doorgaans binnen twee minuten. In het geval van een gedeelde doorvoer database met een groot aantal containers, kan de latentie van de automatische synchronisatie van afzonderlijke containers hoger zijn en kan het tot vijf minuten duren. We willen graag meer informatie over hoe deze latentie past bij uw scenario's. Neem hiervoor contact op met het Azure Cosmos DB- [team](mailto:cosmosdbsynapselink@microsoft.com).

De functie voor automatische synchronisatie in combi natie met analytische opslag biedt de volgende belang rijke voor delen:

### <a name="scalability--elasticity"></a>Schaal baarheid & elasticiteit

Door gebruik te maken van horizontale partitionering kan Azure Cosmos DB transactionele Store de opslag en door Voer op elastische wijze schalen zonder uitval tijd. Horizon taal partitioneren in het transactionele archief biedt schaal baarheid & elasticiteit in automatische synchronisatie om ervoor te zorgen dat gegevens in bijna realtime worden gesynchroniseerd met de analytische opslag. De gegevens synchronisatie vindt plaats ongeacht de door Voer van transactionele verkeer, of het nu 1000 bewerkingen per seconde of 1.000.000 bewerkingen per seconde is en heeft geen invloed op de ingerichte door Voer in het transactionele archief. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Automatisch schema-updates verwerken

Azure Cosmos DB transactionele Store is schema-neutraal en biedt u de mogelijkheid om uw transactionele toepassingen te herhalen zonder schema-of index beheer te hoeven afhandelen. Azure Cosmos DB Analytical Store daarentegen is geschematiseerde om te optimaliseren voor analytische query prestaties. Met de functie Automatische synchronisatie beheert Azure Cosmos DB het schema dezicht over de meest recente updates van het transactionele archief.  Het beheert ook de schema weergave in de analytische out-of-the-box, inclusief het verwerken van geneste gegevens typen.

Naarmate uw schema wordt gegroeid en nieuwe eigenschappen worden toegevoegd na verloop van tijd, presenteert de analytische opslag automatisch een samenvoegings schema voor alle historische schema's in het transactionele archief.

#### <a name="schema-constraints"></a>Schema beperkingen

De volgende beperkingen zijn van toepassing op de operationele gegevens in Azure Cosmos DB wanneer u de analyse opslag inschakelt om het schema op de juiste wijze automatisch af te leiden en te representeren:

* U kunt Maxi maal 1000 eigenschappen hebben voor elk nest niveau in het schema en een maximale geneste diepte van 127.
  * Alleen de eerste 1000 eigenschappen worden weer gegeven in de analytische opslag.
  * Alleen de eerste 127 geneste niveaus worden weer gegeven in de analytische opslag.

* Hoewel JSON-documenten (en Cosmos DB verzamelingen/containers) hoofdletter gevoelig zijn vanuit het oogpunt van uniekheid, is de analytische opslag niet.

  * **In hetzelfde document:** Namen van eigenschappen op hetzelfde niveau moeten uniek zijn wanneer het hoofdletter gevoelig is. Het volgende JSON-document heeft bijvoorbeeld ' naam ' en ' naam ' op hetzelfde niveau. Hoewel het een geldig JSON-document is, voldoet het niet aan de beperking van uniekheid en wordt het daarom niet volledig weer gegeven in de analytische opslag. In dit voor beeld zijn ' naam ' en ' naam ' hetzelfde wanneer deze in een niet-hoofdletter gevoelige manier worden vergeleken. `"Name": "fred"`Wordt alleen weer gegeven in de analytische opslag, omdat dit de eerste instantie is. En `"name": "john"` worden helemaal niet weer gegeven.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **In verschillende documenten:** Eigenschappen op hetzelfde niveau en met dezelfde naam, maar in verschillende gevallen, worden weer gegeven in dezelfde kolom, met behulp van de naam notatie van het eerste exemplaar. De volgende JSON-documenten hebben bijvoorbeeld `"Name"` en `"name"` op hetzelfde niveau. Omdat de eerste document indeling is `"Name"` , is dit wat wordt gebruikt om de naam van de eigenschap in de analytische opslag weer te geven. Met andere woorden, de naam van de kolom in de analytische opslag is `"Name"` . Beide `"fred"` en worden `"john"` weer gegeven in de `"Name"` kolom.


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* Het eerste document van de verzameling definieert het initiële analytische archief-schema.
  * Eigenschappen in het eerste niveau van het document worden als kolommen weer gegeven.
  * Documenten met meer eigenschappen dan het oorspronkelijke schema genereren nieuwe kolommen in de analytische opslag.
  * Kolommen kunnen niet worden verwijderd.
  * Bij het verwijderen van alle documenten in een verzameling wordt het analytische archief schema niet opnieuw ingesteld.
  * Er is geen schema versie beheer. De laatste versie die is afgeleid van transactionele Store, is wat u ziet in de analytische opslag.

* Op dit moment bieden we geen ondersteuning meer voor Azure Synapse Spark-Lees kolom namen die lege waarden bevatten (spaties).

* Voor expliciete waarden zou u een ander gedrag verwachten `null` :
  * In Spark-Pools in azure Synapse worden deze waarden gelezen als `0` (nul).
  * Met SQL serverloze Pools in azure Synapse worden deze waarden gelezen alsof `NULL` het eerste document van de verzameling voor dezelfde eigenschap een waarde met een `non-numeric` gegevens type heeft.
  * SQL serverloze Pools in azure Synapse lezen deze waarden als `0` (nul) als het eerste document van de verzameling voor dezelfde eigenschap een waarde heeft met een `numeric` gegevens type.

* Er wordt een ander gedrag in rekening gehouden met ontbrekende kolommen:
  * In Spark-Pools in azure Synapse worden deze kolommen weer gegeven als `undefined` .
  * SQL serverloze Pools in azure Synapse vertegenwoordigen deze kolommen als `NULL` .

#### <a name="schema-representation"></a>Schema representatie

Er zijn twee modi voor schemarepresentatie in de analytische opslag. Deze modi maken een afweging tussen de eenvoud van een kolomweergave, het behandelen van de polymorfe schema's en eenvoud van de query-ervaring:

* Goed gedefinieerde schema representatie
* Schema representatie van volledig beeld kwaliteit

> [!NOTE]
> Voor SQL-api's (core-API-accounts), als analytische opslag is ingeschakeld, is de standaard schema weergave in het analytische archief goed gedefinieerd. Overwegende dat voor Azure Cosmos DB-API voor MongoDB-accounts de standaard schema representatie in de analytische opslag een volledig beeld van een schema weergave is. Als u scenario's hebt waarvoor een andere schema weergave is vereist dan de standaard aanbieding voor elk van deze Api's, neem dan contact op met het [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com) om het in te scha kelen.

**Goed gedefinieerde schema representatie**

Met de goed gedefinieerde schema representatie maakt u een eenvoudige tabellaire weer gave van de schema-neutraal gegevens in het transactionele archief. De goed gedefinieerde schema weergave heeft de volgende overwegingen:

* Een eigenschap heeft altijd hetzelfde type over meerdere items.

  * `{"a":123} {"a": "str"}`Heeft bijvoorbeeld geen goed gedefinieerd schema, omdat dit `"a"` soms een teken reeks is en soms een getal. In dit geval registreert de analytische opslag het gegevens type van `"a"` als het gegevens type van `“a”` in het eerste item in de levens duur van de container. Het document wordt nog steeds opgenomen in de analytische opslag, maar items waarvan het gegevens type `"a"` anders is dan niet.
  
    Deze voor waarde is niet van toepassing op null-eigenschappen. `{"a":123} {"a":null}`Is bijvoorbeeld nog steeds goed gedefinieerd.

* Matrix typen moeten één herhaald type bevatten.

  * `{"a": ["str",12]}`Is bijvoorbeeld geen goed gedefinieerd schema, omdat de matrix een combi natie van integer-en teken reeks typen bevat.

> [!NOTE]
> Als de Azure Cosmos DB Analytical Store de goed gedefinieerde schema weergave volgt en de bovenstaande specificatie wordt geschonden door bepaalde items, worden deze items niet opgenomen in de analytische opslag.

* Er wordt een ander gedrag in rekening gehouden met de verschillende typen in een goed gedefinieerd schema:
  * Spark-Pools in azure Synapse vertegenwoordigen deze waarden als `undefined` .
  * SQL serverloze Pools in azure Synapse vertegenwoordigen deze waarden als `NULL` .


**Schema representatie van volledig beeld kwaliteit**

De weer gave van het schema voor volledig beeld kwaliteit is ontworpen voor het afhandelen van de volledige breedte van polymorfisme-schema's in de neutraal operationele gegevens. In deze schema representatie worden er geen items uit de analytische opslag verwijderd, zelfs niet als de goed gedefinieerde schema beperkingen (die geen gemengde gegevens type velden of matrices voor gemengde gegevens typen zijn) worden geschonden.

Dit wordt bereikt door de blad eigenschappen van de operationele gegevens in de analytische opslag te vertalen met DISTINCT-kolommen op basis van het gegevens type van de waarden in de eigenschap. De namen van de Leaf-eigenschappen worden uitgebreid met gegevens typen als achtervoegsel in het analytische opslag schema, zodat ze query's zonder dubbel zinnigheid kunnen zijn.

Laten we bijvoorbeeld het volgende voorbeeld document in het transactionele archief volgen:

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

De blad eigenschap `streetNo` binnen het geneste object `address` wordt weer gegeven in het analytische-archief schema als kolom `address.object.streetNo.int32` . Het gegevens type wordt toegevoegd als een achtervoegsel aan de kolom. Op deze manier wordt, als een ander document wordt toegevoegd aan de transactionele Store, waar de waarde van de Leaf `streetNo` -eigenschap ' 123 ' is (Let op een teken reeks), wordt het schema van de analytische opslag automatisch gegroeid zonder het type van een eerder geschreven kolom te wijzigen. Een nieuwe kolom die wordt toegevoegd aan de analytische opslag, `address.object.streetNo.string` waar deze waarde van "123" wordt opgeslagen.

**Het gegevens type voor de toewijzing van het achtervoegsel**

Hier volgt een overzicht van alle eigenschaps gegevens typen en de bijbehorende achtervoegsels in het analytische archief:

|Oorspronkelijk gegevens type  |Achtervoegsel  |Voorbeeld  |
|---------|---------|---------|
| Dubbel |  ".float64" |    24,99|
| Matrix | ". matrix" |    ["a", "b"]|
|Binair | '. binary ' |0|
|Booleaans    | ". BOOL"   |Waar|
|Int32  | ". Int32"  |123|
|Int64  | ". int64"  |255486129307|
|Null   | ". null"   | null|
|Tekenreeks|    ". teken reeks" | "ABC"|
|Tijdstempel |    '. time stamp ' |  Tijds tempel (0, 0)|
|DateTime   |". datum"    | ISODate ("2020-08-21T07:43:07.375 Z")|
|ObjectId   |'. objectId '    | ObjectId ("5f3f7b59330ec25c132623a2")|
|Document   |'. object ' |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Rendabele archivering van historische gegevens

Gegevenslaaging verwijst naar de schei ding van gegevens tussen opslag infrastructuren die zijn geoptimaliseerd voor verschillende scenario's. Hierdoor worden de algehele prestaties en kosten effectiviteit van de end-to-end-gegevens stack verbeterd. Met een analytische winkel biedt Azure Cosmos DB nu ondersteuning voor het automatisch trapsgewijs delen van gegevens vanuit het transactionele archief naar een analytisch archief met verschillende gegevens indelingen. Met een analytische opslag die is geoptimaliseerd voor de opslag kosten in vergelijking met de transactionele Store, kunt u veel meer operationele gegevens bewaren voor historische analyse.

Nadat de analytische opslag is ingeschakeld, kunt u, op basis van de behoeften voor het bewaren van gegevens van de transactionele werk belastingen, de eigenschap transactionele Store Time to Live (transactionele TTL) zodanig configureren dat records automatisch uit het transactionele archief worden verwijderd na een bepaalde tijds periode. Op dezelfde manier kunt u met de ' analytische time-out voor analyse (analytische TTL) ' de levens cyclus beheren van de gegevens die in het analytische archief worden bewaard, onafhankelijk van het transactionele archief. Door analytische opslag in te scha kelen en TTL-eigenschappen te configureren, kunt u de Bewaar periode voor gegevens voor de twee winkels naadloos laag maken en definiëren.

### <a name="global-distribution"></a>Wereldwijde distributie

Als u een wereld wijd gedistribueerd Azure Cosmos DB account hebt, is het beschikbaar in alle regio's van dat account nadat u het analytische Archief voor een container hebt ingeschakeld.  Wijzigingen in de operationele gegevens worden globaal gerepliceerd in alle regio's. U kunt analytische query's effectief uitvoeren met de dichtstbijzijnde regionale kopie van uw gegevens in Azure Cosmos DB.

### <a name="security"></a>Beveiliging

Verificatie met het analytische archief is hetzelfde als het transactionele Archief voor een bepaalde data base. U kunt primaire of alleen-lezen sleutels gebruiken voor verificatie. U kunt gebruikmaken van gekoppelde services in Synapse Studio om te voor komen dat de Azure Cosmos DB sleutels in de Spark-notebooks worden geplakt. Toegang tot deze gekoppelde service is beschikbaar voor iedereen die toegang heeft tot de werk ruimte.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Ondersteuning voor meerdere Azure Synapse Analytics-Runtimes

De analytische opslag is geoptimaliseerd voor schaal baarheid, elasticiteit en prestaties voor analytische werk belastingen zonder enige afhankelijkheid op de reken tijden. De opslag technologie wordt zelf beheerd om uw analyse werkbelastingen te optimaliseren zonder hand matige inspanningen.

Als u het analytische opslag systeem loskoppelt van het analytische berekenings systeem, kunnen gegevens in Azure Cosmos DB-analytische opslag tegelijk worden opgevraagd bij de verschillende analyse-runtimes die worden ondersteund door Azure Synapse Analytics. Vanaf nu ondersteunt Azure Synapse Analytics Apache Spark en serverloze SQL-groep met Azure Cosmos DB-analytische opslag.

> [!NOTE]
> U kunt alleen lezen uit de analytische opslag met behulp van Azure Synapse Analytics-uitvoerings tijd. U kunt de gegevens terugschrijven naar uw transactionele archief als een te leveren laag.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Koers

In de analytische opslag wordt een prijs model op basis van verbruik gevolgd waarvoor u in rekening wordt gebracht:

* Opslag: het volume van de gegevens dat elke maand in het analytische archief wordt bewaard, inclusief historische gegevens zoals gedefinieerd door de analyse-TTL.

* Analytische schrijf bewerkingen: de volledig beheerde synchronisatie van operationele gegevens updates naar het analytische archief vanuit het transactionele archief (automatische synchronisatie)

* Analytische Lees bewerkingen: de Lees bewerkingen die worden uitgevoerd voor het analytische archief vanuit een Azure Synapse Analytics Spark-groep en de uitvoerings tijd van de serverloze SQL-pool.

Prijzen voor een analytische opslag zijn gescheiden van het prijs model voor de transactie opslag. Er is geen concept van het ingerichte RUs in de analytische opslag. Zie [Azure Cosmos DB-pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/)voor meer informatie over het prijs model voor de analytische opslag.

Als u een schatting wilt maken van de kosten op hoog niveau om analytische opslag op een Azure Cosmos DB-container in te scha kelen, kunt u de [Azure Cosmos DB capacity planner](https://cosmos.azure.com/capacitycalculator/) gebruiken en een schatting maken van uw analytische opslag-en schrijf bewerkings kosten. Analytische Lees bewerkings kosten zijn afhankelijk van de kenmerken van de analytische werk belasting, maar als een schatting op hoog niveau is het scannen van 1 TB aan gegevens in de analytische opslag meestal het resultaat van 130.000 analytische Lees bewerkingen en de kosten van $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analytische time-to-Live (TTL)

Analytical TTL geeft aan hoe lang gegevens voor een container moeten worden bewaard in uw analytische opslag. 

Als het analytische archief is ingeschakeld, worden invoeg acties, updates en verwijderingen van operationele gegevens automatisch gesynchroniseerd vanuit transactioneel archief naar een analytische archief, onafhankelijk van de transactionele TTL-configuratie. De retentie van deze operationele gegevens in de analytische opslag kan worden bepaald door de analytische TTL-waarde op container niveau, zoals hieronder is aangegeven:

De analyse-TTL voor een container wordt ingesteld met behulp van de `AnalyticalStoreTimeToLiveInSeconds` eigenschap:

* Als de waarde is ingesteld op 0, ontbreekt (of is ingesteld op null): het analytische archief is uitgeschakeld en er worden geen gegevens gerepliceerd uit transactionele Store naar een analytische archief

* Indien aanwezig en de waarde is ingesteld op '-1 ': de analytische opslag behoudt alle historische gegevens, ongeacht de Bewaar periode van de gegevens in het transactionele archief. Met deze instelling geeft u aan dat het analytische archief oneindig bewaren van uw operationele gegevens bevat

* Indien aanwezig en de waarde is ingesteld op een positief getal ' n ': items verlopen vanaf de ' n ' seconden van het analytische archief na de laatste wijziging in het transactionele archief. Deze instelling kan worden gebruikt als u uw operationele gegevens gedurende een beperkte periode wilt bewaren in de analytische opslag, ongeacht de retentie van de gegevens in het transactionele archief

Enkele punten om in overweging te nemen:

*   Nadat de analytische opslag is ingeschakeld met een analytische TTL-waarde, kan deze later worden bijgewerkt naar een andere geldige waarde. 
*   Hoewel transactionele TTL kan worden ingesteld op container-of item niveau, kan analytische TTL alleen op het container niveau worden ingesteld.
*   U kunt uw operationele gegevens in het analytische archief langer bewaren door de analytische TTL >= transactionele TTL op het niveau van de container in te stellen.
*   De analytische opslag kan worden gemaakt om het transactionele archief te spie gelen door analytische TTL = transactionele TTL in te stellen.

Wanneer u het analytische archief op een container inschakelt:

* Van de Azure Portal is de optie analyse-TTL ingesteld op de standaard waarde-1. U kunt deze waarde wijzigen in ' n ' seconden door te navigeren naar container instellingen onder Data Explorer. 
 
* Vanuit de Azure SDK of Power shell of CLI kunt u de optie voor de analytische TTL inschakelen door deze in te stellen op-1 of n. 

Zie [analytische TTL configureren voor een container](configure-synapse-link.md#create-analytical-ttl)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor meer informatie:

* [Azure Synapse Link voor Azure Cosmos DB](synapse-link.md)

* [Aan de slag met Azure Synapse Link voor Azure Cosmos DB](configure-synapse-link.md)

* [Veelgestelde vragen over Synapse Link voor Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Use cases voor Azure Synapse Link voor Azure Cosmos DB](synapse-link-use-cases.md)
