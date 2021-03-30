---
title: 'Zelfstudie: Kabels aansluiten op een Azure FXT Edge Filer'
description: De netwerkpoorten bekabelen en voeding aansluiten voor Azure FXT Edge Filer-hardware
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 80458d99ed6ad19c8a837f0989798d4dcc156b61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219527"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Zelfstudie: Netwerkverbindingen maken en voeding leveren voor het Azure FXT Edge Filer-knooppunt

In deze zelfstudie leert u hoe u de netwerkverbindingen kunt bekabelen voor een Azure FXT Edge Filer-hardwareknooppunt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Het type netwerkkabel voor uw omgeving kiezen
> * Een Azure FXT Edge Filer-knooppunt verbinden met uw datacentrumnetwerk
> * Kabels geleiden via de kabelarm
> * Voeding aansluiten op het rekapparaat en de stroom inschakelen

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet de Azure FXT Edge Filer worden geïnstalleerd in een standaard apparatuurrek. De kabelarm moet worden geïnstalleerd op het Filer-knooppunt.

## <a name="identify-ports"></a>Poorten identificeren

Identificeer de verschillende poorten op de achterkant van uw Azure FXT Edge Filer.

![Achterkant van een bekabeld apparaat](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Het apparaat bekabelen

* Verbind de RJ-45-poorten met de netwerkbron van uw datacentrum, zoals beschreven in [Netwerkpoorten](#network-ports).  
* Verbind de [iDRAC-poort](#idrac-port) veilig met een afzonderlijk netwerk met een beveiligde DHCP-server.
* Gebruik de USB-poorten en de VGA-poort om een toetsenbord en monitor te koppelen met het knooppunt voor de initiële installatie. U moet het knooppunt opstarten en [een eerste wachtwoord instellen](fxt-node-password.md) om de andere poorten van het knooppunt te activeren. Lees [Set initial passwords](fxt-node-password.md) (Eerste wachtwoorden instellen) voor meer informatie.

In dit artikel wordt ook beschreven hoe u [verbinding met netstroom maakt](#connect-power-cables) voor het knooppunt.

In dit artikel wordt ook uitgelegd hoe u de [seriële poort](#serial-port-only-when-necessary) van het knooppunt aansluit, indien nodig voor gespecialiseerde probleemoplossing.

### <a name="network-ports"></a>Netwerkpoorten

Elk Azure FXT Edge Filer-knooppunt bevat de volgende netwerkpoorten:

* Zes snelle gegevenspoorten met dubbele snelheid van 25 GbE/10 GbE:

  * Vier poorten worden geleverd door twee invoegbare netwerkadapters met twee poorten
  * Twee poorten worden geleverd door de mezzanine-netwerkadapter van het moederbord

* Twee 1GbE-poorten worden geleverd door de mezzanine-netwerkadapter van het moederbord

De snelle gegevenspoorten van 25 GbE/10 GbE hebben standaard SFP28-compatibele kooien. Als u optische kabels wilt gebruiken, moet u modules voor optische SFP28-ontvangers installeren (niet meegeleverd).

De 1GbE-poorten hebben standaard RJ-45-connectoren.

Voor een volledige lijst van ondersteunde kabels, schakelaar en ontvangers raadpleegt u de [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Het type verbindingen dat voor uw systeem moet worden gebruikt, is afhankelijk van uw datacentrumomgeving.

* Als u verbinding maakt met een 25GbE-netwerk, bekabelt u elk van de snelle gegevenspoorten met een van de volgende kabeltypen:

  * Optische kabel en optische SFP28-ontvanger met 25 GbE of capaciteit voor dubbele 25 GbE/10 GbE
  * Twinaxial-kabel met capaciteit van 25 GbE type SFP28 met directe bevestiging

* Als u verbinding maakt met een 10GbE-netwerk, bekabelt u elk van de snelle gegevenspoorten met een van de volgende kabeltypen:

  * Optische kabel en optische SFP28-ontvanger met 10 GbE of capaciteit voor dubbele 25 GbE/10 GbE.
  * Twinaxial-kabel met capaciteit van 25 GbE type SFP28 met directe bevestiging
  * Twinaxial-kabel met capaciteit van 10 GbE type SFP28 met directe bevestiging

* De 1GbE-netwerkpoorten worden gebruikt voor clusterbeheerverkeer. Schakel de optie **1Gb-beheernetwerk gebruiken** in bij het maken van het cluster als u een fysiek afzonderlijk netwerk wilt maken voor de clusterconfiguratie (beschreven in [Configure the management network](fxt-cluster-create.md#configure-the-management-network) (Het beheernetwerk configureren)). Bekabel de poorten met een standaard Cat5-kabel of hoger, zoals beschreven in de lijst met ondersteunde kabels.

  Als u van plan bent om de poorten met hoge snelheid te gebruiken voor al het verkeer, kunt u de 1GbE-poorten onbekabeld laten. Standaard worden de 1GbE-netwerkpoorten niet gebruikt als er een gegevenspoort met hogere snelheid beschikbaar is.  

### <a name="idrac-port"></a>iDRAC-poort  

De poort met de naam iDRAC is een 1Gb-aansluiting die communicatie mogelijk maakt met een controller voor externe toegang die wordt gebruikt voor hardwarebeheer en -bewaking. FXT-software maakt gebruik van de Intelligent Platform Management Interface (IPMI) met deze controller voor probleemoplossing en herstel. U kunt de ingebouwde [iDRAC-interface](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) gebruiken om hardware te bewaken via deze poort. iDRAC- en IPMI-toegang zijn standaard ingeschakeld.

> [!Note]
> De iDRAC-poort kan het besturingssysteem overslaan en rechtstreeks communiceren met hardware op het knooppunt.

Gebruik deze beveiligingsstrategieën bij het koppelen en configureren van de iDRAC-poort:

* Koppel iDRAC-poorten alleen aan een netwerk dat fysiek is gescheiden van het gegevensnetwerk dat wordt gebruikt voor toegang tot het cluster.
* Stel een beveiligd iDRAC-beheerderswachtwoord in op elk knooppunt. U moet dit wachtwoord instellen om de instructies voor het uitvoeren van de hardware te activeren in [Hardware-wachtwoorden instellen](fxt-node-password.md).
* De standaardconfiguratie van de iDRAC-poort maakt gebruik van DHCP en IPv4 voor IP-adrestoewijzing. Zorg ervoor dat uw DHCP-omgeving goed is beveiligd en dat de verbindingen tussen DHCP-clients en de DHCP-server worden beperkt. (Het configuratiescherm van het cluster bevat instellingen voor het wijzigen van de adresconfiguratiemethode van de knooppunten nadat u het cluster hebt gemaakt.)
* Zorg ervoor dat de iDRAC-poort is ingesteld op 'exclusieve modus' (de standaardinstelling), waarmee het netwerkverkeer voor iDRAC/IPMI wordt beperkt tot de toegewezen RJ-45-poort.

De iDRAC-poort vereist geen snelle netwerkverbinding.
  
### <a name="serial-port-only-when-necessary"></a>Seriële poort (alleen indien vereist)

In sommige gevallen kan de service en ondersteuning van Microsoft u vragen een terminal te verbinden met de seriële poort van een knooppunt om een probleem op te sporen.

De console koppelen:

1. Zoek de seriële poort (COM1) aan de achterzijde van het FXT Edge Filer-knooppunt.
1. Gebruik een null-modemkabel om de seriële poort te verbinden met een terminal die is geconfigureerd voor ANSI-115200-8N1.
1. Meld u aan bij de console en volg de stappen van de ondersteuningsmedewerkers.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Kabels geleiden via de kabelarm

Elk Azure FXT Edge Filer-knooppunt wordt geleverd met een optionele kabelarm. De kabelarm vereenvoudigt het geleiden van kabels en biedt eenvoudiger toegang tot de achtergrond van het chassis zonder dat u kabels hoeft los te koppelen.

Volg deze instructies om de kabels via de kabelarm te leiden:

1. Bundel de kabels met de meegeleverde tie-wraps waar ze de baskets in- en uitkomen, zodat ze aangrenzende systemen niet verstoren (1).
1. Breng de arm in de servicepositie en leid de kabelbundel door de binnenste en buitenste baskets (2).
1. Gebruik de vaste klittenbanden aan beide uiteinden van de baskets om de kabels vast te maken (3).
1. Draai de arm terug naar de lade (4).
1. Installeer de statusindicatorkabel aan de achterkant van het systeem en maak de kabel vast door deze via de arm te leiden. Koppel het andere uiteinde van de kabel aan de hoek van de buitenste basket van de arm (5).

   > [!CAUTION]
   > Om schade aan uitstekende kabels te voorkomen, moet u ervoor zorgen dat de statusindicatorkabel heel strak loopt nadat u deze kabel door de arm hebt geleid.

![Afbeelding van kabelarm met geplaatste kabels](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
> Als u de kabelarm niet hebt geplaatst, kunt u de klittenbanden in de rail kit gebruiken om de kabels aan de achtergrond van uw systeem te leiden.
>
>  1. Zoek de buitenste haakjes van de arm aan de binnenkant van beide rekflensen.
>  2. Bundel de kabels voorzichtig, zodat ze de systeemconnectoren aan de linker- en rechterkant niet raken.
>  3. Haal de klittenbanden door de sleuven op de buitenste haakjes van de arm aan de zijkanten van het systeem om de kabelbundels vast te maken.
>
>     ![Kabels geleiden zonder een kabelarm](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Informatie over vereisten voor IP-adressen

IP-adressen voor hardwareprofielen in een hybride opslagcache van Azure FXT Edge Filer worden beheerd door de clustersoftware.

Elk knooppunt vereist ten minste één IP-adres, maar knooppuntadressen worden toegewezen wanneer knooppunten worden toegevoegd aan of verwijderd uit het cluster.

Het totale aantal IP-adressen dat is vereist, is afhankelijk van het aantal knooppunten in de cache.

Configureer het IP-adresbereik via de software van het configuratiescherm nadat de knooppunten zijn geïnstalleerd. Lees [Gather information for the cluster](fxt-cluster-create.md#gather-information-for-the-cluster) (Informatie verzamelen voor het cluster) voor meer informatie.  

## <a name="connect-power-cables"></a>Stroomkabels aansluiten

Elk Azure FXT Edge Filer-knooppunt gebruikt twee voedingseenheden.

> [!TIP]
> Als u gebruik wilt maken van de twee redundante voedingseenheden, koppelt u elke netstroomkabel aan een voedingsverdelingseenheid op een onafhankelijk vertakkingscircuit.
>
> U kunt een voedingseenheid gebruiken om de voedingsverdelingseenheden van stroom te voorzien voor extra beveiliging.

1. Verbind de inbegrepen netsnoeren met de voedingseenheden in het chassis. Zorg ervoor dat de snoeren en voedingseenheden volledig zijn aangesloten.
1. Koppel de netsnoeren aan de voedingsverdelingseenheden op het apparatuurrek. Gebruik, indien mogelijk, twee afzonderlijke voedingsbronnen voor de twee snoeren.

### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Een Azure FXT Edge Filer-knooppunt inschakelen

Als u het knooppunt wilt inschakelen, drukt u op de aan/uit-knop aan de voorkant van het systeem. De knop bevindt zich aan de rechterkant van het bedieningspaneel.

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Een Azure FXT Edge Filer-knooppunt uitzetten

De aan/uit-knop kan worden gebruikt om het systeem uit te schakelen tijdens het testen en voordat het aan een cluster wordt toegevoegd. Nadat een Azure FXT Edge Filer-knooppunt echter is gebruikt als onderdeel van een cluster, kunt u beter de software van het clusterconfiguratiescherm gebruiken om de hardware af te sluiten. Lees [How to safely power off Azure FXT Edge Filer hardware](fxt-power-off.md) (Veilig de Azure FXT Edge Filer-hardware uitschakelen) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nadat u klaar bent met het bekabelen van de hardware, kunt u alle knooppunten inschakelen en deze initialiseren door hun hoofdwachtwoorden in te stellen.
> [!div class="nextstepaction"]
> [Initiële wachtwoorden instellen](fxt-node-password.md)
