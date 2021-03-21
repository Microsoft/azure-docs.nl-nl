---
title: Linux-migratie naar Azure File Sync
description: Meer informatie over het migreren van bestanden van een locatie van een Linux-server naar een hybride Cloud implementatie met Azure File Sync en Azure-bestands shares.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: ff26318cafdf493579961fc718643f831ae9efeb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564251"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migreren van Linux naar een hybride Cloud implementatie met Azure File Sync

Dit migratie artikel is een van de verschillende sleutel woorden NFS en Azure File Sync. Controleer of dit artikel van toepassing is op uw scenario:

> [!div class="checklist"]
> * Gegevens Bron: NAS (Network Attached Storage)
> * Migratie route: Linux-server met SAMBA &rArr; Windows Server 2012R2 of later &rArr; synchroniseren met Azure-bestands shares
> * On-premises bestanden opslaan in cache: Ja, het uiteindelijke doel is een Azure File Sync-implementatie.

Als uw scenario anders is, bekijkt u de [tabel met migratie handleidingen](storage-files-migration-overview.md#migration-guides).

Azure File Sync werkt op Windows Server-exemplaren met Direct Attached Storage (DAS). Het biedt geen ondersteuning voor synchronisatie van en naar Linux-clients of een externe SMB-share (Server Message Block) of NFS-shares (Network File System).

Als gevolg hiervan is het transformeren van uw bestands Services naar een hybride implementatie een migratie naar Windows Server nodig. Dit artikel begeleidt u bij het plannen en uitvoeren van een dergelijke migratie.

## <a name="migration-goals"></a>Migratiedoelen

Het doel is om de shares die u hebt op uw Linux Samba-server te verplaatsen naar een Windows Server-exemplaar. Gebruik vervolgens Azure File Sync voor een hybride Cloud implementatie. Deze migratie moet worden uitgevoerd op een manier die de integriteit van de productie gegevens en beschik baarheid tijdens de migratie waarborgt. Ten laatste moet de downtime tot een minimum worden beperkt, zodat deze kan worden aangepast aan of slechts een beetje meer regel matig onderhouds Vensters kan hebben.

## <a name="migration-overview"></a>Migratieoverzicht

Zoals vermeld in het [overzichts artikel](storage-files-migration-overview.md)over Azure files migratie, met het juiste Kopieer programma en de aanpak is belang rijk. Uw Linux Samba-server maakt SMB-shares rechtstreeks beschikbaar op uw lokale netwerk. Robocopy, ingebouwd in Windows Server, is de beste manier om uw bestanden in dit migratie scenario te verplaatsen.

Als u niet werkt met Samba op uw Linux-server en liever mappen wilt migreren naar een hybride implementatie op Windows Server, kunt u Linux-Kopieer hulpprogramma's gebruiken in plaats van Robocopy. Houd rekening met de betrouw baarheid van uw Kopieer programma. Raadpleeg de [sectie basis beginselen](storage-files-migration-overview.md#migration-basics) van migratie in het artikel migratie overzicht voor meer informatie over wat u moet zoeken in een kopieer programma.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: bepalen hoeveel Azure-bestands shares u nodig hebt

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>Fase 2: een geschikt Windows Server-exemplaar op locatie inrichten

* Maak een exemplaar van Windows Server 2019 als een virtuele machine of fysieke server. Windows Server 2012 R2 is de minimale vereiste. Een failover-cluster van Windows Server wordt ook ondersteund.
* Direct Attached Storage (DAS) inrichten of toevoegen. Network Attached Storage (NAS) wordt niet ondersteund.

  De hoeveelheid opslag ruimte die u inricht kan kleiner zijn dan wat u momenteel gebruikt op uw Linux Samba-server, als u de Azure File Sync [Cloud-laag](storage-sync-cloud-tiering-overview.md) functie gebruikt. 

De hoeveelheid opslag ruimte die u hebt ingericht, kan kleiner zijn dan wat u momenteel op uw Linux Samba-server gebruikt. Voor deze configuratie optie moet u ook gebruikmaken van de functie voor het maken van [Cloud lagen](storage-sync-cloud-tiering-overview.md) in azure file sync. Wanneer u echter uw bestanden vanuit de grotere Linux Samba-server ruimte naar het kleinere Windows Server-volume in een latere fase kopieert, moet u in batches werken:

  1. Verplaats een reeks bestanden die op de schijf passen.
  2. Bestands synchronisatie en Cloud lagen laten deel nemen.
  3. Wanneer er meer vrije ruimte op het volume wordt gemaakt, gaat u door met de volgende batch bestanden. U kunt ook de RoboCopy-opdracht in het komende [Robocopy-gedeelte](#phase-7-robocopy) controleren voor gebruik van de nieuwe `/LFSM` Switch. Met `/LFSM` kunt u uw Robocopy-taken aanzienlijk vereenvoudigen, maar dit is niet compatibel met een aantal andere Robocopy-switches die u mogelijk afhankelijk maakt.
    
  U kunt deze batch aanpak vermijden door de gelijkwaardige ruimte in te richten op het Windows Server-exemplaar dat uw bestanden op de Linux Samba-server innemen. Overweeg om ontdubbeling in Windows in te scha kelen. Als u deze hoge hoeveelheid opslag ruimte niet permanent wilt door voeren voor uw Windows Server-exemplaar, kunt u de grootte van het volume beperken na de migratie en voordat u het beleid voor Cloud lagen hebt aangepast. Hiermee maakt u een kleinere on-premises cache van uw Azure-bestands shares.

De resource configuratie (Compute en RAM) van het Windows Server-exemplaar dat u implementeert, is vooral afhankelijk van het aantal items (bestanden en mappen) dat u wilt synchroniseren. U kunt het beste een configuratie met hogere prestaties uitvoeren als u problemen ondervindt.

[Meer informatie over hoe u het formaat van een Windows Server-exemplaar kunt aanpassen op basis van het aantal items (bestanden en mappen) dat u wilt synchroniseren.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Het eerder gekoppelde artikel bevat een tabel met een bereik voor Server geheugen (RAM). U kunt naar het kleinere nummer voor uw server richten, maar u verwacht dat de initiële synchronisatie aanzienlijk meer tijd kan duren.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: de Azure File Sync Cloud resource implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: Azure storage-resources implementeren

In deze fase raadpleegt u de toewijzings tabel uit fase 1 en gebruikt u deze om het juiste aantal Azure-opslag accounts en bestands shares in te richten.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: de Azure File Sync-agent implementeren

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>Fase 6: Azure File Sync configureren voor de implementatie van Windows Server

Uw geregistreerde on-premises Windows Server-exemplaar moet gereed zijn en zijn verbonden met internet voor dit proces.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Cloud lagen is de Azure File Sync-functie waarmee de lokale server minder opslag capaciteit kan hebben dan is opgeslagen in de Cloud, maar de volledige naam ruimte beschikbaar is. Lokaal interessante gegevens worden ook lokaal in de cache opgeslagen voor snelle toegang tot de prestaties. Cloud lagen is een optionele functie voor elk Azure File Sync server-eind punt.

> [!WARNING]
> Als u minder opslag ruimte op uw Windows Server-volumes hebt ingericht dan uw gegevens die op de Linux Samba-server worden gebruikt, is Cloud lagen niet verplicht. Als u Cloud lagen niet inschakelt, maakt de server geen ruimte vrij om alle bestanden op te slaan. Stel het beleid voor lagen, tijdelijk voor de migratie, in op 99 procent beschik bare ruimte voor een volume. Ga terug naar de instellingen voor de Cloud lagen nadat de migratie is voltooid en stel het beleid in op een meer nuttig niveau voor de lange termijn.

Herhaal de stappen voor het maken van de synchronisatie groep en de toevoeging van de overeenkomende servermap als server-eind punt voor alle Azure-bestands shares en server locaties die moeten worden geconfigureerd voor synchronisatie.

Na het maken van alle server eindpunten werkt synchronisatie. U kunt een test bestand maken en het synchroniseren vanuit uw server locatie naar de verbonden Azure-bestands share (zoals beschreven in het Cloud-eind punt in de synchronisatie groep).

Beide locaties, de Server mappen en de Azure-bestands shares, zijn niet leeg en wachten op gegevens. In de volgende stap begint u met het kopiëren van bestanden naar het Windows Server-exemplaar voor Azure File Sync om ze naar de cloud te verplaatsen. Als u Cloud lagen hebt ingeschakeld, begint de server vervolgens met het trapsgewijs delen van bestanden als er geen capaciteit meer is op de lokale volumes.

## <a name="phase-7-robocopy"></a>Fase 7: Robocopy

De basis benadering van de migratie is het gebruik van Robocopy voor het kopiëren van bestanden en het gebruik van Azure File Sync om de synchronisatie uit te voeren.

Voer de eerste lokale kopie uit naar de doelmap van Windows Server:

1. Bepaal de eerste locatie op de Linux Samba-server.
1. Identificeer de overeenkomende map op het Windows Server-exemplaar waarop al Azure File Sync is geconfigureerd.
1. Start de kopie met behulp van Robocopy.

Met de volgende Robocopy-opdracht worden bestanden van de opslag ruimte van uw Linux Samba-server gekopieerd naar de doelmap van uw Windows-Server. Windows Server synchroniseert deze met de Azure-bestands shares. 

Als u minder opslag ruimte op uw Windows Server-exemplaar hebt ingericht dan uw bestanden op de Linux Samba-server innemen, hebt u Cloud lagen geconfigureerd. Wanneer het lokale Windows Server-volume vol raakt, worden [Cloud lagen](storage-sync-cloud-tiering-overview.md) gestart en lagen die al zijn gesynchroniseerd. Cloud-lagen genereren voldoende ruimte om de kopie van de Linux Samba-server voort te zetten. Controles voor Cloud lagen worden één keer per uur uitgevoerd om te zien wat er is gesynchroniseerd en om schijf ruimte vrij te maken om het beleid van 99 procent beschik bare ruimte voor een volume te bereiken.

Het is mogelijk dat Robocopy sneller bestanden verplaatst dan u naar de Cloud en laag kunt synchroniseren, waardoor er geen lokale schijf ruimte meer beschikbaar is. Robocopy mislukt. U wordt aangeraden de shares te door lopen in een reeks die het probleem voor komt. Denk bijvoorbeeld aan dat u geen Robocopy-taken voor alle shares tegelijk kunt starten. Of overweeg het verplaatsen van shares die passen bij de huidige hoeveelheid beschik bare ruimte op het Windows Server-exemplaar. Als uw Robocopy-taak mislukt, kunt u de opdracht altijd opnieuw uitvoeren, mits u de volgende optie voor spie gelen/opschonen gebruikt:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Fase 8: gebruikers knippen

Wanneer u de Robocopy-opdracht voor de eerste keer uitvoert, hebben uw gebruikers en toepassingen nog steeds toegang tot bestanden op de Linux Samba-server en kunnen ze worden gewijzigd. Het is mogelijk dat Robocopy een map heeft verwerkt en verplaatst naar de volgende. vervolgens voegt een gebruiker in de bron locatie (Linux) een bestand toe, wijzigt of verwijdert dat nu niet wordt verwerkt in deze huidige Robocopy-uitvoering. Dit gedrag is verwacht.

De eerste uitvoering is het verplaatsen van het bulk deel van de gegevens naar uw Windows Server-exemplaar en naar de Cloud via Azure File Sync. Het eerste exemplaar kan enige tijd in beslag nemen, afhankelijk van:

* De Download bandbreedte.
* De upload bandbreedte.
* De snelheid van het lokale netwerk en het aantal hoe optimaal het aantal Robocopy-threads overeenkomt met het.
* Het aantal items (bestanden en mappen) die Robocopy en Azure File Sync moeten verwerken.

Nadat de eerste uitvoering is voltooid, voert u de opdracht opnieuw uit.

Het is sneller dan de tweede keer, omdat er alleen wijzigingen moeten worden getransporteerd die zijn opgetreden sinds de laatste uitvoering. In deze tweede periode kunt u nog steeds nieuwe wijzigingen uitvoeren.

Herhaal dit proces totdat u tevreden bent over de hoeveelheid tijd die nodig is voor het volt ooien van een Robocopy-bewerking voor een specifieke locatie binnen een aanvaardbaar venster voor uitval tijd.

Wanneer u de bewaarde downtime overweegt en u bent voor bereid om de Linux-locatie offline te halen, kunt u de Acl's op de hoofdmap van de share wijzigen zodat gebruikers geen toegang meer tot de locatie hebben. U kunt ook een andere passende stap ondernemen waarmee wordt voor komen dat inhoud in deze map op uw Linux-server wordt gewijzigd.

Voer een laatste Robocopy-ronde uit. Alle wijzigingen die mogelijk zijn gemist, worden opgehaald. Hoe lang deze laatste stap duurt, is afhankelijk van de snelheid van de Robocopy-scan. U kunt een schatting maken van de tijd (die gelijk is aan uw downtime) door te meten hoe lang de vorige uitvoering heeft geduurd.

Maak een share op de Windows Server-map en stel eventueel uw DFS-N-implementatie zodanig in dat deze ernaar verwijst. Zorg ervoor dat u dezelfde machtigingen op share niveau hebt ingesteld als voor de SMB-shares van de Linux Samba-server. Als u lokale gebruikers hebt gebruikt op uw Linux Samba-server, moet u deze gebruikers opnieuw maken als lokale Windows Server-gebruikers. U moet ook de bestaande Sid's die Robocopy naar uw Windows Server-exemplaar verplaatst, toewijzen aan de Sid's van uw nieuwe lokale Windows Server-gebruikers. Als u Active Directory accounts en toegangs beheer lijsten hebt gebruikt, worden deze door Robocopy verplaatst, en hoeft u geen verdere actie te ondernemen.

U bent klaar met het migreren van een share of een groep shares naar een gemeen schappelijke hoofdmap of volume (afhankelijk van uw toewijzing vanuit fase 1).

U kunt proberen enkele van deze kopieën parallel uit te voeren. Het is raadzaam om het bereik van een Azure-bestands share tegelijk te verwerken.

> [!WARNING]
> Nadat u alle gegevens van uw Linux Samba-server naar het Windows Server-exemplaar hebt verplaatst en uw migratie is voltooid, gaat u terug naar *alle*  synchronisatie groepen in de Azure Portal. Pas het percentage beschik bare ruimte voor het volume van de Cloud-laag aan wat beter geschikt is voor het gebruik van de cache, zoals 20 procent. 

Het beleid voor beschik bare ruimte in het volume voor de Cloud-laag wordt toegepast op een volume niveau waarbij mogelijk meerdere server eindpunten worden gesynchroniseerd. Als u vergeet de beschik bare ruimte op zelfs één server eindpunt aan te passen, blijft de synchronisatie de meest beperkende regel Toep assen en wordt geprobeerd om 99 procent vrije schijf ruimte te houden. De lokale cache wordt mogelijk niet uitgevoerd zoals u verwacht. De prestaties kunnen acceptabel zijn als uw doel is om de naam ruimte te hebben voor een volume dat alleen zelden gebruikte archiverings gegevens bevat, en u de rest van de opslag ruimte voor een ander scenario wilt reserveren.

## <a name="troubleshoot"></a>Problemen oplossen

Het meest voorkomende probleem is dat de Robocopy-opdracht mislukt met een **vol volume** aan de Windows Server-zijde. Cloud lagen reageert eenmaal elk uur om inhoud te verlaten van de lokale Windows Server-schijf die is gesynchroniseerd. Het doel is om de beschik bare ruimte van 99 procent op het volume te bereiken.

Laat de voortgang van de synchronisatie en Cloud lagen vrij schijf ruimte vrijmaken. U ziet dat in Verkenner op Windows Server.

Wanneer uw Windows Server-exemplaar voldoende beschik bare capaciteit heeft, wordt het probleem opgelost door de opdracht opnieuw uit te voeren. Er zijn geen onderbrekingen wanneer u deze situatie krijgt en u kunt door gaan met vertrouwen. Het ongemak van het uitvoeren van de opdracht is het enige gevolg.

Controleer de koppeling in de volgende sectie voor het oplossen van problemen met Azure File Sync.

## <a name="next-steps"></a>Volgende stappen

Er is meer informatie over Azure-bestands shares en Azure File Sync. De volgende artikelen bevatten geavanceerde opties, aanbevolen procedures en hulp bij het oplossen van problemen. Deze artikelen maken een koppeling naar de [documentatie van Azure file share](storage-files-introduction.md) .

* [Overzicht van Azure File Sync](./storage-sync-files-planning.md)
* [Implementatie handleiding Azure File Sync](./storage-how-to-create-file-share.md)
* [Problemen met Azure File Sync oplossen](storage-sync-files-troubleshoot.md)