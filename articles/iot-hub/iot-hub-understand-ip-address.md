---
title: Informatie over het IP-adres van uw IoT-hub-| Microsoft Docs
description: Meer inzicht in het uitvoeren van query's op het IP-adres van uw IoT-hub en de eigenschappen ervan. Het IP-adres van uw IoT-hub kan worden gewijzigd tijdens bepaalde scenario's, zoals herstel na noodherstel of regionale failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 7d807a15d358bd621baedbff253f0c731e43ed26
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874167"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP-adressen

De IP-adres voorvoegsels IoT Hub openbare eindpunten worden periodiek gepubliceerd onder de _AzureIoTHub-servicetag_ [](../virtual-network/service-tags-overview.md).

> [!NOTE]
> Voor apparaten die in on-premises netwerken zijn geïmplementeerd, ondersteunt Azure IoT Hub integratie van VNET-connectiviteit met privé-eindpunten. Zie [IoT Hub ondersteuning voor VNet voor](./virtual-network-support.md) meer informatie.


U kunt deze IP-adres voorvoegsels gebruiken om de connectiviteit tussen IoT Hub en uw apparaten of netwerkactiva te beheren om verschillende doelen voor netwerkisolatie te implementeren:

| Doel | Toepasselijke scenario's | Methode |
|------|-----------|----------|
| Zorg ervoor dat uw apparaten en services communiceren met IoT Hub eindpunten | [Apparaat-naar-cloud-](./iot-hub-devguide-messaging.md)en [cloud-naar-apparaat-berichten,](./iot-hub-devguide-messages-c2d.md) [directe methoden,](./iot-hub-devguide-direct-methods.md)apparaat- en [module-tweelingen](./iot-hub-devguide-device-twins.md) en [apparaatstreams](./iot-hub-device-streams-overview.md) | Gebruik _AzureIoTHub-_ en _EventHub-servicetags_ om IoT Hub en EVENT Hub IP-adres voorvoegsels te ontdekken en regels voor TOESTAAN te configureren op de firewallinstelling van uw apparaten en services voor deze IP-adres voorvoegsels; zet verkeer neer op andere DOEL-IP-adressen waar de apparaten of services niet mee mogen communiceren. |
| Zorg ervoor IoT Hub dat het apparaat-eindpunt alleen verbindingen ontvangt van uw apparaten en netwerkactiva | [Apparaat-naar-cloud-](./iot-hub-devguide-messaging.md)en [cloud-naar-apparaat-berichten,](./iot-hub-devguide-messages-c2d.md) [directe methoden,](./iot-hub-devguide-direct-methods.md)apparaat- en [module-tweelingen](./iot-hub-devguide-device-twins.md) en [apparaatstreams](./iot-hub-device-streams-overview.md) | Gebruik IoT Hub [IP-filterfunctie](iot-hub-ip-filtering.md) om verbindingen vanaf uw apparaten en IP-adressen van netwerkactiva toe te staan (zie [de sectie beperkingen).](#limitations-and-workarounds) | 
| Zorg ervoor dat de aangepaste eindpuntresources van uw routes (opslagaccounts, Service Bus en Event Hubs) alleen bereikbaar zijn vanaf uw netwerkactiva | [Berichtroutering](./iot-hub-devguide-messages-d2c.md) | Volg de richtlijnen van uw resource voor het beperken van connectiviteit (bijvoorbeeld via [firewallregels,](../storage/common/storage-network-security.md) [privékoppelingen](../private-link/private-endpoint-overview.md)of [service-eindpunten);](../virtual-network/virtual-network-service-endpoints-overview.md) Gebruik _AzureIoTHub-servicetags_ om IoT Hub IP-adres voorvoegsels te ontdekken en ALLOW-regels toe te voegen voor deze IP-voorvoegsels in de firewallconfiguratie van uw resource (zie de [sectie beperkingen).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Aanbevolen procedures

* Wanneer u ALLOW-regels toevoegt aan de firewallconfiguratie van uw apparaten, kunt u het beste specifieke poorten bieden die worden gebruikt [door toepasselijke protocollen.](./iot-hub-devguide-protocols.md#port-numbers)

* De IP-adres voorvoegsels van de IoT-hub kunnen worden gewijzigd. Deze wijzigingen worden regelmatig gepubliceerd via servicetags voordat ze van kracht worden. Het is daarom belangrijk dat u processen ontwikkelt om regelmatig de nieuwste servicetags op te halen en te gebruiken. Dit proces kan worden geautomatiseerd via de [detectie-API voor servicetags.](../virtual-network/service-tags-overview.md#service-tags-on-premises) Houd er rekening mee dat de detectie-API voor servicetags nog steeds in preview is en dat in sommige gevallen niet de volledige lijst met tags en IP-adressen wordt geproduceerd. Overweeg de servicetags in downloadbare JSON-indeling te gebruiken totdat de [detectie-API algemeen beschikbaar is.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* *AzureIoTHub gebruiken.[ regionaam]* om IP-voorvoegsels te identificeren die worden gebruikt door IoT-hub-eindpunten in een specifieke regio. Zorg er ook voor dat connectiviteit met IP-voorvoegsels van de geopaarregio van uw IoT Hub is ingeschakeld om rekening te houden met herstel na noodherstel van datacenters of regionale [failover.](iot-hub-ha-dr.md)

* Het instellen van firewallregels in IoT Hub mogelijk de connectiviteit die nodig is om Azure CLI- en PowerShell-opdrachten uit te voeren op uw IoT Hub. U kunt dit voorkomen door REGELS VOOR TOESTAAN toe te voegen voor de IP-adres voorvoegsels van uw clients, zodat CLI- of PowerShell-clients opnieuw kunnen communiceren met uw IoT Hub.  


## <a name="limitations-and-workarounds"></a>Beperkingen en tijdelijke oplossingen

* IoT Hub IP-filterfunctie heeft een limiet van 100 regels. Deze limiet en kunnen worden verhoogd via aanvragen via de klantondersteuning van Azure. 

* Uw geconfigureerde [IP-filterregels](iot-hub-ip-filtering.md) worden alleen toegepast op uw IoT Hub IP-eindpunten en niet op het ingebouwde Event Hub-eindpunt van uw IoT-hub. Als u ook vereist dat IP-filtering wordt toegepast op de Event Hub waar uw berichten worden opgeslagen, kunt u dit doen met uw eigen Event Hub-resource, waar u de gewenste IP-filterregels rechtstreeks kunt configureren. Om dit te doen, moet u uw eigen [](./iot-hub-devguide-messages-d2c.md) Event Hub-resource inrichten en berichtroutering instellen om uw berichten naar die resource te verzenden in plaats van de ingebouwde Event Hub van uw IoT Hub. Ten slotte, zoals beschreven in de bovenstaande tabel, moet u, om de functionaliteit voor berichtroutering in te schakelen, ook connectiviteit toestaan vanuit de IP-adres voorvoegsels van IoT Hub naar uw inrichtende Event Hub-resource.

* Bij routering naar een opslagaccount is het toestaan van verkeer van de IP-adres voorvoegsels van IoT Hub alleen mogelijk wanneer het opslagaccount zich in een andere regio dan uw IoT Hub.

## <a name="support-for-ipv6"></a>Ondersteuning voor IPv6 

IPv6 wordt momenteel niet ondersteund op IoT Hub.
