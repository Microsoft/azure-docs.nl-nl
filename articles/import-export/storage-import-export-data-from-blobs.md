---
title: Azure import/export gebruiken om gegevens te exporteren uit Azure-blobs | Microsoft Docs
description: Meer informatie over het maken van export taken in Azure Portal voor het overdragen van gegevens uit Azure-blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 8ccc7b641e2bfcb4ea8733b9d4f793229c430bc0
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652866"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>De Azure Import/Export-service gebruiken voor het exporteren van gegevens uit Azure Blob-opslag

In dit artikel vindt u stapsgewijze instructies voor het gebruik van de Azure import/export-service om grote hoeveel heden gegevens veilig te exporteren vanuit Azure Blob-opslag. De service vereist dat u lege stations naar het Azure-Data Center verzendt. De service exporteert gegevens van uw opslag account naar de stations en stuurt de stations vervolgens terug.

## <a name="prerequisites"></a>Vereisten

Voordat u een export taak maakt om gegevens over te dragen van Azure Blob Storage, moet u de volgende lijst met vereisten voor deze service aandachtig door nemen en volt ooien.
U moet het volgende doen:

- Een actief Azure-abonnement hebben dat kan worden gebruikt voor de import/export-service.
- Ten minste één Azure Storage account hebben. Zie de lijst met [ondersteunde opslag accounts en opslag typen voor de import/export-service](storage-import-export-requirements.md). Zie [een opslag account maken](../storage/common/storage-account-create.md)voor meer informatie over het maken van een nieuw opslag account.
- Voldoende aantal schijven van [ondersteunde typen](storage-import-export-requirements.md#supported-disks)hebben.
- Een FedEx/DHL-account hebben. Als u een andere transporteur dan FedEx/DHL wilt gebruiken, neemt u contact op met Azure Data Box Operations-team op `adbops@microsoft.com` .
  - Het account moet geldig zijn, moet een saldo hebben en moet de retour verzendings mogelijkheden hebben.
  - Genereer een tracking nummer voor de export taak.
  - Elke taak moet een afzonderlijk traceringsnummer hebben. Meerdere taken met hetzelfde traceringsnummer worden niet ondersteund.
  - Als u geen draaggolf account hebt, gaat u naar:
    - [Een FedEx-account maken](https://www.fedex.com/en-us/create-account.html)of
    - [Maak een DHL-account](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Stap 1: een export taak maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om een export taak te maken in de Azure Portal.

1. Meld u aan bij <https://portal.azure.com/> .
2. Zoeken naar **import/export-taken**.

    ![Zoeken naar import/export-taken](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Selecteer **+ Nieuw**.

    ![Selecteer + Nieuw om een nieuwe te maken ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. In **Basisbeginselen**:

    - Selecteer **exporteren vanuit Azure**.
    - Voer een beschrijvende naam in voor de export taak. Gebruik de naam die u kiest om de voortgang van uw taken bij te houden.
        - De naam mag alleen kleine letters, cijfers, afbreek streepjes en onderstrepings tekens bevatten.
        - De naam moet beginnen met een letter en mag geen spaties bevatten.
    - Selecteer een abonnement.
    - Voer een resource groep in of Selecteer deze.

        ![Basisbeginselen](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **taak Details**:

    - Selecteer het opslag account waarin de gegevens moeten worden geëxporteerd. Gebruik een opslag account waar u zich bevindt.
    - De locatie van de dropoff wordt automatisch ingevuld op basis van de regio van het geselecteerde opslag account.
    - Geef de BLOB-gegevens op die u wilt exporteren uit uw opslag account naar uw lege station of stations.
    - Kies ervoor om alle BLOB-gegevens in het opslag account te **exporteren** .

         ![Alles exporteren](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - U kunt opgeven welke containers en blobs u wilt exporteren.
        - U kunt als volgt **een BLOB opgeven die moet worden geëxporteerd**: gebruik de **gelijkteken** -selector. Geef het relatieve pad naar de BLOB op, beginnend met de container naam. Gebruik *$root* om de basis container op te geven.
        - **Om alle blobs op te geven die beginnen met een voor voegsel**: gebruik de kiezer **begint met** selector. Geef het voor voegsel op, beginnend met een slash/. Het voor voegsel kan het voor voegsel van de container naam, de volledige container naam of de volledige container naam, gevolgd door het voor voegsel van de BLOB-naam. U moet de BLOB-paden in een geldige indeling opgeven om te voor komen dat er fouten optreden tijdens de verwerking, zoals wordt weer gegeven in deze scherm opname. Zie voor [beelden van geldige BLOB-paden](#examples-of-valid-blob-paths)voor meer informatie.

           ![Geselecteerde containers en blobs exporteren](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - U kunt exporteren vanuit het BLOB-lijst bestand.

        ![Exporteren uit BLOB-lijst bestand](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Als de blob die moet worden geëxporteerd, wordt gebruikt tijdens het kopiëren van de gegevens, neemt Azure import/export-service een moment opname van de BLOB en kopieert de moment opname.

6. In **retour verzendings gegevens**:

    - Selecteer de transporteur in de vervolg keuzelijst. Als u een andere transporteur dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolg keuzelijst. Neem contact op met Azure Data Box Operations-team `adbops@microsoft.com`  met de informatie over de provider die u wilt gebruiken.
    - Voer een geldig account nummer van een transporteur in dat u hebt gemaakt met die transporteur. Micro soft gebruikt dit account om de schijven terug naar u te verzenden zodra de export taak is voltooid.
    - Geef een volledige en geldige naam voor de contact persoon, telefoon, e-mail, adres, plaats, post code, provincie en land/regio op.

        > [!TIP]
        > In plaats van een e-mail adres voor één gebruiker op te geven, moet u een groeps-e-mail opgeven. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder deze verlaat.

7. In **samen vatting**:

    - Bekijk de details van de taak.
    - Noteer de naam van de taak en geleverd Azure Data Center-verzend adres voor het verzenden van schijven naar Azure.

        > [!NOTE]
        > De schijven altijd verzenden naar het Data Center dat wordt vermeld in de Azure Portal. Als de schijven naar het verkeerde Data Center worden verzonden, wordt de taak niet verwerkt.

    - Klik op **OK** om het maken van de export taak te volt ooien.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende stappen om een export taak te maken in de Azure Portal.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Een taak maken

1. Gebruik de opdracht [AZ extension add](/cli/azure/extension#az_extension_add) om de extensie [AZ import-export](/cli/azure/ext/import-export/import-export) toe te voegen:

    ```azurecli
    az extension add --name import-export
    ```

1. Als u een lijst wilt weer geven van de locaties waar u schijven kunt ontvangen, gebruikt u de opdracht [AZ import-export location](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. Voer de volgende opdracht [AZ import-export Create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) uit om een export taak te maken die gebruikmaakt van uw bestaande opslag account:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > In plaats van een e-mail adres voor één gebruiker op te geven, moet u een groeps-e-mail opgeven. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder deze verlaat.

   Met deze taak worden alle blobs in uw opslag account geëxporteerd. U kunt een BLOB voor exporteren opgeven door deze waarde voor **--exporteren** te vervangen:

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Deze parameter waarde exporteert de blob met de naam *logo.bmp* in de hoofd container.

   U kunt ook alle blobs in een container selecteren met behulp van een voor voegsel. Vervang deze waarde voor **--export**:

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Zie voor [beelden van geldige BLOB-paden](#examples-of-valid-blob-paths)voor meer informatie.

   > [!NOTE]
   > Als de blob die moet worden geëxporteerd, wordt gebruikt tijdens het kopiëren van de gegevens, neemt Azure import/export-service een moment opname van de BLOB en kopieert de moment opname.

1. Gebruik de opdracht [AZ import-export List](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) om alle taken voor de resource groep myierg te bekijken:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Voer de opdracht [AZ import-export update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) uit om uw taak bij te werken of uw taak te annuleren:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Gebruik de volgende stappen om een export taak te maken in Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Hoewel de Power shell-module **AZ. ImportExport** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet. Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Een taak maken

1. Gebruik de cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) om een lijst op te halen van de locaties waar u schijven kunt ontvangen:

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Voer het volgende [nieuwe AzImportExport-](/powershell/module/az.importexport/new-azimportexport) voor beeld uit om een export taak te maken die gebruikmaakt van uw bestaande opslag account:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
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
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > In plaats van een e-mail adres voor één gebruiker op te geven, moet u een groeps-e-mail opgeven. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder deze verlaat.

   Met deze taak worden alle blobs in uw opslag account geëxporteerd. U kunt een BLOB voor exporteren opgeven door deze waarde te vervangen door **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Deze parameter waarde exporteert de blob met de naam *logo.bmp* in de hoofd container.

   U kunt ook alle blobs in een container selecteren met behulp van een voor voegsel. Vervang deze waarde voor **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Zie voor [beelden van geldige BLOB-paden](#examples-of-valid-blob-paths)voor meer informatie.

   > [!NOTE]
   > Als de blob die moet worden geëxporteerd, wordt gebruikt tijdens het kopiëren van de gegevens, neemt Azure import/export-service een moment opname van de BLOB en kopieert de moment opname.

1. Gebruik de cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) om alle taken voor de resource groep myierg te bekijken:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Voer de cmdlet [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) uit om uw taak bij te werken of uw taak te annuleren:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Stap 2: de stations verzenden

Als u het aantal stations dat u nodig hebt niet weet, gaat u naar het [aantal stations controleren](#check-the-number-of-drives). Als u weet wat het aantal stations is, gaat u verder met het verzenden van de stations.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Stap 3: de taak bijwerken met tracerings informatie

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Stap 4: de schijven ontvangen

Wanneer het dash board rapporteert dat de taak is voltooid, worden de schijven aan u verzonden en het tracerings nummer voor de verzen ding is beschikbaar op de portal.

1. Nadat u de stations met geëxporteerde gegevens hebt ontvangen, moet u de BitLocker-sleutels voor het ontgrendelen van de stations ophalen. Ga naar de export taak in de Azure Portal. Klik op het tabblad **importeren/exporteren** .
2. Selecteer en klik op uw export taak in de lijst. Ga naar **versleuteling** en kopieer de sleutels.

   ![BitLocker-sleutels voor de export taak weer geven](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Gebruik de BitLocker-sleutels om de schijven te ontgrendelen.

Het exporteren is voltooid.

## <a name="step-5-unlock-the-disks"></a>Stap 5: de schijven ontgrendelen

Gebruik de volgende opdracht om het station te ontgrendelen:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Hier volgt een voor beeld van de voorbeeld invoer.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

Op dit moment kunt u de taak verwijderen of laten staan. Taken worden na 90 dagen automatisch verwijderd.

## <a name="check-the-number-of-drives"></a>Controleer het aantal stations

Deze *optionele* stap helpt u bij het bepalen van het aantal stations dat vereist is voor de export taak. Voer deze stap uit op een Windows-systeem met een [ondersteunde besturingssysteem versie](storage-import-export-requirements.md#supported-operating-systems).

1. [Down load de WAImportExport-versie 1](https://www.microsoft.com/download/details.aspx?id=42659) op het Windows-systeem.
2. Unzip naar de standaardmap `waimportexportv1` . Bijvoorbeeld `C:\WaImportExportV1`.
3. Open een Power shell-of opdracht regel venster met Administrator bevoegdheden. Als u de map wilt wijzigen in de map ungezipte, voert u de volgende opdracht uit:

   `cd C:\WaImportExportV1`

4. Als u het aantal schijven wilt controleren dat is vereist voor de geselecteerde blobs, voert u de volgende opdracht uit:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    De para meters worden in de volgende tabel beschreven:

    |Opdracht regel parameter|Description|
    |--------------------------|-----------------|
    |**/logdir:**|Optioneel. De logboekmap. Uitgebreide logboek bestanden worden naar deze map geschreven. Als dat niet is opgegeven, wordt de huidige map gebruikt als Logboekmap.|
    |**SN**|Vereist. De naam van het opslag account voor de export taak.|
    |**SK**|Alleen vereist als er geen container-SAS is opgegeven. De account sleutel voor het opslag account voor de export taak.|
    |**/csas:**|Alleen vereist als er geen sleutel voor het opslag account is opgegeven. De container-SAS voor het weer geven van de blobs die in de export taak moeten worden geëxporteerd.|
    |**/ExportBlobListFile:**|Vereist. Het pad naar het XML-bestand met de lijst met Blob-paden of voor voegsels voor BLOB-pad voor de blobs die moeten worden geëxporteerd. De bestands indeling die wordt gebruikt in het `BlobListBlobPath` element in de [put-taak](/rest/api/storageimportexport/jobs) bewerking van de service import/export-rest API.|
    |**/DriveSize:**|Vereist. De grootte van stations die moeten worden gebruikt voor een export taak, *bijvoorbeeld* 500 GB, 1,5 TB.|

    Bekijk een [voor beeld van de opdracht PreviewExport](#example-of-previewexport-command).

5. Controleer of u kunt lezen/schrijven naar de stations die voor de export taak worden verzonden.

### <a name="example-of-previewexport-command"></a>Voorbeeld van de opdracht PreviewExport

In het volgende voor beeld ziet u de `PreviewExport` opdracht:

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Het exporteren van een BLOB-lijst bestand kan BLOB-namen en BLOB-voor voegsels bevatten, zoals hier wordt weer gegeven:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Het Azure-hulp programma voor importeren/exporteren bevat alle blobs die moeten worden geëxporteerd en berekent hoe ze moeten worden ingepakt in stations met de opgegeven grootte, waarbij rekening wordt gehouden met de nood zakelijke overhead. vervolgens wordt het aantal stations dat nodig is voor het opslaan van blobs en het gebruik van de gegevens van het station geschat.

Hier volgt een voor beeld van de uitvoer, waarbij informatieve logboeken zijn wegge laten:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Voor beelden van geldige BLOB-paden

De volgende tabel bevat voor beelden van geldige BLOB-paden:

   | Kiezer | BLOB-pad | Description |
   | --- | --- | --- |
   | Begint met |/ |Exporteert alle blobs in het opslag account |
   | Begint met |/$root/ |Exporteert alle blobs in de basis container |
   | Begint met |/book |Exporteert alle blobs in een container die begint met het voorvoegsel **boek** |
   | Begint met |audio |Exporteert alle blobs in container **muziek** |
   | Begint met |/music/love |Exporteert alle blobs in container **muziek** die beginnen met het **voor voegsel** |
   | Gelijk aan |$root/logo.bmp |Exporteert BLOB- **logo.bmp** in de hoofd container |
   | Gelijk aan |Video's/story.mp4 |Exporteert BLOB- **story.mp4** in container **Video's** |

## <a name="next-steps"></a>Volgende stappen

- [De taak en de status van het station weer geven](storage-import-export-view-drive-status.md)
- [Vereisten voor importeren/exporteren controleren](storage-import-export-requirements.md)
