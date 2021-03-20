---
title: Taak met meerdere stappen voor het maken, testen van & patch-installatie kopie
description: Inleiding tot taken met meerdere stappen, een functie van ACR-taken in Azure Container Registry die werk stromen op basis van taken bieden voor het bouwen, testen en repareren van container installatie kopieën in de Cloud.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78399693"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Meerdere stappen uitvoeren voor het bouwen, testen en patchen van taken in ACR-taken

Met taken uit meerdere stappen worden de build-en push functionaliteit voor één installatie kopie uitgebreid van ACR-taken met meerdere stappen, op meerdere containers gebaseerde werk stromen. Gebruik taken met meerdere stappen voor het bouwen en pushen van verschillende installatie kopieën, in serie of parallel. Voer vervolgens deze installatie kopieën uit als opdrachten binnen één taak. Elke stap definieert een build-of push bewerking voor een container installatie kopie en kan ook de uitvoering van een container definiëren. Bij elke stap in een taak met meerdere stappen wordt een container als uitvoerings omgeving gebruikt.

> [!IMPORTANT]
> Als u eerder taken hebt gemaakt tijdens de preview met de opdracht `az acr build-task`, moeten deze taken opnieuw worden gemaakt met de opdracht [az acr task][az-acr-task].

U kunt bijvoorbeeld een taak uitvoeren met stappen waarmee de volgende logica wordt geautomatiseerd:

1. Een installatie kopie voor een webtoepassing bouwen
1. De Web Application-container uitvoeren
1. Een test installatie kopie voor een webtoepassing bouwen
1. Voer de test container voor webtoepassingen uit die tests uitvoert op de actieve toepassings container
1. Als de tests zijn geslaagd, bouwt u een helm-archief pakket voor grafieken op
1. Een `helm upgrade` gebruik maken van het nieuwe helm-diagram archief pakket

Alle stappen worden uitgevoerd in azure, waardoor het werk wordt overgeleid naar de reken resources van Azure en u vrijmaakt van infrastructuur beheer. Naast uw Azure container Registry betaalt u alleen voor de resources die u gebruikt. Zie voor meer informatie over prijzen het gedeelte **container bouwen** in [Azure container Registry prijzen][pricing].


## <a name="common-task-scenarios"></a>Veelvoorkomende taak scenario's

Met taken met meerdere stappen kunt u scenario's zoals de volgende logica inschakelen:

* Een of meer container installatie kopieën bouwen, labelen en pushen, in serie of parallel.
* Resultaten van de test-en code dekking van de eenheid uitvoeren en vastleggen.
* Functionele tests uit te voeren en vast te leggen. ACR-taken bieden ondersteuning voor het uitvoeren van meer dan één container, waarbij een reeks aanvragen tussen de containers wordt uitgevoerd.
* Voer uitvoeringen op basis van een taak uit, met inbegrip van de stappen voor het bouwen van een container installatie kopie.
* Implementeer een of meer containers met uw favoriete implementatie-engine naar uw doel omgeving.

## <a name="multi-step-task-definition"></a>Definitie van meerdere stappen taken

Een taak met meerdere stappen in ACR-taken wordt gedefinieerd als een reeks stappen in een YAML-bestand. Elke stap kan afhankelijkheden opgeven op het slagen van een of meer van de vorige stappen. De volgende taak stap typen zijn beschikbaar:

* [`build`](container-registry-tasks-reference-yaml.md#build): Maak een of meer container installatie kopieën met behulp van de vertrouwde `docker build` syntaxis, in serie of parallel.
* [`push`](container-registry-tasks-reference-yaml.md#push): Push gemaakte installatie kopieën naar een container register. Persoonlijke registers zoals Azure Container Registry worden ondersteund, evenals de open bare docker-hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Voer een container uit, zodat deze als functie kan worden gebruikt in de context van de actieve taak. U kunt para meters door geven aan de container `[ENTRYPOINT]` en eigenschappen zoals env, Detach en andere bekende `docker run` para meters opgeven. `cmd`Met het stap type kunnen eenheid en functionele tests worden uitgevoerd, met gelijktijdige container uitvoering.

De volgende code fragmenten laten zien hoe u deze taak stap typen kunt combi neren. Taken met meerdere stappen kunnen zo eenvoudig zijn als het bouwen van één installatie kopie van een Dockerfile en naar uw REGI ster pushen, met een YAML-bestand dat vergelijkbaar is met:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Of complexere, zoals deze fictieve definitie van meerdere stappen, die stappen bevat voor Build, test, helm package en helm Deploy (container Registry en helm opslagplaats configuratie niet weer gegeven):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
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

Zie [taak voorbeelden](container-registry-tasks-samples.md) voor YAML-bestanden en Dockerfiles voor taken in meerdere stappen voor verschillende scenario's.

## <a name="run-a-sample-task"></a>Een voorbeeld taak uitvoeren

Taken ondersteunen beide hand matige uitvoering, een zogeheten ' Quick run ', en de automatische uitvoering van een update voor git-door Voer of basis installatie kopie.

Als u een taak wilt uitvoeren, definieert u eerst de stappen van de taak in een YAML-bestand en voert u vervolgens de Azure CLI-opdracht [AZ ACR run][az-acr-run]uit.

Hier volgt een voor beeld van een Azure CLI-opdracht waarmee een taak wordt uitgevoerd met een voor beeld van een YAML-bestand van de taak. Met de stappen wordt een installatie kopie gemaakt en gepusht. Werk `\<acrName\>` bij met de naam van uw eigen Azure container Registry voordat u de opdracht uitvoert.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Wanneer u de taak uitvoert, moet de uitvoer de voortgang van elke stap weer geven die is gedefinieerd in het YAML-bestand. In de volgende uitvoer worden de stappen als en weer gegeven `acb_step_0` `acb_step_1` .

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

Zie voor meer informatie over geautomatiseerd builds van Git of basis installatie kopie-update de artikelen voor het [automatiseren van installatie kopieën](container-registry-tutorial-build-task.md) en [basis installatie kopieën update builds](container-registry-tutorial-base-image-update.md) .

## <a name="next-steps"></a>Volgende stappen

U vindt hier een Naslag informatie en voor beelden voor taken in meerdere stappen:

* [Taak verwijzing](container-registry-tasks-reference-yaml.md) -taak stap typen, eigenschappen en gebruik.
* [Taak voorbeelden](container-registry-tasks-samples.md) : voor beeld `task.yaml` en docker-bestanden voor verschillende scenario's, eenvoudig naar complex.
* [Cmd opslag plaats](https://github.com/AzureCR/cmd) : een verzameling containers als opdrachten voor ACR-taken.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task