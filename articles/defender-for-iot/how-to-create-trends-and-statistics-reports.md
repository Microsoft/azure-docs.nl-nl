---
title: Rapporten met trends en statistieken genereren
description: Krijg inzicht in netwerk activiteiten, statistieken en trends door gebruik te maken van Defender voor IoT trends en statistieken widgets.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: c28e2f1c24d39ceb915be9f4f6f222d70de9ee73
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522220"
---
# <a name="sensor-trends-and-statistics-reports"></a>Sensor trends en statistieken rapporten

## <a name="about-sensor-trends-and-statistics-reports"></a>Over sensor trends en statistieken rapporten

U kunt widget grafieken en cirkel diagrammen maken om inzicht te krijgen in netwerk trends en-statistieken. Widgets kunnen worden gegroepeerd onder door de gebruiker gedefinieerde Dash boards.

> [!NOTE]
> Beheerders-en beveiligings analisten kunnen trends en statistieken rapporten maken.

Het dash board bestaat uit widgets die de volgende typen informatie grafisch beschrijven:

- Verkeer per poort
- Hoofd verkeer per poort
- Kanaal bandbreedte
- Totale band breedte
- Actieve TCP-verbinding
- Bovenste band breedte per VLAN
- Apparaten:
  - Nieuwe apparaten
  - Apparaten bezet
  - Apparaten op leverancier
  - Apparaten op besturings systeem
  - Aantal apparaten per VLAN
  - Niet-verbonden apparaten
- Connectiviteit valt per uur
- Waarschuwingen voor incidenten per type
- Database tabel toegang
- Widgets van protocol sectie
- DELTAV
  - DeltaV Roc-verwerkings distributie
  - DeltaV Roc gebeurtenissen op naam
  - DeltaV gebeurtenissen op tijd
- AMS
  - AMS verkeer per server poort
  - AMS-verkeer via opdracht
- Ethernet-en IP-adres:
  - Ethernet-en IP-adres verkeer door overschrijvings service
  - Ethernet-en IP-adres verkeer door overschrijvings klasse
  - Ethernet-en IP-adres verkeer via opdracht
- OPC
  - OPC best beheer bewerkingen
  - OPC, bovenste I/O-bewerkingen
- Siemens S7:
  - S7 verkeer per besturings functie
  - S7 verkeer per subfunctie
- VLAN
  - Aantal apparaten per VLAN
  - Bovenste band breedte per VLAN
- 60870-5-104
  - IEC-60870-verkeer door ASDU
- BACNET
  - BACnet Services
- DNP3
  - DNP3-verkeer per functie
- SRTP:
  - SRTP verkeer per service code
  - SRTP-fouten per dag
- SuiteLink:
  - SuiteLink populairste labels
  - Gedrag van SuiteLink numerieke code
- IEC-60870-verkeer door ASDU
- DNP3-verkeer per functie
- MMS-verkeer per service
- Modbus-verkeer per functie
- OPC-UA-verkeer per service

> [!NOTE]
>  De tijd in de widgets wordt ingesteld op basis van de sensor tijd.

## <a name="create-reports"></a>Rapporten maken

Dash boards en widgets bekijken:

Selecteer **Trends & statistieken** in het menu aan de zijkant.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Scherm opname van een onderzoek.":::

Standaard worden resultaten weer gegeven voor detecties in de afgelopen 7 dagen. U kunt filter hulpprogramma's gebruiken om dit bereik te wijzigen. Bijvoorbeeld een zoek opdracht in de vrije tekst.

## <a name="next-steps"></a>Volgende stappen

Rapportage van risico [beoordeling](how-to-create-risk-assessment-reports.md) 
 [Sensor gegevens analyse query's](how-to-create-data-mining-queries.md) 
 [Aanvals vector rapportage](how-to-create-attack-vector-reports.md)