---
title: Streaming-gebeurtenissen vastleggen-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat een overzicht van de functie Capture waarmee u gebeurtenissen streaming kunt vastleggen via Azure Event Hubs.
ms.topic: article
ms.date: 02/16/2021
ms.openlocfilehash: 9f0ec1223c06b908a9aa9f3ac5c5b19ead2fe962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100595961"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Gebeurtenissen vastleggen via Azure Event Hubs in Azure Blob Storage of Azure Data Lake Storage
Met Azure Event Hubs kunt u automatisch de streaminggegevens vastleggen in Event Hubs in een [Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) of [Azure data Lake Storage gen 1-of gen 2](https://azure.microsoft.com/services/data-lake-store/) -account van uw keuze, met extra flexibiliteit voor het opgeven van een tijd-of grootte-interval. Het instellen van vastleggen is snel, er zijn geen administratieve kosten om deze uit te voeren en deze worden automatisch geschaald met Event Hubs [doorvoer eenheden](event-hubs-scalability.md#throughput-units). Event Hubs Capture is de eenvoudigste manier om streaminggegevens te laden in azure, en u kunt zich concentreren op gegevens verwerking in plaats van op het vastleggen van gegevens.

> [!NOTE]
> Het configureren van Event Hubs Capture voor gebruik van Azure Data Lake Storage **gen 2** is hetzelfde als het configureren van een Azure-Blob Storage. Zie [configure Event hubs Capture](event-hubs-capture-enable-through-portal.md)(Engelstalig) voor meer informatie. 

Met Event Hubs Capture kunt u in realtime en op batch gebaseerde pijp lijnen op dezelfde stroom verwerken. Dit betekent dat u oplossingen kunt bouwen die in de loop van de tijd met uw behoeften groeien. Of u nu batch-gebaseerde systemen bouwt met een ogen blik naar toekomstige realtime verwerking, of als u een efficiënt koud pad wilt toevoegen aan een bestaande realtime-oplossing, kunt u met Event Hubs Capture gemakkelijker werken met streaming-gegevens.

> [!IMPORTANT]
> Het Azure Storage-of Azure Data Lake Storage-account van de doel opslag moet zich in hetzelfde abonnement als de Event Hub bevallen. 

## <a name="how-event-hubs-capture-works"></a>Hoe Event Hubs Capture werkt

Event Hubs is een duurzame buffer voor het bewaren van tijd voor telemetrie-ingangen, vergelijkbaar met een gedistribueerd logboek. De te schalen sleutel in Event Hubs is het [gepartitioneerde consument model](event-hubs-scalability.md#partitions). Elke partitie is een onafhankelijk gegevens segment dat onafhankelijk van elkaar wordt verbruikt. Na verloop van tijd overschrijdt u de gegevens op basis van de Configureer bare Bewaar periode. Als gevolg hiervan wordt een gegeven Event Hub nooit ' te vol '.

Met Event Hubs Capture kunt u uw eigen Azure Blob-opslag account en-container of Azure Data Lake Storage-account opgeven dat wordt gebruikt voor het opslaan van de vastgelegde gegevens. Deze accounts kunnen zich in dezelfde regio bevinden als uw Event Hub of in een andere regio en worden toegevoegd aan de flexibiliteit van de functie voor het vastleggen van Event Hubs.

Vastgelegde gegevens worden geschreven in [Apache Avro][Apache Avro] -indeling: een compacte, snelle en binaire indeling die voorziet in uitgebreide gegevens structuren met inline-schema. Deze indeling wordt veel gebruikt in het Hadoop-ecosysteem, het Stream Analytics en het Azure Data Factory. Verderop in dit artikel vindt u meer informatie over het werken met Avro.

### <a name="capture-windowing"></a>Vastleggen in venster

Met Event Hubs Capture kunt u een venster instellen voor het beheren van vastleggen. Dit venster is een minimale configuratie van grootte en tijd met een ' eerste WINS-beleid ', wat betekent dat de eerste trigger die wordt aangetroffen, een vastleg bewerking veroorzaakt. Als u een scherm van vijf tien minuten, 100 MB of 1 MB per seconde verzendt, wordt het venster grootte geactiveerd vóór het tijd venster. Elke partitie legt onafhankelijk vast en schrijft een voltooide blok-Blob op het moment van vastleggen, met de naam van het tijdstip waarop het vastleg interval is aangetroffen. De naamgevings Conventie voor opslag is als volgt:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

De datum waarden worden opgevuld met nullen. een voor beeld van een bestands naam is:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

In het geval dat uw Azure Storage-BLOB tijdelijk niet beschikbaar is, behoudt Event Hubs Capture uw gegevens voor de Bewaar periode voor gegevens die op uw Event Hub is geconfigureerd en vult de gegevens terug wanneer uw opslag account weer beschikbaar is.

### <a name="scaling-to-throughput-units"></a>Schalen naar doorvoer eenheden

Event Hubs verkeer wordt bepaald door [doorvoer eenheden](event-hubs-scalability.md#throughput-units). Een enkele doorvoer eenheid maakt 1 MB per seconde of 1000 gebeurtenissen per seconde van binnenkomend en twee keer zo hoog mogelijk. Standard Event Hubs kunnen worden geconfigureerd met 1-20 doorvoer eenheden en u kunt meer kopen met een [ondersteunings aanvraag][support request]voor quotum verhoging. Het gebruik buiten uw aangeschafte doorvoer eenheden wordt beperkt. Met Event Hubs Capture worden gegevens rechtstreeks uit de interne Event Hubs-opslag gekopieerd, worden de uituitgangs quota voor doorvoer eenheden overs Laan en wordt de uitvoerder opgeslagen voor andere verwerkings lezers, zoals Stream Analytics of Spark.

Zodra de configuratie is geconfigureerd, wordt Event Hubs Capture automatisch uitgevoerd wanneer u uw eerste gebeurtenis verzendt en blijft de uitvoering. Om ervoor te zorgen dat de uitvoering van de downstream-verwerking gemakkelijker is om te weten dat het proces werkt, Event Hubs worden lege bestanden geschreven wanneer er geen gegevens zijn. Dit proces biedt een voorspel bare uitgebracht en een markering die uw batch-processors kan invoeren.

## <a name="setting-up-event-hubs-capture"></a>Event Hubs Capture instellen

U kunt vastleggen configureren op het moment van Event Hub aanmaak met behulp van de [Azure Portal](https://portal.azure.com)of Azure Resource Manager sjablonen gebruiken. Raadpleeg voor meer informatie de volgende artikelen:

- [Event Hubs Capture inschakelen met behulp van de Azure-portal](event-hubs-capture-enable-through-portal.md)
- [Een Event Hubs-naamruimte met een gebeurtenishub maken en Capture inschakelen met behulp van een Azure Resource Manager-sjabloon](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

> [!NOTE]
> Als u de Capture-functie inschakelt voor een bestaande Event Hub, legt de functie gebeurtenissen vast die op de Event Hub arriveren **nadat** de functie is ingeschakeld. Er worden geen gebeurtenissen vastgelegd die bestonden in de Event Hub voordat de functie werd ingeschakeld. 

## <a name="exploring-the-captured-files-and-working-with-avro"></a>De vastgelegde bestanden verkennen en werken met Avro

Met Event Hubs Capture maakt u bestanden in de Avro-indeling, zoals opgegeven in het geconfigureerde tijd venster. U kunt deze bestanden weer geven in elk hulp programma, zoals [Azure Storage Explorer][Azure Storage Explorer]. U kunt de bestanden lokaal downloaden om ze te kunnen gebruiken.

De bestanden die worden geproduceerd door Event Hubs Capture hebben het volgende Avro-schema:

![Avro-schema][3]

Een eenvoudige manier om Avro-bestanden te verkennen, is met behulp van de [Avro-Hulpprogram ma's][Avro Tools] jar van Apache. U kunt Apache- [analyse][Apache Drill] ook gebruiken voor een lichte, op SQL gebaseerde ervaring of [Apache Spark][Apache Spark] voor het uitvoeren van complexe gedistribueerde verwerking op de opgenomen gegevens. 

### <a name="use-apache-drill"></a>Apache-boren gebruiken

[Apache Drill][Apache Drill] is een open-source SQL-query-engine voor het verkennen van Big Data, waarmee u op elke gewenste manier gestructureerde en semi-gestructureerde gegevens kunt opvragen. De engine kan worden uitgevoerd als een zelfstandig knoop punt of als een enorm cluster voor geweldige prestaties.

Er is een systeem eigen ondersteuning beschikbaar voor Azure Blob Storage, waarmee u eenvoudig gegevens kunt opvragen in een AVRO-bestand, zoals beschreven in de documentatie:

[Apache-detail weergave: Azure Blob Storage-invoeg toepassing][Apache Drill: Azure Blob Storage Plugin]

Als u snel vastgelegde bestanden wilt doorzoeken, kunt u een virtuele machine maken en uitvoeren met Apache-Drill ingeschakeld via een container om toegang te krijgen tot Azure Blob-opslag. Zie het volgende voor beeld: [streaming op schaal met Event hubs Capture](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-capture).

### <a name="use-apache-spark"></a>Apache Spark gebruiken

[Apache Spark][Apache Spark] is een geïntegreerde analyse-engine voor grootschalige gegevens verwerking. Het ondersteunt verschillende talen, waaronder SQL, en kan eenvoudig toegang krijgen tot Azure Blob-opslag. Er zijn enkele opties om Apache Spark in azure uit te voeren en elk biedt eenvoudige toegang tot Azure Blob-opslag:

- [HDInsight: adres bestanden in azure Storage][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob-opslag][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](../aks/spark-job.md) 

### <a name="use-avro-tools"></a>Avro-Hulpprogram Ma's gebruiken

[Avro-Hulpprogram ma's][Avro Tools] zijn beschikbaar als jar-pakket. Nadat u het jar-bestand hebt gedownload, kunt u het schema van een specifiek Avro-bestand weer geven door de volgende opdracht uit te voeren:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Met deze opdracht wordt

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

U kunt ook Avro-Hulpprogram Ma's gebruiken om het bestand te converteren naar de JSON-indeling en andere verwerking uit te voeren.

Als u geavanceerdere verwerking wilt uitvoeren, kunt u Avro downloaden en installeren voor uw keuze van platform. Op het moment van schrijven zijn er implementaties beschikbaar voor C, C++, C \# , Java, NodeJS, perl, PHP, python en Ruby.

Apache Avro is volledig aan de slag met hand leidingen voor [Java][Java] en [python][Python]. U kunt ook het artikel [aan de slag met Event hubs vastleggen](event-hubs-capture-python.md) lezen.

## <a name="how-event-hubs-capture-is-charged"></a>Hoe Event Hubs Capture in rekening wordt gebracht

Event Hubs Capture wordt op vergelijk bare wijze gemeten met doorvoer eenheden: per uur. De kosten zijn direct evenredig met het aantal aangeschafte doorvoer eenheden voor de naam ruimte. Naarmate doorvoer eenheden toenemen en dalen, nemen de Event Hubs Capture-meters toe en afnemen om de prestaties te verbeteren. De meters ontstaan beide. Zie [Event hubs prijzen](https://azure.microsoft.com/pricing/details/event-hubs/)voor prijs informatie. 

Bij het vastleggen wordt geen afkomend quotum gebruikt, aangezien dit afzonderlijk wordt gefactureerd. 

## <a name="integration-with-event-grid"></a>Integratie met Event Grid 

U kunt een Azure Event Grid-abonnement maken met een Event Hubs naam ruimte als bron. In de volgende zelf studie ziet u hoe u een Event Grid-abonnement kunt maken met een Event Hub als een bron en een Azure Functions-app als een Sink: het [verwerken en migreren van vastgelegde Event hubs gegevens naar een Azure Synapse-analyse met behulp van Event grid en Azure functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Volgende stappen
Event Hubs Capture is de eenvoudigste manier om gegevens in azure op te halen. Met Azure Data Lake, Azure Data Factory en Azure HDInsight kunt u batch verwerking en andere analyses uitvoeren met behulp van de vertrouwde Program ma's en platformen van uw keuze, op elke gewenste schaal.

Meer informatie over het inschakelen van deze functie met behulp van de Azure Portal en Azure Resource Manager sjabloon:

- [Azure Portal gebruiken om Event Hubs Capture in te schakelen](event-hubs-capture-enable-through-portal.md)
- [Een Azure Resource Manager sjabloon gebruiken om Event Hubs vastleggen in te scha kelen](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://github.com/microsoft/AzureStorageExplorer/releases
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://downloads.apache.org/avro/stable/java/
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming at Scale: Event Hubs Capture]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
