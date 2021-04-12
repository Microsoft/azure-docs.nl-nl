---
title: Migratie van on-premises NAS naar Azure-bestands shares
description: Meer informatie over het migreren van bestanden van een NAS-locatie (on-premises Network Attached Storage) naar Azure-bestands shares met Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a8420d23c8bda29290722975ada2acca6733f0e7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491667"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>DataBox gebruiken om te migreren van NAS (Network Attached Storage) naar Azure-bestands shares

Dit migratie artikel is een van de tref woorden NAS en Azure DataBox. Controleer of dit artikel van toepassing is op uw scenario:

> [!div class="checklist"]
> * Gegevens Bron: NAS (Network Attached Storage)
> * Migratie route: NAS &rArr; DataBox &rArr; Azure-bestands share
> * Lokale cache bestanden: Nee, het uiteindelijke doel is het gebruik van de Azure-bestands shares rechtstreeks in de Cloud. Er is geen abonnement om Azure File Sync te gebruiken.

Als uw scenario anders is, bekijkt u de [tabel met migratie handleidingen](storage-files-migration-overview.md#migration-guides).

Dit artikel richt u op end-to-end door de planning, implementatie en netwerk configuraties die nodig zijn voor de migratie van uw NAS-apparaat naar functionele Azure-bestands shares. In deze hand leiding wordt gebruikgemaakt van Azure DataBox voor bulk gegevens overdracht (offline gegevens overdracht).

## <a name="migration-goals"></a>Migratiedoelen

Het doel is om de shares die u hebt op uw NAS-apparaat te verplaatsen naar Azure en ze de systeem eigen Azure-bestands shares te geven die u kunt gebruiken zonder dat u een Windows-Server nodig hebt. Deze migratie moet worden uitgevoerd op een manier die de integriteit van de productie gegevens en beschik baarheid tijdens de migratie waarborgt. Ten laatste moet de downtime tot een minimum worden beperkt, zodat deze kan worden aangepast aan of slechts een beetje meer regel matig onderhouds Vensters kan hebben.

## <a name="migration-overview"></a>Migratieoverzicht

Het migratie proces bestaat uit verschillende fasen. U moet Azure Storage-accounts en-bestands shares implementeren, netwerken configureren, migreren met behulp van Azure DataBox, met wijzigingen via RoboCopy, en ten slotte uw gebruikers naar de zojuist gemaakte Azure-bestands shares knippen. In de volgende secties worden de fasen van het migratie proces gedetailleerd beschreven.

> [!TIP]
> Als u terugkeert naar dit artikel, gebruikt u de navigatie aan de rechter kant om naar de migratie fase te gaan waar u was gebleven.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: bepalen hoeveel Azure-bestands shares u nodig hebt

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Fase 2: Azure storage-resources implementeren

In deze fase raadpleegt u de toewijzings tabel uit fase 1 en gebruikt u deze om het juiste aantal Azure-opslag accounts en bestands shares in te richten.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>Fase 3: bepalen hoeveel Azure DataBox-apparaten u nodig hebt

Start deze stap alleen, wanneer u de vorige fase hebt voltooid. Uw Azure storage-resources (opslag accounts en bestands shares) moeten op dit moment worden gemaakt. Tijdens uw DataBox-volg orde moet u opgeven in welke opslag accounts de DataBox gegevens gaat verplaatsen.

In deze fase moet u de resultaten van het migratie plan vanuit de vorige fase toewijzen aan de limieten van de beschik bare opties voor DataBox. Deze overwegingen helpen u bij het maken van een plan voor welke DataBox-opties u moet kiezen en hoe veel van hen u nodig hebt om uw NAS-shares te verplaatsen naar Azure-bestands shares.

Houd rekening met de volgende belang rijke limieten om te bepalen hoeveel apparaten van welk type u nodig hebt:

* Azure DataBox kan gegevens naar Maxi maal 10 opslag accounts verplaatsen. 
* Elke DataBox-optie is verkrijgbaar met een eigen bruikbare capaciteit. Zie [DataBox-opties](#databox-options).

Raadpleeg uw migratie plan voor het aantal opslag accounts dat u hebt gekozen om te maken en de shares in elk van deze. Bekijk vervolgens de grootte van elk van de shares op de NAS. Door deze informatie samen te voegen, kunt u optimaliseren en bepalen welk apparaat gegevens moet verzenden naar welke opslag accounts. U kunt twee DataBox-apparaten gebruiken om bestanden te verplaatsen naar hetzelfde opslag account, maar u hoeft niet de inhoud van één bestands share over 2 DataBoxes te splitsen.

### <a name="databox-options"></a>Opties voor DataBox

Voor een standaard migratie moet één of een combi natie van deze drie DataBox opties worden gekozen: 

* DataBox schijven van micro soft sturen u een en Maxi maal vijf SSD-schijven met een capaciteit van 8 TiB, voor een maximum aantal van 40 TiB. De bruikbare capaciteit is ongeveer 20% minder, vanwege versleuteling en de overhead van het bestands systeem. Zie [DataBox disks documentation](../../databox/data-box-disk-overview.md)(Engelstalig) voor meer informatie.
* DataBox dit is de meest voorkomende optie. Een robuust DataBox apparaat dat vergelijkbaar is met een NAS, wordt aan u geleverd. Het heeft een bruikbare capaciteit van 80 TiB. Zie [DataBox-documentatie](../../databox/data-box-overview.md)voor meer informatie.
* DataBox-zwaar deze optie is een robuust DataBox apparaat op wielen, dat vergelijkbaar is met een NAS, met een capaciteit van 1 PiB. De bruikbare capaciteit is ongeveer 20% minder, vanwege versleuteling en de overhead van het bestands systeem. Zie [DataBox Heavy documentation](../../databox/data-box-heavy-overview.md)(Engelstalig) voor meer informatie.

## <a name="phase-4-provision-a-temporary-windows-server"></a>Fase 4: een tijdelijke Windows-server inrichten

Terwijl u wacht totdat uw Azure-DataBox (sen) binnenkomen, kunt u al een of meer Windows-servers implementeren die u nodig hebt voor het uitvoeren van RoboCopy-taken. 

- Het eerste gebruik van deze servers is het kopiëren van bestanden naar het DataBox.
- Het tweede gebruik van deze servers is het opvangen van de wijzigingen die zich hebben voorgedaan op het NAS-apparaat terwijl DataBox werd getransporteerd. Met deze aanpak wordt de uitval tijd op de bron zijde tot een minimum beperkt.

De snelheid waarmee uw RoboCopy-taken werken, is vooral afhankelijk van de volgende factoren:

* IOPS op de bron-en doel opslag
* de beschik bare netwerk bandbreedte ertussen </br> Meer informatie vindt u in de sectie probleem oplossing: [IOPS en bandbreedte overwegingen](#iops-and-bandwidth-considerations)
* de mogelijkheid om snel bestanden en mappen in een naam ruimte te verwerken </br> Meer informatie vindt u in de sectie probleem oplossing: [verwerkings snelheid](#processing-speed)
* het aantal wijzigingen tussen RoboCopy-runs </br> Meer informatie vindt u in de sectie probleem oplossing: [onnodige werk voor komen](#avoid-unnecessary-work)

Het is belang rijk om de gedetailleerde details te onthouden bij het bepalen van het RAM-geheugen en het aantal threads dat u aan uw tijdelijke Windows-Server (s) verstrekt.

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>Fase 5: het gebruik van Azure-bestands shares voorbereiden

Als u tijd wilt besparen, kunt u door gaan met deze fase terwijl u wacht totdat uw DataBox arriveert. Met de informatie in deze fase kunt u bepalen hoe uw servers en gebruikers in Azure en buiten Azure worden ingeschakeld voor gebruik van uw Azure-bestands shares. De meest kritieke beslissingen zijn:

- **Netwerken:** Schakel uw netwerken in om SMB-verkeer te routeren.
- **Verificatie:** Azure Storage-accounts configureren voor Kerberos-verificatie. AdConnect en domein waarmee uw opslag account wordt toegevoegd, kunnen uw apps en gebruikers hun AD-identiteit voor verificatie gebruiken
- **Autorisatie:** Acl's op share niveau voor elke Azure-bestands share zorgen ervoor dat AD-gebruikers en-groepen toegang hebben tot een bepaalde share en binnen een Azure-bestands share. systeem eigen NTFS-Acl's worden overgenomen. Verificatie op basis van bestands-en map-Acl's werkt dan zoals bij on-premises SMB-shares.
- **Bedrijfs continuïteit:** Integratie van Azure-bestands shares in een bestaande omgeving omvat vaak het behouden van bestaande share adressen. Als u geen gebruik maakt van DFS-naam ruimten, kunt u overwegen om in uw omgeving in te richten. U kunt delen van uw gebruikers en scripts blijven gebruiken. U gebruikt DFS-N als een naam ruimte routering voor SMB door DFS-Namespace doelen te omleiden naar Azure-bestands shares na de migratie.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Deze video bevat een hand leiding en demo voor het veilig beschikbaar maken van Azure-bestands shares aan informatie medewerkers en apps in vijf eenvoudige stappen.</br>
        De video verwijst naar specifieke documentatie voor sommige onderwerpen:

* [Overzicht van identiteit](storage-files-active-directory-overview.md)
* [Een domein toevoegen aan een opslag account](storage-files-identity-auth-active-directory-enable.md)
* [Overzicht van netwerken voor Azure-bestands shares](storage-files-networking-overview.md)
* [Open bare en privé-eind punten configureren](storage-files-networking-endpoints.md)
* [Een S2S-VPN configureren](storage-files-configure-s2s-vpn.md)
* [Een Windows P2S VPN configureren](storage-files-configure-p2s-vpn-windows.md)
* [Een Linux P2S-VPN configureren](storage-files-configure-p2s-vpn-linux.md)
* [DNS-door sturen configureren](storage-files-networking-dns.md)
* [DFS configureren-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>Fase 6: bestanden naar uw DataBox kopiëren

Wanneer uw DataBox arriveert, moet u uw DataBox in een regel van het gezichts vermogen instellen op uw NAS-apparaat. Volg de installatie documentatie voor het DataBox-type dat u hebt besteld.

* [Data Box instellen](../../databox/data-box-quickstart-portal.md)
* [Data Box Disk instellen](../../databox/data-box-disk-quickstart-portal.md)
* [Data Box Heavy instellen](../../databox/data-box-heavy-quickstart-portal.md)

Afhankelijk van het type DataBox, zijn er mogelijk DataBox Kopieer hulpprogramma's beschikbaar voor u. Op dit moment worden ze niet aanbevolen voor migraties naar Azure-bestands shares, omdat ze uw bestanden niet kopiëren met volledige betrouw baarheid naar het DataBox. Gebruik in plaats daarvan RoboCopy.

Wanneer uw DataBox arriveert, worden er vooraf ingerichte SMB-shares beschikbaar gesteld voor elk opslag account dat u hebt opgegeven op het moment dat u het plaatst.

* Als uw bestanden naar een Premium Azure-bestands share gaan, is er één SMB-share per Premium-opslag account.
* Als uw bestanden naar een Standard-opslag account gaan, zijn er drie SMB-shares per standaard-GPv1 en GPv2-opslag account. Alleen de bestands shares eindigend op `_AzFiles` zijn relevant voor uw migratie. Alle blok-en pagina-BLOB-shares negeren.

Volg de stappen in de Azure DataBox-documentatie:

1. [Verbinding maken met Data Box](../../databox/data-box-deploy-copy-data.md)
1. Gegevens kopiëren naar Data Box
1. [Uw DataBox voorbereiden voor vertrek naar Azure](../../databox/data-box-deploy-picked-up.md)

In de documentatie van de gekoppelde DataBox wordt een RoboCopy-opdracht opgegeven. De opdracht is echter niet geschikt voor het behouden van de volledige betrouw baarheid van bestanden en mappen. Gebruik deze opdracht in plaats daarvan:

```console
Robocopy /MT:32 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```
* Raadpleeg de tabel in het [gedeelte aanstaande Robocopy](#robocopy)voor meer informatie over de details van de afzonderlijke Robocopy-vlaggen.
* Als u meer wilt weten over de juiste grootte van het thread aantal `/MT:n` , optimaliseert u de snelheid van de Robocopy en maakt u een goede buur in uw Data Center, Bekijk dan de [sectie voor probleem oplossing in Robocopy](#troubleshoot).


## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>Fase 7: het opvangen van RoboCopy van uw NAS

Zodra uw DataBox meldt dat alle bestanden en mappen zijn geplaatst in de geplande Azure-bestands shares, kunt u door gaan met deze fase.
Een ophaal-RoboCopy is alleen nodig als de gegevens op de NAS mogelijk zijn gewijzigd sinds het kopiëren van de DataBox is gestart. In bepaalde scenario's waarin u een share gebruikt voor archiverings doeleinden, kunt u de wijzigingen aan de share op uw NAS mogelijk stopzetten totdat de migratie is voltooid. Mogelijk hebt u ook de mogelijkheid om uw bedrijfs vereisten te gebruiken door NAS-shares in te stellen op alleen-lezen tijdens de migratie.

In gevallen waarin u wilt dat een share tijdens de migratie lezen/schrijven is en alleen een klein downtime-venster kan worden geabsorbeerd, is deze stap voor het opvangen van de RoboCopy belang rijk om te volt ooien voordat de failover van gebruikers rechtstreeks naar de Azure-bestands share wordt uitgevoerd.

In deze stap voert u RoboCopy-taken uit om uw Cloud shares te voorzien van de meest recente wijzigingen op uw NAS sinds de tijd dat u uw shares op de DataBox hebt opgesplitst.
Deze ophaal-RoboCopy kan snel worden voltooid of enige tijd duren, afhankelijk van de hoeveelheid verloop die is opgetreden op uw NAS-shares.

Voer de eerste lokale kopie uit naar de doelmap van Windows Server:

1. De eerste locatie op uw NAS-apparaat identificeren.
1. Identificeer de overeenkomende Azure-bestands share.
1. Koppel de Azure-bestands share als een lokaal netwerk station op uw tijdelijke Windows Server
1. De kopie starten met behulp van RoboCopy zoals beschreven

### <a name="mounting-an-azure-file-share"></a>Een Azure-bestands share koppelen

Voordat u RoboCopy kunt gebruiken, moet u de Azure-bestands share toegankelijk maken via SMB. De eenvoudigste manier is om de share als een lokaal netwerk station te koppelen aan de Windows-Server die u wilt gebruiken voor RoboCopy. 

> [!IMPORTANT]
> Voordat u een Azure-bestands share kunt koppelen aan een lokale Windows-Server, moet u de volgende fase hebben voltooid: voorbereiden op het gebruik van Azure-bestands shares.

Wanneer u klaar bent, raadpleegt u het [artikel een Azure-bestands share gebruiken met Windows How-to](storage-how-to-use-files-windows.md) , en koppelt u de Azure-bestands share waarvoor u de NAS-up Robocopy wilt starten.

### <a name="robocopy"></a>RoboCopy

Met de volgende RoboCopy-opdracht kopieert u alleen de verschillen (bijgewerkte bestanden en mappen) van de NAS-opslag naar uw Azure-bestands share. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> [Raadpleeg de sectie probleem oplossing](#troubleshoot) als de Robocopy uw productie omgeving beïnvloedt, veel fouten rapporteert of niet zo snel als verwacht wordt uitgevoerd.

### <a name="user-cut-over"></a>Gebruiker knippen

Wanneer u de RoboCopy-opdracht voor de eerste keer uitvoert, hebben uw gebruikers en toepassingen nog steeds toegang tot bestanden op de NAS en kunnen ze deze wijzigen. Het is mogelijk dat de RoboCopy een directory heeft verwerkt, naar de volgende gaat en vervolgens een gebruiker op de bron locatie (NAS) een bestand toevoegt, wijzigt of verwijdert dat nu niet wordt verwerkt in deze huidige RoboCopy-uitvoering. Dit gedrag is verwacht.

Bij de eerste uitvoering wordt het meren deel van de gegevens naar uw Azure-bestands share verplaatst. Het eerste exemplaar kan enige tijd duren. Raadpleeg de [sectie probleem oplossing](#troubleshoot) voor meer inzicht in wat de snelheid van Robocopy kan beïnvloeden.

Zodra de eerste uitvoering is voltooid, voert u de opdracht opnieuw uit.

Een tweede keer dat u RoboCopy voor dezelfde share uitvoert, wordt deze sneller voltooid, omdat alleen de wijzigingen moeten worden getransporteerd die sinds de laatste uitvoering zijn opgetreden. U kunt herhaalde taken uitvoeren voor dezelfde share.

Wanneer u de downtime in overweging neemt, moet u de gebruikers toegang tot uw op NAS gebaseerde shares verwijderen. U kunt dit doen door alle stappen die voor komen dat gebruikers de structuur en inhoud van bestanden en mappen wijzigen. Een voor beeld is om uw DFS-Namespace naar een niet-bestaande locatie te wijzen of de hoofd-Acl's op de share te wijzigen.

Voer een laatste RoboCopy-ronde uit. Er worden wijzigingen opgehaald, die mogelijk zijn gemist.
Hoe lang deze laatste stap duurt, is afhankelijk van de snelheid van de RoboCopy-scan. U kunt een schatting maken van de tijd (die gelijk is aan uw downtime) door te meten hoe lang de vorige uitvoering heeft geduurd.

Maak een share op de Windows Server-map en stel eventueel uw DFS-N-implementatie zodanig in dat deze ernaar verwijst. Zorg ervoor dat u dezelfde machtigingen op share niveau hebt ingesteld als op uw NAS SMB-share. Als u een NAS die aan een domein is toegevoegd, worden de gebruikers-Sid's automatisch vergeleken wanneer de gebruikers bestaan in Active Directory en RoboCopy kopieert bestanden en meta gegevens met volledige betrouw baarheid. Als u lokale gebruikers op uw NAS hebt gebruikt, moet u deze gebruikers opnieuw maken als lokale gebruikers van Windows Server en de bestaande Sid's van de nieuwe Windows Server-computer aan de Sid's van uw Windows Server-lokale gebruikers toewijzen.

U bent klaar met het migreren van een share/groep shares naar een gemeen schappelijke hoofdmap of volume. (Afhankelijk van uw toewijzing vanuit fase 1)

U kunt proberen enkele van deze kopieën parallel uit te voeren. Het is raadzaam om het bereik van een Azure-bestands share tegelijk te verwerken.

## <a name="troubleshoot"></a>Problemen oplossen

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>Volgende stappen

Er is meer informatie over Azure-bestands shares. De volgende artikelen helpen u bij het begrijpen van geavanceerde opties, aanbevolen procedures en ook voor het oplossen van problemen. Deze artikelen maken een koppeling naar de [documentatie van Azure file share](storage-files-introduction.md) .

* [Overzicht migratie](storage-files-migration-overview.md)
* [Microsoft Azure Storage bewaken, problemen opsporen en oplossen](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [Aandachtspunten voor netwerken voor directe toegang](storage-files-networking-overview.md)
* [Back-up: moment opnamen van Azure-bestands shares](storage-snapshots-files.md)
