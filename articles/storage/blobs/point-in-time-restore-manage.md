---
title: Herstel naar een bepaald tijdstip uitvoeren op blok-BLOB-gegevens
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van herstel naar een bepaald tijdstip om een set blok-blobs naar de vorige status te herstellen op een gegeven moment.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 7bd85c60025475e8208847a12ccc2729743a975a
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803915"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Herstel naar een bepaald tijdstip uitvoeren op blok-BLOB-gegevens

U kunt herstel punt in tijd gebruiken om een of meer sets blok-blobs naar een eerdere status te herstellen. In dit artikel wordt beschreven hoe u herstel naar een bepaald tijdstip inschakelt voor een opslag account en hoe u een herstel bewerking uitvoert.

Zie herstel naar een bepaald tijdstip [voor blok-blobs voor](point-in-time-restore-overview.md)meer informatie over herstel naar een bepaald tijdstip.

> [!CAUTION]
> Herstel naar een bepaald tijdstip biedt alleen ondersteuning voor het herstellen van bewerkingen op blok-blobs. Bewerkingen op containers kunnen niet worden hersteld. Als u een container uit het opslag account verwijdert door de bewerking voor het verwijderen van een [container](/rest/api/storageservices/delete-container) aan te roepen, kan deze container niet worden hersteld met een herstel bewerking. In plaats van een volledige container te verwijderen, moet u afzonderlijke blobs verwijderen als u deze mogelijk later wilt herstellen.

## <a name="enable-and-configure-point-in-time-restore"></a>Herstel naar een bepaald tijdstip inschakelen en configureren

Voordat u herstel naar een bepaald tijdstip inschakelt en configureert, moet u de vereisten voor het opslag account inschakelen: voorlopig verwijderen, feed wijzigen en BLOB-versie beheer. Raadpleeg de volgende artikelen voor meer informatie over het inschakelen van elk van deze functies:

- [Voorlopig verwijderen inschakelen voor blobs](./soft-delete-blob-enable.md)
- [De wijzigings feed in-en uitschakelen](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)

> [!IMPORTANT]
> Het inschakelen van de functie voor voorlopig verwijderen, wijzigings invoer en BLOB-versie beheer kan leiden tot extra kosten. Zie voor meer informatie [voorlopig verwijderen voor blobs](soft-delete-blob-overview.md), [ondersteuning voor feeds wijzigen in Azure Blob Storage](storage-blob-change-feed.md)en [BLOB-versie beheer](versioning-overview.md).

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Voer de volgende stappen uit om herstel naar een bepaald tijdstip met de Azure Portal te configureren:

1. Ga in Azure Portal naar uw opslagaccount.
1. Kies onder **instellingen** de optie **gegevens beveiliging**.
1. Selecteer herstel naar een bepaald **tijdstip inschakelen** . Wanneer u deze optie selecteert, wordt voorlopig verwijderen voor blobs, versie beheer en wijzigings feed ook ingeschakeld.
1. Stel het maximale herstel punt voor herstel naar een bepaald tijdstip in dagen in. Dit aantal moet minstens één dag kleiner zijn dan de retentie periode die is opgegeven voor het voorlopig verwijderen van de blob.
1. Sla uw wijzigingen op.

In de volgende afbeelding ziet u een opslag account dat is geconfigureerd voor herstel naar een bepaald tijdstip met een herstel punt van zeven dagen geleden en een Bewaar periode voor het zacht verwijderen van de BLOB 14 dagen.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Scherm afbeelding die laat zien hoe u herstel naar een bepaald tijdstip in de Azure Portal kunt configureren":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u herstel naar een bepaald tijdstip wilt configureren met Power shell, installeert u eerst de [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) -module versie 2.6.0 of hoger. Roep vervolgens de Enable-AzStorageBlobRestorePolicy opdracht om herstel naar een bepaald tijdstip voor het opslag account in te scha kelen.

In het volgende voor beeld wordt zacht verwijderen ingeschakeld en wordt de Bewaar periode voor de tijdelijke verwijdering ingesteld, wordt feed en versie beheer ingeschakeld en wordt vervolgens herstel naar een bepaald tijdstip mogelijk.    Wanneer u het voor beeld uitvoert, moet u de waarden tussen punt haken vervangen door uw eigen waarden:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
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

---

## <a name="perform-a-restore-operation"></a>Een herstel bewerking uitvoeren

Wanneer u een herstel bewerking uitvoert, moet u het herstel punt opgeven als een UTC-waarde voor **datum/tijd** . Containers en blobs worden op die dag en tijd hersteld naar hun status. Het kan enkele minuten duren voordat de herstel bewerking is voltooid.

U kunt alle containers in het opslag account herstellen of u kunt een bereik van blobs in een of meer containers herstellen. Een bereik van blobs is gedefinieerd lexicographically, wat betekent dat het om een woordenboek volgorde gaat. Er worden Maxi maal tien lexicographical-bereiken ondersteund per herstel bewerking. Het begin van het bereik is inclusief en het einde van het bereik is exclusief.

Het container patroon dat is opgegeven voor het begin bereik en het eind bereik moet mini maal drie tekens bevatten. De slash (/) die wordt gebruikt om een container naam te scheiden van een BLOB-naam, telt niet mee aan dit minimum.

Joker tekens worden niet ondersteund in een lexicographical-bereik. Joker tekens worden beschouwd als standaard tekens.

U kunt blobs in de `$root` containers herstellen `$web` door ze expliciet op te geven in een bereik dat is door gegeven aan een herstel bewerking. De `$root` `$web` containers en worden alleen hersteld als ze expliciet zijn opgegeven. Andere systeem containers kunnen niet worden hersteld.

Alleen blok-blobs worden hersteld. Pagina-blobs en toevoeg-blobs worden niet opgenomen in een herstel bewerking. Zie herstel naar een bepaald [tijdstip voor blok-blobs](point-in-time-restore-overview.md)voor meer informatie over de beperkingen met betrekking tot toevoeg-blobs.

> [!IMPORTANT]
> Wanneer u een herstel bewerking uitvoert, blokkeert Azure Storage gegevens bewerkingen op de blobs in de bereiken die worden teruggezet voor de duur van de bewerking. Lees-, schrijf-en verwijder bewerkingen worden geblokkeerd op de primaire locatie. Daarom kunnen bewerkingen, zoals het weer geven van containers in het Azure Portal, niet worden uitgevoerd zoals verwacht tijdens het terugzetten.
>
> Lees bewerkingen van de secundaire locatie kunnen door gaan tijdens de herstel bewerking als het opslag account geo-gerepliceerd is.
>
> De tijd die nodig is voor het herstellen van een set gegevens is gebaseerd op het aantal schrijf-en verwijder bewerkingen tijdens de herstel periode. Bijvoorbeeld: een account met 1.000.000 objecten met 3.000 objecten per dag en 1.000 objecten die per dag worden verwijderd, duurt ongeveer twee uur om terug te gaan naar een punt 30 dagen in het verleden. Een Bewaar periode en herstel van meer dan 90 dagen in het verleden worden niet aanbevolen voor een account met deze wijzigings factor.

### <a name="restore-all-containers-in-the-account"></a>Alle containers in het account herstellen

U kunt alle containers in het opslag account herstellen om ze te retour neren naar de vorige status op een bepaald moment.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Voer de volgende stappen uit om alle containers en blobs in het opslag account te herstellen met de Azure Portal:

1. Navigeer naar de lijst met containers voor uw opslag account.
1. Kies op de werk balk de optie **containers herstellen** en vervolgens **alle herstellen**.
1. Geef in het deel venster **alle containers herstellen** het herstel punt op door een datum en tijd op te geven.
1. Bevestig dat u wilt door gaan door het selectie vakje in te scha kelen.
1. Selecteer **herstellen** om de herstel bewerking te starten.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Scherm afbeelding die laat zien hoe u alle containers herstelt naar een opgegeven herstel punt":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u alle containers en blobs in het opslag account met Power shell wilt herstellen, roept u de opdracht **Restore-AzStorageBlobRange** aan. Standaard wordt de opdracht **Restore-AzStorageBlobRange** asynchroon uitgevoerd en wordt een object van het type **PSBlobRestoreStatus** geretourneerd dat u kunt gebruiken om de status van de herstel bewerking te controleren.

In het volgende voor beeld worden de containers in het opslag account asynchroon teruggezet naar de status 12 uur vóór het huidige moment en worden enkele eigenschappen van de herstel bewerking gecontroleerd:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Als u de herstel bewerking synchroon wilt uitvoeren, neemt u de para meter **-WaitForComplete** op in de opdracht. Wanneer de para meter **-WaitForComplete** aanwezig is, wordt er een bericht weer gegeven met de herstel-id voor de bewerking en worden de bewerkingen vervolgens geblokkeerd totdat de herstel bewerking is voltooid. Houd er rekening mee dat de tijd die nodig is voor een herstel bewerking afhankelijk is van de hoeveelheid gegevens die moet worden hersteld en een grote herstel bewerking kan tot een uur duren.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

---

### <a name="restore-ranges-of-block-blobs"></a>Bereiken van blok-blobs herstellen

U kunt een of meer lexicographical-bereiken van blobs binnen één container of meerdere containers herstellen om deze blobs op een bepaald moment in hun vorige status te retour neren.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Voer de volgende stappen uit om een reeks blobs in een of meer containers te herstellen met de Azure Portal:

1. Navigeer naar de lijst met containers voor uw opslag account.
1. Selecteer de container of containers die u wilt herstellen.
1. Kies op de werk balk de optie **containers herstellen** en vervolgens **herstellen geselecteerd**.
1. Geef in het deel venster **geselecteerde containers herstellen** het herstel punt op door een datum en tijd op te geven.
1. Geef de bereiken op die u wilt herstellen. Gebruik een slash (/) om de container naam te afbakenen vanuit het voor voegsel van de blob.
1. Standaard bevat het deel venster **geselecteerde containers herstellen** een bereik dat alle blobs in de container bevat. Verwijder dit bereik als u de volledige container niet wilt herstellen. Het standaard bereik wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Scherm afbeelding met het standaard-BLOB-bereik dat moet worden verwijderd voordat het aangepaste bereik wordt opgegeven":::

1. Bevestig dat u wilt door gaan door het selectie vakje in te scha kelen.
1. Selecteer **herstellen** om de herstel bewerking te starten.

In de volgende afbeelding ziet u een herstel bewerking voor een set bereiken.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Scherm afbeelding die laat zien hoe u de bereiken van blobs in een of meer containers kunt herstellen":::

De herstel bewerking die wordt weer gegeven in de afbeelding, voert de volgende acties uit:

- Hiermee herstelt u de volledige inhoud van *container1*.
- Hiermee herstelt u blobs in het lexicographical bereik *blob1* via *blob5* in *container2*. Dit bereik herstelt blobs met namen als *blob1*, *blob11*, *blob100*, *blob2*, enzovoort. Omdat het einde van het bereik exclusief is, herstelt de blobs waarvan de naam begint met *blob4*, maar herstelt geen blobs waarvan de naam begint met *blob5*.
- Herstelt alle blobs in *container3* en *container4*. Omdat het einde van het bereik exclusief is, herstelt dit bereik niet *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u één bereik van blobs wilt herstellen, roept u de opdracht **Restore-AzStorageBlobRange** aan en geeft u een lexicographical-bereik van container-en BLOB-namen op voor de `-BlobRestoreRange` para meter. Als u bijvoorbeeld de blobs in één container met de naam *container1* wilt herstellen, kunt u een bereik opgeven dat begint met *container1* en eindigt met *container2*. Er zijn geen vereisten voor de containers met de naam in het begin-en eind bereik. Omdat het einde van het bereik exclusief is, zelfs als het opslag account een container met de naam *container2* bevat, wordt alleen de container met de naam *container1* hersteld:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Als u een subset van blobs in een container wilt herstellen, gebruikt u een slash (/) om de container naam van het voor voegsel van de BLOB te scheiden. Het volgende bereik selecteert bijvoorbeeld blobs in één container waarvan de naam begint met de letters *d* t/m *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Geef vervolgens het bereik op voor de opdracht **Restore-AzStorageBlobRange** . Geef het herstel punt op door een UTC- **datum** waarde voor de para meter op te geven `-TimeToRestore` . In het volgende voor beeld worden de blobs in het opgegeven bereik tot de status 3 dagen vóór het huidige moment hersteld:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Standaard wordt de opdracht **Restore-AzStorageBlobRange** asynchroon uitgevoerd. Wanneer u een herstel bewerking asynchroon initieert, wordt in Power shell direct een tabel met eigenschappen voor de bewerking weer gegeven:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Als u meerdere bereiken van blok-blobs wilt herstellen, geeft u een matrix met bereiken op voor de `-BlobRestoreRange` para meter. In het volgende voor beeld worden twee bereiken opgegeven voor het herstellen van de volledige inhoud van *container1* en *container4* naar de status 24 uur geleden, en wordt het resultaat opgeslagen in een variabele:

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

Als u de herstel bewerking synchroon wilt uitvoeren en de uitvoering wilt blok keren totdat deze is voltooid, neemt u de para meter **-WaitForComplete** op in de opdracht.

---

## <a name="next-steps"></a>Volgende stappen

- [Herstel naar een bepaald tijdstip voor blok-blobs](point-in-time-restore-overview.md)
- [Voorlopig verwijderen](./soft-delete-blob-overview.md)
- [Feed wijzigen](storage-blob-change-feed.md)
- [BLOB-versie beheer](versioning-overview.md)