---
title: Een beheerde identiteit toewijzen aan een toepassingsrol met behulp van Power shell-Azure AD
description: Stapsgewijze instructies voor het toewijzen van een beheerde identiteits toegang aan de rol van een andere toepassing met behulp van Power shell.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2d844054e3744aafa01f43ded354a5b5d9f8b4b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732061"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Een beheerde identiteits toegang toewijzen aan een toepassingsrol met behulp van Power shell

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een identiteit in Azure Active Directory. Ze werken zonder dat er referenties in uw code hoeven te worden gebruikt. Azure-Services gebruiken deze identiteit voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie. Toepassings rollen bieden een vorm van op rollen gebaseerd toegangs beheer en toestaan dat een service autorisatie regels implementeert.

In dit artikel leert u hoe u een beheerde identiteit kunt toewijzen aan een toepassingsrol die wordt weer gegeven door een andere toepassing met behulp van Azure AD Power shell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de voorbeeldscripts wilt uitvoeren, hebt u twee opties:
    - Gebruik de [Azure Cloud shell](../../cloud-shell/overview.md), die u kunt openen met behulp van de knop **try it** in de rechter bovenhoek van code blokken.
    - Voer scripts lokaal uit door de nieuwste versie van [Azure AD Power shell](/powershell/azure/active-directory/install-adv2)te installeren.

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Een beheerde identiteits toegang tot de app-rol van een andere toepassing toewijzen

1. Beheerde identiteit inschakelen voor een Azure-resource, [zoals een Azure-VM](qs-configure-powershell-windows-vm.md).

1. Zoek de object-ID van de service-principal van de beheerde identiteit.

   **Voor een door het systeem toegewezen beheerde identiteit** kunt u de object-id vinden op de Azure Portal op de **identiteits** pagina van de resource. U kunt ook het volgende Power shell-script gebruiken om de object-ID te vinden. U hebt de resource-ID nodig van de resource die u hebt gemaakt in stap 1, die beschikbaar is in de Azure Portal op de pagina **Eigenschappen** van de resource.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **Voor een door de gebruiker toegewezen beheerde identiteit** kunt u de object-id van de beheerde identiteit vinden op het Azure Portal op de **overzichts** pagina van de resource. U kunt ook het volgende Power shell-script gebruiken om de object-ID te vinden. U hebt de resource-ID van de door de gebruiker toegewezen beheerde identiteit nodig.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Maak een nieuwe toepassings registratie om de service te vertegenwoordigen waarnaar uw beheerde identiteit een aanvraag verzendt. Als de API of service die de app-rol verleent aan de beheerde identiteit, al een Service-Principal in uw Azure AD-Tenant heeft, slaat u deze stap over. Als u bijvoorbeeld de beheerde identiteit toegang wilt verlenen aan de Microsoft Graph-API, kunt u deze stap overs Laan.

1. Zoek de object-ID van de service-principal van de service toepassing. U kunt dit vinden met behulp van de Azure Portal. Ga naar Azure Active Directory en open de pagina **bedrijfs toepassingen** , zoek de toepassing en zoek de **object-id**. U kunt de object-ID van de Service-Principal ook vinden met de weergave naam met behulp van het volgende Power shell-script:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Weergave namen voor toepassingen zijn niet uniek. Daarom moet u controleren of u de juiste service-principal voor de toepassing hebt verkregen.

1. Voeg een [app-functie](../develop/howto-add-app-roles-in-azure-ad-apps.md) toe aan de toepassing die u in stap 3 hebt gemaakt. U kunt de rol maken met behulp van de Azure Portal of Microsoft Graph gebruiken. U kunt bijvoorbeeld een app-functie als volgt toevoegen:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Wijs de app-rol toe aan de beheerde identiteit. U hebt de volgende informatie nodig om de app-rol toe te wijzen:
    * `managedIdentityObjectId`: de object-ID van de service-principal van de beheerde identiteit, die u in stap 2 hebt gevonden.
    * `serverServicePrincipalObjectId`: de object-ID van de service-principal van de server toepassing, die u in stap 4 hebt gevonden.
    * `appRoleId`: de ID van de app-functie die wordt weer gegeven door de server-app, die u in stap 5 hebt gegenereerd, is de app-functie-ID `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Voer het volgende Power shell-script uit om de roltoewijzing toe te voegen:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Script voltooien

In dit voorbeeld script ziet u hoe u de beheerde identiteit van een Azure-web-app toewijst aan een app-rol.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met Power shell](qs-configure-powershell-windows-vm.md)voor meer informatie over het inschakelen van beheerde identiteiten op een virtuele Azure-machine.