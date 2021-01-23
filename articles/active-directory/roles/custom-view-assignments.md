---
title: Aangepaste roltoewijzingen weer geven in de Azure AD-Portal | Microsoft Docs
description: U kunt nu leden van een Azure AD-beheerdersrol weer geven en beheren in het Azure AD-beheer centrum.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc83a069f4a7ee8fc654da06f57cf876b6b5a139
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741975"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Aangepaste roltoewijzingen in Azure Active Directory weer geven

In dit artikel wordt beschreven hoe u aangepaste rollen kunt weer geven die u hebt toegewezen in Azure Active Directory (Azure AD). In Azure Active Directory (Azure AD) kunnen rollen worden toegewezen in een organisatie bereik of met een bereik met één toepassing.

- Roltoewijzingen voor het bereik van de organisatie worden toegevoegd aan en kunnen worden weer gegeven in de lijst met toewijzingen voor afzonderlijke toepassings rollen.
- Roltoewijzingen in het bereik met één toepassing worden niet toegevoegd aan en kunnen niet worden weer gegeven in de lijst met bereik toewijzingen in de organisatie.

## <a name="view-role-assignments-in-the-azure-portal"></a>Roltoewijzingen weer geven in de Azure Portal

In deze procedure wordt beschreven hoe u toewijzingen van een rol met bereik voor de hele organisatie weergeeft.

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met privileged Role Administrator of Global Administrator Permissions in de Azure AD-organisatie.
1. Selecteer **Azure Active Directory**, selecteer **rollen en beheerders** en selecteer vervolgens een rol om deze te openen en de eigenschappen ervan weer te geven.
1. Selecteer **toewijzingen** om de toewijzingen voor de rol weer te geven.

    ![Roltoewijzingen en-machtigingen weer geven bij het openen van een rol in de lijst](./media/custom-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Roltoewijzingen weer geven met behulp van Azure AD Power shell

In deze sectie wordt beschreven hoe u toewijzingen van een rol met bereik voor de hele organisatie weergeeft. In dit artikel wordt gebruikgemaakt van de [Azure Active Directory module Power shell versie 2](/powershell/module/azuread/#directory_roles) . Als u Scope toewijzingen met één toepassing wilt weer geven met behulp van Power shell, kunt u de cmdlets gebruiken in [aangepaste rollen toewijzen met Power shell](./custom-assign-powershell.md).

### <a name="prepare-powershell"></a>PowerShell voorbereiden

Eerst moet u [de Azure ad preview Power shell-module downloaden](https://www.powershellgallery.com/packages/AzureAD/).

Als u de Azure AD Power shell-module wilt installeren, gebruikt u de volgende opdrachten:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Als u wilt controleren of de module gereed is voor gebruik, gebruikt u de volgende opdracht:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>De toewijzingen van een rol weer geven

Voor beeld van het weer geven van de toewijzingen van een rol.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Roltoewijzingen weer geven met behulp van Microsoft Graph-API

In deze sectie wordt beschreven hoe u toewijzingen van een rol met bereik voor de hele organisatie weergeeft.  Als u Scope toewijzingen met één toepassing wilt weer geven met behulp van Graph API, kunt u de bewerkingen gebruiken in [aangepaste rollen toewijzen met Graph API](./custom-assign-graph.md).

HTTP-aanvraag voor het ophalen van een roltoewijzing voor een bepaalde roldefinitie.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Antwoord

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Toewijzingen van het bereik met één toepassing weer geven

In deze sectie wordt beschreven hoe u toewijzingen van een rol met een bereik met één toepassing weergeeft. Deze functie is momenteel beschikbaar als openbare preview-versie.

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met privileged Role Administrator of Global Administrator Permissions in de Azure AD-organisatie.
1. Selecteer **app-registraties** en selecteer vervolgens de app-registratie om de eigenschappen ervan weer te geven. Mogelijk moet u **alle toepassingen** selecteren om de volledige lijst van app-registraties in uw Azure AD-organisatie weer te geven.

    ![App-registraties maken of bewerken op de App-registraties pagina](./media/custom-view-assignments/appreg-all-apps.png)

1. Selecteer in de app-registratie **rollen en beheerders**, en selecteer vervolgens een rol om de eigenschappen ervan weer te geven.

    ![Roltoewijzingen van de app-registratie weer geven op de pagina App-registraties](./media/custom-view-assignments/appreg-assignments.png)

1. Selecteer **toewijzingen** om de toewijzingen voor de rol weer te geven. Als u de weer gave toewijzingen in de app-registratie opent, ziet u de toewijzingen die zijn afgestemd op deze Azure AD-resource.

    ![Roltoewijzingen van de app-registratie weer geven vanuit de eigenschappen van een app-registratie](./media/custom-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Volgende stappen

* U kunt dit met ons delen op het forum voor [Azure AD-beheerders](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Zie [beheerders rollen toewijzen](permissions-reference.md)voor meer informatie over functies en de toewijzing van beheerdersrol.
* Zie voor standaard gebruikers machtigingen een [vergelijking van de standaard machtigingen voor gast-en gebruikers rechten](../fundamentals/users-default-permissions.md).
