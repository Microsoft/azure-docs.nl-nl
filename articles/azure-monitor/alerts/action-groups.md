---
title: Actiegroepen maken en beheren in Azure Portal
description: Meer informatie over het maken en beheren van actie groepen in de Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: dukek
ms.openlocfilehash: 0771249e94d3e00cbeaff00406a0dbf33777a14d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490327"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Actiegroepen maken en beheren in Azure Portal
Een actie groep is een verzameling voor keuren voor meldingen die zijn gedefinieerd door de eigenaar van een Azure-abonnement. Azure Monitor-en Service Health-waarschuwingen gebruiken actie groepen om gebruikers te laten weten dat een waarschuwing is geactiveerd. Verschillende waarschuwingen kunnen dezelfde actie groep of verschillende actie groepen gebruiken, afhankelijk van de vereisten van de gebruiker. 

In dit artikel wordt beschreven hoe u actie groepen maakt en beheert in de Azure Portal.

Elke actie bestaat uit de volgende eigenschappen:

* **Type**: de melding of actie die is uitgevoerd. Voor beelden hiervan zijn het verzenden van een spraak oproep, SMS, e-mail; of het activeren van verschillende typen geautomatiseerde acties. Zie typen verderop in dit artikel.
* **Naam**: een unieke id in de actie groep.
* **Details**: de bijbehorende details die per *type* verschillen.

Zie voor meer informatie over het gebruik van Azure Resource Manager sjablonen voor het configureren van actie groepen de [actie groep Resource Manager-sjablonen](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Een actie groep maken met behulp van de Azure Portal

1. Zoek in het [Azure Portal](https://portal.azure.com)naar en selecteer **monitor**. In het deel venster **monitor** worden al uw bewakings instellingen en-gegevens in één weer gave geconsolideerd.

1. Selecteer **waarschuwingen** en selecteer vervolgens **acties beheren**.

    ![Knop acties beheren](./media/action-groups/manage-action-groups.png)
    
1. Selecteer **actie groep toevoegen** en vul de relevante velden in de wizard ervaring in.

    ![De opdracht ' actie groep toevoegen '](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Basis instellingen voor de actie groep configureren

Onder **Project Details**:

Selecteer het **abonnement** en de **resource groep** waarin de actie groep wordt opgeslagen.

Geef onder **Exemplaardetails** het volgende op:

1. Voer een naam in voor de **actie groep**.

1. Voer een **weergave naam** in. De weergave naam wordt gebruikt in plaats van de naam van een volledige actie groep wanneer er meldingen worden verzonden met behulp van deze groep.

      ![Het dialoog venster actie groep toevoegen](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Meldingen configureren

1. Klik op de knop **volgende: meldingen >** om naar het tabblad **meldingen** te gaan of selecteer het tabblad **meldingen** boven aan het scherm.

1. Definieer een lijst met meldingen die moeten worden verzonden wanneer een waarschuwing wordt geactiveerd. Geef het volgende op voor elke melding:

    a. **Meldings type**: Selecteer het type melding dat u wilt verzenden. De beschikbare opties zijn:
      * Rol e-mail Azure Resource Manager: een e-mail verzenden naar gebruikers die zijn toegewezen aan bepaalde ARM-rollen op abonnements niveau.
      * E-mail/SMS/push/Voice: verzend deze meldings typen naar specifieke ontvangers.
    
    b. **Naam**: Voer een unieke naam in voor de melding.

    c. **Details**: Voer een e-mail adres, telefoon nummer, enzovoort in op basis van het geselecteerde meldings type.
    
    d. **Algemeen waarschuwings schema**: u kunt ervoor kiezen om het [algemene waarschuwings schema](./alerts-common-schema.md)in te scha kelen. Dit biedt het voor deel van het gebruik van een single Extensible en Unified alert Payload in alle waarschuwings Services in azure monitor.

    ![Het tabblad meldingen](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Acties configureren

1. Klik op de knop **volgende: acties >** om naar het tabblad **acties** te gaan of selecteer het tabblad **acties** boven aan het scherm.

1. Definieer een lijst met acties die moeten worden geactiveerd wanneer een waarschuwing wordt geactiveerd. Geef het volgende op voor elke actie:

    a. **Actie type**: Selecteer Automation-Runbook, Azure function, ITSM, Logic app, beveiligde webhook, webhook.
    
    b. **Naam**: Voer een unieke naam in voor de actie.

    c. **Details**: Voer een webhook-URI, een Azure-app, ITSM-verbinding of een Automation-runbook in op basis van het actie type. Geef voor ITSM-actie ook **werk item** en andere velden op die nodig zijn voor het ITSM-hulp programma.
    
    d. **Algemeen waarschuwings schema**: u kunt ervoor kiezen om het [algemene waarschuwings schema](./alerts-common-schema.md)in te scha kelen. Dit biedt het voor deel van het gebruik van een single Extensible en Unified alert Payload in alle waarschuwings Services in azure monitor.
    
    ![Het tabblad acties](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>De actie groep maken

1. U kunt indien gewenst de instellingen voor **tags** bekijken. Hiermee kunt u sleutel-waardeparen koppelen aan de actie groep voor uw categorisatie en is deze een functie die beschikbaar is voor elke Azure-resource.

    ![Het tabblad Labels](./media/action-groups/action-group-4-tags.png)
    
1. Klik op **Controleren + maken** om de instellingen te controleren. Er wordt een snelle validatie uitgevoerd van uw invoer om ervoor te zorgen dat alle vereiste velden zijn geselecteerd. Als er problemen zijn, worden deze hier vermeld. Zodra u de instellingen hebt gecontroleerd, klikt u op **maken** om de actie groep in te richten.
    
    ![Het tabblad controleren en maken](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Wanneer u een actie configureert om een persoon op de hoogte te stellen per e-mail of SMS, ontvangt de gebruiker een bevestiging dat deze is toegevoegd aan de actie groep.

## <a name="manage-your-action-groups"></a>Uw actie groepen beheren

Nadat u een actie groep hebt gemaakt, kunt u **actie groepen** weer geven door **acties beheren** te selecteren op de pagina **waarschuwings signalen** in het deel venster **monitor** . Selecteer de actie groep die u wilt beheren:

* Acties toevoegen, bewerken of verwijderen.
* De actie groep verwijderen.

## <a name="action-specific-information"></a>Actie-specifieke informatie

> [!NOTE]
> Zie [limieten voor abonnements Services voor controle](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) op numerieke limieten voor elk van de onderstaande items.  

### <a name="automation-runbook"></a>Automation-runbook
Raadpleeg de [service limieten](../../azure-resource-manager/management/azure-subscription-service-limits.md) van het Azure-abonnement voor limieten voor nettoladingen van het Runbook.

Mogelijk hebt u een beperkt aantal Runbook-acties in een actie groep. 

### <a name="azure-app-push-notifications"></a>Push meldingen van Azure-app
Schakel push meldingen in voor de [Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/) door het e-mail adres op te geven dat u gebruikt als uw account-id bij het configureren van de Azure Mobile App.

Mogelijk hebt u een beperkt aantal Azure-app-acties in een actie groep.

### <a name="email"></a>E-mail
E-mails worden verzonden vanaf de volgende e-mail adressen. Controleren of uw e-mail filtering op de juiste wijze is geconfigureerd
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Mogelijk hebt u een beperkt aantal e-mail acties in een actie groep. Zie het artikel [informatie over de frequentie beperking](./alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>E-mailadres voor Azure Resource Manager-rol
E-mail verzenden naar de leden van de rol van het abonnement. E-mail wordt alleen verzonden naar **Azure AD-gebruikers** leden van de rol. Er wordt geen e-mail verzonden naar Azure AD-groepen of service-principals.

Een e-mail melding wordt alleen verzonden naar het *primaire e-mail* adres.

Als u geen meldingen ontvangt op uw *primaire e-mail*, kunt u de volgende stappen uitvoeren:

1. Ga in Azure Portal naar *Active Directory*.
2. Klik op alle gebruikers (in het linkerdeel venster). er wordt een lijst met gebruikers weer gegeven (in het rechterdeel venster).
3. Selecteer de gebruiker voor wie u de *primaire e-mail* gegevens wilt controleren.

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Voor beeld voor het controleren van het gebruikers profiel." border="true":::

4. Klik in het gebruikers profiel onder contact gegevens op het tabblad E-mail op de knop *bewerken* bovenaan en voeg uw *primaire e-mail* toe en klik bovenaan op de knop *Opslaan* .

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Voor beeld voor het toevoegen van een primair e-mail bericht." border="true":::

Mogelijk hebt u een beperkt aantal e-mail acties in een actie groep. Zie het artikel [informatie over de frequentie beperking](./alerts-rate-limiting.md) .

### <a name="function"></a>Functie
Hiermee wordt een bestaand HTTP trigger-eind punt aangeroepen in [Azure functions](../../azure-functions/functions-get-started.md).

Mogelijk hebt u een beperkt aantal functie acties in een actie groep.

### <a name="itsm"></a>ITSM
Voor de actie ITSM is een ITSM-verbinding vereist. Meer informatie over het maken van een [ITSM-verbinding](./itsmc-overview.md).

Mogelijk hebt u een beperkt aantal ITSM-acties in een actie groep. 

### <a name="logic-app"></a>Logische apps
Mogelijk hebt u een beperkt aantal logische app-acties in een actie groep.

### <a name="secure-webhook"></a>Beveiligde webhook

> [!NOTE]
> Het gebruik van de webhook-actie vereist dat het eind punt van de doel-webhook geen details vereist van de waarschuwing om correct te laten functioneren of kan de waarschuwings context gegevens parseren die worden verstrekt als onderdeel van de POST-bewerking. Als het webhook-eind punt de informatie over de context van de waarschuwing niet zelf kan verwerken, kunt u een oplossing gebruiken zoals een actie van een [logische app](./action-groups-logic-app.md) voor een aangepaste manipulatie van de waarschuwings context informatie die overeenkomt met de verwachte gegevens indeling van de webhook.
> De gebruiker moet de **eigenaar** zijn van de service-principal van de webhook om ervoor te zorgen dat de beveiliging niet wordt geschonden. Elke Azure-klant heeft toegang tot alle object-Id's via de portal, zonder de eigenaar te controleren, maar iedereen kan de beveiligde webhook toevoegen aan hun eigen actie groep voor Azure monitor-waarschuwings melding die de beveiliging schendt.

Met de actie groepen webhook kunt u gebruikmaken van Azure Active Directory om de verbinding tussen uw actie groep en de beveiligde web-API (webhook-eind punt) te beveiligen. De algemene werk stroom voor het gebruik van deze functionaliteit wordt hieronder beschreven. Zie [overzicht van micro soft Identity platform (v 2.0)](../../active-directory/develop/v2-overview.md)voor een overzicht van Azure AD-toepassingen en-service-principals.

1. Maak een Azure AD-toepassing voor uw beveiligde web-API. Zie de [beveiligde web-API: app-registratie](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Configureer uw beveiligde API om te worden [aangeroepen door een daemon-app](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Schakel actie groepen in voor het gebruik van uw Azure AD-toepassing.

    > [!NOTE]
    > U moet lid zijn van de [rol Azure AD-toepassings beheerder](../../active-directory/roles/permissions-reference.md#all-roles) om dit script uit te voeren.
    
    - Wijzig de Connect-AzureAD aanroep van het Power shell-script om uw Azure AD-Tenant-ID te gebruiken.
    - Wijzig de variabele van het Power shell-script $myAzureADApplicationObjectId om de object-ID van uw Azure AD-toepassing te gebruiken.
    - Voer het gewijzigde script uit.
    
3. Configureer de actie groep beveiligde webhooks.
    - Kopieer de waarde $myApp. ObjectId uit het script en voer deze in het veld toepassings object-ID in de actie definitie van webhook in.
    
    ![Actie beveiligde webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Power shell-script voor beveiligde webhook

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object ID of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
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
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application (ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>Sms
Zie de [informatie over het beperken](./alerts-rate-limiting.md) van de frequentie en het [gedrag van SMS-berichten](./alerts-sms-behavior.md) voor aanvullende belang rijke informatie. 

Mogelijk hebt u een beperkt aantal SMS-acties in een actie groep.

> [!NOTE]
> Als de gebruikers interface van de Azure Portal actie groep niet toestaat dat u uw land-/regiocode selecteert, wordt SMS niet ondersteund voor uw land/regio.  Als uw land-/regiocode niet beschikbaar is, kunt u stemmen om uw land/regio toe te voegen op de stem van de [gebruiker](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). In afwachting van een tijdelijke oplossing is het aan te bieden dat uw actie groep een webhook aanroept aan een SMS-provider van derden met ondersteuning in uw land/regio.  

Prijzen voor ondersteunde landen/regio's worden vermeld op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).

**Lijst met landen waarin SMS-meldingen worden ondersteund**

| Landnummer | Naam van land/regio |
|:---|:---|
| 61 | Australië |
| 43 | Oostenrijk |
| 32 | België |
| 55 | Brazilië |
| 1 |Canada |
| 56 | Chili |
| 86 | China |
| 420 | Tsjechische Republiek |
| 45 | Denemarken |
| 372 | Estland |
| 358 | Finland |
| 33 | Frankrijk |
| 49 | Duitsland |
| 852 | Hongkong |
| 91 | India |
| 353 | Ierland |
| 972 | Israël |
| 39 | Italië |
| 81 | Japan |
| 352 | Luxemburg |
| 60 | Maleisië |
| 52 | Mexico |
| 31 | Nederland |
| 64 | Nieuw-Zeeland |
| 47 | Noorwegen |
| 351 | Portugal |
| 1 | Puerto Rico |
| 40 | Roemenië |
| 65 | Singapore |
| 27 | Zuid-Afrika |
| 82 | Zuid-Korea |
| 34 | Spanje |
| 41 | Zwitserland |
| 886 | Taiwan |
| 44 | Verenigd Koninkrijk |
| 1 | Verenigde Staten |

### <a name="voice"></a>Spraak
Zie het artikel [informatie over de frequentie beperking](./alerts-rate-limiting.md) voor meer belang rijk gedrag.

Mogelijk hebt u een beperkt aantal spraak acties in een actie groep.

> [!NOTE]
> Als de gebruikers interface van de Azure Portal actie groep niet toestaat dat u uw land-/regiocode selecteert, worden telefoon gesprekken niet ondersteund voor uw land/regio. Als uw land-/regiocode niet beschikbaar is, kunt u stemmen om uw land/regio toe te voegen op de stem van de [gebruiker](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  In de tussen tijd is het een goed hulp om uw actie groep een webhook te laten aanroepen naar een aanbieder van een telefoon oproep van derden met ondersteuning voor uw land/regio.  
> De land code die nu wordt ondersteund in Azure Portal actie groep voor spraak meldingen is + 1 (Verenigde Staten). 

Prijzen voor ondersteunde landen/regio's worden vermeld op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>Webhook

> [!NOTE]
> Het gebruik van de webhook-actie vereist dat het eind punt van de doel-webhook geen details vereist van de waarschuwing om correct te laten functioneren of kan de waarschuwings context gegevens parseren die worden verstrekt als onderdeel van de POST-bewerking. Als het webhook-eind punt de informatie over de context van de waarschuwing niet zelf kan verwerken, kunt u een oplossing gebruiken zoals een actie van een [logische app](./action-groups-logic-app.md) voor een aangepaste manipulatie van de waarschuwings context informatie die overeenkomt met de verwachte gegevens indeling van de webhook.

Webhooks worden verwerkt aan de hand van de volgende regels
- Voor een webhook-aanroep is Maxi maal drie keer geprobeerd.
- Er wordt opnieuw geprobeerd de aanroep uit te voeren als er geen antwoord wordt ontvangen binnen de time-outperiode of als een van de volgende HTTP-status codes wordt geretourneerd: 408, 429, 503 of 504.
- Bij de eerste aanroep wordt 10 seconden gewacht op een antwoord.
- De tweede en derde keer wachten 30 seconden op een reactie.
- Nadat de drie pogingen tot het aanroepen van de webhook zijn mislukt, wordt het eind punt gedurende 15 minuten door de actie groep aangeroepen.

Zie [IP-adressen van actie groepen](../app/ip-addresses.md) voor bron-IP-adresbereiken.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gedrag van SMS-waarschuwingen](./alerts-sms-behavior.md).  
* Krijg [inzicht in het webhook-schema van waarschuwingen voor activiteiten logboeken](./activity-log-alerts-webhook.md).  
* Meer informatie over [ITSM-connector](./itsmc-overview.md).
* Meer informatie over de [frequentie limiet](./alerts-rate-limiting.md) voor waarschuwingen.
* Een [overzicht van waarschuwingen voor het activiteitenlogboek](./alerts-overview.md) en meer informatie over het ontvangen van waarschuwingen.  
* Meer informatie over het [configureren van waarschuwingen wanneer een service status melding wordt geplaatst](../../service-health/alerts-activity-log-service-notifications-portal.md).
