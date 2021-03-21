---
title: Dynamisch partities toevoegen aan een Event Hub in azure Event Hubs
description: Dit artikel laat u zien hoe u dynamisch partities kunt toevoegen aan een Event Hub in azure Event Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: e6efdc7bab309f825032555c97f1e1128f5addd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625262"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Dynamisch partities toevoegen aan een Event Hub (Apache Kafka onderwerp) in azure Event Hubs
Event Hubs daarentegen biedt streaming van berichten via een model op basis van gepartitioneerd gebruik, waarbij elke consumer slechts een specifieke subset of partitie van de berichtenstroom leest. Dit patroon maakt een horizontale schaal voor de verwerking van gebeurtenissen mogelijk en biedt andere stroomgerichte functies die niet beschikbaar zijn in wachtrijen en onderwerpen. Een partitie is een geordende reeks gebeurtenissen die in een Event Hub wordt bewaard. Als er nieuwere gebeurtenissen plaatsvinden, worden deze toegevoegd aan het einde van deze reeks. Zie [partities](event-hubs-scalability.md#partitions) voor meer informatie over partities in het algemeen.

U kunt het aantal partities opgeven op het moment van het maken van een Event Hub. In sommige scenario's moet u mogelijk partities toevoegen nadat de Event Hub is gemaakt. In dit artikel wordt beschreven hoe u dynamisch partities kunt toevoegen aan een bestaande Event Hub. 

> [!IMPORTANT]
> Dynamische toevoegingen van partities zijn alleen beschikbaar op **toegewezen** Event hubs clusters.

> [!NOTE]
> Voor Apache Kafka-clients wordt een **Event hub** toegewezen aan een **Kafka-onderwerp**. Zie [Kafka en Event hubs conceptuele toewijzing](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping) voor meer toewijzingen tussen Azure Event Hubs en Apache Kafka


## <a name="update-the-partition-count"></a>Het aantal partities bijwerken
In deze sectie wordt beschreven hoe u het aantal partities van een Event Hub op verschillende manieren bijwerkt (Power shell, CLI, enzovoort).

### <a name="powershell"></a>PowerShell
Gebruik de Power shell [-opdracht set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) om de partities in een event hub bij te werken. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
Gebruik de [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az-eventhubs-eventhub-update) cli-opdracht om partities in een event hub bij te werken. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager-sjabloon
Update waarde van de `partitionCount` eigenschap in de Resource Manager-sjabloon en implementeer de sjabloon opnieuw om de resource bij te werken. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
Gebruik de `AlterTopics` API (bijvoorbeeld via het CLI **-hulp programma Kafka-topics** ) om het aantal partities te verhogen. Zie [Kafka-onderwerpen aanpassen](http://kafka.apache.org/documentation/#basic_ops_modify_topic)voor meer informatie. 

## <a name="event-hubs-clients"></a>Event Hubs-clients
Laten we eens kijken hoe Event Hubs-clients zich gedragen wanneer het aantal partities wordt bijgewerkt op een Event Hub. 

Wanneer u een partitie toevoegt aan een bestaande, even hub, ontvangt de Event Hub-client een `MessagingException` van de service die de clients informeert dat de meta gegevens van de entiteit (entiteit uw event hub is en dat de meta gegevens van de partitie gegevens) zijn gewijzigd. De AMQP-koppelingen worden door de clients automatisch opnieuw geopend, waarna de gewijzigde meta gegevens worden opgehaald. De clients functioneren vervolgens normaal.

### <a name="senderproducer-clients"></a>Afzender/producer-clients
Event Hubs biedt drie opties voor de afzender:

- **Afzender partitioneren** : in dit scenario verzenden clients rechtstreeks naar een partitie. Hoewel partities herkenbaar zijn en gebeurtenissen rechtstreeks naar hen kunnen worden verzonden, wordt dit patroon niet aangeraden. Het toevoegen van partities heeft geen invloed op dit scenario. U wordt aangeraden toepassingen opnieuw te starten zodat ze nieuwe partities kunnen detecteren. 
- Verzender van de **partitie sleutel** : in dit scenario worden clients de gebeurtenissen met een sleutel verzonden zodat alle gebeurtenissen die bij die sleutel horen, in dezelfde partitie eindigen. In dit geval wordt de sleutel en routes naar de bijbehorende partitie gehasht. De update van het aantal partities kan leiden tot problemen met de hash-wijziging. Als u dus een bestelling maakt, moet u ervoor zorgen dat uw toepassing alle gebeurtenissen van bestaande partities verbruikt voordat u het aantal partities verhoogt.
- **Round Robin (standaard)** : in dit scenario wordt de Event hubs service Round Robin van de gebeurtenissen verdeeld over partities en wordt ook een taakverdelings algoritme gebruikt. Event Hubs-service is op de hoogte van het aantal partities dat wordt gewijzigd en verzonden naar nieuwe partities binnen enkele seconden van het wijzigen van het aantal partities.

### <a name="receiverconsumer-clients"></a>Ontvanger/consumenten clients
Event Hubs biedt directe ontvangers en een eenvoudige consumenten bibliotheek met de naam van de [Event processor host (oude SDK)](event-hubs-event-processor-host.md)  of de [gebeurtenis processor (nieuwe SDK)](event-processor-balance-partition-load.md).

- **Directe ontvangers** : de ontvangers Luis teren naar specifieke partities. Het runtime gedrag wordt niet beïnvloed wanneer partities voor een Event Hub worden uitgeschaald. De toepassing die gebruikmaakt van directe ontvangers, moet ervoor zorgen dat de nieuwe partities worden opgehaald en de ontvangers dienovereenkomstig worden toegewezen.
- **Event processor host** : deze client vernieuwt de meta gegevens van de entiteit niet automatisch. Dit leidt tot een toename van het aantal partities. Het opnieuw maken van een gebeurtenis processor exemplaar zorgt ervoor dat de meta gegevens van een entiteit worden opgehaald, waardoor nieuwe blobs worden gemaakt voor de zojuist toegevoegde partities. Bestaande blobs worden niet beïnvloed. Het opnieuw starten van alle gebeurtenis processor instanties wordt aanbevolen om ervoor te zorgen dat alle exemplaren op de hoogte zijn van de nieuwe toegevoegde partities en dat taak verdeling correct wordt afgehandeld tussen de gebruikers.

    Als u de oude versie van .NET SDK ([WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)) gebruikt, wordt tijdens het opnieuw opstarten van de host van de gebeurtenis processor een bestaand controle punt verwijderd als het aantal partities in het controle punt niet overeenkomt met het aantal partities dat is opgehaald uit de service. Dit gedrag kan gevolgen hebben voor uw toepassing. 

## <a name="apache-kafka-clients"></a>Apache Kafka-clients
In deze sectie wordt beschreven hoe Apache Kafka-clients die gebruikmaken van het Kafka-eind punt van Azure Event Hubs zich gedragen wanneer het aantal partities voor een Event Hub wordt bijgewerkt. 

Kafka-clients die gebruikmaken van Event Hubs met het Apache Kafka-protocol, gedragen zich anders dan Event Hub clients die gebruikmaken van het AMQP-protocol. Kafka-clients updaten elke milliseconden hun meta gegevens `metadata.max.age.ms` . U geeft deze waarde op in de client configuraties. De `librdkafka` bibliotheken gebruiken ook dezelfde configuratie. Meta gegevens updates informeren de clients van service wijzigingen, inclusief het aantal partities. Zie [Apache Kafka configuraties voor Event hubs](apache-kafka-configurations.md)voor een lijst met configuraties.

### <a name="senderproducer-clients"></a>Afzender/producer-clients
Producenten bepalen altijd dat verzend aanvragen de partitie bestemming bevatten voor elke set geproduceerde records. Dit betekent dat alle productie partities worden uitgevoerd aan de client zijde met de weer gave van de meta gegevens van de Broker van de producent. Zodra de nieuwe partities worden toegevoegd aan de meta gegevens weergave van de producent, zijn ze beschikbaar voor producer-aanvragen.

### <a name="consumerreceiver-clients"></a>Consumer/ontvanger-clients
Wanneer een lid van een consument groep een meta gegevens vernieuwt en de zojuist gemaakte partities ophaalt, initieert dat lid een groeps herverdeling. Meta gegevens van de consument worden vervolgens vernieuwd voor alle groeps leden en de nieuwe partities worden toegewezen door de toegewezen herverdelings leider.

## <a name="recommendations"></a>Aanbevelingen

- Als u partitie sleutel gebruikt voor uw Producer-toepassingen en afhankelijk zijn van sleutel hashing om ervoor te zorgen dat een partitie kan worden geordend, wordt dynamisch toevoegen van partities niet aanbevolen. 

    > [!IMPORTANT]
    > Terwijl de bestaande gegevens worden geordend, wordt de partitie-hashing verbroken voor berichten die worden gehasht nadat het aantal partities is gewijzigd.
- Het is raadzaam om een partitie toe te voegen aan een bestaand onderwerp of Event Hub exemplaar wordt aanbevolen in de volgende gevallen:
    - Wanneer u de standaard methode voor het verzenden van gebeurtenissen gebruikt
     - Kafka standaard strategieën voor partitioneren, voor beeld – de strategie voor het koppelen van een gebruiker


## <a name="next-steps"></a>Volgende stappen
Zie [partities](event-hubs-scalability.md#partitions)voor meer informatie over partities.

