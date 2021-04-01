---
title: Meer informatie over ondersteuning voor Azure IoT Hub AMQP | Microsoft Docs
description: "Ontwikkelaars handleiding: ondersteuning voor apparaten die verbinding maken met IoT Hub op het apparaat gerichte en service gerichte eind punten met behulp van het AMQP-protocol. Bevat informatie over ingebouwde AMQP-ondersteuning in de Azure IoT-apparaat-Sdk's."
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7b3dcfc51df7f0fe4291e9c5babccc1444ad32e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81730757"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Communiceren met uw IoT-hub met behulp van het AMQP-Protocol

Azure IoT Hub ondersteunt [OASIS Advanced Message queueing Protocol (AMQP) versie 1,0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) om diverse functies te leveren via apparaten die aan het apparaat en de service gerichte eind punten. Dit document beschrijft het gebruik van AMQP-clients om verbinding te maken met een IoT-hub om IoT Hub functionaliteit te gebruiken.

## <a name="service-client"></a>Service-client

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Verbinding maken en verifiëren met een IoT-hub (Service-client)

Als u verbinding wilt maken met een IoT-hub met behulp van AMQP, kan een client gebruikmaken van de verificatie [op basis van op claims gebaseerde beveiliging (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) of [Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

De volgende informatie is vereist voor de service-client:

| Informatie | Waarde |
|-------------|--------------|
| IoT hub-hostnaam | `<iot-hub-name>.azure-devices.net` |
| Sleutelnaam | `service` |
| Toegangssleutel | Een primaire of secundaire sleutel die is gekoppeld aan de service |
| Shared Access Signature | Een korte hand tekening voor gedeelde toegang in de volgende indeling: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` . Zie [toegang tot IOT hub beheren](./iot-hub-devguide-security.md#security-token-structure)voor het verkrijgen van de code voor het genereren van deze hand tekening.

In het volgende code fragment wordt de [uAMQP-bibliotheek in python](https://github.com/Azure/azure-uamqp-python) gebruikt om verbinding te maken met een IOT-hub via een afzender koppeling.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Cloud-naar-apparaat-berichten aanroepen (Service-client)

Zie [Cloud-naar-apparaat-berichten vanuit uw IOT-hub verzenden](iot-hub-devguide-messages-c2d.md)voor meer informatie over de Cloud-naar-apparaat-bericht uitwisseling tussen de service en de IOT-hub en tussen het apparaat en de IOT-hub. De service-client gebruikt twee koppelingen om berichten te verzenden en feedback te ontvangen voor eerder verzonden berichten van apparaten, zoals wordt beschreven in de volgende tabel:

| Gemaakt door | Type koppeling | Pad van koppeling | Description |
|------------|-----------|-----------|-------------|
| Service | Koppeling naar afzender | `/messages/devicebound` | Cloud-naar-apparaat-berichten die bestemd zijn voor apparaten, worden door de service naar deze koppeling verzonden. Voor berichten die via deze koppeling `To` worden verzonden, wordt de eigenschap ingesteld op het pad van de ontvanger van het doel apparaat `/devices/<deviceID>/messages/devicebound` . |
| Service | Koppeling ontvanger | `/messages/serviceBound/feedback` | Het volt ooien, afkeuren en afhandelen van feedback berichten die afkomstig zijn van apparaten die op deze koppeling door de service zijn ontvangen. Zie [Cloud-naar-apparaat-berichten verzenden vanuit een IOT-hub](./iot-hub-devguide-messages-c2d.md#message-feedback)voor meer informatie over feedback berichten. |

Het volgende code fragment laat zien hoe u een Cloud-naar-apparaat-bericht maakt en het naar een apparaat verzendt met behulp van de [uAMQP-bibliotheek in python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Als u feedback wilt ontvangen, maakt de service-client een koppeling naar een ontvanger. Het volgende code fragment laat zien hoe u een koppeling maakt met behulp van de [uAMQP-bibliotheek in python](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Zoals u in de voor gaande code kunt zien, heeft een feedback bericht van Cloud naar apparaat het inhouds type *toepassing/vnd.microsoft.iothub.feedback.jsop*. U kunt de eigenschappen in de JSON-hoofd tekst van het bericht gebruiken om de leverings status van het oorspronkelijke bericht af te leiden:

* `statusCode`De sleutel in de feedback tekst heeft een van de volgende waarden: *geslaagd*, *verlopen*, *DeliveryCountExceeded*, *afgewezen* of *verwijderd*.

* De sleutel `deviceId` in de feedback tekst heeft de id van het doel apparaat.

* De sleutel `originalMessageId` in de feedback tekst is de id van het oorspronkelijke Cloud-naar-apparaat-bericht dat is verzonden door de service. U kunt deze leverings status gebruiken om feedback voor Cloud-naar-apparaat-berichten te correleren.

### <a name="receive-telemetry-messages-service-client"></a>Telemetrie-berichten ontvangen (Service-client)

Standaard slaat uw IoT-hub geopnamete telemetrie-berichten op in een ingebouwde Event Hub. Uw service-client kan het AMQP-protocol gebruiken om de opgeslagen gebeurtenissen te ontvangen.

Voor dit doel moet de service-client eerst verbinding maken met het eind punt van de IoT-hub en een omleidings adres ontvangen voor de ingebouwde Event hubs. De service-client gebruikt het gegeven adres om verbinding te maken met de ingebouwde Event Hub.

In elke stap moet de client de volgende informatie bevatten:

* Geldige service referenties (Service Shared Access Signature token).

* Een goed opgemaakt pad naar de groeps partitie van de Consumer waarvan het van plan is berichten op te halen. Voor een bepaalde Consumer groep en partitie-ID heeft het pad de volgende indeling: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (de standaard Consumer groep is `$Default` ).

* Een optioneel filter predicaat voor het aanwijzen van een begin punt in de partitie. Dit predicaat kan de vorm hebben van een volgorde nummer, een verschuiving of een time stamp.

In het volgende code fragment wordt de [uAMQP-bibliotheek in python](https://github.com/Azure/azure-uamqp-python) gebruikt om de voor gaande stappen te demonstreren:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Voor een bepaalde apparaat-ID gebruikt de IoT-hub een hash van de apparaat-ID om te bepalen met welke partitie de berichten moeten worden opgeslagen. In het voor gaande code fragment ziet u hoe gebeurtenissen worden ontvangen van een enkele dergelijke partitie. Houd er echter rekening mee dat een typische toepassing vaak gebeurtenissen moet ophalen die zijn opgeslagen in alle Event Hub partities.

## <a name="device-client"></a>Apparaatclient

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Verbinding maken en verifiëren met een IoT-hub (apparaatclient)

Als u verbinding wilt maken met een IoT-hub met behulp van AMQP, kan een apparaat gebruikmaken [van op claims gebaseerde beveiliging (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) of [Simple Authentication and Security Layer (SASL)-](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) verificatie.

De volgende informatie is vereist voor de apparaatclient:

| Informatie | Waarde |
|-------------|--------------|
| IoT hub-hostnaam | `<iot-hub-name>.azure-devices.net` |
| Toegangssleutel | Een primaire of secundaire sleutel die is gekoppeld aan het apparaat |
| Shared Access Signature | Een korte hand tekening voor gedeelde toegang in de volgende indeling: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` . Zie [toegang tot IOT hub beheren](./iot-hub-devguide-security.md#security-token-structure)voor het verkrijgen van de code voor het genereren van deze hand tekening.

In het volgende code fragment wordt de [uAMQP-bibliotheek in python](https://github.com/Azure/azure-uamqp-python) gebruikt om verbinding te maken met een IOT-hub via een afzender koppeling.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

De volgende koppelings paden worden ondersteund als apparaat-bewerkingen:

| Gemaakt door | Type koppeling | Pad van koppeling | Description |
|------------|-----------|-----------|-------------|
| Apparaten | Koppeling ontvanger | `/devices/<deviceID>/messages/devicebound` | Cloud-naar-apparaat-berichten die bestemd zijn voor apparaten, worden op deze koppeling door elk doel apparaat ontvangen. |
| Apparaten | Koppeling naar afzender | `/devices/<deviceID>/messages/events` | Apparaat-naar-Cloud-berichten die vanaf een apparaat worden verzonden, worden via deze koppeling verzonden. |
| Apparaten | Koppeling naar afzender | `/messages/serviceBound/feedback` | Cloud-naar-apparaat-bericht feedback dat via deze koppeling via apparaten naar de service wordt verzonden. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Cloud-naar-apparaat-opdrachten ontvangen (apparaatclient)

Cloud-naar-apparaat-opdrachten die worden verzonden naar apparaten, arriveren op een `/devices/<deviceID>/messages/devicebound` koppeling. Apparaten kunnen deze berichten in batches ontvangen en de bericht gegevens Payload, bericht eigenschappen, aantekeningen of toepassings eigenschappen in het bericht naar behoefte gebruiken.

Het volgende code fragment maakt gebruik [van de uAMQP-bibliotheek in python](https://github.com/Azure/azure-uamqp-python)) om Cloud-naar-apparaat-berichten te ontvangen door een apparaat.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Telemetrie-berichten verzenden (apparaatclient)

U kunt ook telemetrie-berichten vanaf een apparaat verzenden met behulp van AMQP. Het apparaat kan optioneel een woorden lijst met toepassings eigenschappen of verschillende bericht eigenschappen, zoals bericht-ID, opgeven.

In het volgende code fragment wordt de [uAMQP-bibliotheek in python](https://github.com/Azure/azure-uamqp-python) gebruikt voor het verzenden van apparaat-naar-Cloud-berichten van een apparaat.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* De AMQP-verbindingen kunnen worden verstoord vanwege een netwerk storing of het verlopen van het verificatie token (gegenereerd in de code). De service-client moet deze omstandigheden afhandelen en de verbinding en koppelingen, indien nodig, opnieuw tot stand brengen. Als een verificatie token verloopt, kan de client voor komen dat een verbinding wordt verbroken door proactief het token te vernieuwen voordat het verloopt.

* De client moet af en toe de koppelings omleidingen correct kunnen verwerken. Raadpleeg de documentatie van uw AMQP-client om een dergelijke bewerking te begrijpen.

## <a name="next-steps"></a>Volgende stappen

Zie de [AMQP v 1.0-specificatie](https://www.amqp.org/sites/amqp.org/files/amqp.pdf)voor meer informatie over het AMQP-protocol.

Zie voor meer informatie over IoT Hub-berichten:

* [Cloud-naar-apparaat-berichten](./iot-hub-devguide-messages-c2d.md)
* [Ondersteuning voor extra protocollen](iot-hub-protocol-gateway.md)
* [Ondersteuning voor het MQTT-Protocol (Message Queuing telemetrie Trans Port)](./iot-hub-mqtt-support.md)
