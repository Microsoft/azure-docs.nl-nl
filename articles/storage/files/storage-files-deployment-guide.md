---
title: Azure Files implementeren | Microsoft Docs
description: Meer informatie over het implementeren van Azure Files van begin tot einde. Gegevens overdragen naar Azure Files. Automatisch koppelen op de benodigde computers of servers.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a0415133bf3168c846e1105efe992c2c48c57ff2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492179"
---
# <a name="how-to-deploy-azure-files"></a>Azure Files implementeren
[Azure files](storage-files-introduction.md) biedt volledig beheerde bestands shares in de cloud die toegankelijk zijn via het industrie standaard SMB-protocol. In dit artikel wordt uitgelegd hoe u Azure Files in uw organisatie nagenoeg implementeert.

Voordat u de stappen in dit artikel uitvoert, wordt u ten zeerste geadviseerd om de Lees [planning voor een Azure files-implementatie](storage-files-planning.md) te volgen.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u de volgende stappen al hebt uitgevoerd:

- U hebt een Azure Storage-account gemaakt met de gewenste tolerantie-en versleutelings opties, in de regio die u wenst. Zie [een opslag account maken](../common/storage-account-create.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor stapsgewijze instructies voor het maken van een opslag account.
- Maak een Azure-bestands share met uw gewenste quotum in uw opslag account. Zie [een bestands share maken](storage-how-to-create-file-share.md) voor stapsgewijze instructies voor het maken van een bestands share.

## <a name="transfer-data-into-azure-files"></a>Gegevens overdragen naar Azure Files
U wilt wellicht bestaande bestands shares, zoals opgeslagen op locatie, migreren naar uw nieuwe Azure-bestands share. In deze sectie wordt uitgelegd hoe u gegevens kunt verplaatsen naar een Azure-bestands share via verschillende populaire methoden die worden beschreven in de [plannings handleiding](storage-files-planning.md#migration)

### <a name="azure-file-sync"></a>Azure File Sync
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Dit doet u door uw Windows-servers te transformeren naar een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als waar ook ter wereld u nodig hebt.

Azure File Sync kan worden gebruikt om gegevens te migreren naar een Azure-bestands share, zelfs als het synchronisatie mechanisme niet gewenst is voor gebruik op lange termijn. Meer informatie over het gebruik van Azure File Sync voor het overdragen van gegevens naar een Azure-bestands share vindt u in [planning voor een Azure file sync implementatie](storage-sync-files-planning.md) en [het implementeren van Azure file sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Azure Import/Export
Met de Azure import/export-service kunt u veilig grote hoeveel heden gegevens overdragen naar een Azure-bestands share door harde schijven naar een Azure-Data Center te verzenden. Zie [de Microsoft Azure import/export-service gebruiken om gegevens over te dragen naar Azure Storage](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor een meer gedetailleerd overzicht van de service.

> [!Note]  
> De Azure import/export-service biedt op dit moment geen ondersteuning voor het exporteren van bestanden vanuit een Azure-bestands share.

Met de volgende stappen worden gegevens van een on-premises locatie naar uw Azure-bestands share geïmporteerd.

1. Het vereiste aantal harde schijven aanschaffen dat moet worden verzonden naar Azure. Harde schijven kunnen een wille keurige schijf grootte hebben, maar moet een 2,5-of 3,5-SSD of HDD ondersteunen die de SATA II of SATA III Standard ondersteunt. 

2. Verbinding maken en koppelen van elke schijf op de server/PC die de gegevens overdracht uitvoert. Voor optimale prestaties raden we u aan om de on-premises export taak lokaal uit te voeren op de server die de gegevens bevat. In sommige gevallen, zoals wanneer de bestands server die de gegevens verzendt, een NAS-apparaat is, is dit mogelijk niet mogelijk. In dat geval is het perfect om elke schijf op een PC te koppelen.

3. Zorg ervoor dat elke schijf online, geïnitialiseerd en toegewezen is aan een stationsletter. Als u een station online wilt brengen, initialiseren en toewijzen van een stationsletter, opent u de MMC-module voor schijf beheer (diskmgmt. msc).

    - Als u een schijf online wilt brengen (als deze nog niet online is), klikt u met de rechter muisknop op de schijf in het onderste deel venster van de MMC van schijf beheer en selecteert u ' online '.
    - Als u een schijf wilt initialiseren, klikt u met de rechter muisknop op de schijf in het onderste deel venster (nadat de schijf online is) en selecteert u initialiseren. Zorg ervoor dat u ' GPT ' selecteert wanneer u wordt gevraagd.

        ![Een scherm opname van het menu schijf initialiseren in de MMC van schijf beheer](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Als u een stationsletter aan de schijf wilt toewijzen, klikt u met de rechter muisknop op de ruimte ' niet toegewezen ' van de online en geïnitialiseerde schijf en klikt u op Nieuw eenvoudig volume. Hiermee kunt u stationsletter toewijzen. Houd er rekening mee dat u het volume niet hoeft te Format teren. dit wordt later gedaan.

        ![Een scherm opname van de wizard Nieuw eenvoudig volume in de MMC voor schijf beheer](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Maak het CSV-bestand van de gegevensset. Het CSV-bestand van de gegevensset is een toewijzing tussen het pad naar de on-premises gegevens en de gewenste Azure-bestands share waarnaar de gegevens moeten worden gekopieerd. Zo wijst het volgende gegevensset-CSV-bestand een on-premises bestands share (' F:\shares\scratch ') toe aan een Azure-bestands share (' MyAzureFileShare '):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Er kunnen meerdere shares met een opslag account worden opgegeven. Zie [het CSV-bestand van de gegevensset voorbereiden](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie.

5. Maak het CSV-bestand van het stationset. Het CSV-bestand van het station bevat een lijst met de schijven die beschikbaar zijn voor de on-premises export agent. U kunt bijvoorbeeld de volgende CSV-bestanden van stationset `X:` , `Y:` en `Z:` stations gebruiken voor de on-premises export taak:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Zie [het CSV-bestand van het stationset voorbereiden](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie.

6. Gebruik het [hulp programma WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) om uw gegevens naar een of meer harde schijven te kopiëren.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Wijzig de gegevens op de harde schijven of het logboek bestand niet nadat de schijf voorbereiding is voltooid.

7. [Een import taak maken](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy is een bekend kopieerprogramma dat wordt geleverd bij Windows en Windows Server. Robocopy kan worden gebruikt om gegevens over te brengen naar Azure Files door de bestands share lokaal te koppelen en vervolgens de gekoppelde locatie als bestemming in de Robocopy-opdracht te gebruiken. Het gebruik van Robocopy is heel eenvoudig:

1. [Koppel uw Azure-bestands share](storage-how-to-use-files-windows.md). Voor optimale prestaties raden we u aan de Azure-bestands share lokaal te koppelen op de server die de gegevens bevat. In sommige gevallen, zoals wanneer de bestands server die de gegevens verzendt, een NAS-apparaat is, is dit mogelijk niet mogelijk. In dat geval is het perfect om de Azure-bestands share op een PC te koppelen. In dit voor beeld `net use` wordt gebruikt op de opdracht regel om de bestands share te koppelen:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Gebruik `robocopy` op de opdracht regel om gegevens naar de Azure-bestands share te verplaatsen:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy heeft een groot aantal opties om het Kopieer gedrag naar wens aan te passen. Bekijk de pagina met de hand leiding voor [Robocopy](/windows-server/administration/windows-commands/robocopy) voor meer informatie.

### <a name="azcopy"></a>AzCopy
AzCopy is een opdracht regel programma dat is ontworpen voor het kopiëren van gegevens van en naar Azure Files, evenals Azure Blob-opslag, met behulp van eenvoudige opdrachten met optimale prestaties. Het gebruik van AzCopy is eenvoudig:

1. Down load de [nieuwste versie van AzCopy in Windows](https://aka.ms/downloadazcopy) of [Linux](../common/storage-use-azcopy-v10.md?toc=/azure/storage/files/toc.json#download-azcopy).
2. Gebruik `azcopy` op de opdracht regel om gegevens te verplaatsen naar de Azure-bestands share. De syntaxis van Windows is als volgt: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    In Linux is de syntaxis van de opdracht iets anders:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy heeft een groot aantal opties om het Kopieer gedrag naar wens aan te passen. Zie [aan de slag met AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie.

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatisch koppelen op de benodigde Pc's/servers
Als u een on-premises bestands share wilt vervangen, is het handig om de shares vooraf te koppelen op de computers waarop deze worden gebruikt. Dit kan automatisch worden gedaan in een lijst met computers.

> [!Note]  
> Als u een Azure-bestands share wilt koppelen, moet u de sleutel van het opslag account gebruiken als wacht woord. Daarom raden we u aan om te koppelen in vertrouwde omgevingen. 

### <a name="windows"></a>Windows
Power shell kan worden gebruikt om de opdracht mount op meerdere Pc's uit te voeren. In het volgende voor beeld `$computers` is hand matig ingevuld, maar u kunt de lijst met computers die automatisch moeten worden gekoppeld, genereren. U kunt deze variabele bijvoorbeeld vullen met resultaten van Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Een eenvoudig bash-script in combi natie met SSH kan hetzelfde resultaat opleveren in het volgende voor beeld. De `$computer` variabele is op een vergelijk bare manier links ingevuld door de gebruiker:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Volgende stappen
- [Een Azure File Sync-implementatie plannen](storage-sync-files-planning.md)
- [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
- [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)