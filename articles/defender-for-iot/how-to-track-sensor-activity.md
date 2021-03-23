---
title: Sensoractiviteit volgen
description: In de tijd lijn van de gebeurtenis ziet u een tijd lijn voor activiteiten die zijn gedetecteerd op uw netwerk, waaronder waarschuwingen en waarschuwings beheer acties, netwerk gebeurtenissen en gebruikers bewerkingen, zoals het aanmelden van gebruikers en het verwijderen van gebruikers.
ms.date: 12/10/2020
ms.topic: article
ms.openlocfilehash: 195908001fbd247ed2e0fa007bc8dcd5ebf28e60
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781616"
---
# <a name="track-sensor-activity"></a>Sensoractiviteit volgen

## <a name="event-timeline"></a>Tijd lijn van gebeurtenis

De tijd lijn van de gebeurtenis geeft een tijd lijn van de activiteit die uw sensor heeft gedetecteerd. Bijvoorbeeld:

  - Waarschuwingen en acties voor waarschuwings beheer

  - Netwerk gebeurtenissen

  - Gebruikers bewerkingen zoals het aanmelden van gebruikers en het verwijderen van gebruikers

De tijd lijn van de gebeurtenis biedt een chronologische weer gave van gebeurtenissen die in het netwerk zijn opgetreden. In de tijd lijn van de gebeurtenis kunnen inzichten en analyses worden uitgevoerd voor de keten van gebeurtenissen die worden voorafgegaan en gevolgd door een aanval of incident, die helpt bij het onderzoek en forensische.

> [!NOTE]
> *Beheerders* en *beveiligings analisten* kunnen de procedures uitvoeren die in deze sectie worden beschreven.

De gebeurtenis logboeken weer geven:

- Selecteer in het menu aan de zijkant **gebeurtenis tijdlijn**.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Bekijk uw gebeurtenissen op de tijd lijn van de gebeurtenis.":::

Naast het weer geven van de gebeurtenissen die de sensor heeft gedetecteerd, kunt u hand matig gebeurtenissen toevoegen aan de tijd lijn. Dit proces is handig als de gebeurtenis zich in een extern systeem heeft voorgedaan, maar wel van invloed is op uw netwerk. het is belang rijk om de gebeurtenis vast te leggen en deze als onderdeel van de tijd lijn weer te geven.

Gebeurtenissen hand matig toevoegen:

- Selecteer **gebeurtenis maken**.

Informatie over gebeurtenis logboeken exporteren naar een CSV-bestand:

- Selecteer **Exporteren**.

## <a name="filter-the-event-timeline"></a>De tijd lijn van de gebeurtenis filteren

De tijd lijn filteren om apparaten en gebeurtenissen weer te geven die voor u van belang zijn.

De tijd lijn filteren:

1. Selecteer **Geavanceerde filters**.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="Gebruik het venster Geavanceerde filters voor gebeurtenissen om uw gebeurtenissen te filteren.":::

2. Stel gebeurtenis filters als volgt in:

   - **Adres toevoegen**: gebeurtenissen weer geven voor specifieke apparaten.

   - **Adres uitsluiten**: gebeurtenissen voor specifieke apparaten verbergen.

   - **Gebeurtenis typen toevoegen**: specifieke gebeurtenis typen weer geven.

   - **Uitsluiten gebeurtenis typen**: specifieke gebeurtenis typen verbergen.

   - **Apparaatgroep**: Selecteer een apparaatgroep, zoals deze in de apparaattoewijzing is gedefinieerd. Alleen de gebeurtenissen uit deze groep worden weer gegeven.

3. Selecteer **Alles wissen** om alle geselecteerde filters te wissen.

4. **Alleen** zoeken op waarschuwingen, **waarschuwingen en kennisgevingen**, of **alle gebeurtenissen**.

5. Selecteer **datum selecteren** om een specifieke datum te kiezen. Kies een dag, uur en minuut. Gebeurtenissen uit het geselecteerde tijds bestek worden weer gegeven.

6.  Selecteer **gebruikers bewerkingen** om gebruikers bewerkings gebeurtenissen op te nemen of uit te sluiten.

7.  Selecteer de pijl (**V**) om meer informatie over de gebeurtenis weer te geven:

    - Selecteer de gerelateerde waarschuwingen (indien van toepassing) om een gedetailleerde beschrijving van de waarschuwing weer te geven.

    - Selecteer het apparaat om het apparaat op de kaart weer te geven.

    - Selecteer **gebeurtenissen filteren op verwante apparaten** als u wilt filteren op gerelateerde apparaten.

    - Selecteer **pcap-bestand** om het pcap-bestand te downloaden (als dit bestaat) met daarin een pakket opname van het hele netwerk op een specifiek tijdstip. 
    
      Het PCAP-bestand bevat technische informatie die netwerk engineers kunnen helpen bij het bepalen van de exacte para meters van de gebeurtenis. U kunt het PCAP-bestand analyseren met een Network Protocol Analyzer, zoals wireshark, een open source-toepassing.

## <a name="see-also"></a>Zie ook

[Waarschuwingen weergeven](how-to-view-alerts.md)
