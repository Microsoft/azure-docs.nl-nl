---
title: Lokale configuratie van beveiligings agent (C)
description: Meer informatie over Defender voor lokale configuraties van agents voor C.
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: dc4ea30080711633e52f966b89a16f7246a30673
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779406"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Meer informatie over het bestand LocalConfiguration.json - C-agent

De Defender voor IoT-beveiligings agent gebruikt configuraties uit een lokaal configuratie bestand.
De beveiligings agent leest de configuratie eenmaal tijdens het opstarten van de agent.
De configuratie die in het lokale configuratie bestand is gevonden, bevat verificatie configuratie en andere aan de agent gerelateerde configuraties.
Het bestand bevat configuraties in ' sleutel-waarde ' paren in de JSON-notatie en de configuraties worden gevuld wanneer de agent wordt geïnstalleerd.

Het bestand bevindt zich standaard op de volgende locatie:/var/ASCIoTAgent/LocalConfiguration.js

Wijzigingen in het configuratie bestand worden uitgevoerd wanneer de agent opnieuw wordt gestart.

## <a name="security-agent-configurations-for-c"></a>Beveiligings agent configuraties voor C

| Configuratie naam | Mogelijke waarden | Details |
|:-----------|:---------------|:--------|
| AgentId | GUID | De unieke id van de agent |
| TriggerdEventsInterval | ISO8601 teken reeks | Scheduler-interval voor de verzameling geactiveerde gebeurtenissen |
| ConnectionTimeout | ISO8601 teken reeks | Er is een time-out opgetreden voor de verbinding met IoThub |
| Verificatie | JsonObject | Verificatie configuratie. Dit object bevat alle informatie die nodig is voor verificatie op basis van IoTHub |
| Identiteit | ' DPS ', ' SecurityModule ', ' apparaat ' | Verificatie-identiteit: DPS als verificatie wordt uitgevoerd via DPS, SecurityModule als verificatie wordt uitgevoerd via een Defender-IoT-micro-agentcredentials of-apparaat als verificatie wordt uitgevoerd met de referenties van het apparaat |
| Authentic | "SasToken", "SelfSignedCertificate" | het gebruikers geheim voor authenticatie: Kies SasToken als het geheim gebruiken een symmetrische sleutel is, kies het zelfondertekende certificaat als het geheim een zelfondertekend certificaat is  |
| Bestandspad | Pad naar bestand (teken reeks) | Pad naar het bestand dat het authenticatie geheim bevat |
| HostName | tekenreeks | De hostnaam van de Azure IoT hub. doorgaans <mijn hub>. azure-devices.net |
| DeviceId | tekenreeks | De ID van het apparaat (zoals geregistreerd in azure IoT Hub) |
| DPS | JsonObject | Configuraties met betrekking tot DPS |
| IDScope | tekenreeks | ID-bereik van DPS |
| Registratie | tekenreeks  | Registratie-ID van DPS-apparaat |
| Logboekregistratie | JsonObject | Configuraties van gerelateerde agent registratie |
| SystemLoggerMinimumSeverity | 0 <= getal <= 4 | logboek berichten die gelijk zijn aan deze Ernst, worden geregistreerd in/var/log/syslog (0 is de laagste Ernst) |
| DiagnosticEventMinimumSeverity | 0 <= getal <= 4 | logboek berichten die gelijk zijn aan deze Ernst, worden als diagnostische gebeurtenissen verzonden (0 is de laagste Ernst) |

## <a name="security-agent-configurations-code-example"></a>Voor beeld van configuratie code voor beveiligings agent

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van de Defender voor IOT-service
- Meer informatie over de [architectuur](architecture.md) van Defender voor IOT
- De Defender voor IoT- [service](quickstart-onboard-iot-hub.md) inschakelen
- Lees de [Veelgestelde vragen over](resources-frequently-asked-questions.md) Defender voor IOT-service
- Meer informatie over het openen van [onbewerkte beveiligings gegevens](how-to-security-data-access.md)
- Meer informatie over [aanbevelingen](concept-recommendations.md)
- Beveiligings [waarschuwingen](concept-security-alerts.md) begrijpen
