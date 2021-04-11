---
title: MABS (Azure Backup Server) v3 UR1 Protection-matrix
description: In dit artikel wordt een ondersteunings matrix weer gegeven met alle werk belastingen, gegevens typen en installaties die Azure Backup Server beveiligt.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: cfdd227135a2124e22a604bad4bd41594a38fb37
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561267"
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
| Servers (64-bits)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Virtuele Azure-machine (wanneer de werk belasting wordt uitgevoerd als virtuele machine van Azure)  <br><br>    Fysieke server  <br><br>    Virtuele Hyper-V-machine  <br><br>     Virtuele VMware-machine  <br><br>    Azure Stack | V3 UR1                            | Volume, share, map, bestand <br><br>    Ontdubbelde volumes (alleen NTFS) <br><br>Bij het beveiligen van een WS 2016 NTFS-volume met MABS v3 dat wordt uitgevoerd op Windows Server 2019, kunnen de herstel bewerkingen worden beïnvloed. We hebben een correctie voor het uitvoeren van herstel bewerkingen op een niet-verouderde manier die deel zal uitmaken van latere versies van MABS. Neem contact op met MABS-ondersteuning als u deze oplossing op MABS v3 UR1 nodig hebt.<br><br> Wanneer u een WS 2019 NTFS-volume met MABS v3 op Windows Server 2016 beveiligt, worden de back-ups en herstel bewerkingen niet-ontdubbeld. Dit betekent dat de back-ups meer ruimte op de MABS-server verbruiken dan het oorspronkelijke NTFS-volume dat is ontdubbeld.   <br><br>   Systeem status en bare metal (niet ondersteund wanneer de werk belasting wordt uitgevoerd als virtuele machine van Azure) |
| Servers (64-bits)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (u moet [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)installeren) | Fysieke server  <br><br>    Virtuele Hyper-V-machine   <br><br>      Virtuele VMware-machine  <br><br>   Azure Stack | V3 UR1                            | Volume, share, map, bestand, systeem status/bare metal        |
| SQL Server                | SQL Server 2019, 2017, 2016 en [ondersteunde](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016)sps, 2014 en ondersteunde [SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | Fysieke server  <br><br>     Virtuele Hyper-V-machine    <br><br>     Virtuele VMware-machine  <br><br>   Azure virtuele machine (indien de werkbelasting wordt uitgevoerd als Azure virtuele machine)  <br><br>     Azure Stack | V3 UR1                            | Alle implementatie scenario's: data base       <br><br>  MABS v3 UR1 ondersteunt de back-ups van SQL-data bases via ReFS-volumes     <br><br>     MABS biedt geen ondersteuning voor SQL Server-data bases die worden gehost op Windows Server 2012 Scale-Out file servers (SOFS). <br><br>   MABS kan SQL Server gedistribueerde beschikbaarheids groep (DAG) of beschikbaarheids groep (AG) niet beveiligen, waarbij de rolnaam op het failovercluster afwijkt van de naam AG op SQL.       |
| Exchange                   | Exchange 2019, 2016                                         | Fysieke server   <br><br>   Virtuele Hyper-V-machine   <br><br>      Virtuele VMware-machine  <br><br>   Azure Stack  <br><br>    Azure virtuele machine (indien de werkbelasting wordt uitgevoerd als Azure virtuele machine) | V3 UR1                            | Beveiligen (alle implementatie scenario's): zelfstandige Exchange Server, data base onder een beschikbaarheids groep van een Data Base (DAG)  <br><br>    Herstellen (alle implementatiescenario's): postvak, postvakdatabases onder een DAG    <br><br>  Het maken van een back-up van Exchange over ReFS wordt ondersteund met MABS v3 UR1 |
| SharePoint                 | Share point 2019, 2016 met de nieuwste SPs                       | Fysieke server  <br><br>    Virtuele Hyper-V-machine  <br><br>    Virtuele VMware-machine  <br><br>   Azure virtuele machine (indien de werkbelasting wordt uitgevoerd als Azure virtuele machine)   <br><br>   Azure Stack | V3 UR1                            | Beveiligen (alle implementatie scenario's): Farm, front-end webserver inhoud  <br><br>    Herstellen (alle implementatie scenario's): Farm, Data Base, webtoepassing, bestand of lijst item, share Point zoeken, front-end webserver  <br><br>    Het beveiligen van een share point-farm die de SQL Server 2012 AlwaysOn-functie voor de inhouds databases gebruikt, wordt niet ondersteund. |

## <a name="vm-backup"></a>VM-back-up

| **Workload**                                                 | **Versie**                                             | **Installatie van Azure Backup Server**                      | **Ondersteund Azure Backup Server** | **Beveiliging en herstel**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V host-MABS Protection-agent op de Hyper-V-hostserver, het cluster of de virtuele machine | Windows Server 2019, 2016, 2012 R2, 2012               | Fysieke server  <br><br>    Virtuele Hyper-V-machine  <br><br>    Virtuele VMware-machine | V3 UR1                             | Beveiligen: Hyper-V-computers, gedeelde cluster volumes (Csv's)  <br><br>    Herstellen: virtuele machine, herstel op item niveau van bestanden en mappen die alleen beschikbaar zijn voor Windows, volumes, virtuele harde schijven |
| VMware-Vm's                                                  | VMware Server 5,5, 6,0 of 6,5, 6,7 (versie met licentie) | Virtuele Hyper-V-machine  <br><br>   Virtuele VMware-machine         | V3 UR1                             | Beveiligen: VMware-Vm's op cluster Shared volumes (Csv's), NFS en SAN-opslag   <br><br>     Herstellen: virtuele machine, herstel op item niveau van bestanden en mappen die alleen beschikbaar zijn voor Windows, volumes, virtuele harde schijven <br><br>    VMware-vApps worden niet ondersteund. |

>[!NOTE]
> MABS biedt geen ondersteuning voor het maken van back-ups van virtuele machines met doorgangs schijven of gebruikers die een externe VHD gebruiken. In deze scenario's wordt u aangeraden back-ups op gast niveau te gebruiken met MABS en een agent op de virtuele machine te installeren om een back-up van de gegevens te maken.

## <a name="linux"></a>Linux

| **Workload** | **Versie**                               | **Installatie van Azure Backup Server**                      | **Ondersteund Azure Backup Server** | **Beveiliging en herstel**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux wordt uitgevoerd als [Hyper-V-](back-up-hyper-v-virtual-machines-mabs.md) of [VMware](backup-azure-backup-server-vmware.md) -gast | Fysieke server, on-premises Hyper-V VM, Windows VM in VMware | V3 UR1                             | Hyper-V moet worden uitgevoerd op Windows Server 2012 R2, Windows Server 2016 of Windows Server 2019. Beveiligen: volledige virtuele machine   <br><br>   Herstellen: volledige virtuele machine   <br><br>    Alleen bestandsconsistente momentopnamen worden ondersteund.    <br><br>   Zie het artikel [Linux op distributies die zijn goedgekeurd door Azure](../virtual-machines/linux/endorsed-distros.md)voor een volledige lijst met ondersteunde Linux-distributies en-versies. |

## <a name="azure-expressroute-support"></a>Ondersteuning voor Azure ExpressRoute

U kunt een back-up maken van uw gegevens via Azure ExpressRoute met open bare peering (beschikbaar voor oude circuits) en micro soft-peering. Back-up via privé-peering wordt niet ondersteund.

Met open bare peering: Zorg ervoor dat u toegang hebt tot de volgende domeinen/adressen:

* URL's
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-adressen
  * 20.190.128.0/18
  * 40.126.0.0/18

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

>[!NOTE]
>MABS biedt alleen ondersteuning voor de beveiliging van Hyper-V virtuele machines op gedeelde cluster volumes (Csv's). Het beveiligen van andere werkbelastingen die zijn gehost op CSV's, wordt niet ondersteund.

Azure Backup Server kunt de cluster werkbelastingen beveiligen die zich in hetzelfde domein bevinden als de MABS-server, en in een onderliggend of vertrouwd domein. Als u gegevens bronnen in niet-vertrouwde domeinen of werk groepen wilt beveiligen, gebruikt u NTLM of certificaat verificatie voor één server of alleen certificaat authenticatie voor een cluster.

## <a name="data-protection-issues"></a>Problemen met beveiliging van gegevens

* MABS kan geen back-up maken van virtuele machines met gedeelde stations (die mogelijk zijn gekoppeld aan andere Vm's), omdat de Hyper-V-VSS Writer geen back-up kan maken van volumes waarvan een back-up is gemaakt door gedeelde Vhd's.

* Wanneer u een gedeelde map beveiligt, bevat het pad naar de gedeelde map het logische pad op het volume. De beveiliging kan niet worden uitgevoerd als u de gedeelde map verplaatst. Als u een beveiligde gedeelde map moet verplaatsen, verwijdert u deze uit de beveiligings groep en voegt u deze vervolgens toe aan de beveiliging na de verplaatsing.  Als u het pad van een beveiligde gegevens bron wijzigt op een volume dat gebruikmaakt van de Encrypting File System (EFS) en het nieuwe bestandspad groter is dan 5120 tekens, zal de gegevens beveiliging mislukken.

* U kunt het domein van een beveiligde computer niet wijzigen en de beveiliging zonder onderbreking voortzetten. Daarnaast kunt u het domein van een beveiligde computer niet wijzigen en de bestaande replica's en herstel punten koppelen aan de computer wanneer deze opnieuw is beveiligd. Als u het domein van een beveiligde computer moet wijzigen, verwijder dan eerst de gegevens bronnen op de computer uit de beveiliging. Beveilig vervolgens de gegevens bron op de computer nadat deze een nieuw domein heeft.

* U kunt de naam van een beveiligde computer niet wijzigen en de beveiliging zonder onderbreking voortzetten. U kunt ook de naam van een beveiligde computer niet wijzigen en de bestaande replica's en herstel punten koppelen aan de computer wanneer deze opnieuw is beveiligd. Als u de naam van een beveiligde computer moet wijzigen, verwijder dan eerst de gegevensbronnen uit de beveiliging van de computer. Beveilig vervolgens de gegevensbron op de computer nadat deze een nieuwe naam heeft gekregen.

* MABS identificeert automatisch de tijd zone van een beveiligde computer tijdens de installatie van de beveiligings agent. Als een beveiligde computer naar een andere tijdzone wordt verplaatst nadat de beveiliging is geconfigureerd, zorg er dan voor dat u de computertijd wijzigt in het Configuratiescherm. Werk vervolgens de tijd zone bij in de MABS-data base.

* MABS kan werk belastingen beveiligen in hetzelfde domein als de MABS-server, of in onderliggende en vertrouwde domeinen. U kunt ook de volgende werk belastingen beveiligen in werk groepen en niet-vertrouwde domeinen met behulp van NTLM of verificatie via certificaat:

  * SQL Server
  * Bestandsserver
  * Hyper-V

  Deze werkbelastingen kunnen worden uitgevoerd op een enkele server of in een clusterconfiguratie. Als u een werk belasting wilt beveiligen die zich niet in een vertrouwd domein bevindt, raadpleegt u [computers in werk groepen en niet-vertrouwde domeinen voorbereiden](/system-center/dpm/prepare-environment-for-dpm) voor exacte details van wat er wordt ondersteund en welke verificatie is vereist.

## <a name="unsupported-data-types"></a>Niet-ondersteunde gegevens typen

MABS biedt geen ondersteuning voor het beveiligen van de volgende gegevens typen:

* Vaste koppelingen

* Reparsepunten, inclusief DFS-koppelingen en koppelingspunten

* Metagegevens van koppelpunten: een beveiligingsgroep kan gegevens met koppelpunten bevatten. In dit geval beveiligt DPM het gekoppelde volume dat het doel is van het koppelpunt, maar worden de metagegevens van het koppelpunt niet beveiligd. Wanneer u gegevens herstelt die koppelpunten bevatten, moet u de koppelpuntstructuur handmatig herstellen.

* Gegevens op gekoppelde volumes in gekoppelde volumes

* Prullenbak

* Wisselbestanden

* De map System Volume Information Als u systeem informatie voor een computer wilt beveiligen, moet u de systeem status van de computer selecteren als lid van de groep beveiligen.

* Niet-NTFS-volumes

* Bestanden die harde koppelingen of symbolische koppelingen van Windows Vista bevatten.

* Gegevens op bestands shares die als host fungeren voor Upd's hosten (gebruikers profiel schijven)

* Bestanden met een van de volgende combinaties van kenmerken:

  * Versleuteling en reparse

  * Versleuteling en Single Instance Storage (SIS)

  * Versleuteling en hoofdlettergevoeligheid

  * Versleuteling en verspreiding

  * Hoofdlettergevoeligheid en SIS

  * Compressie en SIS

## <a name="next-steps"></a>Volgende stappen

* [Ondersteunings matrix voor back-up met Microsoft Azure Backup Server of System Center DPM](backup-support-matrix-mabs-dpm.md)