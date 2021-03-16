---
title: Aanbevelingen op basis van een agent
titleSuffix: Azure Defender for IoT
description: Meer informatie over het concept van beveiligings aanbevelingen en hoe ze worden gebruikt voor Defender voor IoT-apparaten.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: e746f37fdf3b67467c1844ebea9191679d52d6d1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491262"
---
# <a name="security-recommendations-for-iot-devices"></a>Beveiligings aanbevelingen voor IoT-apparaten

Met Defender voor IoT worden uw Azure-resources en IoT-apparaten gescand en worden beveiligings aanbevelingen gegeven om uw kwets baarheid te beperken.
Beveiligings aanbevelingen zijn maat regelen en zijn bedoeld om klanten te helpen bij het naleven van de best practices voor beveiliging.

In dit artikel vindt u een lijst met aanbevelingen die kunnen worden geactiveerd op uw IoT-apparaten.

## <a name="agent-based-recommendations"></a>Aanbevelingen op basis van een agent

Aanbevelingen voor apparaten bieden inzichten en suggesties voor het verbeteren van de beveiliging van postuur.

| Severity | Naam | Gegevensbron | Beschrijving |
|--|--|--|--|
| Normaal | Poorten op het apparaat openen | Klassieke Defender-IoT-micro-agent| Er is een luisterend eind punt gevonden op het apparaat. |
| Normaal | Permissief firewall beleid gevonden in een van de ketens. | Klassieke Defender-IoT-micro-agent| Toegestaan firewall beleid gevonden (invoer/uitvoer). Het firewall beleid moet standaard al het verkeer weigeren en regels definiëren om de benodigde communicatie met/van het apparaat mogelijk te maken. |
| Normaal | Er is een strikte firewall regel in de invoer keten gevonden | Klassieke Defender-IoT-micro-agent| Er is een regel in de firewall gevonden die een soepel patroon bevat voor een breed scala aan IP-adressen of poorten. |
| Normaal | Er is een strikte firewall regel in de uitvoer keten gevonden | Klassieke Defender-IoT-micro-agent| Er is een regel in de firewall gevonden die een soepel patroon bevat voor een breed scala aan IP-adressen of poorten. |
| Normaal | Validatie van de basislijn planning van het besturings systeem is mislukt | Klassieke Defender-IoT-micro-agent| Het apparaat voldoet niet aan de [CIS Linux-Benchmarks](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Aanbevelingen op basis van agents

Operationele aanbevelingen bieden inzichten en suggesties voor het verbeteren van de configuratie van de beveiligings agent.

| Severity | Naam | Gegevensbron | Beschrijving |
|--|--|--|--|
| Beperkt | Agent verzendt ongebruikte berichten | Klassieke Defender-IoT-micro-agent| 10% of meer beveiligings berichten zijn in de afgelopen 24 uur kleiner dan 4 KB. |
| Beperkt | Dubbele configuratie voor beveiliging niet optimaal | Klassieke Defender-IoT-micro-agent| De dubbele configuratie van beveiliging is niet optimaal. |
| Beperkt | Conflict tussen beveiligings configuratie | Klassieke Defender-IoT-micro-agent| Er zijn conflicten gevonden in de beveiligings-dubbele configuratie. |  |

## <a name="next-steps"></a>Volgende stappen

- [Overzicht](overview.md) van Defender voor IOT-service
- Meer informatie over [het verkrijgen van toegang tot uw beveiligings gegevens](how-to-security-data-access.md)
- Meer informatie over [het onderzoeken van een apparaat](how-to-investigate-device.md)
