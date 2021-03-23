---
title: Waarschuwingsinformatie doorsturen
description: U kunt waarschuwings gegevens verzenden naar partner systemen door te werken met doorstuur regels.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: bc405f7d4837bf81d9cfcd859d562b7152cfc54b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778794"
---
# <a name="forward-alert-information"></a>Waarschuwingsinformatie doorsturen

U kunt waarschuwings gegevens verzenden naar partners die zijn geïntegreerd met Azure Defender voor IoT, op syslog-servers, op e-mail adressen en meer. Als u met regels voor door sturen werkt, kunt u snel waarschuwings gegevens leveren aan beveiligings belanghebbenden.  

Syslog en andere standaard acties voor door sturen worden met uw systeem bezorgd. Meer doorstuur acties kunnen beschikbaar worden wanneer u integreert met partner leveranciers, zoals Microsoft Azure Sentinel, ServiceNow of Splunk.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Waarschuwings gegevens.":::

Defender voor IoT-beheerders hebben toestemming om doorstuur regels te gebruiken.

## <a name="about-forwarded-alert-information"></a>Informatie over doorgestuurde waarschuwingen

Waarschuwingen bieden informatie over een uitgebreid scala aan beveiligings-en operationele gebeurtenissen. Bijvoorbeeld:

  - Datum en tijd van de waarschuwing

  - Engine die de gebeurtenis heeft gedetecteerd

  - Waarschuwings titel en beschrijvende bericht

  - Ernst van waarschuwing

  - Bron-en doel naam en IP-adres

  - Verdacht verkeer gedetecteerd

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="De adres scan is gedetecteerd.":::

Relevante informatie wordt verzonden naar partner systemen wanneer er regels voor door sturen worden gemaakt.

## <a name="create-forwarding-rules"></a>Doorstuur regels maken

Een nieuwe doorstuur regel maken:

1. Selecteer **door sturen** in het menu aan de zijkant.

   :: Image Type = "content" source = "media/procedures-to-work-with-Alerts-sensor/create-forwarding-rule-screen.png" Alt-Text = "een doorstuur regel pictogram maken.":::

2. Selecteer **doorstuur regel maken**.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="Maak een nieuwe doorstuur regel.":::

3. Voer de naam van de doorstuur regel in.

### <a name="forwarding-rule-criteria"></a>Regel criteria voor door sturen 

Criteria definiëren om een doorstuur regel te activeren. Bij het werken met criteria voor doorstuur regels kunt u het volume van de gegevens die van de sensor worden verzonden, lokaliseren en beheren naar externe systemen. De volgende opties zijn beschikbaar:

**Protocollen**: Activeer alleen de doorstuur regel als het verkeer dat is gedetecteerd, meer dan een specifiek protocol heeft uitgevoerd. Selecteer de vereiste protocollen in de vervolg keuzelijst of kies alle.

**Engines**: Selecteer de vereiste engines of kies ze allemaal. Waarschuwingen van geselecteerde engines worden verzonden.

**Ernst niveaus**: dit is het minimale incident dat moet worden door gestuurd, in termen van Ernst niveau. Als u bijvoorbeeld **secundaire**, kleine waarschuwingen en een waarschuwing boven dit Ernst niveau selecteert, worden deze doorgestuurd. Niveaus zijn vooraf gedefinieerd.

### <a name="forwarding-rule-actions"></a>Regel acties door sturen

Door regel acties door sturen wordt de sensor geïnstrueerd waarschuwings gegevens door te sturen naar partner leveranciers of-servers. U kunt voor elke doorstuur regel meerdere acties maken.

Naast de doorstuur acties die met uw systeem worden geleverd, kunnen andere acties beschikbaar worden wanneer u integreert met partner leveranciers. 

#### <a name="email-address-action"></a>E-mail adres actie

Verzend een e-mail met de informatie over de waarschuwing. U kunt één e-mail adres per regel invoeren.

E-mail adres voor de doorstuur regel definiëren:

1. Voer één e-mail adres in. Als er meer dan één e-mail bericht moet worden verzonden, maakt u een andere actie.

2. Voer de tijd zone voor het tijds tempel in voor de waarschuwings detectie op de SIEM.

3. Selecteer **Indienen**.

#### <a name="syslog-server-actions"></a>Syslog-server acties

De volgende indelingen worden ondersteund:

- Sms-berichten

- CEF-berichten

- LEEF-berichten

- Object berichten

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="Acties voor doorstuur regel maken.":::

Voer de volgende parameters in:

- Naam van syslog-host en-poort.

- Protocol TCP en UDP.

- Tijd zone voor het tijds tempel voor de waarschuwings detectie op de SIEM.

- TLS-versleutelings certificaat bestand en sleutel bestand voor CEF-servers (optioneel).
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="Configureer uw versleuteling voor uw doorstuur regel.":::

| Uitvoer velden van syslog-tekst berichten | Beschrijving |
|--|--|
| Datum en tijd | De datum en tijd waarop de syslog-server computer de informatie heeft ontvangen. |
| Prioriteit | Gebruiker. waarschuwing |
| Hostnaam | IP-adres van sensor |
| Protocol | TCP of UDP |
| Bericht | Sensor: de naam van de sensor.<br /> Waarschuwing: de titel van de waarschuwing.<br /> Type: het type van de waarschuwing. Kan schending van het **protocol**, schending van het **beleid**, **malware**, **afwijkend** of **operationeel** zijn.<br /> Ernst: de ernst van de waarschuwing. Kan **waarschuwing**, **secundair**, **primair** of **kritiek** zijn.<br /> Bron: de naam van het bron apparaat.<br /> Bron-IP: het IP-adres van het bron apparaat.<br /> Doel: de naam van het doel apparaat.<br /> Doel-IP: het IP-adres van het doel apparaat.<br /> Bericht: het bericht van de waarschuwing.<br /> Waarschuwings groep: de waarschuwings groep die is gekoppeld aan de waarschuwing. |


| Uitvoer van syslog-object | Beschrijving |
|--|--|
| Datum en tijd |   De datum en tijd waarop de syslog-server computer de informatie heeft ontvangen. |  
| Prioriteit |    Gebruiker. waarschuwing | 
| Hostnaam |    Sensor-IP | 
| Bericht | Sensor naam: de naam van het apparaat. <br /> Waarschuwings tijd: de tijd waarop de waarschuwing is gedetecteerd: kan variëren van de tijd van de syslog-server machine en is afhankelijk van de tijd zone configuratie van de doorstuur regel. <br /> Titel van waarschuwing: de titel van de waarschuwing. <br /> Waarschuwings bericht: het bericht van de waarschuwing. <br /> Ernst van waarschuwing: de ernst van de waarschuwing: **waarschuwing**, **secundair**, **primair** of **kritiek**. <br /> Waarschuwings type: **schending** van het Protocol, **schending van beleid**, **malware**, **afwijkend** of **operationeel**. <br /> Protocol: het Protocol van de waarschuwing.  <br /> **Source_MAC**: IP-adres, naam, leverancier of besturings systeem van het bron apparaat. <br /> Destination_MAC: IP-adres, naam, leverancier of besturings systeem van de bestemming. Als er gegevens ontbreken, is de waarde **N/A**. <br /> alert_group: de waarschuwings groep die aan de waarschuwing is gekoppeld. |


| Uitvoer indeling syslog CEF | Beschrijving |
|--|--|
| Datum en tijd | De datum en tijd waarop de syslog-server computer de informatie heeft ontvangen. |
| Prioriteit | Gebruiker. waarschuwing | 
| Hostnaam | IP-adres van sensor |
| Bericht | CEF: 0 <br />Azure Defender voor IoT <br />Sensor naam: de naam van het sensor toestel. <br />Sensor versie <br />Titel van waarschuwing: de titel van de waarschuwing. <br />msg: het bericht van de waarschuwing. <br />Protocol: het Protocol van de waarschuwing. <br />Ernst: **waarschuwing**, **secundair**, **primair** of **kritiek**. <br />type: **schending** van het Protocol, **schending van beleid**, **malware**, **afwijkend** of **operationeel**. <br /> starten: het tijdstip waarop de waarschuwing is gedetecteerd. <br />Kan variëren van de tijd van de syslog-server machine en is afhankelijk van de tijd zone configuratie van de doorstuur regel. <br />src_ip: IP-adres van het bron apparaat.  <br />dst_ip: IP-adres van het doel apparaat.<br />Kat: de waarschuwings groep die is gekoppeld aan de waarschuwing.  |

| Uitvoer indeling syslog LEEF | Beschrijving |
|--|--|
| Datum en tijd |   De datum en tijd waarop de syslog-server computer de informatie heeft ontvangen. |  
| Prioriteit |    Gebruiker. waarschuwing | 
| Hostnaam |    Sensor-IP |
| Bericht | Sensor naam: de naam van het Azure Defender voor IoT-apparaat. <br />LEEF: 1.0 <br />Azure Defender voor IoT <br />Sensoren  <br />Sensor versie <br />Waarschuwing voor Azure Defender voor IoT <br />Titel: de titel van de waarschuwing. <br />msg: het bericht van de waarschuwing. <br />Protocol: het Protocol van de waarschuwing.<br />Ernst: **waarschuwing**, **secundair**, **primair** of **kritiek**. <br />type: het type waarschuwing: schending van **het protocol**, **schending van beleid**, **malware**, **afwijkend** of **operationeel**. <br />starten: het tijdstip van de waarschuwing.Houd er rekening mee dat deze kan afwijken van de tijd van de syslog-server machine. (Dit is afhankelijk van de configuratie van de tijd zone.) <br />src_ip: IP-adres van het bron apparaat.<br />dst_ip: IP-adres van het doel apparaat. <br />Kat: de waarschuwings groep die is gekoppeld aan de waarschuwing. |

Nadat u alle informatie hebt ingevoerd, selecteert u **verzenden**.

#### <a name="netwitness-action"></a>Netwitness-actie

Waarschuwings gegevens naar een netwitness-server verzenden.

Door sturen van netwitness-para meters definiëren:

1. Voer de **hostnaam** en **poort** gegevens van de netwitness in.

2. Voer de tijd zone voor het tijds tempel in voor de waarschuwings detectie op de SIEM.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="Voeg een tijd zone toe aan uw doorstuur regel.":::

3. Selecteer **Indienen**.

#### <a name="integrated-vendor-actions"></a>Acties voor geïntegreerde leveranciers

Mogelijk hebt u uw systeem geïntegreerd met een beveiliging, Apparaatbeheer of een andere branche leverancier. Met deze integraties kunt u:

  - Waarschuwings gegevens verzenden.

  - Inventaris gegevens van het apparaat verzenden.

  - Communiceer met firewalls aan de leveranciers zijde.

Integraties helpen eerder gestuurde beveiligings oplossingen te bridgen, de zicht baarheid van het apparaat te verbeteren en de systeem-brede reactie te versnellen om zo snel mogelijk Risico's te verminderen.

Gebruik de sectie acties om de referenties en andere informatie in te voeren die vereist zijn om te communiceren met geïntegreerde leveranciers.

Raadpleeg de relevante artikelen over partner integratie voor meer informatie over het instellen van regels voor door sturen voor de integraties.

### <a name="test-forwarding-rules"></a>Regels voor door sturen

Test de verbinding tussen de sensor en de partner server die is gedefinieerd in de regels voor door sturen:

1. Selecteer de regel in het dialoog venster **regel voor door sturen** .

2. Schakel het selectie vakje **meer** in.

3. Selecteer **test bericht verzenden**.

4. Ga naar uw partner systeem om te controleren of de informatie die door de sensor is verzonden, is ontvangen.

### <a name="edit-and-delete-forwarding-rules"></a>Doorstuur regels bewerken en verwijderen 

Een doorstuur regel bewerken:

- Selecteer in het scherm **doorstuur regel** de optie **bewerken** onder de vervolg keuzelijst **meer** . Breng de gewenste wijzigingen aan en selecteer **verzenden**.

Een doorstuur regel verwijderen:

- Selecteer in het scherm **doorstuur regel** de optie **verwijderen** onder de vervolg keuzelijst **meer** . Selecteer **OK** in het dialoog venster **waarschuwing** .

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>Regels voor door sturen en uitsluitingen van waarschuwingen

De beheerder heeft mogelijk regels voor het uitsluiten van waarschuwingen gedefinieerd. Met deze regels kunnen beheerders nauw keurigere controle over waarschuwingen activeren door de sensor te instrueren om waarschuwings gebeurtenissen op basis van verschillende para meters te negeren. Deze para meters kunnen adressen van apparaten, waarschuwings namen of specifieke Sens oren bevatten.

Dit betekent dat de door u gedefinieerde regels voor door sturen mogelijk worden genegeerd op basis van de uitsluitings regels die de beheerder heeft gemaakt. Uitsluitings regels worden gedefinieerd in de on-premises beheer console.

## <a name="see-also"></a>Zie ook

[Waarschuwings werk stromen versnellen](how-to-accelerate-alert-incident-response.md)
