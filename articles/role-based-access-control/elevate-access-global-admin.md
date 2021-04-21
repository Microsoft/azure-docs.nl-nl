---
title: Toegang verhogen om alle Azure-abonnementen en beheergroepen te beheren
description: Beschrijft hoe u de toegang voor een globale beheerder kunt verhogen voor het beheren van alle abonnementen en beheergroepen in Azure Active Directory met behulp van de Azure Portal of REST API.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/09/2020
ms.author: rolyon
ms.openlocfilehash: 37d50c030a2b426cb3e9af57afb899b7fab68388
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778466"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Toegang verhogen om alle Azure-abonnementen en beheergroepen te beheren

Als globale beheerder in Azure Active Directory (Azure AD) hebt u mogelijk geen toegang tot alle abonnementen en beheergroepen in uw directory. In dit artikel worden de manieren beschreven waarop u uw toegang tot alle abonnementen en beheergroepen kunt verhogen.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Waarom zou u uw toegangstoegang moeten verhogen?

Als u een globale beheerder bent, kan het zijn dat u de volgende acties wilt uitvoeren:

- Weer toegang krijgen tot een Azure-abonnement of -beheergroep wanneer een gebruiker geen toegang meer heeft
- Een andere gebruiker of uzelf toegang te verlenen tot een Azure-abonnement of -beheergroep
- Alle Azure-abonnementen of -beheergroepen in een organisatie bekijken
- Een automatiserings-app (zoals een facturerings- of controle-app) toegang verlenen tot alle Azure-abonnementen of -beheergroepen

## <a name="how-does-elevated-access-work"></a>Hoe werkt verhoogde toegang?

Azure AD- en Azure-resources worden onafhankelijk van elkaar beveiligd. Dat wil zeggen dat Azure AD-roltoewijzingen geen toegang verlenen tot Azure-resources en Azure-roltoewijzingen geen toegang verlenen tot Azure AD. Als u echter een globale [beheerder](../active-directory/roles/permissions-reference.md#global-administrator) in Azure AD bent, kunt u uzelf toegang geven tot alle Azure-abonnementen en beheergroepen in uw directory. Gebruik deze mogelijkheid als u geen toegang hebt tot resources van het Azure-abonnement, zoals virtuele machines of opslagaccounts, en u de bevoegdheid van de globale beheerder wilt gebruiken om toegang te krijgen tot deze resources.

Wanneer u uw toegangsbereik verhoogt, krijgt u de rol [Gebruikerstoegangbeheerder](built-in-roles.md#user-access-administrator) toegewezen in Azure in het hoofdbereik ( `/` ).Hiermee kunt u alle resources weergeven en toegang toewijzen in elk abonnement of elke beheergroep in de directory. Roltoewijzingen van gebruikerstoegangbeheerder kunnen worden verwijderd met Azure PowerShell, Azure CLI of de REST API.

U moet deze verhoogde toegang verwijderen nadat u de wijzigingen hebt aangebracht die u moet aanbrengen in het hoofdbereik.

![Toegang verhogen](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Toegang verhogen voor een globale beheerder

Volg deze stappen om de toegang voor een globale beheerder uit te voeren met behulp van Azure Portal.

1. Meld u als globale [Azure Portal](https://portal.azure.com) aan [bij het Azure Active Directory](https://aad.portal.azure.com) of het beheercentrum.

    Als u een Azure AD Privileged Identity Management, [activeert u de roltoewijzing van de globale beheerder.](../active-directory/privileged-identity-management/pim-how-to-activate-role.md)

1. Open **Azure Active Directory**.

1. Selecteer **Eigenschappen** onder **Beheren**.

   ![Selecteer Eigenschappen voor Azure Active Directory eigenschappen - schermopname](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Stel **onder Toegangsbeheer voor Azure-resources** de schakelknop in op **Ja.**

   ![Toegangsbeheer voor Azure-resources - schermopname](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Wanneer u de schakelknop in stelt op **Ja,** krijgt u de rol Gebruikerstoegangbeheerder toegewezen in Azure RBAC in het hoofdbereik (/). Hiermee krijgt u toestemming om rollen toe te wijzen in alle Azure-abonnementen en beheergroepen die zijn gekoppeld aan deze Azure AD-directory. Deze schakelknop is alleen beschikbaar voor gebruikers aan wie de rol Globale beheerder in Azure AD is toegewezen.

   Wanneer u de schakelknop in stelt **op Nee,** wordt de rol Gebruikerstoegangbeheerder in Azure RBAC verwijderd uit uw gebruikersaccount. U kunt geen rollen meer toewijzen in alle Azure-abonnementen en -beheergroepen die zijn gekoppeld aan deze Azure AD-directory. U kunt alleen de Azure-abonnementen en -beheergroepen bekijken en beheren waarvoor u toegang hebt gekregen.

    > [!NOTE]
    > Als u een [Privileged Identity Management,](../active-directory/privileged-identity-management/pim-configure.md)wordt de wisselknop Toegangsbeheer voor **Azure-resources** niet gewijzigd in Nee als u de roltoewijzing deactiveert.  Als u de minst bevoegde toegang wilt behouden, raden we u aan deze schakelknop in te stellen op **Nee** voordat u de roltoewijzing deactiveert.
    
1. Klik **op Opslaan** om uw instelling op te slaan.

   Deze instelling is geen globale eigenschap en is alleen van toepassing op de momenteel aangemelde gebruiker. U kunt de toegang niet verhogen voor alle leden van de rol Globale beheerder.

1. Meld u af en weer aan om uw toegang te vernieuwen.

    U hebt nu toegang tot alle abonnementen en beheergroepen in uw directory. Wanneer u het deelvenster Toegangsbeheer (IAM) bekijkt, ziet u dat aan u de rol Gebruikerstoegangsbeheerder is toegewezen in het hoofdbereik.

   ![Abonnementsroltoewijzingen met hoofdbereik - schermopname](./media/elevate-access-global-admin/iam-root.png)

1. Maak de wijzigingen die u moet aanbrengen bij verhoogde toegang.

    Zie Azure-rollen toewijzen met behulp van de Azure Portal voor [meer informatie over het toewijzen Azure Portal.](role-assignments-portal.md) Als u een Privileged Identity Management, zie [Azure-resources ontdekken om Azure-resourcerollen](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) te beheren [of toe te wijzen.](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)

1. Voer de stappen in de volgende sectie uit om uw verhoogde toegang te verwijderen.

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Volg deze stappen om de roltoewijzing Gebruikerstoegangbeheerder te verwijderen in het hoofdbereik ( `/` ).

1. Meld u aan als dezelfde gebruiker die is gebruikt om de toegang te verhogen.

1. Klik in de navigatielijst op **Azure Active Directory** klik vervolgens op **Eigenschappen.**

1. Stel de **schakelknop Toegangsbeheer voor Azure-resources** weer in op **Nee.** Aangezien dit een instelling per gebruiker is, moet u zijn aangemeld als dezelfde gebruiker die is gebruikt om de toegang te verhogen.

    Als u de roltoewijzing Administrator voor gebruikerstoegang in het deelvenster Toegangsbeheer (IAM) probeert te verwijderen, ziet u het volgende bericht. Als u de roltoewijzing wilt verwijderen, moet u de schakelknop weer instellen op Nee of Azure PowerShell, Azure CLI of de REST API. 

    ![Roltoewijzingen met hoofdbereik verwijderen](./media/elevate-access-global-admin/iam-root-remove.png)

1. Meld u af als globale beheerder.

    Als u een Privileged Identity Management, deactiveert u de roltoewijzing globale beheerder.

    > [!NOTE]
    > Als u een [Privileged Identity Management,](../active-directory/privileged-identity-management/pim-configure.md)wordt de wisselknop Toegangsbeheer voor **Azure-resources** niet gewijzigd in Nee als u de roltoewijzing **deactiveert.** Als u de minst bevoegde toegang wilt behouden, raden we u aan deze schakelknop in te stellen op **Nee** voordat u de roltoewijzing deactiveert.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Lijst met roltoewijzingen in hoofdbereik (/)

Gebruik de opdracht `/` [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) om de roltoewijzing Gebruikerstoegangbeheerder voor een gebruiker in het hoofdbereik ( ) weer te geven.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Volg deze stappen om de roltoewijzing Gebruikerstoegangbeheerder voor uzelf of een andere gebruiker in het hoofdbereik ( `/` ) te verwijderen.

1. Meld u aan als een gebruiker die verhoogde toegang kan verwijderen. Dit kan dezelfde gebruiker zijn die is gebruikt voor het verhogen van toegang of een andere globale beheerder met verhoogde toegang in het hoofdbereik.

1. Gebruik de [opdracht Remove-AzRoleAssignment om](/powershell/module/az.resources/remove-azroleassignment) de roltoewijzing Administrator voor gebruikerstoegang te verwijderen.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="elevate-access-for-a-global-administrator"></a>Toegang verhogen voor een globale beheerder

Gebruik de volgende basisstappen om de toegang voor een globale beheerder uit te voeren met behulp van de Azure CLI.

1. Gebruik de [opdracht az rest](/cli/azure/reference-index#az_rest) om het eindpunt aan te roepen, waarmee u de rol Administrator voor gebruikerstoegang krijgt in het `elevateAccess` hoofdbereik ( `/` ).

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Maak de wijzigingen die u moet aanbrengen bij verhoogde toegang.

    Zie Azure-rollen toewijzen met behulp van de Azure CLI voor meer informatie over het toewijzen [van rollen.](role-assignments-cli.md)

1. Voer de stappen in een latere sectie uit om uw verhoogde toegang te verwijderen.

### <a name="list-role-assignment-at-root-scope-"></a>Roltoewijzing in hoofdbereik (/)

Gebruik de opdracht az role assignment list om de roltoewijzing Gebruikerstoegangbeheerder voor een gebruiker in het hoofdbereik ( ) `/` [weer te](/cli/azure/role/assignment#az_role_assignment_list) geven.

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Volg deze stappen om de roltoewijzing Gebruikerstoegangbeheerder voor uzelf of een andere gebruiker in het hoofdbereik ( `/` ) te verwijderen.

1. Meld u aan als een gebruiker die verhoogde toegang kan verwijderen. Dit kan dezelfde gebruiker zijn die is gebruikt voor het verhogen van toegang of een andere globale beheerder met verhoogde toegang in het hoofdbereik.

1. Gebruik de [opdracht az role assignment delete om](/cli/azure/role/assignment#az_role_assignment_delete) de roltoewijzing Beheerder gebruikerstoegang te verwijderen.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST-API

### <a name="elevate-access-for-a-global-administrator"></a>Toegang verhogen voor een globale beheerder

Gebruik de volgende basisstappen om de toegang voor een globale beheerder uit te voeren met behulp van REST API.

1. Roep met REST `elevateAccess` aan, waarmee u de rol Administrator voor gebruikerstoegang krijgt in het hoofdbereik ( `/` ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Maak de wijzigingen die u moet aanbrengen bij verhoogde toegang.

    Zie Azure-rollen toewijzen met behulp van de REST API voor meer informatie over [het toewijzen REST API.](role-assignments-rest.md)

1. Voer de stappen in een latere sectie uit om uw verhoogde toegang te verwijderen.

### <a name="list-role-assignments-at-root-scope-"></a>Lijst met roltoewijzingen in hoofdbereik (/)

U kunt alle roltoewijzingen voor een gebruiker in het hoofdbereik () opn `/` genoemd.

- Roep [GET roleAssignments aan,](/rest/api/authorization/roleassignments/listforscope) waarbij de object-id is van de gebruiker van wie u de roltoewijzingen `{objectIdOfUser}` wilt ophalen.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Lijst met weigeren toewijzingen op hoofdbereik (/)

U kunt alle weigerende toewijzingen voor een gebruiker in het hoofdbereik () opn `/` genoemd.

- Roep GET denyAssignments aan, waarbij de object-id is van de gebruiker van wie u de toewijzingen voor weigeren `{objectIdOfUser}` wilt ophalen.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Wanneer u aanroept, maakt u een roltoewijzing voor uzelf. Als u deze bevoegdheden wilt intrekken, moet u de roltoewijzing Gebruikerstoegangbeheerder voor uzelf verwijderen in het `elevateAccess` hoofdbereik ( `/` ).

1. Roep [GET roleDefinitions aan](/rest/api/authorization/roledefinitions/get) waarbij gelijk is aan User Access Administrator om de naam-id van de rol Administrator voor `roleName` gebruikerstoegang te bepalen.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Sla de id van de `name` parameter op, in dit geval `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` .

1. U moet ook de roltoewijzing voor de directorybeheerder op mapbereik. Vermeld alle toewijzingen in mapbereik voor de van de `principalId` directorybeheerder die de aanroep voor verhoogde toegang heeft gedaan. Hiermee worden alle toewijzingen in de map voor de object-id weergegeven.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Een directorybeheerder mag niet veel toewijzingen hebben. Als de vorige query te veel toewijzingen retourneert, kunt u ook een query uitvoeren voor alle toewijzingen op mapbereikniveau en vervolgens de resultaten filteren: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. De vorige aanroepen retourneren een lijst met roltoewijzingen. Zoek de roltoewijzing waar het bereik is en de eindigt met de rolnaam-id die u in stap 1 hebt gevonden en die overeenkomt met de `"/"` `roleDefinitionId` `principalId` objectId van de directorybeheerder. 
    
    Voorbeeldroltoewijzing:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Sla de id opnieuw op uit de parameter , in dit geval `name` 111111111-1111-1111-1111-111111111111.

1. Gebruik tot slot de roltoewijzings-id om de toewijzing te verwijderen die is toegevoegd door `elevateAccess` :

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Volgende stappen

- [Inzicht in de verschillende rollen](rbac-and-directory-admin-roles.md)
- [Azure-rollen toewijzen met behulp van REST API](role-assignments-rest.md)
