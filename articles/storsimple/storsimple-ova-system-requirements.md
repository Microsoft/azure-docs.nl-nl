---
title: Systeem vereisten voor de virtuele matrix Microsoft Azure StorSimple
description: Meer informatie over de software-en netwerk vereisten voor uw virtuele StorSimple-matrix
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 5970e82619667a47ba160c84df2cdeb145b0dab8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966170"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Systeemvereisten voor StorSimple virtuele array

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Overzicht

In dit artikel worden de belangrijkste systeem vereisten beschreven voor uw Microsoft Azure StorSimple virtuele matrix en voor de opslag-clients die toegang hebben tot de matrix. We raden u aan de informatie zorgvuldig te bekijken voordat u uw StorSimple-systeem implementeert en vervolgens naar de gewenste gegevens te verwijzen tijdens de implementatie en de volgende bewerking.

De systeem vereisten zijn onder andere:

* **Software vereisten voor opslag clients** : beschrijft de ondersteunde virtualisatieplatform, webbrowsers, iSCSI-initia Tors, SMB-clients, minimale vereisten voor virtuele apparaten en eventuele aanvullende vereisten voor deze besturings systemen.
* **Netwerk vereisten voor het StorSimple-apparaat** : bevat informatie over de poorten die in uw firewall moeten worden geopend om iSCSI-, Cloud-of beheer verkeer toe te staan.

De informatie over de StorSimple-systeem vereisten die in dit artikel wordt gepubliceerd, is alleen van toepassing op virtuele StorSimple-matrices.

* Voor 8000-serie apparaten gaat u naar [systeem vereisten voor uw StorSimple 8000-serie apparaat](./storsimple-8000-system-requirements.md).
* Voor 7000-serie apparaten gaat u naar [systeem vereisten voor uw StorSimple 5000-7000-serie apparaat](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Softwarevereisten
De software vereisten omvatten de informatie over de ondersteunde webbrowsers, SMB-versies, virtualisatieplatform en de minimale vereisten voor virtuele apparaten.

### <a name="supported-virtualization-platforms"></a>Ondersteunde virtualisatieplatform
| **Hypervisor** | **Versie** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 en hoger |
| VMware ESXi |5,0, 5,5, 6,0 en 6,5. |

> [!IMPORTANT]
> Installeer geen VMware-hulpprogram ma's op uw virtuele StorSimple-matrix. Dit leidt tot een niet-ondersteunde configuratie.

### <a name="virtual-device-requirements"></a>Vereisten voor virtuele apparaten
| **Onderdeel** | **Vereiste** |
| --- | --- |
| Minimum aantal virtuele processors (kern geheugens) |4 |
| Mini maal geheugen (RAM) |8 GB <br> Voor een bestands Server 8 GB voor minder dan 2.000.000 bestanden en 16 GB voor 2-4 miljoen bestanden|
| Schijf ruimte<sup>1</sup> |BESTURINGSSYSTEEM schijf-80 GB <br></br>Gegevens schijf-500 GB tot 8 TB |
| Minimum aantal netwerk interface (s) |1 |
| Internet bandbreedte<sup>2</sup> |Mini maal vereiste band breedte: 5 Mbps <br> Aanbevolen band breedte: 100 Mbps <br> De snelheid van gegevens overdracht kan worden geschaald met de Internet bandbreedte. 100 GB aan gegevens duurt bijvoorbeeld 2 dagen om een overdracht uit te voeren met 5 Mbps, wat kan leiden tot back-upfouten omdat dagelijkse back-ups niet op een dag worden voltooid. Met een band breedte van 100 Mbps kan 100 GB aan gegevens worden overgebracht in 2,5 uur.   |

<sup>1</sup> -Thin-provisioned

<sup>2</sup> : de netwerk vereisten kunnen variëren, afhankelijk van de dagelijkse gegevens wijzigings frequentie. Als een apparaat bijvoorbeeld een back-up moet maken van 10 GB of meer wijzigingen gedurende een dag, kan de dagelijkse back-up tot 4,25 uur duren (als de gegevens niet kunnen worden gecomprimeerd of gedupliceerd).

### <a name="supported-web-browsers"></a>Ondersteunde webbrowsers
| **Onderdeel** | **Versie** | **Aanvullende vereisten/notities** |
| --- | --- | --- |
| Microsoft Edge |Nieuwste versie | |
| Internet Explorer |Nieuwste versie |Getest met Internet Explorer 11 |
| Google Chrome |Nieuwste versie |Getest met Chrome 46 |

### <a name="supported-storage-clients"></a>Ondersteunde opslag clients
De volgende software vereisten gelden voor de iSCSI-initia tors die toegang hebben tot uw virtuele StorSimple-matrix (geconfigureerd als een iSCSI-server).

| **Ondersteunde besturingssystemen** | **Versie vereist** | **Aanvullende vereisten/notities** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |StorSimple kan Thin provisioned en volledig ingerichte volumes maken. Er kunnen geen gedeeltelijk ingerichte volumes worden gemaakt. StorSimple iSCSI-volumes worden alleen ondersteund voor: <ul><li>Eenvoudige volumes op Windows Basic-schijven.</li><li>Windows NTFS voor het format teren van een volume.</li> |

De volgende software vereisten gelden voor de SMB-clients die toegang hebben tot uw virtuele StorSimple-matrix (geconfigureerd als een bestands server).

| **SMB-versie** |
| --- |
| SMB 2. x |
| SMB 3.0 |
| SMB 3,02 |

> [!IMPORTANT]
> Kopieer geen bestanden die worden beveiligd door Windows Encrypting File System (EFS) naar de StorSimple-Bestands server (virtuele matrix). Dit leidt tot een niet-ondersteunde configuratie.


### <a name="supported-storage-format"></a>Ondersteunde opslag indeling
Alleen de Azure Block Blob Storage wordt ondersteund. Pagina-blobs worden niet ondersteund. Meer informatie [over blok-blobs en pagina-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Netwerkvereisten
De volgende tabel geeft een lijst van de poorten die in uw firewall moeten worden geopend om iSCSI-, SMB-, Cloud-of beheer verkeer toe te staan. *In of* uitgaand van deze *tabel verwijst naar* de richting van waar inkomende clients toegang tot uw apparaat aanvragen. *Out* of *uitgaand* verwijst naar de richting waarin uw StorSimple-apparaat gegevens extern verzendt, behalve de implementatie: bijvoorbeeld uitgaand naar Internet.

| **Poort nummer <sup>1</sup>** | **In of uit** | **Poort bereik** | **Vereist** | **Opmerkingen** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Uit |WAN |Nee |De uitgaande poort wordt gebruikt voor toegang tot internet om updates op te halen. <br></br>De uitgaande webproxy kan door de gebruiker worden geconfigureerd. |
| TCP 443 (HTTPS) |Uit |WAN |Ja |De uitgaande poort wordt gebruikt voor toegang tot gegevens in de Cloud. <br></br>De uitgaande webproxy kan door de gebruiker worden geconfigureerd. |
| UDP 53 (DNS) |Uit |WAN |In sommige gevallen; Zie opmerkingen. |Deze poort is alleen vereist als u een DNS-server op Internet gebruikt. <br></br> Houd er rekening mee dat bij het implementeren van een bestands server het gebruik van lokale DNS-server wordt aangeraden. |
| UDP 123 (NTP) |Uit |WAN |In sommige gevallen; Zie opmerkingen. |Deze poort is alleen vereist als u een NTP-server op Internet gebruikt.<br></br> Houd er rekening mee dat bij het implementeren van een bestands server de synchronisatie tijd met uw Active Directory domein controllers wordt aanbevolen. |
| TCP 80 (HTTP) |In |LAN |Ja |Dit is de binnenkomende poort voor lokale gebruikers interface op het StorSimple-apparaat voor lokaal beheer. <br></br> Houd er rekening mee dat toegang tot de lokale gebruikers interface via HTTP automatisch wordt omgeleid naar HTTPS. |
| TCP 443 (HTTPS) |In |LAN |Ja |Dit is de binnenkomende poort voor lokale gebruikers interface op het StorSimple-apparaat voor lokaal beheer. |
| TCP 3260 (iSCSI) |In |LAN |Nee |Deze poort wordt gebruikt om toegang te krijgen tot gegevens via iSCSI. |

<sup>1</sup> er moeten geen binnenkomende poorten worden geopend op het open bare Internet.

> [!IMPORTANT]
> Zorg ervoor dat de firewall geen TLS-verkeer tussen het StorSimple-apparaat en Azure wijzigt of ontsleutelt.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>URL-patronen voor firewall regels
Netwerk beheerders kunnen regel matig geavanceerde firewall regels configureren op basis van de URL-patronen om het binnenkomende en uitgaande verkeer te filteren. Uw virtuele matrix en de StorSimple-Apparaatbeheer service zijn afhankelijk van andere micro soft-toepassingen, zoals Azure Service Bus, Azure Active Directory Access Control, opslag accounts en Microsoft Update servers. De URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gebruikt voor het configureren van firewall regels. Het is belang rijk te weten dat de URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gewijzigd. Dit heeft de netwerk beheerder nodig om de firewall regels voor uw StorSimple te controleren en bij te werken als dat nodig is. 

We raden u aan om de firewall regels voor uitgaand verkeer, op basis van StorSimple vaste IP-adressen, in de meeste gevallen goed in te stellen. U kunt echter de onderstaande informatie gebruiken om geavanceerde firewall regels in te stellen die nodig zijn om beveiligde omgevingen te maken.

> [!NOTE]
> 
> * De IP-adressen van het apparaat moeten altijd worden ingesteld op alle netwerk interfaces die zijn ingeschakeld voor de Cloud. 
> * De doel-Ip's moeten worden ingesteld op [Azure Data Center IP-bereiken](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| URL-patroon | Onderdeel/functionaliteit |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple-apparaatbeheerfunctie<br>Access Control Service<br>Azure Service Bus<br>Verificatie service|
| `http://*.backup.windowsazure.com` |Apparaatregistratie |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Certificaat intrekken |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure Storage-accounts en-bewaking |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update-servers<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Ondersteunings pakket |
| `https://*.data.microsoft.com` |Telemetrie-service in Windows, zie de [Update voor klant ervaring en diagnostische telemetrie](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Volgende stappen
* [De portal voorbereiden om uw virtuele StorSimple-matrix te implementeren](storsimple-virtual-array-deploy1-portal-prep.md)