---
title: Azure import/export gebruiken om gegevens over te dragen naar Azure Files | Microsoft Docs
description: Meer informatie over het maken van import taken in de Azure Portal om gegevens over te dragen naar Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/29/2020
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9d1d0f4b615bbf4cc7faf82d70a4de0b0157ed82
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326349"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Azure Import/Export-service gebruiken om gegevens te importeren naar Azure Files

In dit artikel vindt u stapsgewijze instructies voor het gebruik van de Azure import/export-service om grote hoeveel heden gegevens veilig te importeren in Azure Files. Als u gegevens wilt importeren, moet u ondersteunde schijf stations met uw gegevens verzenden naar een Azure-Data Center.

De import/export-service ondersteunt alleen het importeren van Azure Files naar Azure Storage. Het exporteren van Azure Files wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voordat u een import taak maakt om gegevens over te dragen naar Azure Files, moet u de volgende lijst met vereisten zorgvuldig door nemen en volt ooien. U moet het volgende doen:

- Een actief Azure-abonnement hebben om te gebruiken met de import/export-service.
- Ten minste één Azure Storage account hebben. Zie de lijst met [ondersteunde opslag accounts en opslag typen voor de import/export-service](storage-import-export-requirements.md). Zie [een opslag account maken](storage-account-create.md)voor meer informatie over het maken van een nieuw opslag account.
- Voldoende aantal schijven van [ondersteunde typen](storage-import-export-requirements.md#supported-disks)hebben.
- Een Windows-systeem met een [ondersteunde besturingssysteem versie](storage-import-export-requirements.md#supported-operating-systems)hebben.
- [Down load de WAImportExport-versie 2](https://aka.ms/waiev2) op het Windows-systeem. Unzip naar de standaardmap `waimportexport` . Bijvoorbeeld `C:\WaImportExport`.
- Een FedEx/DHL-account hebben. Als u een andere transporteur dan FedEx/DHL wilt gebruiken, neemt u contact op met Azure Data Box Operations-team op `adbops@microsoft.com` .
    - Het account moet geldig zijn, moet een saldo hebben en moet de retour verzendings mogelijkheden hebben.
    - Genereer een tracking nummer voor de export taak.
    - Elke taak moet een afzonderlijk traceringsnummer hebben. Meerdere taken met hetzelfde traceringsnummer worden niet ondersteund.
    - Als u geen draaggolf account hebt, gaat u naar:
        - [Een FedEx-account maken](https://www.fedex.com/en-us/create-account.html)of
        - [Maak een DHL-account](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Stap 1: de stations voorbereiden

Met deze stap wordt een logboek bestand gegenereerd. In het logboek bestand worden basis gegevens opgeslagen, zoals serie nummer van het station, de versleutelings sleutel en Details van het opslag account.

Voer de volgende stappen uit om de stations voor te bereiden.

1. Verbind onze schijf stations met het Windows-systeem via SATA-connectors.
2. Maak één NTFS-volume op elk station. Wijs een stationsletter aan het volume toe. Gebruik mountpoints niet.
3. Wijzig het *dataset.csv* -bestand in de hoofdmap waar het hulp programma zich bevindt. Afhankelijk van of u een bestand of map of beide wilt importeren, voegt u in het *dataset.csv* -bestand vermeldingen toe die vergelijkbaar zijn met de volgende voor beelden.

   - **Een bestand importeren**: in het volgende voor beeld bevindt de te kopiëren gegevens zich in het station F:. Uw bestands *MyFile1.txt*  wordt gekopieerd naar de hoofdmap van de *MyAzureFileshare1*. Als de *MyAzureFileshare1* niet bestaat, wordt deze in het Azure Storage-account gemaakt. De mapstructuur wordt behouden.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Een map importeren**: alle bestanden en mappen onder *MyFolder2* worden recursief gekopieerd naar de bestands share. De mapstructuur wordt behouden.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Er kunnen meerdere vermeldingen worden gemaakt in hetzelfde bestand dat overeenkomt met de mappen of bestanden die worden geïmporteerd.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Meer informatie over [het voorbereiden van het CSV-bestand van de gegevensset](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).


4. Wijzig het *driveset.csv* -bestand in de hoofdmap waar het hulp programma zich bevindt. Voeg in het *driveset.csv* -bestand vermeldingen toe die vergelijkbaar zijn met de volgende voor beelden. Het stationset-bestand bevat de lijst met schijven en bijbehorende stationsletters, zodat het hulp programma de lijst met schijven die moeten worden voor bereid, correct kan selecteren.

    In dit voor beeld wordt ervan uitgegaan dat er twee schijven zijn gekoppeld en basis-NTFS-volumes G:\ en H:\ worden gemaakt. H:\is is niet versleuteld terwijl G: al is versleuteld. Het hulp programma formatteert en versleutelt de schijf die als host fungeert voor H:\ alleen (en niet G: \) .

   - **Voor een niet-versleutelde schijf**: Geef *versleutelen* op om BitLocker-versleuteling op de schijf in te scha kelen.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Voor een schijf die al is versleuteld**: Geef *AlreadyEncrypted* op en voer de BitLocker-sleutel in.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Er kunnen meerdere vermeldingen worden gemaakt in hetzelfde bestand dat overeenkomt met meerdere stations. Meer informatie over [het voorbereiden van het CSV-bestand van het station](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).

5. Gebruik de `PrepImport` optie om gegevens naar het schijf station te kopiëren en voor te bereiden. Voer de volgende opdracht uit voor de eerste Kopieer sessie om directory's en/of bestanden te kopiëren met een nieuwe Kopieer sessie:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Hieronder ziet u een voor beeld van een import.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Een logboek bestand met de naam die u hebt ingevoerd met `/j:` de para meter, wordt gemaakt voor elke uitvoering van de opdracht regel. Elke schijf die u voorbereidt, heeft een logboek bestand dat moet worden geüpload wanneer u de import taak maakt. Stations zonder logboek bestanden worden niet verwerkt.

    > [!IMPORTANT]
    > - Wijzig de gegevens op de schijf stations of het logboek bestand niet nadat de schijf voorbereiding is voltooid.

Voor extra voor beelden gaat u naar [voor beelden van logboek bestanden](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Stap 2: een import taak maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om een import taak te maken in de Azure Portal.
1. Meld u aan bij https://portal.azure.com/ .
2. Ga naar **alle services > opslag > import/export-taken**.

    ![Ga naar importeren/exporteren](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klik op **import/export-taak maken**.

    ![Klik op import/export-taak](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. In de **basis beginselen**:

    - Selecteer **importeren in azure**.
    - Voer een beschrijvende naam in voor de import taak. Gebruik deze naam om uw taken bij te houden terwijl ze worden uitgevoerd en zodra ze zijn voltooid.
        -  Deze naam mag alleen kleine letters, cijfers, afbreek streepjes en onderstrepings tekens bevatten.
        -  De naam moet beginnen met een letter en mag geen spaties bevatten.
    - Selecteer een abonnement.
    - Selecteer een resourcegroep.

        ![Import taak maken-stap 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. In **taak Details**:

    - Upload de logboek bestanden die u hebt gemaakt tijdens de vorige [stap 1: bereid de stations](#step-1-prepare-the-drives)voor.
    - Selecteer het opslag account waarin de gegevens worden geïmporteerd.
    - De locatie van de dropoff wordt automatisch ingevuld op basis van de regio van het geselecteerde opslag account.

       ![Import taak maken-stap 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. In **retour verzendings gegevens**:

    - Selecteer de transporteur in de vervolg keuzelijst. Als u een andere transporteur dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolg keuzelijst. Neem contact op met Azure Data Box Operations-team `adbops@microsoft.com`  met de informatie over de provider die u wilt gebruiken.
    - Voer een geldig account nummer van een transporteur in dat u hebt gemaakt met die transporteur. Micro soft gebruikt dit account om de schijven terug naar u te verzenden zodra uw import taak is voltooid.
    - Geef een volledige en geldige naam op voor de contact persoon, telefoon, e-mail, adres, plaats, post code, provincie en land/regio.

        > [!TIP]
        > In plaats van een e-mail adres voor één gebruiker op te geven, moet u een groeps-e-mail opgeven. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder deze verlaat.

       ![Import taak maken-stap 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. In de **samen vatting**:

    - Geef het Azure Data Center-verzend adres voor de verzend schijven terug naar Azure. Zorg ervoor dat de naam en het volledige adres van de taak worden vermeld op het verzend label.
    - Klik op **OK** om het maken van de import taak te volt ooien.

        ![Import taak maken-stap 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende stappen om een import taak te maken in de Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Een taak maken

1. Gebruik de opdracht [AZ extension add](/cli/azure/extension#az_extension_add) om de extensie [AZ import-export](/cli/azure/ext/import-export/import-export) toe te voegen:

    ```azurecli
    az extension add --name import-export
    ```

1. U kunt een bestaande resource groep gebruiken of er een maken. Voer de opdracht [AZ Group Create](/cli/azure/group#az_group_create) uit om een resource groep te maken:

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. U kunt een bestaand opslag account gebruiken of er een maken. Als u een opslag account wilt maken, voert u de opdracht [AZ Storage account create](/cli/azure/storage/account#az_storage_account_create) uit:

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. Als u een lijst wilt weer geven met de locaties waarnaar u schijven kunt verzenden, gebruikt u de opdracht [AZ import-export location](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. Gebruik de opdracht [AZ import-export locatie weer geven](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_show) om locaties op te halen voor uw regio:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Voer de volgende opdracht [AZ import-export Create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) uit om een import taak te maken:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > In plaats van een e-mail adres voor één gebruiker op te geven, moet u een groeps-e-mail opgeven. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder deze verlaat.


1. Gebruik de opdracht [AZ import-export List](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) om alle taken voor de resource groep myierg te bekijken:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Voer de opdracht [AZ import-export update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) uit om uw taak bij te werken of uw taak te annuleren:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Gebruik de volgende stappen om een import taak te maken in Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Hoewel de Power shell-module **AZ. ImportExport** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet. Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Een taak maken

1. U kunt een bestaande resource groep gebruiken of er een maken. Voer de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) uit om een resource groep te maken:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. U kunt een bestaand opslag account gebruiken of er een maken. Als u een opslag account wilt maken, voert u de cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) uit:

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Gebruik de cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) om een lijst op te halen van de locaties waarnaar u schijven kunt verzenden:

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Gebruik de `Get-AzImportExportLocation` cmdlet met de `Name` para meter om locaties op te halen voor uw regio:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Voer het volgende [nieuwe AzImportExport-](/powershell/module/az.importexport/new-azimportexport) voor beeld uit om een import taak te maken:

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > In plaats van een e-mail adres voor één gebruiker op te geven, moet u een groeps-e-mail opgeven. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder deze verlaat.

1. Gebruik de cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) om alle taken voor de resource groep myierg te bekijken:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Voer de cmdlet [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) uit om uw taak bij te werken of uw taak te annuleren:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Stap 3: de stations naar het Azure-Data Center verzenden

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Stap 4: de taak bijwerken met tracerings informatie

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Stap 5: gegevens uploaden naar Azure controleren

De taak bijhouden tot voltooiing. Nadat de taak is voltooid, controleert u of uw gegevens zijn geüpload naar Azure. Verwijder de on-premises gegevens pas nadat u hebt gecontroleerd of de upload is geslaagd.

## <a name="samples-for-journal-files"></a>Voor beelden voor logboek bestanden

Als u **meer stations wilt toevoegen**, maakt u een nieuw stationset-bestand en voert u de volgende opdracht uit.

Voor volgende Kopieer sessies naar de verschillende schijf stations dan opgegeven in *InitialDriveset. CSV* -bestand, geeft u een nieuw *CSV* -bestand van stationset op en geeft u het op als waarde voor de para meter `AdditionalDriveSet` . Gebruik dezelfde naam voor het **logboek bestand** en geef een **nieuwe sessie-id** op. De indeling van het CSV-bestand van AdditionalDriveset is gelijk aan de InitialDriveSet-indeling.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Hieronder ziet u een voor beeld van een import.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Als u extra gegevens wilt toevoegen aan dezelfde schijfset, gebruikt u de opdracht PrepImport voor de volgende Kopieer sessies om extra bestanden/mappen te kopiëren.

Voor volgende Kopieer sessies naar dezelfde harde schijven die zijn opgegeven in *InitialDriveset.csv* bestand, geeft u dezelfde naam op voor het **logboek bestand** en geeft u een **nieuwe sessie-id** op. het is niet nodig om de sleutel van het opslag account op te geven.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Hieronder ziet u een voor beeld van een import.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Volgende stappen

* [De taak en de status van het station weer geven](storage-import-export-view-drive-status.md)
* [Vereisten voor importeren/exporteren controleren](storage-import-export-requirements.md)