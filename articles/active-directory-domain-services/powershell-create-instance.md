---
title: Azure DS Domain Services inschakelen met behulp van PowerShell | Microsoft Docs
description: Leer hoe u Azure Active Directory Domain Services configureert en inschakelt met behulp van Azure AD PowerShell en Azure PowerShell.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 03/10/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8056e95b731b1818e10d7415cb813d6aba0ec7fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149064"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Azure Active Directory Domain Services inschakelen met behulp van PowerShell

Azure AD DS (Active Directory Domain Services) biedt beheerde domeinservices - zoals domeindeelname, groepsbeleid, LDAP, Kerberos/NTLM-verificatie - die volledig compatibel zijn met Windows Server Active Directory. U gebruikt deze domeinservices zonder zelf domeincontrollers te implementeren, te beheren en er patches op toe te passen. Azure AD DS kan met uw bestaande Azure AD-tenant worden geïntegreerd. Met deze integratie kunnen gebruikers zich aanmelden met behulp van hun bedrijfsreferenties, en u kunt bestaande groepen en gebruikersaccounts gebruiken om de toegang tot resources te beveiligen.

Dit artikel laat zien hoe u Azure AD DS kunt inschakelen met behulp van PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om dit artikel te voltooien:

* Installeer en configureer Azure PowerShell.
    * Indien nodig, volgt u de instructies voor [installeren van de Azure PowerShell-module en verbinding maken met uw Azure-abonnement](/powershell/azure/install-az-ps).
    * Zorg ervoor dat u zich bij uw Azure-abonnement aanmeldt met behulp van de [Connect-AzAccount][Connect-AzAccount]-cmdlet.
* Installeer en configureer Azure AD PowerShell.
    * Indien nodig, volgt u de instructies voor het [installeren van de Azure AD PowerShell-module en verbinding maken met Azure AD](/powershell/azure/active-directory/install-adv2).
    * Zorg ervoor dat u zich bij de Azure-tenant aanmeldt met behulp van de [Connect-AzureAD][Connect-AzureAD]-cmdlet.
* U hebt *bevoegdheden van een globale beheerder* voor de Azure AD-tenant nodig om Azure AD DS in te schakelen.
* U hebt bevoegdheden van een *Inzender* voor uw Azure-abonnement nodig om de vereiste Azure AD DS-resources te maken.

  > [!IMPORTANT]
  > Hoewel de Power shell-module **AZ. ADDomainServices** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet.

  ```azurepowershell-interactive
  Install-Module -Name Az.ADDomainServices
  ```

## <a name="create-required-azure-ad-resources"></a>Vereiste Azure AD-resources maken

Voor Azure AD DS is een Service-Principal vereist om te verifiëren en te communiceren en een Azure AD-groep om te definiëren welke gebruikers beheerders machtigingen hebben in het beheerde domein.

Maak eerst een Azure AD-Service-Principal met behulp van een specifieke toepassings-ID met de naam *Domain Controller Services*. In open bare Azure is de ID-waarde *2565bd9d-DA50-47d4-8b85-4c97f669dc36*. In andere Clouds is de waarde *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Wijzig deze toepassings-id niet.

Maak een service-principal in Azure AD met behulp van de [New-AzureADServicePrincipal][New-AzureADServicePrincipal]-cmdlet:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Maak nu een Azure AD-groep met de naam *AAD DC-beheerders*. Aan gebruikers die zijn toegevoegd aan deze groep, worden vervolgens machtigingen verleend voor het uitvoeren van beheertaken in het beheerde domein.

Haal eerst de groepsobject-id *AAD DC-beheerders* op met de cmdlet [Get-AzureADGroup][Get-AzureADGroup]. Als de groep niet bestaat, maakt u de groep *AAD DC-beheerders* met behulp van de cmdlet [New-AzureADGroup][New-AzureADGroup]:

```powershell
# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# If the group doesn't exist, create it
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}
```

Nu de groep *AAD DC-beheerders* is gemaakt, haalt u de gewenste gebruikersobject-id op met de cmdlet [Get-AzureADUser][Get-AzureADUser] en vervolgens voegt u de gebruiker toe aan de groep met de cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember].

In het volgende voorbeeld ziet u de gebruikersobject-id voor het account met een UPN van `admin@contoso.onmicrosoft.com`. Vervang dit gebruikersaccount door de UPN van de gebruiker die u wilt toevoegen aan de groep *AAD DC Administrators*:

```powershell
# Retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

Registreer eerst de Azure AD Domain Services-resourceprovider met behulp van de [Register-AzResourceProvider][Register-AzResourceProvider]-cmdlet:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Maak vervolgens een resourcegroep met behulp van de [New-AzResourceGroup][New-AzResourceGroup]-cmdlet. De resourcegroep in het volgende voorbeeld heet *myResourceGroup* en is gemaakt in de regio *westus*. Gebruik uw eigen naam en gewenste regio:

```azurepowershell-interactive
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Maak het virtuele netwerk en de subnetten voor Azure AD Domain Services. Er zijn twee subnetten gemaakt: één voor *DomainServices* en één voor *Workloads*. Azure AD DS wordt geïmplementeerd in het toegewezen subnet *DomainServices*. Implementeer geen andere toepassingen of workloads in dit subnet. Gebruik de afzonderlijke *workloads* of andere subnetten voor de rest van de VM's.

Maak de subnetten met behulp van de [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig]-cmdlet. Maak vervolgens het virtuele netwerk met behulp van de [New-AzVirtualNetwork][New-AzVirtualNetwork]-cmdlet.

```azurepowershell-interactive
$VnetName = "myVnet"

# Create the dedicated subnet for Azure AD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -AddressPrefix 10.0.0.0/24

# Create an additional subnet for your own VM workloads
$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Azure AD DS heeft een netwerkbeveiligingsgroep nodig om de poorten die nodig zijn voor het beheerde domein te beveiligen en al het andere inkomende verkeer te blokkeren. Een [netwerkbeveiligingsgroep][nsg-overview] (NSG: Network Security Group) bevat een lijst met beveiligingsregels waarmee netwerkverkeer naar verkeer in een virtueel Azure-netwerk wordt toegestaan of geweigerd. In Azure AD DS fungeert de netwerkbeveiligingsgroep als een extra beveiligingslaag om de toegang tot het beheerde domein af te grendelen. Zie [Netwerkbeveiligingsgroepen en vereiste poorten][network-ports]om de vereiste poorten weer te geven.

De volgende PowerShell-cmdlets gebruiken [New-AzNetworkSecurityRuleConfig][New-AzNetworkSecurityRuleConfig] om de regels te maken en vervolgens [New-AzNetworkSecurityGroup][New-AzNetworkSecurityGroup] om de netwerkbeveiligingsgroep te maken. De netwerkbeveiligingsgroep en regels worden vervolgens gekoppeld aan het subnet van het virtuele netwerk met behulp van de [set-AzVirtualNetworkSubnetConfig][Set-AzVirtualNetworkSubnetConfig]-cmdlet.

```azurepowershell-interactive
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

## <a name="create-a-managed-domain"></a>Een beheerd domein maken

Nu gaan we een beheerd domein maken. Stel uw Azure-abonnements-id in en geef vervolgens een naam op voor het beheerde domein, bijvoorbeeld *aaddscontoso.com*. U kunt uw abonnements-id ophalen met behulp van de [Get-AzSubscription][Get-AzSubscription]-cmdlet.

Als u een regio kiest die beschikbaarheidszones ondersteunt, worden de Azure AD DS-resources gedistribueerd over zones voor extra redundantie.

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's.

U hoeft niets te configureren voor Azure AD DS om te worden gedistribueerd over zones. De distributie van resources over zones wordt automatisch afgehandeld op het Azure-platform. Zie [Wat zijn beschikbaarheidszones in Azure?][availability-zones] voor meer informatie en om de beschikbaarheid van regio’s te zien.

```azurepowershell-interactive
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
$replicaSetParams = @{
  Location = $AzureLocation
  SubnetId = "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"
}
$replicaSet = New-AzADDomainServiceReplicaSetObject @replicaSetParams

$domainServiceParams = @{
  Name = $ManagedDomainName
  ResourceGroupName = $ResourceGroupName
  DomainName = $ManagedDomainName
  ReplicaSet = $replicaSet
}
New-AzADDomainService @domainServiceParams
```

Het duurt enkele minuten om de resource te maken en het beheer terug te geven aan de PowerShell-prompt. Het inrichten van het beheerde domein wordt op de achtergrond voortgezet. Het kan een uur duren voordat de implementatie is voltooid. Op de **Overzichtspagina** voor uw beheerde domein in de Azure-portal wordt de huidige status weergegeven tijdens deze implementatiefase.

Wanneer in de Azure-portal wordt weergegeven dat inrichten van het beheerde domein gereed is, moeten de volgende taken worden voltooid:

* Werk de DNS-instellingen voor het virtuele netwerk bij, zodat virtuele machines het beheerde domein kunnen vinden voor domeindeelname of verificatie.
    * Selecteer het beheerde domein in de portal om DNS te configureren. In het **Overzichtsvenster** wordt u gevraagd om deze DNS-instellingen automatisch te configureren.
* U moet [Wachtwoordsynchronisatie in Azure AD DS inschakelen](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eindgebruikers zich bij het beheerde domein kunnen aanmelden met hun bedrijfsreferenties.

## <a name="complete-powershell-script"></a>PowerShell-script voltooien

In het volgende voltooide PowerShell-script worden alle taken gecombineerd die in dit artikel worden weergegeven. Kopieer het script en sla het op in een bestand met de extensie `.ps1`. Voer het script uit in een lokale PowerShell-console of in [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Als u Azure AD DS wilt inschakelen, moet u een globale beheerder zijn voor de Azure AD-tenant. U hebt ook minstens de bevoegdheden van een *Inzender* nodig voor het Azure-abonnement.

```azurepowershell-interactive
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Create the delegated administration group for Azure AD Domain Services if it doesn't already exist.
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork

# Enable Azure AD Domain Services for the directory.
$replicaSetParams = @{
  Location = $AzureLocation
  SubnetId = "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"
}
$replicaSet = New-AzADDomainServiceReplicaSetObject @replicaSetParams

$domainServiceParams = @{
  Name = $ManagedDomainName
  ResourceGroupName = $ResourceGroupName
  DomainName = $ManagedDomainName
  ReplicaSet = $replicaSet
}
New-AzADDomainService @domainServiceParams
```

Het duurt enkele minuten om de resource te maken en het beheer terug te geven aan de PowerShell-prompt. Het inrichten van het beheerde domein wordt op de achtergrond voortgezet. Het kan een uur duren voordat de implementatie is voltooid. Op de **Overzichtspagina** voor uw beheerde domein in de Azure-portal wordt de huidige status weergegeven tijdens deze implementatiefase.

Wanneer in de Azure-portal wordt weergegeven dat inrichten van het beheerde domein gereed is, moeten de volgende taken worden voltooid:

* Werk de DNS-instellingen voor het virtuele netwerk bij, zodat virtuele machines het beheerde domein kunnen vinden voor domeindeelname of verificatie.
    * Selecteer het beheerde domein in de portal om DNS te configureren. In het **Overzichtsvenster** wordt u gevraagd om deze DNS-instellingen automatisch te configureren.
* U moet [Wachtwoordsynchronisatie in Azure AD DS inschakelen](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eindgebruikers zich bij het beheerde domein kunnen aanmelden met hun bedrijfsreferenties.

## <a name="next-steps"></a>Volgende stappen

Als u het beheerde domein in actie wilt zien, kunt u [een Windows-VM koppelen][windows-join], [beveiligde LDAP configureren][tutorial-ldaps] en [wachtwoordhashsynchronisatie configureren][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[nsg-overview]: ../virtual-network/network-security-groups-overview.md
[network-ports]: network-considerations.md#network-security-groups-and-required-ports

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md
[New-AzNetworkSecurityRuleConfig]: /powershell/module/az.network/new-aznetworksecurityruleconfig
[New-AzNetworkSecurityGroup]: /powershell/module/az.network/new-aznetworksecuritygroup
[Set-AzVirtualNetworkSubnetConfig]: /powershell/module/az.network/set-azvirtualnetworksubnetconfig
