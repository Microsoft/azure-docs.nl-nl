---
title: Informatie over het IP-adres van uw IoT-hub | Microsoft Docs
description: Meer informatie over het opvragen van uw IoT hub IP-adres en de bijbehorende eigenschappen. Het IP-adres van uw IoT-hub kan worden gewijzigd tijdens bepaalde scenario's, zoals herstel na nood gevallen of een regionale failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 1bfae8c7afbfdc6e73dd8bb17b94e6543361e9ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83848225"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP-adressen

De IP-adres voorvoegsels van IoT Hub open bare eind punten worden periodiek gepubliceerd onder het label _AzureIoTHub_ - [service](../virtual-network/service-tags-overview.md).

> [!NOTE]
> Voor apparaten die zijn geïmplementeerd in een on-premises netwerk, ondersteunt Azure IoT Hub VNET-connectiviteits integratie met persoonlijke eind punten. Zie [IOT hub ondersteuning voor VNet](./virtual-network-support.md) voor meer informatie.


U kunt deze IP-adres voorvoegsels gebruiken om de connectiviteit tussen IoT Hub en uw apparaten of netwerk assets te beheren, zodat u verschillende netwerk isolatie doelen kunt implementeren:

| Doel | Toepasselijke scenario's | Methode |
|------|-----------|----------|
| Zorg ervoor dat uw apparaten en services met alleen IoT Hub-eind punten communiceren | [Apparaat-naar-Cloud](./iot-hub-devguide-messaging.md)-en [Cloud-naar-apparaat](./iot-hub-devguide-messages-c2d.md) -berichten, [directe methoden](./iot-hub-devguide-direct-methods.md), [apparaat-en module apparaatdubbels](./iot-hub-devguide-device-twins.md) en [streams](./iot-hub-device-streams-overview.md) van apparaten | Gebruik _AzureIoTHub_ -en _EventHub_ -service tags om IOT hub en Event hub IP-adres voorvoegsels te detecteren en configureer regels voor het op uw apparaten en services ingestelde firewall-instellingen voor deze IP-adres voorvoegsels dienovereenkomstig. verkeer verplaatsen naar andere doel-IP-adressen waarvoor u niet wilt dat de apparaten of services communiceren met. |
| Zorg ervoor dat het eind punt van uw IoT Hub alleen verbindingen van uw apparaten en netwerk assets ontvangt | [Apparaat-naar-Cloud](./iot-hub-devguide-messaging.md)-en [Cloud-naar-apparaat](./iot-hub-devguide-messages-c2d.md) -berichten, [directe methoden](./iot-hub-devguide-direct-methods.md), [apparaat-en module apparaatdubbels](./iot-hub-devguide-device-twins.md) en [streams](./iot-hub-device-streams-overview.md) van apparaten | Gebruik IoT Hub [IP-filter functie](iot-hub-ip-filtering.md) om verbindingen van uw apparaten en IP-adressen van netwerk activa (Zie de sectie [beperkingen](#limitations-and-workarounds) ) toe te staan. | 
| Zorg ervoor dat de aangepaste eindpunt resources (opslag accounts, service bus en Event hubs) van uw routes alleen bereikbaar zijn vanaf uw netwerk assets | [Berichtroutering](./iot-hub-devguide-messages-d2c.md) | Volg de richt lijnen van uw resource over het beperken van connectiviteit (bijvoorbeeld via [firewall regels](../storage/common/storage-network-security.md), [persoonlijke koppelingen](../private-link/private-endpoint-overview.md)of [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)). Gebruik _AzureIoTHub_ -service tags om IOT hub IP-adres voorvoegsels te detecteren en regels toe te voegen voor de IP-voor voegsels van de firewall configuratie van uw bron (Zie de sectie [beperkingen](#limitations-and-workarounds) ). |



## <a name="best-practices"></a>Aanbevolen procedures

* Wanneer u regels voor toestaan toevoegt aan de firewall configuratie van uw apparaten, kunt u het beste specifieke [poorten opgeven die worden gebruikt door de toepasselijke protocollen](./iot-hub-devguide-protocols.md#port-numbers).

* De IP-adres voorvoegsels van IoT hub zijn onderhevig aan wijzigingen. Deze wijzigingen worden regel matig gepubliceerd via service Tags voordat ze van kracht worden. Het is daarom belang rijk dat u processen ontwikkelt om regel matig de meest recente service tags op te halen en te gebruiken. Dit proces kan worden geautomatiseerd via de [service Tags detectie-API](../virtual-network/service-tags-overview.md#service-tags-on-premises). Houd er rekening mee dat service Tags detectie-API nog steeds beschikbaar is in de preview-versie. in sommige gevallen is het niet mogelijk om de volledige lijst met tags en IP-adressen te maken. Als de detectie-API algemeen beschikbaar is, kunt u overwegen de service tags te gebruiken in de te [downloaden JSON-indeling](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Gebruik de *AzureIoTHub. [ regio naam]* label voor het identificeren van IP-voor voegsels die worden gebruikt door IOT hub-eind punten in een specifieke regio. Als u wilt account voor herstel na nood gevallen van Data Center of [regionale failover](iot-hub-ha-dr.md) , zorgt u ervoor dat de verbinding met IP-voor voegsels van de geografische paar regio van uw IOT hub ook is ingeschakeld.

* Het instellen van firewall regels in IoT Hub blokkeert mogelijk de connectiviteit die nodig is om Azure CLI-en Power shell-opdrachten uit te voeren op uw IoT Hub. U kunt dit voor komen door regels toe te voegen voor de IP-adres voorvoegsels van uw clients om CLI-of Power shell-clients opnieuw in te scha kelen om met uw IoT Hub te communiceren.  


## <a name="limitations-and-workarounds"></a>Beperkingen en tijdelijke oplossingen

* IoT Hub IP-filter functie heeft een limiet van 10 regels. Deze limiet en kan via aanvragen worden verhoogd via de klant ondersteuning van Azure. 

* De geconfigureerde [IP-filter regels](iot-hub-ip-filtering.md) worden alleen toegepast op uw IOT hub IP-eind punten en niet op het ingebouwde Event hub-eind punt van uw IOT-hub. Als u ook wilt dat IP-filtering moet worden toegepast op de Event hub waar uw berichten worden opgeslagen, kunt u uw eigen event hub-resource plaatsen, waar u uw gewenste IP-filter regels rechtstreeks kunt configureren. Hiervoor moet u uw eigen event hub-resource inrichten en [bericht routering](./iot-hub-devguide-messages-d2c.md) instellen om uw berichten te verzenden naar die resource in plaats van de ingebouwde Event hub van uw IOT hub. Ten slotte, zoals beschreven in de bovenstaande tabel, om de functionaliteit voor bericht routering in te scha kelen, moet u ook verbinding met de IP-adres voorvoegsels van IoT Hub naar uw ingerichte Event hub-resource toestaan.

* Wanneer een route ring naar een opslag account wordt toegestaan, kan verkeer van de IP-adres voorvoegsels van IoT Hub alleen worden uitgevoerd als het opslag account zich in een andere regio bevindt als uw IoT Hub.

## <a name="support-for-ipv6"></a>Ondersteuning voor IPv6 

IPv6 wordt momenteel niet ondersteund in IoT Hub.
