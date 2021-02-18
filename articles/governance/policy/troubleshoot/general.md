---
title: Veelvoorkomende fouten oplossen
description: Meer informatie over het oplossen van problemen met het maken van beleids definities, de diverse Sdk's en de invoeg toepassing voor Kubernetes.
ms.date: 01/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: 6e0e4067f07266bae9c87fd4443d27314cc28c0b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592601"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Fouten bij het gebruik van Azure Policy oplossen

Wanneer u beleids definities maakt, met Sdk's werkt of de [Azure Policy voor Kubernetes](../concepts/policy-for-kubernetes.md) -invoeg toepassing instelt, kunnen er fouten optreden. In dit artikel worden verschillende algemene fouten beschreven die zich kunnen voordoen en worden manieren voorgesteld om deze op te lossen.

## <a name="find-error-details"></a>Fout details zoeken

De locatie van de fout Details is afhankelijk van het aspect van Azure Policy waarmee u werkt.

- Als u werkt met een aangepast beleid, gaat u naar de Azure Portal om terugvallende feedback over het schema op te halen of raadpleegt u de resulterende [compatibiliteits gegevens](../how-to/get-compliance-data.md) om te zien hoe resources zijn geëvalueerd.
- Als u werkt met een van de verschillende Sdk's, biedt de SDK Details over waarom de functie is mislukt.
- Als u werkt met de invoeg toepassing voor Kubernetes, begint u met de [logboek registratie](../concepts/policy-for-kubernetes.md#logging) in het cluster.

## <a name="general-errors"></a>Algemene fouten

### <a name="scenario-alias-not-found"></a>Scenario: alias niet gevonden

#### <a name="issue"></a>Probleem

Er wordt een onjuiste of niet-bestaande alias gebruikt in een beleids definitie. Azure Policy maakt gebruik van [aliassen](../concepts/definition-structure.md#aliases) om toe te wijzen aan Azure Resource Manager eigenschappen.

#### <a name="cause"></a>Oorzaak

Er wordt een onjuiste of niet-bestaande alias gebruikt in een beleids definitie.

#### <a name="resolution"></a>Oplossing

Controleer eerst of de Resource Manager-eigenschap een alias heeft. Als u de beschik bare aliassen wilt opzoeken, gaat u naar [Azure Policy-extensie voor Visual Studio code](../how-to/extension-for-vscode.md) of de SDK.
Als de alias voor een resource manager-eigenschap niet bestaat, maakt u een ondersteunings ticket.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Scenario: evaluatie Details zijn niet up-to-date

#### <a name="issue"></a>Probleem

Een resource heeft de status _niet gestart_ , of de compatibiliteits Details zijn niet actueel.

#### <a name="cause"></a>Oorzaak

De toewijzing van een nieuw beleid of initiatief duurt ongeveer 30 minuten. Nieuwe of bijgewerkte resources binnen het bereik van een bestaande toewijzing worden in ongeveer 15 minuten beschikbaar. Een standaard compatibiliteits scan wordt elke 24 uur uitgevoerd. Zie [evaluatie triggers](../how-to/get-compliance-data.md#evaluation-triggers)voor meer informatie.

#### <a name="resolution"></a>Oplossing

Wacht eerst een juiste hoeveelheid tijd om een evaluatie te volt ooien en de nalevings resultaten beschikbaar te stellen in de Azure Portal of de SDK. Zie [evaluatie scan op aanvraag](../how-to/get-compliance-data.md#on-demand-evaluation-scan)om een nieuwe evaluatie scan te starten met Azure PowerShell of de rest API.

### <a name="scenario-compliance-isnt-as-expected"></a>Scenario: compatibiliteit is niet zoals verwacht

#### <a name="issue"></a>Probleem

Een resource bevindt zich niet in de evaluatie status _compatibel_ of _niet-compatibel_ die voor de resource wordt verwacht.

#### <a name="cause"></a>Oorzaak

De resource bevindt zich niet in het juiste bereik voor de beleids toewijzing of de beleids definitie werkt niet zoals bedoeld.

#### <a name="resolution"></a>Oplossing

Ga als volgt te werk om problemen met de beleids definitie op te lossen:

1. Wacht eerst de juiste hoeveelheid tijd om een evaluatie te volt ooien en de nalevings resultaten beschikbaar te stellen in Azure Portal of SDK. 

1. Zie [evaluatie scan op aanvraag](../how-to/get-compliance-data.md#on-demand-evaluation-scan)om een nieuwe evaluatie scan te starten met Azure PowerShell of de rest API.
1. Zorg ervoor dat de toewijzings parameters en het toewijzings bereik correct zijn ingesteld.
1. Selecteer de [beleidsdefinitiemodus](../concepts/definition-structure.md#mode):
   - De modus moet gelden `all` voor alle resource typen.
   - De modus moet zijn `indexed` als de beleids definitie controleert op Tags of locatie.
1. Zorg ervoor dat het bereik van de resource niet wordt [uitgesloten](../concepts/assignment-structure.md#excluded-scopes) of [uitgezonderd](../concepts/exemption-structure.md).
1. Als naleving voor een beleids toewijzing `0/0` bronnen bevat, zijn er geen resources vastgesteld die van toepassing zijn binnen het toewijzings bereik. Controleer de beleids definitie en het toewijzings bereik.
1. Zie [de redenen voor niet-naleving bepalen](../how-to/determine-non-compliance.md)voor een resource die niet aan het beleid voldoet. De vergelijking van de definitie van de geëvalueerde eigenschaps waarde geeft aan waarom een resource niet-compatibel was.
   - Als de **doel waarde** onjuist is, wijzigt u de beleids definitie.
   - Als de **huidige waarde** onjuist is, valideert u de resource-Payload tot en met `resources.azure.com` .
1. Zie [probleem oplossing: afdwinging is niet zoals verwacht](#scenario-enforcement-not-as-expected)voor andere veelvoorkomende problemen en oplossingen.

Als u nog steeds een probleem met uw gedupliceerde en aangepaste ingebouwde beleids definitie of aangepaste definitie hebt, maakt u een ondersteunings ticket onder het **ontwerpen van een beleid** om het probleem correct te routeren.

### <a name="scenario-enforcement-not-as-expected"></a>Scenario: afdwinging is niet zoals verwacht

#### <a name="issue"></a>Probleem

Een resource waarvan u verwacht dat Azure Policy worden uitgevoerd, heeft geen actie ondergaand en er is geen vermelding in het [Azure-activiteiten logboek](../../../azure-monitor/essentials/platform-logs-overview.md).

#### <a name="cause"></a>Oorzaak

De beleids toewijzing is geconfigureerd voor de [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) -instelling _uitgeschakeld_.
Hoewel **enforcementMode** is uitgeschakeld, wordt het beleids effect niet afgedwongen en is er geen vermelding in het activiteiten logboek.

#### <a name="resolution"></a>Oplossing

Los het afdwingen van uw beleids toewijzing op door de volgende handelingen uit te voeren:

1. Wacht eerst de juiste hoeveelheid tijd om een evaluatie te volt ooien en de nalevings resultaten beschikbaar te stellen in de Azure Portal of de SDK. 

1. Zie [evaluatie scan op aanvraag](../how-to/get-compliance-data.md#on-demand-evaluation-scan)om een nieuwe evaluatie scan te starten met Azure PowerShell of de rest API.
1. Zorg ervoor dat de toewijzings parameters en het toewijzings bereik correct zijn ingesteld en dat **enforcementMode** is _ingeschakeld_.
1. Selecteer de [beleidsdefinitiemodus](../concepts/definition-structure.md#mode):
   - De modus moet gelden `all` voor alle resource typen.
   - De modus moet zijn `indexed` als de beleids definitie controleert op Tags of locatie.
1. Zorg ervoor dat het bereik van de resource niet wordt [uitgesloten](../concepts/assignment-structure.md#excluded-scopes) of [uitgezonderd](../concepts/exemption-structure.md).
1. Controleer of de resource lading overeenkomt met de beleids logica. U kunt dit doen door [een HTTP-archief (HAR)-tracering](../../../azure-portal/capture-browser-trace.md) vast te leggen of door de eigenschappen van de Azure Resource Manager sjabloon (arm-sjabloon) te controleren.
1. Zie [problemen oplossen: compatibiliteit niet zoals verwacht](#scenario-compliance-isnt-as-expected)voor andere veelvoorkomende problemen en oplossingen.

Als u nog steeds een probleem met uw gedupliceerde en aangepaste ingebouwde beleids definitie of aangepaste definitie hebt, maakt u een ondersteunings ticket onder het **ontwerpen van een beleid** om het probleem correct te routeren.

### <a name="scenario-denied-by-azure-policy"></a>Scenario: geweigerd door Azure Policy

#### <a name="issue"></a>Probleem

Het maken of bijwerken van een resource wordt geweigerd.

#### <a name="cause"></a>Oorzaak

Een beleids toewijzing aan het bereik van uw nieuwe of bijgewerkte bron voldoet aan de criteria van een beleids definitie met een [weigerings](../concepts/effects.md#deny) effect. Resources die voldoen aan deze definities, kunnen niet worden gemaakt of bijgewerkt.

#### <a name="resolution"></a>Oplossing

Het fout bericht van een beleids toewijzing voor weigeren omvat de beleids definitie en beleids toewijzing-Id's. Als de fout informatie in het bericht ontbreekt, is het ook beschikbaar in het [activiteiten logboek](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log). Gebruik deze informatie om meer te weten te komen over de resource beperkingen en pas de bron eigenschappen in uw aanvraag aan om de toegestane waarden te vergelijken.

## <a name="template-errors"></a>Sjabloon fouten

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenario: door beleid ondersteunde functies die worden verwerkt door de sjabloon

#### <a name="issue"></a>Probleem

Azure Policy ondersteunt een aantal ARM-sjabloon functies en-functies die alleen beschikbaar zijn in een beleids definitie. Resource Manager verwerkt deze functies als onderdeel van een implementatie in plaats van als onderdeel van een beleids definitie.

#### <a name="cause"></a>Oorzaak

Het gebruik van ondersteunde functies, zoals `parameter()` of `resourceGroup()` , resulteert in het verwerkte resultaat van de functie tijdens de implementatie in plaats van de functie toe te staan voor de beleids definitie en de Azure Policy Engine om te verwerken.

#### <a name="resolution"></a>Oplossing

Als u een functie wilt door geven als onderdeel van een beleids definitie, gaat u naar de volledige teken reeks met `[` een zodanige resultaat dat de eigenschap eruitziet `[[resourceGroup().tags.myTag]` . Het escape teken zorgt ervoor dat Resource Manager de waarde als een teken reeks behandelt bij het verwerken van de sjabloon. Azure Policy wordt de functie vervolgens in de beleids definitie geplaatst, zodat deze dynamisch kan worden uitgevoerd. Zie [syntaxis en expressies in azure Resource Manager-sjablonen](../../../azure-resource-manager/templates/template-expressions.md)voor meer informatie.

## <a name="add-on-for-kubernetes-installation-errors"></a>Invoeg toepassing voor Kubernetes-installatie fouten

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Scenario: installatie met behulp van een helm-grafiek mislukt vanwege een wachtwoord fout

#### <a name="issue"></a>Probleem

De `helm install azure-policy-addon` opdracht mislukt en retourneert een van de volgende fouten:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Oorzaak

Het gegenereerde wacht woord bevat een komma ( `,` ), waarop het helm-diagram wordt gesplitst.

#### <a name="resolution"></a>Oplossing

Wanneer u uitvoert `helm install azure-policy-addon` , moet u de komma ( `,` ) in de wachtwoord waarde met een back slash ( `\` ).

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Scenario: installatie met behulp van een helm-grafiek mislukt omdat de naam al bestaat

#### <a name="issue"></a>Probleem

De `helm install azure-policy-addon` opdracht mislukt en retourneert de volgende fout:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Oorzaak

Het helm-diagram met de naam `azure-policy-addon` is al geïnstalleerd of gedeeltelijk geïnstalleerd.

#### <a name="resolution"></a>Oplossing

Volg de instructies om [de Azure Policy voor de invoeg toepassing Kubernetes te verwijderen](../concepts/policy-for-kubernetes.md#remove-the-add-on)en voer de `helm install azure-policy-addon` opdracht opnieuw uit.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenario: door de gebruiker toegewezen identiteiten van de virtuele Azure-machine worden vervangen door door het systeem toegewezen beheerde identiteiten

#### <a name="issue"></a>Probleem

Nadat u gast configuratie beleids initiatieven hebt toegewezen aan controle-instellingen binnen een machine, worden de door de gebruiker toegewezen beheerde identiteiten die aan de computer zijn toegewezen, niet meer toegewezen. Er wordt alleen een door het systeem toegewezen beheerde identiteit toegewezen.

#### <a name="cause"></a>Oorzaak

De beleids definities die eerder zijn gebruikt in de DeployIfNotExists-definities van de gast configuratie zorgen ervoor dat een door het systeem toegewezen identiteit wordt toegewezen aan de computer, maar ook de door de gebruiker toegewezen identiteits toewijzingen zijn verwijderd.

#### <a name="resolution"></a>Oplossing

De definities die eerder het probleem hebben veroorzaakt, worden weer gegeven als _\[ afgeschaft \]_ en worden vervangen door beleids definities waarmee de vereisten worden beheerd zonder dat door de gebruiker toegewezen beheerde identiteiten worden verwijderd. Er is een hand matige stap vereist. Verwijder bestaande beleids toewijzingen die zijn gemarkeerd als _\[ afgeschaft \]_ en vervang deze door het bijgewerkte vereisten beleid en beleids definities die dezelfde naam hebben als de oorspronkelijke.

Zie voor een gedetailleerde beschrijving het blog bericht [belang rijke wijziging is uitgebracht voor controle beleid voor gast configuratie](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Invoeg toepassing voor Kubernetes algemene fouten

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Scenario: de invoeg toepassing kan het Azure Policy service-eind punt niet bereiken vanwege uitgevals beperkingen

#### <a name="issue"></a>Probleem

De invoeg toepassing kan het Azure Policy service-eind punt niet bereiken en retourneert een van de volgende fouten:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Oorzaak

Dit probleem treedt op wanneer een uitgang van het cluster wordt vergrendeld.

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat de domeinen en poorten die in de volgende artikelen worden vermeld, zijn geopend:

- [Vereiste uitgaande netwerk regels en FQDN-namen (Fully Qualified Domain names) voor AKS-clusters](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Installeer de Azure Policy-invoeg toepassing voor Azure Arc-enabled Kubernetes (preview)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Scenario: de invoeg toepassing kan het Azure Policy service-eind punt niet bereiken vanwege de Aad-pod-identiteits configuratie

#### <a name="issue"></a>Probleem

De invoeg toepassing kan het Azure Policy service-eind punt niet bereiken en retourneert een van de volgende fouten:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer _add-pod-Identity_ is geïnstalleerd op het cluster en de _uitvoeren-systeem_ -peul niet wordt uitgesloten in _Aad-pod-Identity_.

Het _Aad-pod-Identity_ component node Managed Identity (NMI) peul wijzigt de knoop punten ' iptables om aanroepen naar het eind punt van de meta gegevens van het Azure-exemplaar te onderscheppen. Deze instelling betekent dat elke aanvraag die wordt gedaan aan het eind punt van de meta gegevens, wordt onderschept door NMI, zelfs als de pod geen _Aad-pod-identiteit_ gebruikt.
De _AzurePodIdentityException_ -CUSTOMRESOURCEDEFINITION (CRD) kan worden geconfigureerd om _Aad-pod-identiteit_ te informeren dat elke aanvraag naar een eind punt van de meta gegevens die afkomstig is van een pod die overeenkomt met de labels die in de CRD zijn gedefinieerd, moet worden geproxyd zonder enige verwerking in NMI.

#### <a name="resolution"></a>Oplossing

Sluit het systeem, die het `kubernetes.azure.com/managedby: aks` label bevat in _uitvoeren-_ naam ruimte in _Aad-pod-identiteit_ uit door de _AzurePodIdentityException_ CRD te configureren.

Zie [de pod-identiteit van Azure Active Directory (Azure AD) voor een specifieke pod/toepassing uitschakelen](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)voor meer informatie.

Als u een uitzonde ring wilt configureren, volgt u dit voor beeld:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Scenario: de resource provider is niet geregistreerd

#### <a name="issue"></a>Probleem

De invoeg toepassing kan het Azure Policy service-eind punt bereiken, maar in de logboeken van de invoeg toepassing wordt een van de volgende fouten weer gegeven:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Oorzaak

De resource provider micro soft. PolicyInsights is niet geregistreerd. Het moet worden geregistreerd voor de invoeg toepassing om beleids definities op te halen en om compatibiliteits gegevens te retour neren.

#### <a name="resolution"></a>Oplossing

Registreer de resource provider micro soft. PolicyInsights in het cluster abonnement. Zie [een resource provider registreren](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)voor instructies.

### <a name="scenario-the-subscription-is-disabled"></a>Scenario: het abonnement is uitgeschakeld

#### <a name="issue"></a>Probleem

De invoeg toepassing kan het Azure Policy service-eind punt bereiken, maar de volgende fout wordt weer gegeven:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Oorzaak

Deze fout houdt in dat het abonnement een probleem heeft vastgesteld en dat de functie vlag `Microsoft.PolicyInsights/DataPlaneBlocked` is toegevoegd om het abonnement te blok keren.

#### <a name="resolution"></a>Oplossing

[Neem contact op met het functie Team](mailto:azuredg@microsoft.com)om dit probleem te onderzoeken en op te lossen.

## <a name="next-steps"></a>Volgende stappen

Als uw probleem niet in dit artikel wordt vermeld of als u het niet kunt oplossen, gaat u naar ondersteuning door naar een van de volgende kanalen te gaan:

- Krijg antwoorden van experts via [micro soft Q&A](/answers/topics/azure-policy.html).
- Verbinding maken met [@AzureSupport](https://twitter.com/azuresupport) . Deze officiële Microsoft Azure bron op Twitter helpt de gebruikers ervaring te verbeteren door de Azure-community te verbinden met de juiste antwoorden, ondersteuning en experts.
- Als u nog steeds hulp nodig hebt, gaat u naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteert u **een ondersteunings aanvraag indienen**.
