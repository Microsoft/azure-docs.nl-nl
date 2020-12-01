---
title: Publiceren en abonneren met Azure IoT Edge | Microsoft Docs
description: IoT Edge MQTT Broker gebruiken voor het publiceren en abonneren van berichten
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: ecb034ae621c935c3ebcd5b480e116c2cb1d864f
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435532"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Publiceren en abonneren met Azure IoT Edge

U kunt Azure IoT Edge MQTT Broker gebruiken voor het publiceren en abonneren van berichten. In dit artikel leest u hoe u verbinding kunt maken met deze Broker, hoe u berichten publiceert en hierop abonneert via door de gebruiker gedefinieerde onderwerpen en hoe u IoT Hub bericht primitieven kunt gebruiken. De IoT Edge MQTT Broker is ingebouwd in de IoT Edge hub. Zie de functies voor het [brokerpen van de IOT Edge hub](iot-edge-runtime.md)voor meer informatie.

> [!NOTE]
> IoT Edge MQTT Broker bevindt zich momenteel in de open bare preview.

## <a name="pre-requisites"></a>Vereisten

- Een Azure-account met een geldig abonnement
- [Azure cli](/cli/azure/) met de `azure-iot` cli-extensie geïnstalleerd. Zie [de installatie stappen voor Azure IOT-extensie voor Azure Azure cli](/cli/azure/azure-cli-reference-for-iot)voor meer informatie.
- Een **IOT hub** van de SKU F1, S1, S2 of S3.
- Een **IOT edge apparaat hebben met versie 1,2 of hoger**. Omdat IoT Edge MQTT Broker momenteel beschikbaar is in de open bare preview, stelt u de volgende omgevings variabelen in op True in de edgeHub-container om de MQTT Broker in te scha kelen:

   | Name | Waarde |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Mosquitto-clients** geïnstalleerd op het IOT edge-apparaat. In dit artikel wordt gebruikgemaakt van de populaire Mosquitto-clients [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) en [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). In plaats daarvan kunnen andere MQTT-clients worden gebruikt. Als u de Mosquitto-clients op een Ubuntu-apparaat wilt installeren, voert u de volgende opdracht uit:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Installeer de Mosquitto-server niet omdat deze mogelijk de MQTT-poorten (8883 en 1883) blokkeert en een conflict veroorzaakt met de IoT Edge hub.

## <a name="connect-to-iot-edge-hub"></a>Verbinding maken met IoT Edge hub

Als u verbinding maakt met IoT Edge hub, worden dezelfde stappen gevolgd zoals beschreven in de [verbinding maken met IOT hub met een Gene RIEK MQTT-client artikel](../iot-hub/iot-hub-mqtt-support.md) of in de [conceptuele beschrijving van het artikel IOT Edge hub](iot-edge-runtime.md). Deze stappen zijn:

1. De MQTT-client brengt eventueel een *beveiligde verbinding* tot stand met de IOT Edge hub met behulp van Transport Layer Security (TLS)
2. De MQTT-client *verifieert* zichzelf bij IOT Edge hub
3. De IoT Edge hub *autoriseert* de MQTT-client per autorisatie beleid

### <a name="secure-connection-tls"></a>Beveiligde verbinding (TLS)

Transport Layer Security (TLS) wordt gebruikt om een versleutelde communicatie tussen de client en de IoT Edge hub tot stand te brengen.

Als u TLS wilt uitschakelen, gebruikt u poort 1883 (MQTT) en koppelt u de edgeHub-container aan poort 1883.

Als u TLS wilt inschakelen, wordt een TLS-kanaal gestart als een client verbinding maakt op poort 8883 (MQTTS) met de MQTT Broker. De Broker verzendt de certificaat keten die de client moet valideren. Als u de certificaat keten wilt valideren, moet het basis certificaat van de MQTT-Broker als vertrouwd certificaat op de client worden geïnstalleerd. Als het basis certificaat niet wordt vertrouwd, wordt de client bibliotheek geweigerd door de MQTT-Broker met een certificaat verificatie fout. De stappen voor het installeren van dit basis certificaat van de Broker op de client zijn hetzelfde als in de [transparante gateway](how-to-create-transparent-gateway.md) en worden beschreven in de documentatie [een downstream-apparaat voorbereiden](how-to-connect-downstream-device.md#prepare-a-downstream-device) .

### <a name="authentication"></a>Verificatie

Een MQTT-client kan zichzelf alleen verifiëren als eerst een CONNECT-pakket naar de MQTT Broker moet worden verzonden om een verbinding in de naam te initiëren. Dit pakket biedt drie soorten verificatie-informatie: a `client identifier` , a `username` en een `password` :

- Het `client identifier` veld bevat de naam van het apparaat of de module naam in IOT hub. Er wordt gebruikgemaakt van de volgende syntaxis:

  - Voor een apparaat: `<device_name>`

  - Voor een module: `<device_name>/<module_name>`

   Als u verbinding wilt maken met de MQTT Broker, moet een apparaat of module zijn geregistreerd in IoT Hub.

   De Broker staat geen verbindingen toe van meerdere clients die dezelfde referenties gebruiken. De Broker verbreekt de verbinding van de reeds verbonden client als een tweede client verbinding maakt met behulp van dezelfde referenties.

- Het `username` veld is afgeleid van het apparaat of de module naam en de naam van het IoTHub-apparaat maakt gebruik van de volgende syntaxis:

  - Voor een apparaat: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Voor een module: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- Het `password` veld van het verbindings pakket is afhankelijk van de verificatie modus:

  - Wanneer u gebruikmaakt van [symmetrische sleutel verificatie](how-to-authenticate-downstream-device.md#symmetric-key-authentication), `password` is het veld een SAS-token.
  - Wanneer u [X. 509 zelfondertekende authenticatie](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)gebruikt, `password` is het veld niet aanwezig. In deze verificatie modus is een TLS-kanaal vereist. De client moet verbinding maken met poort 8883 om een TLS-verbinding tot stand te brengen. Tijdens de TLS-Handshake vraagt de MQTT Broker een client certificaat aan. Dit certificaat wordt gebruikt om de identiteit van de client te controleren en daarom `password` is het veld niet meer nodig wanneer het Connect-pakket wordt verzonden. Als u een client certificaat en het wachtwoord veld verzendt, leidt dit tot een fout en wordt de verbinding gesloten. MQTT-bibliotheken en TLS-client bibliotheken hebben doorgaans een manier om een client certificaat te verzenden wanneer een verbinding wordt gestart. U ziet een voor beeld van een stapsgewijze stap in de sectie [met het x509-certificaat voor client verificatie](how-to-authenticate-downstream-device.md#x509-self-signed-authentication).

Modules die worden geïmplementeerd door IoT Edge [symmetrische sleutel verificatie](how-to-authenticate-downstream-device.md#symmetric-key-authentication) gebruiken en kunnen de lokale [IOT Edge workload API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) aanroepen om programmatisch een SAS-token te verkrijgen, zelfs als u offline bent.

### <a name="authorization"></a>Autorisatie

Zodra een MQTT-client is geverifieerd voor IoT Edge hub, moet deze worden gemachtigd om verbinding te maken. Nadat de verbinding tot stand is gebracht, moet deze worden gemachtigd voor publiceren of abonneren op specifieke onderwerpen. Deze autorisaties worden verleend door de IoT Edge hub op basis van het autorisatie beleid. Het autorisatie beleid is een set instructies die wordt weer gegeven als een JSON-structuur die via het dubbele wordt verzonden naar de IoT Edge hub. Bewerk een IoT Edge hub tussen het configureren van het autorisatie beleid.

> [!NOTE]
> Voor de open bare preview is het bewerken van autorisatie beleid van de MQTT-Broker alleen beschikbaar via Visual Studio, Visual Studio code of de Azure CLI. De Azure Portal biedt momenteel geen ondersteuning voor het bewerken van de IoT Edge hub dubbele en het bijbehorende autorisatie beleid.

Elke autorisatie beleids instructie bestaat uit de combi natie van `identities` , `allow` of `deny` effecten, `operations` en `resources` :

- `identities` Beschrijf het onderwerp van het beleid. Het moet worden toegewezen aan de `client identifier` verzonden door-clients in hun Connect-pakket.
- `allow` of `deny` effecten bepalen of bewerkingen moeten worden toegestaan of geweigerd.
- `operations` Definieer de acties die moeten worden geautoriseerd. `mqtt:connect``mqtt:publish`en `mqtt:subscribe` zijn vandaag de drie ondersteunde acties.
- `resources` Definieer het object van het beleid. Dit kan een onderwerp of een onderwerp patroon zijn dat is gedefinieerd met [MQTT-joker tekens](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107).

Hieronder ziet u een voor beeld van een autorisatie beleid waarmee ' rogue_client '-client expliciet geen verbinding kan maken, kunnen alle Azure IoT-clients verbinding maken en toestaan dat ' sensor_1 ' wordt gepubliceerd op onderwerp `events/alerts` .

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

Een aantal dingen die u moet onthouden bij het schrijven van uw autorisatie beleid:

- Het vereist `$edgeHub` dubbele schema versie 1,2
- Standaard worden alle bewerkingen geweigerd.
- Autorisatie-instructies worden geëvalueerd in de volg orde waarin ze worden weer gegeven in de JSON-definitie. Het begint met het zoeken naar `identities` en vervolgens de eerste instructies voor toestaan of weigeren selecteren die overeenkomen met de aanvraag. In het geval van conflicten tussen de instructies Allow en deny, wint de instructie deny.
- Verschillende variabelen (bijvoorbeeld substituties) kunnen worden gebruikt in het autorisatie beleid:
    - `{{iot:identity}}` Hiermee wordt de identiteit van de momenteel verbonden client aangeduid. Bijvoorbeeld een apparaat-id zoals `myDevice` een module-ID, zoals `myEdgeDevice/SampleModule` .
    - `{{iot:device_id}}` Hiermee wordt de identiteit van het momenteel verbonden apparaat aangeduid. Bijvoorbeeld een apparaat-id zoals `myDevice` of de apparaat-id waar een module op lijkt `myEdgeDevice` .
    - `{{iot:module_id}}` Hiermee wordt de identiteit van de momenteel verbonden module aangeduid. Deze variabele is leeg voor verbonden apparaten of een module-identiteit zoals `SampleModule` .
    - `{{iot:this_device_id}}` Hiermee wordt de identiteit aangeduid van het IoT Edge apparaat dat het autorisatie beleid uitvoert. Bijvoorbeeld `myIoTEdgeDevice`.

Autorisaties voor IoT hub-onderwerpen worden enigszins anders behandeld dan door de gebruiker gedefinieerde onderwerpen. Dit zijn de belangrijkste punten die u moet onthouden:

- Azure IoT-apparaten of-modules hebben een expliciete autorisatie regel nodig om verbinding te maken met IoT Edge hub MQTT Broker. Hieronder vindt u een standaard beleid voor verbindings autorisatie.
- Azure IoT-apparaten of-modules hebben standaard toegang tot hun eigen IoT hub-onderwerpen zonder enige expliciete autorisatie regel. Autorisaties zijn echter afkomstig van bovenliggende/onderliggende relaties in dat geval en deze relaties moeten worden ingesteld. IoT Edge modules worden automatisch ingesteld als onderliggende items van hun IoT Edge apparaat, maar apparaten moeten expliciet worden ingesteld als onderliggende items van hun IoT Edge gateway.
- Azure IoT-apparaten of-modules hebben toegang tot de onderwerpen, waaronder IoT hub-onderwerpen, van andere apparaten of modules die voorzien in de juiste expliciete autorisatie regels worden gedefinieerd.

Hier volgt een standaard autorisatie beleid dat kan worden gebruikt om alle Azure IoT-apparaten of-modules in te scha kelen om **verbinding te maken** met de Broker:

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

Nu u begrijpt hoe u verbinding kunt maken met de IoT Edge MQTT Broker, gaan we kijken hoe deze kunnen worden gebruikt voor het publiceren en abonneren van berichten eerst op door de gebruiker gedefinieerde onderwerpen, en vervolgens op IoT hub-onderwerpen en tot slot naar een andere MQTT-Broker.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Publiceer en Abonneer u op door de gebruiker gedefinieerde onderwerpen

In dit artikel gebruikt u één client met de naam **sub_client** die zich abonneert op een onderwerp en een andere client met de naam **pub_client** die naar een onderwerp publiceert. We gebruiken de [symmetrische sleutel verificatie](how-to-authenticate-downstream-device.md#symmetric-key-authentication) , maar u kunt dit ook doen met [x. 509 zelfondertekende verificatie](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) of [x. 509 CA-ondertekende verificatie](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).

### <a name="create-publisher-and-subscriber-clients"></a>Publisher-en Subscriber-clients maken

Maak twee IoT-apparaten in IoT Hub en ontvang hun wacht woorden. Gebruik de Azure CLI van uw terminal om:

1. Twee IoT-apparaten maken in IoT Hub, bovenliggende items op uw IoT Edge apparaat:

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. Hun wacht woord ophalen door een SAS-token te genereren:

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

3. Kopieer de SAS-token. Dit is de waarde die overeenkomt met de SAS-sleutel van de uitvoer. Hier volgt een voor beeld van de uitvoer van de Azure CLI-opdracht:

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Publisher-en Subscriber-clients autoriseren

Als u de uitgever en de abonnee wilt autoriseren, bewerkt u de IoT Edge hub, hetzij via Azure CLI, Visual Studio of Visual Studio code, om het volgende autorisatie beleid toe te voegen:

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
                     "sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               },
               {
                  "identities": [
                     "pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Verificatie van symmetrische sleutels zonder TLS

#### <a name="subscribe"></a>Abonneren

Verbind uw **sub_client** MQTT-client met de MQTT Broker en Abonneer u op de `test_topic` door de volgende opdracht uit te voeren op uw IOT edge-apparaat:

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

`<edge_device_address>`  =  `localhost` in dit voor beeld omdat de client wordt uitgevoerd op hetzelfde apparaat als IOT Edge.

Houd er rekening mee dat poort 1883 (MQTT), zonder TLS, in dit eerste voor beeld wordt gebruikt. Een ander voor beeld met poort 8883 (MQTTS), met TLS ingeschakeld, wordt in de volgende sectie weer gegeven.

De **sub_client** MQTT-client is nu gestart en wacht op inkomende berichten op `test_topic` .

#### <a name="publish"></a>Publiceren

Verbind uw **pub_client** MQTT-client met de MQTT Broker en publiceert een bericht op hetzelfde `test_topic` als hierboven door de volgende opdracht uit te voeren op uw IOT edge-apparaat vanaf een andere terminal:

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

`<edge_device_address>`  =  `localhost` in dit voor beeld omdat de client wordt uitgevoerd op hetzelfde apparaat als IOT Edge.

Als de opdracht wordt uitgevoerd, ontvangt de **sub_client** MQTT-client het bericht Hello.

### <a name="symmetric-keys-authentication-with-tls"></a>Symmetrische sleutels authenticatie met TLS

Om TLS in te scha kelen, moet de poort worden gewijzigd van 1883 (MQTT) naar 8883 (MQTTS) en moeten clients beschikken over het basis certificaat van de MQTT Broker om de certificaat keten te kunnen valideren die door de MQTT Broker is verzonden. U kunt dit doen door de stappen te volgen die worden beschreven in de sectie [beveiligde verbinding (TLS)](#secure-connection-tls).

Omdat de clients worden uitgevoerd op hetzelfde apparaat als de MQTT-Broker in bovenstaand voor beeld, gelden dezelfde stappen voor het inschakelen van TLS door alleen het poort nummer van 1883 (MQTT) te wijzigen in 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Publiceren en abonneren op IoT Hub onderwerpen

Met de Sdk's van het [Azure IOT-apparaat](https://github.com/Azure/azure-iot-sdks) kunnen clients al IOT hub bewerkingen uitvoeren, maar kunnen ze niet publiceren/abonneren op door de gebruiker gedefinieerde onderwerpen. IoT Hub bewerkingen kunnen worden uitgevoerd met behulp van MQTT-clients die gebruikmaken van de semantiek van publiceren en abonneren, zolang de protocollen van de IoT hub-primitieven worden geëerbiedigd. We gaan de specifieke kenmerken door nemen om inzicht te krijgen in de werking van deze protocollen.

### <a name="send-telemetry-data-to-iot-hub"></a>Stuur telemetriegegevens naar IoT Hub

Het verzenden van telemetriegegevens naar IoT Hub is vergelijkbaar met publiceren op een door de gebruiker gedefinieerd onderwerp, maar met behulp van een specifiek IoT Hub onderwerp:

- Voor een apparaat wordt telemetrie verzonden in het onderwerp: `devices/<device_name>/messages/events`
- Voor een module wordt telemetrie verzonden in het onderwerp: `devices/<device_name>/<module_name>/messages/events`

Daarnaast kunt u een route maken, zoals `FROM /messages/* INTO $upstream` om telemetrie te verzenden van de IOT Edge MQTT-Broker naar IOT hub. Zie voor meer informatie over route ring [Declareer routes](module-composition.md#declare-routes).

### <a name="get-twin"></a>Ophalen

Het ophalen van het apparaat/de module dubbele is geen typisch MQTT patroon. De client moet een aanvraag verzenden voor de dubbele IoT Hub worden gebruikt.

De client moet zich abonneren op een IoT Hub specifiek onderwerp om apparaatdubbels te kunnen ontvangen `$iothub/twin/res/#` . De naam van dit onderwerp wordt overgenomen van IoT Hub en alle clients moeten zich abonneren op hetzelfde onderwerp. Dit betekent niet dat apparaten of modules het dubbele van elkaar ontvangen. IoT Hub en IoT Edge hub weet welke twee moeten worden geleverd, zelfs als alle apparaten naar dezelfde onderwerps naam Luis teren. 

Zodra het abonnement is gemaakt, moet de client het twee vragen om een bericht te publiceren naar een IoT Hub specifiek onderwerp `$iothub/twin/GET/?rid=<request_id>/#` waar  `<request_id>` een wille keurige id is. IoT hub verzendt vervolgens het antwoord met de aangevraagde gegevens over het onderwerp `$iothub/twin/res/200/?rid=<request_id>` , waarop de-client zich abonneert. Zo kan een client zijn of haar aanvragen koppelen aan de antwoorden.

### <a name="receive-twin-patches"></a>Dubbele patches ontvangen

Een client moet zich abonneren op een speciaal IoTHub-onderwerp om dubbele patches te ontvangen `$iothub/twin/PATCH/properties/desired/#` . Zodra het abonnement is gemaakt, ontvangt de client de dubbele patches die zijn verzonden door IoT Hub in dit onderwerp. 

### <a name="receive-direct-methods"></a>Directe methoden ontvangen

Het ontvangen van een directe methode is vergelijkbaar met het ontvangen van volledige apparaatdubbels, met de toevoeging dat de client moet bevestigen dat deze de aanroep heeft ontvangen. Eerst wordt de client geabonneerd op een speciaal onderwerp voor IoT hub `$iothub/methods/POST/#` . Zodra een directe methode op dit onderwerp wordt ontvangen, moet de client de aanvraag-id extra heren `rid` uit het subonderwerp waarop de directe methode wordt ontvangen en uiteindelijk een bevestigings bericht in een speciaal onderwerp van de IOT-hub publiceren `$iothub/methods/res/200/<request_id>` .

### <a name="send-direct-methods"></a>Directe methoden verzenden

Het verzenden van een directe methode is een HTTP-aanroep en gaat dus niet door naar de MQTT-Broker. Zie voor meer informatie over het verzenden van een directe methode naar IoT hub [begrijpen en directe methoden aanroepen](../iot-hub/iot-hub-devguide-direct-methods.md). Als u een directe methode lokaal naar een andere module wilt verzenden, raadpleegt u het [voor beeld voor het intrekken van Azure IOT C# SDK direct-methode](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Publiceren en abonneren tussen MQTT-Brokers

De IoT Edge hub bevat een MQTT-brug om twee MQTT-brokers met elkaar te verbinden. Een MQTT-brug wordt vaak gebruikt om een MQTT-Broker te verbinden die wordt uitgevoerd op een andere MQTT Broker. Er wordt doorgaans alleen een subset van het lokale verkeer naar een andere Broker gepusht.

> [!NOTE]
> De IoT Edge hub-brug kan momenteel alleen worden gebruikt tussen geneste IoT Edge-apparaten. Het kan niet worden gebruikt voor het verzenden van gegevens naar IoT hub omdat IoT hub geen volledig functionele MQTT-Broker is. Zie [communiceren met uw IOT-hub met behulp van het MQTT-protocol](../iot-hub/iot-hub-mqtt-support.md)voor meer informatie over de ondersteuning van meer IOT hub MQTT Broker-functies. Zie [een downstream IOT edge-apparaat verbinden met een Azure IOT Edge gateway](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) voor meer informatie over het nesten van IOT edge apparaten 

In een geneste configuratie fungeert de IoT Edge hub-MQTT-brug als een client van de bovenliggende MQTT-Broker. Daarom moeten autorisatie regels worden ingesteld op de bovenliggende EdgeHub zodat de onderliggende EdgeHub kan publiceren en abonneren op specifieke door de gebruiker gedefinieerde onderwerpen waarvoor de brug is geconfigureerd.

De MQTT-brug van IoT Edge wordt geconfigureerd via een JSON-structuur die via de dubbele naar de IoT Edge hub wordt verzonden. Bewerk een IoT Edge hub tussen de configuratie van de MQTT-brug.

> [!NOTE]
> Voor de open bare preview is de configuratie van de MQTT-brug alleen beschikbaar via Visual Studio, Visual Studio code of Azure CLI. De Azure Portal biedt momenteel geen ondersteuning voor het bewerken van de IoT Edge hub dubbele en de MQTT-brug configuratie.

De MQTT-brug kan worden geconfigureerd om een IoT Edge hub MQTT Broker te verbinden met meerdere externe brokers. Voor elke externe Broker zijn de volgende instellingen vereist:

- `endpoint` is het adres van de externe MQTT-Broker waarmee verbinding moet worden gemaakt. Alleen bovenliggende IoT Edge apparaten worden op dit moment ondersteund en worden gedefinieerd door de variabele `$upstream` .
- `settings` Hiermee definieert u welke onderwerpen moeten worden overbrugd voor een eind punt. Er kunnen meerdere instellingen per eind punt zijn en de volgende waarden worden gebruikt om deze te configureren:
    - `direction`: `in` Als u zich wilt abonneren op de onderwerpen van de externe Broker of `out` Als u wilt publiceren naar de onderwerpen van de externe Broker
    - `topic`: het patroon van het basis onderwerp moet worden gevonden. [MQTT-joker tekens](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) kunnen worden gebruikt voor het definiëren van dit patroon. Verschillende voor voegsels kunnen worden toegepast op dit onderwerps patroon op de lokale Broker en op de externe Broker.
    - `outPrefix`: Het voor voegsel dat wordt toegepast op het `topic` patroon in de externe Broker.
    - `inPrefix`: Het voor voegsel dat wordt toegepast op het `topic` patroon van de lokale Broker.

Hieronder ziet u een voor beeld van een IoT Edge MQTT-brug configuratie waarmee alle berichten die op `alerts/#` de onderwerpen van een bovenliggend IOT edge apparaat worden ontvangen, opnieuw worden gepubliceerd op een onderliggend IOT edge apparaat in dezelfde onderwerpen en alle berichten die op de onderwerpen `/local/telemetry/#` van een onderliggend IOT edge apparaat worden verzonden, opnieuw publiceert naar een bovenliggend IOT edge apparaat op onderwerpen `/remote/messages/#` .

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
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
Andere opmerkingen over de MQTT-brug van IoT Edge hub:
- Het MQTT-protocol wordt automatisch als upstream-protocol gebruikt wanneer de MQTT Broker wordt gebruikt en die IoT Edge wordt gebruikt in een geneste configuratie, bijvoorbeeld met een `parent_hostname` opgegeven. Zie [Cloud communicatie](iot-edge-runtime.md#cloud-communication)voor meer informatie over upstream-protocollen. Zie [een downstream IOT edge apparaat verbinden met een Azure IOT Edge gateway](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)voor meer informatie over geneste configuraties.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over de IoT Edge hub](iot-edge-runtime.md#iot-edge-hub)
