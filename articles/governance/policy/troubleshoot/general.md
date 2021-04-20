---
title: Veelvoorkomende fouten oplossen
description: Meer informatie over het oplossen van problemen met het maken van beleidsdefinities, de verschillende SDK's en de invoegsel voor Kubernetes.
ms.date: 04/19/2021
ms.topic: troubleshooting
ms.openlocfilehash: c4feae11c6d8d78a43bae9882405e292a18e90bd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725058"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Fouten met het gebruik van Azure Policy

Wanneer u beleidsdefinities maakt, met SDK's werkt of de Azure Policy voor [kubernetes-invoegsel](../concepts/policy-for-kubernetes.md) invoegselt, kunnen er fouten optreden. In dit artikel worden verschillende algemene fouten beschreven die kunnen optreden en worden manieren voor het oplossen ervan beschreven.

## <a name="find-error-details"></a>Foutdetails zoeken

De locatie van de foutdetails is afhankelijk van welk aspect van Azure Policy u werkt.

- Als u met een aangepast beleid werkt, gaat u naar de Azure Portal om feedback [](../how-to/get-compliance-data.md) over het schema te krijgen of bekijkt u de resulterende nalevingsgegevens om te zien hoe resources zijn geëvalueerd.
- Als u met een van de verschillende SDK's werkt, biedt de SDK details over waarom de functie is mislukt.
- Als u met de invoegsel voor Kubernetes werkt, begint u met de [logboekregistratie](../concepts/policy-for-kubernetes.md#logging) in het cluster.

## <a name="general-errors"></a>Algemene fouten

### <a name="scenario-alias-not-found"></a>Scenario: Alias niet gevonden

#### <a name="issue"></a>Probleem

Een onjuiste of geenxistent alias wordt gebruikt in een beleidsdefinitie. Azure Policy [aliassen gebruikt om](../concepts/definition-structure.md#aliases) deze toe te Azure Resource Manager eigenschappen.

#### <a name="cause"></a>Oorzaak

Een onjuiste of geenxistent alias wordt gebruikt in een beleidsdefinitie.

#### <a name="resolution"></a>Oplossing

Controleer eerst of de eigenschap Resource Manager een alias heeft. Als u de beschikbare aliassen wilt op zoeken, gaat u [naar Azure Policy-extensie voor Visual Studio Code](../how-to/extension-for-vscode.md) of de SDK.
Als de alias voor een Resource Manager-eigenschap niet bestaat, maakt u een ondersteuningsticket.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Scenario: Evaluatiegegevens zijn niet up-to-date

#### <a name="issue"></a>Probleem

Een resource heeft de status _Niet gestart_ of de nalevingsdetails zijn niet actueel.

#### <a name="cause"></a>Oorzaak

Het duurt ongeveer 30 minuten voordat een nieuw beleid of initiatief is toegepast. Nieuwe of bijgewerkte resources binnen het bereik van een bestaande toewijzing zijn binnen ongeveer 15 minuten beschikbaar. Er wordt elke 24 uur een standaard nalevingsscan uitgevoerd. Zie evaluatietriggers [voor meer informatie.](../how-to/get-compliance-data.md#evaluation-triggers)

#### <a name="resolution"></a>Oplossing

Wacht eerst even totdat een evaluatie is voltooien en de nalevingsresultaten beschikbaar zijn in de Azure Portal of de SDK. Als u een nieuwe evaluatiescan wilt starten met Azure PowerShell of de REST API, gaat u [naar Evaluatiescan op aanvraag.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)

### <a name="scenario-compliance-isnt-as-expected"></a>Scenario: Naleving is niet zoals verwacht

#### <a name="issue"></a>Probleem

Een resource heeft niet de evaluatietoestand _Compatibel_ of _Niet-compatibel_ die voor de resource wordt verwacht.

#### <a name="cause"></a>Oorzaak

De resource is niet in het juiste bereik voor de beleidstoewijzing of de beleidsdefinitie werkt niet zoals bedoeld.

#### <a name="resolution"></a>Oplossing

Ga als volgt te werk om problemen met uw beleidsdefinitie op te lossen:

1. Wacht eerst de juiste hoeveelheid tijd totdat een evaluatie is voltooien en de nalevingsresultaten beschikbaar zijn in Azure Portal of SDK. 

1. Als u een nieuwe evaluatiescan wilt starten met Azure PowerShell of de REST API, gaat u [naar Evaluatiescan op aanvraag.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)
1. Zorg ervoor dat de toewijzingsparameters en het toewijzingsbereik correct zijn ingesteld.
1. Selecteer de [beleidsdefinitiemodus](../concepts/definition-structure.md#mode):
   - De modus moet voor `all` alle resourcetypen zijn.
   - De modus moet zijn `indexed` als de beleidsdefinitie op tags of locatie controleert.
1. Zorg ervoor dat het bereik van de resource niet [wordt uitgesloten of](../concepts/assignment-structure.md#excluded-scopes) [uitgesloten.](../concepts/exemption-structure.md)
1. Als bij naleving voor een beleidstoewijzing resources worden weer gegeven, zijn er geen resources vastgesteld `0/0` die van toepassing zijn binnen het toewijzingsbereik. Controleer zowel de beleidsdefinitie als het toewijzingsbereik.
1. Zie De redenen voor niet-naleving bepalen voor een niet-compatibele resource die naar verwachting compatibel [is.](../how-to/determine-non-compliance.md) De vergelijking van de definitie met de geëvalueerde eigenschapswaarde geeft aan waarom een resource niet compatibel is.
   - Als de **doelwaarde onjuist** is, wijzigt u de beleidsdefinitie.
   - Als de **huidige waarde onjuist** is, valideert u de nettolading van de resource via `resources.azure.com` .
1. Zie Problemen oplossen: Afdwingen niet zoals verwacht voor andere veelvoorkomende problemen [en oplossingen.](#scenario-enforcement-not-as-expected)

Als u nog steeds een probleem hebt met uw gedupliceerde en aangepaste  ingebouwde beleidsdefinitie of aangepaste definitie, maakt u een ondersteuningsticket onder Een beleid schrijven om het probleem correct te laten lopen.

### <a name="scenario-enforcement-not-as-expected"></a>Scenario: Afdwingen is niet zoals verwacht

#### <a name="issue"></a>Probleem

Er wordt geen actie onder Azure Policy resource die u verwacht te gebruiken en die geen vermelding heeft in het [Azure-activiteitenlogboek.](../../../azure-monitor/essentials/platform-logs-overview.md)

#### <a name="cause"></a>Oorzaak

De beleidstoewijzing is geconfigureerd voor de instelling [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) van _Uitgeschakeld._
Hoewel **enforcementMode** is uitgeschakeld, wordt het beleidseffect niet afgedwongen en is er geen vermelding in het activiteitenlogboek.

#### <a name="resolution"></a>Oplossing

Ga als volgt te werk om problemen met het afdwingen van uw beleidstoewijzing op te lossen:

1. Wacht eerst de juiste hoeveelheid tijd totdat een evaluatie is voltooien en de nalevingsresultaten beschikbaar zijn in de Azure Portal of de SDK. 

1. Als u een nieuwe evaluatiescan wilt starten met Azure PowerShell of de REST API, zie [Evaluatiescan op aanvraag.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)
1. Zorg ervoor dat de toewijzingsparameters en het toewijzingsbereik correct zijn ingesteld en **dat enforcementMode** is _ingeschakeld._
1. Selecteer de [beleidsdefinitiemodus](../concepts/definition-structure.md#mode):
   - De modus moet voor `all` alle resourcetypen zijn.
   - De modus moet zijn als `indexed` de beleidsdefinitie controleert op tags of locatie.
1. Zorg ervoor dat het bereik van de resource niet [wordt uitgesloten of](../concepts/assignment-structure.md#excluded-scopes) [uitgesloten.](../concepts/exemption-structure.md)
1. Controleer of de nettolading van de resource overeenkomt met de beleidslogica. U kunt dit doen door een [HTTP Archive-tracering (HAR)](../../../azure-portal/capture-browser-trace.md) vast te leggen of door de eigenschappen van de Azure Resource Manager-sjabloon (ARM-sjabloon) te controleren.
1. Zie Problemen oplossen: Naleving niet zoals verwacht voor andere veelvoorkomende problemen [en oplossingen.](#scenario-compliance-isnt-as-expected)

Als u nog steeds een probleem hebt met uw gedupliceerde en aangepaste  ingebouwde beleidsdefinitie of aangepaste definitie, maakt u een ondersteuningsticket onder Een beleid schrijven om het probleem correct te laten lopen.

### <a name="scenario-denied-by-azure-policy"></a>Scenario: Geweigerd door Azure Policy

#### <a name="issue"></a>Probleem

Het maken of bijwerken van een resource wordt geweigerd.

#### <a name="cause"></a>Oorzaak

Een beleidstoewijzing voor het bereik van uw nieuwe of bijgewerkte resource voldoet aan de criteria van een beleidsdefinitie [met](../concepts/effects.md#deny) het effect Weigeren. Resources die aan deze definities voldoen, kunnen niet worden gemaakt of bijgewerkt.

#### <a name="resolution"></a>Oplossing

Het foutbericht van een beleidstoewijzing voor weigeren bevat de beleidsdefinitie en beleidstoewijzings-ID's. Als de foutgegevens in het bericht worden overgeslagen, is deze ook beschikbaar in het [activiteitenlogboek](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log). Gebruik deze informatie voor meer informatie om inzicht te krijgen in de resourcebeperkingen en om de resource-eigenschappen in uw aanvraag aan te passen aan de toegestane waarden.

### <a name="scenario-definition-targets-multiple-resource-types"></a>Scenario: Definitie is gericht op meerdere resourcetypen

#### <a name="issue"></a>Probleem

Een beleidsdefinitie die meerdere resourcetypen bevat, mislukt de validatie tijdens het maken of bijwerken met de volgende fout:

```error
The policy definition '{0}' targets multiple resource types, but the policy rule is authored in a way that makes the policy not applicable to the target resource types '{1}'.
```

#### <a name="cause"></a>Oorzaak

De beleidsdefinitieregel heeft een of meer voorwaarden die niet worden geëvalueerd door de doelresourcetypen.

#### <a name="resolution"></a>Oplossing

Als een alias wordt gebruikt, moet u ervoor zorgen dat de alias alleen wordt geëvalueerd op basis van het resourcetype waar deze bij hoort door een typevoorwaarde toe te voegen. Een alternatief is om de beleidsdefinitie op te splitsen in meerdere definities om te voorkomen dat deze gericht is op meerdere resourcetypen.

## <a name="template-errors"></a>Sjabloonfouten

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenario: Door beleid ondersteunde functies verwerkt door sjabloon

#### <a name="issue"></a>Probleem

Azure Policy ondersteunt een aantal ARM-sjabloonfuncties en -functies die alleen beschikbaar zijn in een beleidsdefinitie. Resource Manager worden deze functies verwerkt als onderdeel van een implementatie in plaats van als onderdeel van een beleidsdefinitie.

#### <a name="cause"></a>Oorzaak

Het gebruik van ondersteunde functies, zoals of , resulteert in het verwerkte resultaat van de functie tijdens de implementatie in plaats van dat de functie voor de beleidsdefinitie en `parameter()` `resourceGroup()` Azure Policy-engine kan worden verwerkt.

#### <a name="resolution"></a>Oplossing

Als u een functie wilt doorgeven als onderdeel van een beleidsdefinitie, escapet u de volledige tekenreeks zodanig dat `[` de eigenschap eruitziet als `[[resourceGroup().tags.myTag]` . Het escape-teken zorgt ervoor Resource Manager de waarde als een tekenreeks behandelt wanneer de sjabloon wordt verwerkt. Azure Policy vervolgens de functie in de beleidsdefinitie, zodat deze dynamisch kan zijn zoals verwacht. Zie Syntaxis en [expressies in Azure Resource Manager sjablonen voor meer informatie.](../../../azure-resource-manager/templates/template-expressions.md)

## <a name="add-on-for-kubernetes-installation-errors"></a>Invoegproces voor Kubernetes-installatiefouten

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Scenario: Installatie met behulp van een Helm-grafiek mislukt vanwege een wachtwoordfout

#### <a name="issue"></a>Probleem

De `helm install azure-policy-addon` opdracht mislukt en retourneert een van de volgende fouten:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Oorzaak

Het gegenereerde wachtwoord bevat een komma ( `,` ), waarop de Helm-grafiek wordt gesplitst.

#### <a name="resolution"></a>Oplossing

Wanneer u `helm install azure-policy-addon` gebruikt, escapet u de komma ( `,` ) in de wachtwoordwaarde met een backslash ( `\` ).

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Scenario: Installatie met behulp van een Helm-grafiek mislukt omdat de naam al bestaat

#### <a name="issue"></a>Probleem

De `helm install azure-policy-addon` opdracht mislukt en retourneert de volgende fout:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Oorzaak

De Helm-grafiek met `azure-policy-addon` de naam is al geïnstalleerd of gedeeltelijk geïnstalleerd.

#### <a name="resolution"></a>Oplossing

Volg de instructies voor [het verwijderen Azure Policy kubernetes-invoegsel](../concepts/policy-for-kubernetes.md#remove-the-add-on)en voer de opdracht opnieuw `helm install azure-policy-addon` uit.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenario: Door de gebruiker toegewezen identiteiten van virtuele Azure-machines worden vervangen door door het systeem toegewezen beheerde identiteiten

#### <a name="issue"></a>Probleem

Nadat u initiatieven voor gastconfiguratiebeleid hebt toegewezen om instellingen binnen een computer te controleren, worden de door de gebruiker toegewezen beheerde identiteiten die aan de machine zijn toegewezen, niet meer toegewezen. Alleen een door het systeem toegewezen beheerde identiteit wordt toegewezen.

#### <a name="cause"></a>Oorzaak

De beleidsdefinities die eerder zijn gebruikt in deployifnotexists-definities voor gastconfiguratie hebben ervoor gezorgd dat een door het systeem toegewezen identiteit wordt toegewezen aan de computer, maar ze hebben ook de door de gebruiker toegewezen identiteitstoewijzingen verwijderd.

#### <a name="resolution"></a>Oplossing

De definities die dit probleem eerder hebben veroorzaakt, worden weergegeven als _\[ Afgeschaft \]_ en vervangen door beleidsdefinities die vereisten beheren zonder door de gebruiker toegewezen beheerde identiteiten te verwijderen. Er is een handmatige stap vereist. Verwijder alle bestaande beleidstoewijzingen die zijn gemarkeerd als _\[ Afgeschaft \]_ en vervang deze door het bijgewerkte vereiste beleidsinitiatief en de beleidsdefinities die dezelfde naam hebben als het origineel.

Zie het blogbericht Belangrijke wijziging uitgebracht voor controlebeleid voor gastconfiguratie voor een [gedetailleerd verhaal.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Invoegsel voor algemene Kubernetes-fouten

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Scenario: De invoeg-invoeging kan het Azure Policy service-eindpunt niet bereiken vanwege beperkingen voor toegangsverlening

#### <a name="issue"></a>Probleem

De invoeg-on kan het Azure Policy service-eindpunt niet bereiken en retourneert een van de volgende fouten:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Oorzaak

Dit probleem treedt op wanneer een clusteruitgress is vergrendeld.

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat de domeinen en poorten die in de volgende artikelen worden vermeld, zijn geopend:

- [Vereiste uitgaande netwerkregels en FQDN's (Fully Qualified Domain Names) voor AKS-clusters](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [De Azure Policy voor Kubernetes Azure Arc kubernetes (preview) installeren](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Scenario: De invoeg-on kan het Azure Policy service-eindpunt niet bereiken vanwege de configuratie aad-pod-identity

#### <a name="issue"></a>Probleem

De invoeg-on kan het Azure Policy service-eindpunt niet bereiken en retourneert een van de volgende fouten:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer _add-pod-identity_ is geïnstalleerd op het cluster en de _kube-system-pods_ niet worden uitgesloten in _aad-pod-identity._

Het _onderdeel aad-pod-identity_ Node Managed Identity (NMI)-pods wijzigt de iptables van de knooppunten om aanroepen naar het eindpunt van de metagegevens van het Azure-exemplaar te onderscheppen. Deze installatie betekent dat elke aanvraag die wordt ingediend bij het eindpunt voor metagegevens, wordt onderschept door NMI, zelfs als de pod _geen aad-pod-identity gebruikt._
De _AzurePodIdentityException_ CustomResourceDefinition (CRD) kan worden geconfigureerd om _aad-pod-identity_ te informeren dat aanvragen naar een metagegevens-eindpunt die afkomstig zijn van een pod die overeenkomt met de labels die zijn gedefinieerd in de CRD, moeten worden geproxied zonder verwerking in NMI.

#### <a name="resolution"></a>Oplossing

Sluit de systeempods uit die het `kubernetes.azure.com/managedby: aks` label in _de kube-system-naamruimte_ in _aad-pod-identity_ hebben door de _CRD AzurePodIdentityException_ te configureren.

Zie Disable [the Azure Active Directory (Azure AD) pod identity for a specific pod/application (De](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)identiteit van de pod Azure Active Directory Azure AD) uitschakelen voor een specifieke pod/toepassing voor meer informatie.

Als u een uitzondering wilt configureren, volgt u dit voorbeeld:

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

### <a name="scenario-the-resource-provider-isnt-registered"></a>Scenario: De resourceprovider is niet geregistreerd

#### <a name="issue"></a>Probleem

De invoeg-on kan het Azure Policy service-eindpunt bereiken, maar in de invoeglogboeken wordt een van de volgende fouten weergegeven:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Oorzaak

De resourceprovider Microsoft.PolicyInsights is niet geregistreerd. Deze moet worden geregistreerd voor de invoeg-on om beleidsdefinities op te halen en nalevingsgegevens te retourneren.

#### <a name="resolution"></a>Oplossing

Registreer de resourceprovider Microsoft.PolicyInsights in het clusterabonnement. Zie Een [resourceprovider registreren voor instructies.](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

### <a name="scenario-the-subscription-is-disabled"></a>Scenario: Het abonnement is uitgeschakeld

#### <a name="issue"></a>Probleem

De invoegcode kan het Azure Policy service-eindpunt bereiken, maar de volgende fout wordt weergegeven:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Oorzaak

Deze fout betekent dat het abonnement als problematisch is vastgesteld en dat de functievlag is toegevoegd om `Microsoft.PolicyInsights/DataPlaneBlocked` het abonnement te blokkeren.

#### <a name="resolution"></a>Oplossing

Neem contact op met het functieteam om dit probleem te onderzoeken [en op te lossen.](mailto:azuredg@microsoft.com)

## <a name="next-steps"></a>Volgende stappen

Als uw probleem niet wordt vermeld in dit artikel of als u het niet kunt oplossen, kunt u ondersteuning krijgen door een van de volgende kanalen te bezoeken:

- Krijg antwoorden van experts via [Microsoft Q&A](/answers/topics/azure-policy.html).
- Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) . Deze officiële Microsoft Azure-resource op Twitter helpt de gebruikerservaring te verbeteren door de Azure-community te verbinden met de juiste antwoorden, ondersteuning en experts.
- Als u nog steeds hulp nodig hebt, gaat u [naar ondersteuning voor Azure site](https://azure.microsoft.com/support/options/) en **selecteert u Een ondersteuningsaanvraag indienen.**
