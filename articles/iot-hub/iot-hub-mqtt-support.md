---
title: Meer informatie over ondersteuning voor Azure IoT Hub MQTT | Microsoft Docs
description: Ondersteuning voor apparaten die verbinding maken met een IoT Hub apparaat gericht eind punt met behulp van het MQTT-protocol. Bevat informatie over ingebouwde MQTT-ondersteuning in de Azure IoT-apparaat-Sdk's.
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
ms.openlocfilehash: 9678648b6417138e216ba2dce3a3605bb4c1bce4
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169229"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Communiceren met uw IoT-hub met het MQTT-protocol

Met IoT Hub kunnen apparaten communiceren met de eind punten van IoT Hub apparaten met behulp van:

* [MQTT v 3.1.1](https://mqtt.org/) op poort 8883
* MQTT v 3.1.1 over WebSocket op poort 443.

IoT Hub is niet een volledig functionele MQTT-broker en biedt geen ondersteuning voor al het gedrag in de MQTT v3.1.1-standaard. In dit artikel wordt beschreven hoe apparaten ondersteund MQTT-gedrag kunnen gebruiken om te communiceren met IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

De communicatie van alle apparaten met IoT Hub moet worden beveiligd met behulp van TLS/SSL. Daarom biedt IoT Hub geen ondersteuning voor niet-beveiligde verbindingen via poort 1883.

## <a name="connecting-to-iot-hub"></a>Verbinding maken met IoT Hub

Een apparaat kan het MQTT-protocol gebruiken om verbinding te maken met een IoT-hub met behulp van een van de volgende opties.

* Bibliotheken in de [Azure IOT sdk's](https://github.com/Azure/azure-iot-sdks).
* Het MQTT-protocol direct.

De MQTT-poort (8883) is geblokkeerd in veel bedrijfs-en educatieve netwerk omgevingen. Als u poort 8883 niet in uw firewall kunt openen, kunt u het beste MQTT via web Sockets gebruiken. MQTT via web sockets communiceert via poort 443, wat bijna altijd is geopend in netwerk omgevingen. Zie [de apparaat-Sdk's gebruiken](#using-the-device-sdks)voor meer informatie over het opgeven van de MQTT en MQTT via web sockets-protocollen wanneer u de Azure IOT sdk's gebruikt.

## <a name="using-the-device-sdks"></a>De apparaat-Sdk's gebruiken

[Apparaat-sdk's](https://github.com/Azure/azure-iot-sdks) die het MQTT-protocol ondersteunen, zijn beschikbaar voor Java, Node.js, C, C# en python. De Sdk's van het apparaat maken gebruik van de Standard-IoT Hub connection string om een verbinding met een IoT-hub tot stand te brengen. Als u het MQTT-protocol wilt gebruiken, moet de para meter client protocol worden ingesteld op **MQTT**. U kunt ook MQTT via web sockets opgeven in de para meter client protocol. De Sdk's van het apparaat maken standaard verbinding met een IoT Hub met de vlag **CleanSession** ingesteld op **0** en gebruiken **QoS 1** voor berichten uitwisseling met de IOT-hub. Hoewel het mogelijk is om **QoS 0** te configureren voor een snellere uitwisseling van berichten, moet u er rekening mee houden dat de levering niet gegarandeerd noch bevestigd wordt. Daarom wordt **QoS 0** vaak aangeduid als brand en verg eten.

Wanneer een apparaat is verbonden met een IoT-hub, bieden de Sdk's van het apparaat methoden die het apparaat in staat stellen om berichten uit te wisselen met een IoT-hub.

De volgende tabel bevat koppelingen naar code voorbeelden voor elke ondersteunde taal en geeft de para meter op die moet worden gebruikt om een verbinding tot stand te brengen met IoT Hub met behulp van het MQTT-of het MQTT via web sockets-protocol.

| Taal | Para meter MQTT-Protocol | MQTT over web sockets protocol-para meter
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | Azure-IOT-Device-mqtt. Mqtt | Azure-IOT-Device-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol). MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [Transport type](/dotnet/api/microsoft.azure.devices.client.transporttype). Mqtt | Transport type. Mqtt valt terug naar MQTT via web sockets als MQTT mislukt. Als u alleen MQTT via web-sockets wilt opgeven, gebruikt u TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Biedt standaard ondersteuning voor MQTT | Toevoegen `websockets=True` in de aanroep voor het maken van de client |

Het volgende fragment laat zien hoe u het MQTT via web sockets kunt opgeven wanneer u de Azure IoT Node.js SDK gebruikt:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Het volgende fragment laat zien hoe u het MQTT via web sockets-protocol opgeeft wanneer u de Azure IoT python-SDK gebruikt:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Standaard time-out voor Keep-Alive

Om ervoor te zorgen dat een client/IoT Hub verbinding actief blijft, worden zowel de service als de client regel matig een *Keep-Alive* ping naar elkaar verzonden. De client die IoT SDK gebruikt, verzendt een Keep-Alive met het interval dat is gedefinieerd in deze tabel:

|Taal  |Standaard interval voor Keep-Alive  |Configureerbaar  |
|---------|---------|---------|
|Node.js     |   180 seconden      |     No    |
|Java     |    230 seconden     |     No    |
|C     | 240 seconden |  [Ja](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 seconden |  [Ja](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 seconden |  No   |

Na de [MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)-IOT hub specificatie is het Keep-Alive ping-interval van 1,5 keer de client Keep-Alive waarde. IoT Hub beperkt echter de maximale time-out aan de server zijde tot 29,45 minuten (1767 seconden), omdat alle Azure-Services zijn gebonden aan de Azure load balancer TCP-time-out voor inactiviteit, 29,45 minuten. 

Bijvoorbeeld, een apparaat dat de Java-SDK gebruikt, verzendt de Keep-Alive ping, waarna de netwerk verbinding wordt verbroken. 230 seconden later is het apparaat de Keep-Alive ping missen omdat het offline is. IoT Hub de verbinding echter niet onmiddellijk sluit, wacht u nog een `(230 * 1.5) - 230 = 115` paar seconden voordat u de verbinding met het apparaat verbreekt met de fout [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md). 

De maximale client Keep-Alive-waarde die u kunt instellen is `1767 / 1.5 = 1177` seconden. Elk verkeer stelt de Keep-Alive opnieuw in. Bijvoorbeeld: een geslaagde SAS-token vernieuwing stelt de Keep-Alive opnieuw in.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Een apparaat-app migreren van AMQP naar MQTT

Als u de apparaat- [sdk's](https://github.com/Azure/azure-iot-sdks)gebruikt, moet u bij het overschakelen van het gebruik van AMQP naar MQTT de para meter protocol wijzigen in de initialisatie van de client, zoals eerder is aangegeven.

Zorg er daarom voor dat u de volgende items controleert:

* AMQP retourneert fouten voor veel omstandigheden, terwijl MQTT de verbinding beëindigt. Als gevolg hiervan kan de logica voor het afhandelen van uitzonde ringen enkele wijzigingen vereisen.

* MQTT biedt geen ondersteuning voor de *afwijzings* bewerkingen wanneer er [Cloud-naar-apparaat-berichten worden](iot-hub-devguide-messaging.md)ontvangen. Als uw back-end-app een reactie van de apparaat-app moet ontvangen, overweeg dan om [directe methoden](iot-hub-devguide-direct-methods.md)te gebruiken.

* AMQP wordt niet ondersteund in de python-SDK.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Voor beeld in C met MQTT zonder Azure IoT SDK

In de [MQTT van de IOT-voorbeeld opslagplaats](https://github.com/Azure-Samples/IoTMQTTSample)vindt u een aantal C/C++-demo projecten die laten zien hoe u telemetrie-berichten verzendt en gebeurtenissen ontvangt met een IOT-hub zonder de Azure IOT C SDK te gebruiken. 

In deze voor beelden wordt de Mosquitto-bibliotheek voor eclips gebruikt voor het verzenden van berichten naar de MQTT-Broker die in de IoT hub is geïmplementeerd.

Voor meer informatie over het aanpassen van de voor beelden voor het gebruik van de [Azure IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md) -conventies, Zie [zelf studie-MQTT gebruiken om een IOT Plug en Play Device-client te ontwikkelen](../iot-pnp/tutorial-use-mqtt.md).

Deze opslag plaats bevat:

**Voor Windows:**

* TelemetryMQTTWin32: bevat code voor het verzenden van een telemetrie-bericht naar een Azure IoT hub, gebouwd en uitgevoerd op een Windows-computer.

* SubscribeMQTTWin32: bevat code voor het abonneren op gebeurtenissen van een bepaalde IoT-hub op een Windows-computer.

* DeviceTwinMQTTWin32: bevat code voor het zoeken naar en abonneren op de dubbele gebeurtenissen van een apparaat in de Azure IoT hub op een Windows-computer.

* PnPMQTTWin32: bevat code voor het verzenden van een telemetrie-bericht met IoT Plug en Play-apparaatfuncties naar een Azure IoT hub, gebouwd en uitgevoerd op een Windows-computer. Meer informatie vindt u in [IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md)

**Voor Linux:**

* MQTTLinux: bevat code en bouw script voor uitvoering op Linux (WSL, Ubuntu en Raspbian zijn tot nu toe getest).

* LinuxConsoleVS2019: bevat dezelfde code, maar in een VS2019-project gericht WSL (Windows Linux-subsysteem). Met dit project kunt u fouten opsporen in de code die wordt uitgevoerd in Linux stap voor stap van Visual Studio.

**Voor mosquitto_pub:**

Deze map bevat twee voor beelden van opdrachten die worden gebruikt met mosquitto_pub hulp programma van Mosquitto.org.

* Mosquitto_sendmessage: een eenvoudig tekst bericht verzenden naar een Azure IoT hub die fungeert als een apparaat.

* Mosquitto_subscribe: om gebeurtenissen weer te geven die in een Azure IoT-hub optreden.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Het MQTT-protocol direct gebruiken (als een apparaat)

Als een apparaat de Sdk's van het apparaat niet kan gebruiken, kan het nog steeds verbinding maken met de eind punten van het open bare apparaat met behulp van het MQTT-protocol op poort 8883. In het **Connect** -pakket moet het apparaat de volgende waarden gebruiken:

* Gebruik voor het veld **ClientId** het **apparaat**-id.

* Voor het veld **username** gebruikt u `{iothubhostname}/{device_id}/?api-version=2018-06-30` , waarbij `{iothubhostname}` de volledige CNAME is van de IOT-hub.

    Als de naam van uw IoT-hub bijvoorbeeld **contoso.Azure-devices.net** is en als de naam van uw apparaat **MyDevice01** is, moet het veld volledige **gebruikers naam** het volgende bevatten:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

    Het is raadzaam om in het veld API-Version te vermelden. Anders kan dit leiden tot onverwacht gedrag. 
    
* Gebruik voor het **wachtwoord** veld een SAS-token. De indeling van de SAS-token is hetzelfde als voor de HTTPS-en AMQP-protocollen:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Als u X. 509-certificaat authenticatie gebruikt, zijn SAS-token wachtwoorden niet vereist. Zie voor meer informatie [X. 509-beveiliging instellen in uw Azure IOT hub](iot-hub-security-x509-get-started.md) en de code-instructies volgen in het [gedeelte TLS/SSL-configuratie](#tlsssl-configuration).

  Zie voor meer informatie over het genereren van SAS-tokens het gedeelte apparaat van het [gebruik van IOT hub beveiligings tokens](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Bij het testen kunt u ook de platformoverschrijdende [Azure IOT-Hulpprogram ma's voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) of de CLI-extensie opdracht [AZ IOT hub generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token) gebruiken om snel een SAS-token te genereren dat u kunt kopiëren en plakken in uw eigen code.

### <a name="for-azure-iot-tools"></a>Voor Azure IoT-Hulpprogram Ma's

1. Vouw het tabblad **apparaten van Azure IOT hub** uit in de linkerbovenhoek van Visual Studio code.
  
2. Klik met de rechter muisknop op uw apparaat en selecteer **SAS-token voor apparaat genereren**.
  
3. Stel de **verval tijd** in en druk op ENTER.
  
4. Het SAS-token wordt gemaakt en naar het klem bord gekopieerd.

   Het SAS-token dat wordt gegenereerd, heeft de volgende structuur:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Het deel van dit token dat moet worden gebruikt als het **wachtwoord** veld om verbinding te maken met MQTT is:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Voor MQTT Connect-en Disconnect-pakketten IoT Hub een gebeurtenis op het kanaal van de **Operations-bewaking** . Deze gebeurtenis bevat aanvullende informatie die u kan helpen bij het oplossen van verbindings problemen.

De apparaat-app **kan een bericht** in het **Connect** -pakket opgeven. De app van het apparaat moet de naam van het `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` onderwerp gebruiken om  te definiëren dat berichten worden doorgestuurd als een telemetrie-bericht.  Als de netwerk verbinding is gesloten, maar er nog geen **Verbrekings** pakket van het apparaat is ontvangen, stuurt IOT hub **het bericht dat** in het **Connect** -pakket is opgegeven, naar het telemetrie-kanaal. Het telemetrie-kanaal kan het eind punt van de standaard **gebeurtenissen** zijn of een aangepast eind punt dat is gedefinieerd door IOT hub route ring. Aan het bericht is de eigenschap **iothub-Message type** met de waarde **van toegewezen** .

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Het MQTT-protocol direct gebruiken (als een module)

Verbinding maken met IoT Hub via MQTT via een module-identiteit is vergelijkbaar met het apparaat (zoals beschreven [in de sectie over het direct gebruiken van het MQTT-protocol als een apparaat](#using-the-mqtt-protocol-directly-as-a-device)), maar u moet het volgende gebruiken:

* Stel de client-ID in op `{device_id}/{module_id}` .

* Als verificatie met gebruikers naam en wacht woord, stelt u de gebruikers naam in op `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` en gebruikt u het SAS-token dat is gekoppeld aan de module identiteit als uw wacht woord.

* Gebruiken `devices/{device_id}/modules/{module_id}/messages/events/` als onderwerp voor het publiceren van telemetrie.

* Gebruik `devices/{device_id}/modules/{module_id}/messages/events/` als onderwerp.

* De dubbele GET-en PATCH-onderwerpen zijn identiek voor modules en apparaten.

* Het onderwerp dubbele status is identiek voor modules en apparaten.

## <a name="tlsssl-configuration"></a>TLS/SSL-configuratie

Als u het MQTT-protocol direct wilt gebruiken, *moet* de client verbinding maken via TLS/SSL. Pogingen om deze stap over te slaan mislukken met verbindings fouten.

Als u een TLS-verbinding wilt maken, moet u mogelijk het DigiCert Baltimore-basis certificaat downloaden en hiernaar verwijzen. Dit certificaat is de versie die door Azure wordt gebruikt om de verbinding te beveiligen. U kunt dit certificaat vinden in de [Azure-IOT-SDK-c-](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) opslag plaats. Meer informatie over deze certificaten vindt u op [de website van Digicert](https://www.digicert.com/digicert-root-certificates.htm).

Een voor beeld van hoe u dit kunt implementeren met behulp van de python-versie van de [PAHO MQTT-bibliotheek](https://pypi.python.org/pypi/paho-mqtt) van de eclips-basis, kan er als volgt uitzien.

Installeer eerst de PAHO-bibliotheek vanaf uw opdracht regel omgeving:

```cmd/sh
pip install paho-mqtt
```

Implementeer vervolgens de client in een python-script. Vervang de tijdelijke aanduidingen als volgt:

* `<local path to digicert.cer>` is het pad naar een lokaal bestand dat het DigiCert Baltimore-basis certificaat bevat. U kunt dit bestand maken door de certificaat gegevens te kopiëren van [certificaten. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) in de Azure IOT SDK voor c. Voeg de regels toe `-----BEGIN CERTIFICATE-----` en `-----END CERTIFICATE-----` Verwijder de `"` markeringen aan het begin en het einde van elke regel, en verwijder de `\r\n` tekens aan het einde van elke regel.

* `<device id from device registry>` is de ID van een apparaat dat u hebt toegevoegd aan uw IoT-hub.

* `<generated SAS token>` is een SAS-token voor het apparaat dat is gemaakt zoals eerder in dit artikel is beschreven.

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

Als u wilt verifiëren met behulp van een certificaat voor een apparaat, werkt u het code fragment hierboven bij met de volgende wijzigingen (Zie [een X. 509 CA-certificaat verkrijgen](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) over het voorbereiden op verificatie op basis van een certificaat):

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

## <a name="sending-device-to-cloud-messages"></a>Apparaat-naar-Cloud-berichten verzenden

Na een geslaagde verbinding kan een apparaat berichten verzenden naar IoT Hub met `devices/{device_id}/messages/events/` of `devices/{device_id}/messages/events/{property_bag}` als **onderwerpnaam**. Met het- `{property_bag}` element kan het apparaat berichten verzenden met aanvullende eigenschappen in een indeling met URL-code ring. Bijvoorbeeld:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Dit `{property_bag}` element maakt gebruik van dezelfde code ring als query reeksen in het HTTPS-protocol.

Hier volgt een lijst met IoT Hub implementatie-specifieke gedragingen:

* IoT Hub biedt geen ondersteuning voor QoS 2-berichten. Als een apparaat-app een bericht publiceert met **QoS 2**, wordt de netwerk verbinding IOT hub gesloten.

* IoT Hub blijven geen berichten behouden. Als een apparaat een bericht verzendt waarbij de vlag **bewaren** is ingesteld op 1, IOT hub voegt de eigenschap **mqtt-retain** toe aan het bericht. In dit geval wordt in plaats van het behouden bericht te blijven IoT Hub door gegeven aan de back-end-app.

* IoT Hub ondersteunt slechts één actieve MQTT-verbinding per apparaat. Een nieuwe MQTT-verbinding namens dezelfde apparaat-ID leidt ertoe IoT Hub de bestaande verbinding te verwijderen en **400027 ConnectionForcefullyClosedOnNewConnection** wordt geregistreerd in IOT hub logboeken


Zie voor meer informatie [de hand leiding voor SMS-ontwikkel aars](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten ontvangen

Als u berichten van IoT Hub wilt ontvangen, moet een apparaat zich abonneren met `devices/{device_id}/messages/devicebound/#` als een **onderwerps filter**. Het Joker teken op meerdere niveaus `#` in het onderwerps filter wordt alleen gebruikt om het apparaat in staat te stellen extra eigenschappen in de onderwerpnaam te ontvangen. IoT Hub staat het gebruik van de of- `#` `?` joker tekens voor het filteren van subonderwerpen niet toe. Omdat IoT Hub geen pub-submessa ging Broker voor algemeen gebruik is, worden alleen de gedocumenteerde onderwerps namen en onderwerps filters ondersteund.

Het apparaat ontvangt geen berichten van IoT Hub totdat het is geabonneerd op het apparaat-specifieke eind punt, vertegenwoordigd door het `devices/{device_id}/messages/devicebound/#` onderwerps filter. Nadat een abonnement is ingesteld, ontvangt het apparaat Cloud-naar-apparaat-berichten die zijn verzonden na het tijdstip van het abonnement. Als het apparaat verbinding maakt met de vlag **CleanSession** ingesteld op **0**, wordt het abonnement gehandhaafd in verschillende sessies. In dit geval wordt de volgende keer dat het apparaat verbinding maakt met **CleanSession 0** alle openstaande berichten ontvangen die worden verzonden terwijl de verbinding is verbroken. Als het apparaat gebruikmaakt van een **CleanSession** -vlag ingesteld op **1** , worden er geen berichten van IOT hub ontvangen totdat deze zich op het eind punt van het apparaat abonneert.

IoT Hub levert berichten met de **onderwerpnaam** `devices/{device_id}/messages/devicebound/` of `devices/{device_id}/messages/devicebound/{property_bag}` wanneer er bericht eigenschappen zijn. `{property_bag}` bevat URL-gecodeerde sleutel/waarde-paren van bericht eigenschappen. Alleen toepassings eigenschappen en door de gebruiker instel bare systeem eigenschappen (zoals **messageId** of **correlationId**) worden opgenomen in de eigenschappen verzameling. De namen van systeem eigenschappen hebben het voor voegsel **$** , toepassings eigenschappen gebruiken de oorspronkelijke eigenschaps naam zonder voor voegsel. Zie het [verzenden van apparaat-naar-Cloud-berichten](#sending-device-to-cloud-messages)voor meer informatie over de indeling van de eigenschappen verzameling.

In Cloud-naar-apparaat-berichten worden de waarden in de eigenschappen verzameling weer gegeven, zoals in de volgende tabel:

| Eigenschaps waarde | Wijze | Description |
|----|----|----|
| `null` | `key` | Alleen de sleutel wordt weer gegeven in de eigenschappen verzameling |
| lege teken reeks | `key=` | De sleutel gevolgd door een gelijkteken zonder waarde |
| niet-null, niet-lege waarde | `key=value` | De sleutel gevolgd door een gelijkteken en de waarde |

In het volgende voor beeld ziet u een eigenschappen verzameling die drie toepassings eigenschappen bevat: **prop1** met de waarde `null` ; **prop2**, een lege teken reeks (""); en **prop3** met de waarde "een teken reeks".

```mqtt
/?prop1&prop2=&prop3=a%20string
```

Wanneer een apparaat-app zich abonneert op een onderwerp met **QoS 2**, geeft IOT hub Maxi maal QoS-niveau 1 in het **SUBACK** -pakket. Daarna levert IoT Hub berichten naar het apparaat met behulp van QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>De eigenschappen van een onderliggend apparaat ophalen

Eerst wordt een apparaat geabonneerd op om `$iothub/twin/res/#` de reacties van de bewerking te ontvangen. Vervolgens wordt er een leeg bericht naar het onderwerp verzonden `$iothub/twin/GET/?$rid={request id}` met een ingevuld waarde voor **aanvraag-id**. De service verzendt vervolgens een antwoord bericht met daarin het apparaat dubbele gegevens over `$iothub/twin/res/{status}/?$rid={request id}` het onderwerp, waarbij dezelfde **aanvraag-id** wordt gebruikt als voor de aanvraag.

De aanvraag-ID kan bestaan uit een geldige waarde voor de waarde van een bericht eigenschap, zoals wordt weer gegeven in de [hand leiding voor ontwikkel aars van IOT hub berichten](iot-hub-devguide-messaging.md)en de status wordt gevalideerd als een geheel getal.

De hoofd tekst van het antwoord bevat de sectie eigenschappen van het apparaat, zoals wordt weer gegeven in het volgende voor beeld van een antwoord:

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

De mogelijke status codes zijn:

|Status | Beschrijving |
| ----- | ----------- |
| 200 | Geslaagd |
| 429 | Te veel aanvragen (beperkt), conform [IOT hub beperking](iot-hub-devguide-quotas-throttling.md) |
| 5 * * | Serverfouten |

Zie de hand leiding voor de [ontwikkel aars van het apparaat](iot-hub-devguide-device-twins.md)voor meer informatie.

## <a name="update-device-twins-reported-properties"></a>De gerapporteerde eigenschappen van het apparaat bijwerken

Voor het bijwerken van de gerapporteerde eigenschappen geeft het apparaat een verzoek om IoT Hub via een publicatie in een aangewezen MQTT-onderwerp. Nadat de aanvraag is verwerkt, reageert IoT Hub de geslaagde of mislukte status van de update bewerking via een publicatie naar een ander onderwerp. Dit onderwerp kan worden geabonneerd op het apparaat, zodat het kan worden gewaarschuwd over het resultaat van de dubbele update aanvraag. Voor het implementeren van dit type aanvraag/antwoord interactie in MQTT maken we gebruik van het principe van de aanvraag-ID ( `$rid` ) die in eerste instantie door het apparaat wordt gegeven in de update-aanvraag. Deze aanvraag-ID is ook opgenomen in de reactie van IoT Hub, zodat het apparaat de reactie op de specifieke eerdere aanvraag kan correleren.

In de volgende reeks wordt beschreven hoe een apparaat de gerapporteerde eigenschappen bijwerkt in het apparaat dubbele in IoT Hub:

1. Een apparaat moet eerst worden geabonneerd op het `$iothub/twin/res/#` onderwerp om de reacties van de bewerking van IOT hub te ontvangen.

2. Een apparaat verzendt een bericht dat de dubbele update van het apparaat naar het `$iothub/twin/PATCH/properties/reported/?$rid={request id}` onderwerp bevat. Dit bericht bevat een **aanvraag-ID-** waarde.

3. De service verzendt vervolgens een antwoord bericht met daarin de nieuwe ETag-waarde voor de gerapporteerde eigenschappen verzameling in het onderwerp `$iothub/twin/res/{status}/?$rid={request id}` . Dit antwoord bericht gebruikt dezelfde **aanvraag-id** als de aanvraag.

De hoofd tekst van het aanvraag bericht bevat een JSON-document dat nieuwe waarden voor gerapporteerde eigenschappen bevat. Elk lid in het JSON-document of voegt het overeenkomstige lid toe aan het document van het dubbele apparaat. Een ledenset om `null` het lid van het insluitende object te verwijderen. Bijvoorbeeld:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

De mogelijke status codes zijn:

|Status | Beschrijving |
| ----- | ----------- |
| 204 | Geslaagd (er is geen inhoud geretourneerd) |
| 400 | Ongeldige aanvraag. Onjuist gevormde JSON |
| 429 | Te veel aanvragen (beperkt), conform [IOT hub beperking](iot-hub-devguide-quotas-throttling.md) |
| 5 * * | Serverfouten |

In het onderstaande code fragment python ziet u het dubbele gerapporteerde eigenschappen update proces via MQTT (met behulp van PAHO MQTT-client):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Bij het slagen van dubbele gerapporteerde update-bewerking hierboven heeft het publicatie bericht van IoT Hub het volgende onderwerp: `$iothub/twin/res/204/?$rid=1&$version=6` , waarbij `204` de status code aangeeft dat geslaagd is, `$rid=1` overeenkomt met de aanvraag-id die is opgegeven door het apparaat in de code en `$version` overeenkomt met de versie van de sectie gerapporteerde eigenschappen van Device apparaatdubbels na de update.

Zie de hand leiding voor de [ontwikkel aars van het apparaat](iot-hub-devguide-device-twins.md)voor meer informatie.

## <a name="receiving-desired-properties-update-notifications"></a>Meldingen voor gewenste eigenschappen van updates ontvangen

Wanneer een apparaat is verbonden, stuurt IoT Hub meldingen naar het onderwerp `$iothub/twin/PATCH/properties/desired/?$version={new version}` , dat de inhoud bevat van de update die is uitgevoerd door de back-end van de oplossing. Bijvoorbeeld:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Als voor updates van eigenschappen, `null` betekenen waarden dat het lid van het JSON-object wordt verwijderd. Houd er ook rekening mee dat `$version` de nieuwe versie van het gedeelte gewenste eigenschappen van de dubbele.

> [!IMPORTANT]
> IoT Hub genereert alleen wijzigings meldingen wanneer apparaten zijn verbonden. Zorg ervoor dat u de [stroom](iot-hub-devguide-device-twins.md#device-reconnection-flow) voor het opnieuw verbinden van apparaten implementeert om ervoor te zorgen dat de gewenste eigenschappen gesynchroniseerd worden tussen IOT hub en de apparaat-app.

Zie de hand leiding voor de [ontwikkel aars van het apparaat](iot-hub-devguide-device-twins.md)voor meer informatie.

## <a name="respond-to-a-direct-method"></a>Reageren op een directe methode

Eerst moet een apparaat zich abonneren op `$iothub/methods/POST/#` . IoT Hub verzendt methode aanvragen naar het onderwerp `$iothub/methods/POST/{method name}/?$rid={request id}` met een geldige JSON of een lege hoofd tekst.

Om te reageren, stuurt het apparaat een bericht met een geldige JSON of lege hoofd tekst naar het onderwerp `$iothub/methods/res/{status}/?$rid={request id}` . In dit bericht moet de **aanvraag-id** overeenkomen met het account in het aanvraag bericht en moet de **status** een geheel getal zijn.

Zie de [ontwikkelaars handleiding voor direct-methoden](iot-hub-devguide-direct-methods.md)voor meer informatie.

## <a name="additional-considerations"></a>Aanvullende overwegingen

Als laatste overweging: als u het MQTT-protocol gedrag aan de Cloud zijde moet aanpassen, moet u de [Azure IOT-protocol gateway](iot-hub-protocol-gateway.md)door nemen. Met deze software kunt u een aangepaste protocol gateway met hoge prestaties implementeren die rechtstreeks is verbonden met IoT Hub. Met de Azure IoT-protocol gateway kunt u het Protocol van het apparaat aanpassen om Brownfield MQTT-implementaties of andere aangepaste protocollen aan te passen. Deze benadering vereist echter dat u een aangepaste protocol gateway uitvoert en gebruikt.

## <a name="next-steps"></a>Volgende stappen

Zie de [MQTT-documentatie](https://mqtt.org/)voor meer informatie over het MQTT-protocol.

Zie voor meer informatie over het plannen van uw IoT Hub-implementatie:

* [Azure Certified voor IoT-apparaatcatalogus](https://devicecatalog.azure.com/)
* [Aanvullende protocollen ondersteunen](iot-hub-protocol-gateway.md)
* [Vergelijken met Event Hubs](iot-hub-compare-event-hubs.md)
* [Schalen, HA en DR](iot-hub-scaling.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)
