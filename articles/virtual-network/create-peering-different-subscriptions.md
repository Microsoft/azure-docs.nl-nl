---
title: Een VNet-peering maken-verschillende abonnementen
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken van een virtuele netwerk peering tussen virtuele netwerken die zijn gemaakt via Resource Manager en die voor komen in verschillende Azure-abonnementen in dezelfde of een andere Azure Active Directory Tenant.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: kumud
ms.openlocfilehash: 1f8b7162787e870337918794f353ab8dc94c1965
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518242"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions-and-azure-active-directory-tenants"></a>Een peering voor een virtueel netwerk maken-Resource Manager, verschillende abonnementen en Azure Active Directory tenants

In deze zelf studie leert u hoe u een virtueel netwerk met peering maakt tussen virtuele netwerken die zijn gemaakt via Resource Manager. De virtuele netwerken bevinden zich in verschillende abonnementen die deel kunnen uitmaken van verschillende Azure Active Directory-tenants (Azure AD). Met peering van twee virtuele netwerken kunnen bronnen in verschillende virtuele netwerken met elkaar communiceren met dezelfde band breedte en latentie, alsof de resources zich in hetzelfde virtuele netwerk bevonden. Meer informatie over [peering van virtuele netwerken](virtual-network-peering-overview.md).

De stappen voor het maken van peering op een virtueel netwerk zijn verschillend, afhankelijk van het feit of de virtuele netwerken zich in dezelfde of verschillende abonnementen bevinden, en welk [Azure-implementatie model](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuele netwerken zijn gemaakt via. Meer informatie over het maken van een virtuele netwerk-peering in andere scenario's door het scenario te selecteren in de volgende tabel:

|Azure-implementatiemodel  | Azure-abonnement  |
|--------- |---------|
|[Beide in Resource Manager](tutorial-connect-virtual-networks-portal.md) |Hetzelfde|
|[Eén in Resource Manager, één klassiek](create-peering-different-deployment-models.md) |Hetzelfde|
|[Eén in Resource Manager, één klassiek](create-peering-different-deployment-models-subscriptions.md) |Verschillend|

U kunt geen virtuele netwerk peering maken tussen twee virtuele netwerken die zijn geïmplementeerd via het klassieke implementatie model. Als u virtuele netwerken wilt verbinden die beide zijn gemaakt via het klassieke implementatie model, kunt u een Azure- [VPN gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruiken om verbinding te maken met de virtuele netwerken.

Deze zelf studie vergelijkt virtuele netwerken in dezelfde regio. U kunt ook virtuele netwerken in verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region)peeren. Het is raadzaam om vertrouwd te raken met de [vereisten en beperkingen van de peering](virtual-network-manage-peering.md#requirements-and-constraints) voordat u peering van virtuele netwerken uitvoert.

U kunt de [Azure Portal](#portal), de Azure [-opdracht regel interface](#cli) (CLI), Azure [Power shell](#powershell)of een [Azure Resource Manager sjabloon](#template) gebruiken om een peering voor het virtuele netwerk te maken. Selecteer een van de vorige koppelingen van het hulp programma om rechtstreeks naar de stappen voor het maken van een virtuele netwerk-peering te gaan met het hulp programma van uw keuze.

Als de virtuele netwerken zich in verschillende abonnementen bevinden en de abonnementen zijn gekoppeld aan verschillende Azure Active Directory tenants, voert u de volgende stappen uit voordat u doorgaat:
1. Voeg de gebruiker toe van elke Active Directory Tenant als [gast gebruiker](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) in de tegenovergestelde Azure Active Directory Tenant.
1. Elke gebruiker moet de uitnodiging voor gastgebruiker accepteren van de tegenovergestelde Azure Active Directory-tenant.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Peering-Azure Portal maken

In de volgende stappen worden verschillende accounts voor elk abonnement gebruikt. Als u een account gebruikt dat machtigingen heeft voor beide abonnementen, kunt u hetzelfde account voor alle stappen gebruiken, de stappen voor het aanmelden bij de portal overs Laan en de stappen voor het toewijzen van andere gebruikers machtigingen voor de virtuele netwerken overs Laan.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als *GebruikerA*. Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
2. Selecteer **+ een resource maken**, selecteer **netwerken** en selecteer vervolgens **virtueel netwerk**.
3. Selecteer of typ de volgende voorbeeld waarden voor de volgende instellingen en selecteer vervolgens **maken**:
    - **Naam**: *myVnetA*
    - **Adres ruimte**: *10.0.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Adres bereik van subnet**: *10.0.0.0/24*
    - **Abonnement**: Selecteer abonnement A.
    - **Resource groep**: Selecteer **nieuwe maken** en voer *myResourceGroupA* in
    - **Locatie**: *VS-Oost*
4. Typ *myVnetA* in het vak **Zoek resources** bovenaan de portal. Selecteer **myVnetA** wanneer deze wordt weer gegeven in de zoek resultaten.
5. Selecteer **toegangs beheer (IAM)** in de verticale lijst met opties aan de linkerkant.
6. Selecteer **+ roltoewijzing toevoegen** onder **myVnetA-toegangs beheer (IAM)**.
7. Selecteer **netwerkinzender** in het vak **rol** .
8. Selecteer in het vak **selecteren** *UserB* of typ het e-mail adres van UserB om ernaar te zoeken.
9. Selecteer **Opslaan**.
10. Selecteer onder **myVnetA-toegangs beheer (IAM)** **Eigenschappen** in de verticale lijst met opties aan de linkerkant. Kopieer de **resource-id** die wordt gebruikt in een latere stap. De resource-ID is vergelijkbaar met het volgende voor beeld: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA` .
11. Meld u bij de portal aan als GebruikerA en meld u aan als UserB.
12. Voer de stappen 2-3 uit, voer de volgende waarden in in stap 3:

    - **Naam**: *myVnetB*
    - **Adres ruimte**: *10.1.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Adres bereik van subnet**: *10.1.0.0/24*
    - **Abonnement**: Selecteer abonnement B.
    - **Resource groep**: Selecteer **nieuwe maken** en voer *myResourceGroupB* in
    - **Locatie**: *VS-Oost*

13. Typ *myVnetB* in het vak **Zoek resources** bovenaan de portal. Selecteer **myVnetB** wanneer deze wordt weer gegeven in de zoek resultaten.
14. Onder **myVnetB** selecteert u **Eigenschappen** in de verticale lijst met opties aan de linkerkant. Kopieer de **resource-id** die wordt gebruikt in een latere stap. De resource-ID is vergelijkbaar met het volgende voor beeld: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB` .
15. Selecteer **toegangs beheer (IAM)** onder **myVnetB** en voer de stappen 5-10 voor myVnetB uit, en geef **GebruikerA** op in stap 8.
16. Meld u bij de portal aan als UserB en meld u aan als GebruikerA.
17. Typ *myVnetA* in het vak **Zoek resources** bovenaan de portal. Selecteer **myVnetA** wanneer deze wordt weer gegeven in de zoek resultaten.
18. Selecteer **myVnetA**.
19. Selecteer onder **instellingen** de optie **peerings**.
20. Selecteer onder **myVnetA-peerings** **+ toevoegen**
21. Voer onder **peering toevoegen**, of selecteren, de volgende opties in en selecteer vervolgens **OK**:
     - **Naam**: *myVnetAToMyVnetB*
     - **Implementatie model van het virtuele netwerk**: Selecteer **Resource Manager**.
     - **Ik weet wat mijn resource-id** is: Schakel dit selectie vakje in.
     - **Resource-id**: Voer de resource-id in van stap 14.
     - **Toegang tot virtueel netwerk toestaan:** Zorg ervoor dat **ingeschakeld** is geselecteerd.
    In deze zelf studie worden geen andere instellingen gebruikt. Meer informatie over alle instellingen voor peering vindt u in [peering van virtuele netwerken beheren](virtual-network-manage-peering.md#create-a-peering).
22. De peering die u hebt gemaakt, wordt kort gewacht na het selecteren van **OK** in de vorige stap. **Gestart** , wordt vermeld in de kolom **peering status** voor de **myVnetAToMyVnetB** -peering die u hebt gemaakt. U hebt myVnetA gekoppeld aan myVnetB, maar nu moet u myVnetB peeren naar myVnetA. De peering moet in beide richtingen worden gemaakt zodat resources in de virtuele netwerken met elkaar kunnen communiceren.
23. Meld u bij de portal aan als GebruikerA en meld u aan als UserB.
24. Voer de stappen 17-21 opnieuw uit voor myVnetB. In stap 21 benoemen we de peering *myVnetBToMyVnetA*, selecteert u *MyVnetA* voor het **virtuele netwerk** en voert u de id uit stap 10 in het vak **resource-id** in.
25. Een paar seconden na het selecteren van **OK** om de peering voor myVnetB te maken, wordt de **myVnetBToMyVnetA** -peering die u zojuist hebt gemaakt weer gegeven met **verbinding** in de kolom **status van peering** .
26. Meld u bij de portal aan als UserB en meld u aan als GebruikerA.
27. Voer de stappen 17-19 opnieuw uit. De **peering-status** voor de **myVnetAToVNetB** -peering is nu ook **verbonden**. De peering wordt tot stand gebracht nadat u **verbinding hebt** gemaakt in de kolom **peering status** voor beide virtuele netwerken in de peering. Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u gebruikmaakt van de standaard naam omzetting van Azure voor de virtuele netwerken, kunnen de bronnen in de virtuele netwerken geen namen omzetten over de virtuele netwerken. Als u namen wilt omzetten in virtuele netwerken in een peering, moet u uw eigen DNS-server maken. Meer informatie over het instellen van [naam omzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Optioneel**: Hoewel het maken van virtuele machines niet in deze zelf studie wordt behandeld, kunt u een virtuele machine in elk virtueel netwerk maken en verbinding met de ene virtuele machine maken om de verbinding te valideren.
29. **Optioneel**: als u de resources die u in deze zelf studie maakt, wilt verwijderen, voert u de stappen uit in de sectie [resources verwijderen](#delete-portal) van dit artikel.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Peering maken-Azure CLI

In deze zelf studie worden verschillende accounts voor elk abonnement gebruikt. Als u gebruikmaakt van een account met machtigingen voor beide abonnementen, kunt u hetzelfde account voor alle stappen gebruiken, de stappen voor het afmelden van Azure overs Laan en de regels van het script verwijderen waarmee gebruikers roltoewijzingen worden gemaakt. Vervang UserA@azure.com en UserB@azure.com in alle volgende scripts door de gebruikers namen die u gebruikt voor GebruikerA en UserB.

De volgende scripts:

- Vereist de Azure CLI-versie 2.0.4 of hoger. Voer `az --version` uit om de versie te bekijken. Als u een upgrade wilt uitvoeren, raadpleegt u [Azure cli installeren](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Werkt in een bash-shell. Zie [De Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows) voor opties voor het uitvoeren van Azure CLI-scripts in de Windows-client.

In plaats van de CLI en de bijbehorende afhankelijkheden te installeren, kunt u de Azure Cloud Shell gebruiken. De Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop **try it** in het onderstaande script, waarmee een Cloud shell wordt aangeroepen waarmee u zich kunt aanmelden bij uw Azure-account met.

1. Open een CLI-sessie en meld u aan bij Azure als GebruikerA met behulp van de `azure login` opdracht. Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
2. Kopieer het volgende script naar een tekst editor op uw PC, vervang door `<SubscriptionA-Id>` de id van abonnementa, kopieer het gewijzigde script, plak het in uw cli-sessie en druk op `Enter` . Als u uw abonnements-id niet weet, voert u de `az account show` opdracht in. De waarde voor **id** in de uitvoer is uw abonnements-id.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Meld u aan bij Azure als GebruikerA met de `az logout` opdracht en meld u als UserB aan bij Azure. Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
4. MyVnetB maken. Kopieer de inhoud van het script in stap 2 naar een tekst editor op uw PC. Vervang door `<SubscriptionA-Id>` de id van SubscriptionB. Wijzig 10.0.0.0/16 naar 10.1.0.0/16, Wijzig alles als in B en alle BS naar A. Kopieer het gewijzigde script, plak het in uw CLI-sessie en druk op `Enter` .
5. Meld u als UserB aan bij Azure en meld u aan bij Azure als GebruikerA.
6. Maak een virtuele netwerk peering van myVnetA naar myVnetB. Kopieer de volgende script inhoud naar een tekst editor op uw PC. Vervang door `<SubscriptionB-Id>` de id van SubscriptionB. Als u het script wilt uitvoeren, kopieert u het gewijzigde script, plakt u het in uw CLI-sessie en drukt u op ENTER.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. De peering-status van myVnetA weer geven.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    De status wordt **gestart**. De **verbinding** wordt gewijzigd wanneer u de peering hebt gemaakt voor MyVnetA van myVnetB.

8. Meld u aan bij Azure van Azure en meld u aan als UserB.
9. Maak de peering van myVnetB naar myVnetA. Kopieer de inhoud van het script in stap 6 naar een tekst editor op uw PC. Vervang door `<SubscriptionB-Id>` de id van abonnementa en Wijzig alles naar B en alle BS naar A. Nadat u de wijzigingen hebt aangebracht, kopieert u het gewijzigde script, plakt u het in uw CLI-sessie en drukt u op `Enter` .
10. De peering-status van myVnetB weer geven. Kopieer de inhoud van het script in stap 7 naar een tekst editor op uw PC. Wijzig A in B voor de namen van de resource groep en het virtuele netwerk, kopieer het script, plak het gewijzigde script in in uw CLI-sessie en druk vervolgens op `Enter` . De status van de peering is **verbonden**. De peering-status van myVnetA verandert in **verbonden** nadat u de peering hebt gemaakt van MyVnetB naar myVnetA. U kunt GebruikerA opnieuw aanmelden bij Azure en stap 7 opnieuw uitvoeren om de status van de peering van myVnetA te controleren.

    > [!NOTE]
    > De peering wordt pas tot stand gebracht als de peering-status is **verbonden** voor beide virtuele netwerken.

11. **Optioneel**: Hoewel het maken van virtuele machines niet in deze zelf studie wordt behandeld, kunt u een virtuele machine in elk virtueel netwerk maken en verbinding met de ene virtuele machine maken om de verbinding te valideren.
12. **Optioneel**: als u de resources die u in deze zelf studie maakt, wilt verwijderen, voert u de stappen in [resources verwijderen](#delete-cli) in dit artikel uit.

Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u gebruikmaakt van de standaard naam omzetting van Azure voor de virtuele netwerken, kunnen de bronnen in de virtuele netwerken geen namen omzetten over de virtuele netwerken. Als u namen wilt omzetten in virtuele netwerken in een peering, moet u uw eigen DNS-server maken. Meer informatie over het instellen van [naam omzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="create-peering---powershell"></a><a name="powershell"></a>Peering maken-Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze zelf studie worden verschillende accounts voor elk abonnement gebruikt. Als u gebruikmaakt van een account met machtigingen voor beide abonnementen, kunt u hetzelfde account voor alle stappen gebruiken, de stappen voor het afmelden van Azure overs Laan en de regels van het script verwijderen waarmee gebruikers roltoewijzingen worden gemaakt. Vervang UserA@azure.com en UserB@azure.com in alle volgende scripts door de gebruikers namen die u gebruikt voor GebruikerA en UserB.

1. Controleer of u Azure PowerShell versie 1.0.0 of hoger hebt. U kunt dit doen door de `Get-Module -Name Az` meest recente versie van de Power shell [AZ-module](/powershell/azure/install-az-ps)te installeren. Zie [Overzicht van Azure PowerShell](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json) als u nog geen ervaring hebt met Azure PowerShell.
2. Start een Power shell-sessie.
3. Meld u in Power shell aan bij Azure als GebruikerA door de opdracht in te voeren `Connect-AzAccount` . Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
4. Maak een resource groep en een virtueel netwerk A. Kopieer het volgende script naar een tekst editor op uw PC. Vervang door `<SubscriptionA-Id>` de id van abonnementa. Als u uw abonnements-id niet weet, voert u de `Get-AzSubscription` opdracht in om deze weer te geven. De waarde voor **id** in de geretourneerde uitvoer is uw abonnements-id. Als u het script wilt uitvoeren, kopieert u het gewijzigde script, plakt u het in Power shell en drukt u vervolgens op `Enter` .

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Meld u aan bij de gebruiker van Azure en meld u aan bij UserB. Het account waarmee u zich aanmeldt, moet de benodigde machtigingen hebben voor het maken van een virtuele netwerk peering. Zie [machtigingen voor peering voor virtuele netwerken](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
6. Kopieer de inhoud van het script in stap 4 naar een tekst editor op uw PC. Vervang door `<SubscriptionA-Id>` de id van het abonnement B. Wijzig 10.0.0.0/16 in 10.1.0.0/16. Wijzig alles naar B en alle in BS naar A. Als u het script wilt uitvoeren, kopieert u het gewijzigde script, plakt u het in Power shell en drukt u vervolgens op `Enter` .
7. Meld u UserB van Azure en meld u aan bij GebruikerA.
8. Maak de peering van myVnetA naar myVnetB. Kopieer het volgende script naar een tekst editor op uw PC. Vervang door `<SubscriptionB-Id>` de id van abonnement B. Als u het script wilt uitvoeren, kopieert u het gewijzigde script, plakt u in Power shell en drukt u vervolgens op `Enter` .

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. De peering-status van myVnetA weer geven.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    De status wordt **gestart**. De **verbinding** wordt gewijzigd wanneer u de peering hebt ingesteld op MyVnetA vanuit myVnetB.

10. Meld u aan bij de gebruiker van Azure en meld u aan bij UserB.
11. Maak de peering van myVnetB naar myVnetA. Kopieer de inhoud van het script in stap 8 naar een tekst editor op uw PC. Vervang door `<SubscriptionB-Id>` de id van het abonnement A en Wijzig alles als B en alle BS naar A. Als u het script wilt uitvoeren, kopieert u het gewijzigde script, plakt u het in Power shell en drukt u vervolgens op `Enter` .
12. De peering-status van myVnetB weer geven. Kopieer de inhoud van het script in stap 9 naar een tekst editor op uw PC. Wijzig A in B voor de namen van de resource groep en het virtuele netwerk. Als u het script wilt uitvoeren, plakt u het gewijzigde script in Power shell en drukt u vervolgens op `Enter` . De status is **verbonden**. De peering-status van **myVnetA** verandert in **verbonden** nadat u de peering hebt gemaakt van **myVnetB** naar **myVnetA**. U kunt GebruikerA opnieuw aanmelden bij Azure en stap 9 opnieuw uitvoeren om de status van de peering van myVnetA te controleren.

    > [!NOTE]
    > De peering wordt pas tot stand gebracht als de peering-status is **verbonden** voor beide virtuele netwerken.

    Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u gebruikmaakt van de standaard naam omzetting van Azure voor de virtuele netwerken, kunnen de bronnen in de virtuele netwerken geen namen omzetten over de virtuele netwerken. Als u namen wilt omzetten in virtuele netwerken in een peering, moet u uw eigen DNS-server maken. Meer informatie over het instellen van [naam omzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Optioneel**: Hoewel het maken van virtuele machines niet in deze zelf studie wordt behandeld, kunt u een virtuele machine in elk virtueel netwerk maken en verbinding met de ene virtuele machine maken om de verbinding te valideren.
14. **Optioneel**: als u de resources die u in deze zelf studie maakt, wilt verwijderen, voert u de stappen in [resources verwijderen](#delete-powershell) in dit artikel uit.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Een peering-Resource Manager-sjabloon maken

1. Als u een virtueel netwerk wilt maken en de juiste [machtigingen](virtual-network-manage-peering.md#permissions)wilt toewijzen, voert u de stappen in de secties [Portal](#portal), [Azure cli](#cli)of [Power shell](#powershell) van dit artikel uit.
2. Sla de tekst op die volgt op een bestand op uw lokale computer. Vervang door `<subscription ID>` de abonnements-id van de gebruiker. U kunt het bestand bijvoorbeeld opslaan als vnetpeeringA.jsop.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Meld u aan bij Azure als GebruikerA en implementeer de sjabloon met behulp van de [Portal](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [Power shell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file)of de [Azure cli](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file). Geef de bestands naam op die u hebt opgeslagen in stap 2 van de voorbeeld tekst van de JSON.
4. Kopieer de voor beeld-JSON van stap 2 naar een bestand op uw computer en breng wijzigingen aan in de regels die beginnen met:
   - **naam**: Wijzig *myVnetA/myVnetAToMyVnetB* in *myVnetB/myVnetBToMyVnetA*.
   - **id**: Vervang door `<subscription ID>` de abonnements-id van UserB en wijzig *myVnetB* in *myVnetA*.
5. Voer stap 3 opnieuw uit en meld u als UserB aan bij Azure.
6. **Optioneel**: Hoewel het maken van virtuele machines niet in deze zelf studie wordt behandeld, kunt u een virtuele machine in elk virtueel netwerk maken en verbinding met de ene virtuele machine maken om de verbinding te valideren.
7. **Optioneel**: als u de resources die u in deze zelf studie maakt, wilt verwijderen, voert u de stappen in de sectie [resources verwijderen](#delete) van dit artikel uit met behulp van de Azure Portal, Power shell of de Azure cli.

## <a name="delete-resources"></a><a name="delete"></a>Resources verwijderen
Wanneer u deze zelf studie hebt voltooid, kunt u de resources die u in de zelf studie hebt gemaakt, verwijderen, zodat u geen gebruik hoeft te maken van de kosten. Als u een resource groep verwijdert, worden ook alle resources in de resource groep verwijderd.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Meld u aan bij de Azure Portal als GebruikerA.
2. Voer in het zoekvak van de portal **myResourceGroupA** in. Selecteer **myResourceGroupA** in de zoek resultaten.
3. Selecteer **Verwijderen**.
4. Om het verwijderen te bevestigen, voert u in het vak **Geef de naam van de resource groep** **myResourceGroupA** in en selecteert u vervolgens **verwijderen**.
5. Meld u bij de portal aan als GebruikerA en meld u aan als UserB.
6. Voer de stappen 2-4 voor myResourceGroupB uit.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Meld u aan bij Azure als GebruikerA en voer de volgende opdracht uit:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Meld u aan bij Azure als GebruikerA en meld u aan als UserB.
3. Voer de volgende opdracht uit:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>Zo

1. Meld u aan bij Azure als GebruikerA en voer de volgende opdracht uit:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Meld u aan bij Azure als GebruikerA en meld u aan als UserB.
3. Voer de volgende opdracht uit:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Volgende stappen

- U moet de belang rijke [beperkingen en het gedrag van peering voor het virtuele netwerk](virtual-network-manage-peering.md#requirements-and-constraints) zorgvuldig kennen voordat u een peering voor het virtuele netwerk maakt voor productie gebruik.
- Meer informatie over alle [instellingen van peering voor virtuele netwerken](virtual-network-manage-peering.md#create-a-peering).
- Meer informatie over het [maken van een hub-en-spoke-netwerk topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) met peering voor virtuele netwerken.