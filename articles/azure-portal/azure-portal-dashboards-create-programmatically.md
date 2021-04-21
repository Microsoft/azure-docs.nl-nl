---
title: Programmatisch Azure-dashboards maken
description: Gebruik een dashboard in de Azure Portal als een sjabloon om op programmatische wijze Azure-dashboards te maken. Bevat JSON-verwijzing.
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: 416eeb772e347b28fcb4a4dcc93c746562ea3571
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767055"
---
# <a name="programmatically-create-azure-dashboards"></a>Programmatisch Azure-dashboards maken

Dit artikel helpt u bij het programmatisch maken en publiceren van Azure-dashboards. In het hele document wordt verwezen naar het dashboard dat hieronder wordt weergegeven.

![voorbeelddashboard](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Overzicht

Gedeelde dashboards in de [Azure Portal](https://portal.azure.com) zijn [resources,](../azure-resource-manager/management/overview.md) net als virtuele machines en opslagaccounts. U kunt resources programmatisch beheren met behulp van de [Azure Resource Manager REST API's,](/rest/api/) [de Azure CLI](/cli/azure)en Azure PowerShell [opdrachten](/powershell/azure/get-started-azureps).

Veel functies zijn gebaseerd op deze API's om resourcebeheer eenvoudiger te maken. Elk van deze API's en hulpprogramma's biedt manieren om resources te maken, weer te geven, op te halen, te wijzigen en te verwijderen. Omdat dashboards resources zijn, kunt u uw favoriete API of hulpprogramma kiezen om te gebruiken.

Welke hulpprogramma's u ook gebruikt om een dashboard programmatisch te maken, u maakt een JSON-weergave van uw dashboardobject. Dit object bevat informatie over de tegels op het dashboard. Het bevat grootten, posities, resources waar ze aan zijn gebonden en eventuele aanpassingen van gebruikers.

De meest praktische manier om dit JSON-document op te bouwen, is met de Azure Portal. U kunt uw tegels interactief toevoegen en plaatsen. Exporteert vervolgens de JSON en maakt een sjabloon op basis van het resultaat voor later gebruik in scripts, programma's en implementatiehulpprogramma's.

## <a name="create-a-dashboard"></a>Een dashboard maken

Als u een dashboard wilt maken, **selecteert** u Dashboard in [Azure Portal](https://portal.azure.com) menu en selecteert u **vervolgens Nieuw dashboard.**

![opdracht nieuw dashboard](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Gebruik de tegelgalerie om tegels te zoeken en toe te voegen. Tegels worden toegevoegd door ze te slepen en te verwijderen. Sommige tegels ondersteunen het formaat van tegels met behulp van een sleepgrepen.

![greep slepen om grootte te wijzigen](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Andere hebben vaste grootten om uit te kiezen in hun contextmenu.

![contextmenu grootten om grootte te wijzigen](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Het dashboard delen

Nadat u het dashboard hebt geconfigureerd, bestaat de volgende stap uit het publiceren van het dashboard met behulp van de **opdracht** Delen.

![een dashboard delen](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Als **u Delen** selecteert, wordt u gevraagd naar welk abonnement en welke resourcegroep u wilt publiceren. U moet schrijftoegang hebben tot het abonnement en de resourcegroep die u kiest. Zie Azure-rollen toewijzen met [behulp van de Azure Portal.](../role-based-access-control/role-assignments-portal.md)

![wijzigingen aanbrengen in delen en toegang](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>De JSON-weergave van het dashboard ophalen

Het publiceren duurt slechts enkele seconden. Wanneer dit is gebeurd, is de volgende stap het ophalen van de JSON met behulp van de **opdracht** Downloaden.

![JSON-weergave downloaden](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Een sjabloon maken op basis van de JSON

De volgende stap is het maken van een sjabloon op basis van deze JSON. Gebruik deze sjabloon programmatisch met de juiste API's voor resourcebeheer, opdrachtregelprogramma's of binnen de portal.

U hoeft de JSON-structuur van het dashboard niet volledig te begrijpen om een sjabloon te maken. In de meeste gevallen wilt u de structuur en configuratie van elke tegel behouden. Gebruik vervolgens parameters voor de set Azure-resources waar de tegels naar wijzen. Bekijk uw geëxporteerde JSON-dashboard en zoek alle exemplaren van Azure-resource-ID's. Ons voorbeelddashboard heeft meerdere tegels die allemaal op één virtuele Azure-machine wijzen. Dat komt doordat ons dashboard alleen naar deze ene resource kijkt. Als u de voorbeeld-JSON, die aan het einde van het document is opgenomen, zoekt naar '/subscriptions', vindt u verschillende exemplaren van deze id.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Als u dit dashboard in de toekomst wilt publiceren voor een virtuele machine, moet u een parameter opgeven voor elk exemplaar van deze tekenreeks in de JSON.

Er zijn twee benaderingen voor API's die resources maken in Azure:

* Imperatieve API's maken één resource tegelijk. Zie Resources voor [meer informatie.](/rest/api/resources/resources)
* Een implementatiesysteem op basis van sjablonen dat meerdere, afhankelijke resources maakt met één API-aanroep. Zie Deploy [resources with Resource Manager templates and Azure PowerShell (Resources implementeren met sjablonen en Azure PowerShell) voor meer Azure PowerShell.](../azure-resource-manager/templates/deploy-powershell.md)

Implementatie op basis van sjablonen ondersteunt parameterisering en sjablonen. We gebruiken deze benadering in dit artikel.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programmatisch een dashboard maken op basis van uw sjabloon met behulp van een sjabloonimplementatie

Azure biedt de mogelijkheid om de implementatie van meerdere resources te beheren. U maakt een implementatiesjabloon die de set resources uitdrukken die moeten worden geïmplementeerd en de relaties ertussen.  De JSON-indeling van elke resource is hetzelfde als wanneer u ze één voor één maakt. Het verschil is dat de sjabloontaal een aantal concepten toevoegt, zoals variabelen, parameters, basisfuncties en meer. Deze uitgebreide syntaxis wordt alleen ondersteund in de context van een sjabloonimplementatie. Het werkt niet als het wordt gebruikt met de imperatieve API's die eerder zijn besproken. Zie Understand the structure and syntax of Azure Resource Manager templates (Inzicht in de structuur en [syntaxis van Azure Resource Manager sjablonen) voor meer informatie.](../azure-resource-manager/templates/template-syntax.md)

Parameterisering moet worden uitgevoerd met behulp van de parametersyntaxis van de sjabloon.  U vervangt alle exemplaren van de resource-id die we eerder hebben gevonden, zoals hier wordt weergegeven.

Voorbeeld van een JSON-eigenschap met in code gecodeerde resource-id:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Voorbeeld van een JSON-eigenschap die is geconverteerd naar een geparameteriseerde versie op basis van sjabloonparameters

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Declareer de vereiste metagegevens van de sjabloon en de parameters bovenaan de JSON-sjabloon als de volgende:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```
Zodra u de sjabloon hebt geconfigureerd, implementeert u deze met een van de volgende methoden:

* [REST-API's](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure-CLI](/cli/azure/group/deployment#az_group_deployment_create)
* [De pagina Azure Portal sjabloonimplementatie](https://portal.azure.com/#create/Microsoft.Template)

Vervolgens ziet u twee versies van ons voorbeelddashboard JSON. De eerste is de versie die we hebben geëxporteerd vanuit de portal die al aan een resource was gebonden. De tweede is de sjabloonversie die programmatisch kan worden gebonden aan elke virtuele machine en kan worden geïmplementeerd met behulp van Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON-weergave van ons voorbeelddashboard vóór templating

In dit voorbeeld ziet u wat u kunt verwachten als u dit artikel hebt gevolgd. Met de instructies is de JSON-weergave geëxporteerd van een dashboard dat al is geïmplementeerd. De in code gecodeerde resource-id's geven aan dat dit dashboard verwijst naar een specifieke virtuele Azure-machine.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Sjabloonweergave van ons voorbeelddashboard

De sjabloonversie van het dashboard heeft drie parameters gedefinieerd met de `virtualMachineName` naam `virtualMachineResourceGroup` , en `dashboardName` .  Met de parameters kunt u dit dashboard telkens wanneer u implementeert naar een andere virtuele Azure-machine laten wijzen. Dit dashboard kan programmatisch worden geconfigureerd en geïmplementeerd om te wijzen naar elke virtuele Azure-machine. Als u deze functie wilt testen, kopieert u de volgende sjabloon en plakt u deze in [de pagina Azure Portal sjabloonimplementatie.](https://portal.azure.com/#create/Microsoft.Template)

In dit voorbeeld wordt een dashboard zelf geïmplementeerd, maar met de sjabloontaal kunt u meerdere resources implementeren en een of meer dashboards tegelijk bundelen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

Nu u een voorbeeld hebt gezien van het gebruik van een geparameteriseerde sjabloon voor het implementeren van een dashboard, kunt u proberen de sjabloon te implementeren met behulp van [de Azure Resource Manager REST](/rest/api/)API's, de Azure [CLI](/cli/azure)of [Azure PowerShell opdrachten](/powershell/azure/get-started-azureps).

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Programmatisch een dashboard maken met behulp van Azure CLI

Bereid uw omgeving voor op Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- In deze voorbeelden wordt het volgende dashboard gebruikt: [portal-dashboard-template-testvm.jsop](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Vervang inhoud tussen vierkante haken door uw waarden.

Voer de [opdracht az portal dashboard create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) uit om een dashboard te maken:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

U kunt een dashboard bijwerken met behulp van de opdracht [az portal dash board update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update):

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Bekijk de details van een dashboard door de opdracht [az portal dashboard show uit te](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) voeren:

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Als u alle dashboards voor het huidige abonnement wilt zien, gebruikt u [az portal dash board List](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

U kunt ook alle dashboards voor een resourcegroep bekijken:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Zie Manage Azure Portal settings and preferences (Instellingen en voorkeuren beheren) [voor meer informatie over desktops.](set-preferences.md)

Zie [az portal dash board](/cli/azure/ext/portal/portal/dashboard) voor meer informatie over Azure CLI-ondersteuning voor dashboards.
