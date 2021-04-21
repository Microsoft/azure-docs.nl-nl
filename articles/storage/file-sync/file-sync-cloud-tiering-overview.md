---
title: Inzicht Azure File Sync opslaglagen in cloudlagen | Microsoft Docs
description: Inzicht in cloudopslaglagen, een optionele Azure File Sync functie. Veelgebruikte bestanden worden lokaal in de cache opgeslagen op de server; andere zijn gelaagd voor Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1f58aa4e2aa4637dfb9fc8b29c52209975e3e367
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797053"
---
# <a name="cloud-tiering-overview"></a>Overzicht van cloudopslaglagen
Cloudopslaglagen, een optionele functie van Azure File Sync, vermindert de hoeveelheid lokale opslag die vereist is, terwijl de prestaties van een on-premises bestandsserver behouden blijven.

Wanneer deze functie is ingeschakeld, worden alleen vaak gebruikte (hot) bestanden op uw lokale server opgeslagen. Niet vaak gebruikt (cool) bestanden worden gesplitst in naamruimte (bestands- en mapstructuur) en bestandsinhoud. De naamruimte wordt lokaal opgeslagen en de bestandsinhoud wordt opgeslagen in een Azure-bestands share in de cloud. 

Wanneer een gebruiker een gelaagd bestand opent, Azure File Sync de bestandsgegevens uit de bestands share in Azure naadloos terughalen.

## <a name="how-cloud-tiering-works"></a>Hoe cloudopslaglagen werken

### <a name="cloud-tiering-policies"></a>Beleidsregels voor cloudopslaglagen
Wanneer u opslag in cloudlagen inschakelen, zijn er twee beleidsregels die u kunt instellen om Azure File Sync te informeren wanneer cool-bestanden in lagen moeten worden opgeslagen: het beleid voor vrije ruimte op het **volume** en het **datumbeleid**. 

#### <a name="volume-free-space-policy"></a>Beleid voor vrije ruimte op volume
Het **beleid voor vrije** ruimte op het volume vertelt Azure File Sync om cool-bestanden in de cloud op te slaan wanneer een bepaalde hoeveelheid ruimte op uw lokale schijf wordt ingenomen. 

Als de capaciteit van uw lokale schijf bijvoorbeeld 200 GB is en u wilt dat ten minste 40 GB van de lokale schijfcapaciteit altijd vrij blijft, moet u het volumebeleid voor vrije ruimte instellen op 20%. Volume vrije ruimte is van toepassing op volumeniveau in plaats van op het niveau van afzonderlijke directories of server-eindpunten. 

Zie de sectie Synchronisatiebeleidsregels die invloed hebben op opslag in [cloudlagen)](#sync-policies-that-affect-cloud-tiering)voor meer informatie over de invloed van het beleid voor vrije ruimte op het volume dat in eerste instantie wordt gedownload wanneer een nieuw server-eindpunt wordt toegevoegd.

#### <a name="date-policy"></a>Datumbeleid
Met het **datumbeleid** worden cool-bestanden in een laag in de cloud opgeslagen als ze een aantal dagen niet zijn gebruikt (dat wil zeggen, lezen of weggeschreven). Als u bijvoorbeeld hebt opgemerkt dat bestanden die langer dan 15 dagen zijn verwijderd zonder te worden gebruikt, doorgaans archiveringsbestanden zijn, moet u uw datumbeleid instellen op 15 dagen. 

Zie Choose Azure File Sync cloud tiering policies (Beleid voor opslag in cloudlagen kiezen) voor meer voorbeelden van hoe het datumbeleid en het beleid voor vrije ruimte [voor volumes samenwerken.](file-sync-choose-cloud-tiering-policies.md)

### <a name="windows-server-data-deduplication"></a>Windows Server-gegevens ontdubbeling
Gegevensverdubbeling wordt ondersteund op volumes met cloudopslaglagen die vanaf Windows Server 2016 zijn ingeschakeld. Zie Planning for [an Azure File Sync deployment (Planning voor een Azure File Sync implementatie) voor meer informatie.](file-sync-planning.md#data-deduplication)

### <a name="cloud-tiering-heatmap"></a>Heatmap voor opslag in cloudlagen
Azure File Sync bewaakt gedurende een periode de toegang tot bestanden (lees- en schrijfbewerkingen) en wijst, op basis van hoe vaak en recent de toegang is, een heatscore toe aan elk bestand. Deze scores worden gebruikt om een 'heatmap' van uw naamruimte op elk server-eindpunt te maken. Deze heatmap is een lijst met alle synchronisatiebestanden op een locatie waar opslag in cloudlagen is ingeschakeld, geordend op hun heatscore. Veelgebruikte bestanden die onlangs zijn geopend, worden als 'hot' beschouwd, terwijl bestanden die onwel waren en al enige tijd niet zijn geopend, als 'cool' worden beschouwd. 

Om de relatieve positie van een afzonderlijk bestand in die heatmap te bepalen, gebruikt het systeem het maximum van de tijdstempels in de volgende volgorde: MAX(Last Access Time, Last Modified Time, Creation Time). 

Normaal gesproken wordt de laatste toegangstijd bijgespoord en beschikbaar. Wanneer er echter een nieuw server-eindpunt wordt gemaakt en opslag in cloudlagen is ingeschakeld, is er onvoldoende tijd verstreken om bestandstoegang te observeren. Als er geen geldige laatste toegangstijd is, wordt in plaats daarvan de laatste wijzigingstijd gebruikt om de relatieve positie in de heatmap te evalueren.  

Het datumbeleid werkt op dezelfde manier. Zonder de laatste toegangstijd zal het datumbeleid reageren op de laatste wijzigingstijd. Als dit niet beschikbaar is, wordt terugvallen op de aan te maken tijd van een bestand. Na een periode ziet het systeem steeds meer aanvragen voor bestandstoegang en wordt automatisch gebruik gemaakt van de laatste toegangstijd die automatisch wordt bijgespoord.

> [!Note]
> Cloudopslaglagen zijn niet afhankelijk van de NTFS-functie voor het bijhouden van de laatste toegangstijd. Deze NTFS-functie is standaard uitgeschakeld en vanwege prestatieoverwegingen wordt u aangeraden deze functie niet handmatig in te schakelen. Opslag in cloudlagen houdt de laatste toegangstijd afzonderlijk bij.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Synchronisatiebeleidsregels die invloed hebben op opslag in cloudlagen

Met Azure File Sync agent versie 11 zijn er twee extra Azure File Sync-beleidsregels die van invloed zijn op opslag in cloudlagen: eerste **download** en proactieve **terugroepen.**

#### <a name="initial-download"></a>Eerste download

Wanneer een server verbinding maakt met een Azure-bestands share met bestanden in de share, kunt u nu bepalen hoe u wilt dat de server de gegevens van de bestands share in eerste instantie downloadt. Wanneer opslag in cloudlagen is ingeschakeld, hebt u twee opties. 

![Een schermopname van de eerste download wanneer opslag in cloudlagen is ingeschakeld](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

De eerste optie is om eerst de naamruimte in te roepen en vervolgens de bestandsinhoud terug te roepen op de tijdstempel laatst gewijzigd, totdat de capaciteit van de lokale schijf is bereikt. Als u voldoende schijfruimte hebt en u weet dat bestanden die het laatst zijn gewijzigd lokaal in de cache moeten worden opgeslagen, is deze optie ideaal. Bestanden die niet lokaal kunnen worden opgeslagen vanwege een gebrek aan schijfruimte, worden gelaagd.        

De tweede optie is om in eerste instantie alleen de naamruimte in te roepen en de bestandsinhoud alleen terug te roepen wanneer deze wordt gebruikt. Deze optie is het beste als u de capaciteit die op uw lokale schijf wordt gebruikt wilt minimaliseren en gebruikers wilt laten bepalen welke bestanden lokaal in de cache moeten worden opgeslagen. Alle bestanden beginnen als gelaagde bestanden met deze optie.

![Een schermopname van de eerste download wanneer opslag in cloudlagen is uitgeschakeld](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Wanneer opslag in cloudlagen is uitgeschakeld, hebt u een derde optie, die bestaat uit het voorkomen van gelaagde bestanden. In dit geval worden bestanden pas weergegeven op de server wanneer ze volledig zijn gedownload. Als u toepassingen hebt waarvoor volledige bestanden aanwezig moeten zijn en gelaagde bestanden in de naamruimte niet kunnen tolereren, is dit ideaal.      

#### <a name="proactive-recalling"></a>Proactief terughalen

Wanneer een bestand wordt gemaakt of gewijzigd, kunt u proactief een bestand terugroepen naar servers die u opgeeft. Hierdoor is het nieuwe of gewijzigde bestand direct beschikbaar voor gebruik op elke opgegeven server. 

Een wereldwijd gedistribueerd bedrijf heeft bijvoorbeeld filialen in de VERENIGDE Staten en India. Informatiemedewerkers maken 's ochtends (Us time) een nieuwe map en nieuwe bestanden voor een geheel nieuw project en werken er de hele dag aan. Azure File Sync synchroniseert de map en bestanden naar de Azure-bestands share (cloud-eindpunt). Informatiemedewerkers in India blijven in hun tijdzone aan het project werken. Wanneer ze 's ochtends aankomt, moet de lokale Azure File Sync-server in India deze nieuwe bestanden lokaal beschikbaar hebben, zodat het Team van India efficiënt kan werken met een lokale cache. Als u deze modus inschakelen, wordt voorkomen dat de initiële toegang tot bestanden langzamer verloopt vanwege terugroepen op aanvraag en kan de server de bestanden proactief terugroepen zodra ze zijn gewijzigd of gemaakt in de Azure-bestands share.

Als bestanden die naar de server worden teruggeroepen, niet daadwerkelijk lokaal nodig zijn, kan de onnodige terugroepactie uw verkeer en kosten voor uit te gaan verhogen. Schakel daarom alleen proactief terughalen in wanneer u weet dat het vooraf vullen van de cache van een server met recente wijzigingen vanuit de cloud een positief effect heeft op gebruikers of toepassingen die de bestanden op die server gebruiken. 

Het inschakelen van proactief inroepen kan ook leiden tot een verhoogd bandbreedtegebruik op de server en kan ertoe leiden dat andere relatief nieuwe inhoud op de lokale server agressief in lagen wordt opgeslagen als gevolg van de toename van het aantal bestanden dat wordt ingeroepen. Dit kan op zijn beurt leiden tot meer terugroepen als de bestanden die in lagen worden opgeslagen, door servers als hot worden beschouwd. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Een afbeelding van het downloadgedrag van de Azure-bestands share voor een server-eindpunt dat momenteel van kracht is en een knop om een menu te openen waarmee dit kan worden gewijzigd.":::

Zie Deploy Azure File Sync voor meer informatie [over proactieve Azure File Sync.](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share)

## <a name="tiered-vs-locally-cached-file-behavior"></a>Gedrag van gelaagd versus lokaal in cache opgeslagen bestanden

Opslag in cloudlagen is de scheiding tussen de naamruimte (de bestands- en maphiërarchie, evenals de bestandseigenschappen) en de bestandsinhoud. 

#### <a name="tiered-file"></a>Gelaagd bestand

Voor gelaagde bestanden is de grootte op schijf nul omdat de bestandsinhoud zelf niet lokaal wordt opgeslagen. Wanneer een bestand gelaagd is, vervangt Azure File Sync bestandssysteemfilter (StorageSync.sys) het bestand lokaal door een aanwijzer (reparsepunt). Het reparsepunt vertegenwoordigt een URL naar het bestand in de Azure-bestands share. Een gelaagd bestand heeft zowel het kenmerk offline als het kenmerk FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ingesteld in NTFS, zodat toepassingen van derden gelaagde bestanden veilig kunnen identificeren.   

![Een schermopname van de eigenschappen van een bestand wanneer het gelaagd is: alleen naamruimte.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Lokaal in cache opgeslagen bestand

Aan de andere kant is de grootte van een bestand dat is opgeslagen op een on-premises bestandsserver ongeveer gelijk aan de logische grootte van het bestand omdat het hele bestand (bestandskenmerken + bestandsinhoud) lokaal wordt opgeslagen.     

![Een schermopname van de eigenschappen van een bestand wanneer het niet gelaagd is: naamruimte + bestandsinhoud.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Het is ook mogelijk dat een bestand gedeeltelijk in lagen wordt opgeslagen (of gedeeltelijk wordt ingeroepen). In een gedeeltelijk gelaagd bestand is een deel van het bestand op schijf. Dit kan gebeuren wanneer bestanden gedeeltelijk worden gelezen door toepassingen zoals multimedia spelers of zip-hulpprogramma's die ondersteuning bieden voor streamingtoegang tot bestanden. Azure File Sync is slim en haalt alleen de aangevraagde informatie op uit de verbonden Azure-bestands share.

> [!NOTE]
> Grootte vertegenwoordigt de logische grootte van het bestand. Grootte op schijf vertegenwoordigt de fysieke grootte van de bestandsstroom die is opgeslagen op de schijf.

## <a name="next-steps"></a>Volgende stappen

* [Beleidsregels Azure File Sync cloudlagen kiezen](file-sync-choose-cloud-tiering-policies.md)
* [Planning voor een Azure Files Sync-implementatie](file-sync-planning.md)