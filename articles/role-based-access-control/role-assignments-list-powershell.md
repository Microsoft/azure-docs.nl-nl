---
title: Azure-roltoewijzingen weer geven met Azure PowerShell-Azure RBAC
description: Meer informatie over hoe u kunt bepalen welke resources gebruikers, groepen, service-principals of beheerde identiteiten hebben toegang tot het gebruik van Azure PowerShell en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
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
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 980e9f1ffd453a070dde72f8c7ca34c7ef08f948
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964351"
---
# <a name="list-azure-role-assignments-using-azure-powershell"></a>Toewijzingen van Azure-roltoewijzingen weer geven met Azure PowerShell

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] In dit artikel wordt beschreven hoe u roltoewijzingen met Azure PowerShell kunt weer geven.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Als uw organisatie uitbestede beheer functies heeft voor een service provider die gebruikmaakt van [Azure delegated resource management](../lighthouse/concepts/azure-delegated-resource-management.md), worden roltoewijzingen die door die service provider worden toegestaan, hier niet weer gegeven.

## <a name="prerequisites"></a>Vereisten

- [Power shell in azure Cloud shell](../cloud-shell/overview.md) of [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Roltoewijzingen voor het huidige abonnement weer geven

De eenvoudigste manier om een lijst op te halen met alle roltoewijzingen in het huidige abonnement (inclusief overgenomen roltoewijzingen van basis-en beheer groepen) is door [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) zonder para meters te gebruiken.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Roltoewijzingen weergeven voor een abonnement

Gebruik [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)om alle roltoewijzingen in een abonnements bereik weer te geven. Als u de abonnements-ID wilt ophalen, kunt u deze vinden op de Blade **abonnementen** in de Azure portal of u kunt [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)gebruiken.

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Roltoewijzingen voor een gebruiker weergeven

Gebruik [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)om alle rollen weer te geven die aan een opgegeven gebruiker zijn toegewezen.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Gebruik [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)om alle rollen weer te geven die zijn toegewezen aan een opgegeven gebruiker en de rollen die zijn toegewezen aan de groepen waartoe de gebruiker behoort.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Roltoewijzingen voor een resourcegroep opvragen

Als u alle roltoewijzingen wilt weer geven in een bereik van een resource groep, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Roltoewijzingen voor een beheer groep weer geven

Gebruik [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)om alle roltoewijzingen in een beheer groeps bereik weer te geven. Als u de beheer groep-ID wilt ophalen, kunt u deze vinden op de Blade **beheer groepen** in de Azure portal of u kunt [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)gebruiken.

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-a-resource"></a>Roltoewijzingen voor een resource weer geven

Als u roltoewijzingen voor een specifieke resource wilt weer geven, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) en de `-Scope` para meter. Het bereik is afhankelijk van de resource. Als u het bereik wilt ophalen, kunt u `Get-AzRoleAssignment` zonder para meters uitvoeren om alle roltoewijzingen weer te geven en vervolgens het bereik te vinden dat u wilt weer geven.

```azurepowershell
Get-AzRoleAssignment -Scope "/subscriptions/<subscription_id>/resourcegroups/<resource_group_name>/providers/<provider_name>/<resource_type>/<resource>
```

In dit volgende voor beeld ziet u hoe u de roltoewijzingen voor een opslag account kunt weer geven. Houd er rekening mee dat met deze opdracht ook roltoewijzingen in hogere bereiken worden weer gegeven, zoals resource groepen en abonnementen, die gelden voor dit opslag account.

```Example
PS C:\> Get-AzRoleAssignment -Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"
```

Als u alleen roltoewijzingen wilt weer geven die rechtstreeks aan een resource zijn toegewezen, kunt u de opdracht [where-object](/powershell/module/microsoft.powershell.core/where-object) gebruiken om de lijst te filteren.

```Example
PS C:\> Get-AzRoleAssignment | Where-Object {$_.Scope -eq "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"}
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Roltoewijzingen voor de klassieke service beheerder en mede beheerders weer geven

Gebruik [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)om roltoewijzingen voor de klassieke abonnements beheerder en mede beheerders weer te geven.

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Roltoewijzingen voor een beheerde identiteit weer geven

1. Haal de object-ID op van de door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteit. 

    U kunt [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)gebruiken om de object-id van een door de gebruiker toegewezen beheerde identiteit op te halen.

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. Als u de roltoewijzingen wilt weer geven, gebruikt u [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure PowerShell](role-assignments-powershell.md)