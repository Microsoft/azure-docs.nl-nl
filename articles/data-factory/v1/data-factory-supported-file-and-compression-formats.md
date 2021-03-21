---
title: Bestands-en compressie-indelingen in Azure Data Factory
description: Meer informatie over de bestands indelingen die door Azure Data Factory worden ondersteund.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7b53d9cd64c50d8305714878324dd355eb6d1840
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368724"
---
# <a name="file-and-compression-formats-supported-by-azure-data-factory"></a>Bestands-en compressie-indelingen die worden ondersteund door Azure Data Factory
*Dit onderwerp is van toepassing op de volgende connectors: [Amazon S3](data-factory-amazon-simple-storage-service-connector.md), [Azure Blob](data-factory-azure-blob-connector.md), [Azure data Lake Store](data-factory-azure-datalake-connector.md), [Bestands systeem](data-factory-onprem-file-system-connector.md), [FTP](data-factory-ftp-connector.md), [HDFS](data-factory-hdfs-connector.md), [http](data-factory-http-connector.md)en [SFTP](data-factory-sftp-connector.md).*

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [ondersteunde bestands indelingen en compressie-codecs in Data Factory](../supported-file-formats-and-compression-codecs.md).

Azure Data Factory ondersteunt de volgende typen bestands indelingen:

* [Tekst indeling](#text-format)
* [JSON-indeling](#json-format)
* [Avro-indeling](#avro-format)
* [ORC-indeling](#orc-format)
* [Parquet-indeling](#parquet-format)

## <a name="text-format"></a>Tekst indeling
Als u wilt lezen uit een tekst bestand of naar een tekst bestand wilt schrijven, stelt u de `type` eigenschap in het `format` gedeelte van de gegevensset in op **TextFormat**. U kunt ook de volgende **optionele** eigenschappen opgeven in het gedeelte `format`. Raadpleeg het gedeelte [TextFormat-voorbeeld](#textformat-example) voor configuratie-instructies.

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| columnDelimiter |Het teken dat wordt gebruikt voor het scheiden van kolommen in een bestand. U kunt overwegen om een zeldzaam niet-afdrukbaar teken te gebruiken dat waarschijnlijk niet aanwezig is in uw gegevens. Geef bijvoorbeeld ' \u0001 ' op, die het begin van de kop (SOH) vertegenwoordigt. |Er is slechts één teken toegestaan. De **standaardwaarde** is een **komma (',')**. <br/><br/>Als u een Unicode-teken wilt gebruiken, raadpleegt u [Unicode-tekens](https://en.wikipedia.org/wiki/List_of_Unicode_characters) om de bijbehorende code te verkrijgen. |Nee |
| rowDelimiter |Het teken dat wordt gebruikt voor het scheiden van rijen in een bestand. |Er is slechts één teken toegestaan. De **standaardwaarde** is een van de volgende leeswaarden **['\r\n', '\r', '\n']** en de schrijfwaarde **'\r\n'**. |Nee |
| escapeChar |Dit speciale teken wordt gebruikt om een scheidingsteken voor kolommen van de inhoud van het invoerbestand om te zetten. <br/><br/>Het is niet mogelijk om zowel escapeChar als quoteChar voor een tabel op te geven. |Er is slechts één teken toegestaan. Er is geen standaardwaarde. <br/><br/>Voor beeld: als u een komma (', ') hebt als scheidings teken voor de kolom, maar u de komma wilt gebruiken in de tekst (bijvoorbeeld: "Hallo, wereld"), kunt u $ opgeven als escape teken en de teken reeks "Hallo $, wereld" in de bron. |Nee |
| quoteChar |Het teken dat wordt gebruikt om een tekenreekswaarde te citeren. De scheidingstekens voor kolommen en rijen binnen de aanhalingstekens worden beschouwd als onderdeel van de tekenreekswaarde. Deze eigenschap is van toepassing op gegevenssets voor invoer en uitvoer.<br/><br/>Het is niet mogelijk om zowel escapeChar als quoteChar voor een tabel op te geven. |Er is slechts één teken toegestaan. Er is geen standaardwaarde. <br/><br/>Voorbeeld: als u kolommen scheidt met komma's (', '), maar u het kommateken in een tekst wilt gebruiken (voorbeeld: <Hallo, wereld>), kunt u " (dubbel aanhalingsteken) als het aanhalingsteken opgeven en de tekenreeks "Hallo, wereld" in de bron gebruiken. |Nee |
| nullValue |Een of meer tekens die worden gebruikt om een null-waarde te vertegenwoordigen. |Een of meer tekens. De **standaardwaarden** zijn **'\N' en 'NULL'** voor lezen en **'\N'** voor schrijven. |Nee |
| encodingName |Geef de coderingsnaam op. |Een geldige coderingsnaam. Zie [De eigenschap Encoding.EncodingName](/dotnet/api/system.text.encoding). Voorbeeld: windows 1250 of shift_jis. De **standaardwaarde** is **UTF-8**. |Nee |
| firstRowAsHeader |Hiermee geeft u op of de eerste rij als een header moet worden gezien. Bij een gegevensset voor invoer leest Data Factory de eerste rij als een header. Bij een gegevensset voor uitvoer schrijft Data Factory de eerste rij als een header. <br/><br/>Zie [Gebruiksscenario's`firstRowAsHeader` en `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) voor voorbeeldscenario's. |Waar<br/><b>False (standaard)</b> |Nee |
| skipLineCount |Geeft het aantal rijen aan dat moet worden overgeslagen bij het lezen van gegevens in invoerbestanden. Als zowel skipLineCount als firstRowAsHeader is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand. <br/><br/>Zie [Gebruiksscenario's`firstRowAsHeader` en `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) voor voorbeeldscenario's. |Geheel getal |Nee |
| treatEmptyAsNull |Hiermee geeft u aan of null of lege tekenreeks moeten worden behandeld als een null-waarde bij het lezen van gegevens uit een invoerbestand. |**True (standaard)**<br/>Niet waar |Nee |

### <a name="textformat-example"></a>Voorbeeld van TextFormat
In de volgende JSON-definitie voor een gegevensset zijn enkele van de optionele eigenschappen opgegeven.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Gebruik een `escapeChar` in plaats van `quoteChar`, vervang de regel door `quoteChar` met het volgende escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scenario's voor het gebruik van firstRowAsHeader en skipLineCount
* U kopieert vanuit een bron die geen bestand is, naar een tekstbestand en wilt een headerregel toevoegen die de metagegevens van het schema bevat (bijvoorbeeld: SQL-schema). Geef voor `firstRowAsHeader` 'True' op in de uitvoergegevensset voor dit scenario.
* U wilt kopiëren vanuit een tekstbestand met een headerregel naar een sink die geen bestand is en wilt die regel verwijderen. Geef voor `firstRowAsHeader` 'True' op in de invoergegevensset.
* U wilt kopiëren uit een tekstbestand en wilt een paar regels aan het begin overslaan die geen gegevens of headerinformatie bevatten. Geef `skipLineCount` op om aan te geven hoeveel regels er moeten worden overgeslagen. Als de rest van het bestand een headerregel bevat, kunt u ook `firstRowAsHeader` opgeven. Als zowel `skipLineCount` als `firstRowAsHeader` is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand

## <a name="json-format"></a>JSON-indeling
Als u **een JSON-bestand wilt importeren/exporteren naar/van Azure Cosmos DB**, raadpleegt u de sectie [JSON-documenten importeren/exporteren](data-factory-azure-documentdb-connector.md#importexport-json-documents) in [gegevens verplaatsen naar/van Azure Cosmos DB](data-factory-azure-documentdb-connector.md) artikel.

Als u de JSON-bestanden wilt parseren of de gegevens in JSON-indeling wilt schrijven, stelt u de `type` eigenschap in de `format` sectie in op **JsonFormat**. U kunt ook de volgende **optionele** eigenschappen opgeven in het gedeelte `format`. Zie het gedeelte [JsonFormat-voorbeeld](#jsonformat-example) voor configuratie-instructies.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| filePattern |Hiermee geeft u het patroon aan van gegevens die zijn opgeslagen in elk JSON-bestand. Toegestane waarden zijn **setOfObjects** en **arrayOfObjects**. De **standaardwaarde** is **setOfObjects**. Zie het gedeelte [JSON-bestandpatronen](#json-file-patterns) voor meer informatie over deze patronen. |Nee |
| jsonNodeReference | Als u wilt bladeren en gegevens wilt ophalen uit de objecten in een matrixveld met hetzelfde patroon, geeft u het JSON-pad van die matrix op. Deze eigenschap wordt alleen ondersteund bij het kopiëren van gegevens uit JSON-bestanden. | Nee |
| jsonPathDefinition | Hiermee geeft u de JSON-padexpressie aan voor elke kolomtoewijzing met een aangepaste kolomnaam (begin met een kleine letter). Deze eigenschap wordt alleen ondersteund bij het kopiëren van gegevens uit JSON-bestanden. U kunt gegevens ophalen uit het object of een matrix. <br/><br/> Voor velden onder het hoofdobject begint u met root $; voor velden binnen de matrix die is gekozen door de eigenschap `jsonNodeReference`, begint u vanaf het element van de matrix. Zie het gedeelte [JsonFormat-voorbeeld](#jsonformat-example) voor configuratie-instructies. | Nee |
| encodingName |Geef de coderingsnaam op. Zie voor de lijst met geldige namen voor versleuteling [De eigenschap Encoding.EncodingName](/dotnet/api/system.text.encoding). Bijvoorbeeld: windows 1250 of shift_jis. De **standaard** waarde is: **UTF-8**. |Nee |
| nestingSeparator |Teken dat wordt gebruikt voor het scheiden van geneste niveaus. De standaardwaarde is '.' (punt). |Nee |

### <a name="json-file-patterns"></a>JSON-bestandpatronen

Met de Kopieer activiteit kunt u de volgende patronen van JSON-bestanden parseren:

- **Type I: setOfObjects**

    Elk bestand bevat één object of meerdere door regels gescheiden/samengevoegde objecten. Wanneer deze optie is geselecteerd in een uitvoergegevensset, produceert de kopieerbewerking een enkel JSON-bestand met één object per regel (door regels gescheiden).

    * **voorbeeld van JSON-bestand met één object**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **voorbeeld van JSON-bestand dat door regels is gescheiden**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **voorbeeld van JSON-bestand met samengevoegde objecten**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Type II: arrayOfObjects**

    Elk bestand bevat een matrix met objecten.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>Voorbeeld van JsonFormat

**Voorbeeld 1: gegevens uit JSON-bestanden kopiëren**

Bekijk de volgende twee voor beelden bij het kopiëren van gegevens uit JSON-bestanden. De algemene punten die moeten worden genoteerd:

**Voorbeeld 1: gegevens ophalen uit object en matrix**

In dit voorbeeld kunt u verwachten dat één JSON-hoofdobject wordt toegewezen aan één record in het tabelresultaat. Als u een JSON-bestand hebt met de volgende inhoud:  

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```
en u wilt het kopiëren naar een Azure SQL-tabel in de volgende indeling door gegevens te extraheren uit de objecten en matrix:

| Id | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | Pc | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 13/1/2017 11:24:37 uur |

De invoergegevensset met het type **JsonFormat** wordt als volgt gedefinieerd: (gedeeltelijke definitie met alleen belangrijke onderdelen). Met name:

- Het gedeelte `structure` definieert de aangepaste kolomnamen en het bijbehorende gegevenstype tijdens het converteren van gegevens in tabelvorm. Dit gedeelte is **optioneel**, tenzij u kolommen moet toewijzen. Zie de sectie [bron gegevensset toewijzen aan kolommen voor doel gegevensset](data-factory-map-columns.md) voor meer informatie.
- Met `jsonPathDefinition` geeft u het JSON-pad op voor elke kolom die aangeeft waar de gegevens moeten worden opgehaald. Als u gegevens wilt kopiëren uit een matrix, kunt u **matrix [x]. Property** gebruiken om de waarde van de gegeven eigenschap uit het Xth-object op te halen, of u kunt **matrix [*]. Property** gebruiken om de waarde te vinden van een object dat dergelijke eigenschap bevat.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}      
        }
    }
}
```

**Voorbeeld 2: meerdere objecten met hetzelfde patroon uit een matrix toepassen**

In dit voorbeeld probeert u een JSON-hoofdobject te transformeren naar meerdere records in een tabelresultaat. Als u een JSON-bestand hebt met de volgende inhoud:  

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
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
    "city": [ { "sanmateo": "No 1" } ]
}
```
en u wilt het kopiëren naar een Azure SQL-tabel in de volgende indeling, door de gegevens binnen de matrix af te vlakken en samen te voegen met de algemene root-gegevens:

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

De invoergegevensset met het type **JsonFormat** wordt als volgt gedefinieerd: (gedeeltelijke definitie met alleen belangrijke onderdelen). Met name:

- Het gedeelte `structure` definieert de aangepaste kolomnamen en het bijbehorende gegevenstype tijdens het converteren van gegevens in tabelvorm. Dit gedeelte is **optioneel**, tenzij u kolommen moet toewijzen. Zie de sectie [bron gegevensset toewijzen aan kolommen voor doel gegevensset](data-factory-map-columns.md) voor meer informatie.
- Met `jsonNodeReference` geeft u aan dat moet worden gebladerd naar het object en dat er gegevens uit moeten worden opgehaald met hetzelfde patroon onder de **matrix** orderlines.
- Met `jsonPathDefinition` geeft u het JSON-pad op voor elke kolom die aangeeft waar de gegevens moeten worden opgehaald. In dit voorbeeld bevinden 'ordernumber', 'orderdate' en 'city' zich onder het root-object. Het JSON-pad begint met '$.', terwijl 'order_pd' en 'order_price' worden gedefinieerd met het pad dat is afgeleid van het matrixelement zonder '$.'.

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}         
        }
    }
}
```

**Houd rekening met de volgende punten:**

* Als `structure` en `jsonPathDefinition` niet zijn gedefinieerd in de gegevensset van Data Factory, detecteert de kopieerbewerking het schema van het eerste object en wordt het hele object afgevlakt.
* Als de JSON-invoer een matrix heeft, zet de kopieerbewerking de volledige matrix-waarde standaard om in een tekenreeks. U kunt ervoor kiezen om er gegevens uit op te halen met behulp van `jsonNodeReference` en/of `jsonPathDefinition`, of deze stap over te slaan door deze niet op te geven in `jsonPathDefinition`.
* Als er dubbele namen op hetzelfde niveau voorkomen, gebruikt de kopieerbewerking de laatste.
* Eigenschapnamen zijn hoofdlettergevoelig. Twee eigenschappen met dezelfde naam maar met een verschil in hoofdletters en kleine letters worden behandeld als twee afzonderlijke eigenschappen.

**Voorbeeld 2: gegevens schrijven naar een JSON-bestand**

Als u de volgende tabel in SQL Database hebt:

| Id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

voor elke record gaat u naar een JSON-object in de volgende indeling schrijven:
```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

De uitvoergegevensset met het type **JsonFormat** wordt als volgt gedefinieerd: (gedeeltelijke definitie met alleen belangrijke onderdelen). Meer specifiek: `structure` in de sectie worden de aangepaste eigenschapnamen in het doel bestand gedefinieerd `nestingSeparator` (de standaard waarde is '. ') worden gebruikt om de nest laag te identificeren uit de naam. Dit gedeelte is **optioneel**, tenzij u de naam van de eigenschap wilt wijzigen ten opzichte van de naam van de bronkolom of sommige eigenschappen wilt nesten.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="avro-format"></a>AVRO-indeling
Als u de Avro-bestanden wilt parseren of de gegevens in Avro-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **AvroFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "AvroFormat",
}
```

Als u de Avro-indeling wilt gebruiken in een Hive-tabel, kunt u naar [de zelf studie van Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)verwijzen.

Houd rekening met de volgende punten:  

* [Complexe gegevens typen](https://avro.apache.org/docs/current/spec.html#schema_complex) worden niet ondersteund (records, enums, matrices, kaarten, vakbonden en vast).

## <a name="orc-format"></a>ORC-indeling
Als u de ORC-bestanden wilt parseren of de gegevens in ORC-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **OrcFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Als u de ORC niet **ongewijzigd** kopieert tussen on-premises gegevensopslag en gegevensopslag in de cloud, moet u de JRE 8 (Java Runtime Environment) op de gatewaycomputer installeren. Een 64-bits-gateway vereist 64-bits JRE en 32-bits JRE is vereist voor een 32-bits-gateway. U vindt beide versies [hier](https://go.microsoft.com/fwlink/?LinkId=808605). Kies de juiste versie.
>
>

Houd rekening met de volgende punten:

* Complexe gegevenstypen worden niet ondersteund (STRUCT, MAP, LIST, UNION)
* Een ORC-bestand heeft drie [opties voor compressie](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Data Factory ondersteunt het lezen van gegevens uit ORC-bestanden in een van deze gecomprimeerde indelingen. Hierbij wordt de compressiecodec in de metagegevens gebruikt om de gegevens te lezen. Bij het schrijven naar een ORC-bestand kiest Data Factory echter ZLIB, de standaardinstelling voor ORC. Er is momenteel geen optie om dit gedrag te overschrijven.

## <a name="parquet-format"></a>Parquet-indeling
Als u de Parquet-bestanden wilt parseren of de gegevens in Parquet-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **ParquetFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> Als u de Parquet niet **ongewijzigd** kopieert tussen on-premises gegevensopslag en gegevensopslag in de cloud, moet u de JRE 8 (Java Runtime Environment) op de gatewaycomputer installeren. Een 64-bits-gateway vereist 64-bits JRE en 32-bits JRE is vereist voor een 32-bits-gateway. U vindt beide versies [hier](https://go.microsoft.com/fwlink/?LinkId=808605). Kies de juiste versie.
>
>

Houd rekening met de volgende punten:

* Complexe gegevenstypen worden niet ondersteund (MAP, LIST)
* Parquet-bestanden hebben de volgende opties voor compressie: NONE, SNAPPY, GZIP en LZO. Data Factory ondersteunt het lezen van gegevens uit ORC-bestanden in een van deze gecomprimeerde indelingen. Hierbij wordt de compressiecodec in de metagegevens gebruikt om de gegevens te lezen. Bij het schrijven naar een Parquet-bestand kiest Data Factory echter SNAPPY, de standaardinstelling voor Parquet. Er is momenteel geen optie om dit gedrag te overschrijven.

## <a name="compression-support"></a>Ondersteuning voor compressie
Het verwerken van grote gegevens sets kan I/O-en netwerk knelpunten veroorzaken. Gecomprimeerde gegevens in archieven kunnen daarom niet alleen de gegevens overdracht over het netwerk versnellen en schijf ruimte besparen, maar ook aanzienlijke prestatie verbeteringen in de verwerking van big data. Momenteel wordt compressie ondersteund voor gegevens archieven op basis van bestanden zoals Azure Blob of een on-premises bestands systeem.  

Als u compressie voor een gegevensset wilt opgeven, gebruikt u de eigenschap **Compression** in de JSON van de gegevensset, zoals in het volgende voor beeld:   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```

Stel dat de voor beeld-gegevensset wordt gebruikt als uitvoer van een Kopieer activiteit. de Kopieer activiteit comprimeert de uitvoer gegevens met GZIP-codec met een optimale verhouding en schrijft vervolgens de gecomprimeerde gegevens naar een bestand met de naam pagecounts.csv. gz in de Azure Blob Storage.

> [!NOTE]
> Compressie-instellingen worden niet ondersteund voor gegevens in de **Avro Format**, **OrcFormat** of **ParquetFormat**. Bij het lezen van bestanden in deze indelingen Data Factory detecteert en gebruikt de compressie-codec in de meta gegevens. Wanneer u naar bestanden in deze indelingen schrijft, kiest Data Factory de standaard compressie-codec voor die indeling. Bijvoorbeeld ZLIB voor OrcFormat en SNAPPY voor ParquetFormat.   

De sectie **compressie** heeft twee eigenschappen:  

* **Type:** de compressie-codec, die **gzip**, **Deflate**, **bzip2** of **ZipDeflate** kan zijn.  
* **Niveau:** de compressie ratio, die **optimaal** of **snelst** kan zijn.

  * **Snelst:** De compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.
  * **Optimaal**: de compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt.

    Zie het onderwerp [compressie niveau](/dotnet/api/system.io.compression.compressionlevel) voor meer informatie.

Wanneer u `compression` een eigenschap opgeeft in een JSON van een invoer gegevensset, kan de pijp lijn gecomprimeerde gegevens van de bron lezen en wanneer u de eigenschap in een JSON van een uitvoer gegevensset opgeeft, kan de Kopieer activiteit gecomprimeerde gegevens naar het doel schrijven. Hier volgen enkele voor beelden van scenario's:

* Lees de door GZIP gecomprimeerde gegevens van een Azure-Blob, Decomprimeer deze en schrijf resultaat gegevens naar Azure SQL Database. U definieert de invoer van de Azure Blob-gegevensset met de `compression` `type` JSON-eigenschap als gzip.
* Gegevens lezen uit een bestand met een onbewerkte tekst vanuit een on-premises bestands systeem, comprimeren met de GZip-indeling en de gecomprimeerde gegevens schrijven naar een Azure-Blob. U definieert een uitvoer van een Azure Blob-gegevensset met de `compression` `type` JSON-eigenschap als gzip.
* Lees het zip-bestand van de FTP-server, Decomprimeer het om de bestanden erin op te halen en de bestanden naar Azure Data Lake Store te brengen. U definieert een invoer-FTP-gegevensset met de `compression` `type` JSON-eigenschap als ZipDeflate.
* Lees een door GZIP gecomprimeerde gegevens uit een Azure-Blob, Decomprimeer het, comprimeer deze met BZIP2 en schrijf resultaat gegevens naar een Azure-Blob. In dit geval definieert u de invoer van de Azure Blob-gegevensset die is `compression` `type` ingesteld op gzip en de uitvoer gegevensset met `compression` `type` ingesteld op bzip2.   


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor gegevens archieven op basis van bestanden die worden ondersteund door Azure Data Factory:

- [Azure Blob Storage](data-factory-azure-blob-connector.md)
- [Azure Data Lake Store](data-factory-azure-datalake-connector.md)
- [FTP](data-factory-ftp-connector.md)
- [HDFS](data-factory-hdfs-connector.md)
- [Bestands systeem](data-factory-onprem-file-system-connector.md)
- [Amazon S3](data-factory-amazon-simple-storage-service-connector.md)