---
title: GitHub Actions & Azure Kubernetes Service (preview)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Wijzigingen van een pull-aanvraag rechtstreeks in een pull-aanvraag Azure Kubernetes Service met GitHub Actions en Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, GitHub Actions, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 84d4f94cdb756b0bc11026baaa3acf065604c421
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777541"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub Actions & Azure Kubernetes Service (preview)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces biedt een werkstroom met behulp van GitHub Actions waarmee u wijzigingen van een pull-aanvraag rechtstreeks in AKS kunt testen voordat de pull-aanvraag wordt samengevoegd met de main branch. Een toepassing die wordt uitgevoerd om wijzigingen van een pull-aanvraag te controleren, kan het vertrouwen van zowel de ontwikkelaar als de teamleden vergroten. Deze toepassing kan teamleden, zoals productmanagers en ontwerpers, ook helpen deel uit te maken van het beoordelingsproces tijdens de eerste fasen van de ontwikkeling.

In deze handleiding leert u het volgende:

* Azure Dev Spaces instellen op een beheerd Kubernetes-cluster in Azure.
* Implementeer een grote toepassing met meerdere microservices in een dev-ruimte.
* CI/CD instellen met GitHub-acties.
* Test één microservice in een geïsoleerde dev-ruimte binnen de context van de volledige toepassing.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
* [Azure CLI geïnstalleerd][azure-cli-installed].
* [Helm 3 geïnstalleerd.][helm-installed]
* Een GitHub-account [GitHub Actions ingeschakeld.][github-actions-beta-signup]
* De [Azure Dev Spaces Bike Sharing-voorbeeldtoepassing die](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) wordt uitgevoerd op een AKS-cluster.

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken

Een Azure Container Registry (ACR) maken:

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> De naam van uw ACR moet uniek zijn binnen Azure en 5-50 alfanumerieke tekens bevatten. Alle letters die u gebruikt, moeten kleine letters zijn.

Sla de *loginServer-waarde* uit de uitvoer op omdat deze in een latere stap wordt gebruikt.

## <a name="create-a-service-principal-for-authentication"></a>Een service-principal voor verificatie maken

Gebruik [az ad sp create-for-rbac om][az-ad-sp-create-for-rbac] een service-principal te maken. Bijvoorbeeld:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Sla de JSON-uitvoer op omdat deze in een latere stap wordt gebruikt.

Gebruik [az aks show om][az-aks-show] de id van *uw* AKS-cluster weer te geven:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Gebruik [az acr show om][az-acr-show] de id van *de* ACR weer te geven:

```azurecli
az acr show --name <acrName> --query id
```

Gebruik [az role assignment create om][az-role-assignment-create] Inzender toegang *te* geven tot uw AKS-cluster en *AcrPush-toegang* tot uw ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> U moet de eigenaar van zowel uw AKS-cluster als ACR zijn om uw service-principal toegang te geven tot deze resources.

## <a name="configure-your-github-action"></a>Uw GitHub-actie configureren

> [!IMPORTANT]
> U moet GitHub Actions ingeschakeld voor uw opslagplaats. Als u GitHub Actions voor uw opslagplaats wilt inschakelen, gaat u naar uw opslagplaats op GitHub, klikt u op het tabblad Acties en kiest u om acties voor deze opslagplaats in teschakelen.

Navigeer naar de gevorkte opslagplaats en klik op *Instellingen.* Klik op *Geheimen* in de linkerzijbalk. Klik *op Een nieuw geheim toevoegen om* elk nieuw geheim hieronder toe te voegen:

1. *AZURE_CREDENTIALS:* de volledige uitvoer van het maken van de service-principal.
1. *RESOURCE_GROUP:* de resourcegroep voor uw AKS-cluster, in dit voorbeeld *MyResourceGroup.*
1. *CLUSTER_NAME:* de naam van uw AKS-cluster, in dit voorbeeld *MyAKS.*
1. *CONTAINER_REGISTRY:* de *loginServer* voor de ACR.
1. *HOST:* de host voor uw Dev Space, die de vorm *heeft<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>,* in dit voorbeeld *dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET:* de naam van het geheim dat u wilt gebruiken, bijvoorbeeld *demo-secret*.
1. *MASTER_SPACE:* de naam van uw bovenliggende Dev Space, in dit voorbeeld *dev*.
1. *REGISTRY_USERNAME:* de *clientId van* de JSON-uitvoer van het maken van de service-principal.
1. *REGISTRY_PASSWORD:* *het clientSecret van* de JSON-uitvoer van het maken van de service-principal.

> [!NOTE]
> Al deze geheimen worden gebruikt door de GitHub-actie en zijn geconfigureerd in [.github/workflows/bikes.yml.][github-action-yaml]

Als u de hoofdruimte wilt bijwerken nadat uw pr is samengevoegd, voegt u optioneel het *GATEWAY_HOST-geheim* toe. Dit heeft de vorm *<MASTER_SPACE>.gateway.<HOST_SUFFIX>.* In dit voorbeeld is *dev.gateway.fedcab0987.eus.azds.io*. Nadat u uw wijzigingen in de main branch in uw fork hebt samengevoegd, wordt er een andere actie uitgevoerd om uw hele toepassing opnieuw te bouwen en uit te voeren in de hoofddev-ruimte. In dit voorbeeld is de hoofdruimte *dev*. Deze actie is geconfigureerd in [.github/workflows/bikesharing.yml.][github-action-bikesharing-yaml]

Als u wilt dat de wijzigingen in uw pr worden uitgevoerd in een kleine ruimte, moet u ook de geheimen MASTER_SPACE *en* *HOST* bijwerken. Als uw toepassing bijvoorbeeld wordt uitgevoerd in *dev* met een onderliggende ruimte *dev/azureuser1,* om de pr uit te voeren in een onderliggende ruimte *van dev/azureuser1:*

* Werk *MASTER_SPACE* bij naar de onderliggende ruimte die u als bovenliggende ruimte wilt gebruiken, in dit voorbeeld *azureuser1.*
* Werk *HOST* bij *naar<GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, in dit voorbeeld *dev.bikesharingweb.fedcab0987.eus.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Een nieuwe vertakking maken voor codewijzigingen

Navigeer `BikeSharingApp/` naar en maak een nieuwe vertakking met de naam *bike-images.*

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Bewerk [bikes/server.js][bikes-server-js] regels 232 en 233 te verwijderen:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

De sectie moet er nu als de volgende uitzien:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Sla het bestand op en gebruik `git add` en om uw wijzigingen te `git commit` fasen.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Uw wijzigingen pushen

Gebruik `git push` om de nieuwe vertakking naar uw gevorkte opslagplaats te pushen:

```cmd
git push origin bike-images
```

Nadat het pushen is voltooid, gaat u naar uw gevorkte opslagplaats op GitHub om een pull-aanvraag te maken met de hoofdvertakking in uw gevorkte opslagplaats als basisvertakking in vergelijking met de *fietsafbeeldingenvertakking.* 

Nadat uw pull-aanvraag is geopend, gaat u naar het *tabblad* Acties. Controleer of er een nieuwe actie is gestart en of de *Service Bikes wordt* gebouwd.

## <a name="view-the-child-space-with-your-changes"></a>De onderliggende ruimte weergeven met uw wijzigingen

Nadat de actie is voltooid, ziet u een opmerking met een URL naar uw nieuwe onderliggende ruimte op basis van de wijzigingen in de pull-aanvraag.

> [!div class="mx-imgBorder"]
> ![Url voor GitHub-actie](../media/github-actions/github-action-url.png)

Navigeer *naar de bikesharingweb-service* door de URL van de opmerking te openen. Selecteer *Aurelia Contact (klant)* als de gebruiker en selecteer vervolgens een fiets die u wilt huren. Controleer of u de tijdelijke aanduiding voor de fiets niet meer ziet.

Als u uw wijzigingen  samenvoegt in de hoofdvertakking in uw fork, wordt een andere actie uitgevoerd om uw hele toepassing opnieuw te bouwen en uit te voeren in de bovenliggende dev-ruimte. In dit voorbeeld is de bovenliggende ruimte *dev*. Deze actie is geconfigureerd in [.github/workflows/bikesharing.yml.][github-action-bikesharing-yaml]

## <a name="clean-up-your-azure-resources"></a>Uw Azure-resources opschonen

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure Dev Spaces werkt.

> [!div class="nextstepaction"]
> [Hoe Azure Dev Spaces werkt](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
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
