---
title: Beveiligde webhooklevering met Azure AD in Azure Event Grid
description: Beschrijft hoe u gebeurtenissen levert aan HTTPS-eindpunten die worden beveiligd door Azure Active Directory met Azure Event Grid
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 6a0f9059e17d96d497b425abc9749e69c5ab4d41
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575544"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Gebeurtenissen publiceren naar beveiligde Azure Active Directory-eindpunten
In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD) gebruikt om de verbinding tussen uw gebeurtenisabonnement en uw **webhook-eindpunt te beveiligen.**  Zie Overzicht van Microsoft [Identity Platform (v2.0)](../active-directory/develop/v2-overview.md)voor een overzicht van Azure AD-toepassingen en service-principals.

In dit artikel wordt Azure Portal demonstratie gebruikt, maar de functie kan ook worden ingeschakeld met cli, PowerShell of de SDK's.

> [!IMPORTANT]
> Er is een extra toegangscontrole geïntroduceerd als onderdeel van het maken of bijwerken van een gebeurtenisabonnement op 30 maart 2021 om een beveiligingsprobleem op te pakken. De service-principal van de abonneeclient moet een eigenaar zijn of een rol hebben toegewezen aan de service-principal van de doeltoepassing. Configureer uw AAD-toepassing opnieuw aan de hand van de onderstaande nieuwe instructies.


## <a name="create-an-azure-ad-application"></a>Een Azure AD-toepassing maken
Registreer uw webhook bij Azure AD door een Azure AD-toepassing te maken voor uw beveiligde eindpunt. Zie [Scenario: Beveiligde web-API.](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview) Configureer uw beveiligde API om te worden aangeroepen door een daemon-app.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Uw Event Grid Azure AD-toepassing gebruiken inschakelen
In deze sectie ziet u hoe u Event Grid Azure AD-toepassing kunt gebruiken. 

> [!NOTE]
> U moet lid zijn van de rol [Azure AD-toepassingsbeheerder om](../active-directory/roles/permissions-reference.md#all-roles) dit script uit te voeren.

### <a name="connect-to-your-azure-tenant"></a>Verbinding maken met uw Azure-tenant
Maak eerst verbinding met uw Azure-tenant met behulp van de `Connect-AzureAD` opdracht . 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Microsoft.EventGrid-service-principal maken
Voer het volgende script uit om de service-principal voor **Microsoft.EventGrid te** maken als deze nog niet bestaat. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else {
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Een rol maken voor uw toepassing   
Voer het volgende script uit om een rol te maken voor uw Azure AD-toepassing. In dit voorbeeld is de naam van de rol: **AzureEventGridSecureWebhookSubscriber.** Wijzig de van het PowerShell-script `$myTenantId` om uw Azure AD-tenant-id en met `$myAzureADApplicationObjectId` de object-id van uw Azure AD-toepassing te gebruiken

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
} else {      
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

### <a name="create-role-assignment-for-the-client-creating-event-subscription"></a>Roltoewijzing maken voor de client die een gebeurtenisabonnement maakt
De roltoewijzing moet worden gemaakt in de webhook-Azure AD-app voor de AAD-app of AAD-gebruiker die het gebeurtenisabonnement maakt. Gebruik een van de onderstaande scripts, afhankelijk van of een AAD-app of AAD-gebruiker het gebeurtenisabonnement maakt.

> [!IMPORTANT]
> Er is een extra toegangscontrole geïntroduceerd als onderdeel van het maken of bijwerken van een gebeurtenisabonnement op 30 maart 2021 om een beveiligingsprobleem aan te pakken. De service-principal van de abonneeclient moet een eigenaar zijn of een rol hebben toegewezen aan de service-principal van de doeltoepassing. Configureer uw AAD-toepassing opnieuw aan de hand van de onderstaande nieuwe instructies.

#### <a name="create-role-assignment-for-an-event-subscription-aad-app"></a>Roltoewijzing maken voor een AAD-app voor een gebeurtenisabonnement 

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
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="create-role-assignment-for-an-event-subscription-aad-user"></a>Roltoewijzing maken voor een AAD-gebruiker voor een gebeurtenisabonnement 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="create-role-assignment-for-event-grid-service-principal"></a>Roltoewijzing maken voor Event Grid Service-principal
Voer de New-AzureADServiceAppRoleAssignment uit om een Event Grid toe te wijzen aan de rol die u in de vorige stap hebt gemaakt.

```powershell
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Voer de volgende opdrachten uit om informatie uit te voeren die u later gaat gebruiken.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Het gebeurtenisabonnement configureren
Volg deze stappen bij het maken van een gebeurtenisabonnement:

1. Selecteer het eindpunttype web **hook**. 
1. Geef de **eindpunt-URI op.**

    ![Webhook van het eindpunttype selecteren](./media/secure-webhook-delivery/select-webhook.png)
1. Selecteer het **tabblad Extra** functies bovenaan de pagina **Gebeurtenisabonnementen** maken.
1. Ga als volgende **te werk op** het tabblad Extra functies:
    1. Selecteer **AAD-verificatie gebruiken** en configureer de tenant-id en toepassings-id:
    1. Kopieer de Azure AD-tenant-id uit de uitvoer van het script en voer deze in het veld **Tenant-id van AAD** in.
    1. Kopieer de Azure AD-toepassings-id uit de uitvoer van het script en voer deze in het **veld AAD-toepassings-id** in. U kunt ook de URI van de AAD-toepassings-id gebruiken. Zie dit artikel voor meer informatie over de URI van de [toepassings-id.](../app-service/configure-authentication-provider-aad.md)

        ![Actie Webhook beveiligen](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Volgende stappen

* Zie Monitor Event Grid message delivery (Levering van berichten controleren) Event Grid informatie over het bewaken [van leveringen van gebeurtenissen.](monitor-event-delivery.md)
* Zie voor meer informatie over de verificatiesleutel [Event Grid beveiliging en verificatie.](security-authentication.md)
* Zie abonnementschema voor meer Azure Event Grid over het [maken Event Grid abonnement.](subscription-creation-schema.md)
