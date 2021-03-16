---
title: Meer informatie over hoe de runtime apparaten beheert-Azure IoT Edge | Microsoft Docs
description: Meer informatie over hoe de IoT Edge runtime modules, beveiliging, communicatie en rapportage beheert op uw apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 74cfe4ba3c92d8d96dd196ef6f612b9ed7c0da9d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496249"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Inzicht in de runtime van Azure IoT Edge en de architectuur ervan

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

De IoT Edge-runtime is een verzameling programma's die ervoor zorgt dat u een apparaat als een IoT Edge-apparaat kunt gebruiken. De IoT Edge-runtime-onderdelen maken het samen IoT Edge apparaten de mogelijkheid code te ontvangen die aan de rand worden uitgevoerd en de resultaten te communiceren.

De IoT Edge runtime is verantwoordelijk voor de volgende functies op IoT Edge apparaten:

* Workloads op het apparaat installeren en bijwerken.

* De Azure IoT Edge-beveiligingsstandaarden op het apparaat onderhouden.

* Zorg ervoor dat [IOT Edge modules](iot-edge-modules.md) altijd worden uitgevoerd.

* De status van de module aan de cloud rapporteren voor externe bewaking.

* Communicatie tussen downstream-apparaten en IoT Edge apparaten beheren.

* Communicatie tussen modules op een IoT Edge apparaat beheren.

* De communicatie tussen een IoT Edge apparaat en de Cloud beheren.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* Communicatie tussen IoT Edge apparaten beheren.
::: moniker-end

![Runtime communiceert inzichten en module status naar IoT Hub](./media/iot-edge-runtime/Pipeline.png)

De verantwoordelijkheden van de IoT Edge runtime worden onderverdeeld in twee categorieën: communicatie en module beheer. Deze twee rollen worden uitgevoerd door twee onderdelen die deel uitmaken van de IoT Edge runtime. De *IOT Edge agent* implementeert en bewaakt de modules, terwijl de *IOT Edge hub* verantwoordelijk is voor communicatie.

Zowel de IoT Edge agent als de IoT Edge hub zijn modules, net zoals elke andere module die wordt uitgevoerd op een IoT Edge apparaat. Ze worden soms ook wel de *runtime modules* genoemd.

## <a name="iot-edge-agent"></a>IoT Edge-agent

De IoT Edge-agent is een van de twee modules waaruit de Azure IoT Edge-runtime bestaat. Het is verantwoordelijk voor het instantiëren van modules, om ervoor te zorgen dat ze blijven worden uitgevoerd en de status van de modules terug te rapporteren aan IoT Hub. Deze configuratie gegevens worden geschreven als een eigenschap van de IoT Edge Agent module dubbele.

De [IOT Edge Security daemon](iot-edge-security-manager.md) start de IOT Edge-agent op het opstarten van het apparaat. De-agent haalt de module twee van IoT Hub op en inspecteert het implementatie manifest. Het implementatie manifest is een JSON-bestand dat de modules declareert die moeten worden gestart.

Elk item in het implementatie manifest bevat specifieke informatie over een module en wordt gebruikt door de IoT Edge-agent voor het beheren van de levens cyclus van de module. Meer informatie over de eigenschappen die worden gebruikt door de IoT Edge-agent om modules te beheren, vindt u in de [Eigenschappen van de IOT Edge agent en de IOT Edge hub-module apparaatdubbels](module-edgeagent-edgehub.md).

De IoT Edge agent verzendt runtime-antwoord naar IoT Hub. Hier volgt een lijst met mogelijke reacties:
  
* 200-OK
* 400-de implementatie configuratie is onjuist of ongeldig.
* 417-er is geen implementatie configuratie ingesteld op het apparaat.
* 412-de schema versie in de implementatie configuratie is ongeldig.
* 406-het IoT Edge apparaat is offline of stuurt geen status rapporten.
* 500-er is een fout opgetreden in de IoT Edge-runtime.

Zie [informatie over het implementeren van modules en het maken van routes in IOT Edge](module-composition.md)voor meer informatie over het maken van implementatie manifesten.

### <a name="security"></a>Beveiliging

De IoT Edge-agent speelt een cruciale rol in de beveiliging van een IoT Edge apparaat. Er worden bijvoorbeeld acties uitgevoerd zoals het controleren van de installatie kopie van een module voordat deze wordt gestart.

Meer informatie over het Azure IoT Edge Security Framework vindt u in de [IOT Edge Security Manager](iot-edge-security-manager.md).

## <a name="iot-edge-hub"></a>IoT Edge hub

De IoT Edge hub is de andere module die de Azure IoT Edge runtime vormt. Het fungeert als een lokale proxy voor IoT Hub door dezelfde protocol eindpunten als IoT Hub weer te geven. Deze consistentie betekent dat clients verbinding kunnen maken met de IoT Edge runtime, net zoals ze zouden IoT Hub.

De IoT Edge hub is geen volledige versie van IoT Hub die lokaal wordt uitgevoerd. IoT Edge hub delegeert sommige taken op de achtergrond aan IoT Hub. IoT Edge hub downloadt bijvoorbeeld automatisch verificatie gegevens van IoT Hub bij de eerste verbinding om een apparaat in staat te stellen verbinding te maken. Nadat de eerste verbinding tot stand is gebracht, worden de verificatie gegevens lokaal opgeslagen in de cache van IoT Edge hub. Toekomstige verbindingen van dat apparaat zijn geautoriseerd zonder dat u opnieuw autorisatie-informatie uit de Cloud hoeft te downloaden.

### <a name="cloud-communication"></a>Cloud communicatie

Om de band breedte te verminderen die uw IoT Edge oplossing gebruikt, optimaliseert de IoT Edge hub het aantal werkelijke verbindingen dat in de Cloud wordt gemaakt. IoT Edge hub maakt logische verbindingen van modules of downstream-apparaten en combineert deze voor één fysieke verbinding met de Cloud. De details van dit proces zijn transparant voor de rest van de oplossing. Clients denken dat ze hun eigen verbinding met de Cloud hebben, zelfs als ze allemaal via dezelfde verbinding worden verzonden. De IoT Edge hub kan de AMQP of het MQTT-protocol gebruiken om upstream te communiceren met de Cloud, onafhankelijk van protocollen die worden gebruikt door downstream-apparaten. De IoT Edge hub ondersteunt echter alleen het combi neren van logische verbindingen in één fysieke verbinding door AMQP als het upstream-protocol en de bijbehorende multiplexing-mogelijkheden te gebruiken. AMQP is het standaard upstream-protocol.

![IoT Edge hub is een gateway tussen fysieke apparaten en IoT Hub](./media/iot-edge-runtime/gateway-communication.png)

IoT Edge hub kunt bepalen of deze is verbonden met IoT Hub. Als de verbinding is verbroken, slaat IoT Edge hub berichten of dubbele updates lokaal op. Zodra een verbinding tot stand is gebracht, worden alle gegevens gesynchroniseerd. De locatie die wordt gebruikt voor deze tijdelijke cache wordt bepaald door een eigenschap van de module van de IoT Edge hub. De grootte van de cache wordt niet afgetopt en zal groeien zolang het apparaat opslag capaciteit heeft. Zie [offline mogelijkheden](offline-capabilities.md)voor meer informatie.

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>Module communicatie

IoT Edge-hub vereenvoudigt de communicatie tussen modules. Als u IoT Edge hub als Message Broker gebruikt, blijven de modules onafhankelijk van elkaar. Modules hoeven alleen de invoer op te geven waarmee de berichten worden geaccepteerd en de uitvoer waarmee ze berichten schrijven. Een oplossings ontwikkelaar kan deze invoer en uitvoer samen voegen, zodat de modules gegevens verwerken in de volg orde die specifiek is voor die oplossing.

![IoT Edge hub vereenvoudigt de communicatie van module-naar-module](./media/iot-edge-runtime/module-endpoints.png)

Als u gegevens naar de IoT Edge hub wilt verzenden, roept een module de SendEventAsync-methode aan. Het eerste argument geeft aan op welke uitvoer het bericht moet worden verzonden. De volgende pseudocode verzendt een bericht op **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Als u een bericht wilt ontvangen, registreert u een call back waarmee berichten worden verwerkt die in een specifieke invoer worden weer gegeven. De volgende pseudocode registreert de functie messageProcessor die moet worden gebruikt voor het verwerken van alle berichten die op **input1** worden ontvangen:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Voor meer informatie over de ModuleClient-klasse en de communicatie methoden raadpleegt u de API-verwijzing voor uw voorkeurs taal van de SDK: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)of [Node.js](/javascript/api/azure-iot-device/moduleclient).

De oplossings ontwikkelaar is verantwoordelijk voor het opgeven van de regels die bepalen hoe IoT Edge hub berichten tussen modules doorgeeft. Routerings regels worden gedefinieerd in de Cloud en naar IoT Edge hub gepusht in de module dubbele. Dezelfde syntaxis voor IoT Hub routes wordt gebruikt voor het definiëren van routes tussen modules in Azure IoT Edge. Zie [informatie over het implementeren van modules en het tot stand brengen van routes in IOT Edge](module-composition.md)voor meer informatie.

![Routes tussen modules worden IoT Edge hub door lopen](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Lokale communicatie

IoT Edge hub vereenvoudigt lokale communicatie. Hiermee schakelt u apparaat-naar-module-, module-naar-apparaat-communicatie in door de Broker van berichten om apparaten en modules onafhankelijk van elkaar te laten blijven.

>[!NOTE]
> De functie MQTT Broker bevindt zich in de open bare Preview met IoT Edge versie 1,2. Deze moet expliciet worden ingeschakeld.

De IoT Edge hub ondersteunt twee Broker mechanismen:

1. De [functies voor bericht routering die worden ondersteund door IOT hub](../iot-hub/iot-hub-devguide-messages-d2c.md) en,
2. Een MQTT-Broker voor algemeen gebruik die voldoet aan de [MQTT-standaard v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)

#### <a name="using-routing"></a>Route ring gebruiken

Het eerste Broker-mechanisme maakt gebruik van dezelfde routerings functies als IoT Hub om op te geven hoe berichten tussen apparaten of modules worden door gegeven. Bij de eerste apparaten of modules worden de invoer gegevens van berichten en de uitvoer waarnaar ze berichten schrijven, opgegeven. Vervolgens kan een oplossings ontwikkelaar berichten tussen een bron, zoals uitvoer, en een doel, bijvoorbeeld invoer, met mogelijke filters routeren.

![Routes tussen modules worden IoT Edge hub door lopen](./media/iot-edge-runtime/module-endpoints-routing.png)

Route ring kan worden gebruikt door apparaten of modules die zijn gebouwd met de Sdk's van het Azure IoT-apparaat via de AMQP of het MQTT-protocol. Alle Messa ging IoT Hub primitieven, zoals telemetrie, direct methods, C2D, apparaatdubbels, worden ondersteund, maar communicatie over door de gebruiker gedefinieerde onderwerpen wordt niet ondersteund.

Zie [informatie over het implementeren van modules en het tot stand brengen van routes in IOT Edge](module-composition.md) voor meer informatie over routes.

#### <a name="using-the-mqtt-broker"></a>De MQTT-Broker gebruiken

Het tweede Broker-mechanisme is gebaseerd op een Standard MQTT-Broker. MQTT is een licht gewicht protocol voor bericht overdracht dat optimale prestaties van op bronnen beperkte apparaten garandeert en een populaire standaard voor publiceren en abonneren is. Apparaten of modules abonneren op onderwerpen om berichten te ontvangen die worden gepubliceerd door andere apparaten of modules. IoT Edge hub implementeert een eigen MQTT-Broker die voldoet [aan de specificaties van MQTT versie 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html).

De MQTT Broker maakt twee extra communicatie patronen in vergelijking met route ring: lokale broadcast en Point-to-point-communicatie. Lokale broadcast is handig wanneer een apparaat of module meerdere andere apparaten of modules lokaal moet waarschuwen. Met Point-to-point communicatie kunnen twee IoT Edge apparaten of twee IoT-apparaten lokaal communiceren zonder retour naar de Cloud.

![Lokaal publiceren en abonneren met IoT Edge hub](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

De MQTT-Broker kan worden gebruikt door apparaten of modules die zijn gebouwd met de Azure IoT-apparaat-Sdk's die communiceren via het MQTT-protocol of door MQTT-clients voor algemeen gebruik. Met uitzonde ring van C2D alle Messa ging IoT Hub primitieven, zoals telemetrie, direct methods, apparaatdubbels worden ondersteund. IoT Hub speciale onderwerpen die worden gebruikt door IoT Hub primitieven worden ondersteund en zijn dus door de gebruiker gedefinieerde onderwerpen.
Dit onderwerp kan een IoT Hub speciaal onderwerp of een door de gebruiker gedefinieerd onderwerp zijn.

In tegens telling tot het routerings mechanisme is de volg orde van berichten alleen het beste en niet gegarandeerd en filteren van berichten wordt niet ondersteund door de Broker. Het ontbreken van deze functies kan de MQTT-Broker echter sneller gebruiken dan route ring.

Zie voor meer informatie over de MQTT-Broker [publiceren en abonneren met IOT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>Vergelijking tussen Broker-mechanismen

Dit zijn de beschik bare functies voor elk Broker mechanisme:

|Functies  | Routering  | MQTT Broker  |
|---------|---------|---------|
|D2C-telemetrie    |     &#10004;    |         |
|Lokale telemetrie     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|Dubbel     |    &#10004;     |    &#10004;     |
|C2D voor apparaten     |   &#10004;      |         |
|Ordenen     |    &#10004;     |         |
|Filteren     |     &#10004;    |         |
|Door de gebruiker gedefinieerde onderwerpen     |         |    &#10004;     |
|Apparaat-naar-apparaat     |         |    &#10004;     |
|Lokale omroep     |         |    &#10004;     |
|Prestaties     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>Verbinding maken met de IoT Edge hub

De IoT Edge hub accepteert verbindingen van apparaat-of module-clients, hetzij via het MQTT-protocol of het AMQP-protocol.

>[!NOTE]
> IoT Edge hub ondersteunt clients die verbinding maken met behulp van MQTT of AMQP. Clients die HTTP gebruiken, worden niet ondersteund.

Wanneer een client verbinding maakt met de IoT Edge hub, gebeurt het volgende:

1. Als Transport Layer Security (TLS) wordt gebruikt (aanbevolen), wordt er een TLS-kanaal gebouwd om een versleutelde communicatie tussen de client en de IoT Edge hub tot stand te brengen.
2. Verificatie gegevens worden van de client naar IoT Edge hub verzonden om zichzelf te identificeren.
3. IoT Edge hub machtigt of weigert de verbinding op basis van het autorisatie beleid.

#### <a name="secure-connections-tls"></a>Beveiligde verbindingen (TLS)

Standaard accepteert de IoT Edge hub alleen verbindingen die zijn beveiligd met Transport Layer Security (TLS), bijvoorbeeld versleutelde verbindingen die niet kunnen worden ontsleuteld door derden.

Als een client verbinding maakt met poort 8883 (MQTTS) of 5671 (AMQPS) naar de IoT Edge hub, moet een TLS-kanaal worden gebouwd. Tijdens de TLS-Handshake verzendt de IoT Edge hub zijn certificaat keten die de client moet valideren. Als u de certificaat keten wilt valideren, moet het basis certificaat van de IoT Edge hub als vertrouwd certificaat op de client zijn geïnstalleerd. Als het basis certificaat niet wordt vertrouwd, wordt de client bibliotheek door de IoT Edge hub afgewezen met een certificaat verificatie fout.

De stappen die u moet volgen om dit basis certificaat van de Broker op apparaatclients te installeren, worden beschreven in de [transparante gateway](how-to-create-transparent-gateway.md) en in de documentatie van [een downstream-apparaat voorbereiden](how-to-connect-downstream-device.md#prepare-a-downstream-device) . Modules kunnen hetzelfde basis certificaat gebruiken als de IoT Edge hub door gebruik te maken van de IoT Edge daemon API.

#### <a name="authentication"></a>Verificatie

De IoT Edge hub accepteert alleen verbindingen van apparaten of modules die een IoT Hub-identiteit hebben, bijvoorbeeld die zijn geregistreerd in IoT Hub en een van de drie client authenticatie methoden hebben die door IoT hub worden ondersteund om te voorzien in de identiteit: [symmetrische sleutels verificatie](how-to-authenticate-downstream-device.md#symmetric-key-authentication), [x. 509 zelfondertekende authenticatie](how-to-authenticate-downstream-device.md#x509-self-signed-authentication), [x. 509 CA-ondertekende verificatie](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  Deze IoT Hub-identiteiten kunnen lokaal door de IoT Edge hub worden geverifieerd, zodat er nog steeds verbinding kan worden gemaakt met offline.

Opmerkingen:

* IoT Edge-modules ondersteunen momenteel alleen symmetrische sleutel verificatie.
* MQTT-clients met alleen lokale gebruikers naam en wacht woorden worden niet geaccepteerd door de IoT Edge hub MQTT Broker, ze moeten IoT Hub-identiteiten gebruiken.

#### <a name="authorization"></a>Autorisatie

Nadat de IoT Edge-hub is geverifieerd, heeft deze twee manieren om client verbindingen te autoriseren:

* Door te controleren of een client behoort tot de set vertrouwde clients die zijn gedefinieerd in IoT Hub. De set vertrouwde clients wordt opgegeven door het instellen van relaties tussen bovenliggende en onderliggende items of apparaten/modules in IoT Hub. Wanneer een module wordt gemaakt in IoT Edge, wordt er automatisch een vertrouwens relatie tot stand gebracht tussen deze module en het IoT Edge apparaat. Dit is het enige autorisatie model dat wordt ondersteund door het Routing Broker-mechanisme.

* Door een autorisatie beleid in te stellen. Dit autorisatie beleid is een document met alle geautoriseerde client identiteiten die toegang hebben tot resources op de IoT Edge hub. Dit is het primaire autorisatie model dat wordt gebruikt door de IoT Edge hub MQTT Broker, hoewel de relaties bovenliggend/onderliggend en apparaat/module ook kunnen worden begrepen door de MQTT-Broker voor IoT Hub onderwerpen.

### <a name="remote-configuration"></a>Externe configuratie

De IoT Edge hub wordt volledig beheerd door de Cloud. De configuratie wordt opgehaald uit IoT Hub via de bijbehorende [module dubbele](iot-edge-modules.md#module-twins). Het bevat:

* Routes configureren
* Autorisatie beleid
* Configuratie van MQTT-brug

Daarnaast kunt u verschillende configuraties uitvoeren door [omgevings variabelen in te stellen op de IOT Edge hub](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Telemetrie-kwaliteit van runtime

IoT Edge verzamelt anonieme telemetrie van de runtime van de host en systeem modules om de product kwaliteit te verbeteren. Deze informatie wordt telemetrie voor de runtime kwaliteit genoemd. De verzamelde telemetrie wordt periodiek verzonden als apparaat-naar-Cloud-berichten IoT Hub van de IoT Edge agent. Deze berichten worden niet weer gegeven in de normale telemetrie van de klant en verbruiken geen quota voor berichten.

De IoT Edge agent en hub genereren metrische gegevens die u kunt verzamelen om de prestaties van het apparaat te begrijpen. Een subset van deze metrische gegevens wordt door de IoT Edge-agent verzameld als onderdeel van de telemetrie-kwaliteit van de uitvoering. De metrische gegevens die worden verzameld voor de telemetrie van de runtime kwaliteit, worden aangeduid met het label `ms_telemetry` . Zie voor meer informatie over alle beschik bare metrische gegevens [toegang tot ingebouwde metrische](how-to-access-built-in-metrics.md)gegevens.

Alle persoonlijke of organisatief Identificeer bare gegevens, zoals apparaat-en module namen, worden vóór het uploaden verwijderd om ervoor te zorgen dat de anonieme aard van de telemetrie van de runtime kwaliteit wordt gegarandeerd.

De IoT Edge-agent verzamelt elk uur de telemetrie en stuurt één bericht naar IoT Hub om de 24 uur.

Als u het verzenden van runtime-telemetrie van uw apparaten wilt afmelden, zijn er twee manieren om dit te doen:

* Stel de `SendRuntimeQualityTelemetry` omgevings variabele in op `false` **edgeAgent** of
* Schakel de optie in de Azure Portal uit tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

* [Informatie over Azure IoT Edge-modules](iot-edge-modules.md)
* [Meer informatie over het implementeren van modules en het vaststellen van routes naar IoT Edge](module-composition.md)
* [Meer informatie over het publiceren en abonneren met IoT Edge](how-to-publish-subscribe.md)
* [Meer informatie over de metrische gegevens van IoT Edge runtime](how-to-access-built-in-metrics.md)
