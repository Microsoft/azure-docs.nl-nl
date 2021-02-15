---
title: Werken met het sensor console-dash board
description: Met het dash board kunt u snel de beveiligings status van uw netwerk bekijken. Het biedt een overzicht van bedreigingen voor uw hele systeem op een tijd lijn, samen met informatie over gerelateerde apparaten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: eb37434213dd756ba5d7137b93a1cd37da5bb9ae
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523631"
---
# <a name="the-dashboard"></a>Het dash board

Met het dash board kunt u snel de beveiligings status van uw netwerk bekijken. Het biedt een overzicht van bedreigingen voor uw hele systeem op een tijd lijn, samen met informatie over gerelateerde apparaten, waaronder:

- Waarschuwingen op verschillende ernst niveaus:

- Kritiek

- Primair

- Secundair

- Waarschuwingen

- In de twee indica toren in het midden van de pagina worden de pakketten per seconde (PPS) en niet-bevestigde waarschuwingen (UA) weer gegeven. **PPS** is het aantal pakketten dat per seconde door het systeem is bevestigd. **Ua** is het aantal waarschuwingen dat nog niet is bevestigd.

- Lijst met niet-bevestigde waarschuwingen met hun beschrijving.

- Tijd lijn met de beschrijving van de waarschuwing.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Dashboard":::

## <a name="viewing-the-latest-alerts"></a>De meest recente waarschuwingen weer geven

De UA-meter (niet-bevestigde waarschuwingen) in het midden van de pagina geeft het aantal dergelijke waarschuwingen aan. Als u een lijst met waarschuwingen wilt weer geven, selecteert u onder aan de dashboard pagina **meer waarschuwingen** of selecteert u **waarschuwingen** in het menu aan de zijkant.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Niet-bevestigde waarschuwingen":::

## <a name="status-boxes"></a>Status vakken

In deze sectie wordt elk status venster beschreven.

| Status en meters | Description |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Kritieke waarschuwingen"::: | **Kritieke waarschuwingen** : in het vak aan de bovenkant van de pagina wordt het aantal kritieke waarschuwingen aangegeven. Schakel dit selectie vakje in om beschrijvingen van de waarschuwingen weer te geven op de tijd lijn en op de lijst onder de meters, indien van toepassing.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Grote waarschuwingen"::: | **Grote waarschuwingen** : het vak in de rechter bovenhoek van de pagina geeft het aantal grote waarschuwingen aan. Schakel dit selectie vakje in om beschrijvingen van de waarschuwingen weer te geven op de tijd lijn en op de lijst onder de meters, indien van toepassing.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Kleine waarschuwingen"::: | **Kleine waarschuwingen** : in het vak linksonder in de pagina wordt het aantal kleine waarschuwingen aangegeven. Schakel dit selectie vakje in om beschrijvingen van de waarschuwingen weer te geven op de tijd lijn en op de lijst onder de meters, indien van toepassing.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Waarschuwings meldingen"::: | **Waarschuwingen** : het vak onder in het midden van de pagina geeft het aantal waarschuwingen. Schakel dit selectie vakje in om beschrijvingen van de waarschuwingen weer te geven op de tijd lijn en op de lijst onder de meters, indien van toepassing.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="All Alerts"::: | **Alle waarschuwingen** : het vak aan de rechter kant van de pagina geeft het totale aantal kritieke, primaire, secundaire en waarschuwings meldingen aan. Schakel dit selectie vakje in om beschrijvingen van de waarschuwingen weer te geven op de tijd lijn en op de lijst onder de meters, indien van toepassing. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Pakketten per seconde"::: | **Pakketten per seconde (PPS)** : de PPS-metriek is een indicatie van de prestaties van het netwerk. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Niet-bevestigde gebeurtenissen (UA)"::: | Niet- **bevestigde gebeurtenissen** : met deze metriek wordt het aantal niet-bevestigde gebeurtenissen aangegeven.

## <a name="using-the-timeline"></a>De tijd lijn gebruiken

De waarschuwingen worden weer gegeven langs een verticale tijd lijn die datum-en tijd gegevens bevat.

De tijd lijn wordt grafisch weer gegeven:

- Kritieke waarschuwingen

- Grote waarschuwingen

- Kleine waarschuwingen

- Waarschuwings meldingen

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Tijdlijn grafiek":::

## <a name="viewing-alerts"></a>Waarschuwingen weer geven

Selecteer **de pijl-omlaag onder aan** een waarschuwings venster om de waarschuwings vermelding en de informatie over apparaten weer te geven.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Informatie over waarschuwings vermeldingen en apparaten":::

- Selecteer het apparaat om de fysieke-modus toewijzing weer te geven. De onderhevige apparaten zijn gemarkeerd.

- Klik ergens in het waarschuwings venster om aanvullende details over de waarschuwing weer te geven. Er wordt een pop-upvenster weer gegeven dat er ongeveer als volgt uitziet

- Selecteer :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: om een CSV-bestand over de waarschuwing te exporteren.

- Alleen beheerders en beveiligings analisten: Selecteer :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="alles bevestigen"::: om alle bijbehorende waarschuwingen te **bevestigen** .

- Selecteer :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::om een waarschuwings rapport te downloaden als een PDF-bestand.

- Selecteer :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="vastmaken":::om de waarschuwing vast te maken of te losmaken. Als u pincode selecteert, wordt deze toegevoegd aan het venster **vastgemaakte waarschuwingen** in het scherm **waarschuwingen** .

- Selecteer :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="downloaden"::: om de waarschuwing te onderzoeken door het gerelateerde pcap-bestand te downloaden met een netwerkprotocol analyse.

- Selecteer :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="Cloud"::: om een gerelateerd gefilterd pcap-bestand te downloaden dat alleen de waarschuwings-relevante pakketten bevat, waardoor de grootte van het uitvoer bestand wordt verkleind en een meer gerichte analyse mogelijk is. U kunt deze weer geven met behulp van [wireshark](https://www.wireshark.org/).

- Selecteer :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="Navigatie"::: om naar de gebeurtenis tijdlijn te navigeren op het tijdstip van de aangevraagde waarschuwing. Op deze manier kunt u andere gebeurtenissen evalueren die kunnen optreden rond de specifieke waarschuwing.

- Alleen beheerders en beveiligings analisten: Wijzig de status van de waarschuwing van niet bevestigd in bevestigd. Selecteer leren om de gedetecteerde activiteit goed te keuren.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Niet-geautoriseerde Internet connectiviteit gedetecteerd":::

## <a name="next-steps"></a>Volgende stappen

[Werken met waarschuwingen op uw sensor](how-to-work-with-alerts-on-your-sensor.md)
