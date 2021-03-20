---
title: Opslag en gegevens verplaatsing voor Rendering
description: Meer informatie over de verschillende opties voor opslag en gegevens verplaatsing voor het weer geven van de workloads van assets en uitvoer bestanden.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 55ec04df2a107dabfc72298bc8849c13f3a926e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86147308"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opties voor opslag en gegevens verplaatsing voor het weer geven van Asset-en uitvoer bestanden

Er zijn meerdere opties om de scène-en Asset-bestanden beschikbaar te maken voor de rendering-toepassingen op de groeps-Vm's:

* [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md):
  * Scène-en Asset-bestanden worden geüpload naar Blob-opslag vanuit een lokaal bestands systeem. Wanneer de toepassing wordt uitgevoerd door een taak, worden de vereiste bestanden gekopieerd van Blob Storage naar de VM, zodat deze toegankelijk zijn voor de rendering-toepassing. De uitvoer bestanden worden geschreven door de rendering-toepassing naar de VM-schijf en vervolgens gekopieerd naar de Blob-opslag.  De uitvoer bestanden kunnen, indien nodig, worden gedownload van de Blob-opslag naar een lokaal bestands systeem.
  * Azure Blob-opslag is een eenvoudige en voordelige optie voor kleinere projecten.  Als alle asset-bestanden zijn vereist op elke pool-VM, moet het aantal en de grootte van de Asset-bestanden toenemen om ervoor te zorgen dat de bestands overdrachten zo efficiënt mogelijk zijn.  
* Azure Storage als een bestands systeem met behulp van [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md):
  * Voor Linux-Vm's kan een opslag account worden weer gegeven en gebruikt als een bestands systeem wanneer het blobfuse Virtual File System-stuur programma wordt gebruikt.
  * Deze optie heeft als voor deel dat het voordeliger is, omdat er geen Vm's vereist zijn voor het bestands systeem, plus blobfuse-caching op de Vm's voor komt herhaalde down loads van dezelfde bestanden voor meerdere taken en taken.  Het verplaatsen van gegevens is ook eenvoudig omdat de bestanden eenvoudigweg blobs en standaard-Api's en-hulpprogram ma's, zoals azcopy, kunnen worden gebruikt voor het kopiëren van bestanden tussen een on-premises bestands systeem en Azure Storage.
* Bestands systeem of bestands share:
  * Afhankelijk van de vereisten voor het besturings systeem en de prestaties/schaal van de virtuele machine, bevatten de opties [Azure files](../storage/files/storage-files-introduction.md), met behulp van een VM met gekoppelde schijven voor NFS, met behulp van meerdere vm's met gekoppelde schijven voor een gedistribueerd bestands systeem zoals glusterfs, of het gebruik van een aanbieding van derden.
  * [Avere Systems](https://www.averesystems.com/) is nu onderdeel van micro soft en zal in de nabije toekomst oplossingen hebben die ideaal zijn voor grootschalige, hoogwaardige rendering.  Met de avere-oplossing kan een op Azure gebaseerde NFS of SMB-cache worden gemaakt die werkt in combi natie met Blob Storage of met on-premises NAS-apparaten.
  * Met een bestands systeem kunnen bestanden rechtstreeks worden gelezen of geschreven naar het bestands systeem of kunnen worden gekopieerd tussen het bestands systeem en de Vm's van de groep.
  * Met een gedeeld bestands systeem kan een groot aantal assets worden gedeeld tussen projecten en taken die kunnen worden gebruikt, met het weer geven van taken alleen toegang tot wat vereist is.

## <a name="using-azure-blob-storage"></a>Azure Blob-opslag gebruiken

Er moet een Blob Storage-account of een voor algemeen gebruik v2-opslag account worden gebruikt.  Deze twee typen opslag accounts kunnen worden geconfigureerd met aanzienlijk hogere limieten vergeleken met een v1-opslag account voor algemeen gebruik, zoals wordt beschreven in [dit blog bericht](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Als de hogere limieten zijn geconfigureerd, zijn er veel betere prestaties en schaal baarheid mogelijk, met name wanneer er veel pool-Vm's zijn die toegang hebben tot het opslag account.

### <a name="copying-files-between-client-and-blob-storage"></a>Bestanden kopiëren tussen de client-en Blob-opslag

Om bestanden te kopiëren naar en van Azure Storage, kunnen diverse mechanismen worden gebruikt, waaronder de opslag-BLOB API, de [Azure Storage gegevens verplaatsings bibliotheek](https://github.com/Azure/azure-storage-net-data-movement), het azcopy-opdracht regel programma voor [Windows](../storage/common/storage-use-azcopy-v10.md) of [Linux](../storage/common/storage-use-azcopy-v10.md), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)en [Azure batch Explorer](https://azure.github.io/BatchExplorer/).

Als u bijvoorbeeld azcopy gebruikt, kunnen alle assets in een map als volgt worden overgedragen:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Als u alleen gewijzigde bestanden wilt kopiëren, kan de para meter/XO worden gebruikt:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bezig met kopiëren van invoer-Asset-bestanden van Blob-opslag naar batch-pool Vm's

Er zijn een aantal verschillende benaderingen voor het kopiëren van bestanden met de beste benadering die wordt bepaald door de grootte van de taak assets.
De eenvoudigste benadering is het kopiëren van alle asset-bestanden naar de groeps-Vm's voor elke taak:

* Wanneer er bestanden uniek zijn voor een taak, maar zijn vereist voor alle taken van een taak, kan een taak [voorbereidings taak](/rest/api/batchservice/job/add#jobpreparationtask) worden opgegeven om alle bestanden te kopiëren.  De taak voor het voorbereiden van taken wordt eenmaal uitgevoerd wanneer de eerste taak taak wordt uitgevoerd op een virtuele machine, maar niet opnieuw wordt uitgevoerd voor de volgende taak taken.
* Een taak [release taak](/rest/api/batchservice/job/add#jobreleasetask) moet worden opgegeven om de bestanden per taak te verwijderen nadat de taak is voltooid. Hierdoor wordt voor komen dat de VM-schijf gevuld wordt door alle taak Asset bestanden.
* Als er meerdere taken zijn die gebruikmaken van dezelfde activa, met alleen incrementele wijzigingen in de activa voor elke taak, worden alle asset-bestanden nog steeds gekopieerd, zelfs als er slechts een subset is bijgewerkt.  Dit is inefficiënt wanneer er veel grote activa bestanden zijn.

Als activa bestanden opnieuw worden gebruikt tussen taken, met alleen incrementele wijzigingen tussen taken, is een efficiëntere, maar iets andere manier om assets op te slaan in de gedeelde map op de VM en gewijzigde bestanden te synchroniseren.

* De taak voor taak voorbereiding voert de kopie met behulp van azcopy met de para meter/XO naar de gedeelde map van de virtuele machine die is opgegeven door AZ_BATCH_NODE_SHARED_DIR omgevings variabele.  Hierdoor worden alleen gewijzigde bestanden naar elke VM gekopieerd.
* Er moet rekening worden gehouden met de grootte van alle assets om ervoor te zorgen dat ze passen op het tijdelijke station van de groeps-Vm's.

Azure Batch heeft ingebouwde ondersteuning voor het kopiëren van bestanden tussen een opslag account en de virtuele machines van de batch-pool.  Taak [bron bestanden](/rest/api/batchservice/job/add#resourcefile) kopiëren bestanden van de opslag naar de pool-vm's en kunnen worden opgegeven voor de taak voorbereidings taken.  Helaas, wanneer er honderden bestanden zijn, is het mogelijk om een limiet te bereiken en kunnen er taken mislukken.  Wanneer er een groot aantal assets is, wordt het aanbevolen om de azcopy-opdracht regel te gebruiken in de taak voorbereidings taken, die joker tekens kunnen gebruiken en geen limiet heeft.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Uitvoer bestanden naar Blob-opslag kopiëren vanuit de virtuele machines van de batch-pool

[Uitvoer bestanden](/rest/api/batchservice/task/add#outputfile) kunnen worden gebruikt voor het kopiëren van bestanden van een pool-VM naar opslag.  Een of meer bestanden kunnen worden gekopieerd van de virtuele machine naar een opgegeven opslag account zodra de taak is voltooid.  De weer gegeven uitvoer moet worden gekopieerd, maar het kan ook wenselijk zijn om logboek bestanden op te slaan.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Een virtueel bestands systeem voor blobfuse gebruiken voor Linux-VM-Pools

Blobfuse is een virtueel bestandssysteem stuur programma voor Azure Blob Storage, waarmee u bestanden kunt openen die zijn opgeslagen als blobs in een opslag account via het Linux-bestands systeem.

Groeps knooppunten kunnen het bestands systeem koppelen wanneer het starten of koppelen kan plaatsvinden als onderdeel van een taak voorbereidings taak: een taak die alleen wordt uitgevoerd wanneer de eerste taak in een taak wordt uitgevoerd op een knoop punt.  Blobfuse kan worden geconfigureerd om gebruik te maken van een ramdisk en de lokale SSD voor het opslaan van bestanden in de cache, waardoor de prestaties aanzienlijk worden verbeterd als meerdere taken op een knoop punt toegang hebben tot bepaalde bestanden.

[Voorbeeld sjablonen zijn beschikbaar](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) voor het uitvoeren van zelfstandige V-Ray-Renders met behulp van een blobfuse-bestands systeem en kunnen worden gebruikt als basis voor sjablonen voor andere toepassingen.

### <a name="accessing-files"></a>Toegang tot bestanden

Taak taken geven paden op voor invoer bestanden en uitvoer bestanden met behulp van het gekoppelde bestands systeem.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bezig met kopiëren van invoer-Asset-bestanden van Blob-opslag naar batch-pool Vm's

Als bestanden zijn eenvoudigweg blobs in Azure Storage, kunnen standaard-BLOB-Api's,-hulpprogram ma's en-UIs worden gebruikt om bestanden te kopiëren tussen een on-premises bestands systeem en Blob Storage. bijvoorbeeld azcopy, Storage Explorer, Batch Explorer, enzovoort.

## <a name="using-azure-files-with-windows-vms"></a>Azure Files met Windows-Vm's gebruiken

[Azure files](../storage/files/storage-files-introduction.md) biedt volledig beheerde bestands shares in de cloud die toegankelijk zijn via het SMB-protocol.  Azure Files is gebaseerd op Azure Blob-opslag. het is [rendabel](https://azure.microsoft.com/pricing/details/storage/files/) en kan worden geconfigureerd met gegevens replicatie naar een andere regio, zodat deze wereld wijd redundant is.  [Schaal doelen](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) moeten worden gecontroleerd om te bepalen of Azure files moet worden gebruikt op basis van de grootte van de prognose groep en het aantal Asset-bestanden.

Er is [documentatie](../storage/files/storage-how-to-use-files-windows.md) over het koppelen van een Azure-bestands share.

### <a name="mounting-an-azure-files-share"></a>Een Azure Files share koppelen

Als u in batch wilt gebruiken, moet er een koppelings bewerking worden uitgevoerd telkens wanneer een taak wordt uitgevoerd, omdat het niet mogelijk is om de verbinding tussen taken te behouden.  De eenvoudigste manier om dit te doen is door cmdkey te gebruiken om referenties op te slaan met de begin taak in de pool configuratie en vervolgens de share vóór elke taak te koppelen.

Voor beeld van het gebruik van cmdkey in een groeps sjabloon (escaped voor gebruik in het JSON-bestand): Houd er rekening mee dat bij het scheiden van de cmdkey-aanroep van de aanroep net use, de gebruikers context voor de begin taak gelijk moet zijn aan die waarmee de taken worden uitgevoerd:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Voor beeld van opdracht regel taak taak:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Toegang tot bestanden

Taak taken geven paden op voor invoer bestanden en uitvoer bestanden met behulp van het gekoppelde bestands systeem, hetzij met behulp van een toegewezen station of een UNC-pad.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bezig met kopiëren van invoer-Asset-bestanden van Blob-opslag naar batch-pool Vm's

Azure Files worden ondersteund door alle belangrijkste Api's en hulpprogram ma's die Azure Storage ondersteuning hebben. bijvoorbeeld azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer, enzovoort.

[Azure file sync](../storage/files/storage-sync-files-planning.md) is beschikbaar voor het automatisch synchroniseren van bestanden tussen een on-premises bestands systeem en een Azure-bestands share.

## <a name="next-steps"></a>Volgende stappen

Zie de gedetailleerde documentatie voor meer informatie over de opslag opties:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)
* [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Files](../storage/files/storage-files-introduction.md)
