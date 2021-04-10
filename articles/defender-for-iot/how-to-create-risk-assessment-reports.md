---
title: Rapporten voor risico analyse maken
description: Krijg inzicht in netwerk Risico's die worden gedetecteerd door afzonderlijke Sens oren of een geaggregeerde weer gave van door alle Sens oren gedetecteerde Risico's.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 853157ef1b97fefdd15785b2a71c7ccc5d06a9a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784251"
---
# <a name="risk-assessment-reporting"></a>Rapportage van risico beoordeling

## <a name="about-risk-assessment-reports"></a>Rapporten over risico analyse

Rapporten voor risico analyse bieden:

- Een algehele beveiligings score voor de apparaten die worden gedetecteerd door de organisatie Sens oren.

- Een beveiligings score voor elk netwerk apparaat dat door een afzonderlijke sensor wordt gedetecteerd.

- Een uitsplitsing van het aantal kwets bare apparaten, apparaten waarvoor verbeteringen en beveiligde apparaten nodig zijn.

-  Inzicht in beveiliging en operationele problemen:

    - Configuratie problemen

    - Beveiligingslek met betrekking tot het apparaat door beveiligings niveau

    - Problemen met netwerk beveiliging

    - Operationele netwerk problemen

    - Verbindingen met ICS-netwerken

    - Internet verbindingen

    - Industriële malware-indica toren

    - Protocolproblemen

    - Aanvals vectoren

### <a name="risk-mitigation"></a>Risico beperking

Rapporten bieden aanbevelingen om u te helpen uw beveiligings score te verbeteren. Installeer bijvoorbeeld de meest recente beveiligings updates, voer een upgrade van de firmware uit naar de nieuwste versie of volg de waarschuwingen.

## <a name="about-security-scores"></a>Over beveiligings scores

In elk rapport wordt een algehele netwerk beveiligings Score gegenereerd. De score is het percentage van de beveiliging van 100 procent. Een Score van 30% zou bijvoorbeeld aangeven dat uw netwerk 30% veilig is.

Scores voor risico analyse zijn gebaseerd op informatie die is geleerd van pakket inspectie, gedrags model motoren en een SCADA voor de status van de machine.

**Beveiligde apparaten** zijn apparaten met een beveiligings Score van meer dan 90%.

**Apparaten die moeten worden verbeterd**: apparaten met een beveiligings score tussen 70 procent en 89%.

**Kwets bare apparaten** zijn apparaten met een beveiligings Score van minder dan 70%.

## <a name="create-risk-assessment-reports"></a>Rapporten voor risico analyse maken

Maak een rapport met een PDF-risico analyse. De rapport naam wordt automatisch gegenereerd als risk-assessment-report-1.pdf. Het nummer wordt bijgewerkt voor elk nieuw rapport dat u maakt.  De tijd en dag van maken worden weer gegeven.

### <a name="create-a-sensor-risk-assessment-report"></a>Een rapport met een beoordeling van de sensor risico maken

Maak een rapport met een risico analyse op basis van detecties die zijn gemaakt door de sensor waarbij u bent aangemeld.

Een rapport maken:

1. Meld u aan bij de sensor console.
1. Selecteer **risico analyse** in het menu aan de zijkant.
1. Selecteer **rapport genereren**. Het rapport wordt weer gegeven in de sectie gearchiveerde rapporten.
1. Selecteer in de sectie gearchiveerde rapporten het rapport om het te downloaden.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Een weer gave van de risico analyse.":::

Een bedrijfs logo importeren:

- Selecteer **logo importeren**.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Een rapport voor een risico analyse voor een on-premises beheer console maken

Maak een rapport met een risico analyse op basis van detecties die zijn gemaakt door de Sens oren die worden beheerd door uw on-premises beheer console. 

Een rapport maken:

1. Selecteer **risico analyse** in het menu aan de zijkant.

2. Selecteer een sensor in de vervolg keuzelijst **sensor selecteren** .

3. Selecteer **rapport genereren**.

4. Selecteer **downloaden** in het gedeelte **Gearchiveerde rapporten** .

Een bedrijfs logo importeren:

- Selecteer **logo importeren**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importeer uw logo via de weer gave risico analyse.":::

## <a name="see-also"></a>Zie ook

[Aanvals vector rapportage](how-to-create-attack-vector-reports.md)

