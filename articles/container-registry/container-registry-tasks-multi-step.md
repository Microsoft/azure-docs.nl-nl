---
title: Taak met meerdere stappen voor het bouwen, testen & patch-afbeelding
description: Inleiding tot taken met meerdere stappen, een functie van ACR-taken in Azure Container Registry die werkstromen op basis van taken biedt voor het bouwen, testen en patchen van containerafbeeldingen in de cloud.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: d57044fa8a0db7d661eb50284b34a6bbec9a1879
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781015"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Build-, test- en patchtaken met meerdere stappen uitvoeren in ACR-taken

Taken met meerdere stappen breiden de build-and-push-functie van één ACR-taken uit met werkstromen met meerdere stappen en meerdere containers. Gebruik taken met meerdere stappen om meerdere afbeeldingen te bouwen en te pushen, in serie of parallel. Voer deze afbeeldingen vervolgens uit als opdrachten binnen één taakuit te voeren. Elke stap definieert een build- of pushbewerking van een container-container en kan ook de uitvoering van een container definiëren. Elke stap in een taak met meerdere stappen gebruikt een container als uitvoeringsomgeving.

> [!IMPORTANT]
> Als u eerder taken hebt gemaakt tijdens de preview met de opdracht `az acr build-task`, moeten deze taken opnieuw worden gemaakt met de opdracht [az acr task][az-acr-task].

U kunt bijvoorbeeld een taak uitvoeren met stappen die de volgende logica automatiseren:

1. Een webtoepassingsafbeelding bouwen
1. De webtoepassingscontainer uitvoeren
1. Een testafbeelding voor een webtoepassing bouwen
1. De testcontainer voor de webtoepassing uitvoeren die tests uitvoert op de toepassingscontainer die wordt uitgevoerd
1. Als de tests slagen, bouwt u een helm-grafiekarchiefpakket
1. Een uitvoeren met `helm upgrade` behulp van het nieuwe helm-grafiekarchiefpakket

Alle stappen worden uitgevoerd in Azure, door het werk te offloaden naar de rekenbronnen van Azure en u te vrij te maken van infrastructuurbeheer. Naast uw Azure-containerregister betaalt u alleen voor de resources die u gebruikt. Zie de sectie **Container Build** in Azure Container Registry pricing voor meer informatie [over prijzen.][pricing]


## <a name="common-task-scenarios"></a>Algemene taakscenario's

Taken met meerdere stappen maken scenario's mogelijk zoals de volgende logica:

* Een of meer containerafbeeldingen bouwen, taggen en pushen, in serie of parallel.
* Voer de eenheidstest en de codedekkingsresultaten uit en leg deze vast.
* Functionele tests uitvoeren en vastleggen. ACR-taken ondersteunt het uitvoeren van meer dan één container en voert een reeks aanvragen tussen deze containers uit.
* Taakgebaseerde uitvoering uitvoeren, met inbegrip van de stappen vóór/na het bouwen van een container-image.
* Implementeer een of meer containers met uw favoriete implementatie-engine in uw doelomgeving.

## <a name="multi-step-task-definition"></a>Taakdefinitie met meerdere stappen

Een taak met meerdere stappen in ACR-taken wordt gedefinieerd als een reeks stappen in een YAML-bestand. Elke stap kan afhankelijkheden opgeven van de geslaagde voltooiing van een of meer vorige stappen. De volgende typen taken zijn beschikbaar:

* [`build`](container-registry-tasks-reference-yaml.md#build): Bouw een of meer containerafbeeldingen met behulp van `docker build` vertrouwde syntaxis, in reeksen of parallel.
* [`push`](container-registry-tasks-reference-yaml.md#push): Push ingebouwde afbeeldingen naar een containerregister. Privéregisters zoals Azure Container Registry worden ondersteund, net als de openbare Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Voer een container uit, zodat deze kan worden uitgevoerd als een functie binnen de context van de taak die wordt uitgevoerd. U kunt parameters doorgeven aan de van de container en eigenschappen opgeven, `[ENTRYPOINT]` zoals env, loskoppelen en andere bekende `docker run` parameters. Het `cmd` staptype maakt eenheids- en functionele tests mogelijk, met gelijktijdige uitvoering van de container.

De volgende codefragmenten laten zien hoe u deze typen taken kunt combineren. Taken met meerdere stappen kunnen net zo eenvoudig zijn als het bouwen van één afbeelding van een Dockerfile en het pushen naar uw register, met een YAML-bestand dat vergelijkbaar is met:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Of complexer, zoals deze fictieve definitie met meerdere stappen die stappen bevat voor bouwen, testen, Helm-pakketten en Helm-implementatie (configuratie van containerregister en Helm-opslagplaats wordt niet weergegeven):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build: -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

Zie [taakvoorbeelden](container-registry-tasks-samples.md) voor YAML-bestanden met meerdere stappen en Dockerfiles voor verschillende scenario's.

## <a name="run-a-sample-task"></a>Een voorbeeldtaak uitvoeren

Taken ondersteunen zowel handmatige uitvoering, ook wel een 'snelle uitvoering' genoemd, als geautomatiseerde uitvoering op Git-doorvoering of update van basisafbeeldingen.

Als u een taak wilt uitvoeren, definieert u eerst de stappen van de taak in een YAML-bestand en voert u vervolgens de Azure CLI-opdracht [az acr run uit.][az-acr-run]

Hier is een voorbeeld van een Azure CLI-opdracht voor het uitvoeren van een taak met behulp van een YAML-voorbeeldtaakbestand. Met de stappen wordt een afbeelding gebouwd en vervolgens ge pusht. Werk `\<acrName\>` bij met de naam van uw eigen Azure-containerregister voordat u de opdracht gaat uitvoeren.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Wanneer u de taak hebt uitgevoerd, moet de uitvoer de voortgang van elke stap die in het YAML-bestand is gedefinieerd, tonen. In de volgende uitvoer worden de stappen weergegeven als `acb_step_0` en `acb_step_1` .

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Zie de zelfstudie over het automatiseren van [](container-registry-tutorial-build-task.md) builds van afbeeldingen en builds van [basis-updates](container-registry-tutorial-base-image-update.md) voor meer informatie over geautomatiseerde builds op Git-commit of update van basisafbeeldingen.

## <a name="next-steps"></a>Volgende stappen

U vindt hier verwijzingen naar taken met meerdere stappen en voorbeelden:

* [Taakverwijzing:](container-registry-tasks-reference-yaml.md) typen taakstap, hun eigenschappen en gebruik.
* [Taakvoorbeelden:](container-registry-tasks-samples.md) `task.yaml` voorbeeld- en Docker-bestanden voor verschillende scenario's, eenvoudig tot complex.
* [Cmd-repo:](https://github.com/AzureCR/cmd) een verzameling containers als opdrachten voor ACR-taken.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task]: /cli/azure/acr/task
