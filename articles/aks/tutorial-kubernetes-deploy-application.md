---
title: 'Zelfstudie voor Kubernetes in Azure: een toepassing implementeren'
description: In deze zelfstudie over Azure Kubernetes Service (AKS) gaat u een toepassing met meerdere containers in uw cluster implementeren met behulp van een aangepaste installatiekopie die is opgeslagen in Azure Container Registry.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: a0de097a545a831e39a671fe4cf5eadcd336ce24
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250176"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Zelfstudie: Toepassingen uitvoeren in AKS (Azure Kubernetes Service)

Kubernetes biedt een gedistribueerd platform voor toepassingen in containers. U gaat uw eigen toepassingen en services implementeren in een Kubernetes-cluster, en u laat het cluster de beschikbaarheid en connectiviteit beheren. In deze zelfstudie, deel vier van zeven, wordt een voorbeeldtoepassing geïmplementeerd in een Kubernetes-cluster. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een Kubernetes-manifestbestand bijwerken
> * Een toepassing in Kubernetes uitvoeren
> * De toepassing testen

In latere zelf studies wordt deze toepassing uitgeschaald en bijgewerkt.

In deze snelstart wordt ervan uitgegaan dat u een basisbegrip hebt van Kubernetes-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelfstudies is een toepassing verpakt in een containerinstallatiekopie, is deze installatiekopie geüpload naar Azure Container Registry en is een Kubernetes-cluster gemaakt.

Om deze zelfstudie te voltooien hebt u het vooraf gemaakte Kubernetes-manifestbestand `azure-vote-all-in-one-redis.yaml` nodig. Dit bestand is met de broncode van de toepassing gedownload in een vorige zelfstudie. Controleer of u een kloon van de opslagplaats hebt gemaakt en of u mappen in de gekloonde opslagplaats hebt gewijzigd. Als u deze stappen niet hebt uitgevoerd en u deze zelfstudie wilt volgen, begint u met [Tutorial 1 – Create container images][aks-tutorial-prepare-app] (Zelfstudie 1: containerinstallatiekopieën maken).

Voor deze zelfstudie moet u Azure CLI versie 2.0.53 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="update-the-manifest-file"></a>Het manifestbestand bijwerken

In deze zelfstudies wordt met een instantie van Azure Container Registry (ACR) de containerinstallatiekopie voor de voorbeeldtoepassing opgeslagen. Om de toepassing te implementeren, moet u de naam van de installatiekopie in het Kubernetes-manifestbestand bijwerken zodat de naam van de ACR-aanmeldingsserver erin is opgenomen.

Haal de naam van de ACR-aanmeldingsserver als volgt op met de opdracht [az acr list][az-acr-list].

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Het voorbeeldmanifestbestand van de Git-opslagplaats dat in de eerste zelfstudie is gekloond, gebruikt *microsoft* als de naam van de aanmeldingsserver. Zorg ervoor dat u zich in de gekloonde map *azure-voting-app-redis* bevindt en open het manifestbestand met een teksteditor, zoals `vi`:

```console
vi azure-vote-all-in-one-redis.yaml
```

Vervang *microsoft* door de naam van de ACR-aanmeldingsserver. De naam van de installatie kopie vindt u in regel 60 van het manifest bestand. In het volgende voorbeeld ziet u de standaardnaam van de installatiekopie:

```yaml
containers:
- name: azure-vote-front
  image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
```

Geef uw eigen naam van de ACR-aanmeldingsserver op zodat uw manifestbestand eruitziet zoals in het volgende voorbeeld:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Sla het bestand op en sluit het. In `vi` gebruikt u `:wq`.

## <a name="deploy-the-application"></a>De toepassing implementeren

Gebruik de opdracht [kubectl apply][kubectl-apply] om uw toepassing te implementeren. Deze opdracht parseert het manifestbestand en maakt de gedefinieerde Kubernetes-objecten. Geef het voorbeeldmanifestbestand op, zoals wordt weergegeven in het volgende voorbeeld:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

In de volgende voorbeelduitvoer ziet u dat de resources zijn gemaakt in het AKS-cluster:

```console
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren.

Gebruik de opdracht [kubectl get service][kubectl-get] met het argument `--watch` om de voortgang te controleren.

```console
kubectl get service azure-vote-front --watch
```

Eerst wordt het *Extern IP-adres* voor de service *azure-vote-front* weergegeven als *in behandeling*:

```output
azure-vote-front   LoadBalancer   10.0.34.242   <pending>     80:30676/TCP   5s
```

Zodra het *EXTERNAL-IP*-adres is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```output
azure-vote-front   LoadBalancer   10.0.34.242   52.179.23.131   80:30676/TCP   67s
```

Open een webbrowser naar het externe IP-adres van uw service als u de toepassing in actie wilt zien:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote.png" alt-text="Scherm opname van de container afbeelding Azure stem-app die wordt uitgevoerd in een AKS-cluster dat wordt geopend in een lokale webbrowser" lightbox="./media/container-service-kubernetes-tutorials/azure-vote.png":::

Als de toepassing niet is geladen, kan dit zijn veroorzaakt door een autorisatieprobleem met het register van de installatiekopie. Als u de status van uw containers wilt bekijken, gebruikt u de opdracht `kubectl get pods`. Zie [Verifiëren bij Azure Container Registry vanuit Azure Kubernetes Service](cluster-container-registry-integration.md) als de containerinstallatiekopieën niet kunnen worden opgehaald.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie is een Azure Vote-voorbeeldtoepassing geïmplementeerd in een Kubernetes-cluster in AKS. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Kubernetes-manifestbestand bijwerken
> * Een toepassing in Kubernetes uitvoeren
> * De toepassing testen

Ga naar de volgende zelfstudie om te leren hoe u zowel een Kubernetes-toepassing als de onderliggende Kubernetes-infrastructuur schaalt.

> [!div class="nextstepaction"]
> [Kubernetes-toepassing en -infrastructuur schalen][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
