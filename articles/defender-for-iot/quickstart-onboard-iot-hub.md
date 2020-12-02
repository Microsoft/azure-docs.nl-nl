---
title: 'Quickstart: De service inschakelen'
description: Ontdek hoe u de Defender for IoT-beveiligingsservice in uw Azure IoT-hub inschakelt.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 786fcd1a0c6d7df2c38a086a830a63f7179d7d40
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352504"
---
# <a name="quickstart-onboard-azure-defender-for-iot-service-in-iot-hub"></a>Quickstart: De Azure Defender for IoT-service onboarden in IoT Hub

In dit artikel wordt uitgelegd hoe u de Defender for IoT-service in uw bestaande IoT-hub inschakelt. Als u momenteel niet over een Azure IoT-hub beschikt, raadpleegt u [Een Azure IoT-hub maken met Azure Portal](../iot-hub/iot-hub-create-through-portal.md) om aan de slag te gaan.

> [!NOTE]
> Defender for IoT biedt momenteel alleen ondersteuning voor IoT-hubs van de Standard-laag.

## <a name="prerequisites-for-enabling-the-service"></a>Vereisten voor het inschakelen van de service

- Log Analytics-werkruimte
  - Er worden door Defender Center for IoT standaard twee soorten informatie opgeslagen in uw Log Analytics-werkruimte. **beveiligingswaarschuwingen** en **aanbevelingen**.
  - U kunt ervoor kiezen om opslagruimte toe te voegen voor een extra gegevenstype **onbewerkte gebeurtenissen**. Houd er rekening mee dat bij het opslaan van **onbewerkte gebeurtenissen** in Log Analytics extra opslagkosten worden berekend.
- Azure IoT Hub (Standard-laag)
- Bekijk alle [servicevereisten](service-prerequisites.md)

## <a name="enable-defender-for-iot-on-your-iot-hub"></a>Defender for IoT inschakelen op uw IoT Hub

U kunt als volgt de beveiliging in uw Azure IoT-hub inschakelen:

1. Open uw **Azure IoT-hub** in Azure Portal.
1. Klik onder het menu **Beveiliging** op **Uw IoT-oplossing beveiligen**.

Gefeliciteerd. U hebt nu Defender for IoT ingeschakeld in uw Azure IoT-hub.

### <a name="geolocation-and-ip-address-handling"></a>Verwerking van geolocatie en IP-adres

Als u uw IoT-oplossing wilt beveiligen, worden de IP-adressen van binnenkomende en uitgaande verbindingen van en naar uw IoT-apparaten, IoT Edge en Azure IoT-hub(s) standaard verzameld en opgeslagen. Deze informatie is essentieel voor het detecteren van abnormale verbindingen vanuit verdachte IP-bronnen. Wanneer er bijvoorbeeld wordt geprobeerd om verbindingen tot stand te brengen met een IP-bron van een bekend botnet of van een IP-bron buiten uw geolocatie. Defender for IoT-service biedt de flexibiliteit om het verzamelen van IP-adresgegevens op elk gewenst moment in en uit te schakelen.

U kunt als volgt het verzamelen van IP-adresgegevens in- of uitschakelen:

1. Open uw Azure IoT-hub en selecteer vervolgens **Instellingen** in het menu **Beveiliging**.
1. Kies het scherm **Gegevensverzameling** en wijzig naar wens de instellingen voor de verwerking van geolocatie en/of IP-adressen.

### <a name="log-analytics-creation"></a>Het maken van logboekanalyses

Wanneer Defender for IoT is ingeschakeld, wordt er een standaard Azure Log Analytics-werkruimte gemaakt om onbewerkte beveiligingsgebeurtenissen, waarschuwingen en aanbevelingen voor uw IoT-apparaten, IoT Edge en Azure IoT Hub op te slaan. Elke maand zijn de eerste vijf (5) GB aan gegevens die per klant worden opgenomen in de Azure Log Analytics-service gratis. Elke GB aan gegevens die wordt opgenomen in uw Azure Log Analytics-werkruimte, wordt gedurende de eerste 31 dagen gratis bewaard. Meer informatie over [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/)-prijzen.

U kunt als volgt de werkruimteconfiguratie van Log Analytics wijzigen:

1. Open uw Azure IoT-hub en selecteer vervolgens **Instellingen** in het menu **Beveiliging**.
1. Kies het scherm **Gegevensverzameling** en wijzig naar wens de werkruimteconfiguratie van Log Analytics-instellingen.

### <a name="customize-your-iot-security-solution"></a>Uw IoT-beveiligingsoplossing aanpassen

Als u de Defender for IoT-oplossing inschakelt, worden standaard alle Azure IoT-hubs in uw Azure-abonnement automatisch beveiligd.

U kunt als volgt de Defender for IoT-service voor een specifieke Azure IoT-hub in- of uitschakelen:

1. Open uw Azure IoT-hub en selecteer vervolgens **Instellingen** in het menu **Beveiliging**.
1. Kies het scherm **Gegevensverzameling** en wijzig naar wens de beveiligingsinstellingen van Azure IoT-hubs in uw Azure-abonnement.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om uw oplossing te configureren...

> [!div class="nextstepaction"]
> [De oplossing configureren](quickstart-configure-your-solution.md)