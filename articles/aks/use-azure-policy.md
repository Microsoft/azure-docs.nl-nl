---
title: Gebruik Azure Policy om uw cluster te beveiligen
description: Gebruik Azure Policy om een AKS Azure Kubernetes Service cluster te beveiligen.
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 6462c2987155925b7df5241d8fb6aa13c1e37b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777721"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Uw cluster beveiligen met Azure Policy

Als u de beveiliging van uw AKS-cluster (Azure Kubernetes Service) wilt verbeteren, kunt u ingebouwd beveiligingsbeleid toepassen en afdwingen op uw cluster met behulp van Azure Policy. [Azure Policy][azure-policy] helpt bij het afdwingen van organisatiestandaarden en het beoordelen van naleving op schaal. Nadat u de [Azure Policy-invoegversie][kubernetes-policy-reference]voor AKS hebt geïnstalleerd, kunt u afzonderlijke beleidsdefinities of groepen beleidsdefinities genaamd initiatieven (ook wel beleidssets genoemd) toepassen op uw cluster. Zie [Azure Policy ingebouwde definities voor AKS][aks-policies] voor een volledige lijst met AKS-beleid en initiatiefdefinities.

In dit artikel wordt beschreven hoe u beleidsdefinities op uw cluster kunt toepassen en hoe u kunt controleren of deze toewijzingen worden afgedwongen.

## <a name="prerequisites"></a>Vereisten

- Een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].
- De Azure Policy add-on voor AKS geïnstalleerd op een AKS-cluster. Volg deze [stappen om de Azure Policy te installeren.][azure-policy-addon]

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Een ingebouwde beleidsdefinitie of initiatief toewijzen

Als u een beleidsdefinitie of initiatief wilt toepassen, gebruikt u de Azure Portal.

1. Navigeer naar Azure Policy service in Azure Portal.
1. Selecteer definities in het linkerdeelvenster Azure Policy pagina **.**
1. Selecteer **onder Categorieën** de optie `Kubernetes` .
1. Kies de beleidsdefinitie of het initiatief dat u wilt toepassen. Selecteer voor dit voorbeeld het `Kubernetes cluster pod security baseline standards for Linux-based workloads` initiatief.
1. Selecteer **Toewijzen**.
1. Stel het **Bereik** in op de resourcegroep van het AKS-cluster met de Azure Policy invoeg-invoeggebruik is ingeschakeld.
1. Selecteer de **pagina Parameters** en werk effect **bij van** in om te blokkeren dat nieuwe `audit` `deny` implementaties het basislijninitiatief schenden. U kunt ook extra naamruimten toevoegen die u wilt uitsluiten van evaluatie. Laat voor dit voorbeeld de standaardwaarden staan.
1. Selecteer **Beoordelen en maken en** vervolgens Maken **om** de beleidstoewijzing in te dienen.

## <a name="validate-a-azure-policy-is-running"></a>Valideren of Azure Policy wordt uitgevoerd

Controleer of de beleidstoewijzingen zijn toegepast op uw cluster door het volgende uit te voeren:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> Het kan tot [20][azure-policy-assign-policy] minuten duren voordat beleidstoewijzingen met elk cluster zijn gesynchroniseerd.

De uitvoer moet er ongeveer als het volgende uit zien:

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

### <a name="validate-rejection-of-a-privileged-pod"></a>Afwijzing van een bevoorrechte pod valideren

Laten we eerst testen wat er gebeurt wanneer u een pod met de beveiligingscontext van `privileged: true` inplant. Deze beveiligingscontext escaleert de bevoegdheden van de pod. Het initiatief staat bevoegde pods niet toe, dus de aanvraag wordt geweigerd, waardoor de implementatie wordt geweigerd.

Maak een bestand met de `nginx-privileged.yaml` naam en plak het volgende YAML-manifest:

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

Maak de pod met [de opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f nginx-privileged.yaml
```

Zoals verwacht kan de pod niet worden gepland, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

De pod bereikt de planningsfase niet, dus er zijn geen resources om te verwijderen voordat u verder gaat.

### <a name="test-creation-of-an-unprivileged-pod"></a>Het maken van een niet-geprilegeerde pod testen

In het vorige voorbeeld heeft de containerafbeelding automatisch geprobeerd de hoofdmap te gebruiken om NGINX te verbinden met poort 80. Deze aanvraag is geweigerd door het beleidsinitiatief, waardoor de pod niet kan worden gestart. We gaan nu dezelfde NGINX-pod uitvoeren zonder bevoegde toegang.

Maak een bestand met de `nginx-unprivileged.yaml` naam en plak het volgende YAML-manifest:

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

Maak de pod met behulp van [de opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f nginx-unprivileged.yaml
```

De pod is gepland. Wanneer u de status van de pod controleert met behulp van de [opdracht kubectl get pods,][kubectl-get] is de pod *Wordt uitgevoerd:*

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

In dit voorbeeld ziet u het basisinitiatief dat alleen van invloed is op implementaties die beleidsregels in de verzameling schenden. Toegestane implementaties blijven werken.

Verwijder de niet-bevoorrechte NGINX-pod met behulp van de [opdracht kubectl delete][kubectl-delete] en geef de naam van uw YAML-manifest op:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Een beleid of initiatief uitschakelen

Het basislijninitiatief verwijderen:

1. Navigeer naar het deelvenster Beleid op Azure Portal.
1. Selecteer **Toewijzingen** in het linkerdeelvenster.
1. Klik op **de knop ...** naast het `Kubernetes cluster pod security baseline standards for Linux-based workloads` initiatief.
1. Selecteer **Toewijzing verwijderen.**

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe Azure Policy werkt:

- [Overzicht van Azure-beleid][azure-policy]
- [Azure Policy en het aks-initiatief][aks-policies]
- Verwijder de [Azure Policy-invoeg-on][azure-policy-addon-remove].

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
