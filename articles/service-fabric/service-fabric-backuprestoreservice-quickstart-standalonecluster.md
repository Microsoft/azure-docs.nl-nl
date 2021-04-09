---
title: Periodieke back-ups maken/herstellen in zelfstandige Azure-Service Fabric
description: Gebruik een zelfstandige Service Fabric periodieke functie voor back-up en herstel voor het inschakelen van periodieke gegevens back-ups van uw toepassings gegevens.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: d78a627c0c50a3e2ec57138e40cb5bc97486d6f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103198694"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Periodieke back-ups maken en herstellen in een zelfstandige Service Fabric
> [!div class="op_single_selector"]
> * [Clusters op Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Zelfstandige clusters](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric is een platform voor gedistribueerde systemen waarmee u gemakkelijk betrouw bare, gedistribueerde, op micro Services gebaseerde Cloud toepassingen kunt ontwikkelen en beheren. Hiermee kan zowel stateless als stateful micro services worden uitgevoerd. Stateful Services kunnen de onveranderlijke, gezaghebbende status behouden naast de aanvraag en het antwoord of een volledige trans actie. Als een stateful service gedurende lange tijd niet beschikbaar is of als er gegevens verloren gaan als gevolg van een nood geval, moet deze mogelijk worden hersteld naar een recente back-up van de status om de service te kunnen blijven ontvangen nadat er een back-up is gemaakt.

Service Fabric de status op meerdere knoop punten repliceert om ervoor te zorgen dat de service Maxi maal beschikbaar is. Zelfs als één knoop punt in het cluster uitvalt, blijft de service beschikbaar. In bepaalde gevallen is het echter toch wenselijk dat de service gegevens betrouwbaar zijn voor bredere storingen.
 
Een service moet bijvoorbeeld mogelijk een back-up van de gegevens maken om te beschermen tegen de volgende scenario's:
- Permanent verlies van een volledig Service Fabric cluster.
- Permanent verlies van een meerderheid van de replica's van een service partitie
- Administratieve fouten waarbij de status per ongeluk wordt verwijderd of beschadigd. Een beheerder met voldoende bevoegdheden kan bijvoorbeeld de service op een onjuiste wijze verwijderen.
- Fouten in de service die leiden tot beschadigde gegevens. Dit kan bijvoorbeeld gebeuren wanneer een service code-upgrade begint met het schrijven van fout gegevens naar een betrouw bare verzameling. In dat geval kunnen zowel de code als de gegevens worden teruggedraaid naar een eerdere status.
- Offline gegevens verwerking. Het kan handig zijn om gegevens offline te verwerken voor business intelligence die los van de service worden uitgevoerd waardoor de gegevens worden gegenereerd.

Service Fabric biedt een ingebouwde API voor het [maken van back-ups en herstel](service-fabric-reliable-services-backup-restore.md)bewerkingen. Toepassings ontwikkelaars kunnen deze Api's gebruiken om regel matig een back-up te maken van de status van de service. Daarnaast moeten ontwikkel aars een back-up maken (en herstellen) als een API van de service om een back-up te kunnen activeren van buiten de service op een bepaald moment (bijvoorbeeld voordat de toepassing wordt bijgewerkt). Voor het onderhouden van de back-ups zijn hiervoor extra kosten van toepassing. U kunt bijvoorbeeld elk half uur vijf incrementele back-ups maken, gevolgd door een volledige back-up. Na de volledige back-up kunt u de vorige incrementele back-ups verwijderen. Deze aanpak vereist extra code voor extra kosten tijdens de ontwikkeling van toepassingen.

Het maken van een back-up van de toepassings gegevens op periodieke basis is een basis behoefte voor het beheren van een gedistribueerde toepassing en het beschermen van gegevens verlies of het langdurige verlies van de beschik baarheid van de service. Service Fabric biedt een optionele service voor back-up en herstel, waarmee u periodieke back-ups kunt configureren van stateful Reliable Services (inclusief actor Services) zonder dat u extra code hoeft te schrijven. Het vereenvoudigt ook het herstellen van eerder gemaakte back-ups. 

Service Fabric biedt een reeks Api's om de volgende functionaliteit te bieden met betrekking tot periodieke back-up-en herstel functies:

- Plan periodieke back-ups van betrouw bare stateful Services en Reliable Actors met ondersteuning voor het uploaden van back-ups naar (externe) opslag locaties. Ondersteunde opslag locaties
    - Azure Storage
    - Bestands share (on-premises)
- Back-ups opsommen
- Een ongeplande back-up van een partitie activeren
- Een partitie herstellen met de vorige back-up
- Back-ups tijdelijk onderbreken
- Retentie beheer van back-ups (gepland)

## <a name="prerequisites"></a>Vereisten
* Service Fabric cluster met infrastructuur versie 6,4 of hoger. Raadpleeg dit [artikel](service-fabric-cluster-creation-for-windows-server.md) voor de stappen voor het downloaden van het vereiste pakket.
* X. 509-certificaat voor het versleutelen van geheimen die nodig zijn om verbinding te maken met opslag om back-ups op te slaan. Raadpleeg het [artikel](service-fabric-windows-cluster-x509-security.md) om te weten hoe u een zelfondertekend X. 509-certificaat kunt verkrijgen of maken.

* Service Fabric betrouw bare stateful toepassing die is gebouwd met Service Fabric SDK-versie 3,0 of hoger. Voor toepassingen die zijn gericht op .NET Core 2,0, moet de toepassing worden gebouwd met Service Fabric SDK-versie 3,1 of hoger.
* Installeer de micro soft. ServiceFabric. Power shell. http-module (preview) om configuratie aanroepen te maken.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.PowerShell.Http -AllowPrerelease
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
Eerst moet u de service voor _back-up en herstel_ inschakelen in uw cluster. Haal de sjabloon op voor het cluster dat u wilt implementeren. U kunt de [voorbeeld sjablonen](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)gebruiken. Schakel de _service voor back-up en herstel_ in met de volgende stappen:

1. Controleer of de `apiversion` is ingesteld `10-2017` in in het configuratie bestand van het cluster en als dat niet het geval is, werkt u de optie bij, zoals wordt weer gegeven in het volgende code fragment:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Schakel nu de _Service back-up en herstel_ in door de volgende sectie toe te voegen `addonFeatures` onder `properties` sectie, zoals wordt weer gegeven in het volgende code fragment: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Het X. 509-certificaat configureren voor het versleutelen van referenties. Dit is belang rijk om ervoor te zorgen dat de referenties, indien aanwezig, worden versleuteld om verbinding te maken met de opslag. Configureer het versleutelings certificaat door de volgende sectie toe te voegen `BackupRestoreService` onder `fabricSettings` sectie, zoals wordt weer gegeven in het volgende code fragment: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            },
            {
                "name": "SecretEncryptionCertX509StoreName",
                "value": "My"
            }]
        }
        ...
    }
    ```

4. Nadat u uw cluster configuratie bestand met de voor gaande wijzigingen hebt bijgewerkt, past u deze toe en laat u de implementatie/upgrade volt ooien. Zodra het proces is voltooid, wordt de _back-up-en herstel service_ gestart in uw cluster. De URI van deze service is `fabric:/System/BackupRestoreService` en de service bevindt zich in de sectie systeem service van de service Fabric Explorer. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Periodieke back-ups inschakelen voor betrouw bare stateful service en Reliable Actors
We gaan stappen uitvoeren om periodieke back-ups in te scha kelen voor betrouw bare stateful service en Reliable Actors. Bij deze stappen wordt ervan uitgegaan
- Het cluster is geconfigureerd met back-up-en herstel service_.
- Een betrouw bare stateful service wordt op het cluster geïmplementeerd. Voor het doel van deze Snelstartgids is de toepassings-URI `fabric:/SampleApp` en de URI voor een betrouw bare stateful service die deel uitmaakt van deze toepassing `fabric:/SampleApp/MyStatefulService` . Deze service wordt geïmplementeerd met één partitie en de partitie-ID is `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7` .  

### <a name="create-backup-policy"></a>Back-upbeleid maken

De eerste stap is het maken van een back-upbeleid met een beschrijving van back-upschema, doel opslag voor back-upgegevens, beleids naam en maximale incrementele back-ups die moeten worden toegestaan voordat het volledige back-up-en bewaar beleid voor back-upopslag wordt geactiveerd. 

Voor back-upopslag maakt u de bestands share en geeft u ReadWrite toegang tot deze bestands share voor alle Service Fabric knooppunt machines. In dit voor beeld wordt ervan uitgegaan dat de share met de naam `BackupStore` aanwezig is op `StorageServer` .


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

Voer het volgende Power shell-script uit om de vereiste REST API aan te roepen om een nieuw beleid te maken.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
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
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken

1. Ga in Service Fabric Explorer naar het tabblad Back-ups en selecteer acties > back-upbeleid maken.

    ![Back-upbeleid maken][6]

2. Vul de informatie in. Voor zelfstandige clusters moet file share zijn geselecteerd.

    ![Back-upbeleid maken bestands share][7]

### <a name="enable-periodic-backup"></a>Periodieke back-up inschakelen
Nadat het beleid is gedefinieerd om te voldoen aan de vereisten voor gegevens beveiliging van de toepassing, moet het back-upbeleid zijn gekoppeld aan de toepassing. Afhankelijk van de vereiste kan het back-upbeleid worden gekoppeld aan een toepassing, service of partitie.


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
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer gebruiken

1. Selecteer een toepassing en ga naar actie. Klik op back-up van toepassing inschakelen/bijwerken.

    ![Back-up van toepassing inschakelen][3] 

2. Selecteer ten slotte het gewenste beleid en selecteer *back-up inschakelen*.

    ![Beleid selecteren][4]

### <a name="verify-that-periodic-backups-are-working"></a>Controleren of periodieke back-ups werken

Na het inschakelen van de back-up voor de toepassing, krijgen alle partities die horen bij betrouw bare stateful Services en Reliable Actors onder de toepassing, regel matig een back-up van het bijbehorende back-upbeleid.

![Status gebeurtenis voor partitie waarnaar][0]

### <a name="list-backups"></a>Back-ups weer geven

Back-ups die zijn gekoppeld aan alle partities die deel uitmaken van betrouw bare stateful Services en Reliable Actors van de toepassing kunnen worden geïnventariseerd met behulp van de _GetBackups_ -API. Afhankelijk van de vereiste kunnen de back-ups worden geïnventariseerd voor de toepassing, de service of een partitie.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Power shell met behulp van de module micro soft. ServiceFabric. Power shell. http

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Rest-aanroep met Power shell

Voer de volgende Power shell-script uit om de HTTP API aan te roepen om de back-ups te inventariseren die zijn gemaakt voor alle partities in de `SampleApp` toepassing.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Voorbeeld uitvoer voor de bovenstaande uitvoering:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
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

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png
