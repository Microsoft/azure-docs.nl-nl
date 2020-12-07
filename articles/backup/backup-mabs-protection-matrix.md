---
title: MABS (Azure Backup Server) v3 UR1 Protection-matrix
description: In dit artikel wordt een ondersteunings matrix weer gegeven met alle werk belastingen, gegevens typen en installaties die Azure Backup Server beveiligt.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: d37d51ee781dfbc5bcd56fa3158a622202a979c6
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754178"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS (Azure Backup Server) v3 UR1 Protection-matrix

In dit artikel vindt u een overzicht van de verschillende servers en workloads die u kunt beveiligen met Azure Backup Server. In de volgende matrix ziet u wat kan worden beveiligd met Azure Backup Server.

Gebruik de volgende matrix voor MABS v3 UR1:

* Werk belastingen: het type werk belasting van de technologie.

* Versie: ondersteunde MABS-versie voor de werk belastingen.

* Installatie van MABS: de computer/locatie waarop u MABS wilt installeren.

* Beveiliging en herstel – Geef een lijst weer met gedetailleerde informatie over de werk belastingen, zoals ondersteunde opslag container of ondersteunde implementatie.

>[!NOTE]
>Ondersteuning voor de 32-bits beveiligings agent is afgeschaft met MABS v3 UR1. Zie [32-bits beveiligings agent afschaffen](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Ondersteuningsmatrix voor beveiliging

De volgende secties bevatten informatie over de ondersteunings matrix voor beveiliging voor MABS:

* [Back-ups van toepassingen](#applications-backup)
* [VM-back-up](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Back-ups van toepassingen

| **Workload**               | **Versie**                                                  | **Installatie van Azure Backup Server**                       | **Ondersteund Azure Backup Server** | **Beveiliging en herstel**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Client computers (64-bits) | Windows 10                                                  | Fysieke server  <br><br>    Virtuele Hyper-V-machine    <br><br>   Virtuele VMware-machine | V3 UR1                            | Volume, share, map, bestanden, ontdubbelde volumes   <br><br>   Beveiligde volumes moeten NTFS zijn. FAT en FAT32 worden niet ondersteund.  <br><br>    Volumes moeten minimaal 1 GB zijn. Azure Backup Server gebruikt Volume Shadow Copy Service (VSS) om de moment opname van de gegevens te maken en de moment opname werkt alleen als het volume ten minste 1 GB is. |
| Servers (64-bits)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Virtuele Azure-machine (wanneer de werk belasting wordt uitgevoerd als virtuele machine van Azure)  <br><br>    Fysieke server  <br><br>    Virtuele Hyper-V-machine  <br><br>     Virtuele VMware-machine  <br><br>    Azure Stack | V3 UR1                            | Volume, share, map, bestand <br><br>    Ontdubbelde volumes (alleen NTFS)  <br><br>   Systeem status en bare metal (niet ondersteund wanneer de werk belasting wordt uitgevoerd als virtuele machine van Azure) |
| Servers (64-bits)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (u moet [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)installeren) | Fysieke server  <br><br>    Virtuele Hyper-V-machine   <br><br>      Virtuele VMware-machine  <br><br>   Azure Stack | V3 UR1                            | Volume, share, map, bestand, systeem status/bare metal        |
| SQL Server                | SQL Server 2019, 2017, 2016 en [ondersteunde](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016)sps, 2014 en ondersteunde [SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | Fysieke server  <br><br>     Virtuele Hyper-V-machine    <br><br>     Virtuele VMware-machine  <br><br>   Azure virtuele machine (indien de werkbelasting wordt uitgevoerd als Azure virtuele machine)  <br><br>     Azure Stack | V3 UR1                            | Alle implementatie scenario's: data base       <br><br>  MABS v3 UR1 ondersteunt de back-ups van SQL-data bases via ReFS-volumes                  |
| Exchange                   | Exchange 2019, 2016                                         | Fysieke server   <br><br>   Virtuele Hyper-V-machine   <br><br>      Virtuele VMware-machine  <br><br>   Azure Stack  <br><br>    Azure virtuele machine (indien de werkbelasting wordt uitgevoerd als Azure virtuele machine) | V3 UR1                            | Beveiligen (alle implementatie scenario's): zelfstandige Exchange Server, data base onder een beschikbaarheids groep van een Data Base (DAG)  <br><br>    Herstellen (alle implementatiescenario's): postvak, postvakdatabases onder een DAG    <br><br>  Het maken van een back-up van Exchange over ReFS wordt ondersteund met MABS v3 UR1 |
| SharePoint                 | Share point 2019, 2016 met de nieuwste SPs                       | Fysieke server  <br><br>    Virtuele Hyper-V-machine  <br><br>    Virtuele VMware-machine  <br><br>   Azure virtuele machine (indien de werkbelasting wordt uitgevoerd als Azure virtuele machine)   <br><br>   Azure Stack | V3 UR1                            | Beveiligen (alle implementatie scenario's): Farm, front-end webserver inhoud  <br><br>    Herstellen (alle implementatie scenario's): Farm, Data Base, webtoepassing, bestand of lijst item, share Point zoeken, front-end webserver  <br><br>    Het beveiligen van een share point-farm die de SQL Server 2012 AlwaysOn-functie voor de inhouds databases gebruikt, wordt niet ondersteund. |

## <a name="vm-backup"></a>VM-back-up

| **Workload**                                                 | **Versie**                                             | **Installatie van Azure Backup Server**                      | **Ondersteund Azure Backup Server** | **Beveiliging en herstel**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V host-MABS Protection-agent op de Hyper-V-hostserver, het cluster of de virtuele machine | Windows Server 2019, 2016, 2012 R2, 2012               | Fysieke server  <br><br>    Virtuele Hyper-V-machine  <br><br>    Virtuele VMware-machine | V3 UR1                             | Beveiligen: Hyper-V-computers, gedeelde cluster volumes (Csv's)  <br><br>    Herstellen: virtuele machine, herstel op item niveau van bestanden en mappen die alleen beschikbaar zijn voor Windows, volumes, virtuele harde schijven |
| VMware-Vm's                                                  | VMware Server 5,5, 6,0 of 6,5, 6,7 (versie met licentie) | Virtuele Hyper-V-machine  <br><br>   Virtuele VMware-machine         | V3 UR1                             | Beveiligen: VMware-Vm's op cluster Shared volumes (Csv's), NFS en SAN-opslag   <br><br>     Herstellen: virtuele machine, herstel op item niveau van bestanden en mappen die alleen beschikbaar zijn voor Windows, volumes, virtuele harde schijven <br><br>    VMware-vApps worden niet ondersteund. |

## <a name="linux"></a>Linux

| **Workload** | **Versie**                               | **Installatie van Azure Backup Server**                      | **Ondersteund Azure Backup Server** | **Beveiliging en herstel**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux wordt uitgevoerd als [Hyper-V-](back-up-hyper-v-virtual-machines-mabs.md) of [VMware](backup-azure-backup-server-vmware.md) -gast | Fysieke server, on-premises Hyper-V VM, Windows VM in VMWare | V3 UR1                             | Hyper-V moet worden uitgevoerd op Windows Server 2012 R2, Windows Server 2016 of Windows Server 2019. Beveiligen: volledige virtuele machine   <br><br>   Herstellen: volledige virtuele machine   <br><br>    Alleen bestandsconsistente momentopnamen worden ondersteund.    <br><br>   Zie het artikel [Linux op distributies die zijn goedgekeurd door Azure](../virtual-machines/linux/endorsed-distros.md)voor een volledige lijst met ondersteunde Linux-distributies en-versies. |

## <a name="azure-expressroute-support"></a>Ondersteuning voor Azure ExpressRoute

U kunt een back-up maken van uw gegevens via Azure ExpressRoute met open bare peering (beschikbaar voor oude circuits) en micro soft-peering. Back-up via privé-peering wordt niet ondersteund.

Met open bare peering: Zorg ervoor dat u toegang hebt tot de volgende domeinen/adressen:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Selecteer bij micro soft-peering de volgende services/regio's en relevante Community-waarden:

* Azure Active Directory (12076:5060)
* Microsoft Azure regio (op basis van de locatie van uw Recovery Services kluis)
* Azure Storage (op basis van de locatie van uw Recovery Services kluis)

Zie de [routerings vereisten voor ExpressRoute](../expressroute/expressroute-routing.md)voor meer informatie.

>[!NOTE]
>Open bare peering is afgeschaft voor nieuwe circuits.

## <a name="cluster-support"></a>Clusterondersteuning

Azure Backup Server kunnen gegevens in de volgende geclusterde toepassingen beveiligen:

* Bestandsservers

* SQL Server

* Hyper-V: als u een Hyper-V-cluster beveiligt met een uitgebreide MABS-beveiligings agent, kunt u geen secundaire beveiliging toevoegen voor de beveiligde Hyper-V-werk belastingen.

* Exchange Server-Azure Backup Server kan niet-gedeelde schijf clusters beveiligen voor ondersteunde Exchange Server-versies (cluster-continue replicatie), en kan ook Exchange Server beveiligen die is geconfigureerd voor lokale continue replicatie.

* SQL Server-Azure Backup Server biedt geen ondersteuning voor het maken van back-ups van SQL Server-data bases die worden gehost op Csv's (cluster Shared volumes).

Azure Backup Server kunt de cluster werkbelastingen beveiligen die zich in hetzelfde domein bevinden als de MABS-server, en in een onderliggend of vertrouwd domein. Als u gegevens bronnen in niet-vertrouwde domeinen of werk groepen wilt beveiligen, gebruikt u NTLM of certificaat verificatie voor één server of alleen certificaat authenticatie voor een cluster.
