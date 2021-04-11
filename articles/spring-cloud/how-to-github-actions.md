---
title: Azure lente Cloud CI/CD met GitHub-acties
description: CI/CD-werk stroom voor Azure lente-Cloud bouwen met GitHub-acties
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 8400fcacbfa4c76aceb079b788255e3d3b83ce33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878008"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure lente Cloud CI/CD met GitHub-acties

GitHub-acties ondersteunen een geautomatiseerde werk stroom voor de levens cyclus van software ontwikkeling. Met GitHub-acties voor Azure lente-Cloud kunt u werk stromen maken in uw opslag plaats voor het bouwen, testen, inpakken, vrijgeven en implementeren van implementatie naar Azure. 

## <a name="prerequisites"></a>Vereisten
In dit voor beeld is de [Azure cli](/cli/azure/install-azure-cli)vereist.

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub-opslag plaats en verificatie instellen
U hebt een Azure-Service-Principal-referentie nodig om de Azure-aanmeldings actie te autoriseren. Als u een Azure-referentie wilt ophalen, voert u de volgende opdrachten uit op de lokale computer:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Om toegang te krijgen tot een specifieke resource groep kunt u het bereik beperken:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

De opdracht moet een JSON-object uitvoeren:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

In dit voor beeld wordt het steeltoe-voor [beeld gebruikt op github](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample).  Splits de opslag plaats, open de GitHub-opslagplaats pagina voor de fork en selecteer het tabblad **instellingen** . Open het menu **geheimen** en selecteer **nieuw geheim**:

 ![Nieuw geheim toevoegen](./media/github-actions/actions1.png)

Stel de geheime naam `AZURE_CREDENTIALS` en de waarde ervan in op de JSON-teken reeks die u hebt gevonden onder de kop *uw github-opslag plaats instellen en verifiëren*.

 ![Geheime gegevens instellen](./media/github-actions/actions2.png)

U kunt de Azure-aanmeldings referentie ook ophalen uit Key Vault in GitHub-acties, zoals wordt uitgelegd in [Azure lente verifiëren met Key Vault in github-acties](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Service-exemplaar inrichten
Voer de volgende opdrachten uit met behulp van de Azure CLI om uw Azure lente-Cloud service-exemplaar in te richten.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>De werk stroom bouwen
De werk stroom is gedefinieerd met behulp van de volgende opties.

### <a name="prepare-for-deployment-with-azure-cli"></a>Voorbereiden op implementatie met Azure CLI

De opdracht `az spring-cloud app create` is momenteel niet idempotent. Nadat u het eenmaal hebt uitgevoerd, krijgt u een fout melding als u dezelfde opdracht opnieuw uitvoert. We raden u aan deze werk stroom uit te voeren op bestaande Azure lente-Cloud-apps en-exemplaren.

Gebruik de volgende Azure CLI-opdrachten voor voor bereiding:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Direct implementeren met Azure CLI

Maak het `.github/workflows/main.yml` bestand in de opslag plaats met de volgende inhoud. Vervang `<your resource group name>` en `<your service name>` door de juiste waarden.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub-opslag plaats en verificatie instellen
U hebt een Azure-Service-Principal-referentie nodig om de Azure-aanmeldings actie te autoriseren. Als u een Azure-referentie wilt ophalen, voert u de volgende opdrachten uit op de lokale computer:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Om toegang te krijgen tot een specifieke resource groep kunt u het bereik beperken:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
De opdracht moet een JSON-object uitvoeren:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

In dit voor beeld wordt het [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) -voor beeld gebruikt op github.  Fork het voor beeld, open de pagina GitHub repository en klik op het tabblad **instellingen** . Open het menu **geheimen** en klik op **een nieuw geheim toevoegen**:

 ![Nieuw geheim toevoegen](./media/github-actions/actions1.png)

Stel de geheime naam `AZURE_CREDENTIALS` en de waarde ervan in op de JSON-teken reeks die u hebt gevonden onder de kop *uw github-opslag plaats instellen en verifiëren*.

 ![Geheime gegevens instellen](./media/github-actions/actions2.png)

U kunt de Azure-aanmeldings referentie ook ophalen uit Key Vault in GitHub-acties, zoals wordt uitgelegd in [Azure lente verifiëren met Key Vault in github-acties](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Service-exemplaar inrichten
Voer de volgende opdrachten uit met behulp van de Azure CLI om uw Azure lente-Cloud service-exemplaar in te richten.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>De werk stroom bouwen
De werk stroom is gedefinieerd met behulp van de volgende opties.

### <a name="prepare-for-deployment-with-azure-cli"></a>Voorbereiden op implementatie met Azure CLI
De opdracht `az spring-cloud app create` is momenteel niet idempotent.  We raden u aan deze werk stroom uit te voeren op bestaande Azure lente-Cloud-apps en-exemplaren.

Gebruik de volgende Azure CLI-opdrachten voor voor bereiding:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Direct implementeren met Azure CLI
Maak het `.github/workflow/main.yml` bestand in de opslag plaats:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Implementeren met Azure CLI-actie
De `run` opdracht AZ maakt gebruik van de nieuwste versie van Azure cli. Als er belang rijke wijzigingen zijn, kunt u ook een specifieke versie van Azure CLI gebruiken met Azure/CLI `action` . 

> [!Note] 
> Deze opdracht wordt uitgevoerd in een nieuwe container, dus `env` werkt niet en de toegang tot cross Action-bestanden heeft mogelijk extra beperkingen.

Maak het github/workflow/Main. yml-bestand in de opslag plaats:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Implementeren met maven-invoeg toepassing
Een andere mogelijkheid is om de [maven-invoeg toepassing](./spring-cloud-quickstart.md) te gebruiken voor het implementeren van het jar en het bijwerken van de app-instellingen. De opdracht `mvn azure-spring-cloud:deploy` is idempotent en maakt zo nodig automatisch apps. U hoeft niet vooraf bijbehorende apps te maken.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>De werk stroom uitvoeren

GitHub- **acties** moeten automatisch worden ingeschakeld nadat u naar github hebt gepusht `.github/workflow/main.yml` . De actie wordt geactiveerd wanneer u een nieuwe door Voer pusht. Als u dit bestand in de browser maakt, moet uw actie al worden uitgevoerd.

Als u wilt controleren of de actie is ingeschakeld, klikt u op het tabblad **acties** op de pagina github-opslag plaats:

![De actie controleren is ingeschakeld](./media/github-actions/actions3.png)

Als uw actie in een fout wordt uitgevoerd, bijvoorbeeld als u de Azure-referentie niet hebt ingesteld, kunt u controles opnieuw uitvoeren nadat de fout is opgelost. Klik op de pagina GitHub-opslag plaats op **acties**, selecteer de specifieke werk stroom taak en klik vervolgens op de knop **controles opnieuw uitvoeren** om de controles opnieuw uit te voeren:

![Controles opnieuw uitvoeren](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Volgende stappen

* [Key Vault voor GitHub acties voor lente-Cloud](./spring-cloud-github-actions-key-vault.md)
* [Service-principals Azure Active Directory](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)
* [GitHub-acties voor Azure](https://github.com/Azure/actions/)
