---
title: Een Windows Server-container maken op een AKS-cluster met behulp van Azure CLI
description: Meer informatie over hoe u snel een Kubernetes-cluster maakt, een toepassing implementeert in een Windows Server-container in azure Kubernetes service (AKS) met behulp van de Azure CLI.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 13b4fbd21bb348d1ef79a3ca68128869115745cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200907"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Een Windows Server-container maken op een Azure Kubernetes service (AKS)-cluster met behulp van Azure CLI

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In dit artikel implementeert u een AKS-cluster met behulp van de Azure CLI. U kunt ook een ASP.NET-voorbeeld toepassing in een Windows Server-container implementeren in het cluster.

![Afbeelding van bladeren naar ASP.NET-voorbeeld toepassing](media/windows-container/asp-net-sample-app.png)

In dit artikel wordt ervan uitgegaan dat u basis informatie krijgt over Kubernetes-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor meerdere knooppunt groepen:

* U kunt de eerste knooppunt groep niet verwijderen.

De volgende extra beperkingen zijn van toepassing op Windows Server-knooppunt groepen:

* Het AKS-cluster kan Maxi maal 10 knooppunt groepen bevatten.
* Het AKS-cluster kan Maxi maal 100 knoop punten in elke knooppunt groep bevatten.
* De naam van de Windows Server-knooppunt groep heeft een limiet van 6 tekens.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resourcegroep maakt, wordt u gevraagd een locatie op te geven. Op deze locatie zijn de metagegevens van de resourcegroep opgeslagen. Dit is ook de locatie waar uw resources worden uitgevoerd in Azure als u tijdens het maken van de resource geen andere regio opgeeft. Maak een resourcegroep met de opdracht [az group create][az-group-create].

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

> [!NOTE]
> In dit artikel wordt gebruikgemaakt van de bash-syntaxis voor de opdrachten in deze zelf studie.
> Als u Azure Cloud Shell gebruikt, controleert u of de vervolg keuzelijst in de linkerbovenhoek van het venster Cloud Shell is ingesteld op **bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

In de volgende voorbeelduitvoer ziet u dat de resourcegroep is gemaakt:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Als u een AKS-cluster wilt uitvoeren dat knooppunt Pools ondersteunt voor Windows Server-containers, moet uw cluster gebruikmaken van een netwerk beleid dat gebruikmaakt van de invoeg toepassing [Azure cni][azure-cni-about] (Geavanceerd). Zie [Azure cni-netwerken configureren][use-advanced-networking]voor meer informatie over het plannen van de vereiste subnet bereiken en netwerk overwegingen. Gebruik de opdracht [AZ AKS Create][az-aks-create] om een AKS-cluster te maken met de naam *myAKSCluster*. Met deze opdracht worden de benodigde netwerk bronnen gemaakt als deze nog niet bestaan.

* Het cluster is geconfigureerd met twee knoop punten.
* `--windows-admin-password`Met de `--windows-admin-username` para meters en worden de beheerders referenties ingesteld voor Windows Server-containers die op het cluster zijn gemaakt en moeten voldoen aan de [vereisten voor Windows Server-wacht woorden][windows-server-password]. Als u de para meter *Windows-admin-password* niet opgeeft, wordt u gevraagd een waarde op te geven.
* De knooppunt groep gebruikt `VirtualMachineScaleSets` .

> [!NOTE]
> Om ervoor te zorgen dat uw cluster betrouwbaar functioneert, moet u ten minste twee knoop punten in de standaard knooppunt groep uitvoeren.

Maak een gebruikers naam om te gebruiken als beheerders referenties voor uw Windows Server-containers in uw cluster. Met de volgende opdrachten wordt u naar een gebruikers naam gevraagd en deze WINDOWS_USERNAME instellen voor gebruik in een latere opdracht (Houd er rekening mee dat de opdrachten in dit artikel worden ingevoerd in een BASH-shell).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

Maak uw cluster om een para meter op te geven `--windows-admin-username` . Met de volgende voorbeeld opdracht maakt u een cluster met behulp van de waarde uit *WINDOWS_USERNAME* die u in de vorige opdracht hebt ingesteld. U kunt ook rechtstreeks een andere gebruikers naam opgeven in de para meter in plaats van met *WINDOWS_USERNAME*. Met de volgende opdracht wordt u ook gevraagd om een wacht woord te maken voor de beheerders referenties voor uw Windows Server-containers in uw cluster. U kunt ook de para meter *Windows-admin-password* gebruiken en daar uw eigen waarde opgeven.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!NOTE]
> Als er een fout is opgetreden bij het valideren van het wacht woord, controleert u of het wacht woord dat u instelt voldoet aan de [vereisten voor Windows Server][windows-server-password] Als uw wacht woord aan de vereisten voldoet, kunt u proberen om de resource groep te maken in een andere regio. Probeer vervolgens het cluster te maken met de nieuwe resource groep.

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling. Af en toe kan het cluster langer dan een paar minuten duren. In deze gevallen Maxi maal 10 minuten toestaan.

## <a name="add-a-windows-server-node-pool"></a>Een Windows Server-knooppunt groep toevoegen

Standaard wordt een AKS-cluster gemaakt met een knooppunt groep waarvoor Linux-containers kunnen worden uitgevoerd. Gebruik `az aks nodepool add` de opdracht om een extra knooppunt groep toe te voegen waarop Windows Server-containers naast de Linux-knooppunt groep kunnen worden uitgevoerd.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Met de bovenstaande opdracht maakt u een nieuwe knooppunt groep met de naam *npwin* en voegt u deze toe aan de *myAKSCluster*. Bij het maken van een knooppunt groep voor het uitvoeren van Windows Server-containers, wordt de standaard waarde voor de *grootte van knoop punt-vm* *Standard_D2s_v3*. Als u de para meter van het *knoop punt-VM-grootte* wilt instellen, controleert u de lijst met [beperkte VM-grootten][restricted-vm-sizes]. De minimale aanbevolen grootte is *Standard_D2s_v3*. De bovenstaande opdracht gebruikt ook het standaard-subnet in de standaard-vnet dat is gemaakt tijdens het uitvoeren `az aks create` .

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```console
kubectl get nodes
```

In de volgende voorbeeld uitvoer ziet u de knoop punten in het cluster. Zorg ervoor dat de status van alle knoop punten *gereed* is:

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. In dit artikel wordt een manifest gebruikt om alle objecten te maken die nodig zijn om de voorbeeld toepassing ASP.NET uit te voeren in een Windows Server-container. Dit manifest bevat een [Kubernetes-implementatie][kubernetes-deployment] voor de voorbeeld toepassing ASP.net en een externe [Kubernetes-service][kubernetes-service] voor toegang tot de toepassing vanaf internet.

De voorbeeld toepassing ASP.NET wordt meegeleverd als onderdeel van de [.NET Framework][dotnet-samples] -voor beelden en wordt uitgevoerd in een Windows Server-container. AKS vereist dat Windows Server-containers worden gebaseerd op installatie kopieën van *Windows server 2019* of hoger. In het manifest bestand Kubernetes moet ook een [knooppunt kiezer][node-selector] worden gedefinieerd, zodat uw AKS-cluster de pod van uw ASP.net-voorbeeld toepassing uitvoert op een knoop punt waarop Windows Server-containers kunnen worden uitgevoerd.

Maak een bestand met de naam `sample.yaml` en kopieer de volgende YAML-definitie naar het bestand. Als u Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met behulp van `vi` of `nano`, zoals bij een virtueel of fysiek systeem:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Implementeer de toepassing met de opdracht [kubectl apply][kubectl-apply] en geef de naam op van uw YAML-manifest:

```console
kubectl apply -f sample.yaml
```

In de volgende voorbeeld uitvoer ziet u dat de implementatie en service zijn gemaakt:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren. Af en toe kan de service langer dan een paar minuten duren om in te richten. In deze gevallen Maxi maal 10 minuten toestaan.

Gebruik de opdracht [kubectl get service][kubectl-get] met het argument `--watch` om de voortgang te controleren.

```console
kubectl get service sample --watch
```

In eerste instantie wordt het *externe IP-adres* voor de *voorbeeld* service weer gegeven als *in behandeling*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het *EXTERNAL-IP*-adres is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Als u de voor beeld-app in actie wilt zien, opent u een webbrowser op het externe IP-adres van uw service.

![Afbeelding van bladeren naar ASP.NET-voorbeeld toepassing](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Als u een time-out voor de verbinding krijgt bij het laden van de pagina, moet u controleren of de voor beeld-app klaar is met de volgende opdracht [kubectl Get Peule--Watch]. Soms wordt de Windows-container niet gestart op het moment dat het externe IP-adres beschikbaar is.

## <a name="delete-cluster"></a>Cluster verwijderen

Gebruik de opdracht [az group delete][az-group-delete] om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen wanneer u het cluster niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Kubernetes-cluster geïmplementeerd en een ASP.NET-voorbeeld toepassing in een Windows Server-container geïmplementeerd. [Open het Kubernetes-webdashboard][kubernetes-dashboard] voor het cluster dat u zojuist hebt gemaakt.

Voor meer informatie over AKS en een volledig stapsgewijs voorbeeld van code tot implementatie gaat u naar de zelfstudie over Kubernetes-clusters.

> [!div class="nextstepaction"]
> [AKS-zelfstudie][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference