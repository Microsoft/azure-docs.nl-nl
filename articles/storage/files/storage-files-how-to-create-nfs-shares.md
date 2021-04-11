---
title: Een NFS-share maken-Azure Files (preview-versie)
description: Meer informatie over het maken van een Azure-bestands share die kan worden gekoppeld met het Network File System-protocol.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b085b9991175d8cd43e2dac0db80c5af4e703c34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521234"
---
# <a name="how-to-create-an-nfs-share"></a>Een NFS-share maken
Azure-bestands shares zijn volledig beheerde bestands shares die zich in de cloud bevinden. In dit artikel wordt beschreven hoe u een bestands share maakt die gebruikmaakt van het NFS-protocol. Zie [Azure file share-protocollen](storage-files-compare-protocols.md)voor meer informatie over beide protocollen.

## <a name="limitations"></a>Beperkingen
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionale beschikbaarheid
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Vereisten
- NFS-shares kunnen alleen worden geopend vanuit vertrouwde netwerken. Verbindingen met uw NFS-share moeten afkomstig zijn van een van de volgende bronnen:
    - [Maak een persoonlijk eind punt](storage-files-networking-endpoints.md#create-a-private-endpoint) (aanbevolen) of [Beperk de toegang tot uw open bare eind punt](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Een punt-naar-site-VPN (P2S) op Linux configureren voor gebruik met Azure files](storage-files-configure-p2s-vpn-linux.md).
    - [Configureer een site-naar-site-VPN voor gebruik met Azure files](storage-files-configure-s2s-vpn.md).
    - [ExpressRoute](../../expressroute/expressroute-introduction.md)configureren.

- Als u van plan bent om de Artikel CLI te gebruiken, [installeert u de nieuwste versie](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Het NFS 4,1-protocol registreren
Als u de Azure PowerShell-module of de Azure CLI gebruikt, registreert u uw functie met de volgende opdrachten:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Gebruik Azure PowerShell of Azure CLI om de NFS 4,1-functie voor Azure Files te registreren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

De registratie goedkeuring kan tot een uur duren. Gebruik de volgende opdrachten om te controleren of de registratie is voltooid:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Gebruik Azure PowerShell of Azure CLI om de registratie van de NFS 4,1-functie voor Azure Files te controleren. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>Een FileStorage-opslag account maken
NFS 4,1-shares zijn momenteel alleen beschikbaar als Premium-bestands shares. Als u een Premium-bestands share met NFS 4,1-protocol ondersteuning wilt implementeren, moet u eerst een FileStorage-opslag account maken. Een opslag account is een object op het hoogste niveau in azure dat een gedeelde opslag groep vertegenwoordigt die kan worden gebruikt voor het implementeren van meerdere Azure-bestands shares.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een FileStorage-opslag account wilt maken, gaat u naar de Azure Portal.

1. Selecteer in het Azure Portal **opslag accounts** in het menu links.

    ![Azure Portal hoofd pagina Selecteer een opslag account](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. Kies in het venster **Opslagaccounts** dat wordt weergegeven de optie **Toevoegen**.
3. Selecteer het abonnement waarin u het opslagaccount wilt maken.
4. De resource groep selecteren waarin het opslag account moet worden gemaakt

5. Voer vervolgens een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.
6. Selecteer een locatie voor uw opslagaccount of gebruik de standaardlocatie.
7. Selecteer **Premium** voor **prestaties** .

    U moet **Premium** voor **FileStorage** selecteren als beschik bare optie in de vervolg keuzelijst **account soort** .

8. Selecteer **account type** en kies **FileStorage**.
9. Zorg ervoor dat **replicatie** is ingesteld op de standaard waarde van **lokaal redundante opslag (LRS)**.

    ![Een opslag account maken voor een Premium-bestands share](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.
11. Selecteer **Maken**.

Als uw opslag account is gemaakt, gaat u naar de resource.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u een FileStorage-opslag account wilt maken, opent u een Power shell-prompt en voert u de volgende opdrachten uit, moet u lid worden van vervangen `<resource-group>` en `<storage-account>` met de juiste waarden voor uw omgeving.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u een FileStorage-opslag account wilt maken, opent u de Terminal en voert u de volgende opdrachten uit, moet u lid worden van vervangen `<resource-group>` en `<storage-account>` met de juiste waarden voor uw omgeving.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
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
    
   Zie [bestaande Windows Power shell upgraden](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell) voor informatie over het bijwerken van uw versie van Power shell
    
1. Installeer de nieuwste versie van de PowershellGet-module.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Sluit de Power shell-console en open deze opnieuw.

1. Installeer de module **AZ. Storage** preview versie **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van Power shell-modules.
   
1. Als u een Premium-bestands share met de module Azure PowerShell wilt maken, gebruikt u de cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

    > [!NOTE]
    > Premium-bestands shares worden gefactureerd met behulp van een ingericht model. De ingerichte grootte van de share wordt `QuotaGiB` hieronder aangegeven. Zie [Wat is het ingerichte model](understanding-billing.md#provisioned-model) en de [pagina met Azure files prijzen](https://azure.microsoft.com/pricing/details/storage/files/)voor meer informatie.

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u een Premium-bestands share wilt maken met de Azure CLI, gebruikt u de opdracht [AZ Storage share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Premium-bestands shares worden gefactureerd met behulp van een ingericht model. De ingerichte grootte van de share wordt `quota` hieronder aangegeven. Zie [Wat is het ingerichte model](understanding-billing.md#provisioned-model) en de [pagina met Azure files prijzen](https://azure.microsoft.com/pricing/details/storage/files/)voor meer informatie.

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>Volgende stappen
Nu u een NFS-share hebt gemaakt om deze te gebruiken, moet u deze koppelen aan uw Linux-client. Zie [een NFS-share koppelen](storage-files-how-to-mount-nfs-shares.md)voor meer informatie.

Zie [problemen met Azure NFS-bestands shares oplossen](storage-troubleshooting-files-nfs.md)als u problemen ondervindt.