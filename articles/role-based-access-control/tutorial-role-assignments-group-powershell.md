---
title: 'Zelfstudie: een groep toegang verlenen tot Azure-resources met behulp van Azure PowerShell - Azure RBAC'
description: Meer informatie over hoe u een groep toegang verleent tot Azure-resources met behulp van Azure PowerShell en op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) vindt u in deze zelfstudie.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: e25db6213c99afaa46732831534956b01adba0b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555230"
---
# <a name="tutorial-grant-a-group-access-to-azure-resources-using-azure-powershell"></a>Zelfstudie: een groep toegang verlenen tot Azure-resources met behulp van Azure PowerShell

Met [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](overview.md) kunt u de toegang tot Azure-resources beheren. In deze zelfstudie geeft u een groep toestemming om alles in een abonnement te bekijken en om alles in een resourcegroep te beheren met Azure PowerShell.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een groep op verschillende niveaus toegang geven
> * Toegang opvragen
> * Toegang intrekken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Toestemming om groepen te maken in Azure Active Directory (of beschikken over een bestaande groep)
- [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md)

## <a name="role-assignments"></a>Roltoewijzingen

In Azure RBAC verleent u toegang door een roltoewijzing te maken. Een roltoewijzing bestaat uit drie elementen: beveiligings-principal, roldefinitie en bereik (ook wel scope of niveau genoemd). Dit zijn de twee roltoewijzingen die u gaat uitvoeren in deze zelfstudie:

| Beveiligings-principal | Roldefinitie | Bereik |
| --- | --- | --- |
| Groeperen<br>(RBAC Tutorial Group) | [Lezer](built-in-roles.md#reader) | Abonnement |
| Groeperen<br>(RBAC Tutorial Group)| [Inzender](built-in-roles.md#contributor) | Resourcegroep<br>(rbac-tutorial-resource-group) |

   ![Roltoewijzingen voor een groep](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>Een groep maken

Als u een rol wilt toewijzen, hebt u een gebruiker, groep of service-principal nodig. Als u nog geen groep hebt, kunt u er een maken.

- In Azure Cloud shell maakt u een nieuwe groep met de opdracht [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

Als u geen machtigingen hebt voor het maken van groepen, kunt u in plaats daarvan de [Zelfstudie: een gebruiker toegang tot Azure-resources verlenen met behulp van Azure PowerShell](tutorial-role-assignments-user-powershell.md).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

U gaat een resourcegroep maken om te laten zien hoe u een rol kunt toewijzen op het niveau van een resourcegroep.

1. Vraag een lijst met regiolocaties op met behulp van de opdracht [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Selecteer een locatie bij u in de buurt en wijs deze toe aan een variabele.

   ```azurepowershell
   $location = "westus"
   ```

1. Maak een nieuwe resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Toegang verlenen

U verleent toegang aan een groep door met de opdracht [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) een rol toe te wijzen. U moet hierbij de beveiligings-principal, de roldefinitie en het bereik opgeven.

1. Haal de object-id van de groep op met de opdracht [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. Sla de object-id op in een variabele.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. Vraag de id van uw abonnement op met de opdracht [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Sla het abonnementsbereik op in een variabele.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Wijs de rol [Lezer](built-in-roles.md#reader) toe aan de groep op abonnementsniveau.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. Wijs de rol [Inzender](built-in-roles.md#contributor) toe aan de groep op het niveau van de resourcegroep.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>Toegang opvragen

1. Om de toegang voor het abonnement te controleren, gebruikt u de opdracht [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) om een lijst van de roltoewijzingen weer te geven.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    In de uitvoer ziet u dat de rol Lezer (Reader) op abonnementsniveau is toegewezen aan RBAC Tutorial Group.

1. Om de toegang voor de resourcegroep te controleren, gebruikt u de opdracht [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) om een lijst van de roltoewijzingen weer te geven.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    In de uitvoer ziet u dat zowel de rol Inzender (Contributor) als Lezer (Reader) is toegewezen aan RBAC Tutorial Group. De rol Inzender is toegewezen aan de resourcegroep rbac-tutorial-resource-group en de rol Lezer wordt overgenomen op abonnementsniveau.

## <a name="optional-list-access-using-the-azure-portal"></a>(Optioneel) Toegang opvragen via Azure Portal

1. Als u de roltoewijzingen wilt bekijken in Azure Portal, gaat u naar de blade **Toegangsbeheer (IAM)** voor het abonnement.

    ![Roltoewijzingen voor een groep op abonnementsniveau](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. Open de blade **Toegangsbeheer (IAM)** voor de resourcegroep.

    ![Roltoewijzingen voor een groep op het niveau van een resourcegroep](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>Toegang intrekken

U kunt de toegang voor gebruikers, groepen en toepassingen intrekken door met de opdracht [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) een roltoewijzing te verwijderen.

1. Gebruik de volgende opdracht om op het niveau van de resourcegroep de toewijzing van de rol Inzender te verwijderen voor de groep.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Gebruik de volgende opdracht om op abonnementsniveau de toewijzing van de rol Lezer te verwijderen voor de groep.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de in deze zelfstudie gemaakte resources wilt opschonen, verwijdert u de resourcegroep en de groep.

1. Verwijder de resourcegroep met de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Wanneer u wordt gevraagd om de bewerking te bevestigen, typt u **Y**. Het duurt een paar seconden om de groep te verwijderen.

1. Verwijder de groep met de opdracht [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup).

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    Als u een foutbericht ziet wanneer u de groep probeert te verwijderen, kunt u de groep ook in Azure Portal verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure-rollen toewijzen met behulp van Azure PowerShell](role-assignments-powershell.md)