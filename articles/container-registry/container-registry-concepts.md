---
title: Over registers, opslag plaatsen, afbeeldingen en artefacten
description: Inleiding tot de belangrijkste concepten van Azure-container registers, opslag plaatsen, container installatie kopieën en andere artefacten.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578098"
---
# <a name="about-registries-repositories-and-artifacts"></a>Over registers, opslag plaatsen en artefacten

In dit artikel worden de belangrijkste concepten van container registers, opslag plaatsen en container installatie kopieën en gerelateerde artefacten geïntroduceerd. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="REGI ster, opslag plaatsen en artefacten":::

## <a name="registry"></a>Register

Een container *register* is een service waarmee container installatie kopieën en gerelateerde artefacten worden opgeslagen en gedistribueerd. Docker hub is een voor beeld van een openbaar container register dat fungeert als een algemene catalogus van docker-container installatie kopieën. Azure Container Registry biedt gebruikers rechtstreeks controle over hun container inhoud, met geïntegreerde verificatie, [geo-replicatie](container-registry-geo-replication.md) die wereld wijde distributie en betrouw baarheid ondersteunt voor implementaties van netwerken, [virtuele netwerken met persoonlijke koppelingen](container-registry-private-link.md), [Label vergrendeling](container-registry-image-lock.md)en vele andere verbeterde functies. 

Naast docker-compatibele container installatie kopieën ondersteunt Azure Container Registry diverse [inhouds artefacten](container-registry-image-formats.md) , waaronder helm-grafieken en OCI-afbeeldings indelingen (open container Initiative).

## <a name="repository"></a>Opslagplaats

Een *opslag plaats* is een verzameling container installatie kopieën of andere artefacten in een REGI ster met dezelfde naam, maar met verschillende labels. De volgende drie installatie kopieën bevinden zich bijvoorbeeld in de `acr-helloworld` opslag plaats:

- *ACR-HelloWorld: nieuwste*
- *ACR-HelloWorld: v1*
- *ACR-HelloWorld: v2*

Opslagplaats namen kunnen ook [naam ruimten](container-registry-best-practices.md#repository-namespaces)bevatten. Met naam ruimten kunt u verwante opslag plaatsen en eigendoms artefacten in uw organisatie identificeren met door komma's gescheiden namen. Het REGI ster beheert echter alle opslag plaatsen onafhankelijk, niet als een-hiërarchie. Bijvoorbeeld:

- *Marketing/campaign10-18/Web: v2*
- *Marketing-campaign10-18/API: v3*
- *Marketing-campaign10-18/Email-Sender: v2*
- *product-retouren/webverzending: 20180604*
- *product-retourneert/legacy-integrator: 20180715*

Opslagplaats namen mogen alleen bestaan uit kleine letters, punten, streepjes, onderstrepings tekens en slashes. 

Zie voor de volledige naamgevings regels voor opslag plaatsen de [specificatie open container Initiative distributie](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="artifact"></a>Artefact

Een container installatie kopie of ander artefact in een REGI ster is gekoppeld aan een of meer tags, heeft een of meer lagen en wordt geïdentificeerd door een manifest. Meer informatie over hoe deze onderdelen aan elkaar zijn gerelateerd, kan u helpen uw REGI ster effectief te beheren.

### <a name="tag"></a>Tag

De- *tag* voor een afbeelding of ander artefact specificeert de versie. Een enkel artefact in een opslag plaats kan worden toegewezen aan een of meer tags en kan ook ' zonder Tags ' zijn. Dat wil zeggen dat u alle tags uit een installatie kopie kunt verwijderen terwijl de gegevens van de afbeelding (de lagen) in het REGI ster blijven.

De opslag plaats (of opslag plaats en naam ruimte) plus een tag definieert de naam van een afbeelding. U kunt een installatie kopie pushen en ophalen door de naam op te geven in de push-of pull-bewerking. De tag `latest` wordt standaard gebruikt als u er geen opgeeft in uw docker-opdrachten.

Hoe u container installatie kopieën labelt, wordt door uw scenario's begeleid om ze te ontwikkelen of te implementeren. Stabiele Tags worden bijvoorbeeld aanbevolen voor het onderhouden van uw basis installatie kopieën en unieke labels voor het implementeren van installatie kopieën. Zie voor meer informatie [aanbevelingen voor het coderen en versie beheer van container installatie kopieën](container-registry-image-tag-version.md).

Raadpleeg de [docker-documentatie](https://docs.docker.com/engine/reference/commandline/tag/)voor de naamgevings regels voor labels.

### <a name="layer"></a>Laag

Container installatie kopieën en artefacten bestaan uit een of meer *lagen*. Met verschillende typen artefacten worden lagen verschillend gedefinieerd. In een docker-container installatie kopie correspondeert bijvoorbeeld elke laag met een regel in de Dockerfile die de afbeelding definieert:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Lagen van een container installatie kopie":::

Artefacten in een register share gang bare lagen, waardoor de efficiëntie van opslag wordt verhoogd. Het is bijvoorbeeld mogelijk dat verschillende installatie kopieën in verschillende opslag plaatsen een gemeen schappelijke ASP.NET Core base-laag hebben, maar er wordt slechts één exemplaar van die laag opgeslagen in het REGI ster. Laag delen optimaliseert ook laag distributie naar knoop punten, met meerdere artefacten die algemene lagen delen. Als een afbeelding die al in een knoop punt staat, de laag ASP.NET Core heeft, wordt de laag door de volgende pull-bewerking van een andere afbeelding die naar dezelfde laag verwijst, niet naar het knoop punt overgedragen. In plaats daarvan verwijst deze naar de laag die al bestaat op het knoop punt.

Lagen worden niet gedeeld door de verschillende registers om te zorgen voor veilige isolatie en bescherming tegen de mogelijke laag bewerking.

### <a name="manifest"></a>Manifest

Elke container installatie kopie of artefact die naar een container register is gepusht, is gekoppeld aan een *manifest*. Het manifest dat door het REGI ster wordt gegenereerd wanneer de inhoud wordt gepusht, identificeert de artefacten uniek en geeft de lagen aan. U kunt de manifesten voor een opslag plaats weer geven met de Azure CLI [-opdracht AZ ACR repository show-manifests][az-acr-repository-show-manifests]. 

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

U kunt de manifesten voor een opslag plaats weer geven met de Azure CLI [-opdracht AZ ACR repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Geef bijvoorbeeld de manifesten voor de opslag plaats ' ACR-HelloWorld ' weer:

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
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Manifest Digest

Manifesten worden geïdentificeerd aan de hand van een unieke SHA-256-Hash of met de samen vatting van het *manifest*. Elke afbeelding of elk afzonderlijk artefact: of het label is gelabeld of niet, wordt geïdentificeerd door de samen vatting. De Digest-waarde is uniek, zelfs als de laag gegevens van het artefact gelijk zijn aan die van een ander artefact. Dit mechanisme biedt u de mogelijkheid om herhaaldelijk gelabelde installatie kopieën naar een REGI ster te pushen. U kunt bijvoorbeeld herhaaldelijk `myimage:latest` naar uw REGI ster pushen zonder fout omdat elke installatie kopie wordt geïdentificeerd door de unieke samen vatting.

U kunt een artefact van een REGI ster ophalen door de samen vatting op te geven in de pull-bewerking. Sommige systemen kunnen worden geconfigureerd om te worden opgehaald door Digest, omdat hiermee wordt gegarandeerd dat de versie van de installatie kopie wordt opgehaald, zelfs als een afbeelding met identieke labels later naar het REGI ster wordt gepusht.

> [!IMPORTANT]
> Als u herhaaldelijk gewijzigde artefacten met identieke Tags pusht, kunt u ' zwevende '-artefacten maken die niet zijn gelabeld, maar nog steeds ruimte in uw REGI ster gebruiken. Niet-gelabelde afbeeldingen worden niet weer gegeven in de Azure CLI of in het Azure Portal wanneer u afbeeldingen op label lijst of weer geven. De lagen zijn echter nog steeds aanwezig en gebruiken ruimte in het REGI ster. Als u een niet-gecodeerde afbeelding verwijdert, maakt u register ruimte vrij wanneer het manifest de enige is, of het laatste, dat verwijst naar een bepaalde laag. Zie [container installatie kopieën in azure container Registry verwijderen](container-registry-delete.md)voor meer informatie over het vrijmaken van ruimte die wordt gebruikt door niet-gecodeerde installatie kopieën.

## <a name="addressing-an-artifact"></a>Een artefact adresseren

Als u een register artefact wilt adresseren voor push-en pull-bewerkingen met docker of andere client hulpprogramma's, combineert u de volledige naam van het REGI ster, de naam van de opslag plaats (inclusief pad naar de naam ruimte) en een artefact code of manifest Digest. Zie de voor gaande secties voor uitleg over deze voor waarden.

  **Adres op label**: `[loginServerUrl]/[repository][:tag]`
    
  **Adres per Digest**: `[loginServerUrl]/[repository@sha256][:digest]`  

Wanneer u docker of andere client hulpprogramma's gebruikt om artefacten naar een Azure container Registry te halen of pushen, gebruikt u de volledig gekwalificeerde URL van het REGI ster, ook wel de naam van de *aanmeldings server* genoemd. In de Azure-Cloud bevindt de volledig gekwalificeerde URL van een Azure container Registry zich in de indeling `myregistry.azurecr.io` (alle kleine letters).

> [!NOTE]
> * U kunt geen poort nummer opgeven in de URL van de aanmeldings server van het REGI ster, zoals `myregistry.azurecr.io:443` . 
> * Het label `latest` wordt standaard gebruikt als u geen tag opgeeft in uw opdracht.  

   
### <a name="push-by-tag"></a>Push by-tag

Voorbeelden: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Pull by-tag

Voorbeeld: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Pull by manifest Digest


Voorbeeld:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Volgende stappen

Meer informatie over [register opslag](container-registry-storage.md) en [ondersteunde inhouds indelingen](container-registry-image-formats.md) in azure container Registry.

Meer informatie over het [pushen en ophalen van installatie kopieën](container-registry-get-started-docker-cli.md) van Azure container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


