---
title: 'Zelfstudie: onboarding van resources'
description: Door het onboarden van resources via aangepaste providers kunt u bestaande Azure-resources bewerken en uitbreiden.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75649937"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Zelfstudie: onboarding van resources met aangepaste providers van Azure

In deze zelfstudie implementeert u in Azure een aangepaste resourceprovider die de API van Azure Resource Manager uitbreidt met het resourcetype Microsoft.CustomProviders/associations. De zelfstudie laat zien hoe u bestaande resources uitbreidt die zich buiten de resourcegroep bevinden, waar het exemplaar van de aangepaste provider zich bevindt. In deze zelfstudie maakt de aangepaste resourceprovider gebruik van een logische app van Azure, maar u kunt elk openbaar API-eindpunt gebruiken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet u het volgende weten:

* De mogelijkheden van [aangepaste Azure-providers](overview.md).
* Basiskennis van [onboarding van resources met aangepaste providers](concepts-resource-onboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Aan de slag met het onboarden van resources

In deze zelfstudie moeten twee onderdelen worden geïmplementeerd: de aangepaste provider en de koppeling. Om het proces te vereenvoudigen, kunt u desgewenst één sjabloon gebruiken waarmee beide onderdelen worden geïmplementeerd.

De sjabloon maakt gebruik van deze resources:

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/workflows
* Microsoft.CustomProviders/associations

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>De infrastructuur van de aangepaste provider implementeren

In het eerste deel van de sjabloon wordt de infrastructuur van de aangepaste provider geïmplementeerd. Deze infrastructuur definieert het effect van de koppelingsresource. Als u niet bekend bent met aangepaste providers, leest u eerst [Azure Custom Resource Providers Overview](overview.md) (Overzicht van aangepaste Azure-resourceproviders).

Laten we de infrastructuur van de aangepaste provider implementeren. U kunt de sjabloon kopiëren, opslaan en implementeren, of meedoen en de infrastructuur implementeren met behulp van Azure Portal.

1. Ga naar [Azure Portal](https://portal.azure.com).

2. Zoek naar **sjablonen** in **Azure-services** of via het zoekvak:

   ![Sjablonen zoeken](media/tutorial-resource-onboarding/templates.png)

3. Selecteer **Toevoegen** in het deelvenster **Sjablonen**:

   ![Selecteer Toevoegen](media/tutorial-resource-onboarding/templatesadd.png)

4. Geef onder **Algemeen** waarden op voor **Naam** en **Beschrijving** voor de nieuwe sjabloon:

   ![Naam en beschrijving van sjabloon](media/tutorial-resource-onboarding/templatesdescription.png)

5. Maak de Resource Manager-sjabloon door de JSON-sjabloon te kopiëren vanuit de sectie 'Aan de slag met het onboarden van resources' in dit artikel:

   ![Een Resource Manager-sjabloon maken](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. Selecteer **Toevoegen** om de sjabloon te maken. Als de nieuwe sjabloon niet wordt weergegeven, selecteert u **Vernieuwen**.

7. Selecteer de sjabloon die u zojuist hebt gemaakt en selecteer **Implementeren**:

   ![Selecteer de nieuwe sjabloon en selecteer vervolgens Implementeren](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Voer de instellingen voor de vereiste velden in en selecteer vervolgens het abonnement en de resourcegroep. U kunt het selectievakje **Id aangepaste resourceprovider** leeg laten.

   | Naam van de instelling | Vereist? | Beschrijving |
   | ------------ | -------- | ----------- |
   | Locatie | Ja | De locatie voor de resources in de sjabloon. |
   | Naam van logische app | No | De naam van de logische app. |
   | Naam van aangepaste resourceprovider | No | De naam van de aangepaste resourceprovider. |
   | Id van aangepaste resourceprovider | No | Een bestaande aangepaste resourceprovider die ondersteuning biedt voor de koppelingsresource. Als u hier een waarde opgeeft, worden de implementatie van de logische app en de aangepaste provider overgeslagen. |
   | Naam van koppeling | No | De naam van de koppelingsresource. |

   Voorbeeldparameters:

   ![Sjabloonparameters invoeren](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Ga naar de implementatie en wacht tot deze voltooid is. Er verschijnt een scherm dat er ongeveer als volgt uitziet. U ziet de nieuwe koppelingsresource als uitvoer:

   ![Geslaagde implementatie](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Hier ziet u de resourcegroep, met **Verborgen items weergeven** geselecteerd:

   ![Implementatie van aangepaste provider](media/tutorial-resource-onboarding/showhidden.png)

10. Bekijk het tabblad **Uitvoeringsgeschiedenis** van de logische app om de aanroepen te zien voor het maken van de koppeling:

    ![Uitvoeringsgeschiedenis van logische app](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Aanvullende koppelingen implementeren

Nadat de infrastructuur van de aangepaste provider is ingesteld, kunt u eenvoudig meer koppelingen implementeren. De resourcegroep voor aanvullende koppelingen hoeft niet dezelfde te zijn als de resourcegroep waar u de infrastructuur van de aangepaste provider hebt geïmplementeerd. Als u een koppeling wilt maken, moet u de machtigingen Microsoft.CustomProviders/resourceproviders/write hebben voor de id van de opgegeven aangepaste resourceprovider.

1. Ga naar de resource **Microsoft.CustomProviders/resourceProviders** van de aangepaste provider in de resourcegroep van de vorige implementatie. U moet het selectievakje **Verborgen typen weergeven** inschakelen:

   ![Ga naar de resource](media/tutorial-resource-onboarding/showhidden.png)

2. Kopieer de eigenschap Resource-id van de aangepaste provider.

3. Zoek naar **sjablonen** in **Azure-services** of via het zoekvak:

   ![Sjablonen zoeken](media/tutorial-resource-onboarding/templates.png)

4. Selecteer de sjabloon die u eerder hebt gemaakt en selecteer vervolgens **Implementeren**:

   ![Selecteer de sjabloon die u eerder hebt gemaakt en selecteer vervolgens Implementeren](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Voer de instellingen voor de vereiste velden in en selecteer vervolgens het abonnement en een andere resourcegroep. Voer voor de instelling **Id aangepaste resourceprovider** de resource-id in die u eerder hebt gekopieerd uit de aangepaste provider die u eerder hebt geïmplementeerd.

6. Ga naar de implementatie en wacht tot deze voltooid is. Nu moet alleen de resource voor de nieuwe koppeling worden geïmplementeerd:

   ![Resource voor nieuwe koppeling](media/tutorial-resource-onboarding/createdassociationresource.png)

Als u wilt, kunt u teruggaan naar de **uitvoeringsgeschiedenis** van de logische app en dan ziet u dat er een andere aanroep is gedaan naar de logische app. U kunt de logische app bijwerken om extra functionaliteit toe te voegen voor elke gemaakte koppeling.

## <a name="getting-help"></a>Ondersteuning vragen

Als u vragen hebt over aangepaste Azure-providers, kunt u deze stellen op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Misschien is een soortgelijke vraag al eerder gesteld en beantwoord, dus controleer dit eerst vóór u een vraag stelt. Voeg de tag `azure-custom-providers` toe om snel antwoord te krijgen.

