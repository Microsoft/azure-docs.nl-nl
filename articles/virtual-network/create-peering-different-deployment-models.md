---
title: Een peering voor het virtuele netwerk van Azure maken-verschillende implementatie modellen-hetzelfde abonnement | Microsoft Docs
description: Meer informatie over het maken van een virtuele netwerk peering tussen virtuele netwerken die zijn gemaakt via verschillende Azure-implementatie modellen die in hetzelfde Azure-abonnement bestaan.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 1ff9fcbb693f7e606c07985f9bce9acd60c5591a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222969"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Een peering voor een virtueel netwerk maken-verschillende implementatie modellen, hetzelfde abonnement

In deze zelf studie leert u hoe u een virtueel netwerk kunt maken dat is gekoppeld aan virtuele netwerken die via verschillende implementatie modellen zijn gemaakt. Beide virtuele netwerken bevinden zich in hetzelfde abonnement. Met peering van twee virtuele netwerken kunnen bronnen in verschillende virtuele netwerken met elkaar communiceren met dezelfde band breedte en latentie, alsof de resources zich in hetzelfde virtuele netwerk bevonden. Meer informatie over [peering van virtuele netwerken](virtual-network-peering-overview.md).

De stappen voor het maken van peering op een virtueel netwerk zijn verschillend, afhankelijk van het feit of de virtuele netwerken zich in dezelfde of verschillende abonnementen bevinden, en welk [Azure-implementatie model](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuele netwerken zijn gemaakt via. Meer informatie over het maken van een virtuele netwerk-peering in andere scenario's door te klikken op het scenario in de volgende tabel:

|Azure-implementatiemodel  | Azure-abonnement  |
|--------- |---------|
|[Beide in Resource Manager](tutorial-connect-virtual-networks-portal.md) |Hetzelfde|
|[Beide in Resource Manager](create-peering-different-subscriptions.md) |Verschillend|
|[Eén in Resource Manager, één klassiek](create-peering-different-deployment-models-subscriptions.md) |Verschillend|

Kan geen peering voor het virtuele netwerk maken tussen twee virtuele netwerken die zijn geïmplementeerd via het klassieke implementatie model. Als u virtuele netwerken wilt verbinden die beide zijn gemaakt via het klassieke implementatie model, kunt u een Azure- [VPN gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruiken om verbinding te maken met de virtuele netwerken.

Deze zelf studie vergelijkt virtuele netwerken in dezelfde regio. U kunt ook virtuele netwerken in verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region)peeren. Het is raadzaam om vertrouwd te raken met de [vereisten en beperkingen van de peering](virtual-network-manage-peering.md#requirements-and-constraints) voordat u peering van virtuele netwerken uitvoert.

U kunt de Azure Portal, de Azure [-opdracht regel interface](#cli) (CLI), Azure [Power shell](#powershell)of een Azure Resource Manager sjabloon gebruiken om een peering voor het virtuele netwerk te maken. Klik op een van de vorige koppelingen van het hulp programma om rechtstreeks naar de stappen voor het maken van een virtuele netwerk-peering te gaan met het hulp programma van uw keuze.

## <a name="create-peering---azure-portal"></a>Peering-Azure Portal maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#requirements-and-constraints)voor een lijst met machtigingen.
2. Klik op **+ Nieuw**, klik op **netwerken** en klik vervolgens op **virtueel netwerk**.
3. Voer op de Blade **virtueel netwerk maken** een waarde in of selecteer waarden voor de volgende instellingen en klik vervolgens op **maken**:
    - **Naam**: *myVnet1*
    - **Adres ruimte**: *10.0.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Adres bereik van subnet**: *10.0.0.0/24*
    - **Abonnement**: Selecteer uw abonnement
    - **Resource groep**: Selecteer **nieuwe maken** en voer *myResourceGroup* in
    - **Locatie**: *VS-Oost*
4. Klik op **+ New**. Typ in het vak **Marketplace doorzoeken de** tekst *virtueel netwerk*. Klik op **virtueel netwerk** wanneer dit wordt weer gegeven in de zoek resultaten.
5. Selecteer in de Blade **virtueel netwerk** de optie **klassiek** in het vak **een implementatie model selecteren** en klik vervolgens op **maken**.
6. Voer op de Blade **virtueel netwerk maken** een waarde in of selecteer waarden voor de volgende instellingen en klik vervolgens op **maken**:
    - **Naam**: *myVnet2*
    - **Adres ruimte**: *10.1.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Adres bereik van subnet**: *10.1.0.0/24*
    - **Abonnement**: Selecteer uw abonnement
    - **Resource groep**: Selecteer **bestaande gebruiken** en selecteer *myResourceGroup*
    - **Locatie**: *VS-Oost*
7. Typ *myResourceGroup* in het vak **Zoek resources** bovenaan de portal. Klik op **myResourceGroup** wanneer deze wordt weer gegeven in de zoek resultaten. Er wordt een Blade voor de **myresourcegroup** -resource groep weer gegeven. De resource groep bevat de twee virtuele netwerken die zijn gemaakt in de vorige stappen.
8. Klik op **myVNet1**.
9. Klik op de Blade **myVnet1** die wordt weer gegeven op **peerings** in de verticale lijst met opties aan de linkerkant van de Blade.
10. Klik op de Blade **myVnet1-peerings** die verschijnt op **+ toevoegen**
11. Voer op de Blade **peering toevoegen** die wordt weer gegeven, de volgende opties in of Selecteer deze en klik vervolgens op **OK**:
     - **Naam**: *myVnet1ToMyVnet2*
     - **Implementatie model van het virtuele netwerk**: Selecteer **klassiek**.
     - **Abonnement**: Selecteer uw abonnement
     - **Virtueel netwerk**: Klik op **een virtueel netwerk kiezen** en klik vervolgens op **myVnet2**.
     - **Toegang tot virtueel netwerk toestaan:** Zorg ervoor dat **ingeschakeld** is geselecteerd.
    In deze zelf studie worden geen andere instellingen gebruikt. Meer informatie over alle instellingen voor peering vindt u in [peering van virtuele netwerken beheren](virtual-network-manage-peering.md#create-a-peering).
12. Nadat u in de vorige stap op **OK** hebt geklikt, wordt de Blade **peering toevoegen** gesloten en wordt de Blade **myVnet1-peerings** opnieuw weer geven. Na enkele seconden wordt de peering die u hebt gemaakt, weer gegeven op de Blade. **Verbonden** wordt weer gegeven in de kolom **peering status** voor de **myVnet1ToMyVnet2** -peering die u hebt gemaakt.

    De peering is nu tot stand gebracht. Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u gebruikmaakt van de standaard naam omzetting van Azure voor de virtuele netwerken, kunnen de bronnen in de virtuele netwerken geen namen omzetten over de virtuele netwerken. Als u namen wilt omzetten in virtuele netwerken in een peering, moet u uw eigen DNS-server maken. Meer informatie over het instellen van [naam omzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Optioneel**: Hoewel het maken van virtuele machines niet in deze zelf studie wordt behandeld, kunt u een virtuele machine in elk virtueel netwerk maken en verbinding met de ene virtuele machine maken om de verbinding te valideren.
14. **Optioneel**: als u de resources die u in deze zelf studie maakt, wilt verwijderen, voert u de stappen uit in de sectie [resources verwijderen](#delete-portal) van dit artikel.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Peering maken-Azure CLI

Voer de volgende stappen uit met behulp van de klassieke Azure-CLI en de Azure CLI. U kunt de stappen van de Azure Cloud Shell volt ooien door alleen de knop **proberen** te selecteren in een van de volgende stappen, of door de [klassieke cli](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) en [cli](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) te installeren en de opdrachten op de lokale computer uit te voeren.

1. Als u de Cloud Shell gebruikt, gaat u verder met stap 2, want de Cloud Shell meldt u automatisch aan bij Azure. Open een opdracht sessie en meld u aan bij Azure met behulp van de `azure login` opdracht.
2. Voer de CLI in Service Management-modus uit door de opdracht in te voeren `azure config mode asm` .
3. Voer de volgende opdracht in om het virtuele netwerk (klassiek) te maken:

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Voer het volgende bash CLI-script uit met behulp van de CLI, niet de klassieke CLI. Zie [de Azure cli installeren in Windows](/cli/azure/install-azure-cli-windows)voor opties voor het uitvoeren van bash CLI-scripts op Windows-computers.

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. Maak een virtuele netwerk peering tussen de twee virtuele netwerken die zijn gemaakt via de verschillende implementatie modellen met behulp van de CLI. Kopieer het volgende script naar een tekst editor op uw PC. Vervang door `<subscription id>` uw abonnements-id. Als u uw abonnements-ID niet weet, voert u de `az account show` opdracht in. De waarde voor **id** in de uitvoer is uw abonnements-id. Plak het gewijzigde script in uw CLI-sessie en druk vervolgens op `Enter` .

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. Nadat het script is uitgevoerd, controleert u de peering voor het virtuele netwerk (Resource Manager). Kopieer de volgende opdracht, plak deze in uw CLI-sessie en druk vervolgens op `Enter` :

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   De uitvoer toont een **verbinding** in de kolom **PeeringState** .

   Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u gebruikmaakt van de standaard naam omzetting van Azure voor de virtuele netwerken, kunnen de bronnen in de virtuele netwerken geen namen omzetten over de virtuele netwerken. Als u namen wilt omzetten in virtuele netwerken in een peering, moet u uw eigen DNS-server maken. Meer informatie over het instellen van [naam omzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Optioneel**: Hoewel het maken van virtuele machines niet in deze zelf studie wordt behandeld, kunt u een virtuele machine in elk virtueel netwerk maken en verbinding met de ene virtuele machine maken om de verbinding te valideren.
8. **Optioneel**: als u de resources die u in deze zelf studie maakt, wilt verwijderen, voert u de stappen in [resources verwijderen](#delete-cli) in dit artikel uit.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Peering maken-Power shell

1. Installeer de meest recente versie van de Power shell [Azure](https://www.powershellgallery.com/packages/Azure) -en [AZ](https://www.powershellgallery.com/packages/Az/) -modules. Zie [Overzicht van Azure PowerShell](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json) als u nog geen ervaring hebt met Azure PowerShell.
2. Start een Power shell-sessie.
3. Meld u in Power shell aan bij Azure door de opdracht in te voeren `Add-AzureAccount` . Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#requirements-and-constraints)voor een lijst met machtigingen.
4. Als u een virtueel netwerk (klassiek) met Power shell wilt maken, moet u een nieuw netwerk configuratie bestand maken of wijzigen. Meer informatie over het [exporteren, bijwerken en importeren van netwerk configuratie bestanden](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Het bestand moet het volgende **VirtualNetworkSite** -element bevatten voor het virtuele netwerk dat in deze zelf studie wordt gebruikt:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Het importeren van een gewijzigd netwerk configuratie bestand kan wijzigingen veroorzaken in bestaande virtuele netwerken (klassiek) in uw abonnement. Zorg ervoor dat u het vorige virtuele netwerk alleen toevoegt en dat u geen bestaande virtuele netwerken wijzigt of verwijdert uit uw abonnement.
5. Meld u aan bij Azure om het virtuele netwerk (Resource Manager) te maken door de opdracht in te voeren `Connect-AzAccount` . Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#requirements-and-constraints)voor een lijst met machtigingen.
6. Maak een resource groep en een virtueel netwerk (Resource Manager). Kopieer het script, plak het in Power shell en druk vervolgens op `Enter` .

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Maak een virtuele netwerk peering tussen de twee virtuele netwerken die zijn gemaakt via de verschillende implementatie modellen. Kopieer het volgende script naar een tekst editor op uw PC. Vervang door `<subscription id>` uw abonnements-id. Als u uw abonnements-ID niet weet, voert u de `Get-AzSubscription` opdracht in om deze weer te geven. De waarde voor **id** in de geretourneerde uitvoer is uw abonnements-id. Als u het script wilt uitvoeren, kopieert u het gewijzigde script uit de tekst editor, klikt u met de rechter muisknop in uw Power shell-sessie en drukt u vervolgens op `Enter` .

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Nadat het script is uitgevoerd, controleert u de peering voor het virtuele netwerk (Resource Manager). Kopieer de volgende opdracht, plak deze in uw Power shell-sessie en druk vervolgens op `Enter` :

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    De uitvoer toont een **verbinding** in de kolom **PeeringState** .

    Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u gebruikmaakt van de standaard naam omzetting van Azure voor de virtuele netwerken, kunnen de bronnen in de virtuele netwerken geen namen omzetten over de virtuele netwerken. Als u namen wilt omzetten in virtuele netwerken in een peering, moet u uw eigen DNS-server maken. Meer informatie over het instellen van [naam omzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Optioneel**: Hoewel het maken van virtuele machines niet in deze zelf studie wordt behandeld, kunt u een virtuele machine in elk virtueel netwerk maken en verbinding met de ene virtuele machine maken om de verbinding te valideren.
10. **Optioneel**: als u de resources die u in deze zelf studie maakt, wilt verwijderen, voert u de stappen in [resources verwijderen](#delete-powershell) in dit artikel uit.

## <a name="delete-resources"></a><a name="delete"></a>Resources verwijderen

Wanneer u deze zelf studie hebt voltooid, kunt u de resources die u in de zelf studie hebt gemaakt, verwijderen, zodat u geen gebruik hoeft te maken van de kosten. Als u een resource groep verwijdert, worden ook alle resources in de resource groep verwijderd.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Voer in het zoekvak van de portal **myResourceGroup** in. Klik in de zoek resultaten op **myResourceGroup**.
2. Klik op de Blade **myResourceGroup** op het pictogram **verwijderen** .
3. Als u het verwijderen wilt bevestigen, voert u in het vak **Geef de naam van de resource groep** **myResourceGroup** in en klikt u op **verwijderen**.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Gebruik de Azure CLI om het virtuele netwerk (Resource Manager) te verwijderen met de volgende opdracht:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Gebruik de klassieke CLI om het virtuele netwerk (klassiek) te verwijderen met de volgende opdrachten:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Voer de volgende opdracht in om het virtuele netwerk (Resource Manager) te verwijderen:

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Als u het virtuele netwerk (klassiek) met Power shell wilt verwijderen, moet u een bestaand netwerk configuratie bestand wijzigen. Meer informatie over het [exporteren, bijwerken en importeren van netwerk configuratie bestanden](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Verwijder het volgende VirtualNetworkSite-element voor het virtuele netwerk dat wordt gebruikt in deze zelf studie:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Het importeren van een gewijzigd netwerk configuratie bestand kan wijzigingen veroorzaken in bestaande virtuele netwerken (klassiek) in uw abonnement. Zorg ervoor dat u het vorige virtuele netwerk alleen verwijdert en dat u geen andere bestaande virtuele netwerken wijzigt of verwijdert uit uw abonnement.

## <a name="next-steps"></a>Volgende stappen

- U moet de belang rijke [beperkingen en het gedrag van peering voor het virtuele netwerk](virtual-network-manage-peering.md#requirements-and-constraints) zorgvuldig kennen voordat u een peering voor het virtuele netwerk maakt voor productie gebruik.
- Meer informatie over alle [instellingen van peering voor virtuele netwerken](virtual-network-manage-peering.md#create-a-peering).
- Meer informatie over het [maken van een hub-en-spoke-netwerk topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) met peering voor virtuele netwerken.