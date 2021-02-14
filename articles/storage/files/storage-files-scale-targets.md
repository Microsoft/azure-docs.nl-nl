---
title: Schaalbaarheids- en prestatiedoelen in Azure Files
description: Meer informatie over de schaalbaarheids-en prestatie doelen voor Azure Files, met inbegrip van de capaciteit, aanvraag snelheid en binnenkomende en uitgaande bandbreedte limieten.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6ef255d78d3dd3ff6fcc5eba7aad522018185299
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518892"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Schaalbaarheids- en prestatiedoelen in Azure Files
[Azure files](storage-files-introduction.md) biedt volledig beheerde bestands shares in de cloud die toegankelijk zijn via de protocollen van het SMB-en NFS-bestands systeem. In dit artikel worden de schaalbaarheids-en prestatie doelen voor Azure Files en Azure File Sync beschreven.

De schaal baarheid en prestatie doelen die hier worden weer gegeven, zijn hoogwaardige doelen, maar kunnen worden beïnvloed door andere variabelen in uw implementatie. De door Voer voor een bestand kan bijvoorbeeld ook worden beperkt door de beschik bare netwerk bandbreedte, niet alleen de servers die als host fungeren voor uw Azure-bestands shares. We raden u ten zeerste aan om uw gebruiks patroon te testen om te bepalen of de schaal baarheid en prestaties van Azure Files voldoen aan uw vereisten. We zijn ook van belang om deze limieten in de loop van de tijd te verhogen. 

## <a name="azure-files-scale-targets"></a>Azure Files-schaaldoelen
Azure-bestandsshares worden geïmplementeerd in opslagaccounts. Dat zijn objecten op het hoogste niveau die een gedeelde opslagpool vertegenwoordigen. Deze opslag groep kan worden gebruikt voor het implementeren van meerdere bestands shares. Er zijn drie categorieën die u moet overwegen: opslag accounts, Azure-bestands shares en bestanden.

### <a name="storage-account-scale-targets"></a>Schaal doelen van opslag account
Azure ondersteunt meerdere typen opslag accounts voor verschillende opslag scenario's die klanten kunnen hebben, maar er zijn twee hoofd typen opslag accounts voor Azure Files. Welk type opslag account u moet maken, is afhankelijk van of u een standaard bestands share of een Premium-bestands share wilt maken: 

- **GPv2-opslagaccounts (versie twee voor algemeen gebruik)** : Met GPv2-opslagaccounts kunt u Azure-bestandsshares implementeren op (HDD-)hardware met een standaard/harde schijf. Naast het opslaan van Azure-bestandsshares kunnen GPv2-opslagaccounts andere opslagresources, zoals blobcontainers, wachtrijen of tabellen, opslaan. Bestands shares kunnen worden geïmplementeerd in de door de trans actie geoptimaliseerde (standaard), warme of koud lagen.

- **FileStorage-opslagaccounts**: Met FileStorage-opslagaccounts kunt u Azure-bestandsshares implementeren op (SSD-)hardware met een premium/solid-state schijf. FileStorage-accounts kunnen alleen worden gebruikt voor het opslaan van Azure-bestandsshares. Er kunnen geen andere opslagresources (blobcontainers, wachtrijen, tabellen enz.) worden geïmplementeerd in een FileStorage-account.

| Kenmerk | GPv2-opslag accounts (standaard) | FileStorage-opslag accounts (Premium) |
|-|-|-|
| Aantal opslag accounts per regio per abonnement | 250 | 250 |
| Maximale capaciteit van opslagaccount | 5 PiB<sup>1</sup> | 100 TiB (ingericht) |
| Maximum aantal bestands shares | Onbeperkt | Onbeperkt: de totale ingerichte grootte van alle shares moet kleiner zijn dan de maximale capaciteit van het opslag account |
| Maximum aantal gelijktijdige aanvragen | 20.000 IOPS<sup>1</sup> | 100.000 IOPS |
| Maximale ingangs | <ul><li>VS/Europa: 10 GBP/Sec<sup>1</sup></li><li>Andere regio's (LRS/ZRS): 10 GBP/Sec<sup>1</sup></li><li>Andere regio's (GRS): 5 GBP/Sec<sup>1</sup></li></ul> | 4.136-MiB per seconde |
| Maximum aantal uitgangen | 50 GBP/Sec<sup>1</sup> | 6.204-MiB per seconde |
| Maximum aantal regels voor virtuele netwerken | 200 | 200 |
| Maximum aantal IP-adres regels | 200 | 200 |
| Lees bewerkingen voor beheer | 800 per 5 minuten | 800 per 5 minuten |
| Schrijf bewerkingen voor beheer | 10 per seconde/1200 per uur | 10 per seconde/1200 per uur |
| Beheer lijst bewerkingen | 100 per 5 minuten | 100 per 5 minuten |

<sup>1</sup> algemene opslag accounts voor versie 2 ondersteunen hogere capaciteits limieten en hogere limieten voor binnenkomend op aanvraag. Neem contact op met de [Azure-ondersteuning](https://azure.microsoft.com/support/faq/) om een hogere accountlimiet aan te vragen.

### <a name="azure-file-share-scale-targets"></a>Schaal doelen voor Azure-bestands shares
| Kenmerk | Standaard bestands shares<sup>1</sup> | Premiumbestandsshares |
|-|-|-|
| Minimale grootte van een bestandsshare | Geen minimum | 100 GiB (ingericht) |
| Eenheid voor verg Roten/verkleinen van ingerichte grootte | N.v.t. | 1 GiB |
| Maximale grootte van een bestandsshare | <ul><li>100 TiB, met de functie voor grote bestands shares is<sup>2</sup> ingeschakeld</li><li>5 TiB, standaard</li></ul> | 100 TiB |
| Maximum aantal bestanden in een bestandsshare | Geen limiet | Geen limiet |
| Maximum aantal aanvragen (maximum aantal IOPS) | <ul><li>10.000, met de functie voor grote bestands shares is<sup>2</sup> ingeschakeld</li><li>1.000 of 100 aanvragen per 100 MS, standaard</li></ul> | <ul><li>Basis lijn IOPS: 400 + 1 IOPS per GiB, Maxi maal 100.000</li><li>IOPS-bursting: Max (4000, 3x IOPS per GiB), Maxi maal 100.000</li></ul> |
| Maximum aantal inkomende waarden voor één bestandsshare | <ul><li>Maxi maal 300 MiB/sec. met de functie voor grote bestands shares is<sup>2</sup> ingeschakeld</li><li>Maxi maal 60 MiB per seconde, standaard</li></ul> | 40-MiB/s + 0,04 * ingerichte GiB |
| Maximum aantal uitgaande waarden voor één bestandsshare | <ul><li>Maxi maal 300 MiB/sec. met de functie voor grote bestands shares is<sup>2</sup> ingeschakeld</li><li>Maxi maal 60 MiB per seconde, standaard</li></ul> | 60-MiB/s + 0,06 * ingerichte GiB |
| Maximum aantal momentopnamen van shares | 200 moment opnamen | 200 moment opnamen |
| Maximum lengte van de naam van het object (mappen en bestanden) | 2048 tekens | 2048 tekens |
| Maximum pathname-onderdeel (in het pad \A\B\C\D is elke letter een onderdeel) | 255 tekens | 255 tekens |
| Limiet voor vaste koppelingen (alleen NFS) | N.v.t. | 178 |
| Maximumaantal SMB Multichannel-kanalen | N.v.t. | 4 |
| Maximaal aantal opgeslagen toegangsbeleidsregels per bestandsshare | 5 | 5 |

<sup>1</sup> de limieten voor standaard bestands shares zijn van toepassing op alle drie de lagen die beschikbaar zijn voor standaard bestands shares: trans actie geoptimaliseerd, warm en cool.

<sup>2</sup> standaard op standaard bestands shares is 5 Tib. Zie [grote bestands shares inschakelen en maken](./storage-files-how-to-create-large-file-share.md) voor meer informatie over het uitbreiden van de standaard bestands shares tot 100 Tib.

### <a name="file-scale-targets"></a>Bestands schaal doelen
| Kenmerk | Bestanden in standaard bestands shares  | Bestanden in Premium-bestands shares  |
|-|-|-|
| Maximale bestandsgrootte | 4 TiB | 4 TiB |
| Maximum aantal gelijktijdige aanvragen | 1.000 IOPS | Maxi maal 8.000<sup>1</sup> |
| Maximum aantal binnenkomend voor een bestand | 60-MiB per seconde | 200 MiB per seconde (Maxi maal 1 GiB/s met SMB meerdere kanalen preview)<sup>2</sup>|
| Maximum aantal uitgangen voor een bestand | 60-MiB per seconde | 300 MiB per seconde (Maxi maal 1 GiB/s met SMB meerdere kanalen preview)<sup>2</sup> |
| Maximum aantal gelijktijdige ingangen | 2.000 ingangen | 2.000 ingangen  |

<sup>1 is van toepassing op het lezen en schrijven van Ios (doorgaans kleinere io-grootten die kleiner zijn dan of gelijk zijn aan 64 KiB). Meta gegevensbewerkingen, behalve Lees-en schrijf bewerkingen, kunnen lager zijn.</sup> 
 <sup>2 onderhevig aan netwerk limieten voor computers, beschik bare band breedte, i/o-grootte, wachtrij diepte en andere factoren. Zie prestaties van [SMB meerdere kanalen](./storage-files-smb-multichannel-performance.md)voor meer informatie.</sup>

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync-schaaldoelen
De volgende tabel geeft de grenzen van de test van micro soft aan en geeft ook aan welke doelen vaste limieten zijn:

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
| Minimale bestandsgrootte om een bestand gelaagd te maken | V9 en nieuwer: Op basis van de grootte van het bestandssysteemcluster (dubbele bestandssysteemclustergrootte). Als de cluster grootte van het bestands systeem bijvoorbeeld 4 KiB is, is de minimale bestands grootte 8 KiB.<br> V8 en ouder: 64 KiB  | Ja |

> [!Note]  
> Een Azure File Sync-eindpunt kan tot de grootte van een Azure-bestandsshare worden opgeschaald. Als de limiet van de Azure-bestandsshare, kan er niet meer worden gesynchroniseerd.

### <a name="azure-file-sync-performance-metrics"></a>Metrische Azure File Sync-gegevens voor prestaties
Omdat de Azure File Sync-agent wordt uitgevoerd op een Windows Server-computer die verbinding maakt met de Azure-bestands shares, is de daad werkelijke synchronisatie prestaties afhankelijk van een aantal factoren in uw infra structuur: Windows Server en de onderliggende schijf configuratie, netwerk bandbreedte tussen de server en de Azure-opslag, de bestands grootte, de totale gegevensset en de activiteit op de gegevensset. Omdat Azure File Sync op bestands niveau werkt, worden de prestatie kenmerken van een oplossing op basis van een Azure File Sync beter gemeten in het aantal objecten (bestanden en mappen) dat per seconde wordt verwerkt.

Voor Azure File Sync zijn de prestaties in twee fasen kritiek:

1. **Eerste** eenmalige inrichting: als u de prestaties van de eerste inrichting wilt optimaliseren, raadpleegt u de voor bereiding op [Azure file sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) voor de optimale implementatie details.
2. **Voortdurende synchronisatie**: nadat de gegevens in eerste instantie zijn geseed in de Azure-bestands shares, Azure File Sync meerdere eind punten in de synchronisatie bewaard.

Om u te helpen bij het plannen van uw implementatie voor elk van de fasen, zijn de resultaten die zijn waargenomen tijdens de interne test op een systeem met een configuratie

| Systeemconfiguratie | Details |
|-|-|
| CPU | 64 virtuele kernen met 64 MiB L3-cache |
| Geheugen | 128 GiB |
| Schijf | SAS-schijven met RAID 10 met cache met batterij back-ups |
| Netwerk | 1 Gbps-netwerk |
| Workload | Algemeen Bestands server|

| Eerste eenmalige inrichting  | Details |
|-|-|
| Aantal objecten | 25.000.000-objecten |
| Grootte van gegevensset| ~ 4,7 TiB |
| Gemiddelde bestands grootte | ~ 200 KiB (grootste bestand: 100 GiB) |
| Initiële inventarisatie van wijzigingen in de Cloud | 20 objecten per seconde  |
| Upload doorvoer | 20 objecten per seconde per synchronisatie groep |
| Door Voer van naam ruimte downloaden | 400 objecten per seconde |

### <a name="initial-one-time-provisioning"></a>Eerste eenmalige inrichting
**Initiële inventarisatie van de Cloud wijziging**: wanneer er een nieuwe synchronisatie groep wordt gemaakt, is de eerste stap in de inventarisatie voor het wijzigen van de Cloud. In dit proces worden alle items in de Azure-bestands share geïnventariseerd. Tijdens dit proces zijn er geen synchronisatie-activiteiten, dat wil zeggen dat er geen items worden gedownload van het Cloud-eind punt naar het server eindpunt en dat er geen items worden geüpload van het server eindpunt naar het eind punt in de Cloud. De synchronisatie activiteit wordt hervat zodra de oorspronkelijke inventarisatie van wijzigingen in de Cloud is voltooid.
De snelheid van de prestaties is 20 objecten per seconde. Klanten kunnen een schatting maken van de tijd die nodig is om de initiële inventarisatie van de Cloud wijziging te volt ooien door het aantal items in de Cloud share te bepalen en de volgende formule te gebruiken om de tijd in dagen te berekenen. 

   **Tijd (in dagen) voor initiële Cloud inventarisatie = (aantal objecten in het Cloud eindpunt)/(20 * 60 * 60 * 24)**

**Door Voer van naam ruimte downloaden** Wanneer een nieuw server eindpunt wordt toegevoegd aan een bestaande synchronisatie groep, downloadt de Azure File Sync-agent geen bestands inhoud van het eind punt in de Cloud. Eerst wordt de volledige naam ruimte gesynchroniseerd en vervolgens wordt de achtergrond terugroepen geactiveerd om de bestanden te downloaden, in hun geheel of, als Cloud lagen zijn ingeschakeld, naar het beleid voor Cloud lagen dat is ingesteld op het server eindpunt.

| Voortdurende synchronisatie  | Details  |
|-|--|
| Aantal gesynchroniseerde objecten| 125.000-objecten (~ 1% verloop) |
| Grootte van gegevensset| 50 GiB |
| Gemiddelde bestands grootte | ~ 500 KiB |
| Upload doorvoer | 20 objecten per seconde per synchronisatie groep |
| Volledige down load door Voer * | 60 objecten per seconde |

* Als Cloud lagen zijn ingeschakeld, ziet u waarschijnlijk betere prestaties omdat slechts een deel van de bestands gegevens worden gedownload. Met Azure File Sync worden alleen de gegevens van bestanden in de cache gedownload wanneer deze op een van de eind punten worden gewijzigd. Voor alle gelaagde of zojuist gemaakte bestanden worden de bestands gegevens niet gedownload door de agent en wordt de naam ruimte in plaats daarvan gesynchroniseerd met alle server eindpunten. De agent biedt ook ondersteuning voor gedeeltelijke down loads van gelaagde bestanden, aangezien deze door de gebruiker worden geopend. 

> [!Note]  
> De bovenstaande getallen zijn geen indicatie van de prestaties die u zult ervaren. De daad werkelijke prestaties zijn afhankelijk van meerdere factoren die aan het begin van deze sectie worden beschreven.

Als algemene hand leiding voor uw implementatie moet u een aantal zaken in acht nemen:

- De object doorvoer wordt ongeveer proportioneel geschaald naar rato van het aantal synchronisatie groepen op de server. Het splitsen van gegevens in meerdere synchronisatie groepen op een server levert een betere door Voer, die ook wordt beperkt door de server en het netwerk.
- De object doorvoer is omgekeerd evenredig met de MiB per tweede door voer. Voor kleinere bestanden krijgt u een hogere door Voer in termen van het aantal verwerkte objecten per seconde, maar lagere MiB per seconde door voer. Voor grotere bestanden krijgt u echter minder objecten die per seconde worden verwerkt, maar een hogere MiB per seconde door voer. De MiB per tweede door Voer wordt beperkt door de Azure Files schaal doelen.

## <a name="see-also"></a>Zie ook
- [Een Azure Files-implementatie plannen](storage-files-planning.md)
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)