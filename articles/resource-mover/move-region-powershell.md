---
title: Resources verplaatsen tussen regio's met behulp van Power shell in azure resource verhuizer
description: Meer informatie over het verplaatsen van resources over regio's met behulp van Power shell in azure resource verhuizer.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 0aca0e49d72025686cf44d434fa7a43ae0c86e0b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461120"
---
# <a name="move-resources-across-regions-in-powershell"></a>Resources verplaatsen tussen regio's in Power shell

Meer informatie over hoe u Azure-resources naar een andere regio kunt verplaatsen met behulp van Power shell in azure resource verhuizer. 

> [!NOTE]
> Azure Resource Mover is momenteel in preview.



## <a name="before-you-start"></a>Voordat u begint

- Uw Azure-abonnement moet toegang hebben tot het verplaatsen van resources en u moet [eigenaar](../role-based-access-control/built-in-roles.md#owner) -of [gebruikers toegang hebben tot beheerders](../role-based-access-control/built-in-roles.md#user-access-administrator) machtigingen voor het abonnement.
- Resource-overschakeling houdt geen wijzigingen en upgrades bij, dus breng de vereiste wijzigingen aan in resources voordat u ze gaat verplaatsen.
- Wanneer u resources verplaatst met Power shell, kunt u de instellingen voor de doel regio op dit moment niet bewerken. Wijzig deze instellingen rechtstreeks in de portal.
- Wanneer u resources toevoegt aan een verzameling die u wilt verplaatsen, worden de meta gegevens over de verplaatsing opgeslagen in een resource groep die voor het doel is gemaakt, ter voor bereiding om ze te verplaatsen naar een andere regio. Deze resource groep bevindt zich momenteel in de regio's VS-Oost 2 of Europa-noord. Azure-resources kunnen worden verplaatst tussen open bare regio's die gebruikmaken van meta gegevens die in een van deze regio's aanwezig zijn.

## <a name="sample-values"></a>Voorbeeldwaarden

We gebruiken deze waarden in onze script voorbeelden:

**Instelling** | **Waarde** 
--- | ---
Abonnements-id | abonnement-id
Locatie van resource-verhuizer service | VS - oost 2
Resource groep voor meta gegevens | RegionMoveRG-centraalus-westcentralus
Locatie van de resource groep voor meta gegevens | VS - oost 2
Verzamelings naam verplaatsen | MoveCollection-centraalus-westcentralus
Bron regio |  centralus
Bron resource groep | PSDemoRM
Doel regio | US - west-centraal
Doelresourcegroep | PSDemoRMtgt
Te verplaatsen VM | PSDemoVM

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>De resource groep voor meta gegevens maken

Maak een resource groep om de meta gegevens van de verzameling en configuratie-informatie te verplaatsen.

```azurepowershell-interactive
# Create the resource group for metadata
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**Verwachte uitvoer**:

![Tekst uitvoeren na maken van resource groep](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

Registreer de resource provider micro soft. migrate, zodat de MoveCollection-resource kan worden gemaakt.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>De verzameling voor verplaatsen maken

Het MoveCollection-object slaat meta gegevens en configuratie gegevens op over resources die u wilt verplaatsen.

- Voor het MoveCollection-object om toegang te krijgen tot het abonnement waarin de Azure resource Services-service zich bevindt, heeft het een door het [systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (voorheen de Managed Service Identity (MSI) genoemd) die wordt vertrouwd door het abonnement.
- De identiteit wordt toegewezen aan de rol Inzender of de beheerder van de gebruiker toegang voor het bron abonnement.
- Als u een rol aan de identiteit wilt toewijzen, hebt u een rol nodig in het abonnement (zoals eigenaar of gebruikers toegangs beheerder) met de volgende machtigingen:
    -  Microsoft.Authorization/roleAssignments/write
    - Micro soft. Authorization/roleAssignments/verwijderen


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus" -ResourceGroupName "RegionMoveRG-centralus-westcentralus" -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**Verwachte uitvoer**:

![Uitvoer tekst na maken van een verzameling voor verplaatsen](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>Een beheerde identiteit toewijzen 

Stel de abonnements-ID in, haal de id-principal op van het object MoveCollection en wijs hieraan de Azure-rollen toe.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus" -Name "MoveCollection-centralus-westcentralus"

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
```

## <a name="add-resources-to-the-move-collection"></a>Resources toevoegen aan de verzameling verplaatsen

Haal de ID op van de bron bron die u wilt verplaatsen. Voeg deze vervolgens toe aan de verzameling voor verplaatsen.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**Verwachte uitvoer**

![Uitvoer tekst na het ophalen van de resource-ID](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**Verwachte uitvoer** 
 ![ Uitvoer tekst na toevoegen van de resource](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>Afhankelijkheden oplossen

Valideer de resources die u hebt toegevoegd en los eventuele afhankelijkheden op andere resources op.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**Uitvoer als afhankelijkheden bestaan**

Als de resource in de verzameling voor het verplaatsen afhankelijkheden heeft met andere resources, wordt een fout bericht gegeven.

![Uitvoer tekst na het controleren van afhankelijkheden](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>Afhankelijkheden ophalen

Als de resource die u wilt verplaatsen afhankelijk is van andere resources, kunt u informatie ophalen over de ontbrekende afhankelijkheden.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Verwachte uitvoer**

![Uitvoer tekst na ophalen van afhankelijkheden](./media/move-region-powershell/missing-dependencies.png)

In dit voor beeld worden twee afhankelijkheden geïdentificeerd als ontbrekend:

- Bron resource groep: PSDemoRM
- NIC op virtuele machine: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>Afhankelijkheden toevoegen aan verzameling


Met de resource-Id's die u hebt opgehaald, identificeert u de namen van de ontbrekende resources en voegt u deze toe aan de verzameling voor verplaatsen.

### <a name="add-the-nic"></a>De NIC toevoegen

Haal de naam en het type van de NIC op en voeg deze toe aan de verzameling.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”
```

**Verwachte uitvoer**

![Uitvoer tekst na ophalen van NIC](./media/move-region-powershell/output-retrieve-nic.png)

Voeg nu de NIC toe aan de verzameling voor verplaatsen. Dit voor beeld geeft dezelfde naam aan de doel-NIC. Behoud de instellingen en de behuizing consistent.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**Verwachte uitvoer**

![Uitvoer tekst na toevoegen van de NIC aan de verzameling](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>De bron resource groep toevoegen

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**Verwachte uitvoer**

![Uitvoer tekst na toevoegen van de resource groep aan de verzameling](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>Afhankelijkheden opnieuw controleren

Controleer opnieuw op ontbrekende afhankelijkheden.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Uitvoer**

Er zijn aanvullende afhankelijkheden gevonden.

![Uitvoer tekst na opnieuw controleren op afhankelijkheden](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>Aanvullende afhankelijkheden ophalen

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Verwachte uitvoer**


![Uitvoer tekst na het ophalen van extra afhankelijkheden](./media/move-region-powershell/additional-missing-dependencies.png)

In dit voor beeld worden drie meer afhankelijkheden geïdentificeerd als ontbrekend:

- Openbaar IP-adres: psdemovm-IP
- Virtueel Azure-netwerk: psdemorm-vnet
- Netwerk beveiligings groep (NSG): psdemovm-NSG

## <a name="add-additional-dependencies"></a>Aanvullende afhankelijkheden toevoegen

1. [Volg de instructies](#add-dependencies-to-collection) om deze resources toe te voegen aan de verzameling verplaatsen.
2. Nadat u resources hebt toegevoegd, moet u deze opnieuw valideren totdat alle afhankelijkheden zijn toegevoegd.


## <a name="prepare-and-move-the-source-resource-group"></a>De bron resource groep voorbereiden en verplaatsen

In de bron regio bereidt u resources voor voordat u ze verplaatst. Het voorbereidings proces is afhankelijk van de resources die u wilt verplaatsen. Voor bereiding op stateless bronnen kan bijvoorbeeld een Azure Resource Manager-sjabloon (ARM) voorbereiden en exporteren. Of voor stateful bronnen kan replicatie worden uitgeschakeld. 

Voordat u de bron bronnen kunt voorbereiden, moet u de bron resource groep voorbereiden en verplaatsen.

### <a name="prepare"></a>Voorbereiden

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -MoveResource “PSDemoRM”
```

**Verwachte uitvoer**

![Uitvoer tekst na het voorbereiden van de bron resource groep](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>Verplaatsen initiëren

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -MoveResource “PSDemoRM”
```

**Verwachte uitvoer**

![Uitvoer tekst na het initiëren van de verplaatsing van de bron resource groep](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**Verwachte uitvoer**

![Uitvoer tekst na het door voeren van de bron resource groep](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>Resources voorbereiden

Nadat de bron resource groep is verplaatst naar de doel regio, haalt u een lijst op met de resources in de verzameling verplaatsen en bereidt u deze voor.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “<subscription-id>“ -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**Verwachte uitvoer**

![Uitvoer tekst na het ophalen van de resources in de verzameling](./media/move-region-powershell/collection-resources.png)

Bereid nu de resources voor.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
U kunt ook resources voorbereiden en verplaatsen met behulp van de resource-ID in plaats van met de naam:

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**Verwachte uitvoer**

![Uitvoer tekst na het voorbereiden van resources in de verzameling](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>Verplaatsing van resources initiëren

Start de verplaatsing nadat u de resources hebt voor bereid.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Verwachte uitvoer**

![Uitvoer tekst na het initiëren van de resource verplaatsing](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>De verplaatsing negeren of door voeren

Na de eerste verplaatsing kunt u beslissen of u de verplaatsing wilt doorvoeren of verwijderen. 

- **Verwijderen**: Mogelijk wilt u een verplaatsing verwijderen als u een test uitvoert en de bronresource niet echt wilt verplaatsen. Als u de verplaatsing negeert, wordt de resource teruggezet in de status *Initiëren verplaatsing in behandeling*. U kunt de verplaatsing vervolgens opnieuw starten als dat nodig is.
- **Doorvoeren**: Met doorvoeren wordt de verplaatsing naar de doelregio voltooid. Na het doorvoeren heeft een bronresource de status *Verwijderen bron in behandeling* en kunt u besluiten of u deze wilt verwijderen.

### <a name="discard"></a>Verwijderen

De verplaatsing negeren:

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Verwachte uitvoer**

![Uitvoer tekst nadat de verplaatsing is verwijderd](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Doorvoeren

De verplaatsing door voeren:

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Verwachte uitvoer**

![Uitvoer tekst na het door voeren van de verplaatsing](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>Bron resources verwijderen

Nadat de verplaatsing is doorgevoerd en u hebt gecontroleerd of de resources werken zoals verwacht in de doel regio, kunt u de bron resources verwijderen in de [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [met behulp van Power shell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)of [Azure cli](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het](remove-move-resources.md) verwijderen van resources uit een verzameling voor verplaatsen.
