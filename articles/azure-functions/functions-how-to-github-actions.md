---
title: GitHub-acties gebruiken om code-updates te maken in Azure Functions
description: Leer hoe u GitHub-acties kunt gebruiken om een werk stroom te definiëren voor het bouwen en implementeren van Azure Functions projecten in GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, github-actions-azure
ms.openlocfilehash: ae3ffb9dc387129153b684a849ba4e58274ad80c
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563276"
---
# <a name="continuous-delivery-by-using-github-action"></a>Continue levering met behulp van GitHub-actie

Gebruik [github-acties](https://github.com/features/actions) om een werk stroom te definiëren om automatisch code te bouwen en te implementeren in uw Azure-functie-app. 

In GitHub acties is een [werk stroom](https://help.github.com/articles/about-github-actions#workflow) een geautomatiseerd proces dat u in uw github-opslag plaats definieert. Dit proces vertelt u GitHub hoe u uw functions-app-project bouwt en implementeert op GitHub. 

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom. 

Voor een Azure Functions werk stroom heeft het bestand drie secties: 

| Sectie | Taken |
| ------- | ----- |
| **Verificatie** | Down load een publicatie profiel.<br/>Maak een GitHub-geheim.|
| **Ontwikkelen** | Stel de omgeving in.<br/>Bouw de functie-app.|
| **Implementeren** | Implementeer de functie-app.|

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account.  
- Een werk functie-app die wordt gehost op Azure met een GitHub-opslag plaats.   
    - [Snelstart: Een functie maken in Azure met behulp van Visual Studio Code](./create-first-function-vs-code-csharp.md)

## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

De aanbevolen manier om te verifiëren met Azure Functions voor GitHub-acties is door gebruik te maken van een publicatie profiel. U kunt ook verifiëren met een service-principal. Zie [deze github-opslag plaats](https://github.com/Azure/functions-action)voor meer informatie. 

Nadat u de referentie voor het publicatie profiel hebt opgeslagen als een [github-geheim](https://docs.github.com/en/actions/reference/encrypted-secrets), gebruikt u dit geheim in uw werk stroom om te verifiëren met Azure. 

#### <a name="download-your-publish-profile"></a>Uw publicatie profiel downloaden

Het publicatie Profiel van uw functie-app downloaden:

1. Selecteer de pagina **overzicht** van de functie-app en selecteer vervolgens **publicatie profiel ophalen**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Publicatie profiel downloaden":::

1. Sla de inhoud van het bestand op en kopieer deze.


### <a name="add-the-github-secret"></a>Het GitHub-geheim toevoegen

1. Blader in [github](https://github.com)naar uw opslag plaats en selecteer **instellingen**  >  **geheimen**  >  **een nieuw geheim toevoegen**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Geheim toevoegen":::

1. Voeg een nieuw geheim toe met `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` de **naam**, de inhoud van het publicatie profiel bestand voor **waarde** en selecteer **geheim toevoegen**.

GitHub kan nu worden geverifieerd bij uw functie-app in Azure.

## <a name="create-the-environment"></a>De omgeving maken 

Het instellen van de omgeving wordt uitgevoerd met een taalspecifiek installatie actie voor de publicatie.

# <a name="net"></a>[.NET](#tab/dotnet)

.NET (inclusief ASP.NET) gebruikt de `actions/setup-dotnet` actie.  
In het volgende voor beeld ziet u het deel van de werk stroom waarmee de omgeving wordt ingesteld:

```yaml
    - name: Setup DotNet 2.2.402 Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 2.2.402
```

# <a name="java"></a>[Java](#tab/java)

Java maakt gebruik van de  `actions/setup-java` actie.  
In het volgende voor beeld ziet u het deel van de werk stroom waarmee de omgeving wordt ingesteld:

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Java script (Node.js) gebruikt de `actions/setup-node` actie.  
In het volgende voor beeld ziet u het deel van de werk stroom waarmee de omgeving wordt ingesteld:

```yaml

    - name: Setup Node 12.x Environment
      uses: actions/setup-node@v1
      with:
        node-version: 12.x
```

# <a name="python"></a>[Python](#tab/python)

Python gebruikt de `actions/setup-python` actie.  
In het volgende voor beeld ziet u het deel van de werk stroom waarmee de omgeving wordt ingesteld:

```yaml
    - name: Setup Python 3.7 Environment
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
```
---

## <a name="build-the-function-app"></a>De functie-app bouwen

Dit is afhankelijk van de taal en voor talen die door Azure Functions worden ondersteund. dit gedeelte moet de standaard stappen voor het bouwen van elke taal zijn.

In het volgende voor beeld ziet u het deel van de werk stroom dat de functie-app bouwt, die specifiek is voor de taal:

# <a name="net"></a>[.NET](#tab/dotnet)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    env:
      POM_XML_DIRECTORY: '.'  # set this to the directory which contains pom.xml file

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'  # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
```
---

## <a name="deploy-the-function-app"></a>De functie-app implementeren
Gebruik de `Azure/functions-action` actie om uw code te implementeren in een functie-app. Deze actie heeft drie para meters:

|Parameter |Uitleg  |
|---------|---------|
|_**app-naam**_ | Ingevuld De naam van de functie-app. |
|_**sleuf naam**_ | Beschrijving De naam van de [implementatie sleuf](functions-deployment-slots.md) waarnaar u wilt implementeren. De sleuf moet al zijn gedefinieerd in uw functie-app. |
|_**publicatie profiel**_ | Beschrijving De naam van het GitHub-geheim voor uw publicatie profiel. |

In het volgende voor beeld wordt versie 1 van de `functions-action` en a gebruikt `publish profile` voor verificatie 

# <a name="net"></a>[.NET](#tab/dotnet)

Stel een .NET Linux-werk stroom in die gebruikmaakt van een publicatie profiel.

```yaml
name: Deploy DotNet project to Azure function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```
Stel een .NET Windows-werk stroom in die gebruikmaakt van een publicatie profiel.

```yaml
name: Deploy DotNet project to Azure function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="java"></a>[Java](#tab/java)

Stel een Java Linux-werk stroom in die gebruikmaakt van een publicatie profiel.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Stel een Java Windows-werk stroom in die gebruikmaakt van een publicatie profiel.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Stel een Node.JS Linux-werk stroom in die gebruikmaakt van een publicatie profiel.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '12.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Stel een Node.JS Windows-werk stroom in die gebruikmaakt van een publicatie profiel.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```
# <a name="python"></a>[Python](#tab/python)

Stel een python Linux-werk stroom in die gebruikmaakt van een publicatie profiel.

```yaml
name: Deploy Python project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.7'                 # set this to the python version to use (supports 3.6, 3.7, 3.8)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v1
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure en GitHub-integratie](/azure/developer/github/)