---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a662598efdca05769c7da9fbeecdf692dccdacb5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028560"
---
## <a name="overview"></a>Overzicht
Azure Storage biedt de mogelijkheid om moment opnamen van blobs te maken. Met moment opnamen wordt de BLOB-status op dat moment vastgelegd. In dit artikel wordt een scenario beschreven waarin u back-ups van virtuele-machine schijven kunt onderhouden met behulp van moment opnamen. U kunt deze methodologie gebruiken wanneer u ervoor kiest om Azure Backup en de herstel service niet te gebruiken en een aangepaste back-upstrategie voor de virtuele-machine schijven te maken. Voor virtuele machines met bedrijfs-of essentiële werk belastingen is het raadzaam om [Azure backup](../articles/backup/backup-azure-vms-introduction.md) te gebruiken als onderdeel van de back-upstrategie.  

Virtuele-machine schijven van Azure worden opgeslagen als pagina-blobs in Azure Storage. Omdat we een back-upstrategie voor virtuele-machine schijven in dit artikel beschrijven, verwijzen we naar moment opnamen in de context van pagina-blobs. Zie [een moment opname van een BLOB maken](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)voor meer informatie over moment opnamen.

## <a name="what-is-a-snapshot"></a>Wat is een moment opname?
Een BLOB-moment opname is een alleen-lezen versie van een blob die op een bepaald moment wordt vastgelegd. Zodra een moment opname is gemaakt, kan deze worden gelezen, gekopieerd of verwijderd, maar niet worden gewijzigd. Moment opnamen bieden een manier om een back-up te maken van een blob, wanneer deze op een bepaald moment wordt weer gegeven. Totdat REST versie 2015-04-05 is, hebt u de mogelijkheid om volledige moment opnamen te kopiëren. Met de REST-versie 2015-07-08 en hoger kunt u ook incrementele moment opnamen kopiëren.

## <a name="full-snapshot-copy"></a>Volledige kopie van de moment opname
U kunt moment opnamen naar een ander opslag account kopiëren als een blob om back-ups van de basis-BLOB te bewaren. U kunt ook een moment opname kopiëren via de bijbehorende basis-blob, zoals het herstellen van de BLOB naar een eerdere versie. Wanneer een moment opname wordt gekopieerd van het ene opslag account naar het andere, neemt het dezelfde ruimte in beslag als de blob van de basis pagina. Daarom is het kopiëren van hele moment opnamen van het ene opslag account langzaam en wordt er veel ruimte in het doel-opslag account verbruikt.

> [!NOTE]
> Als u de basis-BLOB naar een andere bestemming kopieert, worden de moment opnamen van de BLOB niet samen gekopieerd. En als u een basis-BLOB overschrijft met een kopie, worden moment opnamen die zijn gekoppeld aan de basis-BLOB niet beïnvloed en blijven deze intact onder de naam van de basis-blob.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Back-ups maken van schijven met behulp van moment opnamen
Als back-upstrategie voor de schijven van uw virtuele machine kunt u periodieke moment opnamen van de schijf of de pagina-BLOB nemen en deze kopiëren naar een ander opslag account met behulp van hulpprogram ma's zoals een [Kopieer-BLOB](/rest/api/storageservices/Copy-Blob) -bewerking of [AzCopy](../articles/storage/common/storage-use-azcopy-v10.md). U kunt een moment opname kopiëren naar een doel pagina-blob met een andere naam. De resulterende doel pagina-blob is een Beschrijf bare pagina-Blob en geen moment opname. Verderop in dit artikel worden de stappen beschreven voor het maken van back-ups van virtuele machine schijven met behulp van moment opnamen.

### <a name="restore-disks-using-snapshots"></a>Schijven terugzetten met behulp van moment opnamen
Wanneer het tijd is om uw schijf te herstellen naar een stabiele versie die eerder is vastgelegd in een van de moment opnamen van de back-up, kunt u een moment opname kopiëren via de blob van de basis pagina. Nadat de moment opname is gepromoveerd tot de blob van de basis pagina, blijft de moment opname aanwezig, maar wordt de bron overschreven met een kopie die zowel kan worden gelezen als geschreven. Verderop in dit artikel worden de stappen beschreven voor het herstellen van een vorige versie van de schijf uit de moment opname.

### <a name="implementing-full-snapshot-copy"></a>Volledige momentopname kopie implementeren
U kunt een volledige kopie van de moment opname implementeren door het volgende te doen:

* Maak eerst een moment opname van de basis-blob met behulp van de bewerking van de [moment opname-BLOB](/rest/api/storageservices/Snapshot-Blob) .
* Kopieer vervolgens de moment opname naar een doel opslag account met [copy BLOB](/rest/api/storageservices/Copy-Blob).
* Herhaal dit proces voor het onderhouden van back-ups van uw basis-blob.

## <a name="incremental-snapshot-copy"></a>Kopie van incrementele moment opname
De nieuwe functie in de [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) -API biedt een veel betere manier om een back-up te maken van de moment opnamen van uw pagina-blobs of-schijven. De API retourneert de lijst met wijzigingen tussen de basis-Blob en de moment opnamen, waardoor de hoeveelheid opslag ruimte die wordt gebruikt voor het back-upaccount wordt verminderd. De API ondersteunt pagina-blobs op Premium Storage en standaard opslag. Met deze API kunt u sneller en efficiëntere back-upoplossingen maken voor Azure-Vm's. Deze API is beschikbaar met de REST versie 2015-07-08 en hoger.

Met incrementele momentopname kopie kopieert u van het ene opslag account naar het andere, het verschil tussen

* Basis-Blob en de bijbehorende moment opname of
* Een wille keurige twee moment opnamen van de basis-BLOB

Als aan de volgende voor waarden wordt voldaan,

* De blob is gemaakt op januari-1-2016 of hoger.
* De blob is niet overschreven met [PutPage](/rest/api/storageservices/Put-Page) of de [BLOB kopiëren](/rest/api/storageservices/Copy-Blob) tussen twee moment opnamen.

>[!NOTE]
>Deze functie is beschikbaar voor Premium-en Standard Azure-pagina-blobs.

Wanneer u een aangepaste back-upstrategie hebt die moment opnamen gebruikt, kan het kopiëren van de moment opnamen van het ene naar het andere opslag account traag zijn en veel opslag ruimte in beslag nemen. In plaats van de volledige moment opname te kopiëren naar een back-upopslag account, kunt u het verschil tussen opeenvolgende moment opnamen naar een BLOB van een back-uppagina schrijven. Op deze manier wordt de tijd die nodig is om te kopiëren en de ruimte voor het opslaan van back-ups aanzienlijk gereduceerd.

### <a name="implementing-incremental-snapshot-copy"></a>Incrementele momentopname kopie implementeren
U kunt incrementele momentopname kopie implementeren door het volgende te doen:

* Maak een moment opname van de basis-blob met behulp van een [moment opname-BLOB](/rest/api/storageservices/Snapshot-Blob).
* Kopieer de moment opname naar het doel-back-upopslag account in dezelfde of een andere Azure-regio met [copy BLOB](/rest/api/storageservices/Copy-Blob). Dit is de blob van de back-uppagina. Maak een moment opname van de blob van de back-uppagina en sla deze op in het back-upaccount.
* Maak een andere moment opname van de basis-blob met behulp van de moment opname-blob.
* Het verschil tussen de eerste en tweede moment opnamen van de basis-BLOB ophalen met behulp van [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges). Gebruik de nieuwe para meter **prevsnapshot** om de datum/tijd op te geven van de moment opname waarmee u het verschil wilt ophalen. Als deze para meter aanwezig is, bevat het REST antwoord alleen de pagina's die zijn gewijzigd tussen de moment opname van de doel groep en de vorige moment opname, inclusief heldere pagina's.
* Gebruik [PutPage](/rest/api/storageservices/Put-Page) om deze wijzigingen toe te passen op de back-uppagina-blob.
* Maak ten slotte een moment opname van de blob van de back-uppagina en sla deze op in het opslag account voor back-ups.

In de volgende sectie wordt beschreven hoe u back-ups van schijven kunt onderhouden met behulp van een incrementele kopie van de moment opname

## <a name="scenario"></a>Scenario
In deze sectie wordt een scenario beschreven met een aangepaste back-upstrategie voor virtuele-machine schijven met behulp van moment opnamen.

Overweeg een Azure-VM van de DS-serie met een Premium Storage P30-schijf. De P30-schijf met de naam *mypremiumdisk* wordt opgeslagen in een Premium-opslag account met de naam *mypremiumaccount*. Een standaard opslag account met de naam *mybackupstdaccount* wordt gebruikt voor het opslaan van de back-up van *mypremiumdisk*. We willen een moment opname van *mypremiumdisk* elke 12 uur blijven gebruiken.

Zie [een opslag account maken](../articles/storage/common/storage-account-create.md)voor meer informatie over het maken van een opslag account.

Zie [Azure VM-back-ups plannen](../articles/backup/backup-azure-vms-introduction.md)voor meer informatie over het maken van back-ups van virtuele Azure-machines.

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Stappen voor het onderhouden van back-ups van een schijf met incrementele moment opnamen
In de volgende stappen wordt beschreven hoe u moment opnamen van *mypremiumdisk* maakt en de back-ups onderhoudt in *mybackupstdaccount*. De back-up is een standaard pagina-blob met de naam *mybackupstdpageblob*. De blob van de back-uppagina weerspiegelt altijd dezelfde status als de laatste moment opname van *mypremiumdisk*.

1. Maak de blob van de back-uppagina voor uw Premium-opslag schijf door een moment opname te maken van *mypremiumdisk* met de naam *mypremiumdisk_ss1*.
2. Kopieer deze moment opname naar mybackupstdaccount als een pagina-blob met de naam *mybackupstdpageblob*.
3. Maak een moment opname van *mybackupstdpageblob* met de naam *mybackupstdpageblob_ss1*, met behulp van een [moment opname-BLOB](/rest/api/storageservices/Snapshot-Blob) en sla deze op in *mybackupstdaccount*.
4. Maak tijdens het back-upvenster een andere moment opname van *mypremiumdisk*, zeg *mypremiumdisk_ss2* en sla deze op in *mypremiumaccount*.
5. De incrementele wijzigingen ophalen tussen de twee moment opnamen, *mypremiumdisk_ss2* en *mypremiumdisk_ss1*, met behulp van [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) op *mypremiumdisk_ss2* waarbij de para meter **prevsnapshot** is ingesteld op de tijds tempel van *mypremiumdisk_ss1*. Schrijf deze incrementele wijzigingen naar de BLOB- *mybackupstdpageblob* van de back-uppagina in *mybackupstdaccount*. Als er in de incrementele wijzigingen verwijderde bereiken zijn, moeten deze worden gewist uit de blob van de back-uppagina. Gebruik [PutPage](/rest/api/storageservices/Put-Page) om incrementele wijzigingen te schrijven naar de back-uppagina-blob.
6. Maak een moment opname van de back-uppagina BLOB *mybackupstdpageblob*, genaamd *mybackupstdpageblob_ss2*. Verwijder de vorige momentopname *mypremiumdisk_ss1* uit een Premium-opslag account.
7. Herhaal stap 4-6 elk back-upvenster. Op deze manier kunt u back-ups van *mypremiumdisk* in een Standard-opslag account onderhouden.

![Maak een back-up van de schijf met incrementele moment opnamen](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Stappen voor het herstellen van een schijf uit moment opnamen
In de volgende stappen wordt beschreven hoe u de Premium-schijf herstelt, *mypremiumdisk* een eerdere moment opname van het back-upopslag account *mybackupstdaccount*.

1. Bepaal het tijdstip waarop u de Premium-schijf naar wilt terugzetten. Stel dat het een moment opname is *mybackupstdpageblob_ss2*, die wordt opgeslagen in het back-upopslag account *mybackupstdaccount*.
2. Promoot in mybackupstdaccount de *mybackupstdpageblob_ss2* van de moment opname als de nieuwe BLOB- *mybackupstdpageblobrestored* van de back-upbasis pagina.
3. Maak een moment opname van deze teruggezette back-uppagina-blob met de naam *mybackupstdpageblobrestored_ss1*.
4. Kopieer de herstelde pagina-BLOB *mybackupstdpageblobrestored* van *mybackupstdaccount* naar *mypremiumaccount* als de nieuwe Premium-schijf *mypremiumdiskrestored*.
5. Maak een moment opname van *mypremiumdiskrestored* met de naam *mypremiumdiskrestored_ss1* voor het maken van toekomstige incrementele back-ups.
6. Wijs de VM van de DS-serie naar de herstelde schijf *mypremiumdiskrestored* en ontkoppel de oude *mypremiumdisk* van de virtuele machine.
7. Begin met het back-upproces dat in de vorige sectie is beschreven voor de herstelde schijf *mypremiumdiskrestored*, met behulp van de *mybackupstdpageblobrestored* als de back-uppagina blob.

![Schijf herstellen vanuit moment opnamen](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Volgende stappen
Gebruik de volgende koppelingen voor meer informatie over het maken van moment opnamen van een BLOB en het plannen van de back-upinfrastructuur van uw VM.

* [Een moment opname van een BLOB maken](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Uw VM-back-upinfrastructuur plannen](../articles/backup/backup-azure-vms-introduction.md)