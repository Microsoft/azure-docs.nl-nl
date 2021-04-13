---
title: GitHub acties werk stromen voor statische Web Apps van Azure
description: Meer informatie over het gebruik van GitHub-opslag plaatsen voor het instellen van continue implementatie naar statische Web Apps van Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 4f1f432da33bded4fc0f04170673e5943dec5fb0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311325"
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
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

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
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

Via de instellingen die aan de eigenschap zijn gekoppeld `on` , kunt u definiëren welke vertakkingen een taak activeren en triggers instellen om te worden geactiveerd voor verschillende statussen van pull-aanvragen.

In dit voor beeld wordt een werk stroom gestart, omdat de _hoofd_ vertakking verandert. Wijzigingen die de werk stroom starten, bevatten push door voeren en pull-aanvragen openen voor de gekozen vertakking.

## <a name="jobs"></a>Taken

Voor elke gebeurtenis trigger moet een gebeurtenis-handler worden uitgevoerd. [Taken](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) bepalen wat er gebeurt wanneer een gebeurtenis wordt geactiveerd.

Er zijn twee beschik bare taken in het werk stroom bestand statisch Web Apps.

| Naam                     | Beschrijving                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | Wordt uitgevoerd wanneer u een push uitvoert of een pull-aanvraag opent voor de vertakking die in de eigenschap wordt vermeld `on` .          |
| `close_pull_request_job` | Wordt alleen uitgevoerd wanneer u een pull-aanvraag sluit, waardoor de faserings omgeving die is gemaakt op basis van pull-aanvragen, wordt verwijderd. |

## <a name="steps"></a>Stappen

Stappen zijn opeenvolgende taken voor een taak. Met een stap worden acties uitgevoerd, zoals het installeren van afhankelijkheden, het uitvoeren van tests en het implementeren van uw toepassing naar productie.

Een werk stroom bestand definieert de volgende stappen.

| Taak                      | Stappen                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | <ol><li>Hiermee wordt de opslag plaats in de omgeving van de actie gecontroleerd.<li>Bouwt en implementeert de opslag plaats naar statische Azure-Web Apps.</ol> |
| `close_pull_request_job` | <ol><li>Hiermee wordt een statische Web Apps van Azure geïnformeerd dat een pull-aanvraag is gesloten.</ol>                                                        |

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

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

De `repo_token` `action` waarden, en `azure_static_web_apps_api_token` worden door Azure static web apps voor u ingesteld, niet hand matig worden gewijzigd.

## <a name="custom-build-commands"></a>Aangepaste build-opdrachten

U kunt een nauw keurige controle hebben over de opdrachten die tijdens een implementatie worden uitgevoerd. De volgende opdrachten kunnen worden gedefinieerd in de sectie van een taak `with` .

De implementatie aanroept altijd `npm install` vóór een aangepaste opdracht.

| Opdracht             | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `app_build_command` | Hiermee wordt een aangepaste opdracht gedefinieerd die moet worden uitgevoerd tijdens de implementatie van de toepassing voor statische inhoud.<br><br>Als u bijvoorbeeld een productie-build voor een hoek toepassing wilt configureren, maakt u een NPM-script `build-prod` met de naam voor uitvoeren `ng build --prod` en voert u `npm run build-prod` als de aangepaste opdracht in. Als dit veld leeg blijft, probeert de werk stroom de of-opdrachten uit te voeren `npm run build` `npm run build:azure` . |
| `api_build_command` | Hiermee wordt een aangepaste opdracht gedefinieerd die moet worden uitgevoerd tijdens de implementatie van de Azure Functions API-toepassing.                                                                                                                                                                                                                                                                                                  |

## <a name="skip-app-build"></a>App-build overs Laan

Als u volledige controle nodig hebt over de manier waarop de front-end-toepassing is gebouwd, kunt u aangepaste build-stappen toevoegen in uw werk stroom. Vervolgens kunt u de statische-Web Apps actie configureren om het automatische buildproces over te slaan en alleen de app te implementeren die in een vorige stap is gemaakt.

Als u het maken van de app wilt overs Laan, stelt `skip_app_build` u in op `true` `app_location` de locatie van de map die u wilt implementeren.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| Eigenschap         | Beschrijving                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | Stel de waarde in op om `true` het bouwen van de front-end-app over te slaan. |

> [!NOTE]
> U kunt alleen de build voor de front-end-app overs Laan. Als uw app een API heeft, wordt deze nog steeds gemaakt door de statische Web Apps GitHub-actie.

## <a name="route-file-location"></a>Locatie van routebestand

U kunt de werk stroom aanpassen om te zoeken naar de [staticwebapp.config.js](routes.md) in een wille keurige map in uw opslag plaats. De volgende eigenschap kan worden gedefinieerd in de sectie van een taak `with` .

| Eigenschap          | Beschrijving                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `routes_location` | Hiermee definieert u de maplocatie waar de _staticwebapp.config.jsin_ het bestand wordt gevonden. Deze locatie is relatief ten opzichte van de hoofdmap van de opslag plaats. |

Het is met name belang rijk dat u de locatie van uw _staticwebapp.config.jsop_ het bestand kunt vinden. Dit is vooral handig als uw front-end Framework-build-stap dit bestand niet standaard verplaatst naar de `output_location` .

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
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Monorepo-ondersteuning

Een monorepo is een opslag plaats met code voor meer dan één toepassing. Standaard worden in een statisch Web Apps werk stroom bestand alle bestanden in een opslag plaats bijgehouden, maar u kunt dit aanpassen om een enkele app te richten. Daarom heeft elke statische app voor monorepos een eigen configuratie bestand dat naast elkaar wordt weer in de map _. github/werk stromen_ van de opslag plaats.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Als u een werk stroom bestand wilt richten op één app, geeft u de paden op in de `push` `pull_request` secties en.

In het volgende voor beeld ziet u hoe u een `paths` knoop punt toevoegt aan de- `push` en- `pull_request` secties van een bestand met de naam _Azure-static-web-apps-Purple-pond. yml_.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

In dit geval activeren alleen de volgende bestanden een nieuwe build:

- Alle bestanden in de map _app1_
- Alle bestanden in de map _api1_
- Wijzigingen in het werk stroom bestand _Azure-static-web-apps-Purple-pond. yml_ van de app

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Pull-aanvragen in de pre-productieomgevingen controleren](review-publish-pull-requests.md)
