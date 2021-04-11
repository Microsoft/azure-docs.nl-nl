---
title: Secure webhook-levering met Azure AD in Azure Event Grid
description: Hierin wordt beschreven hoe u gebeurtenissen kunt leveren aan HTTPS-eind punten die worden beveiligd door Azure Active Directory met behulp van Azure Event Grid
ms.topic: how-to
ms.date: 03/20/2021
ms.openlocfilehash: 1298910db78ba468dd9744e84ee4629161e0a776
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076033"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Gebeurtenissen publiceren naar beveiligde Azure Active Directory-eindpunten
In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD) gebruikt om de verbinding tussen uw **gebeurtenis abonnement** en het **eind punt van de webhook** te beveiligen. Zie [overzicht van micro soft Identity platform (v 2.0)](../active-directory/develop/v2-overview.md)voor een overzicht van Azure AD-toepassingen en-service-principals.

In dit artikel wordt gebruikgemaakt van de Azure Portal voor demonstratie, maar de functie kan ook worden ingeschakeld met CLI, Power shell of de Sdk's.


## <a name="create-an-azure-ad-application"></a>Een Azure AD-toepassing maken
Registreer uw webhook bij Azure AD door een Azure AD-toepassing voor uw beveiligde eind punt te maken. Zie [scenario: beveiligde web-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview). Configureer uw beveiligde API om te worden aangeroepen door een daemon-app.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Event Grid inschakelen voor het gebruik van uw Azure AD-toepassing
In deze sectie wordt beschreven hoe u Event Grid kunt inschakelen om uw Azure AD-toepassing te gebruiken. 

> [!NOTE]
> U moet lid zijn van de [rol Azure AD-toepassings beheerder](../active-directory/roles/permissions-reference.md#all-roles) om dit script uit te voeren.

### <a name="connect-to-your-azure-tenant"></a>Verbinding maken met uw Azure-Tenant
Maak eerst verbinding met uw Azure-Tenant met behulp van de `Connect-AzureAD` opdracht. 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>De Service-Principal micro soft. EventGrid maken
Voer het volgende script uit om de service-principal te maken voor **micro soft. EventGrid** als deze nog niet bestaat. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Een rol maken voor uw toepassing   
Voer het volgende script uit om een rol te maken voor uw Azure AD-toepassing. In dit voor beeld is de naam van de rol: **AzureEventGridSecureWebhookSubscriber**. Wijzig het Power shell `$myTenantId` -script voor het gebruik van uw Azure AD-Tenant-id en `$myAzureADApplicationObjectId` met de object-id van uw Azure AD-toepassing

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.AllowedMemberTypes.Add("User");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-a-role-assignment"></a>Een roltoewijzing maken
De roltoewijzing moet worden gemaakt in de webhook Azure AD-app voor de AAD-app of de AAD-gebruiker die het gebeurtenis abonnement maakt. Gebruik een van de volgende scripts, afhankelijk van het feit of een AAD-app of AAD-gebruiker het gebeurtenis abonnement maakt.

#### <a name="option-a-create-a-role-assignment-for-event-subscription-aad-app"></a>Optie A. een roltoewijzing maken voor de AAD-app van het gebeurtenis abonnement 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="option-b-create-a-role-assignment-for-event-subscription-aad-user"></a>Optie B. een roltoewijzing maken voor een AAD-gebruiker van het gebeurtenis abonnement 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
New-AzureADUserAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Event Grid Service-Principal toevoegen aan de rol
Voer de New-AzureADServiceAppRoleAssignment opdracht uit om Event Grid Service-Principal toe te wijzen aan de rol die u in de vorige stap hebt gemaakt.

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Voer de volgende opdrachten uit om gegevens uit te voeren die u later gaat gebruiken.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Het gebeurtenis abonnement configureren
Voer de volgende stappen uit bij het maken van een gebeurtenis abonnement:

1. Selecteer het eindpunt type als **webhook**. 
1. Geef de eindpunt- **URI** op.

    ![Type webhook voor eind punt selecteren](./media/secure-webhook-delivery/select-webhook.png)
1. Klik boven aan de pagina **gebeurtenis abonnementen maken** op het tabblad **extra functies** .
1. Voer op het tabblad **extra functies** de volgende stappen uit:
    1. Selecteer **Aad-verificatie gebruiken** en configureer de Tenant-id en toepassings-id:
    1. Kopieer de Azure AD-Tenant-ID uit de uitvoer van het script en voer deze in het veld **Aad-Tenant-id** in.
    1. Kopieer de Azure AD-toepassings-ID uit de uitvoer van het script en voer deze in het veld **Aad-toepassings-id** in.

        ![Actie beveiligde webhook](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)voor meer informatie over het bewaken van gebeurtenis leveringen.
* Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie over de verificatie sleutel.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
