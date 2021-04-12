---
title: Migratie van Storsimple 1200 naar Azure File Sync
description: Meer informatie over hoe u een virtueel StorSimple van 1200 Series kunt migreren naar Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8562d63bf227fff665c70674c7fe66922bce9992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882277"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Migratie van Storsimple 1200 naar Azure File Sync

De StorSimple 1200-serie is een virtueel apparaat dat wordt uitgevoerd in een on-premises Data Center. Het is mogelijk om de gegevens van dit apparaat te migreren naar een Azure File Sync omgeving. Azure File Sync is de standaard-en strategische Azure-service voor de lange termijn waarmee StorSimple-apparaten kunnen worden gemigreerd.

De StorSimple 1200-serie bereikt het [einde van de levens duur](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) in december 2022.  Het is belang rijk dat u zo snel mogelijk begint met het plannen van de migratie. Dit artikel bevat de benodigde achtergrond kennis en migraties stappen voor een geslaagde migratie naar Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Micro soft streeft ernaar klanten te helpen bij hun migratie. E-mail AzureFilesMigration@microsoft . com voor een aangepast migratie plan en voor hulp tijdens de migratie.

Azure File Sync is een micro soft-Cloud service, gebaseerd op twee hoofd onderdelen:

* Bestands synchronisatie en Cloud lagen.
* Bestands shares als systeem eigen opslag in azure, waartoe toegang kan worden verkregen via meerdere protocollen zoals SMB en de REST van het bestand. Een Azure-bestands share is vergelijkbaar met een bestands share op een Windows-Server, die u als een netwerk station zelf kunt koppelen. Het ondersteunt belang rijke aspecten van bestands kwaliteit zoals kenmerken, machtigingen en tijds tempels. In tegens telling tot met StorSimple is geen toepassing/service vereist voor het interpreteren van de bestanden en mappen die zijn opgeslagen in de Cloud. De ideale en meest flexibele benadering om bestands Server gegevens voor algemene doel einden en bepaalde toepassings gegevens in de Cloud op te slaan.

Dit artikel is gericht op de migratie stappen. Als u vóór de migratie meer wilt weten over Azure File Sync, raden we u aan om de volgende artikelen te lezen:

* [Azure File Sync-overzicht](./storage-sync-files-planning.md "Overzicht")
* [Azure File Sync-implementatie handleiding](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migratiedoelen

Het doel is om de integriteit van de productie gegevens te waarborgen en de beschik baarheid te garanderen. Ten laatste moet de downtime tot een minimum worden beperkt, zodat deze kan worden aangepast aan of slechts een beetje meer regel matig onderhouds Vensters kan hebben.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200 migratie pad naar Azure File Sync

Er is een lokale Windows-Server vereist om een Azure File Sync-agent uit te voeren. De Windows-Server kan mini maal een 2012R2-server zijn, maar is in het ideale geval een Windows Server 2019.

Er zijn talloze alternatieve migratie paden en er zou te lang van een artikel kunnen worden gemaakt om ze allemaal te documenteren en te illustreren waarom ze Risico's of nadelen hebben ten opzichte van de route die we aanbevelen als best practice in dit artikel.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Overzicht van migratie route van de stappen verderop in dit artikel.":::

In de vorige afbeelding ziet u de stappen die overeenkomen met de secties in dit artikel.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Stap 1: uw on-premises Windows-Server en-opslag inrichten

1. Maak een Windows Server 2019-met een minimale 2012R2-als een virtuele machine of fysieke server. Een failover-cluster van Windows Server wordt ook ondersteund.
2. Het inrichten of toevoegen van direct gekoppelde opslag (DAS in vergelijking met NAS), wat niet wordt ondersteund. De grootte van de Windows Server-opslag moet gelijk zijn aan of groter zijn dan de grootte van de beschik bare capaciteit van uw virtuele-StorSimple 1200-apparaat.

### <a name="step-2-configure-your-windows-server-storage"></a>Stap 2: uw Windows Server-opslag configureren

In deze stap wijst u uw StorSimple-opslag structuur (volumes en shares) toe aan de opslag structuur van uw Windows-Server.
Als u van plan bent om wijzigingen aan te brengen in uw opslag structuur, zoals het aantal volumes, de koppeling van gegevens mappen met volumes of de structuur van de submap boven of onder uw huidige SMB/NFS-shares, is het tijd om deze wijzigingen in overweging te nemen.
Het wijzigen van de structuur van uw bestanden en mappen nadat Azure File Sync is geconfigureerd, is omslachtig en moet worden vermeden.
In dit artikel wordt ervan uitgegaan dat u 1:1 toewijst, dus u moet uw toewijzing wijzigen in overweging wanneer u de stappen in dit artikel volgt.

* Geen van uw productie gegevens moet eindigen op het Windows Server-systeem volume. Cloud lagen worden niet ondersteund op systeem volumes. Deze functie is echter vereist voor de migratie en doorlopende bewerkingen als een StorSimple vervanging.
* Richt hetzelfde aantal volumes in op uw Windows-Server als op uw virtuele StorSimple 1200-apparaat.
* Configureer alle Windows Server-functies,-onderdelen en-instellingen die u nodig hebt. U wordt aangeraden Windows Server-updates aan te melden om uw besturings systeem veilig en up-to-date te houden. We raden u ook aan Microsoft Update aan te melden om micro soft-toepassingen up-to-date te houden, inclusief de Azure File Sync-agent.
* Configureer geen mappen of shares voordat u de volgende stappen leest.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Stap 3: de eerste Azure File Sync Cloud resource implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Stap 4: overeenkomen met uw lokale volume en mapstructuur met Azure File Sync en Azure file share-resources

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Stap 5: Azure-bestands shares inrichten

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Stap 6: Windows Server-doel mappen configureren

In de vorige stappen hebt u alle aspecten gezien die de onderdelen van uw synchronisatie Topologies bepalen. Het is nu tijd om de server voor te bereiden op het ontvangen van bestanden voor upload.

Maak **alle** mappen die worden gesynchroniseerd met de eigen Azure-bestands share.
Het is belang rijk dat u de mapstructuur volgt die u eerder hebt gedocumenteerd. Als u bijvoorbeeld hebt besloten om meerdere lokale SMB-shares in één Azure-bestands share te synchroniseren, moet u deze onder een algemene hoofdmap op het volume plaatsen. Maak deze hoofdmap voor het doel op het volume nu.

Het aantal Azure-bestands shares dat u hebt ingericht, moet overeenkomen met het aantal mappen dat u in deze stap hebt gemaakt en het aantal volumes dat u op het hoofd niveau wilt synchroniseren.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Stap 7: de Azure File Sync-agent implementeren

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Stap 8: synchronisatie configureren

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Zorg ervoor dat u Cloud lagen inschakelt.** Dit is vereist als uw lokale server onvoldoende ruimte heeft voor het opslaan van de totale grootte van uw gegevens in de Cloud opslag van StorSimple. Stel het beleid voor lagen tijdelijk in voor de migratie tot 99% beschik bare ruimte op het volume.

Herhaal de stappen voor het maken van de synchronisatie groep en het toevoegen van de overeenkomende servermap als server-eind punt voor alle Azure-bestands shares/server locaties die moeten worden geconfigureerd voor synchronisatie.

### <a name="step-9-copy-your-files"></a>Stap 9: uw bestanden kopiëren

De basis benadering van de migratie is een RoboCopy van uw virtuele StorSimple-apparaat naar uw Windows-Server en Azure File Sync naar Azure-bestands shares.

Voer de eerste lokale kopie uit naar de doelmap van Windows Server:

* Bepaal de eerste locatie op het virtuele-StorSimple-apparaat.
* Identificeer de overeenkomende map op de Windows-Server, waarop al Azure File Sync zijn geconfigureerd.
* De kopie starten met behulp van RoboCopy

Met de volgende RoboCopy-opdracht worden bestanden van uw StorSimple Azure-opslag naar uw lokale StorSimple teruggehaald en vervolgens verplaatst naar de doelmap van Windows Server. De Windows-Server synchroniseert deze met de Azure-bestands share (s). Omdat het lokale Windows Server-volume vol is, worden er Cloud lagen in gemaakt en lagen die al zijn gesynchroniseerd. Met Cloud lagen wordt voldoende ruimte gegenereerd om de kopie van het virtuele StorSimple-apparaat te hervatten. Controles voor Cloud lagen worden één keer per uur uitgevoerd om te zien wat er is gesynchroniseerd en om schijf ruimte vrij te maken voor het bereiken van de beschik bare ruimte van 99% volume.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Achtergrondbitmap

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Hiermee kan RoboCopy meerdere threads uitvoeren. De standaard waarde is 8, maximum is 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Hiermee wordt de status van de uitvoer naar een logboek bestand opgeslagen als UNICODE (het bestaande logboek wordt overschreven).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Uitvoer naar console venster. Wordt gebruikt in combi natie met uitvoer naar een logboek bestand.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Voert RoboCopy uit in dezelfde modus als een back-uptoepassing zou gebruiken. Hiermee kan RoboCopy bestanden verplaatsen waarnaar de huidige gebruiker geen machtigingen heeft.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Hiermee kan deze RoboCopy-opdracht verschillende keren worden uitgevoerd, opeenvolgend op hetzelfde doel/dezelfde bestemming. Hiermee wordt aangegeven wat er eerder is gekopieerd en wordt deze wegge laten. Alleen wijzigingen, toevoegingen en '*verwijderingen*' worden verwerkt, die zijn opgetreden sinds de laatste uitvoering. Als de opdracht nog niet eerder is uitgevoerd, wordt er niets wegge laten. Dit is een uitstekende optie voor bron locaties die nog steeds worden gebruikt en worden gewijzigd.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      betrouw baarheid van het bestand kopiëren (standaard is/COPY: DAT), kopieer vlaggen: D = gegevens, A = kenmerken, T = tijds tempels, S = Security = NTFS Acl's, O = eigenaargegevens, U = controle-informatie
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      ALLE bestands gegevens kopiëren (gelijk aan/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      betrouw baarheid voor het kopiëren van mappen (standaard is/DCOPY: DA), kopieer vlaggen: D = gegevens, A = kenmerken, T = tijds tempels
   :::column-end:::
:::row-end:::

Wanneer u de RoboCopy-opdracht voor de eerste keer uitvoert, hebben uw gebruikers en toepassingen nog steeds toegang tot de StorSimple-bestanden en-mappen. Dit kan mogelijk worden gewijzigd. Het is mogelijk dat de RoboCopy een directory heeft verwerkt, naar de volgende gaat en vervolgens een gebruiker op de bron locatie (StorSimple) een bestand toevoegt, wijzigt of verwijdert dat nu niet wordt verwerkt in deze huidige RoboCopy-uitvoering. Dat is prima.

De eerste keer dat u het meren deel van de gegevens naar on-premises, naar uw Windows-Server en back-up naar de Cloud verplaatst via Azure File Sync. Dit kan enige tijd duren, afhankelijk van:

* de Download bandbreedte
* de intrek snelheid van de Cloud service StorSimple
* de upload bandbreedte
* het aantal items (bestanden en mappen) dat door beide services moet worden verwerkt

Zodra de eerste uitvoering is voltooid, voert u de opdracht opnieuw uit.

De tweede keer dat deze sneller wordt voltooid, hoeft alleen wijzigingen te worden transporteren die sinds de laatste uitvoering zijn opgetreden. Deze wijzigingen zijn waarschijnlijk al lokaal voor de StorSimple, omdat ze recent zijn. Dit verkort de tijd, omdat de nood zaak voor terughalen vanuit de Cloud wordt verminderd. Tijdens deze tweede uitvoering kunnen er nog steeds nieuwe wijzigingen worden verzameld.

Herhaal dit proces totdat u er zeker van bent dat de hoeveelheid tijd die nodig is om te volt ooien, een aanvaard bare downtime is.

Wanneer u een acceptabele downtime hebt, kunt u de locatie van de StorSimple offline halen. u kunt de SMB-share bijvoorbeeld verwijderen zodat geen enkele gebruiker toegang heeft tot de map of een andere geschikte stap kan uitvoeren, waardoor inhoud in deze map op StorSimple niet kan worden gewijzigd.

Voer een laatste RoboCopy-ronde uit. Hiermee worden alle wijzigingen opgehaald, die mogelijk zijn gemist.
Hoe lang deze laatste stap duurt, is afhankelijk van de snelheid van de RoboCopy-scan. U kunt een schatting maken van de tijd (die gelijk is aan uw downtime) door te meten hoe lang de vorige uitvoering heeft geduurd.

Maak een share op de Windows Server-map en stel eventueel uw DFS-N-implementatie zodanig in dat deze ernaar verwijst. Zorg ervoor dat u dezelfde machtigingen op share niveau hebt ingesteld als op uw StorSimple SMB-share.

U bent klaar met het migreren van een share/groep shares naar een gemeen schappelijke hoofdmap of volume. (Afhankelijk van wat u hebt toegewezen en heeft besloten dat nodig is om naar dezelfde Azure-bestands share te gaan.)

U kunt proberen enkele van deze kopieën parallel uit te voeren. Het is raadzaam om het bereik van een Azure-bestands share tegelijk te verwerken.

> [!WARNING]
> Wanneer u alle gegevens van de StorSimple naar de Windows-Server hebt verplaatst en uw migratie is voltooid, gaat u terug naar ***alle***  synchronisatie groepen in de Azure Portal en past u de waarde voor het percentage beschik bare ruimte in de Cloud laag aan om iets beter geschikt te maken voor het gebruik van de cache, bijvoorbeeld 20%. 

Het beleid voor beschik bare ruimte op het niveau van de Cloud-laag is een volume dat mogelijk meerdere server eindpunten synchroniseert. Als u vergeet de beschik bare ruimte op zelfs één server eindpunt aan te passen, blijft synchronisatie de meest beperkende regel Toep assen en wordt geprobeerd om 99% vrije schijf ruimte te houden, waardoor de lokale cache niet wordt uitgevoerd zoals u zou verwachten. Tenzij het uw doel is om alleen de naam ruimte te hebben voor een volume dat slechts zelden wordt gebruikt, worden er gegevens gearchiveerd.

## <a name="troubleshoot"></a>Problemen oplossen

Het meest waarschijnlijke probleem dat u kunt uitvoeren in, is dat de RoboCopy-opdracht mislukt met *' volume vol '* aan de Windows Server-zijde. Als dat het geval is, is de download snelheid waarschijnlijk beter dan uw Upload snelheid. Cloud lagen reageert eenmaal elk uur om inhoud te verlaten van de lokale Windows Server-schijf, die is gesynchroniseerd.

Laat de voortgang van de synchronisatie en Cloud lagen vrij schijf ruimte vrijmaken. U ziet dat in Verkenner op uw Windows-Server.

Als uw Windows-Server voldoende beschik bare capaciteit heeft, wordt het probleem opgelost door de opdracht opnieuw uit te voeren. Er zijn geen onderbrekingen wanneer u deze situatie krijgt en u kunt door gaan met vertrouwen. Het ongemak van het uitvoeren van de opdracht is het enige gevolg.

U kunt ook andere Azure File Sync problemen uitvoeren.
Als dat het geval is, raadpleegt u de **koppeling Azure file sync probleemoplossings gids** voor meer informatie.

## <a name="relevant-links"></a>Relevante koppelingen

Migratie-inhoud:

* [Migratie handleiding voor de StorSimple 8000-serie](storage-files-migration-storsimple-8000.md)

Azure File Sync inhoud:

* [Overzicht van AFS](./storage-sync-files-planning.md)
* [AFS-implementatie handleiding](./storage-how-to-create-file-share.md)
* [Problemen met AFS oplossen](storage-sync-files-troubleshoot.md)