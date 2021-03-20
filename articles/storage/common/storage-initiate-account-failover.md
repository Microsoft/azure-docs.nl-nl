---
title: Failover van een opslag account initiëren
titleSuffix: Azure Storage
description: Meer informatie over het initiëren van een account-failover in het geval dat het primaire eind punt voor uw opslag account niet meer beschikbaar is. De failover werkt de secundaire regio bij om de primaire regio voor uw opslag account te worden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/29/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93bcbab9445d83bf17b37b6affc1d2bc70703bbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97814326"
---
# <a name="initiate-a-storage-account-failover"></a>Failover van een opslag account initiëren

Als het primaire eind punt voor uw Geo-redundante opslag account om welke reden dan ook niet beschikbaar is, kunt u een failover voor het account initiëren. Met een account-failover wordt het secundaire eind punt bijgewerkt om het primaire eind punt voor uw opslag account te worden. Zodra de failover is voltooid, kunnen clients beginnen met het schrijven naar de nieuwe primaire regio. Met geforceerde failover kunt u hoge Beschik baarheid voor uw toepassingen onderhouden.

In dit artikel wordt beschreven hoe u een failover voor een account initieert voor uw opslag account met behulp van de Azure Portal, Power shell of Azure CLI. Zie [nood herstel en failover van het opslag account](storage-disaster-recovery-guidance.md)voor meer informatie over account-failover.

> [!WARNING]
> Een account-failover resulteert doorgaans in gegevens verlies. Bekijk [inzicht in het account failover proces](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)voor meer informatie over de implicaties van een account-failover en voor bereiding op gegevens verlies.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u een account-failover kunt uitvoeren op uw opslag account, moet u ervoor zorgen dat uw opslag account is geconfigureerd voor geo-replicatie. Uw opslag account kan een van de volgende redundantie opties gebruiken:

- Geografisch redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS)
- Geo-zone-redundante opslag (GZRS) of geo-zone-redundante opslag met lees toegang (RA-GZRS)

Zie [Azure Storage redundantie](storage-redundancy.md)voor meer informatie over Azure Storage redundantie.

Houd er rekening mee dat de volgende functies en services niet worden ondersteund voor account-failover:

- Azure File Sync biedt geen ondersteuning voor de failover van het opslag account. Er mag geen failover-overschakeling worden uitgevoerd voor opslagaccounts met Azure-bestandsshares die worden gebruikt als cloudeindpunten in Azure File Sync. Als u dat wel doet, werkt de synchronisatie niet meer en kan dit leiden tot onverwacht gegevensverlies van bestanden in cloudlagen.
- ADLS Gen2 opslag accounts (accounts waarvoor een hiërarchische naam ruimte is ingeschakeld) worden op dit moment niet ondersteund.
- Er kan geen failover worden uitgevoerd voor een opslag account met Premium-blok-blobs. Opslag accounts die ondersteuning bieden voor Premium-blok-blobs ondersteunen momenteel geen geo-redundantie.
- Er kan geen failover worden uitgevoerd voor een opslag account met een of meer [Onveranderbaarheid-beleids](../blobs/storage-blob-immutable-storage.md) containers waarvoor een virus is ingeschakeld. Een niet-vergrendelde/vergrendelde, op tijd gebaseerde Bewaar-en wettelijk Bewaar beleidsregels voor komen failover om naleving te behouden.

## <a name="initiate-the-failover"></a>De failover initiëren

## <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om een account-failover te initiëren vanuit de Azure Portal:

1. Ga naar uw opslagaccount.
1. Selecteer onder **Instellingen** de optie **Geografische replicatie**. De volgende afbeelding toont de geo-replicatie en de failover-status van een opslag account.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="Scherm opname van geo-replicatie en failover-status":::

1. Controleer of uw opslag account is geconfigureerd voor geo-redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS). Als dat niet het geval is, selecteert u vervolgens **configuratie** onder **instellingen** om uw account bij te werken naar geo-redundant.
1. De **laatste synchronisatie tijd** eigenschap geeft aan hoe ver de secundaire achterligt van de primaire. **Tijd van laatste synchronisatie** biedt een schatting van de mate van gegevens verlies die u zult ondervinden nadat de failover is voltooid. Zie voor meer informatie over het controleren van de **laatste synchronisatie tijd** -eigenschap [de laatste synchronisatie tijd van een opslag account](last-sync-time-get.md).
1. Selecteren **Voorbereiden voor failover**.
1. Bekijk het bevestigings venster. Wanneer u klaar bent, voert u **Ja** in om de failover te bevestigen en te initiëren.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="Scherm opname van het bevestigings dialoogvenster voor een account-failover":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De functie account failover is algemeen beschikbaar, maar is nog steeds afhankelijk van een preview-module voor Power shell. Als u Power shell wilt gebruiken om een account-failover te initiëren, moet u eerst de module AZ. Storage [1.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) installeren. Volg deze stappen om de module te installeren:

1. Eerdere installaties van Azure PowerShell verwijderen:

    - Verwijder eerdere installaties van Azure PowerShell van Windows met de instelling **Apps & onderdelen** onder **instellingen**.
    - Verwijder alle **Azure** -modules uit `%Program Files%\WindowsPowerShell\Modules` .

1. Zorg ervoor dat de meest recente versie van PowerShellGet is geïnstalleerd. Open een Windows Power shell-venster en voer de volgende opdracht uit om de meest recente versie te installeren:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Sluit het Power shell-venster en open het opnieuw nadat u PowerShellGet hebt geïnstalleerd.

1. Installeer de nieuwste versie van Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installeer een Azure Storage preview-module die de failover van het account ondersteunt:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

Voer de volgende opdracht uit om een account-failover te initiëren vanuit Power shell:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gebruiken om een account-failover te initiëren, voert u de volgende opdrachten uit:

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>Belang rijke implicaties van account-failover

Wanneer u een failover voor een account initieert voor uw opslag account, worden de DNS-records voor het secundaire eind punt bijgewerkt zodat het secundaire eind punt het primaire eind punt wordt. Zorg ervoor dat u de mogelijke gevolgen voor uw opslag account begrijpt voordat u een failover initieert.

Als u de omvang van waarschijnlijke gegevens verlies wilt schatten voordat u een failover initieert, controleert u de eigenschap **laatste synchronisatie tijd** . Zie voor meer informatie over het controleren van de **laatste synchronisatie tijd** -eigenschap [de laatste synchronisatie tijd van een opslag account](last-sync-time-get.md).

De tijd die nodig is voor de failover na het initiëren van de initialisatie, kan verschillen van minder dan een uur.

Na de failover wordt uw type opslag account automatisch geconverteerd naar lokaal redundante opslag (LRS) in de nieuwe primaire regio. U kunt geografisch redundante opslag (GRS) of geografisch redundante opslag met lees toegang (RA-GRS) opnieuw inschakelen voor het account. Houd er rekening mee dat het converteren van LRS naar GRS of RA-GRS een extra kosten in beslag brengt. Zie [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie.

Nadat u GRS voor uw opslag account opnieuw hebt ingeschakeld, begint micro soft met het repliceren van de gegevens in uw account naar de nieuwe secundaire regio. De replicatie tijd is afhankelijk van de hoeveelheid gegevens die wordt gerepliceerd.  

## <a name="next-steps"></a>Volgende stappen

- [Herstel na noodgeval en failover van opslagaccount](storage-disaster-recovery-guidance.md)
- [De eigenschap van de laatste synchronisatie tijd voor een opslag account controleren](last-sync-time-get.md)
- [Geo-redundantie gebruiken om Maxi maal beschik bare toepassingen te ontwerpen](geo-redundant-design.md)
- [Zelfstudie: Een maximaal beschikbare toepassing bouwen met Blob-opslag](../blobs/storage-create-geo-redundant-storage.md)
