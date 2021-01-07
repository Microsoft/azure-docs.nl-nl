---
title: Azure-roltoewijzingen toevoegen of verwijderen met Azure PowerShell-Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van Azure PowerShell en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2020
ms.author: rolyon
ms.openlocfilehash: 3bb09133ba6991554072b4bf68b5306c78f868a7
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964283"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] In dit artikel wordt beschreven hoe u rollen toewijst met behulp van Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Om roltoewijzingen toe te voegen of te verwijderen, hebt u het volgende nodig:

- Machtigingen voor `Microsoft.Authorization/roleAssignments/write` en `Microsoft.Authorization/roleAssignments/delete`, zoals [Beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [Eigenaar](built-in-roles.md#owner)
- [Power shell in azure Cloud shell](../cloud-shell/overview.md) of [Azure PowerShell](/powershell/azure/install-az-ps)
- Het account dat u gebruikt om de Power shell-opdracht uit te voeren, moet de Microsoft Graph `Directory.Read.All` machtiging hebben.

## <a name="steps-to-add-a-role-assignment"></a>Stappen om een roltoewijzing toe te voegen

In azure RBAC kunt u een roltoewijzing toevoegen om toegang te verlenen. Een roltoewijzing bestaat uit drie elementen: beveiligings-principal, roldefinitie en bereik (ook wel scope of niveau genoemd). Voer de volgende stappen uit om een roltoewijzing toe te voegen.

### <a name="step-1-determine-who-needs-access"></a>Stap 1: bepalen wie toegang moet hebben

U kunt een rol toewijzen aan een gebruiker, groep, Service-Principal of beheerde identiteit. Als u een roltoewijzing wilt toevoegen, moet u mogelijk de unieke ID van het object opgeven. De ID heeft de volgende indeling: `11111111-1111-1111-1111-111111111111` . U kunt de ID ophalen met behulp van de Azure Portal of Azure PowerShell.

**Gebruiker**

Voor een Azure AD-gebruiker haalt u de user principal name op, zoals *patlong \@ contoso.com* of de gebruikers object-id. U kunt [Get-AzADUser](/powershell/module/az.resources/get-azaduser)gebruiken om de object-id op te halen.

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Groep**

Voor een Azure AD-groep hebt u de object-ID van de groep nodig. U kunt [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)gebruiken om de object-id op te halen.

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Service-principal**

Voor een Azure AD-Service-Principal (identiteit die wordt gebruikt door een toepassing) hebt u de Service-Principal object-ID nodig. U kunt [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)gebruiken om de object-id op te halen. Gebruik voor een service-principal de object-ID en **niet** de toepassings-id.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Beheerde identiteit**

Voor een door het systeem toegewezen of een door de gebruiker toegewezen beheerde identiteit hebt u de object-ID nodig. U kunt [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)gebruiken om de object-id op te halen.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-find-the-appropriate-role"></a>Stap 2: de juiste rol zoeken

Machtigingen worden samen in rollen gegroepeerd. U kunt kiezen uit een lijst met verschillende [ingebouwde rollen van Azure](built-in-roles.md) of u kunt uw eigen aangepaste rollen gebruiken. Het is een best practice om toegang te verlenen met de mini maal benodigde bevoegdheden, dus vermijd het toewijzen van een bredere rol.

U kunt [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)gebruiken om de rollen weer te geven en de unieke rol-id op te halen.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

U kunt als volgt de details van een bepaalde rol weer geven.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

Zie [Azure Role-definities weer](role-definitions-list.md#azure-powershell)geven voor meer informatie.
 
### <a name="step-3-identify-the-needed-scope"></a>Stap 3: het benodigde bereik identificeren

Azure biedt vier niveaus van bereik: resource, [resource groep](../azure-resource-manager/management/overview.md#resource-groups), abonnement en [beheer groep](../governance/management-groups/overview.md). Het is een best practice om toegang te verlenen met de mini maal benodigde bevoegdheden, dus vermijd het toewijzen van een rol in een breder bereik. Zie [Bereik](scope-overview.md) voor meer informatie over het bereik.

**Resourcebereik**

Voor resource bereik hebt u de resource-ID voor de resource nodig. U kunt de resource-ID vinden door te kijken naar de eigenschappen van de resource in de Azure Portal. Een resource-ID heeft de volgende indeling.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Bereik van de resourcegroep**

Voor het bereik van de resource groep hebt u de naam van de resource groep nodig. U kunt de naam vinden op de pagina **resource groepen** in de Azure portal of u kunt [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)gebruiken.

```azurepowershell
Get-AzResourceGroup
```

**Abonnements bereik** 

Voor het abonnements bereik hebt u de abonnements-ID nodig. U kunt de ID vinden op de pagina **abonnementen** in het Azure portal of u kunt [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)gebruiken.

```azurepowershell
Get-AzSubscription
```

**Beheer groeps bereik** 

Voor beheer groeps bereik hebt u de naam van de beheer groep nodig. U kunt de naam vinden op de pagina **beheer groepen** in de Azure portal of u kunt [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)gebruiken.

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-add-role-assignment"></a>Stap 4: roltoewijzing toevoegen

Gebruik de opdracht [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) om een roltoewijzing toe te voegen. Afhankelijk van het bereik heeft de opdracht meestal een van de volgende notaties.

**Resourcebereik**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Bereik van de resourcegroep**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Abonnements bereik** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Beheer groeps bereik** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="add-role-assignment-examples"></a>Voor beelden van functie toewijzing toevoegen

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Roltoewijzing voor alle BLOB-containers in een opslag account bron bereik toevoegen

Hiermee wijst u de rol van de BLOB voor het decoderen van gegevens over de [opslag](built-in-roles.md#storage-blob-data-contributor) van een opslag account met de naam *storage12345* toe aan een service-principal met object-id *55555555-5555-5555-5555-555555555555* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Roltoewijzings toevoegen voor een specifiek BLOB-container resource bereik

Hiermee wijst u de rol van [BLOB voor gegevens opslag](built-in-roles.md#storage-blob-data-contributor) toe aan een service-principal met object-id *55555555-5555-5555-5555-555555555555* in een resource bereik voor een BLOB-container met de naam *BLOB-01*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Roltoewijzing toevoegen voor een groep in een specifiek bron bereik voor een virtueel netwerk

Wijst de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toe aan de groep *Pharma verkoop beheerders* met id aaaaaaaa-AAAA-AAAA-AAAA-aaaaaaaaaaaa in een resource bereik voor een virtueel netwerk met de naam *Pharma-Sales-project-Network*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="add-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Een roltoewijzing toevoegen voor een gebruiker in een bereik van een resource groep

Wijst de rol van [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toe aan *patlong \@ contoso.com* gebruiker op het *Pharma-Sales-* resource groeps bereik.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

U kunt ook de volledig gekwalificeerde resource groep opgeven met de `-Scope` para meter:

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Roltoewijzing voor een gebruiker toevoegen met behulp van de unieke rol-ID in een resource groeps bereik

Er zijn een aantal keren dat een rolnaam kan worden gewijzigd, bijvoorbeeld:

- U gebruikt uw eigen aangepaste rol en u besluit de naam te wijzigen.
- U gebruikt een preview-functie met **(preview)** in de naam. Wanneer de rol wordt vrijgegeven, wordt de naam van de rol gewijzigd.

Zelfs als een rol een andere naam heeft gekregen, wordt de rol-ID niet gewijzigd. Als u scripts of automatisering gebruikt om roltoewijzingen te maken, is het een best practice om de unieke rol-ID te gebruiken in plaats van de rolnaam. Als de naam van een rol wordt gewijzigd, zijn uw scripts waarschijnlijker goed.

In het volgende voor beeld wordt de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toegewezen aan de *patlong \@ contoso.com* -gebruiker op het *Pharma-Sales-* resource groeps bereik.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Roltoewijzing toevoegen voor een toepassing in een bereik van een resource groep

Wijst de rol van [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toe aan een toepassing met Service-Principal object-id 77777777-7777-7777-7777-777777777777 op het *Pharma* van de resource groep.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Roltoewijzing voor een gebruiker toevoegen aan een abonnements bereik

Wijst de rol van [lezer](built-in-roles.md#reader) toe aan de *annm \@ example.com* -gebruiker bij een abonnements bereik.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Roltoewijzing voor een gebruiker toevoegen aan een beheer groeps bereik

Wijst de rol [facturerings lezer](built-in-roles.md#billing-reader) toe aan de *Alain \@ example.com* -gebruiker in een bereik van een beheer groep.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

In azure RBAC verwijdert u een roltoewijzing met behulp van [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)om de toegang te verwijderen.

In het volgende voor beeld wordt de toewijzing van de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) uit de *patlong \@ contoso.com* -gebruiker voor de resource groep *Pharma-Sales* verwijderd:

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Hiermee verwijdert u de rol van [lezer](built-in-roles.md#reader) uit de *team groep Anne Mack* met id 22222222-2222-2222-2222-222222222222 op abonnements bereik.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Hiermee verwijdert u de rol van [facturerings lezer](built-in-roles.md#billing-reader) van de *Alain \@ example.com* -gebruiker in het bereik van de beheer groep.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Als u het volgende fout bericht wordt weer gegeven: ' de opgegeven informatie is niet toegewezen aan een roltoewijzing ', zorg ervoor dat u ook `-Scope` de `-ResourceGroupName` para meters of opgeeft. Zie [problemen met Azure RBAC oplossen](troubleshooting.md#role-assignments-with-identity-not-found)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Toewijzingen van Azure-roltoewijzingen weer geven met Azure PowerShell](role-assignments-list-powershell.md)
- [Zelfstudie: een groep toegang verlenen tot Azure-resources met behulp van Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Resources beheren met Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
