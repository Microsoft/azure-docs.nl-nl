---
title: Opslag en gegevens verplaatsen voor rendering
description: Meer informatie over de verschillende opties voor opslag en gegevens movement voor het weergeven van workloads van asset- en uitvoerbestand.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 0a18ee6961cb601b0fa9db7213eb6115afa20096
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765193"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opties voor opslag en gegevens movement voor het weergeven van asset- en uitvoerbestanden

Er zijn meerdere opties voor het beschikbaar maken van de scène- en assetbestanden voor de renderingtoepassingen op de pool-VM's:

* [Azure Blob Storage:](../storage/blobs/storage-blobs-introduction.md)
  * Scène- en assetbestanden worden vanuit een lokaal bestandssysteem geüpload naar blobopslag. Wanneer de toepassing wordt uitgevoerd door een taak, worden de vereiste bestanden gekopieerd van blobopslag naar de VM, zodat ze toegankelijk zijn voor de renderingtoepassing. De uitvoerbestanden worden door de renderingtoepassing naar de VM-schijf geschreven en vervolgens gekopieerd naar de blobopslag.  Indien nodig kunnen de uitvoerbestanden worden gedownload van blobopslag naar een lokaal bestandssysteem.
  * Azure Blob Storage is een eenvoudige en rendabele optie voor kleinere projecten.  Omdat alle assetbestanden op elke pool-VM vereist zijn, moet u er, zodra het aantal en de grootte van assetbestanden toeneemt, goed voor zorgen dat de bestandsoverdrachten zo efficiënt mogelijk zijn.  
* Azure Storage als een bestandssysteem met [blobfuse:](../storage/blobs/storage-how-to-mount-container-linux.md)
  * Voor linux-VM's kan een opslagaccount worden blootgesteld en gebruikt als een bestandssysteem wanneer het stuurprogramma voor het virtuele bestandssysteem blobfuse wordt gebruikt.
  * Deze optie heeft het voordeel dat het zeer rendabel is, omdat er geen VM's vereist zijn voor het bestandssysteem, plus blobfuse-caching op de VM's voorkomt herhaalde downloads van dezelfde bestanden voor meerdere taken en taken.  Gegevensversleuteling is ook eenvoudig omdat de bestanden gewoon blobs zijn en standaard-API's en hulpprogramma's, zoals azcopy, kunnen worden gebruikt om bestanden te kopiëren tussen een on-premises bestandssysteem en Azure Storage.
* Bestandssysteem of bestands share:
  * Afhankelijk van het besturingssysteem van de VM en de vereisten voor prestaties/schaal, zijn de opties onder andere [Azure Files,](../storage/files/storage-files-introduction.md)het gebruik van een VM met gekoppelde schijven voor NFS, het gebruik van meerdere VM's met gekoppelde schijven voor een gedistribueerd bestandssysteem zoals WiltsterFS of het gebruik van een aanbieding van derden.
  * [Avere Systems](https://www.averesystems.com/) maakt nu deel uit van Microsoft en zal in de nabije toekomst oplossingen hebben die ideaal zijn voor grootschalige rendering met hoge prestaties.  Met de Avere-oplossing kan een op Azure gebaseerde NFS- of SMB-cache worden gemaakt die werkt in combinatie met blobopslag of met on-premises NAS-apparaten.
  * Met een bestandssysteem kunnen bestanden worden gelezen of rechtstreeks naar het bestandssysteem worden geschreven of worden gekopieerd tussen het bestandssysteem en de pool-VM's.
  * Met een gedeeld bestandssysteem kan een groot aantal assets die worden gedeeld tussen projecten en taken worden gebruikt, waarbij renderingtaken alleen toegang hebben tot wat nodig is.

## <a name="using-azure-blob-storage"></a>Azure Blob Storage gebruiken

Er moet een Blob Storage-account of een v2-opslagaccount voor algemeen gebruik worden gebruikt.  Deze twee typen opslagaccounts kunnen worden geconfigureerd met aanzienlijk hogere limieten in vergelijking met een algemeen v1-opslagaccount, zoals beschreven in [dit blogbericht.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)  Wanneer deze zijn geconfigureerd, zorgen de hogere limieten voor veel betere prestaties en schaalbaarheid, met name wanneer er veel pool-VM's zijn die toegang hebben tot het opslagaccount.

### <a name="copying-files-between-client-and-blob-storage"></a>Bestanden kopiëren tussen client- en blobopslag

Als u bestanden naar en van Azure Storage wilt kopiëren, kunnen verschillende mechanismen worden gebruikt, waaronder de storage blob-API, de [Azure Storage Data Movement Library,](https://github.com/Azure/azure-storage-net-data-movement)het opdrachtregelprogramma azcopy voor [Windows](../storage/common/storage-use-azcopy-v10.md) of [Linux,](../storage/common/storage-use-azcopy-v10.md) [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)en [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Met azcopy kunnen bijvoorbeeld alle assets in een map als volgt worden overgedragen:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Als u alleen gewijzigde bestanden wilt kopiëren, kan de /XO parameter worden gebruikt:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Invoeractivumbestanden kopiëren van blob-opslag naar Batch-pool-VM's

Er zijn een aantal verschillende methoden voor het kopiëren van bestanden met de beste benadering die wordt bepaald door de grootte van de taakactiva.
De eenvoudigste aanpak is het kopiëren van alle assetbestanden naar de pool-VM's voor elke taak:

* Wanneer er bestanden zijn die uniek zijn voor een job, maar [](/rest/api/batchservice/job/add#jobpreparationtask) vereist zijn voor alle taken van een job, kan een jobvoorbereidingstaak worden opgegeven om alle bestanden te kopiëren.  De jobvoorbereidingstaak wordt eenmaal uitgevoerd wanneer de eerste taak wordt uitgevoerd op een VM, maar niet opnieuw wordt uitgevoerd voor volgende jobtaken.
* Er [moet een job-releasetaak](/rest/api/batchservice/job/add#jobreleasetask) worden opgegeven om de bestanden per taak te verwijderen zodra de taak is voltooid; Zo voorkomt u dat de VM-schijf wordt gevuld door alle assetbestanden van de taak.
* Wanneer er meerdere taken zijn die gebruikmaken van dezelfde assets, met alleen incrementele wijzigingen in de assets voor elke taak, worden alle assetbestanden nog steeds gekopieerd, zelfs als alleen een subset is bijgewerkt.  Dit zou inefficiënt zijn wanneer er veel grote assetbestanden zijn.

Wanneer assetbestanden opnieuw worden gebruikt tussen taken, met alleen incrementele wijzigingen tussen taken, is een efficiëntere maar iets meer betrokken benadering het opslaan van assets in de gedeelde map op de VM en het synchroniseren van gewijzigde bestanden.

* De jobvoorbereidingstaak voert de kopie uit met azcopy met de parameter /XO naar de gedeelde map van de VM die is opgegeven AZ_BATCH_NODE_SHARED_DIR omgevingsvariabele.  Hiermee worden alleen gewijzigde bestanden naar elke VM gekopieerd.
* U moet nadenken over de grootte van alle assets om ervoor te zorgen dat ze op de tijdelijke schijf van de pool-VM's passen.

Azure Batch biedt ingebouwde ondersteuning voor het kopiëren van bestanden tussen een opslagaccount en virtuele VM's in batchpools.  [Taakresourcebestanden](/rest/api/batchservice/job/add#resourcefile) kopiëren bestanden van opslag naar pool-VM's en kunnen worden opgegeven voor de taakvoorbereidingstaak.  Wanneer er honderden bestanden zijn, is het helaas mogelijk om een limiet te bereikt en kunnen taken mislukken.  Wanneer er grote aantallen assets zijn, is het raadzaam om de opdrachtregel azcopy te gebruiken in de taakvoorbereidingstaak, die jokertekens kan gebruiken en geen limiet heeft.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Uitvoerbestanden kopiëren naar blob-opslag vanuit Batch-pool-VM's

[Uitvoerbestanden kunnen](/rest/api/batchservice/task/add#outputfile) worden gebruikt om bestanden te kopiëren van een pool-VM naar de opslag.  Een of meer bestanden kunnen worden gekopieerd van de VM naar een opgegeven opslagaccount zodra de taak is voltooid.  De weergegeven uitvoer moet worden gekopieerd, maar het kan ook wenselijk zijn om logboekbestanden op te slaan.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Een virtueel bestandssysteem blobfuse gebruiken voor Linux-VM-pools

Blobfuse is een stuurprogramma voor het virtuele bestandssysteem voor Azure Blob Storage, waarmee u toegang krijgt tot bestanden die zijn opgeslagen als blobs in een opslagaccount via het Linux-bestandssysteem.

Poolknooppunten kunnen het bestandssysteem aan het begin van het bestandssysteem monteren of de mount kan plaatsvinden als onderdeel van een jobvoorbereidingstaak: een taak die alleen wordt uitgevoerd wanneer de eerste taak in een taak op een knooppunt wordt uitgevoerd.  Blobfuse kan worden geconfigureerd om gebruik te maken van zowel een ramdisk als de lokale SSD van de VM's voor het opslaan in de caching van bestanden, waardoor de prestaties aanzienlijk toenemen als meerdere taken op een knooppunt toegang hebben tot sommige van dezelfde bestanden.

[Voorbeeldsjablonen zijn beschikbaar voor](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) het uitvoeren van zelfstandige V-Ray-renders met behulp van een blobfuse-bestandssysteem en kunnen worden gebruikt als basis voor sjablonen voor andere toepassingen.

### <a name="accessing-files"></a>Toegang tot bestanden

Taaktaken geven paden op voor invoerbestanden en uitvoerbestanden met behulp van het aan elkaar geplaatste bestandssysteem.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Invoeractivumbestanden kopiëren van blob-opslag naar Batch-pool-VM's

Omdat bestanden gewoon blobs in Azure Storage zijn, kunnen standaard blob-API's, hulpprogramma's en API's worden gebruikt om bestanden te kopiëren tussen een on-premises bestandssysteem en blobopslag; bijvoorbeeld azcopy, Storage Explorer, Batch Explorer, enzovoort.

## <a name="using-azure-files-with-windows-vms"></a>Een Azure Files windows-VM's gebruiken

[Azure Files](../storage/files/storage-files-introduction.md) biedt volledig beheerde bestands shares in de cloud die toegankelijk zijn via het SMB-protocol.  Azure Files is gebaseerd op Azure Blob Storage; Het is [kostenefficiënt](https://azure.microsoft.com/pricing/details/storage/files/) en kan worden geconfigureerd met gegevensreplicatie naar een andere regio, dus globaal redundant.  [Schaaldoelen](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) moeten worden gecontroleerd om te bepalen of Azure Files moeten worden gebruikt op de prognosegroepgrootte en het aantal assetbestanden.

Er is documentatie [over](../storage/files/storage-how-to-use-files-windows.md) het toevoegen van een Azure-bestands share.

### <a name="mounting-an-azure-files-share"></a>Een Azure Files maken

Als u in Batch wilt gebruiken, moet er telkens een koppelingsbewerking worden uitgevoerd wanneer een taak wordt uitgevoerd, omdat het niet mogelijk is om de verbinding tussen taken te houden.  De eenvoudigste manier om dit te doen, is door cmdkey te gebruiken voor het persistent maken van referenties met behulp van de begintaak in de poolconfiguratie en vervolgens de share vóór elke taak te mounten.

Voorbeeld van cmdkey in een poolsjabloon (escaped voor gebruik in JSON-bestand). Houd er rekening mee dat wanneer u de cmdkey-aanroep scheidt van de net use-aanroep, de gebruikerscontext voor de begintaak hetzelfde moet zijn als die voor het uitvoeren van de taken:

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

Voorbeeldtaakopdrachtregel:
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

Taaktaken geven paden op voor invoerbestanden en uitvoerbestanden met behulp van het aan elkaar geplaatste bestandssysteem, met behulp van een station of een UNC-pad.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Invoeractivumbestanden kopiëren van blob-opslag naar Batch-pool-VM's

Azure Files worden ondersteund door alle belangrijkste API's en hulpprogramma's die ondersteuning Azure Storage bieden; bijvoorbeeld azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer, enzovoort.

[Azure File Sync](../storage/file-sync/file-sync-planning.md) is beschikbaar voor het automatisch synchroniseren van bestanden tussen een on-premises bestandssysteem en een Azure-bestands share.

## <a name="next-steps"></a>Volgende stappen

Zie de uitgebreide documentatie voor meer informatie over de opslagopties:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)
* [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Files](../storage/files/storage-files-introduction.md)
