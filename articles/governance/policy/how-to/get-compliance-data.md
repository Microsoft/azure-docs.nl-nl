---
title: Nalevings gegevens voor beleid ophalen
description: Azure Policy evaluaties en effecten bepalen de naleving. Meer informatie over hoe u de compatibiliteits Details van uw Azure-resources kunt ophalen.
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 3c1c128b414444c6004f32f3f3173548f81a82e1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577108"
---
# <a name="get-compliance-data-of-azure-resources"></a>Compatibiliteits gegevens van Azure-resources ophalen

Een van de grootste voor delen van Azure Policy is het inzicht en de bedienings elementen biedt meer informatie over resources in een abonnement of [beheer groep](../../management-groups/overview.md) abonnementen. Dit besturings element kan op veel verschillende manieren worden gebruikt, zoals het voor komen van het maken van resources op de verkeerde locatie, het afdwingen van algemeen en consistent gebruik van tags of het controleren van bestaande resources op de juiste configuraties en instellingen. In alle gevallen worden gegevens gegenereerd door Azure Policy zodat u de compatibiliteits status van uw omgeving kunt begrijpen.

Er zijn verschillende manieren om toegang te krijgen tot de nalevings gegevens die zijn gegenereerd door de toewijzingen van uw beleid en initiatief:

- De [Azure Portal](#portal) gebruiken
- Via script [opdracht regel](#command-line)

Voordat u de methoden voor het rapporteren van naleving bekijkt, kijken we naar wanneer de compatibiliteits informatie wordt bijgewerkt en de frequentie en gebeurtenissen die een evaluatie cyclus activeren.

> [!WARNING]
> Als de nalevings status wordt gerapporteerd als **niet geregistreerd**, controleert u of de resource provider **micro soft. PolicyInsights** is geregistreerd en of de gebruiker beschikt over de juiste machtigingen voor Azure op rollen gebaseerde toegangs beheer (Azure RBAC), zoals beschreven in [azure RBAC-machtigingen in azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Evaluatie triggers

De resultaten van een voltooide evaluatie cyclus zijn beschikbaar in de `Microsoft.PolicyInsights` resource provider via `PolicyStates` en- `PolicyEvents` bewerkingen. Zie [Azure Policy Insights](/rest/api/policy-insights/)voor meer informatie over de bewerkingen van de Azure Policy Insights-rest API.

De evaluaties van toegewezen beleid en initiatieven worden uitgevoerd als gevolg van verschillende gebeurtenissen:

- Een beleid of initiatief wordt voor het eerst toegewezen aan een bereik. Het duurt ongeveer 30 minuten voordat de toewijzing wordt toegepast op het gedefinieerde bereik. Zodra deze is toegepast, begint de evaluatie cyclus voor bronnen binnen die scope op basis van het zojuist toegewezen beleid of initiatief, en afhankelijk van de effecten die worden gebruikt door het beleid of initiatief, worden bronnen gemarkeerd als compatibel, niet-compatibel of uitgezonderd. Een groot beleid of initiatief dat is geëvalueerd op basis van een groot bereik van resources kan enige tijd duren. Als zodanig is er geen vooraf gedefinieerde verwachting van wanneer de evaluatie cyclus is voltooid. Zodra het proces is voltooid, zijn bijgewerkte resultaten van de naleving beschikbaar in de portal en Sdk's.

- Een beleid of initiatief dat al is toegewezen aan een bereik, wordt bijgewerkt. De evaluatie cyclus en de timing voor dit scenario zijn hetzelfde als voor een nieuwe toewijzing aan een bereik.

- Een resource wordt geïmplementeerd of bijgewerkt binnen een bereik met een toewijzing via Azure Resource Manager, REST API of een ondersteunde SDK. In dit scenario wordt de effect gebeurtenis (toevoegen, controleren, weigeren, implementeren) en compatibele status informatie voor de afzonderlijke resource beschikbaar in de portal en de Sdk's ongeveer 15 minuten later. Deze gebeurtenis veroorzaakt geen evaluatie van andere resources.

- Er wordt een [beleids uitsluiting](../concepts/exemption-structure.md) gemaakt, bijgewerkt of verwijderd. In dit scenario wordt de bijbehorende toewijzing geëvalueerd voor het gedefinieerde uitsluitings bereik.

- Evaluatie cyclus voor standaard compatibiliteit. Eenmaal per 24 uur worden toewijzingen automatisch opnieuw geëvalueerd. Een groot beleid of initiatief van veel resources kan enige tijd in beslag nemen, dus er is geen vooraf gedefinieerde verwachting wanneer de evaluatie cyclus is voltooid. Zodra het proces is voltooid, zijn bijgewerkte resultaten van de naleving beschikbaar in de portal en Sdk's.

- De provider van de [gast configuratie](../concepts/guest-configuration.md) resource is bijgewerkt met compatibiliteits Details door een beheerde resource.

- Scan op aanvraag

### <a name="on-demand-evaluation-scan"></a>Evaluatiescan op aanvraag

Een evaluatie scan voor een abonnement of een resource groep kan worden gestart met Azure CLI, Azure PowerShell, een aanroep van de REST API of met behulp van de [Azure Policy compatibiliteits scan github actie](https://github.com/marketplace/actions/azure-policy-compliance-scan).
Deze scan is een asynchroon proces.

#### <a name="on-demand-evaluation-scan---github-action"></a>Evaluatie scan op aanvraag-GitHub-actie

Gebruik de [Azure Policy compatibiliteits scan actie](https://github.com/marketplace/actions/azure-policy-compliance-scan) om een evaluatie scan op aanvraag te activeren vanuit uw [github-werk stroom](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) op een of meer resources, resource groepen of abonnementen, en Gate the workflow op basis van de compatibiliteits status van bronnen. U kunt de werk stroom ook zo configureren dat deze op een gepland tijdstip wordt uitgevoerd, zodat u de meest recente nalevings status op een geschikt tijdstip krijgt. Deze GitHub actie kan optioneel een rapport genereren over de nalevings status van gescande resources voor verdere analyse of voor archivering.

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

Voor meer informatie en voor beelden van werk stromen raadpleegt u de [github-actie voor Azure Policy nalevings scan opslag plaats](https://github.com/Azure/policy-compliance-scan).

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Evaluatie scan op aanvraag-Azure CLI

De compatibiliteits scan wordt gestart met de opdracht [AZ policy state trigger-scan](/cli/azure/policy/state#az_policy_state_trigger_scan) .

`az policy state trigger-scan`Hiermee wordt standaard een evaluatie gestart voor alle resources in het huidige abonnement. Als u een evaluatie voor een specifieke resource groep wilt starten, gebruikt u de para meter voor de **resource groep** . In het volgende voor beeld wordt een nalevings scan gestart in het huidige abonnement voor de resource groep _MyRG_ :

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

U kunt ervoor kiezen om niet te wachten tot het asynchrone proces is voltooid voordat u doorgaat met de para meter **no-wait** .

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Evaluatie scan op aanvraag-Azure PowerShell

De compatibiliteits scan wordt gestart met de cmdlet [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan) .

`Start-AzPolicyComplianceScan`Hiermee wordt standaard een evaluatie gestart voor alle resources in het huidige abonnement. Als u een evaluatie voor een specifieke resource groep wilt starten, gebruikt u de para meter **ResourceGroupName** . In het volgende voor beeld wordt een nalevings scan gestart in het huidige abonnement voor de resource groep _MyRG_ :

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

U kunt ervoor zorgen dat Power shell wacht totdat de asynchrone aanroep is voltooid voordat de resultaten uitvoer wordt opgegeven of dat deze op de achtergrond als een [taak](/powershell/module/microsoft.powershell.core/about/about_jobs)wordt uitgevoerd. Als u een Power shell-taak wilt gebruiken om de compatibiliteits scan op de achtergrond uit te voeren, gebruikt u de para meter **AsJob** en stelt u de waarde in op een object, zoals `$job` in dit voor beeld:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

U kunt de status van de taak controleren door op het object te controleren `$job` . De taak is van het type `Microsoft.Azure.Commands.Common.AzureLongRunningJob` . Gebruiken `Get-Member` op het `$job` object om beschik bare eigenschappen en methoden weer te geven.

Terwijl de compatibiliteits scan wordt uitgevoerd, controleert het `$job` object resultaten als volgt:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Wanneer de compatibiliteits scan is voltooid, verandert de **status** eigenschap in _voltooid_.

#### <a name="on-demand-evaluation-scan---rest"></a>Evaluatie scan op aanvraag-REST

Als asynchroon proces wordt het REST-eind punt voor het starten van de scan niet gewacht totdat de scan is voltooid. In plaats daarvan biedt het een URI om de status van de aangevraagde evaluatie op te vragen.

In elke REST API-URI zijn er verschillende variabelen die worden gebruikt en die u moet vervangen door uw eigen waarden:

- `{YourRG}` -Vervang door de naam van de resource groep
- Vervang `{subscriptionId}` door uw abonnements-ID

De scan ondersteunt de evaluatie van resources in een abonnement of in een resource groep. Start een scan op bereik met een REST API opdracht **post** met de volgende URI-structuren:

- Abonnement

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Resourcegroep

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

De aanroep retourneert een **geaccepteerde** status van 202. Opgenomen in de antwoord header is een **locatie** -eigenschap met de volgende indeling:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` statisch gegenereerd voor het aangevraagde bereik. Als voor een scope al een scan op aanvraag wordt uitgevoerd, wordt er geen nieuwe scan gestart. In plaats daarvan wordt de nieuwe aanvraag dezelfde `{ResourceContainerGUID}` **locatie** -URI gegeven als status. Een REST API **Get** -opdracht naar de **locatie** -URI retourneert een **202 geaccepteerd** tijdens de evaluatie. Wanneer de evaluatie scan is voltooid, wordt de status **200 OK** geretourneerd. De hoofd tekst van een voltooide scan is een JSON-antwoord met de status:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Evaluatie scan op aanvraag-Visual Studio code

Met de extensie Azure Policy voor Visual Studio code kan een evaluatie scan voor een specifieke resource worden uitgevoerd. Deze scan is een synchroon proces, in tegens telling tot de Azure PowerShell-en REST-methoden.
Zie [evaluatie op aanvraag met de VS code-extensie](./extension-for-vscode.md#on-demand-evaluation-scan)voor meer informatie en stappen.

## <a name="how-compliance-works"></a>Hoe naleving werkt

In een toewijzing is een resource **niet-compatibel** als deze niet voldoet aan het beleid of initiatief regels en niet wordt _uitgesloten_. In de volgende tabel ziet u hoe verschillende beleids effecten werken met de evaluatie van de voor waarde voor de resulterende nalevings status:

| Resourcestatus | Effect | Beleidsevaluatie | Nalevingsstatus |
| --- | --- | --- | --- |
| Nieuw of bijgewerkt | Controleren, wijzigen, AuditIfNotExist | True | Niet-compatibel |
| Nieuw of bijgewerkt | Controleren, wijzigen, AuditIfNotExist | False | Compatibel |
| Bestaat | Weigeren, Controleren, Toevoegen, Wijzigen, DeployIfNotExist, AuditIfNotExist | True | Niet-compatibel |
| Bestaat | Weigeren, Controleren, Toevoegen, Wijzigen, DeployIfNotExist, AuditIfNotExist | False | Compatibel |

> [!NOTE]
> Voor de effecten DeployIfNotExist en AuditIfNotExist moet de IF-instructie TRUE en de bestaansvoorwaarde FALSE zijn om niet-compatibel te zijn. Indien TRUE, activeert de IF-voorwaarde de evaluatie van de bestaansvoorwaarde voor de gerelateerde resources.

Stel dat u een resource groep hebt – ContsoRG, waarbij sommige opslag accounts (rood gemarkeerd) worden weer gegeven in open bare netwerken.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagram van opslag accounts die worden blootgesteld aan open bare netwerken in de resource groep contoso R G." border="false":::
   Diagram met afbeeldingen voor vijf opslag accounts in de resource groep contoso R G.  Opslag accounts 1 en 3 zijn blauw, terwijl opslag accounts twee, vier en vijf rood zijn.
:::image-end:::

In dit voor beeld moet u zich op het hoede zijn voor beveiligings Risico's. Nu u een beleids toewijzing hebt gemaakt, wordt deze geëvalueerd voor alle opgenomen en niet-uitgesloten opslag accounts in de resource groep ContosoRG. De drie niet-compatibele opslag accounts worden gecontroleerd, waardoor de status ervan wordt gewijzigd in **niet-compatibel.**

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagram van naleving van het opslag account in de resource groep contoso R G." border="false":::
   Diagram met afbeeldingen voor vijf opslag accounts in de resource groep contoso R G. Opslag accounts die zich in één en drie bevinden, hebben nu een groen vinkje, terwijl voor opslag accounts twee, vier en vijf nu rode waarschuwings tekens onder zijn.
:::image-end:::

Naast het **conform** -en **niet-nalevings** beleid en de bronnen gelden vier andere statussen:

- **Uitgezonderd**: de resource bevindt zich binnen het bereik van een toewijzing, maar heeft een [gedefinieerde uitzonde ring](../concepts/exemption-structure.md).
- **Conflicterende**: er zijn twee of meer beleids definities met conflicterende regels. Bijvoorbeeld, twee definities voegen dezelfde tag toe met verschillende waarden.
- **Niet gestart**: de evaluatie cyclus is niet gestart voor het beleid of de resource.
- **Niet geregistreerd**: de Azure Policy resource provider is niet geregistreerd of het account dat is aangemeld, heeft geen machtiging voor het lezen van de compatibiliteits gegevens.

Azure Policy maakt gebruik van de velden **type**, **naam** of **kind** in de definitie om te bepalen of een resource een overeenkomst is. Wanneer de bron overeenkomt, wordt deze beschouwd als toepasselijk en heeft deze de status **compatibel**, **niet-compatibel** of **uitgezonderd**. Als een van beide **typen**, namen **of typen** de enige eigenschap in de definitie is, worden alle opgenomen en niet-vrijgestelde resources beschouwd als toepasselijk en geëvalueerd. 

Het nalevings percentage wordt bepaald door het verdelen van de **compatibele** en **vrijgestelde** resources op basis van het _totale aantal resources_. _Het totale aantal resources_ wordt gedefinieerd als de som van de **compatibele**, **niet-compatibele**, **uitgesloten** en **conflicterende** resources. De algemene compatibiliteits aantallen zijn de som van afzonderlijke resources die in **overeenstemming** zijn met of worden **uitgesloten** van de som van alle afzonderlijke resources. In de onderstaande afbeelding zijn er 20 afzonderlijke resources die van toepassing zijn en slechts één **niet-compatibel** is.
De algemene bron compatibiliteit is 95% (19 van 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Scherm opname van Details van beleids naleving van de pagina naleving." border="false":::

> [!NOTE]
> Naleving van regelgeving in Azure Policy is een preview-functie. Nalevings eigenschappen van de SDK en pagina's in de portal zijn anders voor ingeschakelde initiatieven. Zie [naleving van regelgeving](../concepts/regulatory-compliance.md) voor meer informatie

## <a name="portal"></a>Portal

De Azure Portal demonstreert een grafische ervaring voor het visualiseren en het leren van de status van naleving in uw omgeving. Op de pagina **beleid** bevat de optie **overzicht** Details over de beschik bare bereiken over de naleving van beide beleids regels en initiatieven. Samen met de compatibiliteits status en het aantal per toewijzing bevat het een diagram met de compatibiliteit van de afgelopen zeven dagen. De pagina **naleving** bevat veel dezelfde informatie (met uitzonde ring van de grafiek), maar biedt extra filters en sorteer opties.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Scherm afbeelding van de nalevings pagina, filter opties en Details." border="false":::

Omdat een beleid of initiatief kan worden toegewezen aan verschillende bereiken, bevat de tabel het bereik voor elke toewijzing en het type definitie dat is toegewezen. Er worden ook het aantal niet-compatibele resources en niet-compatibele beleids regels voor elke toewijzing gegeven. Het selecteren van een beleid of initiatief in de tabel biedt een diep gaande blik op de naleving van de desbetreffende toewijzing.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Scherm opname van de pagina nalevings Details, inclusief aantallen en Details van resource-compatibel." border="false":::

De lijst met resources op het tabblad **resource compatibiliteit** bevat de evaluatie status van bestaande resources voor de huidige toewijzing. Het tabblad wordt standaard ingesteld op **niet-compatibel**, maar kan worden gefilterd.
Gebeurtenissen (toevoegen, controleren, weigeren, implementeren, wijzigen) die zijn geactiveerd door de aanvraag om een resource te maken, worden weer gegeven op het tabblad **gebeurtenissen** .

> [!NOTE]
> Voor een AKS-engine beleid is de weer gegeven resource de resource groep.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Scherm afbeelding van het tabblad gebeurtenissen op de pagina nalevings Details." border="false":::

<a name="component-compliance"></a> Voor resources van de [resource provider modus](../concepts/definition-structure.md#resource-provider-modes) selecteert u op het tabblad **resource naleving** de resource, klikt u met de rechter muisknop op de rij en selecteert u **compatibiliteits details weer geven** de details van de onderdeel naleving. Deze pagina bevat ook tabbladen voor een overzicht van de beleids regels die zijn toegewezen aan deze resource, gebeurtenissen, onderdeel gebeurtenissen en wijzigings geschiedenis.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Scherm afbeelding van het tabblad Compatibiliteit van onderdelen en Details van naleving voor een toewijzing van de resource provider modus." border="false":::

Klik op de pagina Resource naleving met de rechter muisknop op de rij van de gebeurtenis waarvoor u meer informatie wilt verzamelen en selecteer **activiteiten logboeken weer geven**. De pagina activiteiten logboek wordt geopend en wordt vooraf gefilterd op de zoek opdracht met Details voor de toewijzing en de gebeurtenissen. Het activiteiten logboek bevat aanvullende context en informatie over deze gebeurtenissen.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Scherm opname van het activiteiten logboek voor de Azure Policy activiteiten en evaluaties." border="false":::

### <a name="understand-non-compliance"></a>Meer informatie over niet-naleving

Wanneer een resource als **niet-compatibel** wordt beschouwd, zijn er veel mogelijke redenen. Zie [niet-naleving bepalen](./determine-non-compliance.md)om de reden vast te stellen waarom een resource **niet-compatibel** is of om de wijziging te vinden.

## <a name="command-line"></a>Opdrachtregel

Dezelfde informatie die beschikbaar is in de portal kan worden opgehaald met de REST API (inclusief [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell en Azure cli. Zie de naslag informatie voor [Azure Policy Insights](/rest/api/policy-insights/) voor volledige details over de rest API. De REST API-referentie pagina's hebben een groene ' Try-' knop voor elke bewerking waarmee u deze in de browser kunt uitproberen.

Gebruik ARMClient of een soortgelijk hulp programma voor het afhandelen van verificatie voor Azure voor de REST API-voor beelden.

### <a name="summarize-results"></a>Resultaten samenvatten

Met de REST API kan samen vatting worden uitgevoerd door container, definitie of toewijzing. Hier volgt een voor beeld van een samen vatting op het abonnements niveau met behulp van Azure Policy Insight- [samen vatting voor het abonnement](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

De uitvoer geeft een samen vatting van het abonnement. In de voorbeeld uitvoer hieronder vindt u een overzicht van de naleving onder **waarde. results. nonCompliantResources** en **Value. results. nonCompliantPolicies**. Deze aanvraag bevat meer informatie, waaronder elke toewijzing die de niet-compatibele getallen en de definitie-informatie voor elke toewijzing heeft opgeleverd. Elk beleids object in de hiërarchie bevat een **queryResultsUri** die kan worden gebruikt om aanvullende details op dat niveau op te halen.

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

### <a name="query-for-resources"></a>Query voor resources

In het bovenstaande voor beeld bevat **Value. policyAssignments. policyDefinitions. results. queryResultsUri** een voor beeld-URI voor alle niet-compatibele resources voor een specifieke beleids definitie. Op de **$filter** waarde is ComplianceState gelijk aan ' niet-compatibel ', PolicyAssignmentId is opgegeven voor de beleids definitie en vervolgens de PolicyDefinitionId zelf. De reden voor het opnemen van de PolicyAssignmentId in het filter is omdat de PolicyDefinitionId kan bestaan in verschillende beleids-of initiatief toewijzingen met verschillende bereiken. Door zowel de PolicyAssignmentId als de PolicyDefinitionId op te geven, kunnen we expliciet worden gebruikt in de resultaten die we zoeken. Voorheen werd voor PolicyStates de laatste **versie** gebruikt, waarbij automatisch een **van** - **en-** tijd venster van de laatste 24 uur wordt ingesteld.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Het onderstaande voor beeld antwoord is afgekapt tot één niet-conforme resource voor de boog. Het gedetailleerde antwoord heeft verschillende gegevens over de resource, het beleid of het initiatief en de toewijzing. U ziet dat u ook kunt zien welke toewijzings parameters zijn door gegeven aan de beleids definitie.

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

Wanneer een resource wordt gemaakt of bijgewerkt, wordt een resultaat van een beleids evaluatie gegenereerd. Resultaten worden _beleids gebeurtenissen_ genoemd. Gebruik de volgende URI om recente beleids gebeurtenissen weer te geven die zijn gekoppeld aan het abonnement.

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

Zie voor meer informatie over het uitvoeren van query's op beleids gebeurtenissen het artikel over [Azure Policy Events](/rest/api/policy-insights/policyevents) .

### <a name="azure-cli"></a>Azure CLI

De [Azure cli](/cli/azure/what-is-azure-cli) -opdracht groep voor Azure policy omvat de meeste bewerkingen die beschikbaar zijn in REST of Azure PowerShell. Zie [overzicht van Azure cli-Azure Policy](/cli/azure/policy)voor een volledige lijst met beschik bare opdrachten.

Voor beeld: ophalen van de status samenvatting voor het bovenste toegewezen beleid met het hoogste aantal niet-compatibele resources.

```azurecli-interactive
az policy state summarize --top 1
```

Het bovenste gedeelte van het antwoord ziet er als volgt uit:

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

Voor beeld: ophalen van de status record voor de meest recent geëvalueerde resource (standaard ingesteld op tijds tempel in aflopende volg orde).

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

Voor beeld: de Details ophalen voor alle niet-compatibele virtuele netwerk resources.

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

Voor beeld: het ophalen van gebeurtenissen die betrekking hebben op niet-compatibele virtuele netwerk bronnen die zijn opgetreden na een specifieke datum.

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

De module Azure PowerShell voor Azure Policy is beschikbaar op de PowerShell Gallery als [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights). Met PowerShellGet kunt u de module installeren met `Install-Module -Name Az.PolicyInsights` (zorg ervoor dat u de nieuwste [Azure PowerShell](/powershell/azure/install-az-ps) hebt geïnstalleerd):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

De module heeft de volgende cmdlets:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Voor beeld: ophalen van de status samenvatting voor het bovenste toegewezen beleid met het hoogste aantal niet-compatibele resources.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Voor beeld: ophalen van de status record voor de meest recent geëvalueerde resource (standaard ingesteld op tijds tempel in aflopende volg orde).

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

Voor beeld: de Details ophalen voor alle niet-compatibele virtuele netwerk resources.

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

Voor beeld: het ophalen van gebeurtenissen met betrekking tot niet-compatibele virtuele netwerk bronnen die zijn opgetreden na een specifieke datum, het converteren naar een CSV-object en het exporteren naar een bestand.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

De uitvoer van het `$policyEvents` object ziet er als volgt uit:

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

Het veld **PrincipalOid** kan worden gebruikt om een specifieke gebruiker te verkrijgen met de cmdlet Azure PowerShell `Get-AzADUser` . Vervang **{principalOid}** door de reactie die u uit het vorige voor beeld hebt ontvangen.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

Als u een [log Analytics-werk ruimte](../../../azure-monitor/logs/log-query-overview.md) hebt met `AzureActivity` van de [analyse van activiteitenlogboek oplossing](../../../azure-monitor/essentials/activity-log.md) die aan uw abonnement is gekoppeld, kunt u de resultaten van de niet-naleving van de evaluatie van nieuwe en bijgewerkte resources ook weer geven met behulp van eenvoudige Kusto-query's en de `AzureActivity` tabel. Met details in Azure Monitor-logboeken kunnen waarschuwingen worden geconfigureerd om niet-naleving te controleren.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Scherm opname van Azure Monitor logboeken met Azure Policy acties in de tabel AzureActivity." border="false":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
