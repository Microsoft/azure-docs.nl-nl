---
title: Helm-grafieken opslaan
description: Meer informatie over het opslaan van Helm-grafieken voor uw Kubernetes-toepassingen met behulp van opslagplaatsen in Azure Container Registry
ms.topic: article
ms.date: 04/15/2021
ms.openlocfilehash: 6698eb8f5e18511717e44bf5dc06a51d8f3903b8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537310"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Helm-grafieken pushen en naar een Azure-containerregister halen

Als u snel toepassingen voor Kubernetes wilt beheren en implementeren, kunt u de [opensource Helm-pakketbeheer gebruiken.][helm] Met Helm worden toepassingspakketten gedefinieerd als [grafieken](https://helm.sh/docs/topics/charts/), die worden verzameld en opgeslagen in een [Helm-grafiekopslagplaats.](https://helm.sh/docs/topics/chart_repository/)

In dit artikel wordt beschreven hoe u Helm-grafieken kunt hosten in een Azure-containerregister met behulp van Helm 3-opdrachten. In veel scenario's zou u uw eigen grafieken maken en uploaden voor de toepassingen die u ontwikkelt. Zie de ontwikkelaarshandleiding voor grafieksjablonen voor meer informatie over het bouwen van [uw eigen Helm-grafieken.][develop-helm-charts] U kunt ook een bestaande Helm-grafiek opslaan vanuit een andere Helm-repo.

## <a name="helm-3-or-helm-2"></a>Helm 3 of Helm 2?

Als u Helm-grafieken wilt opslaan, beheren en installeren, gebruikt u een Helm-client en de Helm CLI. Belangrijke releases van de Helm-client zijn Helm 3 en Helm 2. Zie de veelgestelde vragen over de versie voor meer informatie over [de verschillen in versies.](https://helm.sh/docs/faq/) 

Helm 3 moet worden gebruikt voor het hosten van Helm-grafieken in Azure Container Registry. Met Helm 3 kunt u het volgende doen:

* Kan een of meer Helm-opslagplaatsen maken in een Azure-containerregister
* Sla Helm 3-grafieken op in een register [als OCI-artefacten.](container-registry-image-formats.md#oci-artifacts) Azure Container Registry biedt ga-ondersteuning voor [OCI-artefacten,](container-registry-oci-artifacts.md)waaronder Helm-grafieken.
* Verifieert u met uw register met behulp van de `helm registry login` opdracht .
* Gebruik opdrachten in de Helm CLI om Helm-grafieken in een register te `helm chart` pushen, op te halen en te beheren
* Gebruik om grafieken vanuit een lokale opslagplaatscache te installeren op een `helm install` Kubernetes-cluster.
> [!NOTE]
> Vanaf Helm 3 worden [az acr helm-opdrachten][az-acr-helm] voor gebruik met de Helm 2-client afgeschaft. Er wordt minimaal drie maanden vooraf gewerkt voordat de opdracht wordt verwijderd. Zie Helm v2 migreren naar v3 als u eerder Helm 2-grafieken [hebt geïmplementeerd.](https://helm.sh/docs/topics/v2_v3_migration/)

## <a name="prerequisites"></a>Vereisten

De volgende resources zijn nodig voor het scenario in dit artikel:

- **Een Azure-containerregister** in uw Azure-abonnement. Maak indien nodig een register met behulp van [de Azure Portal](container-registry-get-started-portal.md) of de [Azure CLI.](container-registry-get-started-azure-cli.md)
- **Helm-clientversie 3.1.0 of hoger:** voer uit om `helm version` uw huidige versie te vinden. Zie Helm installeren voor meer informatie over het installeren en upgraden [van Helm.][helm-install]
- **Een Kubernetes-cluster** waarin u een Helm-grafiek installeert. Maak indien nodig een [Azure Kubernetes Service cluster][aks-quickstart]. 
- **Azure CLI versie 2.0.71 of hoger:** voer uit `az --version` om de versie te vinden. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="enable-oci-support"></a>OCI-ondersteuning inschakelen

Gebruik de `helm version` opdracht om te controleren of u Helm 3 hebt geïnstalleerd:

```console
helm version
```

Stel de volgende omgevingsvariabele in om OCI-ondersteuning in teschakelen in de Helm 3-client. Deze ondersteuning is momenteel experimenteel. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Een voorbeeldgrafiek maken

Maak een testgrafiek met behulp van de volgende opdrachten:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Als eenvoudig voorbeeld wijzigt u de map in de `templates` map en verwijdert u daar eerst de inhoud:

```console
cd hello-world/templates
rm -rf *
```

Maak in `templates` de map een bestand met de naam door de volgende opdracht uit te `configmap.yaml` voeren:

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

Zie voor meer informatie over het maken en uitvoeren van [dit Aan de slag](https://helm.sh/docs/chart_template_guide/getting_started/) in de Helm Docs.

## <a name="save-chart-to-local-registry-cache"></a>Grafiek opslaan in lokale registercache

Wijzig de map in de `hello-world` submap. Voer vervolgens uit om een kopie van de grafiek lokaal op te slaan en maak ook een alias met de volledig gekwalificeerde naam van het register (in kleine letters) en de doelopslagplaats en `helm chart save` tag. 

In het volgende voorbeeld is de registernaam *mycontainerregistry,* is de doel-repo *hello-world* en is de doelgrafiektag *v1,* maar vervangt u waarden voor uw omgeving:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Voer `helm chart list` uit om te bevestigen dat u de grafieken in de lokale registercache hebt opgeslagen. De uitvoer ziet er ongeveer zo uit:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Verifiëren met het register

Voer de opdracht uit in de Helm 3 CLI om te verifiëren bij het register met behulp van `helm registry login` referenties die geschikt zijn voor uw scenario. [](container-registry-authentication.md)

Maak bijvoorbeeld een Azure Active Directory service-principal met pull- en [pushmachtigingen](container-registry-auth-service-principal.md#create-a-service-principal) (rol AcrPush) naar het register. Vervolgens moet u de referenties voor de service-principal `helm registry login` aanleveren. In het volgende voorbeeld wordt het wachtwoord gebruikt met behulp van een omgevingsvariabele:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Pushgrafiek naar register

Voer de opdracht uit in de Helm 3 CLI om de grafiek naar de volledig gekwalificeerde `helm chart push` doelopslagplaats te pushen:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Na een geslaagde push is de uitvoer vergelijkbaar met:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Grafieken in de opslagplaats bekijken

Net als bij afbeeldingen die zijn opgeslagen in een Azure-containerregister, kunt u [az acr repository-opdrachten][az-acr-repository] gebruiken om de opslagplaatsen weer te geven die als host voor uw grafieken worden gebruikt, evenals grafiektags en manifesten. 

Voer bijvoorbeeld [az acr repository show uit om][az-acr-repository-show] de eigenschappen te zien van de opslagplaats die u in de vorige stap hebt gemaakt:

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

Voer de [opdracht az acr repository show-manifests][az-acr-repository-show-manifests] uit om details te zien van de grafiek die is opgeslagen in de opslagplaats. Bijvoorbeeld:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Uitvoer, afgekort in dit voorbeeld, toont een `configMediaType` van `application/vnd.cncf.helm.config.v1+json` :

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

## <a name="pull-chart-to-local-cache"></a>Grafiek naar lokale cache pullen

Als u een Helm-grafiek wilt installeren in Kubernetes, moet de grafiek zich in de lokale cache. Voer in dit voorbeeld eerst uit om `helm chart remove` de bestaande lokale grafiek met de naam te `mycontainerregistry.azurecr.io/helm/hello-world:v1` verwijderen:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Voer `helm chart pull` uit om de grafiek te downloaden van het Azure-containerregister naar uw lokale cache:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Helm-grafiek exporteren

Als u verder wilt werken met de grafiek, exporteert u deze naar een lokale map met behulp van `helm chart export` . Exporteert bijvoorbeeld de grafiek die u naar de map hebt `install` gehaald:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Als u informatie over de geëxporteerde grafiek in de repo wilt weergeven, moet u de opdracht uitvoeren in de map waarin u `helm show chart` de grafiek hebt geëxporteerd.

```console
cd install
helm show chart hello-world
```

Helm retourneert gedetailleerde informatie over de meest recente versie van uw grafiek, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Helm-grafiek installeren

Voer `helm install` uit om de Helm-grafiek te installeren die u naar de lokale cache hebt gehaald en geëxporteerd. Geef een releasenaam op, zoals *myhelmtest* of geef de `--generate-name` parameter door. Bijvoorbeeld:

```console
helm install myhelmtest ./hello-world
```

De uitvoer na een geslaagde grafiekinstallatie is vergelijkbaar met:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Voer de opdracht uit om de installatie te `helm get manifest` controleren. 

```console
helm get manifest myhelmtest
```

De opdracht retourneert de YAML-gegevens in uw `configmap.yaml` sjabloonbestand.

Voer `helm uninstall` uit om de grafiekversie op uw cluster te verwijderen:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Grafiek uit het register verwijderen

Als u een grafiek uit het containerregister wilt verwijderen, gebruikt u [de opdracht az acr repository delete.][az-acr-repository-delete] Voer de volgende opdracht uit en bevestig de bewerking wanneer u hier om wordt gevraagd:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Volgende stappen

* Zie Helm-grafieken ontwikkelen voor meer informatie over het maken en implementeren [van Helm-grafieken.][develop-helm-charts]
* Meer informatie over het installeren van toepassingen met Helm in [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Helm-grafieken kunnen worden gebruikt als onderdeel van het buildproces van de container. Zie Use Azure Container Registry-taken voor [meer informatie.][acr-tasks]

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
