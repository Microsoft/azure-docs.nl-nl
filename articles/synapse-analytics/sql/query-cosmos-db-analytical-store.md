---
title: Query's uitvoeren op Azure Cosmos DB gegevens met behulp van serverloze SQL-groep in azure Synapse-koppeling (preview-versie)
description: In dit artikel leert u hoe u Azure Cosmos DB query's kunt uitvoeren met behulp van een serverloze SQL-groep in azure Synapse link (preview).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 2ffc524c14b9ba281d7e386f7f8c726093f11dbf
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94661015"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Query's uitvoeren op Azure Cosmos DB gegevens met serverloze SQL-groep in azure Synapse-koppeling (preview-versie)

Met een Synapse serverloze SQL-pool kunt u gegevens in uw Azure Cosmos DB containers [die in bijna](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) realtime zijn ingeschakeld, analyseren zonder dat dit van invloed is op de prestaties van uw transactionele werk belastingen. Het biedt een bekende T-SQL-syntaxis voor het opvragen van gegevens uit de [analytische opslag](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) en de geïntegreerde connectiviteit met een breed scala aan bi-en ad-hoc hulp middelen voor query's via de T-SQL-interface.

Voor het uitvoeren van query's in Azure Cosmos DB wordt de volledige [selectie](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) Surface Area ondersteund via de [OpenRowSet](develop-openrowset.md) -functie, inclusief het meren deel van [SQL-functies en-Opera tors](overview-features.md). U kunt ook resultaten van de query opslaan die gegevens uit Azure Cosmos DB leest, samen met gegevens in Azure Blob Storage of Azure Data Lake Storage met de [optie externe tabel maken als selecteren](develop-tables-cetas.md#cetas-in-serverless-sql-pool). U kunt momenteel geen serverloze SQL-groeps query resultaten opslaan in Azure Cosmos DB met behulp van CETAS. 

In dit artikel leert u hoe u een query kunt schrijven met een serverloze SQL-groep waarmee gegevens worden opgevraagd van Azure Cosmos DB containers die Synapse-koppeling zijn ingeschakeld. In [deze](./tutorial-data-analyst.md) zelf studie vindt u meer informatie over het bouwen van SERVERloze SQL-pool weergaven over Azure Cosmos DB containers en het verbinden ervan met Power bi modellen. 

> [!IMPORTANT]
> In deze zelf studie wordt een container met een [Azure Cosmos DB goed gedefinieerd schema](../../cosmos-db/analytical-store-introduction.md#schema-representation)gebruikt. De query-ervaring dat de serverloze SQL-pool voorziet in een [Azure Cosmos DB volledig betrouw bare schema](#full-fidelity-schema) is tijdelijk gedrag dat wordt gewijzigd op basis van de feedback van de preview-versie. Vertrouw niet op het schema van de resultatenset van de `OPENROWSET` functie zonder de- `WITH` component waarmee gegevens worden gelezen uit een container met een schema met volledige betrouw baarheid, omdat de query-ervaring mogelijk kan worden uitgelijnd en wordt gewijzigd op basis van het goed gedefinieerde schema. Post uw feedback in het [Feedback forum van Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics) of neem contact op met het [product team](mailto:cosmosdbsynapselink@microsoft.com) van de Synapse-koppeling om feedback te geven.

## <a name="overview"></a>Overzicht

Voor het ondersteunen van het uitvoeren van query's en het analyseren van gegevens in Azure Cosmos DB Analytical Store gebruikt de serverloze SQL-pool de volgende `OPENROWSET` syntaxis:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Met de Azure Cosmos DB connection string geeft u de Azure Cosmos DB account naam, de database naam, de hoofd sleutel van het database account en een optionele regio naam te `OPENROWSET` gebruiken. 

> [!IMPORTANT]
> Zorg ervoor dat u een UTF-8-database sortering (bijvoorbeeld) gebruikt, `Latin1_General_100_CI_AS_SC_UTF8` omdat de teken reeks waarden in Cosmos DB analytische archief worden gecodeerd als UTF-8-tekst.
> De tekst codering in het bestand en de sortering kunnen onverwachte tekst conversie fouten veroorzaken.
> U kunt eenvoudig de standaard sortering van de huidige data base wijzigen met behulp van de volgende T-SQL-instructie: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

De connection string heeft de volgende indeling:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

De naam van de Azure Cosmos DB container is opgegeven zonder aanhalings tekens in de `OPENROWSET` syntaxis. Als de naam van de container speciale tekens bevat (bijvoorbeeld een streepje '-'), moet de naam tussen `[]` vier Kante haken worden geplaatst in de `OPENROWSET` syntaxis.

> [!NOTE]
> Een serverloze SQL-groep biedt geen ondersteuning voor het uitvoeren van query's Azure Cosmos DB transactionele opslag.

## <a name="sample-data-set"></a>Set voorbeeldgegevens

De voor beelden in dit artikel zijn gebaseerd op gegevens van het [Euro pees centrum voor ziekte preventie en-controle (ECDC) COVID-19 gevallen](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) en [COVID-19 open Research DataSet (koord-19), DOI: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

U kunt de licentie en de structuur van de gegevens op deze pagina's bekijken en voorbeeld gegevens downloaden voor [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) -en [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) -gegevens sets.

Volg de instructies in dit artikel voor informatie over het opvragen van Cosmos DB gegevens met een serverloze SQL-groep, zorg ervoor dat u de volgende resources maakt:
* Een Azure Cosmos DB database account dat [Synapse-koppeling is ingeschakeld](../../cosmos-db/configure-synapse-link.md)
* Een Azure Cosmos DB-Data Base met de naam `covid`
* Twee Azure Cosmos DB containers met de naam `EcdcCases` en `Cord19` met de bovenstaande voorbeeld gegevens sets geladen.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Azure Cosmos DB gegevens verkennen met een automatische schema-deinterferentie

De gemakkelijkste manier om gegevens in Azure Cosmos DB te verkennen, is door gebruik te maken van de automatische functionaliteit voor het afwijzen van schema's. Door de `WITH` component van de instructie te weglaten `OPENROWSET` , kunt u een SERVERloze SQL-groep instrueren om het schema van de analytische opslag van de Azure Cosmos DB-container automatisch te detecteren (af te leiden).

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
In het bovenstaande voor beeld geven we een serverloze SQL-groep om verbinding te maken met de `covid` Data base in azure Cosmos DB account dat is `MyCosmosDbAccount` geverifieerd met behulp van de Azure Cosmos DB sleutel (pop in het bovenstaande voor beeld). Vervolgens opent u de `EcdcCases` analytische opslag van de container in de `West US 2` regio. Omdat er geen projectie van specifieke eigenschappen is, `OPENROWSET` retourneert functie alle eigenschappen van de Azure Cosmos DB items. 

Onder veronderstelling dat de items in Cosmos DB container `date_rep` , `cases` , en `geo_id` Eigenschappen, de resultaten van deze query worden weer gegeven in de volgende tabel:

| date_rep | meldingen | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Als u gegevens uit de andere container in dezelfde Azure Cosmos DB-Data Base wilt verkennen, kunt u dezelfde connection string en de vereiste container referentie gebruiken als derde para meter:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Expliciet schema opgeven

In het geval van automatische schema-deinterferentie in `OPENROWSET` biedt een eenvoudige, gebruiks vriendelijke querience, uw bedrijfs scenario's vereisen mogelijk dat u het schema expliciet moet opgeven voor alleen-lezen relevante eigenschappen van de Azure Cosmos DB gegevens.

`OPENROWSET` Hiermee kunt u expliciet opgeven welke eigenschappen u wilt lezen uit de gegevens in de container en de gegevens typen ervan opgeven. Stel dat we gegevens hebben geïmporteerd uit [ECDC COVID gegevensset](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) met de volgende structuur in azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Deze platte JSON-documenten in Azure Cosmos DB kunnen worden weer gegeven als een set rijen en kolommen in Synapse SQL. `OPENROWSET` met de functie kunt u een subset van eigenschappen opgeven die u wilt lezen en de exacte kolom typen in de `WITH` component:

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Het resultaat van deze query kan er als volgt uitzien:

| date_rep | meldingen | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Raadpleeg de [regels voor SQL-type toewijzingen](#azure-cosmos-db-to-sql-type-mappings) aan het einde van het artikel voor meer informatie over de SQL-typen die moeten worden gebruikt voor Azure Cosmos DB waarde.

## <a name="querying-nested-objects-and-arrays"></a>Query's uitvoeren op geneste objecten en matrices

Met Azure Cosmos DB kunt u complexere gegevens modellen vertegenwoordigen door ze als geneste objecten of matrices samen te stellen. Met de functie voor automatische synchronisatie van de Synapse-koppeling voor Azure Cosmos DB wordt de schema weergave in de analytische out-of-the-box beheerd. Dit omvat het verwerken van geneste gegevens typen voor uitgebreide query's vanuit een serverloze SQL-pool.

De gegevensset van de koord bevat bijvoorbeeld JSON [-](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) documenten die volgen op de volgende structuur:

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

De geneste objecten en matrices in Azure Cosmos DB worden weer gegeven als JSON-teken reeksen in het query resultaat wanneer de `OPENROWSET` functie deze leest. Een optie voor het lezen van de waarden van deze complexe typen, omdat SQL-kolommen gebruikmaken van SQL-JSON-functies:

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

Het resultaat van deze query kan er als volgt uitzien:

| titel | verbergen | first_autor_name |
| --- | --- | --- |
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

Als alternatief kunt u ook de paden naar geneste waarden in de objecten opgeven bij het gebruik van de `WITH` component:

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

Meer informatie over het analyseren van [complexe gegevens typen in Synapse-koppeling](../how-to-analyze-complex-schema.md) en [geneste structuren in een serverloze SQL-groep](query-parquet-nested-types.md).

> [!IMPORTANT]
> Als er onverwachte tekens worden weer geven in uw tekst zoals `MÃƒÂ©lade` in plaats van `Mélade` de database sortering is niet ingesteld op [utf8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) -sortering. 
> [Wijzig de sortering van de data base](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) in een utf8-sortering met behulp van een SQL-instructie zoals `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flattening-nested-arrays"></a>Geneste matrices afvlakken

Azure Cosmos DB gegevens kunnen geneste submatrixen hebben, zoals de matrix van de auteur uit de [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) -gegevensset:

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

In sommige gevallen moet u mogelijk de eigenschappen van het bovenste item (meta gegevens) toevoegen aan alle elementen van de matrix (auteurs). Met serverloze SQL-pool kunt u geneste structuren plat maken door de functie toe te passen `OPENJSON` op de geneste matrix:

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

Het resultaat van deze query kan er als volgt uitzien:

| titel | verbergen | instantie | duren | betrokkenheid |
| --- | --- | --- | --- | --- |
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Aanvullende informatie een eco-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 3 # |`{"laboratory":"","institution":"U…` | 
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Aanvullende informatie een eco-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Als er onverwachte tekens worden weer geven in uw tekst zoals `MÃƒÂ©lade` in plaats van `Mélade` de database sortering is niet ingesteld op [utf8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) -sortering. 
> [Wijzig de sortering van de data base](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) in een utf8-sortering met behulp van een SQL-instructie zoals `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Toewijzingen van het SQL-type Azure Cosmos DB

Het is belang rijk om eerst te weten dat hoewel Azure Cosmos DB transactionele Store schema-neutraal is, de analytische opslag geschematiseerde kan worden geoptimaliseerd voor de prestaties van analytische query's. Met de functie voor automatische synchronisatie van de Synapse-koppeling beheert Azure Cosmos DB de schema weergave in de analytische out-of-the-box. Dit omvat het verwerken van geneste gegevens typen. Omdat serverloze SQL-pool de analytische opslag opvraagt, is het belang rijk om te begrijpen hoe Azure Cosmos DB invoer gegevens typen worden toegewezen aan SQL-gegevens typen.

Azure Cosmos DB-accounts van de SQL-API (core) ondersteunen JSON-eigenschaps typen Number, String, Boolean, null, genest object of matrix. U moet SQL-typen kiezen die overeenkomen met deze JSON-typen als u `WITH` component gebruikt in `OPENROWSET` . Zie onder de SQL-kolom typen die moeten worden gebruikt voor verschillende eigenschaps typen in Azure Cosmos DB.

| Azure Cosmos DB eigenschaps type | SQL-kolom Type |
| --- | --- |
| Boolean-waarde | bit |
| Geheel getal | bigint |
| Decimaal | float |
| Tekenreeks | varchar (UTF8-database sortering) |
| Datum/tijd (ISO-indelings teken reeks) | varchar (30) |
| Datum en tijd (UNIX-tijds tempel) | bigint |
| Null | `any SQL type` 
| Genest object of matrix | varchar (max) (UTF8-database sortering), geserialiseerd als JSON-tekst |

## <a name="full-fidelity-schema"></a>Schema voor volledige beeld kwaliteit

Azure Cosmos DB volledige-coderings schema worden zowel waarden als het beste overeenkomende type voor elke eigenschap in een container vastgelegd.
`OPENROWSET` de functie voor een container met een volledig betrouwbaar schema bevat zowel het type als de werkelijke waarde in elke cel. Stel dat met de volgende query de items worden gelezen uit een container met een schema met volledige kwaliteit:

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

Voor elke waarde ziet u het type dat in Cosmos DB container item is geïdentificeerd. De meeste waarden voor de `date_rep` eigenschap bevatten `date` waarden, maar sommige worden onjuist opgeslagen als teken reeksen in Cosmos db. Met het schema full fidelity worden zowel waarden die juist zijn getypt als `date` onjuiste opgemaakte `string` waarden geretourneerd.
Aantal cases is een informatie die is opgeslagen als `int32` waarde, maar er is één waarde die wordt ingevoerd als decimaal getal. Deze waarde is van het `float64` type. Als er waarden zijn die groter zijn dan het hoogste `int32` aantal, worden deze opgeslagen als `int64` type. Alle `geo_id` waarden in dit voor beeld worden opgeslagen als `string` typen.

> [!IMPORTANT]
> `OPENROWSET` de functie zonder `WITH` component geeft beide waarden weer met verwachte typen en de waarden met onjuist ingevoerde typen. Deze func is ontworpen voor het verkennen van gegevens en niet voor rapportage. Parse-waarden die zijn geretourneerd door deze functie niet parseren om rapporten te maken en een expliciete [with-component](#querying-items-with-full-fidelity-schema) te gebruiken om uw rapporten te creëren.
> U moet de waarden opschonen die onjuiste typen hebben in Azure Cosmos DB-container om een corect te kunnen Toep assen in een analytisch archief met volledige kwaliteit. 

Voor het uitvoeren van query's op Azure Cosmos DB accounts van de Mongo DB-API-soort, kunt u meer te weten komen over de schema weergave voor volledige beeld kwaliteit in het analytische archief en de uitgebreide eigenschapnamen die [hier](../../cosmos-db/analytical-store-introduction.md#analytical-schema)worden gebruikt.

### <a name="querying-items-with-full-fidelity-schema"></a>Query's uitvoeren op items met een volledig beeld schema

Tijdens het uitvoeren van een query op een volledig Fidelity-schema moet u in de component expliciet het SQL-type opgeven en het eigenschaps type Cosmos DB verwacht `WITH` . Gebruik niet `OPENROWSET` zonder- `WITH` component in de rapporten omdat de indeling van de resultatenset kan worden gewijzigd in preview op basis van feedback.

In het volgende voor beeld wordt ervan uitgegaan dat het `string` juiste type is voor `geo_id` eigenschap en `int32` juist type voor `cases` eigenschap:

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

Als u waarden hebt met andere typen ( `cases.int64` , `cases.float64` ) die niet kunnen worden gereinigd in Cosmos DB container, moet u deze expliciet verwijzen in `WITH` de-component en de resultaten combi neren. Met de volgende query worden beide `int32` , `int64` en `float64` opgeslagen in de `cases` kolom geaggregeerd:

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

In dit voor beeld wordt het aantal cases opgeslagen als `int32` , `int64` , of `float64` waarden en alle waarden moeten worden geëxtraheerd om het aantal cases per land te berekenen. 

## <a name="known-issues"></a>Bekende problemen

- De query-ervaring die serverloze SQL-pool biedt voor [Azure Cosmos DB volledig beeld schema](#full-fidelity-schema) is tijdelijk gedrag dat wordt gewijzigd op basis van de preview-feedback. Vertrouw niet op het schema dat `OPENROWSET` Function zonder `WITH` component bevat tijdens de open bare preview, omdat de query-ervaring mogelijk kan worden uitgelijnd met een goed gedefinieerd schema op basis van feedback van klanten. Neem contact op met het [product team van Synapse link](mailto:cosmosdbsynapselink@microsoft.com) om feedback te geven.
- Een serverloze SQL-groep retourneert geen compilatie fout als de `OPENROSET` kolom sortering geen UTF-8-code ring heeft. U kunt eenvoudig de standaard sortering wijzigen voor alle `OPENROWSET` functies die in de huidige Data Base worden uitgevoerd, met behulp van de volgende T-SQL-instructie: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

In de volgende tabel worden mogelijke fouten en acties voor het oplossen van problemen weer gegeven:

| Fout | Hoofdoorzaak |
| --- | --- |
| Syntaxis fouten:<br/> -Onjuiste syntaxis bij OPENROWSET<br/> - `...` is geen herkende optie voor BULKSGEWIJZE OPENROWSET-provider.<br/> -Onjuiste syntaxis bij `...` | Mogelijke hoofd oorzaken<br/> -Niet ' CosmosDB ' gebruiken als eerste para meter,<br/> -Letterlijke teken reeks gebruiken in plaats van id in derde para meter,<br/> -Niet opgeven derde para meter (container naam) |
| Er is een fout opgetreden in de CosmosDB-connection string | -Account, Data Base, sleutel is niet opgegeven <br/> -Er is een optie in connection string die niet wordt herkend.<br/> -Punt komma `;` wordt toegevoegd aan het einde van Connection String |
| Het omzetten van het CosmosDB-pad is mislukt met de fout ' onjuiste account naam ' of ' onjuiste database naam ' | De opgegeven account naam, database naam of container kan niet worden gevonden of de analyse opslag heeft geen o de opgegeven verzameling ingeschakeld|
| Het omzetten van het CosmosDB-pad is mislukt met de fout ' onjuiste geheime waarde ' of ' geheim is null of leeg ' | De account sleutel is ongeldig of ontbreekt. |
| Kolom `column name` van het type `type name` is niet compatibel met het externe gegevens type `type name` | Het opgegeven kolom Type in de `WITH` component komt niet overeen met het type in Cosmos DB container. Probeer het kolom type te wijzigen, omdat dit wordt beschreven in de sectie [Azure Cosmos DB aan de SQL-type toewijzingen](#azure-cosmos-db-to-sql-type-mappings) of het `VARCHAR` type gebruik. |
| Kolom bevat `NULL` waarden in alle cellen. | Mogelijk verkeerde kolom naam of pad-expressie in `WITH` component. De kolom naam (of padexpressie na de kolom Type) in- `WITH` component moet overeenkomen met een eigenschaps naam in Cosmos DB verzameling. Vergelijking is **hoofdletter gevoelig**  (bijvoorbeeld `productCode` en `ProductCode` andere eigenschappen). |

U kunt suggesties en problemen melden op de [pagina met feedback over Azure Synapse](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen:

- [Power BI en serverloze SQL-groep met Azure Synapse-koppeling gebruiken](../../cosmos-db/synapse-link-power-bi.md)
- [Weer gaven maken en gebruiken in een serverloze SQL-groep](create-use-views.md) 
- [Zelf studie over het bouwen van serverloze SQL-pool weergaven over Azure Cosmos DB en verbinding maken met Power BI modellen via DirectQuery](./tutorial-data-analyst.md)
