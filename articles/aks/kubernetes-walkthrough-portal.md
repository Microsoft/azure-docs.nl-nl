---
title: 'Quickstart: Een AKS-cluster implementeren met behulp van de Azure Portal'
titleSuffix: Azure Kubernetes Service
description: Lees hoe u met Azure Portal snel een Kubernetes-cluster kunt maken, een toepassing kunt implementeren en de prestaties kunt bewaken in Azure Kubernetes Service (AKS).
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: 28ba2ffd2007aeb45081cf66b05395a2b8456bf7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779701"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Quickstart: Een AKS-cluster (Azure Kubernetes Service) implementeren met Azure Portal

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In deze quickstart gaat u het volgende doen:
* Implementeer een AKS-cluster met behulp van Azure Portal. 
* Voer een toepassing met meerdere containers uit met een webfront-end en een Redis-exemplaar in het cluster. 
* Controleer de status van het cluster en de pods die uw toepassing uitvoeren.

![Afbeelding van browsen naar de Azure Vote-voorbeeldtoepassing](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

In deze snelstart wordt ervan uitgegaan dat u een basisbegrip hebt van Kubernetes-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.

2. Selecteer **Containers** > **Kubernetes-service**.

3. Configureer op de pagina **Basisprincipes** de volgende opties:
    - **Projectdetails:** 
        * Selecteer een Azure-**abonnement**.
        * Selecteer of maak een **Azure-resourcegroep,** zoals *myResourceGroup.*
    - **Clusterdetails:** 
        * Voer een **Kubernetes-clusternaam** in, zoals *myAKSCluster*. 
        * Selecteer een **Regio** en **Kubernetes-versie** voor het AKS-cluster.
    - **Primaire knooppuntgroep:** 
        * Selecteer een VM-**knooppuntgrootte** voor de AKS-knooppunten. De VM-grootte kan *niet* meer worden gewijzigd als een AKS-cluster eenmaal is geïmplementeerd.
        * Selecteer het aantal knooppunten dat u in het cluster wilt implementeren. Stel voor deze quickstart het **Aantal knooppunten** in op *1*. Het aantal knooppunten kan nog *wel* worden gewijzigd als het cluster is geïmplementeerd.
    
    ![AKS-cluster maken - basisgegevens opgeven](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Selecteer **Volgende: knooppuntgroepen** wanneer u klaar bent.

5. Laat de **standaardopties voor Knooppuntgroepen** staan. Klik boven aan het venster op **Volgende: Verificatie**.
    > [!CAUTION]
    > Het kan enkele minuten duren voordat nieuw gemaakte Azure AD-service-principals zijn doorgegeven en beschikbaar zijn. Dit leidt tot fouten met de fout 'service-principal niet gevonden' en validatiefouten in Azure Portal. Als u deze bump tegen komt, raadpleegt u [ons artikel over probleemoplossing voor](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) oplossingen.

6. Configureer de volgende opties op de pagina **Verificatie**:
    - Maak een nieuwe clusteridentiteit door:
        * Laat het **veld Verificatie** met een door het systeem beheerde identiteit **achter,** of
        * **Service-principal kiezen** voor het gebruik van een service-principal. 
            * Selecteer *(nieuwe) standaardservice-principal om* een standaardservice-principal te maken, of
            * Selecteer *Service-principal configureren* om een bestaande te gebruiken. U moet de SPN-client-id en het geheim van de bestaande principal verstrekken.
    - Schakel de optie Op kubernetes-rollen gebaseerd toegangsbeheer (Kubernetes RBAC) in om een meer fijnkeurig beheer te bieden over de toegang tot de Kubernetes-resources die in uw AKS-cluster zijn geïmplementeerd.

    Standaard worden *basis* netwerkfuncties gebruikt en Azure Monitor voor containers is ingeschakeld. 

7. Klik op **Beoordelen en maken** en vervolgens op **Maken** wanneer de validatie is voltooid. 


8. Het duurt een paar minuten om het AKS-cluster te maken. Wanneer de implementatie is voltooid, navigeert u naar uw resource door een van de volgende te kiezen:
    * Klik op **Ga naar resource** of
    * Blader naar de AKS-clusterresourcegroep en selecteer de AKS-resource. 
        * Per voorbeeldclusterdashboard hieronder: bladeren naar *myResourceGroup* en *myAKSCluster-resource* selecteren.

        ![Voorbeeld van AKS-dashboard in de Azure-portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u de Kubernetes-opdrachtregelclient [kubectl][kubectl]. `kubectl` is al geïnstalleerd als u Azure Cloud Shell. 

1. Open Cloud Shell met behulp van de knop `>_` boven in de Azure-portal.

    ![Open Azure Cloud Shell in de portal](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > Deze bewerkingen uitvoeren in een lokale shell-installatie:
    > 1. Controleer of Azure CLI is geïnstalleerd.
    > 2. Maak verbinding met Azure via de `az login` opdracht .

2. Configureer `kubectl` om verbinding te maken met uw Kubernetes-cluster met behulp van de opdracht az [aks get-credentials.][az-aks-get-credentials] Met de volgende opdracht worden referenties gedownload en wordt de Kubernetes CLI geconfigureerd voor het gebruik ervan.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Controleer de verbinding met uw cluster met om `kubectl get` een lijst met clusterknooppunten te retourneren.

    ```console
    kubectl get nodes
    ```

    Uitvoer toont het enkele knooppunt dat in de vorige stappen is gemaakt. Zorg ervoor dat de knooppuntstatus Gereed *is:*

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

Een Kubernetes-manifestbestand definieert de gewenste status van een cluster, zoals welke containerafbeeldingen moeten worden uitgevoerd. 

In deze quickstart gebruikt u een manifest om alle objecten te maken die nodig zijn om de Azure Vote-toepassing uit te voeren. Dit manifest bevat twee Kubernetes-implementaties:
* De Azure Vote Python-voorbeeldtoepassingen.
* Een Redis-exemplaar. 

Er worden ook twee Kubernetes-services gemaakt:
* Een interne service voor het Redis-exemplaar.
* Een externe service voor toegang tot de Azure Vote-toepassing via internet.

1. Gebruik in Cloud Shell editor een bestand met de naam `azure-vote.yaml` , zoals:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml`Of 
    * `vi azure-vote.yaml`. 

1. Kopieer de volgende YAML-definitie:

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

1. Implementeer de toepassing met behulp `kubectl apply` van de opdracht en geef de naam van uw YAML-manifest op:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    Uitvoer toont de gemaakte implementaties en services:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren.

Als u de voortgang wilt controleren, gebruikt `kubectl get service` u de opdracht met het argument `--watch` .

```console
kubectl get service azure-vote-front --watch
```

De **external-IP-uitvoer** voor de `azure-vote-front` service wordt in eerste instantie als in behandeling weer *geven.*

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het **EXTERNAL-IP-adres** is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het `kubectl` watch-proces te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:


```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Open een webbrowser naar het externe IP-adres van uw service om de Azure Vote-app te zien.

![Afbeelding van browsen naar de Azure Vote-voorbeeldtoepassing](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Status en logboeken controleren

Toen u het cluster maakte, werd Azure Monitor voor containers ingeschakeld. Azure Monitor voor containers biedt metrische statusgegevens voor zowel het AKS-cluster als de pods die op het cluster worden uitgevoerd.

Het duurt enkele minuten voordat metrische gegevens zijn ingevuld in Azure Portal. De huidige status, uptime en resourcegebruik voor de Azure Vote-pods weergeven:

1. Blader terug naar de AKS-resource in de Azure Portal.
1. Kies **onder** Bewaking aan de linkerkant **de optie Inzichten.**
1. Kies bovenaan + **Filter toevoegen.**
1. Selecteer **Naamruimte** als eigenschap en kies vervolgens *\<All but kube-system\>* .
1. Selecteer **Containers om** ze weer te geven.

De `azure-vote-back` `azure-vote-front` containers en worden weergegeven, zoals wordt weergegeven in het volgende voorbeeld:

![De status van actieve containers in AKS weergeven](media/kubernetes-walkthrough-portal/monitor-containers.png)

Als u logboeken voor de `azure-vote-front` pod wilt weergeven, **selecteert u Containerlogboeken weergeven** in de vervolgkeuzelijst containers. Deze logboeken bevatten de stromen *stdout* en *stderr* van de container.

![De containerlogboeken in AKS weergeven](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Cluster verwijderen

Schoon uw overbodige resources op om Azure-kosten te voorkomen. Selecteer de **knop Verwijderen** op het AKS-clusterdashboard. U kunt ook de [opdracht az aks delete][az-aks-delete] gebruiken in de Cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal.
> 
> Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="get-the-code"></a>Code ophalen

In deze quickstart zijn al bestaande containerafbeeldingen gebruikt om een Kubernetes-implementatie te maken. De gerelateerde toepassingscode, Dockerfile en het Kubernetes-manifestbestand zijn [beschikbaar op GitHub.][azure-vote-app]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Kubernetes-cluster geïmplementeerd en vervolgens een toepassing met meerdere containers geïmplementeerd. Toegang tot het Kubernetes-webdashboard voor uw AKS-cluster.


Ga verder met de zelfstudie Kubernetes-cluster voor meer informatie over AKS door een volledig voorbeeld te volgen, waaronder het bouwen van een toepassing, het implementeren vanuit Azure Container Registry, het bijwerken van een toepassing die wordt uitgevoerd en het schalen en upgraden van uw cluster.

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-delete]: /cli/azure/aks#az_aks_delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations