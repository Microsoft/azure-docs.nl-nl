---
title: Communicatie protocollen en poorten voor Azure IoT Hub | Microsoft Docs
description: 'Ontwikkelaars handleiding: hierin worden de ondersteunde communicatie protocollen voor apparaat-naar-Cloud-en Cloud-naar-apparaat-communicatie en de te openen poort nummers beschreven.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: e2578b47d27ef062d83ba8621a49e9a8f439897c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919022"
---
# <a name="reference---choose-a-communication-protocol"></a>Referentie-Kies een communicatie protocol

Met IoT Hub kunnen voor apparaten de volgende protocollen worden gebruikt voor communicatie aan de zijde van het apparaat:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT via WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP via WebSockets
* HTTPS

Zie [apparaat-naar-Cloud-communicatie richtlijnen](iot-hub-devguide-d2c-guidance.md) en [Cloud-naar-apparaat-communicatie richtlijnen](iot-hub-devguide-c2d-guidance.md)voor meer informatie over de wijze waarop deze protocollen specifieke IOT hub functies ondersteunen.

De volgende tabel bevat de aanbevelingen op hoog niveau voor uw keuze van het Protocol:

| Protocol | Wanneer u dit protocol moet kiezen |
| --- | --- |
| MQTT <br> MQTT via WebSocket |Gebruik op alle apparaten die geen verbinding hoeven te maken met meerdere apparaten (elk met een eigen referenties per apparaat) via dezelfde TLS-verbinding. |
| AMQP <br> AMQP via WebSocket |Gebruik op veld-en Cloud gateways om verbinding te maken met multiplexing op verschillende apparaten. |
| HTTPS |Gebruiken voor apparaten die geen ondersteuning bieden voor andere protocollen. |

Houd rekening met de volgende punten wanneer u uw protocol kiest voor communicatie aan de apparaten:

* **Patroon van Cloud naar apparaat**. HTTPS heeft geen efficiënte manier voor het implementeren van server push. Wanneer u HTTPS gebruikt, worden de apparaten gecontroleerd IoT Hub voor Cloud-naar-apparaat-berichten. Deze aanpak is inefficiënt voor zowel het apparaat als het IoT Hub. Onder de huidige HTTPS-richt lijnen moet elk apparaat elke 25 minuten een poll voor berichten ontvangen. Het uitgeven van meer HTTPS resulteert in IoT Hub het beperken van de aanvragen. MQTT en AMQP ondersteunings server push bij het ontvangen van Cloud-naar-apparaat-berichten. Ze scha kelen onmiddellijke push berichten van IoT Hub naar het apparaat. Als leverings latentie een probleem is, zijn MQTT of AMQP de beste protocollen om te gebruiken. Voor zelden verbonden apparaten werkt HTTPS ook.

* **Veld gateways**. MQTT en HTTPS ondersteunen per TLS-verbinding slechts één apparaat-ID, plus referenties. Daarom worden deze protocollen niet ondersteund voor [veld Gateway scenario's](iot-hub-devguide-endpoints.md#field-gateways) waarvoor multiplex berichten moeten worden gebruikt met meerdere apparaat-id's over een enkele of een pool van upstream-verbindingen naar IOT hub. Dergelijke gateways kunnen gebruikmaken van een protocol dat ondersteuning biedt voor meerdere apparaat-id's per verbinding, zoals AMQP, voor hun upstream-verkeer.

* **Weinig bron apparaten**. De MQTT-en HTTPS-bibliotheken hebben een kleinere footprint dan de AMQP-bibliotheken. Als het apparaat een beperkt aantal bronnen heeft (bijvoorbeeld minder dan 1 MB RAM-geheugen), zijn deze protocollen mogelijk de enige protocol implementatie die beschikbaar is.

* **Netwerk-traversal**. Het standaard AMQP-protocol gebruikt poort 5671 en MQTT luistert op poort 8883. Het gebruik van deze poorten kan problemen veroorzaken in netwerken die worden gesloten met niet-HTTPS-protocollen. Gebruik MQTT over websockets, AMQP via websockets of HTTPS in dit scenario.

* **Grootte van nettolading**. MQTT en AMQP zijn binaire protocollen, wat resulteert in meer compact payloads dan HTTPS.

> [!WARNING]
> Wanneer u HTTPS gebruikt, moet elk apparaat niet vaker dan eens in de 25 minuten worden gepeild voor Cloud-naar-apparaat-berichten. In de ontwikkeling kan elk apparaat vaker worden doorzocht, indien gewenst.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="port-numbers"></a>Poort nummers

Apparaten kunnen met behulp van verschillende protocollen communiceren met IoT Hub in Azure. Normaal gesp roken wordt de keuze van het protocol aangestuurd door de specifieke vereisten van de oplossing. De volgende tabel geeft een lijst van de uitgaande poorten die open moeten zijn voor een apparaat om een specifiek protocol te kunnen gebruiken:

| Protocol | Poort |
| --- | --- |
| MQTT |8883 |
| MQTT via WebSockets |443 |
| AMQP |5671 |
| AMQP via WebSockets |443 |
| HTTPS |443 |

Zodra u een IoT-hub in een Azure-regio hebt gemaakt, behoudt de IoT-hub hetzelfde IP-adres voor de levens duur van de IoT-hub. Als micro soft de IoT-hub echter verplaatst naar een andere schaal eenheid om de kwaliteit van de service te behouden, wordt er een nieuw IP-adres toegewezen.

## <a name="next-steps"></a>Volgende stappen

Zie [communiceren met uw IOT-hub met behulp van het MQTT-protocol](iot-hub-mqtt-support.md)voor meer informatie over de wijze waarop IOT hub het MQTT-protocol implementeert.
