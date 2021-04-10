---
title: Gateways voor downstream-apparaten-Azure IoT Edge | Microsoft Docs
description: Gebruik Azure IoT Edge om een transparant, dekkend of proxy gateway-apparaat te maken dat gegevens van meerdere downstream-apparaten naar de Cloud verzendt of lokaal verwerkt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/23/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: aa8b7372af91fc7cb194dfc3a6212cb4ce1fa0a2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027343"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Hoe een IoT Edge-apparaat kan worden gebruikt als gateway

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge-apparaten kunnen als gateways fungeren, waardoor er een verbinding is tussen andere apparaten op het netwerk en IoT Hub.

De module IoT Edge hub fungeert als IoT Hub, zodat u verbindingen kunt afhandelen van andere apparaten die een identiteit hebben met dezelfde IoT-hub. Dit type gateway patroon wordt *transparant* genoemd, omdat berichten van downstream-apparaten kunnen worden door gegeven aan IOT hub alsof er geen gateway tussen hen is.

Voor apparaten die niet of geen verbinding kunnen maken met IoT Hub zelf, kunnen IoT Edge gateways deze verbinding bieden. Dit type gateway patroon wordt *omzetting* genoemd, omdat het IOT edge apparaat de verwerking van inkomende downstream-apparaten moet uitvoeren voordat deze naar IOT hub kunnen worden doorgestuurd. Deze scenario's vereisen extra modules op de IoT Edge-gateway om de verwerkings stappen af te handelen.

De transparante en vertaal gateway patronen sluiten elkaar niet uit. Eén IoT Edge apparaat kan worden gebruikt als een transparante gateway en een Vertaal gateway.

Alle gateway patronen bieden de volgende voor delen:

* **Analytics aan de rand** : gebruik AI-services lokaal om gegevens te verwerken die afkomstig zijn van downstream-apparaten zonder een telemetrie met een volledige kwaliteit naar de cloud te verzenden. Zoek en reageer lokaal op inzichten en verzend alleen een subset van gegevens naar IoT Hub.
* De isolatie van het **downstream-apparaat** : het gateway apparaat kan alle downstream-apparaten afschermen tegen bloot stelling aan Internet. Het kan liggen tussen een operationeel technologie netwerk (OT) dat geen connectiviteit heeft en een IT-netwerk dat toegang biedt tot het web. Op dezelfde manier kunnen apparaten die geen verbinding kunnen maken met IoT Hub zelf, verbinding maken met een gateway apparaat.
* **Verbindings multiplexing** : alle apparaten die verbinding maken met IOT hub via een IOT Edge gateway kunnen dezelfde onderliggende verbinding gebruiken. Deze multiplexing-functionaliteit vereist dat de IoT Edge gateway AMQP als upstream-protocol gebruikt.
* Uitgaand **verkeer** : het IOT edge apparaat implementeert automatisch exponentiële uitstel als IOT hub het verkeer beperkt, terwijl de berichten lokaal worden bewaard. Dit voor deel is uw oplossing robuust voor pieken in het verkeer.
* **Offline ondersteuning** : het gateway apparaat slaat berichten en dubbele updates op die niet aan IOT hub kunnen worden geleverd.

## <a name="transparent-gateways"></a>Transparante gateways

In het transparante gateway patroon kunnen apparaten die theoretisch verbinding maken met IoT Hub, in plaats daarvan verbinding maken met een gateway apparaat. De downstream-apparaten hebben hun eigen IoT Hub identiteiten en verbinden met behulp van de MQTT-of AMQP-protocollen. Via de gateway wordt simpelweg communicatie tussen de apparaten en IoT Hub doorgegeven. Zowel de apparaten als de gebruikers die met hen communiceren via IoT Hub, zijn niet op de hoogte van de communicatie van een gateway. Dit gebrek aan bewustzijn betekent dat de gateway als *transparant* wordt beschouwd.

Zie [inzicht in de Azure IOT Edge runtime en de architectuur](iot-edge-runtime.md)voor meer informatie over hoe de IOT Edge hub communicatie beheert tussen downstream-apparaten en de Cloud.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"
![Diagram-transparant gateway patroon](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

>[!NOTE]
>In IoT Edge versie 1,1 en ouder kunnen IoT Edge apparaten niet worden downstream van een IoT Edge gateway.
>
>Vanaf versie 1,2 van IoT Edge kunnen transparante gateways verbindingen van downstream IoT Edge apparaten afhandelen. Voor meer informatie gaat u naar de [IoT Edge 1,2](?view=iotedge-2020-11&preserve-view=true) -versie van dit artikel.

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Vanaf versie 1,2 van IoT Edge kunnen transparante gateways verbindingen van downstream IoT Edge apparaten afhandelen.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>Relaties tussen bovenliggende en onderliggende items

U declareert transparante gateway relaties in IoT Hub door de IoT Edge gateway in te stellen als het *bovenliggende* knoop punt van een *onderliggend* downstream-apparaat dat hiermee verbinding maakt.

De bovenliggende/onderliggende relatie wordt tot stand gebracht op drie punten in de gateway configuratie:

#### <a name="cloud-identities"></a>Cloud-identiteiten

Voor alle apparaten in een transparant Gateway scenario zijn Cloud identiteiten vereist zodat ze kunnen worden geverifieerd bij IoT Hub. Wanneer u een apparaat-id maakt of bijwerkt, kunt u de bovenliggende of onderliggende apparaten van het apparaat instellen. Met deze configuratie wordt het bovenliggende gateway apparaat geautoriseerd om verificatie voor de onderliggende apparaten te verwerken.

>[!NOTE]
>Instellen van het bovenliggende apparaat in IoT Hub gebruikt als een optionele stap voor downstream-apparaten die gebruikmaken van symmetrische sleutel verificatie. Vanaf versie 1.1.0 moet elk downstream-apparaat echter worden toegewezen aan een bovenliggend apparaat.
>
>U kunt de IoT Edge hub configureren om terug te gaan naar het vorige gedrag door de omgevings variabele **AuthenticationMode** in te stellen op de waarde **CloudAndScope**.

Onderliggende apparaten kunnen slechts één bovenliggend item hebben. Elk bovenliggend item kan Maxi maal 100 onderliggende items hebben.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge-apparaten kunnen zowel ouders als onderliggende items zijn in transparante gateway-relaties. Er kan een hiërarchie worden gemaakt van meerdere IoT Edge apparaten die aan elkaar rapporteren. Het bovenste knoop punt van een gateway hiërarchie kan Maxi maal vijf generaties van onderliggende items hebben. Een IoT Edge apparaat kan bijvoorbeeld vijf lagen IoT Edge apparaten die als onderliggende items zijn gekoppeld. Het IoT Edge apparaat in de vijfde generatie kan echter geen kinderen hebben, IoT Edge of anderszins.
::: moniker-end

#### <a name="gateway-discovery"></a>Gateway detectie

Een onderliggend apparaat moet het bovenliggende apparaat kunnen vinden in het lokale netwerk. Configureer gateway-apparaten met een **hostnaam**, een Fully QUALIFIED domain name (FQDN) of een IP-adres, die door de onderliggende apparaten worden gebruikt om deze te zoeken.

Gebruik op downstream IoT-apparaten de para meter **gatewayHostname** in de Connection String om naar het bovenliggende apparaat te verwijzen.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Gebruik op downstream IoT Edge-apparaten de para meter **parent_hostname** in het configuratie bestand om naar het bovenliggende apparaat te verwijzen.
::: moniker-end

#### <a name="secure-connection"></a>Beveiligde verbinding

Bovenliggende en onderliggende apparaten moeten ook hun verbindingen met elkaar verifiëren. Elk apparaat heeft een kopie nodig van een gedeeld basis-CA-certificaat dat door de onderliggende apparaten wordt gebruikt om te controleren of ze verbinding maken met de juiste gateway.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Wanneer meerdere IoT Edge gateways met elkaar in een gateway hiërarchie verbinding maken, moeten alle apparaten in de hiërarchie één certificaat keten gebruiken.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>Mogelijkheden van apparaten achter transparante gateways

Alle IoT Hub primitieven die samen werken met de Messa ging-pijp lijn van IoT Edge ondersteunen ook transparante gateway scenario's. Elke IoT Edge gateway heeft opslag-en doorstuur mogelijkheden voor berichten die worden verzonden.

Gebruik de volgende tabel om te zien hoe verschillende IoT Hub mogelijkheden voor apparaten worden ondersteund, vergeleken met apparaten achter gateways.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

| Mogelijkheid | IoT-apparaat | IoT achter een gateway |
| ---------- | ---------- | -------------------- |
| [Apparaat-naar-Cloud-berichten (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Ja-IoT-D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, kinder IoT-D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Cloud-naar-apparaat-berichten (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Ja-IoT-C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, IoT-onderliggend C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [Directe methoden](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Yes-IoT direct-methode](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, onderliggende IoT direct-methode](./media/iot-edge-as-gateway/check-yes.png) |
| [Apparaatdubbels](../iot-hub/iot-hub-devguide-device-twins.md) en [module apparaatdubbels](../iot-hub/iot-hub-devguide-module-twins.md) | ![Ja-IoT-apparaatdubbels](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, kinder IoT-apparaatdubbels](./media/iot-edge-as-gateway/check-yes.png) |
| [Bestand uploaden](../iot-hub/iot-hub-devguide-file-upload.md) | ![Ja, IoT-bestand uploaden](./media/iot-edge-as-gateway/check-yes.png) | ![Geen-IoT-onderliggend bestand uploaden](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| Mogelijkheid | IoT-apparaat | IoT achter een gateway | IoT Edge-apparaat | IoT Edge achter een gateway |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [Apparaat-naar-Cloud-berichten (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Ja-IoT-D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, kinder IoT-D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, onderliggende IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Cloud-naar-apparaat-berichten (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Ja-IoT-C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, IoT-onderliggend C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Geen-IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![Geen-IoT Edge onderliggende C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [Directe methoden](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Yes-IoT direct-methode](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, onderliggende IoT direct-methode](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, IoT Edge directe methode](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, onderliggende IoT Edge directe methode](./media/iot-edge-as-gateway/check-yes.png) |
| [Apparaatdubbels](../iot-hub/iot-hub-devguide-device-twins.md) en [module apparaatdubbels](../iot-hub/iot-hub-devguide-module-twins.md) | ![Ja-IoT-apparaatdubbels](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, kinder IoT-apparaatdubbels](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, IoT Edge apparaatdubbels](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, onderliggende IoT Edge apparaatdubbels](./media/iot-edge-as-gateway/check-yes.png) |
| [Bestand uploaden](../iot-hub/iot-hub-devguide-file-upload.md) | ![Ja, IoT-bestand uploaden](./media/iot-edge-as-gateway/check-yes.png) | ![Geen-IoT-onderliggend bestand uploaden](./media/iot-edge-as-gateway/crossout-no.png) | ![Bestand uploaden zonder IoT Edge](./media/iot-edge-as-gateway/crossout-no.png) | ![Geen-IoT Edge voor het uploaden van het onderliggende bestand](./media/iot-edge-as-gateway/crossout-no.png) |
| Container installatie kopieën ophalen |   |   | ![Container-pull met Ja-IoT Edge](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, onderliggende IoT Edge container pull](./media/iot-edge-as-gateway/check-yes.png) |
| BLOB uploaden |   |   | ![Ja, IoT Edge BLOB uploaden](./media/iot-edge-as-gateway/check-yes.png) | ![Ja, onderliggende IoT Edge BLOB uploaden](./media/iot-edge-as-gateway/check-yes.png) |

**Container installatie kopieën** kunnen worden gedownload, opgeslagen en bezorgd van bovenliggende apparaten op onderliggende apparaten.

**Blobs**, waaronder ondersteunings bundels en Logboeken, kunnen worden geüpload van onderliggende apparaten naar bovenliggende apparaten.

::: moniker-end

## <a name="translation-gateways"></a>Vertaal gateways

Als downstream-apparaten geen verbinding kunnen maken met IoT Hub, moet de IoT Edge-gateway fungeren als een vertaler. Dit patroon is vaak vereist voor apparaten die geen ondersteuning bieden voor MQTT, AMQP of HTTP. Omdat deze apparaten geen verbinding kunnen maken met IoT Hub, kunnen ze ook geen verbinding maken met de IoT Edge hub-module zonder enige voor bereiding.

Aangepaste of modules van derden die vaak specifiek zijn voor de hardware of het Protocol van het downstream-apparaat, moeten worden geïmplementeerd op de IoT Edge gateway. Deze Vertaal modules nemen de inkomende berichten en scha kelen in een indeling die kan worden geïnterpreteerd door IoT Hub.

Er zijn twee patronen voor vertaal gateways: *protocol omzetting* en *identiteits omzetting*.

![Diagram-Vertaal gateway patronen](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>Protocol omzetting

In het patroon van de Protocol conversie gateway heeft alleen de IoT Edge gateway een identiteit met IoT Hub. De Vertaal module ontvangt berichten van downstream-apparaten, zet ze om in een ondersteund protocol en vervolgens verzendt het IoT Edge apparaat de berichten namens de downstream-apparaten. Alle informatie lijkt op het ene apparaat, de gateway. Downstreamapparaten moeten extra identificatiegegevens in hun berichten insluiten als cloudtoepassingen de gegevens per apparaat willen analyseren. Daarnaast worden IoT Hub primitieven, zoals apparaatdubbels en directe methoden, alleen ondersteund voor het gateway apparaat, niet voor downstream-apparaten. Gateways in dit patroon worden in tegens telling tot transparante gateways beschouwd als *dekkend* , omdat ze de identiteiten van downstream-apparaten verbergen.

Protocol vertaling ondersteunt apparaten die zijn beperkt tot resources. Veel bestaande apparaten produceren gegevens die zakelijke inzichten kunnen ervaren. ze zijn echter niet ontworpen met het oog op Cloud connectiviteit. Met dekkende gateways kunnen deze gegevens worden ontgrendeld en worden gebruikt in een IoT-oplossing.

### <a name="identity-translation"></a>Identiteits vertalingen

Het gateway patroon voor identiteits vertalingen is gebaseerd op protocol omzetting, maar de IoT Edge gateway biedt ook een IoT Hub apparaat-id namens de downstream-apparaten. De Vertaal module is verantwoordelijk voor het inzicht in het protocol dat wordt gebruikt door de downstream-apparaten, het leveren van de identiteit en het vertalen van hun berichten naar IoT Hub primitieven. Downstreamapparaten worden in IoT Hub weergegeven als eersteklasapparaten met apparaatdubbels en methoden. Een gebruiker kan communiceren met de apparaten in IoT Hub en is zich niet bewust van het tussenliggende gatewayapparaat.

Identiteits vertalingen bieden de voor delen van protocol vertalingen en biedt bovendien volledige beheer baarheid van downstream-apparaten vanuit de Cloud. Alle apparaten in uw IoT-oplossing worden weer gegeven in IoT Hub, ongeacht het protocol dat ze gebruiken.

### <a name="device-capabilities-behind-translation-gateways"></a>Mogelijkheden van apparaten achter Vertaal gateways

In de volgende tabel wordt uitgelegd hoe IoT Hub-functies worden uitgebreid naar downstream-apparaten in beide patronen van de Vertaal gateway.

| Mogelijkheid | Protocol omzetting | Identiteits vertalingen |
| ---------- | -------------------- | -------------------- |
| Identiteiten die zijn opgeslagen in het IoT Hub-identiteits register | Alleen de identiteit van het gateway apparaat | Identiteit van alle verbonden apparaten |
| Dubbel apparaat | Alleen de gateway heeft een apparaat-en module-apparaatdubbels | Elk verbonden apparaat heeft een eigen apparaat, twee |
| Directe methoden en Cloud-naar-apparaat-berichten | De cloud kan alleen het gateway apparaat adresseren | De cloud kan elk aangesloten apparaat afzonderlijk adresseren |
| [Beperkings-en quota IoT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Toep assen op het gateway apparaat | Toep assen op elk apparaat |

Wanneer u het Protocol conversie patroon gebruikt, delen alle apparaten die via die gateway verbinding maken dezelfde Cloud-naar-apparaat-wachtrij, die Maxi maal 50 berichten kan bevatten. Gebruik dit patroon alleen wanneer er weinig apparaten zijn die verbinding maken via elke veld Gateway en het verkeer van de Cloud naar het apparaat laag is.

De IoT Edge runtime bevat geen mogelijkheden voor het converteren van protocollen of identiteiten. Deze patronen vereisen aangepaste of modules van derden die vaak specifiek zijn voor de hardware en het gebruikte protocol. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) bevat verschillende modules voor protocol omzetting waaruit u kunt kiezen. Zie [Azure IOT Edge LoRaWAN Starter Kit](https://github.com/Azure/iotedge-lorawan-starterkit)voor een voor beeld dat gebruikmaakt van het Vertaal patroon voor identiteiten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de drie stappen voor het instellen van een transparante gateway:

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstream-apparaat verifiëren voor Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
