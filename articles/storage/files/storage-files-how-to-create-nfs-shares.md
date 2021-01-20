---
title: Een NFS-share maken-Azure Files (preview-versie)
description: Meer informatie over het maken van een Azure-bestands share die kan worden gekoppeld met het Network File System-protocol.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 323eed77d6f7a6ccfcdd0a7c7aecff3a125300dc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602662"
---
# <a name="how-to-create-an-nfs-share"></a>Een NFS-share maken

Azure-bestands shares zijn volledig beheerde bestands shares die zich in de cloud bevinden. Ze kunnen worden geopend met behulp van het Server Message Block-protocol of het NFS-protocol (Network File System). In dit artikel wordt beschreven hoe u een bestands share maakt die gebruikmaakt van het NFS-protocol. Zie [Azure file share-protocollen](storage-files-compare-protocols.md)voor meer informatie over beide protocollen.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionale beschikbaarheid

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Vereisten

- Maak een [FileStorage-account](storage-how-to-create-premium-fileshare.md).

    > [!IMPORTANT]
    > NFS-shares kunnen alleen worden geopend vanuit vertrouwde netwerken. Verbindingen met uw NFS-share moeten afkomstig zijn van een van de volgende bronnen:

    - [Maak een persoonlijk eind punt](storage-files-networking-endpoints.md#create-a-private-endpoint) (aanbevolen) of [Beperk de toegang tot uw open bare eind punt](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Een punt-naar-site-VPN (P2S) op Linux configureren voor gebruik met Azure files](storage-files-configure-p2s-vpn-linux.md).
    - [Configureer een site-naar-site-VPN voor gebruik met Azure files](storage-files-configure-s2s-vpn.md).
    - [ExpressRoute](../../expressroute/expressroute-introduction.md)configureren.
- Als u van plan bent om de Artikel CLI te gebruiken, [installeert u de nieuwste versie](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="register-the-nfs-41-protocol"></a>Het NFS 4,1-protocol registreren

Als u de Azure PowerShell-module of de Azure CLI gebruikt, registreert u uw functie met de volgende opdrachten:

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-feature-registration"></a>Functie registratie controleren

De registratie goedkeuring kan tot een uur duren. Gebruik de volgende opdrachten om te controleren of de registratie is voltooid:

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="verify-storage-account-kind"></a>Type opslag account verifiëren

Op dit moment kunnen alleen FileStorage-accounts NFS-shares maken. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als u wilt controleren welk type opslag account u hebt, navigeert u ernaar in de Azure Portal. Selecteer vervolgens vanuit uw opslag account **Eigenschappen**. Controleer op de Blade eigenschappen de waarde onder **soort account**, de waarde moet **FileStorage** zijn.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u wilt controleren of u een FileStorage-account hebt, kunt u de volgende opdracht gebruiken:

```azurepowershell
$accountKind=Get-AzStorageAccount -ResourceGroupName "yourResourceGroup" -Name "yourStorageAccountName"
$accountKind.Kind
```

De uitvoer moet **FileStorage**, als dat niet het geval is, dan is uw opslag account het verkeerde type. Zie [een Azure Premium-bestands share maken voor meer informatie over](storage-how-to-create-premium-fileshare.md)het maken van een **FileStorage** -account.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u wilt controleren of u een FileStorage-account hebt, kunt u de volgende opdracht gebruiken:

```azurecli
az storage account show -g yourResourceGroup -n yourStorageAccountName
```

De uitvoer moet **' soort ': ' FileStorage '** bevatten. als dit niet het geval is, is uw opslag account het verkeerde type. Zie [een Azure Premium-bestands share maken voor meer informatie over](storage-how-to-create-premium-fileshare.md)het maken van een **FileStorage** -account.

---
## <a name="create-an-nfs-share"></a>Een NFS-share maken

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nu u een FileStorage-account hebt gemaakt en het netwerk hebt geconfigureerd, kunt u een NFS-bestands share maken. Het proces is vergelijkbaar met het maken van een SMB-share. in plaats van **SMB** selecteert u **NFS** bij het maken van de share.

1. Navigeer naar uw opslag account en selecteer **Bestands shares**.
1. Selecteer **+ Bestands share** om een nieuwe bestands share te maken.
1. Geef de bestands share een naam en selecteer een ingerichte capaciteit.
1. Voor **protocol** Select **NFS (preview-versie)**.
1. Voor **root Squash** maakt u een selectie.

    - Hoofdmap-Squash (standaard): toegang voor de externe super gebruiker (root) wordt toegewezen aan de UID (65534) en GID (65534).
    - Geen hoofd-Squash: externe super gebruiker (root) ontvangt toegang als basis.
    - Alle Squash: alle gebruikers toegang is toegewezen aan UID (65534) en GID (65534).
    
1. Selecteer **Maken**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Scherm opname van de Blade voor het maken van een bestands share":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Zorg ervoor dat .NET Framework is geïnstalleerd. Zie [.NET Framework downloaden](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Controleer of de versie van Power shell die is geïnstalleerd, `5.1` of hoger is met behulp van de volgende opdracht.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Zie [bestaande Windows Power shell upgraden](/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) voor informatie over het bijwerken van uw versie van Power shell
    
1. Installeer de nieuwste versie van de PowershellGet-module.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Sluit de Power shell-console en open deze opnieuw.

1. Installeer de module **AZ. Storage** preview versie **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps?view=azps-3.0.0) voor meer informatie over het installeren van Power shell-modules.
   
1. Als u een Premium-bestands share met de module Azure PowerShell wilt maken, gebruikt u de cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

> [!NOTE]
> Ingerichte share groottes worden opgegeven door het share quotum, bestands shares worden gefactureerd op basis van de ingerichte grootte. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/storage/files/) voor meer informatie.

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een Premium-bestands share wilt maken met de Azure CLI, gebruikt u de opdracht [AZ Storage share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Ingerichte share groottes worden opgegeven door het share quotum, bestands shares worden gefactureerd op basis van de ingerichte grootte. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/storage/files/) voor meer informatie.

```azurecli-interactive
az storage share-rm create \
    --storage-account $STORAGEACCT \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>Volgende stappen

Nu u een NFS-share hebt gemaakt om deze te gebruiken, moet u deze koppelen aan uw Linux-client. Zie [een NFS-share koppelen](storage-files-how-to-mount-nfs-shares.md)voor meer informatie.

Zie [problemen met Azure NFS-bestands shares oplossen](storage-troubleshooting-files-nfs.md)als u problemen ondervindt.
