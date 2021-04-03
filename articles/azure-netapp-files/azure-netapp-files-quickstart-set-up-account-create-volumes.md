---
title: 'Quickstart: Azure NetApp Files en NFS-volume instellen'
description: 'Snelstart: beschrijft het snel instellen van Azure NetApp Files en het maken van een volume.'
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-azurecli, subject-armqs
ms.openlocfilehash: e31a1cef427062723adf4b45bd47cd8009630128
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888807"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Quickstart: Azure NetApp Files instellen en een NFS-volume maken

Dit artikel beschrijft het snel instellen van Azure NetApp Files en het maken van een volume.

In deze snelstart stelt u de volgende items in:

- Registratie voor Azure NetApp Files en NetApp Resourceprovider
- Een NetApp-account
- Een capaciteitspool
- Een NFS-volume voor Azure NetApp Files

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

> [!IMPORTANT]
> U moet toegang krijgen tot de Azure NetApp Files-service. Zie de [Azure NetApp Files waitlist submission page](https://aka.ms/azurenetappfiles) om toegang tot de service aan te vragen.  U moet wachten op een officiële bevestigingse-mail van het Azure NetApp Files-team voordat u verdergaat.

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registreren voor Azure NetApp Files en NetApp Resourceprovider

> [!NOTE]
> Dit kan enige tijd duren.
>

# <a name="portal"></a>[Portal](#tab/azure-portal)

Open zoals hierboven beschreven een Cloud Shell-sessie en volg deze Azure CLI-stappen voor registratiestappen met de portal:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voor dit artikel is Azure PowerShell-module Az-versie 2.6.0 of nieuwer vereist. Voer `Get-Module -ListAvailable Az` uit om uw huidige versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u wilt, kunt u in plaats daarvan Cloud Shell-console gebruiken in een PowerShell-sessie.

1. Geef in een PowerShell-opdrachtprompt (of PowerShell Cloud Shell-sessie) het abonnement op dat in de lijst met goedgekeurde abonnementen staat voor Azure NetApp Files:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Registreer de Azure Resourceprovider:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Bereid uw omgeving voor op Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a>[Sjabloon](#tab/template)

Geen.

Gebruik de Azure Portal, PowerShell of de Azure CLI om u te registreren voor Azure NetApp Files en de NetApp resourceprovider.

Zie [Registreren voor Azure NetApp Files](azure-netapp-files-register.md) voor meer informatie.

---

## <a name="create-a-netapp-account"></a>Een NetApp-account maken

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Voer in het zoekvak van Azure Portal **Azure NetApp Files** in, en selecteer vervolgens **Azure NetApp Files** in de lijst die wordt weergegeven.

      ![Selecteer Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Klik op **+ Toevoegen** om een nieuw NetApp-account te maken.

     ![Nieuw NetApp-account maken](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Geef in het venster Nieuwe NetApp-account de volgende gegevens op:
   1. Voer **myAccount1** in als accountnaam.
   2. Selecteer uw abonnement.
   3. Selecteer **Nieuwe maken** om een nieuwe resourcegroep te maken. Voer **myRG1** in als naam van de resourcegroep. Klik op **OK**.
   4. Selecteer uw accountlocatie.

      ![Venster Nieuwe NetApp-account](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)

      ![Venster Resourcegroepen](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Klik op **Maken** om uw nieuwe NetApp-account te maken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definieer enkele variabelen, zodat we ze tijdens de rest van de voorbeelden kunnen raadplegen:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ```

    > [!NOTE]
    > Raadpleeg [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) voor een lijst met ondersteunde regio's.
    > Om de regionaam te verkrijgen die wordt ondersteund door onze opdrachtregelprogramma's gebruikt u `Get-AzLocation | select Location`
    >

1. Maak een nieuwe resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Maak een Azure NetApp Files-account met de opdracht [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount):

    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Definieer enkele variabelen, zodat we ze tijdens de rest van de voorbeelden kunnen raadplegen:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ```

    > [!NOTE]
    > Raadpleeg [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) voor een lijst met ondersteunde regio's.
    > Om de regionaam te verkrijgen die wordt ondersteund door onze opdrachtregelprogramma's gebruikt u `az account list-locations --query "[].{Region:name}" --out table`
    >

2. Maak een nieuwe resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create):

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Maak een Azure NetApp Files-account met de opdracht [az netappfiles account create](/cli/azure/netappfiles/account#az-netappfiles-account-create):

    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a>[Sjabloon](#tab/template)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Het volgende code fragment laat zien hoe u een NetApp-account maakt in een Azure Resource Manager sjabloon (ARM-sjabloon) met behulp van de [Microsoft.NetApp/netAppAccounts](/azure/templates/microsoft.netapp/netappaccounts) resource. Als u de code wilt uitvoeren, downloadt u het [volledige ARM-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) van onze GitHub-opslag.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="set-up-a-capacity-pool"></a>Een capaciteitspool instellen

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de beheerblade Azure NetApp Files uw NetApp-account (**myAccount1**).

    ![NetApp-account selecteren](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)

2. Klik in de beheerblade Azure NetApp Files van uw NetApp-account op **Capaciteitspools**.

    ![Klikken op capaciteitspools](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)

3. Klik op **+ Pools toevoegen**.

    ![Klikken op Pools toevoegen](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Geef de gegevens op voor de capaciteitspool:
    * Voer **mypool1** in als naam van de pool.
    * Selecteer **Premium** als serviceniveau.
    * Specificeer **4 (TiB)** als grootte van de pool.
    * Gebruik het **Auto** QoS-type.

5. Klik op **Create**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Nieuwe variabelen definiëren voor toekomstige verwijzingen

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Een nieuwe capaciteitspool maken met de opdracht [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Nieuwe variabelen definiëren voor toekomstige verwijzingen

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Een nieuwe capaciteitspool maken met de opdracht [az netappfiles pool create](/cli/azure/netappfiles/pool#az-netappfiles-pool-create)

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

# <a name="template"></a>[Sjabloon](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

Het volgende codefragment laat zien hoe u een capaciteitspool maakt in een Azure Resource Manager sjabloon (ARM-sjabloon) met behulp van de [Microsoft.NetApp/netAppAccounts/capacityPools](/azure/templates/microsoft.netapp/netappaccounts/capacitypools) resource. Als u de code wilt uitvoeren, downloadt u het [volledige ARM-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) van onze GitHub-opslag.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Een NFS-volume maken voor Azure NetApp Files

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Klik in de beheerblade Azure NetApp Files van uw NetApp-account op **Volumes**.

    ![Klikken op Volumes](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)

2. Klik op **+ Volume toevoegen**.

    ![Klikken op Volumes toevoegen](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)

3. Geef de gegevens op voor het volume in het venster Volume maken:
   1. Voer **myvol1** in als volumenaam.
   2. Selecteer uw capaciteitspool (**mypool1**).
   3. Gebruik de standaardwaarde als quotum.
   4. Klik onder virtueel netwerk op **Nieuw maken** om een nieuw virtueel Azure-netwerk (Vnet) te maken.  Geef de volgende informatie op:
       * Voer **myvnet1** in als Vnet-naam.
       * Geef een adresruimte op voor uw instelling, bijvoorbeeld: 10.7.0.0/16
       * Voer **myANFsubnet** in als subnetnaam.
       * Specificeer het adresbereik van het subnet, bijvoorbeeld: 10.7.0.0/24. U kunt het speciale subnet niet delen met andere bronnen.
       * Selecteer **Microsoft.NetApp/volumes** voor delegatie van het subnet.
       * Klik op **OK** om het Vnet te maken.
   5. Selecteer in subnet het zojuist gemaakte Vnet (**myvnet1**) als gemachtigde subnet.

      ![Een volume maken](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)

      ![Venster een virtueel netwerk maken](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)

4. Klik op **Protocol** en voer de volgende acties uit:
    * Selecteer **NFS** als protocoltype voor het volume.
    * Geef **myfilepath1** op als het bestandspad dat wordt gebruikt om het exportpad voor het volume te maken.
    * Selecteer de NFS-versie (**NFSv3** of **NFSv4.1**) voor het volume.
      Raadpleeg [opmerkingen](azure-netapp-files-create-volumes.md#considerations) en [best practice](azure-netapp-files-create-volumes.md#best-practice) voor informatie over NFS-versies.

    ![NFS-protocol opgeven voor snelstart](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Klik op **Controleren + maken**.

    ![Venster Controleren en maken](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)

6. Controleer de gegevens voor het volume en klik vervolgens op **Maken**.
    Het gemaakte volume verschijn in de blade Volumes.

    ![Volume gemaakt](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Maak een subnetdelegatie naar 'Microsoft.NetApp/volumes' met de opdracht [New-AzDelegation](/powershell/module/az.network/new-azdelegation).

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Maak een subnetconfiguratie met de opdracht [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig).

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Maak het virtueel netwerk met de opdracht [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Maak het volume met de opdracht [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume).

    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Definieer enkele variabelen voor later gebruik.

    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Maak een virtueel netwerk zonder subnet met de opdracht [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create).

    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Maak een gedelegeerd subnet met de opdracht [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create).

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Maak het volume met de opdracht [az netappfiles volume create](/cli/azure/netappfiles/volume#az-netappfiles-volume-create).

    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

# <a name="template"></a>[Sjabloon](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

De volgende codefragmenten laten zien hoe u een VNet instelt en een Azure NetApp Files-volume maakt in een Azure Resource Manager-sjabloon (ARM-sjabloon). VNet-installatie maakt gebruik van de [Microsoft.Network/virtualNetworks](/azure/templates/Microsoft.Network/virtualNetworks) resource. Voor het maken van volumes wordt gebruikgemaakt van de [Microsoft.NetApp/netAppAccounts/capacityPools/volumes](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes) resource. Als u de code wilt uitvoeren, downloadt u het [volledige ARM-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) van onze GitHub-opslag.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>Resources opschonen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wanneer u klaar bent en als u wilt, kunt u de resourcegroep verwijderen. Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt.

> [!IMPORTANT]
> Alle resources binnen de resourcegroepen worden permanent verwijderd en kunnen niet worden hersteld.

1. Voer in het zoekvak van Azure Portal **Azure NetApp Files** in, en selecteer vervolgens **Azure NetApp Files** in de lijst die wordt weergegeven.

2. Klik in de lijst met abonnementen op de resourcegroep (myRG1) die u wilt verwijderen.

    ![Navigeren naar resourcegroepen](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Klik in de pagina van de resourcegroep op **Resourcegroep verwijderen**.

    ![Schermopname met de knop Resourcegroep verwijderen gemarkeerd.](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png)

    Een venster wordt geopend en toont een waarschuwing over de resources die worden verwijderd met de resourcegroep.

4. Voer de naam in van de resourcegroep (myRG1) om te bevestigen dat u de resourcegroep en alle resources daarin permanent wilt verwijderen en klik dan op **Verwijderen**.

    ![Bevestig verwijderen resourcegroep](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png )

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Wanneer u klaar bent en als u wilt, kunt u de resourcegroep verwijderen. Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt.

> [!IMPORTANT]
> Alle resources binnen de resourcegroepen worden permanent verwijderd en kunnen niet worden hersteld.

1. Verwijder de resourcegroep met de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Wanneer u klaar bent en als u wilt, kunt u de resourcegroep verwijderen. Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt.

> [!IMPORTANT]
> Alle resources binnen de resourcegroepen worden permanent verwijderd en kunnen niet worden hersteld.

1. U kunt de resourcegroep verwijderen met de opdracht [az group delete](/cli/azure/group#az-group-delete).

    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a>[Sjabloon](#tab/template)

Geen.

Gebruik de Azure Portal, PowerShell of de Azure CLI om de resourcegroep te verwijderen.

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)

> [!div class="nextstepaction"]
> [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)

> [!div class="nextstepaction"]
> [Een NFS-volume maken](azure-netapp-files-create-volumes.md)
