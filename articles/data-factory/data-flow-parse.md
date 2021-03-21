---
title: Gegevens transformatie parseren in gegevens stroom toewijzen
description: Inge sloten kolom documenten parseren
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710189"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Trans formatie parseren in gegevens stroom toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de parseren trans formatie om kolommen in uw gegevens in de document vorm te parseren. De huidige ondersteunde typen van Inge sloten documenten die kunnen worden geparseerd, zijn JSON en tekst met scheidings tekens.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Configuratie

In het configuratie scherm voor het parseren van trans formatie kiest u eerst het type gegevens dat is opgenomen in de kolommen die u inline wilt parseren. De parser-trans formatie bevat ook de volgende configuratie-instellingen.

![Instellingen parseren](media/data-flow/data-flow-parse-1.png "Parseren")

### <a name="column"></a>Kolom

Net als bij afgeleide kolommen en aggregaties, kunt u een kolom afsluiten wijzigen door deze te selecteren in de vervolg keuzelijst. U kunt hier ook de naam van een nieuwe kolom typen. ADF slaat de geparseerde bron gegevens op in deze kolom.

### <a name="expression"></a>Expression

Gebruik de opbouw functie voor expressies om de bron voor uw parsering in te stellen. Dit kan net zo eenvoudig zijn als het selecteren van de bron kolom met de zelf opgenomen gegevens die u wilt parseren, of u kunt complexe expressies maken om te parseren.

### <a name="output-column-type"></a>Type uitvoer kolom

Hier gaat u het doel uitvoer schema configureren van de parsering die in één kolom wordt geschreven.

![Voor beeld parseren](media/data-flow/data-flow-parse-2.png "Voor beeld parseren")

In dit voor beeld hebben we het parseren gedefinieerd van het binnenkomende veld ' jsonString ', dat tekst zonder opmaak is, maar opgemaakt als een JSON-structuur. We gaan de geparseerde resultaten opslaan als JSON in een nieuwe kolom met de naam ' JSON ' met dit schema:

```(trade as boolean, customers as string[])```

Raadpleeg het tabblad controleren en voor beeld van gegevens om te controleren of de uitvoer juist is toegewezen.

## <a name="examples"></a>Voorbeelden

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

### <a name="examples"></a>Voorbeelden

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Volgende stappen

* Gebruik de [Afvlakkings transformatie](data-flow-flatten.md) om rijen naar kolommen te draaien.
* Gebruik de [afgeleide kolom transformatie](data-flow-derived-column.md) om kolommen naar rijen te draaien.
