---
title: Een NFS-share maken - Azure Files (preview)
description: Meer informatie over het maken van een Azure-bestands share die kan worden aangesloten met behulp van het network file system-protocol.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b549c625f0a6ff0480eafc38f84d292e66350950
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717121"
---
# <a name="how-to-create-an-nfs-share"></a>Een NFS-share maken
Azure-bestands shares zijn volledig beheerde bestands shares die zich in de cloud verplaatsen. In dit artikel wordt beschreven hoe u een bestands share maakt die gebruikmaakt van het NFS-protocol. Zie Protocollen voor Azure-bestands delen voor meer informatie [over beide protocollen.](storage-files-compare-protocols.md)

## <a name="limitations"></a>Beperkingen
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionale beschikbaarheid
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Vereisten
- NFS-shares zijn alleen toegankelijk vanuit vertrouwde netwerken. Verbindingen met uw NFS-share moeten afkomstig zijn van een van de volgende bronnen:
    - Maak [een privé-eindpunt (aanbevolen)](storage-files-networking-endpoints.md#create-a-private-endpoint) of [beperk de toegang tot uw openbare eindpunt.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Configureer een punt-naar-site-VPN (P2S) in Linux voor gebruik met Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Configureer een site-naar-site-VPN voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md).
    - Configureer [ExpressRoute.](../../expressroute/expressroute-introduction.md)

- Als u van plan bent om de Artikel CLI te gebruiken, [installeert u de nieuwste versie](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Het NFS 4.1-protocol registreren
Als u de module Azure PowerShell of de Azure CLI gebruikt, registreert u uw functie met behulp van de volgende opdrachten:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Gebruik Azure PowerShell of Azure CLI om de NFS 4.1-functie te registreren voor Azure Files.

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

Registratiegoedkeuring kan tot een uur duren. Gebruik de volgende opdrachten om te controleren of de registratie is voltooid:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Gebruik Azure PowerShell of Azure CLI om de registratie van de NFS 4.1-functie voor de Azure Files. 

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

## <a name="create-a-filestorage-storage-account"></a>Een FileStorage-opslagaccount maken
Op dit moment zijn NFS 4.1-shares alleen beschikbaar als Premium-bestands shares. Als u een Premium-bestands share wilt implementeren met ondersteuning voor het NFS 4.1-protocol, moet u eerst een FileStorage-opslagaccount maken. Een opslagaccount is een object op het hoogste niveau in Azure dat een gedeelde opslaggroep vertegenwoordigt die kan worden gebruikt om meerdere Azure-bestands shares te implementeren.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een FileStorage-opslagaccount wilt maken, gaat u naar de Azure Portal.

1. Selecteer in Azure Portal menu aan **de linkerkant** Opslagaccounts.

    ![Azure Portal opslagaccount op de hoofdpagina.](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Kies in het venster **Opslagaccounts** dat wordt weergegeven de optie **Toevoegen**.
1. Selecteer het abonnement waarin u het opslagaccount wilt maken.
1. Selecteer de resourcegroep waarin u het opslagaccount wilt maken
1. Voer vervolgens een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.
1. Selecteer een locatie voor uw opslagaccount of gebruik de standaardlocatie.
1. Selecteer **bij Prestaties** de optie **Premium**.

    U moet **Premium selecteren** voor **Bestandsshares** om een beschikbare optie te zijn in de **vervolgkeuzeoptie Soort** account.

1. Voor **Premium-accounttype** kiest **u Bestandsshares.**

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Schermopname van de geselecteerde Premium-prestaties.":::

1. Laat **Replicatie** ingesteld op de standaardwaarde lokaal **redundante opslag (LRS).**
1. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.
1. Selecteer **Maken**.

Nadat de resource van uw opslagaccount is gemaakt, gaat u er naartoe.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u een FileStorage-opslagaccount wilt maken, opent u een PowerShell-prompt en voert u de volgende opdrachten uit. Vergeet niet om en te vervangen door de juiste waarden `<resource-group>` `<storage-account>` voor uw omgeving.

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
Als u een FileStorage-opslagaccount wilt maken, opent u uw terminal en voert u de volgende opdrachten uit. Vergeet niet om en te vervangen door de juiste `<resource-group>` `<storage-account>` waarden voor uw omgeving.

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

Nu u een FileStorage-account hebt gemaakt en de netwerken hebt geconfigureerd, kunt u een NFS-bestands share maken. Het proces is vergelijkbaar met het maken van een SMB-share. U **selecteert NFS** in plaats **van SMB** bij het maken van de share.

1. Navigeer naar uw opslagaccount en selecteer **Bestands shares.**
1. Selecteer **+ Bestands share** om een nieuwe bestands share te maken.
1. Noem de bestands share en selecteer een inrichtende capaciteit.
1. Bij **Protocol** **selecteert u NFS (preview)**.
1. Maak **een selectie voor Hoofdstoorzaak.**

    - Hoofdsuplet (standaardinstelling) - Toegang voor de externe superuser (hoofdmap) is ingesteld op UID (65534) en GID (65534).
    - Geen hoofdslellen: externe superuser (root) ontvangt toegang als root.
    - All squash: alle gebruikerstoegang is toe te staan aan UID (65534) en GID (65534).
    
1. Selecteer **Maken**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/files-nfs-create-share.png" alt-text="Schermopname van de blade voor het maken van een bestands share.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Zorg ervoor dat het .NET Framework is geïnstalleerd. Zie [Download .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Controleer met de volgende opdracht of de geïnstalleerde versie van PowerShell of hoger `5.1` is.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Zie Bestaande versies upgraden om uw versie van PowerShell [bij te Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. Installeer de nieuwste versie van de PowershellGet-module.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Sluit de PowerShell-console en open deze opnieuw.

1. Installeer de **Preview-module van Az.Storage** **versie 2.5.2-preview.**

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Zie De module Azure PowerShell installeren voor meer informatie over het installeren [van PowerShell Azure PowerShell module](/powershell/azure/install-az-ps)
   
1. Gebruik de cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) om een Premium-bestandsshare Azure PowerShell maken.

    > [!NOTE]
    > Premium-bestands shares worden gefactureerd met behulp van een ingericht model. De inrichten grootte van de share wordt hieronder `QuotaGiB` opgegeven. Zie Inzicht in het inrichtende [model en de](understanding-billing.md#provisioned-model) pagina Azure Files prijzen voor meer [informatie.](https://azure.microsoft.com/pricing/details/storage/files/)

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Gebruik de opdracht [az storage share create](/cli/azure/storage/share-rm) om een Premium-bestands share te maken met de Azure CLI.

> [!NOTE]
> Premium-bestands shares worden gefactureerd met behulp van een ingericht model. De inrichten grootte van de share wordt hieronder `quota` opgegeven. Zie Inzicht in het inrichtende [model en de](understanding-billing.md#provisioned-model) pagina Azure Files prijzen voor meer [informatie.](https://azure.microsoft.com/pricing/details/storage/files/)

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
Nu u een NFS-share hebt gemaakt, moet u deze aan uw Linux-client monteren om deze te kunnen gebruiken. Zie How [to mount an NFS share (Een NFS-share maken) voor meer informatie.](storage-files-how-to-mount-nfs-shares.md)

Zie Problemen met [Azure NFS-bestands](storage-troubleshooting-files-nfs.md)shares oplossen als u problemen ervaart.