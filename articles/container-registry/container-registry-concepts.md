---
title: Over registers, opslagplaatsen, afbeeldingen en artefacten
description: Inleiding tot de belangrijkste concepten van Azure-containerregisters, opslagplaatsen, containerafbeeldingen en andere artefacten.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 64ab3812b3f23a7b3a480d3530c82bd39f2d29a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784079"
---
# <a name="about-registries-repositories-and-artifacts"></a>Over registers, opslagplaatsen en artefacten

In dit artikel worden de belangrijkste concepten van containerregisters, opslagplaatsen en containerafbeeldingen en gerelateerde artefacten beschreven. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Register, opslagplaatsen en artefacten":::

## <a name="registry"></a>Register

Een *containerregister* is een service die containerafbeeldingen en gerelateerde artefacten opseert en distribueert. Docker Hub is een voorbeeld van een openbaar containerregister dat fungeert als een algemene catalogus met Docker-containerafbeeldingen. Azure Container Registry biedt gebruikers directe controle over hun containerinhoud, met geïntegreerde verificatie, [geo-replicatie](container-registry-geo-replication.md) ter ondersteuning van wereldwijde distributie en betrouwbaarheid voor implementaties dicht bij het netwerk, configuratie van virtuele netwerken [met Private Link,](container-registry-private-link.md) [tagvergrendeling](container-registry-image-lock.md)en vele andere verbeterde functies. 

Naast met Docker compatibele containerafbeeldingen biedt Azure Container Registry [](container-registry-image-formats.md) ondersteuning voor diverse inhoudsartefacten, waaronder Helm-grafieken en OCI-afbeeldingsindelingen (Open Container Initiative).

## <a name="repository"></a>Opslagplaats

Een *opslagplaats* is een verzameling containerafbeeldingen of andere artefacten in een register met dezelfde naam, maar met verschillende tags. De volgende drie afbeeldingen staan bijvoorbeeld in de `acr-helloworld` opslagplaats:

- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Namen van opslagplaatsen kunnen ook [naamruimten bevatten.](container-registry-best-practices.md#repository-namespaces) Met naamruimten kunt u gerelateerde opslagplaatsen en het eigendom van artefacten in uw organisatie identificeren met behulp van namen met slash-scheidingstekens. Het register beheert echter alle opslagplaatsen onafhankelijk, niet als een hiërarchie. Bijvoorbeeld:

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

Namen van opslagplaatsen kunnen alleen kleine alfanumerieke tekens, punten, streepjes, onderstrepingstekens en slashes bevatten. 

Zie Open Container Initiative Distribution Specification (Specificatie van Open Container Initiative Distribution) voor volledige naamgevingsregels [voor opslagplaatsen.](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)

## <a name="artifact"></a>Artefact

Een containerafbeelding of ander artefact in een register is gekoppeld aan een of meer tags, heeft een of meer lagen en wordt geïdentificeerd door een manifest. Inzicht in hoe deze onderdelen zich tot elkaar verhouden, kan u helpen uw register effectief te beheren.

### <a name="tag"></a>Tag

De *tag* voor een afbeelding of een ander artefact geeft de versie aan. Eén artefact in een opslagplaats kan worden toegewezen aan een of meer tags en kan ook 'zonder tag' zijn. Dat wil zeggen dat u alle tags uit een afbeelding kunt verwijderen, terwijl de gegevens van de afbeelding (de lagen) in het register blijven.

De opslagplaats (of opslagplaats en naamruimte) plus een tag definieert de naam van een afbeelding. U kunt een afbeelding pushen en pullen door de naam op te geven in de push- of pull-bewerking. De tag wordt standaard gebruikt als u er geen op geeft `latest` in uw Docker-opdrachten.

Hoe u containerafbeeldingen tagt, wordt begeleid door uw scenario's om ze te ontwikkelen of te implementeren. Stabiele tags worden bijvoorbeeld aanbevolen voor het onderhouden van uw basisafbeeldingen en unieke tags voor het implementeren van afbeeldingen. Zie Recommendations for [tagging and versioning container images (Aanbevelingen voor het taggen en versieren van containerafbeeldingen) voor meer informatie.](container-registry-image-tag-version.md)

Zie de Docker-documentatie voor [naamgevingsregels voor tags.](https://docs.docker.com/engine/reference/commandline/tag/)

### <a name="layer"></a>Laag

Containerafbeeldingen en artefacten zijn uit een of meer *lagen.* Verschillende artefacttypen definiëren lagen anders. In een Docker-containerafbeelding komt elke laag bijvoorbeeld overeen met een regel in de Dockerfile die de afbeelding definieert:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Lagen van een containerafbeelding":::

Artefacten in een register delen gemeenschappelijke lagen, waardoor de efficiëntie van de opslag wordt verbeterd. Verschillende afbeeldingen in verschillende opslagplaatsen kunnen bijvoorbeeld een gemeenschappelijke ASP.NET Core-basislaag hebben, maar er wordt slechts één kopie van die laag opgeslagen in het register. Delen van lagen optimaliseert ook laagdistributie naar knooppunten, met meerdere artefacten die algemene lagen delen. Als een afbeelding die al op een knooppunt staat, de laag ASP.NET Core als basis bevat, wordt de laag niet door de volgende pull van een andere afbeelding die verwijst naar dezelfde laag naar het knooppunt. In plaats daarvan verwijst deze naar de laag die al op het knooppunt staat.

Lagen worden niet gedeeld tussen registers om veilige isolatie en bescherming te bieden tegen mogelijke laagbewerkingen.

### <a name="manifest"></a>Manifest

Elke container-afbeelding of elk artefact dat naar een containerregister wordt pusht, is gekoppeld aan een *manifest*. Het manifest, dat door het register wordt gegenereerd wanneer de inhoud wordt pushen, identificeert de artefacten op unieke manier en specificeert de lagen. U kunt de manifesten voor een opslagplaats weer geven met de Azure CLI-opdracht [az acr repository show-manifests.][az-acr-repository-show-manifests] 

Een basismanifest voor een `hello-world` Linux-afbeelding ziet er ongeveer als volgt uit:

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

U kunt de manifesten voor een opslagplaats weer geven met de Azure CLI-opdracht [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Vermeld bijvoorbeeld de manifesten voor de opslagplaats 'acr-helloworld':

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp&quot;: &quot;2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp&quot;: &quot;2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp&quot;: &quot;2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Manifest digest

Manifesten worden geïdentificeerd door een unieke SHA-256-hash of *manifest digest.* Elke afbeelding of elk artefact, ongeacht of deze is getagd of niet, wordt geïdentificeerd door de samenvatting ervan. De samenvattingswaarde is uniek, zelfs als de laaggegevens van het artefact identiek zijn aan die van een ander artefact. Met dit mechanisme kunt u herhaaldelijk identiek getagde afbeeldingen naar een register pushen. U kunt bijvoorbeeld herhaaldelijk zonder fouten naar uw register pushen, omdat elke afbeelding `myimage:latest` wordt geïdentificeerd door de unieke samenvatting.

U kunt een artefact uit een register halen door de samenvatting ervan op te geven in de pull-bewerking. Sommige systemen kunnen worden geconfigureerd voor pull door digest, omdat hiermee wordt gegarandeerd dat de versie van de afbeelding wordt getrokken, zelfs als een identiek getagde afbeelding later naar het register wordt pusht.

> [!IMPORTANT]
> Als u herhaaldelijk gewijzigde artefacten met identieke tags pusht, kunt u 'zwevende' artefacten maken die niet zijn gelabeld, maar nog steeds ruimte in uw register verbruiken. Afbeeldingen zonder tag worden niet weergegeven in de Azure CLI of in de Azure Portal wanneer u afbeeldingen per tag welijst of bekijkt. De lagen zijn echter nog steeds aanwezig en verbruiken ruimte in uw register. Als u een niet-getagged afbeelding wilt verwijderen, wordt er registerruimte vrij gemaakt wanneer het manifest de enige of de laatste is, die verwijst naar een bepaalde laag. Zie Containerafbeeldingen verwijderen in Azure Container Registry voor meer informatie over het vrij maken van ruimte die wordt gebruikt [door afbeeldingen zonder Azure Container Registry.](container-registry-delete.md)

## <a name="addressing-an-artifact"></a>Een artefact aanpakken

Als u een registerartefact wilt aanpakken voor push- en pull-bewerkingen met Docker of andere clienthulpprogramma's, combineert u de volledig gekwalificeerde registernaam, de naam van de opslagplaats (inclusief het naamruimtepad, indien van toepassing) en een artefacttag of manifest digest. Zie de vorige secties voor uitleg over deze termen.

  **Adres op tag**: `[loginServerUrl]/[repository][:tag]`
    
  **Adres per samenvatting:**`[loginServerUrl]/[repository@sha256][:digest]`  

Wanneer u Docker of andere clienthulpprogramma's gebruikt om artefacten op te halen of te pushen naar een Azure-containerregister, gebruikt u de volledig gekwalificeerde URL van het register, ook wel de naam van de *aanmeldingsserver* genoemd. In de Azure-cloud heeft de volledig gekwalificeerde URL van een Azure-containerregister de indeling `myregistry.azurecr.io` (in kleine letters).

> [!NOTE]
> * U kunt geen poortnummer opgeven in de aanmeldingsserver-URL van het register, zoals `myregistry.azurecr.io:443` . 
> * De tag wordt standaard gebruikt als u geen tag op te geven `latest` in de opdracht.  

   
### <a name="push-by-tag"></a>Pushen per tag

Voorbeelden: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Pull by tag

Voorbeeld: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Pull by manifest digest


Voorbeeld:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Volgende stappen

Meer informatie over [registeropslag en](container-registry-storage.md) [ondersteunde inhoudsindelingen](container-registry-image-formats.md) in Azure Container Registry.

Meer informatie over het [pushen en pullen van afbeeldingen](container-registry-get-started-docker-cli.md) Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
