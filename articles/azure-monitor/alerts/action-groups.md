---
title: Actiegroepen maken en beheren in Azure Portal
description: Meer informatie over het maken en beheren van actiegroepen in de Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: dukek
ms.openlocfilehash: 1486415c5d225163dd2b2c7e79cd008ad0a76588
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514866"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Actiegroepen maken en beheren in Azure Portal
Een actiegroep is een verzameling meldingsvoorkeuren die is gedefinieerd door de eigenaar van een Azure-abonnement. Azure Monitor en Service Health gebruiken actiegroepen om gebruikers te waarschuwen dat er een waarschuwing is geactiveerd. Verschillende waarschuwingen kunnen dezelfde actiegroep of verschillende actiegroepen gebruiken, afhankelijk van de vereisten van de gebruiker. 

In dit artikel wordt beschreven hoe u actiegroepen maakt en beheert in de Azure Portal.

Elke actie bestaat uit de volgende eigenschappen:

* **Type:** de uitgevoerde melding of actie. Voorbeelden hiervan zijn het verzenden van een spraakoproep, sms, e-mail; of het activeren van verschillende typen geautomatiseerde acties. Zie typen verder in dit artikel.
* **Naam:** een unieke id binnen de actiegroep.
* **Details:** de bijbehorende details die per *type variëren.*

Zie Actiegroep voor het gebruik van Azure Resource Manager [Resource Manager sjablonen](./action-groups-create-resource-manager-template.md)voor het configureren van actiegroepen.

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Maak een actiegroep met behulp van de Azure Portal

1. Zoek en [Azure Portal](https://portal.azure.com)monitor in de **Azure Portal.** In **het deelvenster** Monitor worden al uw bewakingsinstellingen en -gegevens in één weergave samengevoegd.

1. Selecteer **Waarschuwingen** en selecteer vervolgens **Acties beheren.**

    ![Knop Acties beheren](./media/action-groups/manage-action-groups.png)
    
1. Selecteer **Actiegroep toevoegen** en vul de relevante velden in de wizard-ervaring in.

    ![De opdracht Actiegroep toevoegen](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Basisinstellingen voor actiegroep configureren

Onder **Projectdetails:**

Selecteer het **Abonnement** en **de Resourcegroep** waarin de actiegroep is opgeslagen.

Geef onder **Exemplaardetails** het volgende op:

1. Voer de **naam van een actiegroep in.**

1. Voer een **weergavenaam in.** De weergavenaam wordt gebruikt in plaats van een volledige naam van de actiegroep wanneer meldingen worden verzonden met behulp van deze groep.

      ![Het dialoogvenster Actiegroep toevoegen](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Meldingen configureren

1. Klik op **de** knop >meldingen om naar  het tabblad Meldingen  te gaan of selecteer het tabblad Meldingen bovenaan het scherm.

1. Definieer een lijst met meldingen die moeten worden verstuurd wanneer een waarschuwing wordt geactiveerd. Geef voor elke melding het volgende op:

    a. **Meldingstype:** selecteer het type melding dat u wilt verzenden. De beschikbare opties zijn:
      * E-Azure Resource Manager rol: verzend een e-mailbericht naar gebruikers die zijn toegewezen aan bepaalde ARM-rollen op abonnementsniveau.
      * E-mail/sms/push/spraak: verzend deze meldingstypen naar specifieke ontvangers.
    
    b. **Naam:** Voer een unieke naam in voor de melding.

    c. **Details:** voer op basis van het geselecteerde meldingstype een e-mailadres, telefoonnummer, enzovoort in.
    
    d. **Algemeen waarschuwingsschema:** u kunt [](./alerts-common-schema.md)ervoor kiezen om het algemene waarschuwingsschema in te Azure Monitor.

    ![Het tabblad Meldingen](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Acties configureren

1. Klik op de knop **Volgende: Acties >** om naar het tabblad **Acties** te gaan of selecteer het **tabblad** Acties bovenaan het scherm.

1. Definieer een lijst met acties die moeten worden geactiveerd wanneer een waarschuwing wordt geactiveerd. Geef voor elke actie het volgende op:

    a. **Actietype:** selecteer Automation Runbook, Azure Function, ITSM, Logic App, Secure Webhook, Webhook.
    
    b. **Naam:** voer een unieke naam in voor de actie.

    c. **Details:** Voer op basis van het actietype een webhook-URI, Azure-app, ITSM-verbinding of Automation-runbook in. Geef voor ITSM-actie ook **werkitem en** andere velden op die uw ITSM-hulpprogramma nodig heeft.
    
    d. **Algemeen waarschuwingsschema:** u kunt [](./alerts-common-schema.md)ervoor kiezen om het algemene waarschuwingsschema in te Azure Monitor.
    
    ![Het tabblad Acties](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>De actiegroep maken

1. U kunt indien gewenst de instellingen voor **tags** bekijken. Hiermee kunt u sleutel-waardeparen koppelen aan de actiegroep voor uw categorisatie. Dit is een functie die beschikbaar is voor elke Azure-resource.

    ![Het tabblad Tags](./media/action-groups/action-group-4-tags.png)
    
1. Klik op **Controleren + maken** om de instellingen te controleren. Hiermee wordt een snelle validatie van uw invoer uitgevoerd om ervoor te zorgen dat alle vereiste velden zijn geselecteerd. Als er problemen zijn, worden deze hier vermeld. Nadat u de instellingen hebt gecontroleerd, klikt u op **Maken om** de actiegroep in terichten.
    
    ![Het tabblad Beoordelen en maken](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Wanneer u een actie configureert om een persoon via e-mail of sms op de hoogte te stellen, ontvangt deze een bevestiging dat deze persoon is toegevoegd aan de actiegroep.

## <a name="manage-your-action-groups"></a>Uw actiegroepen beheren

Nadat u een actiegroep hebt gemaakt,  kunt u Actiegroepen **weergeven** door Acties beheren te selecteren op de **landingspagina** Waarschuwingen in **het deelvenster** Controle. Selecteer de actiegroep die u wilt beheren:

* Acties toevoegen, bewerken of verwijderen.
* Verwijder de actiegroep.

## <a name="action-specific-information"></a>Actiespecifieke informatie

> [!NOTE]
> Zie [Abonnementsservicelimieten voor bewaking](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) voor numerieke limieten voor elk van de onderstaande items.  

### <a name="automation-runbook"></a>Automation-runbook
Raadpleeg de [servicelimieten voor Azure-abonnementen](../../azure-resource-manager/management/azure-subscription-service-limits.md) voor limieten voor Runbook-nettoladingen.

Mogelijk hebt u een beperkt aantal runbookacties in een actiegroep. 

### <a name="azure-app-push-notifications"></a>Pushmeldingen voor Azure-apps
Schakel pushmeldingen naar de [Azure mobile app](https://azure.microsoft.com/features/azure-portal/mobile-app/) door het e-mailadres op te geven dat u als account-id gebruikt bij het configureren van Azure mobile app.

Mogelijk hebt u een beperkt aantal Acties voor Azure-apps in een actiegroep.

### <a name="email"></a>E-mail
E-mailberichten worden verzonden vanaf de volgende e-mailadressen. Zorg ervoor dat uw e-mailfiltering op de juiste wijze is geconfigureerd
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Mogelijk hebt u een beperkt aantal e-mailacties in een actiegroep. Zie het [artikel over snelheidsbeperking.](./alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>E-mailadres voor Azure Resource Manager-rol
Verzend een e-mail naar de leden van de rol van het abonnement. E-mail wordt alleen verzonden **naar Azure AD-gebruikersleden** van de rol. Er wordt geen e-mail verzonden naar Azure AD-groepen of service-principals.

Er wordt alleen een e-mailmelding verzonden naar *het primaire e-mailadres.*

Als u geen meldingen ontvangt voor uw primaire *e-mail,* kunt u de volgende stappen proberen:

1. Ga Azure Portal naar *Active Directory*.
2. Klik op Alle gebruikers (in het linkerdeelvenster). U ziet een lijst met gebruikers (in het rechterdeelvenster).
3. Selecteer de gebruiker waarvoor u de primaire e-mailgegevens *wilt* controleren.

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Voorbeeld van het controleren van een gebruikersprofiel." border="true":::

4. In Gebruikersprofiel onder Contactgegevens als het tabblad E-mail leeg is,  klikt u  bovenaan op de knop Bewerken, voegt u uw primaire e-mail toe en klikt u bovenaan op de knop Opslaan. 

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Voorbeeld van het toevoegen van primaire e-mail." border="true":::

Mogelijk hebt u een beperkt aantal e-mailacties in een actiegroep. Zie het [artikel over snelheidsbeperking.](./alerts-rate-limiting.md)

Tijdens het instellen van *de ARM-e-mailrol* moet u ervoor zorgen dat aan de onderstaande drie voorwaarden wordt voldaan:

1. Het type entiteit dat aan de rol wordt toegewezen, moet **'Gebruiker' zijn.**
2. De toewijzing moet worden uitgevoerd op **abonnementsniveau.**
3. De gebruiker moet een e-mail hebben geconfigureerd in het **AAD-profiel.** 


### <a name="function"></a>Functie
Roept een bestaand HTTP-trigger-eindpunt aan in [Azure Functions](../../azure-functions/functions-get-started.md). Als u een aanvraag wilt verwerken, moet uw eindpunt de HTTP POST-woorden verwerken.

Mogelijk hebt u een beperkt aantal functieacties in een actiegroep.

### <a name="itsm"></a>ITSM
ItsM Action vereist een ITSM-verbinding. Meer informatie over het maken van een [ITSM-verbinding.](./itsmc-overview.md)

Mogelijk hebt u een beperkt aantal ITSM-acties in een actiegroep. 

### <a name="logic-app"></a>Logische apps
Mogelijk hebt u een beperkt aantal acties voor logische apps in een actiegroep.

### <a name="secure-webhook"></a>Beveiligde webhook
Met de actie Beveiligde webhook voor actiegroepen kunt u profiteren van Azure Active Directory om de verbinding tussen uw actiegroep en uw beveiligde web-API (webhook-eindpunt) te beveiligen. De algemene werkstroom voor het gebruik van deze functionaliteit wordt hieronder beschreven. Zie Overzicht van Microsoft [Identity Platform (v2.0)](../../active-directory/develop/v2-overview.md)voor een overzicht van Azure AD-toepassingen en service-principals.

> [!NOTE]
> Voor het gebruik van de webhookactie is vereist dat het doelwebhook-eindpunt geen details van de waarschuwing nodig heeft om te kunnen functioneren, of dat het de contextinformatie van de waarschuwing kan parseren die is opgegeven als onderdeel van de POST-bewerking. Als het webhook-eindpunt de informatie over de waarschuwingscontext niet zelf [](./action-groups-logic-app.md) kan verwerken, kunt u een oplossing zoals een logische app-actie gebruiken voor een aangepaste manipulatie van de contextinformatie van de waarschuwing, zodat deze overeenkomen met de verwachte gegevensindeling van de webhook.

1. Maak een Azure AD-toepassing voor uw beveiligde web-API. Zie [Beveiligde web-API: App-registratie.](../../active-directory/develop/scenario-protected-web-api-app-registration.md)
    - Configureer uw beveiligde API om te [worden aangeroepen door een daemon-app](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Schakel Actiegroepen in om uw Azure AD-toepassing te gebruiken.

    > [!NOTE]
    > U moet lid zijn van de rol [Azure AD-toepassingsbeheerder om](../../active-directory/roles/permissions-reference.md#all-roles) dit script uit te voeren.
    
    - Wijzig de aanroep van het PowerShell-script Connect-AzureAD om uw Azure AD-tenant-id te gebruiken.
    - Wijzig de variabele van het PowerShell-script $myAzureADApplicationObjectId om de object-id van uw Azure AD-toepassing te gebruiken.
    - Voer het gewijzigde script uit.
    
3. Configureer de actie Beveiligde webhook voor actiegroep.
    - Kopieer de waarde $myApp.ObjectId uit het script en voer deze in het veld Toepassingsobject-id in de actiedefinitie Webhook in.
    
    ![Actie Webhook beveiligen](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>PowerShell-script voor beveiligde webhook

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
Zie informatie over [snelheidsbeperking en](./alerts-rate-limiting.md) gedrag [van sms-waarschuwingen](./alerts-sms-behavior.md) voor aanvullende belangrijke informatie. 

Mogelijk hebt u een beperkt aantal sms-acties in een actiegroep.

> [!NOTE]
> Als u Azure Portal de gebruikersinterface van de actiegroep niet uw land-/regiocode kunt selecteren, wordt sms niet ondersteund voor uw land/regio.  Als uw land-/regiocode niet beschikbaar is, kunt u stemmen om uw land/regio toe te voegen op [user voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). In de tussentijd kunt u uw actiegroep een webhook laten aanroepen naar een sms-provider van derden met ondersteuning in uw land/regio.  

Prijzen voor ondersteunde landen/regio's worden vermeld op Azure Monitor [pagina met prijzen.](https://azure.microsoft.com/pricing/details/monitor/)

**Lijst met landen waar sms-meldingen worden ondersteund**

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
Zie het [artikel rate limiting information (Informatie](./alerts-rate-limiting.md) over snelheidsbeperking) voor meer belangrijk gedrag.

Mogelijk hebt u een beperkt aantal spraakacties in een actiegroep.

> [!NOTE]
> Als u Azure Portal van de actiegroep niet uw land-/regiocode kunt selecteren, worden spraakoproepen niet ondersteund voor uw land/regio. Als uw land-/regiocode niet beschikbaar is, kunt u stemmen om uw land/regio toe te voegen via [user voice.](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)  In de tussentijd kunt u uw actiegroep een webhook laten aanroepen naar een externe provider voor spraakoproepen met ondersteuning in uw land/regio.  
> De landcode die momenteel wordt ondersteund in Azure Portal voor spraakmeldingen is +1 (Verenigde Staten). 

Prijzen voor ondersteunde landen/regio's worden vermeld op Azure Monitor [pagina met prijzen.](https://azure.microsoft.com/pricing/details/monitor/)

### <a name="webhook"></a>Webhook

> [!NOTE]
> Voor het gebruik van de webhookactie is vereist dat het doelwebhook-eindpunt geen details van de waarschuwing nodig heeft om te kunnen functioneren, of dat het de contextinformatie van de waarschuwing kan parseren die is opgegeven als onderdeel van de POST-bewerking. Als het webhook-eindpunt de informatie over de waarschuwingscontext niet zelf [](./action-groups-logic-app.md) kan verwerken, kunt u een oplossing zoals een logische app-actie gebruiken voor een aangepaste manipulatie van de contextinformatie van de waarschuwing, zodat deze overeenkomen met de verwachte gegevensindeling van de webhook.

Webhooks worden verwerkt met behulp van de volgende regels
- Een webhook-aanroep wordt maximaal drie keer geprobeerd.
- De aanroep wordt opnieuw proberen als er geen antwoord wordt ontvangen binnen de time-outperiode of als een van de volgende HTTP-statuscodes wordt geretourneerd: 408, 429, 503 of 504.
- De eerste aanroep wacht 10 seconden op een antwoord.
- De tweede en derde poging wachten 30 seconden op een antwoord.
- Nadat de drie pogingen om de webhook aan te roepen zijn mislukt, roept een actiegroep het eindpunt 15 minuten aan.

Zie [IP-adressen van actiegroep voor](../app/ip-addresses.md) bron-IP-adresbereiken.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gedrag van sms-waarschuwingen.](./alerts-sms-behavior.md)  
* Krijg inzicht [in het webhookschema voor waarschuwingen voor activiteitenlogboek.](./activity-log-alerts-webhook.md)  
* Meer informatie over [ITSM-connector](./itsmc-overview.md).
* Meer informatie over [snelheidsbeperking](./alerts-rate-limiting.md) voor waarschuwingen.
* Een [overzicht van waarschuwingen voor het activiteitenlogboek](./alerts-overview.md) en meer informatie over het ontvangen van waarschuwingen.  
* Meer informatie over het [configureren van waarschuwingen wanneer er een service health-melding wordt geplaatst.](../../service-health/alerts-activity-log-service-notifications-portal.md)
