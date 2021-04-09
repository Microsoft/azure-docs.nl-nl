---
title: Waarschuwings werk stromen versnellen
description: Verbeter de werk stromen voor waarschuwingen en incidenten.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: c12d1135b7a7bc87a38a609aeeb6ada8caa9a25e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779117"
---
# <a name="accelerate-alert-workflows"></a>Waarschuwings werk stromen versnellen

In dit artikel wordt beschreven hoe u de werk stromen van waarschuwingen kunt versnellen met behulp van waarschuwings opmerkingen, waarschuwings groepen en aangepaste waarschuwings regels in azure Defender voor IoT.  Met deze hulpprogram ma's kunt u het volgende doen:

- Analyseer en beheer het grote volume van waarschuwings gebeurtenissen dat in uw netwerk is gedetecteerd.

- Specifieke netwerk activiteit lokaliseren en verwerken.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Incident werk stromen versnellen met behulp van waarschuwings opmerkingen

Werk met waarschuwings opmerkingen om de communicatie tussen individuen en teams te verbeteren tijdens het onderzoek van een waarschuwings gebeurtenis.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="Scherm opname waarin schadelijke activiteiten worden weer gegeven.":::

Gebruik waarschuwings opmerkingen om te verbeteren:

- **Werk stroom stappen**: Geef de stappen voor het beperken van waarschuwingen.

- **Follow-up van de werk stroom**: Meld u aan dat de stappen zijn uitgevoerd.

- **Richt lijnen voor werk stroom**: geef aanbevelingen, inzichten of waarschuwingen over de gebeurtenis op.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="Scherm opname waarin waarschuwings opmerkingen worden weer gegeven.":::

De lijst met beschik bare opties wordt weer gegeven in elke waarschuwing. Gebruikers kunnen een of meer berichten selecteren.

Waarschuwings opmerkingen toevoegen:

1. Selecteer **systeem instellingen** in het menu aan de zijkant.

2. Selecteer **waarschuwings opmerkingen** in het venster **systeem instelling** .

3. Voer in het vak **opmerkingen toevoegen** de tekst van de opmerking in. Gebruik Maxi maal 50 tekens. Komma's zijn niet toegestaan.

4. Selecteer **Toevoegen**.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Incident werk stromen versnellen met behulp van waarschuwings groepen

Met waarschuwings groepen kunnen SOC-teams waarschuwingen weer geven en filteren in hun SIEM-oplossingen en deze waarschuwingen vervolgens beheren op basis van beveiligings beleid voor ondernemingen en zakelijke prioriteiten. Zo worden waarschuwingen over nieuwe detecties in een detectie groep ingedeeld. Deze groep bevat waarschuwingen die betrekking hebben op de detectie van nieuwe apparaten, nieuwe VLAN'S, nieuwe gebruikers accounts, nieuwe MAC-adressen en meer.

Waarschuwings groepen worden toegepast wanneer u regels voor door sturen maakt voor de volgende partner oplossingen:

  - Syslog-servers

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="Scherm opname van het maken van een doorstuur regel.":::

De relevante waarschuwings groep wordt weer gegeven in oplossingen voor partner uitvoer. 

### <a name="requirements"></a>Vereisten

De waarschuwings groep wordt weer gegeven in ondersteunde partner oplossingen met de volgende voor voegsels:

- **kat** voor QRadar, ArcSight, syslog CEF, syslog-leef

- **Waarschuwings groep** voor syslog-tekst berichten

- **alert_group** voor syslog-objecten

Deze velden moeten worden geconfigureerd in de partner oplossing om de naam van de waarschuwings groep weer te geven. Als er geen waarschuwing is gekoppeld aan een waarschuwings groep, wordt in het veld in de partner oplossing **n.v.t.** weer gegeven.

### <a name="default-alert-groups"></a>Standaard waarschuwings groepen

De volgende waarschuwings groepen worden automatisch gedefinieerd:

- Gedrag van abnormale communicatie
- Aangepaste waarschuwingen
- Externe toegang
- Gedrag van abnormale HTTP-communicatie
- Detectie
- Opdrachten opnieuw starten en stoppen
- Verificatie
- Firmware wijzigen
- Scannen
- Gedrag voor onbevoegde communicatie
- Ongeldige opdrachten
- Sensor verkeer
- Afwijkingen van de band breedte
- Toegang tot het internet
- Vermoeden van malware
- Buffer overloop 
- Bewerkings fouten
- Vermoeden van schadelijke activiteiten
- Opdracht fouten
- Operationele problemen
- Configuratiewijzigingen
- Programmering

Waarschuwings groepen zijn vooraf gedefinieerd. Neem contact op met [Microsoft ondersteuning](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099)voor meer informatie over waarschuwingen die zijn gekoppeld aan waarschuwings groepen en over het maken van aangepaste waarschuwings groepen.

## <a name="customize-alert-rules"></a>Waarschuwings regels aanpassen

Gebruik aangepaste waarschuwings regels voor meer informatie over de activiteit van belang voor u. 

U kunt aangepaste waarschuwings regels toevoegen op basis van:

- Een categorie, bijvoorbeeld een protocol, poort of bestand.
- Bron-en doel adressen
- Een voor waarde op basis van de gekozen categorie, bijvoorbeeld een functie die is gekoppeld aan een protocol, een bestands naam, poort of transport nummer.
- Een voor waarde op basis van de referentie datum en tijd, bijvoorbeeld als er een detectie is uitgevoerd op een specifieke dag of een bepaald deel van de dag.

Als de sensor de activiteit detecteert die in de regel wordt beschreven, wordt de waarschuwing verzonden.
informatie die door afzonderlijke Sens oren wordt gedetecteerd. U kunt bijvoorbeeld een regel definiëren waarmee wordt aangegeven dat een sensor een waarschuwing moet activeren op basis van een bron-IP, doel-IP of opdracht (binnen een Protocol). Wanneer de sensor het verkeer detecteert dat in de regel is gedefinieerd, wordt er een waarschuwing of gebeurtenis gegenereerd.

U kunt ook waarschuwings regel acties gebruiken om Defender voor IoT te instrueren tot:

- Gebruikers toestaan om toegang te krijgen tot het PCAP-bestand vanuit de waarschuwing.
- Wijs een ernst van de waarschuwing toe.
- Genereer een gebeurtenis in plaats van een waarschuwing. De gedetecteerde informatie wordt weer gegeven in de tijd lijn van de gebeurtenis.

:::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="Scherm opname waarin een door de gebruiker gedefinieerde regel wordt weer gegeven.":::

Het waarschuwings bericht geeft aan dat een door de gebruiker gedefinieerde regel de waarschuwing heeft geactiveerd.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Scherm opname waarin aangepaste waarschuwingen worden weer gegeven.":::

Een aangepaste waarschuwings regel maken:

1. Selecteer **aangepaste waarschuwingen** in het menu aan de zijkant van een sensor.
1. Selecteer het plus teken ( **+** ) om een regel te maken.
1. Definieer een regel naam.
1. Selecteer een categorie of protocol in het deel venster **Categorieën** .
1. Definieer een specifiek bron-en doel-IP-of MAC-adres of kies een wille keurig adres.
1. Definieer een of meer regel voorwaarden. Er kunnen twee categorieën voor waarden worden gemaakt:
    - Voor waarden op basis van unieke waarden die zijn gekoppeld aan de geselecteerde categorie. Selecteer toevoegen en definieer de waarden.
    - Voor waarden gebaseerd op de wanneer de activiteit is gedetecteerd. Selecteer in de sectie detecties een tijds periode en dag waarop de detectie moet plaatsvinden om de waarschuwing te kunnen verzenden. U kunt ervoor kiezen om de waarschuwing te verzenden als de activiteit op elk gewenst moment, tijdens of na het werk uur wordt gedetecteerd. Gebruik de optie werk uren definiëren om Defender te instrueren voor IoT-werk uren voor uw organisatie.
1. Regel acties definiëren: 
    - Geef aan of de regel een **waarschuwing** of **gebeurtenis** activeert.
    - Wijs een Ernst niveau toe aan de waarschuwing.
    - Geef aan of de waarschuwing een PCAP-bestand zal bevatten.
1. Selecteer **Opslaan**.

De regel wordt toegevoegd aan de lijst met **aangepaste waarschuwings regels** , waar u de para meters voor de basis regel kunt controleren, de laatste keer dat de regel is geactiveerd, en meer. U kunt de regel ook in de lijst inschakelen en uitschakelen.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Scherm afbeelding van een door de gebruiker toegevoegde, aangepaste regel.":::

### <a name="see-also"></a>Zie ook

[De informatie in waarschuwingen weergeven](how-to-view-information-provided-in-alerts.md)

[De waarschuwingsgebeurtenis beheren](how-to-manage-the-alert-event.md)
