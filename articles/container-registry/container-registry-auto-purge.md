---
title: Tags en manifesten opschonen
description: Gebruik een opschoon opdracht om meerdere tags en manifesten uit een Azure-container register te verwijderen op basis van leeftijd en een label filter, en om eventueel opschoon bewerkingen te plannen.
ms.topic: article
ms.date: 01/27/2021
ms.openlocfilehash: ab1a925092784effd07431d75e4ec1535c53ed33
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927273"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Afbeeldingen automatisch uit een Azure container Registry verwijderen

Wanneer u een Azure container Registry gebruikt als onderdeel van een werk stroom voor ontwikkel aars, kan het REGI ster snel worden gevuld met afbeeldingen of andere artefacten die na een korte periode niet nodig zijn. Mogelijk wilt u alle labels verwijderen die ouder zijn dan een bepaalde duur of die overeenkomen met een opgegeven naam filter. Als u meerdere artefacten snel wilt verwijderen, wordt in dit artikel de opdracht geïntroduceerd die `acr purge` u kunt uitvoeren als een taak op aanvraag of [geplande](container-registry-tasks-scheduled.md) ACR. 

De `acr purge` opdracht wordt momenteel gedistribueerd in een open bare container installatie kopie ( `mcr.microsoft.com/acr/acr-cli:0.4` ), gebouwd op basis van de bron code in de [ACR-cli](https://github.com/Azure/acr-cli) opslag plaats in github.

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de ACR-taak voorbeelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2.0.76 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren. 

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

> [!WARNING]
> Gebruik de `acr purge` opdracht met de waarschuwing-verwijderde afbeeldings gegevens zijn ONherstelbaar. Als u systemen hebt die installatie kopieën pullen per manifest Digest (in plaats van de naam van de afbeelding), mag u geen niet-gelabelde installatie kopieën leegmaken. Als u niet-gelabelde afbeeldingen verwijdert, kunnen die systemen de installatie kopieën niet uit het REGI ster halen. In plaats van op manifest te halen, kunt u overwegen om een uniek schema voor *labels* te gebruiken, een [Aanbevolen best practice](container-registry-image-tag-version.md).

Zie [container installatie kopieën in azure container Registry verwijderen](container-registry-delete.md)als u de tags of manifesten met één afbeelding wilt verwijderen met Azure cli-opdrachten.

## <a name="use-the-purge-command"></a>De opdracht leegmaken gebruiken

De `acr purge` container opdracht verwijdert afbeeldingen op label in een opslag plaats die overeenkomen met een naam filter en die ouder zijn dan een opgegeven duur. Standaard worden alleen label verwijzingen verwijderd, niet de onderliggende [manifesten](container-registry-concepts.md#manifest) en laag gegevens. De opdracht heeft een optie om ook manifesten te verwijderen. 

> [!NOTE]
> `acr purge` verwijdert geen afbeeldings code of opslag plaats waar het `write-enabled` kenmerk is ingesteld `false` . Zie [een container installatie kopie vergren delen in een Azure container Registry](container-registry-image-lock.md)voor meer informatie.

`acr purge` is ontworpen om te worden uitgevoerd als een container opdracht in een [ACR-taak](container-registry-tasks-overview.md), zodat deze automatisch wordt geverifieerd met het REGI ster waarin de taak wordt uitgevoerd en er vervolgens acties worden uitgevoerd. In de taak voorbeelden in dit artikel wordt de `acr purge` opdracht [alias](container-registry-tasks-reference-yaml.md#aliases) gebruikt in plaats van een volledig gekwalificeerde container installatie kopie opdracht.

Geef ten minste het volgende op wanneer u uitvoert `acr purge` :

* `--filter` -Een opslag plaats en een *reguliere expressie* voor het filteren van labels in de opslag plaats. Voor beelden: `--filter "hello-world:.*"` komt overeen met alle tags in de `hello-world` opslag plaats en `--filter "hello-world:^1.*"` komt overeen met de labels die beginnen met `1` . Meerdere `--filter` para meters door geven om meerdere opslag plaatsen te verwijderen.
* `--ago` -Een [teken reeks](https://golang.org/pkg/time/) met de duur van het type Go om een duur aan te geven waarboven de afbeeldingen worden verwijderd. De duur bestaat uit een reeks van een of meer decimale getallen, elk met een achtervoegsel van een eenheid. Geldige tijds eenheden zijn "d" voor dagen, "h" voor uren en "m" voor minuten. Zo selecteert u bijvoorbeeld `--ago 2d3h6m` alle gefilterde afbeeldingen die meer dan 2 dagen, 3 uur en 6 minuten geleden zijn gewijzigd, en selecteert u de afbeeldingen die voor het `--ago 1.5h` laatst zijn gewijzigd, meer dan 1,5 uur geleden.

`acr purge` ondersteunt verschillende optionele para meters. De volgende twee worden in voor beelden in dit artikel gebruikt:

* `--untagged` -Geeft aan dat manifesten zonder gekoppelde labels (niet-*gecodeerde manifesten*) worden verwijderd.
* `--dry-run` -Geeft aan dat er geen gegevens worden verwijderd, maar de uitvoer is hetzelfde als als de opdracht wordt uitgevoerd zonder deze vlag. Deze para meter is handig voor het testen van een opschoon opdracht om er zeker van te zijn dat de gegevens die u wilt behouden, niet per ongeluk worden verwijderd.
* `--keep` -Geeft aan dat het meest recente x-aantal tags dat moet worden verwijderd, wordt bewaard.
* `--concurrency` -Hiermee geeft u op dat de x-leegmaak taken gelijktijdig moeten worden verwerkt. Er wordt een standaard waarde gebruikt als deze para meter niet is ingesteld.

Voer uit voor aanvullende para meters `acr purge --help` . 

`acr purge` ondersteunt andere functies van ACR-taken, zoals het [uitvoeren van variabelen](container-registry-tasks-reference-yaml.md#run-variables) en [taak uitvoer logboeken](container-registry-tasks-logs.md) die zijn gestreamd en ook worden opgeslagen voor later ophalen.

### <a name="run-in-an-on-demand-task"></a>Uitvoeren in een taak op aanvraag

In het volgende voor beeld wordt de opdracht [AZ ACR run uitgevoerd][az-acr-run] om de `acr purge` opdracht op aanvraag uit te voeren. In dit voor beeld worden alle afbeeldings Tags en manifesten in de `hello-world` opslag plaats in *myregistry* verwijderd die meer dan 1 dag geleden zijn gewijzigd. De container opdracht wordt door gegeven met behulp van een omgevings variabele. De taak wordt uitgevoerd zonder bron context.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Uitvoeren in een geplande taak

In het volgende voor beeld wordt de opdracht [AZ ACR Task Create][az-acr-task-create] gebruikt om een [geplande ACR-taak](container-registry-tasks-scheduled.md)te maken. De taak verwijdert Tags die meer dan zeven dagen geleden zijn gewijzigd in de `hello-world` opslag plaats. De container opdracht wordt door gegeven met behulp van een omgevings variabele. De taak wordt uitgevoerd zonder bron context.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Voer de opdracht [AZ ACR Task show][az-acr-task-show] uit om te zien dat de timer trigger is geconfigureerd.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Grote aantallen Tags en manifesten opschonen

Het verwijderen van een groot aantal tags en manifesten kan enkele minuten of langer duren. Als u duizenden Tags en manifesten wilt verwijderen, moet de opdracht mogelijk langer worden uitgevoerd dan de standaardtime-outtijd van 600 seconden voor een taak op aanvraag of 3600 seconden voor een geplande taak. Als de time-outwaarde wordt overschreden, wordt alleen een subset van tags en manifesten verwijderd. Om ervoor te zorgen dat een grootschalige opschoon bewerking is voltooid, geeft `--timeout` u de para meter door om de waarde te verhogen. 

Met de volgende on-demand taak wordt bijvoorbeeld een time-outperiode van 3600 seconden ingesteld (1 uur):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Voor beeld: geplande opschoning van meerdere opslag plaatsen in een REGI ster

In dit voor beeld wordt uitgelegd hoe u `acr purge` meerdere opslag plaatsen in een REGI ster periodiek opschoont. U kunt bijvoorbeeld een ontwikkelings pijplijn hebben die installatie kopieën pusht naar de `samples/devimage1` opslag plaatsen `samples/devimage2` . U importeert regel matig ontwikkelings installatie kopieën in een productie opslagplaats voor uw implementaties, dus u hebt de ontwikkel installatie kopieën niet meer nodig. Op wekelijkse basis verwijdert u de `samples/devimage1` en `samples/devimage2` opslag plaatsen, ter voor bereiding op het werk van de volgende week.

### <a name="preview-the-purge"></a>Voor beeld van de opschoning

Voordat u gegevens verwijdert, raden we u aan om een opschoon taak op aanvraag uit te voeren met behulp van de `--dry-run` para meter. Met deze optie kunt u de tags en manifesten zien die de opdracht opschoont, zonder dat er gegevens worden verwijderd. 

In het volgende voor beeld selecteert het filter in elke opslag plaats alle tags. De `--ago 0d` para meter komt overeen met afbeeldingen van alle leeftijden in de opslag plaatsen die overeenkomen met de filters. Wijzig zo nodig de selectie criteria voor uw scenario. De `--untagged` para meter geeft aan dat er naast labels ook manifesten moeten worden verwijderd. De container opdracht wordt door gegeven aan de opdracht [AZ ACR run][az-acr-run] met behulp van een omgevings variabele.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Bekijk de uitvoer van de opdracht om de labels en manifesten te zien die overeenkomen met de selectie parameters. Omdat de opdracht wordt uitgevoerd met `--dry-run` , worden er geen gegevens verwijderd.

Voorbeelduitvoer:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>De opschoning plannen

Nadat u de droge uitvoering hebt gecontroleerd, maakt u een geplande taak om de opschoon bewerking te automatiseren. In het volgende voor beeld wordt een wekelijkse taak op zondag om 1:00 UTC gepland om de vorige opdracht leegmaken uit te voeren:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Voer de opdracht [AZ ACR Task show][az-acr-task-show] uit om te zien dat de timer trigger is geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere opties voor het [verwijderen van afbeeldings gegevens](container-registry-delete.md) in azure container Registry.

Zie [opslag van container installatie kopieën in azure container Registry](container-registry-storage.md)voor meer informatie over installatie kopie opslag.

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

