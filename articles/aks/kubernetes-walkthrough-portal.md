---
title: 'Quick Start: een AKS-cluster implementeren met behulp van de Azure Portal'
titleSuffix: Azure Kubernetes Service
description: Lees hoe u met Azure Portal snel een Kubernetes-cluster kunt maken, een toepassing kunt implementeren en de prestaties kunt bewaken in Azure Kubernetes Service (AKS).
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: aa07dd84cbd107aca77236f4d084ef95a7f1005b
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544278"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Quickstart: Een AKS-cluster (Azure Kubernetes Service) implementeren met Azure Portal

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In deze quickstart gaat u het volgende doen:
* Implementeer een AKS-cluster met behulp van de Azure Portal. 
* Voer een toepassing met meerdere containers uit met een web-front-end en een redis-exemplaar in het cluster. 
* Bewaak de status van het cluster en de peuling waarmee uw toepassing wordt uitgevoerd.

![Afbeelding van browsen naar de Azure Vote-voorbeeldtoepassing](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

In deze snelstart wordt ervan uitgegaan dat u een basisbegrip hebt van Kubernetes-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.

2. Selecteer **Containers** > **Kubernetes-service**.

3. Configureer op de pagina **Basisprincipes** de volgende opties:
    - **Project Details**: 
        * Selecteer een Azure-**abonnement**.
        * Selecteer of maak een Azure- **resource groep**, zoals *myResourceGroup*.
    - **Cluster Details**: 
        * Voer een **Kubernetes-clusternaam** in, zoals *myAKSCluster*. 
        * Selecteer een **Regio** en **Kubernetes-versie** voor het AKS-cluster.
    - **Primaire knooppunt groep**: 
        * Selecteer een VM-**knooppuntgrootte** voor de AKS-knooppunten. De VM-grootte kan *niet* meer worden gewijzigd als een AKS-cluster eenmaal is geïmplementeerd.
        * Selecteer het aantal knooppunten dat u in het cluster wilt implementeren. Stel voor deze quickstart het **Aantal knooppunten** in op *1*. Het aantal knooppunten kan nog *wel* worden gewijzigd als het cluster is geïmplementeerd.
    
    ![AKS-cluster maken - basisgegevens opgeven](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Selecteer **Volgende: knooppuntgroepen** wanneer u klaar bent.

5. Behoud de standaard opties voor **knooppunt groepen** . Klik boven aan het venster op **Volgende: Verificatie**.
    > [!CAUTION]
    > Het kan enkele minuten duren voordat nieuwe Azure AD-service-principals zijn door gegeven en beschikbaar komen, waardoor "Service-Principal niet gevonden" fouten en validatie fouten in Azure Portal. Als u deze bumper hebt bereikt, gaat u naar [ons artikel over probleem oplossing](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) voor risico beperking.

6. Configureer de volgende opties op de pagina **Verificatie**:
    - Maak een nieuwe cluster identiteit door een van de volgende:
        * Het **authenticatie** veld met door **systeem assinged beheerde identiteit** verlaten of
        * **Service-Principal** kiezen voor het gebruik van een service-principal. 
            * Selecteer *(nieuw) standaard Service-Principal* om een standaard service-principal te maken, of
            * Selecteer *Service-Principal configureren* om een bestaande te gebruiken. U moet de SPN-client-ID en het geheim van de bestaande Principal opgeven.
    - Schakel de optie Kubernetes op basis van op rollen gebaseerd toegangs beheer (Kubernetes RBAC) in om meer nauw keurige controle over toegang te bieden tot de Kubernetes-resources die zijn geïmplementeerd in uw AKS-cluster.

    Standaard worden *basis* netwerkfuncties gebruikt en Azure Monitor voor containers is ingeschakeld. 

7. Klik op **Beoordelen en maken** en vervolgens op **Maken** wanneer de validatie is voltooid. 


8. Het duurt een paar minuten om het AKS-cluster te maken. Wanneer de implementatie is voltooid, gaat u naar uw resource door een van de volgende handelingen uit te voeren:
    * Klik op **Ga naar resource** of
    * Blader naar de AKS-cluster resource groep en selecteer de AKS-resource. 
        * Een voor beeld van een cluster Dashboard: Blader naar *myResourceGroup* en selecteer *myAKSCluster* -resource.

        ![Voorbeeld van AKS-dashboard in de Azure-portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u de Kubernetes-opdracht regel-client, [kubectl][kubectl]. `kubectl` is al geïnstalleerd als u Azure Cloud Shell gebruikt. 

1. Open Cloud Shell met behulp van de knop `>_` boven in de Azure-portal.

    ![Open Azure Cloud Shell in de portal](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Voor het uitvoeren van deze bewerkingen in een installatie van een lokale shell:
    > 1. Controleer of Azure CLI is geïnstalleerd.
    > 2. Maak verbinding met Azure via de `az login` opdracht.

2. Configureer `kubectl` om verbinding te maken met uw Kubernetes-cluster met behulp van de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] . Met de volgende opdracht worden referenties gedownload en wordt de Kubernetes CLI geconfigureerd om deze te gebruiken.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Controleer de verbinding met uw cluster met `kubectl get` om een lijst met cluster knooppunten te retour neren.

    ```console
    kubectl get nodes
    ```

    In de uitvoer ziet u het enkele knoop punt dat u in de vorige stappen hebt gemaakt. Zorg ervoor dat de knooppunt status *gereed* is:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een Kubernetes-manifest bestand wordt de gewenste status van een cluster gedefinieerd, zoals welke container installatie kopieën moeten worden uitgevoerd. 

In deze Snelstartgids gebruikt u een manifest om alle objecten te maken die nodig zijn om de Azure stem-toepassing uit te voeren. Dit manifest bevat twee Kubernetes-implementaties:
* Het voor beeld van Azure stem python-toepassingen.
* Een redis-exemplaar. 

Er worden ook twee Kubernetes-Services gemaakt:
* Een interne service voor het redis-exemplaar.
* Een externe service voor toegang tot de Azure stem-toepassing via internet.

1. Gebruik in de Cloud Shell een editor om een bestand te maken met de naam `azure-vote.yaml` , bijvoorbeeld:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml`, of 
    * `vi azure-vote.yaml`. 

1. Kopiëren in de volgende YAML definitie:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. Implementeer de toepassing met behulp van de `kubectl apply` opdracht en geef de naam van het yaml-manifest op:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    Uitvoer toont de geslaagde implementaties en services:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren.

Als u de voortgang wilt bewaken, gebruikt u de `kubectl get service` opdracht met het `--watch` argument.

```console
kubectl get service azure-vote-front --watch
```

De **externe-IP** -uitvoer voor de `azure-vote-front` service wordt in eerste instantie weer gegeven als *in behandeling*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het **externe IP** -adres is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt `CTRL-C` u om het `kubectl` controle proces te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:


```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Open een webbrowser naar het externe IP-adres van uw service om de Azure Vote-app te zien.

![Afbeelding van browsen naar de Azure Vote-voorbeeldtoepassing](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Status en logboeken controleren

Toen u het cluster maakte, werd Azure Monitor voor containers ingeschakeld. Azure Monitor voor containers voorziet in metrische gegevens over de status voor zowel het AKS-cluster als de gehele uitvoering op het cluster.

Het kan een paar minuten duren voordat de metrische gegevens zijn ingevuld in de Azure Portal. Voor een overzicht van de huidige status, de uptime en het resource gebruik van Azure stemmen:

1. Ga terug naar de AKS-resource in het Azure Portal.
1. Kies onder **bewaking** aan de linkerkant het gedeelte **inzichten**.
1. Kies aan de bovenkant om **+ filter toe te voegen**.
1. Selecteer **naam ruimte** als eigenschap en kies vervolgens *\<All but kube-system\>* .
1. Selecteer **containers** om ze weer te geven.

De `azure-vote-back` `azure-vote-front` containers en worden weer gegeven, zoals wordt weer gegeven in het volgende voor beeld:

![De status van actieve containers in AKS weergeven](media/kubernetes-walkthrough-portal/monitor-containers.png)

`azure-vote-front`Selecteer **container logboeken weer geven** in de vervolg keuzelijst containers om de logboeken voor de pod weer te geven. Deze logboeken bevatten de stromen *stdout* en *stderr* van de container.

![De containerlogboeken in AKS weergeven](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Cluster verwijderen

Opschonen van uw overbodige resources om Azure-kosten te voor komen. Selecteer de knop **verwijderen** in het AKS-cluster dashboard. U kunt ook de opdracht [AZ AKS delete][az-aks-delete] gebruiken in de Cloud shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal.
> 
> Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="get-the-code"></a>Code ophalen

Er zijn al bestaande container installatie kopieën gebruikt in deze Quick Start om een Kubernetes-implementatie te maken. Het manifest bestand gerelateerde toepassings code, Dockerfile en Kubernetes is [beschikbaar op github.][azure-vote-app]

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een Kubernetes-cluster geïmplementeerd en vervolgens een toepassing met meerdere containers geïmplementeerd. Open het Kubernetes-webdashboard voor uw AKS-cluster.


Ga verder met de zelf studie voor het Kubernetes-cluster voor meer informatie over AKS door door loop een volledig voor beeld, waaronder het bouwen van een toepassing, het implementeren van een implementatie vanuit Azure Container Registry, het bijwerken van een actieve toepassing, het schalen en upgraden van uw cluster.

> [!div class="nextstepaction"]
> [AKS-zelfstudie][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations