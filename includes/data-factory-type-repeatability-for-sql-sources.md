---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 9447cec55c53861ca57d5416a91ffefd35fdd20b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91571850"
---
## <a name="repeatability-during-copy"></a>Herhaalbaarheid tijdens kopiëren
Wanneer u gegevens naar Azure SQL/SQL Server kopieert vanuit andere gegevensopslagen, moet u denk aan herhaalbaarheid om onbedoelde resultaten te voorkomen. 

Wanneer u gegevens kopieer naar Azure SQL/SQL Server Database, wordt de gegevensset door de kopieeractiviteit standaard toegevoegd aan de sinktabel. Wanneer u bijvoorbeeld gegevens kopieert van een CSV-bestand met twee records naar Azure SQL/SQL Server Database, ziet de tabel er as volgt uit:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Stel dat u fouten hebt gevonden in het bronbestand en de hoeveelheid van Down Tube hebt bijgewerkt van 2 naar 4 in het bronbestand. Als u het gegevenssegment voor die periode opnieuw uitvoert, ziet u dat er twee nieuwe records zijn toegevoegd aan Azure SQL/SQL Server Database. In het onderstaande wordt ervan uitgegaan dat de kolommen in de tabel de primaire sleutelbeperking hebben.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Om dit te voorkomen, moet u specifieke upsert-semantiek opgeven door een van de twee onderstaande mechanismen te gebruiken.

> [!NOTE]
> Een segment kan automatisch opnieuw worden uitgevoerd in Azure Data Factory, zoals in het beleid voor opnieuw proberen staat opgegeven.
> 
> 

### <a name="mechanism-1"></a>Mechanisme 1
U kunt de eigenschap **sqlWriterCleanupScript** gebruiken om eerst een schoonmaakactie uit te voeren wanneer een segment is uitgevoerd. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Het opschoningsscript wordt eerst tijdens het kopiëren van een bepaald segment uitgevoerd, waardoor de gegevens van de SQL-tabel die bij dat segment hoort, worden verwijderd. De activiteit voert daarna de gegevens in de SQL-tabel in. 

Als het segment opnieuw wordt uitgevoerd, ziet u dat de hoeveelheid naar wens is bijgewerkt.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Stel dat de record Flat Washer is verwijderd uit het oorspronkelijke csv-bestand. Als u het segment vervolgens opnieuw uitvoert, ziet u het volgende resultaat: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Niets nieuws moest worden uitgevoerd. De kopieeractiviteit heeft het opschoningsscript uitgevoerd om de bijbehorende gegevens voor dat segment te verwijderen. Vervolgens wordt de invoer uit de csv gelezen (die toen 1 record bevatte) en ingevoerd in de tabel. 

### <a name="mechanism-2"></a>Mechanisme 2
> [!IMPORTANT]
> sliceIdentifierColumnName wordt momenteel niet ondersteund voor Azure Synapse Analytics. 

Een ander mechanisme voor herhaalbaarheid is om een toegewezen kolom (**sliceIdentifierColumnName**) op te nemen in de doeltabel. Deze kolom wordt gebruikt door Azure Data Factory om ervoor te zorgen dat de bron en bestemming gesynchroniseerd blijven. Deze benadering werkt wanneer er flexibiliteit is in het wijzigen of definiëren van het doelschema van de SQL-tabel. 

Deze kolom wordt gebruikt door Azure Data Factory voor herhaalbaarheidsdoeleinden. In het proces maakt Azure Data Factory geen schemawijzigingen in de tabel. Manier om deze aanpak te gebruiken:

1. Definieer een kolom van een binair type (32) in de SQL-doeltabel. Er mogen geen beperkingen zijn voor deze kolom. We noemen deze tabel 'ColumnForADFuseOnly' voor dit voorbeeld.
2. Gebruik die als volgt in de kopieeractiviteit:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory vult deze kolom in om ervoor te zorgen dat de bron en bestemming gesynchroniseerd blijven. De waarden van deze kolom mogen niet buiten deze context door de gebruiker worden gebruikt. 

Net als bij mechanisme 1 schoont de kopieeractiviteit eerst de gegevens op voor een bepaald segment uit de SQL-doeltabel en vervolgens wordt de kopieeractiviteit normaal uitgevoerd om de gegevens van de bron naar de bestemming te kopiëren. 

