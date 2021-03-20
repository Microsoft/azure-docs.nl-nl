---
title: Afbeeldingen met meerdere architecturen in uw REGI ster
description: Gebruik uw Azure container Registry om multi-Architecture (multi-Arch) installatie kopieën te bouwen, te importeren, op te slaan en te implementeren
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: f8467cd3108ae4faea9ecb4c9d9ae339f476c311
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100012312"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Afbeeldingen met meerdere architecturen in uw Azure container Registry

In dit artikel vindt u een beschrijving van *multi-Architecture* installatie kopieën (*multi-Arch*) en hoe u Azure container Registry-functies kunt gebruiken om ze te maken, op te slaan en te gebruiken. 

Een multi-Arch afbeelding is een type container installatie kopie die varianten voor verschillende architecturen kan combi neren en soms voor verschillende besturings systemen. Wanneer een installatie kopie wordt uitgevoerd met ondersteuning voor meerdere architecturen, selecteren container clients automatisch een afbeeldings variant die overeenkomt met uw besturings systeem en architectuur.

## <a name="manifests-and-manifest-lists"></a>Manifesten en manifest lijsten

Multi-Arch afbeeldingen zijn gebaseerd op afbeeldings manifesten en manifest lijsten.

### <a name="manifest"></a>Manifest

Elke container installatie kopie wordt vertegenwoordigd door een [manifest](container-registry-concepts.md#manifest). Een manifest is een JSON-bestand waarmee de installatie kopie op unieke wijze wordt geïdentificeerd en waarnaar wordt verwezen in de bijbehorende lagen. 

Een basis manifest voor een Linux- `hello-world` installatie kopie ziet er ongeveer als volgt uit:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
U kunt een manifest in Azure Container Registry weer geven met behulp van de Azure Portal of hulpprogram ma's, zoals de opdracht [AZ ACR repository show-manifests](/cli/azure/acr/repository#az_acr_repository_show_manifests) in de Azure cli.

### <a name="manifest-list"></a>Manifest lijst

Een *manifest lijst* voor een multi-Arch afbeelding (in het algemeen bekend als een [afbeeldings index](https://github.com/opencontainers/image-spec/blob/master/image-index.md) voor OCI-afbeeldingen) is een verzameling (index) van installatie kopieën en u maakt er een met een of meer afbeeldings namen. Het bevat details over elk van de installatie kopieën, zoals het ondersteunde besturings systeem en de architectuur, de grootte en de manifest samenvatting. De lijst met manifesten kan worden gebruikt op dezelfde manier als de naam van een installatie kopie in `docker pull` en `docker run` opdrachten. 

De `docker` cli beheert manifesten en manifest lijsten met behulp van de opdracht [docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/) .

> [!NOTE]
> Momenteel zijn de `docker manifest` opdracht en subopdrachten experimenteel. Raadpleeg de docker-documentatie voor meer informatie over het gebruik van experimentele opdrachten.

U kunt een manifest lijst weer geven met behulp van de `docker manifest inspect` opdracht. Hieronder vindt u de uitvoer voor de multi-arch `mcr.microsoft.com/mcr/hello-world:latest` -afbeelding, die drie manifesten heeft: twee voor Linux OS architecturen en één voor een Windows-architectuur.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

Wanneer een lijst met meerdere boog manifesten wordt opgeslagen in Azure Container Registry, kunt u ook de lijst met manifesten weer geven met behulp van de Azure Portal of met hulpprogram ma's zoals de opdracht [AZ ACR repository show-manifests](/cli/azure/acr/repository#az_acr_repository_how_manifests) .

## <a name="import-a-multi-arch-image"></a>Een multi-Arch-afbeelding importeren 

Een bestaande multi-Arch afbeelding kan worden geïmporteerd in een Azure container Registry met behulp van de opdracht [AZ ACR import](/cli/azure/acr#az_acr_import) . De syntaxis voor het importeren van afbeeldingen is hetzelfde als bij een installatie kopie met één architectuur. Net als bij het importeren van een afbeelding met één architectuur, worden met de import van een multi-Arch-afbeelding geen docker-opdrachten gebruikt. 

Zie [container installatie kopieën naar een container register importeren](container-registry-import-images.md)voor meer informatie.

## <a name="push-a-multi-arch-image"></a>Een multi-Arch-afbeelding pushen

Wanneer u werk stromen hebt gemaakt voor het maken van container installatie kopieën voor verschillende architecturen, voert u de volgende stappen uit om een multi-Arch-installatie kopie naar uw Azure container Registry te pushen.

1. Voorzie en push elke architectuur-specifieke installatie kopie naar het container register. In het volgende voor beeld wordt uitgegaan van twee Linux-architecturen: arm64 en amd64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. Voer uit `docker manifest create` om een manifest lijst te maken om de voor gaande afbeeldingen te combi neren in een multi-Arch-afbeelding.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Push het manifest naar het container register met `docker manifest push` :

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. Gebruik de `docker manifest inspect` opdracht om de lijst met manifesten weer te geven. Een voor beeld van een opdracht uitvoer wordt weer gegeven in een voor gaande sectie.

Nadat u het multi-Arch-manifest naar het REGI ster hebt gepusht, kunt u op dezelfde manier werken met de multi-Arch-afbeelding als bij een installatie kopie met één architectuur. Stel bijvoorbeeld de installatie kopie met behulp van `docker pull` en gebruik [AZ ACR repository](/cli/azure/acr/repository#az_acr_repository) -opdrachten om tags, manifesten en andere eigenschappen van de installatie kopie weer te geven.

## <a name="build-and-push-a-multi-arch-image"></a>Een multi-Arch-afbeelding bouwen en pushen

U kunt met behulp van functies van [ACR-taken](container-registry-tasks-overview.md)een multi-Arch-afbeelding bouwen en pushen naar uw Azure container Registry. U kunt bijvoorbeeld een [taak met meerdere stappen](container-registry-tasks-multi-step.md) definiëren in een yaml-bestand dat een Linux-multi-Arch-afbeelding bouwt.

In het volgende voor beeld wordt ervan uitgegaan dat u afzonderlijke Dockerfiles hebt voor twee architecturen, arm64 en amd64. Hiermee worden de architectuur-specifieke installatie kopieën gebouwd en gepusht. vervolgens wordt een multi-Arch-manifest met de tag gemaakt en gepusht `latest` .

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Volgende stappen

* Gebruik [Azure-pijp lijnen](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) om container installatie kopieën voor verschillende architecturen te maken.
* Meer informatie over het bouwen van installatie kopieën met meerdere platforms met behulp van de experimentele docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) -invoeg toepassing.

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
