---
title: CI/CD voor Azure lente-Cloud
description: CI/CD voor Azure lente-Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 331ef39facb9f7cf8f069f2a238be325f53de2d0
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618030"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD voor Azure lente-Cloud

Met de hulpprogram ma's voor continue integratie en continue levering kunt u snel updates implementeren voor bestaande toepassingen met minimale inspanning en risico. Azure DevOps helpt u bij het organiseren en beheren van deze belang rijke taken. Momenteel biedt Azure lente Cloud geen specifieke Azure DevOps-invoeg toepassing.  U kunt echter uw lente-Cloud toepassingen integreren met DevOps met behulp van een [Azure cli-taak](/azure/devops/pipelines/tasks/deploy/azure-cli).

Dit artikel laat u zien hoe u een Azure CLI-taak kunt gebruiken met Azure veer Cloud om te integreren met Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Een Azure Resource Manager service verbinding maken

Lees [dit artikel](/azure/devops/pipelines/library/connect-to-azure) voor meer informatie over het maken van een Azure Resource Manager service verbinding met uw Azure DevOps-project. Zorg ervoor dat u hetzelfde abonnement selecteert dat u gebruikt voor uw Azure lente-Cloud service-exemplaar.

## <a name="azure-cli-task-templates"></a>Azure CLI-taak sjablonen
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>Artefacten implementeren

U kunt uw projecten bouwen en implementeren met behulp van een reeks van `tasks` . Dit fragment definieert variabelen, een .NET core-taak om de toepassing te bouwen en een Azure CLI-taak om het *zip* -bestand te implementeren.

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --assign-endpoint
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>Artefacten implementeren

U kunt uw projecten bouwen en implementeren met behulp van een reeks van `tasks` . In dit fragment wordt eerst een Maven-taak gedefinieerd voor het bouwen van de toepassing, gevolgd door een tweede taak die het JAR-bestand implementeert met behulp van de Azure lente-Azure CLI-extensie.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Implementeren vanuit bron

Het is mogelijk om rechtstreeks naar Azure te implementeren zonder een afzonderlijke build-stap.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Uw eerste Azure Spring Cloud-toepassing implementeren](spring-cloud-quickstart.md)