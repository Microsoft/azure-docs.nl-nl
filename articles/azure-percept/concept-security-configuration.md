---
title: Azure percept firewall-configuratie en beveiligings aanbevelingen
description: Meer informatie over de configuratie van Azure percept firewall en beveiligings aanbevelingen
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: cfc20a30104e24a3950c71bdd8377544803d2f25
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604410"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Azure percept firewall-configuratie en beveiligings aanbevelingen

Raadpleeg de onderstaande richt lijnen voor meer informatie over het configureren van firewalls en best practices voor algemene beveiliging met Azure percept.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Firewalls configureren voor Azure percept DK

Als uw netwerk installatie vereist dat u verbindingen die zijn gemaakt met Azure percept DK-apparaten expliciet toestaat, raadpleegt u de volgende lijst met onderdelen.

Deze controle lijst is een start punt voor firewall regels:

|URL (* = Joker teken)|Uitgaande TCP-poorten|Gebruik|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|443|SOM van Azure DK-verificatie en-autorisatie|
|*. auth.projectsantacruz.azure.net|443|SOM van Azure DK-verificatie en-autorisatie|

Bekijk ook de lijst met [verbindingen die door Azure IOT Edge worden gebruikt](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Aanvullende aanbevelingen voor implementatie naar productie

Azure percept DK biedt een groot aantal beveiligings mogelijkheden uit het kader. Naast de krachtige beveiligings functies die deel uitmaken van de huidige versie, raadt micro soft ook de volgende richt lijnen aan bij het overwegen van productie-implementaties:

- Sterke fysieke beveiliging van het apparaat zelf
- Zorg ervoor dat versleuteling van gegevens op rest is ingeschakeld
- De postuur van het apparaat continu controleren en snel reageren op waarschuwingen
- Het aantal beheerders beperken dat toegang heeft tot het apparaat

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure percept-beveiliging](./overview-percept-security.md)