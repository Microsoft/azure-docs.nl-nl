---
title: Structuur van Azure-dashboards
description: Door loop de JSON-structuur van een Azure-dash board met behulp van een voorbeeld dashboard. Bevat verwijzingen naar resource-eigenschappen.
ms.topic: conceptual
ms.date: 12/20/2019
ms.openlocfilehash: d37e2fd9c9f6ef6e7ddea6dea002f26f20cd66a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745958"
---
# <a name="the-structure-of-azure-dashboards"></a>Structuur van Azure-dashboards
In dit document wordt de structuur van een Azure-dash board beschreven, met behulp van het volgende dash board:

![voorbeelddashboard](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Omdat gedeelde [Azure-Dash boards resources zijn](../azure-resource-manager/management/overview.md), kan dit dash board worden weer gegeven als JSON.  In de volgende JSON staat het dash board dat hierboven is gevisualiseerd.

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
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
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

## <a name="common-resource-properties"></a>Algemene bron eigenschappen

Laten we de relevante secties van de JSON opsplitsen.  De eigenschappen van het hoogste niveau, de __id__, de __naam__, het __type__, de __locatie__ en de __labels__ worden gedeeld in alle Azure-resource typen. Dat wil zeggen dat ze niet veel te maken hebben met de inhoud van het dash board.

### <a name="the-id-property"></a>De eigenschap ID

De Azure-Resource-ID, met inachtneming [van de naamgevings regels van Azure-resources](/azure/architecture/best-practices/resource-naming). Wanneer de portal een dash board maakt, kiest deze in het algemeen een ID in de vorm van een GUID, maar u kunt een geldige naam gebruiken wanneer u ze via een programma maakt. 

### <a name="the-name-property"></a>De eigenschap name
De naam is het segment van de resource-ID die het abonnement, het resource type of de informatie over de resource groep niet bevat. In feite is dit het laatste segment van de resource-ID.

### <a name="the-type-property"></a>De eigenschap type
Alle Dash boards zijn van het type __micro soft. Portal/Dash boards__.

### <a name="the-location-property"></a>De locatie-eigenschap
In tegens telling tot andere resources hebben Dash boards geen runtime-onderdeel.  Voor dash boards geeft de locatie de primaire geografische locatie aan waar de JSON-weer gave van het dash board wordt opgeslagen. De waarde moet een van de locatie codes zijn die kunnen worden opgehaald met behulp [van de locatie-API voor de abonnementen bron](/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>De eigenschap Tags
Tags zijn een gemeen schappelijke functie van Azure-resources waarmee u uw resource kunt ordenen door wille keurige naam paren te gebruiken. Voor dash boards is er één speciale tag met de naam __verborgen titel__. Als uw dash board deze eigenschap heeft gevuld, wordt deze als weergave naam gebruikt voor uw dash board in de portal. De naam van een Azure-resource-id kan niet worden gewijzigd, maar Tags kunnen. Met deze tag kunt u een renamable-weergave naam voor uw dash board hebben.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Het object Properties
Het object Properties bevat twee eigenschappen, __lenzen__ en __meta gegevens__. De eigenschap __lenzen__ bevat informatie over de tegels op het dash board.  De __meta gegevens__ eigenschap is mogelijk voor toekomstige functies.

### <a name="the-lenses-property"></a>De eigenschap lenzen
De eigenschap __lenzen__ bevat het dash board. Houd er rekening mee dat het object lenzen in dit voor beeld een enkele eigenschap met de naam ' 0 ' bevat. Lenzen zijn een groeperings concept dat momenteel niet is geïmplementeerd in dash boards. Nu hebben al uw Dash boards deze enkele eigenschap in het object lens, met de naam "0".

### <a name="the-lens-object"></a>Het lens object
Het object onder de ' 0 ' bevat twee eigenschappen, een __volg orde__ en __delen__.  In de huidige versie van Dash boards is __order__ altijd 0. De eigenschap __Parts__ bevat een object dat de afzonderlijke onderdelen (ook wel tegels genoemd) in het dash board definieert.

Het object __Parts__ bevat een eigenschap voor elk onderdeel, waarbij de naam van de eigenschap een getal is. Dit getal is niet significant. 

### <a name="the-part-object"></a>Het onderdeel object
Elk afzonderlijk onderdeel object heeft een __positie__ en __meta gegevens__.

### <a name="the-position-object"></a>Het positie object
De eigenschap __Position__ bevat de grootte-en locatie-informatie voor het onderdeel, uitgedrukt als __x__, __y__, __rowspan__ en __colspan__. De waarden zijn qua raster eenheden. Deze raster eenheden zijn zichtbaar wanneer het dash board zich in de aanpassings modus bevindt, zoals hier wordt weer gegeven. Als u wilt dat een tegel een breedte heeft van twee raster eenheden, een hoogte van één raster eenheid en een locatie in de linkerbovenhoek van het dash board, ziet het positie-object er als volgt uit:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![Scherm afbeelding toont een close-up van het raster, waarbij één vier Kante raster eenheid is gemarkeerd.](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Het meta gegevens object
Elk onderdeel heeft een eigenschap meta data, een object heeft slechts één vereiste eigenschap met de naam __type__. Deze teken reeks vertelt de portal op welke tegel moet worden weer gegeven. Het dash board voor beeld maakt gebruik van de volgende typen tegels:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` : Wordt gebruikt om metrische gegevens over bewaking weer te geven
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` : Wordt gebruikt om weer te geven met tekst of afbeeldingen met basis opmaak voor lijsten, koppelingen, enzovoort.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` : Wordt gebruikt voor het weer geven van Video's van YouTube, Channel 9 en elk ander type video dat in een HTML-video code werkt.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` : Wordt gebruikt om de naam en status van een virtuele Azure-machine weer te geven.

Elk type onderdeel heeft een eigen configuratie. De mogelijke configuratie-eigenschappen worden __input__, __Settings__ en __Asset__ genoemd. 

### <a name="the-inputs-object"></a>Het inputs-object
Het object invoer bevat meestal informatie die een tegel verbindt met een resource-exemplaar.  Het onderdeel van de virtuele machine in het dash board voor beeld bevat één invoer die gebruikmaakt van de Azure-Resource-ID om de binding uit te drukken.  Deze indeling van de resource-ID is consistent voor alle Azure-resources.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Het deel diagram met metrische gegevens heeft één invoer waarmee de resource wordt aangegeven die moet worden gekoppeld, evenals informatie over de gegevens die worden weer gegeven. Hier volgt de invoer voor de tegel die het netwerk in en de metrische gegevens van het netwerk weergeeft.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
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
]

```

### <a name="the-settings-object"></a>Het instellingen object
Het object Settings bevat de Configureer bare elementen van een deel.  In het dash board van het voor beeld gebruikt het deel voor de prijs verlaging instellingen voor het opslaan van de aangepaste inhoud voor prijs verlaging en een Configureer bare titel en subtitel.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Op dezelfde manier heeft de video tegel eigen instellingen met een verwijzing naar de video die u wilt afspelen, een instelling voor automatisch afspelen en de informatie over de optionele titel.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Het object Asset
Voor tegels die zijn gekoppeld aan de eerste klasse beheer bare Portal objecten (die assets worden genoemd), wordt deze relatie weer gegeven via het object Asset.  In het dash board voor beeld bevat de tegel virtuele machine de naam van deze asset.  De eigenschap __idInputName__ geeft aan de portal door dat de id-invoer de unieke id voor de Asset bevat, in dit geval de resource-id. Voor de meeste Azure-resource typen zijn activa gedefinieerd in de portal.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`