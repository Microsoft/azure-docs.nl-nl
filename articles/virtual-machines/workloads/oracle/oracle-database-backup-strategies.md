---
title: Oracle Database in azure Virtual Machines back-upstrategieen
description: Opties voor het maken van back-ups van Oracle-data bases in een Azure Linux VM-omgeving.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 8a1eb1c21663e0294cd384daa0ba644adf78007a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673216"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Oracle Database in azure Linux VM-back-upstrategieën

Met database back-ups wordt de data base beschermd tegen gegevens verlies vanwege een fout in het opslag onderdeel en de fout in het Data Center. Ze kunnen ook worden gebruikt voor het herstellen van een menselijke fout en een manier om een Data Base te klonen voor ontwikkelings-en test doeleinden. 

In azure, omdat alle opslag uiterst redundant is en het verlies van een of meer schijven niet leidt tot een database storing, zullen back-ups meestal worden gebruikt om te beschermen tegen menselijke fouten, voor het klonen van bewerkingen of het bewaren van gegevens voor regelgevende doel einden. Ze zijn ook een manier om te beschermen tegen regionale uitval wanneer een nood herstel technologie zoals Dataguard configureren niet in gebruik is. In dit geval moeten de back-ups in verschillende Azure-regio's worden opgeslagen met behulp van geo-redundante replicatie, zodat deze beschikbaar is buiten de regio van de primaire data base.

## <a name="azure-storage"></a>Azure Storage 

De [Azure Storage services](../../../storage/common/storage-introduction.md) zijn de oplossing voor de Cloud opslag van micro soft voor moderne scenario's voor gegevens opslag. Azure Storage biedt een aantal services die kunnen worden gebruikt om externe opslag te koppelen aan de virtuele machine van Azure Linux, geschikt als back-upmedia voor Oracle-data bases. Een back-upprogramma zoals Oracle RMAN is vereist om een back-up-of herstel bewerking te initiëren en de back-up naar/van de Azure Storage-service te kopiëren.
 
Azure Storage services bieden de volgende voor delen:

-  Duurzaam en maximaal beschikbaar. Redundantie zorgt ervoor dat uw gegevens veilig zijn in geval van tijdelijke hardwarefouten. Alle opslag is standaard drievoudig gespiegeld. U kunt er ook voor kiezen om gegevens in data centers of geografische regio's te repliceren voor extra beveiliging tegen lokale rampen of natuur rampen. Op deze manier gerepliceerde gegevens blijven maximaal beschikbaar in het geval van een stroomstoring.

-  Beveiligd. Alle gegevens die naar een Azure Storage-account worden geschreven, worden versleuteld door de service. Azure Storage biedt u gedetailleerde controle over wie toegang tot uw gegevens heeft.

-  Aanpas. Azure Storage is in hoge mate schaalbaar om te voldoen aan de gegevensopslag- en prestatiebehoeften van de huidige toepassingen.

-  Bijgehouden. Azure verwerkt onderhoud van hardware, updates en kritieke problemen voor u.

-  Toegankelijk. Gegevens in Azure Storage zijn overal ter wereld toegankelijk via HTTP of HTTPS. Micro soft biedt client bibliotheken voor Azure Storage in verschillende talen, waaronder .NET, Java, Node.js, Python, PHP, Ruby, Go en anderen, en een rijp REST API. Azure Storage ondersteunt scripts in Azure PowerShell of Azure CLI. En Azure Portal en Azure Storage Explorer bieden handige visuele oplossingen voor het werken met uw gegevens.

Het Azure Storage-platform bevat de volgende gegevens services die geschikt zijn voor gebruik als back-upmedia voor de Oracle-Data Base:

-  Azure Blobs: een in hoge mate schaalbaar objectarchief voor tekst en binaire gegevens. Biedt ook ondersteuning voor big data Analytics via Data Lake Storage Gen2.

-  Azure Files: beheerde bestands shares voor Cloud-en on-premises implementaties.

-  Azure-schijven: opslag volumes op blok niveau voor Azure-Vm's.

### <a name="cross-regional-storage-mounting"></a>Kruis regionale opslag koppelen

De mogelijkheid om toegang te krijgen tot back-upopslag in verschillende regio's is een belang rijk aspect van BCDR en is handig voor het klonen van data bases van back-ups in verschillende geografische regio's. Azure-Cloud opslag biedt vijf [Redundantie](../../../storage/common/storage-redundancy.md) niveaus
- Lokaal redundante opslag [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) waar uw gegevens drie keer worden gerepliceerd binnen één fysieke locatie in de primaire regio.  
- Zone redundant Storage [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) waarbij uw gegevens worden beveiligd door LRS in de primaire regio en synchroon via drie beschikbaarheids zones (AZ) in de primaire regio worden gerepliceerd. Elke AZ wordt ook LRS beveiligd. 
- Geo-Redundant Storage [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) waar uw gegevens worden beveiligd door LRS in de primaire regio en die asynchroon worden gerepliceerd naar een secundaire regio, waar ze ook worden beveiligd door LRS.
- Geo-zone-redundante opslag [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) kopieert uw gegevens synchroon over drie Azure-beschikbaarheids zones in de primaire regio met behulp van ZRS. Vervolgens worden uw gegevens asynchroon gekopieerd naar één fysieke locatie in de secundaire regio. De gegevens van alle locaties worden ook beveiligd door LRS. 
- Lees toegang Geo-Redundant Storage [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) en de geo-zone-redundante opslag met lees toegang [(RA-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) staat alleen-lezen toegang toe voor gegevens die naar de secundaire regio worden gerepliceerd. 

#### <a name="blob-storage-and-file-storage"></a>Blob Storage en File Storage

Wanneer u Azure Files gebruikt met SMB-of NFS 4,1 (preview)-protocollen om als back-upopslag te koppelen, moet u er rekening mee houden dat Azure Files geen ondersteuning biedt voor geografisch redundante opslag met lees toegang (RA-GRS) en een geografisch redundante opslag met lees toegang (RA-GZRS). 

Als de vereiste voor back-upopslag meer dan 5 TiB Azure Files vereist een [functie voor grote bestands shares](../../../storage/files/storage-files-planning.md) die geen ondersteuning biedt voor GRS of GZRS-redundantie: alleen LRS wordt ondersteund. 

Azure-Blob gekoppeld met NFS 3,0 (preview)-protocol biedt momenteel alleen ondersteuning voor LRS en ZRS-redundantie.  

Azure-Blob die is geconfigureerd met een optie voor redundantie kan worden gekoppeld met behulp van Blobfuse.

#### <a name="recovery-services-vault"></a>Recovery Services-kluis 

Een Recovery Services-kluis is een beheerentiteit waarmee herstelpunten worden opgeslagen die in de loop van de tijd zijn gemaakt en die een interface biedt voor het uitvoeren van back-upbewerkingen. Dit omvat het maken van back-ups op aanvraag, het uitvoeren van herstelbewerkingen en het maken van back-upbeleid.
Azure Backup beheert automatisch de opslag voor de kluis. U moet opgeven hoe die opslag wordt gerepliceerd op het moment dat deze wordt gemaakt. Het kan niet worden gewijzigd nadat de items in de kluis zijn beveiligd. Kies de geo-redundante instelling voor regionale redundantie.

Als u van plan bent om terug te gaan naar een secundaire [Azure-gekoppelde regio](../../../best-practices-availability-paired-regions.md) , schakelt u de functie voor het [terugzetten van meerdere regio's](../../../backup/backup-create-rs-vault.md) in. Wanneer het terugzetten van meerdere regio's is ingeschakeld, wordt de back-upopslag verplaatst van GRS naar geografisch redundante opslag met lees toegang (RA-GRS). 

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Blob-opslag is een uiterst rendabele, duurzame en veilige Cloud opslag service die wordt gebruikt voor het opslaan van grote hoeveel heden ongestructureerde gegevens en is een ideaal medium voor het gebruik van Oracle Database back-ups. Azure Blob-opslag kan worden gekoppeld aan virtuele machines van Azure Linux met behulp van NFS v 3.0-protocol of Blobfuse (Linux-ZEKERing).

#### <a name="azure-blob-blobfuse"></a>Azure Blob Blobfuse

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) is een open-source project dat is ontwikkeld om een virtueel bestands systeem te bieden dat wordt ondersteund door de Azure Blob-opslag. De open-source bibliotheek van libzekering wordt gebruikt om te communiceren met de Linux-kernelpatch en implementeert de bestandssysteem bewerkingen met behulp van de Azure Storage Blob REST-Api's. Blobfuse is momenteel beschikbaar voor de distributies van Ubuntu en CentOS/RedHat. Het is ook beschikbaar voor Kubernetes met behulp van het [CSI-stuur programma](https://github.com/kubernetes-sigs/blob-csi-driver). 

Hoewel alomtegenwoordige in alle Azure-regio's en werkt met alle typen opslag accounts, met inbegrip van algemeen gebruik v1/v2 en Azure Data Lake Store Gen2, is de prestaties die worden aangeboden door Blobfuse minder dan alternatieve protocollen, zoals SMB of NFS. Voor geschiktheid als back-upmedia voor data bases kunt u het gebruik van SMB-of [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) -protocollen aanbevelen om Azure Blob-opslag te koppelen. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v 3.0 (preview-versie)

Azure-ondersteuning voor het Network File System (NFS) v 3.0-protocol is nu beschikbaar als preview-versie. Met [NFS](../../../storage/blobs/network-file-system-protocol-support.md) -ondersteuning kunnen Windows-en Linux-clients een BLOB storage-container koppelen aan een Azure VM. 

De open bare preview van NFS 3,0 biedt ondersteuning voor GPV2-opslag accounts met de prestaties van de Standard-laag in de volgende regio's: 
- Australië - oost
- Korea - centraal
- VS Zuid-Centraal. 

Het opslag account dat wordt gebruikt voor NFS-koppeling moet zich in een VNet bevinden om netwerk beveiliging te garanderen. Azure Active Directory-beveiliging (AD) en toegangs beheer lijsten (Acl's) worden nog niet ondersteund in accounts waarvoor ondersteuning voor NFS 3,0-protocol is ingeschakeld.

### <a name="azure-files"></a>Azure Files

[Azure files](../../../storage/files/storage-files-introduction.md) is een volledig beheerd gedistribueerd bestands systeem op basis van de cloud dat kan worden gekoppeld aan on-premises of in de cloud gebaseerde Windows-, Linux-of macOS-clients.

Azure Files biedt volledig beheerde cross-platform bestands shares in de cloud die toegankelijk zijn via het SMB-protocol (Server Message Block) en het NFS-protocol (Network File System) (preview). Azure Files biedt momenteel geen ondersteuning voor multi-protocol toegang, zodat een share alleen een NFS-share of een SMB-share kan zijn. Het is raadzaam om te bepalen welk protocol het beste bij uw behoeften past voordat u Azure-bestands shares maakt.

Azure-bestands shares kunnen ook worden beveiligd door Azure Backup naar de Recovery Services-kluis, waardoor de Oracle RMAN-back-ups een extra beveiligingslaag bieden.

#### <a name="azure-files-nfs-v41-preview"></a>Azure Files NFS v 4.1 (preview-versie)

Azure-bestands shares kunnen worden gekoppeld in Linux-distributies met behulp van het NFS-protocol (Network File System) v 4.1. In de preview-versie zijn er een aantal beperkingen voor ondersteunde functies, die [hier](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)worden beschreven. 

De preview-versie van Azure Files NFS v 4.1 is ook beperkt tot de volgende [regio's](../../../storage/files/storage-files-how-to-mount-nfs-shares.md):
- VS-Oost (LRS en ZRS)
- US - oost 2
- US - west 2
- Europa -west
- Azië - zuidoost
- Verenigd Koninkrijk Zuid
- Australië - oost

#### <a name="azure-files-smb-30"></a>Azure Files SMB 3,0

Azure-bestands shares kunnen worden gekoppeld in Linux-distributies met behulp van de SMB-kernel-client. Het CIFS-protocol (Common Internet File System), beschikbaar op Linux-distributies, is een dialect van SMB. Wanneer u Azure Files SMB op Linux-Vm's koppelt, wordt het gekoppeld als CIFS-type bestands systeem en moet het CIFS-pakket worden geïnstalleerd. 

Azure Files SMB is algemeen beschikbaar in alle Azure-regio's en wordt dezelfde prestatie kenmerken als NFS v 3.0 en v 4.1-protocollen weer gegeven. Dit is de aanbevolen methode voor het bieden van back-upopslag media naar Azure Linux-Vm's.  

Er zijn twee ondersteunde versies van SMB beschikbaar, SMB 2,1 en SMB 3,0, met de laatste aanbevolen waarde, omdat deze ondersteuning biedt voor versleuteling in de door voer. De versies van de Linux-kernel hebben echter verschillende ondersteuning voor SMB 2,1 en 3,0 en u moet de tabel [hier](../../../storage/files/storage-how-to-use-files-linux.md) controleren om ervoor te zorgen dat uw toepassing SMB 3,0 ondersteunt. 

Omdat Azure Files is ontworpen om een service voor bestands shares met meerdere gebruikers te zijn, zijn er bepaalde kenmerken die u moet afstemmen om deze beter geschikt te maken als back-upopslag media. Het wordt aanbevolen om caching uit te scha kelen en de gebruikers-en groeps-Id's voor het gemaakte bestand in te stellen.

## <a name="azure-netapp-files"></a>Azure NetApp Files

De [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) -service is een volledige opslag oplossing voor Oracle-data bases in azure-vm's. Het is gebouwd op basis van een hoogwaardige bestands opslag op bedrijfs niveau en biedt ondersteuning voor elk type werk belasting en is standaard Maxi maal beschikbaar. In combi natie met het dNFS-stuur programma (Oracle direct NFS) biedt Azure NetApp Files een zeer geoptimaliseerde opslaglaag voor de Oracle Database.

Azure NetApp Files biedt efficiënte, op opslag gebaseerde moment opnamen kopieën op het onderliggende opslag systeem dat gebruikmaakt van een omleidings mechanisme (RoW). Kopieën van moment opnamen zijn zeer snel te nemen en te herstellen, maar ze fungeren alleen als eerste regel-of-verdedigings team. dit account kan worden gebruikt voor het grootste deel van de vereiste herstel bewerkingen van een bepaalde organisatie, wat vaak het geval is bij het herstellen van menselijke fouten. Momentopname kopieën zijn echter geen volledige back-up. Om alle vereisten voor back-up en herstel te kunnen dekken, moeten externe momentopname replica's en/of andere back-upkopieën worden gemaakt in een externe Geografie om te worden beschermd tegen regionale storingen. Lees dit [rapport](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf)voor meer informatie over het gebruik van NetApp-bestanden voor Oracle-data bases in Azure.

## <a name="azure-backup-service"></a>Azure Backup-Service

[De Azure backup](../../../backup/backup-overview.md) is een volledig beheerde PaaS die eenvoudige, veilige en rendabele oplossingen biedt om een back-up van uw gegevens te maken en deze te herstellen vanuit de Microsoft Azure Cloud. Azure Backup kunt back-ups maken en herstellen van on-premises clients, Azure-VM'S, Azure Files-shares, evenals SQL Server, Oracle, MySQL, PostreSQL en SAP HANA data bases op Azure-Vm's. 

Azure Backup biedt onafhankelijke en geïsoleerde back-ups om te voorkomen dat oorspronkelijke gegevens per ongeluk worden vernietigd. Back-ups worden opgeslagen in een [Recovery Services kluis](../../../backup/backup-azure-recovery-services-vault-overview.md) met ingebouwd beheer van herstel punten. Configuratie en schaalbaarheid zijn eenvoudig, back-ups zijn geoptimaliseerd en kunt u eenvoudig naar behoefte herstellen. Er wordt gebruikgemaakt van de onderliggende kracht en onbeperkte schaal van de Azure-Cloud om hoge Beschik baarheid te bieden zonder onderhoud of bewakings overhead. Azure Backup beperkt de hoeveelheid inkomende of uitgaande gegevens die u overdraagt, of factureert de gegevens die worden overgedragen, en de gegevens worden tijdens de overdracht en op rest beveiligd. 

Om ervoor te zorgen dat duurzaamheid Azure Backup meerdere typen replicatie biedt om uw back-upgegevens Maxi maal beschikbaar te houden.

- Met lokaal redundante opslag [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) worden uw gegevens drie keer gerepliceerd (er worden drie kopieën gemaakt van uw gegevens) in een opslag schaal eenheid in een Data Center.
- Geo-redundante opslag [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) is de standaard en aanbevolen replicatie optie. Met GRS worden uw gegevens gerepliceerd naar een secundaire regio (honderden kilometers verwijderd van de primaire locatie van de brongegevens).

Een kluis die is gemaakt met GRS-redundantie bevat de optie voor het configureren van de functie voor het [terugzetten van meerdere regio's](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) , waarmee u gegevens in een secundaire, aan Azure gekoppelde regio kunt herstellen.

Azure Backup-service biedt een [Framework](../../../backup/backup-azure-linux-app-consistent.md) om toepassings consistentie te krijgen tijdens het maken van back-ups van Windows-en Linux-vm's voor diverse toepassingen, zoals Oracle, MySQL, Mongo DB, SAP Hana en PostgreSQL, toepassings consistente moment opnamen genoemd. Dit omvat het aanroepen van een pre-script (om de toepassingen stil te leggen) voordat een moment opname van schijven wordt gemaakt en het aanroepen van post script (opdrachten voor het opheffen van de toepassingen) nadat de moment opname is voltooid, om de toepassingen te retour neren naar de normale modus. Hoewel voor beelden van pre-scripts en post-scripts worden gegeven in GitHub, is het maken en onderhouden van deze scripts uw verantwoordelijkheid. In het geval van Oracle moet de data base zich in de archivelog-modus bevinden om online back-ups mogelijk te maken. de bijbehorende opdrachten voor het starten en beëindigen van de Data Base worden uitgevoerd in de pre-en post-scripts, die u moet maken en onderhouden. 

Azure Backup biedt nu een [verbeterd pre-script en post script-Framework](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), dat momenteel als preview-versie beschikbaar is, waarbij de Azure backup-service vooraf gebundelde pre-scripts en post scripts voor geselecteerde toepassingen biedt. Azure Backup gebruikers hoeft alleen de naam van de toepassing te noemen en de back-up van Azure VM roept automatisch de relevante pre-scripts en post scripts aan. De verpakte pre-scripts en post scripts worden onderhouden door het Azure Backup-team, zodat gebruikers kunnen worden verzekerd van de ondersteuning, het eigendom en de geldigheid van deze scripts. Momenteel zijn de ondersteunde toepassingen voor het uitgebreide Framework Oracle en MySQL, met meer toepassings typen die in de toekomst worden verwacht. De moment opname is een volledige kopie van de opslag en niet een incrementele of gekopieerde moment opname van een schrijf bewerking. het is dus een effectief medium om uw data base van te herstellen.

## <a name="next-steps"></a>Volgende stappen

- [Oracle Database Snelstartgids maken](oracle-database-quick-create.md)
- [Back-up maken van Oracle Database naar Azure Files](oracle-database-backup-azure-storage.md)
- [Back-ups maken van Oracle Database met behulp van Azure Backup-Service](oracle-database-backup-azure-backup.md)


