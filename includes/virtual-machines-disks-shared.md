---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 74c77356df4f35461a8b9f1459712cdcf7f77cbf
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555245"
---
Gedeelde Azure-schijven is een nieuwe functie voor Azure Managed disks waarmee u tegelijkertijd een beheerde schijf kunt koppelen aan meerdere virtuele machines (Vm's). Als u een beheerde schijf aan meerdere Vm's koppelt, kunt u nieuwe, geclusterde toepassingen implementeren of migreren naar Azure.

## <a name="how-it-works"></a>Uitleg

Vm's in het cluster kunnen lezen van of schrijven naar de gekoppelde schijf op basis van de reserve ring die is gekozen door de geclusterde toepassing met behulp van [permanente SCSI-reserve ringen](https://www.t10.org/members/w_spc3.htm) (SCSI-PR). SCSI-PR is een industrie standaard die wordt gebruikt door toepassingen die on-premises worden uitgevoerd op Storage Area Network (SAN). Door SCSI-PR op een beheerde schijf in te scha kelen, kunt u deze toepassingen naar Azure migreren.

Gedeelde beheerde schijven bieden gedeelde blok opslag die toegankelijk is vanaf meerdere Vm's. deze worden weer gegeven als Lun's (Logical Unit Numbers). Lun's worden vervolgens weer gegeven aan een initiator (VM) vanaf een doel (schijf). Deze Lun's zien eruit als direct gekoppelde opslag (DAS) of een lokale schijf naar de virtuele machine.

Gedeelde beheerde schijven bieden niet systeem eigen een volledig beheerd bestands systeem dat kan worden geopend met SMB/NFS. U moet een cluster beheer gebruiken, zoals Windows Server failover cluster (WSFC) of pacemaker, die de communicatie tussen cluster knooppunten en schrijf vergrendeling afhandelt.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>Vereisten voor het besturingssysteem

Gedeelde schijven bieden ondersteuning voor verschillende besturings systemen. Zie de secties [Windows](#windows) of [Linux](#linux) voor de ondersteunde besturings systemen.

## <a name="disk-sizes"></a>Schijf grootten

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Voorbeeld werkbelastingen

### <a name="windows"></a>Windows

Gedeelde Azure-schijven worden ondersteund op Windows Server 2008 en nieuwer. De meeste op Windows gebaseerde clustering bouwt voort op WSFC, die alle basis infrastructuur voor de communicatie van het cluster knooppunt afhandelt, zodat uw toepassingen kunnen profiteren van parallelle toegangs patronen. Met WSFC kunt u zowel CSV- als niet-CSV-opties maken, afhankelijk van uw versie van Windows Server. Raadpleeg [Een failovercluster maken](/windows-server/failover-clustering/create-failover-cluster)voor meer informatie.

Enkele populaire toepassingen die worden uitgevoerd op WSFC zijn:

- [Een FCI maken met gedeelde Azure-schijven (SQL Server op virtuele machines van Azure)](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- Scale-out Bestands server (SoFS) [sjabloon] (https://aka.ms/azure-shared-disk-sofs-template)
- SAP ASCS/SCS [sjabloon] (https://aka.ms/azure-shared-disk-sapacs-template)
- Bestandsserver voor algemeen gebruik (IW-workload)
- Gebruikersprofielschijf van externe bureaubladserver (RDS UPD)

### <a name="linux"></a>Linux

Gedeelde Azure-schijven worden ondersteund op:
- [SUSE SLE voor SAP en SUSE SLE HA 15 SP1 en hoger](https://www.suse.com/c/azure-shared-disks-excercise-w-sles-for-sap-or-sle-ha/)
- [Ubuntu 18,04 en hoger](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [RHEL Developer Preview voor een versie van RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/index?lb_target=production#azure-configuring-shared-block-storage_configuring-rhel-high-availability-on-azure)
- [Oracle Enter prise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

Linux-clusters kunnen gebruikmaken van cluster managers zoals [pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker bouwt voort op [corosync](http://corosync.github.io/corosync/), waardoor cluster communicatie mogelijk wordt voor toepassingen die in Maxi maal beschik bare omgevingen worden geïmplementeerd. Enkele algemene geclusterde bestands systemen zijn [ocfs2](https://oss.oracle.com/projects/ocfs2/) en [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). U kunt SCSI-permanente reserve ringen (SCSI PR) en/of STONITH block device (SBD)-clustering modellen gebruiken voor arbitrating toegang tot de schijf. Bij gebruik van SCSI-PR kunt u reserve ringen en registraties manipuleren met behulp van hulpprogram ma's als [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) en [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Stroom permanente reserve ring

In het volgende diagram ziet u een voor beeld van een geclusterde database toepassing met twee knoop punten die gebruikmaakt van SCSI-PR om failover van het ene naar het andere knoop punt in te scha kelen.

![Cluster met twee knoop punten. Een toepassing die wordt uitgevoerd op het cluster, is bezig met het afhandelen van toegang tot de schijf](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

De stroom is als volgt:

1. De geclusterde toepassing die op zowel Azure VM1 als VM2 wordt uitgevoerd, registreert de intentie om deze te lezen of te schrijven naar de schijf.
1. Het toepassings exemplaar op VM1 maakt vervolgens exclusieve reserve ring om naar de schijf te schrijven.
1. Deze reserve ring wordt afgedwongen op uw Azure-schijf en de data base kan nu uitsluitend naar de schijf schrijven. Schrijf bewerkingen van het toepassings exemplaar op VM2 zullen niet slagen.
1. Als het toepassings exemplaar op VM1 uitvalt, kan het exemplaar op VM2 nu een Data Base-failover initiëren en de schijf overnemen.
1. Deze reserve ring wordt nu afgedwongen op de Azure-schijf en de schijf accepteert geen schrijf bewerkingen meer van VM1. Er worden alleen schrijf bewerkingen geaccepteerd van VM2.
1. De geclusterde toepassing kan de data base-failover volt ooien en aanvragen van VM2 verwerken.

Het volgende diagram illustreert een andere algemene geclusterde werk belasting die bestaat uit meerdere knoop punten die gegevens van de schijf lezen voor het uitvoeren van parallelle processen, zoals de training van machine learning modellen.

![VM-cluster met vier knoop punten, elk knoop punt registreert intentie om te schrijven, de toepassing neemt exclusieve reserve ring voor het goed verwerken van schrijf resultaten](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

De stroom is als volgt:

1. De geclusterde toepassing die op alle Vm's wordt uitgevoerd, registreert het doel om te lezen van of te schrijven naar de schijf.
1. Het toepassings exemplaar op VM1 neemt een exclusieve reserve ring om naar de schijf te schrijven terwijl er Lees bewerkingen op de schijf van andere Vm's worden geopend.
1. Deze reserve ring wordt afgedwongen op uw Azure-schijf.
1. Alle knoop punten in het cluster kunnen nu van de schijf worden gelezen. Slechts één knoop punt schrijft resultaten terug naar de schijf, namens alle knoop punten in het cluster.

### <a name="ultra-disks-reservation-flow"></a>Overdracht van ultra schijven-reserverings stroom

Ultra disks bieden een extra beperking voor een totaal van twee gashendel. Als gevolg hiervan kan de overdrachts stroom voor Ultra disks werken zoals beschreven in de vorige sectie, of kan de prestaties nauw keuriger worden beperkt en gedistribueerd.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Een afbeelding van een tabel waarin de ' ReadOnly '-of ' Lees-en schrijf toegang ' wordt weer gegeven voor reserverings houder, geregistreerd en andere.":::

## <a name="performance-throttles"></a>Prestatie bandbreedten

### <a name="premium-ssd-performance-throttles"></a>Prestatie bandbreedten Premium-SSD

Met Premium SSD is de schijf-IOPS en de door Voer opgelost, bijvoorbeeld IOPS van een P30 is 5000. Deze waarde blijft ongewijzigd of de schijf wordt gedeeld over twee Vm's of 5 Vm's. De schijf limieten kunnen worden bereikt vanaf één virtuele machine of worden verdeeld over twee of meer virtuele machines. 

### <a name="ultra-disk-performance-throttles"></a>Hoge prestaties voor de schijf

Ultra schijven hebben de unieke mogelijkheid om uw prestaties in te stellen door aanpas bare kenmerken weer te geven en u toe te passen. Standaard zijn er slechts twee kenmerken die kunnen worden gewijzigd, maar gedeelde Ultra schijven hebben twee extra kenmerken.


|Kenmerk  |Beschrijving  |
|---------|---------|
|DiskIOPSReadWrite     |Het totale aantal IOPS dat is toegestaan voor alle Vm's die de share schijf koppelen aan schrijf toegang.         |
|DiskMBpsReadWrite     |De totale door Voer (MB/s) die is toegestaan voor alle Vm's die de gedeelde schijf koppelen met schrijf toegang.         |
|DiskIOPSReadOnly*     |Het totale aantal IOPS dat is toegestaan voor alle Vm's en de gedeelde schijf als `ReadOnly` .         |
|DiskMBpsReadOnly*     |De totale door Voer (MB/s) die is toegestaan voor alle Vm's die de gedeelde schijf koppelen als `ReadOnly` .         |

\* Is alleen van toepassing op gedeelde Ultra schijven

In de volgende formules wordt uitgelegd hoe de prestatie kenmerken kunnen worden ingesteld, omdat deze door gebruikers worden gewijzigd:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - IOPS-limieten van 300 IOPS/GiB, Maxi maal 160K IOPS per schijf
    - Mini maal 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly is ten minste 2 IOPS/GiB
- DiskMBpsRead schrijven/DiskMBpsReadOnly:
    - De doorvoer limiet van één schijf is 256 KiB/s voor elke ingerichte IOPS, tot een maximum van 2000 MBps per schijf
    - De minimale gegarandeerde door Voer per schijf is 4KiB/s voor elke ingerichte IOPS, met een totale basis lijn van Mini maal 1 MBps

#### <a name="examples"></a>Voorbeelden

In de volgende voor beelden ziet u een aantal scenario's waarin u kunt zien hoe de beperking kan werken met gedeelde Ultra schijven, met name.

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster met twee knoop punten met behulp van gedeelde cluster volumes

Hier volgt een voor beeld van een WSFC-netwerk met twee knoop punten met geclusterde gedeelde volumes. Met deze configuratie hebben beide Vm's gelijktijdig schrijf toegang tot de schijf, wat resulteert in de vertraging voor `ReadWrite` de twee virtuele machines en de gashendel die niet wordt `ReadOnly` gebruikt.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV twee-knoop punt-zeer voor beeld":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster met twee knoop punten zonder cluster share volumes

Hier volgt een voor beeld van een WSFC met twee knoop punten die geen geclusterde gedeelde volumes gebruikt. Met deze configuratie heeft slechts één virtuele machine schrijf toegang tot de schijf. Dit leidt ertoe dat de `ReadWrite` beperking alleen voor de primaire virtuele machine wordt gebruikt en dat de `ReadOnly` beperking alleen wordt gebruikt door de secundaire.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV-twee knoop punten geen CSV Ultra disk-voor beeld":::

##### <a name="four-node-linux-cluster"></a>Linux-cluster met vier knoop punten

Hier volgt een voor beeld van een Linux-cluster met vier knoop punten met één schrijver en drie scale-out lezers. Met deze configuratie heeft slechts één virtuele machine schrijf toegang tot de schijf. Dit leidt ertoe dat de `ReadWrite` beperking alleen wordt gebruikt voor de primaire virtuele machine en `ReadOnly` dat de beperking wordt gesplitst door de secundaire vm's.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Voor beeld van een super beperking van vier knoop punten":::

#### <a name="ultra-pricing"></a>Hoge prijzen

Ultra gedeelde schijven worden geprijsd op basis van de ingerichte capaciteit, de totale ingerichte IOPS (diskIOPSReadWrite + diskIOPSReadOnly) en de totale ingerichte door Voer (diskMBpsReadWrite + diskMBpsReadOnly). Er worden geen extra kosten in rekening gebracht voor elke extra VM-koppeling. Zo wordt een ultra gedeelde schijf met de volgende configuratie (diskSizeGB: 1024, DiskIOPSReadWrite: 10000, DiskMBpsReadWrite: 600, DiskIOPSReadOnly: 100, DiskMBpsReadOnly: 1) in rekening gebracht met 1024 GiB, 10100 IOPS en 601 MBps, ongeacht of deze is gekoppeld aan twee Vm's of vijf Vm's.