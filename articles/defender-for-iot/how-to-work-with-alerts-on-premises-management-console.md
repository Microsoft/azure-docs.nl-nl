---
title: Werken met waarschuwingen in de on-premises beheerconsole
description: Gebruik de on-premises beheer console om een Enter prise-weer gave van recente bedreigingen in uw netwerk te verkrijgen en beter te begrijpen hoe de gebruikers van de sensor ze verwerken.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 604650f0cb08eac4a3ab1cfd3fdcbf2e7ff0d19e
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105032137"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Werken met waarschuwingen in de on-premises beheerconsole 

U kunt het volgende doen vanuit het venster **waarschuwingen** in de-beheer console:

- Werken met waarschuwings filters

- Werken met waarschuwings tellers

- Waarschuwings gegevens weer geven

- Waarschuwings gebeurtenissen beheren

- Regels voor waarschuwings uitsluiting maken

- Regels voor het uitsluiten van waarschuwingen activeren vanaf externe systemen

- Incident werk stroom versnellen met waarschuwings groepen

## <a name="view-alerts-in-the-on-premises-management-console"></a>Waarschuwingen weer geven in de on-premises beheer console

De on-premises beheer console voegt waarschuwingen van alle verbonden Sens oren samen. Dit biedt een bedrijfs weergave van recente bedreigingen in uw netwerk en helpt u beter inzicht te krijgen in hoe de gebruikers van de sensor ze verwerken.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Scherm opname van het venster waarschuwingen.":::

### <a name="work-with-alert-filters"></a>Werken met waarschuwings filters

Het venster **waarschuwingen** geeft de waarschuwingen weer die zijn gegenereerd door Sens oren die zijn verbonden met uw on-premises beheer console. U kunt alle waarschuwingen voor verbonden Sens oren bekijken of de waarschuwingen weer geven die zijn verzonden vanuit een specifiek:

- Site

- Zone

- Apparaat

- Sensoren

Selecteer **filters wissen** om alle waarschuwingen weer te geven.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Wis uw filters door de knop filters wissen te selecteren.":::

### <a name="work-with-alert-counters"></a>Werken met waarschuwings tellers

Waarschuwings tellers geven een uitsplitsing van waarschuwingen op ernst en de bevestigde status.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="Het waarschuwings item laat zien hoeveel waarschuwingen u hebt.":::

De volgende Ernst niveaus worden weer gegeven in het waarschuwings item:

- **Kritiek**: geeft aan dat er een schadelijke aanval is die onmiddellijk moet worden afgehandeld.

- **Major**: geeft een beveiligings risico dat belang rijk is voor het oplossen van het probleem.

- **Secundair**: geeft een deel van het basislijn gedrag aan dat een beveiligings risico kan bevatten.

- **Waarschuwing**: Hiermee wordt een afwijking van het basislijn gedrag aangegeven zonder beveiligings Risico's.

Ernst niveaus zijn vooraf gedefinieerd.

U kunt de teller aanpassen om getallen op te geven op basis van bevestigde en niet-bevestigde waarschuwingen. Niet-bevestigde waarschuwingen zijn geactiveerd op Defender voor IoT-Sens oren, maar zijn nog niet door de Opera tors op de sensor gecontroleerd.

Wanneer de optie **bevestigde waarschuwingen weer geven** is geselecteerd, worden alle bevestigde waarschuwingen in het venster **waarschuwingen** weer gegeven.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Bekijk de bevestigde waarschuwingen.":::

### <a name="view-alert-information"></a>Waarschuwings gegevens weer geven

De waarschuwing geeft de volgende informatie weer:

- Een samen vatting van de waarschuwings gebeurtenis.

- Ernst van waarschuwing.

- Een koppeling naar de waarschuwing in de sensor die deze heeft gedetecteerd.

- Een waarschuwings-UUID. De UUID bestaat uit de waarschuwings-ID die is gekoppeld aan de waarschuwings gebeurtenis die is gedetecteerd op de sensor, gescheiden door een koppel teken en gevolgd door een uniek systeem-ID-nummer.

**Waarschuwing UUID van on-premises beheer console**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Een apparaat is verbonden maar is niet geautoriseerd.":::

**ID van sensor waarschuwing**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="ID van sensor waarschuwing.":::

Als u werkt met UUID, zorgt u ervoor dat elke waarschuwing die in de on-premises beheer console wordt weer gegeven, doorzoekbaar is en herkenbaar is aan een uniek nummer. Dit is vereist omdat waarschuwingen die zijn gegenereerd op basis van meerdere Sens oren, dezelfde waarschuwings-ID kunnen opleveren.

> [!NOTE]
> Standaard worden UUID in de volgende partner systemen weer gegeven wanneer u regels voor door sturen hebt gedefinieerd: ArcSight, syslog-servers, QRadar, Sentinel en netwitness. Setup is niet vereist.

Waarschuwings gegevens weer geven:

- Selecteer een waarschuwing in de lijst met waarschuwingen.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Scherm opname van waarschuwings informatie.":::

De waarschuwing weer geven in de sensor:

- Selecteer **sensor openen** in de waarschuwing.

De apparaten in een zone kaart weer geven:

- Selecteer **apparaten weer geven** om de kaart weer te geven met de focus op het apparaat waarvoor een waarschuwing is gegeven en alle apparaten die hieraan zijn gekoppeld.

## <a name="manage-alert-events"></a>Waarschuwings gebeurtenissen beheren

Er zijn verschillende opties beschikbaar voor het beheren van waarschuwings gebeurtenissen vanuit de on-premises beheer console.

- Waarschuwings gebeurtenissen leren of bevestigen. Selecteer **leren & erken** om alle waarschuwings gebeurtenissen te leren die kunnen worden geautoriseerd en om alle waarschuwings gebeurtenissen te bevestigen die momenteel niet zijn bevestigd.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Selecteer leren & erken om alles te leren.":::

- Waarschuwings gebeurtenissen dempen en de demping opheffen.

Zie het artikel over het [beheren van waarschuwings gebeurtenissen](how-to-manage-the-alert-event.md) in de sensor voor meer informatie over het leren, bevestigen en dempen van waarschuwings gebeurtenissen.

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
1. Selecteer Uitgebreide waarschuwingen exporteren om waarschuwings gegevens in afzonderlijke rijen te exporteren voor elke waarschuwing die betrekking heeft op meerdere apparaten. Wanneer uitgebreide waarschuwingen exporteren is geselecteerd, wordt in het CSV-bestand een dubbele rij van de waarschuwing gemaakt met de unieke items in elke rij. Met deze optie kunt u de geëxporteerde waarschuwings gebeurtenissen gemakkelijker onderzoeken.  

## <a name="create-alert-exclusion-rules"></a>Regels voor waarschuwings uitsluiting maken

Geef Defender voor IoT opdracht om waarschuwings triggers te negeren op basis van:

- Tijd zones en tijds perioden

- Adres van apparaat (IP, MAC, subnet)

- Waarschuwings namen

- Een specifieke sensor

Maak regels voor het uitsluiten van waarschuwingen als u wilt dat Defender voor IoT activiteiten negeert waarmee een waarschuwing wordt geactiveerd.

Als u bijvoorbeeld weet dat alle binnenkomende apparaten die door een specifieke sensor worden bewaakt, twee dagen worden door lopen, kunt u een uitsluitings regel definiëren die ervoor zorgt dat Defender voor IoT waarschuwingen detecteert die door deze sensor worden gedetecteerd tijdens de vooraf gedefinieerde periode.

### <a name="alert-exclusion-logic"></a>Logica voor uitsluitingen van waarschuwingen

Logica van waarschuwings regel is `AND` gebaseerd. Dit betekent dat een waarschuwing alleen wordt geactiveerd wanneer aan de voor waarden van de regel wordt voldaan.

Als er geen regel voorwaarde is gedefinieerd, bevat de voor waarde alle opties. Als u bijvoorbeeld de naam van een sensor in de regel niet opneemt, wordt deze toegepast op alle Sens oren.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Scherm opname van de weer gave uitsluitings regel maken.":::

Regel overzichten worden weer gegeven in het venster **uitsluitings regel** .

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Scherm opname van de weer gave samen vatting van uitsluitings regel.":::

Naast het werken met uitsluitings regels, kunt u waarschuwingen onderdrukken door ze te dempen.

### <a name="create-exclusion-rules"></a>Uitsluitings regels maken

Uitsluitings regels maken:

1. Selecteer in het linkerdeel venster van de on-premises beheer console de optie **waarschuwing uitsluiten**. Definieer een nieuwe uitsluitings regel door het pictogram **toevoegen** te selecteren :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: in de rechter bovenhoek van het venster dat wordt geopend. Het dialoog venster **uitsluitings regel maken** wordt geopend.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Maak een uitsluitings waarschuwing door de informatie hier in te vullen.":::

2. Voer een regel naam in het veld **naam** in. De naam mag geen aanhalings tekens ( `"` ) bevatten.

3. Voer in de sectie **per tijd zone/periode** een tijds periode binnen een specifieke tijd zone in. Gebruik deze functie wanneer een uitsluitings regel is gemaakt voor een bepaalde periode in één tijd zone, maar moet worden geïmplementeerd op hetzelfde tijdstip in andere tijd zones. U moet bijvoorbeeld een uitsluitings regel tussen 8:00 en 10:00 uur Toep assen in drie verschillende tijd zones. In dit geval maakt u drie afzonderlijke uitsluitings regels die dezelfde tijds periode en de relevante tijd zone gebruiken.

4. Selecteer **ADD** (Toevoegen). Tijdens de uitsluitings periode worden er geen waarschuwingen gemaakt voor de verbonden Sens oren.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Scherm opname van de weer gave per tijds periode.":::

5. Geef in de sectie **per apparaat adres** de volgende opties op:

  - Het IP-adres van het apparaat, het MAC-adres of het subnet-adres dat u wilt uitsluiten.

  - Verkeers richting voor de uitgesloten apparaten, bron en bestemming.

6. Selecteer **ADD** (Toevoegen).

7. Typ in de sectie **op waarschuwings titel** de titel van de waarschuwing. Selecteer in de vervolg keuzelijst de titel van de waarschuwing of de titels die moeten worden uitgesloten.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Scherm afbeelding van de titel weergave op waarschuwing.":::

8. Selecteer **ADD** (Toevoegen).

9. In de sectie **op sensor naam** typt u de naam van de sensor. Selecteer in de vervolg keuzelijst de sensor of Sens oren die u wilt uitsluiten.

10. Selecteer **ADD** (Toevoegen).

11. Selecteer **SAVE** (Opslaan). De nieuwe regel wordt weer gegeven in de lijst met regels.

U kunt waarschuwingen onderdrukken door ze te dempen of door regels voor het uitsluiten van waarschuwingen te maken. In deze sectie worden mogelijke gebruiks voorbeelden voor beide functies beschreven.

- **Uitsluitings regel**. Een uitsluitings regel schrijven wanneer:

  - U weet vooraf dat u de gebeurtenis uit de Data Base wilt uitsluiten. U weet bijvoorbeeld dat het scenario dat bij een bepaalde sensor wordt gedetecteerd, irrelevante waarschuwingen zal activeren. Zo kunt u onderhouds werkzaamheden uitvoeren voor organisatie-PLCs op een specifieke site en wilt u waarschuwingen met betrekking tot PLCs voor deze site onderdrukken.

  - U wilt dat Defender voor IoT gebeurtenissen negeert voor een specifieke periode (voor systeem onderhouds taken).

  - U wilt gebeurtenissen in een specifiek subnet negeren.

  - U wilt de waarschuwings gebeurtenissen beheren die zijn gegenereerd op basis van verschillende Sens oren met één regel.

  - U wilt de uitsluitingen van waarschuwingen niet als een gebeurtenis in het gebeurtenis logboek bijhouden.

- **Dempen**. Een waarschuwing dempen wanneer:

  - Items die gedempt moeten zijn, zijn niet gepland. U weet niet vooraf welke gebeurtenissen irrelevant zullen zijn.

  - U wilt de waarschuwing in het venster **waarschuwingen** onderdrukken, maar u wilt deze wel bijhouden in het gebeurtenis logboek.

  - U wilt gebeurtenissen op een specifiek kanaal negeren.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Regels voor het uitsluiten van waarschuwingen activeren vanaf externe systemen

Regels voor het uitsluiten van waarschuwingen activeren vanaf externe systemen. U kunt bijvoorbeeld uitsluitings regels beheren vanuit ondernemings ticket systemen of-systemen die de processen voor netwerk onderhoud beheren.

Definieer de Sens oren, motoren, begin tijd en eind tijd om de regel toe te passen. Zie voor meer informatie [Defender voor IOT API-sensor-en beheer console-api's](references-work-with-defender-for-iot-apis.md).

Regels die u maakt met behulp van de API, worden weer gegeven in het venster **uitsluitings regel** als ro.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Scherm opname van de weer gave uitsluitings regel voor bewerken.":::

## <a name="next-steps"></a>Volgende stappen

[Werk met waarschuwingen op uw sensor](how-to-work-with-alerts-on-your-sensor.md).
Bekijk de [waarschuwingen voor de Defender-engine voor IOT](alert-engine-messages.md).