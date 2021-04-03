---
title: Azure Event Hubs-Exchange-gebeurtenissen met verschillende protocollen
description: In dit artikel wordt uitgelegd hoe consumenten en producenten die gebruikmaken van verschillende protocollen (AMQP, Apache Kafka en HTTPS), gebeurtenissen kunnen uitwisselen bij het gebruik van Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cbc6999e3ede73b948ce034769966922b4b0f282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010316"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Uitwisseling van gebeurtenissen tussen consumenten en producenten die verschillende protocollen gebruiken: AMQP, Kafka en HTTPS
Azure Event Hubs ondersteunt drie protocollen voor consumenten en producenten: AMQP, Kafka en HTTPS. Elk van deze protocollen heeft een eigen manier om een bericht weer te geven, waardoor natuurlijk de volgende vraag zich voordoet: als een toepassing gebeurtenissen verzendt naar een event hub met één protocol en deze gebruikt met een ander protocol, wat betekenen de verschillende onderdelen en waarden van de gebeurtenis eruit als bij de consument? In dit artikel worden aanbevolen procedures voor zowel de producent als de consument beschreven om ervoor te zorgen dat de waarden in een gebeurtenis correct worden geïnterpreteerd door de verbruikte toepassing.

In het advies in dit artikel worden deze clients specifiek behandeld, met de vermelde versies die worden gebruikt voor het ontwikkelen van de code fragmenten:

* Kafka Java-client (versie 1.1.1 van https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs-client voor Java (versie 1.1.0 van https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs-client voor .NET (versie 2.1.0 van https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (versie 5.0.0 van https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (ondersteunt alleen producenten)

Andere AMQP-clients gedragen zich mogelijk iets anders. AMQP heeft een goed gedefinieerd type systeem, maar de specifieke informatie over het serialiseren van taalspecifieke typen naar en van het type systeem is afhankelijk van de client, zoals hoe de client toegang biedt tot de onderdelen van een AMQP-bericht.

## <a name="event-body"></a>Hoofd tekst van gebeurtenis
Alle micro soft AMQP-clients vertegenwoordigen de gebeurtenis hoofdtekst als een niet-geïnterpreteerde verzameling bytes. Een produceertde toepassing geeft een reeks bytes door aan de client en een verbruiks toepassing ontvangt diezelfde volg orde van de client. De interpretatie van de byte reeks vindt plaats in de toepassings code.

Wanneer een gebeurtenis via HTTPS wordt verzonden, is de gebeurtenis hoofdtekst de geposte inhoud, die ook wordt behandeld als niet-geïnterpreteerde bytes. Het is eenvoudig om dezelfde status te krijgen in een Kafka producent of consument door gebruik te maken van de meegeleverde ByteArraySerializer en ByteArrayDeserializer, zoals wordt weer gegeven in de volgende code:

### <a name="kafka-byte-producer"></a>Kafka byte [] producer

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte [] Consumer
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Met deze code wordt een transparante byte pijplijn tussen de twee helften van de toepassing gemaakt en kan de ontwikkelaar van de toepassing op een wille keurige manier hand matig serialiseren en deserialiseren, met inbegrip van het deserialiseren van beslissingen tijdens runtime, bijvoorbeeld op basis van de gegevens van het type of de afzender in de door de gebruiker ingestelde eigenschappen van de gebeurtenis.

Toepassingen met één vast hoofdtekst type voor gebeurtenissen kunnen andere Kafka-serialisatiefunctie gebruiken en deserials om gegevens transparant te converteren. Denk bijvoorbeeld aan een toepassing die gebruikmaakt van JSON. De constructie en interpretatie van de JSON-teken reeks gebeurt op toepassings niveau. Op Event Hubs niveau is de gebeurtenis hoofdtekst altijd een teken reeks, een reeks bytes die tekens in de UTF-8-code ring vertegenwoordigt. In dit geval kan de Kafka-producent of-consument profiteren van de meegeleverde StringSerializer of StringDeserializer, zoals wordt weer gegeven in de volgende code:

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8-teken reeks-producer
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8-teken reeks gebruiker
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Voor de AMQP-kant bieden zowel Java als .NET ingebouwde manieren om teken reeksen te converteren naar en van UTF-8-byte reeksen. De micro soft AMQP-clients vertegenwoordigen gebeurtenissen als een klasse met de naam Event Data. In de volgende voor beelden ziet u hoe u een UTF-8-teken reeks kunt serialiseren in een Event Data van een AMQP-producent, en hoe u een Event Data gebeurtenis hoofdtekst deserialiseren in een UTF-8-teken reeks in een AMQP-Consumer.

### <a name="java-amqp-utf-8-string-producer"></a>Producer voor Java AMQP UTF-8 String
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 string Consumer
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>Producer voor C# .NET UTF-8-teken reeks
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8-teken reeks consument
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Omdat Kafka open-source is, kan de ontwikkelaar van de toepassing de implementatie van elke serialisatiefunctie of deserializer controleren en code implementeren, die een compatibele reeks bytes op de AMQP-zijde produceert of gebruikt.

## <a name="event-user-properties"></a>Eigenschappen van gebeurtenis gebruiker

Eigenschappen van gebruikers sets kunnen worden ingesteld en opgehaald van beide AMQP-clients (in de micro soft AMQP-clients worden de eigenschappen genoemd) en Kafka (waar deze headers worden genoemd). HTTPS-afzenders kunnen gebruikers eigenschappen instellen voor een gebeurtenis door deze op te geven als HTTP-headers in de POST-bewerking. Kafka behandelt echter zowel gebeurtenis teksten als gebeurtenis header waarden als byte reeksen. Overwegende dat in AMQP-clients typen moeten worden gecommuniceerd door de eigenschaps waarden te coderen volgens het type systeem AMQP.

HTTPS is een speciaal geval. Op het moment van verzenden zijn alle eigenschapwaarden UTF-8-tekst. De Event Hubs-service biedt een beperkte mate van interpretatie voor het converteren van de juiste eigenschaps waarden naar met AMQP gecodeerde 32-bits en 64-bits ondertekende gehele getallen, 64-bits drijvende-komma cijfers en Booleaanse waarden. Elke eigenschaps waarde, die niet aan een van deze typen voldoet, wordt beschouwd als een teken reeks.

Door deze benaderingen te combi neren in eigenschaps typen, betekent dit dat een Kafka-Consumer de door de AMQP gecodeerde byte reeks ziet, met inbegrip van de AMQP-type gegevens. Terwijl een AMQP-Consumer ziet de niet-getypte byte reeks die door de Kafka-producent wordt verzonden, die de toepassing moet interpreteren.

Gebruik voor Kafka-consumenten die eigenschappen van AMQP of https-producenten ontvangen, de AmqpDeserializer-klasse, die is gemodelleerd na de andere deserialisatie in het Kafka-ecosysteem. Het interpreteert de type gegevens in de byte reeksen met AMQP-code ring om de gegevens bytes te deserialiseren in een Java-type.

Als best practice wordt u aangeraden een eigenschap op te nemen in berichten die via AMQP of HTTPS worden verzonden. De Kafka-consument kan deze gebruiken om te bepalen of header waarden AMQP-deserialisatie nodig hebben. De waarde van de eigenschap is niet belang rijk. Het heeft alleen een bekende naam nodig die de Kafka-consument kan vinden in de lijst met kopteksten en het gedrag dienovereenkomstig aan te passen.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP naar Kafka deel 1: een gebeurtenis maken en verzenden in C# (.NET) met eigenschappen
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP naar Kafka deel 2: gebruik AmqpDeserializer om die eigenschappen in een Kafka-consument te deserialiseren
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Als de toepassing het verwachte type voor een eigenschap kent, zijn er deserialisatie methoden waarvoor geen casting nodig is, maar treedt er een fout op als de eigenschap niet van het verwachte type is.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP naar Kafka deel 3: een andere manier om AmqpDeserializer te gebruiken in een Kafka-consument
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

De andere richting is meer betrokken, omdat kopteksten die door een Kafka-producent worden ingesteld, altijd worden gezien door een AMQP-consumer als onbewerkte bytes (type org. apache. QPid. Proton. AMQP. binary voor de Microsoft Azure Event Hubs-client voor Java of `System.Byte[]` voor .net AMQP-clients van micro soft). Het eenvoudigste pad is het gebruik van een van de door Kafka geleverde serialisatiefunctie voor het genereren van de bytes voor de header waarden aan de Kafka van de producent en vervolgens een compatibele deserialisatie code op de AMQP aan de gebruiker schrijven.

Net als bij AMQP-to-Kafka is het best practice dat u het beste een eigenschap moet toevoegen in berichten die via Kafka worden verzonden. De AMQP-consument kan de eigenschap gebruiken om te bepalen of koptekst waarden moeten worden gedeserialiseerd. De waarde van de eigenschap is niet belang rijk. Het heeft alleen een bekende naam nodig die de AMQP-consument kan vinden in de lijst met kopteksten en het gedrag dienovereenkomstig aan te passen. Als de Kafka-producent niet kan worden gewijzigd, is het ook mogelijk om te controleren of de waarde van de eigenschap van het type binary of byte is en om te deserialiseren op basis van het soort.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka naar AMQP deel 1: een gebeurtenis maken en verzenden vanuit Kafka met eigenschappen
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka naar AMQP deel 2: deze eigenschappen hand matig deserialiseren in C# (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka naar AMQP deel 3: deze eigenschappen hand matig deserialiseren in Java
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd u hoe u kunt streamen naar Event Hubs zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. Raadpleeg de volgende artikelen voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

* [Meer informatie over Event Hubs](./event-hubs-about.md)
* [Meer informatie over Event Hubs for Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Meer voorbeelden van de Event Hubs for Kafka-GitHub verkennen](https://github.com/Azure/azure-event-hubs-for-kafka)
* Gebruik [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) voor het [streamen van gebeurtenissen van Kafka on-premises naar Event hubs in de Cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* Meer informatie over het streamen van Event Hubs met behulp van [systeem eigen Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache flink](event-hubs-kafka-flink-tutorial.md)of [Akka-streams](event-hubs-kafka-akka-streams-tutorial.md)
