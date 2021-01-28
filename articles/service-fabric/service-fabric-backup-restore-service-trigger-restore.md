---
title: Back-up herstellen in azure Service Fabric
description: Gebruik de functie periodieke back-up en herstel in Service Fabric voor het herstellen van gegevens uit een back-up van uw toepassings gegevens.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 8566d82ef0d91caff47ff17a9cb12fcdc8241884
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928014"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Back-up herstellen in azure Service Fabric

In azure Service Fabric kunnen betrouw bare stateful Services en Reliable Actors een onveranderlijke, gezaghebbende status onderhouden nadat een aanvraag-en respons transactie is voltooid. Een stateful service kan enige tijd in beslag nemen of gegevens verliezen vanwege een nood geval. Als dat gebeurt, moet de service worden hersteld vanuit de meest recente acceptabele back-up zodat deze kan blijven werken.

U kunt bijvoorbeeld een service configureren om een back-up te maken van de gegevens die u wilt beveiligen tegen de volgende scenario's:

- **Herstel na nood geval**: permanent verlies van een volledig service Fabric cluster.
- Het **geval van gegevens verlies**: permanent verlies van een meerderheid van de replica's van een service partitie.
- Het **geval van gegevens verlies**: onopzettelijke verwijdering of beschadiging van de service. Een beheerder kan bijvoorbeeld de service onjuist verwijderen.
- **Geval van beschadigde gegevens**: fouten in de service veroorzaken beschadiging van gegevens. Gegevens beschadiging kan bijvoorbeeld optreden wanneer een service code-upgrade fout gegevens naar een betrouw bare verzameling schrijft. In dat geval moet u mogelijk zowel de code als de gegevens naar een eerdere status herstellen.

## <a name="prerequisites"></a>Vereisten

- Als u een herstel bewerking wilt starten, moet de _fout Analysis Service (FAS)_ zijn ingeschakeld voor het cluster.
- De back-up van de _service Backup Restore (BRS)_ is gemaakt.
- Het terugzetten kan alleen worden geactiveerd op een partitie.
- Installeer de micro soft. ServiceFabric. Power shell. http-module (preview) om configuratie aanroepen te maken.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Als uw PowerShellGet-versie lager is dan 1.6.0, moet u bijwerken om ondersteuning toe te voegen voor de vlag *-AllowPrerelease* :
>
> `Install-Module -Name PowerShellGet -Force`

- Zorg ervoor dat het cluster is verbonden met behulp van de `Connect-SFCluster` opdracht voordat u een configuratie aanvraag maakt met behulp van de module micro soft. ServiceFabric. Power shell. http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Geactiveerde herstel bewerking

Een herstel bewerking kan worden geactiveerd voor een van de volgende scenario's:

- Gegevens herstellen voor herstel _na nood gevallen_.
- Gegevens herstellen voor _gegevens beschadiging/gegevens verlies_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Gegevens herstellen in het geval van nood herstel

Als een volledig Service Fabric cluster verloren is gegaan, kunt u de gegevens herstellen voor de partities van de betrouw bare stateful service en Reliable Actors. De gewenste back-up kan worden geselecteerd in de lijst wanneer u [GetBackupAPI gebruikt met details van back-upopslag](/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). De inventarisatie van de back-up kan zijn voor een toepassing, service of partitie.

In het volgende voor beeld wordt ervan uitgegaan dat het verloren cluster hetzelfde cluster is als waarnaar wordt verwezen in het [inschakelen van periodieke back-ups voor betrouw bare stateful service en reliable actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). In dit geval `SampleApp` wordt het back-upbeleid ingeschakeld en worden de back-ups geconfigureerd om Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

Voer een Power shell-script uit om de REST API te gebruiken om een lijst te retour neren met de back-ups die zijn gemaakt voor alle partities in de `SampleApp` toepassing. De API vereist de back-upopslaggegevens om de beschik bare back-ups weer te geven.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Voorbeeld uitvoer voor de bovenstaande uitvoering:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Kies een van de back-ups om de herstel bewerking te activeren. De huidige back-up voor herstel na nood gevallen kan bijvoorbeeld de volgende back-up zijn:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Voor de Restore API moet u de _BackupId_ -en _sleutel backuplocation_ -gegevens opgeven.

U moet ook een doel partitie in het alternatieve cluster kiezen, zoals wordt beschreven in het [partitie schema](service-fabric-concepts-partitioning.md#get-started-with-partitioning). De alternatieve cluster back-up wordt teruggezet naar de partitie die is opgegeven in het partitie schema van het oorspronkelijke verloren cluster.

Als de partitie-ID op het alternatieve cluster is `1c42c47f-439e-4e09-98b9-88b8f60800c6` , kunt u deze toewijzen aan de oorspronkelijke cluster partitie-id `974bd92a-b395-4631-8a7f-53bd4ae9cf22` door de hoge sleutel en de lage sleutel te vergelijken voor _partitioneren met een bereik (UniformInt64Partition)_.

Bij _benoemde partitionering_ wordt de waarde name vergeleken om de doel partitie in een alternatief cluster te identificeren.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

U vraagt de herstel bewerking af op de partitie van het back-upcluster met behulp van de volgende [Restore API](/rest/api/servicefabric/sfclient-api-restorepartition):

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

U kunt de voortgang van een herstel met TrackRestoreProgress volgen.

> [!NOTE]
> Wanneer u Power shell gebruikt om een partitie te herstellen, als sleutel backuplocation ' $ ' heeft, kunt u deze gebruiken met ' ~ '
>

### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken
U kunt een terugzet bewerking activeren vanuit Service Fabric Explorer. Zorg ervoor dat de geavanceerde modus is ingeschakeld in Service Fabric Explorer instellingen.
1. Selecteer de gewenste partities en klik op acties. 
2. Selecteer trigger Partition herstellen en vul de gegevens voor Azure in:

    ![Partitie herstel activeren][2]

    of file share:

    ![Trigger partitie herstellen bestands share][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Gegevens herstel voor gegevens _beschadiging_ / _gegevens verlies_

Voor _gegevens verlies_ of _beschadiging van gegevens_, kunnen back-ups van partities voor betrouw bare stateful Services en reliable actors partities worden hersteld naar een van de gekozen back-ups.

Het volgende voor beeld is een voortzetting van het [inschakelen van periodieke back-ups voor betrouw bare stateful service en reliable actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). In dit voor beeld is een back-upbeleid voor de partitie ingeschakeld en maakt de service back-ups op een gewenste frequentie in Azure Storage.

Selecteer een back-up in de uitvoer van  [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). In dit scenario wordt de back-up van hetzelfde cluster als voorheen gegenereerd.

Als u de herstel bewerking wilt starten, kiest u een back-up in de lijst. Voor de gegevens beschadiging van het huidige _gegevens verlies_ / selecteert u de volgende back-up:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Geef voor de Restore-API de _BackupId_ -en _sleutel backuplocation_ -Details op. De back-up van het cluster is ingeschakeld, zodat de Service Fabric _Backup Restore-service (BRS)_ de juiste opslag locatie van het bijbehorende back-upbeleid identificeert.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

U kunt de voortgang van het terugzetten volgen met behulp van TrackRestoreProgress.

> [!NOTE]
> Wanneer u Power shell gebruikt om een partitie te herstellen, als sleutel backuplocation ' $ ' heeft, kunt u deze gebruiken met ' ~ '
>

## <a name="track-restore-progress"></a>Voortgang van herstellen bijhouden

Een partitie van een betrouw bare stateful service of een betrouw bare actor accepteert slechts één herstel aanvraag per keer. Een partitie accepteert alleen nog een aanvraag nadat de huidige herstel aanvraag is voltooid. Meerdere herstel aanvragen kunnen tegelijkertijd worden geactiveerd op verschillende partities.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

De herstel aanvraag vordert in de volgende volg orde:

1. **Geaccepteerd**: een _geaccepteerde_ herstel status geeft aan dat de aangevraagde partitie is geactiveerd met de juiste aanvraag parameters.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: de status van de herstel bewerking wordt aangegeven dat er een herstel bewerking wordt _uitgevoerd_ in de partitie met de back-up die in de aanvraag wordt vermeld. De partitie rapporteert de status _dataloss_ .
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Geslaagd**, **mislukt** of **time-out**: een aangevraagde herstel bewerking kan in een van de volgende statussen worden uitgevoerd. Elke status heeft de volgende betekenis-en reactie Details:
    - **Geslaagd**: de herstel status van een _geslaagd_ geeft aan dat de status van de partitie opnieuw is verkregen. De partitie rapporteert _RestoredEpoch_ -en _RestoredLSN_ -statussen samen met de tijd in UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Fout**: een herstel status _mislukt_ geeft aan dat de herstel aanvraag niet kan worden uitgevoerd. De oorzaak van de fout wordt gerapporteerd.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Time-out**: een herstel status _time-out_ geeft aan dat de aanvraag een time-out heeft. Maak een nieuwe herstel aanvraag met een grotere [RestoreTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). De standaard time-out is 10 minuten. Zorg ervoor dat de partitie geen gegevens verlies status heeft voordat u de herstel bewerking opnieuw aanvraagt.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatisch herstellen

U kunt betrouw bare stateful service-en Reliable Actors partities configureren in het Service Fabric-cluster voor _automatisch herstel_. Stel in het back-upbeleid in `AutoRestore` op _waar_. Als u _automatisch terugzetten_ inschakelt, worden gegevens automatisch teruggezet vanaf de nieuwste partitie back-up wanneer gegevens verlies wordt gerapporteerd. Zie voor meer informatie:

- [Activering automatisch herstellen in back-upbeleid](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [RestorePartition API-naslag informatie](/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API-naslag informatie](/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Volgende stappen
- [Inzicht in periodieke back-upconfiguratie](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Naslag informatie over back-up terugzetten REST API](/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png
