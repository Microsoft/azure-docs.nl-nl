---
title: Opbouw functie voor expressies in gegevens stroom toewijzen
description: Expressies bouwen met behulp van Expression Builder in toewijzing van gegevens stromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 753f201fbde5d9e7100b6e257f8dc79e4462d7b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584920"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Expressies bouwen in gegevens stroom toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bij het toewijzen van gegevens stroom worden veel trans formatie-eigenschappen ingevoerd als expressies. Deze expressies bestaan uit kolom waarden, para meters, functies, Opera tors en letterlijke tekens die tijdens de uitvoering resulteren in een Spark-gegevens type. Het toewijzen van gegevens stromen heeft een eigen ervaring om u te helpen bij het bouwen van deze expressies die de **opbouw functie voor expressies** worden genoemd. Het gebruik van de voltooiing van  [IntelliSense](/visualstudio/ide/using-intellisense) -code voor markeren, syntaxis controle en automatisch aanvullen is ontworpen om het maken van gegevens stromen eenvoudig uit te voeren. In dit artikel wordt uitgelegd hoe u de opbouw functie voor expressies gebruikt om uw bedrijfs logica effectief te bouwen.

![Opbouw functie voor expressies](media/data-flow/expresion-builder.png "Opbouwfunctie voor expressies")

## <a name="open-expression-builder"></a>Opbouw functie voor expressies openen

Er zijn meerdere toegangs punten voor het openen van de opbouw functie voor expressies. Deze zijn allemaal afhankelijk van de specifieke context van de data flow-trans formatie. De meest voorkomende use-case is in trans formaties zoals [afgeleide kolom](data-flow-derived-column.md) en [aggregatie](data-flow-aggregate.md) waarbij gebruikers kolommen maken of bijwerken met behulp van de data flow-expressie taal. De opbouw functie voor expressies kan worden geopend door **Expression Builder openen** boven de lijst met kolommen te selecteren. U kunt ook op een kolom context klikken en de opbouw functie voor expressies rechtstreeks op die expressie openen.

![De opbouw functie voor expressies is afgeleid](media/data-flow/open-expression-builder-derive.png "De opbouw functie voor expressies is afgeleid")

In sommige trans formaties als [filter](data-flow-filter.md)klikt u op een blauwe expressie tekstvak om de opbouw functie voor expressies te openen. 

![Vak met blauwe expressie](media/data-flow/expressionbox.png "Vak met blauwe expressie")

Wanneer u verwijst naar kolommen in een matching-of Group-by-voor waarde, kan een expressie waarden uit kolommen ophalen. Selecteer **berekende kolom** om een expressie te maken.

![Optie berekende kolom](media/data-flow/computedcolumn.png "Optie berekende kolom")

In gevallen waarin een expressie of letterlijke waarde geldige invoer is, selecteert u **dynamische inhoud toevoegen** om een expressie te bouwen die resulteert in een letterlijke waarde.

![Optie voor dynamische inhoud toevoegen](media/data-flow/add-dynamic-content.png "Optie voor dynamische inhoud toevoegen")

## <a name="expression-elements"></a>Expressie-elementen

In het toewijzen van gegevens stromen kunnen expressies bestaan uit kolom waarden, para meters, functies, lokale variabelen, Opera tors en letterlijke waarden. Deze expressies moeten resulteren in een Spark-gegevens type zoals teken reeks, Booleaanse waarde of geheel getal.

![Expressie-elementen](media/data-flow/expression-elements.png "Expressie-elementen")

### <a name="functions"></a>Functions

Het toewijzen van gegevens stromen heeft ingebouwde functies en Opera tors die kunnen worden gebruikt in expressies. Voor een lijst met beschik bare functies raadpleegt u de [referentie gegevens stroom taal toewijzen](data-flow-expression-functions.md).

#### <a name="address-array-indexes"></a>Matrix indexen van adres

Gebruik vier Kante haken ([]) om toegang te krijgen tot een specifiek element bij het verwerken van kolommen of functies die matrix typen retour neren. Als de index niet bestaat, wordt de expressie geëvalueerd in NULL.

![Expression Builder-matrix](media/data-flow/expression-array.png "Voor beeld van expressie gegevens")

> [!IMPORTANT]
> Bij het toewijzen van gegevens stromen zijn matrices op één gebaseerd, wat betekent dat het eerste element door index One wordt verwezen. MyArray [1] heeft bijvoorbeeld toegang tot het eerste element van een matrix met de naam ' myArray '.

### <a name="input-schema"></a>Invoer schema

Als uw gegevens stroom gebruikmaakt van een gedefinieerd schema in een van de bronnen, kunt u in veel expressies naar een kolom verwijzen met een naam. Als u schema-drift gebruikt, kunt u met behulp van de `byName()` functies of `byNames()` met behulp van kolom patronen expliciet verwijzen naar kolommen.

#### <a name="column-names-with-special-characters"></a>Kolom namen met speciale tekens

Wanneer u kolom namen hebt die speciale tekens of spaties bevatten, plaatst u de naam tussen accolades om ernaar te verwijzen in een expressie.

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>Parameters

Para meters zijn waarden die tijdens runtime worden door gegeven aan een gegevens stroom vanuit een pijp lijn. Als u wilt verwijzen naar een para meter, klikt u op de para meter in de weer gave **expressie elementen** of verwijst u ernaar met een dollar teken voor de naam. Er wordt bijvoorbeeld naar een para meter met de naam parameter1 verwezen door `$parameter1` . Zie [parameterizing mapping data flows](parameters-data-flow.md)(Engelstalig) voor meer informatie.

### <a name="cached-lookup"></a>In de cache opgeslagen zoekopdrachten

Met een opzoek bewerking in cache kunt u een inline-lookup uitvoeren van de uitvoer van een sink in de cache. Er zijn twee functies beschikbaar voor gebruik op elke sink, `lookup()` en `outputs()` . De syntaxis om te verwijzen naar deze functies is `cacheSinkName#functionName()` . Zie [cache-sinks](data-flow-sink.md#cache-sink)voor meer informatie.

`lookup()` neemt de overeenkomende kolommen in de huidige trans formatie als para meters en retourneert een complexe kolom die gelijk is aan de rij die overeenkomt met de sleutel kolommen in de cache-sink. De geretourneerde complexe kolom bevat een subkolom voor elke kolom die is toegewezen in de cache-sink. Als er bijvoorbeeld een fout code-Sink voor de cache `errorCodeCache` met een sleutel kolom is die overeenkomt met de code en een kolom met de naam `Message` . Aanroepen `errorCodeCache#lookup(errorCode).Message` zou het bericht retour neren dat overeenkomt met de door gegeven code. 

`outputs()` accepteert geen para meters en retourneert de volledige cache-Sink als een matrix met complexe kolommen. Dit kan niet worden aangeroepen als sleutel kolommen worden opgegeven in de Sink en alleen worden gebruikt als de cache-Sink een klein aantal rijen bevat. Bij een veelvoorkomende use-case wordt de maximum waarde van een incrementele sleutel toegevoegd. Als een enkele geaggregeerde rij met een cache `CacheMaxKey` een kolom bevat `MaxKey` , kunt u verwijzen naar de eerste waarde door aan te roepen `CacheMaxKey#outputs()[1].MaxKey` .

![In de cache opgeslagen zoekopdrachten](media/data-flow/cached-lookup-example.png "In de cache opgeslagen zoekopdrachten")

### <a name="locals"></a>Locals

Als u logica deelt over meerdere kolommen of als u uw logica wilt compartimenteren, kunt u een lokale maken binnen een afgeleide column\. Als u wilt verwijzen naar een lokaal, klikt u op de lokale weer gave van de **expressie-elementen** of verwijst u hiernaar met een dubbele punt vóór de naam. Er wordt bijvoorbeeld verwezen naar een lokale, met de naam local1 `:local1` . Meer informatie over lokalen vindt u in de [documentatie over afgeleide kolommen](data-flow-derived-column.md#locals).

## <a name="preview-expression-results"></a>Preview-expressie resultaten

Als de [foutopsporingsmodus](concepts-data-flow-debug-mode.md) is ingeschakeld, kunt u interactief gebruikmaken van het cluster voor fout opsporing om te zien wat uw expressie evalueert. Selecteer **vernieuwen** naast voor beeld van gegevens om de resultaten van de preview van de gegevens bij te werken. U kunt de uitvoer van elke rij weer geven op basis van de invoer kolommen.

![Preview in uitvoering](media/data-flow/preview-expression.png "Voor beeld van expressie gegevens")

## <a name="string-interpolation"></a>Interpolatie van teken reeksen

Bij het maken van lange teken reeksen die expressie-elementen gebruiken, gebruikt u interpolatie van teken reeksen om eenvoudig complexe teken reeks logica te bouwen. Met de interpolatie van teken reeksen voor komt u dat de teken reeks samen voegen uitgebreid wordt gebruikt wanneer para meters zijn opgenomen in de query teken reeks. Gebruik dubbele aanhalings tekens om letterlijke teken reeks tekst samen met expressies te sluiten. U kunt expressie functies, kolommen en para meters toevoegen. Als u de syntaxis van de expressie wilt gebruiken, plaatst u deze tussen accolades,

Enkele voor beelden van interpolatie van teken reeksen:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

> [!NOTE]
> Wanneer u de syntaxis van de teken reeks interpolatie in SQL-bron query's gebruikt, moet de query reeks op één regel staan, zonder/n.

## <a name="commenting-expressions"></a>Expressies voor opmerkingen

Voeg opmerkingen toe aan uw expressies door gebruik te maken van de syntaxis van een meerregelige opmerking met één regel.

De volgende voor beelden zijn geldige opmerkingen:

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

Als u een opmerking boven aan de expressie plaatst, wordt deze weer gegeven in het tekstvak trans formatie om uw transformatie expressies te documenteren.

![Opmerking in het tekstvak trans formatie](media/data-flow/comment-expression.png "Opmerkingen")

## <a name="regular-expressions"></a>Reguliere expressies

Veel expressie taal functies gebruiken reguliere expressie syntaxis. Wanneer u reguliere-expressie functies gebruikt, probeert Expression Builder een back slash ( \\ ) als escape-teken reeks te interpreteren. Wanneer u backslashes in uw reguliere expressie gebruikt, moet u de volledige regex insluiten in accents graves ( \` ) of een dubbele back slash gebruiken.

Een voor beeld waarin accents graves wordt gebruikt:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Een voor beeld waarbij gebruik wordt gemaakt van dubbele slashes:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>Sneltoetsen

Hieronder vindt u een lijst met snelkoppelingen die beschikbaar zijn in de opbouw functie voor expressies. De meeste IntelliSense-snelkoppelingen zijn beschikbaar bij het maken van expressies.

* CTRL + K CTRL + C: de volledige regel opmerking.
* CTRL + K CTRL + U: opmerkingen opheffen.
* F1: Help-opdrachten voor de editor opgeven.
* Alt + pijl-omlaag-toets: huidige regel omlaag verplaatsen.
* Alt + pijl-omhoog-toets: huidige regel omhoog verplaatsen.
* Ctrl + spatie balk: context Help weer geven.

## <a name="commonly-used-expressions"></a>Veelgebruikte expressies

### <a name="convert-to-dates-or-timestamps"></a>Converteren naar datums of tijds tempels

Als u letterlijke teken reeksen wilt toevoegen aan de uitvoer van de tijds tempel, zet u de conversie in ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Als u milliseconden wilt converteren van epoche naar een datum of tijds tempel, gebruikt u `toTimestamp(<number of milliseconds>)` . Als de tijd in seconden is, vermenigvuldigt u met 1.000.

```toTimestamp(1574127407*1000l)```

De eerste ' l ' aan het einde van de vorige expressie geeft een conversie naar een lang type als inline syntaxis.

### <a name="find-time-from-epoch-or-unix-time"></a>Zoek tijd van epoche of Unix-tijd

toLong (currentTimestamp ()-toTimestamp (' 1970-01-01 00:00:00.000 ', ' JJJJ-MM-DD uu: mm: SS. SSS) * 1000

## <a name="next-steps"></a>Volgende stappen

[Beginnen met het bouwen van gegevens transformatie expressies](data-flow-expression-functions.md)
