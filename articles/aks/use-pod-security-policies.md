---
title: Beveiligingsbeleid voor pods gebruiken in Azure Kubernetes Service (AKS)
description: Leer hoe u pod-toegangen kunt controleren met PodSecurityPolicy in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: d70b8e8efbf96e50575845ac88993012fed936d5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767417"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Preview: uw cluster beveiligen met beveiligingsbeleid voor pods in Azure Kubernetes Service (AKS)

> [!WARNING]
> **De functie die in dit document wordt beschreven, beveiligingsbeleid voor pods (preview), begint met afschaffing met Kubernetes-versie 1.21, met verwijdering in versie 1.25. [](https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/)** Naarmate Kubernetes Upstream deze mijlpaal nadert, zal de Kubernetes-community werken aan bruikbare alternatieven. De vorige afschaffingsaankondiging is op dat moment gedaan omdat er geen bruikbare optie voor klanten was. Nu de Kubernetes-community aan een alternatief werkt, is er geen noodzaak meer om Kubernetes af te afgeschaft. 
>
> Nadat de functie voor beveiligingsbeleid voor pods (preview) is afgeschaft, moet u de functie op alle bestaande clusters uitschakelen met behulp van de afgeschafte functie om toekomstige clusterupgrades uit te voeren en de ondersteuning van Azure te kunnen blijven gebruiken.

Om de beveiliging van uw AKS-cluster te verbeteren, kunt u beperken welke pods kunnen worden gepland. Pods die resources aanvragen die u niet toestaat, kunnen niet worden uitgevoerd in het AKS-cluster. U definieert deze toegang met behulp van beveiligingsbeleid voor pods. In dit artikel wordt beschreven hoe u beveiligingsbeleid voor pods gebruikt om de implementatie van pods in AKS te beperken.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

Azure CLI versie 2.0.61 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u beveiligingsbeleid voor pods wilt gebruiken, hebt u de *CLI-extensie aks-preview* versie 0.4.1 of hoger nodig. Installeer de *Azure CLI-extensie aks-preview* met behulp van de [opdracht az extension add][az-extension-add] en controleer vervolgens op beschikbare updates met de opdracht az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Functieprovider voor beveiligingsbeleid voor pods registreren

**Dit document en deze functie zijn ingesteld voor afschaffing op 15 oktober 2020.**

Als u een AKS-cluster wilt maken of bijwerken om beveiligingsbeleid voor pods te gebruiken, moet u eerst een functievlag inschakelen voor uw abonnement. Als u de *functievlag PodSecurityPolicyPreview* wilt registreren, gebruikt u de [opdracht az feature register,][az-feature-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Het duurt enkele minuten voordat de status Geregistreerd *we weergeven.* U kunt de registratiestatus controleren met behulp van de opdracht [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider *Microsoft.ContainerService* met behulp van [de opdracht az provider][az-provider-register] register:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Overzicht van beveiligingsbeleid voor pods

In een Kubernetes-cluster wordt een toegangscontroller gebruikt om aanvragen naar de API-server te onderscheppen wanneer een resource moet worden gemaakt. De toegangscontroller kan vervolgens *de* resourceaanvraag valideren op basis van een set regels of de resource wijzigen om implementatieparameters te wijzigen. 

*PodSecurityPolicy* is een toegangscontroller die controleert of een podspecificatie voldoet aan uw gedefinieerde vereisten. Deze vereisten kunnen het gebruik van bevoegde containers beperken, toegang tot bepaalde opslagtypen of de gebruiker of groep die de container kan uitvoeren als. Wanneer u probeert een resource te implementeren waarbij de podspecificaties niet voldoen aan de vereisten die worden beschreven in het beveiligingsbeleid voor pods, wordt de aanvraag geweigerd. Deze mogelijkheid om te bepalen welke pods in het AKS-cluster kunnen worden gepland, voorkomt mogelijke beveiligingsproblemen of escalaties van bevoegdheden.

Wanneer u beveiligingsbeleid voor pods in een AKS-cluster inschakelen, wordt een aantal standaardbeleidsregels toegepast. Deze standaardbeleidsregels bieden een out-of-the-box-ervaring om te definiëren welke pods kunnen worden gepland. Clustergebruikers kunnen echter problemen krijgen met het implementeren van pods totdat u uw eigen beleidsregels definieert. De aanbevolen aanpak is:

* Een AKS-cluster maken
* Uw eigen beveiligingsbeleid voor pods definiëren
* De functie beveiligingsbeleid voor pods inschakelen

Om te laten zien hoe de standaardbeleidsregels podimplementaties beperken, gaan we in dit artikel eerst de functie beveiligingsbeleid voor pods inschakelen en vervolgens een aangepast beleid maken.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Gedragswijzigingen tussen beveiligingsbeleid voor pods en Azure Policy

Hieronder vindt u een samenvatting van de gedragswijzigingen tussen beveiligingsbeleid voor pods en Azure Policy.

|Scenario| Beveiligingsbeleid voor pods | Azure Policy |
|---|---|---|
|Installatie|Functie voor beveiligingsbeleid voor pods inschakelen |Een Azure Policy inschakelen
|Beleid implementeren| Resource voor beveiligingsbeleid voor pods implementeren| Wijs Azure-beleid toe aan het bereik van het abonnement of de resourcegroep. De Azure Policy-invoegtoepassingen is vereist voor Kubernetes-resourcetoepassingen.
| Standaardbeleid | Wanneer beveiligingsbeleid voor pods is ingeschakeld in AKS, worden de standaardbeleidsregels Privileged en Unrestricted toegepast. | Er worden geen standaardbeleidsregels toegepast door de Azure Policy in te stellen. U moet beleidsregels expliciet inschakelen in Azure Policy.
| Wie kan beleid maken en toewijzen | Clusterbeheerder maakt een beveiligingsbeleidsresource voor pods | Gebruikers moeten minimaal de rol 'eigenaar' of 'Inzender voor resourcebeleid' hebben voor de AKS-clusterresourcegroep. - Via de API kunnen gebruikers beleid toewijzen op het resourcebereik van het AKS-cluster. De gebruiker moet minimaal de machtiging 'eigenaar' of 'Inzender voor resourcebeleid' hebben voor de AKS-clusterresource. - In de Azure Portal kunnen beleidsregels worden toegewezen op beheergroep-/abonnements-/resourcegroepniveau.
| Beleid autoriseren| Gebruikers en serviceaccounts hebben expliciete machtigingen nodig voor het gebruik van beveiligingsbeleid voor pods. | Er is geen aanvullende toewijzing vereist om beleid te autorissen. Zodra beleid is toegewezen in Azure, kunnen alle clustergebruikers dit beleid gebruiken.
| Toepasbaarheid van beleid | De gebruiker met beheerdersrechten slaat het afdwingen van beveiligingsbeleid voor pods over. | Alle gebruikers (& niet-beheerder) zien hetzelfde beleid. Er is geen speciaal casing op basis van gebruikers. Beleidstoepassing kan worden uitgesloten op het niveau van de naamruimte.
| Beleidsbereik | Beveiligingsbeleid voor pods is geen naamruimte | Beperkingssjablonen die door Azure Policy worden gebruikt, zijn geen naamruimte.
| Actie Weigeren/controleren/actie | Beveiligingsbeleid voor pods ondersteunt alleen acties voor weigeren. U kunt de standaardwaarden gebruiken voor het maken van aanvragen. Validatie kan worden uitgevoerd tijdens updateaanvragen.| Azure Policy biedt ondersteuning voor beide & acties voor weigeren. De plannen worden nog niet ondersteund, maar wel gepland.
| Naleving van beveiligingsbeleid voor pods | Er is geen zichtbaarheid van de naleving van pods die bestonden voordat het beveiligingsbeleid voor pods werd inschakelen. Niet-compatibele pods die zijn gemaakt na het inschakelen van het beveiligingsbeleid voor pods, worden geweigerd. | Niet-compatibele pods die bestonden voordat Azure-beleid werd toegepast, worden in beleidsschendingen weer geven. Niet-compatibele pods die zijn gemaakt na het inschakelen van Azure-beleid, worden geweigerd als beleid is ingesteld met een weigereffect.
| Beleidsregels in het cluster weergeven | `kubectl get psp` | `kubectl get constrainttemplate` - Alle beleidsregels worden geretourneerd.
| Standaard beveiligingsbeleid voor pods - Privileged | Er wordt standaard een resource voor het beveiligingsbeleid voor bevoorrechte pods gemaakt bij het inschakelen van de functie. | Privileged Mode impliceert geen beperking, wat betekent dat het gelijk is aan het niet hebben van Azure Policy toewijzing.
| [Standaard beveiligingsbeleid voor pods - basislijn/standaard](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | De gebruiker installeert een basislijnresource voor beveiligingsbeleid voor pods. | Azure Policy biedt een [ingebouwd basislijninitiatief dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) is gebaseerd op het basislijnbeveiligingsbeleid voor pods.
| [Standaard beveiligingsbeleid voor pods - Beperkt](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | De gebruiker installeert een beperkte resource voor beveiligingsbeleid voor pods. | Azure Policy biedt een [ingebouwd, beperkt initiatief dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) is toe te staan aan het beperkte beveiligingsbeleid voor pods.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Beveiligingsbeleid voor pods inschakelen op een AKS-cluster

U kunt het beveiligingsbeleid voor pods in- of uitschakelen met de [opdracht az aks update.][az-aks-update] In het volgende voorbeeld wordt podbeveiligingsbeleid voor de clusternaam *myAKSCluster* in de resourcegroep met de *naam myResourceGroup mogelijk.*

> [!NOTE]
> Schakel voor het echte gebruik het beveiligingsbeleid voor pods pas in als u uw eigen aangepaste beleidsregels hebt gedefinieerd. In dit artikel gaat u het beveiligingsbeleid voor pods inschakelen als eerste stap om te zien hoe het standaardbeleid podimplementaties beperkt.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Standaard AKS-beleid

Wanneer u het beveiligingsbeleid voor pods inschakelen, maakt AKS één standaardbeleid met de naam *privileged*. Bewerk of verwijder het standaardbeleid niet. Maak in plaats daarvan uw eigen beleidsregels die de instellingen definiëren die u wilt beheren. Laten we eerst eens kijken naar de manier waarop deze standaardbeleidsregels van invloed zijn op podimplementaties.

Als u de beschikbare beleidsregels wilt weergeven, gebruikt u [de opdracht kubectl get psp,][kubectl-get] zoals wordt weergegeven in het volgende voorbeeld

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Het *beveiligingsbeleid* voor bevoegde pods wordt toegepast op elke geverifieerde gebruiker in het AKS-cluster. Deze toewijzing wordt beheerd door ClusterRoles en ClusterRoleBindings. Gebruik de [opdracht kubectl get rolebindings][kubectl-get] en zoek naar *de binding default:privileged:* in de *naamruimte kube-system:*

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Zoals wordt weergegeven in de volgende verkorte uitvoer, wordt *de psp:privileged* ClusterRole toegewezen aan alle *systeem:geverifieerde* gebruikers. Deze mogelijkheid biedt een basisniveau van bevoegdheden zonder dat uw eigen beleid wordt gedefinieerd.

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

Het is belangrijk om te begrijpen hoe deze standaardbeleidsregels communiceren met gebruikersaanvragen om pods te plannen voordat u begint met het maken van uw eigen beveiligingsbeleid voor pods. In de volgende secties plannen we een aantal pods om deze standaardbeleidsregels in actie te zien.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Een testgebruiker maken in een AKS-cluster

Wanneer u de opdracht [az aks get-credentials][az-aks-get-credentials] gebruikt, worden de beheerdersreferenties voor het AKS-cluster standaard toegevoegd aan uw  `kubectl` configuratie. De gebruiker met beheerdersrechten slaat het afdwingen van beveiligingsbeleid voor pods over. Als u Azure Active Directory voor uw AKS-clusters gebruikt, kunt u zich aanmelden met de referenties van een niet-beheerder om het afdwingen van beleid in actie te zien. In dit artikel gaan we een testgebruikersaccount maken in het AKS-cluster dat u kunt gebruiken.

Maak een voorbeeldnaamruimte met de *naam psp-aks* voor testbronnen met behulp van de [opdracht kubectl create namespace.][kubectl-create] Maak vervolgens een serviceaccount met de *naam nonadmin-user met behulp* van de opdracht [kubectl create serviceaccount:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Maak vervolgens een RoleBinding voor de *niet-hoofdgebruiker* om basisacties uit te voeren in de naamruimte met behulp van de [opdracht kubectl create rolebinding:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Aliasopdrachten maken voor gebruiker met beheerdersrechten en niet-beheerders

Maak twee opdrachtregelaliassen om het verschil te markeren tussen de gewone gebruiker met beheerdersrechten bij het gebruik van en de niet-beheerder die in de vorige stappen `kubectl` is gemaakt:

* De **alias kubectl-admin** is voor de gewone gebruiker met beheerdersrechten en is beperkt tot de *naamruimte psp-aks.*
* De alias **kubectl-nonadminuser** is voor de *niet-gebruiker* die in de vorige stap is gemaakt en is beperkt tot de *naamruimte psp-aks.*

Maak deze twee aliassen, zoals wordt weergegeven in de volgende opdrachten:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Het maken van een bevoorrechte pod testen

Laten we eerst testen wat er gebeurt wanneer u een pod met de beveiligingscontext van `privileged: true` plant. Met deze beveiligingscontext worden de bevoegdheden van de pod geëscaleerd. In de vorige sectie waarin het standaardbeveiligingsbeleid voor AKS-pods werd weergegeven, moet het *bevoegdheidsbeleid* deze aanvraag weigeren.

Maak een bestand met de `nginx-privileged.yaml` naam en plak het volgende YAML-manifest:

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

Maak de pod met behulp van [de opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

De pod kan niet worden gepland, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

De pod bereikt de planningsfase niet, dus er zijn geen resources om te verwijderen voordat u verder gaat.

## <a name="test-creation-of-an-unprivileged-pod"></a>Het maken van een niet-geprilegeerde pod testen

In het vorige voorbeeld heeft de podspecificatie bevoegde escalatie aangevraagd. Deze aanvraag wordt geweigerd door het *standaardbeveiligingsbeleid voor pods* met bevoegdheden, zodat de pod niet kan worden gepland. We gaan nu dezelfde NGINX-pod uitvoeren zonder de aanvraag voor escalatie van bevoegdheden.

Maak een bestand met de `nginx-unprivileged.yaml` naam en plak het volgende YAML-manifest:

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

Maak de pod met behulp van [de opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

De pod kan niet worden gepland, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

De pod bereikt de planningsfase niet, dus er zijn geen resources om te verwijderen voordat u verder gaat.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Het maken van een pod met een specifieke gebruikerscontext testen

In het vorige voorbeeld heeft de containerafbeelding automatisch geprobeerd de hoofdmap te gebruiken om NGINX te binden aan poort 80. Deze aanvraag is geweigerd door het *standaardbeveiligingsbeleid voor pods* met bevoegdheden, waardoor de pod niet kan worden starten. We gaan nu dezelfde NGINX-pod uitvoeren met een specifieke gebruikerscontext, zoals `runAsUser: 2000` .

Maak een bestand met de `nginx-unprivileged-nonroot.yaml` naam en plak het volgende YAML-manifest:

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

Maak de pod met behulp van [de opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

De pod kan niet worden gepland, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

De pod bereikt de planningsfase niet, dus er zijn geen resources om te verwijderen voordat u verder gaat.

## <a name="create-a-custom-pod-security-policy"></a>Een aangepast beveiligingsbeleid voor pods maken

Nu u het gedrag van het standaardbeveiligingsbeleid voor pods hebt gezien, kunnen we de *niet-gebruiker* een manier bieden om pods te plannen.

Laten we een beleid maken om pods te weigeren die bevoegde toegang aanvragen. Andere opties, zoals *runAsUser* of toegestane *volumes,* worden niet expliciet beperkt. Met dit type beleid wordt een aanvraag voor bevoegde toegang niet uitgevoerd, maar anders kan het cluster de aangevraagde pods uitvoeren.

Maak een bestand met de `psp-deny-privileged.yaml` naam en plak het volgende YAML-manifest:

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

Maak het beleid met behulp van [de opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Als u de beschikbare beleidsregels wilt weergeven, gebruikt u [de opdracht kubectl get psp,][kubectl-get] zoals wordt weergegeven in het volgende voorbeeld. Vergelijk het *psp-deny-privileged-beleid* met het *standaardbeleid* voor bevoegdheden dat in de vorige voorbeelden is afgedwongen om een pod te maken. Alleen het gebruik van *PRIV-escalatie* wordt geweigerd door uw beleid. Er zijn geen beperkingen voor de gebruiker of groep voor het *psp-deny-privileged-beleid.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Gebruikersaccount toestaan het beveiligingsbeleid voor aangepaste pods te gebruiken

In de vorige stap hebt u een beveiligingsbeleid voor pods gemaakt om pods te weigeren die bevoegde toegang aanvragen. Als u wilt toestaan dat het beleid wordt gebruikt, maakt u een *rol* of *een ClusterRole.* Vervolgens koppelt u een van deze rollen met behulp van *een RoleBinding* of *ClusterRoleBinding.*

Voor dit voorbeeld maakt u een ClusterRole waarmee u het *psp-deny-privileged-beleid* kunt gebruiken dat u in de vorige stap hebt gemaakt.  Maak een bestand met de `psp-deny-privileged-clusterrole.yaml` naam en plak het volgende YAML-manifest:

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

Maak de ClusterRole met behulp van de [opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Maak nu een ClusterRoleBinding om de ClusterRole te gebruiken die u in de vorige stap hebt gemaakt. Maak een bestand met de `psp-deny-privileged-clusterrolebinding.yaml` naam en plak het volgende YAML-manifest:

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

Maak een ClusterRoleBinding met behulp van de [opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> In de eerste stap van dit artikel is de functie beveiligingsbeleid voor pods ingeschakeld in het AKS-cluster. De aanbevolen praktijk was om de functie voor het beveiligingsbeleid voor pods alleen in teschakelen nadat u uw eigen beleid hebt gedefinieerd. Dit is de fase waarin u de functie beveiligingsbeleid voor pods inschakelen. Er zijn een of meer aangepaste beleidsregels gedefinieerd en gebruikersaccounts zijn gekoppeld aan dit beleid. U kunt nu de functie voor podbeveiligingsbeleid veilig inschakelen en problemen die worden veroorzaakt door het standaardbeleid minimaliseren.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Het maken van een niet-geprilegeerde pod opnieuw testen

Nu uw aangepaste beveiligingsbeleid voor pods is toegepast en het gebruikersaccount een binding heeft om het beleid te gebruiken, gaan we opnieuw een niet-beveiligde pod maken. Gebruik hetzelfde `nginx-privileged.yaml` manifest om de pod te maken met behulp van de opdracht [kubectl apply:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

De pod is gepland. Wanneer u de status van de pod controleert met behulp van de [opdracht kubectl get pods,][kubectl-get] is de pod *Wordt uitgevoerd:*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

In dit voorbeeld ziet u hoe u aangepaste beveiligingsbeleidsregels voor pods kunt maken om toegang tot het AKS-cluster voor verschillende gebruikers of groepen te definiëren. Het standaard AKS-beleid biedt een nauwe controle over welke pods kunnen worden uitgevoerd. Maak daarom uw eigen aangepaste beleidsregels om vervolgens de beperkingen die u nodig hebt op de juiste manier te definiëren.

Verwijder de niet-bevoorrechte NGINX-pod met behulp van de [opdracht kubectl delete][kubectl-delete] en geef de naam van uw YAML-manifest op:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u het beveiligingsbeleid voor pods wilt uitschakelen, gebruikt u [de opdracht az aks update][az-aks-update] opnieuw. In het volgende voorbeeld wordt het beveiligingsbeleid voor pods uitgeschakeld op de clusternaam *myAKSCluster* in de resourcegroep met de *naam myResourceGroup:*

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

Verwijder het beveiligingsbeleid met behulp [van de opdracht kubectl delete][kubectl-delete] en geef de naam van uw YAML-manifest op:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Verwijder ten slotte de *naamruimte psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u gelezen hoe u een beveiligingsbeleid voor pods maakt om het gebruik van bevoegde toegang te voorkomen. Er zijn veel functies die een beleid kan afdwingen, zoals het type volume of de RunAs-gebruiker. Zie de documentatie naslaginformatie over beveiligingsbeleid voor [Kubernetes-pods][kubernetes-policy-reference]voor meer informatie over de beschikbare opties.

Zie Verkeer tussen pods beveiligen met behulp van netwerkbeleid in AKS voor meer informatie over het beperken van [podnetwerkverkeer.][network-policies]

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
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az_extension_add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md
