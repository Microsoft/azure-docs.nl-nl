---
title: Installatiekopieën vergrendelen
description: Stel kenmerken in voor een container installatie kopie of opslag plaats, zodat deze niet kan worden verwijderd of overschreven in een Azure container Registry.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77659693"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Een container installatie kopie in een Azure container Registry vergren delen

In een Azure container Registry kunt u een installatie kopie versie of een opslag plaats vergren delen zodat deze niet kan worden verwijderd of bijgewerkt. Als u een afbeelding of opslag plaats wilt vergren delen, werkt u de kenmerken bij met de Azure CLI-opdracht [AZ ACR repository update][az-acr-repository-update]. 

Voor dit artikel moet u de Azure CLI in Azure Cloud Shell of lokaal uitvoeren (versie 2.0.55 of hoger aanbevolen). Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!IMPORTANT]
> Dit artikel is niet van toepassing op het vergren delen van een volledig REGI ster, bijvoorbeeld met behulp van **instellingen > sloten** in de Azure Portal, of `az lock` opdrachten in de Azure cli. Het vergren delen van een register bron voor komt niet dat u gegevens in opslag plaatsen kunt maken, bijwerken of verwijderen. Het vergren delen van een REGI ster is alleen van invloed op beheer bewerkingen, zoals het toevoegen of verwijderen van replicaties of het verwijderen van het REGI ster zelf. Meer informatie over [het vergren delen van resources om onverwachte wijzigingen te voor komen](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Scenario's

Een gecodeerde afbeelding in Azure Container Registry is standaard *onveranderbaar*, dus met de juiste machtigingen kunt u herhaaldelijk een installatie kopie met dezelfde tag naar een REGI ster bijwerken en pushen. Container installatie kopieën kunnen ook naar behoefte worden [verwijderd](container-registry-delete.md) . Dit gedrag is handig wanneer u installatie kopieën ontwikkelt en een grootte voor het REGI ster moet behouden.

Wanneer u echter een container installatie kopie implementeert voor productie, hebt u mogelijk een *onveranderlijke* container installatie kopie nodig. Een onveranderlijke installatie kopie is een afbeelding die u niet per ongeluk kunt verwijderen of overschrijven.

Zie [aanbevelingen voor het coderen en versie beheer van container installatie kopieën](container-registry-image-tag-version.md) voor strategieën voor het labelen en de installatie kopieën in het REGI ster.

Gebruik de opdracht [AZ ACR repository update][az-acr-repository-update] om opslagplaats kenmerken in te stellen, zodat u het volgende kunt doen:

* Een installatie kopie versie of een volledige opslag plaats vergren delen

* Een installatie kopie versie of opslag plaats beveiligen tegen verwijderen, maar updates toestaan

* Voor komen dat lees-(pull)-bewerkingen worden uitgevoerd op een installatie kopie versie of een volledige opslag plaats

Zie de volgende secties voor voor beelden. 

## <a name="lock-an-image-or-repository"></a>Een afbeelding of opslag plaats vergren delen 

### <a name="show-the-current-repository-attributes"></a>De huidige opslagplaats kenmerken weer geven
Als u de huidige kenmerken van een opslag plaats wilt weer geven, voert u de volgende opdracht [AZ ACR repository show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>De huidige afbeeldings kenmerken weer geven
Als u de huidige kenmerken van een tag wilt weer geven, voert u de volgende opdracht [AZ ACR repository show][az-acr-repository-show] uit:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Een afbeelding vergren delen op label

Als u de *myrepo/myimage: tag-* afbeelding in *myregistry* wilt vergren delen, voert u de volgende opdracht [AZ ACR repository update][az-acr-repository-update] uit:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Een installatie kopie vergren delen op Manifest Digest

Als u een *myrepo/myimage-* installatie kopie wilt vergren delen die wordt geïdentificeerd door de manifest Digest (SHA-256-Hash, weer gegeven als `sha256:...` ), voert u de volgende opdracht uit. (Als u de manifest Digest wilt vinden die is gekoppeld aan een of meer afbeeldings codes, voert u de opdracht [AZ ACR repository show-manifests][az-acr-repository-show-manifests] uit.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Een opslag plaats vergren delen

Voer de volgende opdracht uit om de *myrepo/myimage-* opslag plaats en alle installatie kopieën daarin te vergren delen:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Een installatie kopie of opslag plaats beveiligen tegen verwijderen

### <a name="protect-an-image-from-deletion"></a>Een installatie kopie beveiligen tegen verwijderen

Voer de volgende opdracht uit als u wilt toestaan dat de afbeelding *myrepo/myimage: tag* wordt bijgewerkt, maar niet verwijderd:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Een opslag plaats beveiligen tegen verwijderen

Met de volgende opdracht wordt de *myrepo/myimage-* opslag plaats ingesteld zodat deze niet kan worden verwijderd. Afzonderlijke installatie kopieën kunnen nog steeds worden bijgewerkt of verwijderd.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Voor komen dat lees bewerkingen worden uitgevoerd op een afbeelding of opslag plaats

Voer de volgende opdracht uit om te voor komen dat lees-en pull-bewerkingen op de *myrepo/myimage: tag* -afbeelding worden uitgevoerd:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Voer de volgende opdracht uit om te voor komen dat lees bewerkingen op alle installatie kopieën in de *myrepo/myimage-* opslag plaats:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Een installatie kopie of opslag plaats ontgrendelen

Als u het standaard gedrag van de *myrepo/myimage: tag-* afbeelding wilt herstellen, zodat deze kan worden verwijderd en bijgewerkt, voert u de volgende opdracht uit:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Voer de volgende opdracht uit om het standaard gedrag van de *myrepo/myimage-* opslag plaats en alle installatie kopieën zodanig te herstellen dat ze kunnen worden verwijderd en bijgewerkt:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de opdracht [AZ ACR repository update][az-acr-repository-update] gebruikt om te voor komen dat installatie kopieën worden verwijderd of bijgewerkt in een opslag plaats. Als u aanvullende kenmerken wilt instellen, raadpleegt u de naslag informatie voor de opdracht [AZ ACR repository update][az-acr-repository-update] .

Als u de kenmerken wilt zien die zijn ingesteld voor een installatie kopie versie of opslag plaats, gebruikt u de opdracht [AZ ACR repository show][az-acr-repository-show] .

Zie [container installatie kopieën in azure container Registry verwijderen][container-registry-delete]voor meer informatie over Verwijder bewerkingen.

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

