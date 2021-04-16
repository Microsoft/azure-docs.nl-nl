---
title: Inzicht Azure IoT Hub eindpunten | Microsoft Docs
description: 'Ontwikkelaarshandleiding: naslaginformatie IoT Hub apparaat- en service-gerichte eindpunten.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: d2b9ea2e075ddcf20860ccb9ab1f2eff654993ad
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499373"
---
# <a name="reference---iot-hub-endpoints"></a>Naslag - IoT Hub eindpunten

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub namen

U vindt de hostnaam van de IoT-hub die uw eindpunten host in de portal op de overzichtspagina van  **uw** hub. De DNS-naam van een IoT-hub ziet er standaard als: `{your iot hub name}.azure-devices.net` .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lijst met ingebouwde IoT Hub eindpunten

Azure IoT Hub is een service met meerdere tenants die de functionaliteit beschikbaar maakt voor verschillende actoren. In het volgende diagram ziet u de verschillende eindpunten die IoT Hub worden weergegeven.

![IoT Hub-eindpunten](./media/iot-hub-devguide-endpoints/endpoints.png)

In de volgende lijst worden de eindpunten beschreven:

* **Resourceprovider**. De IoT Hub resourceprovider maakt een [](../azure-resource-manager/management/overview.md) Azure Resource Manager interface. Met deze interface kunnen eigenaren van Azure-abonnementen IoT-hubs maken en verwijderen en de eigenschappen van de IoT-hub bijwerken. IoT Hub eigenschappen bepalen het [beveiligingsbeleid](iot-hub-devguide-security.md#access-control-and-permissions)op hubniveau, in tegenstelling tot toegangsbeheer op apparaatniveau, en functionele opties voor cloud-naar-apparaat- en apparaat-naar-cloud-berichten. Met IoT Hub resourceprovider kunt u ook [apparaat-id's exporteren.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

* **Apparaatidentiteitsbeheer.** Elke IoT-hub toont een set HTTPS REST-eindpunten voor het beheren van apparaatidentiteiten (maken, ophalen, bijwerken en verwijderen). [Apparaat-id's](iot-hub-devguide-identity-registry.md) worden gebruikt voor apparaatverificatie en toegangsbeheer.

* **Beheer van apparaattweeling.** Elke IoT-hub geeft een set service-gerichte HTTPS REST-eindpunten weer om apparaat-tweelingen op te vragen en bij te werken [](iot-hub-devguide-device-twins.md) (updatetags en eigenschappen). 

* **Takenbeheer.** Elke IoT-hub toont een set service-gerichte HTTPS REST-eindpunten voor het uitvoeren van query's en het beheren van [taken.](iot-hub-devguide-jobs.md)

* **Apparaat-eindpunten**. Voor elk apparaat in het identiteitsregister IoT Hub een set eindpunten. Behalve waar vermeld, worden deze eindpunten blootgesteld met behulp van [de protocollen MQTT v3.1.1,](https://mqtt.org/)HTTPS 1.1 en [AMQP 1.0.](https://www.amqp.org/) AMQP en MQTT zijn ook beschikbaar via [WebSockets](https://tools.ietf.org/html/rfc6455) op poort 443.

  * *Apparaat-naar-cloud-berichten verzenden.* Een apparaat gebruikt dit eindpunt om [apparaat-naar-cloud-berichten te verzenden.](iot-hub-devguide-messages-d2c.md)

  * *Cloud-naar-apparaat-berichten ontvangen.* Een apparaat gebruikt dit eindpunt om gerichte [cloud-naar-apparaat-berichten te ontvangen.](iot-hub-devguide-messages-c2d.md)

  * *Start bestanduploads.* Een apparaat gebruikt dit eindpunt om een sas-URI Azure Storage ontvangen van IoT Hub om [een bestand te uploaden.](iot-hub-devguide-file-upload.md)

  * *Haal de eigenschappen van de apparaattweeling op en werk deze bij.* Een apparaat gebruikt dit eindpunt om toegang te krijgen tot de [eigenschappen](iot-hub-devguide-device-twins.md)van de apparaat dubbel. HTTPS wordt niet ondersteund.

  * *Aanvragen voor directe methoden ontvangen.* Een apparaat gebruikt dit eindpunt om te luisteren naar [aanvragen van](iot-hub-devguide-direct-methods.md)de directe methode. HTTPS wordt niet ondersteund.

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **Service-eindpunten**. Elke IoT-hub toont een set eindpunten voor de back-end van uw oplossing om te communiceren met uw apparaten. Met één uitzondering worden deze eindpunten alleen beschikbaar gesteld met behulp van [de AMQP](https://www.amqp.org/) en AMQP via WebSockets-protocollen. Het eindpunt voor het aanroepen van directe methoden wordt via het HTTPS-protocol blootgesteld.
  
  * *Apparaat-naar-cloud-berichten ontvangen.* Dit eindpunt is compatibel met [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Een back-endservice kan deze gebruiken om de [apparaat-naar-cloud-berichten](iot-hub-devguide-messages-d2c.md) te lezen die door uw apparaten worden verzonden. U kunt naast dit ingebouwde eindpunt ook aangepaste eindpunten maken op uw IoT-hub.
  
  * *Cloud-naar-apparaat-berichten verzenden en leveringsbevestigingen ontvangen.* Met deze eindpunten kan de back-end van uw oplossing betrouwbare [cloud-naar-apparaat-berichten](iot-hub-devguide-messages-c2d.md)verzenden en de bijbehorende bevestigingen voor levering of verloop ontvangen.
  
  * *Bestandsmeldingen ontvangen.* Met dit berichteindpunt kunt u meldingen ontvangen van wanneer uw apparaten een bestand uploaden. 
  
  * *Directe methode-aanroep*. Met dit eindpunt kan een back-endservice een directe [methode op een](iot-hub-devguide-direct-methods.md) apparaat aanroepen.
  
  * *Gebeurtenissen voor het bewaken van bewerkingen ontvangen.* Met dit eindpunt kunt u bewakingsgebeurtenissen voor bewerkingen ontvangen als uw IoT-hub is geconfigureerd om ze te zenden. Zie bewerkingen bewaken voor [IoT Hub meer informatie.](iot-hub-operations-monitoring.md)

In [het artikel Azure IoT SDK's](iot-hub-devguide-sdks.md) worden de verschillende manieren beschreven om toegang te krijgen tot deze eindpunten.

Alle IoT Hub gebruiken het [TLS-protocol](https://tools.ietf.org/html/rfc5246) en er wordt nooit een eindpunt blootgesteld aan niet-versleutelde/onbeveiligde kanalen.

## <a name="custom-endpoints"></a>Aangepaste eindpunten

U kunt bestaande Azure-services in uw Azure-abonnementen koppelen aan uw IoT-hub om te fungeren als eindpunten voor berichtroutering. Deze eindpunten fungeren als service-eindpunten en worden gebruikt als sinks voor berichtroutes. Apparaten kunnen niet rechtstreeks naar de extra eindpunten schrijven. Meer informatie over [berichtroutering.](../iot-hub/iot-hub-devguide-messages-d2c.md)

IoT Hub ondersteunt momenteel de volgende Azure-services als extra eindpunten:

* Azure Storage containers
* Event Hubs
* Service Bus-wachtrijen
* Service Bus-onderwerpen

Zie Quota en beperking voor de limieten voor het aantal eindpunten dat u [kunt toevoegen.](iot-hub-devguide-quotas-throttling.md)

## <a name="endpoint-health"></a>Endpoint Health

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>Veldgateways

In een IoT-oplossing bevindt *een veldgateway* zich tussen uw apparaten en IoT Hub eindpunten. Deze bevindt zich meestal dicht bij uw apparaten. Uw apparaten communiceren rechtstreeks met de veldgateway met behulp van een protocol dat wordt ondersteund door de apparaten. De veldgateway maakt verbinding met IoT Hub eindpunt met behulp van een protocol dat wordt ondersteund door IoT Hub. Een veldgateway kan een toegewezen hardwareapparaat of een computer met weinig vermogen zijn met aangepaste gatewaysoftware.

U kunt deze [Azure IoT Edge](../iot-edge/index.yml) een veldgateway te implementeren. IoT Edge biedt functionaliteit zoals multiplexing van communicatie van meerdere apparaten naar dezelfde IoT Hub verbinding.

## <a name="next-steps"></a>Volgende stappen

Andere naslagonderwerpen in IoT Hub ontwikkelaarshandleiding zijn:

* [IoT Hub querytaal voor apparaattweeling, taken en berichtroutering](iot-hub-devguide-query-language.md)
* [Quota en beperkingen](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-ondersteuning](iot-hub-mqtt-support.md)
* [Inzicht in het IP-adres van uw IoT-hub](iot-hub-understand-ip-address.md)
