---
title: CI/CD configureren met GitHub Actions
description: Meer informatie over het implementeren van uw code Azure App Service een CI/CD-pijplijn met GitHub Actions. Pas de buildtaken aan en voer complexe implementaties uit.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 1ed2b007ae00516a030e67b7f6abacbd00a8d403
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772879"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Implementeren in App Service met behulp van GitHub Actions

Ga aan de [slag GitHub Actions](https://docs.github.com/en/actions/learn-github-actions) om uw werkstroom te automatiseren en vanuit GitHub Azure App Service [implementeren.](overview.md) 

## <a name="prerequisites"></a>Vereisten 

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account.  
- Een werkende Azure App Service app. 
    - .NET: [een ASP.NET Core-web-app maken in Azure](quickstart-dotnetcore.md)
    - ASP.NET: [Een ASP.NET Framework-web-app maken in Azure](quickstart-dotnet-framework.md)
    - JavaScript: [een Node.js-web-app maken in Azure App Service](quickstart-nodejs.md)  
    - Java: [Een Java-app maken op Azure App Service](quickstart-java.md)
    - Python: [Een Python-app maken in Azure App Service](quickstart-python.md)

## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Het bestand heeft drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een service-principal of publicatieprofiel. <br /> 2. Maak een GitHub-opslagplaats. |
|**Build** | 1. De omgeving instellen. <br /> 2. Bouw de web-app. |
|**Implementeren** | 1. De web-app implementeren. |

## <a name="use-the-deployment-center"></a>Het implementatiecentrum gebruiken

U kunt snel aan de slag met GitHub Actions met behulp van App Service Deployment Center. Hiermee wordt automatisch een werkstroombestand gegenereerd op basis van uw toepassingsstack en wordt dit bestand in uw GitHub-opslagplaats in de juiste map geplaatst.

1. Navigeer naar uw web-app in Azure Portal
1. Klik aan de linkerkant op **Implementatiecentrum**
1. Selecteer **onder Continue implementatie (CI/CD)** de optie **GitHub**
1. Selecteer vervolgens **GitHub Actions**
1. Gebruik de vervolgkeuzen om uw GitHub-opslagplaats, -vertakking en toepassingsstack te selecteren
    - Als de geselecteerde vertakking is beveiligd, kunt u het werkstroombestand nog steeds toevoegen. Zorg ervoor dat u uw vertakkingsbeveiligingen controleert voordat u doorgaat.
1. In het laatste scherm kunt u uw selecties bekijken en een voorbeeld bekijken van het werkstroombestand dat wordt vastgelegd in de opslagplaats. Als de selecties juist zijn, klikt u op **Voltooien**

Hiermee wordt het werkstroombestand naar de opslagplaats doorgeslagen. De werkstroom voor het bouwen en implementeren van uw app begint onmiddellijk.

## <a name="set-up-a-workflow-manually"></a>Handmatig een werkstroom instellen

U kunt ook een werkstroom implementeren zonder het Implementatiecentrum te gebruiken. Als u dit wilt doen, moet u eerst implementatiereferenties genereren. 

## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

De aanbevolen manier om te verifiëren met Azure-app Services voor GitHub Actions is met een publicatieprofiel. U kunt ook verifiëren met een service-principal, maar voor het proces zijn meer stappen vereist. 

Sla uw publicatieprofielreferentie of service-principal op als [een GitHub-geheim om](https://docs.github.com/en/actions/reference/encrypted-secrets) te verifiëren bij Azure. U hebt toegang tot het geheim in uw werkstroom. 

# <a name="publish-profile"></a>[Profiel publiceren](#tab/applevel)

Een publicatieprofiel is een referentie op app-niveau. Stel uw publicatieprofiel in als een GitHub-geheim. 

1. Ga naar uw app-service in de Azure Portal. 

1. Selecteer op **de pagina** Overzicht de optie **Publicatieprofiel opmaken.**

1. Sla het gedownloade bestand op. U gebruikt de inhoud van het bestand om een GitHub-geheim te maken.

> [!NOTE]
> Vanaf oktober 2020 moet voor Linux-web-apps de app-instelling zijn ingesteld op voordat `WEBSITE_WEBDEPLOY_USE_SCM` `true` **het publicatieprofiel wordt gedownload.** Deze vereiste wordt in de toekomst verwijderd.

# <a name="service-principal"></a>[Service-principal](#tab/userlevel)

U kunt een [service-principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Vervang in het bovenstaande voorbeeld de tijdelijke aanduidingen door uw abonnements-id, resourcegroepnaam en app-naam. De uitvoer is een JSON-object met de roltoewijzingsreferenties die toegang bieden tot uw App Service-app, vergelijkbaar met hieronder. Kopieer dit JSON-object voor later gebruik.

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
> Het is altijd een goed idee om minimale toegang te verlenen. Het bereik in het vorige voorbeeld is beperkt tot de specifieke App Service app en niet tot de hele resourcegroep.

---

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren


# <a name="publish-profile"></a>[Profiel publiceren](#tab/applevel)

Blader [in GitHub](https://github.com/)door uw opslagplaats, selecteer **Instellingen > Geheimen > Een nieuw geheim toevoegen.**

Als u [referenties op app-niveau wilt](#generate-deployment-credentials)gebruiken, plakt u de inhoud van het gedownloade publicatieprofielbestand in het waardeveld van het geheim. Noem het geheim `AZURE_WEBAPP_PUBLISH_PROFILE` .

Wanneer u uw GitHub-werkstroom configureert, gebruikt u `AZURE_WEBAPP_PUBLISH_PROFILE` de in de actie Azure-web-app implementeren. Bijvoorbeeld:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Service-principal](#tab/userlevel)

Blader [in GitHub](https://github.com/)door uw opslagplaats, selecteer **Instellingen > Geheimen > Een nieuw geheim toevoegen.**

Als u referenties op gebruikersniveau wilt gebruiken, plakt u de volledige JSON-uitvoer van de Azure [CLI-opdracht](#generate-deployment-credentials)in het waardeveld van het geheim. Geef het geheim de naam `AZURE_CREDENTIALS`.

Wanneer u het werkstroombestand later configureert, gebruikt u het geheim voor de invoer `creds` van de Azure-aanmeldingsactie. Bijvoorbeeld:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>De omgeving instellen

U kunt de omgeving instellen met behulp van een van de installatieacties.

|**Taal**  |**Installatieactie**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

De volgende voorbeelden laten zien hoe u de omgeving in kunt stellen voor de verschillende ondersteunde talen:

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
    - uses: actions/checkout@main
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

Het proces van het bouwen van een web-app en het implementeren in Azure App Service afhankelijk van de taal. 

De volgende voorbeelden tonen het deel van de werkstroom waarmee de web-app wordt gebouwd, in verschillende ondersteunde talen.

Voor alle talen kunt u de hoofdmap van de web-app instellen met `working-directory` . 

**.NET**

De `AZURE_WEBAPP_PACKAGE_PATH` omgevingsvariabele stelt het pad naar uw web-app-project in. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

U kunt NuGet-afhankelijkheden herstellen en msbuild uitvoeren met `run` . 

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

Voor Node.js kunt u instellen of `working-directory` wijzigen voor npm-directory in `pushd` . 

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

Als u uw code wilt implementeren in App Service app, gebruikt u de `azure/webapps-deploy@v2` actie . Deze actie heeft vier parameters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **app-name** | (Vereist) Naam van de App Service app | 
| **publish-profile** | (Optioneel) Inhoud van profielbestand publiceren met Web Deploy-geheimen |
| **package** | (Optioneel) Pad naar pakket of map. Het pad kan *.zip, *.war, *.jar of een map bevatten om te implementeren |
| **sleufnaam** | (Optioneel) Een andere bestaande sleuf dan de productiesleuf [invoeren](deploy-staging-slots.md) |


# <a name="publish-profile"></a>[Profiel publiceren](#tab/applevel)

### <a name="net-core"></a>.NET Core

Bouw en implementeer een .NET Core-app in Azure met behulp van een Azure-publicatieprofiel. De `publish-profile` invoer verwijst naar het `AZURE_WEBAPP_PUBLISH_PROFILE` geheim dat u eerder hebt gemaakt.

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
      - uses: actions/checkout@main
      
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

Bouw en implementeer een ASP.NET MVC-app die gebruikmaakt van NuGet en `publish-profile` voor verificatie. 


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

    - uses: actions/checkout@main  
    
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

Bouw en implementeer een Java Spring-app in Azure met behulp van een Azure-publicatieprofiel. De `publish-profile` invoer verwijst naar het `AZURE_WEBAPP_PUBLISH_PROFILE` geheim dat u eerder hebt gemaakt.

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

Als u een `war` wilt implementeren in plaats van een , `jar` wijzigt u de `package` waarde. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>Javascript 

Bouw en implementeer een Node.js-app in Azure met behulp van het publicatieprofiel van de app. De `publish-profile` invoer verwijst naar `AZURE_WEBAPP_PUBLISH_PROFILE` het geheim dat u eerder hebt gemaakt.

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
    - uses: actions/checkout@main
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

Bouw en implementeer een Python-app in Azure met behulp van het publicatieprofiel van de app. U ziet dat `publish-profile` de invoer verwijst naar het `AZURE_WEBAPP_PUBLISH_PROFILE` geheim dat u eerder hebt gemaakt.

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

Bouw en implementeer een .NET Core-app in Azure met behulp van een Azure-service-principal. U ziet dat `creds` de invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.


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
      - uses: actions/checkout@main
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

Bouw en implementeer een ASP.NET MVC-app in Azure met behulp van een Azure-service-principal. U ziet dat `creds` de invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.

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
    - uses: actions/checkout@main
    
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

Bouw en implementeer een Java Spring-app in Azure met behulp van een Azure-service-principal. U ziet dat `creds` de invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.

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

Bouw en implementeer een Node.js-app in Azure met behulp van een Azure-service-principal. U ziet dat `creds` de invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.

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
      uses: actions/checkout@main
   
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

Bouw en implementeer een Python-app in Azure met behulp van een Azure-service-principal. U ziet dat `creds` de invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.

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

U vindt onze set acties gegroepeerd in verschillende opslagplaatsen op GitHub, die elk documentatie en voorbeelden bevatten om u te helpen GitHub voor CI/CD te gebruiken en uw apps te implementeren in Azure.

- [Werkstromen voor acties die in Azure moeten worden geïmplementeerd](https://github.com/Azure/actions-workflow-samples)

- [Azure-aanmelding](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp voor containers](https://github.com/Azure/webapps-container-deploy)

- [Docker-aanmelding/-aanmelding](https://github.com/Azure/docker-login)

- [Gebeurtenissen die workflows activeren](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s implementeren](https://github.com/Azure/k8s-deploy)

- [Starterwerkstromen](https://github.com/actions/starter-workflows)
