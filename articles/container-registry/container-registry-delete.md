---
title: Afbeeldings resources verwijderen
description: Meer informatie over het effectief beheren van de register grootte door container installatie kopie gegevens te verwijderen met behulp van Azure CLI-opdrachten.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78403345"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Container installatie kopieën in Azure Container Registry verwijderen met behulp van de Azure CLI

Als u de grootte van uw Azure container Registry wilt behouden, moet u regel matig verouderde afbeeldings gegevens verwijderen. Hoewel sommige container installatie kopieën die in productie zijn geïmplementeerd, mogelijk meer-termijn opslag vereisen, kunnen anderen doorgaans sneller worden verwijderd. In een geautomatiseerd build-en test scenario kan uw REGI ster bijvoorbeeld snel worden gevuld met installatie kopieën die nooit zijn geïmplementeerd en kunnen ze kort na het volt ooien van de build-en test fase worden verwijderd.

Omdat u de afbeeldings gegevens op verschillende manieren kunt verwijderen, is het belang rijk om te begrijpen hoe elke Verwijder bewerking van invloed is op het gebruik van de opslag. In dit artikel worden verschillende methoden beschreven voor het verwijderen van afbeeldings gegevens:

* Een [opslag plaats](#delete-repository)verwijderen: verwijdert alle installatie kopieën en alle unieke lagen in de opslag plaats.
* Verwijderen per [tag](#delete-by-tag): Hiermee verwijdert u een afbeelding, de tag, alle unieke lagen waarnaar wordt verwezen door de afbeelding en alle andere tags die aan de afbeelding zijn gekoppeld.
* Verwijderen per [manifest Digest](#delete-by-manifest-digest): Hiermee verwijdert u een afbeelding, alle unieke lagen waarnaar wordt verwezen door de afbeelding en alle labels die aan de afbeelding zijn gekoppeld.

Er worden voorbeeld scripts gegeven om verwijderings bewerkingen te automatiseren.

Zie voor een inleiding tot deze concepten [over registers, opslag plaatsen en installatie kopieën](container-registry-concepts.md).

## <a name="delete-repository"></a>Opslag plaats verwijderen

Als u een opslag plaats verwijdert, worden alle installatie kopieën uit de opslag plaats verwijderd, inclusief alle labels, unieke lagen en manifesten. Wanneer u een opslag plaats verwijdert, herstelt u de opslag ruimte die wordt gebruikt door de installatie kopieën die verwijzen naar unieke lagen in die opslag plaats.

Met de volgende Azure CLI-opdracht verwijdert u de opslag plaats ' ACR-HelloWorld ' en alle labels en manifesten in de opslag plaats. Als er geen andere installatie kopieën in het REGI ster verwijzen naar lagen waarnaar wordt verwezen door de verwijderde manifesten, worden ook de laag gegevens verwijderd en wordt de opslag ruimte hersteld.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Verwijderen per tag

U kunt afzonderlijke installatie kopieën uit een opslag plaats verwijderen door de naam en tag van de opslag plaats op te geven in de Verwijder bewerking. Wanneer u per tag verwijdert, herstelt u de opslag ruimte die wordt gebruikt door een unieke laag in de afbeelding (lagen die niet worden gedeeld door andere installatie kopieën in het REGI ster).

Als u wilt verwijderen per tag, gebruikt u [AZ ACR repository delete][az-acr-repository-delete] en geeft u de naam van de installatie kopie op in de `--image` para meter. Alle lagen die uniek zijn voor de afbeelding en alle andere tags die aan de afbeelding zijn gekoppeld, worden verwijderd.

U kunt bijvoorbeeld de installatie kopie ' ACR-HelloWorld: nieuwste ' verwijderen uit het REGI ster ' myregistry ':

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Verwijderen *per label* mag niet worden verward met het verwijderen van een tag (code ring opzeggen). U kunt een tag verwijderen met de Azure CLI-opdracht [AZ ACR repository tag][az-acr-repository-untag]. Er wordt geen ruimte vrijgemaakt wanneer u een installatie kopie tag, omdat het [manifest](container-registry-concepts.md#manifest) en de laag gegevens in het REGI ster blijven. Alleen de tag-verwijzing zelf wordt verwijderd.

## <a name="delete-by-manifest-digest"></a>Verwijderen per manifest Digest

Een [manifest Digest](container-registry-concepts.md#manifest-digest) kan worden gekoppeld aan een, geen of meerdere labels. Wanneer u per Digest verwijdert, worden alle tags waarnaar wordt verwezen door het manifest, verwijderd als laag gegevens voor alle lagen die uniek zijn voor de afbeelding. Gegevens van gedeelde laag worden niet verwijderd.

Als u wilt verwijderen per Digest, vermeldt u eerst de manifest samenvattingen voor de opslag plaats met de installatie kopieën die u wilt verwijderen. Bijvoorbeeld:

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

Geef vervolgens de samen vatting op die u wilt verwijderen in de opdracht [AZ ACR repository delete][az-acr-repository-delete] . De indeling van de opdracht is als volgt:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Als u bijvoorbeeld het laatste manifest wilt verwijderen dat wordt vermeld in de voor gaande uitvoer (met de tag ' v2 '):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

De `acr-helloworld:v2` afbeelding wordt uit het REGI ster verwijderd, evenals de laag gegevens die voor die afbeelding uniek zijn. Als een manifest is gekoppeld aan meerdere tags, worden alle gekoppelde labels ook verwijderd.

## <a name="delete-digests-by-timestamp"></a>Samen vattingen verwijderen per tijds tempel

Als u de grootte van een opslag plaats of REGI ster wilt behouden, moet u mogelijk periodiek manifest samenvattingen verwijderen die ouder zijn dan een bepaalde datum.

Met de volgende Azure CLI-opdracht wordt een lijst weer gegeven met alle manifest samenvatting in een opslag plaats die ouder is dan een opgegeven tijds tempel, in oplopende volg orde. Vervang `<acrName>` en `<repositoryName>` door de waarden die van toepassing zijn voor uw omgeving. De tijds tempel kan een volledige datum-tijd expressie of een datum zijn, zoals in dit voor beeld.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Nadat u de verouderde manifest samenvattingen hebt geïdentificeerd, kunt u het volgende bash-script uitvoeren om manifest-samen vattingen te verwijderen die ouder zijn dan een opgegeven tijds tempel. Hiervoor zijn de Azure CLI en **xargs** vereist. Standaard wordt het script niet verwijderd. Wijzig de `ENABLE_DELETE` waarde in om het verwijderen van de `true` installatie kopie in te scha kelen.

> [!WARNING]
> Gebruik het volgende voorbeeld script met een waarschuwing: verwijderde afbeeldings gegevens zijn onherstelbaar. Als u systemen hebt die installatie kopieën pullen per manifest Digest (in plaats van de naam van de installatie kopie), moet u deze scripts niet uitvoeren. Als u de manifest-samen vattingen verwijdert, voor komt u dat deze systemen de installatie kopieën uit het REGI ster halen. In plaats van op manifest te halen, kunt u overwegen om een uniek schema voor *labels* te gebruiken, een [Aanbevolen best practice](container-registry-image-tag-version.md). 

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

## <a name="delete-untagged-images"></a>Niet-gelabelde afbeeldingen verwijderen

Zoals vermeld in de sectie [manifest Digest](container-registry-concepts.md#manifest-digest) , het pushen van een gewijzigde installatie kopie met behulp van een bestaande tag, **ontlabelt** de eerder gepushte afbeelding, wat resulteert in een zwevende afbeelding (of "Dangling"). Het manifest van de vorige gepushte afbeelding, en de laag gegevens ervan, blijft aanwezig in het REGI ster. Houd rekening met de volgende reeks gebeurtenissen:

1. Push installatie kopie *ACR-HelloWorld* met tag **nieuwste**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Raadpleeg de manifesten voor de opslag plaats *ACR-HelloWorld*:

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

1. *ACR wijzigen-HelloWorld* Dockerfile
1. Push installatie kopie *ACR-HelloWorld* met tag **nieuwste**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Raadpleeg de manifesten voor de opslag plaats *ACR-HelloWorld*:

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

Zoals u kunt zien in de uitvoer van de laatste stap in de reeks, is er nu een zwevend manifest waarvan de `"tags"` eigenschap een lege lijst is. Dit manifest bevindt zich nog in het REGI ster, samen met eventuele unieke laag gegevens waarnaar wordt verwezen. **Als u dergelijke zwevende afbeeldingen en hun laag gegevens wilt verwijderen, moet u verwijderen per manifest Digest**.

## <a name="delete-all-untagged-images"></a>Alle niet-gelabelde afbeeldingen verwijderen

U kunt alle niet-gelabelde afbeeldingen in uw opslag plaats weer geven met behulp van de volgende Azure CLI-opdracht. Vervang `<acrName>` en `<repositoryName>` door de waarden die van toepassing zijn voor uw omgeving.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Als u deze opdracht in een script gebruikt, kunt u alle niet-gelabelde afbeeldingen in een opslag plaats verwijderen.

> [!WARNING]
> Gebruik de volgende voorbeeld scripts met een waarschuwing: verwijderde afbeeldings gegevens zijn onherstelbaar. Als u systemen hebt die installatie kopieën pullen per manifest Digest (in plaats van de naam van de installatie kopie), moet u deze scripts niet uitvoeren. Als u niet-gelabelde afbeeldingen verwijdert, kunnen die systemen de installatie kopieën niet uit het REGI ster halen. In plaats van op manifest te halen, kunt u overwegen om een uniek schema voor *labels* te gebruiken, een [Aanbevolen best practice](container-registry-image-tag-version.md).

**Azure CLI in bash**

Met het volgende bash-script worden alle niet-gelabelde afbeeldingen uit een opslag plaats verwijderd. Hiervoor zijn de Azure CLI en **xargs** vereist. Standaard wordt het script niet verwijderd. Wijzig de `ENABLE_DELETE` waarde in om het verwijderen van de `true` installatie kopie in te scha kelen.

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

**Azure CLI in Power shell**

Met het volgende Power shell-script worden alle niet-gelabelde afbeeldingen uit een opslag plaats verwijderd. Hiervoor is Power shell en de Azure CLI vereist. Standaard wordt het script niet verwijderd. Wijzig de `$enableDelete` waarde in om het verwijderen van de `$TRUE` installatie kopie in te scha kelen.

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

Als alternatief voor het uitvoeren van scripts van Azure CLI-opdrachten voert u een taak op aanvraag of geplande ACR uit om alle labels te verwijderen die ouder zijn dan een bepaalde duur of die overeenkomen met een opgegeven naam filter. Zie [automatisch installatie kopieën verwijderen uit een Azure container Registry](container-registry-auto-purge.md)voor meer informatie.

Stel eventueel een [Bewaar beleid](container-registry-retention-policy.md) in voor elk REGI ster om niet-gecodeerde manifesten te beheren. Wanneer u een Bewaar beleid inschakelt, worden installatie kopieën in het REGI ster die geen gekoppelde labels hebben en de onderliggende laag gegevens, automatisch verwijderd na een ingestelde periode.

## <a name="next-steps"></a>Volgende stappen

Zie [opslag van container installatie kopieën in azure container Registry](container-registry-storage.md)voor meer informatie over de opslag van installatie kopieën in azure container Registry.

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
