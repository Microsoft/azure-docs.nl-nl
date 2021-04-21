---
title: Installatiekopieën vergrendelen
description: Stel kenmerken in voor een containerafbeelding of opslagplaats, zodat deze niet kan worden verwijderd of overschreven in een Azure-containerregister.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 340beb1bb6666ddf0de7de38adee6be71f5f52bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772339"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Een containerafbeelding vergrendelen in een Azure-containerregister

In een Azure-containerregister kunt u een versie van een afbeelding of een opslagplaats vergrendelen, zodat deze niet kan worden verwijderd of bijgewerkt. Als u een afbeelding of opslagplaats wilt vergrendelen, moet u de kenmerken ervan bijwerken met de Azure CLI-opdracht [az acr repository update][az-acr-repository-update]. 

Voor dit artikel moet u De Azure CLI uitvoeren in Azure Cloud Shell of lokaal (versie 2.0.55 of hoger wordt aanbevolen). Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!IMPORTANT]
> Dit artikel is niet van toepassing op het vergrendelen van een heel register, bijvoorbeeld met behulp van Instellingen **> Vergrendelingen** in de Azure Portal of `az lock` opdrachten in de Azure CLI. Als u een registerresource vergrendelt, voorkomt u niet dat u gegevens in opslagplaatsen kunt maken, bijwerken of verwijderen. Het vergrendelen van een register is alleen van invloed op beheerbewerkingen zoals het toevoegen of verwijderen van replicaties of het verwijderen van het register zelf. Meer informatie vindt u in [Resources vergrendelen om onverwachte wijzigingen te voorkomen.](../azure-resource-manager/management/lock-resources.md)

## <a name="scenarios"></a>Scenario's

Een gelabelde afbeelding in Azure Container Registry *is* standaard veranderlijk, dus met de juiste machtigingen kunt u een afbeelding met dezelfde tag herhaaldelijk bijwerken en naar een register pushen. Container-afbeeldingen kunnen [indien nodig](container-registry-delete.md) ook worden verwijderd. Dit gedrag is handig wanneer u afbeeldingen ontwikkelt en een grootte voor uw register moet onderhouden.

Wanneer u echter een containerafbeelding naar productie implementeert, hebt u mogelijk een *onveranderbare* containerafbeelding nodig. Een onveranderbare afbeelding is een afbeelding die u niet per ongeluk kunt verwijderen of overschrijven.

Zie Recommendations for tagging and versioning container images (Aanbevelingen voor het [taggen en versieren van containerafbeeldingen)](container-registry-image-tag-version.md) voor strategieën voor het taggen en versien van afbeeldingen in uw register.

Gebruik de [opdracht az acr repository update][az-acr-repository-update] om kenmerken van de opslagplaats in te stellen, zodat u het volgende kunt doen:

* Een versie van een afbeelding of een volledige opslagplaats vergrendelen

* Een versie of opslagplaats van een afbeelding beveiligen tegen verwijdering, maar updates toestaan

* Leesbewerkingen (pull-bewerkingen) voorkomen op een versie van een afbeelding of een volledige opslagplaats

Zie de volgende secties voor voorbeelden. 

## <a name="lock-an-image-or-repository"></a>Een afbeelding of opslagplaats vergrendelen 

### <a name="show-the-current-repository-attributes"></a>De huidige kenmerken van de opslagplaats tonen
Als u de huidige kenmerken van een opslagplaats wilt zien, moet u de volgende [opdracht az acr repository show][az-acr-repository-show] uitvoeren:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>De huidige afbeeldingskenmerken tonen
Voer de volgende opdracht [az acr repository show][az-acr-repository-show] uit om de huidige kenmerken van een tag te zien:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Een afbeelding vergrendelen op tag

Als u de *afbeelding myrepo/myimage:tag* in *myregistry* wilt vergrendelen, moet u de volgende [opdracht az acr repository update][az-acr-repository-update] uitvoeren:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Een afbeelding vergrendelen op manifest digest

Voer de volgende opdracht uit om een afbeelding *myrepo/myimage* te vergrendelen die wordt geïdentificeerd door manifest digest (SHA-256 hash, weergegeven als `sha256:...` ). (Voer de opdracht [az acr repository show-manifests][az-acr-repository-show-manifests] uit om de manifest digest te vinden die is gekoppeld aan een of meer afbeeldingstags.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Een opslagplaats vergrendelen

Als u de *opslagplaats myrepo/myimage en* alle afbeeldingen in de opslagplaats wilt vergrendelen, moet u de volgende opdracht uitvoeren:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Een afbeelding of opslagplaats beveiligen tegen verwijdering

### <a name="protect-an-image-from-deletion"></a>Een afbeelding beveiligen tegen verwijdering

Voer de volgende opdracht uit om toe te staan dat de afbeelding *myrepo/myimage:tag* wordt bijgewerkt, maar niet wordt verwijderd:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Een opslagplaats beveiligen tegen verwijdering

Met de volgende opdracht stelt *u de opslagplaats myrepo/myimage* in, zodat deze niet kan worden verwijderd. Afzonderlijke afbeeldingen kunnen nog steeds worden bijgewerkt of verwijderd.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Leesbewerkingen op een afbeelding of opslagplaats voorkomen

Voer de volgende opdracht uit om leesbewerkingen (pull-bewerkingen) op de afbeelding *myrepo/myimage:tag* te voorkomen:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Voer de volgende opdracht uit om leesbewerkingen op alle afbeeldingen in de *opslagplaats myrepo/myimage* te voorkomen:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Een afbeelding of opslagplaats ontgrendelen

Voer de volgende opdracht uit om het standaardgedrag van de afbeelding *myrepo/myimage:tag* te herstellen, zodat deze kan worden verwijderd en bijgewerkt:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Voer de volgende opdracht uit om het standaardgedrag van de *opslagplaats myrepo/myimage* en alle afbeeldingen te herstellen, zodat ze kunnen worden verwijderd en bijgewerkt:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over het gebruik van de [opdracht az acr repository update][az-acr-repository-update] om te voorkomen dat versies van de afbeelding in een opslagplaats worden verwijderd of bijgewerkt. Zie de opdrachtverwijzing [az acr repository update][az-acr-repository-update] om aanvullende kenmerken in te stellen.

Als u de kenmerken wilt zien die zijn ingesteld voor een versie of opslagplaats van een afbeelding, gebruikt u [de opdracht az acr repository show.][az-acr-repository-show]

Zie Containerafbeeldingen verwijderen in Azure Container Registry voor [meer informatie over verwijderbewerkingen.][container-registry-delete]

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az_acr_repository_update
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md
