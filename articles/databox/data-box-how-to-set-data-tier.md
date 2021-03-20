---
title: Gegevens verzenden naar een warme, koude, archief-BLOB-laag via Azure Data Box/Azure Data Box Heavy
description: Hierin wordt beschreven hoe u Azure Data Box of Azure Data Box Heavy gebruikt voor het verzenden van gegevens naar de juiste opslaglaag van een blok-blob, zoals warme, koude of archief
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 80c4d8a70454c007ac45f588e59c03ef45f10933
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92125061"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Azure Data Box of Azure Data Box Heavy gebruiken om gegevens te verzenden naar de juiste Azure Storage BLOB-laag

Azure Data Box grote hoeveel heden gegevens naar Azure verplaatsen door u een eigen opslag apparaat te verzenden. U vult het apparaat in met gegevens en retourneert het. De gegevens van Data Box worden geüpload naar een standaard laag die is gekoppeld aan het opslag account. U kunt de gegevens vervolgens naar een andere opslaglaag verplaatsen.

In dit artikel wordt beschreven hoe de gegevens die door Data Box worden geüpload, kunnen worden verplaatst naar een warme, koude of archief-BLOB-laag. Dit artikel is van toepassing op zowel Azure Data Box als Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Kies de juiste opslaglaag voor uw gegevens

Azure Storage biedt drie verschillende lagen voor het opslaan van gegevens op de meest rendabele manier: warme, koude of archief. De laag Hot Storage is geoptimaliseerd voor het opslaan van gegevens die regel matig worden gebruikt. Hot Storage heeft hogere opslag kosten dan cool en archief opslag, maar de laagste toegangs kosten.

De cool Storage-laag is voor zelden gebruikte gegevens die gedurende mini maal 30 dagen moeten worden opgeslagen. De opslag kosten voor de laag koud zijn lager dan die van de Hot Storage-laag, maar de kosten voor gegevens toegang zijn hoog wanneer ze worden vergeleken met de warme laag.

De Azure-archief laag is offline en biedt de laagste opslag kosten, maar ook de hoogste toegangs kosten. Deze laag is bedoeld voor gegevens die gedurende mini maal 180 dagen in archief opslag bewaard blijven. Ga voor meer informatie over elk van deze lagen en het prijs model naar [vergelijking van de opslag lagen](../storage/blobs/storage-blob-storage-tiers.md).

De gegevens van de Data Box of Data Box Heavy worden geüpload naar een opslaglaag die is gekoppeld aan het opslag account. Wanneer u een opslag account maakt, kunt u de toegangs laag als dynamisch of koud opgeven. Afhankelijk van het toegangs patroon van uw werk belasting en kosten, kunt u deze gegevens van de standaard laag naar een andere opslaglaag verplaatsen.

U kunt de opslag gegevens van uw objecten alleen in Blob Storage-of Algemeen v2-accounts (GPv2) laag lagen. General Purpose v1 (GPv1)-accounts bieden geen ondersteuning voor opslaglagen. Als u de juiste opslaglaag voor uw gegevens wilt kiezen, raadpleegt u de overwegingen voor [Azure Blob Storage: Premium, warme, cool en archief opslag lagen](../storage/blobs/storage-blob-storage-tiers.md).

## <a name="set-a-default-blob-tier"></a>Een standaardblob-laag instellen

De standaardlaag van de BLOB wordt opgegeven wanneer het opslag account wordt gemaakt in de Azure Portal. Zodra een opslag type is geselecteerd als GPv2 of Blob-opslag, kan het kenmerk toegangs niveau worden opgegeven. De warme laag is standaard geselecteerd.

De lagen kunnen niet worden opgegeven als u een nieuw account probeert te maken bij het best Ellen van een Data Box of Data Box Heavy. Nadat het account is gemaakt, kunt u het account in de portal wijzigen om de standaardlaag voor toegang in te stellen.

U kunt ook eerst een opslag account maken met het opgegeven kenmerk van de toegangs laag. Wanneer u de Data Box of Data Box Heavy order maakt, selecteert u het bestaande opslag account. Ga voor meer informatie over het instellen van de standaardlaag BLOB tijdens het maken van een opslag account naar [een opslag account maken in azure Portal](../storage/common/storage-account-create.md?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Gegevens naar een niet-standaard-laag verplaatsen

Zodra de gegevens van Data Box apparaat naar de laag standaard worden geüpload, kunt u de gegevens naar een andere laag verplaatsen. Er zijn twee manieren om deze gegevens te verplaatsen naar een niet-standaard-laag.

- **Levenscyclus beheer voor Azure Blob-opslag** : u kunt een op beleid gebaseerde aanpak gebruiken om gegevens automatisch te trapsgewijs of te laten verlopen aan het einde van de levens cyclus. Ga voor meer informatie naar [beheer van de levens cyclus van Azure Blob-opslag](../storage/blobs/storage-lifecycle-management-concepts.md).
- **Scripts** : u kunt een script methode gebruiken via Azure PowerShell om laag niveau op blobniveau in te scha kelen. U kunt de bewerking aanroepen `SetBlobTier` om de laag in te stellen op de blob.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Azure PowerShell gebruiken om de BLOB-laag in te stellen

In de volgende stappen wordt beschreven hoe u de BLOB-laag kunt instellen op archief met behulp van een Azure PowerShell script.

1. Open een Windows Power shell-sessie met verhoogde bevoegdheden. Zorg ervoor dat Power shell 5,0 of hoger wordt uitgevoerd. Type:

   `$PSVersionTable.PSVersion`     

2. Meld u aan bij de Azure PowerShell. 

   `Login-AzAccount`  

3. Definieer de variabelen voor het opslag account, de toegangs sleutel, de container en de opslag context.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Alle blobs in de container ophalen.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Stel de laag van alle blobs in de container in op Archive.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Hieronder ziet u een voorbeeld van de uitvoer:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Als u wilt dat de gegevens worden gearchiveerd op opname, stelt u de standaardlaag in op dynamisch. Als de standaardlaag cool is, is er een vroege verwijdering van dertig dagen als de gegevens onmiddellijk naar het archief worden verplaatst.

## <a name="next-steps"></a>Volgende stappen

-  Meer informatie over het oplossen [van veelvoorkomende scenario's voor gegevenslaagingen met levenscyclus beleid](../storage/blobs/storage-lifecycle-management-concepts.md#examples)