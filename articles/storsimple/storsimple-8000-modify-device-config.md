---
title: De configuratie van de StorSimple 8000-serie wijzigen | Microsoft Docs
description: Hierin wordt beschreven hoe u de StorSimple Apparaatbeheer-service gebruikt om een StorSimple-apparaat dat al is geïmplementeerd, opnieuw te configureren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 5eb5c351462279fe5c4f790e052f73201d211cfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94961172"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>De StorSimple Apparaatbeheer-service gebruiken om de configuratie van uw StorSimple-apparaat te wijzigen

## <a name="overview"></a>Overzicht

De sectie **apparaatinstellingen** Azure Portal op de Blade **instellingen** bevat alle para meters die u opnieuw kunt configureren op een StorSimple-apparaat dat wordt beheerd door een StorSimple Apparaatbeheer-service. In deze zelf studie wordt uitgelegd hoe u de Blade **instellingen** kunt gebruiken om de volgende taken op apparaatniveau uit te voeren:

* Beschrijvende naam van het apparaat wijzigen
* Apparaatinstellingen wijzigen
* Een secundaire DNS toewijzen
* Netwerk interfaces wijzigen
* IP-adressen wisselen of opnieuw toewijzen

## <a name="modify-device-friendly-name"></a>Beschrijvende naam van het apparaat wijzigen

U kunt de Azure Portal gebruiken om de naam van het apparaat te wijzigen en dit een unieke beschrijvende naam te geven. Gebruik de Blade **algemene instellingen** op het apparaat om de beschrijvende naam van het apparaat te wijzigen. De beschrijvende naam mag tekens bevatten en mag Maxi maal 64 tekens lang zijn.

> [!NOTE] 
> U kunt de naam van het apparaat alleen wijzigen in de Azure Portal voordat de installatie van het apparaat is voltooid. Zodra de minimale installatie van het apparaat is voltooid, kunt u de naam van het apparaat niet meer wijzigen.

![Apparaatnaam in algemene instellingen](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Een StorSimple-apparaat dat is verbonden met de StorSimple Apparaatbeheer-service krijgt een standaard naam. De standaard naam weerspiegelt meestal het serie nummer van het apparaat. Bijvoorbeeld een standaard apparaatnaam die 15 tekens lang is, zoals 8600-SHX0991003G44HT, geeft het volgende aan:

* **8600**  : geeft het model van het apparaat aan.
* **SHX** : geeft de productie site aan.
* **0991003** -geeft een specifiek product aan.
* **G44HT**: de laatste vijf cijfers worden verhoogd om unieke serie nummers te maken. Dit is mogelijk geen opeenvolgende set.

## <a name="modify-device-description"></a>Apparaatbeschrijving wijzigen

Gebruik de Blade **algemene instellingen** op het apparaat om de apparaatbeschrijving te wijzigen.

![Apparaatbeschrijving in algemene instellingen](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Een apparaatbeschrijving helpt doorgaans bij het identificeren van de eigenaar en de fysieke locatie van het apparaat. Het beschrijvings veld moet minder dan 256 tekens bevatten.

## <a name="modify-time-settings"></a>Tijd instellingen wijzigen

Uw apparaat moet de tijd synchroniseren om te verifiëren bij de service provider voor de Cloud opslag. Gebruik de Blade **algemene instellingen** op het apparaat om de apparaatinstellingen te wijzigen.

![Apparaatbeschrijving in algemene instellingen 2](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Selecteer uw tijd zone in de vervolg keuzelijst. U kunt Maxi maal twee NTP-servers (Network Time Protocol) opgeven:

 - **Primaire NTP-server** : de configuratie is vereist en wordt opgegeven wanneer u Windows PowerShell voor StorSimple gebruikt om uw apparaat te configureren. U kunt de standaard Windows Server- **time.Windows.com** opgeven als uw NTP-server. U kunt de configuratie van de primaire NTP-server weer geven via de Azure Portal, maar u moet de Windows Power shell-interface gebruiken om deze te wijzigen. Gebruik de `Set-HcsNTPClientServerAddress` cmdlet om de primaire NTP-server van uw apparaat te wijzigen. Ga naar de syntaxis van de cmdlet [set-HcsNTPClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) voor meer informatie.

- **Secundaire NTP-server** : de configuratie is optioneel. U kunt de portal gebruiken om een secundaire NTP-server te configureren.

Wanneer u de NTP-server configureert, moet u ervoor zorgen dat het NTP-verkeer door uw netwerk kan worden door gegeven van uw Data Center naar het internet. Wanneer u een open bare NTP-server opgeeft, moet u ervoor zorgen dat uw netwerk firewalls en andere beveiligings apparaten zodanig zijn geconfigureerd dat NTP-verkeer kan worden getransporteerd naar en van het externe netwerk. Als bidirectionele NTP-verkeer niet is toegestaan, moet u een interne NTP-server gebruiken (een Windows-domein controller biedt deze functie). Als uw apparaat geen tijd kan synchroniseren, kan het mogelijk niet communiceren met uw provider voor Cloud opslag.

Als u een lijst met open bare NTP-servers wilt bekijken, gaat u naar het [Web NTP-servers](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Wat gebeurt er als het apparaat in een andere tijd zone wordt geïmplementeerd?

Als het apparaat in een andere tijd zone wordt geïmplementeerd, wordt de tijd zone van het apparaat gewijzigd. Aangezien alle back-upbeleiden de tijd zone van het apparaat gebruiken, wordt het back-upbeleid automatisch aangepast op basis van de nieuwe tijd zone. Er is geen tussenkomst van de gebruiker is vereist.

## <a name="modify-dns-settings"></a>DNS-instellingen wijzigen

Een DNS-server wordt gebruikt wanneer het apparaat probeert te communiceren met uw provider voor de Cloud opslag. Gebruik de Blade **netwerk instellingen** op het apparaat om de GECONFIGUREERDE DNS-instellingen weer te geven en te wijzigen. 

![DNS-instellingen in netwerk instellingen](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Voor maximale Beschik baarheid moet u de primaire en secundaire DNS-servers configureren tijdens de initiële implementatie van het apparaat.

**Primaire DNS-server** : u gebruikt de Windows PowerShell voor StorSimple om eerst de primaire DNS-server op te geven tijdens de eerste installatie. U kunt de primaire DNS-server alleen opnieuw configureren via de Windows Power shell-interface. Gebruik de `Set-HcsDNSClientServerAddress` cmdlet om de primaire DNS-server van uw apparaat te wijzigen. Ga naar de syntaxis van de cmdlet [set-HcsDNSClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) voor meer informatie.

**Secundaire DNS-server** : als u de secundaire DNS-server wilt wijzigen, gebruikt u de `Set-HcsDNSClientServerAddress` cmdlet in de Windows Power shell-interface van de Blade apparaat-of **netwerk instellingen** van uw StorSimple-apparaat in de Azure Portal.

Als u de secundaire DNS-server in Azure Portal wilt wijzigen, voert u de volgende stappen uit.

1. Ga naar uw StorSimple-apparaatbeheerservice. Selecteer en klik op uw apparaat in de lijst met apparaten.

2. Ga op de Blade **instellingen** naar **Apparaatinstellingen > netwerk**. Hiermee opent u de Blade **netwerk instellingen** . Klik op **DNS-instellingen** tegel. Wijzig het IP-adres van de secundaire DNS-server.

    ![IP-adres van secundaire DNS-server wijzigen](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Klik in de opdracht balk op **Opslaan** en klik op **OK** als u om bevestiging wordt gevraagd.

    ![Wijzigingen opslaan en bevestigen](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Netwerk interfaces wijzigen

Uw apparaat heeft zes netwerk interfaces voor apparaten, waarvan vier GbE en twee van 10 GbE. Deze interfaces worden aangeduid als DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 en DATA 5 zijn 1 GbE, terwijl DATA 2 en DATA 3 10 GbE-netwerk interfaces zijn.

Gebruik de Blade **netwerk instellingen** om elke interface te configureren die moet worden gebruikt.

![Netwerk interfaces configureren via netwerk instellingen](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Om hoge Beschik baarheid te garanderen, raden we u aan ten minste twee iSCSI-interfaces en twee Cloud interfaces op uw apparaat te hebben. We raden u aan om te voor komen dat ongebruikte interfaces worden uitgeschakeld.

Voor elke netwerk interface worden de volgende para meters weer gegeven:

* **Snelheid** : geen door de gebruiker te configureren para meter. DATA 0, DATA 1, DATA 4 en DATA 5 zijn altijd 1 GbE, terwijl DATA 2 en DATA 3 10 GbE-interfaces zijn.
  
  > [!NOTE]
  > Snelheid en duplex worden altijd automatisch onderhandeld. Jumbo-frames worden niet ondersteund.
  
* **Interface status** : een interface kan worden ingeschakeld of uitgeschakeld. Als deze functie is ingeschakeld, probeert het apparaat de interface te gebruiken. U wordt aangeraden alleen de interfaces die zijn verbonden met het netwerk, in te scha kelen. Schakel alle interfaces uit die u niet gebruikt.
* **Interface type** : met deze para meter kunt u iSCSI-verkeer van opslag verkeer in de Cloud isoleren. Dit kan een van de volgende para meters zijn:
  
  * **Cloud ingeschakeld** : wanneer deze functie is ingeschakeld, gebruikt het apparaat deze interface om te communiceren met de Cloud.
  * **iSCSI ingeschakeld** : wanneer deze functie is ingeschakeld, gebruikt het apparaat deze interface om met de iSCSI-host te communiceren.
    
    U kunt het beste iSCSI-verkeer isoleren vanuit opslag verkeer in de Cloud. Als uw host zich in hetzelfde subnet als uw apparaat bevindt, hoeft u geen gateway toe te wijzen. Als uw host echter zich in een ander subnet bevindt dan uw apparaat, moet u een gateway toewijzen.
* **IP-adres** : wanneer u een van de netwerk interfaces configureert, moet u een virtueel IP (VIP) configureren. Dit kan IPv4 of IPv6 zijn of beide. De IPv4-en IPv6-adres families worden ondersteund voor de netwerk interfaces van het apparaat. Wanneer u IPv4 gebruikt, geeft u een 32-bits IP-adres (*xxx.xxx.xxx.xxx*) op in punt decimale notatie. Wanneer u IPv6 gebruikt, moet u een voor voegsel van vier cijfers opgeven en wordt er automatisch een 128-bits adres gegenereerd voor de netwerk interface van het apparaat op basis van dat voor voegsel.
* **Subnet** : dit verwijst naar het subnetmasker en wordt geconfigureerd via de Windows Power shell-interface.
* **Gateway** : dit is de standaard gateway die moet worden gebruikt door deze interface wanneer deze probeert te communiceren met knoop punten die zich niet binnen dezelfde IP-adres ruimte (subnet) bevinden. De standaard gateway moet zich in dezelfde adres ruimte (subnet) bevinden als het IP-adres van de interface, zoals wordt bepaald door het subnetmasker.
* **Vast IP-adres** : dit veld is alleen beschikbaar wanneer u de data 0-interface configureert. Voor bewerkingen zoals updates of het oplossen van problemen met het apparaat, moet u mogelijk rechtstreeks verbinding maken met de apparaat controller. Het vaste IP-adres kan worden gebruikt voor toegang tot zowel de actieve als de passieve controller op het apparaat.

> [!NOTE]
> * Controleer de interface snelheid en duplex op de switch waarmee elke apparaatinterface is verbonden om de juiste werking te garanderen. Switch-interfaces moeten onderhandelen met of moeten worden geconfigureerd voor Gigabit Ethernet (1000 Mbps) en moeten full-duplex zijn. Interfaces die zijn verbonden met tragere snelheden of half-duplex, leiden tot prestatie problemen.
> * Om onderbrekingen en downtime tot een minimum te beperken, raden we u aan om PortFast in te scha kelen op elke switch poort die door de iSCSI-netwerk interface van uw apparaat wordt verbonden. Dit zorgt ervoor dat de netwerk verbinding snel kan worden ingesteld in het geval van een failover.

### <a name="configure-data-0"></a>GEGEVENS configureren 0

DATA 0 is standaard ingeschakeld voor de Cloud. Bij het configureren van gegevens 0 moet u ook twee vaste IP-adressen configureren, één voor elke controller. Deze vaste IP-adressen kunnen worden gebruikt om rechtstreeks toegang te krijgen tot de apparaat controllers en zijn handig wanneer u updates op het apparaat installeert, zodat de garbagecollection goed werkt of wanneer u de controllers opent voor het oplossen van problemen.

U kunt de vaste IP-controllers opnieuw configureren via de Blade DATA 0-instellingen.

![Netwerk interface configureren-gegevens 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> De vaste IP-adressen voor de controller worden gebruikt voor het onderhoud van de updates voor het apparaat en voor het goed functioneren van de ruimte voor het terugwinnen van de Prullenbak. Daarom moeten de vaste IP-adressen routeerbaar zijn en verbinding kunnen maken met internet.

### <a name="configure-data-1---data-5"></a>GEGEVENS configureren 1-DATA 5

Voor DATA 1-DATA 5-netwerk interfaces kunt u alle netwerk instellingen configureren, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Netwerk interfaces gegevens configureren 1-DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>IP-adressen wisselen of opnieuw toewijzen

Als er op dit moment een netwerk interface op de controller wordt toegewezen aan een VIP dat in gebruik is (door hetzelfde apparaat of een ander apparaat in het netwerk), wordt een failover uitgevoerd voor de controller. Als u Vip's verwisselt of opnieuw toewijst voor een netwerk interface van een apparaat, moet u een juiste procedure volgen, omdat u een dubbele IP-situatie zou kunnen maken.

Voer de volgende stappen uit om de Vip's voor een van de netwerk interfaces te vervangen of opnieuw toe te wijzen:

#### <a name="to-reassign-ips"></a>IP-adressen opnieuw toewijzen

1. Het IP-adres voor beide interfaces wissen.
2. Nadat de IP-adressen zijn gewist, wijst u de nieuwe IP-adressen toe aan de respectieve interfaces.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [configureren van MPIO voor uw StorSimple-apparaat](storsimple-8000-configure-mpio-windows-server.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).