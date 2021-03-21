---
title: Herhaal bare kopie in Azure Data Factory
description: Meer informatie over het voor komen van duplicaten, zelfs als een segment dat de gegevens kopieert, meer dan één keer wordt uitgevoerd.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c427cd90412121e896738ca43f4c66dd24b096dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387084"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Herhaal bare kopie in Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Herhaal bare Lees bewerking van relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevens archieven de Herhaal baarheid in de hand om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment op een van beide manieren opnieuw wordt uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment wordt gestart.  
 
> [!NOTE]
> De volgende voor beelden zijn voor Azure SQL, maar zijn van toepassing op alle gegevens archieven die ondersteuning bieden voor rechthoekige data sets. Mogelijk moet u het **type** bron en de **query** -eigenschap (bijvoorbeeld: query in plaats van sqlReaderQuery) aanpassen voor het gegevens archief.   

Normaal gesp roken wilt u bij het lezen van relationele winkels alleen de gegevens lezen die overeenkomen met dat segment. Een manier om dit te doen is met behulp van de systeem variabelen WindowStart en WindowEnd die beschikbaar zijn in Azure Data Factory. Meer informatie over de variabelen en functies in Azure Data Factory hier vindt u in het artikel [Azure Data Factory functies en systeem variabelen](data-factory-functions-variables.md) . Voorbeeld: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Met deze query worden gegevens gelezen die in het segment duur bereik (WindowStart-> WindowEnd) vallen van de tabel MyTable. Als u dit segment opnieuw wilt uitvoeren, moet u er ook voor zorgen dat dezelfde gegevens worden gelezen. 

In andere gevallen kunt u de hele tabel lezen en kan de sqlReaderQuery als volgt definiëren:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Herhaal bare schrijf bewerkingen naar SqlSink
Bij het kopiëren van gegevens naar **Azure SQL/SQL Server** van andere gegevens archieven, moet u de Herhaal baarheid blijven gebruiken om onbedoelde resultaten te voor komen. 

Bij het kopiëren van gegevens naar Azure SQL/SQL Server Data Base, voegt de Kopieer activiteit standaard gegevens toe aan de Sink-tabel. Stel dat u gegevens kopieert uit een CSV-bestand (Comma-Separated Values) met twee records naar de volgende tabel in een Azure SQL/SQL Server-Data Base. Wanneer een segment wordt uitgevoerd, worden de twee records gekopieerd naar de SQL-tabel. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Stel dat u fouten in het bron bestand hebt gevonden en de hoeveelheid van de omlaagbalken hebt bijgewerkt van 2 tot 4. Als u het gegevens segment voor die periode hand matig opnieuw moet uitvoeren, vindt u twee nieuwe records die zijn toegevoegd aan Azure SQL/SQL Server Data Base. In dit voor beeld wordt ervan uitgegaan dat geen van de kolommen in de tabel de beperking Primary Key heeft.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Om dit gedrag te voor komen, moet u UPSERT-semantiek opgeven met een van de volgende twee mechanismen:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mechanisme 1: sqlWriterCleanupScript gebruiken
U kunt de eigenschap **sqlWriterCleanupScript** gebruiken om gegevens uit de Sink-tabel op te schonen voordat u de gegevens invoegt wanneer een segment wordt uitgevoerd. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Wanneer een segment wordt uitgevoerd, wordt het opschoon script eerst uitgevoerd om gegevens te verwijderen die overeenkomen met het segment uit de SQL-tabel. Met de Kopieer activiteit worden gegevens vervolgens ingevoegd in de SQL-tabel. Als het segment opnieuw wordt uitgevoerd, wordt de hoeveelheid naar wens bijgewerkt.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Stel dat de record Flat Washer is verwijderd uit het oorspronkelijke csv-bestand. Vervolgens wordt het volgende resultaat gegenereerd: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

De kopieeractiviteit heeft het opschoningsscript uitgevoerd om de bijbehorende gegevens voor dat segment te verwijderen. Vervolgens wordt de invoer gelezen uit het CSV-bestand (dat vervolgens slechts één record bevat) en ingevoegd in de tabel. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mechanisme 2: sliceIdentifierColumnName gebruiken
> [!IMPORTANT]
> SliceIdentifierColumnName wordt momenteel niet ondersteund voor Azure Synapse Analytics. 

Het tweede mechanisme om Herhaal baarheid te bereiken, is door een toegewezen kolom (sliceIdentifierColumnName) te hebben in de doel tabel. Deze kolom wordt gebruikt door Azure Data Factory om ervoor te zorgen dat de bron en bestemming gesynchroniseerd blijven. Deze benadering werkt wanneer er flexibiliteit is in het wijzigen of definiëren van het doelschema van de SQL-tabel. 

Deze kolom wordt door Azure Data Factory gebruikt voor Herhaal bare doel einden en in het proces Azure Data Factory worden geen schema wijzigingen aangebracht in de tabel. Manier om deze aanpak te gebruiken:

1. Een kolom van het type **binary (32)** in de doel-SQL-tabel definiëren. Er mogen geen beperkingen zijn voor deze kolom. We noemen deze kolom als AdfSliceIdentifier voor dit voor beeld.


    Bron tabel:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Doel tabel: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Gebruik die als volgt in de kopieeractiviteit:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory vult deze kolom in aan de hand van de nood zaak om ervoor te zorgen dat de bron en de bestemming synchroon blijven. De waarden van deze kolom mogen niet buiten deze context worden gebruikt. 

Net als bij mechanisme 1 worden met de Kopieer activiteit automatisch de gegevens voor het opgegeven segment uit de doel-SQL-tabel opgeschoond. Vervolgens worden gegevens uit de bron in toegevoegd aan de doel tabel. 

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende connector artikelen voor volledige JSON-voor beelden: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
