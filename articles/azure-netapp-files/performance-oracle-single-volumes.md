---
title: Prestaties van Oracle-Data Base op Azure NetApp Files één volume | Microsoft Docs
description: Beschrijft prestatie test resultaten van een Azure NetApp Files één volume op Oracle data base.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91571338"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Oracle-databaseprestaties in afzonderlijke Azure NetApp Files-volumes

Dit artikel heeft betrekking op de volgende onderwerpen over Oracle in de Cloud. Deze onderwerpen zijn mogelijk van bijzonder belang voor een database beheerder, Cloud architect of opslag architect:   

* Wanneer u een OLTP-werk belasting (online Trans Action processing) hebt gebrand (voornamelijk wille keurig I/O) of een Online Analytical Processing (OLAP)-werk belasting (voornamelijk sequentiële I/O's), wat betekent dat de prestaties eruitzien?   
* Wat is het verschil in prestaties tussen de reguliere Linux kernel NFS-client (kNFS) en de eigen directe NFS-client van Oracle?
* Wat betreft band breedte zijn de prestaties van een enkele Azure NetApp Files volume genoeg?

## <a name="testing-environment-and-components"></a>Omgeving en onderdelen testen

In het volgende diagram ziet u de omgeving die wordt gebruikt voor het testen. Voor consistentie en eenvoud werden Ansible playbooks gebruikt voor het implementeren van alle elementen van de test bed.

![Oracle-test omgeving](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>Configuratie van virtuele machine

De tests hebben de volgende configuratie voor de virtuele machine gebruikt:
* Besturingssysteem:   
    Red Hat Enter prise Linux 7,8 (WLE-ora01)
* Exemplaar typen:   
    Er zijn twee modellen gebruikt voor het testen: D32s_v3 en D64s_v3
* Aantal netwerk interfaces:   
    Eén (1) geplaatst in subnet 3  
* Cd's   
    Binaire bestanden voor Oracle en het besturings systeem zijn in één Premium-schijf geplaatst

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Azure NetApp Files configuratie
De tests hebben de volgende Azure NetApp Files configuratie gebruikt:   

* Grootte van capaciteits groep:  
    Verschillende groottes van de groep zijn geconfigureerd: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* Service niveau:  
    Ultra (128 MiB/s met band breedte per 1 TiB toegewezen volume capaciteit)
* Omvang  
    Er zijn één en twee volume tests geëvalueerd

### <a name="workload-generator"></a>Workload generator 

De tests die door de werk belasting worden gebruikt, zijn SLOB 2.5.4.2 gegenereerd. SLOB (Sillye Oracle Bench Mark) is een bekende werkbelasting generator in de Oracle-ruimte die is ontworpen om het I/O-subsysteem te stressren en te testen met een SGA-bufferd fysieke I/O-werk belasting.  

SLOB 2.5.4.2 biedt geen ondersteuning voor de pluggable data base (PDB). Als zodanig is er een wijziging toegevoegd aan de `setup.sh` scripts en wordt er `runit.sh` PDB-ondersteuning aan toegevoegd.  

De SLOB variabelen die in de tests worden gebruikt, worden in de volgende secties beschreven.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>Workload 80% SELECT, 20% UPDATE | Wille keurige I/O- `slob.conf` variabelen   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>Workload 100% SELECT | Sequentiële I/O- `slob.conf` variabelen

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>Database

De Oracle-versie die wordt gebruikt voor de tests is Oracle Database Enterprise Edition 19.3.0.0.

De Oracle-para meters zijn als volgt:  
* `sga_max_size`: 4096M
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: True
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

Er is een PDB gemaakt voor de SLOB-data base.

In het volgende diagram ziet u de tabel ruimte met de naam PERFIO met een grootte van 600 GB (20 gegevens bestanden, 30 GB elk) die zijn gemaakt om vier SLOB-gebruikers schema's te hosten. Elk gebruikers schema was 125 GB groot.

![Oracle-database](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>Metrische gegevens voor prestaties

Het doel is om de i/o-prestaties van de toepassing te rapporteren. Daarom gebruiken alle diagrammen in dit artikel metrische gegevens die worden gerapporteerd door de Oracle-data base via de AWR-rapporten (automatische werk belasting). De metrische gegevens die in de diagrammen worden gebruikt, zijn als volgt:   

* **Gemiddeld aantal i/o-aanvragen per seconde**   
    Komt overeen met de som van de gemiddelde lees-i/o-aanvragen per seconde en het gemiddelde aantal i/o-schrijf aanvragen per seconde uit de sectie laad profiel
* **Gemiddeld aantal i/o MB per seconde**   
    Komt overeen met de som van de gemiddelde lees-i/o MB per seconde en de gemiddelde schrijf-i/o MB per seconde uit de sectie laad profiel
* **Gemiddelde lees latentie**   
    Komt overeen met de gemiddelde latentie van de Oracle-wacht gebeurtenis ' db-bestand sequentieel lezen ' in micro seconden
* **Aantal threads/schema**   
    Komt overeen met het aantal SLOB-threads per gebruikers schema

## <a name="performance-measurement-results"></a>Resultaten van prestatie meting  

In deze sectie worden de resultaten van de prestatie meting beschreven.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS-client versus Oracle direct NFS

Dit scenario werd uitgevoerd op een Azure VM-Standard_D32s_v3 (Intel E5-2673 v4 @ 2,30 GHz). De werk belasting is 75% SELECT en 25% UPDATE, voornamelijk wille keurige I/O en met een database buffer van ~ 7,5%. 

Zoals in het volgende diagram wordt weer gegeven, heeft de Oracle DNFS-client tot 2,8 x meer door voer dan de gewone Linux kNFS-client:  

![Linux kNFS-client vergeleken met Oracle direct NFS](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

In het volgende diagram ziet u de latentie curve voor de Lees bewerkingen. In deze context is het knel punt voor de kNFS-client de TCP-socket verbinding met één NFS tot stand gebracht tussen de client en de NFS-server (het Azure NetApp Files volume).  

![Linux kNFS-client vergeleken met Oracle direct NFS latentie-curve](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

De DNFS-client heeft meer i/o-aanvragen kunnen pushen/SEC als gevolg van de mogelijkheid om honderden TCP-socket verbindingen te maken, waardoor de parallellisme kan worden benut. Zoals beschreven in [Azure NetApp files configuratie](#anf_config), biedt elke extra TIB aan toegewezen capaciteit een extra 128MiB/s aan band breedte. DNFS topped uit op 1 GiB/s met door Voer, wat de limiet is die wordt opgelegd door de selectie van 8 TiB capaciteit. Meer capaciteit heeft gekregen, zou meer door Voer zijn aangestuurd.

De door Voer is slechts een van de overwegingen. Een andere overweging is latentie, wat het belangrijkste effect heeft op de gebruikers ervaring. Zoals in het volgende diagram wordt weer gegeven, kunnen latentie verhogingen veel sneller worden verwacht met kNFS dan met DNFS. 

![Linux kNFS-client vergeleken met Oracle direct NFS lees latentie](../media/azure-netapp-files/performance-oracle-read-latency.png)  

Histogrammen bieden uitstekende inzicht in database latentie. In het volgende diagram ziet u een volledig overzicht van het perspectief van het vastgelegde ' db-bestand sequentiële Lees bewerking ', terwijl u DNFS gebruikt op het hoogste gegevens punt gelijktijdig (32 threads/schema). Zoals in het volgende diagram wordt weer gegeven, werd 47% van alle Lees bewerkingen gehonoreerd tussen 512 micro seconden en 1000 micro seconden, terwijl 90% van alle Lees bewerkingen is bediend met een latentie van minder dan 2 MS.

![Linux kNFS-client vergeleken met Oracle direct NFS-histogrammen](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

Daarom is het duidelijk dat DNFS een moet zijn wanneer het gaat om de prestaties van een Oracle data base-exemplaar op NFS te verbeteren.

### <a name="single-volume-performance-limits"></a>Prestatie limieten voor één volume

In deze sectie worden de prestatie limieten van één volume met wille keurige I/O en sequentiële I/O beschreven. 

#### <a name="random-io"></a>Wille keurige I/O

DNFS kan veel meer band breedte in beslag nemen dan wat er wordt gegeven door een Azure NetApp Files prestatie quotum van 8 TB. Door de Azure NetApp Files volume capaciteit te verg Roten naar 16 TiB, wat een momentane wijziging is, is de hoeveelheid volume bandbreedte van 1024 MiB/s door 2X verhoogd naar 2048 MiB/s. 

Het volgende diagram toont een configuratie voor een 80% Select-workload en 20% werk belasting van de data base en een verhouding van 8% voor de database buffer. SLOB heeft een enkel volume kunnen bezorgen tot 200.000 NFS I/O-aanvragen per seconde. Als u overweegt dat elke bewerking 8-KiB grootte is, kon het systeem onder test ~ 200.000 IO-aanvragen per seconde of 1600 MiB/s leveren.
 
![Oracle DNFS-door Voer](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

In het volgende diagram van de lees latentie ziet u dat, als de Lees doorvoer toeneemt, de latentie gelijkmatiger onder de 1 MS-lijn toeneemt en de knie van de curve op ~ 165.000 gemiddeld Lees-IO-aanvragen per seconde bij de gemiddelde lees latentie van ~ 1,3 MS.  Deze waarde is een ongelooflijke latentie waarde voor een I/O-frequentie die niet kan worden behaald met vrijwel alle andere technologie in de Azure-Cloud. 

![DNFS-latentie curve van Oracle](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>Sequentiële I/O  

Zoals in het volgende diagram wordt weer gegeven, is niet alle I/O wille keurige aard, waarbij een RMAN-back-up of een volledige tabel scan wordt overwogen, bijvoorbeeld als werk belasting die zoveel band breedte vereisen als ze kunnen verkrijgen.  Met behulp van dezelfde configuratie zoals eerder beschreven, maar met het volume dat is gewijzigd in 32 TiB, ziet u in het volgende diagram dat één Oracle DB-exemplaar naar een aantal van 3.900 MB/s aan de door Voer kan worden gebrand, wat dicht bij het quotum van de Azure NetApp Files volume van 32 TB is (128 MB/s * 32 = 4096 MB/s).

![Oracle DNFS I/O](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

Azure NetApp Files helpt u bij het maken van uw Oracle-data bases naar de Cloud. Het levert prestaties op het moment dat de data base dit aanvraagt. U kunt op elk gewenst moment dynamisch en niet-disruptively het formaat van uw volume quota wijzigen.

## <a name="next-steps"></a>Volgende stappen

- [Aanbevelingen van prestatiebenchmarks voor Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md)
- [Prestatiebenchmarks voor Linux](performance-benchmarks-linux.md)