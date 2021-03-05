---
title: GitHub acties & Azure Kubernetes service (preview)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Wijzigingen van een pull-aanvraag rechtstreeks controleren en testen in azure Kubernetes service met GitHub-acties en Azure dev Spaces
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, GitHub acties, helm, Service-Mesh, Service-Mesh-route ring, kubectl, K8S
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 37ad621609f5a5631b498e55483e5d16e8ac4472
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202107"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub acties & Azure Kubernetes service (preview)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure dev Spaces biedt een werk stroom met behulp van GitHub-acties waarmee u wijzigingen van een pull-aanvraag rechtstreeks in AKS kunt testen voordat de pull-aanvraag wordt samengevoegd in de hoofd vertakking van de opslag plaats. Als een toepassing wordt uitgevoerd om de wijzigingen van een pull-aanvraag te bekijken, kan het vertrouwen van zowel de ontwikkelaar als de team leden toenemen. Deze actieve toepassing kan ook team leden, zoals product managers en ontwerpers, deel nemen aan het beoordelings proces tijdens de eerste fase van de ontwikkeling.

In deze handleiding leert u het volgende:

* Stel Azure-ontwikkel ruimten in op een beheerd Kubernetes-cluster in Azure.
* Implementeer een grote toepassing met meerdere micro Services naar een dev-ruimte.
* Stel CI/CD in met GitHub-acties.
* Test één micro service in een geïsoleerde ontwikkel ruimte binnen de context van de volledige toepassing.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
* [Azure CLI geïnstalleerd][azure-cli-installed].
* [Helm 3 is geïnstalleerd][helm-installed].
* Een GitHub-account waarvoor [github-acties zijn ingeschakeld][github-actions-beta-signup].
* De [Azure dev Spaces Bike-voorbeeld toepassing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) die wordt uitgevoerd op een AKS-cluster.

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken

Een Azure Container Registry maken (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> De naam van uw ACR moet uniek zijn binnen Azure en mag 5-50 alfanumerieke tekens bevatten. De letters die u gebruikt, moeten kleine letters zijn.

Sla de waarde *login server* op uit de uitvoer omdat deze wordt gebruikt in een latere stap.

## <a name="create-a-service-principal-for-authentication"></a>Een service-principal voor verificatie maken

Gebruik [AZ AD SP create-for-RBAC][az-ad-sp-create-for-rbac] om een service-principal te maken. Bijvoorbeeld:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Sla de JSON-uitvoer op omdat deze wordt gebruikt in een latere stap.

Gebruik [AZ AKS show][az-aks-show] om de id van uw AKS *-* cluster weer te geven:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Gebruik [AZ ACR show][az-acr-show] om de *id* van de ACR weer te geven:

```azurecli
az acr show --name <acrName> --query id
```

Gebruik [AZ Role Assignment Create][az-role-assignment-create] om *Inzender* toegang te geven tot uw AKS-cluster en *AcrPush* toegang tot uw ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> U moet de eigenaar zijn van uw AKS-cluster en ACR om uw Service-Principal toegang te geven tot deze resources.

## <a name="configure-your-github-action"></a>Uw GitHub-actie configureren

> [!IMPORTANT]
> U moet GitHub-acties hebben ingeschakeld voor uw opslag plaats. Als u GitHub-acties voor uw opslag plaats wilt inschakelen, gaat u naar uw opslag plaats op GitHub, klikt u op het tabblad acties en kiest u acties inschakelen voor deze opslag plaats.

Navigeer naar uw gevorkte opslag plaats en klik op *instellingen*. Klik op *geheimen* in de zijbalk links. Klik op *een nieuw geheim toevoegen* om elk nieuw geheim hieronder toe te voegen:

1. *AZURE_CREDENTIALS*: de volledige uitvoer van het maken van de Service-Principal.
1. *RESOURCE_GROUP*: de resource groep voor uw AKS-cluster, in dit voor beeld *MyResourceGroup*.
1. *CLUSTER_NAME*: de naam van uw AKS-cluster, in dit voor beeld *MyAKS*.
1. *CONTAINER_REGISTRY*: de *login server* voor de ACR.
1. *Host*: de host voor uw dev-ruimte, die de vorm *<MASTER_SPACE>. <APP_NAME>. <* HOST_SUFFIX>, die in dit voor beeld is *dev.bikesharingweb.fedcab0987.Eus.azds.io*.
1. *IMAGE_PULL_SECRET*: de naam van het geheim dat u wilt gebruiken, bijvoorbeeld *demo-geheim*.
1. *MASTER_SPACE*: de naam van de bovenliggende ontwikkel ruimte, die in dit voor beeld *dev* is.
1. *REGISTRY_USERNAME*: de *CLIENTID* van de JSON-uitvoer van de Service-Principal is gemaakt.
1. *REGISTRY_PASSWORD*: de *CLIENTSECRET* van de JSON-uitvoer van de Service-Principal is gemaakt.

> [!NOTE]
> Al deze geheimen worden gebruikt door de GitHub-actie en zijn geconfigureerd in [. github/workflows/Bikes. yml][github-action-yaml].

Indien gewenst kunt u, als u de Master ruimte wilt bijwerken nadat de PR is samengevoegd, het *GATEWAY_HOST* geheim toevoegen, waarmee de formulier *<MASTER_SPACE>. gateway. <* HOST_SUFFIX de>, die in dit voor beeld is *dev.gateway.fedcab0987.Eus.azds.io*. Wanneer u de wijzigingen in de hoofd vertakking in uw Fork samenvoegt, wordt er een andere actie uitgevoerd om uw hele toepassing opnieuw te bouwen en uit te voeren in de hoofd ontwikkelaars ruimte. In dit voor beeld is de hoofd ruimte *dev*. Deze actie is geconfigureerd in [. github/workflows/bikesharing. yml][github-action-bikesharing-yaml].

Als u de wijzigingen in uw PR wilt uitvoeren in een grandchild ruimte, moet u ook de *MASTER_SPACE* en de geheimen van de *host* bijwerken. Als uw toepassing bijvoorbeeld wordt uitgevoerd in *dev* met een *azureuser1* van een onderliggende ruimte, kan de PR alleen worden uitgevoerd in een onderliggende ruimte van *dev/azureuser1*:

* Werk *MASTER_SPACE* bij naar de onderliggende ruimte die u als bovenliggende ruimte wilt, in dit voor beeld *azureuser1*.
* Werk de *host* bij naar *<GRANDPARENT_SPACE>. <APP_NAME>. <* HOST_SUFFIX>, in dit voor beeld *dev.bikesharingweb.fedcab0987.Eus.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Een nieuwe vertakking maken voor code wijzigingen

Ga naar `BikeSharingApp/` en maak een nieuwe vertakking met de naam *fiets afbeeldingen*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

[Fietsen/server.js][bikes-server-js] bewerken om de regels 232 en 233 te verwijderen:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

De sectie moet er nu als volgt uitzien:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Sla het bestand `git add` op en gebruik het `git commit` om de wijzigingen te bewerken.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Uw wijzigingen pushen

Gebruik `git push` om uw nieuwe vertakking naar uw gevorkte opslag plaats te pushen:

```cmd
git push origin bike-images
```

Nadat de push is voltooid, gaat u naar de gesplitste opslag plaats op GitHub om een pull-aanvraag te maken met de *hoofd* vertakking in uw gevorkte opslag plaats als basis vertakking in vergelijking met de vertakking van de *fiets installatie kopieën* .

Nadat uw pull-aanvraag is geopend, gaat u naar het tabblad *acties* . Controleer of een nieuwe actie is gestart en de *Bikes* -service bouwt.

## <a name="view-the-child-space-with-your-changes"></a>De onderliggende ruimte met uw wijzigingen weer geven

Nadat de actie is voltooid, ziet u een opmerking met een URL naar uw nieuwe onderliggende ruimte op basis van de wijzigingen in de pull-aanvraag.

> [!div class="mx-imgBorder"]
> ![GitHub-actie-URL](../media/github-actions/github-action-url.png)

Ga naar de *bikesharingweb* -service door de URL te openen in de opmerking. Selecteer *Aurelia Briggs (klant)* als de gebruiker en selecteer vervolgens een te huur fiets. Controleer of de tijdelijke aanduiding voor de fiets niet meer wordt weer geven.

Als u uw wijzigingen in de *hoofd* vertakking in uw Fork samenvoegt, wordt er een andere actie uitgevoerd om uw hele toepassing opnieuw te bouwen en uit te voeren in de bovenliggende ontwikkel ruimte. In dit voor beeld is de bovenliggende ruimte *dev*. Deze actie is geconfigureerd in [. github/workflows/bikesharing. yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure Dev Spaces werkt.

> [!div class="nextstepaction"]
> [Hoe Azure Dev Spaces werkt](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
