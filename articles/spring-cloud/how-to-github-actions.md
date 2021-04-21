---
title: Azure Spring Cloud CI/CD met GitHub Actions
description: CI/CD-werkstroom voor Azure Spring Cloud met GitHub Actions
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: c52279108a8fd8d5a7ac8bbd7c8eb215097b21b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791351"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD met GitHub Actions

GitHub Actions ondersteuning voor een geautomatiseerde levenscycluswerkstroom voor softwareontwikkeling. Met GitHub Actions for Azure Spring Cloud kunt u werkstromen in uw opslagplaats maken om in Azure te bouwen, testen, verpakken, vrijgeven en implementeren. 

## <a name="prerequisites"></a>Vereisten
Voor dit voorbeeld is de [Azure CLI vereist.](/cli/azure/install-azure-cli)

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub-opslagplaats instellen en verifiëren
U hebt een azure-service-principalreferentie nodig om de Azure-aanmeldingsactie te autor toestemming te geven. Voer de volgende opdrachten uit op uw lokale computer om een Azure-referentie op te halen:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Voor toegang tot een specifieke resourcegroep kunt u het bereik beperken:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Met de opdracht moet een JSON-object worden uitgevoerd:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

In dit voorbeeld wordt het [steeltoe-voorbeeld op GitHub gebruikt.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample)  Maak een fork van de opslagplaats, open de pagina GitHub-opslagplaats voor de fork en selecteer het **tabblad** Instellingen. Open het menu **Geheimen** en selecteer **Nieuw geheim**:

 ![Nieuw geheim toevoegen](./media/github-actions/actions1.png)

Stel de geheime naam en de waarde ervan in op de JSON-tekenreeks die u hebt gevonden onder de kop `AZURE_CREDENTIALS` *Uw GitHub-opslagplaats instellen en verifieert*.

 ![Geheime gegevens instellen](./media/github-actions/actions2.png)

U kunt ook de Azure-aanmeldingsreferenties op Key Vault in GitHub-acties, zoals wordt uitgelegd in Azure Spring verifiëren [met Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Service-exemplaar inrichten
Als u uw Azure Spring Cloud service-exemplaar wilt inrichten, voert u de volgende opdrachten uit met behulp van de Azure CLI.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>De werkstroom bouwen
De werkstroom wordt gedefinieerd met behulp van de volgende opties.

### <a name="prepare-for-deployment-with-azure-cli"></a>Implementatie voorbereiden met Azure CLI

De opdracht `az spring-cloud app create` is momenteel niet idempotent. Nadat u deze eenmaal hebt uitgevoerd, krijgt u een foutmelding als u dezelfde opdracht opnieuw hebt uitgevoerd. We raden deze werkstroom aan voor Azure Spring Cloud apps en exemplaren.

Gebruik de volgende Azure CLI-opdrachten ter voorbereiding:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Rechtstreeks implementeren met Azure CLI

Maak het `.github/workflows/main.yml` bestand in de opslagplaats met de volgende inhoud. Vervang `<your resource group name>` en door de juiste `<your service name>` waarden.

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
## <a name="set-up-github-repository-and-authenticate"></a>GitHub-opslagplaats instellen en verifiëren
U hebt een azure-service-principalreferentie nodig om de Azure-aanmeldingsactie te autor toestemming te geven. Voer de volgende opdrachten uit op uw lokale computer om een Azure-referentie op te halen:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Voor toegang tot een specifieke resourcegroep kunt u het bereik beperken:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Met de opdracht moet een JSON-object worden uitgevoerd:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

In dit voorbeeld wordt het [PiggyMetrics-voorbeeld](https://github.com/Azure-Samples/piggymetrics) op GitHub gebruikt.  Fork het voorbeeld, open de pagina van de GitHub-opslagplaats en klik op **het tabblad** Instellingen. Open **het** menu Geheimen en klik **op Een nieuw geheim toevoegen:**

 ![Nieuw geheim toevoegen](./media/github-actions/actions1.png)

Stel de geheime naam en de waarde ervan in op de JSON-tekenreeks die u hebt gevonden onder de kop `AZURE_CREDENTIALS` *Uw GitHub-opslagplaats instellen en verifieren.*

 ![Geheime gegevens instellen](./media/github-actions/actions2.png)

U kunt de Azure-aanmeldingsreferenties ook op Key Vault in GitHub-acties, zoals wordt uitgelegd in Azure Spring verifiëren [met Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Service-exemplaar inrichten
Als u uw Azure Spring Cloud service-exemplaar wilt inrichten, voert u de volgende opdrachten uit met behulp van de Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>De werkstroom bouwen
De werkstroom wordt gedefinieerd met behulp van de volgende opties.

### <a name="prepare-for-deployment-with-azure-cli"></a>Implementatie voorbereiden met Azure CLI
De opdracht `az spring-cloud app create` is momenteel niet idempotent.  We raden deze werkstroom aan voor bestaande Azure Spring Cloud apps en exemplaren.

Gebruik de volgende Azure CLI-opdrachten ter voorbereiding:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Rechtstreeks implementeren met Azure CLI
Maak het `.github/workflow/main.yml` bestand in de opslagplaats:

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
De opdracht az `run` maakt gebruik van de nieuwste versie van Azure CLI. Als er belangrijke wijzigingen zijn, kunt u ook een specifieke versie van Azure CLI gebruiken met `action` azure/CLI. 

> [!Note] 
> Deze opdracht wordt uitgevoerd in een nieuwe container, dus werkt niet en toegang tot bestandsoverschrijdende acties `env` kan extra beperkingen hebben.

Maak het bestand .github/workflow/main.yml in de opslagplaats:
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

## <a name="deploy-with-maven-plugin"></a>Implementeren met De Maven-invoeg-app
Een andere optie is om de [Maven-invoegapp te gebruiken](./spring-cloud-quickstart.md) voor het implementeren van de JAR en het bijwerken van app-instellingen. De opdracht `mvn azure-spring-cloud:deploy` is idempotent en maakt automatisch apps indien nodig. U hoeft niet van tevoren bijbehorende apps te maken.

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

## <a name="run-the-workflow"></a>De werkstroom uitvoeren

GitHub **Actions** moet automatisch worden ingeschakeld nadat u naar `.github/workflow/main.yml` GitHub hebt ge pusht. De actie wordt geactiveerd wanneer u een nieuwe door commit pusht. Als u dit bestand in de browser maakt, moet uw actie al zijn uitgevoerd.

Als u wilt controleren of de actie is ingeschakeld, klikt u op **het tabblad Acties** op de pagina GitHub-opslagplaats:

![De actie Controleren is ingeschakeld](./media/github-actions/actions3.png)

Als uw actie fout wordt uitgevoerd, bijvoorbeeld als u de Azure-referentie niet hebt ingesteld, kunt u controles opnieuw uitvoeren nadat u de fout hebt opgelost. Klik op de pagina GitHub-opslagplaats op **Acties,** selecteer  de specifieke werkstroomtaak en klik vervolgens op de knop Controles opnieuw uitvoeren om controles opnieuw uit te voeren:

![Controles opnieuw uitvoeren](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Volgende stappen

* [Key Vault voor Spring Cloud GitHub-acties](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory service-principals](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)
* [GitHub-acties voor Azure](https://github.com/Azure/actions/)
