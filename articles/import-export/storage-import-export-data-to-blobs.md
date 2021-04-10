---
title: Azure import/export gebruiken om gegevens over te dragen naar Azure-blobs | Microsoft Docs
description: Meer informatie over het maken van import-en export taken in Azure Portal om gegevens over te dragen van en naar Azure-blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/15/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 74f5565ba9dfa48dabfe56c25e3ef30a8caafe14
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563280"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>De Azure import/export-service gebruiken om gegevens te importeren in Azure Blob Storage

In dit artikel vindt u stapsgewijze instructies voor het gebruik van de Azure import/export-service om grote hoeveel heden gegevens veilig te importeren in Azure Blob-opslag. Als u gegevens wilt importeren in azure-blobs, moet u met de service versleutelde schijf stations met uw gegevens naar een Azure-Data Center verzenden.

## <a name="prerequisites"></a>Vereisten

Voordat u een import taak maakt om gegevens over te dragen naar Azure Blob Storage, moet u de volgende lijst met vereisten voor deze service aandachtig door nemen en volt ooien.
U moet het volgende doen:

* Een actief Azure-abonnement hebben dat kan worden gebruikt voor de import/export-service.
* U hebt ten minste één Azure Storage account met een opslag container. Zie de lijst met [ondersteunde opslag accounts en opslag typen voor de import/export-service](storage-import-export-requirements.md).
  * Zie [een opslag account maken](../storage/common/storage-account-create.md)voor meer informatie over het maken van een nieuw opslag account.
  * Voor informatie over opslag container gaat u naar [een opslag container maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Voldoende aantal schijven van [ondersteunde typen](storage-import-export-requirements.md#supported-disks)hebben.
* Een Windows-systeem met een [ondersteunde besturingssysteem versie](storage-import-export-requirements.md#supported-operating-systems)hebben.
* Schakel BitLocker in op het Windows-systeem. Zie [BitLocker inschakelen voor meer informatie](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Down load de meest recente versie 1 van WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) op het Windows-systeem. De nieuwste versie van het hulp programma bevat beveiligings updates voor het toestaan van een externe Protector voor de BitLocker-sleutel en de bijgewerkte functie ontgrendelings modus.

  * Unzip naar de standaardmap `waimportexportv1` . Bijvoorbeeld `C:\WaImportExportV1`.
* Een FedEx/DHL-account hebben. Als u een andere transporteur dan FedEx/DHL wilt gebruiken, neemt u contact op met Azure Data Box Operations-team op `adbops@microsoft.com` .
  * Het account moet geldig zijn, moet een saldo hebben en moet de retour verzendings mogelijkheden hebben.
  * Genereer een tracking nummer voor de export taak.
  * Elke taak moet een afzonderlijk traceringsnummer hebben. Meerdere taken met hetzelfde traceringsnummer worden niet ondersteund.
  * Als u geen draaggolf account hebt, gaat u naar:
    * [Een FedEx-account maken](https://www.fedex.com/en-us/create-account.html)of
    * [Maak een DHL-account](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Stap 1: de stations voorbereiden

Met deze stap wordt een logboek bestand gegenereerd. In het logboek bestand worden basis gegevens opgeslagen, zoals serie nummer van het station, de versleutelings sleutel en Details van het opslag account.

Voer de volgende stappen uit om de stations voor te bereiden.

1. Verbind uw schijf stations met het Windows-systeem via SATA-connectors.
2. Maak één NTFS-volume op elk station. Wijs een stationsletter aan het volume toe. Gebruik mountpoints niet.
3. BitLocker-versleuteling inschakelen op het NTFS-volume. Als u een Windows Server-systeem gebruikt, gebruikt u de instructies in [How to Enable BitLocker in Windows server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Gegevens kopiëren naar versleuteld volume. Gebruik slepen en neerzetten of Robocopy of een dergelijk Kopieer programma. Er wordt een logboek bestand (*. jrn*) gemaakt in dezelfde map waarin u het hulp programma uitvoert.

   Als het station is vergrendeld en u het station moet ontgrendelen, zijn de stappen voor ontgrendelen mogelijk anders, afhankelijk van uw use-case.

   * Als u gegevens hebt toegevoegd aan een vooraf versleuteld station (WAImportExport-hulp programma is niet gebruikt voor versleuteling), gebruikt u de BitLocker-sleutel (een numeriek wacht woord dat u opgeeft) in de pop-up om het station te ontgrendelen.

   * Als u gegevens hebt toegevoegd aan een station dat is versleuteld met het hulp programma WAImportExport, gebruikt u de volgende opdracht om het station te ontgrendelen:

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Open een Power shell-of opdracht regel venster met Administrator bevoegdheden. Als u de map wilt wijzigen in de map ungezipte, voert u de volgende opdracht uit:

    `cd C:\WaImportExportV1`
6. Als u de BitLocker-sleutel van het station wilt ophalen, voert u de volgende opdracht uit:

    `manage-bde -protectors -get <DriveLetter>:`
7. Voer de volgende opdracht uit om de schijf voor te bereiden. **Afhankelijk van de grootte van de gegevens kan het enkele uren tot dagen duren voordat de schijf is voor bereid.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Er wordt een logboek bestand gemaakt in dezelfde map waarin u het hulp programma hebt uitgevoerd. Er worden ook twee andere bestanden gemaakt: een *. XML* -bestand (de map waarin u het hulp programma uitvoert) en een *drive-manifest.xml* bestand (de map waarin de gegevens zich bevinden).

    De gebruikte para meters worden in de volgende tabel beschreven:

    |Optie  |Beschrijving  |
    |---------|---------|
    |/j     |De naam van het logboek bestand met de extensie. jrn. Er wordt een logboek bestand gegenereerd per station. Het is raadzaam om het serie nummer van de schijf als de naam van het logboek bestand te gebruiken.         |
    |/ID:     |De sessie-ID. Gebruik een uniek sessie nummer voor elk exemplaar van de opdracht.      |
    |/t:     |De stationsletter van de schijf die moet worden verzonden. Bijvoorbeeld station `D` .         |
    |/bk:     |De BitLocker-sleutel voor het station. Het numerieke wacht woord van de uitvoer van `manage-bde -protectors -get D:`      |
    |/srcdir:     |De stationsletter van de schijf die moet worden verzonden, gevolgd door `:\` . Bijvoorbeeld `D:\`.         |
    |/dstdir:     |De naam van de doel container in Azure Storage.         |
    |/blobtype:     |Met deze optie geeft u het type blobs op waarnaar u de gegevens wilt importeren. Voor blok-blobs is het type BLOB `BlockBlob` en voor pagina-blobs `PageBlob` .         |
    |/skipwrite:     | Hiermee geeft u op dat er geen nieuwe gegevens moeten worden gekopieerd en dat bestaande gegevens op de schijf moeten worden voor bereid.          |
    |/enablecontentmd5:     |Als u deze optie inschakelt, zorgt u ervoor dat MD5 wordt berekend en is ingesteld als `Content-md5` eigenschap op elke blob. Gebruik deze optie alleen als u het veld wilt gebruiken `Content-md5` nadat de gegevens zijn geüpload naar Azure. <br> Deze optie is niet van invloed op de gegevens integriteits controle (dit gebeurt standaard). De instelling neemt de benodigde tijd voor het uploaden van gegevens naar de Cloud toe.          |
8. Herhaal de vorige stap voor elke schijf die moet worden verzonden. Er wordt een logboek bestand met de gegeven naam gemaakt voor elke uitvoering van de opdracht regel.

    > [!IMPORTANT]
    > * Samen met het logboek bestand `<Journal file name>_DriveInfo_<Drive serial ID>.xml` wordt ook een bestand gemaakt in de map waarin het hulp programma zich bevindt. Het. XML-bestand wordt gebruikt in plaats van het logboek bestand bij het maken van een taak als het logboek bestand te groot is.
   > * De maximale grootte van het logboek bestand dat door de portal wordt toegestaan, is 2 MB. Als het logboek bestand deze limiet overschrijdt, wordt er een fout geretourneerd.

## <a name="step-2-create-an-import-job"></a>Stap 2: een import taak maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om een import taak te maken in de Azure Portal.

1. Meld u aan bij https://portal.azure.com/ .
2. Zoeken naar **import/export-taken**.

   ![Zoeken in import/export-taken](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Selecteer **+ Nieuw**.

   ![Selecteer Nieuw om een nieuwe te maken ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. In **Basisbeginselen**:

   1. Selecteer een abonnement.
   1. Selecteer een resource groep of selecteer **nieuwe maken** en maak een nieuwe.
   1. Voer een beschrijvende naam in voor de import taak. Gebruik de naam om de voortgang van uw taken bij te houden.
      * De naam mag alleen kleine letters, cijfers en afbreek streepjes bevatten.
      * De naam moet beginnen met een letter en mag geen spaties bevatten.

   1. Selecteer **importeren in azure**.

    ![Import taak maken-stap 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

    Selecteer **volgende: taak details >** om door te gaan.

5. In **taak Details**:

   1. Upload de logboek bestanden die u hebt gemaakt tijdens de vorige [stap 1: bereid de stations](#step-1-prepare-the-drives)voor. Als `waimportexport.exe version1` deze is gebruikt, uploadt u één bestand voor elk station dat u hebt voor bereid. Als de grootte van het logboek bestand groter is dan 2 MB, kunt u `<Journal file name>_DriveInfo_<Drive serial ID>.xml` ook de met het logboek bestand gemaakt maken.
   1. Selecteer de Azure-doel regio voor de order.
   1. Selecteer het opslag account voor het importeren.
      
      De locatie van de dropoff wordt automatisch ingevuld op basis van de regio van het geselecteerde opslag account.
   1. Als u geen uitgebreid logboek wilt opslaan, schakelt u het selectie vakje **uitgebreid logboek opslaan in de BLOB-container van ' waimportexport '** uit.

   ![Import taak maken-stap 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   Selecteer **volgende: verzend >** om door te gaan.

6. Bij **verzen ding**:

   1. Selecteer de transporteur in de vervolg keuzelijst. Als u een andere transporteur dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolg keuzelijst. Neem contact op met Azure Data Box Operations-team `adbops@microsoft.com`  met de informatie over de provider die u wilt gebruiken.
   1. Voer een geldig account nummer van een transporteur in dat u hebt gemaakt met die transporteur. Micro soft gebruikt dit account om de schijven terug naar u te verzenden zodra uw import taak is voltooid. Als u geen account nummer hebt, maakt u een [FedEx](https://www.fedex.com/us/oadr/) -of [DHL](https://www.dhl.com/) -draaggolf account.
   1.  Geef een volledige en geldige naam op voor de contact persoon, telefoon, e-mail, adres, plaats, post code, provincie en land/regio.

       > [!TIP]
       > In plaats van een e-mail adres voor één gebruiker op te geven, moet u een groeps-e-mail opgeven. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder deze verlaat.

   ![Import taak maken-stap 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Selecteer **controleren + maken** om door te gaan.

7. In het overzicht van de bestelling:

   1. Bekijk de **voor waarden** en selecteer ik erken dat alle verstrekte informatie juist is en ga akkoord met de voor waarden. De validatie wordt vervolgens uitgevoerd.
   1. Bekijk de taak gegevens die in de samen vatting worden weer gegeven. Noteer de naam van de taak en het verzend adres van Azure Data Center om schijven terug te sturen naar Azure. Deze informatie wordt later op het verzend label gebruikt.
   1. Selecteer **Maken**.

     ![Import taak maken-stap 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende stappen om een import taak te maken in de Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Een taak maken

1. Gebruik de opdracht [AZ extension add](/cli/azure/extension#az_extension_add) om de extensie [AZ import-export](/cli/azure/ext/import-export/import-export) toe te voegen:

    ```azurecli
    az extension add --name import-export
    ```

1. U kunt een bestaande resource groep gebruiken of er een maken. Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken:

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. U kunt een bestaand opslag account gebruiken of er een maken. Als u een opslag account wilt maken, voert u de opdracht [AZ Storage account create](/cli/azure/storage/account#az_storage_account_create) uit:

    ```azurecli
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
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

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

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

## <a name="step-3-optional-configure-customer-managed-key"></a>Stap 3 (optioneel): door de klant beheerde sleutel configureren

Sla deze stap over en ga naar de volgende stap als u de door micro soft beheerde sleutel wilt gebruiken om uw BitLocker-sleutels voor de stations te beveiligen. Als u uw eigen sleutel voor het beveiligen van de BitLocker-sleutel wilt configureren, volgt u de instructies in [door de klant beheerde sleutels configureren met Azure Key Vault voor Azure import/export in de Azure Portal](storage-import-export-encryption-key-portal.md).

## <a name="step-4-ship-the-drives"></a>Stap 4: de stations verzenden

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Stap 5: de taak bijwerken met tracerings informatie

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Stap 6: gegevens uploaden naar Azure controleren

De taak bijhouden tot voltooiing. Nadat de taak is voltooid, controleert u of uw gegevens zijn geüpload naar Azure. Verwijder de on-premises gegevens pas nadat u hebt gecontroleerd of de upload is geslaagd.

## <a name="next-steps"></a>Volgende stappen

* [De taak en de status van het station weer geven](storage-import-export-view-drive-status.md)
* [Vereisten voor importeren/exporteren controleren](storage-import-export-requirements.md)
