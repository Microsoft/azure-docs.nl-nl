---
title: Delegeringswijzigingen in uw beherende tenant bewaken
description: Meer informatie over het bewaken van delegeringsactiviteiten van tenants van klanten naar uw beherende tenant.
ms.date: 02/18/2021
ms.topic: how-to
ms.openlocfilehash: 1a12b916fae9794d6d695191a81ec076917bda31
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814888"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Delegeringswijzigingen in uw beherende tenant bewaken

Als serviceprovider wilt u mogelijk weten wanneer klantabonnementen of resourcegroepen aan uw tenant worden gedelegeerd via [Azure Lighthouse of](../overview.md)wanneer eerder gedelegeerde resources worden verwijderd.

In de beherende tenant houdt het [Azure-activiteitenlogboek](../../azure-monitor/essentials/platform-logs-overview.md) de delegeringsactiviteiten bij op tenantniveau. Deze vastgelegde activiteit omvat alle toegevoegde of verwijderde delegaties van tenants van klanten.

In dit onderwerp wordt uitgelegd wat de machtigingen zijn die nodig zijn voor het bewaken van de delegeringsactiviteiten naar uw tenant (voor al uw klanten). Het bevat ook een voorbeeldscript met één methode voor het uitvoeren van query's en rapportage over deze gegevens.

> [!IMPORTANT]
> Al deze stappen moeten worden uitgevoerd in uw beherende tenant in plaats van in tenants van klanten.
>
> Hoewel we in dit onderwerp verwijzen naar serviceproviders en klanten, kunnen ondernemingen [die meerdere tenants](../concepts/enterprise.md) beheren dezelfde processen gebruiken.

## <a name="enable-access-to-tenant-level-data"></a>Toegang tot gegevens op tenantniveau inschakelen

Als u toegang wilt krijgen tot activiteitenlogboekgegevens [](../../role-based-access-control/built-in-roles.md#monitoring-reader) op tenantniveau, moet aan een account de ingebouwde Azure-rol Bewakingslezer zijn toegewezen voor het hoofdbereik (/). Deze toewijzing moet worden uitgevoerd door een gebruiker met de rol Globale beheerder met extra verhoogde toegang.

### <a name="elevate-access-for-a-global-administrator-account"></a>Toegang verhogen voor een globale beheerdersaccount

Als u een rol wilt toewijzen in het hoofdbereik (/), moet u de rol Globale beheerder met verhoogde toegang hebben. Deze verhoogde toegang moet alleen worden toegevoegd wanneer u de roltoewijzing moet maken en vervolgens moet worden verwijderd wanneer u klaar bent.

Zie Toegang verhogen voor het beheren van alle Azure-abonnementen en [-beheergroepen](../../role-based-access-control/elevate-access-global-admin.md)voor gedetailleerde instructies over het toevoegen en verwijderen van verhoogde bevoegdheden.

Nadat u uw toegangsbereik hebt verhoogd, heeft uw account de rol Gebruikerstoegangbeheerder in Azure in het hoofdbereik. Met deze roltoewijzing kunt u alle resources weergeven en toegang toewijzen in elk abonnement of elke beheergroep in de directory, en roltoewijzingen maken in het hoofdbereik.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>De rol Lezer voor bewaking toewijzen in hoofdbereik

Zodra u uw toegangsrechten hebt verhoogd, kunt u de juiste machtigingen toewijzen aan een account, zodat er query's kunnen worden uitgevoerd op activiteitenlogboekgegevens op tenantniveau. Aan dit account moet [](../../role-based-access-control/built-in-roles.md#monitoring-reader) de ingebouwde Azure-rol Lezer voor bewaking zijn toegewezen in het hoofdbereik van uw beheer-tenant.

> [!IMPORTANT]
> Het verlenen van een roltoewijzing in het hoofdbereik betekent dat dezelfde machtigingen van toepassing zijn op elke resource in de tenant. Omdat dit een breed toegangsniveau is, kunt u deze rol toewijzen aan een [service-principal-account](#use-a-service-principal-account-to-query-the-activity-log)en dat account gebruiken om gegevens op te vragen. U kunt ook de rol Lezer voor bewaking in hoofdbereik toewijzen aan afzonderlijke gebruikers of aan gebruikersgroepen, zodat ze delegeringsinformatie rechtstreeks [in](#view-delegation-changes-in-the-azure-portal)de Azure Portal. Als u dit doet, moet u er rekening mee houden dat dit een breed toegangsniveau is dat moet worden beperkt tot zo weinig mogelijk gebruikers.

Gebruik een van de volgende methoden om de toewijzing van het hoofdbereik te maken.

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

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Verhoogde toegang verwijderen voor het globale beheerdersaccount

Nadat u de rol Lezer voor bewaking in het hoofdbereik [](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) hebt toegewezen aan het gewenste account, moet u de verhoogde toegang voor het globale beheerdersaccount verwijderen, omdat dit toegangsniveau niet meer nodig is.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Delegeringswijzigingen in de Azure Portal

Gebruikers aan wie de rol Lezer voor bewaking in het hoofdbereik is toegewezen, kunnen overdrachtswijzigingen rechtstreeks in de Azure Portal.

1. Navigeer **naar de pagina** Mijn klanten en selecteer **activiteitenlogboek** in het navigatiemenu aan de linkerkant.
1. Zorg ervoor **dat Mapactiviteit** is geselecteerd in het filter bovenaan het scherm.

Er wordt een lijst met overdrachtswijzigingen weergegeven. U kunt Kolommen **bewerken** selecteren om de waarden **Status,** Gebeurteniscategorie,  **Tijd,** Tijdstempel,  **Abonnement,** Gebeurtenis **geïnitieerd** door , **Resourcegroep,** **Resourcetype** en **Resourcewaarden** weer te geven of te verbergen.

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Schermopname van wijzigingen in de delegering in Azure Portal.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Een service-principalaccount gebruiken om een query uit te voeren op het activiteitenlogboek

Omdat de rol Lezer voor bewaking in het hoofdbereik zo'n breed toegangsniveau is, kunt u de rol toewijzen aan een service-principalaccount en dat account gebruiken om query's uit te voeren op gegevens met behulp van het onderstaande script.

> [!IMPORTANT]
> Op dit moment kunnen tenants met een grote hoeveelheid delegeringsactiviteit fouten krijgen bij het uitvoeren van query's op deze gegevens.

Wanneer u een service-principalaccount gebruikt om een query uit te voeren op het activiteitenlogboek, raden we de volgende best practices aan:

- [Maak een nieuw service-principal-account](../../active-directory/develop/howto-create-service-principal-portal.md) dat alleen voor deze functie moet worden gebruikt, in plaats van deze rol toe te wijzen aan een bestaande service-principal die wordt gebruikt voor andere automatisering.
- Zorg ervoor dat deze service-principal geen toegang heeft tot gedelegeerde klantbronnen.
- [Gebruik een certificaat om het veilig](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) te verifiëren en op te slaan in [Azure Key Vault](../../key-vault/general/security-features.md).
- Beperk de gebruikers die toegang hebben om te handelen namens de service-principal.

Zodra u een nieuw service-principal-account hebt gemaakt met controlelezertoegang tot het hoofdbereik van uw beherende tenant, kunt u dit gebruiken om query's uit te voeren op de delegeringsactiviteiten in uw tenant en deze te rapporteren.

[Dit Azure PowerShell script](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) kan worden gebruikt om een query uit te voeren op de afgelopen 1 dag van de activiteit en rapporten over toegevoegde of verwijderde delegaties (of pogingen die niet zijn geslaagd). Er wordt een query uitgevoerd op de [gegevens van het activiteitenlogboek](/rest/api/monitor/TenantActivityLogs/List) van de tenant en vervolgens worden de volgende waarden samengesteld om te rapporteren over delegaties die worden toegevoegd of verwijderd:

- **DelegatedResourceId:** de id van het gedelegeerde abonnement of de resourcegroep
- **CustomerTenantId:** de tenant-id van de klant
- **CustomerSubscriptionId:** de abonnements-id die is gedelegeerd of die de resourcegroep bevat die is gedelegeerd
- **CustomerDelegationStatus:** de statuswijziging voor de gedelegeerde resource (geslaagd of mislukt)
- **EventTimeStamp:** de datum en tijd waarop de overdrachtswijziging is geregistreerd

Houd bij het uitvoeren van query's op deze gegevens rekening met het volgende:

- Als meerdere resourcegroepen worden gedelegeerd in één implementatie, worden afzonderlijke vermeldingen geretourneerd voor elke resourcegroep.
- Wijzigingen die zijn aangebracht in een vorige delegering (zoals het bijwerken van de machtigingsstructuur) worden geregistreerd als een toegevoegde delegatie.
- Zoals hierboven vermeld, moet een account de ingebouwde Azure-rol Lezer voor bewaking hebben in het hoofdbereik (/) om toegang te krijgen tot deze gegevens op tenantniveau.
- U kunt deze gegevens gebruiken in uw eigen werkstromen en rapportage. U kunt bijvoorbeeld de [HTTP Data Collector-API (openbare preview)](../../azure-monitor/logs/data-collector-api.md) gebruiken om gegevens vanuit een [](../../azure-monitor/alerts/action-groups.md) REST API-client te Azure Monitor en vervolgens actiegroepen gebruiken om meldingen of waarschuwingen te maken.

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

- Meer informatie over het onboarden van [klanten Azure Lighthouse.](../concepts/azure-delegated-resource-management.md)
- Meer informatie over [Azure Monitor](../../azure-monitor/index.yml) en het [Azure-activiteitenlogboek](../../azure-monitor/essentials/platform-logs-overview.md).
- Bekijk de [voorbeeldwerkbook Activiteitenlogboeken](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) per domein voor meer informatie over het weergeven van Azure-activiteitenlogboeken in abonnementen met een optie om ze te filteren op domeinnaam.
