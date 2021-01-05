---
title: GitHub acties werk stromen voor statische Web Apps van Azure
description: Meer informatie over het gebruik van GitHub-opslag plaatsen voor het instellen van continue implementatie naar statische Web Apps van Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 5e6188ca2e8e0972e86bed578144a29a96570876
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901195"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>GitHub actions-werk stromen voor de preview-versie van Azure static Web Apps

Wanneer u een nieuwe statische web-app van Azure maakt, genereert Azure een werk stroom voor GitHub-acties om de continue implementatie van de app te beheren. De werk stroom wordt aangedreven door een YAML-bestand. In dit artikel wordt een overzicht van de structuur en opties van het werk stroom bestand beschreven.

Implementaties worden geïnitieerd door [Triggers](#triggers), waarmee [taken](#jobs) worden uitgevoerd die door afzonderlijke [stappen](#steps)worden gedefinieerd.

## <a name="file-location"></a>Bestands locatie

Wanneer u uw GitHub-opslag plaats koppelt aan een statische Web Apps van Azure, wordt een werk stroom bestand toegevoegd aan de opslag plaats.

Volg deze stappen om het gegenereerde werk stroom bestand weer te geven.

1. Open de opslag plaats van de app op GitHub.
1. Klik op het tabblad _code_ op de `.github/workflows` map.
1. Klik op het bestand met een naam die er als volgt uitziet `azure-static-web-apps-<RANDOM_NAME>.yml` .

Het YAML-bestand in uw opslag plaats is vergelijkbaar met het volgende voor beeld:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v2
      with:
        submodules: true
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        output_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>Triggers

Met een [trigger](https://help.github.com/actions/reference/events-that-trigger-workflows) voor github-acties wordt een melding verzonden naar een github-werk stroom voor het uitvoeren van een taak die is gebaseerd op gebeurtenis triggers. Triggers worden weer gegeven met behulp `on` van de eigenschap in het werk stroom bestand.

```yml
on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master
```

Via de instellingen die aan de eigenschap zijn gekoppeld `on` , kunt u definiëren welke vertakkingen een taak activeren en triggers instellen om te worden geactiveerd voor verschillende statussen van pull-aanvragen.

In dit voor beeld wordt een werk stroom gestart terwijl de _hoofd_ vertakking verandert. Wijzigingen die de werk stroom starten, bevatten push door voeren en pull-aanvragen openen voor de gekozen vertakking.

## <a name="jobs"></a>Taken

Voor elke gebeurtenis trigger moet een gebeurtenis-handler worden uitgevoerd. [Taken](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) bepalen wat er gebeurt wanneer een gebeurtenis wordt geactiveerd.

Er zijn twee beschik bare taken in het werk stroom bestand statisch Web Apps.

| Naam  | Beschrijving |
|---------|---------|
|`build_and_deploy_job` | Wordt uitgevoerd wanneer u een push uitvoert of een pull-aanvraag opent voor de vertakking die in de eigenschap wordt vermeld `on` . |
|`close_pull_request_job` | Wordt alleen uitgevoerd wanneer u een pull-aanvraag sluit die de faserings omgeving verwijdert die is gemaakt van pull-aanvragen. |

## <a name="steps"></a>Stappen

Stappen zijn opeenvolgende taken voor een taak. Met een stap worden acties uitgevoerd, zoals het installeren van afhankelijkheden, het uitvoeren van tests en het implementeren van uw toepassing naar productie.

Een werk stroom bestand definieert de volgende stappen.

| Taak  | Stappen  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>Hiermee wordt de opslag plaats in de omgeving van de actie gecontroleerd.<li>Bouwt en implementeert de opslag plaats naar statische Azure-Web Apps.</ol>|
| `close_pull_request_job` | <ol><li>Hiermee wordt een statische Web Apps van Azure geïnformeerd dat een pull-aanvraag is gesloten.</ol>|

## <a name="build-and-deploy"></a>Bouwen en implementeren

De stap met de naam `Build and Deploy` bouwt en implementeert op uw statische Azure web apps-exemplaar. Onder de `with` sectie kunt u de volgende waarden voor uw implementatie aanpassen.

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    output_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| Eigenschap | Beschrijving | Vereist |
|---|---|---|
| `app_location` | Locatie van de toepassings code.<br><br>Voer bijvoorbeeld `/` in als de bron code van uw toepassing zich in de hoofdmap van de opslag plaats bevindt, of `/app` als de code van uw toepassing zich in de map bevindt `app` . | Ja |
| `api_location` | De locatie van uw Azure Functions-code.<br><br>Voer bijvoorbeeld `/api` in als uw app-code zich in een map met de naam bevindt `api` . Als er geen Azure Functions-app wordt gedetecteerd in de map, mislukt de build, wordt ervan uitgegaan dat u geen API wilt. | Nee |
| `output_location` | Locatie van de map voor het build-uitvoer ten opzichte van de `app_location` .<br><br>Bijvoorbeeld, als de bron code van uw toepassing zich in bevindt `/app` en het build-script bestanden naar de `/app/build` map levert, vervolgens `build` als `output_location` waarde instellen. | Nee |

De `repo_token` `action` waarden, en `azure_static_web_apps_api_token` worden door Azure static web apps voor u ingesteld, niet hand matig worden gewijzigd.

## <a name="custom-build-commands"></a>Aangepaste build-opdrachten

U kunt een nauw keurige controle hebben over de opdrachten die tijdens een implementatie worden uitgevoerd. De volgende opdrachten kunnen worden gedefinieerd in de sectie van een taak `with` .

De implementatie aanroept altijd `npm install` vóór een aangepaste opdracht.

| Opdracht            | Beschrijving |
|---------------------|-------------|
| `app_build_command` | Hiermee wordt een aangepaste opdracht gedefinieerd die moet worden uitgevoerd tijdens de implementatie van de toepassing voor statische inhoud.<br><br>Als u bijvoorbeeld een productie-build voor een hoek toepassing wilt configureren, maakt u een NPM-script `build-prod` met de naam voor uitvoeren `ng build --prod` en voert u `npm run build-prod` als de aangepaste opdracht in. Als dit veld leeg blijft, probeert de werk stroom de of-opdrachten uit te voeren `npm run build` `npm run build:Azure` .  |
| `api_build_command` | Hiermee wordt een aangepaste opdracht gedefinieerd die moet worden uitgevoerd tijdens de implementatie van de Azure Functions API-toepassing. |

## <a name="route-file-location"></a>Locatie van routebestand

U kunt de werk stroom aanpassen om te zoeken naar de [routes.js](routes.md) in een wille keurige map in uw opslag plaats. De volgende eigenschap kan worden gedefinieerd in de sectie van een taak `with` .

| Eigenschap            | Beschrijving |
|---------------------|-------------|
| `routes_location` | Hiermee definieert u de maplocatie waar de _routes.jsin_ het bestand wordt gevonden. Deze locatie is relatief ten opzichte van de hoofdmap van de opslag plaats. |

 Het is met name belang rijk dat u de locatie van uw _routes.jsop_ het bestand kunt vinden. Dit is vooral handig als uw front-end Framework-build-stap dit bestand niet standaard verplaatst naar de `output_location` .

## <a name="environment-variables"></a>Omgevingsvariabelen

U kunt omgevings variabelen instellen voor uw build via de `env` sectie van de configuratie van een taak.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: "upload"
          ###### Repository/Build Configurations
          app_location: "/"
          api_location: "api"
          output_location: "public"
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Pull-aanvragen in de pre-productieomgevingen controleren](review-publish-pull-requests.md)
