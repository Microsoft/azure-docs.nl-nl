---
title: Secure webhook-levering met Azure AD in Azure Event Grid
description: Hierin wordt beschreven hoe u gebeurtenissen kunt leveren aan HTTPS-eind punten die worden beveiligd door Azure Active Directory met behulp van Azure Event Grid
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: b01f0342e185883c4d1552800ab9bdb30cea3040
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549149"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Gebeurtenissen publiceren naar beveiligde Azure Active Directory-eindpunten

In dit artikel wordt beschreven hoe u Azure Active Directory kunt gebruiken om de verbinding tussen uw gebeurtenis abonnement en het eind punt van de webhook te beveiligen. Zie [overzicht van micro soft Identity platform (v 2.0)](../active-directory/develop/v2-overview.md)voor een overzicht van Azure AD-toepassingen en-service-principals.

In dit artikel wordt gebruikgemaakt van de Azure Portal voor demonstratie, maar de functie kan ook worden ingeschakeld met CLI, Power shell of de Sdk's.


## <a name="create-an-azure-ad-application"></a>Een Azure AD-toepassing maken

Maak eerst een Azure AD-toepassing voor uw beveiligde eind punt. Zie https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configureer uw beveiligde API om te worden aangeroepen door een daemon-app.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Event Grid inschakelen voor het gebruik van uw Azure AD-toepassing
In deze sectie wordt beschreven hoe u Event Grid kunt inschakelen om uw Azure AD-toepassing te gebruiken. 

> [!NOTE]
> U moet lid zijn van de [rol Azure AD-toepassings beheerder](../active-directory/roles/permissions-reference.md#available-roles) om dit script uit te voeren.

### <a name="connect-to-your-azure-tenant"></a>Verbinding maken met uw Azure-Tenant
Maak eerst verbinding met uw Azure-Tenant met behulp van de `Connect-AzureAD` opdracht. 

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"
Connect-AzureAD -TenantId $myTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>De Service-Principal micro soft. EventGrid maken
Voer het volgende script uit om de service-principal te maken voor **micro soft. EventGrid** als deze nog niet bestaat. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Een rol maken voor uw toepassing   
Voer het volgende script uit om een rol te maken voor uw Azure AD-toepassing. In dit voor beeld is de naam van de rol: **AzureEventGridSecureWebhook**. Wijzig het Power shell `$myTenantId` -script voor het gebruik van uw Azure AD-Tenant-id en `$myAzureADApplicationObjectId` met de object-id van uw Azure AD-toepassing

```PowerShell
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}

# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
} else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Event Grid Service-Principal toevoegen aan de rol    
Voer nu de `New-AzureADServiceAppRoleAssignment` opdracht uit om event Grid Service-Principal toe te wijzen aan de rol die u in de vorige stap hebt gemaakt. 

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Voer de volgende opdrachten uit om informatie te krijgen over de volgende stappen. 

```powershell    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
```
    
## <a name="configure-the-event-subscription"></a>Het gebeurtenis abonnement configureren

Selecteer in de stroom voor het maken van uw gebeurtenis abonnement het eindpunt type ' webhook '. Zodra u uw eind punt-URI hebt opgegeven, klikt u op het tabblad Extra functies boven aan de Blade gebeurtenis abonnementen maken.

![Type webhook voor eind punt selecteren](./media/secure-webhook-delivery/select-webhook.png)

Schakel op het tabblad Extra functies het selectie vakje ' AAD-verificatie gebruiken ' in en configureer de Tenant-ID en toepassings-ID:

* Kopieer de Azure AD-Tenant-ID uit de uitvoer van het script en voer deze in het veld AAD-Tenant-ID in.
* Kopieer de Azure AD-toepassings-ID uit de uitvoer van het script en voer deze in het veld AAD-toepassings-ID in.

    ![Actie beveiligde webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)voor meer informatie over het bewaken van gebeurtenis leveringen.
* Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie over de verificatie sleutel.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
