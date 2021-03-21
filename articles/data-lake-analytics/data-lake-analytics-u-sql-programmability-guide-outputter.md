---
title: Door de gebruiker gedefinieerde U-SQL-hand leiding voor de Programmeer bare outputter voor Azure Data Lake
description: Meer informatie over de door de gebruiker gedefinieerde U-SQL UDO-programmeer gids.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512569"
---
# <a name="use-user-defined-outputter"></a>Door de gebruiker gedefinieerde uitvoerder gebruiken

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL UDO: door de gebruiker gedefinieerde outputter
De door de gebruiker gedefinieerde outputter is een andere U-SQL-UDO waarmee u ingebouwde U-SQL-functionaliteit kunt uitbreiden. Net als bij het Extractor zijn er verschillende ingebouwde outputten.

* *Outputters. Text ()*: schrijft gegevens naar tekst bestanden met scheidings tekens van verschillende code ringen.
* *Outputters.Csv ()*: schrijft gegevens naar CSV-bestanden (door komma's gescheiden waarden) van verschillende code ringen.
* *Outputters. TSV ()*: schrijft gegevens naar bestanden met door tabs gescheiden waarden (TSV) van verschillende code ringen.

Met aangepaste outputter kunt u gegevens in een aangepaste, gedefinieerde indeling schrijven. Dit kan handig zijn voor de volgende taken:

* Het schrijven van gegevens naar semi-gestructureerde of ongestructureerde bestanden.
* Het schrijven van gegevens wordt niet ondersteund door code ring.
* Het wijzigen van uitvoer gegevens of het toevoegen van aangepaste kenmerken.

## <a name="how-to-define-and-use-user-defined-outputter"></a>Door de gebruiker gedefinieerde outputter definiëren en gebruiken
Voor het definiëren van een door de gebruiker gedefinieerde outputter moeten we de `IOutputter` interface maken.

Hieronder volgt de implementatie van de basis `IOutputter` klasse:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Alle invoer parameters voor de outputter, zoals kolom/rij-scheidings tekens, code ring, enzovoort, moeten worden gedefinieerd in de constructor van de-klasse. De `IOutputter` interface moet ook een definitie voor `void Output` override bevatten. Het kenmerk `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` kan eventueel worden ingesteld voor de verwerking van atomische bestanden. Zie de volgende Details voor meer informatie.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` wordt aangeroepen voor elke invoercel. Retourneert de `IUnstructuredWriter output` rijenset.
* De constructor-klasse wordt gebruikt om para meters door te geven aan de door de gebruiker gedefinieerde outputter.
* `Close` wordt gebruikt om de dure status vrij te geven of te bepalen wanneer de laatste rij is geschreven.

**SqlUserDefinedOutputter** kenmerk geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde outputter. Deze klasse kan niet worden overgenomen.

SqlUserDefinedOutputter is een optioneel kenmerk voor een definitie van een door de gebruiker gedefinieerde outputter. Deze wordt gebruikt om de eigenschap AtomicFileProcessing te definiëren.

* BOOL AtomicFileProcessing   

* **True** = geeft aan dat deze outputter atomische uitvoer bestanden vereist (JSON, XML,...)
* **False** = geeft aan dat deze outputter kan omgaan met gesplitste/gedistribueerde bestanden (CSV, seq,...)

De belangrijkste Programmeer bare objecten zijn **rij** en **uitvoer**. Het object **Row** wordt gebruikt voor het opsommen van uitvoer gegevens als `IRow` interface. **Uitvoer** wordt gebruikt om uitvoer gegevens naar het doel bestand in te stellen.

De uitvoer gegevens worden via de `IRow` interface geopend. Er wordt een rij per keer door gegeven aan de uitvoer gegevens.

De afzonderlijke waarden worden opgesomd door de Get-methode van de IRow-interface aan te roepen:

```csharp
row.Get<string>("column_name")
```

Afzonderlijke kolom namen kunnen worden bepaald door het aanroepen van `row.Schema` :

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Met deze aanpak kunt u een flexibele outputter voor elk meta gegevens schema maken.

De uitvoer gegevens worden naar het bestand geschreven met behulp van `System.IO.StreamWriter` . De stream-para meter is ingesteld `output.BaseStream` als onderdeel van `IUnstructuredWriter output` .

Houd er rekening mee dat het belang rijk is om de gegevens buffer te leegmaken naar het bestand na elke rij herhaling. Daarnaast `StreamWriter` moet het object worden gebruikt met het kenmerk wegwerp ingeschakeld (standaard) en met het sleutel woord **using** :

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Als dat niet het geval is, roept u de methode Flush () expliciet na elke iteratie. Dit wordt in het volgende voor beeld weer gegeven.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Kop-en voet teksten instellen voor door de gebruiker gedefinieerde outputter
Als u een koptekst wilt instellen, gebruikt u de stroom voor het uitvoeren van één iteratie.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

De code in het eerste `if (isHeaderRow)` blok wordt slechts één keer uitgevoerd.

Gebruik de verwijzing naar het exemplaar van object () voor de voet tekst `System.IO.Stream` `output.BaseStream` . Schrijf de voet tekst in de methode Close () van de `IOutputter` interface.  (Zie het volgende voor beeld voor meer informatie.)

Hier volgt een voor beeld van een door de gebruiker gedefinieerde outputter:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

En U-SQL base-script:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Dit is een HTML-outputter waarmee een HTML-bestand met tabel gegevens wordt gemaakt.

### <a name="call-outputter-from-u-sql-base-script"></a>Outputter aanroepen vanuit het U-SQL-basis script
Als u een aangepaste outputter wilt aanroepen vanuit het basis-U-SQL-script, moet u het nieuwe exemplaar van het outputter-object maken.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Om te voor komen dat u een exemplaar van het object maakt in het basis script, kunt u een functie-wrapper maken, zoals wordt weer gegeven in het vorige voor beeld:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

In dit geval ziet de oorspronkelijke aanroep er als volgt uit:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>Volgende stappen
* [Programmeer handleiding U-SQL-overzicht](data-lake-analytics-u-sql-programmability-guide.md)
* [Programmeer handleiding voor U-SQL-UDT en UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)