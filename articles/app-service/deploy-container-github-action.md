---
title: Aangepaste container-CI/CD van GitHub-acties
description: Meer informatie over het gebruik van GitHub-acties voor het implementeren van uw aangepaste Linux-container voor App Service van een CI/CD-pijp lijn.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: 4f5deb33218c336da7a477b4f39cd45f7386debf
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97604971"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Een aangepaste container implementeren op App Service met behulp van GitHub-acties

[Github-acties](https://docs.github.com/en/free-pro-team@latest/actions) bieden u de flexibiliteit om een geautomatiseerde werk stroom voor het ontwikkelen van software te bouwen. Met de [actie Azure Web Deploy](https://github.com/Azure/webapps-deploy)kunt u uw werk stroom automatiseren om aangepaste containers te implementeren op [app service](overview.md) met behulp van github-acties.

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en para meters in de werk stroom.

Voor een Azure App Service container werk stroom heeft het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Haal een service-principal of een publicatie profiel op. <br /> 2. Maak een GitHub-opslagplaats. |
|**Ontwikkelen** | 1. Maak de omgeving. <br /> 2. bouw de container installatie kopie. |
|**Implementeren** | 1. Implementeer de container installatie kopie. |

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account. U moet code in een GitHub-opslag plaats hebben om te implementeren in Azure App Service. 
- Een werk container register en Azure App Service-app voor containers. In dit voor beeld wordt Azure Container Registry gebruikt. Zorg ervoor dat u de volledige implementatie voor het Azure App Service van containers hebt voltooid. In tegens telling tot reguliere web apps hebben web apps for containers geen standaard landings pagina. Publiceer de container voor een werkend voor beeld.
    - [Meer informatie over het maken van een container Node.js toepassing met behulp van docker, het pushen van de container installatie kopie naar een REGI ster en het implementeren van de installatie kopie naar Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

De aanbevolen manier om te verifiëren met Azure-app Services voor GitHub-acties is met een publicatie profiel. U kunt ook verifiëren met een Service-Principal, maar voor het proces zijn meer stappen vereist. 

Sla de referentie of service-principal voor het publicatie profiel op als een [github-geheim](https://docs.github.com/en/free-pro-team@latest/actions/reference/encrypted-secrets) voor verificatie bij Azure. U hebt toegang tot het geheim in uw werk stroom. 

# <a name="publish-profile"></a>[Profiel publiceren](#tab/publish-profile)

Een publicatie profiel is een referentie op app-niveau. Stel uw publicatie profiel in als een GitHub-geheim. 

1. Ga naar de app-service in de Azure Portal. 

1. Selecteer op de pagina **overzicht** de optie **publicatie profiel ophalen**.

    > [!NOTE]
    > Vanaf 2020 oktober moeten voor Linux-web-apps de app-instelling `WEBSITE_WEBDEPLOY_USE_SCM` worden ingesteld op `true` **voordat het bestand wordt gedownload**. Deze vereiste wordt in de toekomst verwijderd. Zie [een app service-app configureren in de Azure Portal](/azure/app-service/configure-common)voor meer informatie over het configureren van algemene Web-app-instellingen.  

1. Sla het gedownloade bestand op. U gebruikt de inhoud van het bestand om een GitHub-geheim te maken.

# <a name="service-principal"></a>[Service-principal](#tab/service-principal)

U kunt een [service-principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Vervang in het voor beeld de tijdelijke aanduidingen door de abonnements-ID, naam van de resource groep en de naam van de app. De uitvoer is een JSON-object met de roltoewijzings referenties die toegang bieden tot uw App Service-app. Kopieer dit JSON-object voor later gebruik.

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
> Het is altijd een goed idee om minimale toegang te verlenen. Het bereik in het vorige voor beeld is beperkt tot de specifieke App Service-app en niet de hele resource groep.

---
## <a name="configure-the-github-secret-for-authentication"></a>Het GitHub-geheim voor verificatie configureren

# <a name="publish-profile"></a>[Profiel publiceren](#tab/publish-profile)

In [github](https://github.com/)gaat u naar uw opslag plaats, selecteert u **instellingen > geheimen > een nieuw geheim toe te voegen**.

Als u [referenties op app-niveau](#generate-deployment-credentials)wilt gebruiken, plakt u de inhoud van het gedownloade bestand met het publicatie profiel in het veld waarde van het geheim. Geef het geheim een naam `AZURE_WEBAPP_PUBLISH_PROFILE` .

Wanneer u uw GitHub-werk stroom configureert, gebruikt u de `AZURE_WEBAPP_PUBLISH_PROFILE` in de actie Azure-web-app implementeren. Bijvoorbeeld:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Service-principal](#tab/service-principal)

In [github](https://github.com/)gaat u naar uw opslag plaats, selecteert u **instellingen > geheimen > een nieuw geheim toe te voegen**.

Als u [referenties op gebruikers niveau](#generate-deployment-credentials)wilt gebruiken, plakt u de volledige JSON-uitvoer van de Azure cli-opdracht in het veld waarde van het geheim. Geef het geheim de naam zoals `AZURE_CREDENTIALS` .

Wanneer u het werkstroombestand later configureert, gebruikt u het geheim voor de invoer `creds` van de Azure-aanmeldingsactie. Bijvoorbeeld:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>GitHub-geheimen voor uw REGI ster configureren

Geef geheimen op die moeten worden gebruikt met de actie aanmelden bij docker. In het voor beeld in dit document wordt Azure Container Registry gebruikt voor het container register. 

1. Ga in de Azure Portal of docker naar uw container en kopieer de gebruikers naam en het wacht woord. U vindt de Azure container Registry gebruikers naam en het wacht woord in de Azure portal onder **instellingen**  >  **toegangs sleutels** voor uw REGI ster. 

2. Definieer een nieuw geheim voor de register gebruikersnaam met de naam `REGISTRY_USERNAME` . 

3. Definieer een nieuw geheim voor het register wachtwoord met de naam `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>De container installatie kopie bouwen

In het volgende voor beeld wordt een deel van de werk stroom weer gegeven dat een Node.JS docker-installatie kopie bouwt. Gebruik [docker login](https://github.com/azure/docker-login) om u aan te melden bij een persoonlijk container register. In dit voor beeld wordt Azure Container Registry gebruikt, maar dezelfde actie werkt voor andere registers. 


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

U kunt [docker-aanmelding](https://github.com/azure/docker-login) ook gebruiken om op hetzelfde moment aan te melden bij meerdere container registers. Dit voor beeld bevat twee nieuwe GitHub-geheimen voor verificatie met docker.io. In het voor beeld wordt ervan uitgegaan dat er een Dockerfile is op het hoofd niveau van het REGI ster. 

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

## <a name="deploy-to-an-app-service-container"></a>Implementeren in een App Service-container

Gebruik de actie om uw installatie kopie te implementeren in een aangepaste container in App Service `azure/webapps-deploy@v2` . Deze actie heeft zeven para meters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **app-naam** | Lang De naam van de App Service-app | 
| **publicatie profiel** | Beschrijving Is van toepassing op Web Apps (Windows en Linux) en web app-containers (Linux). Het scenario met meerdere containers wordt niet ondersteund. Inhoud van het profiel publiceren ( \* . publishsettings) met Web Deploy-geheimen | 
| **sleuf naam** | Beschrijving Voer een bestaande sleuf in, behalve de productie sleuf |
| **package** | Beschrijving Is alleen van toepassing op de web-app: het pad naar het pakket of de map. \*. zip, \* . War, \* . jar of een map die moet worden geïmplementeerd |
| **installatie kopieën** | Lang Is alleen van toepassing op Web app-containers: Geef de naam op van de volledig gekwalificeerde container installatie kopie (n). Bijvoorbeeld ' myregistry.azurecr.io/nginx:latest ' of ' python: 3.7.2-Alpine/'. Voor een app met meerdere containers kunnen namen van meerdere container installatie kopieën worden gegeven (gescheiden door meerdere regels) |
| **configuratie-bestand** | Beschrijving Is alleen van toepassing op Web app-containers: pad naar het Docker-Compose-bestand. Moet een volledig gekwalificeerd pad of relatief ten opzichte van de standaard werkmap zijn. Vereist voor apps met meerdere containers. |
| **opstart opdracht** | Beschrijving Voer de opstart opdracht in. Voor ex. DotNet-run-of DotNet-filename.dll |

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

U vindt onze set acties die zijn gegroepeerd in verschillende opslag plaatsen op GitHub, elk met documentatie en voor beelden die u kunnen helpen bij het gebruik van GitHub voor CI/CD en uw apps te implementeren in Azure.

- [Werk stromen voor acties die moeten worden geïmplementeerd in azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-aanmelding](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Aanmelden/afmelden bij docker](https://github.com/Azure/docker-login)

- [Gebeurtenissen die workflows activeren](https://docs.github.com/en/free-pro-team@latest/actions/reference/events-that-trigger-workflows)

- [K8s implementeren](https://github.com/Azure/k8s-deploy)

- [Starter-werk stromen](https://github.com/actions/starter-workflows)
