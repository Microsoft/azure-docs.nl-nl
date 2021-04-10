---
title: Principes van herstel na nood gevallen en voor bereiding op SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Principes van herstel na nood gevallen en voor bereiding op SAP HANA op Azure (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: babd7c1dcae9d83af1f6c41e756b663d92d6d486
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677125"
---
# <a name="disaster-recovery-principles"></a>Principes voor herstel na nood gevallen

HANA grote instanties bieden een functionaliteit voor herstel na nood gevallen tussen HANA grote instantie tempels in verschillende Azure-regio's. Als u bijvoorbeeld HANA grote instantie-eenheden implementeert in de regio vs West van Azure, kunt u de HANA grote instantie-eenheden in de regio VS Oost gebruiken als eenheden voor nood herstel. Zoals eerder vermeld, wordt herstel na nood gevallen niet automatisch geconfigureerd, omdat u moet betalen voor een andere HANA grote instantie-eenheid in de DR-regio. De installatie van nood herstel kan worden uitgebreid en kan worden uitgebreid met scale-out Setup. 

In de scenario's die tot nu toe zijn geïmplementeerd, gebruiken klanten de eenheid in de DR-regio voor het uitvoeren van niet-productie systemen die gebruikmaken van een geïnstalleerd HANA-exemplaar. De HANA-eenheid voor grote instanties moet van dezelfde SKU zijn als de SKU die voor productie doeleinden wordt gebruikt. De volgende afbeelding laat zien hoe de schijf configuratie tussen de server eenheid in de Azure-productie regio en de nood herstel regio er als volgt uitziet:

![Configuratie van DR Setup vanaf het schijf punt van de weer gave](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Zoals in deze overzichts afbeelding wordt weer gegeven, moet u vervolgens een tweede set schijf volumes best Ellen. De doel schijf volumes hebben dezelfde grootte als de productie volumes voor het productie-exemplaar in de nood herstel eenheden. Deze schijf volumes zijn gekoppeld aan de server eenheid HANA-grote instanties op de site voor nood herstel. De volgende volumes worden gerepliceerd van het productie gebied naar de DR-site:

- /hana/data
- /hana/logbackups 
- /Hana/Shared (inclusief/usr/sap)

Het/Hana/log-volume wordt niet gerepliceerd, omdat het SAP HANA transactie logboek niet nodig is om de herstel bewerking uit te voeren vanaf die volumes. 

De functionaliteit van de functie voor herstel na nood gevallen is de functionaliteit voor opslag replicatie die wordt aangeboden door de HANA-infra structuur voor grote instanties. De functionaliteit die wordt gebruikt voor de opslag zijde is geen constante stroom wijzigingen die op asynchrone wijze worden gerepliceerd als er wijzigingen optreden op het opslag volume. In plaats daarvan is het een mechanisme dat afhankelijk is van het feit dat moment opnamen van deze volumes regel matig worden gemaakt. De verschillen tussen een al gerepliceerde moment opname en een nieuwe moment opname die nog niet is gerepliceerd, worden vervolgens overgebracht naar de site voor herstel na nood geval naar de doel schijf volumes.  Deze moment opnamen worden opgeslagen op de volumes en, als er een failover voor herstel na nood geval is, moet op die volumes worden hersteld.  

De eerste overdracht van de volledige gegevens van het volume moet liggen voordat de hoeveelheid gegevens kleiner wordt dan de verschillen tussen moment opnamen. Als gevolg hiervan bevatten de volumes op de DR-site elk een van de volume momentopnamen die zijn uitgevoerd op de productie site. Uiteindelijk kunt u dat DR-systeem gebruiken om een eerdere status te verkrijgen om verloren gegevens te herstellen, zonder het productie systeem terug te draaien.

Als er sprake is van een MCOD-implementatie met meerdere onafhankelijke SAP HANA exemplaren op één HANA grote exemplaar-eenheid, wordt ervan uitgegaan dat alle SAP HANA exemplaren worden opgehaald die worden gerepliceerd naar de DR-zijde.

In gevallen waarbij u HANA-systeem replicatie gebruikt als functionaliteit voor hoge Beschik baarheid in uw productie site en replicatie op basis van opslag gebruikt voor de DR-site, worden de volumes van beide knoop punten van de primaire site naar het DR-exemplaar gerepliceerd. U moet extra opslag ruimte (dezelfde grootte van het primaire knoop punt) kopen op de DR-site om de replicatie van zowel de primaire als de secundaire te kunnen uitvoeren op de DR. 



>[!NOTE]
>De replicatie functionaliteit van de HANA-opslag voor grote instanties is mirroring en replicatie van opslag momentopnamen. Als u geen opslag momentopnamen uitvoert die zijn geïntroduceerd in de sectie back-up en herstel van dit artikel, kunnen er geen replicatie naar de site voor herstel na nood geval zijn. Het uitvoeren van een opslag momentopname is een vereiste voor de opslag replicatie naar de site voor nood herstel.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Voor bereiding van het scenario voor herstel na nood geval
In dit scenario hebt u een productie systeem dat wordt uitgevoerd op HANA grote instanties in de Azure-regio productie. Voor de volgende stappen wordt ervan uitgegaan dat de SID van het HANA-systeem ' PRD ' is en dat er een niet-productie systeem wordt uitgevoerd op HANA grote instanties in de Azure-regio. Voor dit laatste gaan we ervan uit dat de SID ' TST ' is. In de volgende afbeelding ziet u deze configuratie:

![Start van DR Setup](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Als het Server exemplaar nog niet is besteld met de extra opslag volumeset, SAP HANA op Azure Service Management de extra set volumes als doel voor de productie replica koppelen aan de HANA-eenheid voor grote instanties waarop u de TST HANA-instantie uitvoert. Hiervoor moet u de SID van uw productie HANA-exemplaar opgeven. Nadat SAP HANA op Azure Service Management de bijlage van die volumes bevestigt, moet u deze volumes koppelen aan de HANA-eenheid voor grote instanties.

![De volgende stap van DR Setup](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

De volgende stap is dat u het tweede SAP HANA-exemplaar installeert op de HANA-eenheid voor grote instanties in de Azure-regio voor nood herstel, waar u de TST HANA-instantie uitvoert. Het onlangs geïnstalleerde SAP HANA-exemplaar moet dezelfde SID hebben. De gebruikers die zijn gemaakt, moeten dezelfde UID en groeps-ID hebben die het productie-exemplaar heeft. Lees [back-ups maken en herstellen](hana-backup-restore.md) voor meer informatie. Als de installatie is voltooid, moet u het volgende doen:

- Voer stap 2 uit van de voor bereiding van de opslag momentopname die wordt beschreven in [back-up en herstel](hana-backup-restore.md).
- Maak een open bare sleutel voor de DR-eenheid van HANA grote instantie-eenheid als u dit nog niet hebt gedaan. Zie stap 3 van de voor bereiding van de opslag momentopname die wordt beschreven in [back-up en herstel](hana-backup-restore.md).
- Onderhoud de *HANABackupCustomerDetails.txt* met het nieuwe Hana-exemplaar en test of de connectiviteit in Storage goed werkt.  
- Stop de zojuist geïnstalleerde SAP HANA-instantie op de HANA-eenheid voor grote instanties in de Azure-regio.
- Ontkoppel deze PRD-volumes en neem contact op met SAP HANA op Azure Service Management. De volumes kunnen niet worden gekoppeld aan de eenheid omdat deze niet toegankelijk zijn terwijl het doel van de opslag replicatie is.  

![Diagram waarin de replicatie relatie wordt weer gegeven tussen de PRD-volumes in de Azure-regio productie en de PRD-volumes in de Azure-regio.](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Met het operations-team wordt de replicatie relatie tot stand gebracht tussen de PRD-volumes in de Azure-regio productie en de PRD-volumes in de Azure-regio.

>[!IMPORTANT]
>Het/Hana/log-volume wordt niet gerepliceerd omdat het niet nodig is om de gerepliceerde SAP HANA-data base terug te zetten naar een consistente status in de nood herstel site.

Vervolgens stelt u het back-upschema voor opslag momentopname in, of past u dit toe aan uw RTO en RPO in de nood geval. Als u de Recovery Point Objective wilt minimaliseren, stelt u de volgende replicatie intervallen in de HANA-service voor grote exemplaren in:
- Voor de volumes die worden gedekt door de gecombineerde moment opname (momentopname type **Hana**), ingesteld op elke 15 minuten repliceren naar de equivalente opslag volume doelen op de site voor nood herstel.
- Stel voor het back-upvolume van het transactie logboek (type **Logboeken** van moment opnamen) elke 3 minuten repliceren naar de equivalente opslag volume doelen op de site voor nood herstel.

Als u de Recovery Point Objective wilt minimaliseren, stelt u het volgende in:
- Voer een type **Hana** -opslag momentopname uit (zie stap 7: moment opnamen uitvoeren) om de 30 minuten tot 1 uur.
- Voer elke vijf minuten back-ups van SAP HANA transactie Logboeken uit.
- Voer de moment opname van de opslag van een **logboek** in om de 5-15 minuten. Met deze interval periode behaalt u een RPO van ongeveer 15-25 minuten.

Met deze instelling, de volg orde van back-ups van transactie logboeken, opslag momentopnamen en de replicatie van het HANA-transactie logboek back-upvolume en/Hana/Data, en/Hana/Shared (inclusief/usr/sap) kunnen eruitzien als de gegevens die worden weer gegeven in deze afbeelding:

 ![Relatie tussen een back-upmomentopname voor een transactie logboek en een Gesnap-mirror op een tijdsas](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Als u een nog betere RPO wilt maken in het geval van nood herstel, kunt u de HANA-transactie logboek back-ups kopiëren van SAP HANA op Azure (grote exemplaren) naar de andere Azure-regio. Voer de volgende stappen uit om deze verdere RPO-beperking te verfijnen:

1. Maak zo vaak mogelijk een back-up van het HANA-transactie logboek naar/Hana/logbackups.
1. Gebruik rsync om de back-ups van het transactie logboek te kopiëren naar de door NFS share gehoste Azure virtual machines. De virtuele machines bevinden zich in azure Virtual Networks in de Azure-productie regio en in de nood herstel regio's. U moet beide virtuele netwerken van Azure verbinden met het circuit waarmee de productie HANA grote instanties worden verbonden met Azure. Zie de sectie grafische oplossingen in het [netwerk voor herstel na nood gevallen met een grote hoeveelheid Hana-exemplaren](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) . 
1. Bewaar de back-ups van het transactie logboek in de regio in de virtuele machine die is gekoppeld aan de door NFS geëxporteerde opslag.
1. Bij een failover voor nood geval kunt u de back-ups van het transactie logboek op het/Hana/logbackups-volume aanvullen met meer recent gebruikte transactie logboek back-ups op de NFS-share op de site voor nood herstel. 
1. Start een back-up van het transactie logboek om terug te zetten naar de meest recente back-up die kan worden opgeslagen in de DR-regio.

Wanneer de installatie van de replicatie relatie wordt bevestigd door bewerkingen van een grote instantie van HANA en u de back-ups van de moment opname opslag start, wordt de gegevens replicatie gestart.

![Installatie stap van DR voordat een replicatie wordt ingesteld](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Naarmate de replicatie vordert, worden de moment opnamen op de PRD-volumes in de DR Azure-regio's niet hersteld. Ze worden alleen opgeslagen. Als de volumes in een dergelijke status zijn gekoppeld, wordt de status weer gegeven waarin u de volumes hebt ontkoppeld nadat de PRD-SAP HANA instantie in de server eenheid in de Azure-regio is geïnstalleerd. Ze vertegenwoordigen ook de opslag back-ups die nog niet zijn hersteld.

Als er een failover is, kunt u er ook voor kiezen om terug te zetten naar een oudere opslag momentopname in plaats van de laatste opslag momentopname.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [procedure voor failover-herstel na nood gevallen](hana-failover-procedure.md).
