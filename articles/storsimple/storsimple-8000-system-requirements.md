---
title: Systeem vereisten voor de StorSimple 8000-serie | Microsoft Docs
description: Hierin worden de vereisten voor software, netwerken en hoge Beschik baarheid beschreven en aanbevolen procedures voor een Microsoft Azure StorSimple oplossing.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: fa7616a740e8246fa08e950494428095f41ee404
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382851"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000-serie software, hoge Beschik baarheid en netwerk vereisten

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Welkom bij Microsoft Azure StorSimple. Dit artikel bevat een beschrijving van belang rijke systeem vereisten en aanbevolen procedures voor uw StorSimple-apparaat en voor de opslag-clients die toegang hebben tot het apparaat. We raden u aan de informatie zorgvuldig te bekijken voordat u uw StorSimple-systeem implementeert en vervolgens naar de gewenste gegevens te verwijzen tijdens de implementatie en de volgende bewerking.

De systeem vereisten zijn onder andere:

* **Software vereisten voor opslag clients** : beschrijft de ondersteunde besturings systemen en eventuele aanvullende vereisten voor deze besturings systemen.
* **Netwerk vereisten voor het StorSimple-apparaat** : bevat informatie over de poorten die in uw firewall moeten worden geopend om iSCSI-, Cloud-of beheer verkeer toe te staan.
* **Vereisten voor hoge Beschik baarheid voor StorSimple** : beschrijft vereisten voor hoge Beschik baarheid en aanbevolen procedures voor uw StorSimple-apparaat en hostcomputer.

## <a name="software-requirements-for-storage-clients"></a>Software vereisten voor Storage-clients

De volgende software vereisten gelden voor de opslaghardware die toegang hebben tot uw StorSimple-apparaat.

| Ondersteunde besturingssystemen | Versie vereist | Aanvullende vereisten/notities |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |StorSimple iSCSI-volumes worden alleen ondersteund voor gebruik op de volgende Windows-schijf typen:<ul><li>Eenvoudig volume op een standaard schijf</li><li>Eenvoudig en gespiegeld volume op dynamische schijf</li></ul>Alleen de iSCSI-initia tors van de software die standaard aanwezig zijn in het besturings systeem, worden ondersteund. Hardware-iSCSI-initia tors worden niet ondersteund.<br></br>Windows Server 2012 en 2016 Thin Provisioning en ODX-functies worden ondersteund als u een StorSimple iSCSI-volume gebruikt.<br><br>StorSimple kan Thin provisioned en volledig ingerichte volumes maken. Er kunnen geen gedeeltelijk ingerichte volumes worden gemaakt.<br><br>Het opnieuw formatteren van een volume met thin provisioning kan lang duren. Het is raadzaam om het volume te verwijderen en vervolgens een nieuw volume te maken in plaats van het opnieuw te formatteren. Als u echter toch de voorkeur geeft aan opnieuw formatteren:<ul><li>Voer de volgende opdracht uit vóór het opnieuw formatteren om vertragingen wegens vrijmaken van ruimte te voorkomen:  <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Nadat het formatteren is voltooid, gebruikt u de volgende opdracht om het vrijmaken van ruimte opnieuw in te schakelen: <br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Pas de hotfix voor Windows Server 2012 toe, zoals wordt beschreven in [KB 2878635](https://support.microsoft.com/kb/2870270) op uw Windows Server-computer.</li></ul></li></ul></ul> Als u StorSimple Snapshot Manager of StorSimple-adapter configureert voor share point, gaat u naar [Software vereisten voor optionele onderdelen](#software-requirements-for-optional-components). <br> Als uw Windows Server-client het SMB-protocol gebruikt om toegang te krijgen tot het StorSimple-apparaat, gaat u naar [prestaties afstemmen voor SMB-bestands servers](/windows-server/administration/performance-tuning/role/file-server/smb-file-server) voor hulp bij het verhogen van parallelle verwerking.|
| VMware ESX |5,5 en 6,0 |Ondersteund met VMware vSphere als iSCSI-client. De functie VAAI-Block wordt ondersteund met VMware vSphere op StorSimple-apparaten. |
| Linux RHEL/CentOS |5, 6 en 7 |Ondersteuning voor Linux iSCSI-clients met open-iSCSI-initiator versies 5, 6 en 7. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX wordt momenteel niet ondersteund met StorSimple.


## <a name="software-requirements-for-optional-components"></a>Software vereisten voor optionele onderdelen

De volgende software vereisten gelden voor de optionele StorSimple-onderdelen (StorSimple Snapshot Manager en StorSimple adapter voor share point).

| Onderdeel | Host-platform | Aanvullende vereisten/notities |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Het gebruik van StorSimple Snapshot Manager op Windows Server is vereist voor het maken van back-ups/herstellen van gespiegelde dynamische schijven en voor toepassings consistente back-ups.<br> StorSimple Snapshot Manager wordt alleen ondersteund op Windows Server 2008 R2 SP1 (64-bits), Windows Server 2012 R2 en Windows Server 2012.<ul><li>Als u gebruikmaakt van Windows Server 2012, moet u .NET 3.5 – 4.5 installeren voordat u StorSimple Snapshot Manager installeert.</li><li>Als u Windows Server 2008 R2 SP1 gebruikt, moet u Windows Management Framework 3,0 installeren voordat u StorSimple Snapshot Manager installeert.</li></ul> |
| StorSimple Adapter voor SharePoint |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>De StorSimple-adapter voor share point wordt alleen ondersteund op share point 2010 en share point 2013.</li><li>Voor de RBS is SQL Server Enterprise editie, versie 2008 R2 of 2012 vereist.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>Netwerkvereisten voor uw StorSimple-apparaat

Uw StorSimple-apparaat is een vergrendeld apparaat. Poorten moeten echter worden geopend in uw firewall om iSCSI-, Cloud-en beheer verkeer toe te staan. De volgende tabel geeft een lijst van de poorten die in uw firewall moeten worden geopend. *In of* uitgaand van deze *tabel verwijst naar* de richting van waar inkomende clients toegang tot uw apparaat aanvragen. *Out* of *uitgaand* verwijst naar de richting waarin uw StorSimple-apparaat gegevens extern verzendt, behalve de implementatie: bijvoorbeeld uitgaand naar Internet.

| Poort nummer<sup>1, 2</sup> | In of uit | Poort bereik | Vereist | Notities |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Uit |WAN |Nee |<ul><li>De uitgaande poort wordt gebruikt voor toegang tot internet om updates op te halen.</li><li>De uitgaande webproxy kan door de gebruiker worden geconfigureerd.</li><li>Voor het toestaan van systeem updates moet deze poort ook open zijn voor de vaste IP-adressen van de controller.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Uit |WAN |Ja |<ul><li>De uitgaande poort wordt gebruikt voor toegang tot gegevens in de Cloud.</li><li>De uitgaande webproxy kan door de gebruiker worden geconfigureerd.</li><li>Voor het toestaan van systeem updates moet deze poort ook open zijn voor de vaste IP-adressen van de controller.</li><li>Deze poort wordt ook gebruikt op beide controllers voor garbage collection.</li></ul> |
| UDP 53 (DNS) |Uit |WAN |In sommige gevallen; Zie opmerkingen. |Deze poort is alleen vereist als u een DNS-server op Internet gebruikt. |
| UDP 123 (NTP) |Uit |WAN |In sommige gevallen; Zie opmerkingen. |Deze poort is alleen vereist als u een NTP-server op Internet gebruikt. |
| TCP 9354 |Uit |WAN |Ja |De uitgaande poort wordt door het StorSimple-apparaat gebruikt om te communiceren met de StorSimple-Apparaatbeheer service. |
| 3260 (iSCSI) |In |LAN |Nee |Deze poort wordt gebruikt om toegang te krijgen tot gegevens via iSCSI. |
| 5985 |In |LAN |Nee |De binnenkomende poort wordt gebruikt door StorSimple Snapshot Manager om te communiceren met het StorSimple-apparaat.<br>Deze poort wordt ook gebruikt wanneer u extern verbinding maakt met Windows PowerShell voor StorSimple via HTTP. |
| 5986 |In |LAN |Nee |Deze poort wordt gebruikt wanneer u extern verbinding maakt met Windows PowerShell voor StorSimple via HTTPS. |

<sup>1</sup> er moeten geen binnenkomende poorten worden geopend op het open bare Internet.

<sup>2</sup> als meerdere poorten een gateway configuratie hebben, wordt de uitgaande route ring van verkeer bepaald op basis van de routerings volgorde van de poort die wordt beschreven in [poort routering](#routing-metric)hieronder.

<sup>3</sup> de vaste IP-adressen van de controller op uw StorSimple-apparaat moeten routeerbaar zijn en rechtstreeks of via de geconfigureerde webproxy verbinding kunnen maken met internet. De vaste IP-adressen worden gebruikt voor het onderhouden van de updates op het apparaat en voor het opschonen van de verzameling. Als de apparaatsets geen verbinding met internet kunnen maken via de vaste IP-adressen, kunt u het StorSimple-apparaat niet bijwerken en wordt de garbagecollection-verzameling niet goed uitgevoerd.

> [!IMPORTANT]
> Zorg ervoor dat de firewall geen TLS-verkeer tussen het StorSimple-apparaat en Azure wijzigt of ontsleutelt.


### <a name="url-patterns-for-firewall-rules"></a>URL-patronen voor firewall regels

Netwerk beheerders kunnen regel matig geavanceerde firewall regels configureren op basis van de URL-patronen om het binnenkomende en uitgaande verkeer te filteren. Uw StorSimple-apparaat en de StorSimple-Apparaatbeheer service zijn afhankelijk van andere micro soft-toepassingen, zoals Azure Service Bus, Azure Active Directory Access Control, opslag accounts en Microsoft Update servers. De URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gebruikt voor het configureren van firewall regels. Het is belang rijk te weten dat de URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gewijzigd. Dit heeft de netwerk beheerder nodig om de firewall regels voor uw StorSimple te controleren en bij te werken als dat nodig is.

We raden u aan om de firewall regels voor uitgaand verkeer, op basis van StorSimple vaste IP-adressen, in de meeste gevallen goed in te stellen. U kunt echter de onderstaande informatie gebruiken om geavanceerde firewall regels in te stellen die nodig zijn om beveiligde omgevingen te maken.

> [!NOTE]
> De IP-adressen van het apparaat moeten altijd worden ingesteld op alle ingeschakelde netwerk interfaces. De doel-Ip's moeten worden ingesteld op [Azure Data Center IP-bereiken](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


#### <a name="url-patterns-for-azure-portal"></a>URL-patronen voor Azure Portal

| URL-patroon | Onderdeel/functionaliteit | Ip's van apparaat |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple-apparaatbeheerfunctie<br>Access Control Service<br>Azure Service Bus<br>Verificatie service |Netwerk interfaces die zijn ingeschakeld voor de Cloud |
| `https://*.backup.windowsazure.com` |Apparaatregistratie |Alleen gegevens 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Certificaat intrekken |Netwerk interfaces die zijn ingeschakeld voor de Cloud |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure Storage-accounts en-bewaking |Netwerk interfaces die zijn ingeschakeld voor de Cloud |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servers<br> |Alleen vaste IP-adressen voor controller |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Alleen vaste IP-adressen voor controller |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Ondersteunings pakket |Netwerk interfaces die zijn ingeschakeld voor de Cloud |

#### <a name="url-patterns-for-azure-government-portal"></a>URL-patronen voor Azure Government Portal

| URL-patroon | Onderdeel/functionaliteit | Ip's van apparaat |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple-apparaatbeheerfunctie<br>Access Control Service<br>Azure Service Bus<br>Verificatie service |Netwerk interfaces die zijn ingeschakeld voor de Cloud |
| `https://*.backup.windowsazure.us` |Apparaatregistratie |Alleen gegevens 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Certificaat intrekken |Netwerk interfaces die zijn ingeschakeld voor de Cloud |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure Storage-accounts en-bewaking |Netwerk interfaces die zijn ingeschakeld voor de Cloud |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servers<br> |Alleen vaste IP-adressen voor controller |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Alleen vaste IP-adressen voor controller |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Ondersteunings pakket |Netwerk interfaces die zijn ingeschakeld voor de Cloud |

### <a name="routing-metric"></a>Routerings metriek

Een routerings metriek is gekoppeld aan de interfaces en de gateway die de gegevens naar de opgegeven netwerken routeren. De metrische gegevens van de route ring worden gebruikt door het routerings protocol om het beste pad naar een bepaalde bestemming te berekenen, als er meerdere paden naar hetzelfde doel zijn. Hoe lager de routerings metriek, des te hoger de voor keur.

Als meerdere netwerk interfaces en gateways zijn geconfigureerd voor kanaal verkeer, worden de metrische gegevens van de route ring in de context van StorSimple afgespeeld om de relatieve volg orde te bepalen waarin de interfaces zullen worden gebruikt. De metrische gegevens van de route ring kunnen niet worden gewijzigd door de gebruiker. U kunt de cmdlet echter gebruiken `Get-HcsRoutingTable` om de routerings tabel (en meet waarden) op uw StorSimple-apparaat af te drukken. Meer informatie over Get-HcsRoutingTable-cmdlet bij het [oplossen van problemen met StorSimple-implementatie](./storsimple-8000-troubleshoot-deployment.md).

De Routing metric-algoritme die wordt gebruikt voor update 2 en latere versies kunnen als volgt worden uitgelegd.

* Er is een set vooraf vastgestelde waarden toegewezen aan netwerk interfaces.
* Bekijk een voor beeld van een tabel die hieronder wordt weer gegeven met waarden die zijn toegewezen aan de verschillende netwerk interfaces wanneer deze Cloud zijn ingeschakeld of in de Cloud zijn uitgeschakeld, maar met een geconfigureerde gateway. Let op de hier toegewezen waarden zijn alleen voorbeeld waarden.

    | Netwerkinterface | Cloud ingeschakeld | Cloud-uitgeschakeld door gateway |
    |-----|---------------|---------------------------|
    | Gegevens 0  | 1            | -                        |
    | Gegevens 1  | 2            | 20                       |
    | Gegevens 2  | 3            | 30                       |
    | Gegevens 3  | 4            | 40                       |
    | Gegevens 4  | 5            | 50                       |
    | Gegevens 5  | 6            | 60                       |


* De volg orde waarin het Cloud verkeer wordt doorgestuurd via de netwerk interfaces is:
  
    *Data 0 > gegevens 1 > datum 2 > gegevens 3 > gegevens 4 > gegevens 5*
  
    Dit kan worden uitgelegd in het volgende voor beeld.
  
    Overweeg een StorSimple-apparaat met twee netwerk interfaces die zijn ingeschakeld voor de Cloud, data 0 en data 5. Gegevens 1 tot en met gegevens 4 zijn in de Cloud uitgeschakeld, maar hebben een geconfigureerde gateway. De volg orde waarin het verkeer voor dit apparaat wordt doorgestuurd, is:
  
    *Data 0 (1) > gegevens 5 (6) > gegevens 1 (20) > gegevens 2 (30) > gegevens 3 (40) > gegevens 4 (50)*
  
    *De getallen tussen haakjes geven de respectieve routerings metrische gegevens aan.*
  
    Als data 0 mislukt, wordt het Cloud verkeer gerouteerd via gegevens 5. Gezien de voor waarde dat een gateway in het andere netwerk is geconfigureerd, wordt het Cloud verkeer via data 1 door gegeven als zowel de gegevens 0 als de gegevens 5 zijn mislukt.
* Als er een fout optreedt in de Cloud netwerk interface, worden er 3 nieuwe pogingen gedaan met een vertraging van 30 seconden om verbinding te maken met de interface. Als alle pogingen mislukken, wordt het verkeer doorgestuurd naar de volgende beschik bare Cloud interface, zoals bepaald door de routerings tabel. Als alle netwerk interfaces die zijn ingeschakeld voor de Cloud, een failover uitvoeren voor het apparaat naar de andere controller (niet opnieuw opstarten in dit geval).
* Als er een VIP-fout is opgetreden voor een netwerk interface die iSCSI ondersteunt, worden er drie nieuwe pogingen gedaan met een vertraging van 2 seconden. Dit gedrag is hetzelfde als in de vorige versies. Als alle iSCSI-netwerk interfaces mislukken, treedt er een failover voor de controller op (vergezeld van een herstart).
* Er wordt ook een waarschuwing op uw StorSimple-apparaat gegenereerd wanneer er een VIP-fout optreedt. Ga voor meer informatie naar de [snelle referentie voor waarschuwingen](storsimple-8000-manage-alerts.md).
* Bij nieuwe pogingen heeft iSCSI voor rang op de Cloud.
  
    Bekijk het volgende voor beeld: voor een StorSimple-apparaat zijn twee netwerk interfaces ingeschakeld, data 0 en data 1. Data 0 is ingeschakeld voor de Cloud, terwijl data 1 zowel voor Cloud als voor iSCSI is ingeschakeld. Er zijn geen andere netwerk interfaces op dit apparaat ingeschakeld voor Cloud of iSCSI.
  
    Als de gegevens 1 mislukt, omdat dit de laatste iSCSI-netwerk interface is, resulteert dit in een failover van een controller naar data 1 op de andere controller.

### <a name="networking-best-practices"></a>Aanbevolen procedures voor netwerken

Naast de bovenstaande netwerk vereisten, voor de optimale prestaties van uw StorSimple-oplossing, moet u zich houden aan de volgende aanbevolen procedures:

* Zorg ervoor dat uw StorSimple-apparaat al te allen tijde een toegewezen band breedte van 40 Mbps heeft. Deze band breedte mag niet worden gedeeld (of toewijzing moet worden gegarandeerd door het gebruik van QoS-beleid) met andere toepassingen.
* Zorg ervoor dat de netwerk verbinding met Internet altijd beschikbaar is. Bij sporadische of onbetrouwbare Internet verbindingen met de apparaten, met inbegrip van geen Internet verbinding, wordt een niet-ondersteunde configuratie veroorzaakt.
* Isoleer het iSCSI-en Cloud verkeer door speciale netwerk interfaces op uw apparaat te hebben voor iSCSI-en Cloud toegang. Zie [netwerk interfaces wijzigen](storsimple-8000-modify-device-config.md#modify-network-interfaces) op uw StorSimple-apparaat voor meer informatie.
* Gebruik geen LACP-configuratie (Link Aggregation Control Protocol) voor uw netwerk interfaces. Dit is een niet-ondersteunde configuratie.

## <a name="high-availability-requirements-for-storsimple"></a>Vereisten voor hoge Beschik baarheid voor StorSimple

Het hardwareplatform dat deel uitmaakt van de StorSimple-oplossing bevat Beschik baarheid en betrouw baarheid die een basis bieden voor een Maxi maal beschik bare, fout tolerante opslag infrastructuur in uw Data Center. Er zijn echter vereisten en aanbevolen procedures waaraan u moet voldoen om de beschik baarheid van uw StorSimple-oplossing te garanderen. Voordat u StorSimple implementeert, moet u de volgende vereisten en aanbevolen procedures voor het StorSimple-apparaat en de aangesloten hostcomputers aandachtig door nemen.

Voor meer informatie over het bewaken en onderhouden van de hardwareonderdelen van uw StorSimple-apparaat gaat u naar [de StorSimple Apparaatbeheer-service gebruiken om de hardware-onderdelen en status](storsimple-8000-monitor-hardware-status.md) -en [StorSimple vervanging](storsimple-8000-hardware-component-replacement.md)van het hardwareonderdeel te controleren.

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Vereisten en procedures voor hoge Beschik baarheid voor uw StorSimple-apparaat

Lees de volgende informatie aandachtig door om te zorgen voor hoge Beschik baarheid van uw StorSimple-apparaat.

#### <a name="pcms"></a>PCMs

StorSimple-apparaten zijn redundante, hot-swappable stroom-en koel modules (PCMs). Elke PCM heeft voldoende capaciteit om service voor het hele chassis te bieden. Om hoge Beschik baarheid te garanderen, moeten beide PCMs zijn geïnstalleerd.

* Verbind uw PCMs met verschillende energie bronnen om Beschik baarheid te bieden als er een storing optreedt in een energie bron.
* Als een PCM mislukt, moet u onmiddellijk een vervanging aanvragen.
* Verwijder een defecte PCM alleen wanneer u deze hebt vervangen en gereed bent om deze te installeren.
* Verwijder beide PCMs niet tegelijk. De PCM-module bevat de module back-upbatterij. Als u beide PCMs verwijdert, wordt de computer niet meer afgesloten en wordt de status van het apparaat niet opgeslagen. Voor meer informatie over de accu gaat u naar [de module back-upbatterij onderhouden](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Controller modules

StorSimple-apparaten zijn redundante, hot-swappable controller modules. De controller modules worden op een actief/passieve manier gebruikt. Op een bepaald moment is één controller module actief en levert deze service, terwijl de andere controller module passief is. De module passieve controller is ingeschakeld en wordt operationeel als de module Active Controller mislukt of wordt verwijderd. Elke controller module heeft voldoende capaciteit om service voor het hele chassis te bieden. Beide-controller modules moeten worden geïnstalleerd om hoge Beschik baarheid te garanderen.

* Zorg ervoor dat beide controller modules te allen tijde zijn geïnstalleerd.
* Als een controller module uitvalt, moet u onmiddellijk een vervanging aanvragen.
* Verwijder een defecte controller module alleen wanneer u deze hebt vervangen en gereed bent om deze te installeren. Het verwijderen van een module voor uitgebreide Peri Oden is van invloed op de lucht stroom en dus op de koeling van het systeem.
* Zorg ervoor dat de netwerk verbindingen met beide controller modules identiek zijn en dat de verbonden netwerk interfaces een identieke netwerk configuratie hebben.
* Als een controller module uitvalt of moet worden vervangen, moet u ervoor zorgen dat de andere controller module zich in een actieve status bevindt voordat u de defecte controller module vervangt. Als u wilt controleren of een controller actief is, gaat u naar [de actieve controller op het apparaat identificeren](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Verwijder niet beide controller modules tegelijk. Als er een failover van een controller wordt uitgevoerd, sluit u de module stand-by controller niet af of verwijdert u deze uit het chassis.
* Na een failover van een controller moet u ten minste vijf minuten wachten voordat u de controller module verwijdert.

#### <a name="network-interfaces"></a>Netwerkinterfaces

StorSimple-apparaat-controller modules hebben elk 4 1 Gigabit-en 2 10 Gigabit Ethernet-netwerk interfaces.

* Zorg ervoor dat de netwerk verbindingen met beide controller modules identiek zijn en dat de netwerk interfaces waarmee de controller module interfaces zijn verbonden, dezelfde netwerk configuratie hebben.
* Implementeer, indien mogelijk, netwerk verbindingen tussen verschillende switches om te zorgen voor de beschik baarheid van de service in het geval van een storing in het netwerk apparaat.
* Als u de enige of de laatste iSCSI-ingeschakelde interface wilt ontkoppelen (met de toegewezen IP-adressen), schakelt u eerst de interface uit en koppelt u de kabels los. Als de interface eerst wordt ontkoppeld, zorgt dit ervoor dat de actieve controller een failover naar de passieve controller doorvoert. Als de passieve controller ook de bijbehorende interfaces heeft losgekoppeld, worden beide pogingen meerdere keren opnieuw opgestart voordat op één controller wordt verwikkeld.
* Verbind ten minste twee gegevens interfaces met het netwerk vanuit elke controller module.
* Als u de 2 10 GbE-interfaces hebt ingeschakeld, implementeert u deze in verschillende switches.
* Gebruik waar mogelijk MPIO op servers om ervoor te zorgen dat de servers een koppeling, netwerk of interface fout kunnen verdragen.

Ga voor meer informatie over het netwerken van uw apparaat voor hoge Beschik baarheid en prestaties naar [uw StorSimple 8100-apparaat installeren](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) of [installeer uw StorSimple 8600-apparaat](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>Ssd's en Hdd's

StorSimple-apparaten zijn onder andere Solid-State schijven (Ssd's) en harde schijven (Hdd's) die zijn beveiligd met gespiegelde Spaces. Gebruik van gespiegelde ruimten zorgt ervoor dat het apparaat de fout van een of meer Ssd's of Hdd's kan verdragen.

* Zorg ervoor dat alle SSD-en HDD-modules zijn geïnstalleerd.
* Als een SSD of harde schijf uitvalt, moet u onmiddellijk een vervanging aanvragen.
* Als een SSD of harde schijf uitvalt of moet worden vervangen, moet u ervoor zorgen dat u alleen de SSD of HDD verwijdert die moet worden vervangen.
* Verwijder op elk moment niet meer dan één SSD of HDD van het systeem.
  Een fout van twee of meer schijven van een bepaald type (HDD, SSD) of een opeenvolgende storing binnen een korte periode kan leiden tot systeem storingen en mogelijk gegevens verlies. Als dit gebeurt, [neemt u contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) voor hulp.
* Controleer tijdens vervanging de **gedeelde onderdelen** op de Blade **Hardware-status** voor de stations in de ssd's en hdd's. Een groen vinkje geeft aan dat de schijven in orde of OK zijn, terwijl een rood uitroep teken duidt op een defecte SSD of harde schijf.
* U wordt aangeraden Cloud momentopnamen te configureren voor alle volumes die u moet beveiligen in het geval van een systeem fout.

#### <a name="ebod-enclosure"></a>EBOD-behuizing

StorSimple device model 8600 bevat naast de primaire behuizing ook een EBOD-behuizing (Extendedity of disks). Een EBOD bevat EBOD-controllers en harde schijven (Hdd's) die zijn beveiligd met gespiegelde ruimten. Gebruik van gespiegelde ruimten zorgt ervoor dat het apparaat de fout van een of meer Hdd's kan verdragen. De EBOD Enclosure is verbonden met de primaire behuizing via redundante SAS-kabels.

* Zorg ervoor dat zowel de EBOD Enclosure controller-modules, zowel SAS-kabels als alle harde schijven altijd zijn geïnstalleerd.
* Als er een storing optreedt in een module van een EBOD Enclosure controller, moet u onmiddellijk een vervanging aanvragen.
* Als er een fout optreedt in een module van een EBOD Enclosure controller, moet u ervoor zorgen dat de andere controller module actief is voordat u de defecte module vervangt. Als u wilt controleren of een controller actief is, gaat u naar [de actieve controller op het apparaat identificeren](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
* Tijdens een vervanging van een EBOD-controller module controleert u voortdurend de status van het onderdeel in de StorSimple-Apparaatbeheer-service door de hardware-status te **controleren**  >  .
* Als een SAS-kabel uitvalt of moet worden vervangen (Microsoft Ondersteuning moet worden betrokken bij een dergelijke bepaling), moet u ervoor zorgen dat u alleen de SAS-kabel verwijdert die moet worden vervangen.
* Verwijder beide SAS-kabels niet gelijktijdig van het systeem op een bepaald moment.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Aanbevelingen voor hoge Beschik baarheid voor uw hostcomputers

Lees deze best practices aandachtig door om te zorgen voor een hoge Beschik baarheid van hosts die zijn verbonden met uw StorSimple-apparaat.

* Configureer StorSimple met [twee knooppunt configuratie van bestands server clusters][1]. Door afzonderlijke storings punten te verwijderen en te bouwen in redundantie aan de kant van de host, wordt de volledige oplossing Maxi maal beschikbaar.
* Gebruik voortdurend beschik bare shares (CA) die beschikbaar zijn in Windows Server 2012 (SMB 3,0) voor hoge Beschik baarheid tijdens de failover van de opslag controllers. Raadpleeg deze [video demo](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)voor meer informatie over het configureren van bestands server clusters en continu beschik bare shares met Windows Server 2012.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over StorSimple-systeem limieten](storsimple-8000-limits.md).
* [Meer informatie over het implementeren van uw StorSimple-oplossing](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731844(v=ws.10)