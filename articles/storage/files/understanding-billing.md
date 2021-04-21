---
title: Inzicht Azure Files | Microsoft Docs
description: Meer informatie over het interpreteren van de inrichtende en betalen per gebruik-factureringsmodellen voor Azure-bestands shares.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11d22fd83106bb1802514d0c7d5f67724664464d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788381"
---
# <a name="understand-azure-files-billing"></a>Inzicht in Azure Files facturering
Azure Files biedt twee verschillende factureringsmodellen: ingericht en betalen per gebruikt. Het inrichtende model is alleen beschikbaar voor Premium-bestands shares. Dit zijn bestands shares die zijn geïmplementeerd in het type **FileStorage-opslagaccount.** Het model betalen per gebruik is alleen beschikbaar voor standaardbestands shares. Dit zijn bestands shares die zijn geïmplementeerd in het opslagaccount van het type algemeen gebruik versie **2 (GPv2).** In dit artikel wordt uitgelegd hoe beide modellen werken om u inzicht te geven in uw maandelijkse Azure Files factuur.

Zie Azure Files pagina met prijzen voor [Azure Files informatie over prijzen.](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="storage-units"></a>Opslageenheden    
Azure Files base-2 meeteenheden gebruikt om de opslagcapaciteit aan te geven: KiB, MiB, GiB en TiB. Uw besturingssysteem kan al dan niet dezelfde maateenheid of telsysteem gebruiken.

### <a name="windows"></a>Windows

Zowel het Windows-besturingssysteem als Azure Files meten de opslagcapaciteit met behulp van het base-2-telsysteem, maar er is een verschil bij het labelen van eenheden. Azure Files worden de opslagcapaciteit gelabeld met base-2 maateenheden, terwijl Windows de opslagcapaciteit in basis-10 meeteenheden labelt. Bij het rapporteren van opslagcapaciteit converteert Windows de opslagcapaciteit niet van base-2 naar base-10.

|Acroniem  |Definitie  |Eenheid  |Windows wordt weergegeven als  |
|---------|---------|---------|---------|
|KiB     |1024 bytes         |kibibyte         |KB (kilobyte)         |
|Mib     |1024 KiB (1.048.576 bytes)         |mebibyte         |MB (megabyte)         |
|Gib     |1024 MiB (1.073.741.824 bytes)         |gibibyte         |GB (gigabyte)         |
|Tib     |1024 GiB (1.099.511.627.776 bytes)         |tebibyte         |TB (terabyte)         |

### <a name="macos"></a>macOS

Zie [Hoe iOS en macOS opslagcapaciteit rapporteren](https://support.apple.com/HT201402) op de website van Apple om te bepalen welk telsysteem wordt gebruikt.

### <a name="linux"></a>Linux

Een ander telsysteem kan worden gebruikt door elk besturingssysteem of afzonderlijke software. Zie de documentatie om te bepalen hoe ze opslagcapaciteit rapporteren.

## <a name="provisioned-model"></a>Ingericht model
Azure Files maakt gebruik van een ingericht model voor Premium-bestands shares. In een ingericht bedrijfsmodel geeft u proactief aan de Azure Files-service op wat uw opslagvereisten zijn, in plaats van te worden gefactureerd op basis van wat u gebruikt. Dit is vergelijkbaar met het on-premises kopen van hardware. Wanneer u een Azure-bestands share inrichten met een bepaalde hoeveelheid opslag, betaalt u voor die opslag ongeacht of u deze gebruikt of niet, net zoals u de kosten van fysieke media on-premises niet begint te betalen wanneer u ruimte gaat gebruiken. In tegenstelling tot het kopen van fysieke media on-premises, kunnen inrichtende bestands shares dynamisch omhoog of omlaag worden geschaald, afhankelijk van de prestatiekenmerken van uw opslag en I/O.

Wanneer u een Premium-bestands share inrichten, geeft u op hoeveel GiBs uw workload vereist. Elke GiB die u inrichten, geeft u de juiste waarde voor extra IOPS en doorvoer in een vaste verhouding. Naast de basislijn-IOPS waarvoor u gegarandeerd bent, ondersteunt elke Premium-bestands share bursting op basis van best effort. De formules voor IOPS en doorvoer zijn als volgt:

- Basislijn-IOPS = 400 + 1 * inrichtende GiB. (Maximaal 100.000 IOPS).
- Burst Limit = MAX(4000, 3 * Baseline IOPS).
- Egress rate = 60 MiB/s + 0,06 * provisioned GiB.
- Ingress rate = 40 MiB/s + 0,04 * provisioned GiB.

De inrichtende grootte van de bestands share kan op elk moment worden verhoogd, maar kan pas na 24 uur worden verlaagd sinds de laatste toename. Nadat u 24 uur hebt gewacht zonder een quotumverhoging, kunt u het quotum voor delen zo vaak als u wilt verlagen totdat u het opnieuw verhoogt. Wijzigingen in de IOPS-/doorvoerschaal zijn binnen enkele minuten na de inrichtende groottewijziging van kracht.

Het is mogelijk om de grootte van uw inrichtende share te verlagen onder uw gebruikte GiB. Als u dit doet, verliest u geen gegevens, maar wordt u wel gefactureerd voor de gebruikte grootte en ontvangt u de prestaties (basislijn-IOPS, doorvoer en burst-IOPS) van de inrichtende share, niet de gebruikte grootte.

In de volgende tabel ziet u enkele voorbeelden van deze formules voor de inrichtende sharegrootten:

|Capaciteit (GiB) | Basislijn-IOPS | Burst-IOPS | Egress (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Maximaal 4000     | 66   | 44   |
|500         | 900     | Maximaal 4000  | 90   | 60   |
|1\.024       | 1,424   | Maximaal 4000   | 122   | 81   |
|5,120       | 5,520   | Maximaal 15.360  | 368   | 245   |
|10,240      | 10,640  | Maximaal 30.720  | 675   | 450   |
|33,792      | 34,192  | Maximaal 100.000 | 2,088 | 1,392   |
|51,200      | 51,600  | Maximaal 100.000 | 3,132 | 2,088   |
|102,400     | 100.000 | Maximaal 100.000 | 6,204 | 4,136   |

Effectieve prestaties van bestands delen zijn onderhevig aan netwerklimieten van de computer, beschikbare netwerkbandbreedte, I/O-grootten, parallelle uitvoering, en vele andere factoren. Bijvoorbeeld, op basis van interne tests met 8 KiB IO-grootten voor lezen/schrijven, kan één virtuele Windows-machine zonder SMB meerdere kanalen, *Standard F16s_v2,* die is verbonden met een Premium-bestands share via SMB, 20.000 lees-IOPS en 15.000 schrijf-IOPS bereiken. Met IO-grootten voor lezen/schrijven van 512 MiB kan dezelfde VM 1,1 GiB/s-egress en 370 MiB/s ingress-doorvoer bereiken. Dezelfde client kan maximaal \~ 3x prestaties bereiken als SMB meerdere kanalen is ingeschakeld op de Premium-shares. Als u een maximale schaal voor prestaties wilt bereiken, [moet u SMB meerdere kanalen](storage-files-enable-smb-multichannel.md) inschakelen en de belasting over meerdere VM's spreiden. Raadpleeg de [SMB-handleiding voor meerdere kanalen voor](storage-files-smb-multichannel-performance.md) prestaties en [probleemoplossing](storage-troubleshooting-files-performance.md) voor enkele veelvoorkomende prestatieproblemen en tijdelijke oplossingen.

### <a name="bursting"></a>Barsten
Als uw workload de extra prestaties nodig heeft om te voldoen aan de piekvraag, kan uw share burst-tegoeden gebruiken om boven de IOPS-limiet voor de sharebasislijn te gaan om de prestaties te kunnen delen die nodig zijn om aan de vraag te voldoen. Premium-bestands shares kunnen hun IOPS maximaal 4000 of tot een factor drie bursten, wat een hogere waarde is. Bursting is geautomatiseerd en werkt op basis van een tegoedsysteem. Bursting werkt op basis van best effort en de burst-limiet is geen garantie. Bestands shares kunnen maximaal 60 minuten maximaal tot de limiet worden ge bursted. 

Tegoeden worden verzameld in een burst-bucket wanneer het verkeer voor uw bestands share lager is dan de basislijn-IOPS. Een 100 GiB-share heeft bijvoorbeeld 500 basislijn-IOPS. Als het werkelijke verkeer op de share 100 IOPS was voor een specifiek interval van 1 seconde, worden de 400 ongebruikte IOPS gecrediteerd naar een burst-bucket. Op dezelfde manier krijgt een niet-actieve share van 1 TiB een burst-tegoed van 1424 IOPS. Deze tegoeden worden later gebruikt wanneer bewerkingen de basislijn-IOPS overschrijden.

Wanneer een share de basislijn-IOPS overschrijdt en tegoeden heeft in een burst-bucket, wordt de maximale toegestane pieksnelheid bereikt. Shares kunnen blijven bursten zolang er tegoeden resterend zijn, tot maximaal 60 minuten, maar dit is gebaseerd op het aantal opgebouwde burst-tegoeden. Elke I/O buiten de basislijn-IOPS verbruikt één tegoed en zodra alle tegoeden zijn verbruikt, keert de share terug naar de basislijn-IOPS.

Tegoeden voor delen hebben drie staten:

- Toe-eigenend, wanneer de bestands share minder dan de basislijn-IOPS gebruikt.
- Afnemend, wanneer de bestands share meer gebruikt dan de basislijn-IOPS en in de burst-modus.
- Constant: wanneer de bestands share precies de basislijn-IOPS gebruikt, worden er geen tegoed bij elkaar opgeteld of gebruikt.

Nieuwe bestands shares beginnen met het volledige aantal tegoeden in de burst-bucket. Burst-tegoeden worden niet bij elkaar opgeteld als de share-IOPS onder de basislijn-IOPS valt vanwege beperkingen door de server.

## <a name="pay-as-you-go-model"></a>Model voor betalen per gebruikt
Azure Files maakt gebruik van een betalen per gebruik-bedrijfsmodel voor standaardbestands shares. In een bedrijfsmodel met betalen per gebruik wordt het bedrag dat u betaalt bepaald door hoeveel u daadwerkelijk gebruikt, in plaats van op basis van een ingericht bedrag. Op hoog niveau betaalt u kosten voor de hoeveelheid gegevens die op schijf zijn opgeslagen en vervolgens een extra set transacties op basis van uw gebruik van die gegevens. Een model voor betalen per gebruik kan kostenefficiënt zijn, omdat u zich niet te veel hoeft in te delen om rekening te houden met toekomstige groei- of prestatievereisten of om deprovision op te schorten als de gegevensvoetafdruk van uw workload na een periode varieert. Aan de andere kant kan een model voor betalen per gebruik ook lastig te plannen zijn als onderdeel van een budgeteringsproces, omdat het factureringsmodel voor betalen per gebruik wordt aangestuurd door het verbruik van eindgebruikers.

### <a name="differences-in-standard-tiers"></a>Verschillen in standard-lagen
Wanneer u een standaardbestands share maakt, kiest u tussen de voor transacties geoptimaliseerde, hot- en cool-lagen. Alle drie de lagen worden opgeslagen op exact dezelfde standaardopslaghardware. Het belangrijkste verschil voor deze drie lagen is de opslagprijs voor gegevens at-rest, die lager zijn in statische lagen, en de transactieprijzen, die hoger zijn in statische lagen. Dit betekent:

- Voor transactie geoptimaliseerd betekent, zoals de naam aangeeft, de prijs voor werkbelastingen met hoge transacties. Voor transactie geoptimaliseerd heeft de hoogste opslagprijs voor gegevens at-rest, maar de laagste transactieprijs.
- Dynamisch is voor actieve workloads waarvoor geen grote aantallen transacties nodig zijn en heeft een iets lagere opslagprijs voor gegevens at-rest, maar iets hogere transactieprijzen in vergelijking met voor transactie geoptimaliseerd. U kunt het zien als de middelste basis tussen de voor transacties geoptimaliseerde en de cool-laag.
- Cool optimaliseert de prijs voor workloads die niet veel activiteit hebben, met de laagste opslagprijs voor data-at-rest, maar de hoogste transactieprijzen.

Als u een niet vaak geopende workload in de voor transacties geoptimaliseerde laag zet, betaalt u bijna niets voor de paar keer in een maand dat u transacties op uw share maakt, maar betaalt u een hoog bedrag voor de kosten voor gegevensopslag. Als u dezelfde share naar de cool-laag zou verplaatsen, zou u nog steeds bijna niets betalen voor de transactiekosten, simpelweg omdat u heel weinig transacties voor deze workload maakt, maar de cool-laag een veel goedkopere prijs voor gegevensopslag heeft. Als u de juiste laag voor uw use-case selecteert, kunt u uw kosten aanzienlijk verlagen. Als u de juiste laag voor uw use-case selecteert, kunt u uw kosten aanzienlijk verlagen.

En als u een workload met hoge toegang in de cool-laag zet, betaalt u veel meer aan transactiekosten, maar minder voor de kosten voor gegevensopslag. Dit kan leiden tot een situatie waarin de toegenomen kosten van de transactieprijzen opwegen tegen de besparingen van de verlaagde prijs voor gegevensopslag, waardoor u meer betaalt voor 'cool' dan wanneer u de transactie zou optimaliseren. Het is mogelijk dat voor sommige gebruiksniveaus de hot-laag de meest kostenefficiënte laag is, maar dat de cool-laag duurder is dan de geoptimaliseerde transactielaag.

Uw workload- en activiteitsniveau bepalen de meest rendabele laag voor uw standaard bestandsshare. In de praktijk is het bepalen van het werkelijke resourceverbruik van de share (opgeslagen gegevens, schrijftransacties, enzovoort) de beste manier om de meest rendabele laag te kiezen.

### <a name="what-are-transactions"></a>Wat zijn transacties?
Transacties zijn bewerkingen of aanvragen voor Azure Files voor het uploaden, downloaden of anderszins bewerken van de inhoud van de bestands share. Elke actie die wordt ondernomen op een bestands share, wordt omgezet in een of meer transacties, en op standaard-shares die gebruikmaken van het factureringsmodel voor betalen per gebruik, dat wordt omgezet in transactiekosten.

Er zijn vijf basistransactiecategorieën: schrijven, lijst, lezen, overige en verwijderen. Alle bewerkingen die via de REST API of SMB worden uitgevoerd, worden als volgt in een van deze vier categorieën in buckets geplaatst:

| Het type bewerking | Transacties schrijven | Transacties op een lijst zetten | Transacties lezen | Andere transacties | Transacties verwijderen |
|-|-|-|-|-|-|
| Beheerbewerkingen | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Gegevensbewerkingen | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4.1 is alleen beschikbaar voor Premium-bestands shares, die gebruikmaken van het inrichtende factureringsmodel. Transacties hebben geen invloed op de facturering voor Premium-bestands shares.

## <a name="see-also"></a>Zie ook
- [Azure Files pagina met prijzen.](https://azure.microsoft.com/pricing/details/storage/files/)
- [Planning voor een Azure Files implementatie en](storage-files-planning.md) Planning voor een Azure File Sync [implementatie](../file-sync/file-sync-planning.md).
- [Maak een bestands share](storage-how-to-create-file-share.md) en [implementeer Azure File Sync](../file-sync/file-sync-deployment-guide.md).