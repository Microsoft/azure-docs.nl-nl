---
title: Gegevens stroom script toewijzen
description: Overzicht van de script code voor de gegevens stroom van Data Factory-behind-taal
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2021
ms.openlocfilehash: 7dd58a7d4a94b832e52930f8ac6507cdd8f7a20e
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100534818"
---
# <a name="data-flow-script-dfs"></a>Gegevens stroom script (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data flow script (DFS) is de onderliggende meta gegevens, vergelijkbaar met een coderings taal, die wordt gebruikt voor het uitvoeren van de trans formaties die zijn opgenomen in een toewijzings gegevens stroom. Elke trans formatie wordt vertegenwoordigd door een reeks eigenschappen die de benodigde informatie biedt om de taak correct uit te voeren. Het script is zichtbaar en kan worden bewerkt vanuit ADF door te klikken op de knop script in het bovenste lint van de gebruikers interface van de browser.

![Script knop](media/data-flow/scriptbutton.png "Script knop")

`allowSchemaDrift: true,`In een bron transformatie vertelt de service bijvoorbeeld dat alle kolommen uit de bron-gegevensset in de gegevens stroom moeten worden opgenomen, zelfs als deze niet zijn opgenomen in de schema projectie.

## <a name="use-cases"></a>Gebruiksvoorbeelden
De DFS wordt automatisch geproduceerd door de gebruikers interface. U kunt op de knop script klikken om het script weer te geven en aan te passen. U kunt ook scripts maken buiten de ADF-gebruikers interface en vervolgens door geven aan de Power shell-cmdlet. Wanneer u fouten opspoort in complexe gegevens stromen, is het mogelijk dat u de script code achtereen kunt scannen in plaats van de UI-grafiek weergave van uw stromen te scannen.

Hier volgen enkele voor beelden van use cases:
- Een programmatische productie maakt van een groot aantal gegevens stromen die tamelijk vergelijkbaar zijn, d.w.z. het afstempelen van gegevens stromen.
- Complexe expressies die moeilijk te beheren zijn in de gebruikers interface of die het gevolg zijn van validatie problemen.
- Fout opsporing en betere inzicht in verschillende fouten die tijdens de uitvoering worden geretourneerd.

Wanneer u een gegevensstroom script bouwt voor gebruik met Power shell of een API, moet u de opgemaakte tekst in één regel samen vouwen. U kunt tabs en nieuwe regels gebruiken als escape tekens. Maar de tekst moet zijn opgemaakt om in een JSON-eigenschap te passen. Er bevindt zich een knop in de gebruikers interface van de Script Editor aan de onderkant waarmee het script wordt opgemaakt als één regel voor u.

![knop Kopiëren](media/data-flow/copybutton.png "knop Kopiëren")

## <a name="how-to-add-transforms"></a>Trans formaties toevoegen
Voor het toevoegen van trans formaties zijn drie basis stappen vereist: het toevoegen van de kern transformatie gegevens, het omleiden van de invoer stroom en het omleiden van de uitvoer stroom. Dit kan eenvoudig worden weer gegeven in een voor beeld.
Stel dat we beginnen met een eenvoudige bron om gegevens stroom te sinken, zoals in het volgende:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Als we besluiten een afgeleide trans formatie toe te voegen, moet u eerst de tekst van de kern transformatie maken, met een eenvoudige expressie voor het toevoegen van een nieuwe hoofd kolom met de naam `upperCaseTitle` :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Daarna maken we de bestaande DFS en voegen we de trans formatie toe:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

En de inkomende stroom wordt nu omgeleid door de trans formatie te identificeren waarnaar de nieuwe trans formatie moet worden verzonden (in dit geval `source1` ) en het kopiëren van de naam van de stroom naar de nieuwe trans formatie:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Ten slotte wordt de trans formatie geïdentificeerd die na deze nieuwe trans formatie moet worden uitgevoerd en wordt de invoer stroom (in dit geval `sink1` ) vervangen door de naam van de uitvoer stroom van de nieuwe trans formatie:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Basis beginselen van DFS
De DFS bestaat uit een reeks gekoppelde trans formaties, waaronder bronnen, sinks en verschillende andere, waarmee nieuwe kolommen kunnen worden toegevoegd, gegevens worden gefilterd, gegevens kunnen worden samengevoegd en nog veel meer. Normaal gesp roken wordt het script met een of meer bronnen gevolgd door veel trans formaties en eindigend met een of meer Sinks.

Bronnen hebben allemaal dezelfde basis constructie:
```
source(
  source properties
) ~> source_name
```

Zo zou een eenvoudige bron met drie kolommen (movieId, title, genres) er als volgt uitzien:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Alle trans formaties, behalve bronnen, hebben dezelfde basis constructie:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Een eenvoudige afleiding van een trans formatie die een kolom (titel) maakt en overschrijft met een hoofd versie is als volgt:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

En een Sink zonder schema zou gewoon zijn:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Script fragmenten

Script fragmenten zijn deel bare code van het gegevensstroom script dat u kunt gebruiken om te delen tussen gegevens stromen. In deze video vindt u meer informatie over het gebruik van script fragmenten en het gebruik van gegevens stroom script voor het kopiëren en plakken van delen van het script achter uw gegevensstroom grafieken:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Cumulatieve samenvattings statistieken
Voeg een geaggregeerde trans formatie toe aan de gegevens stroom met de naam ' SummaryStats ' en plak deze hieronder in deze code voor de statistische functie in uw script, waarbij de bestaande SummaryStats wordt vervangen. Dit geeft een algemeen patroon voor samenvattings statistieken van gegevens profielen.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
U kunt ook het onderstaande voor beeld gebruiken om het aantal unieke en het aantal afzonderlijke rijen in uw gegevens te tellen. Het onderstaande voor beeld kan worden geplakt in een gegevens stroom met een statistische trans formatie met de naam ValueDistAgg. In dit voor beeld wordt een kolom met de naam ' title ' gebruikt. Zorg ervoor dat u ' titel ' vervangt door de kolom met de teken reeks in de gegevens die u wilt gebruiken om het aantal waarden op te halen.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Alle kolommen in een samen voeging toevoegen
Dit is een algemeen aggregatie patroon dat laat zien hoe u de resterende kolommen in uw uitvoer meta gegevens kunt gebruiken wanneer u aggregaties bouwt. In dit geval gebruiken we de ```first()``` functie om de eerste waarde in elke kolom te kiezen waarvan de naam geen ' film ' is. Als u dit wilt gebruiken, maakt u een geaggregeerde trans formatie met de naam DistinctRows en plakt u deze in uw script boven aan het bestaande DistinctRows-aggregatie script.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Rij-hash-vinger afdruk maken 
Gebruik deze code in uw gegevensstroom script om een nieuwe afgeleide kolom te maken met de naam ```DWhash``` die een ```sha1``` hash van drie kolommen produceert.

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

U kunt dit script ook hieronder gebruiken om een rij-hash te genereren met alle kolommen die aanwezig zijn in uw stream, zonder dat u elke kolom een naam hoeft te geven:

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg equivalent
Deze code fungeert als de T-SQL- ```string_agg()``` functie, en voegt teken reeks waarden samen in een matrix. U kunt die matrix vervolgens casten naar een teken reeks om te gebruiken met SQL-doelen.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>Aantal updates, upsert, invoeg acties, verwijderen
Wanneer u een alter Row trans formatie gebruikt, wilt u mogelijk het aantal updates, upsert, invoeg acties, verwijderen dat het resultaat is van uw beleid voor het wijzigen van rijen. Voeg een geaggregeerde trans formatie toe na de Alter Row en plak dit data flow-script in de cumulatieve definitie voor die aantallen.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>DISTINCT-rij met alle kolommen
Met dit fragment wordt een nieuwe geaggregeerde trans formatie toegevoegd aan uw gegevens stroom, waarmee alle binnenkomende kolommen worden opgehaald, een hash genereren die wordt gebruikt om te groeperen om dubbele waarden te elimineren en vervolgens de eerste instantie van elk duplicaat als uitvoer opgeven. U hoeft de kolommen niet expliciet een naam te gegeven en ze worden automatisch gegenereerd vanuit uw binnenkomende gegevens stroom.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>Controleren op NULL-waarden in alle kolommen
Dit is een fragment dat u in uw gegevens stroom kunt plakken, zodat u alle kolommen voor NULL-waarden voor het algemeen controleert. Deze techniek maakt gebruik van schema drift om alle kolommen in alle rijen te bekijken en gebruikt een voorwaardelijke splitsing om de rijen met NULL-waarden uit de rijen te scheiden zonder NULL-waarden. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>AutoMap schema drift met een selectie
Wanneer u een bestaand database schema van een onbekende of dynamische set binnenkomende kolommen wilt laden, moet u de kolommen aan de rechter kant toewijzen in de Sink-trans formatie. Dit is alleen nodig wanneer u een bestaande tabel wilt laden. Voeg dit fragment vóór uw Sink toe om een selectie te maken waarmee uw kolommen automatisch worden toegewezen. Zorg ervoor dat uw Sink-toewijzing automatisch is toegewezen.

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

### <a name="persist-column-data-types"></a>Kolom gegevens typen behouden
Voeg dit script toe aan de definitie van een afgeleide kolom om de kolom namen en gegevens typen van uw gegevens stroom op te slaan in een permanente Store met behulp van een sink.

```
derive(each(match(type=='string'), $$ = 'string'),
    each(match(type=='integer'), $$ = 'integer'),
    each(match(type=='short'), $$ = 'short'),
    each(match(type=='complex'), $$ = 'complex'),
    each(match(type=='array'), $$ = 'array'),
    each(match(type=='float'), $$ = 'float'),
    each(match(type=='date'), $$ = 'date'),
    each(match(type=='timestamp'), $$ = 'timestamp'),
    each(match(type=='boolean'), $$ = 'boolean'),
    each(match(type=='long'), $$ = 'long'),
    each(match(type=='double'), $$ = 'double')) ~> DerivedColumn1
```

### <a name="fill-down"></a>Omlaag door voeren
Hier vindt u informatie over het implementeren van het algemene ' opvullen '-probleem met gegevens sets wanneer u NULL-waarden wilt vervangen door de waarde van de vorige niet-NULL-waarde in de reeks. Houd er rekening mee dat deze bewerking negatieve gevolgen voor de prestaties kan hebben omdat u een synthetisch venster moet maken in uw hele gegevensset met een ' dummy ' categorie waarde. Daarnaast moet u sorteren op een waarde om de juiste gegevens reeks te maken om de vorige niet-NULL-waarde te vinden. In dit fragment hieronder wordt de categorie synthetisch gemaakt als dummy en wordt gesorteerd op een surrogaat sleutel. U kunt de surrogaat sleutel verwijderen en uw eigen gegevensspecifieke Sorteer sleutel gebruiken. In dit code fragment wordt ervan uitgegaan dat u al een bron transformatie hebt toegevoegd met de naam ```source1```

```
source1 derive(dummy = 1) ~> DerivedColumn
DerivedColumn keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey
SurrogateKey window(over(dummy),
    asc(sk, true),
    Rating2 = coalesce(Rating, last(Rating, true()))) ~> Window1
```

### <a name="moving-average"></a>Zwevend gemiddelde
Zwevend gemiddelde kan zeer eenvoudig worden geïmplementeerd in gegevens stromen met behulp van een Windows-trans formatie. In dit voor beeld wordt een zwevend gemiddelde van 15 dagen voor micro soft gemaakt.

```
window(over(stocksymbol),
    asc(Date, true),
    startRowOffset: -7L,
    endRowOffset: 7L,
    FifteenDayMovingAvg = round(avg(Close),2)) ~> Window1
```

## <a name="next-steps"></a>Volgende stappen

Gegevens stromen verkennen door te beginnen met het [artikel overzicht van gegevens stromen](concepts-data-flow-overview.md)
