---
title: Schaalbaarheids- en prestatiedoelen in Azure Files
description: Meer informatie over de schaalbaarheids- en prestatiedoelen voor Azure Files, waaronder de capaciteit, aanvraagsnelheid en bandbreedtelimieten voor binnenkomende en uitgaande gegevens.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 276dd7aa1925fefaaa94dfdd5d7a5baba5164f56
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790253"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Schaalbaarheids- en prestatiedoelen in Azure Files
[Azure Files](storage-files-introduction.md) biedt volledig beheerde bestands shares in de cloud die toegankelijk zijn via de SMB- en NFS-bestandssysteemprotocollen. In dit artikel worden de schaalbaarheids- en prestatiedoelen voor Azure Files en Azure File Sync.

De hier vermelde schaalbaarheids- en prestatiedoelen zijn hoogwaardige doelen, maar kunnen worden beïnvloed door andere variabelen in uw implementatie. De doorvoer voor een bestand kan bijvoorbeeld ook worden beperkt door de beschikbare netwerkbandbreedte, niet alleen de servers die als host voor uw Azure-bestands shares worden gebruikt. We raden u ten zeerste aan om uw gebruikspatroon te testen om te bepalen of de schaalbaarheid en prestaties van Azure Files voldoen aan uw vereisten. We doen er ook alles aan om deze limieten na een periode te verhogen. 

## <a name="azure-files-scale-targets"></a>Azure Files-schaaldoelen
Azure-bestandsshares worden geïmplementeerd in opslagaccounts. Dat zijn objecten op het hoogste niveau die een gedeelde opslagpool vertegenwoordigen. Deze opslaggroep kan worden gebruikt om meerdere bestands shares te implementeren. Er zijn daarom drie categorieën om rekening mee te houden: opslagaccounts, Azure-bestands shares en bestanden.

### <a name="storage-account-scale-targets"></a>Schaaldoelen voor opslagaccounts
Azure ondersteunt meerdere typen opslagaccounts voor verschillende opslagscenario's die klanten mogelijk hebben, maar er zijn twee hoofdtypen opslagaccounts voor Azure Files. Welk type opslagaccount u moet maken, is afhankelijk van of u een standaardbestands share of een Premium-bestands share wilt maken: 

- **GPv2-opslagaccounts (versie twee voor algemeen gebruik)** : Met GPv2-opslagaccounts kunt u Azure-bestandsshares implementeren op (HDD-)hardware met een standaard/harde schijf. Naast het opslaan van Azure-bestandsshares kunnen GPv2-opslagaccounts andere opslagresources, zoals blobcontainers, wachtrijen of tabellen, opslaan. Bestands shares kunnen worden geïmplementeerd in de voor transacties geoptimaliseerde (standaard),hot- of cool-laag.

- **FileStorage-opslagaccounts**: Met FileStorage-opslagaccounts kunt u Azure-bestandsshares implementeren op (SSD-)hardware met een premium/solid-state schijf. FileStorage-accounts kunnen alleen worden gebruikt voor het opslaan van Azure-bestandsshares. Er kunnen geen andere opslagresources (blobcontainers, wachtrijen, tabellen enz.) worden geïmplementeerd in een FileStorage-account.

| Kenmerk | GPv2-opslagaccounts (standaard) | FileStorage-opslagaccounts (premium) |
|-|-|-|
| Aantal opslagaccounts per regio per abonnement | 250 | 250 |
| Maximale capaciteit van opslagaccount | 5 PiB<sup>1</sup> | 100 TiB (ingericht) |
| Maximum aantal bestands shares | Onbeperkt | Onbeperkt, de totale inrichtende grootte van alle shares moet kleiner zijn dan het maximum dan de maximale capaciteit van het opslagaccount |
| Maximum aantal gelijktijdige aanvragen | 20.000 IOPS<sup>1</sup> | 100.000 IOPS |
| Maximum aantal ingressen | <ul><li>VS/Europa: 10 Gbps per seconde<sup>1</sup></li><li>Andere regio's (LRS/ZRS): 10 Gbps per seconde<sup>1</sup></li><li>Andere regio's (GRS): 5 Gbps per seconde<sup>1</sup></li></ul> | 4136 MiB per seconde |
| Maximum aantal uitgressen | 50 Gbps per seconde<sup>1</sup> | 6204 MiB/sec |
| Maximum aantal regels voor virtuele netwerken | 200 | 200 |
| Maximum aantal IP-adresregels | 200 | 200 |
| Leesbewerkingen voor beheer | 800 per 5 minuten | 800 per 5 minuten |
| Schrijfbewerkingen voor beheer | 10 per seconde/1200 per uur | 10 per seconde/1200 per uur |
| Beheerlijstbewerkingen | 100 per 5 minuten | 100 per 5 minuten |

<sup>1</sup> Opslagaccounts voor algemeen gebruik versie 2 ondersteunen hogere capaciteitslimieten en hogere limieten voor ingress by request. Neem contact op met de [Azure-ondersteuning](https://azure.microsoft.com/support/faq/) om een hogere accountlimiet aan te vragen.

### <a name="azure-file-share-scale-targets"></a>Schaaldoelen voor Azure-bestands delen
| Kenmerk | Standaardbestands shares<sup>1</sup> | Premiumbestandsshares |
|-|-|-|
| Minimale grootte van een bestandsshare | Geen minimum | 100 GiB (ingericht) |
| Inrichten van eenheid voor vergroten/verlagen van grootte | N.v.t. | 1 GiB |
| Maximale grootte van een bestandsshare | <ul><li>100 TiB, met functie voor grote bestands delen ingeschakeld<sup>2</sup></li><li>5 TiB, standaard</li></ul> | 100 TiB |
| Maximum aantal bestanden in een bestandsshare | Geen limiet | Geen limiet |
| Maximale aanvraagsnelheid (max. IOPS) | <ul><li>10.000, met functie voor grote bestands delen ingeschakeld<sup>2</sup></li><li>1000 of 100 aanvragen per 100 ms, standaard</li></ul> | <ul><li>Basislijn-IOPS: 400 + 1 IOPS per GiB, maximaal 100.000</li><li>IOPS-bursting: Maximaal (4000.3x IOPS per GiB), maximaal 100.000</li></ul> |
| Maximum aantal inkomende waarden voor één bestandsshare | <ul><li>Maximaal 300 MiB/sec, met functie voor grote bestands delen ingeschakeld<sup>2</sup></li><li>Maximaal 60 MiB/sec, standaard</li></ul> | 40 MiB/s + 0,04 * inrichten GiB |
| Maximum aantal uitgaande waarden voor één bestandsshare | <ul><li>Maximaal 300 MiB/sec, met functie voor grote bestands delen ingeschakeld<sup>2</sup></li><li>Maximaal 60 MiB/sec, standaard</li></ul> | 60 MiB/s + 0,06 * inrichten GiB |
| Maximum aantal momentopnamen van shares | 200 momentopnamen | 200 momentopnamen |
| Maximum lengte van de naam van het object (mappen en bestanden) | 2048 tekens | 2048 tekens |
| Maximum pathname-onderdeel (in het pad \A\B\C\D is elke letter een onderdeel) | 255 tekens | 255 tekens |
| Limiet voor vaste koppelingen (alleen NFS) | N.v.t. | 178 |
| Maximumaantal SMB Multichannel-kanalen | N.v.t. | 4 |
| Maximaal aantal opgeslagen toegangsbeleidsregels per bestandsshare | 5 | 5 |

<sup>1</sup> De limieten voor standaardbestands shares zijn van toepassing op alle drie de lagen die beschikbaar zijn voor standaardbestands shares: geoptimaliseerd voor transacties, hot en cool.

<sup>2</sup> De standaardwaarde voor standaardbestands shares is 5 TiB. Zie Enable [and create large file shares](./storage-files-how-to-create-large-file-share.md) (Grote bestands shares inschakelen en maken) voor meer informatie over het verhogen van de standaardbestands shares tot 100 TiB.

### <a name="file-scale-targets"></a>Bestandsschaaldoelen
| Kenmerk | Bestanden in standaardbestands shares  | Bestanden in Premium-bestands shares  |
|-|-|-|
| Maximale bestandsgrootte | 4 TiB | 4 TiB |
| Maximum aantal gelijktijdige aanvragen | 1000 IOPS | Maximaal 8000<sup>1</sup> |
| Maximum aantal ingressen voor een bestand | 60 MiB/sec | 200 MiB/sec (maximaal 1 GiB/s met SMB meerdere kanalen preview)<sup>2</sup>|
| Maximum aantal egressen voor een bestand | 60 MiB/sec | 300 MiB/sec (maximaal 1 GiB/s met SMB meerdere kanalen preview)<sup>2</sup> |
| Maximum aantal gelijktijdige grepen | 2000 grepen | 2000 grepen  |

<sup>1 Is van toepassing op IO's voor lezen en schrijven (meestal kleinere I/O-grootten kleiner dan of gelijk aan 64 KiB). Bewerkingen met metagegevens, met andere dan lees- en schrijfbewerkingen, kunnen lager zijn.</sup> 
 <sup>2 Afhankelijk van netwerklimieten voor machines, beschikbare bandbreedte, I/O-grootten, wachtrijdiepte en andere factoren. Zie [SMB meerdere kanalen voor meer informatie.](./storage-files-smb-multichannel-performance.md)</sup>

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync-schaaldoelen
De volgende tabel geeft de grenzen aan van de tests van Microsoft en geeft ook aan welke doelen harde limieten zijn:

| Resource | Doel | Harde limiet |
|----------|--------------|------------|
| Opslagsynchronisatieservices per regio | 100 opslagsynchronisatieservices | Ja |
| Synchronisatiegroepen per opslagsynchronisatieservice | 200 synchronisatiegroepen | Ja |
| Geregistreerde servers per opslagsynchronisatieservice | 99 servers | Ja |
| Cloudeindpunten per synchronisatiegroep | 1 cloudeindpunt | Ja |
| Servereindpunten per synchronisatiegroep | 100 servereindpunten | Yes |
| Servereindpunten per server | 30 servereindpunten | Ja |
| Bestandssysteemobjecten (mappen en bestanden) per synchronisatiegroep | 100 miljoen objecten | Nee |
| Maximumaantal bestandssysteemobjecten (mappen en bestanden) in een map | 5 miljoen objecten | Ja |
| Maximumgrootte beveiligingsbeschrijving (mappen en bestanden) van objecten | 64 KiB | Ja |
| Bestandsgrootte | 100 GiB | Nee |
| Minimale bestandsgrootte om een bestand gelaagd te maken | V9 en nieuwer: Op basis van de grootte van het bestandssysteemcluster (dubbele bestandssysteemclustergrootte). Als de grootte van het bestandssysteemcluster bijvoorbeeld 4 KiB is, is de minimale bestandsgrootte 8 KiB.<br> V8 en ouder: 64 KiB  | Ja |

> [!Note]  
> Een Azure File Sync-eindpunt kan tot de grootte van een Azure-bestandsshare worden opgeschaald. Als de limiet van de Azure-bestandsshare, kan er niet meer worden gesynchroniseerd.

### <a name="azure-file-sync-performance-metrics"></a>Metrische Azure File Sync-gegevens voor prestaties
Omdat de Azure File Sync-agent wordt uitgevoerd op een Windows Server-computer die verbinding maakt met de Azure-bestands shares, zijn de effectieve synchronisatieprestaties afhankelijk van een aantal factoren in uw infrastructuur: Windows Server en de onderliggende schijfconfiguratie, de netwerkbandbreedte tussen de server en de Azure-opslag, de bestandsgrootte, de totale grootte van de gegevensset en de activiteit op de gegevensset. Omdat Azure File Sync op bestandsniveau werkt, worden de prestatiekenmerken van een Azure File Sync-oplossing beter gemeten in het aantal objecten (bestanden en mappen) dat per seconde wordt verwerkt.

Voor Azure File Sync zijn de prestaties in twee fasen essentieel:

1. **Eerste een keer inrichten:** als u de prestaties bij de eerste inrichting wilt optimaliseren, raadpleegt u [Onboarding](../file-sync/file-sync-deployment-guide.md#onboarding-with-azure-file-sync) met Azure File Sync voor de optimale implementatiedetails.
2. **Doorlopende synchronisatie:** nadat de gegevens in eerste instantie in de Azure-bestands shares zijn geseed, Azure File Sync meerdere eindpunten gesynchroniseerd.

Hieronder vindt u de resultaten die worden waargenomen tijdens de interne tests op een systeem met een configuratie om u te helpen bij het plannen van uw implementatie voor elk van de fasen

| Systeemconfiguratie | Details |
|-|-|
| CPU | 64 virtuele kernen met 64 MiB L3-cache |
| Geheugen | 128 GiB |
| Schijf | SAS-schijven met RAID 10 met cache met accu |
| Netwerk | 1 Gbps-netwerk |
| Workload | Algemeen-bestandsserver|

| Eerste een keer inrichten  | Details |
|-|-|
| Aantal objecten | 25 miljoen objecten |
| Grootte van gegevensset| ~4,7 TiB |
| Gemiddelde bestandsgrootte | ~200 KiB (grootste bestand: 100 GiB) |
| Initiële enumeratie van cloudwijziging | 20 objecten per seconde  |
| Doorvoer uploaden | 20 objecten per seconde per synchronisatiegroep |
| Doorvoer van naamruimte downloaden | 400 objecten per seconde |

### <a name="initial-one-time-provisioning"></a>Eerste een keer inrichten

**Initiële enumeratie voor cloudwijziging:** wanneer er een nieuwe synchronisatiegroep wordt gemaakt, is de initiële enumeratie van cloudwijziging de eerste stap die wordt uitgevoerd. In dit proces worden alle items in de Azure-bestands share door het systeem opsnoemd. Tijdens dit proces is er geen synchronisatieactiviteit, dat wil zeggen dat er geen items worden gedownload van het cloud-eindpunt naar het server-eindpunt en dat er geen items worden geüpload van het server-eindpunt naar het cloud-eindpunt. De synchronisatieactiviteit wordt hervat zodra de initiële enumeratie van cloudwijziging is voltooid.
De snelheid van de prestaties is 20 objecten per seconde. Klanten kunnen een schatting maken van de tijd die nodig is om de initiële enumeratie van cloudwijziging te voltooien door het aantal items in de cloud share te bepalen en de volgende formule te gebruiken om de tijd in dagen te bepalen. 

   **Tijd (in dagen) voor initiële cloudinumeratie = (aantal objecten in cloud-eindpunt)/(20 * 60 * 60 * 24)**

**Initiële synchronisatie** van gegevens van Windows Server naar Azure-bestands share: veel Azure File Sync-implementaties beginnen met een lege Azure-bestands share, omdat alle gegevens zich op de Windows Server. In dergelijke gevallen is de initiële enumeratie van cloudwijzigingen snel en wordt het merendeel van de tijd besteed aan het synchroniseren van wijzigingen van De Windows Server naar de Azure-bestands share(s). 

Synchronisatie uploadt gegevens naar de Azure-bestands share, maar er is [](../file-sync/file-sync-server-registration.md#set-azure-file-sync-network-limits) geen downtime op de lokale bestandsserver en beheerders kunnen netwerklimieten instellen om de hoeveelheid bandbreedte te beperken die wordt gebruikt voor het uploaden van gegevens op de achtergrond.

Initiële synchronisatie wordt doorgaans beperkt door de initiële uploadsnelheid van 20 bestanden per seconde per synchronisatiegroep. Klanten kunnen de tijd schatten om al hun gegevens naar Azure te uploaden met behulp van de volgende formules om tijd in dagen te krijgen:  

   **Tijd (in dagen) voor het uploaden van bestanden naar een synchronisatiegroep = (aantal objecten in server-eindpunt)/(20 * 60 * 60 * 24)**

Het splitsen van uw gegevens in meerdere server-eindpunten en synchronisatiegroepen kan deze initiële gegevensupload versnellen, omdat het uploaden parallel kan worden uitgevoerd voor meerdere synchronisatiegroepen met een snelheid van 20 items per seconde per seconde. Er worden dus twee synchronisatiegroepen uitgevoerd met een gecombineerde snelheid van 40 items per seconde. De totale tijd die moet worden voltooid, is de geschatte tijd voor de synchronisatiegroep met de meeste bestanden die moeten worden gesynchroniseerd.

**Doorvoer voor het downloaden van naamruimten** Wanneer een nieuw server-eindpunt wordt toegevoegd aan een bestaande synchronisatiegroep, downloadt de Azure File Sync-agent geen bestandsinhoud van het cloud-eindpunt. Eerst wordt de volledige naamruimte gesynchroniseerd en vervolgens wordt terughalen op de achtergrond activeert om de bestanden te downloaden, in zijn geheel of, als cloudopslaglagen zijn ingeschakeld, naar het cloudopslaglaagbeleid dat is ingesteld op het server-eindpunt.

| Doorlopende synchronisatie  | Details  |
|-|--|
| Aantal gesynchroniseerde objecten| 125.000 objecten (ongeveer 1% verloop) |
| Grootte van gegevensset| 50 GiB |
| Gemiddelde bestandsgrootte | ~500 KiB |
| Doorvoer uploaden | 20 objecten per seconde per synchronisatiegroep |
| Volledige doorvoer downloaden* | 60 objecten per seconde |

*Als opslag in cloudlagen is ingeschakeld, zijn de prestaties waarschijnlijk beter omdat slechts een deel van de bestandsgegevens wordt gedownload. Azure File Sync downloadt alleen de gegevens van bestanden in de cache wanneer ze worden gewijzigd op een van de eindpunten. Voor gelaagde of nieuw gemaakte bestanden downloadt de agent de bestandsgegevens niet en synchroniseert de agent in plaats daarvan alleen de naamruimte met alle server-eindpunten. De agent ondersteunt ook gedeeltelijke downloads van gelaagde bestanden, omdat deze worden gebruikt door de gebruiker. 

> [!Note]  
> De bovenstaande getallen zijn geen indicatie van de prestaties die u ervaart. De werkelijke prestaties zijn afhankelijk van meerdere factoren, zoals beschreven in het begin van deze sectie.

Als algemene handleiding voor uw implementatie moet u rekening houden met het volgende:

- De objectdoorvoer wordt ongeveer geschaald in verhouding tot het aantal synchronisatiegroepen op de server. Het splitsen van gegevens in meerdere synchronisatiegroepen op een server levert een betere doorvoer op, die ook wordt beperkt door de server en het netwerk.
- De objectdoorvoer is in omgekeerde verhouding tot de doorvoer van MiB per seconde. Voor kleinere bestanden ervaart u een hogere doorvoer wat betreft het aantal objecten dat per seconde wordt verwerkt, maar een lagere MiB-doorvoer per seconde. Voor grotere bestanden worden daarentegen minder objecten per seconde verwerkt, maar een hogere MiB-doorvoer per seconde. De doorvoer van MiB per seconde wordt beperkt door de Azure Files schaaldoelen.

## <a name="see-also"></a>Zie ook
- [Een Azure Files-implementatie plannen](storage-files-planning.md)
- [Planning voor een Azure Files Sync-implementatie](../file-sync/file-sync-planning.md)