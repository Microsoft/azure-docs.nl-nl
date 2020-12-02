---
title: Query's uitvoeren op Azure Cosmos DB gegevens met behulp van een serverloze SQL-groep in azure Synapse link preview
description: In dit artikel leert u hoe u een query kunt uitvoeren op Azure Cosmos DB met behulp van een serverloze SQL-groep in azure Synapse link preview.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 439337233e24dfcae2c8c911a9224fd3394d6846
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462699"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link-preview"></a>Azure Cosmos DB gegevens opvragen met een serverloze SQL-groep in azure Synapse link preview

> [!IMPORTANT]
> Er is momenteel een preview-versie van serverloze SQL-groeps ondersteuning voor de Azure Synapse-koppeling voor de Azure Cosmos DB. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Met een serverloze SQL-pool kunt u gegevens in uw Azure Cosmos DB containers die in bijna realtime zijn ingeschakeld, analyseren zonder dat dit van invloed [is op de](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) prestaties van uw transactionele werk belastingen. Het biedt een bekende T-SQL-syntaxis voor het opvragen van gegevens uit de [analytische opslag](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) en de geïntegreerde connectiviteit met een breed scala aan Business Intelligence (BI) en ad-hoc query Programma's via de T-SQL-interface.

Voor het uitvoeren van query's in Azure Cosmos DB wordt de volledige [selectie](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) Surface Area ondersteund door de functie [OpenRowSet](develop-openrowset.md) , die het meren deel van [SQL-functies en-Opera tors](overview-features.md)bevat. U kunt ook resultaten van de query opslaan die gegevens uit Azure Cosmos DB leest, samen met gegevens in Azure Blob Storage of Azure Data Lake Storage met behulp van de [optie externe tabel maken als Select](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS). U kunt momenteel geen serverloze query resultaten van SQL-groep in Azure Cosmos DB opslaan met behulp van CETAS.

In dit artikel leert u hoe u een query schrijft met een serverloze SQL-groep die gegevens opvraagt uit Azure Cosmos DB containers die zijn ingeschakeld met de koppeling Azure Synapse. In [deze zelf studie](./tutorial-data-analyst.md)vindt u meer informatie over het bouwen van SERVERloze SQL-pool weergaven over Azure Cosmos DB containers en het verbinden ervan met Power bi modellen.

> [!IMPORTANT]
> In deze zelf studie wordt een container met een [Azure Cosmos DB goed gedefinieerd schema](../../cosmos-db/analytical-store-introduction.md#schema-representation)gebruikt. De query-ervaring dat de serverloze SQL-pool voorziet in een [Azure Cosmos DB volledig betrouw bare schema](#full-fidelity-schema) is tijdelijk gedrag dat wordt gewijzigd op basis van de feedback van de preview-versie. Vertrouw niet op het schema van de resultatenset van de `OPENROWSET` functie zonder de- `WITH` component waarmee gegevens worden gelezen uit een container met een schema met volledige betrouw baarheid, omdat de query-ervaring mogelijk kan worden uitgelijnd en wordt gewijzigd op basis van het goed gedefinieerde schema. U kunt uw feedback plaatsen in het [Feedback forum van Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics). U kunt ook contact opnemen met het [product team van de Azure Synapse-koppeling](mailto:cosmosdbsynapselink@microsoft.com) om feedback te geven.

## <a name="overview"></a>Overzicht

Voor het ondersteunen van query's en het analyseren van gegevens in een Azure Cosmos DB-analytische opslag, gebruikt een serverloze SQL-pool de volgende `OPENROWSET` syntaxis:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

De Azure Cosmos DB connection string geeft de Azure Cosmos DB account naam, de database naam, de hoofd sleutel voor het database account en een optionele regio naam aan de `OPENROWSET` functie op.

> [!IMPORTANT]
> Zorg ervoor dat u een UTF-8-database sortering gebruikt, bijvoorbeeld `Latin1_General_100_CI_AS_SC_UTF8` omdat de teken reeks waarden in een Azure Cosmos DB analytische archief worden gecodeerd als UTF-8-tekst.
> Het verschil tussen tekst codering in het bestand en de sortering kan leiden tot onverwachte tekst conversie fouten.
> U kunt eenvoudig de standaard sortering van de huidige data base wijzigen met behulp van de T-SQL-instructie `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

De connection string heeft de volgende indeling:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

De naam van de Azure Cosmos DB container is opgegeven zonder aanhalings tekens in de `OPENROWSET` syntaxis. Als de naam van de container speciale tekens bevat, bijvoorbeeld een streepje (-), moet de naam tussen vier Kante haken ( `[]` ) in de syntaxis worden geplaatst `OPENROWSET` .

> [!NOTE]
> Een serverloze SQL-groep biedt geen ondersteuning voor het uitvoeren van query's op een Azure Cosmos DB transactionele Store.

## <a name="sample-dataset"></a>Voor beeld-gegevensset

De voor beelden in dit artikel zijn gebaseerd op gegevens van het [Euro pees centrum voor ziekte preventie en controle (ECDC) COVID-19 gevallen](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) en [COVID-19 open Research gegevensset (kabel-19), DOI: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

U kunt de licentie en de structuur van de gegevens op deze pagina's bekijken. U kunt ook voorbeeld gegevens downloaden voor de gegevens sets [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) en [koord-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Volg de instructies in dit artikel voor informatie over het opvragen van Azure Cosmos DB gegevens met een serverloze SQL-groep, zorg ervoor dat u de volgende resources maakt:

* Een Azure Cosmos DB database account dat de [koppeling van Azure Synapse is ingeschakeld](../../cosmos-db/configure-synapse-link.md).
* Een Azure Cosmos DB-Data Base met de naam `covid` .
* Twee Azure Cosmos DB containers `EcdcCases` `Cord19` met de naam en geladen met de voor gaande voor beelden van gegevens sets.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Azure Cosmos DB gegevens verkennen met een automatische schema-deinterferentie

De gemakkelijkste manier om gegevens in Azure Cosmos DB te verkennen, is door gebruik te maken van de functie voor automatische schema-deinterferentie. Door de `WITH` component van de instructie te weglaten `OPENROWSET` , kunt u het schema van de analytische opslag van de Azure Cosmos DB-container door geven aan de serverloze SQL-groep.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
In het vorige voor beeld hebben we de serverloze SQL-groep geïnstrueerd om verbinding te maken met de `covid` Data base in het Azure Cosmos DB account dat is `MyCosmosDbAccount` geverifieerd met behulp van de Azure Cosmos DB sleutel (de pop in het vorige voor beeld). Vervolgens hebben we het `EcdcCases` analytische archief van de container in de `West US 2` regio geopend. Omdat er geen projectie van specifieke eigenschappen is, retourneert de `OPENROWSET` functie alle eigenschappen uit de Azure Cosmos DB items.

Ervan uitgaande dat de items in de Azure Cosmos DB container `date_rep` , `cases` , en `geo_id` Eigenschappen, de resultaten van deze query worden weer gegeven in de volgende tabel:

| date_rep | meldingen | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Als u gegevens uit de andere container in dezelfde Azure Cosmos DB-Data Base wilt verkennen, kunt u dezelfde connection string gebruiken en naar de vereiste container verwijzen als de derde para meter:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Expliciet schema opgeven

In het geval van automatische schema-deinterferentie in `OPENROWSET` biedt een eenvoudige, gebruiks vriendelijke querience, uw bedrijfs scenario's vereisen mogelijk dat u het schema expliciet moet opgeven voor alleen-lezen relevante eigenschappen van de Azure Cosmos DB gegevens.

Met de `OPENROWSET` functie kunt u expliciet opgeven welke eigenschappen u wilt lezen uit de gegevens in de container en de bijbehorende gegevens typen opgeven.

Stel dat we een aantal gegevens van de [ECDC COVID-gegevensset](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) met de volgende structuur in azure Cosmos DB hebben geïmporteerd:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Deze platte JSON-documenten in Azure Cosmos DB kunnen worden weer gegeven als een set rijen en kolommen in Synapse SQL. `OPENROWSET`Met de functie kunt u een subset van eigenschappen opgeven die u wilt lezen en de exacte kolom typen in de `WITH` component:

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Het resultaat van deze query kan eruitzien als in de volgende tabel:

| date_rep | meldingen | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Zie de [regels voor SQL-type toewijzingen](#azure-cosmos-db-to-sql-type-mappings) aan het einde van het artikel voor meer informatie over de SQL-typen die moeten worden gebruikt voor Azure Cosmos DB waarden.

## <a name="query-nested-objects-and-arrays"></a>Geneste objecten en matrices doorzoeken

Met Azure Cosmos DB kunt u complexere gegevens modellen vertegenwoordigen door ze als geneste objecten of matrices samen te stellen. De functie voor automatische synchronisatie van de Azure Synapse-koppeling voor Azure Cosmos DB beheert de schema representatie in de analytische opslag uit het vak, waaronder het verwerken van geneste gegevens typen waarmee uitgebreide query's kunnen worden uitgevoerd vanuit de serverloze SQL-groep.

De gegevensset van de [kabel-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) bevat bijvoorbeeld JSON-documenten die de volgende structuur volgen:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

De geneste objecten en matrices in Azure Cosmos DB worden weer gegeven als JSON-teken reeksen in het query resultaat wanneer de `OPENROWSET` functie deze leest. Een optie voor het lezen van de waarden van deze complexe typen als SQL-kolommen is het gebruik van SQL JSON-functies:

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

Het resultaat van deze query kan eruitzien als in de volgende tabel:

| title | verbergen | first_autor_name |
| --- | --- | --- |
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

Als alternatief kunt u ook de paden naar geneste waarden in de objecten opgeven wanneer u de- `WITH` component gebruikt:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

Meer informatie over het analyseren van [complexe gegevens typen in azure Synapse-koppeling](../how-to-analyze-complex-schema.md) en [geneste structuren in een serverloze SQL-pool](query-parquet-nested-types.md).

> [!IMPORTANT]
> Als er onverwachte tekens in uw tekst worden weer geven `MÃƒÂ©lade` , zoals in plaats van `Mélade` , is de sortering van de data base niet ingesteld op [UTF-8-](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) sortering.
> [Sortering van de data base wijzigen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) naar UTF-8-sortering met behulp van een SQL-instructie zoals `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flatten-nested-arrays"></a>Geneste matrices samen voegen

Azure Cosmos DB gegevens kunnen geneste submatrixen hebben, zoals de matrix van de auteur van een [koord-19-](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) gegevensset:

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

In sommige gevallen moet u mogelijk de eigenschappen van het bovenste item (meta gegevens) toevoegen aan alle elementen van de matrix (auteurs). Met een serverloze SQL-pool kunt u geneste structuren plat maken door de functie toe te passen `OPENJSON` op de geneste matrix:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Het resultaat van deze query kan eruitzien als in de volgende tabel:

| title | verbergen | instantie | duren | betrokkenheid |
| --- | --- | --- | --- | --- |
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Aanvullende informatie een eco-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 3 # |`{"laboratory":"","institution":"U…` | 
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Als er onverwachte tekens in uw tekst worden weer geven `MÃƒÂ©lade` , zoals in plaats van `Mélade` , is de sortering van de data base niet ingesteld op [UTF-8-](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) sortering. [Sortering van de data base wijzigen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) naar UTF-8-sortering met behulp van een SQL-instructie zoals `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Toewijzingen van het SQL-type Azure Cosmos DB

Hoewel Azure Cosmos DB transactionele Store schema-neutraal is, is het analytische archief geschematiseerde voor het optimaliseren van analytische query prestaties. Met de functie voor automatische synchronisatie van Azure Synapse-koppeling beheert Azure Cosmos DB de schema representatie in de analytische opslag uit het vak, waaronder het verwerken van geneste gegevens typen. Omdat een SQL-groep zonder server een query voor de analytische opslag heeft uitgevoerd, is het belang rijk om te begrijpen hoe Azure Cosmos DB invoer gegevens typen worden toegewezen aan SQL-gegevens typen.

Azure Cosmos DB-accounts van de SQL-API (core) ondersteunen JSON-eigenschaps typen Number, String, Boolean, null, genest object of matrix. U moet SQL-typen kiezen die overeenkomen met deze JSON-typen als u de `WITH` component gebruikt in `OPENROWSET` . De volgende tabel bevat de SQL-kolom typen die moeten worden gebruikt voor verschillende eigenschaps typen in Azure Cosmos DB.

| Azure Cosmos DB eigenschaps type | SQL-kolom Type |
| --- | --- |
| Boolean | bit |
| Geheel getal | bigint |
| Decimaal | float |
| Tekenreeks | varchar (UTF-8-database sortering) |
| Datum/tijd (ISO-indelings teken reeks) | varchar (30) |
| Datum en tijd (UNIX-tijds tempel) | bigint |
| Null | `any SQL type` 
| Genest object of matrix | varchar (max) (UTF-8-database sortering), geserialiseerd als JSON-tekst |

## <a name="full-fidelity-schema"></a>Schema voor volledige beeld kwaliteit

Azure Cosmos DB volledige-coderings schema worden zowel waarden als het beste overeenkomende type voor elke eigenschap in een container vastgelegd. De `OPENROWSET` functie in een container met een volledig coderings schema bevat zowel het type als de werkelijke waarde in elke cel. Stel dat met de volgende query de items worden gelezen uit een container met een schema met volledige kwaliteit:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) as rows
```

Het resultaat van deze query retourneert typen en waarden die zijn opgemaakt als JSON-tekst:

| date_rep | meldingen | geo_id |
| --- | --- | --- |
| {"datum": "2020-08-13"} | {"Int32": "254"} | {"teken reeks": "RS"} |
| {"datum": "2020-08-12"} | {"Int32": "235"}| {"teken reeks": "RS"} |
| {"datum": "2020-08-11"} | {"Int32": "316"} | {"teken reeks": "RS"} |
| {"datum": "2020-08-10"} | {"Int32": "281"} | {"teken reeks": "RS"} |
| {"datum": "2020-08-09"} | {"Int32": "295"} | {"teken reeks": "RS"} |
| {"teken reeks": "2020/08/08"} | {"Int32": "312"} | {"teken reeks": "RS"} |
| {"datum": "2020-08-07"} | {"float64":"339.0"} | {"teken reeks": "RS"} |

Voor elke waarde ziet u het type dat in een Azure Cosmos DB container item is geïdentificeerd. De meeste waarden voor de `date_rep` eigenschap bevatten `date` waarden, maar sommige worden onjuist opgeslagen als teken reeksen in azure Cosmos db. Met het schema full fidelity worden zowel waarden die juist zijn getypt als `date` onjuiste opgemaakte `string` waarden geretourneerd.
Het aantal cases is gegevens opgeslagen als een `int32` waarde, maar er is één waarde die wordt ingevoerd als een decimaal getal. Deze waarde is van het `float64` type. Als er waarden zijn die groter zijn dan het hoogste `int32` aantal, worden deze opgeslagen als het `int64` type. Alle `geo_id` waarden in dit voor beeld worden opgeslagen als `string` typen.

> [!IMPORTANT]
> `OPENROWSET`Met de functie zonder `WITH` component worden beide waarden met verwachte typen en de waarden met onjuist ingevoerde typen weer gegeven. Deze functie is ontworpen voor het verkennen van gegevens en niet voor rapportage. JSON-waarden die zijn geretourneerd door deze functie niet parseren om rapporten te maken. Gebruik een expliciete [with-component](#query-items-with-full-fidelity-schema) om uw rapporten te maken. U moet de waarden die onjuiste typen hebben in de Azure Cosmos DB-container opschonen om correcties toe te passen in de analytische volledige betrouw baarheid.

Als u Azure Cosmos DB accounts van de Mongo DB-API-soort wilt opvragen, kunt u meer te weten komen over de schema weergave van het volledige beeld in de analytische opslag en de uitgebreide eigenschapnamen die moeten worden gebruikt in [Wat is Azure Cosmos DB Analytical Store (preview)?](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="query-items-with-full-fidelity-schema"></a>Een query uitvoeren op items met een volledig beeld schema

Tijdens het uitvoeren van een query op een volledig Fidelity-schema moet u expliciet het SQL-type en het verwachte Azure Cosmos DB eigenschaps type opgeven in de `WITH` component. Gebruik niet `OPENROWSET` zonder een `WITH` component in de rapporten omdat de indeling van de resultatenset kan worden gewijzigd in de preview op basis van feedback.

In het volgende voor beeld wordt ervan uitgegaan dat het `string` juiste type voor de `geo_id` eigenschap is en `int32` het juiste type voor de `cases` eigenschap:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Waarden voor `geo_id` en `cases` die andere typen hebben, worden geretourneerd als `NULL` waarden. Met deze query wordt alleen verwezen naar het `cases` opgegeven type in de expressie ( `cases.int32` ).

Als u waarden hebt met andere typen ( `cases.int64` , `cases.float64` ) die niet kunnen worden opgeschoond in een Azure Cosmos DB container, moet u deze expliciet verwijzen naar een `WITH` component en de resultaten combi neren. Met de volgende query worden beide `int32` , `int64` en `float64` opgeslagen in de `cases` kolom geaggregeerd:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

In dit voor beeld wordt het aantal cases opgeslagen als `int32` , `int64` of `float64` waarden. Alle waarden moeten worden geëxtraheerd om het aantal cases per land te berekenen.

## <a name="known-issues"></a>Bekende problemen

- De query-ervaring die serverloze SQL-pool biedt voor [Azure Cosmos DB volledig beeld schema](#full-fidelity-schema) is tijdelijk gedrag dat wordt gewijzigd op basis van de preview-feedback. Vertrouw niet op het schema dat de `OPENROWSET` functie zonder de- `WITH` component tijdens de open bare preview levert, omdat de query-ervaring mogelijk is uitgelijnd met een goed gedefinieerd schema op basis van feedback van klanten. Als u feedback wilt geven, neemt u contact op met het [Azure Synapse link-product team](mailto:cosmosdbsynapselink@microsoft.com).
- Een serverloze SQL-groep retourneert geen compilatie fout als de `OPENROWSET` kolom sortering geen UTF-8-code ring heeft. U kunt eenvoudig de standaard sortering wijzigen voor alle `OPENROWSET` functies die worden uitgevoerd in de huidige Data Base met behulp van de T-SQL-instructie `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

In de volgende tabel worden mogelijke fouten en acties voor het oplossen van problemen weer gegeven.

| Fout | Hoofdoorzaak |
| --- | --- |
| Syntaxis fouten:<br/> -Onjuiste syntaxis bij OPENROWSET<br/> - `...` is geen herkende optie voor BULKSGEWIJZE OPENROWSET-provider.<br/> -Onjuiste syntaxis bij `...` | Mogelijke hoofd oorzaken:<br/> -Maakt geen gebruik van CosmosDB als de eerste para meter.<br/> -Een letterlijke teken reeks gebruiken in plaats van een id in de derde para meter.<br/> -Niet de derde para meter (container naam) opgeven. |
| Er is een fout opgetreden in de CosmosDB-connection string. | -Het account, de data base of de sleutel is niet opgegeven. <br/> -Er is een optie in een connection string die niet wordt herkend.<br/> -Er `;` wordt aan het einde van een Connection String een punt komma () geplaatst. |
| Het omzetten van het CosmosDB-pad is mislukt met de fout "onjuiste account naam" of "onjuiste database naam". | De opgegeven account naam, database naam of container kan niet worden gevonden, of de analytische opslag is niet ingeschakeld voor de opgegeven verzameling.|
| Het omzetten van het CosmosDB-pad is mislukt met de fout "onjuiste geheime waarde" of "geheim is null of leeg." | De account sleutel is ongeldig of ontbreekt. |
| Kolom `column name` van het type `type name` is niet compatibel met het externe gegevens type `type name` . | Het opgegeven kolom Type in de- `WITH` component komt niet overeen met het type in de Azure Cosmos DB container. Probeer het kolom type te wijzigen, zoals dit wordt beschreven in de sectie [Azure Cosmos DB naar SQL-type toewijzingen](#azure-cosmos-db-to-sql-type-mappings), of gebruik het `VARCHAR` type. |
| Kolom bevat `NULL` waarden in alle cellen. | Mogelijk is er een onjuiste kolom naam of pad-expressie in de `WITH` component. De kolom naam (of het pad-expressie na het kolom Type) in de `WITH` component moet overeenkomen met een eigenschaps naam in de verzameling Azure Cosmos db. Vergelijking is *hoofdletter gevoelig*. `productCode`En `ProductCode` zijn bijvoorbeeld andere eigenschappen. |

U kunt suggesties en problemen melden op de [feedback pagina van Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen:

- [Power BI en serverloze SQL-groep met Azure Synapse-koppeling gebruiken](../../cosmos-db/synapse-link-power-bi.md)
- [Weer gaven maken en gebruiken in een serverloze SQL-groep](create-use-views.md)
- [Zelf studie over het bouwen van serverloze SQL-pool weergaven over Azure Cosmos DB en verbinding maken met Power BI modellen via DirectQuery](./tutorial-data-analyst.md)
