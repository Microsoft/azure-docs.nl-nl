---
title: Waarschuwings gebeurtenissen beheren
description: Waarschuwings gebeurtenissen beheren die in uw netwerk zijn gedetecteerd.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 866ff4cad74b7092dda11a20f8f7bcadf91233ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526898"
---
# <a name="manage-alert-events"></a>Waarschuwings gebeurtenissen beheren

De volgende opties zijn beschikbaar voor het beheren van waarschuwings gebeurtenissen:

 | Bewerking | Beschrijving |
 |--|--|
 | **Learn** | De gedetecteerde gebeurtenis autoriseren. Zie voor meer informatie [over het leren en voor komen van gebeurtenissen](#about-learning-and-unlearning-events). |
 | **Bevestigen** | De waarschuwing eenmaal verbergen voor de gedetecteerde gebeurtenis. De waarschuwing wordt opnieuw geactiveerd als de gebeurtenis opnieuw wordt gedetecteerd. Zie [informatie over bevestiging en het onbevestigde gebeurtenissen](#about-acknowledging-and-unacknowledging-events)voor meer informatie. |
 | **Dempen** | De activiteit continu negeren met identieke apparaten en vergelijkbaar verkeer. Zie voor meer informatie [over het dempen en dedempen van gebeurtenissen](#about-muting-and-unmuting-events). |
 
U kunt ook waarschuwings gegevens exporteren.
## <a name="about-learning-and-unlearning-events"></a>Over het leren en voor komen van gebeurtenissen

Gebeurtenissen die duiden op afwijkingen van het geleerde netwerk, kunnen de geldige netwerk wijzigingen weer spie gelen. Voor beelden zijn mogelijk een nieuw geautoriseerd apparaat dat is toegevoegd aan het netwerk of een geautoriseerde firmware-update.

Wanneer u **leren** selecteert, beschouwt de sensor het verkeer, de configuraties of de activiteit geldig. Dit betekent dat er geen waarschuwingen meer worden geactiveerd voor de gebeurtenis. Dit betekent ook dat de gebeurtenis niet wordt berekend wanneer de sensor risico analyse, aanvals vector en andere rapporten genereert.

U ontvangt bijvoorbeeld een waarschuwing dat de scan activiteit van het adres aangeeft op een apparaat dat niet eerder door een netwerk scanner is gedefinieerd. Als dit apparaat is toegevoegd aan het netwerk met het oog op het scannen, kunt u de sensor de opdracht geven het apparaat als een scan apparaat te leren.

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="Het venster voor gedetecteerde scans van adressen.":::

Geleerde gebeurtenissen kunnen onleesbaar zijn. Wanneer de sensor gebeurtenissen uitgeeft, worden er waarschuwingen met betrekking tot deze gebeurtenis geactiveerd.

## <a name="about-acknowledging-and-unacknowledging-events"></a>Over bevestigings-en bevestigings gebeurtenissen

In bepaalde situaties wilt u mogelijk niet dat een sensor een gedetecteerde gebeurtenis moet ontdekken of kan de optie niet beschikbaar zijn. In plaats daarvan moet het incident mogelijk worden beperkt. Bijvoorbeeld:

- **Een netwerk configuratie of-apparaat beperken**: er wordt een waarschuwing weer gegeven waarin wordt aangegeven dat er een nieuw apparaat is gedetecteerd op het netwerk. Wanneer u onderzoekt, ontdekt u dat het apparaat een niet-geautoriseerd netwerk apparaat is. U kunt het incident afhandelen door het apparaat los te koppelen van het netwerk.
- **Een sensor configuratie bijwerken**: er wordt een waarschuwing weer gegeven waarin wordt aangegeven dat een server een uitzonderlijk groot aantal externe verbindingen heeft gestart. Deze waarschuwing is geactiveerd omdat de drempel waarden van de sensor afwijkingen zijn gedefinieerd om waarschuwingen te activeren boven een bepaald aantal sessies binnen één minuut. U kunt het incident verwerken door de drempel waarden bij te werken.

Nadat u de oplossing of het onderzoek hebt uitgevoerd, kunt u de sensor de waarschuwing laten verbergen door **bevestiging** te selecteren. Als de gebeurtenis opnieuw wordt gedetecteerd, wordt de waarschuwing opnieuw geactiveerd.

De waarschuwing wissen:

  - Selecteer **erkennen**.

De waarschuwing opnieuw weer geven:

  - Open de waarschuwing en selecteer **unerken**.

Het bevestigen van waarschuwingen als verder onderzoek vereist is.

## <a name="about-muting-and-unmuting-events"></a>Over het dempen en dempen van gebeurtenissen

Onder bepaalde omstandigheden wilt u mogelijk uw sensor een specifiek scenario in uw netwerk negeren. Bijvoorbeeld:

  - De **afwijkende** engine activeert een waarschuwing op een piek in de band breedte tussen twee apparaten, maar de Prikker is geldig voor deze apparaten.

  - De engine voor **protocol overtredingen** heeft een waarschuwing over een protocol afwijking gedetecteerd tussen twee apparaten, maar de afwijking is geldig tussen de apparaten.

In deze situaties is Learning niet beschikbaar. Wanneer u niet meer wilt weten en u de waarschuwing wilt onderdrukken en het apparaat wilt verwijderen bij het berekenen van Risico's en aanvals vectoren, kunt u in plaats daarvan de waarschuwings gebeurtenis dempen.

> [!NOTE] 
> U kunt geen gebeurtenissen dempen waarbij een internet apparaat is gedefinieerd als de bron of bestemming.

### <a name="what-alert-activity-is-muted"></a>Welke waarschuwings activiteit is gedempt?

Een gedempt scenario bevat de netwerk apparaten en het verkeer dat voor een gebeurtenis is gedetecteerd. In de titel van de waarschuwing wordt het verkeer beschreven dat wordt gedempt.

Het apparaat of de apparaten die worden gedempt, worden weer gegeven als een afbeelding in de waarschuwing. Als er twee apparaten worden weer gegeven, wordt het specifieke, gewaarschuwde verkeer ertussen gedempt.

**Voorbeeld 1**

Wanneer een gebeurtenis is gedempt, wordt deze telkens wanneer de primaire (bron) een ongeldige functie code verzendt, zoals gedefinieerd door de leverancier, genegeerd.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="Secundair apparaat ontvangen.":::

**Voorbeeld 2**

Wanneer een gebeurtenis is gedempt, wordt deze genegeerd telkens wanneer de bron een HTTP-header met ongeldige inhoud verzendt, ongeacht het doel.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Scherm opname van ongeldige inhoud voor HTTP-header.":::

**Nadat een gebeurtenis is gedempt:**

- De waarschuwing is toegankelijk in de **bevestigde** waarschuwings weergave totdat deze niet meer wordt gedempt.

- De dempings actie wordt weer gegeven in de **tijd lijn** van de gebeurtenis.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Gebeurtenis gedetecteerd en gedempt.":::

- De sensor berekent apparaten opnieuw wanneer risico analyse, aanvals vector en andere rapporten worden gegenereerd. Als u bijvoorbeeld een waarschuwing die schadelijk verkeer op een apparaat detecteert hebt gedempt, wordt dat apparaat niet berekend in het rapport risico beoordeling.

**Een waarschuwing dempen en de demping opheffen:**

- Selecteer het **demp** pictogram op de waarschuwing.

**Uitgedempte waarschuwingen weer geven:**

1. Selecteer de **bevestigde** optie formulier het hoofd scherm **waarschuwingen** .

2. Beweeg de muis aanwijzer over een waarschuwing om te zien of deze gedempt is.  

## <a name="export-alert-information"></a>Waarschuwings gegevens exporteren

Waarschuwings gegevens exporteren naar een CSV-bestand. U kunt informatie over alle gedetecteerde waarschuwingen exporteren of informatie exporteren op basis van de gefilterde weer gave. De volgende gegevens worden geëxporteerd:

- Bron adres
- Doel adres
- Titel van de waarschuwing
- Ernst van waarschuwing
- Waarschuwings bericht
- Aanvullende informatie
- Bevestigde status
- PCAP-Beschik baarheid

Exporteren:

1. Selecteer waarschuwingen in het menu aan de zijkant.
1. Selecteer Exporteren.
1. Selecteer Uitgebreide waarschuwingen exporteren om waarschuwings gegevens in afzonderlijke rijen te exporteren voor elke waarschuwing die betrekking heeft op meerdere apparaten. Wanneer uitgebreide waarschuwingen exporteren is geselecteerd, wordt in het CSV-bestand een dubbele rij van de waarschuwings gebeurtenis gemaakt met de unieke items in elke rij. Met deze optie kunt u de geëxporteerde waarschuwings gebeurtenissen gemakkelijker onderzoeken.

## <a name="see-also"></a>Zie ook

[Controleren welk verkeer wordt bewaakt](how-to-control-what-traffic-is-monitored.md)
