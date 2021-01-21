---
title: Wat is Azure Files facturering? Microsoft Docs
description: Meer informatie over hoe u de ingerichte en betalen per gebruik-facturerings modellen voor Azure-bestands shares kunt interpreteren.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/20/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 19ecbea70d9cb6b8cc31c72ed3c1294cd137ce93
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632475"
---
# <a name="understanding-azure-files-billing"></a>Wat is Azure Files facturering?
Azure Files biedt twee verschillende facturerings modellen: ingericht en betalen naar gebruik. Het ingerichte model is alleen beschikbaar voor Premium-bestands shares, de bestands shares worden geïmplementeerd in het type opslag account **FileStorage** . Het betalen naar gebruik-model is alleen beschikbaar voor standaard bestands shares. Dit zijn bestands shares geïmplementeerd in het type **GPv2 (General version 2)-** opslag account. In dit artikel wordt uitgelegd hoe beide modellen werken om u te helpen uw maandelijkse Azure Files factuur te begrijpen.

U kunt de huidige prijzen voor Azure Files vinden op de [pagina met Azure files prijzen](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="provisioned-model"></a>Ingericht model
Azure Files gebruikt een ingericht model voor Premium-bestands shares. In een ingericht bedrijfs model geeft u proactief op voor de Azure Files-service wat uw opslag vereisten zijn, in plaats van te worden gefactureerd op basis van wat u gebruikt. Dit is vergelijkbaar met het kopen van hardware on-premises, in dat geval bij het inrichten van een Azure-bestands share met een bepaalde hoeveelheid opslag ruimte, u betaalt voor die opslag, ongeacht of u deze gebruikt of niet, net zoals u niet begint met het betalen van de kosten van fysieke media op locatie wanneer u ruimte gebruikt. In tegens telling tot aanschaf van fysieke media op locatie, kunnen ingerichte bestands shares dynamisch worden uitgebreid of omlaag worden geschaald, afhankelijk van uw opslag-en i/o-prestatie kenmerken.

Wanneer u een Premium-bestands share inricht, geeft u op hoeveel GiBs uw werk belasting vereist. Elke GiB die u inricht, geeft u recht op extra IOPS en door Voer op een vaste verhouding. Naast de basis lijn-IOPS waarvoor u gegarandeerd bent, ondersteunt elke Premium-bestands share bursting op basis van beste inspanningen. De formules voor IOPS en door Voer zijn als volgt:

- Basis lijn IOPS = 400 + 1 * ingerichte GiB. (Maxi maal 100.000 IOPS).
- Burst-limiet = MAX (4000, 3 * Baseline IOPS).
- Uitgangs bedrag = 60 MiB/s + 0,06 * ingerichte GiB.
- Ingangs rente = 40 MiB/s + 0,04 * ingerichte GiB.

De ingerichte grootte van de bestands share kan op elk moment worden verhoogd, maar kan pas na 24 uur na de laatste toename worden verlaagd. Nadat u 24 uur hebt gewacht zonder een quota verhoging, kunt u het share quotum zo vaak als u wilt verlagen totdat u het opnieuw verhoogt. Wijzigingen in IOPS/doorvoer schaal worden binnen een paar minuten na de ingerichte grootte gewijzigd.

Het is mogelijk om de grootte van uw ingerichte share onder uw gebruikte GiB te verkleinen. Als u dit doet, verliest u geen gegevens, maar worden er nog steeds kosten in rekening gebracht voor de gebruikte grootte en worden de prestaties (de basis-IOPS, door Voer en burst IOPS) van de ingerichte share ontvangen, niet de gebruikte grootte.

In de volgende tabel ziet u enkele voor beelden van deze formules voor de ingerichte grootte van de shares:

|Capaciteit (GiB) | IOPS basis lijn | Burst IOPS | Uitgang (MiB/s) | Ingangs (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Maximaal 4000     | 66   | 44   |
|500         | 900     | Maximaal 4000  | 90   | 60   |
|1\.024       | 1.424   | Maximaal 4000   | 122   | 81   |
|5.120       | 5.520   | Maxi maal 15.360  | 368   | 245   |
|10.240      | 10.640  | Maxi maal 30.720  | 675   | 450   |
|33.792      | 34.192  | Maxi maal 100.000 | 2.088 | 1.392   |
|51.200      | 51.600  | Maxi maal 100.000 | 3.132 | 2.088   |
|102.400     | 100.000 | Maxi maal 100.000 | 6.204 | 4.136   |

Doel matige prestaties van bestands shares zijn afhankelijk van de netwerk limieten van de computer, beschik bare netwerk bandbreedte, i/o-grootte, parallelle uitvoering, onder vele andere factoren. Bijvoorbeeld, op basis van een interne test met 8 KiB i/o-schrijf grootten, kan één virtuele Windows-machine zonder SMB meerdere kanalen, *standaard F16s_v2*, die is verbonden met Premium file share via SMB, 20.000 Read IOPS en een Maxi maal 15.000 schrijf-IOPS behaalt. Met 512 MiB lees/schrijf-i/o-groottes kan dezelfde virtuele machine 1,1 GiB/s uitgaand worden gemaakt en 370 MiB/s door voer. Dezelfde client kan tot \~ 3x prestaties leiden als SMB meerdere kanalen is ingeschakeld op de Premium-shares. Als u maximale prestaties wilt schalen, [schakelt u SMB meerdere kanalen](storage-files-enable-smb-multichannel.md) in en verspreidt u de belasting over verschillende vm's. Raadpleeg de hand leiding voor het [oplossen van problemen](storage-troubleshooting-files-performance.md) met [SMB meerdere kanalen](storage-files-smb-multichannel-performance.md) voor een aantal veelvoorkomende prestatie problemen en tijdelijke oplossingen.

### <a name="bursting"></a>Toepassingen
Als uw werk belasting de extra prestaties nodig heeft om aan de piek vraag te voldoen, kan uw aandeel burst-tegoed gebruiken om de limiet van de share basislijn te halen om de share prestaties te bieden die nodig zijn om te voldoen aan de vraag. Premium-bestands shares kunnen de IOPS opburstren tot 4.000 of tot een factor van drie, afhankelijk van wat een hogere waarde is. Bursting wordt geautomatiseerd en werkt op basis van een tegoed systeem. Bursting werkt op basis van de beste inspanningen en de burst-limiet is geen garantie. bestands shares *kunnen de limiet* voor een maximale duur van 60 minuten oplopen.

De tegoeden worden in een burst-Bucket verzameld wanneer het verkeer voor uw bestands share onder IOPS voor de basis lijn valt. Een GiB-share van 100 heeft bijvoorbeeld 500 Baseline IOPS. Als het werkelijke verkeer op de share 100 IOPS is voor een specifiek interval van 1 seconde, worden 400 de ongebruikte IOPS gecrediteerd op een burst-Bucket. Op dezelfde manier, een niet-actieve TiB-share, accumuleert burst tegoed bij 1.424 IOPS. Deze tegoeden worden vervolgens later gebruikt wanneer bewerkingen de basis lijn van IOPS overschrijden.

Wanneer een share de limiet van de basis lijn overschrijdt en over een tegoed in een burst-Bucket beschikt, wordt het maximum voor de Maxi maal toegestane piek burst-snelheid gesplitst. Shares kunnen blijven worden opgedeeld zolang de tegoeden resteren, Maxi maal 60 minuten duren, maar dit is gebaseerd op het aantal burst-tegoeden dat is samengevoegd. Elke i/o-limiet van meer dan Baseline IOPS maakt gebruik van één tegoed en zodra alle tegoeden zijn verbruikt, wordt de share teruggestuurd naar de IOPS van de basis lijn.

Aandelen tegoeden hebben drie statussen:

- Samen, wanneer de bestands share minder dan de basis lijn voor IOPS gebruikt.
- Weigeren, wanneer de bestands share meer dan de basis lijn voor IOPS en de bursting-modus gebruikt.
- Wanneer de bestands share exact de basis lijn voor IOPS gebruikt, zijn er geen tegoeden of worden ze gebruikt.

Nieuwe bestands shares beginnen met het volledige aantal tegoeden in de burst-Bucket. Burst-tegoed wordt niet in rekening gebracht als de delen IOPS onder de basis lijn IOPS vallen vanwege het beperken van de server.

## <a name="pay-as-you-go-model"></a>Model voor betalen naar gebruik
Azure Files maakt gebruik van een zakelijk model voor betalen per gebruik voor standaard bestands shares. In een zakelijk model voor betalen per gebruik wordt het bedrag dat u betaalt, bepaald door de hoeveelheid die u daad werkelijk gebruikt, in plaats van op basis van een ingerichte hoeveelheid. Op hoog niveau betaalt u kosten voor de hoeveelheid gegevens die op de schijf is opgeslagen en vervolgens een extra reeks trans acties op basis van uw gebruik van die gegevens. Een model voor betalen per gebruik kan rendabel zijn, omdat u geen rekening hoeft te houden met het oog op toekomstige groei-of prestatie vereisten of als u de inrichting van uw werk belasting in de loop van de tijd wilt afbreken. Anderzijds kan een model voor betalen naar gebruik moeilijk worden gepland als onderdeel van een budget proces, omdat het facturerings model voor betalen per gebruik wordt aangestuurd door het verbruik van eind gebruikers.

### <a name="differences-in-standard-tiers"></a>Verschillen in de standaard lagen
Wanneer u een standaard bestands share maakt, kiest u tussen de geoptimaliseerde, dynamische en coole lagen van de trans actie. Alle drie de lagen worden op exact dezelfde standaard opslaghardware opgeslagen. Het belangrijkste verschil voor deze drie lagen is de opslagprijs voor gegevens at-rest, die lager zijn in statische lagen, en de transactieprijzen, die hoger zijn in statische lagen. Dit betekent:

- Voor transactie geoptimaliseerd betekent, zoals de naam aangeeft, de prijs voor werkbelastingen met hoge transacties. Voor transactie geoptimaliseerd heeft de hoogste opslagprijs voor gegevens at-rest, maar de laagste transactieprijs.
- Dynamisch is voor actieve workloads waarvoor geen grote aantallen transacties nodig zijn en heeft een iets lagere opslagprijs voor gegevens at-rest, maar iets hogere transactieprijzen in vergelijking met voor transactie geoptimaliseerd. U kunt het beschouwen als het middelste wegdek tussen de geoptimaliseerde trans acties en cool-lagen.
- Cool optimaliseert de prijs voor werk belastingen die geen veel activiteit hebben, met de laagste opslag prijs voor de rest, maar met de hoogste transactie prijzen.

Als u een niet regel matig gebruikte werk belasting in de laag geoptimaliseerd voor trans acties plaatst, betaalt u bijna niets voor het aantal keren in een maand dat u een trans actie voor uw share maakt, maar betaalt u een hoog bedrag voor de kosten voor de gegevens opslag. Als u deze zelfde share naar de coole laag zou verplaatsen, betaalt u nog steeds bijna niets voor de transactie kosten, omdat u zeer zelden trans acties voor deze werk belasting maakt, maar de coole laag een veel goedkopere prijs voor de gegevens opslag heeft. Als u de juiste laag selecteert voor uw use-case, kunt u uw kosten aanzienlijk verlagen. Als u de juiste laag selecteert voor uw use-case, kunt u uw kosten aanzienlijk verlagen.

En als u een Maxi maal beschik bare werk belasting in de cool-laag plaatst, betaalt u veel meer in transactie kosten, maar minder voor de kosten voor gegevens opslag. Dit kan leiden tot een situatie waarin de verhoogde kosten van de transactie prijzen de besparing van de verlaagde prijs van de gegevens opslag verhogen, waardoor u meer geld kunt betalen op koeler dan u zou hebben voor de trans actie geoptimaliseerd. Het is mogelijk dat voor sommige gebruiks niveaus die tijdens de warme tier de meest rendabele laag zijn, de cool-laag duurder is dan het optimaliseren van de trans actie.

Uw workload- en activiteitsniveau bepalen de meest rendabele laag voor uw standaard bestandsshare. In de praktijk is het de beste manier om de meest rendabele laag te kiezen, maar ook om te kijken naar het werkelijke resource verbruik van de share (opgeslagen gegevens, schrijf transacties enz.).

### <a name="what-are-transactions"></a>Wat zijn trans acties?
Trans acties zijn bewerkingen of aanvragen van Azure Files om de inhoud van de bestands share te uploaden, te downloaden of op een andere manier te bewerken. Elke actie die op een bestands share wordt uitgevoerd, vertaalt naar een of meer trans acties, en op standaard shares die gebruikmaken van het facturerings model betalen naar gebruik, worden de transactie kosten omgezet.

Er zijn vijf basis transactie Categorieën: schrijven, lijst, lezen, Overig en verwijderen. Alle bewerkingen die via de REST API of SMB worden uitgevoerd, worden als volgt naar een van deze vier categorieën gebuckd:

| Het type bewerking | Schrijf transacties | Trans acties weer geven | Trans acties lezen | Andere trans acties | Trans acties verwijderen |
|-|-|-|-|-|-|
| Beheerbewerkingen | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Gegevensbewerkingen | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4,1 is alleen beschikbaar voor Premium-bestands shares, die gebruikmaken van het ingerichte facturerings model, trans acties hebben geen invloed op facturering voor Premium-bestands shares.

## <a name="see-also"></a>Zie ook
- [Azure files pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/files/).
- [Planning voor een Azure files implementatie](./storage-files-planning.md) en [planning voor een implementatie van Azure file sync](./storage-sync-files-planning.md).
- [Maak een bestands share](./storage-how-to-create-file-share.md) en [Implementeer Azure file sync](./storage-sync-files-deployment-guide.md).