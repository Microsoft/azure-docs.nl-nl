---
title: Interactie met Windows-containers
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Meer informatie over het uitvoeren van Azure Dev Spaces op een bestaand cluster met Windows-containers
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Windows-containers
ms.openlocfilehash: bbef5eafe44e38691327714c14c6a6026d45a3c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777433"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interactie met Windows-containers met Behulp van Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

U kunt Azure Dev Spaces inschakelen voor zowel nieuwe als bestaande Kubernetes-naamruimten. Azure Dev Spaces wordt uitgevoerd en instrumenteer services die worden uitgevoerd op Linux-containers. Deze services kunnen ook communiceren met toepassingen die worden uitgevoerd op Windows-containers in dezelfde naamruimte. In dit artikel wordt beschreven hoe u Dev Spaces gebruikt om services uit te voeren in een naamruimte met bestaande Windows-containers. Op dit moment kunt u geen fouten opsporen of koppelen aan Windows-containers met Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Uw cluster instellen

In dit artikel wordt ervan uitgenomen dat u al een cluster hebt met zowel Linux- als Windows-knooppuntgroepen. Als u een cluster wilt maken met Linux- en Windows-knooppuntgroepen, kunt u de instructies hier [volgen.][windows-container-cli]

Maak verbinding met uw cluster met [behulp van kubectl][kubectl], de Kubernetes-opdrachtregelclient. Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```azurecli-interactive
kubectl get nodes
```

In de volgende voorbeelduitvoer ziet u een cluster met een Windows- en Linux-knooppunt. Zorg ervoor dat de status Gereed is *voor* elk knooppunt voordat u doorgaat.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Pas een [taint toe][using-taints] op uw Windows-knooppunten. De taint op uw Windows-knooppunten voorkomt dat Dev Spaces Linux-containers kan plannen om op uw Windows-knooppunten te worden uitgevoerd. Met de volgende opdrachtvoorbeeldopdracht wordt een taint toegepast op het Windows-knooppunt *aksnpwin987654* uit het vorige voorbeeld.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Wanneer u een taint op een knooppunt toe passen, moet u een overeenkomende toleratie configureren in de implementatiesjabloon van uw service om uw service op dat knooppunt uit te voeren. De voorbeeldtoepassing is al geconfigureerd met een [overeenkomende toleratie][sample-application-toleration-example] voor de taint die u in de vorige opdracht hebt geconfigureerd.

## <a name="run-your-windows-service"></a>Uw Windows-service uitvoeren

Voer uw Windows-service uit op uw AKS-cluster en controleer of deze de *status Wordt uitgevoerd* heeft. In dit artikel wordt een [voorbeeldtoepassing gebruikt om][sample-application] een Windows- en Linux-service te demonstreren die in uw cluster wordt uitgevoerd.

Kloon de voorbeeldtoepassing vanuit GitHub en navigeer naar de `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` map :

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

De voorbeeldtoepassing gebruikt [Helm 3 om][helm-installed] de Windows-service op uw cluster uit te voeren. Navigeer naar `charts` de map en gebruik Helm om de Windows-service uit te voeren:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

De bovenstaande opdracht maakt gebruik van Helm om uw Windows-service uit te voeren in *de naamruimte dev.* Als u geen naamruimte met de naam *dev* hebt, wordt deze gemaakt.

Gebruik de `kubectl get pods` opdracht om te controleren of uw Windows-service wordt uitgevoerd in uw cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Azure Dev Spaces inschakelen

Schakel Dev Spaces in dezelfde naamruimte in die u hebt gebruikt om uw Windows-service uit te voeren. Met de volgende opdracht schakelt u Dev Spaces in de *naamruimte dev* in:

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Uw Windows-service voor Dev Spaces bijwerken

Wanneer u Dev Spaces in een bestaande naamruimte inschakelen met containers die al worden uitgevoerd, probeert Dev Spaces standaard nieuwe containers die in die naamruimte worden uitgevoerd, te instrumenteer. In Dev Spaces worden ook nieuwe containers gebruikt die zijn gemaakt voor de service die al in de naamruimte wordt uitgevoerd. Als u wilt voorkomen dat Dev Spaces een container instrumenteert die wordt uitgevoerd in uw naamruimte, voegt u de *no-proxy-header* toe aan de `deployment.yaml` .

Voeg `azds.io/no-proxy: "true"` toe aan het `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` bestand:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Gebruik `helm list` om de implementatie voor uw Windows-service weer te bieden:

```cmd
$ helm list --namespace dev
NAME             REVISION   UPDATED                    STATUS    CHART            APP VERSION    NAMESPACE
windows-service    1        Wed Jul 24 15:45:59 2019   DEPLOYED  mywebapi-0.1.0   1.0            dev
```

In het bovenstaande voorbeeld is *windows-service* de naam van uw implementatie. Werk uw Windows-service bij met de nieuwe configuratie met behulp van `helm upgrade` :

```cmd
helm upgrade windows-service . --namespace dev
```

Omdat u uw hebt `deployment.yaml` bijgewerkt, zal Dev Spaces uw service niet proberen te instrumenteer.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Uw Linux-toepassing uitvoeren met Azure Dev Spaces

Navigeer naar `webfrontend` de map en gebruik de opdrachten en om uw `azds prep` `azds up` Linux-toepassing in uw cluster uit te voeren.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

Met `azds prep --enable-ingress` de opdracht worden de Helm-grafiek en Dockerfiles voor uw toepassing gegenereerd.

> [!TIP]
> De [Dockerfile en Helm-grafiek](../how-dev-spaces-works-prep.md#prepare-your-code) voor uw project worden gebruikt in Azure Dev Spaces om uw code te bouwen en uit te voeren, maar u kunt deze bestanden wijzigen als u wilt wijzigen hoe het project wordt gebouwd en uitgevoerd.

Met `azds up` de opdracht wordt uw service uitgevoerd in de naamruimte .

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

U kunt de service zien die wordt uitgevoerd door de openbare URL te openen. Deze wordt weergegeven in de uitvoer van de opdracht azds up. In dit voorbeeld is `http://dev.webfrontend.abcdef0123.eus.azds.io/` de openbare URL. Navigeer naar de service in een browser en *klik* bovenaan op Over. Controleer of er een bericht wordt weergegeven van *de mywebapi-service* met de versie van Windows die door de container wordt gebruikt.

![Voorbeeld-app met Windows-versie van mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure Dev Spaces werkt.

> [!div class="nextstepaction"]
> [Hoe Azure Dev Spaces werkt](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
