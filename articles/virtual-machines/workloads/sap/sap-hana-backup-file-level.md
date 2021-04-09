---
title: Azure Backup op bestands niveau SAP HANA | Microsoft Docs
description: Er zijn twee belang rijke back-upmogelijkheden voor SAP HANA op virtuele machines van Azure. dit artikel behandelt SAP HANA Azure Backup op bestands niveau
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0ff226a156721382a289af0f2a8a0f898a57bab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667969"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Azure Backup op bestands niveau SAP HANA

## <a name="introduction"></a>Introductie

Dit artikel is een verwant artikel voor het maken van een [back-upgids voor SAP Hana op Azure virtual machines](./sap-hana-backup-guide.md), dat een overzicht en informatie bevat over het aan de slag gaan en meer informatie over Azure backup service-en opslag momentopnamen. 

Verschillende VM-typen in azure staan een ander aantal aangesloten Vhd's toe. De exacte details worden gedocumenteerd in [grootten voor virtuele Linux-machines in azure](../../sizes.md). Voor de tests waarnaar in deze documentatie wordt verwezen, hebben we een Azure VM GS5 gebruikt, waarmee 64 gekoppelde gegevens schijven kunnen worden gekoppeld. Voor grotere SAP HANA systemen is er mogelijk al een groot aantal schijven gemaakt voor gegevens-en logboek bestanden, mogelijk in combi natie met software striping voor optimale IO-door Voer van de schijf. Lees het artikel [SAP Hana opslag configuraties van virtuele Azure-machines](./hana-vm-operations-storage.md)voor meer informatie over aanbevolen schijf configuraties voor SAP Hana implementaties op Azure-vm's. De aanbevelingen die worden gedaan, zijn ook inclusief schijf ruimte aanbevelingen voor lokale back-ups.

De standaard methode voor het beheren van back-ups/herstellen op bestands niveau is een back-up op basis van bestanden via SAP HANA Studio of via SAP HANA SQL-instructies. Lees voor meer informatie het artikel [SAP Hana verwijzing naar SQL-en systeem weergaven](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.05/en-US/3859e48180bb4cf8a207e15cf25a7e57.html).

![In deze afbeelding ziet u het dialoog venster van het menu-item back-up in SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

In deze afbeelding ziet u het dialoog venster van het menu-item back-up in SAP HANA Studio. Bij het kiezen &quot; &quot; van het type bestand moet een pad worden opgegeven in het bestands systeem waar SAP Hana de back-upbestanden schrijft. Herstellen werkt op dezelfde manier.

Hoewel deze keuze eenvoudig en direct klinkt, zijn er enkele overwegingen. Een virtuele machine van Azure heeft een beperking van het aantal gegevens schijven dat kan worden bijgevoegd. Het is mogelijk dat er geen capaciteit is om SAP HANA back-upbestanden op te slaan op de bestands systemen van de virtuele machine, afhankelijk van de grootte van de data base en de vereisten voor het door Voer van de schijf, wat software striping kan inhouden op meerdere gegevens schijven. Verderop in dit artikel vindt u diverse opties voor het verplaatsen van deze back-upbestanden en het beheren van beperkingen en prestaties voor bestands grootte bij het afhandelen van terabytes aan gegevens.

Een andere optie, die meer vrijheid biedt met betrekking tot de totale capaciteit, is Azure Blob-opslag. Hoewel één BLOB ook is beperkt tot 1 TB, is de totale capaciteit van één BLOB-container momenteel 500 TB. Daarnaast biedt klanten de keuze om deze optie te selecteren, ook wel ' &quot; cool &quot; Blob Storage ', die een kosten voordelen heeft. Zie [Azure Blob-opslag: dynamische, gekoelde en archief toegangs lagen](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal) voor meer informatie over cool Blob Storage.

Gebruik voor extra veiligheid een geografisch gerepliceerd opslag account om de SAP HANA back-ups op te slaan. Zie [Azure Storage redundantie](../../../storage/common/storage-redundancy.md) voor meer informatie over opslag redundantie en opslag replicatie.

Eén kan specifieke Vhd's voor SAP HANA back-ups worden geplaatst in een toegewezen back-upopslag account dat geo-repliceerbaar is. Het is ook mogelijk dat de Vhd's die de SAP HANA back-ups naar een geografisch gerepliceerd opslag account bewaren of naar een opslag account in een andere regio worden gekopieerd.

## <a name="azure-blobxfer-utility-details"></a>Details van het hulp programma Azure blobxfer

Om directory's en bestanden op te slaan in azure Storage, kan de ene CLI of Power shell gebruiken of een hulp programma ontwikkelen met behulp van een van de [Azure sdk's](https://azure.microsoft.com/downloads/). Er is ook een gebruiks klare hulp programma, AzCopy, voor het kopiëren van gegevens naar Azure Storage. (Zie [gegevens overdragen met het AzCopy-hulp programma van Command-Line](../../../storage/common/storage-use-azcopy-v10.md)).

Daarom is blobxfer gebruikt voor het kopiëren van SAP HANA back-upbestanden. Het is open source en wordt gebruikt door veel klanten in productie omgevingen en is beschikbaar op [github](https://github.com/Azure/blobxfer). Met dit hulp programma kunt u gegevens rechtstreeks naar Azure Blob-opslag of een Azure-bestands share kopiëren. Het biedt ook een aantal nuttige functies, zoals MD5-hash, of automatische parallellisme bij het kopiëren van een map met meerdere bestanden.

## <a name="sap-hana-backup-performance"></a>Back-upprestaties van SAP HANA
In dit hoofd stuk worden prestatie overwegingen besproken. De bereikte aantallen hebben mogelijk niet de meest recente status, omdat er een stabiele ontwikkeling is om een betere door voer naar Azure Storage te realiseren. Als gevolg hiervan moet u afzonderlijke tests uitvoeren voor uw configuratie en Azure-regio.

![Deze scherm afbeelding is van de SAP HANA back-upconsole in SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Deze scherm afbeelding toont de SAP HANA back-upconsole van SAP HANA Studio. Het duurde ongeveer 42 minuten om een back-up van 230 GB uit te voeren op één Azure Standard-HDD opslag schijf die is gekoppeld aan de HANA-VM met behulp van het XFS-bestands systeem op de ene schijf.

![Deze scherm afbeelding is van YaST op de SAP HANA test-VM](media/sap-hana-backup-file-level/one-backup-disk.png)

Deze scherm afbeelding is van YaST op de SAP HANA test-VM. U kunt de 1 TB enkele schijf weer geven voor SAP HANA back-up. Het duurde ongeveer 42 minuten om een back-up te maken van 230 GB. Daarnaast werden er 5 200 GB-schijven aangesloten en de software-RAID-md0 gemaakt, met Striping boven op deze vijf Azure-gegevens schijven.

![Dezelfde back-up op de software-RAID herhalen met Striping over vijf gekoppelde Azure Standard-opslag gegevens schijven](media/sap-hana-backup-file-level/five-backup-disks.png)

Herhaal dezelfde back-up op software-RAID met Striping over vijf gekoppelde Azure Standard-opslag gegevens schijven de back-uptijd van 42 minuten tot 10 minuten. De schijven zijn gekoppeld zonder caching naar de virtuele machine. In deze oefening ziet u het belang van schrijf doorvoer van de schijf voor een goede back-uptijd. U kunt overschakelen naar Azure Standard-SSD Storage of Azure Premium Storage om het proces verder te versnellen voor optimale prestaties. In het algemeen wordt opslag van Azure Standard-HDD niet aanbevolen en alleen voor demonstratie doeleinden gebruikt. Aanbeveling is een minimum van Azure Standard-SSD Storage of Azure Premium Storage voor productie systemen te gebruiken.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>SAP HANA back-upbestanden kopiëren naar Azure Blob-opslag
De waarden voor prestatie cijfers, duur van back-ups en het kopiëren van de duur worden mogelijk niet de meest recente status van de Azure-technologie weer gegeven. Micro soft verbetert Azure-opslag geleidelijk, zodat er meer door Voer en latenties worden geleverd. Daarom zijn de getallen alleen voor demonstratie doeleinden. U moet testen op uw persoonlijke behoeften in de Azure-regio van uw keuze om te kunnen beoordelen met de methode.

Een andere mogelijkheid om snel SAP HANA back-upbestanden op te slaan is Azure Blob-opslag. Eén BLOB-container heeft een limiet van ongeveer 500 TB, genoeg voor SAP HANA systemen, met behulp van M32ts, M32ls, M64ls en GS5 VM-typen van Azure, om voldoende SAP HANA back-ups te blijven. Klanten hebben de keuze tussen &quot; Hot &quot; &quot; -en koude &quot; Blob-opslag (Zie [Azure Blob-opslag: dynamische, koele en archief toegangs lagen](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)).

Met het blobxfer-hulp programma kunt u eenvoudig de SAP HANA back-upbestanden rechtstreeks naar Azure Blob-opslag kopiëren.

![Hier ziet u een overzicht van de bestanden van een volledige back-up van SAP HANA bestand](media/sap-hana-backup-file-level/list-of-backups.png)

U kunt de bestanden van een volledige back-up van SAP HANA bestand bekijken. Van de vier bestanden heeft de grootste versie van 230 GB.

![Het duurde ongeveer 3000 seconden om de 230 GB naar een BLOB-container van het Azure Standard-opslag account te kopiëren](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

De MD5-hash niet gebruiken in de eerste test, het duurde ongeveer 3000 seconden om de 230 GB te kopiëren naar een BLOB-container van het Azure Standard-opslag account.

Met de HANA Studio-back-upconsole kan één de maximale bestands grootte van HANA-back-upbestanden beperken. In de voorbeeld omgeving zijn de prestaties verbeterd doordat er meerdere kleinere back-upbestanden zijn, in plaats van een groot 230 GB-bestanden.

Het instellen van de maximale grootte van het back-upbestand voor de HANA-zijde heeft&#39;t de back-uptijd verbeterd, omdat de bestanden opeenvolgend worden geschreven. De maximale bestands grootte is ingesteld op 60 GB, waardoor de back-up vier grote gegevens bestanden heeft gemaakt in plaats van het bestand 230-GB. Het gebruik van meerdere back-upbestanden kan nood zakelijk zijn voor het maken van back-ups van HANA-data bases als uw Back-updoelen beperkingen hebben op de bestands grootte van BLOB

![Als u de parallelle uitvoering van het blobxfer-hulp programma wilt testen, is de maximale bestands grootte voor HANA-back-ups vervolgens ingesteld op 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Voor het testen van de parallellisme van het hulp programma blobxfer is de maximale bestands grootte voor HANA-back-ups ingesteld op 15 GB, wat heeft geleid tot 19 back-upbestanden. Met deze configuratie is de tijd ingesteld voor blobxfer om de 230 GB naar Azure Blob-opslag te kopiëren van 3000 seconden naar meer dan 875 seconden.

Wanneer u het kopiëren van back-ups die worden uitgevoerd op lokale schijven naar andere locaties, zoals Azure Blob-opslag, bekijkt, moet u er rekening mee houden dat de band breedte die wordt gebruikt door een uiteindelijke parallelle kopie, wordt gecontroleerd op basis van het netwerk-of opslag quotum van uw afzonderlijke VM-type. Als gevolg hiervan moet u de duur van de kopie afstemmen op het netwerk en de opslag bandbreedte van de normale werk belasting die wordt uitgevoerd in de virtuele machine. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>SAP HANA back-upbestanden kopiëren naar NFS-share

Microsoft Azure systeem eigen NFS-shares aanbieden via [Azure NetApp files](https://azure.microsoft.com/services/netapp/). U kunt verschillende volumes van een dozijn van TBs maken om back-ups op te slaan en te beheren. U kunt de volumes ook moment opnamen maken op basis van de technologie van NetApp. Azure NetApp Files (ANF) wordt aangeboden in drie verschillende service niveaus die verschillende opslag doorvoer bieden. Lees de artikel [service niveaus voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md)voor meer informatie. U kunt een NFS-volume maken en koppelen vanuit ANF zoals beschreven in het artikel [Snelstartgids: Azure NetApp files instellen en een NFS-volume maken](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal).

Naast het gebruik van systeem eigen NFS-volumes van Azure via ANF, zijn er verschillende mogelijkheden om eigen implementaties te maken die gebruikmaken van NFS-shares in Azure. Het nadeel is dat u zelf deze oplossingen moet implementeren en beheren. Enkele van deze mogelijkheden worden beschreven in de volgende artikelen:

- [Hoge Beschik baarheid voor NFS op Azure Vm's op SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- [GlusterFS in Azure VM's in Red Hat Enterprise Linux voor SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)

NFS-shares die zijn gemaakt met behulp van hierboven beschreven, kunnen worden gebruikt om HANA-back-ups rechtstreeks uit te voeren of om back-ups te kopiëren die zijn uitgevoerd op lokale schijven naar die NFS-shares.

> [!NOTE]
> SAP HANA ondersteuning voor NFS v3 en NFS v4. x. Een andere indeling zoals SMB met CIFS File System wordt niet ondersteund voor het schrijven van HANA-back-ups. Zie ook [SAP-ondersteunings opmerking #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>SAP HANA back-upbestanden kopiëren naar Azure Files

Het is mogelijk een Azure Files share te koppelen aan een virtuele machine in azure Linux. In het artikel [Azure File Storage gebruiken met Linux](../../../storage/files/storage-how-to-use-files-linux.md) vindt u informatie over het uitvoeren van de configuratie. Lees het artikel [Azure files schaal baarheid en prestatie doelen](../../../storage/files/storage-files-scale-targets.md)voor beperkingen van op Azure files of Azure Premium-bestanden.

> [!NOTE]
> SMB met CIFS File System wordt niet ondersteund door SAP HANA om HANA-back-ups te schrijven. Zie ook [SAP-ondersteunings opmerking #1820529](https://launchpad.support.sap.com/#/notes/1820529). Als gevolg hiervan kunt u deze oplossing alleen gebruiken als eind bestemming van een HANA-database back-up die rechtstreeks is uitgevoerd op lokale gekoppelde schijven
> 

In een test die is uitgevoerd op Azure Files, hebben geen Azure Premium-bestanden ongeveer 929 seconden geduurd om 19 back-upbestanden te kopiëren met een totaal volume van 230 GB. We verwachten dat de tijd die nodig is om Azure Premium-bestanden beter te gebruiken. U moet er echter rekening mee houdt dat u de interesses van een snelle kopie wilt afstemmen op de vereisten die uw werk belasting op de netwerk bandbreedte heeft. Omdat elk Azure VM-type het quotum voor netwerk bandbreedte afdwingt, moet u binnen het bereik van het quotum blijven met uw werk belasting en de kopie van de back-upbestanden.

![In deze afbeelding ziet u dat het ongeveer 929 seconden duurde om 19 SAP HANA back-upbestanden te kopiëren](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Het opslaan van SAP HANA back-upbestanden in azure files zou een interessante optie kunnen zijn. Met name met de verbeterde latentie en door Voer van Azure Premium-bestanden.

## <a name="next-steps"></a>Volgende stappen
* In de [back-upgids voor SAP Hana op Azure virtual machines](sap-hana-backup-guide.md) vindt u een overzicht en informatie over aan de slag.
* Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood gevallen op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het tot stand brengen van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure (grote exemplaren).
