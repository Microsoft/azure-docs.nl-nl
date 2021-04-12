---
title: Querysyntaxis voor grafieken zoeken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de zoek query syntaxis in Azure Machine Learning Designer om te zoeken naar knoop punten in de pipeline-grafiek.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259218"
---
# <a name="graph-search-query-syntax"></a>Querysyntaxis voor grafieken zoeken

In dit artikel vindt u meer informatie over de functie voor het zoeken in grafieken in Azure Machine Learning. 

Met de functie voor het zoeken in grafieken kunt u snel naar een knoop punt navigeren wanneer u fouten opspoort of een pijp lijn bouwt. U kunt het sleutel woord of de query in het invoervak in de werk balk of onder het tabblad Zoeken in het linkerdeel venster typen om de zoek actie te activeren. Alle overeenkomende resultaten worden gemarkeerd in het canvas geel en als u een resultaat in het linkerdeel venster selecteert, wordt het knoop punt in het canvas rood gemarkeerd.

![Scherm afbeelding van een voor beeld van een zoek ervaring in de grafiek](media/search/graph-search-0322.png)

De functie voor het zoeken in Zoek opdrachten in volledige tekst in de knooppunt naam en opmerkingen wordt ondersteund. U kunt ook filteren op eigenschap node, zoals runStatus, duration, computeTarget. Het tref woord zoeken is gebaseerd op de Lucene-query. Een volledige zoek opdracht ziet er als volgt uit:  

**[[Lucene-query] | [filter query]]** 

U kunt een lucene-query of filter query gebruiken. Als u beide wilt gebruiken, gebruikt u het **|** scheidings teken. De syntaxis van de filter query is strikter dan de Lucene-query. Dus als de invoer van de klant kan worden geparseerd, wordt de filter query toegepast.

`data OR model | compute in {cpucluster}`Dit is bijvoorbeeld het zoeken naar knoop punten met de naam of opmerking bevat `data` of `model` , en Compute is cpucluster.
 

## <a name="lucene-query"></a>Lucene-query

De functie voor het zoeken in grafieken maakt gebruik van een eenvoudige query voor het zoeken in volledige tekst in het knoop punt ' naam ' en ' Opmerking '. De volgende lucene-Opera tors worden ondersteund:

 
- EN/OF
- Joker tekens die overeenkomen met **?** en **\*** Opera tors.

### <a name="examples"></a>Voorbeelden

- Eenvoudige zoek opdracht: `JSON Data`

- EN/OF: `JSON AND Validation`

- Meerdere en/of: `(JSON AND Validation) OR (TSV AND Training)`

 
- Joker tekens zoeken: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> U kunt een lucene-query niet starten met het teken ' * '.

##  <a name="filter-query"></a>Query filteren

 
Filter query's gebruiken het volgende patroon:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
U kunt de volgende knooppunt eigenschappen gebruiken als sleutels:

- runStatus
- compute
- duur
- gebruikt
- publish
- tags

En gebruik de volgende Opera tors:

- Groter dan of gelijk aan: `>=`
- Kleiner of gelijk aan: `<=`
- Groter `>`
- Jonge `<`
- Waard `==`
- Bestaan `=`
- NotEqual `!=`
- Naast `in`

 
 

### <a name="example"></a>Voorbeeld

- duration > 100;
- status in {failed, NotStarted}
- compute in {GPU-cluster}; runStatus in {voltooid}

## <a name="technical-notes"></a>Technische opmerkingen

- De relatie tussen meerdere filters is "en"
- Als `>=,  >,  <, or  <=` is gekozen, worden waarden automatisch geconverteerd naar een numeriek type. Anders worden teken reeks typen gebruikt voor vergelijking.
- Voor alle waarden van het type teken reeks is hoofdletter gevoelig in vergelijking.
- De operator in verwacht een verzameling als waarde, de syntaxis van de verzameling is `{name1, name2, name3}`
- De ruimte wordt genegeerd tussen tref woorden