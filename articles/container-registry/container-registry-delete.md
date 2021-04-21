---
title: Resources voor afbeeldingen verwijderen
description: Meer informatie over het effectief beheren van de registergrootte door gegevens van containerafbeeldingen te verwijderen met behulp van Azure CLI-opdrachten.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: af277d0c02960c989b4e9119f2ecbfd8f6d7ce07
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783985"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Containerafbeeldingen in Azure Container Registry verwijderen met behulp van de Azure CLI

Als u de grootte van uw Azure-containerregister wilt behouden, moet u regelmatig verouderde afbeeldingsgegevens verwijderen. Hoewel sommige containerafbeeldingen die in productie zijn geïmplementeerd, mogelijk langere opslag vereisen, kunnen andere meestal sneller worden verwijderd. In een scenario voor automatisch bouwen en testen kan uw register bijvoorbeeld snel worden gevuld met afbeeldingen die mogelijk nooit worden geïmplementeerd en kort na het voltooien van de build- en testpass worden verwijderd.

Omdat u afbeeldingsgegevens op verschillende manieren kunt verwijderen, is het belangrijk om te begrijpen hoe elke verwijderbewerking van invloed is op het opslaggebruik. In dit artikel worden verschillende methoden beschreven voor het verwijderen van afbeeldingsgegevens:

* Een opslagplaats [verwijderen:](#delete-repository)hiermee verwijdert u alle afbeeldingen en alle unieke lagen in de opslagplaats.
* Verwijderen op [tag:](#delete-by-tag)hiermee verwijdert u een afbeelding, de tag, alle unieke lagen waarnaar wordt verwezen door de afbeelding en alle andere tags die aan de afbeelding zijn gekoppeld.
* Verwijderen via [manifest digest:](#delete-by-manifest-digest)hiermee verwijdert u een afbeelding, alle unieke lagen waarnaar wordt verwezen door de afbeelding en alle tags die aan de afbeelding zijn gekoppeld.

Er zijn voorbeeldscripts beschikbaar om verwijderbewerkingen te automatiseren.

Zie About [registries, repositories, and images (Over registers, opslagplaatsen en afbeeldingen)](container-registry-concepts.md)voor een inleiding tot deze concepten.

## <a name="delete-repository"></a>Opslagplaats verwijderen

Als u een opslagplaats verwijdert, worden alle afbeeldingen in de opslagplaats verwijderd, inclusief alle tags, unieke lagen en manifesten. Wanneer u een opslagplaats verwijdert, herstelt u de opslagruimte die wordt gebruikt door de afbeeldingen die verwijzen naar unieke lagen in die opslagplaats.

Met de volgende Azure CLI-opdracht worden de opslagplaats acr-helloworld en alle tags en manifesten in de opslagplaats verwijderd. Als er niet wordt verwezen naar lagen waarnaar wordt verwezen door de verwijderde manifesten door andere afbeeldingen in het register, worden de laaggegevens ook verwijderd en wordt de opslagruimte hersteld.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Verwijderen op tag

U kunt afzonderlijke afbeeldingen uit een opslagplaats verwijderen door de naam en tag van de opslagplaats op te geven in de verwijderbewerking. Wanneer u op tag verwijdert, herstelt u de opslagruimte die wordt gebruikt door unieke lagen in de afbeelding (lagen die niet worden gedeeld door andere afbeeldingen in het register).

Als u wilt verwijderen op tag, gebruikt [u az acr repository delete][az-acr-repository-delete] en geeft u de naam van de afbeelding op in de parameter `--image` . Alle lagen die uniek zijn voor de afbeelding en alle andere tags die aan de afbeelding zijn gekoppeld, worden verwijderd.

U kunt bijvoorbeeld de afbeelding 'acr-helloworld:latest' uit het register 'myregistry' verwijderen:

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Verwijderen op *tag* mag niet worden verward met het verwijderen van een tag (lostagging). U kunt een tag verwijderen met de Azure CLI-opdracht [az acr repository untag][az-acr-repository-untag]. Er wordt geen ruimte vrij gemaakt wanneer u een afbeelding onttagt omdat [de manifest-](container-registry-concepts.md#manifest) en laaggegevens in het register blijven. Alleen de tagverwijzing zelf wordt verwijderd.

## <a name="delete-by-manifest-digest"></a>Verwijderen door manifest digest

Een [manifest digest](container-registry-concepts.md#manifest-digest) kan worden gekoppeld aan één, geen of meerdere tags. Wanneer u per samenvatting verwijdert, worden alle tags waarnaar wordt verwezen door het manifest verwijderd, evenals laaggegevens voor lagen die uniek zijn voor de afbeelding. Gedeelde laaggegevens worden niet verwijderd.

Als u wilt verwijderen per samenvatting, vermeldt u eerst de manifest digests voor de opslagplaats met de afbeeldingen die u wilt verwijderen. Bijvoorbeeld:

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
  }
]
```

Geef vervolgens de digest op die u wilt verwijderen in de [opdracht az acr repository delete.][az-acr-repository-delete] De indeling van de opdracht is als volgt:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Als u bijvoorbeeld het laatste manifest wilt verwijderen dat in de voorgaande uitvoer wordt vermeld (met de tag 'v2'):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

De `acr-helloworld:v2` afbeelding wordt verwijderd uit het register, net als alle laaggegevens die uniek zijn voor die afbeelding. Als een manifest is gekoppeld aan meerdere tags, worden alle gekoppelde tags ook verwijderd.

## <a name="delete-digests-by-timestamp"></a>Samenvattingen verwijderen op tijdstempel

Als u de grootte van een opslagplaats of register wilt behouden, moet u mogelijk periodiek manifest digests verwijderen die ouder zijn dan een bepaalde datum.

De volgende Azure CLI-opdracht geeft een lijst van alle manifest digest in een opslagplaats die ouder is dan een opgegeven tijdstempel, in oplopende volgorde. Vervang `<acrName>` en door waarden die geschikt zijn voor uw `<repositoryName>` omgeving. De tijdstempel kan een volledige datum/tijd-expressie of een datum zijn, zoals in dit voorbeeld.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Nadat u verouderde manifest digests hebt bepaald, kunt u het volgende Bash-script uitvoeren om manifest digests ouder dan een opgegeven tijdstempel te verwijderen. Hiervoor zijn de Azure CLI en **xargs vereist.** Standaard wordt het script niet verwijderd. Wijzig de waarde `ENABLE_DELETE` in om het verwijderen van afbeeldingen in `true` teschakelen.

> [!WARNING]
> Wees voorzichtig met het volgende voorbeeldscript: verwijderde afbeeldingsgegevens kunnen niet worden teruggehaald. Als u systemen hebt die afbeeldingen op halen via manifest digest (in plaats van de naam van de afbeelding), moet u deze scripts niet uitvoeren. Door de manifest digests te verwijderen, kunnen deze systemen de afbeeldingen niet uit het register halen. In plaats van een manifest op te halen, kunt u overwegen een uniek *tagschema te* gebruiken, een [aanbevolen best practice](container-registry-image-tag-version.md). 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Niet-getagged afbeeldingen verwijderen

Zoals vermeld in de sectie [Manifest digest,](container-registry-concepts.md#manifest-digest) wordt bij het pushen van een gewijzigde afbeelding met behulp van een bestaande **tag** de eerder pushende afbeelding verwijderd, wat resulteert in een zwevende (of 'zwevende') afbeelding. Het manifest (en de laaggegevens) van de eerder pushede afbeelding blijft in het register. Houd rekening met de volgende reeks gebeurtenissen:

1. Push image *acr-helloworld with* tag **latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Controleer de manifesten voor opslagplaats *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Dockerfile *acr-helloworld* wijzigen
1. Push image *acr-helloworld with* tag **latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Controleer de manifesten voor opslagplaats *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Zoals u kunt zien in de uitvoer van de laatste stap in de reeks, is er nu een zwevend manifest waarvan de eigenschap `"tags"` een lege lijst is. Dit manifest bevindt zich nog steeds in het register, samen met de unieke laaggegevens waarnaar wordt verwezen. **Als u dergelijke zwevende afbeeldingen en hun laaggegevens wilt verwijderen,** moet u verwijderen door manifest digest te gebruiken.

## <a name="delete-all-untagged-images"></a>Alle niet-getagged afbeeldingen verwijderen

U kunt alle niet-getagged afbeeldingen in uw opslagplaats bekijken met behulp van de volgende Azure CLI-opdracht. Vervang `<acrName>` en door waarden die geschikt zijn voor uw `<repositoryName>` omgeving.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Met deze opdracht in een script kunt u alle niet-getagged afbeeldingen in een opslagplaats verwijderen.

> [!WARNING]
> Wees voorzichtig met de volgende voorbeeldscripts. Verwijderde afbeeldingsgegevens kunnen niet worden teruggehaald. Als u systemen hebt die afbeeldingen op halen via manifest digest (in plaats van de naam van de afbeelding), moet u deze scripts niet uitvoeren. Door niet-getagged afbeeldingen te verwijderen, kunnen deze systemen de afbeeldingen niet uit het register halen. In plaats van een manifest op te halen, kunt u overwegen een uniek *tagschema* te gebruiken, een [aanbevolen best practice](container-registry-image-tag-version.md).

**Azure CLI in Bash**

Met het volgende Bash-script worden alle niet-getagged afbeeldingen uit een opslagplaats verwijderd. Hiervoor zijn de Azure CLI en **xargs vereist.** Standaard wordt het script niet verwijderd. Wijzig de waarde `ENABLE_DELETE` in om het verwijderen van afbeeldingen in te `true` stellen.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Azure CLI in PowerShell**

Met het volgende PowerShell-script worden alle niet-getagged afbeeldingen uit een opslagplaats verwijderd. Hiervoor zijn PowerShell en de Azure CLI vereist. Standaard wordt het script niet verwijderd. Wijzig de waarde `$enableDelete` in om het verwijderen van afbeeldingen in `$TRUE` teschakelen.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Automatisch tags en manifesten leegmaken (preview)

Als alternatief voor het uitvoeren van scripts voor Azure CLI-opdrachten kunt u een ACR-taak op aanvraag of een geplande ACR-taak uitvoeren om alle tags te verwijderen die ouder zijn dan een bepaalde duur of om te voldoen aan een opgegeven naamfilter. Zie Voor meer informatie Automatisch afbeeldingen [ops purge uit een Azure-containerregister.](container-registry-auto-purge.md)

U kunt eventueel een [bewaarbeleid instellen](container-registry-retention-policy.md) voor elk register om manifesten zonder aaneengetagged te beheren. Wanneer u een bewaarbeleid inschakelen, worden afbeeldingsmanifests in het register die geen gekoppelde tags en de onderliggende laaggegevens, automatisch verwijderd na een bepaalde periode.

## <a name="next-steps"></a>Volgende stappen

Zie Opslag van containerafbeeldingen in Azure Container Registry voor meer informatie over opslag van [Azure Container Registry.](container-registry-storage.md)

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
