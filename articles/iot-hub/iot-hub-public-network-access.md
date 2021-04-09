---
title: Open bare netwerk toegang voor Azure IoT hub beheren
description: Documentatie over het uitschakelen en inschakelen van open bare netwerk toegang voor IoT hub
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: fbbdaeb796dfa23906c8010a54af14eff6df0b97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026637"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Open bare netwerk toegang beheren voor uw IoT-hub

Als u de toegang tot alleen [het persoonlijke eind punt voor uw IOT-hub in uw VNet](virtual-network-support.md)wilt beperken, schakelt u open bare netwerk toegang uit. Gebruik hiervoor Azure Portal of de `publicNetworkAccess` API. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Open bare netwerk toegang via Azure Portal uitschakelen

1. Ga naar [Azure Portal](https://portal.azure.com)
2. Ga naar uw IoT-hub.
3. Selecteer **netwerken** in het menu aan de linkerkant.
4. Selecteer onder ' open bare netwerk toegang toestaan ' de optie **uitgeschakeld**
5. Selecteer **Opslaan**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Afbeelding met Azure Portal waar open bare netwerk toegang wordt uitgeschakeld" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Als u open bare netwerk toegang wilt inschakelen, selecteert u **alle netwerken** en vervolgens **Opslaan**.

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>Toegang tot de IoT Hub na het uitschakelen van open bare netwerk toegang

Nadat open bare netwerk toegang is uitgeschakeld, is de IoT Hub alleen toegankelijk via [het VNet-persoonlijke eind punt met behulp van een persoonlijke Azure-koppeling](virtual-network-support.md). Deze beperking omvat het openen via Azure Portal, omdat API-aanroepen naar de IoT Hub-service direct worden gemaakt met uw referenties via uw browser.

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>IoT Hub-eind punt, IP-adres en poorten na het uitschakelen van de toegang tot het open bare netwerk

IoT Hub is een PaaS (platform-as-a-Service) met meerdere tenants, zodat verschillende klanten dezelfde pool van compute-, netwerk-en opslag hardwarebronnen delen. De hostnamen van IoT Hub worden toegewezen aan een openbaar eind punt met een openbaar routeerbaar IP-adres via internet. Verschillende klanten delen dit IoT Hub open bare eind punt en IoT-apparaten in meer dan Wide Area Networks en on-premises netwerken hebben toegang tot het netwerk. 

Het uitschakelen van open bare netwerk toegang wordt afgedwongen voor een specifieke IoT hub-resource, waardoor isolatie wordt gegarandeerd. Om ervoor te zorgen dat de service actief is voor andere klant bronnen met behulp van het open bare pad, blijft het open bare eind punt oplosbaar, worden IP-adressen detecteerbaar en blijven de poorten geopend. Dit is geen oorzaak van bezorgdheid omdat micro soft meerdere beveiligings lagen integreert om ervoor te zorgen dat tenants volledig worden geïsoleerd. Zie [isolatie in de open bare Azure-Cloud](../security/fundamentals/isolation-choices.md#tenant-level-isolation)voor meer informatie.

## <a name="ip-filter"></a>IP-filter 

Als open bare netwerk toegang is uitgeschakeld, worden alle [IP-filter](iot-hub-ip-filtering.md) regels genegeerd. Dit komt doordat alle IP-adressen van het open bare Internet worden geblokkeerd. Als u IP-filter wilt gebruiken, gebruikt u de optie **geselecteerde IP-bereiken** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Fout oplossing met ingebouwd eind punt dat compatibel is met Event hub

Er is een bug met IoT Hub waarbij het [ingebouwde Event hub-compatibele eind punt](iot-hub-devguide-messages-read-builtin.md) toegankelijk blijft via het open bare Internet wanneer open bare netwerk toegang tot de IOT hub is uitgeschakeld. Zie toegang [tot het ingebouwde Event hub-eind punt uitschakelen IOT hub voor](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)meer informatie en neem contact met ons op over deze fout.
