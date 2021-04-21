---
title: GitHub Actions werkstromen voor Azure Static Web Apps
description: Meer informatie over het gebruik van GitHub-opslagplaatsen om continue implementatie in te stellen voor Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: b20a1670c13a272ed48088567a205d854ac99179
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791243"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>GitHub Actions werkstromen voor Azure Static Web Apps Preview

Wanneer u een nieuwe resource Azure Static Web Apps, genereert Azure een GitHub Actions om de continue implementatie van de app te beheren. De werkstroom wordt aangestuurd door een YAML-bestand. In dit artikel worden de structuur en opties van het werkstroombestand beschreven.

Implementaties worden geïnitieerd door [triggers](#triggers), die [taken uitvoeren](#jobs) die zijn gedefinieerd door afzonderlijke [stappen](#steps).

> [!NOTE]
> Azure Static Web Apps biedt ook ondersteuning voor Azure DevOps. Zie [Publiceren met Azure DevOps](publish-devops.md) voor informatie over het instellen van een pijplijn.

## <a name="file-location"></a>Bestandslocatie

Wanneer u uw GitHub-opslagplaats aan Azure Static Web Apps, wordt er een werkstroombestand toegevoegd aan de opslagplaats.

Volg deze stappen om het gegenereerde werkstroombestand weer te geven.

1. Open de opslagplaats van de app op GitHub.
1. Klik op het tabblad _Code_ op de `.github/workflows` map .
1. Klik op het bestand met een naam die eruitziet als `azure-static-web-apps-<RANDOM_NAME>.yml` .

Het YAML-bestand in uw opslagplaats is vergelijkbaar met het volgende voorbeeld:

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

Een [GitHub Actions-trigger](https://help.github.com/actions/reference/events-that-trigger-workflows) waarschuwt een GitHub Actions om een taak uit te voeren op basis van gebeurtenistriggers. Triggers worden weergegeven met behulp van de `on` eigenschap in het werkstroombestand.

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

Via instellingen die aan de eigenschap zijn gekoppeld, kunt u definiëren welke vertakkingen een taak activeren en triggers instellen om te `on` activeren voor verschillende pull-aanvraaginstellingen.

In dit voorbeeld wordt een werkstroom gestart wanneer de _hoofdvertakking_ verandert. Wijzigingen die de werkstroom starten, omvatten het pushen van commits en het openen van pull-aanvragen voor de gekozen vertakking.

## <a name="jobs"></a>Taken

Elke gebeurtenistrigger vereist een gebeurtenis-handler. [Taken](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) definiëren wat er gebeurt wanneer een gebeurtenis wordt geactiveerd.

In het Static Web Apps werkstroombestand zijn er twee beschikbare taken.

| Naam                     | Beschrijving                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | Wordt uitgevoerd wanneer u commits pusht of een pull-aanvraag opent voor de vertakking die wordt vermeld in de `on` eigenschap .          |
| `close_pull_request_job` | Wordt ALLEEN uitgevoerd wanneer u een pull-aanvraag sluit, waardoor de faseringsomgeving wordt verwijderd die is gemaakt van pull-aanvragen. |

## <a name="steps"></a>Stappen

Stappen zijn sequentiële taken voor een job. Een stap voert acties uit zoals het installeren van afhankelijkheden, het uitvoeren van tests en het implementeren van uw toepassing in productie.

Een werkstroombestand definieert de volgende stappen.

| Taak                      | Stappen                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | <ol><li>Controleert de opslagplaats in de omgeving van de actie.<li>De opslagplaats wordt gebouwd en geïmplementeerd in Azure Static Web Apps.</ol> |
| `close_pull_request_job` | <ol><li>Waarschuwt Azure Static Web Apps dat een pull-aanvraag is gesloten.</ol>                                                        |

## <a name="build-and-deploy"></a>Bouwen en implementeren

De stap met de `Build and Deploy` naam wordt gebouwd en geïmplementeerd in Azure Static Web Apps-exemplaar. In de `with` sectie kunt u de volgende waarden voor uw implementatie aanpassen.

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

De waarden , en worden voor `repo_token` u ingesteld door Azure Static Web Apps niet handmatig worden `action` `azure_static_web_apps_api_token` gewijzigd.

## <a name="custom-build-commands"></a>Aangepaste buildopdrachten

U kunt een fijnkeurige controle hebben over welke opdrachten tijdens een implementatie worden uitgevoerd. De volgende opdrachten kunnen worden gedefinieerd onder de sectie van een `with` taak.

De implementatie roept altijd aan `npm install` vóór een aangepaste opdracht.

| Opdracht             | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `app_build_command` | Hiermee definieert u een aangepaste opdracht die moet worden uitgevoerd tijdens de implementatie van de toepassing voor statische inhoud.<br><br>Als u bijvoorbeeld een productie-build voor een Angular-toepassing wilt configureren, maakt u een NPM-script met de naam om uit te voeren en in te voeren `build-prod` `ng build --prod` als de aangepaste `npm run build-prod` opdracht. Als dit leeg is, probeert de werkstroom de `npm run build` opdrachten of uit te `npm run build:azure` voeren. |
| `api_build_command` | Hiermee definieert u een aangepaste opdracht die moet worden uitgevoerd tijdens de implementatie van Azure Functions API-toepassing.                                                                                                                                                                                                                                                                                                  |

## <a name="skip-app-build"></a>App-build overslaan

Als u volledige controle nodig hebt over hoe uw front-endtoepassing wordt gebouwd, kunt u aangepaste buildstappen toevoegen aan uw werkstroom. Vervolgens kunt u de Static Web Apps configureren om het automatische bouwproces te omzeilen en de app die in een vorige stap is gebouwd, te implementeren.

Als u het bouwen van de app wilt overslaan, stelt `skip_app_build` u in op en op de locatie van de map die u wilt `true` `app_location` implementeren.

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
| `skip_app_build` | Stel de waarde in op `true` om het bouwen van de front-end-app over te slaan. |

> [!NOTE]
> U kunt de build voor de front-end-app alleen overslaan. Als uw app een API heeft, wordt deze nog steeds gebouwd door de Static Web Apps GitHub Action.

## <a name="route-file-location"></a>Locatie van routebestand

U kunt de werkstroom aanpassen om te zoeken [ naar deroutes.jsin](routes.md) elke map in uw opslagplaats. De volgende eigenschap kan worden gedefinieerd in de sectie van een `with` taak.

| Eigenschap          | Beschrijving                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `routes_location` | Hiermee definieert u de _maplocatie waarroutes.jsbestand_ is gevonden. Deze locatie is relatief ten opzichte van de hoofdmap van de opslagplaats. |

Expliciet zijn over de locatie van uw _routes.jsbestand_ is met name belangrijk als dit bestand niet standaard wordt verplaatst in de buildstap van uw front-end-framework. `output_location`

> [!IMPORTANT]
> Functionaliteit die is gedefinieerd in _routes.jsbestand_ is nu afgeschaft. Zie het Azure Static Web Apps [voor meer](./configuration.md) informatie overstaticwebapp.config.js _op_.

## <a name="environment-variables"></a>Omgevingsvariabelen

U kunt omgevingsvariabelen voor uw build instellen via de `env` sectie van de configuratie van een taak.

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

## <a name="monorepo-support"></a>Ondersteuning voor Monorepo

Een monorepo is een opslagplaats die code bevat voor meer dan één toepassing. Een werkstroombestand Static Web Apps standaard alle bestanden in een opslagplaats bij, maar u kunt dit aanpassen om één app als doel te gebruiken. Daarom heeft elke statische app voor monorepos een eigen configuratiebestand dat naast elkaar in de _map .github/workflows_ van de opslagplaats staat.

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

Als u een werkstroombestand wilt richten op één app, geeft u paden op in de `push` `pull_request` secties en .

In het volgende voorbeeld wordt gedemonstreerd hoe u een knooppunt toevoegt aan de secties en van een bestand met de naam `paths` `push` `pull_request` _azure-static-web-apps-purple-pond.yml._

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

In dit geval activeren alleen wijzigingen in de volgende bestanden een nieuwe build:

- Alle bestanden in de _map app1_
- Alle bestanden in de _map api1_
- Wijzigingen in het werkstroombestand _azure-static-web-apps-purple-pond.yml van_ de app

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Pull-aanvragen in de pre-productieomgevingen controleren](review-publish-pull-requests.md)
