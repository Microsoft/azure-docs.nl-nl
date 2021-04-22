---
title: Azure Import/Export gebruiken om gegevens over te dragen naar Azure Blobs | Microsoft Docs
description: Leer hoe u import- en exporttaken maakt in Azure Portal gegevens over te dragen van en naar Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/15/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 39eb6c164751ebdfa293798850a8d663fe988b82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875679"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>De Azure Import/Export-service gebruiken om gegevens te importeren in Azure Blob Storage

In dit artikel vindt u stapsgewijs instructies voor het gebruik van de Azure Import/Export-service om veilig grote hoeveelheden gegevens te importeren in Azure Blob Storage. Als u gegevens wilt importeren in Azure Blobs, moet u voor de service versleutelde schijfstations met uw gegevens verzenden naar een Azure-datacenter.

## <a name="prerequisites"></a>Vereisten

Voordat u een importeer job maakt om gegevens over te dragen naar Azure Blob Storage, moet u de volgende lijst met vereisten voor deze service zorgvuldig controleren en voltooien.
U moet:

* Een actief Azure-abonnement hebben dat kan worden gebruikt voor de Import/Export-service.
* Ten minste één Azure Storage account met een opslagcontainer. Zie de lijst met [ondersteunde opslagaccounts en opslagtypen voor de Import/Export-service.](storage-import-export-requirements.md)
  * Zie How to Create a Storage Account (Een opslagaccount maken) voor meer informatie [over het maken van een nieuw opslagaccount.](../storage/common/storage-account-create.md)
  * Ga naar Een opslagcontainer maken voor informatie [over de opslagcontainer.](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
* Voldoende schijven met [ondersteunde typen hebben.](storage-import-export-requirements.md#supported-disks)
* Een Windows-systeem met een [ondersteunde versie van het besturingssysteem hebben.](storage-import-export-requirements.md#supported-operating-systems)
* Schakel BitLocker in op het Windows-systeem. Zie [BitLocker inschakelen.](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)
* [Download de nieuwste VERSIE 1 van WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) op het Windows-systeem. De nieuwste versie van het hulpprogramma heeft beveiligingsupdates om een externe beveiliging voor de BitLocker-sleutel en de bijgewerkte ontgrendelingsmodus toe te staan.

  * Uitsip naar de standaardmap `waimportexportv1` . Bijvoorbeeld `C:\WaImportExportV1`.
* Een FedEx/DHL-account hebben. Als u een andere vervoerder dan FedEx/DHL wilt gebruiken, neem dan contact op Azure Data Box Operations-team op `adbops@microsoft.com` .
  * Het account moet geldig zijn, moet een saldo hebben en moet retourmogelijkheden hebben.
  * Genereer een traceringsnummer voor de export job.
  * Elke taak moet een afzonderlijk traceringsnummer hebben. Meerdere taken met hetzelfde traceringsnummer worden niet ondersteund.
  * Als u geen provideraccount hebt, gaat u naar:
    * [Een FedEx-account maken,](https://www.fedex.com/en-us/create-account.html)of
    * [Maak een DHL-account.](http://www.dhl-usa.com/en/express/shipping/open_account.html)

## <a name="step-1-prepare-the-drives"></a>Stap 1: de stations voorbereiden

Met deze stap wordt een logboekbestand gegenereerd. In het logboekbestand worden basisgegevens opgeslagen, zoals het serienummer van het station, de versleutelingssleutel en de details van het opslagaccount.

Voer de volgende stappen uit om de stations voor te bereiden.

1. Verbind uw schijfstations met het Windows-systeem via SATA-connectors.
2. Maak één NTFS-volume op elk station. Wijs een stationletter toe aan het volume. Gebruik geen bevestigingspunten.
3. Schakel BitLocker-versleuteling in op het NTFS-volume. Als u een Windows Server-systeem gebruikt, gebruikt u de instructies in [BitLocker inschakelen op Windows Server 2012 R2.](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)
4. Gegevens kopiëren naar versleuteld volume. Gebruik slepen en neerzetten of Robocopy of een dergelijk kopieerprogramma. Er wordt een *logboekbestand (.jrn)* gemaakt in dezelfde map waarin u het hulpprogramma hebt uitgevoerd.

   Als het station is vergrendeld en u het station moet ontgrendelen, kunnen de stappen voor het ontgrendelen verschillen, afhankelijk van uw gebruikscase.

   * Als u gegevens hebt toegevoegd aan een vooraf versleuteld station (het hulpprogramma WAImportExport is niet gebruikt voor versleuteling), gebruikt u de BitLocker-sleutel (een numeriek wachtwoord dat u opgeeft) in de pop-up om het station te ontgrendelen.

   * Als u gegevens hebt toegevoegd aan een station dat is versleuteld door het hulpprogramma WAImportExport, gebruikt u de volgende opdracht om het station te ontgrendelen:

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Open een PowerShell- of opdrachtregelvenster met beheerdersbevoegdheden. Als u de map wilt wijzigen in de uitgepakte map, moet u de volgende opdracht uitvoeren:

    `cd C:\WaImportExportV1`
6. Voer de volgende opdracht uit om de BitLocker-sleutel van het station op te halen:

    `manage-bde -protectors -get <DriveLetter>:`
7. Voer de volgende opdracht uit om de schijf voor te bereiden. **Afhankelijk van de gegevensgrootte kan het voorbereiden van de schijf enkele uren tot dagen duren.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Er wordt een logboekbestand gemaakt in dezelfde map waarin u het hulpprogramma hebt gemaakt. Er worden ook twee andere bestanden gemaakt: een *XML-bestand* (de map waarin u het hulpprogramma kunt uitvoeren) en een *drive-manifest.xml* -bestand (map waarin de gegevens zich bevinden).

    De gebruikte parameters worden beschreven in de volgende tabel:

    |Optie  |Beschrijving  |
    |---------|---------|
    |/j:     |De naam van het logboekbestand, met de extensie .jrn. Er wordt per station een logboekbestand gegenereerd. U wordt aangeraden het serienummer van de schijf te gebruiken als de naam van het logboekbestand.         |
    |/ID:     |De sessie-id. Gebruik een uniek sessienummer voor elk exemplaar van de opdracht.      |
    |/t:     |De letter van de schijf die moet worden verzonden. Bijvoorbeeld station `D` .         |
    |/bk:     |De BitLocker-sleutel voor het station. Het numerieke wachtwoord van de uitvoer van `manage-bde -protectors -get D:`      |
    |/srcdir:     |De stationletter van de schijf die moet worden verzonden, gevolgd door `:\` . Bijvoorbeeld `D:\`.         |
    |/dstdir:     |De naam van de doelcontainer in Azure Storage.         |
    |/blobtype:     |Met deze optie geeft u het type blobs op waar u de gegevens in wilt importeren. Voor blok-blobs is het blobtype `BlockBlob` en voor pagina-blobs is dit `PageBlob` .         |
    |/skipwrite:     | Hiermee geeft u op dat er geen nieuwe gegevens nodig zijn om te worden gekopieerd en dat bestaande gegevens op de schijf moeten worden voorbereid.          |
    |/enablecontentmd5:     |Wanneer de optie is ingeschakeld, zorgt u ervoor dat MD5 wordt berekend en ingesteld als `Content-md5` eigenschap voor elke blob. Gebruik deze optie alleen als u het veld wilt `Content-md5` gebruiken nadat de gegevens zijn geüpload naar Azure. <br> Deze optie heeft geen invloed op de gegevensintegriteitscontrole (die standaard wordt weergegeven). De instelling verhoogt de tijd die nodig is om gegevens te uploaden naar de cloud.          |
8. Herhaal de vorige stap voor elke schijf die moet worden verzonden. Er wordt een logboekbestand met de opgegeven naam gemaakt voor elke run van de opdrachtregel.

    > [!IMPORTANT]
    > * Samen met het logboekbestand wordt `<Journal file name>_DriveInfo_<Drive serial ID>.xml` er ook een bestand gemaakt in dezelfde map waarin het hulpprogramma zich bevindt. Het XML-bestand wordt gebruikt in plaats van het logboekbestand bij het maken van een taak als het logboekbestand te groot is.
   > * De maximale grootte van het logboekbestand dat de portal toestaat, is 2 MB. Als het logboekbestand deze limiet overschrijdt, wordt er een fout geretourneerd.

## <a name="step-2-create-an-import-job"></a>Stap 2: een importeerstap maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om een importeer job te maken in Azure Portal.

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

   1. Upload de logboekbestanden die u hebt gemaakt tijdens de vorige [stap 1: de stations voorbereiden.](#step-1-prepare-the-drives) Als `waimportexport.exe version1` is gebruikt, uploadt u één bestand voor elk station dat u hebt voorbereid. Als de grootte van het logboekbestand groter is dan 2 MB, kunt u de `<Journal file name>_DriveInfo_<Drive serial ID>.xml` ook gemaakt met het logboekbestand gebruiken.
   1. Selecteer de Azure-doelregio voor de order.
   1. Selecteer het opslagaccount voor het importeren.
      
      De locatie van de vervolgkeuze wordt automatisch ingevuld op basis van de regio van het geselecteerde opslagaccount.
   1. Als u geen uitgebreid logboek wilt opslaan, wist u het logboek Uitgebreid opslaan in de **blobcontainer 'waimportexport'.**

   ![Importeer job maken - Stap 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   Selecteer **Volgende: Verzendadres >** door te gaan.

6. In **Verzending:**

   1. Selecteer de vervoerder in de vervolgkeuzelijst. Als u een andere vervoerder dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolgkeuzekeuze. Neem contact Azure Data Box met het `adbops@microsoft.com`  Operations-team op met de informatie over de vervoerder die u wilt gebruiken.
   1. Voer een geldig provideraccountnummer in dat u met die vervoerder hebt gemaakt. Microsoft gebruikt dit account om de stations naar u terug te verzenden zodra uw import job is voltooid. Als u geen accountnummer hebt, maakt u een [FedEx-](https://www.fedex.com/us/oadr/) of DHL-vervoerdersaccount. [](https://www.dhl.com/)
   1.  Geef een volledige en geldige contactgegevens, telefoonnummer, e-mailadres, adres, plaats, postcode, staat/provincie en land/regio op.

       > [!TIP]
       > In plaats van een e-mailadres voor één gebruiker op te geven, geeft u een groeps-e-mailadres op. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder weggaat.

   ![Importeerstap maken - Stap 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Selecteer **Beoordelen en maken om door** te gaan.

7. In de ordersamenvatting:

   1. Lees de **voorwaarden** en selecteer vervolgens 'Ik bevestig dat alle verstrekte informatie juist is en ga akkoord met de voorwaarden'. Validatie wordt vervolgens uitgevoerd.
   1. Bekijk de taakinformatie in de samenvatting. Noteer de taaknaam en het verzendadres van het Azure-datacenter om schijven terug te sturen naar Azure. Deze informatie wordt later op het verzendlabel gebruikt.
   1. Selecteer **Maken**.

     ![Importeerstap maken - Stap 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende stappen om een importeer job te maken in de Azure CLI.

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
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
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

1. U kunt een bestaand opslagaccount gebruiken of er een maken. Voer de cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) uit om een opslagaccount te maken:

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

## <a name="step-3-optional-configure-customer-managed-key"></a>Stap 3 (optioneel): Door de klant beheerde sleutel configureren

Sla deze stap over en ga naar de volgende stap als u de door Microsoft beheerde sleutel wilt gebruiken om uw BitLocker-sleutels voor de stations te beveiligen. Als u uw eigen sleutel wilt configureren om de BitLocker-sleutel te beveiligen, volgt u de instructies in Door de klant beheerde sleutels configureren met Azure Key Vault voor [Azure Import/Export in de Azure Portal](storage-import-export-encryption-key-portal.md).

## <a name="step-4-ship-the-drives"></a>Stap 4: de stations verzenden

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Stap 5: de taak bijwerken met traceringsgegevens

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Stap 6: het uploaden van gegevens naar Azure controleren

Houd de taak bij tot deze is voltooid. Nadat de taak is voltooid, controleert u of uw gegevens zijn geüpload naar Azure. Verwijder de on-premises gegevens pas nadat u hebt gecontroleerd of het uploaden is geslaagd.

## <a name="next-steps"></a>Volgende stappen

* [De status van de taak en het station weergeven](storage-import-export-view-drive-status.md)
* [Vereisten voor importeren/exporteren controleren](storage-import-export-requirements.md)
