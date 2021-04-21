---
title: Een beheerde identiteit toewijzen aan een toepassingsrol met behulp van PowerShell - Azure AD
description: Stapsgewijs instructies voor het toewijzen van een beheerde identiteit met behulp van PowerShell aan de rol van een andere toepassing.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5150c26d67b77008c11764cc6e51ca7085ffcb31
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784907"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Een beheerde identiteit toegang geven tot een toepassingsrol met behulp van PowerShell

Beheerde identiteiten voor Azure-resources bieden Azure-services een identiteit in Azure Active Directory. Ze werken zonder referenties in uw code. Azure-services gebruiken deze identiteit voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie. Toepassingsrollen bieden een vorm van op rollen gebaseerd toegangsbeheer en bieden een service de mogelijkheid om autorisatieregels te implementeren.

In dit artikel leert u hoe u een beheerde identiteit kunt toewijzen aan een toepassingsrol die beschikbaar wordt gemaakt door een andere toepassing met behulp van Azure AD PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de voorbeeldscripts wilt uitvoeren, hebt u twee opties:
    - Gebruik de [Azure Cloud Shell](../../cloud-shell/overview.md), die u kunt  openen met behulp van de knop Uitproberen in de rechterbovenhoek van codeblokken.
    - Voer scripts lokaal uit door de nieuwste versie van [Azure AD PowerShell te installeren.](/powershell/azure/active-directory/install-adv2)

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Een beheerde identiteit toegang geven tot de app-rol van een andere toepassing

1. Schakel beheerde identiteit in voor een Azure-resource, [zoals een Azure-VM.](qs-configure-powershell-windows-vm.md)

1. Zoek de object-id van de service-principal van de beheerde identiteit.

   **Voor een door het systeem toegewezen beheerde identiteit** vindt u de object-id op de Azure Portal op de pagina **Identiteit van de** resource. U kunt ook het volgende PowerShell-script gebruiken om de object-id te vinden. U hebt de resource-id nodig van de resource die u in stap 1 hebt gemaakt. Deze is beschikbaar in de Azure Portal op de pagina **Eigenschappen van de** resource.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    Voor een door de gebruiker toegewezen beheerde identiteit kunt u de **object-id** van de beheerde identiteit vinden op de Azure Portal op de pagina Overzicht **van de** resource. U kunt ook het volgende PowerShell-script gebruiken om de object-id te vinden. U hebt de resource-id van de door de gebruiker toegewezen beheerde identiteit nodig.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Maak een nieuwe toepassingsregistratie die staat voor de service waar uw beheerde identiteit een aanvraag naar verzendt. Als de API of service die de app-rol verleent aan de beheerde identiteit al een service-principal in uw Azure AD-tenant heeft, slaat u deze stap over. Als u de beheerde identiteit bijvoorbeeld toegang wilt verlenen tot de Microsoft Graph-API, kunt u deze stap overslaan.

1. Zoek de object-id van de service-principal van de servicetoepassing. U vindt deze met behulp van de Azure Portal. Ga naar Azure Active Directory en open de **pagina Bedrijfstoepassingen,** zoek de toepassing en zoek naar de **object-id**. U kunt de object-id van de service-principal ook vinden op basis van de weergavenaam met behulp van het volgende PowerShell-script:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Weergavenamen voor toepassingen zijn niet uniek. Controleer daarom of u de juiste service-principal van de toepassing hebt.

1. Voeg een [app-rol toe](../develop/howto-add-app-roles-in-azure-ad-apps.md) aan de toepassing die u in stap 3 hebt gemaakt. U kunt de rol maken met behulp van de Azure Portal of met behulp van Microsoft Graph. U kunt bijvoorbeeld een app-rol als deze toevoegen:

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
    * `managedIdentityObjectId`: de object-id van de service-principal van de beheerde identiteit, die u in stap 2 hebt gevonden.
    * `serverServicePrincipalObjectId`: de object-id van de service-principal van de servertoepassing, die u in stap 4 hebt gevonden.
    * `appRoleId`: de id van de app-rol die wordt blootgesteld door de server-app, die u in stap 5 hebt gegenereerd. In het voorbeeld is de app-rol-id `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Voer het volgende PowerShell-script uit om de roltoewijzing toe te voegen:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Script voltooien

In dit voorbeeldscript ziet u hoe u de beheerde identiteit van een Azure-web-app toewijst aan een app-rol.

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

- [Overzicht van beheerde identiteit voor Azure-resources](overview.md)
- Zie Beheerde identiteiten configureren voor Azure-resources op een azure-VM met behulp van PowerShell als u beheerde identiteiten wilt [inschakelen op een virtuele Azure-VM.](qs-configure-powershell-windows-vm.md)
