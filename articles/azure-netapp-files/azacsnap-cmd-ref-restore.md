---
title: Herstellen met Azure-toepassing consistent snap shot tool voor Azure NetApp Files | Microsoft Docs
description: Dit onderwerp bevat een hand leiding voor het uitvoeren van de opdracht Restore van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 793b4da8fcf46ba4d5618f8ada86f9c3c8026ffd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865261"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Herstellen met behulp van Azure-toepassing consistent momentopname programma (preview-versie)

Dit artikel bevat een hand leiding voor het uitvoeren van de opdracht Restore van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.

## <a name="introduction"></a>Introductie

Het terugzetten van een volume herstel van een moment opname geschiedt met behulp van de `azacsnap -c restore` opdracht.

> [!IMPORTANT]
> Hiermee wordt geen database herstel uitgevoerd, alleen een herstel van een of meer volumes, zoals wordt beschreven voor elk van de onderstaande opties.

## <a name="command-options"></a>Opdracht Opties

De `-c restore` opdracht heeft de volgende opties:

- `--restore snaptovol` Hiermee wordt een nieuw volume gemaakt op basis van de laatste moment opname op het doel volume. Met deze opdracht wordt een nieuw ' gekloond ' volume gemaakt op basis van het geconfigureerde doel volume, met behulp van de nieuwste volume momentopname als basis voor het maken van het nieuwe volume.  Met deze opdracht wordt de opslag replicatie van primair naar secundair niet onderbroken. In plaats daarvan worden klonen van de meest recente beschik bare moment opname gemaakt op de DR-site en aanbevolen bestands systeem mountpoints van de gekloonde volumes worden weer gegeven. Deze opdracht moet worden uitgevoerd op het grootschalige Azure-exemplaar systeem **in de Dr-regio** (dat wil zeggen, het failover systeem van het doel).

- `--restore revertvolume` Hiermee wordt het doel volume teruggezet naar een eerdere status op basis van de meest recente moment opname.  Gebruik deze opdracht als onderdeel van de DR-failover in de gekoppelde DR-regio. Met deze opdracht wordt de opslag replicatie van de primaire site naar de secundaire site **gestopt** en wordt de doel-Dr-volume (s) teruggezet naar de meest recente beschik bare moment opname op de Dr-volumes, samen met het aanbevolen bestands systeem mountpoints voor de teruggedraaide Dr-volumes. Deze opdracht moet worden uitgevoerd op het grootschalige Azure-exemplaar systeem **in de Dr-regio** (dat wil zeggen, het failover systeem van het doel).
    > [!NOTE]
    > De subopdracht ( `--restore revertvolume` ) is alleen beschikbaar voor een groot Azure-exemplaar en is niet beschikbaar voor Azure NetApp files.
- `--dbsid <SAP HANA SID>` is de SAP HANA SID geselecteerd uit het configuratie bestand waarop de opdrachten voor volume herstel moeten worden toegepast.

- `[--configfile <config filename>]` is een optionele para meter voor het toestaan van aangepaste configuratie bestandsnamen.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>Een testfailover uitvoeren voor DR `azacsnap -c restore --restore snaptovol`

Deze opdracht is net als de ' volledige ' DR-failover-opdracht ( `--restore restorevolume` ), maar in plaats van de replicatie tussen de primaire site en de nood herstel site te verbreken, wordt een kloon volume gemaakt uit de volumes voor herstel na nood gevallen, waardoor het herstellen van de meest recente moment opname in de Dr-site mogelijk is. Deze gekloonde volumes kunnen vervolgens door de klant worden gebruikt om herstel na nood gevallen te testen zonder dat er een volledige failover van de HANA-omgeving hoeft te worden uitgevoerd die de replicatie overeenkomst tussen de primaire site en de nood herstel site verbreekt.

- Meerdere verschillende herstel punten kunnen op deze manier worden getest, elk met een eigen herstel punt.
- De kloon wordt aangeduid met de tijds tempel op het moment dat de opdracht werd uitgevoerd en geeft de meest recente gegevens en andere moment opnamen weer die beschikbaar zijn wanneer deze worden uitgevoerd.

> [!IMPORTANT]
> Deze bewerking is alleen van toepassing op een groot Azure-exemplaar.
>
> - Wanneer deze opdracht is uitgevoerd, moet de contact-e-mail voor bewerkingen worden opgenomen voor contacten voordat de klonen na 4 weken worden verwijderd.
> - Bij elke uitvoering van deze opdracht wordt een nieuwe kloon gemaakt die door micro soft Operations moet worden verwijderd wanneer de test is voltooid.
> - Alle gekloonde volumes die worden gemaakt, worden na 4 weken automatisch verwijderd.

Het configuratie bestand (bijvoorbeeld `DR.json` ) moet alleen de nood herstel volumes en niet de productie volumes bevatten, anders kunnen de productie volumes klonen hebben gemaakt.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>Uitvoer van de `azacsnap -c restore --restore snaptovol` opdracht (voor Single-Node scenario)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --dbsid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> De uitvoer van het volume koppel punten weer geven wijkt af van de verschillende scenario's.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>Volledige DR-failover uitvoeren `azacsnap -c restore --restore revertvolume`

Met deze opdracht **stopt** u de opslag replicatie van de primaire site naar de secundaire site, herstelt u de laatste moment opname op de nood herstel volumes en geeft u de mountpoints op voor de nood herstel volumes.

Deze opdracht moet worden uitgevoerd op de DR-server met behulp van een configuratie bestand (bijvoorbeeld `DR.json` ) met alleen Dr-volumes.

Voer een failover uit naar een DR-site door de opdracht uit te voeren `azacsnap -c restore --restore revertvolume` .  Voor deze opdracht moet een SID worden toegevoegd als een para meter. Dit is de SID van de HANA-instantie die moet worden hersteld op de DR-site.

> [!IMPORTANT]
> Voer deze opdracht alleen uit als u van plan bent de DR-oefening of een test uit te voeren. Met deze opdracht wordt de replicatie onderbroken. U moet contact opnemen met micro soft-bewerkingen om de replicatie opnieuw in te scha kelen.

Op hoog niveau zijn dit de stappen voor het uitvoeren van een DR-failover:

- U moet het HANA-exemplaar op de **primaire** site afsluiten. Deze actie is alleen nodig als u echt de failover naar de DR-site uitvoert om gegevens inconsistentie te voor komen.
- Sluit het HANA-exemplaar op het DR-knoop punt voor de productie-SID.
- Voer de opdracht `azacsnap -c restore --restore revertvolume` uit op het Dr-knoop punt met de sid die u wilt herstellen.
  - De opdracht verbreekt de koppeling van de opslag replicatie van de primaire naar de DR-site
  - Met de opdracht worden de volumes "data" en "other" hersteld zoals ze zijn geconfigureerd.  Normaal gesp roken is deze bewerking voor de volumes voor de- `/hana/data` en- `/hana/logbackups` Bestands systeem.  Het `/hana/shared` Bestands systeem is niet hersteld, maar in plaats daarvan wordt de bestaande `/hana/shared` voor sid op de Dr-locatie gebruikt.
  - Koppel de `/hana/data` volumes en en `/hana/logbackups` Controleer of deze zijn toegevoegd aan het `/etc/fstab` bestand
- Herstel de HANA SYSTEMDB-moment opname. HANA Studio toont alleen de laatste HANA-moment opname die beschikbaar is onder de moment opname van de opslag die is hersteld als onderdeel van de uitvoering van de moment opname-opdracht `azacsnap -c restore --restore revertvolume` .
- Herstel de Tenant database.
- Start het HANA-exemplaar op de DR-site voor de productie-SID (bijvoorbeeld: h80 in dit geval).
- Voer een database test uit.

## <a name="next-steps"></a>Volgende stappen

- [Maak een back-up](azacsnap-cmd-ref-backup.md)
- [Details van de moment opname ophalen](azacsnap-cmd-ref-details.md)
