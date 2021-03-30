---
title: 'Zelfstudie: De Azure FXT Edge Filer-cachecluster'
description: Lees hoe u een hybride opslagcachecluster maakt met de Azure FXT Edge Filer en hoe u zich aanmeldt bij het configuratiescherm van het cluster om de clusterinstellingen te configureren.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: d019f5df4bba6d223076c8ce35151510afedf2e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220802"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Zelfstudie: De Azure FXT Edge Filer-cluster maken

Nadat u de Azure FXT Edge Filer-hardwareknooppunten voor uw cache heeft geïnstalleerd, gebruikt u de FXT-clustersoftware om de cluster te maken.

In deze zelfstudie ontdek u stapsgewijs hoe u uw hardwarenodes als een cluster moet configureren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Welke informatie vereist is voordat u de cluster aanmaakt
> * Het verschil tussen het beheersnetwerk van de cluster, het clusternetwerk en het clientgerichte netwerk
> * Verbinding maken met een clusterknooppunt
> * Een initiële cluster maken met een Azure FXT Edge Filer-knooppunt
> * Aanmelden bij het configuratiescherm van de cluster om de clusterinstellingen te configureren

Deze procedure neemt tussen 15 en 45 minuten in beslag, afhankelijk van hoeveel onderzoek u moet doen om IP-adressen en netwerkresources te identificeren.

## <a name="prerequisites"></a>Vereisten

Zorg dat u aan deze vereisten voldoet voordat u aan deze zelfstudie begint:

* Installeer uw FXT Edge Filer-hardwaresysteem in uw datacentrum

  U hebt slechts één knooppunt nodig om de cluster te maken, maar u moet [ten minste twee aanvullende knooppunten toevoegen](fxt-add-nodes.md) voordat u de cluster kunt configureren en gebruiken.

* Verbind de juiste stroom- en netwerkkabels met het systeem  
* Schakel ten minste één Azure FXT Edge-knooppunt in en [stel het hoofdwachtwoord in](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Verzamel informatie voor de cluster

U heeft de volgende informatie nodig om de Azure FXT Edge Filer-cluster te maken:

* Clusternaam

* Beheerderswachtwoord om in te stellen voor de cluster

* IP-adressen:

  * Eén IP-adres dat clusterbeheer, het netmasker en de router kunnen gebruiken voor het beheersnetwerk
  * Het eerste en laatste IP-adres in een aaneengesloten bereik van IP-adressen voor clustercommunicatie (tussen knooppunten). Zie [Distributie van IP-adressen](#ip-address-distribution) hieronder voor details.
  * (Clientgerichte IP-adressen worden na het aanmaken van de cluster ingesteld.)

* Informatie over de netwerkinfrastructuur:

  * Het IP-adres van een DNS-server voor de cluster
  * De naam van het DNS-domein voor de cluster
  * De naam of het IP-adres voor de NTP-servers van de cluster (één, drie of meer servers)
  * Of u het samenvoegen van koppelingen via IEEE 802.1AX-2008 wilt inschakelen in de interface van de cluster
  * Als u het samenvoegen van koppelingen inschakelt, of u dynamische samenvoeging via IEEE 802.3ad (LACP) wilt gebruiken

U kunt deze onderdelen van de netwerkinfrastructuur configureren nadat u de cluster heeft aangemaakt, maar het is beter om dat meteen te doen.

### <a name="ip-address-distribution"></a>Distributie IP-adres

De hybride opslagcachecluster Azure FXT Edge Filer gebruikt IP-adressen in drie categorieën:

* Beheers-IP: Eén IP-adres voor clusterbeheer

  Dit adres dient als het beginpunt voor toegang tot de hulpprogramma's clusterconfiguratie (het webgebaseerde configuratiescherm of de XML-RPC-API). Dit adres wordt automatisch toegewezen aan het primaire knooppunt in de cluster, en het verandert automatisch mee als het primaire knooppunt verandert.

  Andere IP-adressen kunnen worden gebruikt om toegang te krijgen tot het configuratiescherm, maar het beheers-IP-adres is ontworpen om toegang te bieden wanneer er een failover optreedt bij individuele knooppunten.

* Clusternetwerk: Een bereik van IP-adressen voor clustercommunicatie

  Het clusternetwerk wordt gebruikt voor communicatie tussen clusterknooppunten en om bestanden op te halen uit de back-endopslag (kernfilers).

  **Best practice:** Wijs één IP-adres toe per fysieke poort die gebruikt wordt voor clustercommunicatie op elk Azure FXT Edge Filer-knooppunt. De cluster wijs automatisch de adressen in het opgegeven bereik toe aan individuele knooppunten.

* Clientgericht netwerk: Het bereik van IP-adressen die clients gebruiken om bestanden aan te vragen en te schrijven

  De client-netwerkadressen die gebruikt worden door clients om toegang te krijgen tot de gegevens in de kernopslageenheid via de cluster. Zo kan bijvoorbeeld een NFS-client zich koppelen aan een van deze adressen.

  **Best practice:** Wijs één IP-adres toe per fysieke poort die gebruikt wordt voor clientcommunicatie op elk FXT Series-knooppunt.

  De cluster verdeelt clientgerichte IP-adressen zo gelijkmatig mogelijk over zijn knooppunten.

  Om het gemakkelijker te maken configureren veel beheerders één DNS met RRDNS-configuratie (round robin DNS) om gemakkelijker clientverzoeken te verdelen over het adresbereik. Dankzij deze configuratie kunnen alle clients dezelfde koppelingsopdracht gebruiken om toegang te krijgen tot de cluster. Lees [DNS configureren](fxt-configure-network.md#configure-dns-for-load-balancing) voor meer informatie.

U moet een beheers-IP-adres en een bereik van clusternetwerkadressen opgeven om een nieuwe cluster aan te maken. Clientgerichte adressen worden na het aanmaken van de cluster opgegeven.

## <a name="connect-to-the-first-node"></a>Verbinden met het eerste knooppunt

U kunt ook verbinding maken met een van de geïnstalleerde FXT-knooppunten en de besturingssysteemsoftware gebruiken om de cluster in te stellen.

Als u dit nog niet hebt gedaan, schakelt u ten minste één van de FXT-knooppunten voor uw cluster aan en controleert u of deze een netwerkverbinding en een IP-adres heeft. U moet een nieuw hoofdwachtwoord instellen om het knooppunt te activeren. Volg dus de stappen in [Hardware-wachtwoorden instellen](fxt-node-password.md) als u dat nog niet heeft gedaan.

Om de netwerkverbinding te controleren zorgt u ervoor dat de leds voor de netwerkkoppeling van het knooppunt (en, indien nodig, de indicatoren op de gekoppelde netwerkschakelaar) oplichten. Controlelampjes worden beschreven in [Azure FXT Edge Filer-hardwarestatus bewaken](fxt-monitor.md).

Wanneer het knooppunt opstart wordt een IP-adres gevraagd. Als het is verbonden met een DHCP-server, dan wordt het IP-adres dat voorzien is door DHCP aanvaard. (Dit IP-adres is tijdelijk. Het verandert wanneer u de cluster aanmaakt.)

Als het niet gekoppeld is aan een DHCP-server of geen antwoord krijgt, gebruikt het knooppunt Bonjour-software om een zelftoegewezen IP-adres in te stellen in het formaat 169.254.\*.\*. U moet echter een tijdelijk statisch IP-adres instellen op een van de netwerkkaarten van het knooppunt alvorens het te gebruiken om een cluster te maken. U vindt instructies in dit verouderde document; neem contact op met de klantenservice van Microsoft voor bijgewerkte informatie: [Bijlage A: Een statisch IP-adres instellen op een FXT-knooppunt](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Zoek het IP-adres

Maak verbinding met het Azure FXT Edge Filer-knooppunt om het IP-adres te vinden. U kunt een seriële kabel gebruiken, rechtstreekse verbinding maken met de USB- en VGA-poorten of verbinden via een KVM-schakelaar. (Zie [Initiële wachtwoorden instellen](fxt-node-password.md) voor informatie over poortverbindingen.)

Nadat u verbinding hebt gemaakt, meldt u zich aan met de gebruikersnaam `root` en het wachtwoord dat u hebt ingesteld toen u het knooppunt de eerste keer opstartte.  

Nadat u zich hebt aangemeld, moet u het IP-adres van het knooppunt bepalen.

Gebruik de opdracht `ifconfig` om het adres dat toegewezen is aan dit systeem te zien.

De opdracht `ifconfig | grep -B5 inet` bijvoorbeeld zoekt naar poorten met internetadressen en vijf regels context om de poort-id weer te geven.

Noteer IP-adressen die worden weergegeven in ifconfig-rapport. Adressen die worden weergegeven met poortnamen, zoals e0a of e0b, zijn goede opties. Gebruik geen IP-adressen die worden vermeld met E7*-namen, aangezien deze namen alleen worden gebruikt voor iDRAC/IPMI-servicepoorten.

## <a name="load-the-cluster-configuration-wizard"></a>De configuratiewizard voor de cluster laden

Gebruik de browsergebaseerde configuratietool voor de cluster om de cluster te maken.

Voer het IP-adres voor het knooppunt in een browser in. Als de browser een bericht weergeeft dat de site niet vertrouwd wordt, ga dan gewoon verder naar de site. (Individuele Azure FXT Edge Filer-knooppunten hebben geen door CA verstrekte beveiligingscertificaten.)

![Aanmeldingspagina voor configuratiescherm in browservenster](media/fxt-cluster-create/unconfigured-node-gui.png)

Laat de velden **Gebruikersnaam** en **Wachtwoord** leeg. Klik op **Aanmelden** om de pagina om een cluster aan te maken te laden.

![Eerste installatiescherm voor een ongeconfigureerd knooppunt in het browsergebaseerde configuratiescherm. Het bevat opties om software bij te werken, een cluster handmatig bij te werken of een cluster te configureren vanuit een installatiebestand.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Het cluster maken

Het hulpprogramma voor clusterconfiguratie begeleidt u door een aantal schermen om de Azure FXT Edge Filer-cluster aan te maken. Zorg ervoor dat u de [vereiste informatie](#gather-information-for-the-cluster) bij de hand hebt voor u begint.

### <a name="creation-options"></a>Opties voor maken

Het eerste scherm geeft u drie opties. Gebruik de optie handmatige configuratie tenzij u bijzondere instructies kreeg van een ondersteuningsmedewerker.

Klik op **Ik ga de cluster handmatig configureren** om het scherm met opties voor de configuratie van de nieuwe cluster te laden.

De andere opties worden zelden gebruikt:

* 'De installatiekopie van het systeem bijwerken' vraagt u om nieuwe software voor het besturingssysteem te installeren voor u de cluster maakt. (De softwareversie die momenteel is geïnstalleerd, wordt bovenaan het scherm weergegeven.) U moet het softwarepakketbestand voorzien, via een URL en gebruikersnaam/wachtwoord of door een bestand te uploaden vanaf uw computer.

* De optie met een installatiebestand voor de cluster wordt soms gebruikt door Microsoft Customer Service and Support.

## <a name="cluster-options"></a>Clusteropties

In het volgende scherm wordt u gevraagd om de opties voor de nieuwe cluster te configureren.

De pagina is opgedeeld in twee hoofdsecties, **Basisconfiguratie** en **Netwerkconfiguratie**. De sectie netwerkconfiguratie bevat ook subsecties: een voor het netwerk **Beheer** en een voor het netwerk **Cluster**.

### <a name="basic-configuration"></a>Basisconfiguratie

Vul de basisinformatie voor de nieuwe cluster in de bovenste sectie in.

![Detail van de sectie 'Basisconfiguratie' op de browserpagina. Er zijn drie velden te zien (clusternaam, beheerswachtwoord en wachtwoord bevestigen)](media/fxt-cluster-create/basic-configuration.png)

* **Clusternaam**: voer een unieke naam voor de cluster in.

  De clusternaam moet aan de volgende voorwaarden voldoen:
  
  * Lengte van 1 tot 16 tekens
  * Kan letters, cijfers en het streepje (-) of het onderstrepingsteken (_) bevatten
  * Mag geen andere interpunctie of speciale tekens bevatten
  
  U kunt deze naam later veranderen op de configuratiepagina **Cluster** > **Algemene instellingen**. (Lees voor meer informatie de [Handleiding voor clusterconfiguratie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), die geen deel uitmaakt van deze documentatie.)

  > [!NOTE]
  > Uw clusternaam wordt gebruikt voor de identificatie van systeeminformatie die u uploadt naar ondersteuning voor controle of probleemoplossing. Daarom is het zinvol om uw bedrijfsnaam toe te voegen.

* **Beheerderswachtwoord**: stel het wachtwoord in voor de standaardgebruiker met beheerdersrechten, `admin`.
  
  U moet een individuele gebruikersaccounts instellen voor iedereen die de cluster beheert, maar kun gebruiker `admin` niet verwijderen. Meld u als `admin` aan indien u aanvullende gebruikers wilt aanmaken.

  U kunt het wachtwoord voor `admin` wijzigen op de instellingenpagina **Beheer** > **Gebruikers** in het configuratiescherm voor de cluster. Lees voor meer informatie de documentatie voor **Gebruikers** in de [Handleiding voor clusterconfiguratie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Netwerkconfiguratie

De sectie **Netwerk** vraagt u om de netwerkinfrastructuur die de cluster zal gebruiken op te geven.

Er moeten twee afzonderlijke netwerken geconfigureerd worden:

* Het *beheernetwerk* biedt beheerderstoegang tot de cluster voor configuratie en controle. Het IP-adres dat hier wordt opgegeven, wordt gebruikt om verbinding te maken met het Configuratiescherm of voor SSH-toegang.

  De meeste clusters gebruiken slechts één IP-adres voor beheer, maar als u interfaces wilt toevoegen dan kan dat nadat u de cluster hebt aangemaakt.

* Het *clusternetwerk* wordt gebruikt voor communicatie tussen clusterknooppunten onderling en tussen clusterknooppunten en back-endopslag (kernfilers).

Het clientgerichte netwerk wordt later geconfigureerd, nadat de cluster is aangemaakt.

Deze sectie bevat ook de configuratie voor DNS- en NTP-servers die door beide netwerken gebruiks worden.

### <a name="configure-the-management-network"></a>Het beheernetwerk configureren

De instellingen in de sectie **Beheer** dienen voor het netwerk dat beheerderstoegang voor de cluster biedt.

![detail van de sectie 'Beheer', met velden voor 5 opties en een selectievakje voor 1Gb-beheernetwerk](media/fxt-cluster-create/management-network-filled.png)

* **Beheer-IP**: geef het IP-adres op dat u gaat gebruiken om het Configuratiescherm van de cluster te openen. Dit adres wordt geclaimd door het primaire knooppunt van de cluster, maar het schakelt automatisch over op een gezond knooppunt als het oorspronkelijke, primaire knooppunt niet meer beschikbaar is.

  De meeste clusters gebruiken slechts één IP-adres voor beheer. Als u er meer dan één wilt, dan kunt u ze toevoegen nadat u de cluster hebt aangemaakt op de instellingenpagina **Cluster** > **Beheernetwerk**. Lees meer in de [Handleiding voor clusterconfiguratie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Netmasker**: geef het netmasker voor het beheernetwerk op.

* **Router**: voer het standaard gatewayadres dat gebruikt wordt door het beheersnetwerk.

* **VLAN-tag (optioneel)** : als uw cluster VLAN-tags gebruikt, geef dan de tag voor het beheernetwerk op.

  U kunt aanvullende VLAN-instellingen configureren op de instellingenpagina **Cluster** > **VLAN**. Lees [Werken met VLAN's](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) en [Cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) in de handleiding voor clusterconfiguratie voor meer informatie.

* **MTU**: pas de MTU (maximum transmission unit) voor het beheersnetwerk van uw cluster aan.

* **1Gb mgmt-netwerk gebruiken**: vink dit selectievakje aan om de twee 1GbE-netwerkpoorten op uw FXT-knooppunten enkel toe te wijzen aan het beheernetwerk. (U moet 25GbE/10GbE-poorten beschikbaar laten voor al het overige verkeer.) Als u dit selectievakje niet inschakelt, gebruikt het beheernetwerk de poort met de hoogste beschikbare snelheid.

### <a name="configure-the-cluster-network"></a>Het clusternetwerk configureren

De instellingen van het clusternetwerk zijn van toepassing op verkeer tussen de clusterknooppunten onderling en tussen clusterknooppunten en kern-filers.

![detail van de sectie 'Cluster' met velden voor zes waarden](media/fxt-cluster-create/cluster-network-filled.png)

* **Eerste IP** en **Laatste IP**: voer de IP-adressen in die het bereik voor interne clustercommunicatie definiëren. De IP-adressen die hier worden gebruikt, moeten aaneengesloten zijn en niet toegewezen door DHCP.

  U kunt meer IP-adressen toevoegen nadat u de cluster hebt gemaakt. Gebruik de instellingenpagina **Cluster** > **Clusternetwerken** ([Documentatie van de handleiding voor clusterconfiguratie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  De waarde bij **Aantal IP's in bereik** wordt automatisch berekend en weergegeven.

* **Niet-beheersnetmasker (optioneel)** : geef het netmasker voor het clusternetwerk op.

  Het systeem stelt automatisch de netmaskerwaarde voor die u hebt ingevoerd voor het beheernetwerk. Verander dit indien nodig.

* **Clusterrouter (optioneel)** : geeft het standaard gatewayadres op dat gebruikt wordt door het clusternetwerk.

  Het systeem stelt automatisch hetzelfde gatewayadres voor dat u hebt opgegeven voor het beheernetwerk.

* **VLAN-tag cluster (optioneel)** : als uw cluster VLAN-tags gebruikt, geef dan de tag voor het clusternetwerk op.

* **Niet-beheers-MTU (optioneel)** : pas indien nodig de MTU (maximal transmission unit) voor uw clusternetwerk aan.

### <a name="configure-cluster-dns-and-ntp"></a>DNS en NTP van de cluster configureren

Onder de sectie **Cluster** bevinden zich velden waarin u de DNS- en NTP-server kunt opgeven en het samenvoegen van koppelingen kunt inschakelen. Deze instellingen zijn van toepassing op alle netwerken die de cluster gebruikt

![Details van de sectie voor DNS/NTP-configuratie, met drie velden voor DNS-servers, velden voor DNS-domeinen en DNS-zoeken, drie velden voor NTP-servers en een vervolgkeuzemenu met opties voor het samenvoegen van koppelingen](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS-server(s)** : voer het IP-adres van één of meer DNS-servers (domain name system) in.

  DNS is aanbevolen voor alle clusters, en vereist als u SMB, AD of Kerberos wilt gebruiken.
  
  Configureer voor optimale prestaties de DNS-server van de cluster voor round-robin load balancing zoals beschreven in [DNS configureren voor de Azure FXT Edge Filer-cluster](fxt-configure-network.md#configure-dns-for-load-balancing).

* **DNS-domein**: voer de naam van het netwerkdomein dat de cluster zal gebruiken in.

* **DNS-zoeken**: optioneel, voer aanvullende domeinnamen in die het systeem moet zoeken om DNS-query's op te lossen. U kunt tot zes domeinnamen toevoegen, gescheiden door spaties.

* **NTP-server(s)** : geef één of drie NTP-servers (network time protocol) in de voorziene velden in. U kunt hostnamen of IP-adressen gebruiken.

* **Samenvoegen van koppelingen**: samenvoegen van koppelingen stelt u in staat om te bepalen hoe de ethernetpoorten op de FXT-knooppunten van de cluster omgaan met verschillende soorten verkeer. Lees [Samenvoegen van koppelingen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) in de handleiding voor clusterconfiguratie voor meer informatie.

### <a name="click-the-create-button"></a>Klik op de knop maken

Nadat u alle vereiste instellingen in het formulier hebt opgegeven, klikt u op de knop **Cluster maken**.

![onderkant van ingevuld formulier met cursor op de knop maken in de linkerbenedenhoek](media/fxt-cluster-create/create-cluster.png)

Het systeem geef een bericht weer tijdens het maken van de cluster

![statusbericht clusterconfiguratie in browser: 'Het FXT-knooppunt is nu de cluster aan het maken. Dit duurt enkele minuten. Zodra de cluster is aangemaakt, bezoekt u deze koppeling om de configuratie te voltooien.' met hyperlink op 'bezoekt u deze koppeling'](media/fxt-cluster-create/creating-message.png)

Na enkele ogenblikken kunt u klikken op de koppeling in het bericht om naar het Configuratiescherm van de cluster te gaan. (Deze koppeling leidt u naar het IP-adres dat u heeft opgegeven in **Beheers-IP**.) Het duurt tussen 15 seconden en 1 minuut voordat de koppeling actief wordt nadat u op de knop maken heeft geklikt. Als het webinterface niet geladen wordt, wacht dan nog enkele seconden en klik opnieuw op de koppeling.

Het maken van de cluster duurt een minuut of langer, maar u kunt zich aanmelden bij het Configuratiescherm terwijl dat het proces aan de gang is. Het is normaal dat de dashboardpagina van het configuratiescherm waarschuwingen weergeeft tot de cluster is aangemaakt.

## <a name="open-the-settings-pages"></a>Open de pagina Instellingen

Nadat u de cluster hebt gemaakt, moet u de configuratie van het netwerk en de werkstroom aanpassen.

Gebruik de webinterface van het Configuratiescherm om uw nieuwe cluster in te stellen. Volg de koppeling op het statusscherm voor het maken van uw cluster of ga naar het beheers-IP-adres dat u heeft ingesteld voor de cluster.

Meld u aan bij de webinterface met de gebruikersnaam `admin` en het wachtwoord dat u heeft ingesteld toen u de cluster aanmaakte.

![webbrowser met aanmeldingsvelden van het configuratiescherm](media/fxt-cluster-create/admin-login.png)

Het Configuratiescherm wordt geopend en toont de pagina **Dashboard**. Wanneer de cluster gemaakt is, verdwijnen waarschuwingsberichten van het scherm.

Klik op het tabblad **Instellingen** om de cluster te configureren.

In de zijbalk aan de linkerzijde van het tabblad **Instellingen** vindt u een menu voor de configuratiepagina's. De pagina's worden gegroepeerd op categorie. Klik op + of - boven de categorienaam om de individuele pagina's uit te vouwen of te verbergen.

![Tabblad Instellingen van het configuratiescherm (in browser) met de pagina Cluster > Algemene instellingen](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Installatiestappen voor cluster

Op dit punt in het proces bestaat uw cluster, maar heeft hij slechts één knooppunt, geen clientgerichte IP-adressen en geen back-endopslag. Om uw nieuwe cluster te veranderen in een cachesysteem dat uw werkstroom kan verwerken, zijn aanvullende stappen nodig.

### <a name="required-configuration"></a>Vereiste configuratie

Deze stappen zijn nodig voor de meeste of alle clusters.

* Knooppunten toevoegen aan de cluster

  Drie knooppunten is standaard, maar veel productieclusters hebben er meer- tot een maximaal 24.

  Lees [Clusterknooppunten toevoegen](fxt-add-nodes.md) om te leren hoe u andere Azure FXT Edge Filer-eenheden kunt toevoegen aan uw cluster en om Hoge beschikbaarheid in te schakelen.

* Back-endopslag opgeven

  Voeg definities voor *kernfilers* toe voor elk systeem voor back-endopslag dat de cluster zal gebruiken. Lees [Back-endopslag toevoegen en virtuele naamruimte configureren](fxt-add-storage.md#about-back-end-storage) voor meer informatie.

* Clienttoegang en de virtuele naamruimte instellen

  Maak ten minste één virtuele server (vserver) en wijs deze toe aan een IP-adresbereik dat clientmachines mogen gebruiken. U moet ook de naamruimte voor de cluster configureren (soms de Globale naamruimte of GNS genoemd), een virtueel bestandssysteem waarmee u exports van back-endopslag kunt toewijzen aan virtuele paden. De naamruimte van de cluster geeft clients een consistente en toegankelijke structuur voor bestandssystemen, zelfs wanneer u overschakelt op een ander medium voor back-endopslag. De naamruimte biedt ook een gebruiksvriendelijke virtuele opslaghiërarchie voor Azure-blobcontainers of andere ondersteunde opslag van cloudobjecten.

  Lees [De naamruimte configureren](fxt-add-storage.md#configure-the-namespace) voor details. Deze stap omvat:
  * Vservers maken
  * Koppelingen maken tussen de weergave van het clientnetwerk en de back-endopslag
  * Definiëren welke client-IP-adressen bediend worden door elke vserver

  > [!Note]
  > We raden u aan om goed te plannen voor u de GNS van de cluster gaat instellen. Lees de secties [Een globale naamruimte gebruiken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) en [VServer maken en ermee werken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) in de handleiding voor clusterconfiguratie voor meer ondersteuning.

* [Netwerkinstellingen aanpassen](fxt-configure-network.md)

  Er zijn verschillende netwerkgerelateerde instellingen die moeten worden gecontroleerd of aangepast voor een nieuw cluster. Lees [Netwerkinstellingen aanpassen](fxt-configure-network.md) voor details over deze items:

  * DNS-en NTP-configuratie controleren
  * Directory Services configureren, indien nodig
  * VLAN's instellen
  * Proxyservers configureren
  * IP-adressen toevoegen aan het clusternetwerk
  * Versleutelingscertificaten opslaan

* [Ondersteuningscontrole instellen](#enable-support)

  U moet het privacybeleid voor de configuratietool aanvaarden, en dient tegelijkertijd uw instellingen voor uploadondersteuning te configureren.

  De cluster kan automatisch probleemoplossingsgegevens over uw cluster uploaden, waaronder statistieken en foutopsporingsbestanden. Deze uploads stellen de klantenservice en klantondersteuning van Microsoft in staat om de best mogelijke service te bieden. U kunt kiezen wat gecontroleerd wordt en eventueel de proactieve ondersteuning en probleemoplossing op afstand inschakelen.  

### <a name="optional-configuration"></a>Optionele configuratie

Deze stappen zijn niet vereist voor alle clusters. Ze zijn vereist voor bepaalde soorten werkstromen of voor bepaalde vormen van clusterbeheer.

* Instellingen voor knooppunten aanpassen

  U kunt de namen voor knooppunten instellen en de IPMI-poorten voor knooppunten op clusterniveau of individueel configureren. Als u deze instellingen configureert voor u knooppunten toevoegt aan de cluster, dan kunnen nieuwe knooppunten de instellingen automatisch oppikken wanneer ze erbij komen. De opties worden beschreven in het verouderde document over het aanmaken van clusters [Knooppuntinstellingen aanpassen](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Nog niet alle documentatie voor dit product is beschikbaar op de documentatiesite van Microsoft Azure. Koppelingen naar de [Handleiding voor clusterconfiguratie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) en de verouderde versie van de [Handleiding voor het maken van clusters](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) leiden u naar een aparte website die op GitHub gehost wordt.

* SMB configureren

  Als u SMB toegang wilt geven tot uw cluster en NFS, dan moet u SMB configureren en inschakelen. SMB (soms ook CIFS genoemd) wordt meestal gebruikt om Microsoft Windows-clients te ondersteunen.

  SMB plannen en configureren in complexer dan op een paar knoppen drukken in het Configuratiescherm. Afhankelijk van de vereisten van uw systeem kan SMB een invloed hebben op hoe u uw kernfilers definieert, hoeveel vservers u aanmaakt, hoe u uw koppelingen, naamruimte, toegangsmachtigingen en andere instellingen configureert.

  Lees de sectie [SMB-toegang configureren](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) van de handleiding voor clusterconfiguratie voor meer informatie.

* Aanvullende licenties installeren

  Als u een andere cloudopslag dan Azure Blob wilt gebruiken, dan moet u een aanvullende functielicentie installeren. Neem contact op met uw Microsoft-vertegenwoordiger voor details over de aankoop van een FlashCloud<sup>TM</sup>-licentie. Details worden uitgelegd in [Back-endopslag toevoegen en virtuele naamruimte configureren](fxt-add-storage.md#about-back-end-storage).

### <a name="enable-support"></a>Ondersteuning inschakelen

De Azure FXT Edge Filer-cluster kan automatisch ondersteuningsgegevens over uw cluster uploaden. Dankzij deze uploads kunnen medewerkers de best mogelijke ondersteuning bieden.

Volg deze stappen om ondersteuningsuploads in te schakelen.

1. Ga naar de instellingenpagina **Cluster** > **Ondersteuning**. Aanvaard het privacybeleid

   ![Schermafbeelding met het Configuratievenster en pop-upvenster met de knop Bevestigen om het privacybeleid te aanvaarden](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Klik op de driehoek aan de linkerzijde van **Klantgegevens** om de sectie uit te vouwen.
1. Klik op de knop **Uploadgegevens opnieuw valideren**.
1. Stel de ondersteuningsnaam van de cluster in bij **Unieke clusternaam** - zorg ervoor dat hij uw cluster op unieke wijze identificeert voor ondersteuningsmedewerkers.
1. Schakel de selectievakjes in voor **Controle van statistieken**, **Upload van algemene gegevens** en **Upload van crashgegevens**.
1. Klik op **Submit**  

   ![Schermafbeelding met ingevulde sectie klantgegevens van de pagina instellingen voor ondersteuning](media/fxt-cluster-create/fxt-support-info.png)

1. Klik op de driehoek links naast **Secure Proactive Support (SPS)** om de sectie uit te vouwen.
1. Schakel het selectievakje **SPS Link inschakelen** in.
1. Klik op **Submit**

   ![Schermafbeelding met ingevulde sectie Secure Proactive Support op de pagina met ondersteuningsinstellingen](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u de basiscluster heeft aangemaakt en het privacybeleid heeft aanvaard, voegt u de overige clusterknooppunten toe.

> [!div class="nextstepaction"]
> [Clusterknooppunten toevoegen](fxt-add-nodes.md)
