---
title: CI/CD configureren met GitHub-acties
description: Leer hoe u uw code kunt implementeren in Azure App Service van een CI/CD-pijp lijn met GitHub-acties. Pas de bouw taken aan en Voer complexe implementaties uit.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 16a21acabfd199ba16068e507919b564f01a76d5
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763907"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Implementeren naar App Service met behulp van GitHub-acties

Aan de slag met [github-acties](https://help.github.com/en/articles/about-github-actions) om uw werk stroom te automatiseren en te implementeren in [Azure app service](overview.md) van github. 

## <a name="prerequisites"></a>Vereisten 

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account.  
- Een werkende Azure App Service-app. 
    - .NET: [een ASP.net core-web-app maken in azure](quickstart-dotnetcore.md)
    - ASP.NET: [een ASP.NET Framework-web-app maken in azure](quickstart-dotnet-framework.md)
    - Java script: [een Node.js-web-app maken in azure app service](quickstart-nodejs.md)  
    - Java: [een Java-app maken op Azure app service](quickstart-java.md)
    - Python: [een python-app maken in azure app service](quickstart-python.md)

## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Het bestand heeft drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een service-principal of publicatie profiel. <br /> 2. Maak een GitHub-opslagplaats. |
|**Ontwikkelen** | 1. Stel de omgeving in. <br /> 2. bouw de web-app. |
|**Implementeren** | 1. Implementeer de web-app. |

## <a name="use-the-deployment-center"></a>Het implementatie centrum gebruiken

U kunt snel aan de slag met GitHub-acties met behulp van het App Service Deployment Center. Hiermee wordt automatisch een werk stroom bestand gegenereerd op basis van uw toepassings stack en dit door voeren in uw GitHub-opslag plaats in de juiste map.

1. Navigeer naar uw webapp in het Azure Portal
1. Klik aan de linkerkant op **implementatie centrum**
1. Selecteer bij **continue implementatie (CI/cd)** **github**
1. Selecteer vervolgens **github-acties**
1. Gebruik de vervolg keuzelijsten om uw GitHub-opslag plaats, vertakking en toepassings stack te selecteren
    - Als de geselecteerde vertakking is beveiligd, kunt u toch door gaan met het toevoegen van het werk stroom bestand. Controleer de branch-beveiliging voordat u doorgaat.
1. In het laatste scherm kunt u uw selecties bekijken en een voor beeld bekijken van het werk stroom bestand dat wordt doorgevoerd naar de opslag plaats. Als de selecties juist zijn, klikt u op **volt ooien**

Hiermee wordt het werk stroom bestand door doorgevoerd naar de opslag plaats. De werk stroom voor het maken en implementeren van uw app wordt onmiddellijk gestart.

## <a name="set-up-a-workflow-manually"></a>Een werk stroom hand matig instellen

U kunt ook een werk stroom implementeren zonder het implementatie centrum te gebruiken. Hiervoor moet u eerst implementatie referenties genereren. 

## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

De aanbevolen manier om te verifiëren met Azure-app Services voor GitHub-acties is met een publicatie profiel. U kunt ook verifiëren met een Service-Principal, maar voor het proces zijn meer stappen vereist. 

Sla de referentie of service-principal voor het publicatie profiel op als een [github-geheim](https://docs.github.com/en/actions/reference/encrypted-secrets) voor verificatie bij Azure. U hebt toegang tot het geheim in uw werk stroom. 

# <a name="publish-profile"></a>[Profiel publiceren](#tab/applevel)

Een publicatie profiel is een referentie op app-niveau. Stel uw publicatie profiel in als een GitHub-geheim. 

1. Ga naar de app-service in de Azure Portal. 

1. Selecteer op de pagina **overzicht** de optie **publicatie profiel ophalen**.

1. Sla het gedownloade bestand op. U gebruikt de inhoud van het bestand om een GitHub-geheim te maken.

> [!NOTE]
> Vanaf 2020 oktober moeten voor Linux-web-apps de app-instelling zijn `WEBSITE_WEBDEPLOY_USE_SCM` ingesteld op `true` **voordat het publicatie profiel** kan worden gedownload. Deze vereiste wordt in de toekomst verwijderd.

# <a name="service-principal"></a>[Service-principal](#tab/userlevel)

U kunt een [service-principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

In het bovenstaande voor beeld vervangt u de tijdelijke aanduidingen door de abonnements-ID, naam van de resource groep en de naam van de app. De uitvoer is een JSON-object met de roltoewijzingsreferenties die toegang bieden tot uw App Service-app, vergelijkbaar met hieronder. Kopieer dit JSON-object voor later gebruik.

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

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren


# <a name="publish-profile"></a>[Profiel publiceren](#tab/applevel)

In [github](https://github.com/)gaat u naar uw opslag plaats, selecteert u **instellingen > geheimen > een nieuw geheim toe te voegen**.

Als u [referenties op app-niveau](#generate-deployment-credentials)wilt gebruiken, plakt u de inhoud van het gedownloade bestand met het publicatie profiel in het veld waarde van het geheim. Geef het geheim een naam `AZURE_WEBAPP_PUBLISH_PROFILE` .

Wanneer u uw GitHub-werk stroom configureert, gebruikt u de `AZURE_WEBAPP_PUBLISH_PROFILE` in de actie Azure-web-app implementeren. Bijvoorbeeld:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Service-principal](#tab/userlevel)

In [github](https://github.com/)gaat u naar uw opslag plaats, selecteert u **instellingen > geheimen > een nieuw geheim toe te voegen**.

Als u [referenties op gebruikers niveau](#generate-deployment-credentials)wilt gebruiken, plakt u de volledige JSON-uitvoer van de Azure cli-opdracht in het veld waarde van het geheim. Geef het geheim de naam `AZURE_CREDENTIALS`.

Wanneer u het werkstroombestand later configureert, gebruikt u het geheim voor de invoer `creds` van de Azure-aanmeldingsactie. Bijvoorbeeld:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>De omgeving instellen

Het instellen van de omgeving kan worden uitgevoerd met behulp van een van de installatie acties.

|**Taal**  |**Installatie actie**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

In de volgende voor beelden ziet u hoe u de omgeving instelt voor de verschillende ondersteunde talen:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>De web-app bouwen

Het proces van het bouwen van een web-app en het implementeren van Azure App Service wijzigingen, afhankelijk van de taal. 

In de volgende voor beelden ziet u het deel van de werk stroom dat de web-app bouwt in verschillende ondersteunde talen.

Voor alle talen kunt u de hoofdmap van de web-app instellen met `working-directory` . 

**.NET**

De omgevings variabele `AZURE_WEBAPP_PACKAGE_PATH` stelt het pad naar uw web-app-project in. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

U kunt NuGet-afhankelijkheden herstellen en MSBuild uitvoeren met `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Voor Node.js, kunt u de `working-directory` Directory NPM instellen of wijzigen in `pushd` . 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>Implementeren naar App Service

Als u uw code wilt implementeren in een App Service-app, gebruikt u de `azure/webapps-deploy@v2` actie. Deze actie heeft vier para meters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **app-naam** | Lang De naam van de App Service-app | 
| **publicatie profiel** | Beschrijving Inhoud van profiel bestand publiceren met Web Deploy-geheimen |
| **package** | Beschrijving Het pad naar het pakket of de map. Het pad kan *. zip, *. War, *. jar of een te implementeren map bevatten |
| **sleuf naam** | Beschrijving Voer een bestaande sleuf in, behalve de productie [sleuf](deploy-staging-slots.md) |


# <a name="publish-profile"></a>[Profiel publiceren](#tab/applevel)

### <a name="net-core"></a>.NET Core

Een .NET core-app bouwen en implementeren in azure met behulp van een Azure Publish-profiel. De `publish-profile` invoer verwijst naar het `AZURE_WEBAPP_PUBLISH_PROFILE` geheim dat u eerder hebt gemaakt.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

Bouw en implementeer een ASP.NET MVC-app die gebruikmaakt van NuGet en `publish-profile` voor authenticatie. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@master  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Bouw en implementeer een Java-lente-app in azure met behulp van een Azure Publish-profiel. De `publish-profile` invoer verwijst naar het `AZURE_WEBAPP_PUBLISH_PROFILE` geheim dat u eerder hebt gemaakt.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Als u een `war` in plaats van een wilt implementeren `jar` , wijzigt u de `package` waarde. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>Javascript 

Bouw en implementeer een Node.js-app in azure met behulp van het publicatie Profiel van de app. De `publish-profile` invoer verwijst naar het `AZURE_WEBAPP_PUBLISH_PROFILE` geheim dat u eerder hebt gemaakt.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Bouw en implementeer een python-app in azure met behulp van het publicatie Profiel van de app. U ziet hoe de `publish-profile` invoer verwijst naar het `AZURE_WEBAPP_PUBLISH_PROFILE` geheim dat u eerder hebt gemaakt.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Service-principal](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Een .NET core-app bouwen en implementeren in azure met behulp van een Azure-Service-Principal. U ziet hoe de `creds` invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Bouw en implementeer een ASP.NET MVC-app in azure met behulp van een Azure-Service-Principal. U ziet hoe de `creds` invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@master  
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Bouw en implementeer een Java-lente-app in azure met behulp van een Azure-Service-Principal. U ziet hoe de `creds` invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>Javascript 

Een Node.js-app bouwen en implementeren in azure met behulp van een Azure-Service-Principal. U ziet hoe de `creds` invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Een python-app bouwen en implementeren in azure met behulp van een Azure-Service-Principal. U ziet hoe de `creds` invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>Volgende stappen

U vindt onze set acties die zijn gegroepeerd in verschillende opslag plaatsen op GitHub, elk met documentatie en voor beelden die u kunnen helpen bij het gebruik van GitHub voor CI/CD en uw apps te implementeren in Azure.

- [Werk stromen voor acties die moeten worden geïmplementeerd in azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-aanmelding](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp voor containers](https://github.com/Azure/webapps-container-deploy)

- [Aanmelden/afmelden bij docker](https://github.com/Azure/docker-login)

- [Gebeurtenissen die workflows activeren](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s implementeren](https://github.com/Azure/k8s-deploy)

- [Starter-werk stromen](https://github.com/actions/starter-workflows)
