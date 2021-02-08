---
title: Azure Defender voor IoT-oplossing op basis van een agent configureren
description: Meer informatie over het configureren van gegevens verzameling in azure Defender voor een IoT-oplossing op basis van een agent
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 53fc01839ef522afaffe52cd8a3126e40ba94a05
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809758"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>Azure Defender voor IoT-oplossing op basis van een agent configureren  

In dit artikel wordt beschreven hoe u gegevens verzameling configureert in azure Defender voor een IoT-oplossing op basis van een agent.

## <a name="configure-data-collection"></a>Gegevensverzameling configureren

Gegevens verzameling configureren in azure Defender voor IoT-oplossing op basis van een agent: 

1. Ga naar de Azure Portal en selecteer de IoT Hub waaraan de Defender voor IoT is gekoppeld 

1. Selecteer in het menu  **beveiliging** de   optie **instellingen**. 

1. Selecteer **gegevens verzameling**. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="Selecteer gegevens verzameling in de menu instellingen van de beveiliging.":::

## <a name="geolocation-and-ip-address-handling"></a>Verwerking van geolocatie en IP-adres 

Om uw IoT-oplossing te beveiligen, worden de IP-adressen van de binnenkomende en uitgaande verbindingen voor uw IoT-apparaten, IoT Edge en IoT Hub (s) standaard verzameld en opgeslagen. Deze informatie is essentieel en wordt gebruikt om abnormale connectiviteit van verdachte IP-adres bronnen te detecteren. Bijvoorbeeld wanneer er pogingen zijn gedaan om verbinding te maken met een IP-adres bron van een bekend botnet of van een IP-adres bron buiten uw geolocatie. De Defender voor IoT-service biedt de flexibiliteit om de verzameling van de IP-adres gegevens op elk gewenst moment in en uit te scha kelen. 

Om het verzamelen van IP-adres gegevens in of uit te scha kelen: 

1. Open uw IoT Hub en selecteer vervolgens **instellingen**   in het menu **beveiliging**   . 

1. Selecteer het scherm **gegevens verzameling**   en wijzig de instellingen voor GEOLOCATIE en IP-adres verwerking op basis van uw behoeften. 

## <a name="log-analytics-creation"></a>Het maken van logboekanalyses 

Met Defender voor IoT kunt u beveiligings waarschuwingen, aanbevelingen en onbewerkte beveiligings gegevens opslaan in uw Log Analytics-werk ruimte. Log Analytics opname in IoT Hub is standaard ingesteld op **uitgeschakeld** in de Defender voor IOT-oplossing. Het is mogelijk om Defender voor IoT te koppelen aan een log Analytics-werk ruimte en om ook de beveiligings gegevens op te slaan. 

Er zijn twee soorten gegevens die standaard in uw Log Analytics-werk ruimte worden opgeslagen door Defender voor IoT:
 
- Beveiligings waarschuwingen.

- Vereisten. 

U kunt ervoor kiezen om opslag ruimte toe te voegen aan een extra gegevens type `raw events` . 

> [!Note] 
> Bij het opslaan  `raw events`   in log Analytics worden extra opslag kosten in beslag. 

Log Analytics om met micro agent te kunnen werken: 

1. Navigeer naar de verzameling **werkruimte configuratie**  >  **gegevens** en schakel over naar de wissel knop  **in**. 

1. Maak een nieuwe Log Analytics-werk ruimte of koppel een bestaande. 

1. Controleer of de optie **toegang tot onbewerkte beveiligings gegevens**   is geselecteerd.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Zorg ervoor dat de toegang tot onbewerkte beveiligings gegevens is geselecteerd.":::

1. Selecteer **Opslaan**.

Elke maand is de eerste 5 gigabytes aan gegevens die per klant zijn opgenomen in de Azure Log Analytics-service gratis. Elke gigabyte aan gegevens die zijn opgenomen in uw Azure Log Analytics-werk ruimte, wordt gedurende de eerste 31 dagen gratis bewaard. Zie [log Analytics prijzen](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie over prijzen. 

U kunt als volgt de werkruimteconfiguratie van Log Analytics wijzigen: 

1. In uw IoT Hub selecteert u in het menu **beveiliging** de optie  **instellingen**. 

1. Selecteer het ****   scherm voor het verzamelen van gegevens en wijzig de configuratie van de werk ruimte van log Analytics instellingen naar wens. 

Als u na de configuratie toegang wilt krijgen tot uw waarschuwingen in uw Log Analytics-werk ruimte:

1. Selecteer een waarschuwing in Defender voor IoT.

1. Selecteer **waarschuwingen onderzoeken in log Analytics werk ruimte**.

Als u na de configuratie toegang wilt krijgen tot uw waarschuwingen in uw Log Analytics-werk ruimte:

1. Selecteer een aanbeveling in Defender voor IoT.

1. Selecteer **aanbevelingen onderzoeken in log Analytics werk ruimte**. 
 
Zie [aan de slag met query's in log Analytics](../azure-monitor/log-query/get-started-queries.md)voor meer informatie over het opvragen van gegevens uit log Analytics. 

## <a name="turn-off-defender-for-iot"></a>Defender uitschakelen voor IoT 

Een Defender voor IoT-service inschakelen of uitschakelen op een specifieke IoT Hub: 

1. In uw IoT Hub selecteert u in het menu **beveiliging** de optie  **instellingen**.

1. Selecteer het ****   scherm voor het verzamelen van gegevens en wijzig de configuratie van de werk ruimte van log Analytics instellingen naar wens.

## <a name="next-steps"></a>Volgende stappen 

Ga naar het volgende artikel om [uw oplossing te configureren](quickstart-configure-your-solution.md). 
