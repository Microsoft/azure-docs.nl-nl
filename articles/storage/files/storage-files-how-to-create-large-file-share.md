---
title: Grote bestands shares inschakelen en maken - Azure Files
description: In dit artikel leert u hoe u grote bestands shares kunt inschakelen en maken.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a53f964020583c41e2400d97ad244bacd33813bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818256"
---
# <a name="enable-and-create-large-file-shares"></a>Grote bestands shares inschakelen en maken
Uw Azure-bestands shares kunnen worden opgeschaald tot 100 TiB nadat u grote bestands shares in uw opslagaccount hebt ingeschakeld. Wanneer u grote bestands shares inschakelen, kan dit ook de IOPS en doorvoerlimieten van uw bestands share verhogen. U kunt deze schaalbaarheid ook inschakelen voor uw bestaande opslagaccounts voor bestaande en nieuwe bestands shares. Zie Bestands share en bestandsschaaldoelen voor meer informatie over [prestatieverschillen.](storage-files-scale-targets.md#azure-files-scale-targets)

## <a name="prerequisites"></a>Vereisten
- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- Als u van plan bent om de Artikel CLI te gebruiken, [installeert u de nieuwste versie](/cli/azure/install-azure-cli).
- Als u van plan bent om de Azure PowerShell te gebruiken, [installeert u de nieuwste versie](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Beperkingen
Op dit moment kunt u alleen lokaal redundante opslag (LRS) of zone-redundante opslag (ZRS) gebruiken voor opslagaccounts met grote bestands shares ingeschakeld. U kunt geen geografisch zone-redundante opslag (GZRS), geografisch redundante opslag (GRS), geografisch redundante opslag met leestoegang (RA-GRS) of geografisch zone-redundante opslag met leestoegang (RA-GZRS) gebruiken.

Het inschakelen van grote bestands shares voor een account is een onomkeerbaar proces. Nadat u dit hebt ingeschakeld, kunt u uw account niet converteren naar GZRS, GRS, RA-GRS of RA-GZRS.

## <a name="create-a-new-storage-account"></a>Een nieuw opslagaccount maken

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in de Azure-portal de optie **Alle services**. 
1. Voer in de lijst met resources **Opslagaccounts** in. Terwijl u typt, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Opslagaccounts**.
1. Selecteer + **Nieuw op** de blade Opslagaccounts die **wordt weergegeven.**
1. Vul op de blade Basis de selecties naar wens in.
1. Zorg ervoor dat **Prestaties** is ingesteld op **Standard.**
1. Stel **Redundantie in** op **Lokaal redundante opslag** of **Zone-redundante opslag.**
1. Selecteer **de** blade Geavanceerd en selecteer vervolgens de **optie Ingeschakeld** rechts van **Grote bestands shares.**
1. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.
1. Selecteer **Maken**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Gebruik de volgende opdracht om een opslagaccount te maken met grote bestands shares ingeschakeld. Vervang `<yourStorageAccountName>` , en door uw `<yourResourceGroup>` `<yourDesiredRegion>` gegevens.

```powershell
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -Location <yourDesiredRegion> `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Gebruik de volgende opdracht om een opslagaccount te maken met grote bestands shares ingeschakeld. Vervang `<yourStorageAccountName>` , en door uw `<yourResourceGroup>` `<yourDesiredRegion>` gegevens.

```azurecli-interactive
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
az storage account create \
    --name <yourStorageAccountName> \
    -g <yourResourceGroup> \
    -l <yourDesiredRegion> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --enable-large-file-share
```

---

Nadat u een opslagaccount met een grote bestands share hebt gemaakt, kunt u een bestands share maken die gebruik maakt van de grote capaciteits- en schaallimieten. Zie Een Azure-bestands share maken voor meer informatie over het maken van opslagaccounts en [Azure-bestands shares.](storage-how-to-create-file-share.md)

## <a name="enable-large-files-shares-on-an-existing-account"></a>Grote bestands shares inschakelen voor een bestaand account
U kunt ook grote bestands shares inschakelen voor uw bestaande LRS- en ZRS-opslagaccounts. Als u een GRS-, GZRS-, RA-GRS- of RA-GZRS-account hebt, moet u dit converteren naar een LRS-account voordat u doorgaat.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Open de [Azure Portal](https://portal.azure.com)en navigeer naar het opslagaccount waar u grote bestands shares wilt inschakelen.
1. Open het opslagaccount en selecteer **Bestands shares.**
1. Selecteer **Ingeschakeld op** grote **bestands shares** en selecteer vervolgens **Opslaan.**
1. Selecteer **Overzicht** en selecteer **Vernieuwen.**
1. Selecteer **Capaciteit delen** en selecteer vervolgens **100 TiB** en **Opslaan.**

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Schermopname van het Azure Storage-account, blade bestands shares met 100 tib-shares gemarkeerd.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u grote bestands shares wilt inschakelen voor uw bestaande account, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` en door uw `<yourResourceGroup>` gegevens.

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u grote bestands shares wilt inschakelen voor uw bestaande account, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` en door uw `<yourResourceGroup>` gegevens.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

U hebt nu grote bestands shares ingeschakeld voor uw opslagaccount. Vervolgens moet u het [quotum van een bestaande share bijwerken](#expand-existing-file-shares) om te profiteren van verhoogde capaciteit en schaal. 

> [!Important]  
> Bestaande bestands shares worden niet omhoog geschaald naar de geadverteerde limieten voor grote bestands shares, tenzij het quotum is gewijzigd.

## <a name="expand-existing-file-shares"></a>Bestaande bestands shares uitbreiden
Nadat u grote bestands shares in uw opslagaccount hebt ingeschakeld, moet u bestaande bestands shares in dat opslagaccount uitbreiden om te profiteren van de verhoogde capaciteit en schaal. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Selecteer bestands shares in uw **opslagaccount.**
1. Klik met de rechtermuisknop op de bestands share en **selecteer** quota.
1. Voer de nieuwe grootte in die u wilt en selecteer **OK.**

![De Azure Portal gebruikersinterface met quotum van bestaande bestands shares](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Gebruik de volgende opdracht om het quotum in te stellen op de maximale grootte. Vervang `<YourResourceGroupName>` , en door uw `<YourStorageAccountName>` `<YourStorageAccountFileShareName>` gegevens.

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Gebruik de volgende opdracht om het quotum in te stellen op de maximale grootte. Vervang `<yourResourceGroupName>` , en door uw `<yourStorageAccountName>` `<yourFileShareName>` gegevens.

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>Volgende stappen
* [Een bestands share verbinden en koppelen in Windows](storage-how-to-use-files-windows.md)
* [Een bestands share verbinden en koppelen in Linux](storage-how-to-use-files-linux.md)
* [Een bestands share verbinden en koppelen in macOS](storage-how-to-use-files-mac.md)