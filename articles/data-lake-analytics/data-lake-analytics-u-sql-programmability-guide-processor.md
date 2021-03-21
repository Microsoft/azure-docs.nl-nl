---
title: Door de gebruiker gedefinieerde U-SQL-hand leiding voor de programmeer baarheid van de processor voor Azure Data Lake
description: 'Meer informatie over de U-SQL UDO Programmeer bare hand leiding: door de gebruiker gedefinieerde processor.'
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512596"
---
# <a name="use-user-defined-processor"></a>Door de gebruiker gedefinieerde processor gebruiken

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL UDO: door de gebruiker gedefinieerde processor
Een door de gebruiker gedefinieerde processor of UDP is een type U-SQL-UDO waarmee u de binnenkomende rijen kunt verwerken door programmeer functies toe te passen. Met UDP kunt u kolommen combi neren, waarden wijzigen en nieuwe kolommen toevoegen, indien nodig. In principe kunt u een rijenset verwerken om vereiste gegevens elementen te maken.

## <a name="how-to-define-and-use-user-defined-processor"></a>Een door de gebruiker gedefinieerde processor definiëren en gebruiken
Als u een UDP wilt definiëren, moet u een `IProcessor` interface met het `SqlUserDefinedProcessor` kenmerk maken. Dit is optioneel voor UDP.

Deze interface moet de definitie bevatten voor de `IRow` onderdrukking van de interface-Rijset, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde processor. Deze klasse kan niet worden overgenomen.

Het kenmerk SqlUserDefinedProcessor is **optioneel** voor de UDP-definitie.

De belangrijkste Programmeer bare objecten zijn **invoer** en **uitvoer**. Het invoer object wordt gebruikt voor het opsommen van invoer kolommen en uitvoer en voor het instellen van uitvoer gegevens als resultaat van de processor activiteit.

Voor de inventarisatie van invoer kolommen gebruiken we de- `input.Get` methode.

```csharp
string column_name = input.Get<string>("column_name");
```

De para meter voor de `input.Get` methode is een kolom die wordt door gegeven als onderdeel van de component van de `PRODUCE` `PROCESS` -instructie van het U-SQL-basis script. Het juiste gegevens type moet hier worden gebruikt.

Gebruik de-methode voor uitvoer `output.Set` .

Het is belang rijk te weten dat de aangepaste producent alleen kolommen en waarden uitvoert die zijn gedefinieerd met de aanroep van de `output.Set` methode.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

De werkelijke processor uitvoer wordt geactiveerd door aan te roepen `return output.AsReadOnly();` .

Hier volgt een voor beeld van een processor:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

In dit scenario wordt een nieuwe kolom met de naam ' full_description ' gegenereerd door de bestaande kolommen te combi neren, in dit geval ' gebruiker ' in hoofd letters en ' des '. Er wordt ook een GUID opnieuw gegenereerd en de oorspronkelijke en nieuwe GUID-waarden worden geretourneerd.

Zoals u kunt zien in het vorige voor beeld, kunt u C#-methoden aanroepen tijdens `output.Set` methode aanroep.

Hieronder volgt een voor beeld van een base U-SQL-script dat gebruikmaakt van een aangepaste processor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Volgende stappen
* [Programmeer handleiding U-SQL-overzicht](data-lake-analytics-u-sql-programmability-guide.md)
* [Programmeer handleiding voor U-SQL-UDT en UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)