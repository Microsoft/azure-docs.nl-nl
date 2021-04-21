---
title: Defender for IoT-installatie
description: Meer informatie over het installeren van een sensor en de on-premises beheerconsole voor Azure Defender for IoT.
ms.date: 4/20/2021
ms.topic: how-to
ms.openlocfilehash: e8366a3408e64d95e6c4d50e3ddef84309b4e8e5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829696"
---
# <a name="defender-for-iot-installation"></a>Defender for IoT-installatie

In dit artikel wordt beschreven hoe u de volgende elementen van Azure Defender for IoT:

- **Sensor:** Defender for IoT-sensoren verzamelen ICS-netwerkverkeer met behulp van passieve bewaking (zonder agent). Passieve en niet-rusieve sensoren hebben geen invloed op OT- en IoT-netwerken en -apparaten. De sensor maakt verbinding met een SPAN-poort of netwerk-TAP en begint onmiddellijk met het bewaken van uw netwerk. Detecties worden weergegeven in de sensorconsole. Daar kunt u ze bekijken, onderzoeken en analyseren in een netwerkkaart, apparaatinventaris en een uitgebreid scala aan rapporten. Voorbeelden hiervan zijn rapporten voor risicoanalyse, gegevensanalysequery's en aanvalsvectoren. Lees meer over sensormogelijkheden in de [gebruikershandleiding voor Defender for IoT-sensoren (direct downloaden).](./getting-started.md)

- **On-premises beheerconsole:** met de on-premises beheerconsole kunt u apparaatbeheer, risicobeheer en vulnerability management. U kunt deze ook gebruiken om bedreigingsbewaking en incidentrespons uit te voeren binnen uw onderneming. Het biedt een uniforme weergave van alle netwerkapparaten, belangrijke IoT- en OT-risico-indicatoren en -waarschuwingen die zijn gedetecteerd in faciliteiten waar sensoren worden geïmplementeerd. Gebruik de on-premises beheerconsole om sensoren in netwerken met een air-gapped netwerk weer te geven en te beheren.

In dit artikel worden de volgende installatiegegevens beschreven:

  - **Hardware:** Details van het fysieke apparaat van Dell en HPE.

  - **Software:** Installatie van sensor- en on-premises beheerconsolesoftware.

  - **Virtuele apparaten:** Details van de virtuele machine en software-installatie.

Na de installatie verbindt u de sensor met uw netwerk.

## <a name="about-defender-for-iot-appliances"></a>Over Defender for IoT-apparaten 

De volgende secties bevatten informatie over Defender for IoT-sensorapparaten en het apparaat voor de on-premises beheerconsole van Defender for IoT.

### <a name="physical-appliances"></a>Fysieke apparaten

De Defender for IoT-apparaatsensor maakt verbinding met een SPAN-poort of netwerk-TAP en begint onmiddellijk met het verzamelen van ICS-netwerkverkeer met behulp van passieve (agentloze) bewaking. Dit proces heeft geen invloed op OT-netwerken en apparaten, omdat het niet in het gegevenspad wordt geplaatst en ot-apparaten niet actief worden gescand.

De volgende rack-bevestigingsapparaten zijn beschikbaar:

| **Implementatietype** | **Bedrijf** | **Enterprise** | **SMB** | **Lijn** |
|--|--|--|--|--|
| **Model** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Poorten bewaken** | maximaal 15 RJ45 of 8 OPT | maximaal 9 RJ45 of 6 OPT | maximaal 8 RJ45 of 6 OPT | 4 RJ45 |
| **Maximale bandbreedte\*** | 3 Gb per seconde | 1 Gb per seconde | 1 Gb per seconde | 100 MB per seconde |
| **Maximaal aantal beveiligde apparaten** | 30,000 | 10.000 | 15.000 | 1000 |

*De maximale bandbreedtecapaciteit kan variëren, afhankelijk van de protocoldistributie.

### <a name="virtual-appliances"></a>Virtuele apparaten

De volgende virtuele apparaten zijn beschikbaar:

| **Implementatietype** | **Enterprise** | **SMB** | **Lijn** |
|--|--|--|--|
| **Beschrijving** | Virtueel apparaat voor bedrijfsimplementaties | Virtueel apparaat voor SMB-implementaties | Virtueel apparaat voor regelimplementaties |
| **Maximale bandbreedte\*** | 150 MB per seconde | 15 MB per seconde | 3 MB/sec |
| **Maximaal aantal beveiligde apparaten** | 3000 | 300 | 100 |
| **Implementatietype** | Enterprise | SMB | Lijn |
| **Beschrijving** | Virtueel apparaat voor bedrijfsimplementaties | Virtueel apparaat voor SMB-implementaties | Virtueel apparaat voor regelimplementaties |

*De maximale bandbreedtecapaciteit kan variëren, afhankelijk van de protocoldistributie.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Hardwarespecificaties voor de on-premises beheerconsole

 | Item | Beschrijving |
 |----|--|
 **Beschrijving** | In een architectuur met meerdere gebieden biedt de on-premises beheerconsole zichtbaarheid en controle over geografisch verspreide sites. Het kan worden geïntegreerd met SOC-beveiligingsstacks, waaronder SIEM's, ticketsystemen, firewalls van de volgende generatie, beveiligde platforms voor externe toegang en de Sandbox voor ICS-malware van Defender for IoT. |
 **Implementatietype** | Enterprise |
 **Apparaattype**  | Dell R340, VM |
 **Aantal beheerde sensoren** | Onbeperkt |

## <a name="prepare-for-the-installation"></a>Voorbereiden op de installatie

### <a name="access-the-iso-installation-image"></a>Toegang tot de ISO-installatieafbeelding

De installatieafbeelding is toegankelijk vanuit de Defender for IoT-portal.

Voor toegang tot het bestand:

1. Meld u aan bij uw Defender for IoT-account.

1. Ga naar de **pagina Netwerksensor** **of On-premises beheerconsole** en selecteer een versie die u wilt downloaden.

### <a name="install-from-dvd"></a>Installeren vanaf dvd

Controleer vóór de installatie of u het volgende hebt:

- Een draagbaar dvd-station met de USB-connector.

- Een installatieafbeelding van het ISO-installatieprogramma.

Installeren:

1. Brand de afbeelding op een dvd of bereid een schijf voor op een sleutel. Sluit een draagbaar dvd-station aan op uw computer, klik met de rechtermuisknop op de ISO-afbeelding en selecteer **Op schijf branden.**

1. Sluit de dvd of schijf op een sleutel aan en configureer het apparaat zo dat het opstart vanaf een dvd of schijf op een sleutel.

### <a name="install-from-disk-on-a-key"></a>Installeren vanaf schijf op een sleutel

Controleer vóór de installatie of u het volgende hebt:

  - Rufus geïnstalleerd.
  
  - Een schijf op sleutel met USB-versie 3.0 en hoger. De minimale grootte is 4 GB.

  - Een ISO-installatiebestand voor installatieprogramma's.

De schijf op een sleutel wordt in dit proces gewist.

Een schijf voorbereiden op een sleutel:

1. Voer Rufus uit en selecteer **SENSOR ISO**.

1. Verbind de schijf op een sleutel met het voorpaneel.

1. Stel het BIOS van de server in om op te starten vanaf de USB.

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL-installatie

Voordat u de software op het Dell-apparaat installeert, moet u de BIOS-configuratie van het apparaat aanpassen:

  - [Dell PowerEdge R340 Front Panel](#dell-poweredge-r340-front-panel) en [Dell PowerEdge R340 Back Panel](#dell-poweredge-r340-back-panel) bevat de beschrijving van de voor- en achterpaneel, samen met de informatie die nodig is voor installatie, zoals stuurprogramma's en poorten.

  - [Dell BIOS Configuration biedt](#dell-bios-configuration) informatie over het maken van verbinding met de dell-interface voor apparaatbeheer en het configureren van het BIOS.

  - [Software-installatie (Dell R340) beschrijft](#software-installation-dell-r340) de procedure die nodig is om de Defender for IoT-sensorsoftware te installeren.

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL-vereisten 

Als u het Dell PowerEdge R340XL-apparaat wilt installeren, hebt u het volgende nodig:

- Enterprise-licentie voor Dell Remote Access Controller (iDrac)

- BIOS-configuratie-XML

- Versies van serverfirmware:

  - BIOS-versie 2.1.6

  - iDrac versie 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340-frontpaneel

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 voorpaneel.":::

 1. Linker configuratiescherm 
 1. Optisch station (optioneel) 
 1. Rechter configuratiescherm 
 1. Informatietag 
 1. Aandrijfeenheden  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340-backpaneel

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340-backpaneel.":::

1. Seriële poort 
1. NIC-poort (Gb 1) 
1. NIC-poort (Gb 1) 
1. PCIe met halve hoogte 
1. PcIe-uitbreidingskaartsleuf in volledige hoogte 
1. Voedingseenheid 1 
1. Voedingseenheid 2 
1. Systeemidentificatie 
1. CMA-knop (System Status Indicator Cable Port) 
1. USB 3.0-poort (2) 
1. Toegewezen iDRAC9-netwerkpoort 
1. VGA-poort 

### <a name="dell-bios-configuration"></a>Dell BIOS-configuratie

Dell BIOS-configuratie is vereist om het Dell-apparaat aan te passen voor gebruik met de software.

De BIOS-configuratie wordt uitgevoerd via een vooraf gedefinieerde configuratie. Het bestand is toegankelijk vanuit het [Helpcentrum.](https://help.cyberx-labs.com/)

Importeer het configuratiebestand naar het Dell-apparaat. Voordat u het configuratiebestand gebruikt, moet u de communicatie tussen het Dell-apparaat en de beheercomputer tot stand brengen.

Het Dell-apparaat wordt beheerd door een geïntegreerde iDRAC met levenscycluscontroller (LC). De LC is ingesloten in elke Dell PowerEdge-server en biedt functionaliteit waarmee u uw Dell PowerEdge-apparaten kunt implementeren, bijwerken, bewaken en onderhouden.

Als u de communicatie tussen het Dell-apparaat en de beheercomputer tot stand wilt brengen, moet u het IP-adres van iDRAC en het IP-adres van de beheercomputer op hetzelfde subnet definiëren.

Wanneer de verbinding tot stand is gebracht, kan het BIOS worden geconfigureerd.

Dell BIOS configureren:

1. [Het IP-adres van iDRAC configureren](#configure-idrac-ip-address)

1. [Het BIOS-configuratiebestand importeren](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>IP-adres van iDRAC configureren

1. Licht de sensor in.

1. Als het besturingssysteem al is geïnstalleerd, selecteert u de toets F2 om de BIOS-configuratie in te voeren.

1. Selecteer **iDRAC-instellingen.**

1. Selecteer **Netwerk.**

   > [!NOTE]
   > Tijdens de installatie moet u het standaard-IP-adres en wachtwoord voor iDRAC configureren dat in de volgende stappen wordt vermeld. Na de installatie wijzigt u deze definities.

1. Wijzig het statische IPv4-adres **in 10.100.100.250.**

1. Wijzig het statische subnetmasker **in 255.255.255.0.**

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Schermopname van het statische subnetmasker.":::

1. Selecteer **Terug**  >  **Voltooien.**

#### <a name="import-the-bios-configuration-file"></a>Het BIOS-configuratiebestand importeren

In dit artikel wordt beschreven hoe u het BIOS configureert met behulp van het configuratiebestand.

1. Sluit een pc met een statisch vooraf geconfigureerd IP-adres **10.100.100.200** aan op de **iDRAC-poort.**

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Schermopname van de vooraf geconfigureerde IP-adrespoort.":::

1. Open een browser en voer **10.100.100.250** in om verbinding te maken met de iDRAC-webinterface.

1. Meld u aan met de standaardbeheerdersbevoegdheden van Dell:

   - Gebruikersnaam: **hoofdmap**

   - Wachtwoord: **password:**

1. De referenties van het apparaat zijn:

   - Gebruikersnaam: **XXX**

   - Wachtwoord: **XXX**

     De importserverprofielbewerking wordt gestart.

     > [!NOTE]
     > Voordat u het bestand importeert, moet u het volgende doen:
     > - U bent de enige gebruiker die momenteel is verbonden met iDRAC.
     > - Het systeem staat niet in het BIOS-menu.

1. Ga naar  >  **Configuratieserverconfiguratieprofiel**. Stel de volgende parameters in:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Schermopname van de configuratie van uw serverprofiel.":::

   | Parameter | Configuratie |
   |--|--|
   | Locatietype | Selecteer **Lokaal.** |
   | Bestandspad | Selecteer **Bestand kiezen en** voeg het XML-configuratiebestand toe. |
   | Onderdelen importeren | Selecteer **BIOS, NIC, RAID.** |
   | Maximale wachttijd | Selecteer **20 minuten.** |

1. Selecteer **Importeren**.

1. Als u het proces wilt controleren, gaat u naar  >  **Onderhoudswachtrij voor de taak**.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Schermopname van taakwachtrij.":::

#### <a name="manually-configuring-bios"></a>BIOS handmatig configureren 

U moet het BIOS-apparaat handmatig configureren als:

- U hebt uw apparaat niet gekocht via Pijl.

- U hebt een apparaat, maar u hebt geen toegang tot het XML-configuratiebestand.

Nadat u toegang hebt tot het BIOS, gaat u naar **Apparaatinstellingen.**

Handmatig configureren:

1. U kunt het BIOS-apparaat rechtstreeks openen met behulp van een toetsenbord en scherm of iDRAC gebruiken.

   - Als het apparaat geen Defender for IoT-apparaat is, opent u een browser en gaat u naar het IP-adres dat eerder is geconfigureerd. Meld u aan met de standaardbeheerdersbevoegdheden van Dell. Gebruik **de hoofdmap** voor de gebruikersnaam **en het** wachtwoord.

   - Als het apparaat een Defender for IoT-apparaat is, moet u zich aanmelden met **xxx** als gebruikersnaam en **XXX** voor het wachtwoord.

1. Nadat u toegang hebt tot het BIOS, gaat u naar **Apparaatinstellingen.**

1. Kies de raid-gecontroleerde configuratie door **Geïntegreerde RAID-controller 1: Dell PERC \<PERC H330 Adapter\> Configuration Utility te selecteren.**

1. Selecteer **Configuratiebeheer.**

1. Selecteer **Virtuele schijf maken.**

1. Selecteer raid5 in het veld **RAID-niveau** **selecteren.** Voer in **het veld Naam van virtuele** schijf ROOT **in** en selecteer **Fysieke schijven.**

1. Selecteer **Alles controleren** en selecteer vervolgens Wijzigingen **toepassen**

1. Selecteer **OK**.

1. Schuif omlaag en selecteer **Virtuele schijf maken.**

1. Schakel het **selectievakje Bevestigen** in en selecteer **Ja.**

1. Selecteer **OK**.

1. Ga terug naar het hoofdscherm en selecteer **Systeem-BIOS.**

1. Selecteer **Opstartinstellingen.**

1. Selecteer BIOS **voor de** optie **Opstartmodus.**

1. Selecteer **Terug** en selecteer vervolgens **Voltooien om** de BIOS-instellingen af te sluiten.

### <a name="software-installation-dell-r340"></a>Software-installatie (Dell R340)

Het installatieproces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart.

Installeren:

1. Controleer op een van de volgende manieren of de versiemedia aan het apparaat zijn bevestigd:

   - Verbind de externe cd of schijf op een sleutel met de release.

   - De ISO-afbeelding met behulp van iDRAC. Nadat u zich bij iDRAC heeft aanmelden, selecteert u de virtuele console en selecteert u **vervolgens Virtuele media.**

1. Selecteer in **de sectie Cd/dvd-kaart** de **optie Bestand kiezen.**

1. Kies het ISO-versie-afbeeldingsbestand voor deze versie in het dialoogvenster dat wordt geopend.

1. Selecteer de **knop Apparaat in kaart** brengen.

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Schermopname van een apparaat dat is gebruikt.":::

1. Het medium is bevestigd. Selecteer **Sluiten**.

1. Start het apparaat. Wanneer u iDRAC gebruikt, kunt u de servers opnieuw opstarten door de knop Voor beheer **van Dep te** selecteren. Selecteer vervolgens op de **toetsenbordma macro's** de knop **Toepassen,** waarmee de reeks Ctrl+Alt+Delete wordt start.

1. Selecteer **Engels.**

1. Selecteer **SENSOR-RELEASE- \<version\> Enterprise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Selecteer uw sensorversie en bedrijfstype.":::   

1. Definieer het apparaatprofiel en de netwerkeigenschappen:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Schermopname van het apparaatprofiel en de netwerkeigenschappen.":::   

   | Parameter | Configuratie |
   |--|--|
   | **Hardwareprofiel** | **Enterprise** |
   | **Beheerinterface** | **eno1** |
   | **Netwerkparameters (geleverd door de klant)** | - |
   |**IP-adres van beheernetwerk:** | - |
   | **Subnetmasker:** | - |
   | **hostnaam apparaat:** | - |
   | **Dns:** | - |
   | **STANDAARD IP-adres van gateway:** | - |
   | **invoerinterfaces:** |  Het systeem genereert de lijst met invoerinterfaces voor u. Als u de invoerinterfaces wilt spiegelen, kopieert u alle items in de lijst met een kommascheidingsteken. U hoeft de bruginterface niet te configureren. Deze optie wordt alleen gebruikt voor speciale gebruiksgevallen. |

1. Na ongeveer 10 minuten worden de twee sets referenties weergegeven. De ene is voor **een CyberX-gebruiker** en een voor een **ondersteuningsgebruiker.**  

1. Sla de apparaat-id en wachtwoorden op. U hebt deze referenties nodig voor toegang tot het platform wanneer u het voor het eerst gebruikt.

1. Selecteer **Enter om** door te gaan.

## <a name="hpe-proliant-dl20-installation"></a>HPE ProLiant DL20-installatie

In dit artikel wordt het HPE ProLiant DL20-installatieproces beschreven, dat de volgende stappen bevat:

  - Externe toegang inschakelen en het standaardbeheerderswachtwoord bijwerken.
  - BIOS- en RAID-instellingen configureren.
  - Installeer de software.

### <a name="about-the-installation"></a>Over de installatie

  - Enterprise- en SMB-apparaten kunnen worden geïnstalleerd. Het installatieproces is identiek voor beide typen apparaten, met uitzondering van de matrixconfiguratie.
  - Er wordt een standaardgebruiker met beheerdersrechten opgegeven. U wordt aangeraden het wachtwoord te wijzigen tijdens het netwerkconfiguratieproces.
  - Tijdens het netwerkconfiguratieproces configureert u de iLO-poort op netwerkpoort 1.
  - Het installatieproces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20-frontpaneel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20-frontpaneel.":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20-backpaneel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Het achterpaneel van de HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Externe toegang inschakelen en wachtwoord bijwerken

Gebruik de volgende procedure om netwerkopties in te stellen en het standaardwachtwoord bij te werken.

Het wachtwoord inschakelen en bijwerken:

1. Sluit een scherm en toetsenbord aan op het HP-apparaat, schakel het apparaat in en druk **op F9.**

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Schermopname van het venster HPE ProLiant.":::

1. Ga naar **System Utilities**  >  **System Configuration**  >  **iLO 5 Configuration Utility** Network  >  **Options**.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Schermopname van het venster Systeemconfiguratie.":::

    1.  Selecteer **Gedeelde netwerkpoort-LOM in** het veld **Netwerkinterfaceadapter.**
    
    1.  Schakel DHCP uit.
    
    1.  Voer het IP-adres, subnetmasker en gateway-IP-adres in.

1. Selecteer **F10: Opslaan.**

1. Selecteer **Esc** om terug te gaan naar het **iLO 5-configuratieprogramma** en selecteer vervolgens **Gebruikersbeheer.**

1. Selecteer **Gebruiker bewerken/verwijderen.** De beheerder is de enige standaardgebruiker die is gedefinieerd. 

1. Wijzig het standaardwachtwoord en selecteer **F10: Opslaan.**

### <a name="configure-the-hpe-bios"></a>HpE BIOS configureren

In de volgende procedure wordt beschreven hoe u het HPE BIOS configureert voor de onderneming en SMB-apparaten.

Het HPE BIOS configureren:

1. Selecteer **System Utilities**  >  **System Configuration**  >  **BIOS/Platform Configuration (RBSU)**.

1. Selecteer in **het formulier BIOS/Platform Configuration (RBSU)** de optie **Opstartopties.**

1. Wijzig **de opstartmodus** **in verouderde BIOS-modus** en selecteer **F10: Opslaan.**

1. Selecteer **tweemaal Esc** om het formulier **Systeemconfiguratie te** sluiten.

#### <a name="for-the-enterprise-appliance"></a>Voor het bedrijfsapparaat

1. Selecteer **Embedded RAID 1: HPE Smart Array P408i-a SR Gen 10**  >  **Array Configuration** Create  >  **Array**.

1. Selecteer in **het formulier Matrix** maken alle opties. Er zijn drie opties beschikbaar voor het **Enterprise-apparaat.**

#### <a name="for-the-smb-appliance"></a>Voor het SMB-apparaat

1. Selecteer **Embedded RAID 1: HPE Smart Array P208i-a SR Gen 10**  >  **Array Configuration** Create  >  **Array**.

1. Selecteer **Doorgaan naar volgende formulier**.

1. Stel in **het formulier RAID-niveau** instellen het niveau in op **RAID 5** voor bedrijfsimplementaties en **RAID 1** voor SMB-implementaties.

1. Selecteer **Doorgaan naar volgende formulier**.

1. Voer in **het formulier Logical Drive Label** Logical Drive **1 in.**

1. Selecteer **Wijzigingen verzenden.**

1. Selecteer in **het formulier** Verzenden de optie Terug **naar hoofdmenu.**

1. Selecteer **F10: Opslaan en** druk vervolgens twee keer op **Esc.**

1. Selecteer in **het venster Systeemprogramma's** de optie **One-Time Boot Menu**.

1. Selecteer in **het formulier Menu Voor een** keer opstarten de optie Verouderd BIOS One-Time **opstartmenu**.

1. De **vensters Opstarten in Verouderd** en **Overschrijven van opstarten worden** weergegeven. Kies een optie voor het overschrijven van opstarten; bijvoorbeeld naar een cd-rom-, USB-, HDD- of UEFI-shell.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Schermopname van het eerste venster overschrijven van opstarten.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Schermopname van het tweede venster Overschrijven van opstarten.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Software-installatie (HPE ProLiant DL20-apparaat)

Het installatieproces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart.

De software installeren:

1. Sluit het scherm en toetsenbord aan op het apparaat en maak vervolgens verbinding met de CLI.

1. Verbind een externe cd of schijf op de sleutel met de ISO-afbeelding die u hebt gedownload van de **pagina Updates** in de Defender for IoT-portal.

1. Start het apparaat.

1. Selecteer **Engels.**

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Selectie van Engels in het CLI-venster.":::

1. Selecteer **SENSOR-RELEASE- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Schermopname van het scherm voor het selecteren van een versie.":::

1. Definieer in de installatiewizard het hardwareprofiel en de netwerkeigenschappen:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Schermopname van de installatiewizard.":::

    | Parameter | Configuratie |
    | ----------| ------------- |
    | **Hardwareprofiel** | Selecteer **Enterprise-** of **Office** for SMB-implementaties. |
    | **Beheerinterface** | **eno2** |
    | **Standaardnetwerkparameters (meestal worden de parameters geleverd door de klant)** | **IP-adres van beheernetwerk:** <br/> <br/>**hostnaam apparaat:** <br/>**Dns:** <br/>**het IP-adres van de standaardgateway:**|
    | **invoerinterfaces:** | Het systeem genereert de lijst met invoerinterfaces voor u.<br/><br/>Als u de invoerinterfaces wilt spiegelen, kopieert u alle items in de lijst met een kommascheidingsteken: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**Voor HPE DL20: vermeld eno1, enp1s0f4u4 (iLo-interfaces) niet**<br/><br/>**BRIDGE:** u hoeft de bruginterface niet te configureren. Deze optie wordt alleen gebruikt voor speciale gebruiksgevallen. Druk op **Enter** om verder te gaan. |

1. Na ongeveer tien minuten worden de twee sets referenties weergegeven. De ene is **voor een CyberX-gebruiker** en één voor een **ondersteuningsgebruiker.**

1. Sla de id en wachtwoorden van het apparaat op. U hebt de referenties nodig voor de eerste keer toegang tot het platform.

1. Selecteer **Enter om** door te gaan.

## <a name="hpe-proliant-dl360-installation"></a>HPE ProLiant DL360-installatie

  - Er wordt een standaardgebruiker met beheerdersrechten opgegeven. U wordt aangeraden het wachtwoord te wijzigen tijdens de netwerkconfiguratie.

  - Tijdens de netwerkconfiguratie configureert u de iLO-poort.

  - Het installatieproces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360-voorpaneel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360-voorpaneel.":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360-backpaneel

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360-backpaneel.":::

### <a name="enable-remote-access-and-update-the-password"></a>Externe toegang inschakelen en wachtwoord bijwerken

Raadpleeg de voorgaande secties voor hpe ProLiant DL20-installatie:

  - 'Externe toegang inschakelen en wachtwoord bijwerken'

  - 'Het HPE BIOS configureren'

De bedrijfsconfiguratie is identiek.

> [!Note]
> Controleer in het matrixformulier of u alle opties selecteert.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>Externe iLO-installatie (vanaf een virtueel station)

In deze procedure wordt de iLO-installatie vanaf een virtueel station beschreven.

Installeren:

1. Meld u aan bij de iLO-console en klik vervolgens met de rechtermuisknop op het scherm van de servers.

1. Selecteer **HTML5 Console.**

1. Selecteer in de console het cd-pictogram en kies de optie CD/DVD.

1. Selecteer **Lokaal ISO-bestand.**

1. Kies in het dialoogvenster het relevante ISO-bestand.

1. Ga naar het linkerpictogram, selecteer **Power** en selecteer **Opnieuw instellen.**

1. Het apparaat wordt opnieuw opgestart en het installatieproces van de sensor wordt uitgevoerd.

### <a name="software-installation-hpe-dl360"></a>Software-installatie (HPE DL360)

Het installatieproces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart.

Installeren:

1. Sluit het scherm en toetsenbord aan op het apparaat en maak vervolgens verbinding met de CLI.

1. Verbind een externe cd of schijf op een sleutel met de ISO-afbeelding die u hebt gedownload van de **pagina Updates** in de Defender for IoT-portal.

1. Start het apparaat.

1. Selecteer **Engels.**

1. Selecteer **SENSOR-RELEASE- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Schermopname van het selecteren van de versie.":::

1. Definieer in de installatiewizard het apparaatprofiel en de netwerkeigenschappen.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Schermopname van de installatiewizard.":::

    | Parameter | Configuratie |
    | ----------| ------------- |
    | **Hardwareprofiel** | Selecteer **bedrijfsgegevens.** |
    | **Beheerinterface** | **eno2** |
    | **Standaardnetwerkparameters (geleverd door de klant)** | **IP-adres van beheernetwerk:** <br/>**Subnetmasker:** <br/>**hostnaam apparaat:** <br/>**Dns:** <br/>**het standaard-IP-adres van de gateway:**|
    | **invoerinterfaces:**  | Het systeem genereert een lijst met invoerinterfaces voor u.<br/><br/>Als u de invoerinterfaces wilt spiegelen, kopieert u alle items in de lijst met een kommascheidingsteken.<br/><br/> U hoeft de bruginterface niet te configureren. Deze optie wordt alleen gebruikt voor speciale gebruiksgevallen. |

1. Na ongeveer 10 minuten worden de twee sets referenties weergegeven. Een is voor een **CyberX-gebruiker** en één voor een **ondersteuningsgebruiker.**

1. Sla de id en wachtwoorden van het apparaat op. U hebt deze referenties nodig voor de eerste keer toegang tot het platform.

1. Selecteer **Enter om** door te gaan.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Sensorinstallatie voor het virtuele apparaat

U kunt de virtuele machine voor de Defender for IoT-sensor implementeren in de volgende architecturen:


| Architectuur | Specificaties | Gebruik | Opmerkingen |
|---|---|---|---|
| **Enterprise** | CPU: 8<br/>Geheugen: 32G RAM<br/>HDD: 1800 GB | Productieomgeving | Standaard en meest voorkomende |
| **Kleine ondernemingen** | CPU: 4 <br/>Geheugen: 8G RAM<br/>HDD: 500 GB | Testomgevingen of kleine productieomgevingen | -  |
| **Office** | CPU: 4<br/>Geheugen: 8G RAM<br/>HDD: 100 GB | Kleine testomgevingen | -  |

### <a name="prerequisites"></a>Vereisten

De on-premises beheerconsole ondersteunt zowel VMware- als Hyper-V-implementatieopties. Voordat u met de installatie begint, moet u ervoor zorgen dat u de volgende items hebt:

  - VMware (ESXi 5.5 of hoger) of Hyper-V-hypervisor (Windows 10 Pro of Enterprise) geïnstalleerd en operationeel

  - Beschikbare hardwarebronnen voor de virtuele machine

  - ISO-installatiebestand voor de Azure Defender for IoT sensor

Zorg ervoor dat de hypervisor wordt uitgevoerd.

### <a name="create-the-virtual-machine-esxi"></a>De virtuele machine (ESXi) maken

1. Meld u aan bij de ESXi, kies de relevante **gegevensstore** en selecteer **Datastore Browser.**

1. **Upload** de afbeelding en selecteer **Sluiten.**

1. Ga naar **Virtual Machines** en selecteer vervolgens **VM maken/registreren.**

1. Selecteer **Nieuwe virtuele machine maken** en selecteer vervolgens **Volgende.**

1. Voeg een sensornaam toe en kies:

   - Compatibiliteit: nieuwste **&lt; ESXi-versie &gt;**

   - Gast os-familie: **Linux**

   - Versie van **gast-besturingssysteem: Ubuntu Linux (64-bits)**

1. Selecteer **Next**.

1. Kies het relevante gegevensstore en selecteer **Volgende.**

1. Wijzig de parameters voor virtuele hardware op basis van de vereiste architectuur.

1. Voor **CD/DVD Drive 1 selecteert** u **DATAstore ISO-bestand** en kiest u het ISO-bestand dat u eerder hebt geüpload.

1. Selecteer **Volgende** > **voltooien**.

### <a name="create-the-virtual-machine-hyper-v"></a>De virtuele machine maken (Hyper-V)

In deze procedure wordt beschreven hoe u een virtuele machine maakt met behulp van Hyper-V.

Een virtuele machine maken:

1. Maak een virtuele schijf in Hyper-V-manager.

1. Selecteer **format = VHDX**.

1. Selecteer **type = Dynamisch uitbreiden.**

1. Voer de naam en locatie voor de VHD in.

1. Voer de vereiste grootte in (volgens de architectuur).   

1. Controleer de samenvatting en selecteer **Voltooien.**

1. Maak in **het** menu Acties een nieuwe virtuele machine.

1. Voer een naam in voor de virtuele machine.

1. Selecteer **Generatie**  >  **1 opgeven.**

1. Geef de geheugentoewijzing op (volgens de architectuur) en schakel het selectievakje voor dynamisch geheugen in.

1. Configureer de netwerkadapter op basis van de servernetwerktopologie.

1. Verbind de VHDX die u eerder hebt gemaakt met de virtuele machine.

1. Controleer de samenvatting en selecteer **Voltooien.**

1. Klik met de rechtermuisknop op de nieuwe virtuele machine en selecteer **Instellingen.**

1. Selecteer **Hardware toevoegen** en voeg een nieuwe netwerkadapter toe.

1. Selecteer de virtuele switch die verbinding maakt met het sensorbeheernetwerk.

1. WIJS CPU-resources toe (op basis van de architectuur).

1. Verbind de ISO-afbeelding van de beheerconsole met een virtueel dvd-station.

1. Start de virtuele machine.

2. Selecteer in **het** menu Acties de optie **Verbinding maken om** door te gaan met de software-installatie.

### <a name="software-installation-esxi-and-hyper-v"></a>Software-installatie (ESXi en Hyper-V)

In deze sectie wordt de ESXi- en Hyper-V-software-installatie beschreven.

Installeren:

1. Open de console van de virtuele machine.

1. De VM start vanuit de ISO-afbeelding en het scherm voor taalselectie wordt weergegeven. Selecteer **Engels.**

1. Selecteer de vereiste architectuur.

1. Definieer het apparaatprofiel en de netwerkeigenschappen:

    | Parameter | Configuratie |
    | ----------| ------------- |
    | **Hardwareprofiel** | &lt;vereiste architectuur&gt; |
    | **Beheerinterface** | **ens192** |
    | **Netwerkparameters (geleverd door de klant)** | **IP-adres van beheernetwerk:** <br/>**Subnetmasker:** <br/>**hostnaam apparaat:** <br/>**Dns:** <br/>**Standaardgateway:** <br/>**invoerinterfaces:**|
    | **bruginterfaces:** | U hoeft de bruginterface niet te configureren. Deze optie is alleen voor speciale gebruiksgevallen. |

1. Voer **Y in** om de instellingen te accepteren.

1. Aanmeldingsreferenties worden automatisch gegenereerd en weergegeven. Kopieer de gebruikersnaam en het wachtwoord op een veilige plaats, omdat deze vereist zijn voor aanmelden en beheer.

      - **Ondersteuning:** de gebruiker met beheerders beheerdersaccounts voor gebruikersbeheer.

      - **CyberX:** het equivalent van root voor toegang tot het apparaat.

1. Het apparaat wordt opnieuw opgestart.

1. Toegang tot de beheerconsole via het IP-adres dat eerder is geconfigureerd: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Schermopname van toegang tot de beheerconsole.":::

## <a name="on-premises-management-console-installation"></a>Installatie van on-premises beheerconsole

Voordat u de software op het apparaat installeert, moet u de BIOS-configuratie van het apparaat aanpassen:

### <a name="bios-configuration"></a>BIOS-configuratie

Het BIOS voor uw apparaat configureren:

1. [Externe toegang inschakelen en het wachtwoord bijwerken.](#enable-remote-access-and-update-the-password)

1. [Configureer het BIOS.](#configure-the-hpe-bios)

### <a name="software-installation"></a>Software-installatie

Het installatieproces duurt ongeveer 20 minuten. Na de installatie wordt het systeem meerdere keren opnieuw opgestart. 

Tijdens het installatieproces kunt u een secundaire NIC toevoegen. Als u ervoor kiest om de secundaire NIC niet te installeren tijdens de installatie, kunt u op een later tijdstip een secundaire [NIC](#add-a-secondary-nic) toevoegen. 

De software installeren:

1. Selecteer de gewenste taal voor het installatieproces.

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="Selecteer de gewenste taal voor het installatieproces.":::     

1. Selecteer **MANAGEMENT-RELEASE- \<version\> \<deployment type\>**.

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="Selecteer uw versie.":::   

1. Definieer in de installatiewizard de netwerkeigenschappen:

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="Schermopname van het apparaatprofiel.":::   

   | Parameter | Configuratie |
   |--|--|
   | **beheernetwerkinterface configureren** | Voor Dell: **eth0, eth1** <br /> Voor HP: **enu1, enu2** <br /> of <br />**mogelijke waarde** |
   | **IP-adres van beheernetwerk configureren:** | **IP-adres dat is opgegeven door de klant** |
   | **subnetmasker configureren:** | **IP-adres dat is opgegeven door de klant** |
   | **DNS configureren:** | **IP-adres dat is opgegeven door de klant** |
   | **standaard-IP-adres van gateway configureren:** | **IP-adres dat is opgegeven door de klant** |
   
1. **(Optioneel)** Als u een secundaire netwerkinterfacekaart (NIC) wilt installeren, definieert u het volgende apparaatprofiel en de netwerkeigenschappen:

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="Schermopname van de vragen over de installatie van de secundaire NIC.":::

   | Parameter | Configuratie |
   |--|--|
   | **sensorbewakingsinterface configureren (optioneel):** | **eth1** of **mogelijke waarde** |
   | **een IP-adres configureren voor de interface voor sensorbewaking:** | **IP-adres dat door de klant is opgegeven** |
   | **een subnetmasker configureren voor de interface voor sensorbewaking:** | **IP-adres dat door de klant is opgegeven** |

1. Accepteer de settlings en ga door door te `Y` typen. 

1. Na ongeveer 10 minuten worden de twee sets referenties weergegeven. De ene is voor **een CyberX-gebruiker** en één voor een **ondersteuningsgebruiker.**

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="Kopieer deze referenties omdat ze niet opnieuw worden weergegeven.":::  

   Sla de gebruikersnamen en wachtwoorden op. U hebt deze referenties nodig voor toegang tot het platform wanneer u het voor het eerst gebruikt.

1. Selecteer **Enter om** door te gaan.

Zie Uw poort zoeken voor meer informatie over het vinden van de fysieke poort [op uw apparaat.](#find-your-port)

### <a name="add-a-secondary-nic"></a>Een secundaire NIC toevoegen

U kunt de beveiliging van uw on-premises beheerconsole verbeteren door een secundaire NIC toe te voegen. Door een secundaire NIC toe te voegen, hebt u er een toegewezen voor uw gebruikers en de andere biedt ondersteuning voor de configuratie van een gateway voor gerouteerd netwerken. De tweede NIC is toegewezen aan alle gekoppelde sensoren binnen een IP-adresbereik.

Voor beide NIC's is de gebruikersinterface (UI) ingeschakeld. Als routering niet nodig is, zijn alle functies die worden ondersteund door de gebruikersinterface beschikbaar op de secundaire NIC. Hoge beschikbaarheid wordt uitgevoerd op de secundaire NIC.

Als u ervoor kiest geen secundaire NIC te implementeren, zijn alle functies beschikbaar via de primaire NIC. 

Als u uw on-premises beheerconsole al hebt geconfigureerd en u een secundaire NIC wilt toevoegen aan uw on-premises beheerconsole, gebruikt u de volgende stappen:

1. Gebruik de opdracht voor het opnieuw configureren van het netwerk:

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. Voer de volgende antwoorden in op de volgende vragen:

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="Voer de volgende antwoorden in om uw apparaat te configureren.":::

    | Parameters | Antwoord om in te voeren |
    |--|--|
    | **IP-adres van beheernetwerk** | `N` |
    | **Subnetmasker** | `N` |
    | **DNS** | `N` |
    | **STANDAARD-IP-adres van gateway** | `N` |
    | **Interface voor sensorbewaking (optioneel). Dit is van toepassing wanneer sensoren zich in een ander netwerksegment hebben. Zie de installatie-instructies voor meer informatie.**| `Y`, **selecteert u een mogelijke waarde** |
    | **Een IP-adres voor de sensorbewakingsinterface (toegankelijk voor de sensoren)** | `Y`, **IP-adres dat door de klant wordt geleverd**|
    | **Een subnetmasker voor de sensorbewakingsinterface (toegankelijk voor de sensoren)** | `Y`, **IP-adres dat door de klant wordt geleverd** |
    | **Hostnaam** | **geleverd door de klant** |

1. Controleer alle opties en voer in om `Y` de wijzigingen te accepteren. Het systeem wordt opnieuw opgestart.

### <a name="find-your-port"></a>Uw poort zoeken

Als u problemen hebt met het vinden van de fysieke poort op uw apparaat, kunt u de volgende opdracht gebruiken om:

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

Met deze opdracht wordt het licht op de poort voor de opgegeven periode laten knipperen. Als u bijvoorbeeld int, wordt poort eno1 2 minuten geflitst, zodat u de poort op de achterkant van `sudo ethtool -p eno1 120` uw apparaat kunt vinden. 

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Virtueel apparaat: Installatie van on-premises beheerconsole

De on-premises beheerconsole-VM ondersteunt de volgende architecturen:

| Architectuur | Specificaties | Gebruik | 
|--|--|--|
| Enterprise <br/>(Standaard en meest voorkomende) | CPU: 8 <br/>Geheugen: 32G RAM<br/> HDD: 1,8 TB | Grote productieomgevingen | 
| Enterprise | CPU: 4 <br/> Geheugen: 8G RAM<br/> HDD: 500 GB | Grote productieomgevingen |
| Enterprise | CPU: 4 <br/>Geheugen: 8G RAM <br/> HDD: 100 GB | Kleine testomgevingen | 
   
### <a name="prerequisites"></a>Vereisten

De on-premises beheerconsole ondersteunt zowel VMware- als Hyper-V-implementatieopties. Controleer het volgende voordat u met de installatie begint:

- VMware (ESXi 5.5 of hoger) of Hyper-V-hypervisor (Windows 10 Pro of Enterprise) is geïnstalleerd en operationeel.

- De hardwarebronnen zijn beschikbaar voor de virtuele machine.

- U hebt het ISO-installatiebestand voor de on-premises beheerconsole.
    
- De hypervisor wordt uitgevoerd.

### <a name="create-the-virtual-machine-esxi"></a>De virtuele machine (ESXi) maken

Een virtuele machine maken (ESXi):

1. Meld u aan bij de ESXi, kies de relevante **gegevensstore** en selecteer **Datastore Browser.**

1. Upload de afbeelding en selecteer **Sluiten.**

1. Ga naar **Virtual Machines**.

1. Selecteer **VM maken/registreren.**

1. Selecteer **Nieuwe virtuele machine maken en** selecteer **Volgende.**

1. Voeg een sensornaam toe en kies:

   - Compatibiliteit: \<latest ESXi version>

   - Gast os-familie: Linux

   - Versie van gast-besturingssysteem: Ubuntu Linux (64-bits)

1. Selecteer **Next**.

1. Kies relevante gegevensstore en selecteer **Volgende.**

1. Wijzig de parameters voor virtuele hardware op basis van de vereiste architectuur.

1. Voor **CD/DVD Drive 1 selecteert** u **DATAstore ISO-bestand** en kiest u het ISO-bestand dat u eerder hebt geüpload.

1. Selecteer **Volgende** > **voltooien**.

### <a name="create-the-virtual-machine-hyper-v"></a>De virtuele machine maken (Hyper-V)

Een virtuele machine maken met hyper-V:

1. Maak een virtuele schijf in Hyper-V-manager.

1. Selecteer de indeling **VHDX.**

1. Selecteer **Next**.

1. Selecteer het type **Dynamisch uitbreiden.**

1. Selecteer **Next**.

1. Voer de naam en locatie voor de VHD in.

1. Selecteer **Next**.

1. Voer de vereiste grootte in (op basis van de architectuur).

1. Selecteer **Next**.

1. Controleer de samenvatting en selecteer **Voltooien.**

1. Maak in **het** menu Acties een nieuwe virtuele machine.

1. Selecteer **Next**.

1. Voer een naam in voor de virtuele machine.

1. Selecteer **Next**.

1. Selecteer **Generatie** en stel deze in **op Generatie 1.**

1. Selecteer **Next**.

1. Geef de geheugentoewijzing op (volgens de architectuur) en schakel het selectievakje voor dynamisch geheugen in.

1. Selecteer **Next**.

1. Configureer de netwerkadapter op basis van de servernetwerktopologie.

1. Selecteer **Next**.

1. Verbind de VHDX die u eerder hebt gemaakt met de virtuele machine.

1. Selecteer **Next**.

1. Controleer de samenvatting en selecteer **Voltooien.**

1. Klik met de rechtermuisknop op de nieuwe virtuele machine en selecteer **Instellingen.**

1. Selecteer **Hardware toevoegen** en voeg een nieuwe adapter toe voor **netwerkadapter**.

1. Bij **Virtuele switch selecteert** u de switch die verbinding maakt met het sensorbeheernetwerk.

1. WIJS CPU-resources toe (op basis van de architectuur).

1. Verbind de ISO-afbeelding van de beheerconsole met een virtueel dvd-station.

1. Start de virtuele machine.

1. Selecteer in **het** menu Acties de optie **Verbinding maken om** door te gaan met de software-installatie.

### <a name="software-installation-esxi-and-hyper-v"></a>Software-installatie (ESXi en Hyper-V)

Als u de virtuele machine start, start u het installatieproces vanuit de ISO-installatie.

De software installeren:

1. Selecteer **Engels.**

1. Selecteer de vereiste architectuur voor uw implementatie.

1. Definieer de netwerkinterface voor het sensorbeheernetwerk: interface, IP, subnet, DNS-server en standaardgateway.

1. Aanmeldingsreferenties worden automatisch gegenereerd. Sla de gebruikersnaam en wachtwoorden op. U hebt deze referenties nodig voor toegang tot het platform wanneer u het voor het eerst gebruikt.

   Het apparaat wordt vervolgens opnieuw opgestart.

1. Toegang tot de beheerconsole via het IP-adres dat eerder is geconfigureerd: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Schermopname van het aanmeldingsscherm van de beheerconsole.":::

## <a name="post-installation-validation"></a>Validatie na installatie

Als u de installatie van een fysiek apparaat wilt valideren, moet u veel tests uitvoeren. Hetzelfde validatieproces is van toepassing op alle typen apparaten.

Voer de validatie uit met behulp van de GUI of de CLI. De validatie is beschikbaar voor **de** gebruikersondersteuning en de gebruiker **CyberX.**

Validatie na de installatie moet de volgende tests bevatten:

  - **Sanity test:** controleer of het systeem wordt uitgevoerd.

  - **Versie:** controleer of de versie juist is.

  - **ifconfig:** controleer of alle invoerinterfaces worden uitgevoerd die tijdens het installatieproces zijn geconfigureerd.

### <a name="checking-system-health-by-using-the-gui"></a>Systeemtoestand controleren met behulp van de GEBRUIKERSINTERFACE

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Schermopname van de statuscontrole van het systeem.":::

#### <a name="sanity"></a>Sanity

- **Apparaat:** voert de controle op de werking van het apparaat uit. U kunt dezelfde controle uitvoeren met behulp van de CLI-opdracht `system-sanity` .

- **Versie:** hier wordt de versie van het apparaat weergegeven.

- **Netwerkeigenschappen:** geeft de netwerkparameters van de sensor weer.

#### <a name="redis"></a>Redis

- **Geheugen:** geeft een algemeen beeld van het geheugengebruik, zoals hoeveel geheugen is gebruikt en hoeveel er is gebleven.

- **Langste sleutel:** geeft de langste sleutels weer die een uitgebreid geheugengebruik kunnen veroorzaken.

#### <a name="system"></a>Systeem

- **Kernlogboek:** bevat de laatste 500 rijen van het kernlogboek, zodat u de recente logboekrijen kunt weergeven zonder het hele systeemlogboek te exporteren.

- **Taakbeheer:** vertaalt de taken die worden weergegeven in de tabel met processen naar de volgende lagen: 
  
  - Permanente laag (Redis) 
  - Cash layer (SQL)

- **Netwerkstatistieken:** geeft uw netwerkstatistieken weer.

- **TOP:** Toont de tabel met processen. Het is een Linux-opdracht die een dynamische realtime weergave van het lopende systeem biedt.

- **Back-upgeheugencontrole:** geeft de status van het back-upgeheugen weer en controleert het volgende:
  - De locatie van de back-upmap 
  - De grootte van de back-upmap
  - De beperkingen van de back-upmap
  - Wanneer de laatste back-up is gemaakt
  - Hoeveel ruimte er is voor de extra back-upbestanden

- **ifconfig:** geeft de parameters weer voor de fysieke interfaces van het apparaat.

- **CyberX-nload:** geeft netwerkverkeer en bandbreedte weer met behulp van de tests van zes seconden.

- **Fouten van Core, logboek:** geeft fouten weer uit het kernlogboekbestand.

Voor toegang tot het hulpprogramma:

1. Meld u aan bij de sensor met de referenties **van** de ondersteuningsgebruiker.

1. Selecteer **Systeemstatistieken** in het **venster Systeeminstellingen.**

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Systeemtoestand controleren met behulp van de CLI

**Test 1: Sanity**

Controleer of het systeem actief is:

1. Maak verbinding met de CLI via de Linux-terminal (bijvoorbeeld PuTTY) en de gebruiker **Support**.

1. Voer `system sanity` in.

1. Controleer of alle services groen zijn (actief).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Schermopname van het uitvoeren van services.":::

1. Controleer of **het systeem UP! is. (prod)** wordt onderaan weergegeven.

**Test 2: Versiecontrole**

Controleer of de juiste versie wordt gebruikt:

1. Maak verbinding met de CLI via de Linux-terminal (bijvoorbeeld PuTTY) en de gebruiker **Support**.

1. Voer `system version` in.

1. Controleer of de juiste versie wordt weergegeven.

**Test 3: Netwerkvalidatie**

Controleer of alle invoerinterfaces die tijdens het installatieproces zijn geconfigureerd, worden uitgevoerd:

1. Maak verbinding met de CLI via de Linux-terminal (bijvoorbeeld PuTTY) en de **gebruikersondersteuning**.

1. Voer `network list` in (het equivalent van de Linux-opdracht `ifconfig` ).

1. Controleer of de vereiste invoerinterfaces worden weergegeven. Als er bijvoorbeeld twee koperen quad-NIC's zijn geïnstalleerd, moeten er 10 interfaces in de lijst staan.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Schermopname van de lijst met interfaces.":::

**Test 4: Beheertoegang tot de gebruikersinterface**

Controleer of u toegang hebt tot de web-GUI van de console:

1. Sluit een laptop met een Ethernet-kabel aan op de beheerpoort **(Gb1).**

1. Definieer het NIC-adres van de laptop dat zich binnen hetzelfde bereik bevindt als het apparaat.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Schermopname van beheertoegang tot de gebruikersinterface.":::

1. Ping het IP-adres van het apparaat vanaf de laptop om de connectiviteit te controleren (standaard: 10.100.10.1).

1. Open de Chrome-browser op de laptop en voer het IP-adres van het apparaat in.

1. Selecteer in **het venster Uw verbinding is geen privé** de optie **Geavanceerd** en ga door.

1. De test is geslaagd wanneer het aanmeldingsscherm van Defender for IoT wordt weergegeven.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Schermopname van de toegang tot de beheerconsole.":::

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="you-cant-connect-by-using-a-web-interface"></a>U kunt geen verbinding maken met behulp van een webinterface

1. Controleer of de computer die u probeert te verbinden zich in hetzelfde netwerk bevindt als het apparaat.

1. Controleer of het GUI-netwerk is verbonden met de beheerpoort.

1. Ping het IP-adres van het apparaat. Als er geen ping is:

   1. Sluit een monitor en een toetsenbord aan op het apparaat.

   1. Gebruik de **gebruiker en** het wachtwoord voor ondersteuning om u aan te melden.

   1. Gebruik de opdracht om `network list` het huidige IP-adres weer te geven.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Schermopname van de netwerklijst.":::

1. Als de netwerkparameters onjuist zijn geconfigureerd, gebruikt u de volgende procedure om deze te wijzigen:

   1. Gebruik de opdracht `network edit-settings` .

   1. Als u het IP-adres van het beheernetwerk wilt wijzigen, **selecteert u Y**.

   1. Als u het subnetmasker wilt wijzigen, selecteert **u Y**.

   1. Als u de DNS wilt wijzigen, selecteert **u Y**.

   1. Als u het standaard-IP-adres van de gateway wilt wijzigen, **selecteert u Y**.

   1. Selecteer **N** voor de wijziging van de invoerinterface (alleen sensor).

   1. Selecteer Y om de instellingen **toe te passen.**

1. Maak na het opnieuw opstarten verbinding met de referenties van de ondersteuningsgebruiker en gebruik de `network list` opdracht om te controleren of de parameters zijn gewijzigd.

1. Probeer opnieuw te pingen en verbinding te maken vanuit de gebruikersinterface.

### <a name="the-appliance-isnt-responding"></a>Het apparaat reageert niet

1. Sluit een monitor en toetsenbord aan op het apparaat of gebruik PuTTY om extern verbinding te maken met de CLI.

1. Gebruik de **referenties van** de ondersteuningsgebruiker om u aan te melden.

1. Gebruik de `system sanity` opdracht en controleer of alle processen worden uitgevoerd.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Schermopname van de opdracht voor systeem sanity.":::

Voor andere problemen kunt u contact opnemen [met Microsoft-ondersteuning.](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Bijlage A: Poort spiegelen op vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Een SPAN-poort configureren op een bestaande vSwitch

Een vSwitch heeft geen mogelijkheden voor spiegelen, maar u kunt een tijdelijke oplossing gebruiken om een SPAN-poort te implementeren.

Een SPAN-poort configureren:

1. Open eigenschappen van vSwitch.

1. Selecteer **Toevoegen**.

1. Selecteer **Volgende virtuele**  >  **machine.**

1. Voeg een netwerklabel **SPAN Network in,** **selecteer VLAN ID**  >  **All** en selecteer **vervolgens Volgende.**

1. Selecteer **Finish**.

1. Selecteer **SPAN Network** > **Edit.*

1. Selecteer **Beveiliging** en controleer of het **beleid Promiscuous Mode** is ingesteld op De **modus** Accepteren.

1. Selecteer **OK** en selecteer vervolgens **Sluiten om** de eigenschappen van de vSwitch te sluiten.

1. Open de **eigenschappen van de XSense-VM.**

1. Selecteer **voor Netwerkadapter 2** het **SPAN-netwerk.**

1. Selecteer **OK**.

1. Maak verbinding met de sensor en controleer of spiegelen werkt.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Bijlage B: Toegangssensoren vanuit de on-premises beheerconsole

U kunt de systeembeveiliging verbeteren door directe gebruikerstoegang tot de sensor te voorkomen. Gebruik in plaats daarvan proxytunneling om gebruikers toegang te geven tot de sensor vanuit de on-premises beheerconsole met één firewallregel. Deze techniek beperkt de mogelijkheid van onbevoegde toegang tot de netwerkomgeving buiten de sensor. De ervaring van de gebruiker bij het aanmelden bij de sensor blijft hetzelfde.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Schermopname van de toegang tot de sensor.":::

Tunneling inschakelen:

1. Meld u aan bij de CLI van de  on-premises beheerconsole met **cyberx-** of ondersteuningsgebruikersreferenties.

1. Voer `sudo cyberx-management-tunnel-enable` in.

1. Druk op **Enter**.

1. Voer `--port 10000` in.

### <a name="next-steps"></a>Volgende stappen

[Uw netwerk instellen](how-to-set-up-your-network.md)