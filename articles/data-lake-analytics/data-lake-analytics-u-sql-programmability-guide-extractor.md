---
title: Door de gebruiker gedefinieerde U-SQL-programmeer handleiding voor de hand leiding voor Azure Data Lake
description: 'Meer informatie over de U-SQL UDO Programmeer bare hand leiding: door de gebruiker gedefinieerde extractor.'
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: ad7f6336753903533771033de21aec8262425a61
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608014"
---
# <a name="use-user-defined-extractor"></a>Door de gebruiker gedefinieerde extractor gebruiken

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL UDO: door de gebruiker gedefinieerde Extractor
U kunt met behulp van een extractie-instructie externe gegevens importeren met u-SQL. Een instructie EXTRACT kan ingebouwde UDO-extracten gebruiken:  

* *Extracters. Text ()*: levert extra heren uit tekst bestanden met scheidings tekens van verschillende code ringen.

* *Extractors.Csv ()*: biedt extra heren van bestanden met door komma's gescheiden waarden (CSV) van verschillende code ringen.

* *Extracters. TSV ()*: biedt extractie van bestanden met door tabs gescheiden waarden (TSV) van verschillende code ringen.

Het kan handig zijn om een aangepaste Extractor te ontwikkelen. Dit kan handig zijn bij het importeren van gegevens als u een van de volgende taken wilt uitvoeren:

* Wijzig de invoer gegevens door kolommen te splitsen en afzonderlijke waarden te wijzigen. De PROCESSOR functionaliteit is beter voor het combi neren van kolommen.
* Niet-gestructureerde gegevens, zoals webpagina's en e-mail berichten of semi-gestructureerde gegevens, zoals XML/JSON parseren.
* Gegevens parseren in niet-ondersteunde code ring.

## <a name="how-to-define-and-use-user-defined-extractor"></a>Door de gebruiker gedefinieerde Extractor definiëren en gebruiken
Als u een door de gebruiker gedefinieerde Extractor of USIEF wilt definiëren, moet u een `IExtractor` interface maken. Alle invoer parameters voor het Extractor, zoals kolom/rij scheidings tekens en code ring, moeten worden gedefinieerd in de constructor van de-klasse. De `IExtractor`  interface moet ook een definitie voor de `IEnumerable<IRow>` onderdrukking bevatten:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Het kenmerk **SqlUserDefinedExtractor** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerd extractor. Deze klasse kan niet worden overgenomen.

SqlUserDefinedExtractor is een optioneel kenmerk voor de definitie van USIEF. Hiermee wordt de eigenschap AtomicFileProcessing voor het object USIEF gedefinieerd.

* BOOL AtomicFileProcessing   

* **True** = geeft aan dat voor deze extractie atomische invoer bestanden (JSON, XML,...) zijn vereist
* **False** = geeft aan dat deze extractie kan omgaan met gesplitste/gedistribueerde bestanden (CSV, seq,...)

De belangrijkste USIEF-Programmeer bare objecten zijn **invoer** en **uitvoer**. Het invoer object wordt gebruikt voor het opsommen van invoer gegevens als `IUnstructuredReader` . Het uitvoer object wordt gebruikt om uitvoer gegevens als resultaat van de extractor-activiteit in te stellen.

De invoer gegevens worden geopend via `System.IO.Stream` en `System.IO.StreamReader` .

Voor de inventarisatie van invoer kolommen wordt de invoer stroom eerst gesplitst met behulp van een scheidings teken in een rij.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Daarna kunt u de invoer rij verder splitsen in kolom onderdelen.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Voor het instellen van uitvoer gegevens gebruiken we de- `output.Set` methode.

Het is belang rijk om te begrijpen dat de aangepaste Extractor alleen kolommen en waarden uitvoert die met de uitvoer zijn gedefinieerd. Methode aanroep instellen.

```csharp
output.Set<string>(count, part);
```

De daad werkelijke extractie uitvoer wordt geactiveerd door aan te roepen `yield return output.AsReadOnly();` .

Hieronder vindt u het extractor-voor beeld:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

In dit scenario gebruikt de extractor opnieuw de GUID voor de kolom GUID en worden de waarden van de kolom gebruiker geconverteerd naar hoofd letters. Aangepaste extracten kunnen complexere resultaten opleveren door invoer gegevens te parseren en te bewerken.

Hieronder volgt een basis-U-SQL-script dat gebruikmaakt van een aangepaste extractor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Volgende stappen
* [Programmeer handleiding U-SQL-overzicht](data-lake-analytics-u-sql-programmability-guide.md)
* [Programmeer handleiding voor U-SQL-UDT en UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)