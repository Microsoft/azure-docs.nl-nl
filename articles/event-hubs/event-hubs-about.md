---
title: Wat is Azure Event Hubs? - een service voor de opname van Big Data | Microsoft Docs
description: Meer informatie over Azure Event Hubs, een big data-streamingservice die miljoenen gebeurtenissen per seconde opneemt.
ms.topic: overview
ms.date: 01/13/2021
ms.openlocfilehash: 36eeb38d9ed1696c9524ae9b346065756ce49c46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98195756"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs — Een streamingplatform en service voor het opnemen van big data
Azure Event Hubs is een streamingplatform en service voor het opnemen van big data. Het kan miljoenen gebeurtenissen per seconde ontvangen en verwerken. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen door gebruik te maken van een provider voor realtime analytische gegevens of batchverwerking/opslagadapters.

Hier ziet u enkele scenario's waarbij u Event Hubs kunt gebruiken:

- Anomaliedetectie (fraude/uitbijters)
- Toepassingslogboeken
- Analysepijplijnen, zoals clickstreams
- Live dashboards
- Archiveren van gegevens
- Transactieverwerking
- Gebruikerstelemetrieverwerking
- Telemetrie van apparaten streamen

<iframe width="560" height="315" src="https://www.youtube.com/embed/45wgY-VSk9I" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## <a name="why-use-event-hubs"></a>Het nut van Event Hubs

Gegevens zijn alleen waardevol als er een gemakkelijke manier is om gegevens uit bronnen te verwerken en tijdig inzichten te verkrijgen. Event Hubs biedt een gedistribueerd stroomverwerkingsplatform met lage latentie en naadloze integratie met gegevens- en analyseservices binnen en buiten Azure om uw ​​complete big data-pipeline te bouwen.

De rol die Event Hubs speelt, is die van 'voordeur' van een gebeurtenispijplijn. In oplossingsarchitecturen wordt dit vaak een *event ingestor* genoemd. Een event ingestor is een onderdeel dat of een service die zich tussen gebeurtenisuitgever en gebeurtenisconsumer bevindt en de productie van de gebeurtenisstroom loskoppelt van het gebruik van de betreffende gebeurtenissen. Event Hubs biedt een geïntegreerd streamingplatform met tijdbuffer, waarmee gebeurtenisproducenten worden ontkoppeld van gebeurtenisconsumers.

In de volgende secties worden de belangrijkste functies van de Azure Event Hubs-service beschreven:

## <a name="fully-managed-paas"></a>Volledig beheerde PaaS

Event Hubs is een volledig beheerde platform as a service (PaaS) met weinig overheadkosten voor configuratie of beheer, zodat u zich kunt concentreren op uw bedrijfsoplossingen. [Event Hubs voor Apache Kafka-ecosystemen](event-hubs-for-kafka-ecosystem-overview.md) bieden u de PaaS Kafka-ervaring zonder dat u uw clusters hoeft te beheren, te configureren of uit te voeren.

## <a name="support-for-real-time-and-batch-processing"></a>Ondersteuning voor realtime- en batchverwerking

U kunt uw stroom in realtime opnemen, bufferen, opslaan en verwerken om bruikbare inzichten te krijgen. Event Hubs maakt gebruik van een [gepartitioneerd consumermodel](event-hubs-scalability.md#partitions), waardoor meerdere toepassingen de stroom gelijktijdig kunnen verwerken en u de verwerkingssnelheid kunt regelen.

[Leg uw gegevens bijna in realtime vast](event-hubs-capture-overview.md) in een [ Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) of [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)  voor langdurige bewaring of microbatchverwerking. U kunt dit gedrag bereiken op dezelfde stroom die u gebruikt voor het afleiden van realtime analyses. Het instellen van het vastleggen van gebeurtenisgegevens gaat snel. Er zijn geen beheerkosten om het uit te voeren en het schaalt automatisch met Event Hubs- [doorvoereenheden](event-hubs-scalability.md#throughput-units). Met Event Hubs kunt u zich richten op gegevensverwerking in plaats van gegevens vast te leggen.

Azure Event Hubs kan ook worden geïntegreerd met [Azure Functions](../azure-functions/index.yml) voor een serverloze architectuur.

## <a name="scalable"></a>Schaalbaar

Met Event Hubs kunt u beginnen met gegevensstromen van megabytes en uitgroeien tot gigabytes of terabytes. De functie [Automatisch vergroten](event-hubs-auto-inflate.md) is een van de vele beschikbare opties voor het schalen van het aantal doorvoereenheden om aan uw verbruiksbehoeften te voldoen.

## <a name="rich-ecosystem"></a>Rijk ecosysteem

Met een breed ecosysteem, gebaseerd op het standaard AMQP 1.0-protocol voor de branche, dat beschikbaar is in verschillende talen, zoals [.NET](https://github.com/Azure/azure-sdk-for-net/), [Java](https://github.com/Azure/azure-sdk-for-java/), [Python](https://github.com/Azure/azure-sdk-for-python/) en [JavaScript](https://github.com/Azure/azure-sdk-for-js/) kunt u eenvoudig beginnen met het verwerken van uw streams van Event Hubs. Alle ondersteunde clienttalen bieden integratie op laag niveau. Het ecosysteem biedt u ook naadloze integratie met Azure-services zoals Azure Stream Analytics en Azure Functions, zodat u dus serverloze architecturen kunt bouwen.

Met [Event Hubs voor Apache Kafka-ecosystemen](event-hubs-for-kafka-ecosystem-overview.md) kunnen bovendien clients en toepassingen van [Apache Kafka (1.0 en nieuwer)](https://kafka.apache.org/) met Event Hubs communiceren. U hoeft uw eigen Kafka- en Zookeeper-clusters niet in te stellen, te configureren en te beheren, of een Kafka-as-a-Service-aanbieding te gebruiken die niet systeemeigen is voor Azure.
## <a name="key-architecture-components"></a>Belangrijkste onderdelen van de architectuur
Event Hubs bevat de volgende [belangrijke onderdelen](event-hubs-features.md):

- **Gebeurtenisproducenten**: elke entiteit die gegevens naar een Event Hub verzendt. Gebeurtenisuitgevers kunnen gebeurtenissen publiceren met HTTPS, AMQP 1.0 of Apache Kafka (1.0 of hoger)
- **Partities**: elke consument leest slechts een specifieke subset, of partitie, van de berichtenstroom.
- **Consumentengroepen**: een weergave (status, positie of offset) van een volledige Event Hub. Met consumentengroepen kunnen gebruikstoepassingen elk een afzonderlijke weergave van de gebeurtenisstroom hebben. De stroom wordt onafhankelijk in hun eigen tempo en met eigen verschuivingen afgelezen.
- **Doorvoereenheden**: vooraf gekochte eenheden van capaciteit die de doorvoercapaciteit van Event Hubs regelen.
- **Gebeurtenisontvangers**: elke entiteit die gebeurtenisgegevens van een Event Hub leest. Alle consumenten van Event Hubs maken verbinding via de AMQP 1.0-sessie. De Event Hubs-service biedt gebeurtenissen via een sessie zodra ze beschikbaar komen. Alle Kafka-consumers maken verbinding via het protocol Kafka 1.0 of hoger.

In de volgende afbeelding ziet u de architectuur voor de verwerking van stromen van Event Hubs:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.svg)

## <a name="event-hubs-on-azure-stack-hub"></a>Event Hubs in Azure Stack Hub
Met Event Hubs in Azure Stack Hub kunt u hybride cloudscenario's verwezenlijken. Streamen en op gebeurtenissen gebaseerde oplossingen worden ondersteund, zowel on-premises als voor Azure-cloudverwerking. Of uw scenario nu hybride (verbonden) is of op zichzelf staat, uw oplossing biedt ondersteuning voor de verwerking van gebeurtenissen/streams op grote schaal. Uw scenario is enkel afhankelijk van de grootte van de Event Hubs-cluster, die u kunt inrichten volgens uw behoeften. 

De Event Hubs-versies (op Azure Stack Hub en op Azure) bieden een hoge functiepariteit. Deze pariteit betekent dat SDK's, voorbeelden, PowerShell, CLI en portalen dezelfde ervaring bieden met weinig verschillen. 

Event Hubs in Stack is beschikbaar tijdens de openbare preview. Zie het [overzicht voor Event Hubs in Azure Stack Hub](/azure-stack/user/event-hubs-overview) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Zie de zelfstudies **Gebeurtenissen verzenden en ontvangen** om aan de slag te gaan met Event Hubs:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (alleen verzenden)](event-hubs-c-getstarted-send.md)
- [Apache Storm (alleen ontvangen)](event-hubs-storm-getstarted-receive.md)


Raadpleeg de volgende artikelen voor meer informatie over Event Hubs:

- [Overzicht van functies van Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen](event-hubs-faq.md).
