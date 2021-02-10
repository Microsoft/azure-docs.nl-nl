---
title: Over opslag plaatsen & installatie kopieën
description: Inleiding tot de belangrijkste concepten van Azure-container registers, opslag plaatsen en container installatie kopieën.
ms.topic: article
ms.date: 06/16/2020
ms.openlocfilehash: 0cc7df22236c60bd473385d92c8db563be68f688
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008516"
---
# <a name="about-registries-repositories-and-images"></a>Over registers, opslag plaatsen en installatie kopieën

In dit artikel worden de belangrijkste concepten van container registers, opslag plaatsen en container installatie kopieën en gerelateerde artefacten geïntroduceerd. 

## <a name="registry"></a>Register

Een container *register* is een service waarmee container installatie kopieën worden opgeslagen en gedistribueerd. Docker hub is een openbaar container register dat ondersteuning biedt voor de open source-community en fungeert als een algemene catalogus met installatie kopieën. Azure Container Registry biedt gebruikers rechtstreeks controle over hun installatie kopieën, met geïntegreerde verificatie, [geo-replicatie](container-registry-geo-replication.md) die wereld wijde distributie en betrouw baarheid ondersteunt voor implementaties van netwerken, het [virtuele netwerk en firewall configuratie](container-registry-vnet.md), [Label vergrendeling](container-registry-image-lock.md)en vele andere uitgebreide functies. 

Naast docker-container installatie kopieën ondersteunt Azure Container Registry gerelateerde [inhouds artefacten](container-registry-image-formats.md) , waaronder OCI-afbeeldings indelingen (open container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Inhoud adresseer bare elementen van een artefact

Het adres van een artefact in een Azure container Registry bevat de volgende elementen. 

`[loginUrl]/[repository:][tag]`

* **loginUrl** : de volledig gekwalificeerde naam van de registerpad. De Registry-host in een Azure container Registry heeft de indeling *myregistry*. azurecr.io (alle kleine letters). U moet de loginUrl opgeven wanneer u docker of andere client hulpprogramma's gebruikt om artefacten te halen of te pushen naar een Azure container Registry. 
* **opslag plaats** : naam van een logische groepering van een of meer gerelateerde installatie kopieën of artefacten, bijvoorbeeld de installatie kopieën voor een toepassing of een basis besturingssysteem. Kan *naam ruimte* -pad bevatten. 
* **tag** -id van een specifieke versie van een installatie kopie of artefact die is opgeslagen in een opslag plaats.

De volledige naam van een installatie kopie in een Azure container Registry kan er bijvoorbeeld als volgt uitzien:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Raadpleeg de volgende secties voor meer informatie over deze elementen.

## <a name="repository-name"></a>Naam van opslag plaats

Een *opslag plaats* is een verzameling container installatie kopieën of andere artefacten met dezelfde naam, maar met verschillende labels. De volgende drie installatie kopieën bevinden zich bijvoorbeeld in de opslag plaats ' ACR-HelloWorld ':


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

## <a name="image"></a>Installatiekopie

Een container installatie kopie of ander artefact in een REGI ster is gekoppeld aan een of meer tags, heeft een of meer lagen en wordt geïdentificeerd door een manifest. Meer informatie over hoe deze onderdelen aan elkaar zijn gerelateerd, kan u helpen uw REGI ster effectief te beheren.

### <a name="tag"></a>Tag

De- *tag* voor een afbeelding of ander artefact specificeert de versie. Een enkel artefact in een opslag plaats kan worden toegewezen aan een of meer tags en kan ook ' zonder Tags ' zijn. Dat wil zeggen dat u alle tags uit een installatie kopie kunt verwijderen terwijl de gegevens van de afbeelding (de lagen) in het REGI ster blijven.

De opslag plaats (of opslag plaats en naam ruimte) plus een tag definieert de naam van een afbeelding. U kunt een installatie kopie pushen en ophalen door de naam op te geven in de push-of pull-bewerking. De tag `latest` wordt standaard gebruikt als u er geen opgeeft in uw docker-opdrachten.

Hoe u container installatie kopieën labelt, wordt door uw scenario's begeleid om ze te ontwikkelen of te implementeren. Stabiele Tags worden bijvoorbeeld aanbevolen voor het onderhouden van uw basis installatie kopieën en unieke labels voor het implementeren van installatie kopieën. Zie voor meer informatie [aanbevelingen voor het coderen en versie beheer van container installatie kopieën](container-registry-image-tag-version.md).

Raadpleeg de [docker-documentatie](https://docs.docker.com/engine/reference/commandline/tag/)voor de naamgevings regels voor labels.

### <a name="layer"></a>Laag

Container installatie kopieën bestaan uit een of meer *lagen*, die overeenkomen met een regel in de Dockerfile die de afbeelding definieert. Installatie kopieën in een REGI ster delen algemene lagen, waardoor de efficiëntie van opslag wordt verhoogd. Het is bijvoorbeeld mogelijk dat verschillende installatie kopieën in verschillende opslag plaatsen dezelfde Alpine Linux-basis laag delen, maar er wordt slechts één exemplaar van die laag opgeslagen in het REGI ster.

Laag delen optimaliseert ook laag distributie naar knoop punten met meerdere afbeeldingen die algemene lagen delen. Als een afbeelding die al in een knoop punt staat, bijvoorbeeld de Alpine Linux-laag als basis bevat, wordt de laag door de volgende pull-bewerking van een andere afbeelding die verwijst naar dezelfde laag, niet overgedragen naar het knoop punt. In plaats daarvan verwijst deze naar de laag die al bestaat op het knoop punt.

Lagen worden niet gedeeld door de verschillende registers om te zorgen voor veilige isolatie en bescherming tegen de mogelijke laag bewerking.

### <a name="manifest"></a>Manifest

Elke container installatie kopie of artefact die naar een container register is gepusht, is gekoppeld aan een *manifest*. Het manifest dat door het REGI ster wordt gegenereerd wanneer de installatie kopie wordt gepusht, identificeert de afbeelding uniek en geeft de bijbehorende lagen aan. 

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

Manifesten worden geïdentificeerd aan de hand van een unieke SHA-256-Hash of met de samen vatting van het *manifest*. Elke afbeelding of elk afzonderlijk artefact: of het label is gelabeld of niet, wordt geïdentificeerd door de samen vatting. De Digest-waarde is uniek, zelfs als de laag gegevens van de afbeelding identiek zijn aan die van een andere afbeelding. Dit mechanisme biedt u de mogelijkheid om herhaaldelijk gelabelde installatie kopieën naar een REGI ster te pushen. U kunt bijvoorbeeld herhaaldelijk `myimage:latest` naar uw REGI ster pushen zonder fout omdat elke installatie kopie wordt geïdentificeerd door de unieke samen vatting.

U kunt een installatie kopie van een REGI ster ophalen door de samen vatting op te geven in de pull-bewerking. Sommige systemen kunnen worden geconfigureerd om te worden opgehaald door Digest, omdat hiermee wordt gegarandeerd dat de installatie kopie versie wordt opgehaald, zelfs als er vervolgens een identiek gelabelde afbeelding naar het REGI ster wordt gepusht.

U kunt bijvoorbeeld een installatie kopie ophalen uit de opslag plaats ' ACR-HelloWorld ' per manifest Digest:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Als u herhaaldelijk gewijzigde installatie kopieën met identieke Tags pusht, kunt u zwevende installatie kopieën maken--installatie kopieën die niet zijn gelabeld, maar nog steeds ruimte in uw REGI ster gebruiken. Niet-gelabelde afbeeldingen worden niet weer gegeven in de Azure CLI of in het Azure Portal wanneer u afbeeldingen op label lijst of weer geven. De lagen zijn echter nog steeds aanwezig en gebruiken ruimte in het REGI ster. Als u een niet-gecodeerde afbeelding verwijdert, maakt u register ruimte vrij wanneer het manifest de enige is, of het laatste, dat verwijst naar een bepaalde laag. Zie [container installatie kopieën in azure container Registry verwijderen](container-registry-delete.md)voor meer informatie over het vrijmaken van ruimte die wordt gebruikt door niet-gecodeerde installatie kopieën.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [afbeeldings opslag](container-registry-storage.md) en [ondersteunde inhouds indelingen](container-registry-image-formats.md) in azure container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


