---
title: Programmatisch Azure-Dash boards maken
description: Gebruik een dash board in de Azure Portal als sjabloon om programmatisch Azure-Dash boards te maken. Bevat een JSON-verwijzing.
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: bd56dc1c729c5aa7a77e79aa3af3366166fdcfea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095167"
---
# <a name="programmatically-create-azure-dashboards"></a>Programmatisch Azure-Dash boards maken

Dit artikel begeleidt u stapsgewijs door het proces van het programmatisch maken en publiceren van Azure-Dash boards. In het volgende voor beeld wordt in het document verwezen naar het dash board.

![voorbeelddashboard](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Overzicht

Gedeelde Dash boards in de [Azure Portal](https://portal.azure.com) zijn [bronnen](../azure-resource-manager/management/overview.md) net als virtuele machines en opslag accounts. U kunt bronnen programmatisch beheren met behulp van de [Azure Resource Manager rest api's](/rest/api/), de [Azure CLI](/cli/azure)en de [Azure PowerShell-opdrachten](/powershell/azure/get-started-azureps).

Veel functies bouwen op deze Api's om het bronnen beheer eenvoudiger te maken. Elk van deze Api's en hulpprogram ma's biedt manieren om resources te maken, weer te geven, op te halen, te wijzigen en te verwijderen. Omdat Dash boards resources zijn, kunt u uw favoriete API of het hulp programma kiezen die u wilt gebruiken.

Welke hulpprogram ma's u gebruikt om een dash board programmatisch te maken, bouwt u een JSON-weer gave van uw dashboard object. Dit object bevat informatie over de tegels op het dash board. Dit omvat maten, posities, resources waaraan ze zijn gebonden en eventuele aanpassingen van de gebruiker.

De meest praktische manier om dit JSON-document samen te stellen is door gebruik te maken van de Azure Portal. U kunt tegels interactief toevoegen en positioneren. Vervolgens exporteert u de JSON en maakt u een sjabloon van het resultaat voor later gebruik in scripts, Program ma's en implementatie hulpprogramma's.

## <a name="create-a-dashboard"></a>Een dashboard maken

Als u een dash board wilt maken, selecteert u **dash board** in het menu [Azure Portal](https://portal.azure.com) en selecteert u vervolgens **Nieuw dash board**.

![nieuwe dashboard opdracht](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Gebruik de tegel galerie om tegels te zoeken en toe te voegen. Tegels worden toegevoegd door ze te slepen en neer te zetten. Sommige tegels ondersteunen het wijzigen van de grootte met behulp van een sleep greep.

![greep slepen om grootte te wijzigen](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Anderen hebben vaste groottes die u kunt kiezen in het context menu.

![context menu met grootten om grootte te wijzigen](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Het dash board delen

Nadat u het dash board hebt geconfigureerd, is de volgende stap het publiceren van het dash board met behulp van de opdracht **delen** .

![een dash board delen](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Als u **delen** selecteert, wordt u gevraagd welk abonnement en welke resource groep u wilt publiceren. U moet schrijf toegang hebben tot het abonnement en de resource groep die u kiest. Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

![wijzigingen aanbrengen in delen en toegang](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>De JSON-weer gave van het dash board ophalen

Het publiceren duurt slechts enkele seconden. Wanneer u klaar bent, is de volgende stap het ophalen van de JSON met behulp van de **Download** opdracht.

![JSON-weer gave downloaden](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Een sjabloon maken op basis van de JSON

De volgende stap is het maken van een sjabloon op basis van deze JSON. Gebruik deze sjabloon via een programma met de juiste resource management-Api's, opdracht regel Programma's of binnen de portal.

U hoeft niet volledig inzicht te krijgen in de JSON-structuur van het dash board om een sjabloon te maken. In de meeste gevallen wilt u de structuur en configuratie van elke tegel behouden. Vervolgens para meters de set met Azure-resources waarnaar de tegels verwijzen. Bekijk het geëxporteerde JSON-dash board en Zoek alle exemplaren van Azure-resource-Id's. Het dash board voor beeld bevat meerdere tegels die allemaal op één virtuele Azure-machine staan. Dit komt doordat in het dash board alleen naar deze afzonderlijke resource wordt gezocht. Als u de voor beeld-JSON doorzoekt, die aan het einde van het document is opgenomen, kunt u voor '/Subscriptions ' verschillende exemplaren van deze ID vinden.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Als u dit dash board voor elke virtuele machine in de toekomst wilt publiceren, moet u elk exemplaar van deze teken reeks in de JSON para meters.

Er zijn twee benaderingen voor Api's die resources maken in Azure:

* Met verplichte Api's maakt u één resource per keer. Zie [resources](/rest/api/resources/resources)voor meer informatie.
* Een implementatie systeem op basis van een sjabloon dat meerdere, afhankelijke resources maakt met één API-aanroep. Zie  [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie.

Implementatie op basis van een sjabloon ondersteunt parameterisering en sjabloon. We gebruiken deze benadering in dit artikel.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programmatisch een dash board maken op basis van uw sjabloon met behulp van een sjabloon implementatie

Azure biedt de mogelijkheid om de implementatie van meerdere resources te organiseren. U maakt een implementatie sjabloon waarmee de set resources wordt ingedrukt die moeten worden geïmplementeerd en de relaties ertussen.  De JSON-indeling van elke resource is hetzelfde als wanneer u ze één voor één maakt. Het verschil is dat in de sjabloon taal enkele concepten worden toegevoegd, zoals variabelen, para meters, basis functies en nog veel meer. Deze uitgebreide syntaxis wordt alleen ondersteund in de context van een sjabloon implementatie. Deze functie werkt niet als deze wordt gebruikt met de verplichte Api's die eerder zijn besproken. Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie.

Parameterisering moet worden uitgevoerd met de parameter syntaxis van de sjabloon.  U vervangt alle exemplaren van de resource-ID die u eerder hebt gevonden, zoals hier wordt weer gegeven.

Voor beeld van JSON-eigenschap met in code vastgelegde resource-ID:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Voor beeld van JSON-eigenschap geconverteerd naar een geparametriseerde versie op basis van sjabloon parameters

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

U moet de vereiste meta gegevens van de sjabloon en de para meters boven aan de JSON-sjabloon als volgt declareren:

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
Nadat u uw sjabloon hebt geconfigureerd, implementeert u deze met een van de volgende methoden:

* [REST-API's](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure-CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [De pagina implementatie van Azure Portal sjabloon](https://portal.azure.com/#create/Microsoft.Template)

Vervolgens ziet u twee versies van de JSON van het voor beeld-dash board. De eerste is de versie die is geëxporteerd uit de portal die al is gebonden aan een resource. De tweede is de sjabloon versie die programmatisch kan worden gebonden aan een virtuele machine en met Azure Resource Manager kan worden geïmplementeerd.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON-weer gave van het voorbeeld dashboard vóór sjabloon

Dit voor beeld laat zien wat u kunt verwachten als u dit artikel hebt gevolgd. De instructies exporteren de JSON-weer gave van een dash board dat al is geïmplementeerd. De in code vastgelegde resource-id's laten zien dat dit dash board naar een specifieke virtuele machine van Azure verwijst.

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

### <a name="template-representation-of-our-example-dashboard"></a>Sjabloon weergave van het dash board van ons voor beeld

In de sjabloon versie van het dash board zijn drie para meters met de naam `virtualMachineName` , `virtualMachineResourceGroup` en gedefinieerd `dashboardName` .  Met de para meters kunt u dit dash board op een andere virtuele Azure-machine laten wijzen elke keer dat u implementeert. Dit dash board kan via een programma worden geconfigureerd en geïmplementeerd om te verwijzen naar elke virtuele machine van Azure. Als u deze functie wilt testen, kopieert u de volgende sjabloon en plakt u deze in de [pagina implementatie van Azure Portal sjabloon](https://portal.azure.com/#create/Microsoft.Template).

In dit voor beeld wordt een dash board op zichzelf geïmplementeerd, maar met de sjabloon taal kunt u meerdere resources implementeren en een of meer Dash boards naast elkaar bundelen.

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

Nu u een voor beeld hebt gezien van het gebruik van een geparametriseerde sjabloon voor het implementeren van een dash board, kunt u proberen de sjabloon te implementeren met behulp van de [Azure Resource Manager rest-api's](/rest/api/), de [Azure CLI](/cli/azure)of de [Azure PowerShell-opdrachten](/powershell/azure/get-started-azureps).

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Programmatisch een dash board maken met behulp van Azure CLI

Bereid uw omgeving voor op Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- In deze voor beelden wordt het volgende dash board gebruikt: [portal-dashboard-template-testvm.jsop](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Vervang de inhoud tussen vier Kante haken door uw waarden.

Voer de opdracht [AZ Portal dash board Create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) uit om een dash board te maken:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

U kunt een dashboard bijwerken met behulp van de opdracht [az portal dash board update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update):

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Bekijk de details van een dash board door de opdracht [AZ Portal dash board show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) uit te voeren:

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

Zie [Azure Portal instellingen en voor keuren beheren](set-preferences.md)voor meer informatie over Desk tops.

Zie [az portal dash board](/cli/azure/ext/portal/portal/dashboard) voor meer informatie over Azure CLI-ondersteuning voor dashboards.
