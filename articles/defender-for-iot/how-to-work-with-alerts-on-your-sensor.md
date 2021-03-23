---
title: Over sensor waarschuwingen
description: Werk met waarschuwingen om u te helpen bij het verbeteren van de beveiliging en werking van uw netwerk.
ms.date: 11/30/2020
ms.topic: how-to
ms.openlocfilehash: 178d3aedb44c29f53aab481894defeb4b9355d39
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781344"
---
# <a name="about-sensor-alerts"></a>Over sensor waarschuwingen

Waarschuwingen helpen u bij het verbeteren van de beveiliging en werking van uw netwerk. Waarschuwingen bieden u informatie over:

- Afwijkingen van de activiteit van het geautoriseerde netwerk

- Het protocol en de operationele afwijkingen

- Verdacht verkeer van malware

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Adres scan detecteren.":::

Met opties voor waarschuwings beheer kunnen gebruikers:

- Vraag Sens oren om activiteiten te leren die zijn gedetecteerd als geautoriseerd verkeer.

- Bevestig het beoordelen van de waarschuwing.

- Geef Sens oren opdracht om gebeurtenissen te dempen die zijn gedetecteerd met identieke apparaten en vergelijkbaar verkeer.

Er zijn extra hulpprogram ma's beschikbaar waarmee u het onderzoek naar waarschuwingen kunt verbeteren en versnellen. Bijvoorbeeld:

  - Voeg instructies toe voor revisoren van waarschuwingen.

  - Waarschuwings groepen maken voor weer gave bij SOC-oplossingen. 

  - Zoeken naar specifieke waarschuwingen; gerelateerde PCAP-bestanden controleren; Bekijk de gedetecteerde apparaten en andere verbonden apparaten in de apparaattoewijzing of stuur waarschuwings Details naar partner systemen.

  - Waarschuwingen door sturen naar partner leveranciers: SIEM-systemen, MSSP-systemen en meer.

## <a name="alerts-and-engines"></a>Waarschuwingen en engines

Waarschuwingen worden geactiveerd wanneer de sensor motoren wijzigingen in het netwerk verkeer en gedrag die uw aandacht nodig hebben, kunnen detecteren. In dit artikel wordt het soort waarschuwingen beschreven dat elke engine activeert.

| Waarschuwingstype | Beschrijving |
|-|-|
| Waarschuwingen voor beleids schendingen | Wordt geactiveerd wanneer de engine voor beleids overtreding een afwijking detecteert van het eerder geleerde verkeer. Bijvoorbeeld: <br /> -Er is een nieuw apparaat gedetecteerd.  <br /> -Er wordt een nieuwe configuratie op een apparaat gedetecteerd. <br /> -Een apparaat dat niet als een programmeer apparaat is gedefinieerd, voert een wijziging in het programma uit. <br /> -Er is een firmware versie gewijzigd. |
| Waarschuwingen over Protocol schendingen | Wordt geactiveerd wanneer de engine voor protocol overtreding pakket structuren of veld waarden detecteert die niet voldoen aan de protocol specificatie. | 
| Operationele waarschuwingen | Wordt geactiveerd wanneer de operationele engine netwerk operationele incidenten detecteert of een apparaat defect. Een netwerk apparaat is bijvoorbeeld gestopt met een stop PLC opdracht of een interface op een sensor heeft het bewakings verkeer gestopt. |
| Waarschuwingen voor schadelijke software | Wordt geactiveerd wanneer schadelijke netwerk activiteit wordt gedetecteerd door de malware-engine. De engine detecteert bijvoorbeeld een bekende aanval zoals Conficker. |
| Anomalie waarschuwingen | Wordt geactiveerd wanneer de afwijkende engine een afwijking detecteert. Een apparaat voert bijvoorbeeld netwerk scans uit, maar is niet gedefinieerd als een scan apparaat. |

Er zijn hulpprogram ma's beschikbaar om sensor engines in en uit te scha kelen. Waarschuwingen worden niet geactiveerd vanuit engines die zijn uitgeschakeld. Zie [bepalen welk verkeer wordt bewaakt](how-to-control-what-traffic-is-monitored.md).

## <a name="alerts-and-sensor-reporting"></a>Waarschuwingen en sensor rapportage

Activiteiten die worden weer gegeven in waarschuwingen kunnen worden berekend wanneer u gegevens analyse, risico analyse en aanvals vector rapporten genereert. Wanneer u deze gebeurtenissen beheert, werkt de sensor de rapporten dienovereenkomstig bij.

Bijvoorbeeld:

  - Niet-geautoriseerde connectiviteit tussen een apparaat in een gedefinieerd subnet en apparaten buiten het subnet (openbaar) wordt weer gegeven in het rapport gegevens analyse *Internet activiteit* en de sectie *Internet verbindingen* van risico beoordeling. Nadat deze apparaten zijn geautoriseerd (geleerd), worden ze berekend in het genereren van deze rapporten.

  - Malware-gebeurtenissen die zijn gedetecteerd op netwerk apparaten worden gerapporteerd in rapporten voor risico analyse. Wanneer waarschuwingen over malware-gebeurtenissen *gedempt* zijn, worden betrokken apparaten niet berekend in het rapport risico beoordeling.

## <a name="see-also"></a>Zie ook

- [Leer-en slimme IT-leer modi](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
- [De informatie in waarschuwingen weergeven](how-to-view-information-provided-in-alerts.md)
- [De waarschuwingsgebeurtenis beheren](how-to-manage-the-alert-event.md)
- [Waarschuwings werk stromen versnellen](how-to-accelerate-alert-incident-response.md)
