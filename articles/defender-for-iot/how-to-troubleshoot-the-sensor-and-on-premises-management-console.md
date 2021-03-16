---
title: Problemen met de sensor en on-premises beheerconsole oplossen
description: Los problemen met uw sensor en on-premises beheer console op om eventuele problemen op te lossen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/14/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: ba68bc3eee94689236792f0270d779357dffde9f
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103465773"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>Problemen met de sensor en on-premises beheerconsole oplossen

In dit artikel worden de belangrijkste hulpprogram ma's voor probleem oplossing voor de sensor en de on-premises beheer console beschreven. Naast de items die hier worden beschreven, kunt u de status van uw systeem op de volgende manieren controleren:

**Waarschuwingen**: er wordt een waarschuwing gemaakt wanneer de sensor interface die het verkeer bewaakt, niet beschikbaar is. 

**SNMP**: sensor status wordt bewaakt via SNMP. Azure Defender voor IoT reageert op SNMP-query's die zijn verzonden vanaf een geautoriseerde bewakings server. 

**Systeem meldingen**: wanneer een beheer console de sensor beheert, kunt u waarschuwingen sturen over mislukte sensor back-ups en niet-verbonden Sens oren.

## <a name="sensor-troubleshooting-tools"></a>Hulp middelen voor het oplossen van Sens oren

### <a name="investigate-password-failure-at-initial-sign-in"></a>Wachtwoord fout bij de eerste aanmelding onderzoeken

Wanneer u zich voor de eerste keer aanmeldt bij een vooraf geconfigureerde pijl sensor, moet u wachtwoord herstel uitvoeren.

Uw wacht woord herstellen:

1. Selecteer in het aanmeldings scherm van Defender voor IoT de optie  **wacht woord herstellen**. Het scherm voor **wachtwoord herstel** wordt geopend.

1. Selecteer **cyberx** of **support** en kopieer de unieke id.

1. Ga naar de Azure Portal en selecteer **sites en Sens oren**.  

1. Selecteer het tabblad **wacht woord van on-premises beheer console herstellen** .

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Selecteer de knop on-premises beheer herstellen om het herstel bestand te downloaden.":::

1. Voer de unieke id in die u hebt ontvangen op het scherm voor **wachtwoord herstel** en selecteer **herstellen**. Het `password_recovery.zip` bestand wordt gedownload.

    > [!NOTE]
    > Wijzig het wachtwoord herstel bestand niet. Het is een ondertekend bestand dat niet werkt als u ermee knoeit.

1. Selecteer **uploaden** in het scherm **wacht woord herstellen** . **Het venster bestand voor wachtwoord herstel uploaden** wordt geopend.

1. Selecteer **Bladeren** om het bestand te zoeken `password_recovery.zip` of sleep het `password_recovery.zip` naar het venster.

1. Selecteer **volgende** en uw gebruiker, en het door het systeem gegenereerde wacht woord voor uw beheer console worden weer gegeven.

    > [!NOTE]
    > Wanneer u zich voor de eerste keer aanmeldt bij een sensor of een on-premises beheer console, wordt deze gekoppeld aan het abonnement waarmee u de verbinding hebt gemaakt. Als u het wacht woord voor de gebruiker met Cyberx of ondersteuning opnieuw wilt instellen, moet u dat abonnement selecteren. Zie [wacht woorden opnieuw instellen](how-to-create-and-manage-users.md#resetting-passwords)voor meer informatie over het herstellen van een Cyber user-of-support-gebruikers wachtwoord.

### <a name="investigate-a-lack-of-traffic"></a>Een gebrek aan verkeer onderzoeken

Er wordt een indicator boven aan de console weer gegeven wanneer de sensor detecteert dat er geen verkeer is op een van de geconfigureerde poorten. Deze indicator is zichtbaar voor alle gebruikers.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="Scherm afbeelding van de waarschuwing dat er geen verkeer is gedetecteerd.":::
 
Wanneer dit bericht wordt weer gegeven, kunt u onderzoeken waar er geen verkeer is. Zorg ervoor dat de reeks kabel is verbonden en dat er geen wijzigingen in de bereik architectuur zijn aangebracht.  

Neem contact op met [Microsoft ondersteuning](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)voor ondersteuning en informatie over het oplossen van problemen.

### <a name="check-system-performance"></a>De systeem prestaties controleren 

Wanneer een nieuwe sensor wordt geïmplementeerd of als de sensor bijvoorbeeld langzaam werkt of geen waarschuwingen toont, kunt u de systeem prestaties controleren.

Systeem prestaties controleren:

1. Zorg ervoor dat in het dash board `PPS > 0` .

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="Scherm opname van een voor beeld van een dash board."::: 

1. Selecteer **apparaten** in het menu aan de zijkant.

1. Controleer in het venster **apparaten** of de apparaten worden gedetecteerd.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="Zorg ervoor dat de apparaten worden gedetecteerd.":::

1. Selecteer in het menu aan de zijkant **gegevens analyse**.

1. Selecteer in het venster **gegevens analyse** de optie **alle** en Genereer een rapport.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Genereer een nieuw rapport met behulp van gegevens analyse.":::

1. Zorg ervoor dat het rapport gegevens bevat.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Zorg ervoor dat het rapport gegevens bevat.":::

1. Selecteer in het menu aan de zijkant **Trends & statistieken**.

1. Selecteer in het venster **Trends &-statistieken** de optie **widget toevoegen**.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="Voeg een widget toe door deze te selecteren.":::

1. Voeg een widget toe en zorg ervoor dat de gegevens worden weer gegeven.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="Zorg ervoor dat het object gegevens weergeeft.":::

1. Selecteer in het menu aan de zijkant **waarschuwingen**. Het venster **waarschuwingen** wordt weer gegeven.

1. Controleer of de waarschuwingen zijn gemaakt.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="Controleer of er waarschuwingen zijn gemaakt.":::


### <a name="investigate-a-lack-of-expected-alerts"></a>Een gebrek aan verwachte waarschuwingen onderzoeken

Als in het venster **waarschuwingen** niet wordt aangegeven dat er een waarschuwing wordt weer gegeven, controleert u het volgende:

- Controleer of dezelfde waarschuwing al wordt weer gegeven in het venster **waarschuwingen** als een reactie op een ander beveiligings exemplaar. Als dit het geval is, en deze waarschuwing nog niet is verwerkt, wordt er door de sensor console geen nieuwe waarschuwing weer gegeven.

- Zorg ervoor dat u deze waarschuwing niet hebt uitgesloten door gebruik te maken van de regels voor het **uitsluiten van waarschuwingen** in de beheer console. 

### <a name="investigate-widgets-that-show-no-data"></a>Widgets onderzoeken die geen gegevens tonen

Wanneer de widgets in het venster **Trends & statistiek** geen gegevens weer geven, gaat u als volgt te werk:

- [Controleer de systeem prestaties](#check-system-performance).

- Zorg ervoor dat de instellingen voor tijd en regio correct zijn geconfigureerd en niet zijn ingesteld op een toekomstige tijd. 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>Een apparaattoewijzing onderzoeken die alleen broadcast apparaten weergeeft

Als apparaten die op de kaart worden weer gegeven niet met elkaar zijn verbonden, kan er iets mis zijn met de configuratie van de bereik poort. Dat wil zeggen dat u alleen uitzend apparaten ziet en geen unicast-verkeer.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="Bekijk uw omroep apparaten.":::

In dat geval moet u valideren dat alleen het broadcast verkeer kan worden weer geven. Vraag vervolgens de netwerk technicus om de configuratie van de bereik poort te herstellen, zodat u het unicast-verkeer kunt zien.

Controleren of u alleen het broadcast verkeer ziet:

- Maak in het scherm voor **gegevens analyse** een rapport met de optie **alle** . Controleer vervolgens of alleen broadcast-en multicast verkeer (en geen unicast-verkeer) wordt weer gegeven in het rapport.

Of

- Neem rechtstreeks vanuit de switch een PCAP op of Verbind een laptop met behulp van wireshark.

### <a name="connect-the-sensor-to-ntp"></a>De sensor verbinden met NTP

U kunt een zelfstandige sensor en een beheer console, met de Sens oren die hieraan zijn gerelateerd, configureren om verbinding te maken met NTP.

Een zelfstandige sensor verbinden met NTP:

- [Neem contact op met het ondersteunings team voor](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)ondersteuning.

Verbinding maken met een sensor die wordt beheerd door de beheer console voor NTP:

- De verbinding met NTP is geconfigureerd in de beheer console. Alle Sens oren die de beheer console beheert, krijgen de NTP-verbinding automatisch.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>Onderzoeken wanneer apparaten niet op de kaart worden weer gegeven of u hebt meerdere waarschuwingen met betrekking tot Internet

Soms worden ICS-apparaten geconfigureerd met externe IP-adressen. Deze ICS-apparaten worden niet weer gegeven op de kaart. In plaats van de apparaten wordt een Internet-Cloud weer gegeven op de kaart. De IP-adressen van deze apparaten zijn opgenomen in de Cloud installatie kopie.

Een andere vermelding van hetzelfde probleem doet zich voor wanneer er meerdere waarschuwingen met betrekking tot internet worden weer gegeven.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="Meerdere waarschuwingen met betrekking tot internet.":::

De configuratie herstellen:

1. Klik met de rechter muisknop op het pictogram Cloud op de apparaat kaart en selecteer **IP-adressen exporteren**. Kopieer de open bare bereiken die privé zijn en voeg ze toe aan de lijst met subnetten. Zie [subnets configureren](how-to-control-what-traffic-is-monitored.md#configure-subnets)voor meer informatie.

1. Genereer een nieuw rapport voor gegevens analyse voor Internet verbindingen.

1. In het rapport gegevens analyse selecteert :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: u de beheerders modus en verwijdert u de IP-adressen van uw ICS-apparaten.

### <a name="tweak-the-sensors-quality-of-service"></a>De kwaliteit van de service van de sensor aanpassen

Als u uw netwerk bronnen wilt opslaan, kunt u de band breedte van de interface beperken die de sensor voor dagelijkse procedures gebruikt.

Als u de band breedte van de interface wilt beperken, gebruikt u het `cyberx-xsense-limit-interface` cli-hulp programma dat moet worden uitgevoerd met sudo-machtigingen. Het hulp programma haalt de volgende argumenten op:

  - `* -i`: interfaces (bijvoorbeeld: eth0).

  - `* -l`: limiet (bijvoorbeeld: 30 kbit/1 Mbit). U kunt de volgende band breedte-eenheden gebruiken: kbps, Mbps, kbit, Mbit of bps.

  - `* -c`: Clear (om de bandbreedte beperking van de interface te wissen).

De kwaliteit van de service aanpassen:

1. Meld u aan bij de sensor-CLI als een Defender voor IoT-gebruiker en voer in `sudo cyberx-xsense-limit-interface-I eth0 -l value` .

   Bijvoorbeeld: `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > Voor een fysiek apparaat gebruikt u de em1-interface.

1. Als u de interface beperking wilt wissen, voert u in `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` .

## <a name="on-premises-management-console-troubleshooting-tools"></a>Hulp middelen voor het oplossen van problemen met on-premises beheer console

### <a name="investigate-a-lack-of-expected-alerts"></a>Een gebrek aan verwachte waarschuwingen onderzoeken

Als een verwachte waarschuwing niet wordt weer gegeven in het venster **waarschuwingen** , controleert u het volgende:

- Controleer of dezelfde waarschuwing al wordt weer gegeven in het venster **waarschuwingen** als een reactie op een ander beveiligings exemplaar. Als ja is ingesteld en deze waarschuwing nog niet is verwerkt, wordt er geen nieuwe waarschuwing weer gegeven.

- Controleer of u deze waarschuwing niet hebt uitgesloten door gebruik te maken van de regels voor het **uitsluiten van waarschuwingen** in de on-premises beheer console.  

### <a name="tweak-the-quality-of-service"></a>De kwaliteit van de service aanpassen

Als u uw netwerk bronnen wilt opslaan, kunt u het aantal waarschuwingen dat wordt verzonden naar externe systemen (zoals e-mail berichten of SIEM) beperken tijdens een synchronisatie bewerking tussen een apparaat en de on-premises beheer console.

De standaardwaarde is 50. Dit betekent dat er in één communicatie sessie tussen een apparaat en de on-premises beheer console niet meer dan 50 waarschuwingen voor externe systemen zijn. 

Als u het aantal waarschuwingen wilt beperken, gebruikt u de `notifications.max_number_to_report` eigenschap die beschikbaar is in `/var/cyberx/properties/management.properties` . U hoeft niet opnieuw op te starten nadat u deze eigenschap hebt gewijzigd.

De kwaliteit van de service aanpassen:

1. Meld u aan als Defender voor IoT-gebruiker. 

1. Controleer de standaard waarden:

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   De volgende standaard waarden worden weer gegeven:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Bewerk de standaard instellingen:

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. Bewerk de instellingen van de volgende regels:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Sla de wijzigingen op. U hoeft niet opnieuw op te starten.

## <a name="export-information-for-troubleshooting"></a>Informatie over het oplossen van problemen exporteren

Naast de hulpprogram ma's voor het controleren en analyseren van uw netwerk, kunt u informatie naar het ondersteunings team verzenden voor verdere onderzoek. Wanneer u Logboeken exporteert, genereert de sensor automatisch een eenmalig wacht woord (OTP), dat uniek is voor de geëxporteerde Logboeken in een afzonderlijk tekst bestand. 

Logboeken exporteren:

1. Selecteer **systeem instellingen** in het linkerdeel venster.

1. Selecteer **Logboeken exporteren**.

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="Een logboek exporteren naar systeem ondersteuning.":::

1. Voer in het vak **Bestands naam** de naam in van het bestand dat u wilt gebruiken voor het exporteren van het logboek. De standaard waarde is de huidige datum.

1. Selecteer de gegevens categorieën om te definiëren welke gegevens u wilt exporteren:  

    | Categorie exporteren | Beschrijving |
    |--|--|
    | **Logboeken van het besturings systeem** | Selecteer deze optie om informatie over de status van het besturings systeem op te halen. |
    | **Installatie/upgrade-logboeken** | Selecteer deze optie voor het onderzoeken van de configuratie parameters voor de installatie en de upgrade. |
    | **Systeem Sanity uitvoer** | Selecteer deze optie om de systeem prestaties te controleren. |
    | **Logboeken van de sectie** | Selecteer deze optie om een geavanceerde inspectie van protocol dissectie toe te staan. |
    | **Kernelgeheugendump van het besturings systeem** | Selecteer deze optie om uw Kernelgeheugendump te exporteren. Een kernelgeheugendump bevat alle geheugen die de kernel gebruikt op het moment van het probleem dat is opgetreden in deze kernel. De grootte van het dump bestand is kleiner dan de volledige geheugen dump. Normaal gesp roken is het dump bestand ongeveer een derde grootte van het fysieke geheugen op het systeem. |
    | **Logboeken door sturen** | Selecteer deze optie voor het onderzoeken van de regels voor door sturen. |
    | **SNMP-logboeken** | Selecteer deze optie om informatie over SNMP-status controle te ontvangen. |
    | **Kern toepassings logboeken** | Selecteer deze optie om gegevens over de configuratie en bewerking van de kern toepassing te exporteren. |
    | **Communicatie met CM-logboeken** | Selecteer deze optie als er doorlopende problemen of onderbrekingen van de verbinding met de beheer console zijn. |
    | **Logboeken voor webtoepassingen** | Selecteer deze optie om informatie op te halen over alle aanvragen die worden verzonden vanuit de webinterface van de toepassing. |
    | **Systeem back-up** | Selecteer deze optie als u een back-up van alle systeem gegevens wilt exporteren voor het onderzoeken van de exacte status van het systeem. |
    | **Statistieken voor dissectie** | Selecteer deze optie om geavanceerde inspectie van protocol statistieken toe te staan. |
    | **Database logboeken** | Selecteer deze optie als u logboeken van de systeem database wilt exporteren. Het onderzoeken van systeem logboeken helpt systeem problemen te identificeren. |
    | **Configuratie** | Selecteer deze optie om informatie over alle Configureer bare para meters te exporteren, zodat u zeker weet dat alles correct is geconfigureerd. |

1. Als u alle opties wilt selecteren, selecteert u **Alles selecteren** naast **Categorieën kiezen**.

1. Selecteer **Logboeken exporteren**.

De geëxporteerde logboeken worden toegevoegd aan de lijst **gearchiveerde logboeken** . Verzend de OTP naar het ondersteunings team in een afzonderlijk bericht en medium uit de geëxporteerde Logboeken. Het ondersteunings team kan alleen geëxporteerde logboeken ophalen met behulp van de unieke OTP die wordt gebruikt voor het versleutelen van de logboeken.

De lijst met gearchiveerde logboeken kan Maxi maal vijf items bevatten. Als het aantal items in de lijst buiten dat aantal valt, wordt het oudste item verwijderd.

## <a name="see-also"></a>Zie ook

- [Waarschuwingen weergeven](how-to-view-alerts.md)

- [SNMP MIB-bewaking instellen](how-to-set-up-snmp-mib-monitoring.md)

- [Verbindings gebeurtenissen van sensors begrijpen](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
