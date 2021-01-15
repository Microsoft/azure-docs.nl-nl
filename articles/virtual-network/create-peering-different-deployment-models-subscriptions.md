---
title: Een peering voor het virtuele netwerk van Azure maken-verschillende implementatie modellen-verschillende abonnementen
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken van een virtuele netwerk peering tussen virtuele netwerken die zijn gemaakt via verschillende Azure-implementatie modellen die in verschillende Azure-abonnementen bestaan.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 29a64931ac92eacf8948ae067118b6b25198c85d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223462"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Een peering voor een virtueel netwerk maken-verschillende implementatie modellen en abonnementen

In deze zelf studie leert u hoe u een virtueel netwerk kunt maken dat is gekoppeld aan virtuele netwerken die via verschillende implementatie modellen zijn gemaakt. De virtuele netwerken bevinden zich in verschillende abonnementen. Met peering van twee virtuele netwerken kunnen bronnen in verschillende virtuele netwerken met elkaar communiceren met dezelfde band breedte en latentie, alsof de resources zich in hetzelfde virtuele netwerk bevonden. Meer informatie over [peering van virtuele netwerken](virtual-network-peering-overview.md).

De stappen voor het maken van peering op een virtueel netwerk zijn verschillend, afhankelijk van het feit of de virtuele netwerken zich in dezelfde of verschillende abonnementen bevinden, en welk [Azure-implementatie model](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuele netwerken zijn gemaakt via. Meer informatie over het maken van een virtuele netwerk-peering in andere scenario's door te klikken op het scenario in de volgende tabel:

|Azure-implementatiemodel  | Azure-abonnement  |
|--------- |---------|
|[Beide in Resource Manager](tutorial-connect-virtual-networks-portal.md) |Hetzelfde|
|[Beide in Resource Manager](create-peering-different-subscriptions.md) |Verschillend|
|[Eén in Resource Manager, één klassiek](create-peering-different-deployment-models.md) |Hetzelfde|

U kunt geen virtuele netwerk peering maken tussen twee virtuele netwerken die zijn geïmplementeerd via het klassieke implementatie model. In deze zelf studie worden virtuele netwerken gebruikt die zich in dezelfde regio bevinden. Deze zelf studie vergelijkt virtuele netwerken in dezelfde regio. U kunt ook virtuele netwerken in verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region)peeren. Het is raadzaam om vertrouwd te raken met de [vereisten en beperkingen van de peering](virtual-network-manage-peering.md#requirements-and-constraints) voordat u peering van virtuele netwerken uitvoert.

Bij het maken van een virtuele netwerk peering tussen virtuele netwerken die zich in verschillende abonnementen bevinden, kunnen de abonnementen aan dezelfde Azure Active Directory-Tenant zijn gekoppeld. Als u nog geen Azure Active Directory Tenant hebt, kunt u er snel [een maken](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant).

U kunt de [Azure Portal](#portal), de Azure [-opdracht regel interface](#cli) (CLI) of Azure [Power shell](#powershell) gebruiken om een peering voor het virtuele netwerk te maken. Klik op een van de vorige koppelingen van het hulp programma om rechtstreeks naar de stappen voor het maken van een virtuele netwerk-peering te gaan met het hulp programma van uw keuze.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Peering-Azure Portal maken

In deze zelf studie worden verschillende accounts voor elk abonnement gebruikt. Als u een account gebruikt dat machtigingen heeft voor beide abonnementen, kunt u hetzelfde account voor alle stappen gebruiken, de stappen voor het aanmelden bij de portal overs Laan en de stappen voor het toewijzen van andere gebruikers machtigingen voor de virtuele netwerken overs Laan.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als GebruikerA. Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
2. Klik op **+ Nieuw**, klik op **netwerken** en klik vervolgens op **virtueel netwerk**.
3. Voer op de Blade **virtueel netwerk maken** een waarde in of selecteer waarden voor de volgende instellingen en klik vervolgens op **maken**:
    - **Naam**: *myVnetA*
    - **Adres ruimte**: *10.0.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Adres bereik van subnet**: *10.0.0.0/24*
    - **Abonnement**: Selecteer abonnement A.
    - **Resource groep**: Selecteer **nieuwe maken** en voer *myResourceGroupA* in
    - **Locatie**: *VS-Oost*
4. Typ *myVnetA* in het vak **Zoek resources** bovenaan de portal. Klik op **myVnetA** wanneer deze wordt weer gegeven in de zoek resultaten. Er wordt een Blade voor het virtuele **myVnetA** -netwerk weer gegeven.
5. Klik op de Blade **myVnetA** die wordt weer gegeven op **toegangs beheer (IAM)** in de verticale lijst met opties aan de linkerkant van de Blade.
6. Klik op de Blade **myVnetA-toegangs beheer (IAM)** die wordt weer gegeven op **+ roltoewijzing toevoegen**.
7. In de Blade **roltoewijzing toevoegen** die wordt weer gegeven, selecteert u **Network Inzender** in het vak **rol** .
8. Selecteer in het vak **selecteren** UserB of typ het e-mail adres van UserB om ernaar te zoeken. De lijst met gebruikers die worden weer gegeven, is afkomstig uit dezelfde Azure Active Directory Tenant als het virtuele netwerk waarvoor u de peering instelt. Klik op UserB wanneer deze wordt weer gegeven in de lijst.
9. Klik op **Opslaan**.
10. Meld u bij de portal aan als GebruikerA en meld u aan als UserB.
11. Klik op **+ Nieuw**, typ *virtueel netwerk* in het vak **Zoeken in Marketplace** en klik vervolgens op **virtueel netwerk** in de zoek resultaten.
12. Selecteer in de Blade **Virtual Network** die wordt weer gegeven, de optie **klassiek** in het vak **een implementatie model selecteren** en klik vervolgens op **maken**.
13. In het vak virtueel netwerk maken (klassiek) dat wordt weer gegeven, voert u de volgende waarden in:

    - **Naam**: *myVnetB*
    - **Adres ruimte**: *10.1.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Adres bereik van subnet**: *10.1.0.0/24*
    - **Abonnement**: Selecteer abonnement B.
    - **Resource groep**: Selecteer **nieuwe maken** en voer *myResourceGroupB* in
    - **Locatie**: *VS-Oost*

14. Typ *myVnetB* in het vak **Zoek resources** bovenaan de portal. Klik op **myVnetB** wanneer deze wordt weer gegeven in de zoek resultaten. Er wordt een Blade voor het virtuele **myVnetB** -netwerk weer gegeven.
15. Klik op de Blade **myVnetB** die wordt weer gegeven op **Eigenschappen** in de verticale lijst met opties aan de linkerkant van de Blade. Kopieer de **resource-id** die wordt gebruikt in een latere stap. De resource-ID is vergelijkbaar met het volgende voor beeld: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Voer de stappen 5-9 voor myVnetB uit en voer **GebruikerA** in stap 8 in.
17. Meld u bij de portal aan als UserB en meld u aan als GebruikerA.
18. Typ *myVnetA* in het vak **Zoek resources** bovenaan de portal. Klik op **myVnetA** wanneer deze wordt weer gegeven in de zoek resultaten. Er wordt een Blade voor het virtuele **myVnet** -netwerk weer gegeven.
19. Klik op **myVnetA**.
20. Klik op de Blade **myVnetA** die wordt weer gegeven op **peerings** in de verticale lijst met opties aan de linkerkant van de Blade.
21. Klik op de Blade **myVnetA-peerings** die verschijnt op **+ toevoegen**
22. Voer op de Blade **peering toevoegen** die wordt weer gegeven, de volgende opties in of Selecteer deze en klik vervolgens op **OK**:
     - **Naam**: *myVnetAToMyVnetB*
     - **Implementatie model van het virtuele netwerk**: Selecteer **klassiek**.
     - **Ik weet wat mijn resource-id** is: Schakel dit selectie vakje in.
     - **Resource-id**: Voer de resource-id van myVnetB in van stap 15.
     - **Toegang tot virtueel netwerk toestaan:** Zorg ervoor dat **ingeschakeld** is geselecteerd.
    In deze zelf studie worden geen andere instellingen gebruikt. Meer informatie over alle instellingen voor peering vindt u in [peering van virtuele netwerken beheren](virtual-network-manage-peering.md#create-a-peering).
23. Nadat u in de vorige stap op **OK** hebt geklikt, wordt de Blade **peering toevoegen** gesloten en wordt de Blade **myVnetA-peerings** opnieuw weer geven. Na enkele seconden wordt de peering die u hebt gemaakt, weer gegeven op de Blade. **Verbonden** wordt weer gegeven in de kolom **peering status** voor de **myVnetAToMyVnetB** -peering die u hebt gemaakt. De peering is nu tot stand gebracht. Het is niet nodig om het virtuele netwerk (klassiek) te koppelen aan het virtuele netwerk (Resource Manager).

    Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u gebruikmaakt van de standaard naam omzetting van Azure voor de virtuele netwerken, kunnen de bronnen in de virtuele netwerken geen namen omzetten over de virtuele netwerken. Als u namen wilt omzetten in virtuele netwerken in een peering, moet u uw eigen DNS-server maken. Meer informatie over het instellen van [naam omzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Optioneel**: Hoewel het maken van virtuele machines niet in deze zelf studie wordt behandeld, kunt u een virtuele machine in elk virtueel netwerk maken en verbinding met de ene virtuele machine maken om de verbinding te valideren.
25. **Optioneel**: als u de resources die u in deze zelf studie maakt, wilt verwijderen, voert u de stappen uit in de sectie [resources verwijderen](#delete-portal) van dit artikel.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Peering maken-Azure CLI

In deze zelf studie worden verschillende accounts voor elk abonnement gebruikt. Als u gebruikmaakt van een account met machtigingen voor beide abonnementen, kunt u hetzelfde account voor alle stappen gebruiken, de stappen voor het afmelden van Azure overs Laan en de regels van het script verwijderen waarmee gebruikers roltoewijzingen worden gemaakt. Vervang UserA@azure.com en UserB@azure.com in alle volgende scripts door de gebruikers namen die u gebruikt voor GebruikerA en UserB. Voer de volgende stappen uit met behulp van de klassieke Azure-CLI en de Azure CLI. U kunt de stappen van de Azure Cloud Shell volt ooien door alleen de knop **proberen** te selecteren in een van de volgende stappen, of door de [klassieke cli](/cli/azure/install-classic-cli) en [cli](/cli/azure/install-azure-cli) te installeren en de opdrachten op de lokale computer uit te voeren.

1. Als u de Cloud Shell gebruikt, gaat u verder met stap 2, want de Cloud Shell meldt u automatisch aan bij Azure. Open een opdracht sessie en meld u aan bij Azure met behulp van de `azure login` opdracht.
2. Voer de klassieke CLI uit in Service Management-modus door de opdracht in te voeren `azure config mode asm` .
3. Voer de volgende klassieke CLI-opdracht in om het virtuele netwerk (klassiek) te maken:

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. De resterende stappen moeten worden uitgevoerd met behulp van een bash-shell met de Azure CLI (niet de klassieke CLI).
5. Kopieer het volgende script naar een tekst editor op uw PC. Vervang door `<SubscriptionB-Id>` uw abonnements-id. Als u uw abonnements-id niet weet, voert u de `az account show` opdracht in. De waarde voor **id** in de uitvoer is uw abonnements-id. Kopieer het gewijzigde script, plak het in uw cli-sessie en druk vervolgens op `Enter` .

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Wanneer u het virtuele netwerk (klassiek) in stap 4 hebt gemaakt, heeft Azure het virtuele netwerk in de resource groep voor *standaard netwerken* gemaakt.
6. Meld u UserB buiten Azure en meld u aan als GebruikerA in de CLI.
7. Maak een resource groep en een virtueel netwerk (Resource Manager). Kopieer het volgende script, plak het in uw CLI-sessie en druk vervolgens op `Enter` .

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. Maak een virtuele netwerk peering tussen de twee virtuele netwerken die zijn gemaakt via de verschillende implementatie modellen. Kopieer het volgende script naar een tekst editor op uw PC. Vervang door `<SubscriptionB-id>` uw abonnements-id. Als u uw abonnements-id niet weet, voert u de `az account show` opdracht in. De waarde voor **id** in de uitvoer is uw abonnements-id. Azure heeft het virtuele netwerk (klassiek) gemaakt dat u hebt gemaakt in stap 4 van een resource groep met de naam *standaard netwerk*. Plak het gewijzigde script in uw CLI-sessie en druk vervolgens op `Enter` .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Nadat het script is uitgevoerd, controleert u de peering voor het virtuele netwerk (Resource Manager). Kopieer het volgende script en plak het in uw CLI-sessie:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    De uitvoer toont een **verbinding** in de kolom **PeeringState** .

    Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u gebruikmaakt van de standaard naam omzetting van Azure voor de virtuele netwerken, kunnen de bronnen in de virtuele netwerken geen namen omzetten over de virtuele netwerken. Als u namen wilt omzetten in virtuele netwerken in een peering, moet u uw eigen DNS-server maken. Meer informatie over het instellen van [naam omzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Optioneel**: Hoewel het maken van virtuele machines niet in deze zelf studie wordt behandeld, kunt u een virtuele machine in elk virtueel netwerk maken en verbinding met de ene virtuele machine maken om de verbinding te valideren.
11. **Optioneel**: als u de resources die u in deze zelf studie maakt, wilt verwijderen, voert u de stappen in [resources verwijderen](#delete-cli) in dit artikel uit.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Peering maken-Power shell

In deze zelf studie worden verschillende accounts voor elk abonnement gebruikt. Als u gebruikmaakt van een account met machtigingen voor beide abonnementen, kunt u hetzelfde account voor alle stappen gebruiken, de stappen voor het afmelden van Azure overs Laan en de regels van het script verwijderen waarmee gebruikers roltoewijzingen worden gemaakt. Vervang UserA@azure.com en UserB@azure.com in alle volgende scripts door de gebruikers namen die u gebruikt voor GebruikerA en UserB. 

1. Installeer de meest recente versie van de Power shell [Azure](https://www.powershellgallery.com/packages/Azure) -en [AZ](https://www.powershellgallery.com/packages/Az) -modules. Zie [Overzicht van Azure PowerShell](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json) als u nog geen ervaring hebt met Azure PowerShell.
2. Start een Power shell-sessie.
3. Meld u in Power shell aan bij UserB-abonnement als UserB door de opdracht in te voeren `Add-AzureAccount` . Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
4. Als u een virtueel netwerk (klassiek) met Power shell wilt maken, moet u een nieuw netwerk configuratie bestand maken of wijzigen. Meer informatie over het [exporteren, bijwerken en importeren van netwerk configuratie bestanden](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Het bestand moet het volgende **VirtualNetworkSite** -element bevatten voor het virtuele netwerk dat in deze zelf studie wordt gebruikt:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. Meld u aan bij UserB-abonnement als UserB voor het gebruik van Resource Manager-opdrachten door de opdracht in te voeren `Connect-AzAccount` .
6. Gebruikers machtigingen toewijzen aan virtueel netwerk B. Kopieer het volgende script naar een tekst editor op uw PC en vervang door `<SubscriptionB-id>` de id van het abonnement B. Als u de abonnements-id niet weet, voert u de `Get-AzSubscription` opdracht in die u wilt weer geven. De waarde voor **id** in de geretourneerde uitvoer is uw abonnements-id. Azure heeft het virtuele netwerk (klassiek) gemaakt dat u hebt gemaakt in stap 4 van een resource groep met de naam *standaard netwerken*. Als u het script wilt uitvoeren, kopieert u het gewijzigde script, plakt u het in Power shell en drukt u vervolgens op `Enter` .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Meld u als UserB aan bij Azure en meld u aan bij het abonnement van de gebruiker als GebruikerA door de opdracht in te voeren `Connect-AzAccount` . Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
8. Maak het virtuele netwerk (Resource Manager) door het volgende script te kopiëren, in Power shell te plakken en vervolgens op `Enter` :

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Wijs UserB-machtigingen toe aan myVnetA. Kopieer het volgende script naar een tekst editor op uw PC en vervang door `<SubscriptionA-Id>` de id van abonnement a. Als u de abonnements-id niet weet, voert u de `Get-AzSubscription` opdracht in die u wilt weer geven. De waarde voor **id** in de geretourneerde uitvoer is uw abonnements-id. Plak de gewijzigde versie van het script in Power shell en druk vervolgens `Enter` om het uit te voeren.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Kopieer het volgende script naar een tekst editor op uw PC en vervang door `<SubscriptionB-id>` de id van het abonnement B. Als u een peer-myVnetA wilt myVNetB, kopieert u het gewijzigde script, plakt u het in Power shell en drukt u vervolgens op `Enter` .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Bekijk de peering-status van myVnetA door het volgende script te kopiëren, in Power shell te plakken en op te drukken `Enter` .

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    De status is **verbonden**. De **verbinding** wordt gewijzigd wanneer u de peering hebt ingesteld op MyVnetA vanuit myVnetB.

    Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u gebruikmaakt van de standaard naam omzetting van Azure voor de virtuele netwerken, kunnen de bronnen in de virtuele netwerken geen namen omzetten over de virtuele netwerken. Als u namen wilt omzetten in virtuele netwerken in een peering, moet u uw eigen DNS-server maken. Meer informatie over het instellen van [naam omzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Optioneel**: Hoewel het maken van virtuele machines niet in deze zelf studie wordt behandeld, kunt u een virtuele machine in elk virtueel netwerk maken en verbinding met de ene virtuele machine maken om de verbinding te valideren.
13. **Optioneel**: als u de resources die u in deze zelf studie maakt, wilt verwijderen, voert u de stappen in [resources verwijderen](#delete-powershell) in dit artikel uit.

## <a name="delete-resources"></a><a name="delete"></a>Resources verwijderen
Wanneer u deze zelf studie hebt voltooid, kunt u de resources die u in de zelf studie hebt gemaakt, verwijderen, zodat u geen gebruik hoeft te maken van de kosten. Als u een resource groep verwijdert, worden ook alle resources in de resource groep verwijderd.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Voer in het zoekvak van de portal **myResourceGroupA** in. Klik in de zoek resultaten op **myResourceGroupA**.
2. Klik op de Blade **myResourceGroupA** op het pictogram **verwijderen** .
3. Als u het verwijderen wilt bevestigen, voert u in het vak **Geef de naam van de resource groep** **myResourceGroupA** in en klikt u op **verwijderen**.
4. Typ *myVnetB* in het vak **Zoek resources** bovenaan de portal. Klik op **myVnetB** wanneer deze wordt weer gegeven in de zoek resultaten. Er wordt een Blade voor het virtuele **myVnetB** -netwerk weer gegeven.
5. Klik op de Blade **myVnetB** op **verwijderen**.
6. Klik op **Ja** in het vak **virtueel netwerk verwijderen** om de verwijdering te bevestigen.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Meld u aan bij Azure met de CLI om het virtuele netwerk (Resource Manager) te verwijderen met de volgende opdracht:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Meld u aan bij Azure met de klassieke CLI om het virtuele netwerk (klassiek) te verwijderen met de volgende opdrachten:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Voer bij de Power shell-opdracht prompt de volgende opdracht in om het virtuele netwerk (Resource Manager) te verwijderen:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Als u het virtuele netwerk (klassiek) met Power shell wilt verwijderen, moet u een bestaand netwerk configuratie bestand wijzigen. Meer informatie over het [exporteren, bijwerken en importeren van netwerk configuratie bestanden](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Verwijder het volgende VirtualNetworkSite-element voor het virtuele netwerk dat wordt gebruikt in deze zelf studie:

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
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