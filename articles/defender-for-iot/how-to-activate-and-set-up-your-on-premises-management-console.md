---
title: Uw on-premises beheerconsole activeren en instellen
description: Activering en installatie van de beheer console zorgen ervoor dat Sens oren zijn geregistreerd bij Azure en informatie verzenden naar de on-premises beheer console, en dat de on-premises beheer console Beheer taken uitvoert op verbonden Sens oren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d326a90ffb957604dba74982d8983acedc6ab85d
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522577"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Uw on-premises beheerconsole activeren en instellen 

Activering en installatie van de on-premises beheer console zorgt ervoor dat:

- Netwerk apparaten die u bewaakt via verbonden Sens oren, worden geregistreerd bij een Azure-account.

- Sens oren verzenden gegevens naar de on-premises beheer console.

- De on-premises beheer console voert beheer taken uit op verbonden Sens oren.

- U hebt een SSL-certificaat geïnstalleerd.

## <a name="sign-in-for-the-first-time"></a>Voor de eerste keer aanmelden

Aanmelden bij de beheer console:

- Open een webbrowser en voer het IP-adres en het wacht woord in dat u hebt ontvangen voor de on-premises beheer console tijdens de installatie van het systeem. Als u uw wacht woord bent verg eten, selecteert u **wacht woord herstellen** en raadpleegt u [wachtwoord herstel](how-to-manage-the-on-premises-management-console.md#password-recovery).

## <a name="upload-an-activation-file"></a>Een activerings bestand uploaden

Na de eerste keer aanmelden, activeert u de on-premises beheer console door een activerings bestand te downloaden van de pagina met **prijzen** van de Azure Defender voor IOT-Portal. Dit bestand bevat de geaggregeerde toegewezen apparaten die tijdens het voorbereidings proces zijn gedefinieerd. **Doorgevoerde apparaten** geeft het aantal apparaten aan dat Defender voor IOT bewaakt per abonnement.

Een activerings bestand uploaden:

1. Ga naar de pagina met **prijzen** voor Defender voor IOT.
1. Selecteer de **down load het activerings bestand voor het tabblad beheer console** . Het activerings bestand wordt gedownload.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Down load het activerings bestand.":::

1. Selecteer **systeem instellingen** in de beheer console.
1. Selecteer **Activering**.
1. Selecteer **een bestand kiezen** en selecteer het bestand dat u hebt opgeslagen.

Na de eerste activering overschrijdt het aantal bewaakte apparaten het aantal toegewezen apparaten dat tijdens de onboarding is gedefinieerd. Dit kan bijvoorbeeld gebeuren als u meer Sens oren verbindt met de beheer console. Als er een verschil is tussen het aantal bewaakte apparaten en het aantal toegewezen apparaten, wordt er een waarschuwing weer gegeven in de beheer console. Als dit het geval is, moet u een nieuw activerings bestand uploaden.

## <a name="set-up-a-certificate"></a>Een certificaat instellen

Na de installatie van de-beheer console wordt een lokaal zelfondertekend certificaat gegenereerd dat wordt gebruikt om toegang te krijgen tot de console. Wanneer een beheerder zich voor de eerste keer aanmeldt bij de beheer console, wordt de gebruiker gevraagd een SSL/TLS-certificaat in te voegen. 

Er zijn twee beveiligings niveaus beschikbaar:

- Voldoen aan specifieke vereisten voor certificaat en versleuteling die door uw organisatie zijn aangevraagd door het door de certificerings instantie ondertekende certificaat te uploaden.
- Validatie toestaan tussen de beheer console en verbonden Sens oren. Validatie wordt geëvalueerd op basis van een certificaatintrekkingslijst en de verval datum van het certificaat. *Als de validatie mislukt, wordt de communicatie tussen de beheer console en de sensor gestopt en wordt er een validatie fout weer gegeven in de console.* Deze optie is standaard ingeschakeld na de installatie.  

De-console ondersteunt de volgende typen certificaten:

- Persoonlijke en bedrijfs sleutel infrastructuur (persoonlijke PKI)

- Open bare-sleutel infrastructuur (open bare PKI)

- Lokaal gegenereerd op het apparaat (lokaal zelf ondertekend) 

  > [!IMPORTANT]
  > U wordt aangeraden geen zelfondertekend certificaat te gebruiken. Het certificaat is niet beveiligd en moet alleen worden gebruikt voor test omgevingen. De eigenaar van het certificaat kan niet worden gevalideerd en de beveiliging van uw systeem kan niet worden gehandhaafd. Gebruik deze optie nooit voor productie netwerken.

Een certificaat uploaden:

1. Wanneer u wordt gevraagd na het aanmelden, definieert u een certificaat naam.
1. Upload de CRT-en Key-bestanden.
1. Voer een wachtwoordzin in en upload indien nodig een PEM-bestand.

Mogelijk moet u het scherm vernieuwen nadat u het door de certificerings instantie ondertekende certificaat hebt geüpload.

Validatie tussen de beheer console en verbonden Sens oren uitschakelen:

1. Selecteer **Next**.
1. Schakel de optie voor het inschakelen van validatie op het **hele systeem** uit.

Zie [Manage the on-premises Management Console](how-to-manage-the-on-premises-management-console.md)voor meer informatie over het uploaden van een nieuw certificaat, ondersteunde certificaat bestanden en verwante items.

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Sens oren verbinden met de on-premises beheer console

U moet ervoor zorgen dat Sens oren informatie verzenden naar de on-premises beheer console en dat de on-premises beheer console back-ups kan uitvoeren, waarschuwingen kunnen beheren en andere activiteiten op de Sens oren kan uitvoeren. Gebruik hiervoor de volgende procedures om te controleren of u een eerste verbinding maakt tussen Sens oren en de on-premises beheer console.

Er zijn twee opties beschikbaar om verbinding te maken tussen Azure Defender voor IoT Sens oren en de on-premises beheer console:

- Verbinding maken via de sensor console

- Verbinding maken met behulp van tunneling

Nadat u verbinding hebt gemaakt, moet u een site instellen met deze Sens oren.

### <a name="connect-sensors-from-the-sensor-console"></a>Sens oren verbinden via de sensor console

Voor het verbinden van specifieke Sens oren met de on-premises beheer console van de sensor console:

1. Selecteer in het linkerdeel venster van de sensor console de optie **systeem instellingen**.

2. Selecteer **verbinding met beheer**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="Scherm opname van het status venster van een on-premises beheer console, waarin niet-verbonden wordt weer gegeven.":::

3. Voer in het tekstvak **adres** het IP-adres in van de on-premises beheer console waarmee u verbinding wilt maken.

4. Selecteer **Verbinding maken**. De status wordt gewijzigd:

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="Scherm opname van het status venster van een on-premises beheer console, met verbonden.":::

### <a name="connect-sensors-by-using-tunneling"></a>Sens oren verbinden met tunneling

Schakel een beveiligde tunnel verbinding in tussen de organisatorische Sens oren en de on-premises beheer console. Deze installatie omzeilt de interactie met de firewall van de organisatie, waardoor de kwets baarheid wordt gereduceerd.

Met tunneling kunt u verbinding maken met de on-premises beheer console vanaf het IP-adres en één poort (dat wil zeggen, 9000) op elke sensor.

Tunneling instellen op de on-premises beheer console:

- Meld u aan bij de on-premises beheer console en voer de volgende opdrachten uit:

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

Tunneling op de sensor instellen:

1. Open TCP-poort 9000 op de sensor (netwerk. eigenschappen) hand matig. Als de poort niet open is, wordt de verbinding door de sensor van de on-premises beheer console geweigerd.

2. Meld u aan bij elke sensor en voer de volgende opdrachten uit:

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Een site instellen

Het standaard overzicht van de onderneming biedt een overzicht van uw apparaten op basis van verschillende niveaus van geografische locaties.

De weer gave van uw apparaten is mogelijk vereist wanneer de organisatie structuur en de gebruikers machtigingen complex zijn. In dergelijke gevallen kan het instellen van een site worden bepaald door een algemene organisatie structuur, naast de standaard site-of zone structuur.

Ter ondersteuning van deze omgeving moet u een wereld wijde bedrijfs topologie maken die is gebaseerd op de bedrijfs eenheden, regio's, sites en zones van uw organisatie. U moet ook gebruikers toegangs machtigingen voor deze entiteiten definiëren met behulp van toegangs groepen.

Met toegangs groepen kunt u beter bepalen waar gebruikers apparaten beheren en analyseren in het Defender voor IoT-platform.

### <a name="how-it-works"></a>Uitleg

Voor elke site kunt u een bedrijfs eenheid en een regio definiëren. Vervolgens kunt u zones toevoegen. Dit zijn logische entiteiten in uw netwerk. 

Voor elke zone moet u ten minste één sensor toewijzen. Het model met vijf niveaus biedt de flexibiliteit en granulatie die vereist zijn voor het leveren van het beveiligings systeem dat de structuur van uw organisatie weerspiegelt.

U kunt uw sites rechtstreeks bewerken vanuit een van de kaart weergaven. Wanneer u een site opent vanuit een kaart weergave, wordt het aantal openstaande waarschuwingen weer gegeven naast elke zone.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Scherm opname van een on-premises beheer console kaart met Berlijn data overlay.":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Diagram met Sens oren en regionale relatie.":::

Een site instellen:

1. Voeg nieuwe bedrijfs eenheden toe om de logische structuur van uw organisatie weer te geven.

2. Nieuwe regio's toevoegen aan de regio's van uw organisatie.

3. Een site toevoegen.

4. Zones toevoegen aan een site.

5. Verbind de Sens oren.

6. Wijs sensor toe aan site zones.

Business Units toevoegen:

1. Selecteer in de Enter prise-weer gave **alle sites**  >  **beheer business units**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="Scherm opname van de weer gave bedrijfs eenheden beheren.":::

2. Voer de naam van de nieuwe bedrijfs eenheid in en selecteer **toevoegen**.

Een nieuwe regio toevoegen:

1. Selecteer in de Enter prise-weer gave **alle regio's**  >  **beheer regio's**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="Scherm opname van de weer gave regio's beheren.":::

2. Voer de naam van de nieuwe regio in en selecteer **toevoegen**.

Een nieuwe site toevoegen:

1. Selecteer in de weer gave onderneming :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: op de bovenste balk. De cursor wordt weer gegeven als een plus teken ( **+** ).

2. Plaats de **+** op de locatie van de nieuwe site en selecteer deze. Het dialoog venster **nieuwe site maken** wordt geopend.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Scherm opname van de weer gave nieuwe site maken.":::

3. Definieer de naam en het fysieke adres voor de nieuwe site en selecteer **Opslaan**. De nieuwe site wordt weer gegeven op de site kaart.

Een site verwijderen:

1. Selecteer in het venster **site beheer** een :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: van de balken die de site naam bevat en selecteer vervolgens **site verwijderen**. Het bevestigings venster wordt weer gegeven en u kunt controleren of u de site wilt verwijderen.

2. Selecteer **Ja** in het bevestigings venster. De bevestiging wordt gesloten en het venster **site beheer** wordt weer gegeven zonder de site die u hebt verwijderd.

## <a name="create-enterprise-zones"></a>Ondernemings zones maken

Zones zijn logische entiteiten waarmee u apparaten binnen een site in groepen kunt verdelen op basis van verschillende kenmerken. U kunt bijvoorbeeld groepen maken voor productie lijnen, substations, site gebieden of typen apparaten. U kunt zones definiëren op basis van elk kenmerk dat geschikt is voor uw organisatie.

U configureert zones als onderdeel van het site configuratie proces.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Scherm opname van de weer gave site beheer zones.":::

In de volgende tabel worden de para meters in het venster **site beheer** beschreven.

| Parameter | Beschrijving |
|--|--|
| Name | De naam van de sensor. U kunt deze naam alleen wijzigen van de sensor. Zie de gebruikers handleiding voor Defender voor IoT voor meer informatie. |
| IP | Het IP-adres van de sensor. |
| Versie | De sensor versie. |
| Connectiviteit | De verbindings status van de sensor. De status kan worden **verbonden** of **verbroken**. |
| Laatste upgrade | De datum van de laatste upgrade. |
| Voortgang van de upgrade | Op de voortgangs balk wordt de status van het upgrade proces als volgt weer gegeven:<br />-Pakket uploaden<br />-Installatie voorbereiden<br />-Processen stoppen<br />-Back-ups maken van gegevens<br />Maken van moment opname<br />-Configuratie bijwerken<br />-Afhankelijkheden worden bijgewerkt<br />-Bibliotheken bijwerken<br />-Data bases bijwerken<br />-Start processen<br />-Sanity van het systeem valideren<br />-Validatie geslaagd<br />-Geslaagd<br />-Fout<br />-Upgrade gestart<br />-Installatie startenogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >Raadpleeg [Microsoft ondersteuning](https://support.microsoft.com/) voor hulp voor meer informatie over het uitvoeren van een upgrade. |
| Apparaten | Het aantal apparaten dat door de sensor wordt gecontroleerd. |
| Waarschuwingen | Het aantal waarschuwingen op de sensor. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Hiermee kunt u een sensor aan zones toewijzen. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Hiermee kunt u een niet-verbonden sensor van de site verwijderen. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Hiermee wordt aangegeven hoeveel Sens oren momenteel zijn verbonden met de zone. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Hiermee wordt aangegeven hoeveel activa er momenteel met de zone zijn verbonden. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | Hiermee wordt het aantal waarschuwingen aangegeven dat is verzonden door Sens oren die zijn toegewezen aan de zone. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Hiermee wordt de toewijzing van Sens oren uit zones opheffen. |

Een zone toevoegen aan een site:

1. Selecteer in het venster **site beheer** een :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: van de balken die de site naam bevat en selecteer vervolgens **zone toevoegen**. Het dialoog venster **nieuwe zone maken** wordt weer gegeven.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Scherm opname van de weer gave nieuwe zone maken.":::

2. Voer de zone naam in.

3. Voer een beschrijving in voor de nieuwe zone waarmee duidelijk de kenmerken worden aangegeven die u hebt gebruikt om de site te verdelen in zones.

4. Selecteer **SAVE** (Opslaan). De nieuwe zone wordt weer gegeven in het venster **site beheer** onder de site waartoe deze zone behoort.

Een zone bewerken:

1. Selecteer in het venster **site beheer** in :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: de balk die de zone naam bevat en selecteer vervolgens **zone bewerken**. Het dialoog venster **zone bewerken** wordt weer gegeven.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Scherm afbeelding die het dialoog venster zone bewerken weergeeft.":::

2. Bewerk de zone parameters en selecteer **Opslaan**.

Een zone verwijderen:

1. In het venster **site beheer** selecteert u in :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: de balk die de zone naam bevat en selecteert u **zone verwijderen**.

2. Selecteer **Ja** in het bevestigings venster.

Filteren op basis van de connectiviteits status:

- Selecteer in de linkerbovenhoek :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: naast **verbinding** en selecteer vervolgens een van de volgende opties:

  - **Alle**: geeft alle Sens oren die rapporteren aan deze on-premises beheer console.

  - **Verbonden**: presenteert alleen verbonden Sens oren.

  - Niet **verbonden**: presenteert alleen niet-verbonden Sens oren.

Filteren op basis van de upgrade status:

- Selecteer in de linkerbovenhoek :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: naast **upgrade status** en selecteer een van de volgende opties:

  - **Alle**: geeft alle Sens oren die rapporteren aan deze on-premises beheer console.

  - **Geldig**: presenteert Sens oren met een geldige upgrade status.

  - Wordt **uitgevoerd**: presenteert Sens oren die in het proces van de upgrade zijn.

  - **Mislukt**: geeft Sens oren weer waarvan het upgrade proces is mislukt.

## <a name="assign-sensors-to-zones"></a>Sens oren toewijzen aan zones

Voor elke zone moet u Sens oren toewijzen die de analyse van het lokale verkeer en waarschuwingen uitvoeren. U kunt alleen Sens oren toewijzen die zijn verbonden met de on-premises beheer console.

Een sensor toewijzen:

1. Selecteer **site beheer**. De niet-toegewezen Sens oren worden weer gegeven in de linkerbovenhoek van het dialoog venster.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Scherm opname van de weer gave niet-toegewezen Sens oren.":::

2. Controleer of de **connectiviteits** status is verbonden. Als dat niet het geval is, raadpleegt [u Sens oren koppelen aan de on-premises beheer console](#connect-sensors-to-the-on-premises-management-console) voor meer informatie over verbinding maken. 

3. Selecteer :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: voor de sensor die u wilt toewijzen.

4. Selecteer in het dialoog venster **sensor toewijzen** de bedrijfs eenheid, de regio, de site en de zone die u wilt toewijzen.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Scherm opname van de weer gave sensor toewijzen.":::

5. Selecteer **toewijzen**.

De toewijzing van een sensor opheffen en verwijderen:

1. Koppel de sensor los van de on-premises beheer console. Zie [Sens oren verbinden met de on-premises beheer console](#connect-sensors-to-the-on-premises-management-console) voor meer informatie.

2. Selecteer de sensor in het venster **site beheer** en selecteer deze :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: . De sensor wordt na enkele ogen blikken weer gegeven in de lijst met niet-toegewezen Sens oren.

3. Als u de niet-toegewezen sensor van de site wilt verwijderen, selecteert u de sensor in de lijst met niet-toegewezen Sens oren en selecteert u :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: .

## <a name="next-steps"></a>Volgende stappen

[Problemen met de sensor en on-premises beheerconsole oplossen](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
