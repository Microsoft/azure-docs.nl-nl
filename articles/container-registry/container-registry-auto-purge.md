---
title: Tags en manifesten opseen
description: Gebruik een opsreeropdracht om meerdere tags en manifesten te verwijderen uit een Azure-containerregister op basis van leeftijd en een tagfilter, en plan optioneel opstingsbewerkingen.
ms.topic: article
ms.date: 02/19/2021
ms.openlocfilehash: 562d1940459cb1594b7cd9aca2af280b05a4e419
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784187"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Automatisch afbeeldingen ops purge uit een Azure-containerregister

Wanneer u een Azure-containerregister gebruikt als onderdeel van een ontwikkelingswerkstroom, kan het register snel worden gevuld met afbeeldingen of andere artefacten die na een korte periode niet nodig zijn. U kunt alle tags verwijderen die ouder zijn dan een bepaalde duur of overeenkomen met een opgegeven naamfilter. Als u meerdere artefacten snel wilt verwijderen, introduceert dit artikel de opdracht die u kunt uitvoeren als `acr purge` een ACR-taak op aanvraag [of als een geplande](container-registry-tasks-scheduled.md) ACR-taak. 

De opdracht wordt momenteel gedistribueerd in een openbare container-afbeelding ( ), gebouwd op basis van broncode in de `acr purge` `mcr.microsoft.com/acr/acr-cli:0.4` opslagplaats [acr-cli](https://github.com/Azure/acr-cli) in GitHub.

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de ACR-taakvoorbeelden in dit artikel uit te voeren. Als u deze lokaal wilt gebruiken, is versie 2.0.76 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren. 

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

> [!WARNING]
> Wees voorzichtig `acr purge` met de opdracht . Verwijderde afbeeldingsgegevens kunnen niet worden teruggehaald. Als u systemen hebt die afbeeldingen op halen via manifest digest (in plaats van de naam van de afbeelding), moet u afbeeldingen zondertagged niet opsisten. Als u niet-getagged afbeeldingen kunt verwijderen, kunnen deze systemen de afbeeldingen niet uit het register halen. In plaats van een manifest op te halen, kunt u overwegen een uniek *tagschema te* gebruiken, een [aanbevolen best practice](container-registry-image-tag-version.md).

Als u tags of manifesten met één afbeelding wilt verwijderen met behulp van Azure CLI-opdrachten, zie [Containerafbeeldingen verwijderen in Azure Container Registry](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Gebruik de opdracht purge

De containeropdracht verwijdert afbeeldingen per tag in een opslagplaats die overeenkomen met een naamfilter en `acr purge` die ouder zijn dan een opgegeven duur. Standaard worden alleen tagverwijzingen verwijderd, niet de onderliggende [manifesten en](container-registry-concepts.md#manifest) laaggegevens. De opdracht heeft een optie om ook manifesten te verwijderen. 

> [!NOTE]
> `acr purge` verwijdert geen afbeeldingstag of opslagplaats waar het `write-enabled` kenmerk is ingesteld op `false` . Zie Lock a container image in an Azure container registry (Een [containerafbeelding vergrendelen in een Azure-containerregister) voor meer informatie.](container-registry-image-lock.md)

`acr purge` is ontworpen om te worden uitgevoerd als een containeropdracht in een [ACR-taak,](container-registry-tasks-overview.md)zodat deze automatisch wordt geverifieerd bij het register waar de taak wordt uitgevoerd en er acties worden uitgevoerd. In de taakvoorbeelden in dit artikel wordt de `acr purge` [opdrachtalias](container-registry-tasks-reference-yaml.md#aliases) gebruikt in plaats van een volledig gekwalificeerde opdracht voor de containerafbeelding.

Geef ten minste het volgende op wanneer u uit te `acr purge` voeren:

* `--filter` - Een opslagplaats en een *reguliere expressie om* tags in de opslagplaats te filteren. Voorbeelden: `--filter "hello-world:.*"` komt overeen met alle tags in de opslagplaats en komt overeen met tags die beginnen met `hello-world` `--filter "hello-world:^1.*"` `1` . Geef meerdere `--filter` parameters door om meerdere opslagplaatsen op te schoonen.
* `--ago` - Een [go-stijlduurreeks om](https://golang.org/pkg/time/) een duur aan te geven waarna afbeeldingen worden verwijderd. De duur bestaat uit een reeks van een of meer decimale getallen, elk met een eenheidsachtervoegsel. Geldige tijdseenheden zijn 'd' voor dagen, 'h' voor uren en 'm' voor minuten. Selecteer bijvoorbeeld alle gefilterde afbeeldingen die meer dan 2 dagen, 3 uur en 6 minuten geleden voor het laatst zijn gewijzigd en selecteert afbeeldingen die meer dan `--ago 2d3h6m` 1,5 uur geleden voor het laatst zijn `--ago 1.5h` gewijzigd.

`acr purge` ondersteunt verschillende optionele parameters. De volgende twee worden gebruikt in voorbeelden in dit artikel:

* `--untagged` -Geeft aan dat manifesten die niet zijn gekoppeld tags (*manifesten* zonder tag ) worden verwijderd.
* `--dry-run` -Geeft aan dat er geen gegevens worden verwijderd, maar de uitvoer is hetzelfde als wanneer de opdracht wordt uitgevoerd zonder deze vlag. Deze parameter is handig voor het testen van een opspoelopdracht om ervoor te zorgen dat deze niet per ongeluk gegevens verwijdert die u wilt behouden.
* `--keep` -Geeft aan dat de meest recente x aantal te verwijderen tags worden bewaard.
* `--concurrency` - Hiermee geeft u een aantal opstingstaken op die gelijktijdig moeten worden verwerkt. Er wordt een standaardwaarde gebruikt als deze parameter niet is opgegeven.

Voer uit voor aanvullende `acr purge --help` parameters. 

`acr purge`ondersteunt andere functies van ACR-taken opdrachten, waaronder het uitvoeren van variabelen [en](container-registry-tasks-logs.md) logboeken voor [taakruns](container-registry-tasks-reference-yaml.md#run-variables) die worden gestreamd en ook worden opgeslagen voor later ophalen.

### <a name="run-in-an-on-demand-task"></a>Uitvoeren in een taak op aanvraag

In het volgende voorbeeld wordt de [opdracht az acr run gebruikt][az-acr-run] om de opdracht op aanvraag uit te `acr purge` voeren. In dit voorbeeld worden alle afbeeldingstags en manifesten in de opslagplaats `hello-world` in *myregistry verwijderd die* meer dan één dag geleden zijn gewijzigd. De containeropdracht wordt doorgegeven met behulp van een omgevingsvariabele. De taak wordt uitgevoerd zonder broncontext.

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

In het volgende voorbeeld wordt de [opdracht az acr task create][az-acr-task-create] gebruikt om een dagelijkse geplande [ACR-taak te maken.](container-registry-tasks-scheduled.md) De taak ruimt tags op die meer dan 7 dagen geleden zijn gewijzigd in de `hello-world` opslagplaats. De containeropdracht wordt doorgegeven met behulp van een omgevingsvariabele. De taak wordt uitgevoerd zonder broncontext.

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

Voer de [opdracht az acr task show][az-acr-task-show] uit om te zien of de timertrigger is geconfigureerd.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Grote aantallen tags en manifesten opseen

Het verwijderen van een groot aantal tags en manifesten kan enkele minuten of langer duren. Als u duizenden tags en manifesten wilt verwijderen, moet de opdracht mogelijk langer worden uitgevoerd dan de standaard time-outtijd van 600 seconden voor een taak op aanvraag, of 3600 seconden voor een geplande taak. Als de time-outtijd wordt overschreden, wordt slechts een subset van tags en manifesten verwijderd. Geef de parameter door om de waarde te verhogen om ervoor te zorgen dat grootschalige opsting `--timeout` is voltooid. 

Met de volgende taak op aanvraag stelt u bijvoorbeeld een time-outtijd in van 3600 seconden (1 uur):

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Voorbeeld: Geplande opspatting van meerdere opslagplaatsen in een register

In dit voorbeeld wordt gebruik van gebruikt om periodiek meerdere opslagplaatsen `acr purge` in een register op te schonen. U hebt bijvoorbeeld een ontwikkelingspijplijn die afbeeldingen naar de opslagplaatsen en `samples/devimage1` `samples/devimage2` pusht. U importeert regelmatig ontwikkelafbeeldingen in een productieopslagplaats voor uw implementaties, zodat u de ontwikkelafbeeldingen niet meer nodig hebt. Wekelijks schoont u de opslagplaatsen en op, ter voorbereiding op het werk van `samples/devimage1` `samples/devimage2` de komende week.

### <a name="preview-the-purge"></a>Bekijk een voorbeeld van de opsting

Voordat u gegevens gaat verwijderen, raden we u aan een opstingstaak op aanvraag uit te voeren met behulp van de `--dry-run` parameter . Met deze optie kunt u de tags en manifesten zien die met de opdracht worden verwijderd, zonder gegevens te verwijderen. 

In het volgende voorbeeld selecteert het filter in elke opslagplaats alle tags. De `--ago 0d` parameter komt overeen met afbeeldingen van alle leeftijden in de opslagplaatsen die overeenkomen met de filters. Wijzig de selectiecriteria naar behoefte voor uw scenario. De `--untagged` parameter geeft aan dat naast tags ook manifesten moeten worden verwijderd. De containeropdracht wordt doorgegeven aan de [opdracht az acr run met][az-acr-run] behulp van een omgevingsvariabele.

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

Bekijk de uitvoer van de opdracht om de tags en manifesten te zien die overeenkomen met de selectieparameters. Omdat de opdracht wordt uitgevoerd met `--dry-run` , worden er geen gegevens verwijderd.

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

### <a name="schedule-the-purge"></a>De opsting plannen

Nadat u de dry run hebt gecontroleerd, maakt u een geplande taak om het leeg maken te automatiseren. In het volgende voorbeeld wordt een wekelijkse taak gepland op zondag om 1:00 UTC om de vorige opstingsopdracht uit te voeren:

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

Voer de [opdracht az acr task show][az-acr-task-show] uit om te zien of de timertrigger is geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere opties voor [het verwijderen van afbeeldingsgegevens](container-registry-delete.md) in Azure Container Registry.

Zie Container image storage in Azure Container Registry voor [meer informatie over opslag van Azure Container Registry.](container-registry-storage.md)

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
