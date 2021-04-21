---
title: Updatebeheer inschakelen met behulp van Azure Resource Manager-sjabloon
description: In dit artikel wordt beschreven hoe u een Azure Resource Manager gebruikt om een Updatebeheer.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 09/18/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 687c3d49f98fe6832d23dc1529a9761d862e0666
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830876"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Updatebeheer inschakelen met behulp van Azure Resource Manager-sjabloon

U kunt een Azure Resource Manager [gebruiken om](../../azure-resource-manager/templates/template-syntax.md) de functie Azure Automation Updatebeheer in uw resourcegroep in te stellen. Dit artikel bevat een voorbeeldsjabloon die het volgende automatiseert:

* Automatisch maken van een Azure Monitor Log Analytics-werkruimte.
* Automatisch maken van een Azure Automation-account.
* Automation-account koppelen aan de Log Analytics-werkruimte.
* Automation-runbooks toevoegen aan het account.
* Hiermee schakelt u Updatebeheer functie in.

De sjabloon automatiseert het inschakelen van Updatebeheer op een of meer Azure- of niet-Azure-VM's.

Als u al een Log Analytics-werkruimte en Automation-account hebt geïmplementeerd in een ondersteunde regio in uw abonnement, zijn deze niet gekoppeld. Met behulp van deze sjabloon wordt de koppeling gemaakt en Updatebeheer.

>[!NOTE]
>Als u een ARM-sjabloon gebruikt, wordt het maken van een Uitvoeren als-account voor Automation niet ondersteund. Zie Uitvoeren als-account maken als u handmatig een [](../create-run-as-account.md)Uitvoeren als-account wilt maken vanuit de portal of met PowerShell.

Nadat u deze stappen hebt voltooid, moet u [diagnostische instellingen configureren](../automation-manage-send-joblogs-log-analytics.md) voor uw Automation-account om de taakstatus van de runbook en taakstromen te verzenden naar de gekoppelde Log Analytics-werkruimte.

## <a name="api-versions"></a>API-versies

De volgende tabel geeft een overzicht van de API-versie voor de resources die in dit voorbeeld worden gebruikt.

| Resource | Resourcetype | API-versie |
|:---|:---|:---|
| [Werkruimte](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Automation-account](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [Services gekoppeld aan werkruimte](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |
| [Oplossingen](/azure/templates/microsoft.operationsmanagement/solutions) | oplossingen | 11-01-preview van 2015 |

## <a name="before-using-the-template"></a>Voordat u de sjabloon gebruikt

De JSON-sjabloon is geconfigureerd om u om het volgende te vragen:

* De naam van de werkruimte.
* De regio waarin u de werkruimte wilt maken.
* De naam van het Automation-account.
* De regio waarin u het Automation-account wilt maken.

De volgende parameters in de sjabloon hebben een standaardwaarde voor de Log Analytics-werkruimte:

* *sku* wordt standaard ingesteld op de prijscategorie per GB die is geïntroduceerd in het prijsmodel van april 2018.
* *dataRetention* is standaard ingesteld op 30 dagen.

>[!WARNING]
>Als u een Log Analytics-werkruimte wilt maken of configureren in een abonnement dat is gekoppeld aan het prijsmodel van april 2018, is de enige mogelijke Log Analytics-prijscategorie *PerGB2018*.
>

De JSON-sjabloon bevat standaardwaarden voor de andere parameters die waarschijnlijk worden gebruikt in een standaardconfiguratie in uw omgeving. U kunt de sjabloon opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie. Zie [Resources implementeren met ARM-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md) voor meer informatie over het werken met sjablonen.

Als u geen ervaring hebt met Azure Automation en Azure Monitor, is het belangrijk dat u de volgende configuratiedetails begrijpt. Deze kennis helpt fouten te voorkomen bij het maken, configureren en gebruiken van een Log Analytics werkruimte die aan uw nieuwe Automation-account is gekoppeld.

* Bekijk deze [aanvullende informatie](../../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) om de configuratieopties voor de werkruimte volledig te begrijpen, zoals de modus voor toegangsbeheer, de prijscategorie, retentie en het capaciteitsreserveringsniveau.

* Bekijk [werkruimtetoewijzingen](../how-to/region-mappings.md) om de ondersteunde regio's inline of in een parameterbestand op te geven. Bij het koppelen van een Log Analytics-werkruimte aan een Automation-Account in uw abonnement, worden slechts bepaalde regio's ondersteund.

* Als u geen ervaring hebt met Azure Monitor-logboeken en nog niet eerder een werkruimte hebt geïmplementeerd, raadpleegt u de [richtlijnen voor het ontwerpen van werkruimten](../../azure-monitor/logs/design-logs-deployment.md). U weet dan meer over toegangsbeheer en hebt een beter beeld van de strategieën voor de implementatie van ontwerpen die worden aanbevolen voor uw organisatie.

## <a name="deploy-template"></a>Sjabloon implementeren

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                    "description": "Workspace name"
                }
            },
            "sku": {
                "type": "string",
                "allowedValues": [
                    "pergb2018",
                    "Free",
                    "Standalone",
                    "PerNode",
                    "Standard",
                    "Premium"
                ],
                "defaultValue": "pergb2018",
                "metadata": {
                    "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
                }
            },
            "dataRetention": {
                "type": "int",
                "defaultValue": 30,
                "minValue": 7,
                "maxValue": 730,
                "metadata": {
                    "description": "Number of days to retain data."
                }
            },
            "location": {
                "type": "string",
                "defaultValue": "[resourceGroup().location]",
                "metadata": {
                    "description": "Specifies the location in which to create the workspace."
                }
            },
            "automationAccountName": {
                "type": "string",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "automationAccountLocation": {
                "type": "string",
                "metadata": {
                    "description": "Specifies the location in which to create the Automation account."
                }
            },
            "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
                }
            }
        },
        "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
          }
        },
        "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "location": "[parameters('location')]",
                "name": "[variables('Updates').name]",
                "type": "Microsoft.OperationsManagement/solutions",
                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                },
                "plan": {
                    "name": "[variables('Updates').name]",
                    "publisher": "Microsoft",
                    "promotionCode": "",
                    "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                }
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
                "properties": {
                    "sku": {
                        "name": "Basic"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. Bewerk de sjabloon om aan uw vereisten te voldoen. Overweeg om een [Resource Manager parameterbestand te maken](../../azure-resource-manager/templates/parameter-files.md) in plaats van parameters door te geven als inlinewaarden.

3. Sla dit bestand op in een lokale mapdeployUMSolutiontemplate.js **op**.

4. U kunt deze sjabloon nu implementeren. U kunt PowerShell of de Azure CLI gebruiken. Wanneer u wordt gevraagd om een werkruimte en een Automation-accountnaam, geeft u een naam op die wereldwijd uniek is voor alle Azure-abonnementen.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure-CLI**

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    De implementatie kan enkele minuten duren. Wanneer dit is bereikt, ziet u een bericht dat lijkt op het volgende bericht dat het resultaat bevat:

    ![Voorbeeld van uitvoer als de implementatie is voltooid](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Open in Azure Portal het Automation-account dat u hebt gemaakt.

3. Selecteer **Runbooks** in het linkerdeelvenster. Op de pagina **Runbooks** worden drie zelfstudie-runbooks vermeld die zijn gemaakt met het Automation-account.

    ![Zelfstudie-runbooks die zijn gemaakt met het Automation-account](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Selecteer in het linkerdeel venster **Gekoppelde werkruimte**. Op de pagina **Gekoppelde werkruimte** wordt de Log Analytics werkruimte weergegeven die u eerder hebt opgegeven en die nu is gekoppeld aan uw Automation-account.

    ![Automation-account gekoppeld aan de Log Analytics-werkruimte](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. Selecteer Updatebeheer in het **linkerdeelvenster.** Op de **pagina Updatebeheer** wordt de evaluatiepagina weergegeven zonder enige informatie omdat deze alleen is ingeschakeld en computers niet zijn geconfigureerd voor beheer.

    ![Updatebeheer functie-evaluatieweergave](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u de **oplossing Updates** in de Log Analytics-werkruimte, ontkoppelt u het Automation-account van de werkruimte en verwijdert u vervolgens het Automation-account en de werkruimte.

## <a name="next-steps"></a>Volgende stappen

* Zie Updates en patches Updatebeheer voor uw VM's beheren voor meer informatie over het [gebruik van VM's.](manage-updates-for-vm.md)

* Zie de instructies in Functie verwijderen als Updatebeheer niet meer wilt gebruiken en [Updatebeheer verwijderen.](remove-feature.md)

* Als u VM's uit Updatebeheer wilt verwijderen, raadpleegt u [VM's uit Updatebeheer verwijderen](remove-vms.md).
