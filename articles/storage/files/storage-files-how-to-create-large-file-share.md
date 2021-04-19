---
title: Grote bestands shares inschakelen en maken - Azure Files
description: In dit artikel leert u hoe u grote bestands shares kunt inschakelen en maken.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 20f9aaf73fe0cb30b136254d57e6c9b960c16af4
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716973"
---
# <a name="enable-and-create-large-file-shares"></a>Grote bestands shares inschakelen en maken

Uw Azure-bestands shares kunnen worden opgeschaald tot 100 TiB nadat u grote bestands shares in uw opslagaccount hebt ingeschakeld. Wanneer u grote bestands shares inschakelen, kan dit ook de IOPS en doorvoerlimieten van uw bestands share verhogen. U kunt deze schaalbaarheid ook inschakelen voor uw bestaande opslagaccounts voor bestaande en nieuwe bestands shares. Zie Bestands share en bestandsschaaldoelen voor meer informatie over [prestatieverschillen.](storage-files-scale-targets.md#azure-files-scale-targets)

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- Als u van plan bent om de Artikel CLI te gebruiken, [installeert u de nieuwste versie](/cli/azure/install-azure-cli).
- Als u van plan bent om de module Azure PowerShell gebruiken, [installeert u de nieuwste versie](/powershell/azure/install-az-ps).

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

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Installeer eerst [de nieuwste versie van de Azure CLI,](/cli/azure/install-azure-cli) zodat u grote bestands shares kunt inschakelen.

Gebruik de volgende opdracht om een opslagaccount te maken met grote bestands shares ingeschakeld. Vervang `<yourStorageAccountName>` , en door uw `<yourResourceGroup>` `<yourDesiredRegion>` gegevens.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installeer eerst [de nieuwste versie van PowerShell,](/powershell/azure/install-az-ps) zodat u grote bestands shares kunt inschakelen.

Gebruik de volgende opdracht om een opslagaccount te maken met grote bestands shares ingeschakeld. Vervang `<yourStorageAccountName>` , en door uw `<yourResourceGroup>` `<yourDesiredRegion>` gegevens.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Grote bestands shares inschakelen voor een bestaand account

U kunt ook grote bestands shares inschakelen voor uw bestaande accounts. Als u grote bestands shares inschakelen, kunt u niet converteren naar GZRS, GRS, RA-GRS of RA-GZRS. Het inschakelen van grote bestands shares kan niet ongedaan worden maken voor dit opslagaccount.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Open de [Azure Portal](https://portal.azure.com)en navigeer naar het opslagaccount waar u grote bestands shares wilt inschakelen.
1. Open het opslagaccount en selecteer **Bestands shares.**
1. Selecteer **Ingeschakeld op** grote **bestands shares** en selecteer vervolgens **Opslaan.**
1. Selecteer **Overzicht** en selecteer **Vernieuwen.**
1. Selecteer **Capaciteit delen** en selecteer vervolgens **100 TiB** en **Opslaan.**

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Schermopname van het Azure Storage-account, blade bestands shares met 100 tib-shares gemarkeerd.":::

U hebt nu grote bestands shares ingeschakeld voor uw opslagaccount. Vervolgens moet u het [quotum van een bestaande share bijwerken](#expand-existing-file-shares) om te profiteren van verhoogde capaciteit en schaal.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u grote bestands shares wilt inschakelen voor uw bestaande account, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` en door uw `<yourResourceGroup>` gegevens.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

U hebt nu grote bestands shares ingeschakeld voor uw opslagaccount. Vervolgens moet u het [quotum van een bestaande share bijwerken](#expand-existing-file-shares) om te profiteren van verhoogde capaciteit en schaal.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u grote bestands shares wilt inschakelen voor uw bestaande account, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` en door uw `<yourResourceGroup>` gegevens.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

U hebt nu grote bestands shares ingeschakeld in uw opslagaccount. Vervolgens moet u het [quotum van de bestaande share bijwerken](#expand-existing-file-shares) om te profiteren van verhoogde capaciteit en schaal.

---

## <a name="create-a-large-file-share"></a>Een grote bestands share maken

Nadat u grote bestands shares in uw opslagaccount hebt ingeschakeld, kunt u bestands shares maken in dat account met hogere quota. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Het maken van een grote bestands share is bijna identiek aan het maken van een standaardbestands share. Het belangrijkste verschil is dat u een quotum van maximaal 100 TiB kunt instellen.

1. Selecteer bestands shares in **uw opslagaccount.**
1. Selecteer  **+ bestandsshare**.
1. Voer een naam in voor de bestands share. U kunt ook een quotumgrootte van maximaal 100 TiB instellen. Selecteer vervolgens **Maken**. 

![De Azure Portal gebruikersinterface met de vakken Naam en Quotum](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende opdracht om een grote bestands share te maken. Vervang `<yourStorageAccountName>` , en door uw `<yourStorageAccountKey>` `<yourFileShareName>` gegevens.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende opdracht om een grote bestands share te maken. Vervang `<YourStorageAccountName>` , en door uw `<YourStorageAccountKey>` `<YourStorageAccountFileShareName>` gegevens.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Bestaande bestands shares uitbreiden

Nadat u grote bestands shares in uw opslagaccount hebt ingeschakeld, kunt u ook bestaande bestands shares in dat account uitbreiden naar het hogere quotum. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer bestands shares in uw **opslagaccount.**
1. Klik met de rechtermuisknop op de bestands share en **selecteer** quota.
1. Voer de nieuwe grootte in die u wilt en selecteer **OK.**

![De Azure Portal gebruikersinterface met quotum van bestaande bestands shares](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende opdracht om het quotum in te stellen op de maximale grootte. Vervang `<yourStorageAccountName>` , en door uw `<yourStorageAccountKey>` `<yourFileShareName>` gegevens.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende opdracht om het quotum in te stellen op de maximale grootte. Vervang `<YourStorageAccountName>` , en door uw `<YourStorageAccountKey>` `<YourStorageAccountFileShareName>` gegevens.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Volgende stappen

* [Een bestands share verbinden en koppelen in Windows](storage-how-to-use-files-windows.md)
* [Een bestands share verbinden en koppelen in Linux](storage-how-to-use-files-linux.md)
* [Een bestands share verbinden en koppelen in macOS](storage-how-to-use-files-mac.md)