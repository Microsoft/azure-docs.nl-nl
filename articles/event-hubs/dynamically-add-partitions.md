---
title: Dynamisch partities toevoegen aan een Event Hub in Azure Event Hubs
description: In dit artikel wordt beschreven hoe u dynamisch partities toevoegt aan een Event Hub in Azure Event Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: aeeee1bcefe58b006dac0b6913aaa609cbeefb8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775116"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Dynamisch partities toevoegen aan een Event Hub (Apache Kafka onderwerp) in Azure Event Hubs
Event Hubs daarentegen biedt streaming van berichten via een model op basis van gepartitioneerd gebruik, waarbij elke consumer slechts een specifieke subset of partitie van de berichtenstroom leest. Dit patroon maakt een horizontale schaal voor de verwerking van gebeurtenissen mogelijk en biedt andere stroomgerichte functies die niet beschikbaar zijn in wachtrijen en onderwerpen. Een partitie is een geordende reeks gebeurtenissen die in een Event Hub wordt bewaard. Als er nieuwere gebeurtenissen plaatsvinden, worden deze toegevoegd aan het einde van deze reeks. Zie Partities voor meer informatie over [partities](event-hubs-scalability.md#partitions) in het algemeen

U kunt het aantal partities opgeven op het moment dat u een Event Hub maakt. In sommige scenario's moet u mogelijk partities toevoegen nadat de Event Hub is gemaakt. In dit artikel wordt beschreven hoe u dynamisch partities toevoegt aan een bestaande Event Hub. 

> [!IMPORTANT]
> Dynamische toevoegingen van partities zijn alleen beschikbaar op Toegewezen Event Hubs clusters. 

> [!NOTE]
> Voor Apache Kafka-clients wordt een **Event Hub** aan een **Kafka-onderwerp wijs gemaakt.** Zie Voor meer toewijzingen tussen Azure Event Hubs en Apache Kafka [Kafka en Event Hubs conceptuele toewijzing](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Het aantal partities bijwerken
In deze sectie ziet u hoe u het aantal partities van een Event Hub op verschillende manieren kunt bijwerken (PowerShell, CLI, etc.).

### <a name="powershell"></a>PowerShell
Gebruik de [PowerShell-opdracht Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) om partities in een Event Hub bij te werken. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
Gebruik de [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az_eventhubs_eventhub_update) CLI-opdracht om partities in een Event Hub bij te werken. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager-sjabloon
Werk de waarde van de eigenschap in Resource Manager sjabloon bij enployer de `partitionCount` sjabloon opnieuw om de resource bij te werken. 

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
Gebruik de API (bijvoorbeeld via het `AlterTopics` **CLI-hulpprogramma kafka-topics)** om het aantal partities te verhogen. Zie [Modifying Kafka topics (Kafka-onderwerpen wijzigen) voor meer informatie.](http://kafka.apache.org/documentation/#basic_ops_modify_topic) 

## <a name="event-hubs-clients"></a>Event Hubs-clients
Laten we eens kijken hoe Event Hubs zich gedragen wanneer het aantal partities wordt bijgewerkt op een Event Hub. 

Wanneer u een partitie toevoegt aan een bestaande even hub, ontvangt de Event Hub-client een van de service om de clients te informeren dat entiteitsmetagegevens (entiteit is uw Event Hub en metagegevens de partitiegegevens) zijn `MessagingException` gewijzigd. De AMQP-koppelingen worden automatisch opnieuw geopend door de clients, waarna de gewijzigde metagegevensgegevens worden opgehaald. De clients werken vervolgens normaal.

### <a name="senderproducer-clients"></a>Afzender-/producent-clients
Event Hubs biedt drie opties voor afzenders:

- **Afzender van partitie:** in dit scenario verzenden clients gebeurtenissen rechtstreeks naar een partitie. Hoewel partities identificeerbaar zijn en gebeurtenissen rechtstreeks naar de partities kunnen worden verzonden, raden we dit patroon niet aan. Het toevoegen van partities heeft geen invloed op dit scenario. U wordt aangeraden toepassingen opnieuw op te starten, zodat deze nieuw toegevoegde partities kunnen detecteren. 
- **Afzender van** partitiesleutel: in dit scenario verzenden clients de gebeurtenissen met een sleutel, zodat alle gebeurtenissen die tot die sleutel behoren, in dezelfde partitie eindigen. In dit geval hasht de service de sleutel en routes naar de bijbehorende partitie. De update van het aantal partities kan problemen veroorzaken die niet in de volgorde van de partities zijn vanwege een wijziging in de hash- of hash-indeling. Dus als u de volgorde belangrijk vindt, moet u ervoor zorgen dat uw toepassing alle gebeurtenissen van bestaande partities verbruikt voordat u het aantal partities verhoogt.
- **Round robin-afzender (standaard)** : in dit scenario rondt de Event Hubs-service de gebeurtenissen af over meerdere partities en wordt ook een taakverdelingsalgoritme gebruikt. Event Hubs-service is op de hoogte van wijzigingen in het aantal partities en verzendt deze binnen enkele seconden na het wijzigen van het aantal partities naar nieuwe partities.

### <a name="receiverconsumer-clients"></a>Ontvanger-/consumenten-clients
Event Hubs biedt directe ontvangers en een eenvoudige consumentenbibliotheek met de naam [eventprocessorhost (oude SDK)](event-hubs-event-processor-host.md) of [gebeurtenisprocessor (nieuwe SDK).](event-processor-balance-partition-load.md)

- **Directe ontvangers:** de directe ontvangers luisteren naar specifieke partities. Hun runtimegedrag wordt niet beïnvloed wanneer partities worden geschaald voor een Event Hub. De toepassing die gebruikmaakt van directe ontvangers moet de nieuwe partities ophalen en de ontvangers dienovereenkomstig toewijzen.
- **Gebeurtenisprocessorhost:** deze client vernieuwt niet automatisch de metagegevens van de entiteit. Het wordt dus niet opgepikt bij een toename van het aantal partities. Het opnieuw maken van een exemplaar van een gebeurtenisprocessor zorgt ervoor dat metagegevens van een entiteit worden opgehaald, waardoor er nieuwe blobs worden gemaakt voor de zojuist toegevoegde partities. Bestaande blobs worden niet beïnvloed. Het wordt aanbevolen om alle exemplaren van de gebeurtenisprocessor opnieuw op te starten om ervoor te zorgen dat alle exemplaren op de hoogte zijn van de zojuist toegevoegde partities en dat taakverdeling correct wordt afgehandeld tussen consumenten.

    Als u de oude versie van .NET SDK[(WindowsAzure.ServiceBus)](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)gebruikt, verwijdert de gebeurtenisprocessorhost een bestaand controlepunt bij het opnieuw opstarten als het aantal partities in het controlepunt niet overeen komt met het aantal partities dat is opgehaald uit de service. Dit gedrag kan van invloed zijn op uw toepassing. 

## <a name="apache-kafka-clients"></a>Apache Kafka clients
In deze sectie wordt beschreven Apache Kafka clients die gebruikmaken van het Kafka-eindpunt van Azure Event Hubs zich gedragen wanneer het aantal partities wordt bijgewerkt voor een Event Hub. 

Kafka-clients die gebruikmaken Event Hubs het Apache Kafka-protocol gedragen zich anders dan Event Hub-clients die gebruikmaken van het AMQP-protocol. Kafka-clients werken hun metagegevens eenmaal per `metadata.max.age.ms` milliseconden bij. U geeft deze waarde op in de clientconfiguraties. De `librdkafka` bibliotheken gebruiken ook dezelfde configuratie. Updates van metagegevens informeren de clients over servicewijzigingen, waaronder een toename van het aantal partities. Zie Configuraties configureren voor Apache Kafka voor Event Hubs voor een [lijst met configuraties.](apache-kafka-configurations.md)

### <a name="senderproducer-clients"></a>Afzender-/producent-clients
Producenten schrijven altijd voor dat verzendaanvragen de partitiebestemming voor elke set geproduceerde records bevatten. Dus alle productiepartities worden uitgevoerd aan de clientzijde met de weergave van de metagegevens van de broker. Zodra de nieuwe partities zijn toegevoegd aan de metagegevensweergave van de producent, zijn ze beschikbaar voor producentaanvragen.

### <a name="consumerreceiver-clients"></a>Clients voor consumenten/ontvangers
Wanneer een lid van een consumentengroep metagegevens vernieuwt en de zojuist gemaakte partities oppikt, initieert dat lid een herverdeling van de groep. Metagegevens van consumenten worden vervolgens vernieuwd voor alle groepsleden en de nieuwe partities worden toegewezen door de toegewezen leider voor opnieuw verdelen.

## <a name="recommendations"></a>Aanbevelingen

- Als u partitiesleutel gebruikt met uw producenttoepassingen en afhankelijk bent van sleutelhashing om ervoor te zorgen dat een partitie wordt orded, wordt het niet aanbevolen om partities dynamisch toe te voegen. 

    > [!IMPORTANT]
    > Hoewel de volgorde van de bestaande gegevens behouden blijft, wordt partitiehashing verbroken voor berichten die zijn gehasht nadat het aantal partities is gewijzigd door toevoeging van partities.
- Het toevoegen van een partitie aan een bestaand onderwerp of event hub-exemplaar wordt in de volgende gevallen aanbevolen:
    - Wanneer u de standaardmethode voor het verzenden van gebeurtenissen gebruikt
     - Standaard partitioneringsstrategieën voor Kafka, voorbeeld : Sticky Assignor-strategie


## <a name="next-steps"></a>Volgende stappen
Zie Partitions (Partities) [voor meer informatie over partities.](event-hubs-scalability.md#partitions)
