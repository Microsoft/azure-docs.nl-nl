---
title: Defender voor IoT-installatie
description: Meer informatie over het installeren van een sensor en de on-premises beheer console voor Azure Defender voor IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/2/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2bd994f14863715274e137bce2dd6873eeec1135
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208757"
---
# <a name="defender-for-iot-installation"></a>Defender voor IoT-installatie

In dit artikel wordt beschreven hoe u de volgende elementen van Azure Defender voor IoT installeert:

- **Sensor**: Defender voor IOT Sens oren verzamelt ICS-netwerk verkeer met behulp van passieve bewaking (zonder agent). Passieve en niet-storende Sens oren hebben geen invloed op de netwerken en apparaten van het OT en IoT. De sensor maakt verbinding met een SPANNe-poort of netwerk Tik en begint direct met het controleren van uw netwerk. Detecties worden weer gegeven in de sensor console. Daar kunt u ze weer geven, onderzoeken en analyseren in een netwerk kaart, inventaris van apparaten en een uitgebreid scala aan rapporten. Voor beelden zijn rapporten over risico analyse, query's voor gegevens analyse en aanvals vectoren. Meer informatie over sensor mogelijkheden vindt u in de [Defender voor IOT-sensor gebruikers handleiding (direct downloaden)](https://aka.ms/AzureDefenderforIoTUserGuide).

- **On-premises beheer console**: met behulp van de on-premises beheer console kunt u Apparaatbeheer, risico beheer en het beheer van beveiligings problemen uitvoeren. U kunt dit ook gebruiken om de bewaking van bedreigingen en reactie op incidenten in uw onderneming uit te voeren. Het biedt een uniforme weer gave van alle netwerk apparaten, de belangrijkste IoT en de risico indicatoren en waarschuwingen die zijn gedetecteerd in faciliteiten waar Sens oren worden geïmplementeerd. Gebruik de on-premises beheer console om Sens oren in gapped netwerken weer te geven en te beheren.

In dit artikel komen de volgende installatie gegevens aan bod:

  - **Hardware:** Details van het fysieke apparaat van Dell en HPE.

  - **Software:** Sensor-en on-premises beheer console-installatie van software.

  - **Virtuele apparaten:** Details van de virtuele machine en software-installatie.

Na de installatie verbindt u uw sensor met uw netwerk.

## <a name="about-defender-for-iot-appliances"></a>Over Defender voor IoT-apparaten 

De volgende secties bevatten informatie over Defender voor IoT-sensor toestellen en het apparaat voor de on-premises beheer console van Defender voor IoT.

### <a name="physical-appliances"></a>Fysieke apparaten

De Defender voor IoT-toestel sensor maakt verbinding met een SPANNe-poort of netwerk-Tik en begint onmiddellijk met het verzamelen van ICS-netwerk verkeer met behulp van passieve bewaking (zonder agent). Dit proces heeft geen invloed op de netwerken en apparaten, omdat het niet in het gegevenspad is geplaatst en niet actief is.

De volgende rack koppel apparaten zijn beschikbaar:

| **Implementatie type** | **Bedrijf** | **Enterprise** | **SMB** |  |
|--|--|--|--|--|
| **Model** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Bewakings poorten** | Maxi maal 15 RJ45 of 8 OPT | Maxi maal 9 RJ45 of 6 OPT | Maxi maal 8 RJ45 of 6 OPT | 4 RJ45 |
| **Maximale band \* breedte* _ | 3 GB/sec. | 1 GB/sec. | 1 GB/sec. | 100 MB/sec. |
| *Maxi maal aantal beveiligde apparaten** | 30.000 | 10.000 | 15.000 | 1000 |

* De maximale capaciteit van de band breedte kan variëren, afhankelijk van de protocol distributie.

### <a name="virtual-appliances"></a>Virtuele apparaten

De volgende virtuele apparaten zijn beschikbaar:

| **Implementatie type** | **Enterprise** | **SMB** | **Lijn** |
|--|--|--|--|
| **Beschrijving** | Virtueel apparaat voor bedrijfs implementaties | Virtuele apparaten voor SMB-implementaties | Virtueel apparaat voor regel implementaties |
| **Maximale band \* breedte* _ | 150 MB/sec. | 15 MB/sec. | 3 MB/sec. |
| *Maxi maal aantal beveiligde apparaten** | 3000 | 300 | 100 |
| **Implementatie type** | Onderneming | SMB | Lijn |
| **Beschrijving** | Virtueel apparaat voor bedrijfs implementaties | Virtuele apparaten voor SMB-implementaties | Virtueel apparaat voor regel implementaties |

* De maximale capaciteit van de band breedte kan variëren, afhankelijk van de protocol distributie.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Hardwarespecificaties voor de on-premises beheer console

 | Item | Beschrijving |
 |----|--|
 **Beschrijving** | In een architectuur met meerdere lagen biedt de on-premises beheer console inzicht in en controle over geografisch gedistribueerde sites. Het wordt geïntegreerd met SOC Security-stacks, waaronder Siem's, ticketing Systems, de volgende generatie firewalls, beveiligde RAS-platformen en de Defender voor IoT ICS-sandbox voor externe toegang. |
 **Implementatie type** | Onderneming |
 **Type apparaat**  | Dell R340, VM |
 **Aantal beheerde Sens oren** | Onbeperkt |

## <a name="prepare-for-the-installation"></a>Voorbereiden op de installatie

### <a name="access-the-iso-installation-image"></a>Toegang tot de ISO-installatie kopie

De installatie kopie is toegankelijk vanuit de Defender voor IoT-Portal.

Voor toegang tot het bestand:

1. Meld u aan bij uw Defender voor IoT-account.

2. Ga naar de pagina **netwerk sensor** of **on-premises beheer console** en selecteer een versie die u wilt downloaden.

### <a name="install-from-dvd"></a>Installeren vanaf DVD

Zorg ervoor dat u voor de installatie het volgende hebt:

- Een draagbaar DVD-station met de USB-connector.

- Een ISO-installatie kopie.

Installeren:

1. Brand de installatie kopie op een DVD of bereid een schijf voor op een sleutel. Sluit een draagbaar DVD-station aan op uw computer, klik met de rechter muisknop op de ISO-installatie kopie en selecteer **naar schijf branden**.

1. Verbind de DVD of schijf met een sleutel en configureer het apparaat om op te starten vanaf een DVD of schijf in een sleutel.

### <a name="install-from-disk-on-a-key"></a>Installeren vanaf schijf op een sleutel

Zorg ervoor dat u voor de installatie het volgende hebt:

  - Rufus is geïnstalleerd.
  
  - Een schijf met een sleutel met USB-versie 3,0 en hoger. De minimale grootte is 4 GB.

  - Een ISO-installatie kopie bestand.

De schijf op een sleutel wordt in dit proces gewist.

Een schijf voorbereiden op een sleutel:

1. Voer Rufus uit en selecteer **sensor ISO**.

2. Verbind de schijf met een sleutel met het voor paneel.

3. Stel het BIOS van de server in op opstarten vanaf de USB.

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL-installatie

Voordat u de software op het Dell-apparaat installeert, moet u de BIOS-configuratie van het apparaat aanpassen:

  - Het [Dell PowerEdge R340-voor paneel](#dell-poweredge-r340-front-panel) en [het Dell PowerEdge R340-scherm](#dell-poweredge-r340-back-panel) bevat de beschrijving van de voor-en achterkant-panels, samen met de informatie die vereist is voor de installatie, zoals Stuur Programma's en poorten.

  - De [BIOS-configuratie van Dell](#dell-bios-configuration) biedt informatie over het maken van verbinding met de Dell-interface voor het Apparaatbeheer van apparaten en het configureren van het BIOS.

  - [Software-installatie (Dell R340)](#software-installation-dell-r340) beschrijft de procedure die vereist is voor het installeren van de Defender voor IOT-sensor software.

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL-vereisten 

Voor de installatie van het Dell PowerEdge R340XL-apparaat hebt u het volgende nodig:

- Enter prise-licentie voor de Dell Remote Access Controller (iDrac)

- BIOS-configuratie-XML

- Server firmware versies:

  - BIOS-versie 2.1.6

  - iDrac-versie 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340-voor paneel

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340-voor paneel.":::

 1. Control Panel links 
 2. Optisch station (optioneel) 
 3. Rechts van het configuratie scherm 
 4. Gegevens label 
 5. Aandrijfeenheden  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340-panel

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340-back-uppaneel.":::

1. Seriële poort 
2. NIC-poort (GB 1) 
3. NIC-poort (GB 1) 
4. PCIe voor halve hoogte 
5. Sleuf voor PCIe-uitbreidings kaart op volledige hoogte 
6. Voedings eenheid 1 
7. Voedings eenheid 2 
8. Systeem identificatie 
9. Knop voor kabel poort van systeem status indicator (CMA) 
10. USB 3,0-poort (2) 
11. iDRAC9 toegewezen netwerk poort 
12. VGA-poort 

### <a name="dell-bios-configuration"></a>BIOS-configuratie van Dell

Dell BIOS-configuratie is vereist om het Dell-apparaat aan te passen om met de software te werken.

De BIOS-configuratie wordt uitgevoerd via een vooraf gedefinieerde configuratie. Het bestand is toegankelijk vanuit het [Help Center](https://help.cyberx-labs.com/).

Importeer het configuratie bestand naar het Dell-apparaat. Voordat u het configuratie bestand gebruikt, moet u de communicatie tussen het apparaat van Dell en de beheer computer tot stand brengen.

Het Dell-apparaat wordt beheerd door een geïntegreerde iDRAC met een levenscyclus controller (LC). De krediet brief is inge sloten in elke Dell PowerEdge-server en biedt functionaliteit waarmee u uw Dell PowerEdge-apparaten kunt implementeren, bijwerken, bewaken en onderhouden.

Om de communicatie tussen het apparaat van Dell en de beheer computer tot stand te brengen, moet u het IP-adres van de iDRAC en het IP-adres van de beheer computer definiëren op hetzelfde subnet.

Wanneer de verbinding tot stand is gebracht, kan het BIOS worden geconfigureerd.

Dell-BIOS configureren:

1. [Het IP-adres van iDRAC configureren](#configure-idrac-ip-address)

2. [Het BIOS-configuratie bestand importeren](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>IP-adres van iDRAC configureren

1. Schakel de sensor in.

2. Als het besturings systeem al is geïnstalleerd, selecteert u de toets F2 om de BIOS-configuratie in te voeren.

3. Selecteer **iDRAC-instellingen**.

4. Selecteer **netwerk**.

   > [!NOTE]
   > Tijdens de installatie moet u het standaard iDRAC-IP-adres en-wacht woord configureren dat wordt vermeld in de volgende stappen. Na de installatie wijzigt u deze definities.

5. Wijzig het statische IPv4-adres in **10.100.100.250**.

6. Wijzig het statische subnetmasker in **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Scherm opname van het statische subnetmasker.":::

7. Selecteer **vorige**  >  **volt ooien**.

#### <a name="import-the-bios-configuration-file"></a>Het BIOS-configuratie bestand importeren

In dit artikel wordt beschreven hoe u het BIOS configureert met behulp van het configuratie bestand.

1. Sluit een PC met een statisch vooraf geconfigureerd IP-adres **10.100.100.200** toe aan de **iDRAC** -poort.

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Scherm afbeelding van de vooraf geconfigureerde IP-adres poort.":::

2. Open een browser en voer **10.100.100.250** in om verbinding te maken met de iDRAC-web-interface.

3. Meld u aan met de standaard beheerders bevoegdheden van Dell:

   - Gebruikers naam: **root**

   - Wacht woord: **Calvin**

4. De referenties van het apparaat zijn:

   - Gebruikers naam: **xxx**

   - Wacht woord: **xxx**

     De bewerking van het import Server profiel wordt gestart.

     > [!NOTE]
     > Voordat u het bestand importeert, moet u het volgende controleren:
     > - U bent de enige gebruiker die momenteel is verbonden met iDRAC.
     > - Het systeem bevindt zich niet in het BIOS-menu.

5. Ga naar **configuratie**  >  **Server configuratie profiel**. Stel de volgende para meters in:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Scherm opname van de configuratie van het Server profiel.":::

   | Parameter | Configuratie |
   |--|--|
   | Locatie type | Selecteer **lokaal**. |
   | Bestandspad | Selecteer **bestand kiezen** en voeg het XML-configuratie bestand toe. |
   | Onderdelen importeren | Selecteer **BIOS, NIC, RAID**. |
   | Maximale wacht tijd | Selecteer **20 minuten**. |

6. Selecteer **Importeren**.

7. Als u het proces wilt bewaken, gaat u naar de wachtrij voor **onderhouds**  >  **taken**.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Scherm opname van de taak wachtrij.":::

#### <a name="manually-configuring-bios"></a>BIOS hand matig configureren 

U moet het toestel-BIOS hand matig configureren als:

- U hebt uw apparaat niet gekocht met een pijl.

- U hebt een apparaat, maar u hebt geen toegang tot het XML-configuratie bestand.

Nadat u het BIOS hebt geopend, gaat u naar **Apparaatinstellingen**.

Hand matig configureren:

1. Open het toestel-BIOS rechtstreeks via een toetsen bord en scherm, of gebruik iDRAC.

   - Als het apparaat geen Defender voor IoT-apparaat is, opent u een browser en gaat u naar het IP-adres dat eerder is geconfigureerd. Meld u aan met de standaard beheerders bevoegdheden van Dell. Gebruik het **toegangs punt** voor de gebruikers naam en het **Calvin** voor het wacht woord.

   - Als het apparaat een Defender voor IoT-apparaat is, meldt u zich aan met **xxx** voor de gebruikers naam en **xxx** voor het wacht woord.

2. Nadat u het BIOS hebt geopend, gaat u naar **Apparaatinstellingen**.

3. Kies de door RAID beheerde configuratie door **geïntegreerde RAID-controller 1 te selecteren: Dell PERC \<PERC H330 Adapter\> Configuration Utility**.

4. Selecteer **configuratie beheer**.

5. Selecteer **virtuele schijf maken**.

6. Selecteer in het veld **RAID-niveau selecteren** de optie **RAID5**. Voer in het veld **naam van virtuele schijf** het **toegangs punt** in en selecteer **fysieke schijven**.

7. Selecteer **Alles controleren** en selecteer vervolgens **wijzigingen Toep assen**

8. Selecteer **OK**.

9. Schuif omlaag en selecteer **virtuele schijf maken**.

10. Schakel het selectie vakje **bevestigen** in en selecteer **Ja**.

11. Selecteer **OK**.

12. Ga terug naar het hoofd scherm en selecteer **systeem-BIOS**.

13. Selecteer **opstart instellingen**.

14. Selecteer voor de optie voor de **opstart modus** **BIOS**.

15. Selecteer **vorige** en selecteer vervolgens **volt ooien** om de BIOS-instellingen af te sluiten.

### <a name="software-installation-dell-r340"></a>Software-installatie (Dell R340)

Het installatie proces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart.

Installeren:

1. Controleer of het versie medium op een van de volgende manieren is gekoppeld aan het apparaat:

   - Verbind de externe CD of schijf met een sleutel met de release.

   - Koppel de ISO-installatie kopie met behulp van iDRAC. Nadat u zich hebt aangemeld bij iDRAC, selecteert u de virtuele console en selecteert u vervolgens **virtuele media**.

2. Selecteer in de sectie **cd/dvd toewijzen** de optie **bestand kiezen**.

3. Kies het versie ISO-installatie kopie bestand voor deze versie in het dialoog venster dat wordt geopend.

4. Selecteer de knop **apparaat toewijzen** .

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Scherm opname van een toegewezen apparaat.":::

5. Het medium is gekoppeld. Selecteer **Sluiten**.

6. Start het apparaat. Wanneer u iDRAC gebruikt, kunt u de servers opnieuw opstarten door de knop voor het **besturings element consul** te selecteren. Selecteer vervolgens op de **toetsenbord macro's** de knop **Toep assen** , waarmee de toetsen combinatie CTRL + ALT + DELETE wordt gestart.

7. Selecteer **Engels**.

8. Selecteer **sensor-release- \<version\> Enter prise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Scherm afbeelding waarin de versie selectie wordt weer gegeven.":::   

9. Definieer het profiel van het apparaat en de netwerk eigenschappen:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Scherm opname van het profiel van het apparaat.":::   

   | Parameter | Configuratie |
   |--|--|
   | **Hardwareprofiel** | **gehele** |
   | **Beheer interface** | **eno1** |
   | **Netwerk parameters (door de klant opgegeven)** | - |
   |**IP-adres van beheer netwerk:** | - |
   | **subnetmasker:** | - |
   | **hostnaam van apparaat:** | - |
   | **DNS** | - |
   | **IP-adres van standaard gateway:** | - |
   | **invoer interfaces:** |  Het systeem genereert de lijst met invoer interfaces voor u. Kopieer alle items die in de lijst worden weer gegeven met een komma als scheidings teken om de invoer interfaces te spie gelen. Houd er rekening mee dat de bridge-interface niet hoeft te worden geconfigureerd. Deze optie wordt alleen gebruikt voor speciale use-cases. |

10. Na ongeveer 10 minuten worden de twee sets met referenties weer gegeven. Een is voor een **cyberx** -gebruiker en één voor een **ondersteunings** gebruiker.  

11. Sla de apparaat-ID en wacht woorden op. U hebt deze referenties nodig om toegang te krijgen tot het platform wanneer u het voor het eerst gebruikt.

12. Selecteer **Enter** om door te gaan.

## <a name="hpe-proliant-dl20-installation"></a>Installatie van HPE ProLiant DL20

In dit artikel wordt het installatie proces voor de HPE ProLiant DL20 beschreven. Dit omvat de volgende stappen:

  - Schakel externe toegang in en werk het standaard beheerders wachtwoord bij.
  - BIOS-en RAID-instellingen configureren.
  - De software installeren.

### <a name="about-the-installation"></a>Over de installatie

  - Enter prise-en SMB-apparaten kunnen worden geïnstalleerd. Het installatie proces is identiek voor beide typen apparaten, met uitzonde ring van de matrix configuratie.
  - Er wordt een standaard gebruiker met beheerders rechten gegeven. U wordt aangeraden het wacht woord tijdens het netwerk configuratie proces te wijzigen.
  - Tijdens het netwerk configuratie proces configureert u de iLO-poort op netwerk poort 1.
  - Het installatie proces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20 voor paneel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20 voor paneel.":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20 back-paneel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Het scherm terug van de HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Externe toegang inschakelen en het wacht woord bijwerken

Gebruik de volgende procedure om netwerk opties in te stellen en het standaard wachtwoord bij te werken.

Om het wacht woord in te scha kelen en bij te werken:

1. Sluit een scherm en een toetsen bord aan op het HP-apparaat, schakel het apparaat in en druk op **F9**.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Scherm afbeelding van het HPE ProLiant-venster.":::

2. Ga naar **systeem hulpprogramma's** systeem  >  **configuratie**  >  **ILO 5 configuratie hulpprogramma**  >  **netwerk opties**.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Scherm opname van het venster systeem configuratie.":::

    1.  Selecteer **gedeelde netwerk poort-Lom** in het veld **netwerk interface adapter** .
    
    1.  Schakel DHCP uit.
    
    1.  Voer het IP-adres, het subnetmasker en het IP-adres van de gateway in.

3. Selecteer **F10: opslaan**.

4. Selecteer **ESC** om terug te gaan naar het **configuratie hulpprogramma ILO 5** en selecteer vervolgens **gebruikers beheer**.

5. Selecteer **gebruiker bewerken/verwijderen**. De beheerder is de enige standaard gebruiker die is gedefinieerd. 

6. Wijzig het standaard wachtwoord en selecteer **F10: opslaan**.

### <a name="configure-the-hpe-bios"></a>Het HPE-BIOS configureren

In de volgende procedure wordt beschreven hoe u het HPE-BIOS voor de Enter prise-en SMB-apparaten kunt configureren.

Het HPE-BIOS configureren:

1. Selecteer systeem **hulpprogramma's**  >  **systeem configuratie**  >  **BIOS/platform configuratie (RBSU)**.

2. Selecteer **opstart opties** in het formulier **BIOS/platform Configuration (RBSU)** .

3. Wijzig de **opstart modus** in **verouderde BIOS-modus** en selecteer vervolgens **F10: opslaan**.

4. Selecteer **ESC** twee keer om het formulier **systeem configuratie** te sluiten.

#### <a name="for-the-enterprise-appliance"></a>Voor het bedrijfs apparaat

1. Selecteer **Inge sloten RAID 1: HPE Smart Array P408i-a SR gen 10**  >  **matrix configuratie**  >  **Create array**.

2. Selecteer in het formulier **matrix maken** de optie alle opties. Er zijn drie opties beschikbaar voor het **bedrijfs** apparaat.

#### <a name="for-the-smb-appliance"></a>Voor het SMB-apparaat

1. Selecteer **Inge sloten RAID 1: HPE Smart Array P208i-a SR gen 10**  >  **matrix configuratie**  >  **Create array**.

2. Selecteer **door gaan naar volgende formulier**.

3. Stel in het formulier **RAID-niveau instellen** het niveau in op **RAID 5** voor Enter prise-implementaties en **RAID 1** voor SMB-implementaties.

4. Selecteer **door gaan naar volgende formulier**.

5. Voer in het **Label formulier logisch station** de **logische schijf 1** in.

6. Selecteer **wijzigingen verzenden**.

7. Selecteer in het **Verzend** formulier **terug naar het hoofd menu**.

8. Selecteer **F10: opslaan** en druk twee keer op **ESC** .

9. Selecteer in het venster **systeem Hulpprogramma's** **eenmalig opstart menu**.

10. Selecteer in het formulier voor **eenmalig opstarten** **verouderde BIOS-One-Time opstart menu**.

11. Het Windows-venster **voor opstarten in verouderde** en **opstart** apparaten wordt weer gegeven. Kies een optie voor het overschrijven van de opstart opties; bijvoorbeeld naar een CD-ROM, USB, HDD of UEFI-shell.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Scherm afbeelding met het eerste opstart onderdrukkings venster.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Scherm opname van het tweede venster voor het overschrijven van de opstart procedure.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Software-installatie (HPE ProLiant DL20-apparaat)

Het installatie proces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart.

De software installeren:

1. Sluit het scherm en het toetsen bord aan op het apparaat en maak vervolgens verbinding met de CLI.

2. Sluit een externe CD of schijf aan op de sleutel met de ISO-installatie kopie die u hebt gedownload van de pagina **updates** in de Defender voor IOT-Portal.

3. Start het apparaat.

4. Selecteer **Engels**.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="De selectie van het Engels in het CLI-venster.":::

5. Selecteer **sensor-release- <version> Enter prise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Scherm opname van het scherm voor het selecteren van een versie.":::

6. Definieer in de installatie wizard het profiel van het apparaat en de netwerk eigenschappen:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Scherm opname van de installatie wizard.":::

    | Parameter | Configuratie |
    | ----------| ------------- |
    | **Hardwareprofiel** | Selecteer **Enter prise** of **Office** voor SMB-implementaties. |
    | **Beheer interface** | **eno2** |
    | **Standaard netwerk parameters (meestal de para meters worden opgegeven door de klant)** | **IP-adres van beheer netwerk:** <br/> <br/>**hostnaam van apparaat:** <br/>**DNS** <br/>**het IP-adres van de standaard gateway:**|
    | **invoer interfaces:** | Het systeem genereert de lijst met invoer interfaces voor u.<br/><br/>Kopieer alle items die in de lijst worden weer gegeven met een komma als scheidings teken: **eno5, eno3, eno1, eno6, eno4** om de invoer interfaces te spie gelen.<br/><br/>**Voor HPE DL20: geen lijst met eno1, enp1s0f4u4 (iLo-interfaces)**<br/><br/>**Bridge**: de bridge-interface hoeft niet te worden geconfigureerd. Deze optie wordt alleen gebruikt voor speciale use-cases. Druk op **Enter** om verder te gaan. |

7. Na ongeveer 10 minuten worden de twee sets met referenties weer gegeven. Een is voor een **cyberx** -gebruiker en één voor een **ondersteunings** gebruiker.

8. Sla de ID en wacht woorden van het apparaat op. U hebt de referenties nodig voor de eerste keer toegang tot het platform.

9. Selecteer **Enter** om door te gaan.

## <a name="hpe-proliant-dl360-installation"></a>Installatie van HPE ProLiant DL360

  - Er wordt een standaard gebruiker met beheerders rechten gegeven. U wordt aangeraden het wacht woord tijdens de netwerk configuratie te wijzigen.

  - Tijdens de netwerk configuratie configureert u de iLO-poort.

  - Het installatie proces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360 voor paneel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360 voor paneel.":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360 back-paneel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360 back panel.":::

### <a name="enable-remote-access-and-update-the-password"></a>Externe toegang inschakelen en het wacht woord bijwerken

Raadpleeg de voor gaande secties voor de installatie van HPE ProLiant DL20:

  - "Externe toegang inschakelen en het wacht woord bijwerken"

  - "Het HPE-BIOS configureren"

De bedrijfs configuratie is identiek.

> [!Note]
> Controleer in de matrix vorm of u alle opties selecteert.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>externe installatie van iLO (vanaf een virtueel station)

In deze procedure wordt de iLO-installatie van een virtuele schijf beschreven.

Installeren:

1. Meld u aan bij de iLO-console en klik vervolgens met de rechter muisknop op het scherm servers.

2. Selecteer de **HTML5-console**.

3. Selecteer in de-console het CD-pictogram en kies de CD/DVD-optie.

4. Selecteer **lokaal ISO-bestand**.

5. Kies in het dialoog venster het relevante ISO-bestand.

6. Ga naar het pictogram links, selecteer **inschakelen** en selecteer **opnieuw instellen**.

7. Het apparaat wordt opnieuw opgestart en het sensor installatie proces wordt uitgevoerd.

### <a name="software-installation-hpe-dl360"></a>Software-installatie (HPE DL360)

Het installatie proces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart.

Installeren:

1. Sluit het scherm en het toetsen bord aan op het apparaat en maak vervolgens verbinding met de CLI.

2. Sluit een externe CD of schijf aan op een sleutel met de ISO-installatie kopie die u hebt gedownload van de pagina **updates** in de Defender voor IOT-Portal.

3. Start het apparaat.

4. Selecteer **Engels**.

5. Selecteer **sensor-release- <version> Enter prise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Scherm afbeelding waarin de versie wordt geselecteerd.":::

6. Definieer in de installatie wizard het profiel van het apparaat en de netwerk eigenschappen.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Scherm opname van de installatie wizard.":::

    | Parameter | Configuratie |
    | ----------| ------------- |
    | **Hardwareprofiel** | Selecteer **zakelijk**. |
    | **Beheer interface** | **eno2** |
    | **Standaard netwerk parameters (opgegeven door de klant)** | **IP-adres van beheer netwerk:** <br/>**subnetmasker:** <br/>**hostnaam van apparaat:** <br/>**DNS** <br/>**het IP-adres van de standaard gateway:**|
    | **invoer interfaces:**  | Het systeem genereert een lijst met invoer interfaces voor u.<br/><br/>Kopieer alle items die in de lijst worden weer gegeven met een komma als scheidings teken om de invoer interfaces te spie gelen.<br/><br/>Houd er rekening mee dat de bridge-interface niet hoeft te worden geconfigureerd. Deze optie wordt alleen gebruikt voor speciale use-cases. |

7. Na ongeveer 10 minuten worden de twee sets met referenties weer gegeven. Een is voor een **cyberx** -gebruiker en één voor een **ondersteunings** gebruiker.

8. Sla de ID en wacht woorden van het apparaat op. U hebt deze referenties nodig voor de eerste keer toegang tot het platform.

9. Selecteer **Enter** om door te gaan.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Sensor installatie voor het virtuele apparaat

U kunt de virtuele machine voor de Defender voor IoT-sensor implementeren in de volgende architecturen:


| Architectuur | Specificaties | Gebruik | Opmerkingen |
|---|---|---|---|
| **Enterprise** | CPU: 8<br/>Geheugen: 32G RAM<br/>HDD: 1800 GB | Productie omgeving | Standaard en meest voorkomende |
| **Kleine ondernemingen** | CPU: 4 <br/>Geheugen: 8G RAM<br/>HDD: 500 GB | Test-of kleine productie omgevingen | -  |
| **Office** | CPU: 4<br/>Geheugen: 8G RAM<br/>HDD: 100 GB | Kleine test omgevingen | -  |

### <a name="prerequisites"></a>Vereisten

De on-premises beheer console ondersteunt zowel VMware-als Hyper-V-implementatie opties. Voordat u met de installatie begint, controleert u of u de volgende items hebt:

  - VMware (ESXi 5,5 of hoger) of Hyper-V-Hyper Visor (Windows 10 Pro of ENTER prise) geïnstalleerd en operationeel

  - Beschik bare hardwarebronnen voor de virtuele machine

  - ISO-installatie bestand voor de Azure Defender voor IoT-sensor

Controleer of de Hyper Visor wordt uitgevoerd.

### <a name="create-the-virtual-machine-esxi"></a>De virtuele machine maken (ESXi)

1. Meld u aan bij de ESXi, kies de relevante **gegevens opslag** en selecteer **browser data store**.

2. **Upload** de installatie kopie en selecteer **sluiten**.

3. Ga naar **virtual machines** en selecteer **VM maken/registreren**.

4. Selecteer **nieuwe virtuele machine maken** en selecteer **volgende**.

5. Voeg de naam van een sensor toe en kies:

   - Compatibiliteit: **&lt; meest recente ESXi &gt; -versie**

   - Gast besturingssysteem familie: **Linux**

   - Versie van gast besturingssysteem: **Ubuntu Linux (64-bits)**

6. Selecteer **Next**.

7. Kies de relevante gegevens opslag en selecteer **volgende**.

8. Wijzig de para meters van de virtuele hardware volgens de vereiste architectuur.

9. Selecteer **ISO-bestand Data Store** voor **cd/dvd-station 1** en kies het ISO-bestand dat u eerder hebt geüpload.

10. Selecteer **Volgende** > **voltooien**.

### <a name="create-the-virtual-machine-hyper-v"></a>De virtuele machine maken (Hyper-V)

In deze procedure wordt beschreven hoe u een virtuele machine maakt met behulp van Hyper-V.

Een virtuele machine maken:

1. Maak een virtuele schijf in Hyper-V-beheer.

2. Selecteer **Format = VHDX**.

3. Selecteer **type = dynamisch uitbreiden**.

4. Geef de naam en de locatie voor de VHD op.

5. Voer de vereiste grootte in (volgens de architectuur).   

6. Controleer de samen vatting en selecteer **volt ooien**.

7. Maak een nieuwe virtuele machine in het menu **acties** .

8. Voer een naam in voor de virtuele machine.

9. Selecteer **generatie**  >  **1** opgeven.

10. Geef de geheugen toewijzing op (volgens de architectuur) en schakel het selectie vakje voor dynamisch geheugen in.

11. Configureer de netwerk adapter op basis van de netwerk topologie van uw server.

12. Verbind de VHDX die eerder is gemaakt met de virtuele machine.

13. Controleer de samen vatting en selecteer **volt ooien**.

14. Klik met de rechter muisknop op de nieuwe virtuele machine en selecteer **instellingen**.

15. Selecteer **Hardware toevoegen** en voeg een nieuwe netwerk adapter toe.

16. Selecteer de virtuele switch die verbinding maakt met het netwerk van het sensor beheer.

17. CPU-Resources toewijzen (op basis van de architectuur).

18. De ISO-installatie kopie van de beheer console verbinden met een virtueel DVD-station.

19. Start de virtuele machine.

20. Selecteer in het menu **acties** de optie **verbinding maken** om door te gaan met de installatie van de software.

### <a name="software-installation-esxi-and-hyper-v"></a>Software-installatie (ESXi en Hyper-V)

In deze sectie wordt de installatie van de ESXi-en Hyper-V-software beschreven.

Installeren:

1. Open de console van de virtuele machine.

2. De VM wordt gestart vanaf de ISO-installatie kopie en het scherm taal selectie wordt weer gegeven. Selecteer **Engels**.

3. Selecteer de vereiste architectuur.

4. Definieer het profiel van het apparaat en de netwerk eigenschappen:

    | Parameter | Configuratie |
    | ----------| ------------- |
    | **Hardwareprofiel** | &lt;vereiste architectuur&gt; |
    | **Beheer interface** | **ens192** |
    | **Netwerk parameters (door de klant opgegeven)** | **IP-adres van beheer netwerk:** <br/>**subnetmasker:** <br/>**hostnaam van apparaat:** <br/>**DNS** <br/>**standaard gateway:** <br/>**invoer interfaces:**|
    | **brug interfaces:** | Het is niet nodig om de bridge-interface te configureren. Deze optie geldt alleen voor speciale use cases. |

5. Voer **Y** in om de instellingen te accepteren.

6. Aanmeldings referenties worden automatisch gegenereerd en weer gegeven. Kopieer de gebruikers naam en het wacht woord op een veilige plaats, omdat deze zijn vereist voor aanmelding en beheer.

   - **Ondersteuning**: de gebruiker met beheerders rechten voor gebruikers beheer.

   - **Cyberx**: het equivalent van de hoofdmap voor toegang tot het apparaat.

7. Het apparaat wordt opnieuw opgestart.

8. Toegang tot de beheer console via het eerder geconfigureerde IP-adres: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Scherm opname van de toegang tot de beheer console.":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Virtueel apparaat: on-premises beheer console-installatie

De on-premises beheer console-VM ondersteunt de volgende architecturen:

| Architectuur | Specificaties | Gebruik | 
|--|--|--|
| Onderneming <br/>(Standaard en meest gebruikelijk) | CPU: 8 <br/>Geheugen: 32G RAM<br/> HDD: 1,8 TB | Grote productie omgevingen | 
| Onderneming | CPU: 4 <br/> Geheugen: 8G RAM<br/> HDD: 500 GB | Grote productie omgevingen |
| Onderneming | CPU: 4 <br/>Geheugen: 8G RAM <br/> HDD: 100 GB | Kleine test omgevingen | 
   
### <a name="prerequisites"></a>Vereisten

De on-premises beheer console ondersteunt zowel VMware-als Hyper-V-implementatie opties. Controleer het volgende voordat u met de installatie begint:

- VMware (ESXi 5,5 of hoger) of Hyper-V-Hyper Visor (Windows 10 Pro of ENTER prise) is geïnstalleerd en operationeel.

- De hardwarebronnen zijn beschikbaar voor de virtuele machine.

- U hebt het ISO-installatie bestand voor de on-premises beheer console.
    
- De Hyper Visor wordt uitgevoerd.

### <a name="create-the-virtual-machine-esxi"></a>De virtuele machine maken (ESXi)

Een virtuele machine maken (ESXi):

1. Meld u aan bij de ESXi, kies de relevante **gegevens opslag** en selecteer **browser data store**.

2. Upload de installatie kopie en selecteer **sluiten**.

3. Ga naar **virtual machines**.

4. Selecteer **virtuele machine maken/registreren**.

5. Selecteer **nieuwe virtuele machine maken** en selecteer **volgende**.

6. Voeg de naam van een sensor toe en kies:

   - Tussen \<latest ESXi version>

   - Gast besturingssysteem familie: Linux

   - Versie van gast besturingssysteem: Ubuntu Linux (64-bits)

7. Selecteer **Next**.

8. Kies relevante gegevens opslag en selecteer **volgende**.

9. Wijzig de para meters van de virtuele hardware volgens de vereiste architectuur.

10. Selecteer **ISO-bestand Data Store** voor **cd/dvd-station 1** en kies het ISO-bestand dat u eerder hebt geüpload.

11. Selecteer **Volgende** > **voltooien**.

### <a name="create-the-virtual-machine-hyper-v"></a>De virtuele machine maken (Hyper-V)

Een virtuele machine maken met behulp van Hyper-V:

1. Maak een virtuele schijf in Hyper-V-beheer.

2. Selecteer de format **VHDX**.

3. Selecteer **Next**.

4. Selecteer **dynamisch uitbreiden** van type.

5. Selecteer **Next**.

6. Geef de naam en de locatie voor de VHD op.

7. Selecteer **Next**.

8. Voer de vereiste grootte in (volgens de architectuur).

9. Selecteer **Next**.

10. Controleer de samen vatting en selecteer **volt ooien**.

11. Maak een nieuwe virtuele machine in het menu **acties** .

12. Selecteer **Next**.

13. Voer een naam in voor de virtuele machine.

14. Selecteer **Next**.

15. Selecteer **generatie** en stel deze in op **generatie 1**.

16. Selecteer **Next**.

17. Geef de geheugen toewijzing op (volgens de architectuur) en schakel het selectie vakje voor dynamisch geheugen in.

18. Selecteer **Next**.

19. Configureer de netwerk adapter op basis van de netwerk topologie van uw server.

20. Selecteer **Next**.

21. Verbind de VHDX die eerder is gemaakt met de virtuele machine.

22. Selecteer **Next**.

23. Controleer de samen vatting en selecteer **volt ooien**.

24. Klik met de rechter muisknop op de nieuwe virtuele machine en selecteer **instellingen**.

25. Selecteer **Hardware toevoegen** en voeg een nieuwe adapter toe voor de **netwerk adapter**.

26. Voor **virtuele switch** selecteert u de switch die verbinding maakt met het netwerk van het sensor beheer.

27. CPU-Resources toewijzen (op basis van de architectuur).

28. De ISO-installatie kopie van de beheer console verbinden met een virtueel DVD-station.

29. Start de virtuele machine.

30. Selecteer in het menu **acties** de optie **verbinding maken** om door te gaan met de installatie van de software.

### <a name="software-installation-esxi-and-hyper-v"></a>Software-installatie (ESXi en Hyper-V)

Als u de virtuele machine start, wordt het installatie proces gestart vanuit de ISO-installatie kopie.

De software installeren:

1. Selecteer **Engels**.

2. Selecteer de vereiste architectuur voor uw implementatie.

3. Definieer de netwerk interface voor het sensor Management-netwerk: Interface, IP, subnet, DNS-server en standaard gateway.

4. Aanmeldings referenties worden automatisch gegenereerd en weer gegeven. Bewaar deze referenties op een veilige plaats, omdat deze zijn vereist voor aanmelding en beheer.

  - **Ondersteuning**: de gebruiker met beheerders rechten voor gebruikers beheer.

  - **Cyberx**: het equivalent van de hoofdmap voor toegang tot het apparaat.

5. Het apparaat wordt opnieuw opgestart.

6. Toegang tot de beheer console via het eerder geconfigureerde IP-adres: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Scherm opname van het aanmeldings scherm van de beheer console.":::

## <a name="post-installation-validation"></a>Validatie na de installatie

Als u de installatie van een fysiek apparaat wilt valideren, moet u een aantal tests uitvoeren. Hetzelfde validatie proces is van toepassing op alle typen apparaten.

Voer de validatie uit door de GUI of de CLI te gebruiken. De validatie is beschikbaar voor de gebruikers **ondersteuning** en de gebruiker **cyberx**.

Validatie na de installatie moet de volgende tests bevatten:

  - **Sanity test**: Controleer of het systeem actief is.

  - **Versie**: Controleer of de versie juist is.

  - **ifconfig**: Controleer of alle invoer interfaces die tijdens het installatie proces zijn geconfigureerd, worden uitgevoerd.

### <a name="checking-system-health-by-using-the-gui"></a>De systeem status controleren met de gebruikers interface

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Scherm opname waarin de systeem status controle wordt weer gegeven.":::

#### <a name="sanity"></a>Sanity

- **Apparaat**: voert de controle van het toestel Sanity uit. U kunt dezelfde controle uitvoeren met de CLI-opdracht `system-sanity` .

- **Versie**: hier wordt de versie van het apparaat weer gegeven.

- **Netwerk eigenschappen**: hier worden de para meters van de sensor netwerk weer gegeven.

#### <a name="redis"></a>Redis

- **Geheugen**: geeft de totale afbeelding van het geheugen gebruik, zoals hoeveel geheugen er is gebruikt en hoeveel er nog steeds beschikbaar is.

- **Langste sleutel**: geeft de langste sleutels weer die een uitgebreid geheugen gebruik kunnen veroorzaken.

#### <a name="system"></a>Systeem

- **Kern logboek**: bevat de laatste 500 rijen van het kern logboek, zodat u de recente logboek rijen kunt weer geven zonder het hele systeem logboek te exporteren.

- **Taak beheer**: Hiermee worden de taken die in de tabel met processen worden weer gegeven, vertaald naar de volgende lagen: 
  
  - Permanente laag (redis) 
  - Cash Layer (SQL)

- **Netwerk statistieken**: geeft uw netwerk statistieken weer.

- **Boven**: toont de tabel met processen. Het is een Linux-opdracht die een dynamische realtime-weer gave van het actieve systeem biedt.

- **Controle van het back-upgeheugen**: geeft de status van het back-upgeheugen, waarbij het volgende wordt gecontroleerd:
  - De locatie van de back-upmap 
  - De grootte van de back-upmap
  - De beperkingen van de back-upmap
  - Wanneer de laatste back-up is gemaakt
  - Hoeveel ruimte er beschikbaar is voor de extra back-upbestanden

- **ifconfig**: hier worden de para meters voor de fysieke interfaces van het apparaat weer gegeven.

- **Cyberx nloaden**: geeft het netwerk verkeer en de band breedte weer met behulp van de zes seconden testen.

- **Fouten in de kern, log**: geeft fouten weer in het logboek bestand van de kern.

Voor toegang tot het hulp programma:

1. Meld u aan bij de sensor met de referenties van de **ondersteunings** gebruiker.

2. Selecteer **systeem statistieken** in het venster **systeem instellingen** .

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>De systeem status controleren met behulp van de CLI

**Test 1: Sanity**

Controleer of het systeem actief is:

1. Maak verbinding met de CLI met de Linux-Terminal (bijvoorbeeld PuTTy) en de gebruikers **ondersteuning**.

2. Voer `system sanity` in.

3. Controleer of alle services groen zijn (uitgevoerd).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Scherm opname waarin actieve services worden weer gegeven.":::

4. Controleer of het **systeem actief is. (Prod)** wordt onderaan weer gegeven.

**Test 2: versie controle**

Controleer of de juiste versie wordt gebruikt:

1. Maak verbinding met de CLI met de Linux-Terminal (bijvoorbeeld PuTTy) en de gebruikers **ondersteuning**.

2. Voer `system version` in.

3. Controleer of de juiste versie wordt weer gegeven.

**Test 3: netwerk validatie**

Controleer of alle invoer interfaces die tijdens het installatie proces zijn geconfigureerd, worden uitgevoerd:

1. Maak verbinding met de CLI met de Linux-Terminal (bijvoorbeeld PuTTy) en de gebruikers **ondersteuning**.

2. Voer in `network list` (het equivalent van de Linux `ifconfig` -opdracht).

3. Controleer of de vereiste invoer interfaces worden weer gegeven. Als er bijvoorbeeld twee Quad koper Nic's zijn geïnstalleerd, moeten er 10 interfaces in de lijst staan.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Scherm opname van de lijst met interfaces.":::

**Test 4: beheer toegang tot de gebruikers interface**

Controleer of u toegang hebt tot de Web-GUI van de console:

1. Verbind een laptop met een Ethernet-kabel met de beheer poort (**Gb1**).

2. Definieer het NIC-adres van de laptop in hetzelfde bereik als het apparaat.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Scherm opname van de beheer toegang tot de gebruikers interface.":::

3. Ping het IP-adres van het apparaat van de laptop om de connectiviteit te controleren (standaard: 10.100.10.1).

4. Open de Chrome-browser op de laptop en voer het IP-adres van het apparaat in.

5. Selecteer in het venster **uw verbinding is niet privé** de optie **Geavanceerd** en door gaan.

6. De test is geslaagd wanneer het aanmeldings scherm van Defender voor IoT wordt weer gegeven.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Scherm opname van de toegang tot de beheer console.":::

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="you-cant-connect-by-using-a-web-interface"></a>U kunt geen verbinding maken met behulp van een webinterface

1. Controleer of de computer waarmee u verbinding probeert te maken, zich op hetzelfde netwerk bevindt als het apparaat.

2. Controleer of het GUI-netwerk is verbonden met de beheer poort.

3. Ping het IP-adres van het apparaat. Als er geen ping is:

   1. Een monitor en een toetsen bord aansluiten op het apparaat.

   1. Gebruik de **ondersteunings** gebruiker en het wacht woord om u aan te melden.

   1. Gebruik de opdracht `network list` om het huidige IP-adres weer te geven.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Scherm opname van de lijst met netwerken.":::

4. Als de netwerk parameters onjuist zijn geconfigureerd, gebruikt u de volgende procedure om ze te wijzigen:

   1. Gebruik de opdracht `network edit-settings` .

   1. Selecteer **Y** als u het IP-adres van het beheer netwerk wilt wijzigen.

   1. Selecteer **Y** om het subnetmasker te wijzigen.

   1. Selecteer **Y** om de DNS te wijzigen.

   1. Als u het standaard gateway-IP-adres wilt wijzigen, selecteert u **Y**.

   1. Voor de wijziging in de invoer interface (alleen sensor), selecteert u **N**.

   1. Selecteer **Y** om de instellingen toe te passen.

5. Na opnieuw opstarten maakt u verbinding met de referenties van de ondersteunings gebruiker en gebruikt `network list` u de opdracht om te controleren of de para meters zijn gewijzigd.

6. Probeer opnieuw te pingen en maak verbinding met de gebruikers interface.

### <a name="the-appliance-isnt-responding"></a>Het apparaat reageert niet

1. Sluit een monitor en toetsen bord aan op het apparaat of gebruik PuTTy om extern verbinding te maken met de CLI.

2. Gebruik de referenties van de **ondersteunings** gebruiker om u aan te melden.

3. Gebruik de `system sanity` opdracht en controleer of alle processen worden uitgevoerd.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Scherm opname van de opdracht systeem Sanity.":::

Neem contact op met [Microsoft ondersteuning](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)voor eventuele andere problemen.

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Bijlage A: poort spie gelen op vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Een bereik poort configureren voor een bestaande vSwitch

Een vSwitch heeft geen mogelijkheden voor spie gelen, maar u kunt een eenvoudige tijdelijke oplossing gebruiken voor het implementeren van een bereik poort.

Een bereik poort configureren:

1. Eigenschappen van vSwitch openen.

2. Selecteer **Toevoegen**.

3. Selecteer **virtuele machine**  >  **volgende**.

4. Voeg een netwerk label **bereik netwerk** toe, selecteer **VLAN-id**  >  **all** en selecteer vervolgens **volgende**.

5. Selecteer **Finish**.

6. Selecteer **span Network** > **Edit*.

7. Selecteer **beveiliging** en controleer of het beleid voor de **ongeordende modus** is ingesteld op de modus **accepteren** .

8. Selecteer **OK** en selecteer vervolgens **sluiten** om de vSwitch-eigenschappen te sluiten.

9. Open de **XSense-VM** -eigenschappen.

10. Selecteer voor **netwerk adapter 2** het **bereik** netwerk.

11. Selecteer **OK**.

12. Maak verbinding met de sensor en controleer of mirroring werkt.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Bijlage B: Sens oren openen vanuit de on-premises beheer console

U kunt de systeem beveiliging verbeteren door directe gebruikers toegang tot de sensor te voor komen. Gebruik in plaats daarvan proxy tunneling om gebruikers toegang te geven tot de sensor vanuit de on-premises beheer console met één firewall regel. Deze techniek beperkt de mogelijkheid van ongeoorloofde toegang tot de netwerk omgeving buiten de sensor. De gebruikers ervaring bij het aanmelden bij de sensor blijft hetzelfde.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Scherm opname van de toegang tot de sensor.":::

Tunneling inschakelen:

1. Meld u aan bij de CLI van de on-premises beheer console met **cyberx** of **ondersteunings** gebruikers referenties.

2. Voer `sudo cyberx-management-tunnel-enable` in.

3. Druk op **Enter**.

4. Voer `--port 10000` in.

### <a name="next-steps"></a>Volgende stappen

[Uw netwerk instellen](how-to-set-up-your-network.md)
