---
title: Azure Import/Export gebruiken om gegevens over te dragen naar Azure Files | Microsoft Docs
description: Meer informatie over het maken van importtaken in de Azure Portal om gegevens over te dragen naar Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 9c13ffc597349cdd2b304889d142ca7c2f89c713
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861531"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Azure Import/Export-service gebruiken om gegevens te importeren naar Azure Files

Dit artikel bevat stapsgewijs instructies voor het gebruik van de Azure Import/Export-service om veilig grote hoeveelheden gegevens te importeren in Azure Files. Voor het importeren van gegevens moet u ondersteunde schijfstations met uw gegevens naar een Azure-datacenter verzenden.

De Import/Export-service ondersteunt alleen het importeren van Azure Files in Azure Storage. Het exporteren Azure Files wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voordat u een import job maakt om gegevens over te dragen naar Azure Files, moet u de volgende lijst met vereisten zorgvuldig controleren en voltooien. U moet:

- Een actief Azure-abonnement hebben voor gebruik met de Import/Export-service.
- Ten minste één account Azure Storage account. Zie de lijst met [ondersteunde opslagaccounts en opslagtypen voor de Import/Export-service.](storage-import-export-requirements.md) Zie How to Create a Storage Account (Een opslagaccount maken) voor meer informatie over [het maken van een nieuw opslagaccount.](../storage/common/storage-account-create.md)
- Voldoende schijven van [ondersteunde typen hebben.](storage-import-export-requirements.md#supported-disks)
- Een Windows-systeem met een [ondersteunde versie van het besturingssysteem hebben.](storage-import-export-requirements.md#supported-operating-systems)
- [Download waImportExport versie 2](https://aka.ms/waiev2) op het Windows-systeem. Unzip to the default folder `waimportexport` . Bijvoorbeeld `C:\WaImportExport`.
- Een FedEx/DHL-account hebben. Als u een andere vervoerder dan FedEx/DHL wilt gebruiken, neem dan contact op met Azure Data Box Operations-team op `adbops@microsoft.com` .
    - Het account moet geldig zijn, moet een saldo hebben en moet retourmogelijkheden hebben.
    - Genereer een traceringsnummer voor de export job.
    - Elke taak moet een afzonderlijk traceringsnummer hebben. Meerdere taken met hetzelfde traceringsnummer worden niet ondersteund.
    - Als u geen provideraccount hebt, gaat u naar:
        - [Een FedEx-account maken,](https://www.fedex.com/en-us/create-account.html)of
        - [Maak een DHL-account.](http://www.dhl-usa.com/en/express/shipping/open_account.html)


## <a name="step-1-prepare-the-drives"></a>Stap 1: de stations voorbereiden

Met deze stap wordt een logboekbestand gegenereerd. In het logboekbestand worden basisgegevens opgeslagen, zoals het serienummer van het station, de versleutelingssleutel en de gegevens van het opslagaccount.

Volg de volgende stappen om de stations voor te bereiden.

1. Verbind onze schijfstations met het Windows-systeem via SATA-connectors.
2. Maak één NTFS-volume op elk station. Wijs een stationletter toe aan het volume. Gebruik geen mountpoints.
3. Wijzig het *dataset.csv* bestand in de hoofdmap waar het hulpprogramma zich is. Afhankelijk van of u een bestand of map of beide wilt importeren, voegt u vermeldingen toe in hetdataset.csv *op* de volgende voorbeelden.

   - **Een bestand importeren:** in het volgende voorbeeld zijn de gegevens te kopiëren op de F: station. Uw *bestandsshareMyFile1.txt* gekopieerd naar de hoofdmap van *myAzureFileshare1.* Als *myAzureFileshare1* niet bestaat, wordt deze gemaakt in het Azure Storage account. Mapstructuur wordt onderhouden.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Een map importeren:** alle bestanden en mappen onder *MyFolder2* worden recursief gekopieerd naar bestandsshare. Mapstructuur wordt onderhouden.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Er kunnen meerdere vermeldingen worden gemaakt in hetzelfde bestand dat overeenkomt met mappen of bestanden die worden geïmporteerd.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Meer informatie over het [voorbereiden van het CSV-bestand van de gegevensset.](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)


4. Wijzig het *driveset.csv* bestand in de hoofdmap waar het hulpprogramma zich is. Voeg vermeldingen toe in het *driveset.csv* bestand dat vergelijkbaar is met de volgende voorbeelden. Het driveset-bestand bevat de lijst met schijven en de bijbehorende station letters zodat het hulpprogramma correct de lijst met schijven kan kiezen die moeten worden voorbereid.

    In dit voorbeeld wordt ervan uitgenomen dat er twee schijven zijn gekoppeld en basic NTFS-volumes G:\ en H:\ worden gemaakt. H:\is niet versleuteld terwijl G: al is versleuteld. Het hulpprogramma formatteert en versleutelt de schijf die als host voor H:\ alleen (en niet G: \) .

   - **Voor een schijf die niet is versleuteld: geef Versleutelen** *op om* BitLocker-versleuteling op de schijf in te stellen.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Voor een schijf die al is versleuteld:** geef *AlVersleuteld* op en geef de BitLocker-sleutel op.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Er kunnen meerdere vermeldingen worden gemaakt in hetzelfde bestand dat overeenkomt met meerdere stations. Meer informatie over het [voorbereiden van het CSV-bestand van de driveset.](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)

5. Gebruik de optie om gegevens naar het schijfstation te `PrepImport` kopiëren en voor te bereiden. Voer de volgende opdracht uit voor de eerste kopieersessie voor het kopiëren van mappen en/of bestanden met een nieuwe kopieersessie:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Hieronder wordt een importvoorbeeld weergegeven.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Een logboekbestand met de naam die u hebt `/j:` opgegeven met parameter, wordt gemaakt voor elke run van de opdrachtregel. Elk station dat u voorbereidt, heeft een logboekbestand dat moet worden geüpload wanneer u de import job maakt. Stations zonder logboekbestanden worden niet verwerkt.

    > [!IMPORTANT]
    > - Wijzig de gegevens op de schijfstations of het logboekbestand niet na het voltooien van de schijfvoorbereiding.

Ga voor aanvullende voorbeelden naar [Voorbeelden voor logboekbestanden.](#samples-for-journal-files)

## <a name="step-2-create-an-import-job"></a>Stap 2: een importeer job maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Volg de volgende stappen om een importeer job te maken in Azure Portal.
1. Meld u aan bij https://portal.azure.com/ .
2. Zoek naar **import-/exporttaken.**

    ![Zoeken in import-/exporttaken](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Selecteer **+ Nieuw**.

    ![Selecteer Nieuw om een nieuwe te maken ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. In **Basisbeginselen**:

   1. Selecteer een abonnement.
   1. Selecteer een resourcegroep of selecteer **Nieuwe maken** en maak een nieuwe.
   1. Voer een beschrijvende naam in voor de import job. Gebruik de naam om de voortgang van uw taken bij te houden.
       * De naam mag alleen kleine letters, cijfers en afbreekstreelopen bevatten.
       * De naam moet beginnen met een letter en mag geen spaties bevatten.
   1. Selecteer **Importeren in Azure.**

    ![Importeerstap maken - Stap 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

   Selecteer **Volgende: Taakdetails >** om door te gaan.

5. In **Taakdetails:**

   1. Upload de logboekbestanden die u hebt gemaakt tijdens de vorige [stap 1: de stations voorbereiden.](#step-1-prepare-the-drives)
   1. Selecteer de Azure-doelregio voor de order.
   1. Selecteer het opslagaccount voor het importeren.

      De locatie van de vervolgkeuze wordt automatisch ingevuld op basis van de regio van het geselecteerde opslagaccount.

   1. Als u geen uitgebreid logboek wilt opslaan, wist u het logboek Uitgebreide opslaan in de **blobcontainer 'waimportexport'.**


   ![Importeerstap maken - Stap 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   Selecteer **Volgende: Verzendadres >** door te gaan.

4. In **Verzending:**

    1. Selecteer de vervoerder in de vervolgkeuzelijst. Als u een andere vervoerder dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolgkeuzekeuze. Neem contact Azure Data Box operations-team `adbops@microsoft.com`  op met de informatie over de vervoerder die u wilt gebruiken.
    1. Voer een geldig provideraccountnummer in dat u met die vervoerder hebt gemaakt. Microsoft gebruikt dit account om de stations naar u terug te verzenden zodra uw import job is voltooid.
    1. Geef een volledige en geldige contactgegevens, telefoonnummer, e-mailadres, adres, plaats, postcode, staat/provincie en land/regio op.

        > [!TIP]
        > In plaats van een e-mailadres voor één gebruiker op te geven, geeft u een groeps-e-mailadres op. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder weggaat.

    ![Importeerstap maken - Stap 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Selecteer **Beoordelen en maken om door** te gaan.

5. In de ordersamenvatting:

   1. Lees de **voorwaarden** en selecteer vervolgens 'Ik bevestig dat alle verstrekte informatie juist is en ga akkoord met de voorwaarden'. Validatie wordt vervolgens uitgevoerd.
   1. Bekijk de taakgegevens in de samenvatting. Noteer de taaknaam en het verzendadres van het Azure-datacenter om schijven terug te sturen naar Azure. Deze informatie wordt later op het verzendlabel gebruikt.
   1. Selecteer **Maken**.

        ![Importeer job maken - Stap 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende stappen om een import job te maken in de Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Een taak maken

1. Gebruik de [opdracht az extension add](/cli/azure/extension#az_extension_add) om de extensie az [import-export toe te](/cli/azure/import-export) voegen:

    ```azurecli
    az extension add --name import-export
    ```

1. U kunt een bestaande resourcegroep gebruiken of er een maken. Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken:

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. U kunt een bestaand opslagaccount gebruiken of een account maken. Voer de opdracht az storage account create uit om [een opslagaccount te](/cli/azure/storage/account#az_storage_account_create) maken:

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. Gebruik de opdracht [az import-export location list](/cli/azure/import-export/location#az_import_export_location_list) om een lijst op te halen met de locaties waar u schijven kunt verzenden:

    ```azurecli
    az import-export location list
    ```

1. Gebruik de [opdracht az import-export location show](/cli/azure/import-export/location#az_import_export_location_show) om locaties voor uw regio op te halen:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Voer de volgende [az import-export create-opdracht](/cli/azure/import-export#az_import_export_create) uit om een importeeropdracht te maken:

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
   > In plaats van een e-mailadres op te geven voor één gebruiker, geeft u een groeps-e-mailadres op. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder weggaat.


1. Gebruik de [opdracht az import-export list](/cli/azure/import-export#az_import_export_list) om alle taken voor de resourcegroep myierg weer te geven:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Voer de opdracht [az import-export update](/cli/azure/import-export#az_import_export_update) uit om uw taak bij te werken of uw taak te annuleren:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Gebruik de volgende stappen om een importeer job te maken in Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Hoewel de **PowerShell-module Az.ImportExport** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet . Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Een taak maken

1. U kunt een bestaande resourcegroep gebruiken of er een maken. Voer de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) uit om een resourcegroep te maken:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. U kunt een bestaand opslagaccount gebruiken of een account maken. Voer de cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) uit om een opslagaccount te maken:

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Gebruik de cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) om een lijst op te halen met de locaties waar u schijven kunt verzenden:

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Gebruik de `Get-AzImportExportLocation` cmdlet met de `Name` parameter om locaties voor uw regio op te halen:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Voer het volgende [Voorbeeld van New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) uit om een import job te maken:

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
   > In plaats van een e-mailadres voor één gebruiker op te geven, geeft u een groeps-e-mailadres op. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder weggaat.

1. Gebruik de cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) om alle taken voor de resourcegroep myierg te bekijken:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Voer de cmdlet [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) uit om uw taak bij te werken of uw taak te annuleren:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Stap 3: de schijven naar het Azure-datacenter verzenden

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Stap 4: de taak bijwerken met traceringsgegevens

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Stap 5: het uploaden van gegevens naar Azure controleren

Houd de taak bij tot deze is voltooid. Nadat de taak is voltooid, controleert u of uw gegevens zijn geüpload naar Azure. Verwijder de on-premises gegevens pas nadat u hebt gecontroleerd of het uploaden is geslaagd.

## <a name="samples-for-journal-files"></a>Voorbeelden voor logboekbestanden

Als **u meer stations wilt toevoegen,** maakt u een nieuw stationssetbestand en voer u de opdracht uit zoals hieronder wordt weergegeven.

Voor volgende kopieersessies naar andere schijfstations dan die zijn opgegeven in het *CSV-bestand InitialDriveset* geeft u een nieuw *CSV-bestand* van de stationsset op en geeft u dit op als een waarde voor de parameter `AdditionalDriveSet` . Gebruik dezelfde **logboekbestandsnaam en** geef een nieuwe **sessie-id op.** De indeling van het CSV-bestand AdditionalDriveset is hetzelfde als de InitialDriveSet-indeling.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Hieronder wordt een importvoorbeeld weergegeven.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Als u extra gegevens wilt toevoegen aan dezelfde driveset, gebruikt u de opdracht PrepImport voor volgende kopieersessies om extra bestanden/mappen te kopiëren.

Voor volgende kopieersessies naar dezelfde harde schijfstations die zijn opgegeven  in *InitialDriveset.csv* bestand, geeft u dezelfde logboekbestandsnaam op en geeft u een nieuwe **sessie-id op;** U hoeft de sleutel van het opslagaccount niet op te geven.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Hieronder wordt een importvoorbeeld weergegeven.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Volgende stappen

* [De status van de taak en het station weergeven](storage-import-export-view-drive-status.md)
* [Vereisten voor importeren/exporteren bekijken](storage-import-export-requirements.md)