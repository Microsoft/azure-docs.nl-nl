---
title: Nalevingsgegevens voor beleid op halen
description: Azure Policy evaluaties en effecten bepalen de naleving. Meer informatie over hoe u de nalevingsdetails van uw Azure-resources op kunt halen.
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: e1a9a7fcbbcbd7f490b2f665b40c7ed922ec61ee
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864591"
---
# <a name="get-compliance-data-of-azure-resources"></a>Nalevingsgegevens van Azure-resources op halen

Een van de grootste voordelen van Azure Policy is het inzicht en de besturingselementen die het biedt over resources in een abonnement of [beheergroep](../../management-groups/overview.md) van abonnementen. Dit besturingselement kan op veel verschillende manieren worden uitgevoerd, zoals voorkomen dat resources op de verkeerde locatie worden gemaakt, gemeenschappelijke en consistente taggebruik afdwingen of bestaande resources controleren voor de juiste configuraties en instellingen. In alle gevallen worden gegevens gegenereerd door Azure Policy zodat u inzicht hebt in de nalevingstoestand van uw omgeving.

Er zijn verschillende manieren om toegang te krijgen tot de nalevingsinformatie die wordt gegenereerd door uw beleid en initiatieftoewijzingen:

- De [Azure Portal](#portal)
- Via [opdrachtregelscripts](#command-line)

Voordat we kijken naar de methoden om te rapporteren over naleving, kijken we wanneer compatibiliteitsinformatie wordt bijgewerkt en wat de frequentie en gebeurtenissen zijn die een evaluatiecyclus activeren.

> [!WARNING]
> Als de nalevingsstaat wordt gerapporteerd als Niet **geregistreerd,** controleert u of de **resourceprovider Microsoft.PolicyInsights** is geregistreerd en of de gebruiker over de juiste Azure RBAC-machtigingen (op rollen gebaseerd toegangsbeheer) beschikt, zoals beschreven [in Azure RBAC-machtigingen in Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Evaluatietriggers

De resultaten van een voltooide evaluatiecyclus zijn beschikbaar in de `Microsoft.PolicyInsights` resourceprovider via `PolicyStates` - en `PolicyEvents` -bewerkingen. Zie Inzichten voor meer informatie over de bewerkingen Azure Policy Insights REST API [Azure Policy.](/rest/api/policy/)

Evaluaties van toegewezen beleidsregels en initiatieven vinden plaats als gevolg van verschillende gebeurtenissen:

- Een beleid of initiatief wordt voor het eerst toegewezen aan een bereik. Het duurt ongeveer 30 minuten voordat de toewijzing is toegepast op het gedefinieerde bereik. Zodra de evaluatiecyclus is toegepast, begint de evaluatiecyclus voor resources binnen dat bereik op basis van het nieuw toegewezen beleid of initiatief. Afhankelijk van de effecten die door het beleid of initiatief worden gebruikt, worden resources gemarkeerd als compatibel, niet-compatibel of uitgesloten. Een groot beleid of initiatief dat wordt geëvalueerd op basis van een groot aantal resources kan tijd in nemen. Daarom is er geen vooraf gedefinieerde verwachting van wanneer de evaluatiecyclus is voltooid. Zodra dit is voltooid, zijn bijgewerkte nalevingsresultaten beschikbaar in de portal en SDK's.

- Een beleid of initiatief dat al is toegewezen aan een bereik, wordt bijgewerkt. De evaluatiecyclus en timing voor dit scenario zijn hetzelfde als voor een nieuwe toewijzing aan een bereik.

- Een resource wordt geïmplementeerd of bijgewerkt binnen een bereik met een toewijzing via Azure Resource Manager, REST API of een ondersteunde SDK. In dit scenario komen de effectgebeurtenis (append, audit, deny, deploy) en compatibele statusinformatie voor de afzonderlijke resource beschikbaar in de portal en SDK's ongeveer 15 minuten later. Deze gebeurtenis veroorzaakt geen evaluatie van andere resources.

- Een abonnement (resourcetype ) wordt gemaakt of verplaatst binnen een hiërarchie van beheergroep met een toegewezen beleidsdefinitie die is gericht `Microsoft.Resource/subscriptions` op het resourcetype van het abonnement. [](../../management-groups/overview.md) De evaluatie van de ondersteunde effecten van het abonnement (audit, auditIfNotExist, deployIfNotExists, modify), logboekregistratie en herstelacties duurt ongeveer 30 minuten.

- Er [wordt een](../concepts/exemption-structure.md) beleidsvrijstelling gemaakt, bijgewerkt of verwijderd. In dit scenario wordt de bijbehorende toewijzing geëvalueerd voor het gedefinieerde uitzonderingsbereik.

- Standaardevaluatiecyclus voor naleving. Elke 24 uur worden toewijzingen automatisch opnieuw geëvalueerd. Een groot beleid of initiatief van veel resources kan tijd in beslag nemen, dus er is geen vooraf gedefinieerde verwachting van wanneer de evaluatiecyclus is voltooid. Zodra dit is voltooid, zijn bijgewerkte nalevingsresultaten beschikbaar in de portal en SDK's.

- De [resourceprovider voor](../concepts/guest-configuration.md) gastconfiguratie wordt bijgewerkt met nalevingsgegevens door een beheerde resource.

- Scan op aanvraag

### <a name="on-demand-evaluation-scan"></a>Evaluatiescan op aanvraag

Een evaluatiescan voor een abonnement of resourcegroep kan worden gestart met Azure CLI, Azure PowerShell, een aanroep van de REST API of met behulp van de [GitHub-actie Azure Policy Compliance Scan.](https://github.com/marketplace/actions/azure-policy-compliance-scan)
Deze scan is een asynchroon proces.

#### <a name="on-demand-evaluation-scan---github-action"></a>Evaluatiescan op aanvraag - GitHub Action

Gebruik de [Azure Policy Compliance Scan-actie](https://github.com/marketplace/actions/azure-policy-compliance-scan) om een evaluatiescan op aanvraag te activeren vanuit uw [GitHub-werkstroom](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) op een of meer resources, resourcegroepen of abonnementen, en de werkstroom te gateen op basis van de nalevingsstatus van resources. U kunt de werkstroom ook zo configureren dat deze op een gepland tijdstip wordt uitgevoerd, zodat u de meest recente nalevingsstatus op een handig moment krijgt. Optioneel kan met deze GitHub-actie een rapport worden gegenereerd over de nalevingstoestand van gescande resources voor verdere analyse of voor archivering.

In het volgende voorbeeld wordt een nalevingsscan voor een abonnement uitgevoerd. 

```yaml
on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Zie de GitHub Action for Azure Policy Compliance Scan-opslagplaats voor meer informatie [en voorbeelden van werkstromen.](https://github.com/Azure/policy-compliance-scan)

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Evaluatiescan op aanvraag - Azure CLI

De nalevingsscan wordt gestart met [de opdracht az policy state trigger-scan.](/cli/azure/policy/state#az_policy_state_trigger_scan)

Standaard wordt `az policy state trigger-scan` een evaluatie gestart voor alle resources in het huidige abonnement. Als u een evaluatie wilt starten voor een specifieke resourcegroep, gebruikt u **de parameter resourcegroep.** In het volgende voorbeeld wordt een nalevingsscan gestart in het huidige abonnement voor de _resourcegroep MyRG:_

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

U kunt ervoor kiezen niet te wachten tot het asynchrone proces is voltooid voordat u doorgaat met de parameter **no-wait.**

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Evaluatiescan op aanvraag - Azure PowerShell

De nalevingsscan wordt gestart met de cmdlet [Start-AzPolicyComplianceScan.](/powershell/module/az.policyinsights/start-azpolicycompliancescan)

Start standaard `Start-AzPolicyComplianceScan` een evaluatie voor alle resources in het huidige abonnement. Als u een evaluatie wilt starten voor een specifieke resourcegroep, gebruikt u de parameter **ResourceGroupName.** In het volgende voorbeeld wordt een nalevingsscan gestart in het huidige abonnement voor de _resourcegroep MyRG:_

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

U kunt PowerShell laten wachten tot de asynchrone aanroep is voltooid voordat de resultaten worden uitgevoerd of deze op de achtergrond laten uitvoeren als [taak](/powershell/module/microsoft.powershell.core/about/about_jobs). Als u een PowerShell-taak wilt gebruiken om de nalevingsscan op de achtergrond uit te voeren, gebruikt u de parameter **AsJob** en stelt u de waarde in op een -object, zoals `$job` in dit voorbeeld:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

U kunt de status van de taak controleren door het object te `$job` controleren. De taak is van het type `Microsoft.Azure.Commands.Common.AzureLongRunningJob` . Gebruik `Get-Member` op het `$job` -object om beschikbare eigenschappen en methoden te zien.

Terwijl de nalevingsscan wordt uitgevoerd, levert het `$job` controleren van het object resultaten op zoals deze:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Wanneer de nalevingsscan is voltooid, verandert de **eigenschap Status** in _Voltooid._

#### <a name="on-demand-evaluation-scan---rest"></a>Evaluatiescan op aanvraag - REST

Als een asynchroon proces wacht het REST-eindpunt om de scan te starten niet totdat de scan is voltooid om te reageren. In plaats daarvan biedt het een URI om de status van de aangevraagde evaluatie op te vragen.

In elke REST API-URI zijn er verschillende variabelen die worden gebruikt en die u moet vervangen door uw eigen waarden:

- `{YourRG}` - Vervang door de naam van uw resourcegroep
- Vervang `{subscriptionId}` door uw abonnements-ID

De scan ondersteunt de evaluatie van resources in een abonnement of in een resourcegroep. Start een scan op bereik met een REST API **POST-opdracht** met behulp van de volgende URI-structuren:

- Abonnement

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Resourcegroep

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

De aanroep retourneert **de status 202 Geaccepteerd.** Opgenomen in de antwoordheader is een **eigenschap Location** met de volgende indeling:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` wordt statisch gegenereerd voor het aangevraagde bereik. Als in een bereik al een scan op aanvraag wordt uitgevoerd, wordt er geen nieuwe scan gestart. In plaats daarvan krijgt de nieuwe aanvraag dezelfde `{ResourceContainerGUID}` **locatie-URI** voor de status. Een REST API **GET-opdracht** voor de **locatie-URI** retourneert een **202 Geaccepteerd** terwijl de evaluatie wordt lopende. Wanneer de evaluatiescan is voltooid, wordt de status **200 OK retourneert.** De body van een voltooide scan is een JSON-antwoord met de status:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Evaluatiescan op aanvraag: Visual Studio Code

Met Azure Policy-extensie voor Visual Studio code kan een evaluatiescan voor een specifieke resource worden uitgevoerd. Deze scan is een synchroon proces, in tegenstelling tot de Azure PowerShell en REST-methoden.
Zie Evaluatie op aanvraag met de [VS Code-extensie](./extension-for-vscode.md#on-demand-evaluation-scan)voor meer informatie en stappen.

## <a name="how-compliance-works"></a>Hoe naleving werkt

In een toewijzing is een resource **Niet-compatibel** als deze geen beleidsregels of initiatiefregels volgt en niet wordt _uitgesloten._ In de volgende tabel ziet u hoe verschillende beleidseffecten werken met de evaluatie van voorwaarden voor de resulterende nalevingstoestand:

| Resourcestatus | Effect | Beleidsevaluatie | Nalevingsstatus |
| --- | --- | --- | --- |
| Nieuw of bijgewerkt | Controleren, wijzigen, AuditIfNotExist | True | Niet-compatibel |
| Nieuw of bijgewerkt | Controleren, wijzigen, AuditIfNotExist | False | Compatibel |
| Bestaat | Weigeren, Controleren, Toevoegen, Wijzigen, DeployIfNotExist, AuditIfNotExist | True | Niet-compatibel |
| Bestaat | Weigeren, Controleren, Toevoegen, Wijzigen, DeployIfNotExist, AuditIfNotExist | False | Compatibel |

> [!NOTE]
> Voor de effecten DeployIfNotExist en AuditIfNotExist moet de IF-instructie TRUE en de bestaansvoorwaarde FALSE zijn om niet-compatibel te zijn. Indien TRUE, activeert de IF-voorwaarde de evaluatie van de bestaansvoorwaarde voor de gerelateerde resources.

Stel dat u een resourcegroep hebt: ContsoRG, met een aantal opslagaccounts (rood gemarkeerd) die worden blootgesteld aan openbare netwerken.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagram van opslagaccounts die zijn blootgesteld aan openbare netwerken in de resourcegroep Contoso R G." border="false":::
   Diagram met afbeeldingen voor vijf opslagaccounts in de resourcegroep Contoso R G.  Opslagaccounts één en drie zijn blauw, terwijl opslagaccounts twee, vier en vijf rood zijn.
:::image-end:::

In dit voorbeeld moet u op uw hoede zijn voor beveiligingsrisico's. Nu u een beleidstoewijzing hebt gemaakt, wordt deze geëvalueerd voor alle opgenomen en niet-uitgesloten opslagaccounts in de resourcegroep ContosoRG. De drie niet-compatibele opslagaccounts worden gecontroleerd, waardoor de staten worden veranderd in **Niet-compatibel.**

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagram van de naleving van opslagaccounts in de resourcegroep Contoso R G." border="false":::
   Diagram met afbeeldingen voor vijf opslagaccounts in de resourcegroep Contoso R G. Opslagaccounts één en drie hebben nu groene vinkjes eronder, terwijl opslagaccounts twee, vier en vijf nu rode waarschuwingssignalen eronder hebben.
:::image-end:::

Naast **Compatibel** en **Niet-compatibel** hebben beleidsregels en resources vier andere staten:

- **Uitzondering:** de resource valt binnen het bereik van een toewijzing, maar heeft een [gedefinieerde uitzondering](../concepts/exemption-structure.md).
- **Conflicterend:** er bestaan twee of meer beleidsdefinities met conflicterende regels. Twee definities toevoegen bijvoorbeeld dezelfde tag met verschillende waarden.
- **Niet gestart:** de evaluatiecyclus is niet gestart voor het beleid of de resource.
- **Niet geregistreerd:** de Azure Policy resourceprovider niet is geregistreerd of het account dat is aangemeld, heeft geen machtiging om nalevingsgegevens te lezen.

Azure Policy gebruikt het **type**, **naam** of **soort** velden in de definitie om te bepalen of een resource een overeenkomst is. Wanneer de resource overeenkomt, wordt deze als van toepassing beschouwd en heeft deze de status **Compatibel,** **Niet-compatibel** of **Vrijgesteld.** Als een **van de typen**, **name** of **kind** de enige eigenschap in de definitie is, worden alle opgenomen en niet-uitgesloten resources als van toepassing beschouwd en worden ze geëvalueerd.

Het nalevingspercentage wordt bepaald door **compatibele** en uitgesloten resources **te** delen door het totale _aantal resources._ _Het totale aantal resources_ wordt gedefinieerd als de som van de **compatibele**, **niet-compatibele,** **vrijgestelde** en **conflicterende** resources. De algemene nalevingsnummers zijn de  som van afzonderlijke resources die compatibel of vrijgesteld zijn **gedeeld** door de som van alle afzonderlijke resources. In de onderstaande afbeelding zijn 20 verschillende resources van toepassing en slechts één is **Niet-compatibel.**
De algehele resource-naleving is 95% (19 van de 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Schermopname van nalevingsdetails van het beleid op de pagina Naleving." border="false":::

> [!NOTE]
> Naleving van regelgeving in Azure Policy is een preview-functie. Nalevingseigenschappen van SDK en pagina's in de portal verschillen voor ingeschakelde initiatieven. Zie Naleving van regelgeving voor [meer informatie](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portal

De Azure Portal laat een grafische ervaring zien van het visualiseren en begrijpen van de status van naleving in uw omgeving. Op de **pagina Beleid** bevat de **optie** Overzicht details over de beschikbare scopes over de naleving van zowel beleidsregels als initiatieven. Samen met de nalevingstoestand en het aantal per toewijzing bevat deze een grafiek met de naleving gedurende de afgelopen zeven dagen. De **pagina** Naleving bevat veel van deze informatie (met uitzondering van de grafiek), maar biedt aanvullende filter- en sorteeropties.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Schermopname van de pagina Naleving, filteropties en details." border="false":::

Aangezien een beleid of initiatief kan worden toegewezen aan verschillende scopes, bevat de tabel het bereik voor elke toewijzing en het type definitie dat is toegewezen. Ook wordt het aantal niet-compatibele resources en niet-compatibele beleidsregels voor elke toewijzing opgegeven. Als u een beleid of initiatief selecteert in de tabel, krijgt u meer inzicht in de naleving van die specifieke toewijzing.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Schermopname van de pagina Details van naleving, met inbegrip van tellingen en gegevens over resourceconforme naleving." border="false":::

De lijst met resources op het tabblad **Resource-naleving** toont de evaluatiestatus van bestaande resources voor de huidige toewijzing. Het tabblad wordt standaard ingesteld **op Niet-compatibel,** maar kan worden gefilterd.
Gebeurtenissen (append, audit, deny, deploy, modify) die worden geactiveerd door de aanvraag om een resource te maken, worden weergegeven op het **tabblad** Gebeurtenissen.

> [!NOTE]
> Voor een AKS Engine-beleid is de weergegeven resource de resourcegroep.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Schermopname van het tabblad Gebeurtenissen op de pagina Details van naleving." border="false":::

<a name="component-compliance"></a>Als [u resources in de](../concepts/definition-structure.md#resource-provider-modes) resourceprovidermodus selecteert op het tabblad **Resource-naleving,** de resource selecteert of met de rechtermuisknop op de rij klikt en Nalevingsdetails weergeven selecteert, worden de details van de naleving van het onderdeel geopend.  Deze pagina biedt ook tabbladen om de beleidsregels weer te geven die zijn toegewezen aan deze resource, gebeurtenissen, onderdeelgebeurtenissen en wijzigingsgeschiedenis.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Schermopname van het tabblad Component compliance en nalevingsdetails voor een toewijzing van de resourceprovidermodus." border="false":::

Klik op de pagina voor resource-naleving met de rechtermuisknop op de rij van de gebeurtenis waar u meer informatie over wilt verzamelen en selecteer **Activiteitenlogboeken tonen.** De pagina activiteitenlogboek wordt geopend en is vooraf gefilterd op de zoekopdracht met details voor de toewijzing en de gebeurtenissen. Het activiteitenlogboek bevat aanvullende context en informatie over deze gebeurtenissen.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Schermopname van het activiteitenlogboek voor Azure Policy activiteiten en evaluaties." border="false":::

### <a name="understand-non-compliance"></a>Niet-naleving begrijpen

Wanneer wordt vastgesteld dat een resource **niet-compatibel is,** zijn er veel mogelijke redenen. Zie Niet-naleving bepalen om te bepalen waarom een resource niet **compatibel** is of om de verantwoordelijke wijziging [te vinden.](./determine-non-compliance.md)

## <a name="command-line"></a>Opdrachtregel

Dezelfde informatie die beschikbaar is in de portal, kan worden opgehaald met de REST API (inclusief [met ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell en Azure CLI. Zie de naslaginformatie voor REST API informatie over [de](/rest/api/policy/) Azure Policy informatie. De REST API referentiepagina's hebben een groene knop Proberen bij elke bewerking waarmee u deze direct in de browser kunt proberen.

Gebruik ARMClient of een vergelijkbaar hulpprogramma voor het afhandelen van verificatie bij Azure voor de REST API voorbeelden.

### <a name="summarize-results"></a>Resultaten samenvatten

Met de REST API kunnen samenvattingen worden uitgevoerd door container, definitie of toewijzing. Hier volgt een voorbeeld van een samenvatting op abonnementsniveau met behulp van Azure Policy Insights [Summarize for Subscription](/rest/api/policy/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

De uitvoer geeft een overzicht van het abonnement. In de onderstaande voorbeelduitvoer vindt u de samengevatte naleving onder **value.results.nonCompliantResources** en **value.results.nonCompliantPolicies.** Deze aanvraag bevat meer informatie, waaronder elke toewijzing die de niet-compatibele nummers en de definitie-informatie voor elke toewijzing bevat. Elk beleidsobject in de hiërarchie biedt een **queryResultsUri** die kan worden gebruikt om aanvullende details op dat niveau te krijgen.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Query's uitvoeren op resources

In het bovenstaande voorbeeld biedt **value.policyAssignments.policyDefinitions.results.queryResultsUri** een voorbeeld-URI voor alle niet-compatibele resources voor een specifieke beleidsdefinitie. Als u naar de **$filter-waarde** kijkt, is ComplianceState gelijk (eq) aan 'NonCompliant', PolicyAssignmentId opgegeven voor de beleidsdefinitie en vervolgens de PolicyDefinitionId zelf. De reden voor het op te nemen van de PolicyAssignmentId in het filter is dat de PolicyDefinitionId kan bestaan in verschillende beleids- of initiatieftoewijzingen met verschillende scopes. Door zowel de PolicyAssignmentId als de PolicyDefinitionId op te geven, kunnen we expliciet zijn in de resultaten die we zoeken. Voorheen hebben we voor PolicyStates **de** meest  recente gebruikt, waarmee automatisch een van **en** naar het tijdvenster van de afgelopen 24 uur wordt gemaakt.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Het onderstaande voorbeeld is voor de beknoptheid ingekort tot één niet-compatibele resource. Het gedetailleerde antwoord heeft verschillende soorten gegevens over de resource, het beleid of initiatief en de toewijzing. U ziet dat u ook kunt zien welke toewijzingsparameters zijn doorgegeven aan de beleidsdefinitie.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "ComplianceState": "NonCompliant",
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Gebeurtenissen weergeven

Wanneer een resource wordt gemaakt of bijgewerkt, wordt er een beleidsevaluatieresultaat gegenereerd. Resultaten worden _beleidsgebeurtenissen genoemd._ Gebruik de volgende URI om recente beleidsgebeurtenissen weer te geven die zijn gekoppeld aan het abonnement.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
```

De resultaten zien er ongeveer als volgt uit:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Zie voor meer informatie over het uitvoeren van query's op beleidsgebeurtenissen [Azure Policy naslaginformatie over](/rest/api/policy/policyevents) gebeurtenissen.

### <a name="azure-cli"></a>Azure CLI

De [Azure CLI-opdrachtgroep](/cli/azure/what-is-azure-cli) voor Azure Policy de meeste bewerkingen die beschikbaar zijn in REST of Azure PowerShell. Zie Azure CLI - Azure Policy Overview voor een volledige [lijst met beschikbare opdrachten.](/cli/azure/policy)

Voorbeeld: De statussamenvatting voor het meest toegewezen beleid met het hoogste aantal niet-compatibele resources.

```azurecli-interactive
az policy state summarize --top 1
```

Het bovenste gedeelte van het antwoord ziet eruit als in dit voorbeeld:

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

Voorbeeld: De statusrecord voor de laatst geëvalueerde resource op te vragen (standaard is dit een tijdstempel in aflopende volgorde).

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

Voorbeeld: De details voor alle niet-compatibele virtuele netwerkbronnen op te geven.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

Voorbeeld: Gebeurtenissen met betrekking tot niet-compatibele virtuele netwerkresources die zijn opgetreden na een specifieke datum op te vragen.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

De Azure PowerShell-module voor Azure Policy is beschikbaar op de PowerShell Gallery [als Az.PolicyInsights.](https://www.powershellgallery.com/packages/Az.PolicyInsights) Met Behulp van PowerShellGet kunt u de module installeren met behulp van (zorg ervoor dat u de nieuwste versie `Install-Module -Name Az.PolicyInsights` [Azure PowerShell](/powershell/azure/install-az-ps) geïnstalleerd):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

De module bevat de volgende cmdlets:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Voorbeeld: De statussamenvatting voor het meest toegewezen beleid met het hoogste aantal niet-compatibele resources.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Voorbeeld: de statusrecord voor de laatst geëvalueerde resource op te vragen (standaard is dit op tijdstempel in aflopende volgorde).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Voorbeeld: De details voor alle niet-compatibele virtuele netwerkbronnen op te geven.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Voorbeeld: Gebeurtenissen met betrekking tot niet-compatibele virtuele netwerkresources die zich hebben voorgedaan na een specifieke datum, converteren naar een CSV-object en exporteren naar een bestand.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

De uitvoer van het `$policyEvents` object ziet eruit als de volgende uitvoer:

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Het **veld PrincipalOid** kan worden gebruikt om een specifieke gebruiker op te halen met Azure PowerShell cmdlet `Get-AzADUser` . Vervang **{principalOid} door** het antwoord dat u uit het vorige voorbeeld krijgt.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

Als u een [Log Analytics-werkruimte](../../../azure-monitor/logs/log-query-overview.md) hebt met van de Analyse van activiteitenlogboek-oplossing die is gekoppeld aan uw abonnement, kunt u ook niet-nalevingsresultaten van de evaluatie van nieuwe en bijgewerkte resources bekijken met behulp van eenvoudige `AzureActivity` Kusto-query's en de [](../../../azure-monitor/essentials/activity-log.md) `AzureActivity` tabel. Met details in Azure Monitor logboeken kunnen waarschuwingen worden geconfigureerd om te controleren op niet-naleving.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Schermopname van Azure Monitor logboeken met Azure Policy acties in de tabel AzureActivity." border="false":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk voorbeelden op [Azure Policy voorbeelden.](../samples/index.md)
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Begrijpen hoe u [programmatisch beleid kunt maken.](programmatically-create.md)
- Ontdek hoe u [niet-compatibele resources kunt herstellen](remediate-resources.md).
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
