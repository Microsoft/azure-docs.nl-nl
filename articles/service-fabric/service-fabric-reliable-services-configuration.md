---
title: Azure Service Fabric Reliable Services configureren
description: Meer informatie over het configureren van stateful-Reliable Services in een Azure Service Fabric-toepassing wereld wijd en voor één service.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.custom: devx-track-csharp
ms.openlocfilehash: cda0a9f988afae58a60bff051885a5eec8afe434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023581"
---
# <a name="configure-stateful-reliable-services"></a>Stateful reliable Services configureren
Er zijn twee sets configuratie-instellingen voor betrouw bare Services. Eén set is globaal voor alle betrouw bare Services in het cluster, terwijl de andere set specifiek is voor een bepaalde betrouw bare service.

## <a name="global-configuration"></a>Algemene configuratie
De algemene betrouw bare service configuratie is opgegeven in het cluster manifest voor het cluster in het gedeelte KtlLogger. Hiermee kunt u de locatie en grootte van het gedeelde logboek configureren, plus de globale geheugen limieten die door de logboeken worden gebruikt. Het cluster manifest is een enkel XML-bestand met instellingen en configuraties die van toepassing zijn op alle knoop punten en services in het cluster. Het bestand wordt doorgaans ClusterManifest.xml genoemd. U kunt het cluster manifest voor uw cluster zien met behulp van de Get-ServiceFabricClusterManifest Power shell-opdracht.

### <a name="configuration-names"></a>Configuratie namen
| Name | Eenheid | Standaardwaarde | Opmerkingen |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilo bytes |8388608 |Minimum aantal KB dat moet worden toegewezen in de kernelmodus voor de geheugen groep schrijf buffer voor logboek registratie. Deze geheugen groep wordt gebruikt voor het opslaan van de status informatie voordat naar de schijf wordt geschreven. |
| WriteBufferMemoryPoolMaximumInKB |Kilo bytes |Geen limiet |Maximale grootte van de geheugen groep voor schrijf buffer voor logboek registratie kan worden uitgebreid. |
| SharedLogId |GUID |"" |Hiermee geeft u een unieke GUID op die moet worden gebruikt voor het identificeren van het standaard gedeelde logboek bestand dat wordt gebruikt door alle betrouw bare Services op alle knoop punten in het cluster die de SharedLogId niet opgeven in hun servicespecifieke configuratie. Als SharedLogId is opgegeven, moet SharedLogPath ook worden opgegeven. |
| SharedLogPath |Fully Qualified Path name |"" |Hiermee geeft u het volledige pad op waar het gedeelde logboek bestand wordt gebruikt door alle betrouw bare Services op alle knoop punten in het cluster die de SharedLogPath niet opgeven in hun servicespecifieke configuratie. Als SharedLogPath echter is opgegeven, moet SharedLogId ook worden opgegeven. |
| SharedLogSizeInMB |Mega bytes |8192 |Hiermee geeft u het aantal MB schijf ruimte op dat statisch moet worden toegewezen aan het gedeelde logboek. De waarde moet 2048 of groter zijn. |

In het voor beeld hieronder ziet u in azure ARM of een on-premises JSON-sjabloon hoe u het gedeelde transactie logboek kunt wijzigen dat wordt gemaakt om back-ups te maken van betrouw bare verzamelingen voor stateful Services.

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="sample-local-developer-cluster-manifest-section"></a>Voor beeld van een lokaal ontwikkelaars cluster manifest sectie
Als u dit wilt wijzigen in uw lokale ontwikkel omgeving, moet u het lokale clustermanifest.xml-bestand bewerken.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Opmerkingen
De logger heeft een globale geheugen groep die is toegewezen op basis van niet-wisselbaar kernelgeheugen dat beschikbaar is voor alle betrouw bare Services op een knoop punt om status gegevens in de cache op te zetten voordat ze worden geschreven naar het toegewezen logboek dat is gekoppeld aan de betrouw bare service replica. De pool grootte wordt bepaald door de instellingen WriteBufferMemoryPoolMinimumInKB en WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Hiermee geeft u de oorspronkelijke grootte van deze geheugen groep en de laagste grootte op waarmee de geheugen groep kan worden verkleind. WriteBufferMemoryPoolMaximumInKB is de hoogste grootte waarmee de geheugen groep mag groeien. Elke betrouw bare service replica die wordt geopend, kan de grootte van de geheugen groep verhogen door een door het systeem vastgesteld bedrag tot WriteBufferMemoryPoolMaximumInKB. Als er meer geheugen nodig is dan beschikbaar is, worden de aanvragen voor geheugen uitgesteld totdat er geheugen beschikbaar is. Als de geheugen groep schrijf buffer te klein is voor een bepaalde configuratie, kan dit de prestaties nadelig beïnvloeden.

De instellingen SharedLogId en SharedLogPath worden altijd samen gebruikt voor het definiëren van de GUID en locatie voor het standaard gedeelde logboek voor alle knoop punten in het cluster. Het standaard gedeelde logboek wordt gebruikt voor alle betrouw bare services die de instellingen niet opgeven in de settings.xml voor de specifieke service. Voor de beste prestaties moeten gedeelde logboek bestanden worden geplaatst op schijven die alleen worden gebruikt voor het gedeelde logboek bestand om conflicten te verminderen.

SharedLogSizeInMB Hiermee geeft u de hoeveelheid schijf ruimte op die vooraf moet worden toegewezen voor het gedeelde standaard logboek op alle knoop punten.  SharedLogId en SharedLogPath hoeven niet te worden opgegeven om SharedLogSizeInMB te kunnen opgeven.

## <a name="service-specific-configuration"></a>Servicespecifieke configuratie
U kunt stateful Reliable Services ' standaard configuraties wijzigen met behulp van het configuratie pakket (config) of de service-Implementatie (code).

* **Config** -configuratie via het configuratie pakket wordt bereikt door het Settings.xml bestand te wijzigen dat wordt gegenereerd in de hoofdmap van het micro soft Visual Studio-pakket onder de map config voor elke service in de toepassing.
* **Code**   -configuratie via code wordt bereikt door een ReliableStateManager te maken met behulp van een ReliableStateManagerConfiguration-object met de juiste opties ingesteld.

De Azure Service Fabric runtime zoekt standaard naar vooraf gedefinieerde sectie namen in het Settings.xml bestand en verbruikt de configuratie waarden tijdens het maken van de onderliggende runtime-onderdelen.

> [!NOTE]
> Verwijder de sectie namen van de volgende configuraties **niet** in het Settings.xml-bestand dat is gegenereerd in de Visual Studio-oplossing, tenzij u van plan bent om uw service via code te configureren.
> Voor het wijzigen van de naam van het configuratie pakket of de sectie namen is een code wijziging vereist bij het configureren van de ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Replicatie-beveiligings configuratie
Replicatie-beveiligings configuraties worden gebruikt voor het beveiligen van het communicatie kanaal dat wordt gebruikt tijdens de replicatie. Dit betekent dat services elkaar geen replicatie verkeer kunnen zien, zodat de gegevens die Maxi maal beschikbaar worden, ook worden beveiligd. Een lege beveiligings configuratie sectie voor komt standaard replicatie beveiliging.

> [!IMPORTANT]
> Voor Linux-knoop punten moeten certificaten PEM zijn. Zie [certificaten configureren voor Linux](./service-fabric-configure-certificates-linux.md)voor meer informatie over het zoeken en configureren van certificaten voor Linux. 
> 
> 

### <a name="default-section-name"></a>Standaard sectie naam
ReplicatorSecurityConfig

> [!NOTE]
> Als u deze sectie naam wilt wijzigen, moet u de para meter replicatorSecuritySectionName overschrijven naar de ReliableStateManagerConfiguration-constructor bij het maken van de ReliableStateManager voor deze service.
> 
> 

### <a name="replicator-configuration"></a>Replicator-configuratie
Met replicatie configuraties wordt de Replicator geconfigureerd die verantwoordelijk is voor de status van de stateful betrouw bare service zeer betrouwbaar door de replicatie te repliceren en de status lokaal op te slaan.
De standaard configuratie wordt gegenereerd door de Visual Studio-sjabloon en moet voldoende zijn. In deze sectie vindt u meer informatie over aanvullende configuraties die beschikbaar zijn voor het afstemmen van de Replicator.

### <a name="default-section-name"></a>Standaard sectie naam
ReplicatorConfig

> [!NOTE]
> Als u deze sectie naam wilt wijzigen, moet u de para meter replicatorSettingsSectionName overschrijven naar de ReliableStateManagerConfiguration-constructor bij het maken van de ReliableStateManager voor deze service.
> 
> 

### <a name="configuration-names"></a>Configuratie namen
| Name | Eenheid | Standaardwaarde | Opmerkingen |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Seconden |0,015 |De periode gedurende welke de Replicator op het secundaire wacht na ontvangst van een bewerking voordat een bevestiging wordt verzonden naar de primaire. Alle andere bevestigingen die moeten worden verzonden voor bewerkingen die binnen dit interval worden verwerkt, worden als één antwoord verzonden. |
| ReplicatorEndpoint |N.v.t. |Geen standaard-vereiste para meter |Het IP-adres en de poort die door de primaire/secundaire Replicator worden gebruikt om te communiceren met andere replicatie Programma's in de replicaset. Dit moet verwijzen naar een TCP-bron eindpunt in het service manifest. Raadpleeg de [service manifest bronnen](service-fabric-service-manifest-resources.md) voor meer informatie over het definiëren van eindpunt resources in een service manifest. |
| MaxPrimaryReplicationQueueSize |Aantal bewerkingen |8192 |Maximum aantal bewerkingen in de primaire wachtrij. Er wordt een bewerking vrijgemaakt nadat de primaire Replicator een bevestiging van alle secundaire replicaties heeft ontvangen. Deze waarde moet groter zijn dan 64 en een macht van 2 zijn. |
| MaxSecondaryReplicationQueueSize |Aantal bewerkingen |16384 |Maximum aantal bewerkingen in de secundaire wachtrij. Een bewerking wordt vrijgegeven nadat de status Maxi maal beschikbaar is via persistentie. Deze waarde moet groter zijn dan 64 en een macht van 2 zijn. |
| CheckpointThresholdInMB |MB |50 |De hoeveelheid logboek bestands ruimte waarna de status van een controle punt wordt gemaakt. |
| MaxRecordSizeInKB |KB |1024 |Grootste record grootte die door de Replicator kan worden geschreven in het logboek. Deze waarde moet een meervoud van 4 en groter dan 16 zijn. |
| MinLogSizeInMB |MB |0 (door systeem vastgesteld) |Minimale grootte van het transactionele logboek. Het logboek mag niet worden afgekapt op een grootte onder deze instelling. 0 geeft aan dat de Replicator de minimale logboek grootte bepaalt. Als u deze waarde verhoogt, verhoogt u de kans op gedeeltelijke kopieën en incrementele back-ups omdat de kans dat relevante logboek records worden afgekapt wordt verlaagd. |
| TruncationThresholdFactor |Factor |2 |Bepaalt op welke grootte het logboek moet worden afgekapt. De drempel waarde voor afkap ping wordt bepaald door MinLogSizeInMB vermenigvuldigd met TruncationThresholdFactor. TruncationThresholdFactor moet groter zijn dan 1. MinLogSizeInMB * TruncationThresholdFactor moet kleiner zijn dan MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Factor |4 |Bepaalt op welke grootte van het logboek de replica wordt gestart. Drempel waarde voor bandbreedte beperking (in MB) wordt bepaald door Max ((MinLogSizeInMB * ThrottlingThresholdFactor), (CheckpointThresholdInMB * ThrottlingThresholdFactor)). Drempel waarde voor bandbreedte beperking (in MB) moet groter zijn dan Afbrekings drempel (in MB). De drempel waarde voor afkap ping (in MB) moet kleiner zijn dan MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |De maximale gecumuleerde grootte (in MB) van back-uplogboeken in een bepaalde back-uplogboek keten. Een incrementele back-up mislukt als met de incrementele back-up een back-uplogboek wordt gegenereerd dat de geaccumuleerde back-uplogboeken zou kunnen veroorzaken omdat de relevante volledige back-up groter is dan deze grootte. In dergelijke gevallen is de gebruiker verplicht een volledige back-up te maken. |
| SharedLogId |GUID |"" |Hiermee geeft u een unieke GUID op die moet worden gebruikt voor het identificeren van het gedeelde logboek bestand dat wordt gebruikt met deze replica. Normaal gesp roken moeten services deze instelling niet gebruiken. Als SharedLogId echter is opgegeven, moet SharedLogPath ook worden opgegeven. |
| SharedLogPath |Fully Qualified Path name |"" |Hiermee geeft u het volledige pad op waar het gedeelde logboek bestand voor deze replica wordt gemaakt. Normaal gesp roken moeten services deze instelling niet gebruiken. Als SharedLogPath echter is opgegeven, moet SharedLogId ook worden opgegeven. |
| SlowApiMonitoringDuration |Seconden |300 |Hiermee stelt u het bewakings interval voor beheerde API-aanroepen. Voor beeld: door de gebruiker geleverde Terugbel functie voor back-up. Nadat het interval is verstreken, wordt een waarschuwings status rapport verzonden naar de Health Manager. |
| LogTruncationIntervalSeconds |Seconden |0 |Configureerbaar interval waarmee de afkap ping van het logboek op elke replica wordt gestart. Het wordt gebruikt om ervoor te zorgen dat Logboeken ook worden afgekapt op basis van tijd in plaats van alleen logboek grootte. Deze instelling dwingt ook het leegmaken van verwijderde vermeldingen in een betrouw bare woorden lijst af. Dit kan daarom worden gebruikt om ervoor te zorgen dat verwijderde items tijdig worden opgeschoond. |
| EnableStableReads |Booleaans |Niet waar |Het inschakelen van stabiele Lees bewerkingen beperkt secundaire replica's voor het retour neren van waarden die zijn bevestigd. |

### <a name="sample-configuration-via-code"></a>Voorbeeld configuratie via code
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Voorbeeldconfiguratiebestand
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### <a name="remarks"></a>Opmerkingen
BatchAcknowledgementInterval beheert de replicatie latentie. De waarde ' 0 ' resulteert in de laagst mogelijke latentie, tegen de kosten van de door Voer (omdat er meer bevestigings berichten moeten worden verzonden en verwerkt, elk met minder bevestigingen).
Hoe groter de waarde voor BatchAcknowledgementInterval, hoe hoger de totale replicatie doorvoer, tegen de kosten van een hogere latentie van de bewerking. Dit kan rechtstreeks worden omgezet naar de latentie van trans acties door voeren.

De waarde voor CheckpointThresholdInMB regelt de hoeveelheid schijf ruimte die door de Replicator kan worden gebruikt om status gegevens op te slaan in het specifieke logboek bestand van de replica. Het verhogen van dit naar een hogere waarde dan de standaard instelling kan leiden tot snellere herconfiguratie tijden wanneer een nieuwe replica wordt toegevoegd aan de set. Dit wordt veroorzaakt door de gedeeltelijke status overdracht die plaatsvindt als gevolg van de beschik baarheid van meer geschiedenis van bewerkingen in het logboek. Dit kan leiden tot een grotere herstel tijd van een replica na een crash.

De instelling MaxRecordSizeInKB definieert de maximale grootte van een record die door de Replicator kan worden geschreven naar het logboek bestand. In de meeste gevallen is de standaard record grootte van 1024 KB optimaal. Als de service echter zorgt voor grotere gegevens items die deel uitmaken van de status informatie, moet deze waarde mogelijk worden verhoogd. Er is weinig voor deel bij het maken van MaxRecordSizeInKB kleiner dan 1024, omdat kleinere records alleen de benodigde ruimte voor de kleinere record gebruiken. We verwachten dat deze waarde alleen in zeldzame gevallen moet worden gewijzigd.

De instellingen SharedLogId en SharedLogPath worden altijd samen gebruikt om een service een afzonderlijk gedeeld logboek te laten gebruiken van het gedeelde standaard logboek voor het knoop punt. Voor de beste efficiëntie moeten zoveel mogelijke services hetzelfde gedeelde logboek opgeven. Gedeelde logboek bestanden moeten worden geplaatst op schijven die alleen worden gebruikt voor het gedeelde logboek bestand om de inhoud van de hoofd verplaatsing te verminderen. We verwachten dat deze waarde alleen in zeldzame gevallen moet worden gewijzigd.

## <a name="next-steps"></a>Volgende stappen
* [Fouten opsporen in uw Service Fabric-toepassing in Visual Studio](service-fabric-debugging-your-application.md)
* [Naslag informatie voor ontwikkel aars voor Reliable Services](/previous-versions/azure/dn706529(v=azure.100))
