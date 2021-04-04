---
title: De eigenschap van de laatste synchronisatie tijd voor een opslag account controleren
titleSuffix: Azure Storage
description: Meer informatie over het controleren van de laatste synchronisatie tijd van een opslag account met geo-replicatie. De eigenschap laatste synchronisatie tijd geeft de laatste tijd aan waarop alle schrijf bewerkingen van de primaire regio zijn geschreven naar de secundaire regio.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 242700c05053aa9d07e3a561a21986c8451a46c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91612441"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>De eigenschap van de laatste synchronisatie tijd voor een opslag account controleren

Wanneer u een opslag account configureert, kunt u opgeven dat uw gegevens worden gekopieerd naar een secundaire regio die honderden kilo meters van de primaire regio is. Geo-replicatie biedt duurzaamheid voor uw gegevens in het geval van een aanzienlijke storing in de primaire regio, zoals een natuur ramp. Als u lees toegang tot de secundaire regio uitschakelt, blijven de gegevens beschikbaar voor lees bewerkingen als de primaire regio niet beschikbaar is. U kunt uw toepassing zo ontwerpen dat deze naadloos kan worden gelezen vanuit de secundaire regio als de primaire regio niet meer reageert.

Geografisch redundante opslag (GRS) en geo-zone-redundante opslag (GZRS) repliceren uw gegevens asynchroon naar een secundaire regio. Voor lees toegang tot de secundaire regio schakelt u geografisch redundante opslag met lees toegang (RA-GRS) of geo-zone-redundante opslag met lees toegang (RA-GZRS) in. Zie [Azure Storage redundantie](storage-redundancy.md)voor meer informatie over de verschillende opties voor redundantie die worden geboden door Azure Storage.

In dit artikel wordt beschreven hoe u de **laatste synchronisatie tijd** -eigenschap voor uw opslag account controleert, zodat u eventuele discrepanties tussen de primaire en secundaire regio's kunt evalueren.

## <a name="about-the-last-sync-time-property"></a>Over de laatste synchronisatie tijd eigenschap

Omdat geo-replicatie asynchroon is, is het mogelijk dat de gegevens die naar de primaire regio worden geschreven, nog niet zijn geschreven naar de secundaire regio op het moment dat er een storing optreedt. De **laatste synchronisatie tijd** eigenschap geeft de laatste keer aan dat de gegevens van de primaire regio zijn geschreven naar de secundaire regio. Alle schrijf bewerkingen naar de primaire regio vóór de laatste synchronisatie tijd zijn beschikbaar om te worden gelezen vanaf de secundaire locatie. Schrijf bewerkingen naar de primaire regio na de laatste synchronisatie tijd-eigenschap zijn mogelijk nog niet beschikbaar voor lees bewerkingen.

De eigenschap **laatste synchronisatie tijd** is een GMT-datum/-tijd waarde.

## <a name="get-the-last-sync-time-property"></a>De eigenschap van de laatste synchronisatie tijd ophalen

U kunt Power shell of Azure CLI gebruiken om de waarde van de eigenschap **laatste synchronisatie tijd** op te halen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de laatste synchronisatie tijd voor het opslag account met Power shell wilt ophalen, installeert u versie 1.11.0 of hoger van de module [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) . Controleer vervolgens de eigenschap **GeoReplicationStats. LastSyncTime** van het opslag account. Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door uw eigen waarden:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de laatste synchronisatie tijd voor het opslag account met Azure CLI wilt ophalen, controleert u de eigenschap **geoReplicationStats. lastSyncTime** van het opslag account. Gebruik de `--expand` para meter om waarden te retour neren voor de eigenschappen die onder **geoReplicationStats** zijn genest. Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door uw eigen waarden:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Zie ook

- [Azure Storage-redundantie](storage-redundancy.md)
- [De redundantie optie voor een opslag account wijzigen](redundancy-migration.md)
- [Geo-redundantie gebruiken om Maxi maal beschik bare toepassingen te ontwerpen](geo-redundant-design.md)
