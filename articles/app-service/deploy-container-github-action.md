---
title: Aangepaste container CI/CD van GitHub Actions
description: Informatie over het gebruik van GitHub Actions om uw aangepaste Linux-container te App Service vanuit een CI/CD-pijplijn.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: bf9fba9142de82c6e8518198d54b5e74f1807838
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789425"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Een aangepaste container implementeren in App Service met behulp van GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) biedt u de flexibiliteit om een geautomatiseerde werkstroom voor softwareontwikkeling te bouwen. Met de [Azure Web Deploy-actie](https://github.com/Azure/webapps-deploy)kunt u uw werkstroom automatiseren om aangepaste containers te implementeren in [App Service](overview.md) met behulp GitHub Actions.

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters in de werkstroom.

Voor een Azure App Service containerwerkstroom heeft het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Haal een service-principal op of publiceer een profiel. <br /> 2. Maak een GitHub-opslagplaats. |
|**Build** | 1. Maak de omgeving. <br /> 2. Bouw de containerafbeelding. |
|**Implementeren** | 1. De containerafbeelding implementeren. |

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account. U moet code in een GitHub-opslagplaats hebben om te implementeren in Azure App Service. 
- Een werkend containerregister en Azure App Service app voor containers. In dit voorbeeld wordt Azure Container Registry. Zorg ervoor dat u de volledige implementatie voor Azure App Service containers voltooit. In tegenstelling tot gewone web-apps hebben web-apps voor containers geen standaardlandingspagina. Publiceer de container om een werkend voorbeeld te hebben.
    - [Informatie over het maken van een containertoepassing Node.js docker, het pushen van de container-afbeelding naar een register en het implementeren van de Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

De aanbevolen manier om te verifiëren met Azure-app Services voor GitHub Actions is met een publicatieprofiel. U kunt ook verifiëren met een service-principal, maar voor het proces zijn meer stappen vereist. 

Sla uw publicatieprofielreferentie of service-principal op als [een GitHub-geheim om](https://docs.github.com/en/actions/reference/encrypted-secrets) te verifiëren bij Azure. U hebt toegang tot het geheim in uw werkstroom. 

# <a name="publish-profile"></a>[Profiel publiceren](#tab/publish-profile)

Een publicatieprofiel is een referentie op app-niveau. Stel uw publicatieprofiel in als een GitHub-geheim. 

1. Ga naar uw app-service in de Azure Portal. 

1. Selecteer op **de pagina** Overzicht de optie **Publicatieprofiel krijgen.**

    > [!NOTE]
    > Vanaf oktober 2020 moet voor Linux-web-apps de app-instelling zijn `WEBSITE_WEBDEPLOY_USE_SCM` ingesteld op voordat het bestand wordt `true` **gedownload.** Deze vereiste wordt in de toekomst verwijderd. Zie [Een App Service-app configureren in de Azure Portal](./configure-common.md)voor meer informatie over het configureren van algemene web-app-instellingen.  

1. Sla het gedownloade bestand op. U gebruikt de inhoud van het bestand om een GitHub-geheim te maken.

# <a name="service-principal"></a>[Service-principal](#tab/service-principal)

U kunt een [service-principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Vervang in het voorbeeld de tijdelijke aanduidingen door uw abonnements-id, resourcegroepnaam en app-naam. De uitvoer is een JSON-object met de referenties voor roltoewijzing die toegang bieden tot App Service app. Kopieer dit JSON-object voor later gebruik.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Het is altijd een goed idee om minimale toegang te verlenen. Het bereik in het vorige voorbeeld is beperkt tot de specifieke App Service app en niet de hele resourcegroep.

---
## <a name="configure-the-github-secret-for-authentication"></a>Het GitHub-geheim voor verificatie configureren

# <a name="publish-profile"></a>[Profiel publiceren](#tab/publish-profile)

Blader [in GitHub](https://github.com/)door uw opslagplaats, selecteer **Instellingen > Geheimen > Een nieuw geheim toevoegen.**

Als u [referenties op app-niveau wilt](#generate-deployment-credentials)gebruiken, plakt u de inhoud van het gedownloade publicatieprofielbestand in het waardeveld van het geheim. Noem het geheim `AZURE_WEBAPP_PUBLISH_PROFILE` .

Wanneer u uw GitHub-werkstroom configureert, gebruikt u `AZURE_WEBAPP_PUBLISH_PROFILE` de in de actie Azure Web App implementeren. Bijvoorbeeld:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Service-principal](#tab/service-principal)

Blader [in GitHub](https://github.com/)door uw opslagplaats, selecteer **Instellingen > Geheimen > Een nieuw geheim toevoegen.**

Als u [referenties op gebruikersniveau wilt gebruiken,](#generate-deployment-credentials)plakt u de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. Geef het geheim de naam, zoals `AZURE_CREDENTIALS` .

Wanneer u het werkstroombestand later configureert, gebruikt u het geheim voor de invoer `creds` van de Azure-aanmeldingsactie. Bijvoorbeeld:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>GitHub-geheimen configureren voor uw register

Definieer geheimen die moeten worden gebruikt met de actie Docker-aanmelding. In het voorbeeld in dit document wordt Azure Container Registry gebruikt voor het containerregister. 

1. Ga naar uw container in de Azure Portal of Docker en kopieer de gebruikersnaam en het wachtwoord. U vindt de Azure Container Registry en het wachtwoord in de Azure Portal onder **Instellingen** Toegangssleutels  >   voor uw register. 

2. Definieer een nieuw geheim voor de register-gebruikersnaam met de naam `REGISTRY_USERNAME` . 

3. Definieer een nieuw geheim voor het registerwachtwoord met de naam `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>De containerafbeelding bouwen

In het volgende voorbeeld wordt een deel van de werkstroom voor het bouwen van een Node.JS Docker-afbeelding. Gebruik [Docker-aanmelding om](https://github.com/azure/docker-login) u aan te melden bij een privécontainerregister. In dit voorbeeld wordt Azure Container Registry maar dezelfde actie werkt voor andere registers. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

U kunt [docker-aanmelding ook gebruiken om](https://github.com/azure/docker-login) u aan te melden bij meerdere containerregisters tegelijk. Dit voorbeeld bevat twee nieuwe GitHub-geheimen voor verificatie met docker.io. In het voorbeeld wordt ervan uitgenomen dat er een Dockerfile is op het hoofdniveau van het register. 

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Implementeren naar een App Service container

Als u uw afbeelding wilt implementeren in een aangepaste container in App Service, gebruikt u de `azure/webapps-deploy@v2` actie . Deze actie heeft zeven parameters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **app-name** | (vereist) Naam van de App Service app | 
| **publish-profile** | (Optioneel) Is van toepassing Web Apps (Windows en Linux) en Web App Containers (Linux). Scenario met meerdere containers wordt niet ondersteund. Bestandsinhoud van \* profiel (.publishsettings) publiceren met Web Deploy-geheimen | 
| **sleufnaam** | (Optioneel) Een andere bestaande sleuf dan de productiesleuf invoeren |
| **package** | (Optioneel) Alleen van toepassing op web-app: pad naar pakket of map. \*.zip, \* .war, \* .jar of een map die moet worden geïmplementeerd |
| **Afbeeldingen** | (vereist) Alleen van toepassing op web-app-containers: geef de volledig gekwalificeerde containernaam(en) op. Bijvoorbeeld 'myregistry.azurecr.io/nginx:latest' of 'python:3.7.2-alpine/'. Voor een app met meerdere containers kunnen meerdere namen van containerafbeeldingen worden opgegeven (gescheiden door meerdere lijnen) |
| **configuratiebestand** | (Optioneel) Alleen van toepassing op web-app-containers: pad van Docker-Compose bestand. Moet een volledig gekwalificeerd pad zijn of relatief ten opzichte van de standaarddirectory. Vereist voor apps met meerdere containers. |
| **startup-command** | (Optioneel) Voer de opstartopdracht in. Voor bijvoorbeeld dotnet-run of dotnet-filename.dll |

# <a name="publish-profile"></a>[Profiel publiceren](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Service-principal](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Volgende stappen

U vindt onze set acties gegroepeerd in verschillende opslagplaatsen op GitHub, elk met documentatie en voorbeelden om u te helpen GitHub voor CI/CD te gebruiken en uw apps te implementeren in Azure.

- [Werkstromen voor acties die moeten worden geïmplementeerd in Azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-aanmelding](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker-aanmelding/-aanmelding](https://github.com/Azure/docker-login)

- [Gebeurtenissen die workflows activeren](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s-implementatie](https://github.com/Azure/k8s-deploy)

- [Starterwerkstromen](https://github.com/actions/starter-workflows)