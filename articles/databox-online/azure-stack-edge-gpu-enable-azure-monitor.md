---
title: Azure Monitor op Azure Stack Edge Pro GPU-apparaat inschakelen
description: Hierin wordt beschreven hoe u Azure Monitor op een Azure Stack Edge Pro GPU-apparaat inschakelt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 199ec8e2f1e8eb74d971286a4fc6180eb8b72f2a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595969"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Monitor op uw Azure Stack Edge Pro GPU-apparaat inschakelen

Het bewaken van containers op uw Azure Stack Edge Pro GPU-apparaat is van cruciaal belang, speciaal wanneer u meerdere Compute-toepassingen uitvoert. Met Azure Monitor kunt u container logboeken en geheugen-en processor gegevens verzamelen uit het Kubernetes-cluster dat op uw apparaat wordt uitgevoerd.

In dit artikel worden de stappen beschreven die nodig zijn om Azure Monitor op uw apparaat in te scha kelen en container logboeken te verzamelen in Log Analytics werk ruimte. Het opslag Azure Monitor metrische gegevens wordt momenteel niet ondersteund met uw Azure Stack Edge Pro GPU-apparaat. 


## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u het volgende nodig:

- Een Azure Stack Edge Pro-apparaat. Zorg ervoor dat het apparaat is geactiveerd volgens de stappen in de [zelf studie: Activeer uw apparaat](azure-stack-edge-gpu-deploy-activate.md).
- U hebt de stappen voor het configureren van de **reken** procedure voltooid volgens de [zelf studie: Configureer Compute op uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-configure-compute.md) op uw apparaat. Op uw apparaat moet een IoT Hub resource, een IoT-apparaat en een IoT Edge apparaat zijn geïnstalleerd.


## <a name="create-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

Voer de volgende stappen uit om een log Analytics-werk ruimte te maken. Een log Analytics-werk ruimte is een logische opslag eenheid waar de logboek gegevens worden verzameld en opgeslagen.

1. Selecteer in de Azure Portal **+ een resource maken** en zoek naar **log Analytics werk ruimte** en selecteer vervolgens **maken**. 
1. Configureer de volgende instellingen in de **werk ruimte Create log Analytics**. Accepteer het restant als standaard.

    1. Geef op het tabblad **basis beginselen** het abonnement, de resource groep, de naam en de regio voor de werk ruimte op. 

        ![Tabblad basis informatie over Log Analytics-werk ruimte](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. Accepteer op het tabblad **prijs categorie** het standaard **abonnement voor betalen per gebruik**.

        ![Tabblad prijzen voor Log Analytics werk ruimte](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. Controleer op het tabblad **controleren en maken** de gegevens voor uw werk ruimte en selecteer **maken**.

        ![Log Analytics-werk ruimte + maken bekijken](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

Zie voor meer informatie de gedetailleerde stappen in [een log Analytics-werk ruimte maken via Azure Portal](../azure-monitor/logs/quick-create-workspace.md).



## <a name="enable-container-insights"></a>Container Insights inschakelen

Voer de volgende stappen uit om container Insights in te scha kelen in uw werk ruimte. 

1. Volg de gedetailleerde stappen in de [oplossing Azure monitor containers toevoegen](../azure-monitor/containers/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution). Gebruik het volgende sjabloon bestand `containerSolution.json` :

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. De resource-ID en-locatie ophalen. Ga naar `Your Log Analytics workspace > General > Properties`. Kopieer de volgende gegevens:

    - **resource-id**: dit is de volledig gekwalificeerde Azure-resource-id van de Azure log Analytics-werk ruimte. 
    - **locatie**, de Azure-regio.

    ![Eigenschappen van Log Analytics werk ruimte](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. Gebruik het volgende para meters-bestand `containerSolutionParams.json` . Vervang door `workspaceResourceId` de resource-id en `workspaceRegion` met de locatie die in de vorige stap is gekopieerd.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    Hier volgt een voor beeld van een uitvoer van een Log Analytics-werk ruimte waarvoor container Insights is ingeschakeld:
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>Azure Monitor op uw apparaat configureren

1. Ga naar de zojuist gemaakte Log Analytics resource en kopieer de **werk ruimte-id** en de **primaire sleutel** (werkruimte sleutel).

    ![Agent beheer in Log Analytics werk ruimte](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    Sla deze informatie op zoals u deze in een latere stap gaat gebruiken.

1. [Maak verbinding met de Power shell-interface van het apparaat](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 
    
1. Gebruik de log Analytics-werk ruimte-ID en de werkruimte sleutel met de volgende cmdlet:

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. Nadat de Azure Monitor is ingeschakeld, ziet u Logboeken in de werk ruimte Log Analytics. Als u de status van het Kubernetes-cluster wilt weer geven dat op uw apparaat is geïmplementeerd, gaat u naar **Azure Monitor > inzichten > containers**. Selecteer alle voor de omgevings optie **alle**. 

    ![Metrische gegevens in Log Analytics werk ruimte](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het bewaken van Kubernetes-workloads via het Kubernetes-dash board](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- Meer informatie over het [beheren van waarschuwings meldingen voor gebeurtenis apparaten](azure-stack-edge-gpu-manage-device-event-alert-notifications.md). 