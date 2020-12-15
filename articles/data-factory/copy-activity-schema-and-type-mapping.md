---
title: Schema en gegevens type toewijzing in Kopieer activiteit
description: Meer informatie over de manier waarop Kopieer activiteiten in Azure Data Factory schema's en gegevens typen van bron gegevens naar Sink-gegevens worden toegewezen.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: 2b54ee29b1b03bab5af8410a3fae06438180299d
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507520"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>Schema en gegevens type toewijzing in Kopieer activiteit
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe de Azure Data Factory Kopieer activiteit schema toewijzing en gegevens type toewijzing uit bron gegevens uitvoert om gegevens op te vangen.

## <a name="schema-mapping"></a>Schema toewijzing

### <a name="default-mapping"></a>Standaard toewijzing

De Kopieer activiteit wijst standaard bron gegevens toe aan het **filteren op kolom namen** op hoofdletter gevoelige wijze. Als er geen Sink bestaat, bijvoorbeeld om naar een of meer bestanden te schrijven, worden de namen van de bron velden persistent gemaakt als Sink-namen. Deze standaard toewijzing ondersteunt flexibele schema's en schema drift van bron naar Sink van uitvoering tot uitvoering-alle gegevens die door het brongegevens archief worden geretourneerd, kunnen worden gekopieerd naar sink.

Als uw bron tekst bestand is zonder header regel, is [expliciet toewijzen](#explicit-mapping) vereist omdat de bron geen kolom namen bevat.

### <a name="explicit-mapping"></a>Expliciete toewijzing

U kunt ook een expliciete toewijzing opgeven om de kolom-of veld toewijzing aan te passen van bron naar Sink op basis van uw behoeften. Met expliciete toewijzing kunt u alleen gedeeltelijke bron gegevens kopiëren naar sink, of bron gegevens toewijzen aan Sink met andere namen, of de vorm van tabellarische/hiërarchische gegevens wijzigen. Kopieer activiteit:

1. Hiermee worden de gegevens van de bron gelezen en wordt het bron schema bepaald.
2. Past uw gedefinieerde toewijzing toe.
3. Schrijft de gegevens die moeten worden gefilterd.

Meer informatie over:

- [Gesinkte bron in tabel vorm](#tabular-source-to-tabular-sink)
- [Hiërarchische bron naar tabellarische Sink](#hierarchical-source-to-tabular-sink)
- [Hiërarchische sink in tabel vorm/hiërarchische bron](#tabularhierarchical-source-to-hierarchical-sink)

U kunt de toewijzing configureren voor Data Factory gebruikers interface-> Kopieer activiteit-> toewijzing tabblad of de toewijzing in de eigenschap Kopieer activiteit-> opgeven `translator` . De volgende eigenschappen worden ondersteund in `translator`  ->  `mappings` array->-objecten-> `source` en `sink` , die naar de specifieke kolom/het veld verwijst om gegevens toe te wijzen.

| Eigenschap | Beschrijving                                                  | Vereist |
| -------- | ------------------------------------------------------------ | -------- |
| naam     | De naam van de bron-of sink-kolom/-veld. Toep assen op tabellaire bron en Sink. | Yes      |
| rang telwoord  | Kolom index. Begin vanaf 1. <br>Toep assen en vereist voor het gebruik van gescheiden tekst zonder header-lijn. | No       |
| leertraject     | De expressie JSON-pad voor elk veld dat moet worden uitgepakt of toegewezen. Toep assen op hiërarchische bron en Sink, bijvoorbeeld Cosmos DB, MongoDB of REST-connectors.<br>Voor velden onder het hoofd object begint het JSON-pad met root `$` . voor velden in de door eigenschap gekozen matrix wordt het `collectionReference` JSON-pad gestart vanuit het matrix element zonder `$` . | No       |
| type     | Data Factory tussentijds gegevens type van de kolom Source of sink. Over het algemeen hoeft u deze eigenschap niet op te geven of te wijzigen. Meer informatie over [gegevens type toewijzing](#data-type-mapping). | No       |
| culturele  | Cultuur van de kolom Source of sink. Toep assen wanneer type `Datetime` of is `Datetimeoffset` . De standaardwaarde is `en-us`.<br>Over het algemeen hoeft u deze eigenschap niet op te geven of te wijzigen. Meer informatie over [gegevens type toewijzing](#data-type-mapping). | No       |
| indeling   | Indelings teken reeks die moet worden gebruikt wanneer type `Datetime` of is `Datetimeoffset` . Raadpleeg de [aangepaste datum-en tijd notatie teken reeksen](/dotnet/standard/base-types/custom-date-and-time-format-strings) voor het opmaken van DateTime. Over het algemeen hoeft u deze eigenschap niet op te geven of te wijzigen. Meer informatie over [gegevens type toewijzing](#data-type-mapping). | No       |

De volgende eigenschappen worden ondersteund onder `translator` naast `mappings` :

| Eigenschap            | Beschrijving                                                  | Vereist |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Toep assen bij het kopiëren van gegevens uit een hiërarchische bron, bijvoorbeeld Cosmos DB, MongoDB of REST-connectors.<br>Als u gegevens wilt sequentieel en wilt ophalen uit de objecten **in een matrix veld** met hetzelfde patroon en converteren naar per rij per object, geeft u het JSON-pad van die matrix op om meerdere aanvragen uit te voeren. | No       |

#### <a name="tabular-source-to-tabular-sink"></a>Gesinkte bron in tabel vorm

Als u bijvoorbeeld gegevens uit Sales Force wilt kopiëren naar Azure SQL Database en expliciet drie kolommen wilt toewijzen:

1. Klik op het tabblad Kopieer activiteit-> toewijzing op **schema importeren** om zowel de bron-als Sink-schema's te importeren.

2. Wijs de vereiste velden toe en sluit/verwijder de rest.

![In tabel vorm naar tabel vorm toewijzen](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

Dezelfde toewijzing kan worden geconfigureerd als het volgende in de nettolading van de Kopieer activiteit (Zie `translator` ):

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

Als u gegevens wilt kopiëren uit tekst bestanden met scheidings tekens zonder header, worden de kolommen weer gegeven op rang telwoord in plaats van namen. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>Hiërarchische bron naar tabellarische Sink

Bij het kopiëren van gegevens van een hiërarchische bron naar een tabellaire Sink ondersteunt de Kopieer activiteit de volgende mogelijkheden:

- Gegevens uit objecten en matrices ophalen.
- Meerdere objecten Toep assen met hetzelfde patroon uit een matrix, in welk geval een JSON-object wordt omgezet in meerdere records in een tabel resultaat.

Voor een geavanceerdere trans formatie van hiërarchisch naar tabel vorm kunt u de [gegevens stroom](concepts-data-flow-overview.md)gebruiken. 

Als u bijvoorbeeld een bron MongoDB-document hebt met de volgende inhoud:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

En u wilt het kopiëren naar een tekst bestand in de volgende indeling met de header regel door de gegevens in de matrix *(order_pd en order_price)* samen te voegen en cross samen te voegen met de gemeen schappelijke basis informatie *(getal, datum en plaats)*:

| orderNumber | Order | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

U kunt een dergelijke toewijzing definiëren op Data Factory gebruikers interface voor ontwerpen:

1. Klik op het tabblad Kopieer activiteit-> toewijzing op **schema importeren** om zowel de bron-als Sink-schema's te importeren. Als Data Factory een voor beelden van de bovenste objecten bij het importeren van het schema, als een veld niet wordt weer gegeven, kunt u het toevoegen aan de juiste laag in de hiërarchie door een bestaande veld naam in te stellen en ervoor kiezen om een knoop punt, een object of een matrix toe te voegen.

2. Selecteer de matrix waarvan u de gegevens wilt herhalen en ophalen. Deze wordt automatisch ingevuld als een **verzamelings verwijzing**. Opmerking: er wordt slechts één matrix ondersteund voor deze bewerking.

3. Wijs de vereiste velden toe aan de sink. Data Factory bepaalt automatisch de bijbehorende JSON-paden voor de hiërarchische zijde.

![Hiërarchisch toewijzen aan tabel vorm met behulp van gebruikers interface](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

U kunt ook overschakelen naar **Geavanceerde editor**, in welk geval u de velden van JSON-paden rechtstreeks kunt zien en bewerken. Als u ervoor kiest om nieuwe toewijzing toe te voegen aan deze weer gave, geeft u het JSON-pad op.

![Hiërarchisch toewijzen aan tabel vorm met geavanceerde editor](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

Dezelfde toewijzing kan worden geconfigureerd als het volgende in de nettolading van de Kopieer activiteit (Zie `translator` ):


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>Hiërarchische sink in tabel vorm/hiërarchische bron

De stroom van de gebruikers ervaring is vergelijkbaar met [hiërarchische bron om te sinken in tabel vorm](#hierarchical-source-to-tabular-sink). 

Bij het kopiëren van gegevens uit een tabellaire bron naar een hiërarchische sink, wordt schrijven naar een matrix binnen het object niet ondersteund.

Bij het kopiëren van gegevens van een hiërarchische bron naar een hiërarchische sink kunt u de hiërarchie van de hele laag ook behouden door het object/de matrix te selecteren en te koppelen aan Sink zonder de binnenste velden te raken.

Voor meer geavanceerde trans formatie van gegevens vorm kunt u de [gegevens stroom](concepts-data-flow-overview.md)gebruiken. 

### <a name="parameterize-mapping"></a>Para meters toewijzing

Als u een organisatie-pijp lijn wilt maken om een groot aantal objecten dynamisch te kopiëren, bepaalt u of u de [standaard toewijzing](#default-mapping) kunt gebruiken of dat u een [expliciete toewijzing](#explicit-mapping) voor de desbetreffende objecten moet definiëren.

Als expliciet toewijzen vereist is, kunt u het volgende doen:

1. Definieer een para meter met het object type op het niveau van de pijp lijn, bijvoorbeeld `mapping` .

2. Para meters: op het tabblad Kopieer activiteit-> toewijzing, kies om dynamische inhoud toe te voegen en selecteer de bovenstaande para meter. De nettolading van de activiteit zou er als volgt uitziet:

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. Maak de waarde die moet worden door gegeven aan de toewijzings parameter. Dit moet het volledige object van de `translator` definitie zijn. Raadpleeg de sectie voor beelden in [expliciete toewijzing](#explicit-mapping) . Als de bron tabel bijvoorbeeld is gesinkt, moet de waarde zijn `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` .

## <a name="data-type-mapping"></a>Toewijzing van gegevenstype

De Kopieer activiteit voert bron typen uit naar Sink-type toewijzing met de volgende stroom: 

1. Converteer van eigen bron gegevens typen naar Azure Data Factory tussenliggende gegevens typen.
2. Converteer waar nodig een tussentijds gegevens type, zodat dit overeenkomt met de bijbehorende Sink-typen, die van toepassing zijn op [standaard toewijzingen](#default-mapping) en [expliciete toewijzingen](#explicit-mapping).
3. Converteer van Azure Data Factory tussenliggende gegevens typen naar gesinkte systeem eigen gegevens typen.

De Kopieer activiteit ondersteunt momenteel de volgende tussenliggende gegevens typen: Booleaans, byte, byte matrix, datetime, date time offset, decimaal, double, GUID, Int16, Int32, Int64, SByte, single, String, time span, UInt16, UInt32 en UInt64.

De volgende gegevens type conversies worden ondersteund tussen de tussenliggende typen van bron naar sink.

| Source\Sink | Booleaans | Byte matrix | Decimaal | Datum/tijd <small>(1)</small> | Vlotter punt <small>(2)</small> | GUID | Geheel getal <small>(3)</small> | Tekenreeks | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Booleaans     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Byte matrix  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| Datum/tijd   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimaal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Drijvende komma | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Geheel getal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Tekenreeks      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) datum/tijd bevat DateTime en date time offset.

(2) float-punt bevat enkele en dubbele.

(3) geheel getal omvat SByte, byte, Int16, UInt16, Int32, UInt32, Int64 en UInt64.

> [!NOTE]
> - Gegevens type conversie wordt momenteel ondersteund bij het kopiëren tussen tabellaire gegevens. Hiërarchische bronnen/sinks worden niet ondersteund. Dit betekent dat er geen door het systeem gedefinieerde gegevens type conversie tussen tussenliggende bron-en Sink-typen is.
> - Deze functie werkt met het meest recente gegevensset-model. Als deze optie niet wordt weer geven in de gebruikers interface, kunt u een nieuwe gegevensset maken.

De volgende eigenschappen worden ondersteund in de Kopieer activiteit voor gegevens type conversie (onder `translator` sectie voor het programmatisch ontwerpen):

| Eigenschap                         | Beschrijving                                                  | Vereist |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | De nieuwe gegevens type conversie-ervaring inschakelen. <br>De standaard waarde is False vanwege achterwaartse compatibiliteit.<br><br>Voor nieuwe Kopieer activiteiten die zijn gemaakt via Data Factory gebruikers interface sinds eind juni 2020, is deze gegevens type conversie standaard ingeschakeld voor de beste ervaring en ziet u de volgende type conversie-instellingen op het tabblad Kopieer activiteit-> toewijzing voor toepasselijke scenario's. <br>Als u een pijp lijn wilt maken via een programma, moet u de eigenschap expliciet instellen `typeConversion` op True om deze functie in te scha kelen.<br>Voor bestaande Kopieer activiteiten die zijn gemaakt voordat deze functie wordt uitgebracht, ziet u de opties voor type conversie niet in Data Factory gebruikers interface ontwerpen voor achterwaartse compatibiliteit. | No       |
| typeConversionSettings           | Een groep instellingen voor type conversie. Toep assen wanneer `typeConversion` is ingesteld op `true` . De volgende eigenschappen bevinden zich allemaal onder deze groep. | No       |
| *Onder `typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | Afgekapte gegevens toestaan bij het converteren van bron gegevens naar Sink met een ander type tijdens kopiëren, bijvoorbeeld van decimaal naar geheel getal, van date time offset tot datum tijd. <br>De standaardwaarde is Waar. | No       |
| treatBooleanAsNumber             | Behandel Boole-waarden als getallen, bijvoorbeeld waar als 1.<br>De standaard waarde is False. | No       |
| Date time format                   | Indelings teken reeks bij het converteren van datums zonder tijd zone-offset en teken reeksen, bijvoorbeeld `yyyy-MM-dd HH:mm:ss.fff` .  Raadpleeg [teken reeksen voor aangepaste datum-en tijd notatie](/dotnet/standard/base-types/custom-date-and-time-format-strings) voor gedetailleerde informatie. | No       |
| dateTimeOffsetFormat             | Teken reeks voor opmaak bij het converteren van datums met tijd zone-offset en teken reeksen, bijvoorbeeld `yyyy-MM-dd HH:mm:ss.fff zzz` .  Raadpleeg [teken reeksen voor aangepaste datum-en tijd notatie](/dotnet/standard/base-types/custom-date-and-time-format-strings) voor gedetailleerde informatie. | No       |
| timeSpanFormat                   | Indelings teken reeks bij het converteren van tijd perioden en teken reeksen, bijvoorbeeld `dd\.hh\:mm` . Raadpleeg [teken reeksen voor aangepaste time span-notaties](/dotnet/standard/base-types/custom-timespan-format-strings) voor gedetailleerde informatie. | No       |
| culturele                          | Cultuur gegevens die moeten worden gebruikt bij het converteren van typen, bijvoorbeeld `en-us` of `fr-fr` . | No       |

**Voorbeeld:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>Verouderde modellen

> [!NOTE]
> De volgende modellen om bron kolommen/-velden te koppelen aan sinks, worden nog steeds ondersteund voor compatibiliteit met eerdere versies. We raden u aan het nieuwe model te gebruiken dat wordt vermeld in [schema toewijzing](#schema-mapping). Data Factory gebruikers interface voor het ontwerpen is overgeschakeld op het genereren van het nieuwe model.

### <a name="alternative-column-mapping-legacy-model"></a>Alternatieve kolom toewijzing (verouderd model)

U kunt de > van de Kopieer activiteit opgeven `translator`  ->  `columnMappings` om toe te wijzen tussen gegevens in tabel vorm. In dit geval is de sectie ' Structure ' vereist voor de invoer-en uitvoer gegevens sets. Kolom toewijzing ondersteunt het **toewijzen van alle of subset kolommen in de bron gegevensset ' structuur ' op alle kolommen in de Sink-gegevensset ' Structure**'. Hieronder vindt u fout voorwaarden die resulteren in een uitzonde ring:

- Het query resultaat van de brongegevens opslag heeft geen kolom naam die is opgegeven in de sectie structuur van de invoer gegevensset.
- Het sink-gegevens archief (als met een vooraf gedefinieerd schema) heeft geen kolom naam die is opgegeven in de sectie ' Structure ' van de uitvoer gegevensset.
- Minder kolommen of meer kolommen in de structuur van de Sink-gegevensset dan is opgegeven in de toewijzing.
- Dubbele toewijzing.

In het volgende voor beeld heeft de gegevensset van de invoer een structuur en verwijst deze naar een tabel in een on-premises Oracle-data base.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

In dit voor beeld heeft de uitvoer gegevensset een structuur en verwijst deze naar een tabel in Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

De volgende JSON definieert een Kopieer activiteit in een pijp lijn. De kolommen van de bron die is toegewezen aan de kolommen in Sink met behulp van de eigenschap **Translator**  ->  **columnMappings** .

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Als u de syntaxis van gebruikt `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` om kolom toewijzing op te geven, wordt deze nog steeds ondersteund als-is.

### <a name="alternative-schema-mapping-legacy-model"></a>Alternatieve schema toewijzing (verouderd model)

U kunt een Kopieer activiteit-> opgeven `translator`  ->  `schemaMapping` om toe te wijzen tussen hiërarchische gegevens en tabellaire gegevens in tabel vorm, bijvoorbeeld kopiëren van MongoDb/rest naar tekst bestand en kopiëren van Oracle naar de API van Azure Cosmos DB voor MongoDb. De volgende eigenschappen worden ondersteund in de sectie Kopieer activiteit `translator` :

| Eigenschap            | Beschrijving                                                  | Vereist |
| :------------------ | :----------------------------------------------------------- | :------- |
| type                | De eigenschap type van het conversie programma Copy activity moet worden ingesteld op: **TabularTranslator** | Yes      |
| schemaMapping       | Een verzameling sleutel-waardeparen, die de toewijzings relatie vertegenwoordigt **van de bron aan de Sink-zijde**.<br/>- **Sleutel:** geeft bron aan. Voor **tabel bron** geeft u de naam van de kolom op zoals gedefinieerd in de structuur van de gegevensset. voor een **hiërarchische bron** geeft u de JSON-padexpressie op voor elk veld dat u wilt uitpakken en toewijzen.<br>- **Waarde:** vertegenwoordigt sink. Voor de **tabellaire Sink** geeft u de naam van de kolom op zoals gedefinieerd in de structuur van de gegevensset. voor **hiërarchische Sink** geeft u de JSON-padexpressie op voor elk veld dat u wilt uitpakken en toewijzen. <br>In het geval van hiërarchische gegevens, voor velden onder hoofd object, begint JSON-pad met root $; voor velden binnen de door eigenschap gekozen matrix wordt `collectionReference` het JSON-pad gestart vanuit het matrix element. | Yes      |
| collectionReference | Als u gegevens wilt sequentieel en wilt ophalen uit de objecten **in een matrix veld** met hetzelfde patroon en converteren naar per rij per object, geeft u het JSON-pad van die matrix op om meerdere aanvragen uit te voeren. Deze eigenschap wordt alleen ondersteund wanneer hiërarchische gegevens bron is. | No       |

**Voor beeld: kopiëren van MongoDB naar Oracle:**

Als u bijvoorbeeld een MongoDB-document hebt met de volgende inhoud:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

en u wilt het kopiëren naar een Azure SQL-tabel in de volgende indeling door de gegevens in de matrix *(order_pd en order_price)* samen te voegen en cross samen te voegen met de gemeen schappelijke basis informatie *(getal, datum en plaats)*:

| orderNumber | Order | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Configureer de schema toewijzings regel als de volgende JSON-voor beeld van een Kopieer activiteit:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)