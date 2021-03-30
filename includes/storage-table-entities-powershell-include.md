---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67175710"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Tabel entiteiten beheren

Nu u een tabel hebt, gaan we kijken naar het beheren van entiteiten of rijen in de tabel. 

Entiteiten kunnen Maxi maal 255 eigenschappen hebben, waaronder drie systeem eigenschappen: **PartitionKey**, **RowKey** en **Time Stamp**. U bent verantwoordelijk voor het invoegen en bijwerken van de waarden van **PartitionKey** en **RowKey**. De server beheert de waarde van **Time Stamp**, die niet kan worden gewijzigd. Samen met de **PartitionKey** en **RowKey** wordt de unieke identificatie van elke entiteit in een tabel.

* **PartitionKey**: bepaalt de partitie waarin de entiteit is opgeslagen.
* **RowKey**: identificeert de entiteit binnen de partitie uniek.

U kunt Maxi maal 252 aangepaste eigenschappen definiëren voor een entiteit. 

### <a name="add-table-entities"></a>Tabel entiteiten toevoegen

Entiteiten toevoegen aan een tabel met behulp van **add-AzTableRow**. In deze voor beelden wordt gebruikgemaakt van partitie sleutels met waarden en en de `partition1` `partition2` rij-sleutels die gelijk zijn aan de status afkortingen. De eigenschappen in elke entiteit zijn `username` en `userid` . 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Query uitvoeren op de tabel entiteiten

U kunt een query uitvoeren op de entiteiten in een tabel met behulp van de opdracht **Get-AzTableRow** .

> [!NOTE]
> De cmdlets **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName** en **Get-AzureStorageTableRowByCustomFilter** zijn afgeschaft en worden verwijderd in een toekomstige versie-update.

#### <a name="retrieve-all-entities"></a>Alle entiteiten ophalen

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Met deze opdracht worden resultaten opgeleverd die vergelijkbaar zijn met de volgende tabel:

| userid | gebruikersnaam | partitie | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA (consistentie en beschikbaarheid) |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Chris | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Entiteiten voor een specifieke partitie ophalen

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

De resultaten zien er ongeveer uit als in de volgende tabel:

| userid | gebruikersnaam | partitie | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA (consistentie en beschikbaarheid) |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Entiteiten ophalen voor een specifieke waarde in een specifieke kolom

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Met deze query wordt één record opgehaald.

|veld|waarde|
|----|----|
| userid | 1 |
| gebruikersnaam | Chris |
| PartitionKey | partition1 |
| RowKey      | CA (consistentie en beschikbaarheid) |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Entiteiten ophalen met een aangepast filter 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Met deze query wordt één record opgehaald.

|veld|waarde|
|----|----|
| userid | 1 |
| gebruikersnaam | Chris |
| PartitionKey | partition1 |
| RowKey      | CA (consistentie en beschikbaarheid) |

### <a name="updating-entities"></a>Entiteiten bijwerken 

Er zijn drie stappen voor het bijwerken van entiteiten. Haal eerst de entiteit op die u wilt wijzigen. Voer de wijziging vervolgens door. Ten derde voert u de wijziging door met behulp van **Update-AzTableRow**.

Werk de entiteit met username = ' Jessie ' bij om username = ' Jessie2 ' te hebben. In dit voor beeld ziet u ook een andere manier om een aangepast filter te maken met behulp van .NET-typen.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

De resultaten geven de Jessie2-record weer.

|veld|waarde|
|----|----|
| userid | 2 |
| gebruikersnaam | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Tabel entiteiten verwijderen

U kunt één entiteit of alle entiteiten in de tabel verwijderen.

#### <a name="deleting-one-entity"></a>Eén entiteit verwijderen

Als u één entiteit wilt verwijderen, moet u een verwijzing naar die entiteit ophalen en deze in **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Alle entiteiten in de tabel verwijderen

Als u alle entiteiten in de tabel wilt verwijderen, haalt u deze op en bewaart u de resultaten in de Remove-cmdlet. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
