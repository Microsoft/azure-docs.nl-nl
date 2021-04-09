---
title: Grote bestands shares inschakelen en maken-Azure Files
description: In dit artikel leert u hoe u grote bestands shares inschakelt en maakt.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c22b3f3164cbb7c1a7ed150d093f77777c7b1023
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501291"
---
# <a name="enable-and-create-large-file-shares"></a>Grote bestands shares inschakelen en maken

Wanneer u grote bestands shares inschakelt voor uw opslag account, kunnen uw bestands shares tot 100 TiB worden geschaald, terwijl ook de limieten voor IOPS en door voer worden verhoogd voor de standaard shares. U kunt deze schaal ook inschakelen voor uw bestaande opslag accounts voor uw bestaande bestands shares. Zie [Bestands share en bestands schaal doelen](storage-files-scale-targets.md#azure-files-scale-targets) voor meer informatie. 

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- Als u van plan bent om de Artikel CLI te gebruiken, [installeert u de nieuwste versie](/cli/azure/install-azure-cli).
- Als u de module Azure PowerShell wilt gebruiken, [installeert u de nieuwste versie](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Beperkingen

U kunt nu alleen lokaal redundante opslag (LRS) of zone-redundante opslag (ZRS) gebruiken op accounts met grote bestands shares. U kunt geen geo-zone-redundante opslag (GZRS), geografisch redundante opslag (GRS), geografisch redundante opslag met lees toegang (RA-GRS) of geo-zone-redundante opslag met lees toegang (RA-GZRS) gebruiken.

Het inschakelen van grote bestands shares voor een account is een onomkeerbaar proces. Nadat u dit hebt ingeschakeld, kunt u uw account niet converteren naar GZRS, GRS, RA-GRS of RA-GZRS.

## <a name="create-a-new-storage-account"></a>Een nieuw opslagaccount maken

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in de Azure-portal de optie **Alle services**. 
1. Voer in de lijst met resources **Opslagaccounts** in. Terwijl u typt, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Opslagaccounts**.
1. Selecteer **toevoegen** in het venster **opslag accounts** dat wordt weer gegeven.
1. Selecteer het abonnement dat u gaat gebruiken om het opslag account te maken.
1. Selecteer **Nieuwe maken** onder het veld **Resourcegroep**. Voer een naam in voor de nieuwe resource groep.

    ![Schermafbeelding van het maken van een resourcegroep in de portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Voer vervolgens een naam in voor het opslagaccount. De naam moet uniek zijn binnen Azure. De naam moet ook 3 tot 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.
1. Selecteer een locatie voor uw opslag account.
1. Stel de replicatie in op **lokaal redundante opslag** of **zone-redundante opslag**.
1. De standaard waarden van deze velden wijzigen:

   |Veld  |Waarde  |
   |---------|---------|
   |Implementatiemodel     |Resource Manager         |
   |Prestaties     |Standard         |
   |Soort account     |StorageV2 (algemeen gebruik v2)         |
   |Toegangslaag     |Dynamisch         |

1. Selecteer **Geavanceerd** en selecteer vervolgens het **ingeschakelde** keuze rondje rechts van **grote bestands shares**.
1. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.

    ![Scherm opname met het keuze rondje ' ingeschakeld ' in een nieuw opslag account in de Azure Portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selecteer **Maken**.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Installeer eerst [de nieuwste versie van de Azure cli](/cli/azure/install-azure-cli) zodat u grote bestands shares kunt inschakelen.

Als u een opslag account wilt maken waarvoor grote bestands shares zijn ingeschakeld, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` , `<yourResourceGroup>` en `<yourDesiredRegion>` met uw gegevens.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installeer eerst [de meest recente versie van Power shell](/powershell/azure/install-az-ps) , zodat u grote bestands shares kunt inschakelen.

Als u een opslag account wilt maken waarvoor grote bestands shares zijn ingeschakeld, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` , `<yourResourceGroup>` en `<yourDesiredRegion>` met uw gegevens.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Grote bestands shares op een bestaand account inschakelen

U kunt ook grote bestands shares inschakelen voor uw bestaande accounts. Als u grote bestands shares inschakelt, kunt u niet converteren naar GZRS, GRS, RA-GRS of RA-GZRS. Het inschakelen van grote bestands shares is onomkeerbaar voor dit opslag account.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Open de [Azure Portal](https://portal.azure.com)en ga naar het opslag account waarin u grote bestands shares wilt inschakelen.
1. Open het opslag account en selecteer **configuratie**.
1. Selecteer **ingeschakeld** op **grote bestands shares** en selecteer vervolgens **Opslaan**.
1. Selecteer **overzicht** en selecteer **vernieuwen**.

![Het ingeschakelde keuze rondje voor een bestaand opslag account in de Azure Portal selecteren](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

U hebt nu grote bestands shares ingeschakeld voor uw opslag account. Vervolgens moet u [het quotum van de bestaande share bijwerken](#expand-existing-file-shares) om te profiteren van verhoogde capaciteit en schaal.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u grote bestands shares wilt inschakelen voor uw bestaande account, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` en `<yourResourceGroup>` door uw gegevens.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

U hebt nu grote bestands shares ingeschakeld voor uw opslag account. Vervolgens moet u [het quotum van de bestaande share bijwerken](#expand-existing-file-shares) om te profiteren van verhoogde capaciteit en schaal.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u grote bestands shares wilt inschakelen voor uw bestaande account, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` en `<yourResourceGroup>` door uw gegevens.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

U hebt nu grote bestands shares ingeschakeld voor uw opslag account. Vervolgens moet u [het quotum van de bestaande share bijwerken](#expand-existing-file-shares) om te profiteren van verhoogde capaciteit en schaal.

---

## <a name="create-a-large-file-share"></a>Een grote bestands share maken

Nadat u grote bestands shares hebt ingeschakeld in uw opslag account, kunt u bestands shares maken in dat account met hogere quota's. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Het maken van een grote bestands share is bijna identiek aan het maken van een standaard bestands share. Het belangrijkste verschil is dat u een quotum kunt instellen van Maxi maal 100 TiB.

1. Selecteer in uw opslag account **Bestands shares**.
1. Selecteer  **+ bestandsshare**.
1. Voer een naam in voor de bestands share. U kunt ook de gewenste quota grootte instellen, tot 100 TiB. Selecteer vervolgens **Maken**. 

![De Azure Portal gebruikers interface waarin de naam en quotum vakken worden weer gegeven](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een grote bestands share wilt maken, gebruikt u de volgende opdracht. Vervang `<yourStorageAccountName>` , `<yourStorageAccountKey>` en `<yourFileShareName>` met uw gegevens.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u een grote bestands share wilt maken, gebruikt u de volgende opdracht. Vervang `<YourStorageAccountName>` , `<YourStorageAccountKey>` en `<YourStorageAccountFileShareName>` met uw gegevens.

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

Nadat u grote bestands shares in uw opslag account hebt ingeschakeld, kunt u ook bestaande bestands shares in dat account uitbreiden naar het hogere quotum. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in uw opslag account **Bestands shares**.
1. Klik met de rechter muisknop op de bestands share en selecteer **quotum**.
1. Voer de nieuwe gewenste grootte in en selecteer **OK**.

![De Azure Portal gebruikers interface met het quotum van de bestaande bestands shares](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende opdracht om het quotum in te stellen op de maximum grootte. Vervang `<yourStorageAccountName>` , `<yourStorageAccountKey>` en `<yourFileShareName>` met uw gegevens.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende opdracht om het quotum in te stellen op de maximum grootte. Vervang `<YourStorageAccountName>` , `<YourStorageAccountKey>` en `<YourStorageAccountFileShareName>` met uw gegevens.

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

* [Een bestands share in Windows verbinden en koppelen](storage-how-to-use-files-windows.md)
* [Een bestands share op Linux koppelen en koppelen](storage-how-to-use-files-linux.md)
* [Verbinding maken en koppelen van een bestands share op macOS](storage-how-to-use-files-mac.md)