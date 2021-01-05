---
title: Sensor activiteit volgen
description: In de tijd lijn van de gebeurtenis ziet u een tijd lijn voor activiteiten die zijn gedetecteerd op uw netwerk, waaronder waarschuwingen en waarschuwings beheer acties, netwerk gebeurtenissen en gebruikers bewerkingen, zoals het aanmelden van gebruikers en het verwijderen van gebruikers.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 3895e01b1fbfcde79ff91bd1eade8d902c33b852
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840722"
---
# <a name="track-sensor-activity"></a>Sensor activiteit volgen

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

   - **Adres insluiten**: specifieke gebeurtenis apparaten weer geven.

   - **Adres uitsluiten**: specifieke gebeurtenis apparaten verbergen.

   - **Gebeurtenis typen toevoegen**: specifieke gebeurtenis typen weer geven.

   - **Gebeurtenis typen uitsluiten**: specifieke gebeurtenis typen verbergen.

   - **Apparaatgroep**: Selecteer een apparaatgroep, zoals deze in de apparaattoewijzing is gedefinieerd. Alleen de gebeurtenissen van deze groep worden weer gegeven.

3. Selecteer **Alles wissen** om alle geselecteerde filters te wissen.

4. **Alleen** zoeken op waarschuwingen, **waarschuwingen en kennisgevingen**, of **alle gebeurtenissen**.

5. Selecteer **datum selecteren** om een specifieke datum te kiezen. Kies een dag, uur en minuut. Gebeurtenissen uit het geselecteerde tijds bestek worden weer gegeven.

6.  Selecteer **gebruikers bewerkingen** om gebruikers bewerkings gebeurtenissen op te nemen of uit te sluiten.

7.  Selecteer de pijl (**V**) om meer informatie over de gebeurtenis weer te geven:

    - Selecteer de gerelateerde waarschuwingen (indien van toepassing) om een gedetailleerde beschrijving van de waarschuwing weer te geven.

    - Selecteer het apparaat om het apparaat op de kaart weer te geven.

    - Selecteer **gebeurtenissen filteren op verwante apparaten** als u wilt filteren op gerelateerde apparaten.

    - Selecteer **pcap-bestand** om het pcap-bestand te downloaden (als dit bestaat) met daarin een pakket opname van het hele netwerk op een specifiek tijdstip. 
    
      Het PCAP-bestand bevat technische informatie waarmee technici precies kunnen bepalen waar de gebeurtenis zich voordoet en wat er gebeurt. U kunt het PCAP-bestand analyseren met een Network Protocol Analyzer, zoals wireshark, een gratis toepassing.

## <a name="see-also"></a>Zie tevens

[Waarschuwingen weergeven](how-to-view-alerts.md)
