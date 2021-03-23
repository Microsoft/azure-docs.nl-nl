---
title: Defender-IoT-micro agent voor Azure RTO'S configureren en aanpassen
description: Meer informatie over het configureren en aanpassen van uw Defender-IoT-micro agent voor Azure RTO'S.
ms.topic: how-to
ms.date: 03/07/2021
ms.openlocfilehash: afab823b6bb187c9a7b7529f52efc37b20e8c66f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778981"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-micro agent voor Azure RTO'S (preview) configureren en aanpassen

In dit artikel wordt beschreven hoe u de Defender-IoT-micro-agent voor uw Azure RTO'S-apparaat kunt configureren om te voldoen aan uw netwerk, band breedte en geheugen vereisten.

U moet een doel distributiebestand met een `*.dist` extensie uit de `netxduo/addons/azure_iot/azure_iot_security_module/configs` Directory selecteren.  

Wanneer u een CMake-compilatie omgeving gebruikt, moet u een opdracht regel parameter `IOT_SECURITY_MODULE_DIST_TARGET` voor de gekozen waarde instellen. Bijvoorbeeld `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

In een IAR of een andere niet-CMake-compilatie omgeving moet u het `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` pad toevoegen aan alle bekende opgenomen paden. Bijvoorbeeld `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Gebruik het volgende bestand om het gedrag van uw apparaat te configureren.

**netxduo/addons/azure_iot/azure_iot_security_module/Inc/configs/ \<target distribution> /asc_config. h**

In een CMake-compilatie omgeving moet u de standaard configuratie wijzigen door het bestand te bewerken `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` . Gebruik de volgende CMake `set(ASC_XXX ON)` -indeling of het volgende bestand `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` voor alle andere omgevingen. Bijvoorbeeld `#define ASC_XXX`.

Het standaard gedrag van elke configuratie vindt u in de volgende tabellen: 

## <a name="general"></a>Algemeen

| Naam | Type | Standaard | Details |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Tekenreeks | Defender-IOT-micro agent | De unieke id van het apparaat.  |
| SECURITY_MODULE_VERSION_ (PRIMAIR) (KLEIN) (PATCH)  | Aantal | 3.2.1 | De versie. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Aantal  | 3 | De tijd die de Defender-IoT-micro agent nodig heeft om het beveiligings bericht na een fout te verzenden. (in seconden) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Aantal | 300 | De Defender-IoT-micro-agent wacht tijd (in seconden). Als de tijd wordt overschreden, wordt de status gewijzigd in onderbroken. |

## <a name="collection"></a>Verzameling

| Naam | Type | Standaard | Details |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Aantal  | 30  | De offset van het interval voor de opstart verzameling van de collector. Tijdens het opstarten wordt de waarde toegevoegd aan de verzameling van het systeem om te voor komen dat berichten vanaf meerdere apparaten tegelijkertijd worden verzonden.  |
| ASC_HIGH_PRIORITY_INTERVAL | Aantal | 10 | De groeps interval met hoge prioriteit (in seconden) van de collector. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Aantal | 30 | Het interval voor de gemiddelde prioriteits groep van de Collector (in seconden). |
| ASC_LOW_PRIORITY_INTERVAL | Aantal | 145.440  | Het interval voor de groep met lage prioriteit (in seconden) van de collector. |

#### <a name="collector-network-activity"></a>Activiteit netwerk verzamelaar

Als u de configuratie van de Collector netwerk activiteit wilt aanpassen, gebruikt u het volgende:

| Naam | Type | Standaard | Details |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean-waarde | onjuist | Hiermee wordt de netwerk activiteit gefilterd `TCP` . |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean-waarde | onjuist | Hiermee worden de gebeurtenissen van de netwerk activiteit gefilterd `UDP` . |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean-waarde | onjuist | Hiermee worden de gebeurtenissen van de netwerk activiteit gefilterd `ICMP` . |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Booleaans | true | Hiermee worden alleen de unicast-binnenkomende pakketten vastgelegd. Als deze eigenschap is ingesteld op False, worden ook broadcasts en multi cast vastgelegd. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boolean-waarde  | onjuist  | Hiermee verzendt u een lege gebeurtenissen van de collector. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Aantal | 64 | Het maximum aantal IPv4-netwerk gebeurtenissen dat in het geheugen moet worden opgeslagen. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Aantal | 64  | Het maximum aantal IPv6-netwerk gebeurtenissen dat in het geheugen moet worden opgeslagen. |

### <a name="collectors"></a>Verzamelaars
| Naam | Type | Standaard | Details |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Booleaans | AAN | Hiermee schakelt u de heartbeat-Collector in. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Booleaans | AAN | Hiermee wordt de netwerk activiteit verzamelaar ingeschakeld. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Booleaans | AAN | Hiermee wordt de systeem informatie verzamelaar ingeschakeld.  |

Andere configuratie vlaggen zijn Geavanceerd en bevatten niet-ondersteunde functies. Neem contact op met de ondersteuning om dit te wijzigen of voor meer informatie.
 
## <a name="supported-security-alerts-and-recommendations"></a>Ondersteunde beveiligings waarschuwingen en aanbevelingen

De Defender-IoT-micro-agent voor Azure RTO'S ondersteunt specifieke beveiligings waarschuwingen en aanbevelingen. Zorg ervoor dat u [de relevante waarschuwings-en aanbevelings waarden voor uw service bekijkt en wijzigt](concept-rtos-security-alerts-recommendations.md) .

## <a name="log-analytics-optional"></a>Log Analytics (optioneel)

U kunt Log Analytics inschakelen en configureren om de gebeurtenissen en activiteiten van het apparaat te onderzoeken. Lees meer informatie over het instellen en gebruik [van log Analytics met de Defender voor IOT-service](how-to-security-data-access.md#log-analytics) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen


- Defender-IoT-micro agent voor Azure RTO'S- [beveiligings waarschuwingen en-aanbevelingen](concept-rtos-security-alerts-recommendations.md) controleren en aanpassen
- Raadpleeg indien nodig de [Defender-IOT-micro agent voor Azure rto's-API](azure-rtos-security-module-api.md) .
