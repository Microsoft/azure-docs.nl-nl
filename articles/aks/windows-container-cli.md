---
title: Een Windows Server-container maken in een AKS-cluster met behulp van Azure CLI
description: Leer hoe u snel een Kubernetes-cluster maakt en een toepassing implementeert in een Windows Server-container in Azure Kubernetes Service (AKS) met behulp van de Azure CLI.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 617590a3f482e246b8af5db6dd906591c16b20fa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769423"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Een Windows Server-container maken op een Azure Kubernetes Service -cluster (AKS) met behulp van de Azure CLI

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In dit artikel implementeert u een AKS-cluster met behulp van de Azure CLI. U implementeert ook een ASP.NET-voorbeeldtoepassing in een Windows Server-container naar het cluster.

![Afbeelding van bladeren naar ASP.NET voorbeeldtoepassing](media/windows-container/asp-net-sample-app.png)

In dit artikel wordt ervan uitgenomen dat u basiskennis van Kubernetes-concepten moet hebben. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>Beperkingen

De volgende beperkingen gelden wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor meerdere knooppuntgroepen:

* U kunt de eerste knooppuntgroep niet verwijderen.

De volgende aanvullende beperkingen zijn van toepassing op Windows Server-knooppuntgroepen:

* Het AKS-cluster kan maximaal 10 knooppuntgroepen hebben.
* Het AKS-cluster kan maximaal 100 knooppunten in elke knooppuntgroep hebben.
* De naam van de Windows Server-knooppuntgroep heeft een limiet van 6 tekens.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resourcegroep maakt, wordt u gevraagd een locatie op te geven. Op deze locatie zijn de metagegevens van de resourcegroep opgeslagen. Dit is ook de locatie waar uw resources worden uitgevoerd in Azure als u tijdens het maken van de resource geen andere regio opgeeft. Maak een resourcegroep met de opdracht [az group create][az-group-create].

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

> [!NOTE]
> In dit artikel wordt de Bash-syntaxis gebruikt voor de opdrachten in deze zelfstudie.
> Als u een Azure Cloud Shell gebruikt, moet u ervoor zorgen dat de vervolgkeuzeset in de linkerbovenhoek van het Cloud Shell is ingesteld op **Bash.**

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

Als u een [AKS-cluster][azure-cni-about] wilt uitvoeren dat ondersteuning biedt voor knooppuntgroepen voor Windows Server-containers, moet uw cluster een netwerkbeleid gebruiken dat gebruikmaakt van Azure CNI (geavanceerde) netwerkinvoeging. Zie Configureren Azure CNI netwerk voor meer gedetailleerde informatie over het plannen van de vereiste [subnetbereiken en netwerkoverwegingen.][use-advanced-networking] Gebruik de [opdracht az aks create][az-aks-create] om een AKS-cluster met de naam *myAKSCluster te maken.* Met deze opdracht maakt u de benodigde netwerkbronnen als deze niet bestaan.

* Het cluster is geconfigureerd met twee knooppunten.
* De `--windows-admin-password` parameters en stellen de beheerdersreferenties in voor alle Windows Server-containers die zijn gemaakt op het cluster en `--windows-admin-username` moeten voldoen aan de [wachtwoordvereisten voor Windows Server.][windows-server-password] Als u de parameter *windows-admin-password niet* opgeeft, wordt u gevraagd een waarde op te geven.
* De knooppuntgroep maakt gebruik van `VirtualMachineScaleSets` .

> [!NOTE]
> Om ervoor te zorgen dat uw cluster betrouwbaar werkt, moet u ten minste twee (twee) knooppunten uitvoeren in de standaardknooppuntgroep.

Maak een gebruikersnaam om te gebruiken als beheerdersreferenties voor uw Windows Server-containers in uw cluster. De volgende opdrachten vragen u om een gebruikersnaam en stellen deze in WINDOWS_USERNAME voor gebruik in een latere opdracht (onthoud dat de opdrachten in dit artikel worden ingevoerd in een BASH-shell).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

Maak uw cluster en zorg ervoor dat u de `--windows-admin-username` parameter opgeeft. Met de volgende voorbeeldopdracht maakt u een cluster met behulp van de *waarde WINDOWS_USERNAME* u in de vorige opdracht hebt ingesteld. U kunt ook een andere gebruikersnaam rechtstreeks in de parameter opgeven in plaats van *WINDOWS_USERNAME.* Met de volgende opdracht wordt u ook gevraagd een wachtwoord te maken voor de beheerdersreferenties voor uw Windows Server-containers in uw cluster. U kunt ook de parameter *windows-admin-password* gebruiken en daar uw eigen waarde opgeven.

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
> Als er een wachtwoordvalidatiefout wordt weergegeven, controleert u of het wachtwoord dat u hebt ingesteld, voldoet aan de [wachtwoordvereisten voor Windows Server.][windows-server-password] Als uw wachtwoord aan de vereisten voldoet, maakt u de resourcegroep in een andere regio. Maak vervolgens het cluster met de nieuwe resourcegroep.

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling. Soms duurt het inrichten van het cluster langer dan een paar minuten. In dergelijke gevallen duurt het maximaal 10 minuten.

## <a name="add-a-windows-server-node-pool"></a>Een Windows Server-knooppuntgroep toevoegen

Standaard wordt een AKS-cluster gemaakt met een knooppuntgroep die Linux-containers kan uitvoeren. Gebruik `az aks nodepool add` de opdracht om een extra knooppuntgroep toe te voegen die Windows Server-containers naast de Linux-knooppuntgroep kan uitvoeren.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Met de bovenstaande opdracht maakt u een nieuwe knooppuntgroep met de *naam npwin* en voegt u deze toe aan *myAKSCluster.* Wanneer u een knooppuntgroep maakt om Windows Server-containers uit te voeren, wordt de standaardwaarde voor *node-vm-size* *Standard_D2s_v3*. Als u ervoor kiest om de parameter *node-vm-size* in te stellen, controleert u de lijst met beperkte [VM-grootten.][restricted-vm-sizes] De minimaal aanbevolen grootte is *Standard_D2s_v3*. De bovenstaande opdracht maakt ook gebruik van het standaardsubnet in het standaard-vnet dat is gemaakt bij het uitvoeren van `az aks create` .

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

In de volgende voorbeelduitvoer ziet u alle knooppunten in het cluster. Zorg ervoor dat de status van alle knooppunten Gereed *is:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. In dit artikel wordt een manifest gebruikt om alle objecten te maken die nodig zijn om de voorbeeldtoepassing ASP.NET uitvoeren in een Windows Server-container. Dit manifest bevat een [Kubernetes-implementatie][kubernetes-deployment] voor de ASP.NET-voorbeeldtoepassing en een externe [Kubernetes-service][kubernetes-service] voor toegang tot de toepassing vanaf internet.

De ASP.NET voorbeeldtoepassing wordt geleverd als onderdeel van de [.NET Framework Voorbeelden][dotnet-samples] en wordt uitgevoerd in een Windows Server-container. Voor AKS moeten Windows Server-containers zijn gebaseerd op afbeeldingen van *Windows Server 2019* of hoger. Het Kubernetes-manifestbestand moet ook een knooppunt [selector][node-selector] definiëren om uw AKS-cluster te laten weten dat de pod van uw ASP.NET-voorbeeldtoepassing moet worden uitgevoerd op een knooppunt dat Windows Server-containers kan uitvoeren.

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

In de volgende voorbeelduitvoer ziet u dat de implementatie en service zijn gemaakt:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren. Soms kan het langer dan een paar minuten duren voordat de service is ingericht. In dergelijke gevallen duurt het maximaal 10 minuten.

Gebruik de opdracht [kubectl get service][kubectl-get] met het argument `--watch` om de voortgang te controleren.

```console
kubectl get service sample --watch
```

In eerste instantie *wordt het EXTERNE IP-adres* voor de *voorbeeldservice* weergegeven als in *behandeling.*

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het *EXTERNAL-IP*-adres is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Als u de voorbeeld-app in actie wilt zien, opent u een webbrowser naar het externe IP-adres van uw service.

![Afbeelding van bladeren naar ASP.NET voorbeeldtoepassing](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Als u een time-out voor de verbinding ontvangt bij het laden van de pagina, controleert u of de voorbeeld-app gereed is met de volgende opdracht [kubectl get pods --watch]. Soms wordt de Windows-container niet gestart op het moment dat uw externe IP-adres beschikbaar is.

## <a name="delete-cluster"></a>Cluster verwijderen

Gebruik de opdracht [az group delete][az-group-delete] om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen wanneer u het cluster niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Kubernetes-cluster geïmplementeerd en een ASP.NET-voorbeeldtoepassing geïmplementeerd in een Windows Server-container. [Open het Kubernetes-webdashboard][kubernetes-dashboard] voor het cluster dat u zojuist hebt gemaakt.

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
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-provider-register]: /cli/azure/provider#az_provider_register
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
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference