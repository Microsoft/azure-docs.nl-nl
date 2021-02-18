---
title: Beveiligt u met Azure Policy in azure Kubernetes service (AKS)
description: Meer informatie over het beveiligen van het gehele Azure Policy op Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 09/22/2020
ms.openlocfilehash: 34f2bfe346d7163a254e2ccecd1d7ef63ddb4194
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092630"
---
# <a name="secure-pods-with-azure-policy"></a>Pods beveiligen met Azure Policy

Als u de beveiliging van uw AKS-cluster wilt verbeteren, kunt u bepalen welke functies er worden verleend en of er iets wordt uitgevoerd op basis van het bedrijfs beleid. Deze toegang wordt gedefinieerd via ingebouwd beleid dat is opgegeven door de [Azure Policy-invoeg toepassing voor AKS][kubernetes-policy-reference]. Door extra controle over de beveiligings aspecten van de specificatie van uw Pod te bieden, zoals root-bevoegdheden, kunt u strenger beveiliging en inzicht krijgen in wat er in uw cluster wordt geïmplementeerd. Als een pod niet voldoet aan de voor waarden die zijn opgegeven in het beleid, kan Azure Policy de pod niet toestaan om een schending te starten of te markeren. In dit artikel leest u hoe u Azure Policy kunt gebruiken om de implementatie van een Peul in AKS te beperken.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

### <a name="install-the-azure-policy-add-on-for-aks"></a>De Azure Policy-invoeg toepassing voor AKS installeren

Als u de AKS van Azure Policy wilt beveiligen, moet u de Azure Policy-invoeg toepassing voor AKS op een AKS-cluster installeren. Volg deze [stappen om de invoeg toepassing Azure Policy te installeren](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks).

In dit document wordt ervan uitgegaan dat u het volgende hebt, dat wordt geïmplementeerd in de bovenstaande procedure.

* Registreert de `Microsoft.ContainerService` en `Microsoft.PolicyInsights` resource providers met `az provider register`
* Azure CLI 2,12 of hoger
* Een AKS-cluster met een versie van 1,15 of hoger die is geïnstalleerd met de Azure Policy-invoeg toepassing

## <a name="overview-of-securing-pods-with-azure-policy-for-aks"></a>Overzicht van het beveiligen van Peul met Azure Policy voor AKS

>[!NOTE]
> In dit document vindt u informatie over het gebruik van Azure Policy voor het beveiligen van Peul, de opvolger [van de Kubernetes pod Security Policy functie in Preview](use-pod-security-policies.md).
> **Het Pod-beveiligings beleid (preview) en de invoeg toepassing Azure Policy voor AKS kunnen niet tegelijkertijd worden ingeschakeld.**
> 
> Als u de invoeg toepassing Azure Policy installeert in een cluster waarvoor pod-beveiligings beleid is ingeschakeld, [voert u de volgende stappen uit om pod-beveiligings beleid uit te scha kelen](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

In een AKS-cluster wordt een toegangs controller gebruikt om aanvragen voor de API-server te onderscheppen wanneer een bron moet worden gemaakt en bijgewerkt. De toegangs controller kan vervolgens de resource aanvraag *valideren* op basis van een set regels of deze moet worden gemaakt.

Voorheen werd het [beveiligings beleid voor de functie pod (preview)](use-pod-security-policies.md) ingeschakeld via het Kubernetes-project om te beperken wat er kan worden geïmplementeerd.

Door gebruik te maken van de Azure Policy-invoeg toepassing, kan een AKS-cluster ingebouwde Azure-beleids regels gebruiken, waarmee u Peule en andere Kubernetes-resources kunt beveiligen die vergelijkbaar zijn met Pod-beveiligings beleid. De Azure Policy-invoeg toepassing voor AKS installeert een beheerd exemplaar van [gate keeper](https://github.com/open-policy-agent/gatekeeper), een validatie van de toegangs controller. Azure Policy voor Kubernetes is gebaseerd op de open-source-beleids agent, die afhankelijk is van de [taal](../governance/policy/concepts/policy-for-kubernetes.md#policy-language)van het Rego-beleid.

Dit document bevat informatie over het gebruik van Azure Policy voor het beveiligen van de peuling in een AKS-cluster en instructies voor het migreren van pod-beveiligings beleid (preview).

## <a name="limitations"></a>Beperkingen

De volgende algemene beperkingen zijn van toepassing op de Azure Policy-invoeg toepassing voor Kubernetes-clusters:

- Azure Policy-invoeg toepassing voor Kubernetes wordt ondersteund op Kubernetes versie **1,14** of hoger.
- Azure Policy-invoeg toepassing voor Kubernetes kan alleen worden geïmplementeerd voor Linux-knooppunt Pools
- Alleen ingebouwde beleids definities worden ondersteund
- Maximum aantal niet-compatibele records per beleid per cluster: **500**
- Maximum aantal niet-compatibele records per abonnement: **1.000.000**
- Installatie van gate keeper buiten de Azure Policy-invoeg toepassing wordt niet ondersteund. Verwijder onderdelen die door een eerdere gate keeper-installatie zijn geïnstalleerd voordat u de invoeg toepassing Azure Policy inschakelt.
- [Redenen voor niet-naleving](../governance/policy/how-to/determine-non-compliance.md#compliance-reasons) zijn niet beschikbaar voor deze [resource provider modus](../governance/policy/concepts/definition-structure.md#resource-provider-modes)

De volgende beperkingen gelden alleen voor de Azure Policy-invoeg toepassing voor AKS:

- [AKS pod-beveiligings beleid (preview)](use-pod-security-policies.md) en de Azure Policy-invoeg toepassing voor AKS kunnen niet beide worden ingeschakeld. 
- Naam ruimten automatisch uitgesloten door Azure Policy invoeg toepassing voor evaluatie: _uitvoeren-System_, _gate keeper-System_ en _AKS-Peri Scope_. Als u Calico-netwerk beleid gebruikt met Kubernetes-versie 1,20 en hoger, worden er automatisch twee naam ruimten uitgesloten, die _Calico System_ en _tigera-operator_ zijn.

### <a name="recommendations"></a>Aanbevelingen

Hieronder vindt u algemene aanbevelingen voor het gebruik van de Azure Policy-invoeg toepassing:

- Voor de invoeg toepassing Azure Policy zijn 3 gate keeper-onderdelen vereist om uit te voeren: 1 audit pod en 2 webhook pod-replica's. Deze onderdelen gebruiken meer bronnen als het aantal Kubernetes-resources en beleids toewijzingen toenames in het cluster waarvoor controle-en afdwingings bewerkingen vereist zijn.

  - Voor minder dan 500 peul in één cluster met een maximum van 20 beperkingen: 2 Vcpu's en 350 MB geheugen per onderdeel.
  - Meer dan 500 peul in één cluster met een maximum van 40 beperkingen: 3 Vcpu's en 600 MB geheugen per onderdeel.

De volgende aanbeveling geldt alleen voor AKS en de invoeg toepassing Azure Policy:

- Gebruik de systeem knooppunt groep met `CriticalAddonsOnly` Taint om de taak van de gate keeper te plannen. Zie het [gebruik van systeem knooppunt groepen](use-system-pools.md#system-and-user-node-pools)voor meer informatie.
- Beveiligde uitgaand verkeer van uw AKS-clusters. Zie voor meer informatie controle van uitgaand [verkeer voor cluster knooppunten](limit-egress-traffic.md).
- Als het cluster is `aad-pod-identity` ingeschakeld, wijzigt NMI (node Managed Identity) het knoop punt ' iptables ' om aanroepen naar het eind punt van de meta gegevens van het Azure-exemplaar te onderscheppen. Deze configuratie houdt in dat elke aanvraag voor het eind punt van de meta gegevens wordt onderschept door NMI, zelfs als de pod niet wordt gebruikt `aad-pod-identity` . AzurePodIdentityException CRD kan zodanig worden geconfigureerd `aad-pod-identity` dat alle aanvragen voor het eind punt van de meta gegevens die afkomstig zijn van een pod die overeenkomt met de labels die zijn gedefinieerd in CRD, via een proxy moeten worden gewaarschuwd zonder enige verwerking in NMI. Het systeem van het `kubernetes.azure.com/managedby: aks` label in de naam ruimte _uitvoeren_ moet worden uitgesloten in `aad-pod-identity` door de AzurePodIdentityException CRD te configureren. Zie [Aad-pod-Identity voor een specifieke Pod of toepassing uitschakelen](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)voor meer informatie.
  Als u een uitzonde ring wilt configureren, installeert u de YAML van de [Mic-uitzonde ring](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

De invoeg toepassing Azure Policy vereist CPU-en geheugen bronnen om te kunnen worden gebruikt. Deze vereisten nemen toe als de grootte van een cluster toeneemt. Zie [Azure Policy aanbevelingen][policy-recommendations] voor algemene richt lijnen voor het gebruik van de Azure Policy-invoeg toepassing.

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Azure-beleid om Kubernetes peul te beveiligen

Na de installatie van de Azure Policy-invoeg toepassing worden standaard geen beleids regels toegepast.

Er zijn elf ingebouwde afzonderlijke Azure-beleids regels en twee ingebouwde initiatieven die specifiek zijn beveiligd in een AKS-cluster.
Elk beleid kan worden aangepast met een effect. Hier vindt u een volledige lijst met [AKS-beleid en de ondersteunde effecten][policy-samples]. Lees meer over [Azure Policy effecten](../governance/policy/concepts/effects.md).

Azure-beleid kan worden toegepast op het niveau van de beheer groep, het abonnement of de resource groep. Wanneer u een beleid toewijst op het niveau van de resource groep, moet u ervoor zorgen dat de resource groep van het doel AKS-cluster is geselecteerd binnen het bereik van het beleid. Elk cluster in het toegewezen bereik waarop de Azure Policy-invoeg toepassing is geïnstalleerd, is binnen het bereik van het beleid.

Als u [pod-beveiligings beleid (preview) ](use-pod-security-policies.md)gebruikt, leert u hoe u [kunt migreren naar Azure Policy en over andere gedrags verschillen](#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

### <a name="built-in-policy-initiatives"></a>Ingebouwde beleids initiatieven

Een initiatief in Azure Policy is een verzameling beleids definities die zijn afgestemd op het bereiken van een enkelvoudig overkoepelend doel. Het gebruik van initiatieven kan het beheer en de toewijzing van beleids regels in AKS-clusters vereenvoudigen. Een initiatief bestaat als één object, meer informatie over [Azure Policy initiatieven](../governance/policy/overview.md#initiative-definition).

Azure Policy voor Kubernetes biedt twee ingebouwde initiatieven, die van Peul, [basis lijn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) en [beperkt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00)zijn.

Beide ingebouwde initiatieven zijn gebaseerd op definities die worden gebruikt in [pod-beveiligings beleid van Kubernetes](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml).

|[Beheer van pod-beveiligings beleid](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Koppeling voor Azure Policy definitie| [Baseline Initiative](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) | [Beperkt initiatief](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) |
|---|---|---|---|
|Uitvoering van geprivilegieerde containers niet toestaan|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| Ja | Ja
|Gedeeld gebruik van host-naam ruimten niet toestaan|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| Ja | Ja
|Het gebruik van hostnetwerkadapters en poorten beperken|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| Ja | Ja
|Gebruik van het host-bestands systeem beperken|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| Ja | Ja
|Linux-mogelijkheden beperken tot de [standaardset](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | Ja | Ja
|Gebruik van gedefinieerde volume typen beperken|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Ja, toegestane volume typen zijn `configMap` , `emptyDir` , `projected` , `downwardAPI` , `persistentVolumeClaim`|
|Escalatie van bevoegdheden naar hoofdmap|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Yes |
|De gebruikers-en groeps-Id's van de container beperken|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Yes|
|Het toewijzen van een FSGroup die eigenaar is van de volumes van de pod beperken|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Ja, toegestane regels zijn `runAsUser: mustRunAsNonRoot` , `supplementalGroup: mustRunAs 1:65536` , `fsGroup: mustRunAs 1:65535` , `runAsGroup: mustRunAs 1:65535` .  |
|Vereist seccomp-profiel|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | Ja, allowedProfiles zijn * `docker/default` of `runtime/default` |

\* docker/default is afgeschaft in Kubernetes sinds v 1.11

### <a name="additional-optional-policies"></a>Aanvullende optionele beleids regels

Er zijn extra Azure-beleids regels die afzonderlijk kunnen worden toegepast buiten het Toep assen van een initiatief. U kunt deze beleids regels toevoegen naast initiatieven als aan uw vereisten niet wordt voldaan door de ingebouwde initiatieven.

|[Beheer van pod-beveiligings beleid](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Koppeling voor Azure Policy definitie| Gelden naast Baseline Initiative | Gelden naast beperkt initiatief |
|---|---|---|---|
|Het AppArmor-profiel definiëren dat wordt gebruikt door containers|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | Optioneel | Optioneel |
|Koppelen toestaan die niet alleen-lezen zijn|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | Optioneel | Optioneel |
|Beperken tot specifieke FlexVolume-Stuur Programma's|[Open bare Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | Optioneel: gebruik deze optie als u alleen FlexVolume-Stuur Programma's wilt beperken, maar niet de andere die zijn ingesteld met de optie gebruik van gedefinieerde volume typen beperken | Niet van toepassing: het beperkte initiatief omvat het beperken van het gebruik van gedefinieerde volume typen, waardoor niet alle FlexVolume-Stuur Programma's zijn toegestaan |

### <a name="unsupported-built-in-policies-for-managed-aks-clusters"></a>Niet-ondersteund ingebouwd beleid voor beheerde AKS-clusters

> [!NOTE]
> De volgende drie beleids regels worden **niet ondersteund in AKS** vanwege het aanpassen van aspecten die worden beheerd en beveiligd door aks als een beheerde service. Deze beleids regels worden speciaal gebouwd voor met Azure Arc verbonden clusters met onbeheerde besturings plannen.

|[Beheer van pod-beveiligings beleid](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)|
|---|
|De aangepaste SELinux-context van een container definiëren|
|Het sysctl-profiel beperken dat door containers wordt gebruikt|
|Standaard procedure koppelings typen worden gedefinieerd om de kwets baarheid voor aanvallen te beperken|

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>Naam ruimte uitsluiting

> [!WARNING]
> Het verschil in beheerders naam ruimten zoals uitvoeren-systeem moet worden uitgevoerd voor een cluster om in orde te blijven, waardoor het verwijderen van een vereiste naam ruimte uit de lijst met standaard uitgesloten naam ruimten ertoe kan leiden dat beleids schendingen worden geactiveerd vanwege een vereiste systeem-pod.

AKS vereist dat systeem peul wordt uitgevoerd op een cluster om essentiële services te bieden, zoals DNS-omzetting. Beleids regels die de functionaliteit van de pod beperken, kunnen invloed hebben op de mogelijkheid van systeem pod stabiliteit. Als gevolg hiervan worden de volgende naam ruimten **uitgesloten van beleids evaluatie tijdens toegangs aanvragen tijdens het maken, bijwerken en beleid controleren**. Dit zorgt ervoor dat nieuwe implementaties van deze naam ruimten worden uitgesloten van Azure-beleid.

1. kube-system
1. gate keeper-System
1. azure-arc
1. AKS-Peri Scope

Aanvullende aangepaste naam ruimten kunnen worden uitgesloten van de evaluatie tijdens het maken, bijwerken en controleren. Deze uitsluitingen moeten worden gebruikt als u een speciaal peul hebt dat wordt uitgevoerd in een goedgekeurde naam ruimte en u wilt voor komen dat controle schendingen worden geactiveerd.

## <a name="apply-the-baseline-initiative"></a>Het Baseline Initiative Toep assen

> [!TIP]
> Alle beleids regels zijn standaard ingesteld op een controle-effect. Effecten kunnen op elk moment worden bijgewerkt om te weigeren via Azure Policy.

Om het Baseline Initiative toe te passen, kunnen we via de Azure Portal toewijzen.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. Volg [deze koppeling naar het Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) om het Pod Security Baseline Initiative te controleren
1. Stel het **bereik** in op het abonnements niveau of alleen voor de resource groep met de AKS-clusters waarvoor de Azure Policy-invoeg toepassing is ingeschakeld
1. Selecteer de pagina **para meters** en werk het **effect** bij `audit` naar om `deny` nieuwe implementaties te blok keren die in strijd zijn met het basislijn initiatief
1. Voeg extra naam ruimten toe om uit te sluiten van de evaluatie tijdens het maken, bijwerken en controleren, maar [sommige naam ruimten worden geforceerd uitgesloten van de beleids evaluatie.](#namespace-exclusion) 
 ![ effect bijwerken](media/use-pod-security-on-azure-policy/update-effect.png)
1. Selecteer **controleren + maken** om het beleid in te dienen

Beleid voor bevestigen wordt toegepast op uw cluster door uit te voeren `kubectl get constrainttemplates` .

> [!NOTE]
> [Het kan Maxi maal 20 minuten duren om het beleid te synchroniseren](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) in elk cluster.

De uitvoer moet er ongeveer als volgt uitzien:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>Afwijzing van een privileged pod valideren

We gaan eerst testen wat er gebeurt wanneer u een pod plant met de beveiligings context van `privileged: true` . Met deze beveiligings context worden de bevoegdheden van de pod geëscaleerd. Met het Baseline Initiative zijn privileged peul niet toegestaan. de aanvraag wordt dus geweigerd als gevolg van een geweigerde implementatie.

Maak een bestand `nginx-privileged.yaml` met de naam en plak het volgende YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl apply -f nginx-privileged.yaml
```

Zoals u verwacht, kan de pod niet worden gepland, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Het Pod is niet bereikbaar voor de plannings fase, dus er zijn geen resources om te verwijderen voordat u verdergaat.

## <a name="test-creation-of-an-unprivileged-pod"></a>Het maken van een niet-gemachtigde pod testen

In het vorige voor beeld heeft de container installatie kopie automatisch geprobeerd de root te gebruiken om NGINX te binden aan poort 80. Deze aanvraag is geweigerd door de baseline Policy Initiative, waardoor de pod niet kan worden gestart. Nu kunt u dezelfde NGINX-pod uitvoeren zonder privileged Access.

Maak een bestand `nginx-unprivileged.yaml` met de naam en plak het volgende YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl apply -f nginx-unprivileged.yaml
```

De Pod is gepland. Wanneer u de status van de pod controleert met behulp van de [kubectl Get peul][kubectl-get] opdracht, wordt de pod *uitgevoerd*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

In dit voor beeld ziet u het Baseline-initiatief dat alleen van invloed is op implementaties die beleids regels in de verzameling schenden. Toegestane implementaties blijven functioneren.

Verwijder de NGINX niet-gemachtigde pod met de opdracht [kubectl delete][kubectl-delete] en geef de naam van uw yaml-manifest op:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-policies-and-the-azure-policy-add-on"></a>Beleids regels en de invoeg toepassing Azure Policy uitschakelen

Het Baseline Initiative verwijderen:

1. Ga naar het deel venster beleid op de Azure Portal
1. Selecteer **toewijzingen** in het linkerdeel venster
1. Klik op de '... ' knop naast het basislijn profiel
1. Selecteer toewijzing verwijderen

![Toewijzing verwijderen](media/use-pod-security-on-azure-policy/delete-assignment.png)

Als u de invoeg toepassing Azure Policy wilt uitschakelen, gebruikt u de opdracht [AZ AKS Disable-addons][az-aks-disable-addons] .

```azurecli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Meer informatie over het verwijderen [van de invoeg toepassing Azure Policy van Azure Portal](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks).

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Migreren van Kubernetes pod-beveiligings beleid naar Azure Policy

Als u wilt migreren vanuit pod-beveiligings beleid, moet u de volgende acties uitvoeren op een cluster.

1. Het [pod-beveiligings beleid](use-pod-security-policies.md#clean-up-resources) op het cluster uitschakelen
1. De [invoeg toepassing Azure Policy][kubernetes-policy-reference] inschakelen
1. Schakel het gewenste Azure-beleid in op basis van het [beschik bare ingebouwde beleid][policy-samples]

Hieronder vindt u een overzicht van de gedrags wijzigingen tussen pod-beveiligings beleid en Azure Policy.

|Scenario| Pod-beveiligings beleid | Azure Policy |
|---|---|---|
|Installatie|Functie pod-beveiligings beleid inschakelen |Invoeg toepassing inschakelen Azure Policy
|Beleid implementeren| Resource voor pod-beveiligings beleid implementeren| Wijs Azure-beleid toe aan het bereik van het abonnement of de resource groep. De Azure Policy-invoeg toepassing is vereist voor Kubernetes-resource toepassingen.
| Standaard beleid | Wanneer pod-beveiligings beleid is ingeschakeld in AKS, worden er standaard privileged en beleid voor onbeperkte toegang toegepast. | Er wordt geen standaard beleid toegepast door de invoeg toepassing Azure Policy in te scha kelen. U moet expliciet beleid inschakelen in Azure Policy.
| Wie beleid kan maken en toewijzen | Er wordt een pod-beveiligings beleids resource gemaakt door Cluster beheerder | Gebruikers moeten beschikken over de machtiging ' eigenaar ' of ' Inzender ' van het resource beleid voor de cluster resource groep AKS. -Via-API kunnen gebruikers beleid toewijzen op het AKS-cluster bron bereik. De gebruiker moet mini maal de machtigingen ' eigenaar ' of ' Inzender voor resource beleid ' hebben voor de AKS-cluster bron. -In de Azure Portal kunnen beleids regels worden toegewezen op het niveau van de beheer groep/het abonnement/de resource groep.
| Beleid voor autorisatie| Gebruikers en service accounts vereisen expliciete machtigingen voor het gebruik van pod-beveiligings beleid. | Er is geen aanvullende toewijzing vereist om beleid te autoriseren. Zodra het beleid is toegewezen in azure, kunnen alle cluster gebruikers deze beleids regels gebruiken.
| Toepas baarheid van beleid | De gebruiker met beheerders rechten negeert de afdwinging van pod-beveiligings beleid. | Alle gebruikers (beheerder & niet-beheerder) zien hetzelfde beleid. Er is geen speciale behuizing op basis van gebruikers. De beleids toepassing kan worden uitgesloten op het niveau van de naam ruimte.
| Beleids bereik | Het Pod-beveiligings beleid is niet in een naam ruimte | Beperkings sjablonen die door Azure Policy worden gebruikt, zijn niet in een naam ruimte.
| Actie voor weigeren/controleren/mutatie | Het Pod-beveiligings beleid ondersteunt alleen acties voor weigeren. Mutatie kan worden uitgevoerd met standaard waarden bij het maken van aanvragen. Validatie kan worden uitgevoerd tijdens update aanvragen.| Azure Policy ondersteunt zowel controle & acties weigeren. Mutatie wordt nog niet ondersteund, maar gepland.
| Naleving van pod-beveiligings beleid | Er is geen zicht baarheid van de naleving van de voor Schriften die bestonden voordat het Pod-beveiligings beleid is ingeschakeld. Niet-compatibele peulen die zijn gemaakt nadat pod-beveiligings beleid is ingeschakeld, worden geweigerd. | Het niet-compatibele peul dat bestond voordat een Azure-beleid werd toegepast, zou in beleids schendingen worden weer gegeven. Niet-compatibele peulen die zijn gemaakt na het inschakelen van Azure-beleid, worden geweigerd als beleids regels zijn ingesteld met een weigerings effect.
| Beleids regels op het cluster weer geven | `kubectl get psp` | `kubectl get constrainttemplate` -Alle beleids regels worden geretourneerd.
| Pod beveiligings beleid standaard-privileged | Wanneer u de functie inschakelt, wordt er standaard een privileged pod Security Policy resource gemaakt. | De geprivilegieerde modus impliceert geen beperking. als gevolg hiervan is het gelijk aan geen enkele Azure Policy toewijzing.
| [Pod-beveiligings beleid-standaard-basis lijn/standaard](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | Gebruiker installeert een basis lijn voor het Pod-beveiligings beleid. | Azure Policy biedt een [ingebouwd Baseline-initiatief](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) dat is gekoppeld aan het Pod-beveiligings beleid van de basis lijn.
| [Pod-beveiligings beleid Standard-beperkt](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | Gebruiker installeert een door Pod beveiligings beleid beperkte resource. | Azure Policy biedt een [ingebouwd beperkt initiatief](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) dat is gekoppeld aan het beperkte pod-beveiligings beleid.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een Azure-beleid kunt Toep assen, waardoor het verhoogde toegangs niveau wordt beperkt om te voor komen dat bevoegde toegang wordt gebruikt. Er zijn veel beleids regels die kunnen worden toegepast, zoals beleid voor het beperken van het gebruik van volumes. Zie de [Azure Policy voor Kubernetes-referentie documenten][kubernetes-policy-reference]voor meer informatie over de beschik bare opties.

Zie voor meer informatie over het beperken van pod-netwerk verkeer [beveiligde verkeer tussen peulen met netwerk beleid in AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity
[aad-pod-identity-exception]: https://azure.github.io/aad-pod-identity/docs/configure/application_exception

<!-- LINKS - internal -->
[policy-recommendations]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-limitations]: ../governance/policy/concepts/policy-for-kubernetes.md?#limitations
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete