---
title: Meer Azure IoT Hub MQTT-| Microsoft Docs
description: Ondersteuning voor apparaten die verbinding maken met een IoT Hub apparaat-gericht eindpunt met behulp van het MQTT-protocol. Bevat informatie over ingebouwde MQTT-ondersteuning in de Azure IoT Device SDK's.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
- contperf-fy21q1
- fasttrack-edit
- iot
ms.openlocfilehash: 077b4ea6c7d4506e29673cbb4d6d89a92657b9c1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873681"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Communiceren met uw IoT-hub met het MQTT-protocol

IoT Hub kunnen apparaten communiceren met de eindpunten van IoT Hub apparaat met behulp van:

* [MQTT v3.1.1](https://mqtt.org/) op poort 8883
* MQTT v3.1.1 via WebSocket op poort 443.

IoT Hub is niet een volledig functionele MQTT-broker en biedt geen ondersteuning voor al het gedrag in de MQTT v3.1.1-standaard. In dit artikel wordt beschreven hoe apparaten ondersteund MQTT-gedrag kunnen gebruiken om te communiceren met IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Alle apparaatcommunicatie met IoT Hub moet worden beveiligd met TLS/SSL. Daarom biedt IoT Hub geen ondersteuning voor niet-beveiligde verbindingen via poort 1883.

## <a name="connecting-to-iot-hub"></a>Verbinding maken met IoT Hub

Een apparaat kan het MQTT-protocol gebruiken om verbinding te maken met een IoT-hub met behulp van een van de volgende opties.

* Bibliotheken in de [Azure IoT SDK's](https://github.com/Azure/azure-iot-sdks).
* Het MQTT-protocol rechtstreeks.

De MQTT-poort (8883) is geblokkeerd in veel bedrijfs- en onderwijsnetwerkomgevingen. Als u poort 8883 niet kunt openen in uw firewall, raden we u aan MQTT via websockets te gebruiken. MQTT via Web Sockets communiceert via poort 443, die bijna altijd open is in netwerkomgevingen. Zie Using the device SDK's (De apparaat-SDK's gebruiken) voor meer informatie over het opgeven van de MQTT- en MQTT-protocollen via websocketprotocollen bij het gebruik van de Azure IoT [SDK's.](#using-the-device-sdks)

## <a name="using-the-device-sdks"></a>De apparaat-SDK's gebruiken

[Apparaat-SDK's](https://github.com/Azure/azure-iot-sdks) die ondersteuning bieden voor het MQTT-protocol zijn beschikbaar voor Java, Node.js, C, C# en Python. De apparaat-SDK's gebruiken de IoT Hub connection string om verbinding te maken met een IoT-hub. Als u het MQTT-protocol wilt gebruiken, moet de clientprotocolparameter worden ingesteld **op MQTT**. U kunt MQTT ook opgeven via websockets in de clientprotocolparameter. De apparaat-SDK's maken standaard verbinding met een IoT Hub met de **vlag CleanSession** ingesteld op **0** en gebruiken **QoS 1** voor berichtuitwisseling met de IoT-hub. Hoewel het mogelijk is om **QoS 0** te configureren voor een snellere uitwisseling van berichten, moet u er rekening mee houden dat de levering niet wordt gegarandeerd of bevestigd. Daarom wordt **QoS 0** vaak 'fire and forget' genoemd.

Wanneer een apparaat is verbonden met een IoT-hub, bieden de apparaat-SDK's methoden waarmee het apparaat berichten kan uitwisselen met een IoT-hub.

De volgende tabel bevat koppelingen naar codevoorbeelden voor elke ondersteunde taal en geeft de parameter op die moet worden gebruikt om een verbinding met IoT Hub tot stand te brengen met behulp van het MQTT- of MQTT-protocol via Web Sockets.

| Taal | MQTT-protocolparameter | Protocolparameter MQTT via Web Sockets
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt. Mqtt | azure-iot-device-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol). MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](/dotnet/api/microsoft.azure.devices.client.transporttype). Mqtt | TransportType.Mqtt valt terug naar MQTT via websockets als MQTT uitvalt. Als u alleen MQTT via websockets wilt opgeven, gebruikt u TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Ondersteunt MQTT standaard | Voeg `websockets=True` toe aan de aanroep om de client te maken |

In het volgende fragment ziet u hoe u het MQTT via Web Sockets-protocol opgeeft bij het gebruik van de Azure IoT Node.js SDK:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

In het volgende fragment ziet u hoe u het MQTT via Web Sockets-protocol opgeeft wanneer u de Azure IoT Python SDK gebruikt:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Standaard time-out voor keep-alive

Om ervoor te zorgen dat een client-/IoT Hub verbinding blijft werken, verzenden zowel de service als de client regelmatig een *keep-alive* ping naar elkaar. De client die IoT SDK gebruikt, verzendt een keep-alive op het interval dat in deze tabel hieronder is gedefinieerd:

|Taal  |Standaardinterval voor keep-alive  |Configureerbaar  |
|---------|---------|---------|
|Node.js     |   180 seconden      |     No    |
|Java     |    230 seconden     |     No    |
|C     | 240 seconden |  [Ja](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 seconden |  [Ja](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 seconden |  No   |

Na de [MQTT-specificatie](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)is IoT Hub interval voor keep-alive ping 1,5 keer de keep-alive-waarde van de client. Met IoT Hub wordt de maximale time-out aan de serverzijde echter beperkt tot 29,45 minuten (1767 seconden), omdat alle Azure-services zijn gebonden aan de time-out voor inactieve TCP van Azure load balancer, die 29,45 minuten bedraagt. 

Een apparaat dat bijvoorbeeld de Java-SDK gebruikt, verzendt de keep-alive ping en verliest vervolgens de netwerkverbinding. 230 seconden later mist het apparaat de keep-alive ping omdat het offline is. De IoT Hub wordt echter niet onmiddellijk verbroken. Er wordt nog een seconden gewacht voordat de verbinding met het apparaat wordt verbroken met de fout `(230 * 1.5) - 230 = 115` [404104 DeviceConnectionClosedRemotely.](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md) 

De maximale keep-alive-waarde voor de client die u kunt instellen, is `1767 / 1.5 = 1177` seconden. Verkeer stelt de keep-alive opnieuw in. Als bijvoorbeeld het SAS-token is vernieuwd, wordt de keep-alive opnieuw ingesteld.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Een apparaat-app migreren van AMQP naar MQTT

Als u de [apparaat-SDK's](https://github.com/Azure/azure-iot-sdks)gebruikt, moet u voor het overschakelen van AMQP naar MQTT de protocolparameter wijzigen in de client-initialisatie, zoals eerder vermeld.

Als u dit doet, controleert u de volgende items:

* AMQP retourneert fouten voor veel voorwaarden, terwijl MQTT de verbinding beëindigt. Als gevolg hiervan zijn er voor de afhandelingslogica van uitzonderingen mogelijk enkele wijzigingen vereist.

* MQTT biedt geen ondersteuning voor de *weigeringsbewerkingen* bij het ontvangen van [cloud-naar-apparaat-berichten.](iot-hub-devguide-messaging.md) Als uw back-end-app een antwoord van de apparaat-app moet ontvangen, kunt u overwegen om [directe methoden te gebruiken.](iot-hub-devguide-direct-methods.md)

* AMQP wordt niet ondersteund in de Python SDK.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Voorbeeld in C met MQTT zonder een Azure IoT SDK

In de [voorbeeldopslagplaats van IoT MQTT](https://github.com/Azure-Samples/IoTMQTTSample)vindt u een aantal C/C++-demoprojecten die laten zien hoe u telemetrieberichten verzendt en gebeurtenissen ontvangt met een IoT-hub zonder de Azure IoT C SDK te gebruiken. 

In deze voorbeelden wordt de Eclipse Mosquitto-bibliotheek gebruikt om berichten te verzenden naar de MQTT Broker die is geïmplementeerd in de IoT-hub.

Zie Tutorial - Use MQTT to develop an IoT Plug en Play device client (Zelfstudie : MQTT gebruiken om een IoT-apparaatclient Plug en Play ontwikkelen) voor meer informatie over het aanpassen van de voorbeelden voor het gebruik van de [Azure IoT](../iot-pnp/overview-iot-plug-and-play.md) [Plug en Play-conventies.](../iot-pnp/tutorial-use-mqtt.md)

Deze opslagplaats bevat:

**Voor Windows:**

* TelemetryMQTTWin32: bevat code voor het verzenden van een telemetriebericht naar een Azure IoT-hub, gebouwd en uitgevoerd op een Windows-computer.

* SubscribeMQTTWin32: bevat code om u te abonneren op gebeurtenissen van een bepaalde IoT-hub op een Windows-computer.

* DeviceTwinMQTTWin32: bevat code voor het opvragen en abonneren op de apparaattwin-gebeurtenissen van een apparaat in de Azure IoT-hub op een Windows-computer.

* PnPMQTTWin32: bevat code voor het verzenden van een telemetriebericht met IoT Plug en Play-apparaatmogelijkheden naar een Azure IoT-hub, gebouwd en uitgevoerd op een Windows-computer. Meer informatie over [IoT-Plug en Play](../iot-pnp/overview-iot-plug-and-play.md)

**Voor Linux:**

* MQTTLinux: bevat code en buildscript om te worden uitgevoerd op Linux (WSL, Ubuntu en Raspbian zijn tot nu toe getest).

* LinuxConsoleVS2019: bevat dezelfde code, maar in een VS2019-project dat gericht is op WSL (Windows Linux-subsysteem). Met dit project kunt u stapsgewijs fouten opsporen in de code die in Linux wordt uitgevoerd Visual Studio.

**Voor mosquitto_pub:**

Deze map bevat twee voorbeelden opdrachten die worden gebruikt met mosquitto_pub hulpprogramma geleverd door Mosquitto.org.

* Mosquitto_sendmessage: om een eenvoudig tekstbericht te verzenden naar een Azure IoT-hub die als een apparaat optreedt.

* Mosquitto_subscribe: om gebeurtenissen te zien die plaatsvinden in een Azure IoT-hub.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Het MQTT-protocol rechtstreeks gebruiken (als een apparaat)

Als een apparaat de apparaat-SDK's niet kan gebruiken, kan het nog steeds verbinding maken met de openbare apparaat-eindpunten met behulp van het MQTT-protocol op poort 8883. In het **CONNECT-pakket** moet het apparaat de volgende waarden gebruiken:

* Gebruik voor **het veld ClientId** de **deviceId**.

* Gebruik voor **het** veld Gebruikersnaam , waarbij de volledige `{iothubhostname}/{device_id}/?api-version=2018-06-30` `{iothubhostname}` CName van de IoT-hub is.

    Als de naam van uw IoT-hub bijvoorbeeld **contoso.azure-devices.net** en als de naam van uw apparaat **MyDevice01** is, moet het volledige veld **Gebruikersnaam** het volgende bevatten:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

    Het wordt sterk aanbevolen om de API-versie in het veld op te nemen. Anders kan dit onverwacht gedrag veroorzaken. 
    
* Gebruik voor **het veld** Wachtwoord een SAS-token. De indeling van het SAS-token is hetzelfde als voor zowel de HTTPS- als AMQP-protocollen:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Als u X.509-certificaatverificatie gebruikt, zijn sas-tokenwachtwoorden niet vereist. Zie Set [up X.509 security in your Azure IoT Hub (X.509-beveiliging](iot-hub-security-x509-get-started.md) instellen in uw Azure IoT Hub) en volg de code-instructies in de [sectie TLS/SSL-configuratie voor meer informatie.](#tlsssl-configuration)

  Zie de apparaatsectie van Using IoT Hub security tokens (Beveiligingstokens gebruiken) voor meer informatie over het [genereren van SAS-tokens.](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

  Tijdens het testen kunt u ook de platformoverschrijdende Azure IoT Tools voor [Visual Studio Code of](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) de CLI-extensieopdracht az [iot hub generate-sas-token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) gebruiken om snel een SAS-token te genereren dat u in uw eigen code kunt kopiëren en plakken.

### <a name="for-azure-iot-tools"></a>Voor Azure IoT Tools

1. Vouw het **tabblad AZURE IOT HUB DEVICES** in de linkeronderhoek van Visual Studio Code uit.
  
2. Klik met de rechtermuisknop op uw apparaat en **selecteer SAS-token genereren voor Apparaat**.
  
3. Stel **de verlooptijd** in en druk op Enter.
  
4. Het SAS-token wordt gemaakt en gekopieerd naar het klembord.

   Het SAS-token dat wordt gegenereerd, heeft de volgende structuur:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Het gedeelte van dit token dat moet worden gebruikt als **het veld Wachtwoord** om verbinding te maken met behulp van MQTT is:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Voor MQTT-pakketten verbinden en de verbinding met pakketten verbreken, IoT Hub een gebeurtenis op het **Operations Monitoring-kanaal.** Deze gebeurtenis heeft aanvullende informatie die u kan helpen bij het oplossen van verbindingsproblemen.

De apparaat-app kan een **Will-bericht** opgeven in het **CONNECT-pakket.** De apparaat-app moet of gebruiken als de onderwerpnaam Will om Will-berichten te definiëren die `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` moeten worden doorgestuurd als een telemetriebericht.   Als in dit geval de netwerkverbinding wordt verbroken, maar een **DISCONNECT-pakket** niet  eerder van het apparaat is ontvangen, verzendt IoT Hub het will-bericht dat is opgegeven in het **CONNECT-pakket** naar het telemetriekanaal. Het telemetriekanaal kan het  standaardgebeurtenis-eindpunt zijn of een aangepast eindpunt dat is gedefinieerd door IoT Hub routering. Het bericht heeft de **eigenschap iothub-MessageType** met de waarde **Will** toegewezen.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Het MQTT-protocol rechtstreeks gebruiken (als module)

Verbinding maken met IoT Hub via MQTT met behulp van een module-id is vergelijkbaar met het apparaat (beschreven in de sectie over het rechtstreeks als een apparaat gebruiken van het [MQTT-protocol),](#using-the-mqtt-protocol-directly-as-a-device)maar u moet het volgende gebruiken:

* Stel de client-id in op `{device_id}/{module_id}` .

* Als u met een gebruikersnaam en wachtwoord wilt verifiëren, stelt u de gebruikersnaam in op en gebruikt u het SAS-token dat is gekoppeld aan de `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` module-id als uw wachtwoord.

* Gebruik `devices/{device_id}/modules/{module_id}/messages/events/` als onderwerp voor het publiceren van telemetrie.

* Gebruik `devices/{device_id}/modules/{module_id}/messages/events/` als WILL-onderwerp.

* De get- en PATCH-onderwerpen van de tweeling zijn identiek voor modules en apparaten.

* Het onderwerp status van de tweeling is identiek voor modules en apparaten.

## <a name="tlsssl-configuration"></a>TLS/SSL-configuratie

Als u het MQTT-protocol rechtstreeks wilt gebruiken, moet *uw* client verbinding maken via TLS/SSL. Pogingen om deze stap over te slaan mislukken met verbindingsfouten.

Als u een TLS-verbinding tot stand wilt brengen, moet u mogelijk het DigiCert Baltimore-basiscertificaat downloaden en hier naar verwijzen. Dit certificaat wordt door Azure gebruikt om de verbinding te beveiligen. U vindt dit certificaat in de [opslagplaats Azure-iot-sdk-c.](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) Meer informatie over deze certificaten vindt u op de [website van Digicert.](https://www.digicert.com/digicert-root-certificates.htm)

Een voorbeeld van hoe u dit implementeert met behulp van de Python-versie van de [Paho MQTT-bibliotheek](https://pypi.python.org/pypi/paho-mqtt) van de Eclipse Foundation kan er als volgt uitzien.

Installeer eerst de Paho-bibliotheek vanuit uw opdrachtregelomgeving:

```cmd/sh
pip install paho-mqtt
```

Implementeert vervolgens de client in een Python-script. Vervang de tijdelijke aanduidingen als volgt:

* `<local path to digicert.cer>` is het pad naar een lokaal bestand dat het DigiCert Baltimore Root-certificaat bevat. U kunt dit bestand maken door de certificaatgegevens van [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) te kopiëren in de Azure IoT SDK voor C. Neem de regels en op, verwijder de markeringen aan het begin en einde van elke regel en verwijder de tekens aan het einde van `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` elke `"` `\r\n` regel.

* `<device id from device registry>` is de id van een apparaat dat u hebt toegevoegd aan uw IoT-hub.

* `<generated SAS token>` is een SAS-token voor het apparaat dat is gemaakt, zoals eerder in dit artikel is beschreven.

* `<iot hub name>` de naam van uw IoT-hub.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Als u wilt verifiëren met behulp van een apparaatcertificaat, werkt u het bovenstaande codefragment bij met de volgende wijzigingen (zie How [to get an X.509 CA certificate](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) on how to prepare for certificate-based authentication ):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Apparaat-naar-cloud-berichten verzenden

Nadat de verbinding tot stand is brengen, kan een apparaat berichten naar IoT Hub verzenden met `devices/{device_id}/messages/events/` of `devices/{device_id}/messages/events/{property_bag}` als **onderwerpnaam.** Met `{property_bag}` het -element kan het apparaat berichten met aanvullende eigenschappen verzenden in een indeling met URL-codering. Bijvoorbeeld:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Dit `{property_bag}` element maakt gebruik van dezelfde codering als queryreeksen in het HTTPS-protocol.

Hier volgt een lijst met IoT Hub implementatiespecifieke gedragingen:

* IoT Hub biedt geen ondersteuning voor QoS 2-berichten. Als een apparaat-app een bericht publiceert met **QoS 2,** wordt IoT Hub de netwerkverbinding gesloten.

* IoT Hub blijven Berichten behouden niet behouden. Als een apparaat een bericht verzendt met de **vlag RETAIN** ingesteld op 1, IoT Hub de toepassings-eigenschap **mqtt-retain** aan het bericht toegevoegd. In dit geval wordt het bericht niet behouden, maar IoT Hub aan de back-end-app.

* IoT Hub ondersteunt slechts één actieve MQTT-verbinding per apparaat. Een nieuwe MQTT-verbinding namens dezelfde apparaat-id zorgt ervoor dat IoT Hub de bestaande verbinding laat vallen en **400027 ConnectionForcefullyClosedOnNewConnection** wordt aangemeld bij IoT Hub Logs


Zie de ontwikkelaarshandleiding [voor Messaging voor meer informatie.](iot-hub-devguide-messaging.md)

## <a name="receiving-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten ontvangen

Als u berichten van IoT Hub wilt ontvangen, moet een apparaat zich abonneren met `devices/{device_id}/messages/devicebound/#` als **onderwerpfilter.** Het jokerteken op meerdere niveau in het onderwerpfilter wordt alleen gebruikt om toe te staan dat het apparaat aanvullende eigenschappen `#` in de onderwerpnaam ontvangt. IoT Hub staat het gebruik van de jokertekens of `#` voor het filteren van `?` subtopics niet toe. Omdat IoT Hub geen broker voor pubsubberichten voor algemeen gebruik is, worden alleen de gedocumenteerde onderwerpnamen en onderwerpfilters ondersteund.

Het apparaat ontvangt geen berichten van IoT Hub totdat het is geabonneerd op het apparaatspecifieke eindpunt, vertegenwoordigd door het `devices/{device_id}/messages/devicebound/#` onderwerpfilter. Nadat een abonnement tot stand is gebracht, ontvangt het apparaat cloud-naar-apparaat-berichten die naar het apparaat zijn verzonden na de tijd van het abonnement. Als het apparaat verbinding maakt met **de vlag CleanSession** ingesteld op **0,** blijft het abonnement bestaan tussen verschillende sessies. In dit geval ontvangt het apparaat de volgende keer dat het verbinding maakt met **CleanSession 0** openstaande berichten die naar het apparaat worden verzonden terwijl de verbinding is verbroken. Als het apparaat echter **de cleanSession-vlag** gebruikt die is ingesteld op **1,** ontvangt het geen berichten van IoT Hub totdat het zich abonneert op het apparaat-eindpunt.

IoT Hub levert berichten met de **onderwerpnaam** `devices/{device_id}/messages/devicebound/` of wanneer er `devices/{device_id}/messages/devicebound/{property_bag}` berichteigenschappen zijn. `{property_bag}` bevat url-gecodeerde sleutel-waardeparen van berichteigenschappen. Alleen toepassingseigenschappen en door de gebruiker in te stellen systeemeigenschappen (zoals **messageId** of **correlationId)** zijn opgenomen in de eigenschappenset. Systeemeigenschappennamen hebben het voorvoegsel **$** , toepassingseigenschappen gebruiken de oorspronkelijke eigenschapsnaam zonder voorvoegsel. Zie [Apparaat-naar-cloud-berichten](#sending-device-to-cloud-messages)verzenden voor meer informatie over de indeling van de eigenschappentas.

In cloud-naar-apparaat-berichten worden waarden in de eigenschappentas weergegeven zoals in de volgende tabel:

| Eigenschapswaarde | Vertegenwoordiging | Description |
|----|----|----|
| `null` | `key` | Alleen de sleutel wordt weergegeven in de eigenschappentas |
| lege tekenreeks | `key=` | De sleutel gevolgd door een gelijkteken zonder waarde |
| niet-null, niet-lege waarde | `key=value` | De sleutel gevolgd door een gelijkteken en de waarde |

In het volgende voorbeeld ziet u een eigenschappentas die drie toepassingseigenschappen bevat: **prop1** met de waarde `null` ; **prop2,** een lege tekenreeks (""); en **prop3** met de waarde 'een tekenreeks'.

```mqtt
/?prop1&prop2=&prop3=a%20string
```

Wanneer een apparaat-app zich abonneert op een onderwerp met **QoS 2,** IoT Hub maximaal QoS-niveau 1 in het **SUBACK-pakket** toegekend. Daarna levert IoT Hub berichten aan het apparaat met behulp van QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>De eigenschappen van een apparaat dubbel ophalen

Eerst abonneert een apparaat zich op om de reacties van de `$iothub/twin/res/#` bewerking te ontvangen. Vervolgens wordt een leeg bericht naar het onderwerp `$iothub/twin/GET/?$rid={request id}` gestuurd, met een ingevulde waarde voor **aanvraag-id**. De service verzendt vervolgens een antwoordbericht met de gegevens van de apparaattwee in het onderwerp , met dezelfde `$iothub/twin/res/{status}/?$rid={request id}` **aanvraag-id** als de aanvraag.

Aanvraag-id kan elke geldige waarde voor een waarde voor een bericht-eigenschap zijn, volgens de [ontwikkelaarshandleiding voor IoT Hub messaging](iot-hub-devguide-messaging.md)en de status wordt gevalideerd als een geheel getal.

De antwoord-body bevat de sectie Eigenschappen van de apparaat dubbel, zoals wordt weergegeven in het volgende antwoordvoorbeeld:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

De mogelijke statuscodes zijn:

|Status | Beschrijving |
| ----- | ----------- |
| 200 | Geslaagd |
| 429 | Te veel aanvragen (beperkt), volgens [IoT Hub beperking](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfouten |

Zie de ontwikkelaarshandleiding voor [apparaattweeling voor meer informatie.](iot-hub-devguide-device-twins.md)

## <a name="update-device-twins-reported-properties"></a>Gerapporteerde eigenschappen van apparaat dubbel bijwerken

Als u gerapporteerde eigenschappen wilt bijwerken, moet het apparaat een IoT Hub via een publicatie via een aangewezen MQTT-onderwerp. Nadat de aanvraag is verwerkt, IoT Hub de status geslaagd of mislukt van de updatebewerking via een publicatie naar een ander onderwerp. Dit onderwerp kan door het apparaat worden geabonneerd om het te informeren over het resultaat van de dubbele updateaanvraag. Voor het implementeren van dit type interactie tussen aanvragen en antwoorden in MQTT maken we gebruik van de notatie van aanvraag-id ( ) die het apparaat in eerste instantie in de `$rid` updateaanvraag heeft opgegeven. Deze aanvraag-id is ook opgenomen in het antwoord van IoT Hub zodat het apparaat het antwoord kan correleren met de specifieke eerdere aanvraag.

In de volgende reeks wordt beschreven hoe een apparaat de gerapporteerde eigenschappen in de apparaat dubbel in IoT Hub:

1. Een apparaat moet zich eerst abonneren op het onderwerp om de reacties van de `$iothub/twin/res/#` bewerking te ontvangen van IoT Hub.

2. Een apparaat verzendt een bericht met de update van de apparaattweeling naar het `$iothub/twin/PATCH/properties/reported/?$rid={request id}` onderwerp. Dit bericht bevat een **waarde voor de aanvraag-id.**

3. De service verzendt vervolgens een antwoordbericht met de nieuwe ETag-waarde voor de gerapporteerde eigenschappenverzameling op onderwerp `$iothub/twin/res/{status}/?$rid={request id}` . Dit antwoordbericht gebruikt dezelfde **aanvraag-id als** de aanvraag.

De bericht-body van de aanvraag bevat een JSON-document met nieuwe waarden voor gerapporteerde eigenschappen. Elk lid in het JSON-document wordt bijgewerkt of het bijbehorende lid toevoegen aan het document van de apparaattweeling. Met een lid dat is `null` ingesteld op , wordt het lid verwijderd uit het object dat het bevat. Bijvoorbeeld:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

De mogelijke statuscodes zijn:

|Status | Beschrijving |
| ----- | ----------- |
| 204 | Geslaagd (er wordt geen inhoud geretourneerd) |
| 400 | Slechte aanvraag. Misvormde JSON |
| 429 | Te veel aanvragen (beperkt), volgens [IoT Hub beperking](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfouten |

In het onderstaande Python-codefragment ziet u het updateproces van de dubbel gerapporteerde eigenschappen via MQTT (met behulp van de Paho MQTT-client):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Na het slagen van de hierboven gerapporteerde updatebewerking voor dubbele eigenschappen, heeft het publicatiebericht van IoT Hub het volgende onderwerp: , waarbij de statuscode is die aangeeft dat de bewerking is geslaagd, overeenkomt met de aanvraag-id die is opgegeven door het apparaat in de code en komt overeen met de sectie met de versie van gerapporteerde eigenschappen van apparaat twins na de `$iothub/twin/res/204/?$rid=1&$version=6` `204` `$rid=1` `$version` update.

Zie de ontwikkelaarshandleiding voor [apparaattweeling voor meer informatie.](iot-hub-devguide-device-twins.md)

## <a name="receiving-desired-properties-update-notifications"></a>Meldingen ontvangen over het bijwerken van gewenste eigenschappen

Wanneer een apparaat is verbonden, IoT Hub meldingen naar het onderwerp , dat de inhoud bevat van de update die wordt uitgevoerd door de `$iothub/twin/PATCH/properties/desired/?$version={new version}` back-end van de oplossing. Bijvoorbeeld:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Net als bij updates van eigenschappen `null` betekenen waarden dat het JSON-objectlid wordt verwijderd. Houd er ook rekening mee `$version` dat de nieuwe versie van de sectie gewenste eigenschappen van de tweeling aangeeft.

> [!IMPORTANT]
> IoT Hub genereert alleen wijzigingsmeldingen wanneer apparaten zijn verbonden. Zorg ervoor dat u de stroom voor opnieuw verbinden van apparaten [implementeert](iot-hub-devguide-device-twins.md#device-reconnection-flow) om de gewenste eigenschappen gesynchroniseerd te houden IoT Hub de apparaat-app.

Zie de ontwikkelaarshandleiding voor [apparaattweeling voor meer informatie.](iot-hub-devguide-device-twins.md)

## <a name="respond-to-a-direct-method"></a>Reageren op een directe methode

Eerst moet een apparaat zich abonneren op `$iothub/methods/POST/#` . IoT Hub verzendt methodeaanvragen naar het onderwerp `$iothub/methods/POST/{method name}/?$rid={request id}` , met een geldige JSON of een lege body.

Om te reageren, verzendt het apparaat een bericht met een geldige JSON of lege body naar het onderwerp `$iothub/methods/res/{status}/?$rid={request id}` . In dit bericht moet de **aanvraag-id** overeenkomen met de id in het aanvraagbericht en **moet de status** een geheel getal zijn.

Zie de ontwikkelaarshandleiding voor directe methoden [voor meer informatie.](iot-hub-devguide-direct-methods.md)

## <a name="additional-considerations"></a>Aanvullende overwegingen

Als laatste overweging: als u het gedrag van het MQTT-protocol aan de cloudzijde moet aanpassen, moet u de gateway van het [Azure IoT-protocol controleren.](iot-hub-protocol-gateway.md) Met deze software kunt u een krachtige aangepaste protocolgateway implementeren die rechtstreeks met de IoT Hub. Met de Azure IoT-protocolgateway kunt u het apparaatprotocol aanpassen voor brownfield MQTT-implementaties of andere aangepaste protocollen. Deze aanpak vereist echter wel dat u een aangepaste protocolgateway kunt uitvoeren en gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie de MQTT-documentatie voor meer informatie over het [MQTT-protocol.](https://mqtt.org/)

Voor meer informatie over het plannen van IoT Hub implementatie, zie:

* [Azure Certified voor IoT-apparaatcatalogus](https://devicecatalog.azure.com/)
* [Ondersteuning voor aanvullende protocollen](iot-hub-protocol-gateway.md)
* [Vergelijken met Event Hubs](iot-hub-compare-event-hubs.md)
* [Schalen, ha en dr](iot-hub-scaling.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [IoT Hub ontwikkelaarshandleiding](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)
