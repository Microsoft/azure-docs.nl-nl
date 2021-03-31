---
title: Helm-grafieken opslaan
description: Meer informatie over het opslaan van helm-grafieken voor uw Kubernetes-toepassingen met behulp van opslag plaatsen in Azure Container Registry
ms.topic: article
ms.date: 06/12/2020
ms.openlocfilehash: 69b16f35589586787e1c31a0e9755b9030af755d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86537864"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Helm-grafieken pushen en pullen naar een Azure container Registry

Als u snel toepassingen wilt beheren en implementeren voor Kubernetes, kunt u het [open-source helm-pakket beheer][helm]gebruiken. Met helm worden toepassings pakketten gedefinieerd als [grafieken](https://helm.sh/docs/topics/charts/), die worden verzameld en opgeslagen in een [helm-grafiek opslagplaats](https://helm.sh/docs/topics/chart_repository/).

Dit artikel laat u zien hoe u helm-grafieken kunt hosten in een Azure container Registry met behulp van helm 3-opdrachten. In veel scenario's bouwt en uploadt u uw eigen grafieken voor de toepassingen die u ontwikkelt. Zie de [hand leiding voor de gids voor grafiek sjablonen][develop-helm-charts]voor meer informatie over het bouwen van uw eigen helm-grafieken. U kunt ook een bestaand helm-diagram opslaan vanuit een andere helm-opslag plaats.

> [!IMPORTANT]
> Ondersteuning voor helm-grafieken in Azure Container Registry is momenteel beschikbaar als preview-versie. Previews worden aan u beschikbaar gesteld op voor waarde dat u akkoord gaat met de aanvullende [gebruiks voorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="helm-3-or-helm-2"></a>Helm 3 of helm 2?

Als u helm-grafieken wilt opslaan, beheren en installeren, gebruikt u een helm-client en de helm CLI. Grote releases van de helm-client zijn onder andere helm 3 en helm 2. Raadpleeg de [Veelgestelde vragen](https://helm.sh/docs/faq/)over de versie voor meer informatie over de versie verschillen. 

Helm 3 moet worden gebruikt voor het hosten van helm-grafieken in Azure Container Registry. Met helm 3 kunt u:

* Kan een of meer helm-opslag plaatsen in een Azure container Registry maken
* Sla helm 3-grafieken in een REGI ster op als [OCI-artefacten](container-registry-image-formats.md#oci-artifacts). Momenteel is helm 3-ondersteuning voor OCI *experimenteel*.
* Verificatie met het REGI ster met behulp van de `helm registry login` opdracht.
* `helm chart`Opdrachten in de helm CLI gebruiken om helm-grafieken in een REGI ster te pushen, te verzamelen en te beheren
* Gebruiken `helm install` voor het installeren van grafieken naar een Kubernetes-cluster vanuit een lokale opslagplaats cache.
> [!NOTE]
> Vanaf helm 3 kunnen [AZ ACR helm][az-acr-helm] -opdrachten voor gebruik met de helm 2-client worden afgeschaft. Raadpleeg de [product routekaart](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga). Als u eerder helm 2-grafieken hebt geïmplementeerd, raadpleegt u [helm v2 migreren naar v3](https://helm.sh/docs/topics/v2_v3_migration/).

## <a name="prerequisites"></a>Vereisten

De volgende resources zijn nodig voor het scenario in dit artikel:

- **Een Azure container Registry** in uw Azure-abonnement. Maak, indien nodig, een REGI ster met de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md).
- **Helm-client versie 3.1.0 of hoger** -Voer uit `helm version` om uw huidige versie te vinden. Zie Installing [helm][helm-install](Engelstalig) voor meer informatie over het installeren en upgraden van helm.
- **Een Kubernetes-cluster** waarop u een helm-grafiek wilt installeren. Maak indien nodig een [Azure Kubernetes service-cluster][aks-quickstart]. 
- **Azure CLI-versie 2.0.71 of hoger** : Voer uit `az --version` om de versie te vinden. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="enable-oci-support"></a>OCI-ondersteuning inschakelen

Gebruik de `helm version` opdracht om te controleren of u helm 3 hebt geïnstalleerd:

```console
helm version
```

Stel de volgende omgevings variabele in om OCI-ondersteuning in te scha kelen in de helm 3-client. Op dit moment is dit een experimenteel product. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Een voorbeeld grafiek maken

Maak een test diagram met behulp van de volgende opdrachten:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Als eenvoudig voor beeld wijzigt u de map in de `templates` map en verwijdert u eerst de inhoud.

```console
cd hello-world/templates
rm -rf *
```

Maak in de `templates` map een bestand met `configmap.yaml` de naam door de volgende opdracht uit te voeren:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Zie [aan](https://helm.sh/docs/chart_template_guide/getting_started/) de slag in de helm-documenten voor meer informatie over het maken en uitvoeren van dit voor beeld.

## <a name="save-chart-to-local-registry-cache"></a>Grafiek opslaan naar lokale register cache

Wijzig de map naar de submap `hello-world` . Vervolgens voert `helm chart save` u uit om een kopie van de grafiek lokaal op te slaan en maakt u ook een alias met de volledig gekwalificeerde naam van het REGI ster (alle kleine letters) en de doel opslagplaats en-tag. 

In het volgende voor beeld is de register naam *mycontainerregistry*, de doel-opslag plaats is *Hallo wereld* en de label van de doel grafiek is *v1*, maar vervangt waarden voor uw omgeving:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Voer uit `helm chart list` om te bevestigen dat u de grafieken in de lokale register cache hebt opgeslagen. De uitvoer ziet er ongeveer zo uit:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Verifiëren met het REGI ster

Voer de `helm registry login` opdracht in de helm 3 CLI uit om [met het REGI ster te verifiëren](container-registry-authentication.md) met behulp van de referenties die relevant zijn voor uw scenario.

Maak bijvoorbeeld een Azure Active Directory service- [principal met pull-en push-machtigingen](container-registry-auth-service-principal.md#create-a-service-principal) (AcrPush Role) naar het REGI ster. Geef vervolgens de referenties voor de Service-Principal op `helm registry login` . In het volgende voor beeld wordt het wacht woord door een omgevings variabele geleverd:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Push diagram naar REGI ster

Voer de `helm chart push` opdracht in de helm 3 CLI uit om de grafiek te pushen naar de volledig gekwalificeerde doel opslagplaats:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Na een succes volle Push is de uitvoer vergelijkbaar met:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Grafieken weer geven in de opslag plaats

Net als bij afbeeldingen die zijn opgeslagen in een Azure container Registry kunt u [AZ ACR repository][az-acr-repository] -opdrachten gebruiken om de opslag plaatsen weer te geven die als host fungeren voor uw grafieken, en grafiek Tags en-manifesten. 

Voer bijvoorbeeld [AZ ACR repository show][az-acr-repository-show] uit om de eigenschappen te zien van de opslag plaats die u in de vorige stap hebt gemaakt:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

De uitvoer ziet er ongeveer zo uit:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Voer de opdracht [AZ ACR repository show-manifests][az-acr-repository-show-manifests] uit om de details te bekijken van de grafiek die in de opslag plaats is opgeslagen. Bijvoorbeeld:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Output, afgekort in dit voor beeld toont een `configMediaType` van `application/vnd.cncf.helm.config.v1+json` :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Een pull-grafiek naar een lokale cache

Als u een helm-grafiek wilt installeren op Kubernetes, moet de grafiek zich in de lokale cache bevinden. In dit voor beeld voert u eerst uit `helm chart remove` om de bestaande lokale grafiek te verwijderen met de naam `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Voer `helm chart pull` uit om de grafiek te downloaden van het Azure container Registry naar uw lokale cache:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Helm-grafiek exporteren

Als u verder wilt werken met de grafiek, exporteert u deze naar een lokale map met behulp van `helm chart export` . Exporteer bijvoorbeeld het diagram dat u naar de map hebt opgehaald `install` :

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Als u informatie wilt weer geven voor de geëxporteerde grafiek in de opslag plaats, voert `helm show chart` u de opdracht uit in de map waarnaar u de grafiek hebt geëxporteerd.

```console
cd install
helm show chart hello-world
```

Helm retourneert gedetailleerde informatie over de nieuwste versie van uw grafiek, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Helm-grafiek installeren

Voer uit `helm install` om de helm-grafiek te installeren die u hebt opgehaald naar de lokale cache en hebt geëxporteerd. Geef een release naam op zoals *myhelmtest* of voer de `--generate-name` para meter door. Bijvoorbeeld:

```console
helm install myhelmtest ./hello-world
```

Uitvoer nadat de grafiek is geïnstalleerd, is vergelijkbaar met:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Voer de opdracht uit om de installatie te controleren `helm get manifest` . 

```console
helm get manifest myhelmtest
```

De opdracht retourneert de YAML-gegevens in het `configmap.yaml` sjabloon bestand.

Uitvoeren `helm uninstall` om de grafiek release in het cluster te verwijderen:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Grafiek uit het REGI ster verwijderen

Als u een grafiek uit het container register wilt verwijderen, gebruikt u de opdracht [AZ ACR repository delete][az-acr-repository-delete] . Voer de volgende opdracht uit en bevestig de bewerking wanneer u hierom wordt gevraagd:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Volgende stappen

* Zie [helm-grafieken ontwikkelen][develop-helm-charts]voor meer informatie over het maken en implementeren van helm-grafieken.
* Meer informatie over het installeren van toepassingen met helm in [Azure Kubernetes service (AKS)](../aks/kubernetes-helm.md).
* Helm-grafieken kunnen worden gebruikt als onderdeel van het bouw proces van de container. Zie [Azure container Registry-taken gebruiken][acr-tasks]voor meer informatie.

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[acr-tasks]: container-registry-tasks-overview.md
