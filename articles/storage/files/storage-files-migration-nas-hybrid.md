---
title: Migratie van on-premises NAS naar Azure File Sync
description: Meer informatie over het migreren van bestanden van een on-premises NAS-locatie (Network Attached Storage) naar een hybride Cloud implementatie met Azure File Sync en Azure-bestands shares.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 86e79302716fa502d8562dd563b0a5c5fb220a67
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547545"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migreren van NAS (Network Attached Storage) naar een hybride Cloud implementatie met Azure File Sync

Dit migratie artikel is een van de tref woorden NAS en Azure File Sync. Controleer of dit artikel van toepassing is op uw scenario:

> [!div class="checklist"]
> * Gegevens Bron: NAS (Network Attached Storage)
> * Migratie route: NAS &rArr; Windows Server &rArr; uploaden en synchroniseren met Azure-bestands shares
> * On-premises bestanden opslaan in cache: Ja, het uiteindelijke doel is een Azure File Sync-implementatie.

Als uw scenario anders is, bekijkt u de [tabel met migratie handleidingen](storage-files-migration-overview.md#migration-guides).

Azure File Sync werkt op DAS-locaties (Direct Attached Storage) en biedt geen ondersteuning voor synchronisatie met NAS-locaties (Network Attached Storage).
Dit leidt tot een migratie van uw bestanden die nodig zijn. in dit artikel wordt u begeleid bij het plannen en uitvoeren van een dergelijke migratie.

## <a name="migration-goals"></a>Migratiedoelen

Het doel is om de shares die u op uw NAS-apparaat hebt geplaatst, te verplaatsen naar een Windows-Server. Gebruik vervolgens Azure File Sync voor een hybride Cloud implementatie. Over het algemeen moeten migraties worden uitgevoerd op een manier die de integriteit van de productie gegevens Guaranty en de beschik baarheid tijdens de migratie. Ten laatste moet de downtime tot een minimum worden beperkt, zodat deze kan worden aangepast aan of slechts een beetje meer regel matig onderhouds Vensters kan hebben.

## <a name="migration-overview"></a>Migratieoverzicht

Zoals vermeld in het [overzichts artikel](storage-files-migration-overview.md)over Azure files migratie, met het juiste Kopieer programma en de aanpak is belang rijk. Uw NAS-apparaat maakt SMB-shares rechtstreeks op uw lokale netwerk zichtbaar. RoboCopy, ingebouwde Windows Server, is de beste manier om uw bestanden in dit migratie scenario te verplaatsen.

- Fase 1: [bepalen hoeveel Azure-bestands shares u nodig hebt](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fase 2: [een geschikte Windows Server on-premises inrichten](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fase 3: [de Azure file sync Cloud resource implementeren](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fase 4: [Azure storage-resources implementeren](#phase-4-deploy-azure-storage-resources)
- Fase 5: [de Azure file sync-agent implementeren](#phase-5-deploy-the-azure-file-sync-agent)
- Fase 6: [Azure file sync configureren op de Windows-Server](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fase 7: [Robocopy](#phase-7-robocopy)
- Fase 8: [gebruikers knippen](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: bepalen hoeveel Azure-bestands shares u nodig hebt

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fase 2: een geschikte Windows Server on-premises inrichten

* Maak een Windows Server 2019-met een minimale 2012R2-als een virtuele machine of fysieke server. Een failover-cluster van Windows Server wordt ook ondersteund.
* Het inrichten of toevoegen van direct gekoppelde opslag (DAS in vergelijking met NAS), wat niet wordt ondersteund.

    De hoeveelheid opslag ruimte die u hebt ingericht, kan kleiner zijn dan wat u momenteel op uw NAS-apparaat gebruikt. Voor deze configuratie optie moet u ook gebruikmaken van de functie voor het maken van [Cloud lagen](storage-sync-cloud-tiering-overview.md) in azure file sync.
    Wanneer u echter uw bestanden vanuit de grotere NAS-ruimte naar het kleinere Windows Server-volume in een latere fase kopieert, moet u in batches werken:

    1. Een set bestanden verplaatsen die op de schijf passen
    2. bestands synchronisatie en Cloud lagen actief laten
    3. Wanneer er meer vrije ruimte op het volume wordt gemaakt, gaat u door met de volgende batch bestanden. U kunt ook de RoboCopy-opdracht in het komende [Robocopy-gedeelte](#phase-7-robocopy) controleren voor gebruik van de nieuwe `/LFSM` Switch. Met `/LFSM` kunt u uw Robocopy-taken aanzienlijk vereenvoudigen, maar dit is niet compatibel met een aantal andere Robocopy-switches die u mogelijk afhankelijk maakt.
    
    U kunt deze methode voor batch verwerking vermijden door de gelijkwaardige ruimte op de Windows-Server in te richten die uw bestanden op het NAS-apparaat innemen. Overweeg ontdubbeling op NAS/Windows. Als u deze hoge hoeveelheid opslag ruimte niet permanent wilt door voeren naar uw Windows-Server, kunt u de volume grootte na de migratie verkleinen en voordat u het beleid voor Cloud lagen aanpast. Hiermee maakt u een kleinere on-premises cache van uw Azure-bestands shares.

De resource configuratie (reken kracht en RAM) van de Windows-Server die u implementeert, is vooral afhankelijk van het aantal items (bestanden en mappen) dat u wilt synchroniseren. Als u problemen hebt, kunt u het beste een configuratie met hogere prestaties uitvoeren.

[Meer informatie over het aanpassen van de grootte van een Windows-Server op basis van het aantal items (bestanden en mappen) dat u wilt synchroniseren.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Het eerder gekoppelde artikel bevat een tabel met een bereik voor Server geheugen (RAM). U kunt naar het kleinere nummer voor uw server richten, maar u kunt verwachten dat initiële synchronisatie aanzienlijk meer tijd kan duren.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: de Azure File Sync Cloud resource implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: Azure storage-resources implementeren

In deze fase raadpleegt u de toewijzings tabel uit fase 1 en gebruikt u deze om het juiste aantal Azure-opslag accounts en bestands shares in te richten.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: de Azure File Sync-agent implementeren

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fase 6: Azure File Sync configureren op de Windows-Server

Uw geregistreerde on-premises Windows-Server moet gereed zijn en verbonden zijn met internet voor dit proces.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Cloud lagen is de AFS-functie waarmee de lokale server minder opslag capaciteit kan hebben dan is opgeslagen in de Cloud, maar die de volledige naam ruimte beschikbaar heeft. Lokaal interessante gegevens worden ook lokaal in de cache opgeslagen voor snelle toegang tot de prestaties. Cloud lagen is een optionele functie per Azure File Sync server-eind punt.

> [!WARNING]
> Als u minder opslag ruimte op uw Windows Server-volume (s) hebt ingericht dan uw gegevens die op het NAS-apparaat worden gebruikt, is het gebruik van Cloud lagen verplicht. Als u Cloud lagen niet inschakelt, maakt de server geen ruimte vrij om alle bestanden op te slaan. Stel het beleid voor lagen tijdelijk in voor de migratie tot 99% beschik bare ruimte op het volume. Ga terug naar de instellingen voor de Cloud lagen nadat de migratie is voltooid en stel deze in op een meer lange termijn handig niveau.

Herhaal de stappen voor het maken van de synchronisatie groep en het toevoegen van de overeenkomende servermap als server-eind punt voor alle Azure-bestands shares/server locaties die moeten worden geconfigureerd voor synchronisatie.

Na het maken van alle server eindpunten werkt synchronisatie. U kunt een test bestand maken en het synchroniseren vanuit uw server locatie naar de verbonden Azure-bestands share (zoals beschreven in het Cloud-eind punt in de synchronisatie groep).

Beide locaties, de Server mappen en de Azure-bestands shares zijn op een andere locatie leeg en wachten op gegevens. In de volgende stap gaat u bestanden kopiëren naar de Windows-Server voor Azure File Sync om ze naar de cloud te verplaatsen. Als u Cloud lagen hebt ingeschakeld, begint de server vervolgens met het trapsgewijs delen van bestanden. Als u geen capaciteit meer hebt op de lokale volume (s).

## <a name="phase-7-robocopy"></a>Fase 7: RoboCopy

De basis benadering van de migratie is een RoboCopy van uw NAS-apparaat naar uw Windows-Server en Azure File Sync naar Azure-bestands shares.

Voer de eerste lokale kopie uit naar de doelmap van Windows Server:

* De eerste locatie op uw NAS-apparaat identificeren.
* Identificeer de overeenkomende map op de Windows-Server, waarop al Azure File Sync zijn geconfigureerd.
* De kopie starten met behulp van RoboCopy

Met de volgende RoboCopy-opdracht worden bestanden van uw NAS-opslag naar de doelmap van Windows server gekopieerd. De Windows-Server synchroniseert deze met de Azure-bestands share (s). 

Als u minder opslag ruimte op uw Windows-Server hebt ingericht dan uw bestanden op het NAS-apparaat innemen, hebt u Cloud lagen geconfigureerd. Omdat het lokale Windows Server-volume vol is, worden er [Cloud lagen](storage-sync-cloud-tiering-overview.md) in gemaakt en lagen die al zijn gesynchroniseerd. Cloud-lagen genereren voldoende ruimte om het kopiëren van het NAS-apparaat voort te zetten. Controles voor Cloud lagen worden één keer per uur uitgevoerd om te zien wat er is gesynchroniseerd en om schijf ruimte vrij te maken voor het bereiken van de beschik bare ruimte van 99% volume.
Het is mogelijk dat RoboCopy bestanden sneller verplaatst dan u naar de Cloud en laag lokaal kunt synchroniseren, waardoor er geen lokale schijf ruimte meer beschikbaar is. RoboCopy mislukt. Het wordt aanbevolen dat u de shares in een volg orde doorloopt, waardoor dat niet mogelijk is. U kunt bijvoorbeeld geen RoboCopy-taken voor alle shares tegelijk starten of alleen shares verplaatsen die passen bij de huidige hoeveelheid beschik bare ruimte op de Windows-Server, om een paar te vermelden.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Fase 8: gebruikers knippen

Wanneer u de RoboCopy-opdracht voor de eerste keer uitvoert, hebben uw gebruikers en toepassingen nog steeds toegang tot bestanden op de NAS en kunnen ze deze wijzigen. Het is mogelijk dat de RoboCopy een directory heeft verwerkt, naar de volgende gaat en vervolgens een gebruiker op de bron locatie (NAS) een bestand toevoegt, wijzigt of verwijdert dat nu niet wordt verwerkt in deze huidige RoboCopy-uitvoering. Dit gedrag is verwacht.

De eerste keer dat u het meren deel van de gegevens naar uw Windows-Server en naar de Cloud verplaatst via Azure File Sync. Het eerste exemplaar kan enige tijd in beslag nemen, afhankelijk van:

* de Download bandbreedte
* de upload bandbreedte
* de snelheid van het lokale netwerk en het aantal hoe optimaal het aantal RoboCopy-threads overeenkomt
* het aantal items (bestanden en mappen) dat moet worden verwerkt door RoboCopy en Azure File Sync

Zodra de eerste uitvoering is voltooid, voert u de opdracht opnieuw uit.

De tweede keer dat deze sneller wordt voltooid, hoeft alleen wijzigingen te worden transporteren die sinds de laatste uitvoering zijn opgetreden. Tijdens deze tweede uitvoering kunnen er nog steeds nieuwe wijzigingen worden verzameld.

Herhaal dit proces totdat u tevreden bent over de hoeveelheid tijd die nodig is voor het volt ooien van een RoboCopy voor een specifieke locatie binnen een aanvaardbaar venster voor uitval tijd.

Wanneer u de downtime in overweging neemt, moet u de gebruikers toegang tot uw op NAS gebaseerde shares verwijderen. U kunt dit doen door alle stappen die voor komen dat gebruikers de structuur en inhoud van bestanden en mappen wijzigen. Een voor beeld is om uw DFS-Namespace naar een niet-bestaande locatie te wijzen of de hoofd-Acl's op de share te wijzigen.

Voer een laatste RoboCopy-ronde uit. Er worden wijzigingen opgehaald, die mogelijk zijn gemist.
Hoe lang deze laatste stap duurt, is afhankelijk van de snelheid van de RoboCopy-scan. U kunt een schatting maken van de tijd (die gelijk is aan uw downtime) door te meten hoe lang de vorige uitvoering heeft geduurd.

Maak een share op de Windows Server-map en stel eventueel uw DFS-N-implementatie zodanig in dat deze ernaar verwijst. Zorg ervoor dat u dezelfde machtigingen op share niveau hebt ingesteld als op uw NAS SMB-share. Als u een NAS die aan een domein is toegevoegd, worden de gebruikers-Sid's automatisch vergeleken wanneer de gebruikers bestaan in Active Directory en RoboCopy kopieert bestanden en meta gegevens met volledige betrouw baarheid. Als u lokale gebruikers op uw NAS hebt gebruikt, moet u deze gebruikers opnieuw maken als lokale gebruikers van Windows Server en de bestaande Sid's van de nieuwe Windows Server-computer aan de Sid's van uw Windows Server-lokale gebruikers toewijzen.

U bent klaar met het migreren van een share/groep shares naar een gemeen schappelijke hoofdmap of volume. (Afhankelijk van uw toewijzing vanuit fase 1)

U kunt proberen enkele van deze kopieën parallel uit te voeren. Het is raadzaam om het bereik van een Azure-bestands share tegelijk te verwerken.

> [!WARNING]
> Wanneer u alle gegevens van de NAS naar de Windows-Server hebt verplaatst en uw migratie is voltooid, gaat u terug naar ***alle***  synchronisatie groepen in het Azure Portal en past u de waarde voor het percentage beschik bare ruimte op het volume voor de Cloud laag aan wat beter geschikt is voor het gebruik van de cache, bijvoorbeeld 20%. 

Het beleid voor beschik bare ruimte op het niveau van de Cloud-laag is een volume dat mogelijk meerdere server eindpunten synchroniseert. Als u vergeet de beschik bare ruimte op zelfs één server eindpunt aan te passen, blijft synchronisatie de meest beperkende regel Toep assen en wordt geprobeerd om 99% vrije schijf ruimte te houden, waardoor de lokale cache niet wordt uitgevoerd zoals u zou verwachten. Tenzij het uw doel is om alleen de naam ruimte te hebben voor een volume dat slechts zelden wordt gebruikt, worden de archiverings gegevens opgeslagen en moet u de rest van de opslag ruimte voor een ander scenario reserveren.

## <a name="troubleshoot"></a>Problemen oplossen

Het meest waarschijnlijke probleem dat u kunt uitvoeren in, is dat de RoboCopy-opdracht mislukt met *' volume vol '* aan de Windows Server-zijde. Cloud lagen reageert eenmaal elk uur om inhoud te verlaten van de lokale Windows Server-schijf, die is gesynchroniseerd. Het doel is om uw 99% beschik bare ruimte op het volume te bereiken.

Laat de voortgang van de synchronisatie en Cloud lagen vrij schijf ruimte vrijmaken. U ziet dat in Verkenner op uw Windows-Server.

Als uw Windows-Server voldoende beschik bare capaciteit heeft, wordt het probleem opgelost door de opdracht opnieuw uit te voeren. Er zijn geen onderbrekingen wanneer u deze situatie krijgt en u kunt door gaan met vertrouwen. Het ongemak van het uitvoeren van de opdracht is het enige gevolg.

Controleer de koppeling in de volgende sectie voor het oplossen van problemen met Azure File Sync.

## <a name="next-steps"></a>Volgende stappen

Er is meer informatie over Azure-bestands shares en Azure File Sync. De volgende artikelen helpen u bij het begrijpen van geavanceerde opties, aanbevolen procedures en ook voor het oplossen van problemen. Deze artikelen maken een koppeling naar de [documentatie van Azure file share](storage-files-introduction.md) .

* [Overzicht van AFS](./storage-sync-files-planning.md)
* [AFS-implementatie handleiding](./storage-how-to-create-file-share.md)
* [Problemen met AFS oplossen](storage-sync-files-troubleshoot.md)