---
title: Aangepaste Azure-rollen maken of bijwerken met behulp van Azure PowerShell-Azure RBAC
description: Meer informatie over het weer geven, maken, bijwerken of verwijderen van aangepaste rollen met behulp van Azure PowerShell en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 799475db567c88f067192d027589e9185ee1782b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369171"
---
# <a name="create-or-update-azure-custom-roles-using-azure-powershell"></a>Aangepaste Azure-rollen maken of bijwerken met behulp van Azure PowerShell

> [!IMPORTANT]
> Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als de [ingebouwde rollen van Azure](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken. In dit artikel wordt beschreven hoe u aangepaste rollen oplijstt, maakt, bijwerkt of verwijdert met behulp van Azure PowerShell.

Zie [zelf studie: een aangepaste Azure-rol maken met Azure PowerShell](tutorial-custom-role-powershell.md)voor een stapsgewijze zelf studie over het maken van een aangepaste rol.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u aangepaste rollen wilt maken, hebt u het volgende nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [Eigenaar](built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) of [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

Gebruik de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) om de rollen weer te geven die beschikbaar zijn voor toewijzing in een bereik. In het volgende voor beeld wordt een lijst weer gegeven met alle functies die beschikbaar zijn voor toewijzing in het geselecteerde abonnement.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

In het volgende voor beeld worden alleen de aangepaste rollen vermeld die beschikbaar zijn voor toewijzing in het geselecteerde abonnement.

```azurepowershell
Get-AzRoleDefinition -Custom | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Als het geselecteerde abonnement zich niet in de `AssignableScopes` van de rol bevindt, wordt de aangepaste rol niet weer gegeven.

## <a name="list-a-custom-role-definition"></a>Een aangepaste roldefinitie weer geven

Als u een aangepaste roldefinitie wilt weer geven, gebruikt u [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Dit is dezelfde opdracht die u gebruikt voor een ingebouwde rol.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

In het volgende voor beeld worden alleen de acties van de rol vermeld:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

Als u een aangepaste rol wilt maken, gebruikt u de opdracht [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) . Er zijn twee methoden voor het structureren van de rol, met behulp van `PSRoleDefinition` een object of een JSON-sjabloon. 

### <a name="get-operations-for-a-resource-provider"></a>Get-bewerkingen voor een resource provider

Wanneer u aangepaste rollen maakt, is het belang rijk dat u alle mogelijke bewerkingen van de resource providers kent.
U kunt de lijst met bewerkingen van de [resource provider](resource-provider-operations.md) weer geven of u kunt de opdracht [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) gebruiken om deze informatie op te halen.
Als u bijvoorbeeld alle beschik bare bewerkingen voor virtuele machines wilt controleren, gebruikt u deze opdracht:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Een aangepaste rol maken met het PSRoleDefinition-object

Wanneer u Power shell gebruikt om een aangepaste rol te maken, kunt u een van de [ingebouwde rollen](built-in-roles.md) gebruiken als uitgangs punt of u kunt beginnen met het begin. Het eerste voor beeld in deze sectie begint met een ingebouwde rol en past deze vervolgens aan met meer machtigingen. Bewerk de kenmerken om de gewenste, of toe te voegen `Actions` `NotActions` `AssignableScopes` en sla de wijzigingen vervolgens op als een nieuwe rol.

Het volgende voor beeld begint met de ingebouwde rol Inzender voor de [virtuele machine](built-in-roles.md#virtual-machine-contributor) voor het maken van een aangepaste rol met de naam *virtuele-machine operator*. De nieuwe rol verleent toegang tot alle Lees bewerkingen van *micro soft. Compute*-, *micro soft. Storage*-en *micro soft. Network* resource providers en verleent toegang om virtuele machines te starten, opnieuw op te starten en te bewaken. De aangepaste rol kan in twee abonnementen worden gebruikt.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

In het volgende voor beeld ziet u een andere manier om de aangepaste rol van de *operator virtuele machine* te maken. U begint met het maken van een nieuw `PSRoleDefinition` object. De actie bewerkingen worden opgegeven in de `perms` variabele en ingesteld op de `Actions` eigenschap. De `NotActions` eigenschap wordt ingesteld door de `NotActions` van de ingebouwde rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) te lezen. Omdat de Inzender van de [virtuele machine](built-in-roles.md#virtual-machine-contributor) er geen heeft `NotActions` , is deze regel niet vereist, maar wordt weer gegeven hoe informatie kan worden opgehaald uit een andere rol.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Een aangepaste rol maken met JSON-sjabloon

Een JSON-sjabloon kan worden gebruikt als de bron definitie voor de aangepaste rol. In het volgende voor beeld wordt een aangepaste rol gemaakt waarmee Lees toegang tot opslag-en reken resources, toegang tot ondersteuning en deze rol kan worden toegevoegd aan twee abonnementen. Maak een nieuw bestand `C:\CustomRoles\customrole1.json` met het volgende voor beeld. De id moet worden ingesteld op `null` bij het maken van de eerste rol, omdat er automatisch een nieuwe id wordt gegenereerd. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Voer de volgende Power shell-opdracht uit om de rol toe te voegen aan de abonnementen:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

Net als bij het maken van een aangepaste rol, kunt u een bestaande aangepaste rol wijzigen met behulp van het `PSRoleDefinition` object of een JSON-sjabloon.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Een aangepaste rol bijwerken met het PSRoleDefinition-object

Als u een aangepaste rol wilt wijzigen, gebruikt u eerst de opdracht [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) om de functie definitie op te halen. Breng vervolgens de gewenste wijzigingen aan in de functie definitie. Gebruik tot slot de opdracht [set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) om de gewijzigde roldefinitie op te slaan.

In het volgende voor beeld wordt de `Microsoft.Insights/diagnosticSettings/*` bewerking toegevoegd aan de aangepaste rol van de operator van de *virtuele machine* .

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

In het volgende voor beeld wordt een Azure-abonnement toegevoegd aan het toewijs bare bereik van de aangepaste rol van de operator van de *virtuele machine* .

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

In het volgende voor beeld wordt een beheer groep toegevoegd aan `AssignableScopes` de aangepaste rol van de operator van de *virtuele machine* . Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Een aangepaste rol bijwerken met een JSON-sjabloon

Met de vorige JSON-sjabloon kunt u eenvoudig een bestaande aangepaste rol wijzigen om acties toe te voegen of te verwijderen. Werk de JSON-sjabloon bij en voeg de Lees actie voor netwerken toe, zoals wordt weer gegeven in het volgende voor beeld. De definities in de sjabloon zijn niet cumulatief toegepast op een bestaande definitie, wat inhoudt dat de rol precies zo wordt weer gegeven als u in de sjabloon opgeeft. U moet ook het veld id bijwerken met de ID van de rol. Als u niet zeker weet wat deze waarde is, kunt u de cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) gebruiken om deze informatie op te halen.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Als u de bestaande functie wilt bijwerken, voert u de volgende Power shell-opdracht uit:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Een aangepaste rol verwijderen

Als u een aangepaste rol wilt verwijderen, gebruikt u de opdracht [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) .

In het volgende voor beeld wordt de aangepaste rol van de *operator virtuele machine* verwijderd.

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een aangepaste Azure-rol maken met Azure PowerShell](tutorial-custom-role-powershell.md)
- [Aangepaste Azure-rollen](custom-roles.md)
- [Azure-resource provider bewerkingen](resource-provider-operations.md)
