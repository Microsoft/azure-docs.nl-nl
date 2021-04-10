---
title: Aantal blobs en grootte berekenen met behulp van Azure Storage-inventaris
description: Meer informatie over het berekenen van het aantal en de totale grootte van blobs per container.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: d1aa91ea0f698e609e786d87a0072e6a07c143a3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047314"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Aantal blobs en totale grootte per container berekenen met behulp van Azure Storage-inventarisatie

In dit artikel wordt gebruikgemaakt van de Azure Blob Storage Inventory functie en Azure Synapse om het aantal blobs en de totale grootte van blobs per container te berekenen. Deze waarden zijn handig wanneer u het BLOB-gebruik per container optimaliseert.

BLOB-meta gegevens zijn niet opgenomen in deze methode. De functie Azure Blob Storage Inventory gebruikt de [lijst-blobs](/rest/api/storageservices/list-blobs) rest API met de standaard parameters. Het voor beeld biedt geen ondersteuning voor moment opnamen, $-containers, enzovoort.

> [!IMPORTANT]
> De BLOB-inventarisatie is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of nog niet beschikbaar zijn.

## <a name="enable-inventory-reports"></a>Inventarisatie rapporten inschakelen

De eerste stap in deze methode is om [inventaris rapporten in te scha kelen](blob-inventory.md#enable-inventory-reports) voor uw opslag account. Het kan zijn dat u Maxi maal 24 uur moet wachten na het inschakelen van inventarisatie rapporten voor uw eerste rapport dat moet worden gegenereerd.

Wanneer u een inventaris rapport hebt om te analyseren, verleent u de BLOB Lees toegang tot de container waar het CSV-bestand van het rapport zich bevindt.

1. Ga naar de container met het rapport bestand inventarisatie CSV.
1. Selecteer **Access Control (IAM)** en **Voeg roltoewijzingen toe**

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Selecteer roltoewijzingen toevoegen":::

1. Selecteer **opslag-BLOB-gegevens lezer** in de vervolg keuzelijst met **rollen** .

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="De rol van BLOB-gegevens lezer voor opslag toevoegen vanuit de vervolg keuzelijst":::

1. Voer het e-mail adres in van het account dat u gebruikt om het rapport uit te voeren in het veld **selecteren** .

## <a name="create-an-azure-synapse-workspace"></a>Een Azure Synapse-werkruimte maken

Maak vervolgens [een Azure Synapse-werk ruimte](../../synapse-analytics/get-started-create-workspace.md) waar u een SQL-query gaat uitvoeren om de inventarisatie resultaten te rapporteren.

## <a name="create-the-sql-query"></a>De SQL-query maken

Nadat u de Azure Synapse-werk ruimte hebt gemaakt, voert u de volgende stappen uit.

1. Navigeer naar [https://web.azuresynapse.net](https://web.azuresynapse.net) .
1. Selecteer het tabblad **ontwikkelen** aan de linkerkant.
1. Selecteer het grote plus teken (+) om een item toe te voegen.
1. Selecteer **SQL-script**.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="SQL-script selecteren om een nieuwe query te maken":::

## <a name="run-the-sql-query"></a>De SQL-query uitvoeren

1. Voeg de volgende SQL-query toe aan de Azure Synapse-werk ruimte om [het inventarisatie CSV-bestand te lezen](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file).

    Voor de `bulk` para meter gebruikt u de URL van het CSV-bestand van het inventarisatie rapport dat u wilt analyseren.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Geef een naam op voor uw SQL-query in het deel venster Eigenschappen aan de rechter kant.

1. Publiceer uw SQL-query door op CTRL + S te drukken of de knop **Alles publiceren** te selecteren.

1. Selecteer de knop uitvoeren om de SQL-query **uit** te voeren. Het aantal blobs en de totale grootte per container worden gerapporteerd in het deel venster met **resultaten** .

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Uitvoer van het uitvoeren van het script om het aantal blobs en de totale grootte te berekenen.":::

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage BLOB-inventaris gebruiken voor het beheren van BLOB-gegevens](blob-inventory.md)
- [De totale grootte van een blobcontainer berekenen voor facturering](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)