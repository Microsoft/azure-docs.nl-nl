---
title: Azure Policy gebruiken om uw cluster te beveiligen
description: Gebruik Azure Policy om een AKS-cluster (Azure Kubernetes service) te beveiligen.
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102193409"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Uw cluster beveiligen met Azure Policy

Als u de beveiliging van uw Azure Kubernetes service-cluster (AKS) wilt verbeteren, kunt u op uw cluster ingebouwde beveiligings beleidsregels Toep assen en afdwingen met behulp van Azure Policy. [Azure Policy][azure-policy] helpt organisatie standaarden af te dwingen en de naleving op schaal te beoordelen. Nadat u de [Azure Policy-invoeg toepassing voor AKS][kubernetes-policy-reference]hebt geïnstalleerd, kunt u afzonderlijke beleids definities of groepen beleids definities met de naam initiatieven (ook wel policysets genoemd) Toep assen op uw cluster. Zie [Azure Policy ingebouwde definities voor AKS][aks-policies] voor een volledige lijst met AKS-beleid en-initiatief definities.

In dit artikel wordt beschreven hoe u beleids definities toepast op uw cluster en controleert of de toewijzingen worden afgedwongen.

## <a name="prerequisites"></a>Vereisten

- Een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].
- De Azure Policy-invoeg toepassing voor AKS is geïnstalleerd op een AKS-cluster. Volg deze [stappen om de invoeg toepassing Azure Policy te installeren][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Een ingebouwde beleids definitie of-initiatief toewijzen

Gebruik de Azure Portal om een beleids definitie of-initiatief toe te passen.

1. Ga naar de Azure Policy-service in Azure Portal.
1. Selecteer in het linkerdeel venster van de pagina Azure Policy **definities**.
1. Selecteer onder **Categorieën** selecteren `Kubernetes` .
1. Kies de beleids definitie of het initiatief dat u wilt Toep assen. Voor dit voor beeld selecteert u het `Kubernetes cluster pod security baseline standards for Linux-based workloads` initiatief.
1. Selecteer **Toewijzen**.
1. Stel het **bereik** in op de resource groep van het AKS-cluster waarvoor de Azure Policy-invoeg toepassing is ingeschakeld.
1. Selecteer de pagina **para meters** en werk het **effect** bij `audit` naar om `deny` nieuwe implementaties te blok keren die in strijd zijn met het basislijn initiatief. U kunt ook aanvullende naam ruimten toevoegen om de evaluatie uit te sluiten. Voor dit voor beeld behoudt u de standaard waarden.
1. Selecteer **controleren + maken** en vervolgens **maken** om de beleids toewijzing in te dienen.

## <a name="validate-a-azure-policy-is-running"></a>Controleren of een Azure Policy wordt uitgevoerd

Controleer of de beleids toewijzingen worden toegepast op uw cluster door het volgende uit te voeren:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> [Het kan Maxi maal 20 minuten][azure-policy-assign-policy] duren voordat beleids toewijzingen in elk cluster worden gesynchroniseerd.

De uitvoer moet er ongeveer als volgt uitzien:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Afwijzing van een privileged pod valideren

We gaan eerst testen wat er gebeurt wanneer u een pod plant met de beveiligings context van `privileged: true` . Met deze beveiligings context worden de bevoegdheden van de pod geëscaleerd. Met het initiatief is het niet toegestaan om privileged peul te maken, waardoor de aanvraag wordt geweigerd als gevolg van een geweigerde implementatie.

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

### <a name="test-creation-of-an-unprivileged-pod"></a>Het maken van een niet-gemachtigde pod testen

In het vorige voor beeld heeft de container installatie kopie automatisch geprobeerd de root te gebruiken om NGINX te binden aan poort 80. Deze aanvraag is geweigerd door het beleids initiatief, waardoor de pod niet kan worden gestart. Nu kunt u dezelfde NGINX-pod uitvoeren zonder privileged Access.

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

## <a name="disable-a-policy-or-initiative"></a>Een beleid of initiatief uitschakelen

Het Baseline Initiative verwijderen:

1. Ga naar het deel venster beleid op het Azure Portal.
1. Selecteer **toewijzingen** in het linkerdeel venster.
1. Klik op de knop **..** . naast het `Kubernetes cluster pod security baseline standards for Linux-based workloads` initiatief.
1. Selecteer **toewijzing verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de werking van Azure Policy:

- [Overzicht van Azure-beleid][azure-policy]
- [Azure Policy initiatieven en Policies voor AKS][aks-policies]
- Verwijder de [Azure Policy-invoeg toepassing][azure-policy-addon-remove].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
