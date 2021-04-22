---
title: Herstel naar een bepaald tijdstip uitvoeren op blok-blobgegevens
titleSuffix: Azure Storage
description: Leer hoe u herstel naar een bepaald tijdstip kunt gebruiken om een set blok-blobs op een bepaald moment te herstellen naar de vorige status.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e8c926c2fbc5b19f67fb78d321ee3293c73be939
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869343"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Herstel naar een bepaald tijdstip uitvoeren op blok-blobgegevens

U kunt herstel naar een bepaald tijdstip gebruiken om een of meer sets blok-blobs te herstellen naar een eerdere status. In dit artikel wordt beschreven hoe u herstel naar een bepaald tijdstip kunt inschakelen voor een opslagaccount en hoe u een herstelbewerking kunt uitvoeren.

Zie Herstel naar een bepaald tijdstip voor blok-blobs voor meer informatie over herstel naar een [bepaald tijdstip.](point-in-time-restore-overview.md)

> [!CAUTION]
> Herstel naar een bepaald tijdstip ondersteunt alleen herstelbewerkingen op blok-blobs. Bewerkingen op containers kunnen niet worden hersteld. Als u een container uit het opslagaccount verwijdert door de bewerking [Container](/rest/api/storageservices/delete-container) verwijderen aan te roepen, kan die container niet worden hersteld met een herstelbewerking. In plaats van een hele container te verwijderen, verwijdert u afzonderlijke blobs als u ze later wilt herstellen. Microsoft raadt ook aan om voor containers en blobs een zachte verwijdering in te stellen ter bescherming tegen onbedoelde verwijdering. Zie Voor meer informatie [Soft delete for containers (preview) (Soft delete voor containers (preview)](soft-delete-container-overview.md) en Soft delete for [blobs (Soft delete voor blobs).](soft-delete-blob-overview.md)

## <a name="enable-and-configure-point-in-time-restore"></a>Herstel naar een bepaald tijdstip inschakelen en configureren

Voordat u herstel naar een bepaald tijdstip inschakelen en configureren, moet u de vereisten voor het opslagaccount inschakelen: soft delete, change feed en blob versioning. Zie de volgende artikelen voor meer informatie over het inschakelen van elk van deze functies:

- [Voorlopig verwijderen inschakelen voor blobs](./soft-delete-blob-enable.md)
- [De wijzigingsfeed in- en uitschakelen](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Blobversies inschakelen en beheren](versioning-enable.md)

> [!IMPORTANT]
> Het inschakelen van de functies voor zacht verwijderen, het wijzigen van de feed en het versien van blobs kan leiden tot extra kosten. Zie Voor meer informatie Soft [delete for blobs](soft-delete-blob-overview.md) [,Change feed support in Azure Blob Storage](storage-blob-change-feed.md)en Blob [versioning](versioning-overview.md).

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u herstel naar een bepaald tijdstip wilt configureren met de Azure Portal, volgt u deze stappen:

1. Ga in Azure Portal naar uw opslagaccount.
1. Kies **onder Instellingen** de optie **Gegevensbeveiliging.**
1. Selecteer **Herstel naar een bepaald tijdstip in- of** uitzetten. Wanneer u deze optie selecteert, worden ook de functie voor het verwijderen van blobs, versieversies en de wijzigingsfeed ingeschakeld.
1. Stel het maximum herstelpunt voor herstel naar een bepaald tijdstip in dagen in. Dit aantal moet ten minste één dag minder zijn dan de retentieperiode die is opgegeven voor het verwijderen van blobs.
1. Sla uw wijzigingen op.

In de volgende afbeelding ziet u een opslagaccount dat is geconfigureerd voor herstel naar een bepaald tijdstip met een herstelpunt van zeven dagen geleden en een retentieperiode voor het verwijderen van blobs van 14 dagen.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Schermopname die laat zien hoe u herstel naar een bepaald tijdstip configureert in de Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u herstel naar een bepaald tijdstip wilt configureren met PowerShell, installeert u eerst de [Az.Storage-module](https://www.powershellgallery.com/packages/Az.Storage) versie 2.6.0 of hoger. Roep vervolgens de [opdracht Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) aan om herstel naar een bepaald tijdstip in te stellen voor het opslagaccount.

In het volgende voorbeeld wordt soft delete mogelijk gemaakt en wordt de retentieperiode voor soft-delete inschakelen, de wijzigingsfeed en versiebeleid inschakelen en vervolgens herstel naar een bepaald tijdstip mogelijk gemaakt. Vergeet niet om bij het uitvoeren van het voorbeeld de waarden tussen vierkante haken te vervangen door uw eigen waarden:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u herstel naar een bepaald tijdstip wilt configureren met Azure CLI, installeert u eerst Azure CLI versie 2.2.0 of hoger. Roep vervolgens de [opdracht az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) aan om herstel naar een bepaald tijdstip en de andere vereiste instellingen voor gegevensbeveiliging voor het opslagaccount in teschakelen.

In het volgende voorbeeld wordt het gebruik van 'soft delete' mogelijk gemaakt en wordt de retentieperiode voor soft-delete op 14 dagen, het wijzigen van de feed en versieherstel mogelijk gemaakt en herstel naar een bepaald tijdstip met een herstelperiode van zeven dagen. Vergeet niet om bij het uitvoeren van het voorbeeld de waarden tussen vierkante haken te vervangen door uw eigen waarden:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>Een herstelpunt kiezen

Het herstelpunt is de datum en tijd waarop de gegevens worden hersteld. Azure Storage gebruikt altijd een UTC-datum/tijd-waarde als herstelpunt. Met de Azure Portal kunt u echter het herstelpunt in de lokale tijd opgeven en deze datum/tijd-waarde vervolgens converteren naar een UTC-datum/tijd-waarde om de herstelbewerking uit te voeren.

Wanneer u een herstelbewerking met PowerShell of Azure CLI wilt uitvoeren, moet u het herstelpunt opgeven als een UTC-datum/tijd-waarde. Als het herstelpunt is opgegeven met een lokale tijdwaarde in plaats van een UTC-tijdwaarde, werkt de herstelbewerking in sommige gevallen mogelijk nog steeds zoals verwacht. Als uw lokale tijd bijvoorbeeld UTC min vijf uur is, resulteert het opgeven van een lokale tijdwaarde in een herstelpunt dat vijf uur eerder is dan de waarde die u hebt opgegeven. Als er geen wijzigingen zijn aangebracht in de gegevens in het bereik dat in die periode van vijf uur moet worden hersteld, levert de herstelbewerking dezelfde resultaten op, ongeacht de tijdswaarde die is opgegeven. Het wordt aanbevolen om een UTC-tijd voor het herstelpunt op te geven om onverwachte resultaten te voorkomen.

## <a name="perform-a-restore-operation"></a>Een herstelbewerking uitvoeren

U kunt alle containers in het opslagaccount herstellen of u kunt een bereik van blobs in een of meer containers herstellen. Een bereik van blobs wordt in woordenlijst volgorde gedefinieerd. Per herstelbewerking worden maximaal tien woordenbereiken ondersteund. Het begin van het bereik is inclusief en het einde van het bereik is exclusief.

Het containerpatroon dat is opgegeven voor het beginbereik en het eindbereik moeten minimaal drie tekens bevatten. De slash (/) die wordt gebruikt om een containernaam te scheiden van een blobnaam, telt niet mee voor dit minimum.

Jokertekens worden niet ondersteund in een holografische bereik. Jokertekens worden behandeld als standaardtekens.

U kunt blobs in de containers en herstellen door ze expliciet op te geven in een `$root` bereik dat is doorgegeven aan een `$web` herstelbewerking. De `$root` `$web` containers en worden alleen hersteld als ze expliciet zijn opgegeven. Andere systeemcontainers kunnen niet worden hersteld.

Alleen blok-blobs worden hersteld. Pagina-blobs en toevoegende blobs zijn niet opgenomen in een herstelbewerking. Zie Herstel naar een bepaald tijdstip voor blok-blobs voor meer informatie over beperkingen met betrekking tot [app-blobs.](point-in-time-restore-overview.md)

> [!IMPORTANT]
> Wanneer u een herstelbewerking hebt uitgevoerd, Azure Storage gegevensbewerkingen op de blobs in de bereiks die worden hersteld voor de duur van de bewerking. Lees-, schrijf- en verwijderbewerkingen worden geblokkeerd op de primaire locatie. Daarom werken bewerkingen zoals het in de lijst Azure Portal mogelijk niet zoals verwacht terwijl de herstelbewerking wordt uitgevoerd.
>
> Leesbewerkingen vanaf de secundaire locatie kunnen worden uitgevoerd tijdens de herstelbewerking als het opslagaccount geo-gerepliceerd is.
>
> De tijd die nodig is om een set gegevens te herstellen, is gebaseerd op het aantal schrijf- en verwijderbewerkingen dat is uitgevoerd tijdens de herstelperiode. Een account met één miljoen objecten met 3000 objecten toegevoegd per dag en 1000 objecten die per dag worden verwijderd, heeft bijvoorbeeld ongeveer twee uur nodig om te herstellen naar een punt van 30 dagen in het verleden. Een bewaarperiode en herstel van meer dan 90 dagen in het verleden wordt niet aanbevolen voor een account met deze wijzigingssnelheid.

### <a name="restore-all-containers-in-the-account"></a>Alle containers in het account herstellen

U kunt alle containers in het opslagaccount herstellen om ze op een bepaald moment terug te keren naar de vorige status.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u alle containers en blobs in het opslagaccount wilt herstellen met de Azure Portal, volgt u deze stappen:

1. Navigeer naar de lijst met containers voor uw opslagaccount.
1. Kies op de werkbalk **Containers herstellen en** vervolgens Alle **herstellen.**
1. Geef in **het deelvenster Alle containers** herstellen het herstelpunt op door een datum en tijd op te geven.
1. Bevestig dat u wilt doorgaan door het selectievakje in te checken.
1. Selecteer **Herstellen om** de herstelbewerking te starten.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Schermopname die laat zien hoe u alle containers herstelt naar een opgegeven herstelpunt":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u alle containers en blobs in het opslagaccount wilt herstellen met PowerShell, roept u de opdracht **Restore-AzStorageBlobRange** aan en geeft u het herstelpunt op als een UTC-datum/tijd-waarde. De opdracht **Restore-AzStorageBlobRange** wordt standaard asynchroon uitgevoerd en retourneert een object van het type **PSBlobRestoreStatus** dat u kunt gebruiken om de status van de herstelbewerking te controleren.

In het volgende voorbeeld worden containers in het opslagaccount 12 uur vóór het huidige moment asynchroon hersteld naar hun status en worden enkele eigenschappen van de herstelbewerking gecontroleerd:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Als u de herstelbewerking synchroon wilt uitvoeren, moet u de parameter **-WaitForComplete opnemen** in de opdracht . Wanneer de **parameter -WaitForComplete** aanwezig is, wordt in PowerShell een bericht weergegeven dat de herstel-id voor de bewerking bevat en vervolgens blokkeert bij uitvoering totdat de herstelbewerking is voltooid. Houd er rekening mee dat de tijd die nodig is voor een herstelbewerking afhankelijk is van de hoeveelheid gegevens die moet worden hersteld en dat een grote herstelbewerking maximaal een uur kan duren.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u alle containers en blobs in het opslagaccount wilt herstellen met Azure CLI, roept u de opdracht [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) aan en geeft u het herstelpunt op als een UTC-datum/tijd-waarde.

In het volgende voorbeeld worden alle containers in het opslagaccount asynchroon hersteld naar hun status 12 uur vóór een opgegeven datum en tijd. Als u de status van de herstelbewerking wilt controleren, roept u [az storage account show aan:](/cli/azure/storage/account#az_storage_account_show)

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Als u de eigenschappen van een herstelbewerking wilt controleren, roept u [az storage account show aan](/cli/azure/storage/account#az_storage_account_show) en vouwt u de eigenschap **blobRestoreStatus** uit. In het volgende voorbeeld ziet u hoe u de **eigenschap status controleert.**

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Als u de **opdracht az storage blob restore** synchroon wilt uitvoeren en de uitvoering wilt blokkeren totdat de herstelbewerking is voltooid, laat u de parameter `--no-wait` weg.

---

### <a name="restore-ranges-of-block-blobs"></a>Bereik van blok-blobs herstellen

U kunt een of meer lexografische blobbereiken herstellen binnen één container of tussen meerdere containers om deze blobs op een bepaald moment naar de vorige status te retourneren.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u een bereik van blobs in een of meer containers wilt herstellen met de Azure Portal, volgt u deze stappen:

1. Navigeer naar de lijst met containers voor uw opslagaccount.
1. Selecteer de container of containers die u wilt herstellen.
1. Kies op de werkbalk Containers **herstellen en** selecteer **vervolgens Herstellen.**
1. Geef in **het deelvenster Geselecteerde containers** herstellen het herstelpunt op door een datum en tijd op te geven.
1. Geef de te herstellen bereik op. Gebruik een slash (/) om de containernaam af te bakenen van het blob-voorvoegsel.
1. In het deelvenster **Geselecteerde containers herstellen** wordt standaard een bereik opgegeven dat alle blobs in de container bevat. Verwijder dit bereik als u de hele container niet wilt herstellen. Het standaardbereik wordt weergegeven in de volgende afbeelding.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Schermopname van het standaardblobbereik dat moet worden verwijderd voordat u een aangepast bereik opgeeft":::

1. Bevestig dat u wilt doorgaan door het selectievakje in te checken.
1. Selecteer **Herstellen om** de herstelbewerking te starten.

In de volgende afbeelding ziet u een herstelbewerking voor een reeks reeksen.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Schermopname die laat zien hoe u blobbereiken in een of meer containers kunt herstellen":::

Met de herstelbewerking die in de afbeelding wordt weergegeven, worden de volgende acties uitgevoerd:

- Herstelt de volledige inhoud van *container1*.
- Herstelt blobs in het holografische bereik *blob1* tot *en met blob5* in *container2*. Dit bereik herstelt blobs met namen zoals *blob1,* *blob11,* *blob100,* *blob2,* en meer. Omdat het einde van het bereik exclusief is, worden blobs hersteld waarvan de namen beginnen met *blob4,* maar worden blobs waarvan de namen beginnen met blob5 niet *hersteld.*
- Herstelt alle blobs in *container3* en *container4.* Omdat het einde van het bereik exclusief is, wordt container5 niet door dit bereik *hersteld.*

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u één bereik met blobs wilt herstellen, roept u de opdracht **Restore-AzStorageBlobRange** aan en geeft u een holografische reeks container- en blobnamen op voor de `-BlobRestoreRange` parameter. Als u bijvoorbeeld de blobs in één container met de naam *container1* wilt herstellen, kunt u een bereik opgeven dat begint met *container1* en eindigt met *container2.* Het is niet vereist dat de containers met de naam in de begin- en eindbereiken bestaan. Omdat het einde van het bereik exclusief is, wordt alleen de container met de naam *container1* hersteld, zelfs als het opslagaccount een container met de naam *container2* bevat:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Als u een subset blobs in een container wilt opgeven die u wilt herstellen, gebruikt u een slash (/) om de containernaam te scheiden van het blob-voorvoegselpatroon. In het volgende bereik worden bijvoorbeeld blobs in één container geselecteerd waarvan de naam met de letters *d tot en met* f *begint:*

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Geef vervolgens het bereik op voor de **opdracht Restore-AzStorageBlobRange.** Geef het herstelpunt op door een **UTC-datum/tijd-waarde** op te geven voor de `-TimeToRestore` parameter . In het volgende voorbeeld worden blobs in het opgegeven bereik 3 dagen vóór het huidige moment hersteld naar hun status:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Standaard wordt **de opdracht Restore-AzStorageBlobRange** asynchroon uitgevoerd. Wanneer u een herstelbewerking asynchroon start, wordt in PowerShell onmiddellijk een tabel met eigenschappen voor de bewerking weergegeven:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Als u meerdere reeksen blok-blobs wilt herstellen, geeft u een matrix met bereik voor de `-BlobRestoreRange` parameter op. In het volgende voorbeeld worden twee bereiken opgegeven om de volledige inhoud van *container1* en *container4* 24 uur geleden te herstellen naar hun status en wordt het resultaat op een variabele op slaat:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Als u de herstelbewerking synchroon wilt uitvoeren en de uitvoering wilt blokkeren totdat deze is voltooid, moet u de parameter **-WaitForComplete opnemen** in de opdracht .

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een bereik van blobs wilt herstellen, roept u de [opdracht az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) aan en geeft u een holografische reeks container- en blobnamen op voor de parameter `--blob-range` . Als u meerdere bereik wilt opgeven, geeft u `--blob-range` de parameter op voor elk afzonderlijk bereik.

Als u bijvoorbeeld de blobs in één container met de naam *container1* wilt herstellen, kunt u een bereik opgeven dat begint met *container1* en eindigt met *container2.* Er is geen vereiste dat de containers met de naam in de begin- en eindbereiken bestaan. Omdat het einde van het bereik exclusief is, zelfs als het opslagaccount een container met de naam *container2* bevat, wordt alleen de container met de naam *container1* hersteld.

Als u een subset blobs in een container wilt herstellen, gebruikt u een slash (/) om de containernaam te scheiden van het blob-voorvoegselpatroon. In het onderstaande voorbeeld wordt asynchroon een bereik van blobs in een container hersteld waarvan de namen met de letters `d` beginnen via `f` .

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Als u de **opdracht az storage blob restore** synchroon wilt uitvoeren en de uitvoering wilt blokkeren totdat de herstelbewerking is voltooid, laat u de parameter `--no-wait` weg.

---

## <a name="next-steps"></a>Volgende stappen

- [Herstel naar een bepaald tijdstip voor blok-blobs](point-in-time-restore-overview.md)
- [Soft delete](./soft-delete-blob-overview.md)
- [Feed wijzigen](storage-blob-change-feed.md)
- [Blobversies](versioning-overview.md)