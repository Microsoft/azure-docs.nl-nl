---
title: Doorvoer eenheden automatisch opschalen-Azure Event Hubs | Microsoft Docs
description: Schakel automatisch verg Roten in een naam ruimte in om doorvoer eenheden automatisch te schalen.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1be564472011622b71b3066495748dfdbe6cc791
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020803"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatisch schalen van Azure Event Hubs doorvoer eenheden
Azure Event Hubs is een uiterst schaalbaar platform voor het streamen van gegevens. Als zodanig neemt Event Hubs gebruik vaak toe na het starten van de service. Voor dit gebruik moet u de vooraf vastgestelde [doorvoer eenheden](event-hubs-scalability.md#throughput-units) verg Roten om event hubs te schalen en grotere overdrachts snelheden af te handelen. De functie **automatisch verg Roten** van Event hubs wordt automatisch geschaald door het aantal doorvoer eenheden te verhogen om te voldoen aan de behoeften van het gebruik. Het verhogen van doorvoer eenheden voor komt het beperken van scenario's, waarbij:

* Tarieven voor gegevens ingang overschrijden de ingestelde doorvoer eenheden.
* De tarieven voor gegevens uitwissel aanvragen overschrijden de ingestelde doorvoer eenheden.

De Event Hubs-service verhoogt de door Voer wanneer de belasting groter wordt dan de minimale drempel waarde, zonder dat er aanvragen worden uitgevoerd met ServerBusy-fouten.

## <a name="how-auto-inflate-works"></a>Hoe automatisch verg Roten werkt

Event Hubs verkeer wordt bepaald door [doorvoer eenheden](event-hubs-scalability.md#throughput-units). Eén doorvoer eenheid maakt 1 MB per seconde van binnenkomend en twee keer zo hoog mogelijk. Standaard Event hubs kunnen worden geconfigureerd met 1-20-doorvoer eenheden. Als u automatisch verg Roten, kunt u klein beginnen met de mini maal vereiste doorvoer eenheden die u kiest. De functie schaalt vervolgens automatisch naar de maximum limiet van de doorvoer eenheden die u nodig hebt, afhankelijk van de toename van uw verkeer. Automatisch verg Roten biedt de volgende voor delen:

- Een efficiënt schaal mechanisme om kleine te beginnen en omhoog te schalen naarmate u groeit.
- Automatisch schalen naar de opgegeven bovengrens zonder beperking van problemen.
- Meer controle over schalen, omdat u bepaalt wanneer en hoeveel u wilt schalen.

## <a name="enable-auto-inflate-on-a-namespace"></a>Automatisch verg Roten inschakelen voor een naam ruimte

U kunt automatisch verg Roten op een standaardlaag Event Hubs naam ruimte in-of uitschakelen met behulp van een van de volgende methoden:

- Het [Azure Portal](https://portal.azure.com).
- Een [Azure Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> De Basic-laag Event Hubs naam ruimten bieden geen ondersteuning voor automatisch verg Roten.

### <a name="enable-auto-inflate-through-the-portal"></a>Automatisch verg Roten inschakelen via de portal


#### <a name="enable-at-the-time-of-creation"></a>Inschakelen op het moment van maken 
U kunt de functie voor automatisch verg Roten inschakelen **bij het maken van een event hubs naam ruimte**:
 
![Automatisch verg Roten inschakelen op het moment Event Hub maken](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Als deze optie is ingeschakeld, kunt u kleine met uw doorvoer eenheden starten en omhoog schalen naarmate uw gebruiks behoefte toeneemt. De bovengrens voor de inflatie is niet direct van invloed op de prijzen, wat afhankelijk is van het aantal gebruikte doorvoer eenheden per uur.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Automatisch verg Roten inschakelen voor een bestaande Event Hub
U kunt ook de functie voor automatisch verg Roten inschakelen en de instellingen wijzigen met behulp van de volgende instructies: 
 
1. Selecteer op de pagina **Event hubs naam ruimte** de optie **uitgeschakeld** onder **doorvoer eenheden automatisch verg Roten**.  

    ![Doorvoer eenheden selecteren op de pagina met de Event Hubs naam ruimte](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Schakel op de pagina **schaal instellingen** het selectie vakje voor **inschakelen** in (als de functie voor automatisch schalen niet is ingeschakeld).

    ![Selecteer inschakelen](./media/event-hubs-auto-inflate/scale-settings.png)
3. Voer het **maximum** aantal doorvoer eenheden in of gebruik de schuif balk om de waarde in te stellen. 
4. Beschrijving Werk het **minimum** aantal doorvoer eenheden toe boven aan deze pagina. 


> [!NOTE]
> Wanneer u de automatische vergren configuratie toepast om doorvoer eenheden te verhogen, verzendt de Event Hubs-service Diagnostische logboeken die u informatie geven over waarom en wanneer de door Voer is verhoogd. Als u diagnostische logboek registratie wilt inschakelen voor een Event Hub, selecteert u **Diagnostische instellingen** in het menu links op de pagina Event hub in de Azure Portal. Zie [Diagnostische logboeken instellen voor een Azure-Event hub](event-hubs-diagnostic-logs.md)voor meer informatie. 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Automatisch verg Roten inschakelen met behulp van een Azure Resource Manager sjabloon

U kunt automatisch verg Roten inschakelen tijdens de implementatie van een Azure Resource Manager sjabloon. Stel de `isAutoInflateEnabled` eigenschap bijvoorbeeld in op **True** en stel deze `maximumThroughputUnits` in op 10. Bijvoorbeeld:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Voor de volledige sjabloon, zie de [Event hubs naam ruimte maken en sjabloon verg Roten inschakelen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) op github.


## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht](./event-hubs-about.md)
