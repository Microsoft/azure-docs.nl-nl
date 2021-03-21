---
title: Limieten voor het maken van app-registraties verwijderen - Azure AD | Microsoft Docs
description: Een aangepaste rol toewijzen voor het verlenen van onbeperkte app-registraties in de Azure AD-Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: quickstart
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48740c52e6713735957e1ebf00e8996800eabec2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015774"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Quickstart: Machtigingen verlenen voor onbeperkte app-registraties

In deze quickstart maakt u een aangepaste rol met machtigingen voor het maken van een onbeperkt aantal app-registraties en vervolgens wijst u die rol toe aan een gebruiker. De toegewezen gebruiker kan vervolgens de Azure AD Portal, Azure AD PowerShell of de Microsoft Graph-API gebruiken om toepassingsregistraties te maken. In tegenstelling tot de ingebouwde rol van toepassingsontwikkelaar verleent deze aangepaste rol de mogelijkheid om een onbeperkt aantal toepassingsregistraties te maken. De rol van toepassingsontwikkelaar biedt de mogelijkheid, maar het totale aantal gemaakte objecten is beperkt tot 250 om te voorkomen dat [het objectquotum voor de directory wordt overschreden](../enterprise-users/directory-service-limits-restrictions.md). De minst bevoorrechte rol die vereist is voor het maken en toewijzen van aangepaste Azure AD-rollen, is Beheerder voor bevoorrechte rollen.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-custom-role-using-the-azure-ad-portal"></a>Een aangepaste rol maken met behulp van de Azure AD-Portal

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met de machtigingen Beheerder voor bevoorrechte rollen of Globale beheerder in de Azure AD-organisatie.
1. Selecteer **Azure Active Directory**, selecteer **Rollen en beheerders** en selecteer vervolgens **Nieuwe aangepaste rol**.

    ![Rollen maken of bewerken op de pagina Rollen en beheerders](./media/quickstart-app-registration-limits/new-custom-role.png)

1. Geef op het tabblad **Basis** de optie 'Application Registration Creator' op als de naam van de rol en 'Can create an unlimited number of application registrations' als de beschrijving van de functie en selecteer vervolgens **Volgende**.

    ![Geef een naam en beschrijving op voor een aangepaste rol op het tabblad Basis](./media/quickstart-app-registration-limits/basics-tab.png)

1. Voer op het tabblad **Machtigingen** de optie microsoft.directory/applications/create in het zoekvak in, selecteer de selectievakjes naast de gewenste machtigingen en selecteer vervolgens **Volgende**.

    ![De machtigingen voor een aangepaste rol selecteren op het tabblad Machtigingen](./media/quickstart-app-registration-limits/permissions-tab.png)

1. Controleer op het tabblad **Beoordelen en maken** de machtigingen en selecteer **Maken**.

### <a name="assign-the-role-in-the-azure-ad-portal"></a>De rol toewijzen in de Azure AD-Portal

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met de machtigingen Beheerder voor bevoorrechte rollen of Globale beheerder in uw Azure AD-organisatie.
1. Selecteer **Azure Active Directory** en selecteer vervolgens **Rollen en beheerders**.
1. Selecteer de rol Application Registration Creator en selecteer **Toewijzing toevoegen**.
1. Selecteer de gewenste gebruiker en klik op **Selecteren** om de gebruiker aan de rol toe te voegen.

Klaar! In deze quickstart hebt u een aangepaste rol gemaakt met machtigingen om een onbeperkt aantal app-registraties te maken en vervolgens die rol toe te wijzen aan een gebruiker.

> [!TIP]
> Als u de rol wilt toewijzen aan een toepassing met behulp van de Azure AD-Portal, voert u de naam van de toepassing in het zoekvak van de toewijzingspagina in. Toepassingen worden niet standaard weergegeven in de lijst, maar worden geretourneerd als zoekresultaten.

### <a name="app-registration-permissions"></a>Machtigingen voor app-registratie

Er zijn twee machtigingen beschikbaar voor het verlenen van de mogelijkheid om toepassingsregistraties te maken, elk met verschillende gedragingen.

- microsoft.directory/applications/createAsOwner: Als u deze machtiging toewijst, wordt de maker toegevoegd als de eerste eigenaar van de gemaakte app-registratie en wordt de gemaakte app-registratie meegeteld in het quotum van 250 gemaakte objecten van de maker.
- microsoft.directory/applications/create: Als u deze machtigingen toewijst, wordt de maker niet toegevoegd als de eerste eigenaar van de gemaakte app-registratie en wordt de gemaakte app-registratie niet meegeteld in het quotum van 250 gemaakte objecten van de maker. Gebruik deze machtiging op een verantwoorde manier, omdat de toegewezen persoon app-registraties kan maken totdat de quotum op directoryniveau wordt bereikt. Als beide machtigingen zijn toegewezen, heeft deze machtiging voorrang.

## <a name="create-a-custom-role-in-azure-ad-powershell"></a>Een aangepaste rol maken in Azure AD PowerShell

### <a name="prepare-powershell"></a>PowerShell voorbereiden

Installeer eerst de Azure AD PowerShell-module van de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importeer vervolgens de Azure AD PowerShell preview-module met de volgende opdracht:

```powershell
Import-Module -Name AzureADPreview
```

Als u wilt controleren of de module gereed is voor gebruik, gaat u naar de versie die wordt geretourneerd door de volgende opdracht:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role-in-azure-ad-powershell"></a>De aangepaste rol maken in Azure AD Power shell

Maak een nieuwe rol met behulp van het volgende PowerShell-script:

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-role-in-azure-ad-powershell"></a>De rol toewijzen in Azure AD PowerShell

Wijs de rol toe met behulp van het volgende PowerShell-script:

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-in-the-microsoft-graph-api"></a>Een aangepaste rol maken in de Microsoft Graph-API

HTTP-aanvraag voor het maken van de aangepaste rol.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Hoofdtekst

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-role-in-the-microsoft-graph-api"></a>De rol toewijzen in de Microsoft Graph-API

De roltoewijzing combineert een beveiligingsprincipal-ID (die een gebruiker of serviceprincipal kan zijn), een roldefinitie-ID (rol) en een Azure AD-resourcebereik.

HTTP-aanvraag om een aangepaste rol toe te wijzen.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Hoofdtekst

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Volgende stappen

- U kunt dit met ons delen op het forum voor [Azure AD-beheerders](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Zie [beheerdersrollen toewijzen voor meer informatie over Azure AD-roltoewijzingen](permissions-reference.md).
- Zie [vergelijking van standaardmachtigingen voor gasten en gebruikers](../fundamentals/users-default-permissions.md)voor meer informatie over standaard gebruikersmachtigingen.
