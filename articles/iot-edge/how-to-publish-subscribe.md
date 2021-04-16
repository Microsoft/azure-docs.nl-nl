---
title: Publiceren en abonneren met Azure IoT Edge | Microsoft Docs
description: Gebruik IoT Edge MQTT Broker om berichten te publiceren en te abonneren
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1c4760362e7c2b3965638b3213910b5b8cd6f079
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516175"
---
# <a name="publish-and-subscribe-with-azure-iot-edge-preview"></a>Publiceren en abonneren met Azure IoT Edge (preview)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

U kunt een Azure IoT Edge MQTT-broker gebruiken om berichten te publiceren en te abonneren. In dit artikel wordt beschreven hoe u verbinding maakt met deze broker, berichten publiceert en zich abonneert via door de gebruiker gedefinieerde onderwerpen, en hoe u IoT Hub berichten gebruikt. De IoT Edge MQTT-broker is ingebouwd in IoT Edge hub. Zie de [brokeringmogelijkheden van de](iot-edge-runtime.md)IoT Edge hub voor meer informatie.

> [!NOTE]
> IoT Edge MQTT-broker is momenteel beschikbaar als openbare preview.

## <a name="pre-requisites"></a>Vereisten

- Een Azure-account met een geldig abonnement
- [Azure CLI](/cli/azure/) met de `azure-iot` CLI-extensie geïnstalleerd. Zie de installatiestappen voor [de Azure IoT-extensie voor Azure CLI voor meer informatie.](/cli/azure/azure-cli-reference-for-iot)
- Een **IoT Hub** SKU F1, S1, S2 of S3.
- Een apparaat **IoT Edge versie 1.2 of hoger hebben.** Omdat IoT Edge MQTT-broker momenteel in openbare preview is, stelt u de volgende omgevingsvariabelen in op true op de edgeHub-container om de MQTT-broker in te stellen:

   | Name | Waarde |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Mosquitto-clients** die op het IoT Edge geïnstalleerd. In dit artikel wordt gebruikgemaakt van de populaire Mosquitto-clients [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) en [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). In plaats daarvan kunnen andere MQTT-clients worden gebruikt. Voer de volgende opdracht uit om de Mosquitto-clients te installeren op een Ubuntu-apparaat:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Installeer de Mosquitto-server niet omdat dit kan leiden tot blokkering van de MQTT-poorten (8883 en 1883) en conflict met de IoT Edge hub.

## <a name="connect-to-iot-edge-hub"></a>Verbinding maken met IoT Edge hub

Als u verbinding maakt met IoT Edge Hub, volgt u dezelfde stappen als beschreven in het artikel Verbinding maken met IoT Hub met een algemene [MQTT-client](../iot-hub/iot-hub-mqtt-support.md) of in de conceptuele beschrijving van het [artikel IoT Edge hub.](iot-edge-runtime.md) Deze stappen zijn:

1. Optioneel brengt de MQTT-client een beveiligde verbinding tot stand met de IoT Edge hub met behulp van Transport Layer Security (TLS) 
2. De MQTT-client *verifieert* zichzelf bij IoT Edge hub
3. De IoT Edge hub *machtigt de* MQTT-client volgens het autorisatiebeleid

### <a name="secure-connection-tls"></a>Beveiligde verbinding (TLS)

Transport Layer Security (TLS) wordt gebruikt om een versleutelde communicatie tot stand te brengen tussen de client en de IoT Edge hub.

Als u TLS wilt uitschakelen, gebruikt u poort 1883 (MQTT) en verbindt u de edgeHub-container met poort 1883.

Als een client op poort 8883 (MQTTS) verbinding maakt met de MQTT-broker, wordt een TLS-kanaal gestart om TLS in te kunnenschakelen. De broker verzendt de certificaatketen die de client moet valideren. Als u de certificaatketen wilt valideren, moet het basiscertificaat van de MQTT-broker worden geïnstalleerd als een vertrouwd certificaat op de client. Als het basiscertificaat niet wordt vertrouwd, wordt de clientbibliotheek geweigerd door de MQTT-broker met een verificatiefout voor het certificaat. De stappen die u moet volgen om dit basiscertificaat van de broker op de client te installeren, zijn hetzelfde als in het geval van de transparante [gateway](how-to-create-transparent-gateway.md) en worden beschreven in de documentatie een [downstreamapparaat](how-to-connect-downstream-device.md#prepare-a-downstream-device) voorbereiden.

### <a name="authentication"></a>Verificatie

Een MQTT-client moet eerst een CONNECT-pakket verzenden naar de MQTT-broker om een verbinding in zijn naam te initiëren. Dit pakket bevat drie soorten verificatiegegevens: een `client identifier` , een en een `username` `password` :

- Het `client identifier` veld is de naam van het apparaat of de modulenaam in IoT Hub. Hiervoor wordt de volgende syntaxis gebruikt:

  - Voor een apparaat: `<device_name>`

  - Voor een module: `<device_name>/<module_name>`

   Als u verbinding wilt maken met de MQTT-broker, moet een apparaat of module worden geregistreerd in IoT Hub.

   De broker staat geen verbindingen van meerdere clients met dezelfde referenties toe. De broker verbreekt de verbinding met de reeds verbonden client als een tweede client verbinding maakt met dezelfde referenties.

- Het veld is afgeleid van de naam van het apparaat of de module en de IoTHub-naam waar het apparaat bij hoort, met `username` behulp van de volgende syntaxis:

  - Voor een apparaat: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Voor een module: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- Het `password` veld van het CONNECT-pakket is afhankelijk van de verificatiemodus:

  - Wanneer u [verificatie met symmetrische sleutels gebruikt,](how-to-authenticate-downstream-device.md#symmetric-key-authentication) `password` is het veld een SAS-token.
  - Wanneer u [zelf-ondertekende X.509-verificatie gebruikt,](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) `password` is het veld niet aanwezig. In deze verificatiemodus is een TLS-kanaal vereist. De client moet verbinding maken met poort 8883 om een TLS-verbinding tot stand te brengen. Tijdens de TLS-handshake vraagt de MQTT-broker een clientcertificaat aan. Dit certificaat wordt gebruikt om de identiteit van de client te verifiëren en daarom is het veld later niet meer nodig wanneer `password` het CONNECT-pakket wordt verzonden. Het verzenden van zowel een clientcertificaat als het wachtwoordveld leidt tot een fout en de verbinding wordt gesloten. MQTT-bibliotheken en TLS-clientbibliotheken hebben meestal een manier om een clientcertificaat te verzenden bij het starten van een verbinding. U ziet een stapsgewijs voorbeeld in de sectie [X509-certificaat gebruiken voor clientverificatie.](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)

Modules die zijn geïmplementeerd door [](how-to-authenticate-downstream-device.md#symmetric-key-authentication) IoT Edge maken gebruik van verificatie met symmetrische sleutels en kunnen de API van de lokale [IoT Edge-werkbelasting](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) aanroepen om programmatisch een SAS-token op te halen, zelfs als het offline is.

### <a name="authorization"></a>Autorisatie

Zodra een MQTT-client is geverifieerd bij IoT Edge hub, moet deze worden gemachtigd om verbinding te maken. Zodra er verbinding is, moet deze gemachtigd zijn om specifieke onderwerpen te publiceren of zich te abonneren. Deze autorisaties worden verleend door de IoT Edge hub op basis van het autorisatiebeleid. Het autorisatiebeleid is een set instructies die wordt uitgedrukt als een JSON-structuur die via de dubbel naar de IoT Edge hub wordt verzonden. Bewerk een IoT Edge hub-dubbel om het autorisatiebeleid te configureren.

> [!NOTE]
> Voor de openbare preview ondersteunt alleen de Azure CLI implementaties met MQTT Broker-autorisatiebeleid. De Azure Portal biedt momenteel geen ondersteuning voor het bewerken van IoT Edge hub-dubbel en het autorisatiebeleid.

Elke autorisatiebeleidsverklaring bestaat uit de combinatie van - of `identities` `allow` `deny` `operations` -effecten, , en `resources` :

- `identities` het onderwerp van het beleid beschrijven. Het moet worden toe te wijs aan `client identifier` de die is verzonden door clients in hun CONNECT-pakket.
- `allow``deny`of-effecten bepalen of bewerkingen moeten worden toegestaan of weigeren.
- `operations` de acties definiëren die moeten worden geautoriseerd. `mqtt:connect`en `mqtt:publish` zijn momenteel de drie `mqtt:subscribe` ondersteunde acties.
- `resources` definieer het object van het beleid. Dit kan een onderwerp of een onderwerppatroon zijn dat is gedefinieerd met [MQTT-jokertekens](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107).

Hieronder ziet u een voorbeeld van een autorisatiebeleid waarbij de rogue_client-client expliciet geen verbinding mag maken, alle Azure IoT-clients verbinding kan maken en 'sensor_1' toestemming geeft om naar het onderwerp te `events/alerts` publiceren.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Een aantal zaken om rekening mee te houden bij het schrijven van uw autorisatiebeleid:

- Hiervoor is `$edgeHub` dubbelschemaversie 1.2 vereist
- Standaard worden alle bewerkingen geweigerd.
- Autorisatie-instructies worden geëvalueerd in de volgorde waarin ze worden weergegeven in de JSON-definitie. Eerst kijkt u naar en selecteert u vervolgens de eerste instructies voor toestaan of `identities` weigeren die overeenkomen met de aanvraag. In het geval van conflicten tussen instructies voor toestaan en weigeren, wint de deny-instructie.
- Er kunnen verschillende variabelen (bijvoorbeeld vervangingen) worden gebruikt in het autorisatiebeleid:

  - `{{iot:identity}}` vertegenwoordigt de identiteit van de momenteel verbonden client. Bijvoorbeeld een apparaat-id zoals `myDevice` of een module-id zoals `myEdgeDevice/SampleModule` .
  - `{{iot:device_id}}` vertegenwoordigt de identiteit van het apparaat dat momenteel is verbonden. Bijvoorbeeld een apparaat-id zoals of de `myDevice` apparaat-id waarin een module wordt uitgevoerd, zoals `myEdgeDevice` .
  - `{{iot:module_id}}` vertegenwoordigt de identiteit van de module die momenteel is verbonden. Deze variabele is leeg voor verbonden apparaten of een module-id zoals `SampleModule` .
  - `{{iot:this_device_id}}` vertegenwoordigt de identiteit van het IoT Edge apparaat met het autorisatiebeleid. Bijvoorbeeld `myIoTEdgeDevice`.

Autorisaties voor IoT Hub-onderwerpen worden iets anders verwerkt dan door de gebruiker gedefinieerde onderwerpen. Dit zijn de belangrijkste punten om te onthouden:

- Azure IoT-apparaten of -modules hebben een expliciete autorisatieregel nodig om verbinding te maken met IoT Edge hub MQTT-broker. Hieronder vindt u een standaard autorisatiebeleid voor verbinding.
- Azure IoT-apparaten of -modules hebben standaard toegang tot hun eigen IoT Hub-onderwerpen zonder expliciete autorisatieregel. Autorisaties zijn in dat geval echter het gevolg van bovenliggende/onderliggende relaties en deze relaties moeten worden ingesteld. IoT Edge modules worden automatisch ingesteld als kinderen van hun IoT Edge-apparaat, maar apparaten moeten expliciet worden ingesteld als kinderen van hun IoT Edge gateway.

Hier is een standaardautorisatiebeleid dat kan worden gebruikt om alle Azure IoT-apparaten of -modules in staat te stellen verbinding **te maken** met de broker:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Nu u weet hoe u verbinding maakt met de IoT Edge MQTT-broker, gaan we kijken hoe u deze kunt gebruiken om berichten eerst te publiceren en te abonneren op door de gebruiker gedefinieerde onderwerpen, vervolgens over IoT-hubonderwerpen en ten slotte naar een andere MQTT-broker.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Publiceren en abonneren op door de gebruiker gedefinieerde onderwerpen

In dit artikel gebruikt u één client met de naam sub_client die **zich** abonneert op een onderwerp en een andere client met de **naam pub_client** die naar een onderwerp publiceert. We gebruiken de [](how-to-authenticate-downstream-device.md#symmetric-key-authentication) symmetrische sleutelverificatie, maar hetzelfde kan worden gedaan met [X.509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) zelf-ondertekende verificatie of [X.509 CA-ondertekende verificatie.](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)

### <a name="create-publisher-and-subscriber-clients"></a>Uitgevers- en abonnee-clients maken

Maak twee IoT-apparaten in IoT Hub en haal hun wachtwoorden op. Gebruik de Azure CLI vanuit uw terminal om het volgende te doen:

1. Maak twee IoT-apparaten in IoT Hub, bovenliggende apparaten aan IoT Edge apparaat:

   ```azurecli-interactive
   az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   ```

2. Haal hun wachtwoorden op door een SAS-token te genereren:

   - Voor een apparaat:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
     ```

     waarbij 3600 de duur van het SAS-token in seconden is (bijvoorbeeld 3600 = 1 uur).

   - Voor een module:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
     ```

     waarbij 3600 de duur van het SAS-token in seconden is (bijvoorbeeld 3600 = 1 uur).

3. Kopieer het SAS-token. Dit is de waarde die overeenkomt met de SAS-sleutel uit de uitvoer. Hier is een voorbeeld van de uitvoer van de Bovenstaande Azure CLI-opdracht:

   ```output
   {
      "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
   }
   ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Uitgevers- en abonnee-clients machtigen

Als u de uitgever en abonnee wilt machtigen, bewerkt u IoT Edge hub-dubbel in een IoT Edge implementatie die het volgende autorisatiebeleid bevat.

>[!NOTE]
>Momenteel kunnen implementaties die de MQTT-autorisatie-eigenschappen bevatten, alleen worden toegepast op IoT Edge apparaten met behulp van de Azure CLI.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Verificatie met symmetrische sleutels zonder TLS

#### <a name="subscribe"></a>Abonneren

Verbind uw **sub_client** MQTT-client met de MQTT-broker en abonneer u op de door de volgende opdracht uit te voeren op `test_topic` IoT Edge apparaat:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

waar `<edge_device_address>`  =  `localhost` in dit voorbeeld omdat de client wordt uitgevoerd op hetzelfde apparaat als IoT Edge.

In dit eerste voorbeeld wordt poort 1883 (MQTT) zonder TLS gebruikt. Een ander voorbeeld met poort 8883 (MQTTS), met TLS ingeschakeld, wordt weergegeven in de volgende sectie.

De **sub_client** MQTT-client is nu gestart en wacht op binnenkomende berichten op `test_topic` .

#### <a name="publish"></a>Publiceren

Verbind uw **pub_client** MQTT-client met de MQTT-broker en publiceert een bericht op dezelfde manier als hierboven door de volgende opdracht op uw IoT Edge-apparaat uit te voeren vanuit een andere `test_topic` terminal:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

waar `<edge_device_address>`  =  `localhost` in dit voorbeeld omdat de client wordt uitgevoerd op hetzelfde apparaat als IoT Edge.

Als de opdracht wordt uitgevoerd, **ontvangt sub_client** MQTT-client het bericht 'hello'.

### <a name="symmetric-keys-authentication-with-tls"></a>Verificatie met symmetrische sleutels met TLS

Als u TLS wilt inschakelen, moet de poort worden gewijzigd van 1883 (MQTT) in 8883 (MQTTS) en moeten clients het basiscertificaat van de MQTT-broker hebben om de certificaatketen te kunnen valideren die is verzonden door de MQTT-broker. U kunt dit doen door de stappen in de sectie [Beveiligde verbinding (TLS) te volgen.](#secure-connection-tls)

Omdat de clients worden uitgevoerd op hetzelfde apparaat als de MQTT-broker in het bovenstaande voorbeeld, zijn dezelfde stappen van toepassing op het inschakelen van TLS door alleen het poortnummer te wijzigen van 1883 (MQTT) in 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Publiceren en abonneren op IoT Hub onderwerpen

Met [de Apparaat-SDK's van Azure IoT](https://github.com/Azure/azure-iot-sdks) kunnen clients al IoT Hub-bewerkingen uitvoeren, maar publiceren/abonneren op door de gebruiker gedefinieerde onderwerpen is niet toegestaan. IoT Hub bewerkingen kunnen worden uitgevoerd met behulp van MQTT-clients met behulp van semantiek voor publiceren en abonneren, zolang de protocollen van de IoT-hub primitief in acht worden genomen. We gaan de specifieke informatie door om te begrijpen hoe deze protocollen werken.

### <a name="send-telemetry-data-to-iot-hub"></a>Stuur telemetriegegevens naar IoT Hub

Het verzenden van telemetriegegevens naar IoT Hub is vergelijkbaar met publiceren op een door de gebruiker gedefinieerd onderwerp, maar met behulp van een specifiek IoT Hub onderwerp:

- Voor een apparaat wordt telemetrie verzonden over het onderwerp: `devices/<device_name>/messages/events/`
- Voor een module wordt telemetrie verzonden over het volgende onderwerp: `devices/<device_name>/<module_name>/messages/events/`

Maak daarnaast een route zoals voor het verzenden van telemetrie van de `FROM /messages/* INTO $upstream` IoT Edge MQTT-broker naar de IoT-hub. Zie Routes declareer voor meer informatie [over routering.](module-composition.md#declare-routes)

### <a name="get-twin"></a>Tweelingen krijgen

Het verkrijgen van de apparaat-/module-dubbel is geen typisch MQTT-patroon. De client moet een aanvraag indienen voor de dubbel die IoT Hub gaat verwerken.

Om tweelingen te ontvangen, moet de client zich abonneren op een IoT Hub specifiek onderwerp `$iothub/twin/res/#` . Deze onderwerpnaam wordt overgenomen van IoT Hub en alle clients moeten zich abonneren op hetzelfde onderwerp. Dit betekent niet dat apparaten of modules de dubbel van elkaar ontvangen. IoT Hub en IoT Edge hub weet waar de tweeling moet worden geleverd, zelfs als alle apparaten naar dezelfde onderwerpnaam luisteren.

Zodra het abonnement is gemaakt, moet de client om de tweeling vragen door een bericht te publiceren naar een IoT Hub specifiek onderwerp, waarbij een `$iothub/twin/GET/?rid=<request_id>/#`  `<request_id>` willekeurige id is. De IoT-hub verzendt vervolgens het antwoord met de aangevraagde gegevens over het onderwerp `$iothub/twin/res/200/?rid=<request_id>` waarop de client zich abonneert. Op deze manier kan een client de aanvragen koppelen aan de antwoorden.

### <a name="receive-twin-patches"></a>Dubbele patches ontvangen

Om dubbele patches te ontvangen, moet een client zich abonneren op speciaal IoTHub-onderwerp `$iothub/twin/PATCH/properties/desired/#` . Zodra het abonnement is gemaakt, ontvangt de client de dubbele patches die zijn verzonden door IoT Hub in dit onderwerp.

### <a name="receive-direct-methods"></a>Directe methoden ontvangen

Het ontvangen van een directe methode is vergelijkbaar met het ontvangen van volledige tweelingen met de toevoeging die de client moet bevestigen dat deze de aanroep heeft ontvangen. Eerst abonneert de client zich op een speciaal onderwerp voor IoT `$iothub/methods/POST/#` Hub. Zodra een directe methode is ontvangen in dit onderwerp, moet de client de aanvraag-id extraheren uit het subonderwerp waarop de directe methode wordt ontvangen en ten slotte een bevestigingsbericht publiceren op een speciaal onderwerp voor `rid` IoT `$iothub/methods/res/200/<request_id>` Hub.

### <a name="send-direct-methods"></a>Directe methoden verzenden

Het verzenden van een directe methode is een HTTP-aanroep en gaat dus niet via de MQTT-broker. Zie Directe methoden begrijpen en aanroepen voor het verzenden van een directe methode naar de [IoT-hub.](../iot-hub/iot-hub-devguide-direct-methods.md) Als u een directe methode lokaal naar een andere module wilt verzenden, bekijkt u dit voorbeeld van het aanroepen van directe methode van [de Azure IoT C# SDK.](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597)

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Publiceren en abonneren tussen MQTT-brokers

Om twee MQTT-brokers te verbinden, bevat IoT Edge hub een MQTT-brug. Een MQTT-brug wordt vaak gebruikt om een MQTT-broker die wordt uitgevoerd, te verbinden met een andere MQTT-broker. Slechts een subset van het lokale verkeer wordt meestal naar een andere broker pusht.

> [!NOTE]
> De IoT Edge hub bridge kan momenteel alleen worden gebruikt tussen geneste IoT Edge apparaten. Het kan niet worden gebruikt om gegevens naar IoT Hub te verzenden, omdat IoT Hub geen volledige MQTT-broker is. Zie Communiceren met uw IoT-hub met behulp van het MQTT-protocol voor meer informatie over de ondersteuning van [IoT Hub MQTT-brokerfuncties.](../iot-hub/iot-hub-mqtt-support.md) Zie Connect a downstream IoT Edge device to an IoT Edge Azure IoT Edge gateway (Een downstreamapparaat IoT Edge [verbinden met een Azure IoT Edge-gateway) voor meer](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)informatie over het nesten Azure IoT Edge apparaten.

In een geneste configuratie fungeert de MQTT-brug van de IoT Edge-hub als een client van de bovenliggende MQTT-broker. Daarom moeten autorisatieregels worden ingesteld op de bovenliggende EdgeHub, zodat de onderliggende EdgeHub specifieke door de gebruiker gedefinieerde onderwerpen kan publiceren en zich erop kan abonneren waar de brug voor is geconfigureerd.

De IoT Edge MQTT-brug wordt geconfigureerd via een JSON-structuur die via de dubbel naar de IoT Edge hub wordt verzonden. Bewerk een IoT Edge hub-dubbel om de MQTT-brug te configureren.

> [!NOTE]
> Voor de openbare preview ondersteunt alleen de Azure CLI implementaties met MQTT Bridge-configuraties. De Azure Portal biedt momenteel geen ondersteuning voor het bewerken van IoT Edge hub-dubbel en de configuratie van de MQTT-brug.

De MQTT-brug kan worden geconfigureerd om een IoT Edge hub MQTT-broker te verbinden met meerdere externe brokers. Voor elke externe broker zijn de volgende instellingen vereist:

- `endpoint` is het adres van de externe MQTT-broker om verbinding mee te maken. Alleen bovenliggende IoT Edge worden momenteel ondersteund en worden gedefinieerd door de variabele `$upstream` .
- `settings` definieert welke onderwerpen moeten worden overbrugd voor een eindpunt. Er kunnen meerdere instellingen per eindpunt zijn en de volgende waarden worden gebruikt om het te configureren:
  - `direction`: om u te abonneren op de onderwerpen van de externe broker of om te publiceren naar de onderwerpen `in` `out` van de externe broker
  - `topic`: basisonderwerppatroon dat moet worden gematcht. [MQTT-jokertekens](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) kunnen worden gebruikt om dit patroon te definiëren. Verschillende voorvoegsels kunnen worden toegepast op dit onderwerppatroon op de lokale broker en externe broker.
  - `outPrefix`: Voorvoegsel dat wordt toegepast op het `topic` patroon op de externe broker.
  - `inPrefix`: Voorvoegsel dat wordt toegepast op het `topic` patroon op de lokale broker.

Hieronder vindt u een voorbeeld van een IoT Edge MQTT Bridge-configuratie die alle berichten die zijn ontvangen over onderwerpen van een bovenliggend IoT Edge-apparaat opnieuw op een onderliggend IoT Edge-apparaat in dezelfde onderwerpen, en alle berichten die worden verzonden over onderwerpen van een onderliggend IoT Edge-apparaat opnieuw naar een bovenliggend IoT Edge-apparaat publiceren over `alerts/#` `/local/telemetry/#` `/remote/messages/#` onderwerpen.

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "#",
                    "inPrefix": "/local/telemetry/",
                    "outPrefix": "/remote/messages/"
                }
            ]
        }]
    }
}
```
Andere opmerkingen over de IoT Edge hub MQTT Bridge:
- Het MQTT-protocol wordt automatisch gebruikt als upstream-protocol wanneer de MQTT-broker wordt gebruikt en dat IoT Edge wordt gebruikt in een geneste configuratie, bijvoorbeeld met een `parent_hostname` opgegeven. Zie Cloudcommunicatie voor meer informatie over [upstream-protocollen.](iot-edge-runtime.md#cloud-communication) Zie Connect a downstream IoT Edge device to an Azure IoT Edge gateway (Een downstreamapparaat verbinden met een [Azure IoT Edge-gateway) voor meer informatie over geneste configuraties.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

## <a name="next-steps"></a>Volgende stappen

[Inzicht in IoT Edge hub](iot-edge-runtime.md#iot-edge-hub)
