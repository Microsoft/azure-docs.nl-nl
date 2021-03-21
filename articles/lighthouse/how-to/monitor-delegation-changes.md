---
title: Overdrachts wijzigingen in uw beheer Tenant bewaken
description: Meer informatie over het bewaken van overdrachts activiteiten van klant tenants naar uw beheer Tenant.
ms.date: 02/18/2021
ms.topic: how-to
ms.openlocfilehash: 8bd9e89039c114f3d1088df44198fe00c69bbf82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199063"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Overdrachts wijzigingen in uw beheer Tenant bewaken

Als service provider wilt u mogelijk weten wanneer klanten abonnementen of resource groepen worden gedelegeerd aan uw Tenant via [Azure Lighthouse](../overview.md)of wanneer eerder gedelegeerde resources worden verwijderd.

In het [Azure-activiteiten logboek](../../azure-monitor/essentials/platform-logs-overview.md) wordt in de Tenant beheren de activiteit overdracht op Tenant niveau bijgehouden. Deze geregistreerde activiteit bevat eventuele toegevoegde of verwijderde delegaties van de tenants van de klant.

In dit onderwerp worden de machtigingen beschreven die nodig zijn voor het bewaken van overdrachts activiteiten aan uw Tenant (in al uw klanten). Het bevat ook een voorbeeld script met één methode voor het uitvoeren van query's en rapportage over deze gegevens.

> [!IMPORTANT]
> Al deze stappen moeten worden uitgevoerd in uw beheer Tenant in plaats van in de tenants van een klant.
>
> Hoewel we in dit onderwerp naar service providers en klanten verwijzen, kunnen [bedrijven die meerdere tenants beheren](../concepts/enterprise.md) , dezelfde processen gebruiken.

## <a name="enable-access-to-tenant-level-data"></a>Toegang tot gegevens op Tenant niveau inschakelen

Voor toegang tot activiteiten logboek gegevens op Tenant niveau moet aan een account de ingebouwde rol van de [bewakings lezer](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure zijn toegewezen in het hoofd bereik (/). Deze toewijzing moet worden uitgevoerd door een gebruiker met de rol globale beheerder met extra verhoogde toegang.

### <a name="elevate-access-for-a-global-administrator-account"></a>Toegang tot een globaal beheerders account verhogen

Als u een rol wilt toewijzen in het hoofd bereik (/), moet u de rol globale beheerder hebben met verhoogde toegang. Deze verhoogde toegang moet alleen worden toegevoegd wanneer u de roltoewijzing moet maken en vervolgens kunt verwijderen wanneer u klaar bent.

Zie [toegang uitbreiden voor het beheer van alle Azure-abonnementen en-beheer groepen](../../role-based-access-control/elevate-access-global-admin.md)voor gedetailleerde instructies voor het toevoegen en verwijderen van uitbrei ding van bevoegdheden.

Nadat u de toegang hebt verhoogd, heeft uw account de rol beheerder voor gebruikers toegang in Azure in het hoofd bereik. Met deze roltoewijzing kunt u alle resources weer geven en toegang toewijzen in een abonnement of beheer groep in de map, en kunt u roltoewijzingen maken op basis van het hoofd bereik.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>De rol bewakings lezer aan het hoofd bereik toewijzen

Zodra u uw toegang hebt uitgebreid, kunt u de juiste machtigingen toewijzen aan een account, zodat de gegevens van het activiteiten logboek op Tenant niveau kunnen worden opgevraagd. Dit account moet beschikken over de ingebouwde rol van de [bewakings lezer](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure die is toegewezen aan het hoofd bereik van uw beheer Tenant.

> [!IMPORTANT]
> Het verlenen van een roltoewijzing in het hoofd bereik houdt in dat dezelfde machtigingen van toepassing zijn op elke resource in de Tenant. Omdat dit een breed toegangs niveau is, kunt u [deze rol toewijzen aan een Service-Principal-account en dat account gebruiken om gegevens op te vragen](#use-a-service-principal-account-to-query-the-activity-log). U kunt de rol van bewakings lezer ook toewijzen aan het hoofd bereik voor afzonderlijke gebruikers of aan gebruikers groepen, zodat deze [gegevens over delegering rechtstreeks kunnen weer geven in de Azure Portal](#view-delegation-changes-in-the-azure-portal). Als u dit doet, moet u er rekening mee houden dat dit een breed toegangs niveau is dat moet worden beperkt tot het minste aantal gebruikers dat mogelijk is.

Gebruik een van de volgende methoden om de toewijzing van het basis bereik te maken.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ObjectId <objectId> 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Verhoogde toegang voor het account van de globale beheerder verwijderen

Nadat u de rol bewakings lezer aan het hoofd bereik hebt toegewezen, moet u ervoor zorgen dat u [de verhoogde toegang](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) voor het account van de globale beheerder verwijdert, omdat dit toegangs niveau niet langer nodig is.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Wijzigingen in de delegatie weer geven in de Azure Portal

Gebruikers aan wie de rol van bewakings lezer in het hoofd bereik is toegewezen, kunnen wijzigingen in de overdracht rechtstreeks in het Azure Portal weer geven.

1. Ga naar de pagina **mijn klanten** en selecteer vervolgens **activiteiten logboek** in het navigatie menu aan de linkerkant.
1. Zorg ervoor dat **Directory-activiteit** is geselecteerd in het filter aan de bovenkant van het scherm.

Er wordt een lijst met overdrachts wijzigingen weer gegeven. U kunt **kolommen bewerken** selecteren om de **status**, **gebeurtenis categorie**, **tijd**, **tijds tempel**, **abonnement** en gebeurtenis te tonen of te verbergen, **geïnitieerd door**, **resource groep**, **resource type** en **resource** waarden.

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Scherm opname van overdrachts wijzigingen in de Azure Portal.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Een Service-Principal-account gebruiken om een query uit te zoeken in het activiteiten logboek

Omdat de rol bewakings lezer in hoofd bereik een dergelijk breed toegangs niveau is, is het raadzaam om de rol toe te wijzen aan een Service-Principal-account en dat account te gebruiken om gegevens op te vragen met behulp van het onderstaande script.

> [!IMPORTANT]
> Op dit moment kunnen tenants met een grote hoeveelheid overdrachts activiteit problemen ondervinden bij het uitvoeren van query's op deze gegevens.

Wanneer u een Service-Principal-account gebruikt om een query uit te voeren op het activiteiten logboek, raden we u aan de volgende aanbevolen procedures te volgen:

- [Maak een nieuw Service-Principal-account](../../active-directory/develop/howto-create-service-principal-portal.md) dat alleen voor deze functie wordt gebruikt, in plaats van deze rol toe te wijzen aan een bestaande service-principal die wordt gebruikt voor andere Automation.
- Zorg ervoor dat deze service-principal geen toegang heeft tot de resources van een gedelegeerde klant.
- [Gebruik een certificaat om het te verifiëren](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) en [veilig op te slaan in azure Key Vault](../../key-vault/general/security-overview.md).
- Beperk de gebruikers die toegang hebben tot namens de Service-Principal.

Wanneer u een nieuw Service-Principal-account hebt gemaakt met behulp van de toegang tot het hoofd bereik van de beheer-Tenant, kunt u deze gebruiken voor het opvragen en rapporteren van overdrachts activiteiten in uw Tenant.

[Dit Azure PowerShell script](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) kan worden gebruikt om een query uit te proberen voor de afgelopen 1 dag van de activiteit en rapporten van toegevoegde of verwijderde delegaties (of pogingen die niet zijn gelukt). Het zoekt naar de logboek gegevens van de [Tenant activiteit](/rest/api/monitor/TenantActivityLogs/List) en bouwt vervolgens de volgende waarden op om te rapporteren over delegaties die worden toegevoegd of verwijderd:

- **DelegatedResourceId**: de id van het gedelegeerde abonnement of de resource groep
- **CustomerTenantId**: de Tenant-id van de klant
- **CustomerSubscriptionId**: de abonnements-id die is gedelegeerd of die de resource groep bevat die is gedelegeerd
- **CustomerDelegationStatus**: de status wijziging voor de gedelegeerde resource (geslaagd of mislukt)
- **EventTimeStamp**: de datum en tijd waarop de overdrachts wijziging is geregistreerd

Houd bij het uitvoeren van query's op deze gegevens de volgende informatie:

- Als er meerdere resource groepen in één implementatie worden gedelegeerd, worden afzonderlijke vermeldingen geretourneerd voor elke resource groep.
- Wijzigingen die zijn aangebracht in een eerdere delegering (zoals het bijwerken van de machtigings structuur) worden geregistreerd als een extra delegering.
- Zoals hierboven vermeld, moet een account beschikken over de ingebouwde rol van de bewakings lezer Azure in het hoofd bereik (/) om toegang te kunnen krijgen tot deze gegevens op Tenant niveau.
- U kunt deze gegevens gebruiken in uw eigen werk stromen en rapportage. U kunt bijvoorbeeld de [http data collector API (open bare preview)](../../azure-monitor/logs/data-collector-api.md) gebruiken om gegevens te registreren voor Azure monitor van een rest API-client en vervolgens [actie groepen](../../azure-monitor/alerts/action-groups.md) gebruiken om meldingen of waarschuwingen te maken.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het voorbereiden van klanten op [Azure Lighthouse](../concepts/azure-delegated-resource-management.md).
- Meer informatie over [Azure monitor](../../azure-monitor/index.yml) en het [Azure-activiteiten logboek](../../azure-monitor/essentials/platform-logs-overview.md).
- Raadpleeg de voorbeeld werkmap [activiteiten logboeken per domein](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) voor meer informatie over het weer geven van Azure-activiteiten logboeken tussen abonnementen met een optie om deze te filteren op domein naam.
