---
title: Defender-IoT-micro agent voor Azure RTO'S-ingebouwde & aanpas bare waarschuwingen en aanbevelingen
description: Meer informatie over beveiligings waarschuwingen en aanbevolen herstel met behulp van de Azure IoT Defender-IoT-micro-agent-RTO'S.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: cfbd411617a0b80f4857e08f9803b34b80b873d4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784676"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Defender-IoT-micro agent voor Azure RTO'S-beveiligings waarschuwingen en aanbevelingen (preview-versie)

Met Defender-IoT-micro agent voor Azure RTO'S wordt uw IoT-oplossing voortdurend geanalyseerd met behulp van geavanceerde analyse en bedreigings informatie om u te waarschuwen voor mogelijke schadelijke activiteiten en verdachte systeem aanpassingen. U kunt ook aangepaste waarschuwingen maken op basis van uw kennis van het verwachte apparaat gedrag en basis lijnen.

Een Defender-IoT-micro-agent voor Azure RTO'S-waarschuwing fungeert als een indicatie van mogelijke inbreuk en moet worden onderzocht en opgelost. Een Defender-IoT-micro agent for Azure RTO'S-aanbeveling duidt op zwakke beveiligings postuur die moeten worden doorgevoerd en bijgewerkt. 

In dit artikel vindt u een lijst met ingebouwde waarschuwingen en aanbevelingen die worden geactiveerd op basis van de standaardbereiken en die u kunt aanpassen met uw eigen waarden, op basis van het verwachte of basislijn gedrag. 

Zie [aanpas bare waarschuwingen](concept-customizable-security-alerts.md)voor meer informatie over de werking van het aanpassen van waarschuwingen in de Defender voor IOT-service. De specifieke waarschuwingen en aanbevelingen die beschikbaar zijn voor aanpassing wanneer u de Defender-IoT-micro agent voor Azure RTO'S gebruikt, worden in de volgende tabellen beschreven. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Defender-IoT-micro agent voor Azure RTO'S ondersteunde beveiligings waarschuwingen

### <a name="device-related-security-alerts"></a>Beveiligings waarschuwingen met betrekking tot apparaten

|Activiteit van beveiligings waarschuwingen met betrekking tot apparaten  |Naam waarschuwing  |
|---------|---------|
|IP-adres| Communicatie met een verdacht IP-adres gedetecteerd|
|Vinger afdruk van het 509-apparaat|X. 509 apparaat certificaat vingerafdruk komt niet overeen|
|X. 509-certificaat| X. 509-certificaat is verlopen|
|SAS-token| Verlopen SAS-token|
|SAS-token| Ongeldige SAS-token handtekening|

### <a name="iot-hub-related-security-alerts"></a>Beveiligings waarschuwingen met betrekking tot IoT Hub

|Activiteit beveiligings waarschuwing IoT Hub  |Naam waarschuwing  |
|---------|---------|
|Een certificaat toevoegen    |  Er is een niet-geslaagde poging tot het toevoegen van een certificaat aan een IoT Hub gedetecteerd       |
|Een diagnostische instelling toevoegen of bewerken    | Er is een poging tot het toevoegen of bewerken van een diagnostische instelling van een IoT Hub gedetecteerd      |
|Een certificaat verwijderen    |  Er is een niet-geslaagde poging tot het verwijderen van een certificaat uit een IoT Hub gedetecteerd       |
|Een diagnostische instelling verwijderen    |  Gedetecteerde poging om een diagnostische instelling te verwijderen uit een IoT Hub      |
|Verwijderd certificaat    | Gedetecteerde verwijdering van een certificaat van een IoT Hub        |
|Nieuw certificaat     |  Het toevoegen van een nieuw certificaat aan een IoT Hub is gedetecteerd       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Defender-IoT-micro agent voor Azure RTO'S ondersteunde aanpas bare waarschuwingen

### <a name="device-related-customizable-alerts"></a>Aan het apparaat gerelateerde aanpas bare waarschuwingen

|Aan apparaat gerelateerde activiteit |Naam waarschuwing  |
|---------|---------|
|Actieve verbindingen|Aantal actieve verbindingen bevindt zich niet in het toegestane bereik|
|Cloud-naar-apparaat-berichten in het **MQTT** -Protocol|Het aantal berichten van de Cloud naar het apparaat in het **MQTT** -protocol bevindt zich niet in het toegestane bereik|
|Uitgaande verbinding| Uitgaande verbinding met een IP-adres dat niet is toegestaan|

### <a name="hub-related-customizable-alerts"></a>Aan hub gerelateerde aanpas bare waarschuwingen 

|Aan hub gerelateerde activiteit  |Naam waarschuwing  |
|---------|---------|
|Opschoningen van opdrachten wachtrij     |  Aantal opschoningen van de opdracht wachtrij buiten het toegestane bereik       |
|Cloud-naar-apparaat-berichten in het **MQTT** -Protocol    |  Aantal Cloud-naar-apparaat-berichten in het **MQTT** -protocol buiten het toegestane bereik       |
|Apparaat-naar-Cloud berichten in het **MQTT** -Protocol    | Aantal van het apparaat naar Cloud berichten in het **MQTT** -protocol buiten het toegestane bereik        |
|Directe methode aanroepen     |  Aantal directe methoden dat buiten het toegestane bereik valt       |
|Geweigerde berichten van de Cloud naar het apparaat in het **MQTT** -Protocol     |   Het aantal geweigerde Cloud-naar-apparaat-berichten in het **MQTT** -protocol buiten het toegestane bereik      |
|Updates voor dubbele modules     |  Aantal updates voor dubbele modules buiten het toegestane bereik       |
|Niet-geautoriseerde bewerkingen    |  Aantal niet-geautoriseerde bewerkingen buiten het toegestane bereik       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Defender-IoT-micro agent voor door Azure RTO'S ondersteunde aanbevelingen

### <a name="device-related-recommendations"></a>Aanbevelingen met betrekking tot apparaten

|Apparaat-gerelateerde activiteit  |Naam aanbeveling |
|---------|---------|
|Verificatie referenties    |  Identieke verificatie referenties die worden gebruikt door meerdere apparaten       |

### <a name="hub-related-recommendations"></a>Aan de hub gerelateerde aanbevelingen

|IoT Hub activiteit  |Naam aanbeveling |
|---------|---------|
|IP-filter beleid   |  Het standaard IP-filter beleid moet worden ingesteld op **weigeren**  |
|IP-filter regel| De IP-filter regel bevat een groot IP-bereik|
|Logboeken met diagnostische gegevens|Suggestie voor het inschakelen van Diagnostische logboeken in IoT Hub|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>Alle Defender voor IoT-waarschuwingen en-aanbevelingen

Zie voor een volledige lijst met alle waarschuwingen en aanbevelingen voor de service Defender voor IoT- [beveiligings waarschuwingen](concept-security-alerts.md)en [aanbevelingen](concept-recommendations.md)voor IOT-beveiliging.

## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids: Defender-IoT-micro-agent voor Azure RTO'S](quickstart-azure-rtos-security-module.md)
- [Defender-IoT-micro agent voor Azure RTO'S configureren en aanpassen](how-to-azure-rtos-security-module.md)
- Raadpleeg de [Defender-IOT-micro-agent voor Azure rto's-API](azure-rtos-security-module-api.md)