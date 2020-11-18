---
title: Implementeren en upgraden met Azure Resource Manager
description: Meer informatie over het implementeren van toepassingen en services naar een Service Fabric cluster met behulp van een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: bb866eb24fb1b286f496bad9845d1ee557baa221
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681666"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Toepassingen en services beheren als Azure Resource Manager resources

U kunt toepassingen en services implementeren op uw Service Fabric-cluster via Azure Resource Manager. Dit betekent dat in plaats van het implementeren en beheren van toepassingen via Power shell of CLI, nadat u hebt gewacht totdat het cluster gereed is, u nu toepassingen en services in JSON opneemt en ze in dezelfde resource manager-sjabloon kunt implementeren als uw cluster. Het proces voor het registreren, inrichten en implementeren van de toepassing gebeurt allemaal in één stap.

Dit is de aanbevolen manier om de installatie-, governance-of cluster beheer toepassingen te implementeren die u nodig hebt in uw cluster. Dit omvat de [patch](service-fabric-patch-orchestration-application.md)-indelings toepassing, watchdog of toepassingen die in uw cluster moeten worden uitgevoerd voordat andere toepassingen of services worden geïmplementeerd. 

Als dit van toepassing is, kunt u uw toepassingen beheren als Resource Manager-resources om te verbeteren:
* Audittrail: in Resource Manager wordt elke bewerking gecontroleerd en wordt een gedetailleerd *activiteiten logboek* bijgehouden waarmee u wijzigingen kunt traceren die zijn aangebracht in deze toepassingen en uw cluster.
* Op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC): het beheren van toegang tot clusters en toepassingen die op het cluster zijn geïmplementeerd, kunnen worden uitgevoerd via dezelfde resource manager-sjabloon.
* Azure Resource Manager (via Azure Portal) wordt een one-stop-shop voor het beheren van uw cluster en essentiële toepassings implementaties.

Het volgende code fragment toont de verschillende soorten resources die kunnen worden beheerd via een sjabloon:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Een nieuwe toepassing toevoegen aan uw Resource Manager-sjabloon

1. Bereid de Resource Manager-sjabloon voor uw cluster voor op implementatie. Zie [een service Fabric cluster maken met behulp van Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) voor meer informatie.
2. Denk na over enkele van de toepassingen die u wilt implementeren in het cluster. Zijn er nog steeds andere toepassingen waarop mogelijk afhankelijkheden kunnen worden toegepast? Bent u van plan een cluster governance of installatie toepassingen te implementeren? Deze soorten toepassingen worden het best beheerd via een resource manager-sjabloon, zoals hierboven is beschreven. 
3. Wanneer u hebt vastgesteld welke toepassingen u op deze manier wilt implementeren, moeten de toepassingen worden verpakt, ingepakt en op een bestands share worden geplaatst. De share moet toegankelijk zijn via een REST-eind punt voor Azure Resource Manager om te worden gebruikt tijdens de implementatie.
4. Beschrijf in uw Resource Manager-sjabloon onder uw cluster declaratie de eigenschappen van elke toepassing. Deze eigenschappen omvatten het aantal replica's of instanties en eventuele afhankelijkheids ketens tussen bronnen (andere toepassingen of Services). Zie de [rest API Swagger spec](https://aka.ms/sfrpswaggerspec)voor een lijst met uitgebreide eigenschappen. Houd er rekening mee dat hiermee de toepassings-of service manifesten niet worden vervangen, maar wordt in plaats daarvan een deel van de Resource Manager-sjabloon van het cluster beschreven. Hier volgt een voorbeeld sjabloon met een stateless service *Service1* en een stateful service *Service2* implementeren als onderdeel van *Application1*:

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > De *apiVersion* moet worden ingesteld op `"2019-03-01"` . Deze sjabloon kan ook onafhankelijk van het cluster worden geïmplementeerd, zolang het cluster al is geïmplementeerd.

5. Zetten! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Service Fabric resource provider toepassings resource verwijderen
De volgende trigger wordt het app-pakket voor het ongedaan maken van de inrichting van het cluster geactiveerd en de gebruikte schijf ruimte wordt opgeruimd:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Als u micro soft. ServiceFabric/clusters/Application uit uw ARM-sjabloon verwijdert, wordt de inrichting van de toepassing niet ongedaan gemaakt

>[!NOTE]
> Zodra het verwijderen is voltooid, moet u de pakket versie in SFX of ARM niet meer zien. U kunt de bron van het toepassings type versie waarop de toepassing wordt uitgevoerd, niet verwijderen. ARM/SFRP zorgt ervoor dat dit niet mogelijk is. Als u probeert de inrichting van het verwerkte pakket ongedaan te maken, wordt dit door de SF-runtime voor komen.


## <a name="manage-an-existing-application-via-resource-manager"></a>Een bestaande toepassing beheren via Resource Manager

Als uw cluster al actief is en bepaalde toepassingen die u wilt beheren als Resource Manager-resources, al zijn geïmplementeerd, kunt u in plaats van de toepassingen te verwijderen en ze opnieuw te implementeren, een PUT-aanroep gebruiken met dezelfde Api's zodat de toepassingen worden bevestigd als Resource Manager-resources. Zie [Wat is het service Fabric-toepassings bron model?](./service-fabric-concept-resource-model.md) voor meer informatie.

> [!NOTE]
> Als u een cluster upgrade wilt toestaan om beschadigde apps te negeren, kan de klant ' maxPercentUnhealthyApplications: 100 ' in de sectie ' upgradeDescription/healthPolicy ' opgeven. gedetailleerde beschrijvingen voor alle instellingen bevinden zich in [service fabrics rest API documentatie voor het upgrade beleid](/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)van het cluster.

## <a name="next-steps"></a>Volgende stappen

* Gebruik de [service Fabric cli](service-fabric-cli.md) of [Power shell](service-fabric-deploy-remove-applications.md) om andere toepassingen te implementeren in uw cluster. 
* [Service Fabric cluster bijwerken](service-fabric-cluster-upgrade.md)
