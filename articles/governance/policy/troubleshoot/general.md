---
title: Veelvoorkomende fouten oplossen
description: Meer informatie over het oplossen van problemen met het maken van beleids definities, de verschillende SDK en de invoeg toepassing voor Kubernetes.
ms.date: 12/01/2020
ms.topic: troubleshooting
ms.openlocfilehash: b88d00575adb571c59b562d25067c4a1716fb50f
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882973"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Fouten oplossen met behulp van Azure Policy

Er kunnen fouten optreden bij het maken van beleids definities, het werken met SDK of het instellen van de [Azure Policy voor](../concepts/policy-for-kubernetes.md) de invoeg toepassing Kubernetes. In dit artikel worden verschillende algemene fouten beschreven die zich kunnen voordoen en hoe u deze kunt oplossen.

## <a name="finding-error-details"></a>Fout details zoeken

De locatie van de fout Details is afhankelijk van de actie die de fout veroorzaakt.

- Wanneer u met een aangepast beleid werkt, probeert u het in de Azure Portal om meer informatie over het schema op te halen of om de resulterende [compatibiliteits gegevens](../how-to/get-compliance-data.md) te bekijken om te zien hoe de resources zijn geëvalueerd.
- Bij het werken met verschillende SDK biedt de SDK Details over waarom de functie is mislukt.
- Wanneer u werkt met de invoeg toepassing voor Kubernetes, begint u met de [logboek registratie](../concepts/policy-for-kubernetes.md#logging) in het cluster.

## <a name="general-errors"></a>Algemene fouten

### <a name="scenario-alias-not-found"></a>Scenario: alias niet gevonden

#### <a name="issue"></a>Probleem

Azure Policy maakt gebruik van [aliassen](../concepts/definition-structure.md#aliases) om toe te wijzen aan Azure Resource Manager eigenschappen.

#### <a name="cause"></a>Oorzaak

Er wordt een onjuiste of niet-bestaande alias gebruikt in een beleids definitie.

#### <a name="resolution"></a>Oplossing

Controleer eerst of de Resource Manager-eigenschap een alias heeft. Gebruik [Azure Policy extensie voor Visual Studio code](../how-to/extension-for-vscode.md) of SDK om beschik bare aliassen op te zoeken. Als de alias voor een resource manager-eigenschap niet bestaat, maakt u een ondersteunings ticket.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Scenario: evaluatie Details zijn niet up-to-date

#### <a name="issue"></a>Probleem

Een resource heeft de status niet gestart of de compatibiliteits Details zijn niet actueel.

#### <a name="cause"></a>Oorzaak

De toewijzing van een nieuw beleid of initiatief duurt ongeveer 30 minuten. Nieuwe of bijgewerkte resources binnen het bereik van een bestaande toewijzing worden later meer dan 15 minuten beschikbaar. Er wordt om de 24 uur een standaard compatibiliteits scan uitgevoerd. Zie [evaluatie triggers](../how-to/get-compliance-data.md#evaluation-triggers)voor meer informatie.

#### <a name="resolution"></a>Oplossing

Wacht eerst de juiste hoeveelheid tijd om een evaluatie uit te voeren en de resultaten van naleving beschikbaar te stellen in Azure Portal of SDK. Zie [evaluatie scan op aanvraag](../how-to/get-compliance-data.md#on-demand-evaluation-scan)om een nieuwe evaluatie scan te starten met Azure PowerShell of rest API.

### <a name="scenario-compliance-not-as-expected"></a>Scenario: naleving niet zoals verwacht

#### <a name="issue"></a>Probleem

Een resource bevindt zich niet in de evaluatie status, niet _compatibel_ of _niet-compatibel_, die voor die resource wordt verwacht.

#### <a name="cause"></a>Oorzaak

De resource bevindt zich niet in het juiste bereik voor de beleids toewijzing of de beleids definitie werkt niet zoals bedoeld.

#### <a name="resolution"></a>Oplossing

Volg deze stappen om de beleids definitie op te lossen:

1. Wacht eerst de juiste hoeveelheid tijd om een evaluatie uit te voeren en de resultaten van naleving beschikbaar te stellen in Azure Portal of SDK. Zie [evaluatie scan op aanvraag](../how-to/get-compliance-data.md#on-demand-evaluation-scan)om een nieuwe evaluatie scan te starten met Azure PowerShell of rest API.
1. Controleer of de toewijzings parameters en het toewijzings bereik correct zijn ingesteld.
1. Selecteer de [beleidsdefinitiemodus](../concepts/definition-structure.md#mode):
   - Modus all voor alle resource typen.
   - Modus ' geïndexeerd ' als de beleids definitie controleert op Tags of locatie.
1. Controleer of het bereik van de resource niet wordt [uitgesloten](../concepts/assignment-structure.md#excluded-scopes) of [uitgezonderd](../concepts/exemption-structure.md).
1. Als naleving voor een beleids toewijzing `0/0` bronnen bevat, zijn er geen resources vastgesteld die van toepassing zijn binnen het toewijzings bereik. Controleer de beleids definitie en het toewijzings bereik.
1. Voor een niet-compatibele resource die wordt verwacht te voldoen, controleert u de [redenen voor niet-naleving](../how-to/determine-non-compliance.md). De vergelijking van de definitie voor de waarde van de geëvalueerde eigenschap geeft aan waarom een resource niet aan het beleid voldoet.
   - Als de **doel waarde** onjuist is, wijzigt u de beleids definitie.
   - Als de **huidige waarde** onjuist is, valideert u de resource-Payload tot en met `resources.azure.com` .
1. Controle [problemen oplossen: afdwingen niet zoals verwacht](#scenario-enforcement-not-as-expected) voor andere veelvoorkomende problemen en oplossingen.

Als u nog steeds een probleem met uw gedupliceerde en aangepaste ingebouwde beleids definitie of aangepaste definitie hebt, maakt u een ondersteunings ticket onder het **ontwerpen van een beleid** om het probleem correct te routeren.

### <a name="scenario-enforcement-not-as-expected"></a>Scenario: afdwinging is niet zoals verwacht

#### <a name="issue"></a>Probleem

Een resource waarvan wordt verwacht dat deze wordt uitgevoerd door Azure Policy is niet en er is geen vermelding in het [Azure-activiteiten logboek](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Oorzaak

De beleids toewijzing is geconfigureerd voor [enforcementMode](../concepts/assignment-structure.md#enforcement-mode) van _disabled_. Terwijl de afdwingings modus is uitgeschakeld, wordt het beleids effect niet afgedwongen en is er geen vermelding in het activiteiten logboek.

#### <a name="resolution"></a>Oplossing

Voer de volgende stappen uit om het afdwingen van uw beleids toewijzing op te lossen:

1. Wacht eerst de juiste hoeveelheid tijd om een evaluatie uit te voeren en de resultaten van naleving beschikbaar te stellen in Azure Portal of SDK. Zie [evaluatie scan op aanvraag](../how-to/get-compliance-data.md#on-demand-evaluation-scan)om een nieuwe evaluatie scan te starten met Azure PowerShell of rest API.
1. Controleer of de toewijzings parameters en het toewijzings bereik correct zijn ingesteld en of **enforcementMode** is _ingeschakeld_.
1. Selecteer de [beleidsdefinitiemodus](../concepts/definition-structure.md#mode):
   - Modus all voor alle resource typen.
   - Modus ' geïndexeerd ' als de beleids definitie controleert op Tags of locatie.
1. Controleer of het bereik van de resource niet wordt [uitgesloten](../concepts/assignment-structure.md#excluded-scopes) of [uitgezonderd](../concepts/exemption-structure.md).
1. Controleer of de resourcepayload overeenkomt met de beleidslogica. U kunt dit doen door [een har-tracering](../../../azure-portal/capture-browser-trace.md) vast te leggen of door de eigenschappen van de arm-sjabloon te controleren.
1. Controle [problemen oplossen: naleving niet zoals verwacht](#scenario-compliance-not-as-expected) voor andere veelvoorkomende problemen en oplossingen.

Als u nog steeds een probleem met uw gedupliceerde en aangepaste ingebouwde beleids definitie of aangepaste definitie hebt, maakt u een ondersteunings ticket onder het **ontwerpen van een beleid** om het probleem correct te routeren.

### <a name="scenario-denied-by-azure-policy"></a>Scenario: geweigerd door Azure Policy

#### <a name="issue"></a>Probleem

Het maken of bijwerken van een resource wordt geweigerd.

#### <a name="cause"></a>Oorzaak

Een beleids toewijzing aan het bereik dat door de nieuwe of bijgewerkte resource wordt gebruikt, voldoet aan de criteria van een beleids definitie met een [weigerings](../concepts/effects.md#deny) effect. Resources die deze definities kunnen vergaderingen, kunnen niet worden gemaakt of bijgewerkt.

#### <a name="resolution"></a>Oplossing

Het fout bericht van een beleids toewijzing voor weigeren omvat de beleids definitie en beleids toewijzing-Id's. Als de fout informatie in het bericht ontbreekt, is het ook beschikbaar in het [activiteiten logboek](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Gebruik deze informatie om meer te weten te komen over de resource beperkingen en pas de bron eigenschappen in uw aanvraag aan om de toegestane waarden te vergelijken.

## <a name="template-errors"></a>Sjabloon fouten

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenario: door beleid ondersteunde functies die worden verwerkt door de sjabloon

#### <a name="issue"></a>Probleem

Azure Policy ondersteunt een aantal Azure Resource Manager sjablonen (ARM-sjablonen) en functies die alleen beschikbaar zijn in een beleids definitie. Resource Manager verwerkt deze functies als onderdeel van een implementatie in plaats van als onderdeel van een beleids definitie.

#### <a name="cause"></a>Oorzaak

Het gebruik van ondersteunde functies, zoals `parameter()` of `resourceGroup()` , resulteert in het verwerkte resultaat van de functie tijdens de implementatie in plaats van de functie voor de beleids definitie en de Azure Policy engine te laten verwerken.

#### <a name="resolution"></a>Oplossing

Als u wilt door gaan met een functie die deel uitmaakt van een beleids definitie, moet u de volledige teken reeks met `[` de betreffende eigenschap uitzoeken `[[resourceGroup().tags.myTag]` . Het escape teken heeft tot gevolg dat Resource Manager de waarde als een teken reeks behandelt bij het verwerken van de sjabloon. Azure Policy wordt de functie vervolgens in de beleids definitie geplaatst, zodat deze dynamisch naar verwachting kan worden uitgevoerd. Zie [syntaxis en expressies in azure Resource Manager-sjablonen](../../../azure-resource-manager/templates/template-expressions.md)voor meer informatie.

## <a name="add-on-for-kubernetes-installation-errors"></a>Invoeg toepassing voor Kubernetes-installatie fouten

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Scenario: installeren met behulp van helm-grafiek mislukt bij wacht woord

#### <a name="issue"></a>Probleem

De `helm install azure-policy-addon` opdracht mislukt met een van de volgende berichten:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Oorzaak

Het gegenereerde wacht woord bevat een komma ( `,` ) waarin helm-grafiek wordt gesplitst.

#### <a name="resolution"></a>Oplossing

Escape de komma ( `,` ) in de wachtwoord waarde wanneer deze wordt uitgevoerd `helm install azure-policy-addon` met een back slash ( `\` ).

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Scenario: de installatie met behulp van de helm-grafiek mislukt, omdat er al een naam bestaat

#### <a name="issue"></a>Probleem

De `helm install azure-policy-addon` opdracht mislukt met het volgende bericht:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Oorzaak

Het helm-diagram met de naam `azure-policy-addon` is al geïnstalleerd of gedeeltelijk geïnstalleerd.

#### <a name="resolution"></a>Oplossing

Volg de instructies om [de Azure Policy voor de invoeg toepassing Kubernetes te verwijderen](../concepts/policy-for-kubernetes.md#remove-the-add-on)en voer de `helm install azure-policy-addon` opdracht opnieuw uit.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenario: door de gebruiker toegewezen identiteiten van de virtuele Azure-machine worden vervangen door door het systeem toegewezen beheerde identiteiten

#### <a name="issue"></a>Probleem

Nadat gast configuratie beleids initiatieven zijn toegewezen aan controle-instellingen binnen machines, worden door de gebruiker toegewezen beheerde identiteiten die aan de computer zijn toegewezen, niet meer toegewezen. Er wordt alleen een door het systeem toegewezen beheerde identiteit toegewezen.

#### <a name="cause"></a>Oorzaak

De beleids definities die eerder zijn gebruikt in de DeployIfNotExists-definities van de gast configuratie zorgen ervoor dat een door het systeem toegewezen identiteit wordt toegewezen aan de computer, maar ook door de gebruiker toegewezen identiteits toewijzingen zijn verwijderd.

#### <a name="resolution"></a>Oplossing

De definities die eerder het probleem hebben veroorzaakt, worden weer gegeven als \[ afgeschaft \] en worden vervangen door beleids definities waarmee de vereisten worden beheerd zonder dat door de gebruiker toegewezen beheerde identiteit wordt verwijderd. Er is een hand matige stap vereist. Verwijder bestaande beleids toewijzingen die zijn gemarkeerd \[ als afgeschaft \] en vervang deze door het bijgewerkte vereisten beleid en beleids definities die dezelfde naam hebben als de oorspronkelijke.

Zie het volgende blog bericht voor een gedetailleerde beschrijving:

[Belang rijke wijziging die is uitgebracht voor controle beleid voor gast configuratie](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Invoeg toepassing voor Kubernetes algemene fouten

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-egress-restrictions"></a>Scenario: add-on kan het Azure Policy service-eind punt niet bereiken vanwege uitgevals beperkingen

#### <a name="issue"></a>Probleem

De invoeg toepassing kan het Azure Policy service-eind punt niet bereiken en retourneert een van de volgende fouten:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Oorzaak

Dit probleem doet zich voor wanneer een uitgang van het cluster wordt vergrendeld.

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat de domeinen en poorten in de volgende artikelen zijn geopend:

- [Vereiste uitgaande netwerk regels en FQDN voor AKS-clusters](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Azure Policy-invoeg toepassing installeren voor Azure Arc enabled Kubernetes (preview)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-aad-pod-identity-configuration"></a>Scenario: add-on kan het Azure Policy service-eind punt niet bereiken vanwege Aad-pod-Identity-configuratie

#### <a name="issue"></a>Probleem

De invoeg toepassing kan het Azure Policy service-eind punt niet bereiken en retourneert een van de volgende fouten:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer _add-pod-Identity_ is geïnstalleerd op het cluster en het _uitvoeren-systeem_ niet wordt uitgesloten van _Aad-pod-Identity_.

Het _Aad-pod-Identity_ component node Managed Identity (NMI) peul wijzigt de knoop punten iptables om aanroepen naar het eind punt van de meta gegevens van Azure-instanties te onderscheppen. Deze instelling houdt in dat elke aanvraag die wordt gedaan aan het eind punt van de meta gegevens wordt onderschept door NMI, zelfs als de pod geen _Aad-pod-identiteit_ gebruikt.
**AzurePodIdentityException** CRD kan worden geconfigureerd om _Aad-pod-identiteit_ te informeren dat alle aanvragen voor het eind punt van de meta gegevens die afkomstig zijn van een pod die overeenkomt met de labels die in CRD zijn gedefinieerd, moeten worden gewaarschuwd zonder enige verwerking in NMI.

#### <a name="resolution"></a>Oplossing

Sluit het systeem uit met `kubernetes.azure.com/managedby: aks` het label in _uitvoeren-_ naam ruimte in _Aad-pod-Identity_ door de **AzurePodIdentityException** CRD te configureren.

Zie [Aad pod-identiteit voor een specifieke pod/toepassing uitschakelen](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)voor meer informatie.

Als u een uitzonde ring wilt configureren, raadpleegt u dit voor beeld:

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

De invoeg toepassing kan het Azure Policy service-eind punt bereiken, maar ziet een van de volgende fouten in invoeg toepassings logboeken:

```
The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
https://aka.ms/policy-register-subscription for how to register subscriptions.
```

of

```
policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
Code="InternalServerError" Message="Encountered an internal server error."
```

#### <a name="cause"></a>Oorzaak

De `Microsoft.PolicyInsights` resource provider is niet geregistreerd en moet worden geregistreerd voor de invoeg toepassing om beleids definities op te halen en om compatibiliteits gegevens te retour neren.

#### <a name="resolution"></a>Oplossing

Registreer de `Microsoft.PolicyInsights` resource provider in het cluster abonnement. Zie [een resource provider registreren](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)voor instructies.

### <a name="scenario-the-subscription-is-disabled"></a>Scenario: het abonnement is uitgeschakeld

#### <a name="issue"></a>Probleem

De invoeg toepassing kan het Azure Policy service-eind punt bereiken, maar ziet de volgende fout:

```
The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.
```

#### <a name="cause"></a>Oorzaak

Deze fout betekent dat het abonnement een probleem heeft vastgesteld en dat de functie vlag `Microsoft.PolicyInsights/DataPlaneBlocked` is toegevoegd om het abonnement te blok keren.

#### <a name="resolution"></a>Oplossing

Neem contact op met het team functie `azuredg@microsoft.com` om dit probleem te onderzoeken en op te lossen.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van experts via [micro soft Q&A](/answers/topics/azure-policy.html).
- Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) – het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.
- Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
