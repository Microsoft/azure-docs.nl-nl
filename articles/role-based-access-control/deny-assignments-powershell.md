---
title: Een lijst met Azure deny-toewijzingen met Azure PowerShell-Azure RBAC
description: Meer informatie over het weer geven van de gebruikers, groepen, service-principals en beheerde identiteiten die toegang hebben gekregen tot specifieke Azure-resource acties voor bepaalde bereiken met Azure PowerShell en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6cf379b051ba42be2d7df7d288f07cdc2a0002a1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657632"
---
# <a name="list-azure-deny-assignments-using-azure-powershell"></a>Azure deny-toewijzingen weer geven met Azure PowerShell

Met [Azure deny-toewijzingen](deny-assignments.md) kunnen gebruikers specifieke Azure-resource acties uitvoeren, zelfs als een roltoewijzing deze toegang verleent. In dit artikel wordt beschreven hoe u het weigeren van toewijzingen in Azure PowerShell kunt weer geven.

> [!NOTE]
> U kunt niet rechtstreeks uw eigen weigerings toewijzingen maken. Zie [Azure deny-toewijzingen](deny-assignments.md)voor meer informatie over hoe weigerings toewijzingen worden gemaakt.

## <a name="prerequisites"></a>Vereisten

Als u informatie wilt ophalen over een weiger toewijzing, hebt u het volgende nodig:

- `Microsoft.Authorization/denyAssignments/read` machtiging, die deel uitmaakt van de meeste [ingebouwde rollen van Azure](built-in-roles.md)
- [Power shell in azure Cloud shell](../cloud-shell/overview.md) of [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Lijst met geweigerde toewijzingen weergeven

### <a name="list-all-deny-assignments"></a>Alle weiger toewijzingen weer geven

Gebruik [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)om alle weigerings toewijzingen voor het huidige abonnement weer te geven.

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Lijst met het weigeren van toewijzingen in een bereik van een resource groep

Gebruik [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)om alle weigerings toewijzingen in een resource groeps bereik weer te geven.

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Lijst met het weigeren van toewijzingen bij een abonnements bereik

Gebruik [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)om alle weigerings toewijzingen in een abonnements bereik weer te geven. Als u de abonnements-ID wilt ophalen, kunt u deze vinden op de Blade **abonnementen** in de Azure portal of u kunt [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)gebruiken.

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure deny-toewijzingen](deny-assignments.md)
- [Azure deny-toewijzingen weer geven met behulp van de Azure Portal](deny-assignments-portal.md)
- [Azure deny-toewijzingen weer geven met behulp van de REST API](deny-assignments-rest.md)