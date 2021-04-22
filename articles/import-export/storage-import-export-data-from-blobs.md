---
title: Azure Import/Export gebruiken om gegevens te exporteren uit Azure Blobs | Microsoft Docs
description: Meer informatie over het maken van exporttaken in Azure Portal om gegevens over te dragen van Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 2d4885f23e775f84a412d176568d992ebe01166b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875697"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>De Azure Import/Export-service gebruiken voor het exporteren van gegevens uit Azure Blob-opslag

In dit artikel vindt u stapsgewijs instructies voor het gebruik van de Azure Import/Export-service om veilig grote hoeveelheden gegevens uit Azure Blob Storage te exporteren. Voor de service moet u lege stations naar het Azure-datacenter verzenden. De service exporteert gegevens van uw opslagaccount naar de stations en stuurt de stations vervolgens terug.

## <a name="prerequisites"></a>Vereisten

Voordat u een export job maakt om gegevens over te dragen uit Azure Blob Storage, moet u de volgende lijst met vereisten voor deze service zorgvuldig controleren en voltooien.
U moet:

- Een actief Azure-abonnement hebben dat kan worden gebruikt voor de Import/Export-service.
- Ten minste één account Azure Storage account. Zie de lijst met [ondersteunde opslagaccounts en opslagtypen voor de Import/Export-service.](storage-import-export-requirements.md) Zie How to Create a Storage Account (Een opslagaccount maken) voor meer informatie over [het maken van een nieuw opslagaccount.](../storage/common/storage-account-create.md)
- Voldoende schijven van [ondersteunde typen hebben.](storage-import-export-requirements.md#supported-disks)
- Een FedEx/DHL-account hebben. Als u een andere vervoerder dan FedEx/DHL wilt gebruiken, neem dan contact op met Azure Data Box Operations-team op `adbops@microsoft.com` .
  - Het account moet geldig zijn, moet een saldo hebben en moet retourmogelijkheden hebben.
  - Genereer een traceringsnummer voor de export job.
  - Elke taak moet een afzonderlijk traceringsnummer hebben. Meerdere taken met hetzelfde traceringsnummer worden niet ondersteund.
  - Als u geen provideraccount hebt, gaat u naar:
    - [Een FedEx-account maken,](https://www.fedex.com/en-us/create-account.html)of
    - [Maak een DHL-account.](http://www.dhl-usa.com/en/express/shipping/open_account.html)

## <a name="step-1-create-an-export-job"></a>Stap 1: een export job maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om een export job te maken in de Azure Portal.

1. Meld u aan bij <https://portal.azure.com/> .
2. Zoek naar **import-/exporttaken.**

    ![Zoeken naar import-/exporttaken](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Selecteer **+ Nieuw**.

    ![Selecteer + Nieuw om een nieuwe te maken ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. In **Basisbeginselen**:

   1. Selecteer een abonnement.
   1. Selecteer een resourcegroep of selecteer **Nieuwe maken** en maak een nieuwe.
   1. Voer een beschrijvende naam in voor de import job. Gebruik de naam om de voortgang van uw taken bij te houden.
       * De naam mag alleen kleine letters, cijfers en afbreekstreelopen bevatten.
       * De naam moet beginnen met een letter en mag geen spaties bevatten.

   1. Selecteer **Exporteren vanuit Azure**.

    ![Basisopties voor een exportorder](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    Selecteer **Volgende: Taakdetails >** om door te gaan.

5. In **Taakdetails:**

   1. Selecteer de Azure-regio waar uw gegevens zich momenteel zijn.
   1. Selecteer het opslagaccount van waaruit u gegevens wilt exporteren. Gebruik een opslagaccount dicht bij uw locatie.

      De locatie van de vervolgkeuze wordt automatisch ingevuld op basis van de regio van het geselecteerde opslagaccount.

   1. Geef de blobgegevens op die u wilt exporteren van uw opslagaccount naar een leeg station of station. Kies een van de drie volgende methoden.

      - Kies ervoor **om alle blobgegevens** in het opslagaccount te exporteren.

        ![Alles exporteren](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - Kies **Geselecteerde containers en blobs** en geef containers en blobs op die moeten worden geëxporteerd. U kunt meer dan een van de selectiemethoden gebruiken. Als u de **optie** Toevoegen selecteert, wordt aan de rechterkant een deelvenster geopend waarin u uw selectiereeksen kunt toevoegen.

        |Optie|Beschrijving|
        |------|-----------|      
        |**Containers toevoegen**|Alle blobs in een container exporteren.<br>Selecteer **Containers toevoegen** en voer elke containernaam in.|
        |**Blobs toevoegen**|Geef afzonderlijke blobs op die moeten worden geëxporteerd.<br>Selecteer **Blobs toevoegen.** Geef vervolgens het relatieve pad naar de blob op, te beginnen met de containernaam. Gebruik *$root* om de hoofdcontainer op te geven.<br>U moet de blobpaden in een geldige indeling verstrekken om fouten tijdens de verwerking te voorkomen, zoals wordt weergegeven in deze schermopname. Zie Voorbeelden van geldige [blobpaden voor meer informatie.](#examples-of-valid-blob-paths)|
        |**Voorvoegsels toevoegen**|Gebruik een voorvoegsel om een set met vergelijkbare namen of met dezelfde naam blobs in een container te selecteren. Het voorvoegsel kan het voorvoegsel van de containernaam, de volledige containernaam of een volledige containernaam zijn, gevolgd door het voorvoegsel van de blobnaam. |

        ![Geselecteerde containers en blobs exporteren](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - Kies **Exporteren uit bloblijstbestand (XML-indeling)** en selecteer een XML-bestand met een lijst met paden en voorvoegsels voor de blobs die moeten worden geëxporteerd uit het opslagaccount. U moet het XML-bestand maken en opslaan in een container voor het opslagaccount. Het bestand kan niet leeg zijn.

      > [!IMPORTANT]
      > Als u een XML-bestand gebruikt om de blobs te selecteren die u wilt exporteren, moet u ervoor zorgen dat de XML geldige paden en/of voorvoegsels bevat. Als het bestand ongeldig is of geen gegevens overeenkomt met de opgegeven paden, wordt de order beëindigd met gedeeltelijke gegevens of geen gegevens geëxporteerd.

       Zie Export order using XML file (Order exporteren met XML-bestand) voor meer informatie over het toevoegen van een [XML-bestand aan een container.](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file)

      ![Exporteren vanuit bloblijstbestand](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Als een blob die moet worden geëxporteerd tijdens het kopiëren van gegevens in gebruik is, maakt de Azure Import/Export-service een momentopname van de blob en kopieert deze de momentopname.

   Selecteer **Volgende: Verzendadres >** door te gaan.

6. In **Verzending:**

    - Selecteer de vervoerder in de vervolgkeuzelijst. Als u een andere vervoerder dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolgkeuzekeuze. Neem contact Azure Data Box operations-team `adbops@microsoft.com`  op met de informatie over de vervoerder die u wilt gebruiken.
    - Voer een geldig accountnummer van de vervoerder in dat u met die vervoerder hebt gemaakt. Microsoft gebruikt dit account om de stations naar u terug te verzenden zodra uw export job is voltooid.
    - Geef een volledige en geldige contactgegevens, telefoonnummer, e-mailadres, adres, plaats, postcode, staat/provincie en land/regio op.

        > [!TIP]
        > In plaats van een e-mailadres op te geven voor één gebruiker, geeft u een groeps-e-mailadres op. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder weggaat.

    Selecteer **Beoordelen en maken om door** te gaan.

7. In **Beoordelen en maken:**

   1. Bekijk de details van de taak.
   1. Noteer de taaknaam en het opgegeven verzendadres van het Azure-datacenter voor het verzenden van schijven naar Azure.

      > [!NOTE]
      > Verzend de schijven altijd naar het datacenter dat in de Azure Portal. Als de schijven naar het verkeerde datacenter worden verzonden, wordt de taak niet verwerkt.

   1. Lees de **voorwaarden voor** uw bestelling voor het verwijderen van privacy- en brongegevens. Als u akkoord gaat met de voorwaarden, selecteert u het selectievakje onder de voorwaarden. De validatie van de bestelling begint.

   ![Uw exportorder controleren en maken](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. Nadat de validatie is uitgevoerd, **selecteert u Maken.**

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende stappen om een export job te maken in de Azure Portal.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Een taak maken

1. Gebruik de [opdracht az extension add](/cli/azure/extension#az_extension_add) om de extensie az [import-export toe te](/cli/azure/import-export) voegen:

    ```azurecli
    az extension add --name import-export
    ```

1. Gebruik de opdracht [az import-export location list](/cli/azure/import-export/location#az_import_export_location_list) om een lijst op te halen met de locaties van waaruit u schijven kunt ontvangen:

    ```azurecli
    az import-export location list
    ```

1. Voer de volgende [opdracht az import-export create uit](/cli/azure/import-export#az_import_export_create) om een exportopdracht te maken die gebruikmaakt van uw bestaande opslagaccount:

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
    > In plaats van een e-mailadres voor één gebruiker op te geven, geeft u een groeps-e-mailadres op. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder weggaat.

   Met deze taak exporteert u alle blobs in uw opslagaccount. U kunt een blob opgeven voor export door deze waarde te vervangen door **--export:**

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Met deze parameterwaarde exporteert u de blob *logo.bmp* naam in de hoofdcontainer.

   U kunt ook alle blobs in een container selecteren met behulp van een voorvoegsel. Vervang deze waarde voor **--export:**

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Zie Voorbeelden van geldige [blobpaden voor meer informatie.](#examples-of-valid-blob-paths)

   > [!NOTE]
   > Als de blob die moet worden geëxporteerd tijdens het kopiëren van gegevens in gebruik is, maakt de Azure Import/Export-service een momentopname van de blob en kopieert deze de momentopname.

1. Gebruik de [opdracht az import-export list](/cli/azure/import-export#az_import_export_list) om alle taken voor de resourcegroep myierg weer te geven:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Voer de opdracht [az import-export update](/cli/azure/import-export#az_import_export_update) uit om uw taak bij te werken of uw taak te annuleren:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Gebruik de volgende stappen om een export job te maken in Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Hoewel de **PowerShell-module Az.ImportExport** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet . Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Een taak maken

1. Gebruik de cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) om een lijst op te halen met de locaties van waaruit u schijven kunt ontvangen:

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Voer het volgende [Voorbeeld van New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) uit om een export job te maken die gebruikmaakt van uw bestaande opslagaccount:

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
    > In plaats van een e-mailadres op te geven voor één gebruiker, geeft u een groeps-e-mailadres op. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder weggaat.

   Met deze taak exporteert u alle blobs in uw opslagaccount. U kunt een blob opgeven voor export door deze waarde te vervangen door **-ExportBlobListblobPath:**

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Met deze parameterwaarde exporteert u de blob *logo.bmp* in de hoofdcontainer.

   U kunt ook alle blobs in een container selecteren met behulp van een voorvoegsel. Vervang deze waarde voor **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Zie Voorbeelden van geldige [blobpaden voor meer informatie.](#examples-of-valid-blob-paths)

   > [!NOTE]
   > Als de blob die moet worden geëxporteerd tijdens het kopiëren van gegevens in gebruik is, maakt de Azure Import/Export-service een momentopname van de blob en kopieert deze de momentopname.

1. Gebruik de cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) om alle taken voor de resourcegroep myierg te bekijken:

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

Als u niet weet hoeveel stations u nodig hebt, gaat u naar [Het aantal stations controleren.](#check-the-number-of-drives) Als u het aantal stations weet, gaat u verder met het verzenden van de stations.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Stap 3: de taak bijwerken met traceringsgegevens

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Stap 4: de schijven ontvangen

Wanneer het dashboard rapporteert dat de taak is voltooid, worden de schijven naar u verzonden en is het traceringsnummer voor de verzending beschikbaar op de portal.

1. Nadat u de stations met geëxporteerde gegevens hebt ontvangen, moet u de BitLocker-sleutels ophalen om de stations te ontgrendelen. Ga naar de export job in de Azure Portal. Klik **op het tabblad Importeren/exporteren.**
2. Selecteer en klik op uw export job in de lijst. Ga naar **Versleuteling** en kopieer de sleutels.

   ![BitLocker-sleutels voor export job weergeven](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Gebruik de BitLocker-sleutels om de schijven te ontgrendelen.

De export is voltooid.

## <a name="step-5-unlock-the-disks"></a>Stap 5: de schijven ontgrendelen

Gebruik de volgende opdracht om het station te ontgrendelen:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Hier is een voorbeeld van de voorbeeldinvoer.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

Op dit moment kunt u de taak verwijderen of deze verlaten. Taken worden na 90 dagen automatisch verwijderd.

## <a name="check-the-number-of-drives"></a>Het aantal stations controleren

Deze *optionele* stap helpt u bij het bepalen van het aantal stations dat is vereist voor de export-taak. Voer deze stap uit op een Windows-systeem met een [ondersteunde versie van het besturingssysteem.](storage-import-export-requirements.md#supported-operating-systems)

1. [Download waImportExport versie 1](https://www.microsoft.com/download/details.aspx?id=42659) op het Windows-systeem.
2. Uitsip naar de standaardmap `waimportexportv1` . Bijvoorbeeld `C:\WaImportExportV1`.
3. Open een PowerShell- of opdrachtregelvenster met beheerdersbevoegdheden. Als u de map wilt wijzigen in de uitgepakte map, moet u de volgende opdracht uitvoeren:

   `cd C:\WaImportExportV1`

4. Voer de volgende opdracht uit om het aantal schijven te controleren dat is vereist voor de geselecteerde blobs:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    De parameters worden beschreven in de volgende tabel:

    |Opdrachtregelparameter|Description|
    |--------------------------|-----------------|
    |**/logdir:**|Optioneel. De logboekmap. Uitgebreide logboekbestanden worden naar deze map geschreven. Als dit niet wordt opgegeven, wordt de huidige map gebruikt als de logboekmap.|
    |**/sn:**|Vereist. De naam van het opslagaccount voor de export job.|
    |**/sk:**|Alleen vereist als er geen container-SAS is opgegeven. De accountsleutel voor het opslagaccount voor de export-taak.|
    |**/csas:**|Alleen vereist als er geen opslagaccountsleutel is opgegeven. De container-SAS voor het bekijken van de blobs die moeten worden geëxporteerd in de export-taak.|
    |**/ExportBlobListFile:**|Vereist. Pad naar het XML-bestand met een lijst met blobpaden of blobpad-voorvoegsels voor de blobs die moeten worden geëxporteerd. De bestandsindeling die wordt gebruikt in `BlobListBlobPath` het -element in [de bewerking Taak](/rest/api/storageimportexport/jobs) zetten van de import/export-service REST API.|
    |**/DriveSize:**|Vereist. De grootte van stations die moeten worden gebruikt voor een export-taak, bijvoorbeeld 500 GB, 1,5 TB.|

    Zie een [voorbeeld van de previewExport-opdracht](#example-of-previewexport-command).

5. Controleer of u kunt lezen/schrijven naar de stations die worden verzonden voor de export-taak.

### <a name="example-of-previewexport-command"></a>Voorbeeld van de opdracht PreviewExport

In het volgende voorbeeld wordt de opdracht `PreviewExport` gedemonstreerd:

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Het exportbloblijstbestand kan blobnamen en blob-voorvoegsels bevatten, zoals hier wordt weergegeven:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Het Azure Import/Export-hulpprogramma vermeldt alle blobs die moeten worden geëxporteerd en berekent hoe ze in schijven van de opgegeven grootte moeten worden inpakken, rekening houdend met eventuele benodigde overhead. Vervolgens wordt een schatting gemaakt van het aantal stations dat nodig is om de blobs te bevatten en gebruiksgegevens te station.

Hier is een voorbeeld van de uitvoer, met informatielogboeken weggelaten:

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

## <a name="examples-of-valid-blob-paths"></a>Voorbeelden van geldige blobpaden

In de volgende tabel ziet u voorbeelden van geldige blobpaden:

   | Kiezer | Blobpad | Description |
   | --- | --- | --- |
   | Begint met |/ |Exporteert alle blobs in het opslagaccount |
   | Begint met |/$root/ |Exporteert alle blobs in de hoofdcontainer |
   | Begint met |/book |Hiermee exporteert u alle blobs in een container die begint met het **voorvoegselboek** |
   | Begint met |/music/ |Exporteert alle blobs in container **music** |
   | Begint met |/music/love |Exporteert alle blobs in container **muziek** die beginnen met voorvoegsel **love** |
   | Gelijk aan |$root/logo.bmp |**Blob-logo.bmp** in de hoofdcontainer exporteren |
   | Gelijk aan |video's/story.mp4 |**Blob-story.mp4** in containervideo's **exporteren** |

## <a name="next-steps"></a>Volgende stappen

- [De status van de taak en het station weergeven](storage-import-export-view-drive-status.md)
- [Vereisten voor importeren/exporteren bekijken](storage-import-export-requirements.md)
