---
title: Pod-beveiligings beleid gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over het beheren van pod-toelatingen met behulp van PodSecurityPolicy in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: 4e72bd28910f471656feb27d10c123930305494e
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031219"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Voor beeld: uw cluster beveiligen met behulp van pod-beveiligings beleid in azure Kubernetes service (AKS)

> [!WARNING]
> **De functie die in dit document wordt beschreven, Pod-beveiligings beleid (preview) [, begint met](https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/) Kubernetes versie 1,21 en wordt verwijderd in versie 1,25.** Als Kubernetes upstream benadert deze mijl paal, dan zal de Kubernetes-Community zich bezig gehouden met het documenteren van levensvat bare alternatieven. De vorige aankondiging van de afschaffing werd gedaan op het moment dat er geen geschikte optie is voor klanten. Nu de Kubernetes-community aan een ander werkt, is het niet meer mogelijk om Kubernetes te vervangen. 
>
> Nadat de functie voor beveiligingsbeleid voor pods (preview) is afgeschaft, moet u de functie op alle bestaande clusters uitschakelen met behulp van de afgeschafte functie om toekomstige clusterupgrades uit te voeren en de ondersteuning van Azure te kunnen blijven gebruiken.

Als u de beveiliging van uw AKS-cluster wilt verbeteren, kunt u het aantal peulen dat kan worden gepland, beperken. De meeste resources die u niet toestaat, kunnen niet worden uitgevoerd in het AKS-cluster. U definieert deze toegang met behulp van pod-beveiligings beleid. Dit artikel laat u zien hoe u pod-beveiligings beleid kunt gebruiken om de implementatie van een van de peulen in AKS te beperken.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

U moet de Azure CLI-versie 2.0.61 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u pod-beveiligings beleid wilt gebruiken, hebt u de *AKS-preview cli-* extensie versie 0.4.1 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Pod-functie provider voor beveiligings beleid registreren

**Dit document en deze functie worden op 15 oktober 2020 ingesteld voor afschaffing.**

Als u een AKS-cluster wilt maken of bijwerken om pod-beveiligings beleid te gebruiken, moet u eerst een functie vlag voor uw abonnement inschakelen. Als u de functie vlag *PodSecurityPolicyPreview* wilt registreren, gebruikt u de opdracht [AZ feature REGI ster][az-feature-register] , zoals weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Het duurt enkele minuten voordat de status is *geregistreerd*. U kunt de registratiestatus controleren met behulp van de opdracht [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Overzicht van pod-beveiligings beleid

In een Kubernetes-cluster wordt een toegangs controller gebruikt om aanvragen voor de API-server te onderscheppen wanneer een bron moet worden gemaakt. De toegangs controller kan vervolgens de resource aanvraag *valideren* op basis van een set regels of de resource *mutate* om de implementatie parameters te wijzigen.

*PodSecurityPolicy* is een toegangs controller die een pod-specificatie valideert die voldoet aan uw gedefinieerde vereisten. Deze vereisten kunnen het gebruik van geprivilegieerde containers, toegang tot bepaalde typen opslag beperken of de gebruiker of groep die de container kan uitvoeren als. Wanneer u een resource probeert te implementeren waarbij de pod-specificaties niet voldoen aan de vereisten die worden beschreven in het beveiligings beleid van Pod, wordt de aanvraag geweigerd. Deze mogelijkheid om te bepalen wat de peulen kunnen worden gepland in het AKS-cluster, voor komt dat er mogelijke beveiligings problemen of escalaties van bevoegdheden.

Wanneer u pod-beveiligings beleid inschakelt in een AKS-cluster, worden er een aantal standaard beleids regels toegepast. Deze standaard beleidsregels bieden een out-of-the-box-ervaring om te definiëren wat het peul kan worden gepland. Cluster gebruikers kunnen echter problemen ondervinden bij het implementeren van het gehele beleid totdat u uw eigen beleids regels definieert. De aanbevolen aanpak is:

* Een AKS-cluster maken
* Uw eigen pod-beveiligings beleid definiëren
* De functie pod-beveiligings beleid inschakelen

Als u wilt weer geven hoe de standaard beleids regels pod-implementaties beperken, wordt in dit artikel eerst de functie pod-beveiligings beleid ingeschakeld. vervolgens maakt u een aangepast beleid.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Gedrag verandert tussen pod-beveiligings beleid en Azure Policy

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

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Pod-beveiligings beleid inschakelen op een AKS-cluster

U kunt pod-beveiligings beleid in-of uitschakelen met behulp van de opdracht [AZ AKS update][az-aks-update] . In het volgende voor beeld wordt pod-beveiligings beleid ingeschakeld op de cluster naam *myAKSCluster* in de resource groep met de naam *myResourceGroup*.

> [!NOTE]
> Schakel voor gebruik in de praktijk het beveiligings beleid pod pas in als u uw eigen aangepaste beleids regels hebt gedefinieerd. In dit artikel schakelt u pod-beveiligings beleid als eerste stap in om te zien hoe de standaard beleidsregels de pod-implementaties beperken.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Standaard AKS-beleid

Wanneer u pod-beveiligings beleid inschakelt, maakt AKS één standaard beleid met de naam *privileged*. Bewerk of verwijder het standaard beleid niet. Maak in plaats daarvan uw eigen beleid voor het definiëren van de instellingen die u wilt beheren. Laten we eerst kijken naar wat deze standaard beleidsregels zijn, hoe ze pod-implementaties beïnvloeden.

Als u de beschik bare beleids regels wilt weer geven, gebruikt u de opdracht [kubectl Get PSP][kubectl-get] , zoals wordt weer gegeven in het volgende voor beeld

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Het beveiligings beleid *privileged* pod wordt toegepast op elke geverifieerde gebruiker in het AKS-cluster. Deze toewijzing wordt beheerd door ClusterRoles en ClusterRoleBindings. Gebruik de opdracht [kubectl Get rolebindings][kubectl-get] en zoek naar de *standaard: Privileged:* binding in de *uitvoeren-systeem* naam ruimte:

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Zoals wordt weer gegeven in de volgende verkorte uitvoer, wordt de *PSP: Privileged* ClusterRole toegewezen aan elk *systeem: geverifieerde* gebruikers. Deze mogelijkheid biedt een basis niveau van bevoegdheid zonder dat uw eigen beleid wordt gedefinieerd.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

Het is belang rijk om te begrijpen hoe deze standaard beleidsregels communiceren met gebruikers aanvragen voor het plannen van Peul voordat u begint met het maken van uw eigen pod-beveiligings beleid. In de volgende paar secties gaan we een planning maken om deze standaard beleidsregels in actie te zien.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Een test gebruiker maken in een AKS-cluster

Wanneer u de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] gebruikt, worden de *beheerders* referenties voor het AKS-cluster standaard toegevoegd aan uw `kubectl` configuratie. De gebruiker met beheerders rechten negeert de afdwinging van pod-beveiligings beleid. Als u Azure Active Directory-integratie gebruikt voor uw AKS-clusters, kunt u zich aanmelden met de referenties van een gebruiker die geen beheerder is om het afdwingen van beleid in actie te zien. In dit artikel maken we een test gebruikers account in het AKS-cluster dat u kunt gebruiken.

Maak een voor beeld van een naam ruimte met de naam *PSP-AKS* voor test resources met behulp van de [kubectl maken naam ruimte][kubectl-create] opdracht. Maak vervolgens een service account met de naam niet *-beheerder-gebruiker* met behulp van de kubectl-opdracht [serviceaccount maken][kubectl-create] :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Maak vervolgens een RoleBinding voor de niet *-beheerder* om basis acties uit te voeren in de naam ruimte met behulp van de [kubectl Create RoleBinding][kubectl-create] opdracht:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Alias opdrachten maken voor beheerders en gebruikers die geen beheerder zijn

Als u het verschil tussen de normale gebruiker met beheerders rechten wilt markeren wanneer u `kubectl` en de niet-beheerder gebruiker hebt gemaakt in de vorige stappen, maakt u twee opdracht regel aliassen:

* De alias voor de **kubectl-beheerder** is de gewone beheerder en ligt binnen het bereik van de naam ruimte *PSP-AKS* .
* De **kubectl-nonadminuser** -alias is voor de niet *-beheerder-gebruiker* die in de vorige stap is gemaakt en is afgestemd op de *PSP-AKS-* naam ruimte.

Maak deze twee aliassen, zoals wordt weer gegeven in de volgende opdrachten:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Het maken van een privileged pod testen

We gaan eerst testen wat er gebeurt wanneer u een pod plant met de beveiligings context van `privileged: true` . Met deze beveiligings context worden de bevoegdheden van de pod geëscaleerd. In de vorige sectie waarin het standaard beleid voor AKS-pod wordt weer gegeven, moet het *bevoegdheids* beleid deze aanvraag weigeren.

Maak een bestand `nginx-privileged.yaml` met de naam en plak het volgende YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

De pod kan niet worden gepland, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Het Pod is niet bereikbaar voor de plannings fase, dus er zijn geen resources om te verwijderen voordat u verdergaat.

## <a name="test-creation-of-an-unprivileged-pod"></a>Het maken van een niet-gemachtigde pod testen

In het vorige voor beeld heeft de pod-specificatie geautoriseerde escalatie aangevraagd. Deze aanvraag is geweigerd door het standaard beveiligings beleid van de *bevoegdheid* Pod, zodat de pod niet kan worden gepland. We gaan nu proberen om dezelfde NGINX-pod uit te voeren zonder de escalatie aanvraag van de bevoegdheid.

Maak een bestand `nginx-unprivileged.yaml` met de naam en plak het volgende YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

De pod kan niet worden gepland, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Het Pod is niet bereikbaar voor de plannings fase, dus er zijn geen resources om te verwijderen voordat u verdergaat.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Het maken van een pod met een specifieke gebruikers context testen

In het vorige voor beeld heeft de container installatie kopie automatisch geprobeerd de root te gebruiken om NGINX te binden aan poort 80. Deze aanvraag is geweigerd door het standaard pod-beveiligings beleid voor *bevoegdheden* , waardoor de pod niet kan worden gestart. We gaan nu proberen om dezelfde NGINX-pod uit te voeren met een specifieke gebruikers context, zoals `runAsUser: 2000` .

Maak een bestand `nginx-unprivileged-nonroot.yaml` met de naam en plak het volgende YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

De pod kan niet worden gepland, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Het Pod is niet bereikbaar voor de plannings fase, dus er zijn geen resources om te verwijderen voordat u verdergaat.

## <a name="create-a-custom-pod-security-policy"></a>Een aangepast pod-beveiligings beleid maken

Nu u het gedrag van het standaard beveiligings beleid voor pod hebt gezien, bieden we een manier om de niet *-beheerder* in staat te stellen om heel goed te plannen.

We gaan een beleid maken voor het afwijzen van het Peul waarvoor privileged Access moet worden aangevraagd. Andere opties, zoals *runAsUser* of toegestane *volumes*, worden niet expliciet beperkt. Dit type beleid weigert een aanvraag voor bevoegde toegang, maar anders kan het cluster de aangevraagde peul uitvoeren.

Maak een bestand `psp-deny-privileged.yaml` met de naam en plak het volgende YAML-manifest:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Maak het beleid met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de naam van het yaml-manifest op:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Als u de beschik bare beleids regels wilt weer geven, gebruikt u de opdracht [kubectl Get PSP][kubectl-get] , zoals in het volgende voor beeld wordt weer gegeven. Vergelijk het beleid voor *PSP-deny-privileges* met het standaard *machtigings* beleid dat in de voor gaande voor beelden is afgelegd om een pod te maken. Alleen het gebruik van *PRIV* -escalatie wordt door uw beleid geweigerd. Er zijn geen beperkingen voor de gebruiker of groep voor het beleid voor *PSP-deny-privileged* .

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Gebruikers account toestaan het aangepaste pod-beveiligings beleid te gebruiken

In de vorige stap hebt u een pod-beveiligings beleid gemaakt waarmee u de peuling kunt afwijzen waarvoor privileged Access moet worden aangevraagd. Als u wilt toestaan dat het beleid wordt gebruikt, maakt u een *rol* of een *ClusterRole*. Vervolgens koppelt u een van deze rollen met behulp van een *RoleBinding* of *ClusterRoleBinding*.

Voor dit voor beeld maakt u een ClusterRole waarmee u het beleid *PSP-deny-privileged* kunt *gebruiken* dat u in de vorige stap hebt gemaakt. Maak een bestand `psp-deny-privileged-clusterrole.yaml` met de naam en plak het volgende YAML-manifest:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Maak de ClusterRole met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Maak nu een ClusterRoleBinding om de ClusterRole te gebruiken die u in de vorige stap hebt gemaakt. Maak een bestand `psp-deny-privileged-clusterrolebinding.yaml` met de naam en plak het volgende YAML-manifest:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Maak een ClusterRoleBinding met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> In de eerste stap van dit artikel is de functie pod-beveiligings beleid ingeschakeld op het AKS-cluster. De aanbevolen procedure is om alleen de functie pod-beveiligings beleid in te scha kelen nadat u uw eigen beleid hebt gedefinieerd. Dit is de fase waarin u de functie pod-beveiligings beleid inschakelt. Er zijn een of meer aangepaste beleids regels gedefinieerd en er zijn gebruikers accounts aan die beleids regels gekoppeld. Nu kunt u de functie pod-beveiligings beleid veilig inschakelen en de problemen die worden veroorzaakt door het standaard beleid minimaliseren.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Het maken van een niet-gemachtigd pod opnieuw testen

Als uw aangepaste pod-beveiligings beleid is toegepast en een binding voor het gebruikers account voor het gebruik van het beleid, probeert u opnieuw een niet-gemachtigd pod te maken. Gebruik hetzelfde `nginx-privileged.yaml` manifest om de Pod te maken met behulp van de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

De Pod is gepland. Wanneer u de status van de pod controleert met behulp van de [kubectl Get peul][kubectl-get] opdracht, wordt de pod *uitgevoerd*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

In dit voor beeld ziet u hoe u een aangepast pod-beveiligings beleid kunt maken om de toegang tot het AKS-cluster te definiëren voor verschillende gebruikers of groepen. De standaard AKS-beleids regels bieden een nauw keurig beheer van wat er kan worden uitgevoerd. u kunt dus uw eigen aangepaste beleids regels maken om de benodigde beperkingen op te geven.

Verwijder de NGINX niet-gemachtigde pod met de opdracht [kubectl delete][kubectl-delete] en geef de naam van uw yaml-manifest op:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u pod-beveiligings beleid wilt uitschakelen, gebruikt u de opdracht [AZ AKS update][az-aks-update] opnieuw. In het volgende voor beeld wordt het Pod-beveiligings beleid uitgeschakeld voor de cluster naam *myAKSCluster* in de resource groep met de naam *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Verwijder vervolgens de ClusterRole en ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Verwijder het beveiligings beleid met behulp van de opdracht [kubectl delete][kubectl-delete] en geef de naam van het yaml-manifest op:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Verwijder ten slotte de *PSP-AKS-* naam ruimte:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een pod-beveiligings beleid kunt maken om te voor komen dat privileged Access wordt gebruikt. Er zijn veel functies die een beleid kan afdwingen, zoals het type volume of de RunAs-gebruiker. Zie voor meer informatie over de beschik bare opties de [Kubernetes pod Security Policy Reference docs][kubernetes-policy-reference].

Zie voor meer informatie over het beperken van pod-netwerk verkeer [beveiligde verkeer tussen peulen met netwerk beleid in AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
<!-- LINKS - internal -->
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
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md
