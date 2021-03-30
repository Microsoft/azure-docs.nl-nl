---
title: Informatie over het IP-adres van uw IoT Device Provisioning Service-instantie (DPS) | Microsoft Docs
description: Meer informatie over het opvragen van uw gegevens van uw IoT Device Provisioning Service (DPS) en de bijbehorende eigenschappen. Het IP-adres van uw DPS-exemplaar kan worden gewijzigd tijdens bepaalde scenario's, zoals nood herstel of een regionale failover.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "79284926"
---
# <a name="iot-hub-dps-ip-addresses"></a>IP-adressen van IoT Hub DPS

De IP-adres voorvoegsels voor de open bare eind punten van een IoT Hub Device Provisioning Service (DPS) worden periodiek gepubliceerd onder het label _AzureIoTHub_ - [service](../virtual-network/service-tags-overview.md). U kunt deze IP-adres voorvoegsels gebruiken om de connectiviteit tussen een IoT DPS-exemplaar en apparaten of netwerk assets te beheren om diverse netwerk isolatie doelen te implementeren:

| Doel | Methode |
|------|----------|
| Zorg ervoor dat uw apparaten en services alleen communiceren met IoT Hub DPS-eind punten | Gebruik het _AzureIoTHub_ -service label om IOT hub DPS-instanties te detecteren. Configureer regels voor toestaan op de firewall instelling van uw apparaten en services voor deze IP-adres voorvoegsels dienovereenkomstig. Configureer regels om verkeer te verwijderen naar andere doel-IP-adressen waarvan u niet wilt dat apparaten of services communiceren met. |
| Zorg ervoor dat uw IoT Hub DPS-eind punt alleen verbindingen van uw apparaten en netwerk assets ontvangt | Gebruik de [functie IP-filter](iot-dps-ip-filtering.md) van IOT DPS om filter regels te maken voor de api's van het apparaat en de DPS-service. Deze filter regels kunnen worden gebruikt om alleen verbindingen toe te staan vanaf uw apparaten en IP-adressen van netwerk activa (Zie de sectie [beperkingen](#limitations-and-workarounds) ). | 




## <a name="best-practices"></a>Aanbevolen procedures

* Wanneer u regels voor toestaan toevoegt aan de firewall configuratie van uw apparaten, kunt u het beste specifieke [poorten opgeven die worden gebruikt door de toepasselijke protocollen](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* De IP-adres voorvoegsels van IoT DPS-instanties zijn onderhevig aan wijzigingen. Deze wijzigingen worden regel matig gepubliceerd via service Tags voordat ze van kracht worden. Het is daarom belang rijk dat u processen ontwikkelt om regel matig de meest recente service tags op te halen en te gebruiken. Dit proces kan worden geautomatiseerd via de [service Tags detectie-API](../virtual-network/service-tags-overview.md#service-tags-on-premises). De service Tags detectie-API bevindt zich nog in de preview-versie en in sommige gevallen wordt mogelijk niet de volledige lijst met tags en IP-adressen gegenereerd. Als de detectie-API algemeen beschikbaar is, kunt u overwegen de service tags te gebruiken in de te [downloaden JSON-indeling](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Gebruik de *AzureIoTHub. [ regio naam]* label voor het identificeren van IP-voor voegsels die worden gebruikt door DPS-eind punten in een specifieke regio. Als u wilt account voor nood herstel in Data Center of [regionale failover](../iot-hub/iot-hub-ha-dr.md), zorgt u ervoor dat de verbinding met IP-voor voegsels van de geografische paar regio van uw DPS-exemplaar ook is ingeschakeld.

* Door firewall regels in te stellen voor een DPS-exemplaar, kan de connectiviteit die nodig is voor het uitvoeren van Azure CLI-en Power shell-opdrachten, worden geblokkeerd. Als u deze verbindings problemen wilt voor komen, kunt u regels voor de IP-adres voorvoegsels van uw clients toevoegen, zodat CLI-of Power shell-clients opnieuw kunnen communiceren met uw DPS-exemplaar.  


## <a name="limitations-and-workarounds"></a>Beperkingen en tijdelijke oplossingen

* De IP-filter functie voor DPS heeft een limiet van 100 regels. Deze limiet en kan via aanvragen worden verhoogd via de klant ondersteuning van Azure. 

* Uw geconfigureerde [IP-filter regels](iot-dps-ip-filtering.md) worden alleen toegepast op uw DPS-eind punten en niet op de gekoppelde IOT hub-eind punten. IP-filtering voor gekoppelde IoT-hubs moet afzonderlijk worden geconfigureerd. Zie [IOT hub regels voor IP-filtering](../iot-hub/iot-hub-ip-filtering.md)voor meer informatie.

## <a name="support-for-ipv6"></a>Ondersteuning voor IPv6 

IPv6 wordt momenteel niet ondersteund in IoT Hub of DPS.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over IP-adres configuraties met DPS:

* [IP-filtering configureren](iot-dps-ip-filtering.md)
