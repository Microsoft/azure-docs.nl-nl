---
title: Zacht verwijderen inschakelen - Azure-bestands shares
description: Meer informatie over het inschakelen van het gebruik van een functie voor het verwijderen van gegevens in Azure-bestands shares voor gegevensherstel en het voorkomen van onbedoelde verwijdering.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: a0dff310ce4a40b7a66cc548f3c77213f4a10e00
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716998"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Zacht verwijderen inschakelen voor Azure-bestands shares

Azure Storage biedt een tijdelijke verwijderprocedure voor bestands shares, zodat u uw gegevens gemakkelijker kunt herstellen wanneer deze per ongeluk worden verwijderd door een toepassing of een andere gebruiker van het opslagaccount. Zie How to prevent [accidental deletion of Azure file shares (Onopzettelijk](storage-files-prevent-file-share-deletion.md)verwijderen van Azure-bestands shares voorkomen) voor meer informatie over het verwijderen van een zachte verwijdering.

De volgende secties laten zien hoe u voor Azure-bestands shares voor een bestaand opslagaccount in- en gebruikt:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Aan de slag

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
1. Navigeer naar uw opslagaccount en selecteer **Bestands shares** onder **Gegevensopslag.**
1. Selecteer **Ingeschakeld naast** Zacht **verwijderen.**
1. Selecteer **Ingeschakeld voor** soft delete voor alle **bestands shares.**
1. Selecteer **Bewaarperiode voor bestands delen in dagen** en voer een aantal van uw keuze in.
1. Selecteer **Opslaan om** uw instellingen voor gegevensretentie te bevestigen.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="Schermopname van het deelvenster instellingen voor het verwijderen van het opslagaccount. Markeer de sectie voor het verwijderen van de bestands shares, schakel de schakelknop in, stel een bewaarperiode in en sla deze op. Hiermee kunt u voor alle bestands shares in uw opslagaccount de functie voor zacht verwijderen inschakelen.":::

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Cmdlets voor zacht verwijderen zijn beschikbaar in versie 2.1.3 en hoger van de [Azure CLI-module](/cli/azure/install-azure-cli).

## <a name="getting-started-with-cli"></a>Aan de slag met CLI

Als u de functie voor zacht verwijderen wilt inschakelen, moet u de service-eigenschappen van een bestandsclient bijwerken. In het volgende voorbeeld wordt voor alle bestands shares in een opslagaccount een tijdelijke opslag mogelijk:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

U kunt controleren of soft delete is ingeschakeld en het bewaarbeleid weergeven met de volgende opdracht:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Vereiste

Cmdlets voor zacht verwijderen zijn beschikbaar in de 4.8.0- en nieuwere versies van de Az.Storage-module. 

## <a name="getting-started-with-powershell"></a>Aan de slag met PowerShell

Als u de functie voor zacht verwijderen wilt inschakelen, moet u de service-eigenschappen van een bestandsclient bijwerken. In het volgende voorbeeld wordt voor alle bestands shares in een opslagaccount een tijdelijke opslag mogelijk:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

U kunt controleren of soft delete is ingeschakeld en het retentiebeleid weergeven met de volgende opdracht:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Een zacht verwijderde bestands share herstellen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een zacht verwijderde bestands share herstellen:

1. Navigeer naar uw opslagaccount en selecteer **Bestands shares.**
1. Schakel op de blade bestands delen **De verwijderde shares weergeven in** om alle shares weer te geven die zijn verwijderd.

    Hiermee worden alle shares weergegeven die momenteel de status **Verwijderd** hebben.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Als de statuskolom, de kolom naast de naamkolom, is ingesteld op Verwijderd, heeft uw bestands share de status Verwijderd. En worden permanent verwijderd na de opgegeven retentieperiode.":::

1. Selecteer de share en selecteer **niet-verwijderd.** Hiermee wordt de share hersteld.

    U kunt controleren of de share is hersteld omdat de status ervan wordt overgezet naar **Actief.**

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Als de statuskolom, de kolom naast de naamkolom, is ingesteld op Actief, is de bestands share hersteld.":::

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Cmdlets voor het verwijderen van de zachte versie zijn beschikbaar in versie 2.1.3 van de Azure CLI. Als u een zacht verwijderde bestands share wilt herstellen, moet u eerst de `--deleted-version` waarde van de share op halen. Gebruik de volgende opdracht om alle verwijderde shares voor uw opslagaccount weer te geven om deze waarde op te halen:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Zodra u de share hebt geïdentificeerd die u wilt herstellen, kunt u deze gebruiken met de volgende opdracht om deze te herstellen:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets voor het verwijderen van tijdelijke bestanden zijn beschikbaar in de 4.8.0- en nieuwere versies van de Az.Storage-module. Als u een zacht verwijderde bestands share wilt herstellen, moet u eerst de `-DeletedShareVersion` waarde van de share op halen. Gebruik de volgende opdracht om alle verwijderde shares voor uw opslagaccount weer te geven om deze waarde op te halen:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Zodra u de share hebt geïdentificeerd die u wilt herstellen, kunt u deze gebruiken met de volgende opdracht om deze te herstellen:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Soft Delete uitschakelen

Als u wilt stoppen met het gebruik van soft delete, volgt u deze instructies. Als u een bestands share die tijdelijk is verwijderd definitief wilt verwijderen, moet u deze verwijderen, de functie voor het verwijderen van tijdelijke bestanden uitschakelen en deze opnieuw verwijderen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigeer naar uw opslagaccount en selecteer **Bestands shares** onder **Gegevensopslag.**
1. Selecteer de koppeling naast **Soft delete**.
1. Selecteer **Uitgeschakeld bij** Soft Delete voor alle **bestands shares.**
1. Selecteer **Opslaan om** uw instellingen voor gegevensretentie te bevestigen.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="Door de tijdelijke verwijder uit te stellen, kunt u alle bestands shares in uw opslagaccount direct en permanent verwijderen.":::

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Cmdlets voor zacht verwijderen zijn beschikbaar in versie 2.1.3 van de Azure CLI. U kunt de volgende opdracht gebruiken om de functie voor het verwijderen van gegevens in uw opslagaccount uit te schakelen:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets voor zacht verwijderen zijn beschikbaar in de 4.8.0- en nieuwere versies van de Az.Storage-module. U kunt de volgende opdracht gebruiken om de functie voor het verwijderen van gegevens in uw opslagaccount uit te schakelen:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over een andere vorm van gegevensbescherming en -herstel ons artikel Overzicht van [momentopnamen](storage-snapshots-files.md)van share voor Azure Files.