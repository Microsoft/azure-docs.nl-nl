---
title: Sjabloon voor het maken van Resource Health-waarschuwingen
description: Maak waarschuwingen via een programma waarmee u wordt gewaarschuwd wanneer uw Azure-resources niet meer beschikbaar zijn.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 4f1cbe1e2d2c185906feb4ccba380cb31df864f5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588203"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Resourcestatuswaarschuwingen configureren met Resource Manager-sjablonen

In dit artikel wordt uitgelegd hoe u Resource Health waarschuwingen voor activiteiten Logboeken kunt maken via Azure Resource Manager sjablonen en Azure PowerShell.

Azure Resource Health houdt u op de hoogte van de huidige en historische status van uw Azure-resources. Met Azure Resource Health-waarschuwingen ontvangt u vrijwel in realtime een melding als de status van deze resources wordt gewijzigd. Door Resource Health waarschuwingen via een programma te maken, kunnen gebruikers waarschuwingen bulksgewijs maken en aanpassen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u de instructies op deze pagina wilt volgen, moet u enkele dingen vooraf instellen:

1. U moet de Azure PowerShell- [module](/powershell/azure/install-az-ps) installeren
2. U moet [een actie groep maken of opnieuw gebruiken](../azure-monitor/alerts/action-groups.md) die is geconfigureerd om u op de hoogte te stellen

## <a name="instructions"></a>Instructies
1. Meld u met behulp van Power shell aan bij Azure met uw account en selecteer het abonnement waarmee u wilt communiceren

    ```azurepowershell
    Login-AzAccount
    Select-AzSubscription -Subscription <subscriptionId>
    ```

    > U kunt gebruiken `Get-AzSubscription` om de abonnementen weer te geven waartoe u toegang hebt.

2. De volledige Azure Resource Manager-ID voor uw actie groep zoeken en opslaan

    ```azurepowershell
    (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id
    ```

3. Een resource manager-sjabloon maken en opslaan voor Resource Health waarschuwingen als `resourcehealthalert.json` ([Zie de details hieronder](#resource-manager-template-options-for-resource-health-alerts))

4. Een nieuwe Azure Resource Manager-implementatie met behulp van deze sjabloon maken

    ```azurepowershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>
    ```

5. U wordt gevraagd om de naam van de waarschuwing en de resource-ID van de actie groep die u eerder hebt gekopieerd in te voeren:

    ```azurepowershell
    Supply values for the following parameters:
    (Type !? for Help.)
    activityLogAlertName: <Alert Name>
    actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>
    ```

6. Als alles goed werkte, ontvangt u een bevestiging in Power shell

    ```output
    DeploymentName          : ExampleDeployment
    ResourceGroupName       : <resourceGroup>
    ProvisioningState       : Succeeded
    Timestamp               : 11/8/2017 2:32:00 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                            Name                     Type       Value
                            ===============          =========  ==========
                            activityLogAlertName     String     <Alert Name>
                            activityLogAlertEnabled  Bool       True
                            actionGroupResourceId    String     /...

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Houd er rekening mee dat als u van plan bent dit proces volledig te automatiseren, u de Resource Manager-sjabloon hoeft te bewerken om niet te vragen naar de waarden in stap 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Opties voor de Resource Manager-sjabloon voor Resource Health waarschuwingen

U kunt deze basis sjabloon gebruiken als uitgangs punt voor het maken van Resource Health-waarschuwingen. Deze sjabloon werkt als geschreven en registreert u voor het ontvangen van waarschuwingen voor alle nieuw geactiveerde resource status gebeurtenissen voor alle resources in een abonnement.

> Onder in dit artikel hebt u ook een complexere waarschuwings sjabloon opgenomen waarmee u het signaal tot ruis verhouding voor Resource Health waarschuwingen in vergelijking met deze sjabloon kunt verhogen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Een brede waarschuwing zoals deze wordt doorgaans niet aanbevolen. Meer informatie over hoe we deze melding kunnen beperken tot de gebeurtenissen die hieronder worden besproken.

### <a name="adjusting-the-alert-scope"></a>Het waarschuwings bereik aanpassen

Resource Health waarschuwingen kunnen worden geconfigureerd voor het bewaken van gebeurtenissen in drie verschillende bereiken:

 * Abonnements niveau
 * Niveau van de resource groep
 * Resource niveau

De waarschuwings sjabloon is geconfigureerd op het abonnements niveau, maar als u uw waarschuwing zo wilt configureren dat u alleen over bepaalde resources of resources in een bepaalde resource groep hoeft te worden gewaarschuwd, hoeft u alleen maar de `scopes` sectie in de bovenstaande sjabloon te wijzigen.

Voor een bereik van een resource groeps niveau moet de sectie bereiken er als volgt uitzien:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

En voor een bereik op resource niveau moet de sectie bereik er als volgt uitzien:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Bijvoorbeeld: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> U kunt naar de Azure-portal gaan en de URL bekijken bij het weer geven van uw Azure-resource om deze teken reeks op te halen.

### <a name="adjusting-the-resource-types-which-alert-you"></a>De resource typen aanpassen die u waarschuwen

Waarschuwingen op het niveau van het abonnement of de resource groep kunnen verschillende soorten resources hebben. Als u wilt beperken dat waarschuwingen alleen afkomstig zijn uit een bepaalde subset van resource typen, kunt u die in de `condition` sectie van de sjabloon als volgt definiëren:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Hier gebruiken we de `anyOf` wrapper om de resource status waarschuwing te laten overeenkomen met een van de voor waarden die we opgeven, waardoor er waarschuwingen kunnen worden gegeven die gericht zijn op specifieke resource typen.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>De Resource Health gebeurtenissen aanpassen waarmee u wordt gewaarschuwd
Wanneer resources een status gebeurtenis ondergaan, kunnen ze een reeks fasen door lopen die de status van de status gebeurtenis: `Active` , `In Progress` , en `Updated` `Resolved` .

U wilt mogelijk alleen een melding ontvangen wanneer een resource een slechte status krijgt, in welk geval u de waarschuwing zo wilt configureren dat er alleen een melding wordt weer gegeven wanneer het `status` is `Active` . Als u echter ook wilt worden gewaarschuwd voor de andere fasen, kunt u deze gegevens als volgt toevoegen:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Als u wilt worden gewaarschuwd voor alle vier stadia van de status gebeurtenissen, kunt u deze voor waarde allemaal tegelijk verwijderen en ontvangt u een melding van de `status` eigenschap.

> [!NOTE]
> Elke sectie ' anyOf ' mag slechts één veld type waarden bevatten.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>De Resource Health-waarschuwingen aanpassen om "onbekende" gebeurtenissen te voor komen

Azure Resource Health kunt u de meest recente status van uw resources rapporteren door deze voortdurend te controleren met behulp van test lopers. De relevante statussen van de gerapporteerde integriteit zijn: ' beschikbaar ', ' niet beschikbaar ' en ' gedegradeerd '. In situaties waarin de loper en de Azure-resource niet kunnen communiceren, wordt de status ' onbekend ' gerapporteerd voor de resource en die als een status gebeurtenis ' actief ' wordt beschouwd.

Wanneer een resource echter "onbekend" rapporteert, is het waarschijnlijk dat de status ervan niet is gewijzigd sinds het laatste nauw keurig rapport. Als u waarschuwingen voor onbekende gebeurtenissen wilt uitschakelen, kunt u die logica opgeven in de sjabloon:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

In dit voor beeld melden we alleen op gebeurtenissen waarbij de huidige en vorige status niet ' onbekend ' zijn. Deze wijziging kan een nuttige aanvulling zijn als uw waarschuwingen rechtstreeks naar uw mobiele telefoon of e-mail bericht worden verzonden. 

Houd er rekening mee dat de eigenschappen currentHealthStatus en previousHealthStatus in sommige gebeurtenissen null moeten zijn. Als er bijvoorbeeld een bijgewerkte gebeurtenis optreedt, is het waarschijnlijk dat de integriteits status van de resource sinds het laatste rapport niet is gewijzigd, alleen dat er aanvullende informatie over de gebeurtenis beschikbaar is (bijvoorbeeld oorzaak). Daarom kan het gebruik van de bovenstaande component ertoe leiden dat sommige waarschuwingen niet worden geactiveerd omdat de waarden van eigenschappen. currentHealthStatus en Properties. previousHealthStatus worden ingesteld op null.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>De waarschuwing aanpassen om te voor komen dat door de gebruiker gestarte gebeurtenissen

Resource Health gebeurtenissen kunnen worden geactiveerd door het platform dat is gestart en door de gebruiker gestarte gebeurtenissen. Het kan zinvol zijn om alleen een melding te verzenden wanneer de status gebeurtenis wordt veroorzaakt door het Azure-platform.

Het is eenvoudig om uw waarschuwing zo te configureren dat deze alleen wordt gefilterd op de volgende soorten gebeurtenissen:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Houd er rekening mee dat het veld oorzaak null in sommige gebeurtenissen kan zijn. Dat wil zeggen dat er een status overgang plaatsvindt (bijvoorbeeld beschikbaar voor niet-beschikbaar) en dat de gebeurtenis onmiddellijk wordt geregistreerd om meldings vertragingen te voor komen. Als u de bovenstaande component gebruikt, kan dit ertoe leiden dat een waarschuwing niet wordt geactiveerd, omdat de waarde van de eigenschap Properties. component wordt ingesteld op null.

## <a name="complete-resource-health-alert-template"></a>Sjabloon voor Resource Health waarschuwingen volt ooien

Op basis van de verschillende aanpassingen die in de vorige sectie zijn beschreven, ziet u hier een voorbeeld sjabloon die is geconfigureerd om het signaal tot ruis verhouding te maximaliseren. Houd rekening met de hierboven vermelde aanvullende voor waarden waar de currentHealthStatus-, previousHealthStatus-en veroorzakende eigenschapwaarden in sommige gebeurtenissen Null kunnen zijn.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

U kunt echter het beste bepalen welke configuraties effectief zijn, dus gebruik de hulpprogram ma's voor u in deze documentatie om uw eigen aanpassing te maken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Resource Health:
-  [Overzicht van Azure Resource Health](Resource-health-overview.md)
-  [Resourcetypen en statuscontroles die beschikbaar zijn via Azure Resource Health](resource-health-checks-resource-types.md)


Service Health-waarschuwingen maken:
-  [Waarschuwingen voor Service Health configureren](./alerts-activity-log-service-notifications-portal.md) 
-  [Azure-gebeurtenisschema in het activiteitenlogboek](../azure-monitor/essentials/activity-log-schema.md)
