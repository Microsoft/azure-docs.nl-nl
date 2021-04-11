---
title: Recepten voor docker-containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over het bouwen, testen en opslaan van containers met enkele of alle configuratie-instellingen voor implementatie en hergebruik.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2226e4e7c5df60a4a601408da04a1f2e67c4dc4f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067772"
---
# <a name="create-containers-for-reuse"></a>Containers maken voor hergebruik

Gebruik deze container recepten om Cognitive Services containers te maken die opnieuw kunnen worden gebruikt. Containers kunnen worden gebouwd met enkele of alle configuratie-instellingen, zodat ze _niet_ nodig zijn wanneer de container wordt gestart.

Zodra u deze nieuwe laag of container hebt (met instellingen) en u deze lokaal hebt getest, kunt u de container opslaan in een container register. Wanneer de container wordt gestart, zijn alleen de instellingen nodig die momenteel niet zijn opgeslagen in de container. De persoonlijke register container bevat configuratie ruimte die u kunt door geven aan de hand van deze instellingen in.

## <a name="docker-run-syntax"></a>Syntaxis van docker-uitvoering

`docker run`Voor beelden in dit document wordt ervan uitgegaan dat een Windows-console met een `^` regel voortzettings teken. Houd rekening met het volgende voor eigen gebruik:

* Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.
* Als u een ander besturings systeem dan Windows gebruikt, of een andere console dan Windows-console, gebruikt u de juiste console/terminal, syntaxis voor koppelingen en een regel vervolg teken voor uw console en systeem.  Omdat de Cognitive Services container een Linux-besturings systeem is, gebruikt de doel koppeling een syntaxis voor de Linux-stijl.
* `docker run` voor beelden gebruiken de Directory uit het `c:` station om te voor komen dat er machtigings conflicten zijn in Windows. Als u een specifieke directory moet gebruiken als de invoer Directory, moet u mogelijk de machtiging docker-service verlenen.

## <a name="store-no-configuration-settings-in-image"></a>Geen configuratie-instellingen opslaan in afbeelding

In de voorbeeld `docker run` opdrachten voor elke service worden geen configuratie-instellingen in de container opgeslagen. Wanneer u de container vanuit een console of register service start, moeten die configuratie-instellingen worden door gegeven. De persoonlijke register container bevat configuratie ruimte die u kunt door geven aan de hand van deze instellingen in.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Recept hergebruiken: alle configuratie-instellingen opslaan met container

Maak een met deze instellingen om alle configuratie-instellingen op te slaan `Dockerfile` .

Problemen met deze aanpak:

* De nieuwe container heeft een afzonderlijke naam en label van de oorspronkelijke container.
* Als u deze instellingen wilt wijzigen, moet u de waarden van de Dockerfile wijzigen, de installatie kopie opnieuw samen stellen en opnieuw publiceren naar het REGI ster.
* Als iemand toegang krijgt tot uw container register of uw lokale host, kan deze de container uitvoeren en de Cognitive Services-eind punten gebruiken.
* Als uw cognitieve service geen invoer koppels vereist, voegt u de `COPY` lijnen niet toe aan uw Dockerfile.

Maak Dockerfile, haal uit de bestaande Cognitive Services-container die u wilt gebruiken, en gebruik vervolgens docker-opdrachten in de Dockerfile om informatie in te stellen of in te trekken die de container nodig heeft.

Dit voor beeld:

* Hiermee stelt u het facturerings eindpunt `{BILLING_ENDPOINT}` in op basis van de omgevings sleutel van de host met `ENV` .
* Hiermee stelt u de facturerings-API-sleutel `{ENDPOINT_KEY}` van de omgevings sleutel van de host in met behulp van ' env.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Recept hergebruiken: facturerings instellingen opslaan met container

In dit voor beeld ziet u hoe u de Text Analytics ' sentiment-container bouwt van een Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Bouw en voer de container [lokaal](#how-to-use-container-on-your-local-host) of vanuit uw [privé register container](#how-to-add-container-to-private-registry) waar nodig uit.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Recept hergebruiken: instellingen voor facturering en koppelen opslaan met container

In dit voor beeld ziet u hoe u Language Understanding gebruikt, facturering en modellen opslaat vanuit de Dockerfile.

* Kopieert het Language Understanding model bestand (LUIS) van het bestands systeem van de host met behulp van `COPY` .
* De LUIS-container ondersteunt meer dan één model. Als alle modellen in dezelfde map zijn opgeslagen, hebt u één instructie nodig `COPY` .
* Voer het docker-bestand uit vanaf de relatieve bovenliggende map van de model invoer Directory. Voer voor het volgende voor beeld de `docker build` `docker run` opdrachten en uit vanaf het relatieve bovenliggende item van `/input` . De eerste `/input` op de `COPY` opdracht is de map van de hostcomputer. De tweede `/input` is de map van de container.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Bouw en voer de container [lokaal](#how-to-use-container-on-your-local-host) of vanuit uw [privé register container](#how-to-add-container-to-private-registry) waar nodig uit.

## <a name="how-to-use-container-on-your-local-host"></a>Container gebruiken op uw lokale host

Als u het docker-bestand wilt maken, vervangt u door `<your-image-name>` de nieuwe naam van de installatie kopie en gebruikt u vervolgens:

```console
docker build -t <your-image-name> .
```

Als u de installatie kopie wilt uitvoeren en deze wilt verwijderen wanneer de container stopt ( `--rm` ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Een container toevoegen aan een persoonlijk REGI ster

Volg deze stappen om de Dockerfile te gebruiken en plaats de nieuwe installatie kopie in het persoonlijke container register.  

1. Maak een `Dockerfile` met de tekst van het opnieuw gebruiken van het recept. A `Dockerfile` heeft geen extensie.

1. Vervang waarden in de punt haken door uw eigen waarden.

1. Bouw het bestand in een installatie kopie op de opdracht regel of in de Terminal met de volgende opdracht. Vervang de waarden in de punt haken, `<>` met uw eigen container naam en label.  

    De tag optie, `-t` , is een manier om informatie toe te voegen over wat u voor de container hebt gewijzigd. Een container naam van `modified-LUIS` geeft bijvoorbeeld aan dat de oorspronkelijke container is gelaagd. De label naam van `with-billing-and-model` geeft aan hoe de container language Understanding (Luis) is gewijzigd.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Meld u aan bij Azure CLI vanuit een-console. Met deze opdracht wordt een browser geopend en is verificatie vereist. Nadat u bent geverifieerd, kunt u de browser sluiten en blijven werken in de-console.

    ```azurecli
    az login
    ```

1. Meld u aan bij uw persoonlijke REGI ster met Azure CLI vanuit een-console.

    Vervang de waarden in de punt haken, `<my-registry>` met uw eigen register naam.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    U kunt zich ook aanmelden met docker-aanmelding als u een Service-Principal hebt toegewezen.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Label de container met de locatie van het persoonlijke REGI ster. Vervang de waarden in de punt haken, `<my-registry>` met uw eigen register naam. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Als u geen label naam gebruikt, `latest` wordt geïmpliceerd.

1. Push de nieuwe installatie kopie naar uw persoonlijke container register. Wanneer u uw persoonlijke container register bekijkt, wordt de naam van de opslag plaats die wordt gebruikt in de volgende CLI-opdracht.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure container instance maken en gebruiken](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->