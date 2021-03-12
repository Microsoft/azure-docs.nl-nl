---
title: Periodic backup and restore in Azure Service Fabric (Periodieke back-up en herstel in Azure Service Fabric)
description: Gebruik de periodieke back-up-en herstel functie van Service Fabric voor het inschakelen van periodieke gegevens back-ups van uw toepassings gegevens.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: 42097b50277e78b3f0e8f5e61a2bf70cc08dbc02
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103198712"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Periodieke back-ups maken en herstellen in een Azure Service Fabric-cluster
> [!div class="op_single_selector"]
> * [Clusters op Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Zelfstandige clusters](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric is een platform voor gedistribueerde systemen waarmee u gemakkelijk betrouw bare, gedistribueerde, op micro Services gebaseerde Cloud toepassingen kunt ontwikkelen en beheren. Hiermee kan zowel stateless als stateful micro services worden uitgevoerd. Stateful Services kunnen de onveranderlijke, gezaghebbende status behouden naast de aanvraag en het antwoord of een volledige trans actie. Als een stateful service lange tijd inactief is of gegevens verliest als gevolg van een nood geval, moet deze mogelijk worden hersteld naar een recente back-up van de status om de service te kunnen voortzetten nadat er een back-up is gemaakt.

Service Fabric de status op meerdere knoop punten repliceert om ervoor te zorgen dat de service Maxi maal beschikbaar is. Zelfs als één knoop punt in het cluster uitvalt, blijft de service beschikbaar. In bepaalde gevallen is het echter toch wenselijk dat de service gegevens betrouwbaar zijn voor bredere storingen.
 
Zo kan de service bijvoorbeeld een back-up van de gegevens maken om te beschermen tegen de volgende scenario's:
- In het geval van een permanent verlies van een volledig Service Fabric cluster.
- Permanent verlies van een meerderheid van de replica's van een service partitie
- Administratieve fouten waarbij de status per ongeluk wordt verwijderd of beschadigd. Een beheerder met voldoende bevoegdheden kan bijvoorbeeld de service op een onjuiste wijze verwijderen.
- Fouten in de service die leiden tot beschadigde gegevens. Dit kan bijvoorbeeld gebeuren wanneer een service code-upgrade begint met het schrijven van fout gegevens naar een betrouw bare verzameling. In dat geval kunnen zowel de code als de gegevens worden teruggedraaid naar een eerdere status.
- Offline gegevens verwerking. Het kan handig zijn om gegevens offline te verwerken voor business intelligence die los van de service worden uitgevoerd waardoor de gegevens worden gegenereerd.

Service Fabric biedt een API die is ontworpen voor het maken van [back-ups en herstellen](service-fabric-reliable-services-backup-restore.md). Toepassings ontwikkelaars kunnen deze Api's gebruiken om regel matig een back-up te maken van de status van de service. Daarnaast moeten ontwikkel aars een back-up maken (en herstellen) als een API van de service om een back-up te kunnen maken van buiten de service op een bepaald moment, zoals voordat de toepassing wordt geüpgraded. Voor het onderhouden van de back-ups zijn hiervoor extra kosten van toepassing. U kunt bijvoorbeeld elk half uur vijf incrementele back-ups maken, gevolgd door een volledige back-up. Na de volledige back-up kunt u de vorige incrementele back-ups verwijderen. Deze aanpak vereist extra code voor extra kosten tijdens de ontwikkeling van toepassingen.

Met de service Backup en Restore in Service Fabric kunt u eenvoudig en automatisch back-ups maken van gegevens die zijn opgeslagen in stateful Services. Het maken van een back-up van toepassings gegevens op periodieke basis is essentieel voor de beveiliging tegen gegevens verlies en service niet beschikbaar. Service Fabric biedt een optionele service voor back-up en herstel, waarmee u periodieke back-ups kunt configureren van stateful Reliable Services (inclusief actor Services) zonder dat u extra code hoeft te schrijven. Het vereenvoudigt ook het herstellen van eerder gemaakte back-ups. 


Service Fabric biedt een reeks Api's om de volgende functionaliteit te bieden met betrekking tot periodieke back-up-en herstel functies:

- Plan periodieke back-ups van betrouw bare stateful Services en Reliable Actors met ondersteuning voor het uploaden van back-ups naar (externe) opslag locaties. Ondersteunde opslag locaties
    - Azure Storage
    - Bestands share (on-premises)
- Back-ups opsommen
- Een ad-hoc-back-up van een partitie activeren
- Een partitie herstellen met de vorige back-up
- Back-ups tijdelijk onderbreken
- Retentie beheer van back-ups (gepland)

## <a name="prerequisites"></a>Vereisten
* Service Fabric cluster met infrastructuur versie 6,4 of hoger. Raadpleeg dit [artikel](service-fabric-cluster-creation-via-arm.md) voor stappen voor het maken van service Fabric cluster met behulp van een Azure-resource sjabloon.
* X. 509-certificaat voor het versleutelen van geheimen die nodig zijn om verbinding te maken met opslag om back-ups op te slaan. Raadpleeg het [artikel](service-fabric-cluster-creation-via-arm.md) om te weten hoe u een X. 509-certificaat kunt ophalen of maken.
* Service Fabric betrouw bare stateful toepassing die is gebouwd met Service Fabric SDK-versie 3,0 of hoger. Voor toepassingen die zijn gericht op .NET Core 2,0, moet de toepassing worden gebouwd met Service Fabric SDK-versie 3,1 of hoger.
* Maak Azure Storage-account voor het opslaan van back-ups van toepassingen.
* Installeer de micro soft. ServiceFabric. Power shell. http-module (preview) om configuratie aanroepen te maken.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Als uw PowerShellGet-versie lager is dan 1.6.0, moet u bijwerken om ondersteuning toe te voegen voor de vlag *-AllowPrerelease* :
>
> `Install-Module -Name PowerShellGet -Force`

* Zorg ervoor dat het cluster is verbonden met behulp van de `Connect-SFCluster` opdracht voordat u een configuratie aanvraag maakt met behulp van de module micro soft. ServiceFabric. Power shell. http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Service voor back-up en herstel inschakelen

### <a name="using-azure-portal"></a>Azure Portal gebruiken

Schakel `Include backup restore service` het selectie vakje in `+ Show optional settings` op het `Cluster Configuration` tabblad.

![Service voor het terugzetten van back-ups inschakelen met de portal][1]


### <a name="using-azure-resource-manager-template"></a>Azure Resource Manager-sjabloon gebruiken
Eerst moet u de service voor _back-up en herstel_ inschakelen in uw cluster. Haal de sjabloon op voor het cluster dat u wilt implementeren. U kunt de [voorbeeld sjablonen](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) gebruiken of een resource manager-sjabloon maken. Schakel de _service voor back-up en herstel_ in met de volgende stappen:

1. Controleer of de `apiversion` is ingesteld op **`2018-02-01`** voor de `Microsoft.ServiceFabric/clusters` resource en als dat niet het geval is, werkt u deze bij zoals wordt weer gegeven in het volgende code fragment:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Schakel nu de _Service back-up en herstel_ in door de volgende sectie toe te voegen `addonFeatures` onder `properties` sectie, zoals wordt weer gegeven in het volgende code fragment: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Het X. 509-certificaat configureren voor het versleutelen van referenties. Dit is belang rijk om ervoor te zorgen dat de referenties die zijn verschaft om verbinding te maken met de opslag, worden versleuteld voordat ze worden bewaard. Configureer het versleutelings certificaat door de volgende sectie toe te voegen `BackupRestoreService` onder `fabricSettings` sectie, zoals wordt weer gegeven in het volgende code fragment: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Nadat u de cluster sjabloon met de voor gaande wijzigingen hebt bijgewerkt, past u deze toe en laat u de implementatie/upgrade volt ooien. Zodra het proces is voltooid, wordt de _back-up-en herstel service_ gestart in uw cluster. De URI van deze service is `fabric:/System/BackupRestoreService` en de service bevindt zich in de sectie systeem service van de service Fabric Explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Periodieke back-ups inschakelen voor betrouw bare stateful service en Reliable Actors
We gaan stappen uitvoeren om periodieke back-ups in te scha kelen voor betrouw bare stateful service en Reliable Actors. Bij deze stappen wordt ervan uitgegaan
- Of het cluster is ingesteld met behulp van X. 509-beveiliging met _Backup-en Restore-service_.
- Een betrouw bare stateful service wordt op het cluster geïmplementeerd. Voor het doel van deze Snelstartgids is de toepassings-URI `fabric:/SampleApp` en de URI voor een betrouw bare stateful service die deel uitmaakt van deze toepassing `fabric:/SampleApp/MyStatefulService` . Deze service wordt geïmplementeerd met één partitie en de partitie-ID is `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .
- Het client certificaat met de rol beheerder wordt geïnstalleerd in de archief naam _mijn_ (persoonlijk _) van het_ adres archief op de computer waar de onderstaande scripts worden aangeroepen. In dit voor beeld wordt `1b7ebe2174649c45474a4819dafae956712c31d3` als vinger afdruk van dit certificaat gebruikt. Zie op [rollen gebaseerd toegangs beheer voor service Fabric-clients](service-fabric-cluster-security-roles.md)voor meer informatie over client certificaten.

### <a name="create-backup-policy"></a>Back-upbeleid maken

De eerste stap is het maken van een back-upbeleid met een beschrijving van back-upschema, doel opslag voor back-upgegevens, beleids naam en maximale incrementele back-ups die moeten worden toegestaan voordat het volledige back-up-en bewaar beleid voor back-upopslag wordt geactiveerd. 

Gebruik voor back-upopslag het Azure Storage account dat hierboven is gemaakt. De container `backup-container` is geconfigureerd voor het opslaan van back-ups. Er wordt een container met deze naam gemaakt, als deze nog niet bestaat, tijdens het uploaden van back-ups. Vul `ConnectionString` een geldig Connection String voor het Azure Storage-account in, vervang `account-name` door de naam van uw opslag account en `account-key` met de sleutel van uw opslag account.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

Voer de volgende Power shell-cmdlets uit om een nieuw back-upbeleid te maken. Vervang door `account-name` de naam van uw opslag account en `account-key` met de sleutel van uw opslag account.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

Voer het volgende Power shell-script uit om de vereiste REST API aan te roepen om een nieuw beleid te maken. Vervang door `account-name` de naam van uw opslag account en `account-key` met de sleutel van uw opslag account.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken

1. Ga in Service Fabric Explorer naar het tabblad Back-ups en selecteer acties > back-upbeleid maken.

    ![Back-upbeleid maken][6]

2. Vul de informatie in. Voor Azure-clusters moet u AzureBlobStore selecteren.

    ![Back-upbeleid maken Azure Blob Storage][7]

### <a name="enable-periodic-backup"></a>Periodieke back-up inschakelen
Nadat het back-upbeleid is gedefinieerd om te voldoen aan de vereisten voor gegevens beveiliging van de toepassing, moet het back-upbeleid zijn gekoppeld aan de toepassing. Afhankelijk van de vereiste kan het back-upbeleid worden gekoppeld aan een toepassing, service of partitie.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

Voer het volgende Power shell-script uit om de vereiste REST API aan te roepen om het back-upbeleid te koppelen aan de `BackupPolicy1` bovenstaande stap met de toepassing `SampleApp` .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken
Controleer of de [Geavanceerde modus](service-fabric-visualizing-your-cluster.md#backup-and-restore) voor service Fabric Explorer is ingeschakeld 

1. Selecteer een toepassing en ga naar actie. Klik op back-up van toepassing inschakelen/bijwerken.

    ![Back-up van toepassing inschakelen][3]

2. Selecteer ten slotte het gewenste beleid en klik op back-up inschakelen.

    ![Beleid selecteren][4]


### <a name="verify-that-periodic-backups-are-working"></a>Controleren of periodieke back-ups werken

Nadat u een back-up op toepassings niveau hebt ingeschakeld, worden alle partities die horen bij betrouw bare stateful Services en Reliable Actors onder de toepassing, regel matig met behulp van het bijbehorende back-upbeleid gestart. 

![Status gebeurtenis voor partitie waarnaar][0]

### <a name="list-backups"></a>Back-ups weer geven

Back-ups die zijn gekoppeld aan alle partities die deel uitmaken van betrouw bare stateful Services en Reliable Actors van de toepassing kunnen worden geïnventariseerd met behulp van de _GetBackups_ -API. U kunt back-ups opsommen voor een toepassing, service of partitie.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

Voer de volgende Power shell-script uit om de HTTP API aan te roepen om de back-ups te inventariseren die zijn gemaakt voor alle partities in de `SampleApp` toepassing.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken

Als u back-ups wilt weer geven in Service Fabric Explorer, gaat u naar een partitie en selecteert u het tabblad Back-ups.

![Back-ups opsommen][5]

## <a name="limitation-caveats"></a>Beperking/voor behoud
- Service Fabric Power shell-cmdlets bevinden zich in de preview-modus.
- Geen ondersteuning voor Service Fabric clusters op Linux.

## <a name="next-steps"></a>Volgende stappen
- [Inzicht in periodieke back-upconfiguratie](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Naslag informatie over back-up terugzetten REST API](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png
