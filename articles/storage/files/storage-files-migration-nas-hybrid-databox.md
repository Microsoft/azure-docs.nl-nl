---
title: Migratie van on-premises NAS naar Azure File Sync via Azure DataBox
description: Meer informatie over het migreren van bestanden van een on-premises NAS-locatie (Network Attached Storage) naar een hybride Cloud implementatie met Azure File Sync via Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583854"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Gebruik DataBox om te migreren van NAS (Network Attached Storage) naar een hybride Cloud implementatie met Azure File Sync

Dit migratie artikel is een van de tref woorden NAS, Azure File Sync en Azure DataBox. Controleer of dit artikel van toepassing is op uw scenario:

> [!div class="checklist"]
> * Gegevens Bron: NAS (Network Attached Storage)
> * Migratie route: NAS &rArr; DataBox &rArr; Azure file share &rArr; synchroniseren met Windows Server
> * On-premises bestanden opslaan in cache: Ja, het uiteindelijke doel is een Azure File Sync-implementatie.

Als uw scenario anders is, bekijkt u de [tabel met migratie handleidingen](storage-files-migration-overview.md#migration-guides).

Azure File Sync werkt op DAS-locaties (Direct Attached Storage) en biedt geen ondersteuning voor synchronisatie met NAS-locaties (Network Attached Storage).
Dit leidt tot een migratie van uw bestanden die nodig zijn. in dit artikel wordt u begeleid bij het plannen en uitvoeren van een dergelijke migratie.

## <a name="migration-goals"></a>Migratiedoelen

Het doel is om de shares die u op uw NAS-apparaat hebt geplaatst, te verplaatsen naar een Windows-Server. Gebruik vervolgens Azure File Sync voor een hybride Cloud implementatie. Deze migratie moet worden uitgevoerd op een manier die de integriteit van de productie gegevens en beschik baarheid tijdens de migratie waarborgt. Ten laatste moet de downtime tot een minimum worden beperkt, zodat deze kan worden aangepast aan of slechts een beetje meer regel matig onderhouds Vensters kan hebben.

## <a name="migration-overview"></a>Migratieoverzicht

Het migratie proces bestaat uit verschillende fasen. U moet Azure Storage-accounts en-bestands shares implementeren, een on-premises Windows-Server implementeren, Azure File Sync configureren, migreren met behulp van RoboCopy en tot slot de cut gebruiken. In de volgende secties worden de fasen van het migratie proces gedetailleerd beschreven.

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

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>Fase 4: een geschikte Windows Server on-premises inrichten

Terwijl u wacht totdat uw Azure-DataBox (sen) binnenkomen, kunt u al beginnen met het controleren van de behoeften van een of meer Windows-servers die u gebruikt met Azure File Sync.

* Maak een Windows Server 2019-met een minimale 2012R2-als een virtuele machine of fysieke server. Een failover-cluster van Windows Server wordt ook ondersteund.
* Het inrichten of toevoegen van direct gekoppelde opslag (DAS in vergelijking met NAS), wat niet wordt ondersteund.

De resource configuratie (reken kracht en RAM) van de Windows-Server die u implementeert, is vooral afhankelijk van het aantal items (bestanden en mappen) dat u wilt synchroniseren. Een configuratie met een hogere prestaties wordt aanbevolen als u problemen ondervindt.

[Meer informatie over het aanpassen van de grootte van een Windows-Server op basis van het aantal items (bestanden en mappen) dat u wilt synchroniseren.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Het eerder gekoppelde artikel bevat een tabel met een bereik voor Server geheugen (RAM). U kunt naar het kleinere nummer voor uw server richten, maar u kunt verwachten dat initiële synchronisatie aanzienlijk meer tijd kan duren.

## <a name="phase-5-copy-files-onto-your-databox"></a>Fase 5: bestanden naar uw DataBox kopiëren

Wanneer uw DataBox arriveert, moet u uw DataBox in de regel met het gezichts vermogen instellen op uw NAS-apparaat. Volg de installatie documentatie voor het DataBox-type dat u hebt besteld.

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

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Fase 6: de Azure File Sync Cloud resource implementeren

Voordat u doorgaat met deze hand leiding, moet u wachten tot al uw bestanden zijn ontvangen in de juiste Azure-bestands shares. Het proces van verzen ding en opname van DataBox-gegevens duurt enige tijd.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Fase 7: de Azure File Sync-agent implementeren

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>Fase 8: Azure File Sync configureren op de Windows-Server

Uw geregistreerde on-premises Windows-Server moet gereed zijn en verbonden zijn met internet voor dit proces.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Schakel de functie voor Cloud lagen in en selecteer ' naam ruimte alleen ' in de sectie eerste down load.

> [!IMPORTANT]
> Cloud lagen is de AFS-functie waarmee de lokale server minder opslag capaciteit kan hebben dan is opgeslagen in de Cloud, maar die de volledige naam ruimte beschikbaar heeft. Lokaal interessante gegevens worden ook lokaal in de cache opgeslagen voor snelle toegang tot de prestaties. Cloud lagen is een optionele functie per Azure File Sync server-eind punt. U moet deze functie gebruiken als u niet voldoende lokale schijf capaciteit hebt op de Windows-Server om alle Cloud gegevens op te slaan en als u wilt voor komen dat alle gegevens van de cloud worden gedownload.

Herhaal de stappen voor het maken van de synchronisatie groep en het toevoegen van de overeenkomende servermap als server-eind punt voor alle Azure-bestands shares/server locaties die moeten worden geconfigureerd voor synchronisatie. Wacht totdat de synchronisatie van de naam ruimte is voltooid. In het volgende gedeelte ziet u hoe u dit kunt controleren.

> [!NOTE]
> Na het maken van een server eindpunt werkt synchronisatie. Synchronisatie moet echter de bestanden en mappen die u via DataBox naar de Azure-bestands share hebt verplaatst, inventariseren (detecteren). Afhankelijk van de grootte van de naam ruimte kan dit veel tijd in beslag nemen voordat de naam ruimte van de Cloud wordt weer gegeven op de server.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Fase 9: wachten tot de naam ruimte volledig wordt weer gegeven op de server

Het is belang rijk dat u wacht met de volgende stappen van de migratie die de server volledig heeft gedownload van de Cloud share. Als u bestanden te vroeg op de server gaat verplaatsen, kunt u onnodige uploads en zelfs conflicten met bestands synchronisatie oplossen.

Als u wilt weten of uw server de eerste download synchronisatie heeft voltooid, opent u Logboeken op uw Windows-Server synchroniseren en gebruikt u het gebeurtenis logboek van de Azure File Sync-telemetrie.
Het logboek voor telemetrie bevindt zich in Logboeken onder toepassingen en Services\Microsoft\FileSync\Agent.

Zoek naar de meest recente 9102-gebeurtenis. Gebeurtenis-ID 9102 wordt vastgelegd zodra een synchronisatie sessie is voltooid. In de tekst van de gebeurtenis is dit een veld voor de richting van de Download synchronisatie. ( `HResult` moet nul zijn en bestanden ook worden gedownload).
 
U wilt twee opeenvolgende gebeurtenissen van dit type en inhoud weer geven om te laten zien dat de server klaar is met het downloaden van de naam ruimte. Het is niet erg als er verschillende gebeurtenissen worden gestart tussen de 2 9102-gebeurtenissen.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>Fase 10: het opvangen van RoboCopy van uw NAS

Als uw server de initiële synchronisatie van de volledige naam ruimte vanuit de Cloud share heeft voltooid, kunt u door gaan met deze stap. Het is essentieel dat deze stap is voltooid, voordat u doorgaat met deze stap. Raadpleeg de vorige sectie voor meer informatie.

In deze stap voert u RoboCopy-taken uit om uw Cloud shares te voorzien van de meest recente wijzigingen op uw NAS sinds de tijd dat u uw shares op de DataBox hebt opgesplitst.
Deze ophaal-RoboCopy kan snel worden voltooid of enige tijd duren, afhankelijk van de hoeveelheid verloop die is opgetreden op uw NAS-shares.

> [!WARNING]
> Vanwege een teruggedraaide RoboCopy-gedrag in Windows Server 2019 is de/MIR-switch van RoboCopy niet compatibel met een gelaagd doel directory. U moet voor deze fase van de migratie geen Windows Server 2019-of Windows 10-client gebruiken. Gebruik RoboCopy op een tussenliggend Windows Server 2016.

De basis benadering van de migratie is een RoboCopy van uw NAS-apparaat naar uw Windows-Server en Azure File Sync naar Azure-bestands shares.

Voer de eerste lokale kopie uit naar de doelmap van Windows Server:

1. De eerste locatie op uw NAS-apparaat identificeren.
1. Identificeer de overeenkomende map op de Windows-Server, waarop al Azure File Sync zijn geconfigureerd.
1. De kopie starten met behulp van RoboCopy

Met de volgende RoboCopy-opdracht kopieert u alleen de verschillen (bijgewerkte bestanden en mappen) van de NAS-opslag naar de doelmap van uw Windows-Server. De Windows-Server synchroniseert deze vervolgens naar de Azure-bestands share (s). 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Als u minder opslag ruimte op uw Windows-Server hebt ingericht dan uw bestanden op het NAS-apparaat innemen, hebt u Cloud lagen geconfigureerd. Omdat het lokale Windows Server-volume vol is, worden er [Cloud lagen](storage-sync-cloud-tiering-overview.md) in gemaakt en lagen die al zijn gesynchroniseerd. Cloud-lagen genereren voldoende ruimte om het kopiëren van het NAS-apparaat voort te zetten. Controles voor Cloud lagen worden één keer per uur uitgevoerd om te zien wat er is gesynchroniseerd en om schijf ruimte vrij te maken voor het bereiken van de beschik bare ruimte van 99% volume.
Het is mogelijk dat RoboCopy talloze bestanden moet verplaatsen, meer dan lokale opslag voor op de Windows-Server. Gemiddeld kunt u een hoop dat RoboCopy sneller wordt verplaatst dan Azure File Sync uw bestanden kunt uploaden over uw lokale volume. RoboCopy mislukt. Het wordt aanbevolen dat u de shares in een volg orde doorloopt, waardoor dat niet mogelijk is. U kunt bijvoorbeeld geen RoboCopy-taken voor alle shares tegelijk starten of alleen shares verplaatsen die passen bij de huidige hoeveelheid beschik bare ruimte op de Windows-Server, om een paar te vermelden. Het goede nieuws is dat de/MIR-switch alleen Deltas verplaatst en wanneer een Delta is verplaatst, het bestand niet opnieuw hoeft te worden verplaatst.

### <a name="user-cut-over"></a>Gebruiker knippen

Wanneer u de RoboCopy-opdracht voor de eerste keer uitvoert, hebben uw gebruikers en toepassingen nog steeds toegang tot bestanden op de NAS en kunnen ze deze wijzigen. Het is mogelijk dat de RoboCopy een directory heeft verwerkt, naar de volgende gaat en vervolgens een gebruiker op de bron locatie (NAS) een bestand toevoegt, wijzigt of verwijdert dat nu niet wordt verwerkt in deze huidige RoboCopy-uitvoering. Dit gedrag is verwacht.

De eerste keer dat u het meren deel van de gegevens naar uw Windows-Server en naar de Cloud verplaatst via Azure File Sync. Het eerste exemplaar kan enige tijd in beslag nemen, afhankelijk van:

* de upload bandbreedte
* de snelheid van het lokale netwerk en het aantal hoe optimaal het aantal RoboCopy-threads overeenkomt
* het aantal items (bestanden en mappen) dat moet worden verwerkt door RoboCopy en Azure File Sync

Zodra de eerste uitvoering is voltooid, voert u de opdracht opnieuw uit.

Een tweede keer dat u RoboCopy voor dezelfde share uitvoert, wordt deze sneller voltooid, omdat alleen de wijzigingen moeten worden getransporteerd die sinds de laatste uitvoering zijn opgetreden. U kunt herhaalde taken uitvoeren voor dezelfde share.

Wanneer u de downtime in overweging neemt, moet u de gebruikers toegang tot uw op NAS gebaseerde shares verwijderen. U kunt dit doen door alle stappen die voor komen dat gebruikers de structuur en inhoud van bestanden en mappen wijzigen. Een voor beeld is om uw DFS-Namespace naar een niet-bestaande locatie te wijzen of de hoofd-Acl's op de share te wijzigen.

Voer een laatste RoboCopy-ronde uit. Er worden wijzigingen opgehaald, die mogelijk zijn gemist.
Hoe lang deze laatste stap duurt, is afhankelijk van de snelheid van de RoboCopy-scan. U kunt een schatting maken van de tijd (die gelijk is aan uw downtime) door te meten hoe lang de vorige uitvoering heeft geduurd.

Maak een share op de Windows Server-map en stel eventueel uw DFS-N-implementatie zodanig in dat deze ernaar verwijst. Zorg ervoor dat u dezelfde machtigingen op share niveau hebt ingesteld als op uw NAS SMB-share. Als u een NAS die aan een domein is toegevoegd, worden de gebruikers-Sid's automatisch vergeleken wanneer de gebruikers bestaan in Active Directory en RoboCopy kopieert bestanden en meta gegevens met volledige betrouw baarheid. Als u lokale gebruikers op uw NAS hebt gebruikt, moet u deze gebruikers opnieuw maken als lokale gebruikers van Windows Server en de bestaande Sid's van de nieuwe Windows Server-computer aan de Sid's van uw Windows Server-lokale gebruikers toewijzen.

U bent klaar met het migreren van een share/groep shares naar een gemeen schappelijke hoofdmap of volume. (Afhankelijk van uw toewijzing vanuit fase 1)

U kunt proberen enkele van deze kopieën parallel uit te voeren. Het is raadzaam om het bereik van een Azure-bestands share tegelijk te verwerken.

## <a name="troubleshoot"></a>Problemen oplossen

Het meest waarschijnlijke probleem dat u kunt uitvoeren in, is dat de RoboCopy-opdracht mislukt met *' volume vol '* aan de Windows Server-zijde. Cloud lagen reageert eenmaal elk uur om inhoud te verlaten van de lokale Windows Server-schijf, die is gesynchroniseerd. Het doel is om uw 99% beschik bare ruimte op het volume te bereiken.

Laat de voortgang van de synchronisatie en Cloud lagen vrij schijf ruimte vrijmaken. U ziet dat in Verkenner op uw Windows-Server.

Als uw Windows-Server voldoende beschik bare capaciteit heeft, wordt het probleem opgelost door de opdracht opnieuw uit te voeren. Er zijn geen onderbrekingen wanneer u deze situatie krijgt en u kunt door gaan met vertrouwen. Het ongemak van het uitvoeren van de opdracht is het enige gevolg.

Controleer de koppeling in de volgende sectie voor het oplossen van problemen met Azure File Sync.

## <a name="next-steps"></a>Volgende stappen

Er is meer informatie over Azure-bestands shares en Azure File Sync. De volgende artikelen helpen u bij het begrijpen van geavanceerde opties, aanbevolen procedures en ook voor het oplossen van problemen. Deze artikelen maken een koppeling naar de [documentatie van Azure file share](storage-files-introduction.md) .

* [Overzicht migratie](storage-files-migration-overview.md)
* [Overzicht van AFS](./storage-sync-files-planning.md)
* [AFS-implementatie handleiding](./storage-how-to-create-file-share.md)
* [Problemen met AFS oplossen](storage-sync-files-troubleshoot.md)
