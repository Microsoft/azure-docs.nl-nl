---
title: Implementatieopties
description: Aan de slag met uitleg over de basis werk stroom van Defender voor IoT-functies en-services.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 6aa525fd7f2d82194baa2e2a0c910cb71509c2d5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340013"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Aan de slag met Azure Defender voor IoT

In dit artikel worden de implementatie-en voorbereidings processen beschreven die nodig zijn om Azure Defender voor IoT uit te voeren. Er zijn ook aanvullende stappen vereist. U wordt aangeraden deze stappen te begrijpen en vertrouwd te raken met informatie in begeleide documenten.

Zodra u alle stappen hebt voltooid, bewaakt Azure Defender voor IoT Sens oren uw netwerk. Afhankelijk van hoe u uw oplossing hebt ingesteld, kunnen detecties ook worden verzonden naar de on-premises beheer console of naar de IoT Hub.

Voer de volgende stappen uit om Azure Defender voor IoT actief en werkend te krijgen.

## <a name="1-set-up-azure"></a>1. Azure instellen

- Stel een Azure-account in. Zie [een Azure-account maken](/learn/modules/create-an-azure-account/)voor meer informatie.

- Firewall of proxy: als u een firewall of soortgelijk netwerk apparaat hebt dat is geconfigureerd om specifieke verbindingen toe te staan, controleert u of *. azure-devices.net:443 wordt geopend voor de firewall of proxy. Als joker tekens niet worden ondersteund of als u meer controle wilt, moet u de specifieke IoT Hub FQDN openen in uw FW of proxy. Zie [referentie-IOT hub-eind punten](../iot-hub/iot-hub-devguide-endpoints.md)voor meer informatie.

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. Implementeer hardware, software en onboarding naar sensor

- Hardware aanschaffen en software installeren. Volg de stappen die hier worden beschreven en zie dit artikel en de [hand leiding voor Defender voor IOT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) en de [installatie handleiding](https://aka.ms/AzureDefenderforIoTInstallSensorISO)voor meer informatie.

  - Nadat u de sensor hebt geïnstalleerd, moet u de referenties voor de sensor aanmelden veilig vastleggen. U hebt de referenties nodig voor het uploaden van het activerings bestand naar de sensor.

  - Als u werkt met Sens oren die lokaal worden beheerd, moet u het IP-adres van de sensor of de sensor naam die in de installatie is gedefinieerd, veilig vastleggen. U kunt deze gebruiken bij het maken van een sensor naam tijdens de sensor registratie in de Defender voor IoT-Portal. U kunt deze later gebruiken om gemakkelijker te kunnen bijhouden en een consistente naam te geven tussen de registratie naam in de Azure Defender voor IoT-Portal en het IP-adres van de geïmplementeerde sensor die wordt weer gegeven in de sensor console.

- Registreer de sensor met de Defender voor IoT-Portal en down load een sensor activerings bestand.

- Upload het activerings bestand naar uw sensor.

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. Voer netwerk installatie uit voor sensor bewaking en-beheer

- Verbind uw sensor met het netwerk. Beschreven in de [hand leiding voor netwerk installatie](https://aka.ms/AzureDefenderForIoTNetworkSetup).

## <a name="4-start-discovering-your-network"></a>4. start met het detecteren van uw netwerk

- Tweak systeem instellingen in de sensor console.

- Sens oren verbinden met een on-premises beheer console.

Zie de gebruikers handleiding voor [Azure Defender voor IOT-sensor](https://aka.ms/AzureDefenderforIoTUserGuide) en de [Gebruikers handleiding voor de on-premises beheer console van Defender voor IOT](https://aka.ms/DefenderForIoTManagementConsole)voor meer informatie.

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. Azure Sentinel vullen met waarschuwings gegevens

- Als u waarschuwings gegevens wilt verzenden naar Azure Sentinel, configureert u Azure Sentinel: [Verbind uw gegevens van Defender voor IOT naar Azure Sentinel](how-to-configure-with-sentinel.md).
 

## <a name="next-steps"></a>Volgende stappen

- [Defender inschakelen voor IOT](quickstart-onboard-iot-hub.md)
- Uw [oplossing](quickstart-configure-your-solution.md) configureren
- [Beveiligingsmodules maken](quickstart-create-security-twin.md)
- [Aangepaste waarschuwingen](quickstart-create-custom-alerts.md) configureren
- [Een beveiligingsagent implementeren](how-to-deploy-agent.md)