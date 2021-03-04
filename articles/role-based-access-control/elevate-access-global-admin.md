---
title: Toegang verhogen om alle Azure-abonnementen en beheergroepen te beheren
description: Hierin wordt beschreven hoe u de toegang tot een globale beheerder uitbreidt om alle abonnementen en beheer groepen te beheren in Azure Active Directory met behulp van de Azure Portal of REST API.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/09/2020
ms.author: rolyon
ms.openlocfilehash: efc3bfef7c182ea005ac17a59793ac8f6484a424
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042008"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Toegang verhogen om alle Azure-abonnementen en beheergroepen te beheren

Als globale beheerder in Azure Active Directory (Azure AD) hebt u mogelijk geen toegang tot alle abonnementen en beheer groepen in uw Directory. In dit artikel worden de manieren beschreven waarop u de toegang tot alle abonnementen en beheer groepen kunt verhogen.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Waarom moet u uw toegang verhogen?

Als u een globale beheerder bent, is het mogelijk dat u de volgende acties wilt uitvoeren:

- Opnieuw toegang krijgen tot een Azure-abonnement of-beheer groep wanneer een gebruiker de toegang kwijtraakt
- Een andere gebruiker of uzelf toegang te verlenen tot een Azure-abonnement of -beheergroep
- Alle Azure-abonnementen of-beheer groepen in een organisatie weer geven
- Een Automation-app (zoals een facturerings-of controle-app) toestaan om toegang te krijgen tot alle Azure-abonnementen of-beheer groepen

## <a name="how-does-elevated-access-work"></a>Hoe werkt de toegang met verhoogde bevoegdheden?

Azure AD en Azure-resources worden onafhankelijk van elkaar beveiligd. Dat wil zeggen dat Azure AD-roltoewijzingen geen toegang verlenen tot Azure-resources en Azure-roltoewijzingen geen toegang verlenen tot Azure AD. Als u echter een [globale beheerder](../active-directory/roles/permissions-reference.md#global-administrator) in azure AD bent, kunt u uzelf toegang tot alle Azure-abonnementen en-beheer groepen in uw directory toewijzen. Gebruik deze mogelijkheid als u geen toegang hebt tot Azure-abonnements resources, zoals virtuele machines of opslag accounts, en u uw globale beheerders bevoegdheden wilt gebruiken om toegang te krijgen tot deze resources.

Wanneer u de toegang uitbreidt, krijgt u de rol [beheerder voor gebruikers toegang](built-in-roles.md#user-access-administrator) toegewezen in Azure in het hoofd bereik ( `/` ).Hiermee kunt u alle resources weer geven en toegang toewijzen in een abonnement of beheer groep in de Directory. U kunt de toewijzingen van beheerders rollen voor gebruikers toegang verwijderen met Azure PowerShell, Azure CLI of de REST API.

U moet deze verhoogde toegang verwijderen nadat u de wijzigingen hebt aangebracht die u moet aanbrengen in het hoofd bereik.

![Toegang verhogen](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Toegang verhogen voor een globale beheerder

Volg deze stappen om de toegang tot een globale beheerder te verhogen met behulp van de Azure Portal.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) of het [beheer centrum van Azure Active Directory](https://aad.portal.azure.com) als globale beheerder.

    Als u Azure AD Privileged Identity Management gebruikt, [activeert u de toewijzing van de rol globale beheerder](../active-directory/privileged-identity-management/pim-how-to-activate-role.md).

1. Open **Azure Active Directory**.

1. Selecteer **Eigenschappen** onder **Beheren**.

   ![Eigenschappen voor Azure Active Directory eigenschappen selecteren-scherm afbeelding](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Stel onder **toegangs beheer voor Azure-resources** de schakel optie in op **Ja**.

   ![Toegangs beheer voor Azure-resources-scherm afbeelding](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Wanneer u de wissel knop instelt op **Ja**, wordt de rol gebruikers toegangs beheerder toegewezen in azure RBAC in het hoofd bereik (/). Hiermee verleent u toestemming om rollen toe te wijzen aan alle Azure-abonnementen en-beheer groepen die zijn gekoppeld aan deze Azure AD-Directory. Deze schakel optie is alleen beschikbaar voor gebruikers aan wie de rol van globale beheerder is toegewezen in azure AD.

   Wanneer u de wissel knop op **Nee** instelt, wordt de rol beheerder voor gebruikers toegang in azure RBAC verwijderd uit uw gebruikers account. U kunt geen rollen meer toewijzen in alle Azure-abonnementen en-beheer groepen die zijn gekoppeld aan deze Azure AD-Directory. U kunt alleen de Azure-abonnementen en-beheer groepen weer geven en beheren waartoe u toegang hebt gekregen.

    > [!NOTE]
    > Als u [privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)gebruikt, kan het deactiveren van de roltoewijzing niet worden gewijzigd in de wissel knop **voor toegang voor Azure-resources** **.** Als u de minimale privileged Access wilt behouden, raden we u aan deze wissel knop in te stellen op **Nee** voordat u de roltoewijzing deactiveert.
    
1. Klik op **Opslaan** om de instelling op te slaan.

   Deze instelling is geen algemene eigenschap en is alleen van toepassing op de momenteel aangemelde gebruiker. U kunt de toegang niet verhogen voor alle leden van de rol globale beheerder.

1. Meld u af en meld u opnieuw aan om uw toegang te vernieuwen.

    U hebt nu toegang tot alle abonnementen en beheer groepen in uw Directory. Wanneer u het deel venster toegangs beheer (IAM) bekijkt, ziet u dat u de rol gebruikers toegang beheerder hebt toegewezen in het hoofd bereik.

   ![Abonnements roltoewijzingen met het hoofd bereik-scherm afbeelding](./media/elevate-access-global-admin/iam-root.png)

1. Breng de wijzigingen aan die u nodig hebt om een verhoogde toegang te maken.

    Zie [Azure-rollen toewijzen met behulp van de Azure Portal](role-assignments-portal.md)voor meer informatie over het toewijzen van rollen. Als u Privileged Identity Management gebruikt, raadpleegt u [Azure-resources detecteren om](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) [Azure-resource rollen](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)te beheren of toe te wijzen.

1. Voer de stappen in de volgende sectie uit om de verhoogde toegang te verwijderen.

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Voer de volgende stappen uit om de roltoewijzing voor gebruikers toegang te verwijderen in het hoofd bereik ( `/` ).

1. Meld u aan als dezelfde gebruiker die is gebruikt om de toegang tot verhoogde bevoegdheden uit te breiden.

1. Klik in de navigatie lijst op **Azure Active Directory** en klik vervolgens op **Eigenschappen**.

1. Stel het **toegangs beheer voor Azure-resources** in op **Nee**. Aangezien dit een instelling per gebruiker is, moet u zijn aangemeld als dezelfde gebruiker als waarmee de toegang wordt verhoogd.

    Als u probeert de toewijzing van de rol beheerder voor gebruikers toegang te verwijderen in het deel venster toegangs beheer (IAM), wordt het volgende bericht weer gegeven. Als u de roltoewijzing wilt verwijderen, moet u de wissel knop weer instellen op **Nee** of gebruik Azure PowerShell, Azure CLI of de rest API.

    ![Roltoewijzingen met het hoofd bereik verwijderen](./media/elevate-access-global-admin/iam-root-remove.png)

1. Meld u af als globale beheerder.

    Als u Privileged Identity Management gebruikt, deactiveert u de toewijzing van de rol globale beheerder.

    > [!NOTE]
    > Als u [privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)gebruikt, kan het deactiveren van de roltoewijzing niet worden gewijzigd in de wissel knop **voor toegang voor Azure-resources** **.** Als u de minimale privileged Access wilt behouden, raden we u aan deze wissel knop in te stellen op **Nee** voordat u de roltoewijzing deactiveert.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Roltoewijzing in hoofd bereik weer geven (/)

`/`Gebruik de opdracht [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) om de roltoewijzing van de gebruikers toegang voor een gebruiker in het hoofd bereik () weer te geven.

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

Ga als volgt te werk om de toewijzing van de rol beheerder voor gebruikers toegang voor uzelf of een andere gebruiker in het hoofd bereik () te verwijderen `/` .

1. Meld u aan als een gebruiker die verhoogde toegang kan verwijderen. Dit kan dezelfde gebruiker zijn die is gebruikt voor het verhogen van de toegang of een andere globale beheerder met verhoogde toegang in het hoofd bereik.

1. Gebruik de opdracht [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) voor het verwijderen van de toewijzing van de rol beheerder van gebruikers toegang.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="elevate-access-for-a-global-administrator"></a>Toegang verhogen voor een globale beheerder

Gebruik de volgende basis stappen om de toegang tot een globale beheerder te verhogen met behulp van de Azure CLI.

1. Gebruik de opdracht [AZ rest](/cli/azure/reference-index#az_rest) om het eind punt aan te roepen `elevateAccess` , waarmee u de rol beheerder voor gebruikers toegang in het hoofd bereik ( `/` ) verleent.

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Breng de wijzigingen aan die u nodig hebt om een verhoogde toegang te maken.

    Zie [Azure-rollen toewijzen met behulp van de Azure cli](role-assignments-cli.md)voor meer informatie over het toewijzen van rollen.

1. Voer de stappen in een latere sectie uit om de verhoogde toegang te verwijderen.

### <a name="list-role-assignment-at-root-scope-"></a>Roltoewijzing in hoofd bereik weer geven (/)

`/`Gebruik de opdracht [AZ Role Assignment List](/cli/azure/role/assignment#az-role-assignment-list) om de roltoewijzing van de gebruikers toegang voor een gebruiker in het hoofd bereik () weer te geven.

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

Ga als volgt te werk om de toewijzing van de rol beheerder voor gebruikers toegang voor uzelf of een andere gebruiker in het hoofd bereik () te verwijderen `/` .

1. Meld u aan als een gebruiker die verhoogde toegang kan verwijderen. Dit kan dezelfde gebruiker zijn die is gebruikt voor het verhogen van de toegang of een andere globale beheerder met verhoogde toegang in het hoofd bereik.

1. Gebruik de opdracht [AZ Role Assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) om de roltoewijzing voor gebruikers toegang te verwijderen.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST-API

### <a name="elevate-access-for-a-global-administrator"></a>Toegang verhogen voor een globale beheerder

Gebruik de volgende basis stappen om de toegang tot een globale beheerder te verhogen met behulp van de REST API.

1. Met behulp van REST, bellen `elevateAccess` , waarmee u de rol van beheerder voor gebruikers toegang krijgt in het hoofd bereik ( `/` ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Breng de wijzigingen aan die u nodig hebt om een verhoogde toegang te maken.

    Zie [Azure-rollen toewijzen met behulp van de rest API](role-assignments-rest.md)voor meer informatie over het toewijzen van rollen.

1. Voer de stappen in een latere sectie uit om de verhoogde toegang te verwijderen.

### <a name="list-role-assignments-at-root-scope-"></a>Roltoewijzingen in het hoofd bereik weer geven (/)

U kunt alle roltoewijzingen voor een gebruiker in het hoofd bereik () weer geven `/` .

- Roep [Get roleAssignmentse](/rest/api/authorization/roleassignments/listforscope) `{objectIdOfUser}` op, waarbij de object-id is van de gebruiker wiens roltoewijzingen u wilt ophalen.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Lijst met het weigeren van toewijzingen in het hoofd bereik (/)

U kunt alle weigerings toewijzingen voor een gebruiker in het hoofd bereik () weer geven `/` .

- Roep GET denyAssignmentse `{objectIdOfUser}` op, waarbij de object-id is van de gebruiker wiens toewijzingen weigeren u wilt ophalen.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Verhoogde toegang verwijderen

Wanneer u aanroept `elevateAccess` , maakt u een roltoewijzing voor uzelf, zodat u de bevoegdheden die u nodig hebt om de rol van beheerder voor gebruikers toegang voor uzelf te verwijderen in het hoofd bereik (), kunt intrekken `/` .

1. Roep [Get roleDefinitionse](/rest/api/authorization/roledefinitions/get) aan, waarbij `roleName` is gelijk aan de beheerder van de gebruikers toegang om de naam-id van de beheerder van de gebruikers toegang te bepalen.

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

    Sla de ID op uit de `name` para meter in dit geval `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` .

1. U moet ook de roltoewijzing voor de Directory-beheerder in het bereik van de Directory weer geven. Geef alle toewijzingen op Directory-bereik weer voor de `principalId` Directory-beheerder die de toegangs oproep ter verhoging heeft gemaakt. Hiermee worden alle toewijzingen in de map voor de objectid weer geven.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Een Directory-beheerder mag niet veel toewijzingen hebben, als de vorige query te veel toewijzingen retourneert, kunt u ook een query uitvoeren voor alle toewijzingen, alleen op het niveau van een mapniveau en vervolgens de resultaten filteren: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. De vorige oproepen retour neren een lijst met roltoewijzingen. Zoek naar de roltoewijzing waar de scope zich bevindt `"/"` en `roleDefinitionId` eindigt met de rolnaam-id die u in stap 1 hebt gevonden en `principalId` overeenkomt met de objectId van de Directory-beheerder. 
    
    Toewijzing van voorbeeld functie:
    
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
    
    Sla de ID opnieuw op uit de `name` para meter, in dit geval 11111111-1111-1111-1111-111111111111.

1. Gebruik ten slotte de roltoewijzings-ID om de toewijzing te verwijderen die is toegevoegd door `elevateAccess` :

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Volgende stappen

- [Inzicht in de verschillende rollen](rbac-and-directory-admin-roles.md)
- [Azure-rollen toewijzen met behulp van de REST API](role-assignments-rest.md)
