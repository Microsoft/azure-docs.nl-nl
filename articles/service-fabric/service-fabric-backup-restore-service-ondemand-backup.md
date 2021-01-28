---
title: Back-ups op aanvraag in azure Service Fabric
description: Gebruik de functie voor het maken en terugzetten van back-ups in Service Fabric om een back-up te maken van uw toepassings gegevens.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d7986c8cd8d0714215c7b4dc57170be346e627ed
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928035"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Back-ups op aanvraag in azure Service Fabric

U kunt back-ups maken van gegevens van betrouw bare stateful Services en Reliable Actors om scenario's met betrekking tot nood gevallen of gegevens verlies op te lossen.

Azure Service Fabric heeft functies voor [periodieke back-ups van gegevens](service-fabric-backuprestoreservice-quickstart-azurecluster.md) en de back-up van gegevens op basis van behoefte. Back-ups op aanvraag is handig omdat deze bescherming biedt tegen gegevens _verlies_ / _gegevens beschadiging_ vanwege geplande wijzigingen in de onderliggende service of in de omgeving.

De functies van de back-up op aanvraag zijn handig voor het vastleggen van de status van de Services voordat u de bewerking van een service of service omgeving hand matig start. Als u bijvoorbeeld een wijziging in de binaire bestanden van de service aanbrengt tijdens het bijwerken of Down graden van de service. In een dergelijk geval kan back-ups op aanvraag de gegevens helpen beschermen tegen beschadiging door toepassings code fouten.
## <a name="prerequisites"></a>Vereisten

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


## <a name="triggering-on-demand-backup"></a>Back-ups op aanvraag activeren

Voor back-ups op aanvraag zijn opslag Details vereist voor het uploaden van back-upbestanden. U geeft de back-uplocatie op aanvraag op, hetzij in het periodieke back-upbeleid of in een back-upaanvraag op aanvraag.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Back-ups op aanvraag naar opslag die is opgegeven met een periodiek back-upbeleid

U kunt het periodieke back-upbeleid configureren voor het gebruik van een partitie van een betrouw bare stateful service of een betrouw bare Actor voor extra back-ups op aanvraag naar opslag.

De volgende situatie is de voortzetting van het scenario bij het [inschakelen van periodieke back-ups voor betrouw bare stateful service en reliable actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). In dit geval schakelt u een back-upbeleid in voor het gebruik van een partitie en wordt een back-up uitgevoerd op een ingestelde frequentie in Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

Gebruik de [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) -API om triggering in te stellen voor de back-up op aanvraag voor de partitie-id `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Gebruik de [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) -API om tracering in te scha kelen voor de [voortgang van de back-up op aanvraag](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Back-up op aanvraag naar opgegeven opslag

U kunt een back-up op aanvraag aanvragen voor een partitie van een betrouw bare stateful service of een betrouw bare actor. Geef de opslag informatie op als onderdeel van de back-upaanvraag op aanvraag.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

Gebruik de [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) -API om triggering in te stellen voor de back-up op aanvraag voor de partitie-id `974bd92a-b395-4631-8a7f-53bd4ae9cf22` . Neem de volgende Azure Storage informatie op:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

U kunt de [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) -API gebruiken om tracering in te stellen voor de [voortgang van de back-up op aanvraag](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken
Zorg ervoor dat de geavanceerde modus is ingeschakeld in Service Fabric Explorer instellingen.
1. Selecteer de gewenste partities en klik op acties. 
2. Selecteer trigger Partition backup en vul de gegevens voor Azure in:

    ![Partitie back-up activeren][0]

    of file share:

    ![File share partitie back-up activeren][1]

## <a name="tracking-on-demand-backup-progress"></a>Voortgang van het bijhouden van back-ups op aanvraag

Een partitie van een betrouw bare stateful service of een betrouw bare actor accepteert per keer slechts één back-upaanvraag op aanvraag. Een andere aanvraag kan pas worden geaccepteerd nadat de huidige back-upaanvraag op aanvraag is voltooid.

Verschillende partities kunnen op een later tijdstip back-ups op aanvraag activeren.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Aanvragen voor back-ups op aanvraag kunnen de volgende statussen hebben:

- **Geaccepteerd**: de back-up is gestart op de partitie en wordt uitgevoerd.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Geslaagd**, **mislukt** of **time-out**: een aangevraagde back-up op aanvraag kan worden voltooid met een van de volgende statussen:
  - **Geslaagd**: de _back-upstatus is geslaagd._ Hiermee wordt aangegeven dat er een back-up van de partitie status is gemaakt. Het antwoord geeft _BackupEpoch_ en _BackupLSN_ voor de partitie samen met de tijd in UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Fout**: een back-upstatus _mislukt_ geeft aan dat er een fout is opgetreden tijdens het maken van de back-up van de partitie status. De oorzaak van de fout wordt in antwoord gegeven.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Time-out**: de back-upstatus van een _time-out_ geeft aan dat de back-up van de partitie status in een bepaalde tijd niet kan worden gemaakt. De standaard time-outwaarde is 10 minuten. Initieer een nieuwe aanvraag voor back-ups op aanvraag met een grotere [BackupTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) in dit scenario.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over de configuratie van periodieke back-ups](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referentie voor BackupRestore-REST API](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png
