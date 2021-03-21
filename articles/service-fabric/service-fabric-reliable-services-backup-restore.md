---
title: Back-up en herstel van Service Fabric
description: Conceptuele documentatie voor Service Fabric back-up en herstel, een service voor het configureren van back-ups van betrouw bare stateful Services en Reliable Actors.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: 2674d1285544e4bc9b6fcb3d0b2e6f4b607786a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791608"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Back-up en herstel Reliable Services en Reliable Actors
Azure Service Fabric is een platform met hoge Beschik baarheid dat de status van meerdere knoop punten repliceert om deze hoge Beschik baarheid te behouden.  Dus zelfs als één knoop punt in het cluster uitvalt, blijven de services beschikbaar. Hoewel deze door het platform verschafte redundantie mogelijk voldoende is voor sommige, is het wenselijk dat de service back-ups maakt van gegevens (naar een externe opslag).

> [!NOTE]
> Het is essentieel om een back-up te maken van uw gegevens en deze te herstellen (en te testen of deze werkt zoals verwacht), zodat u kunt herstellen vanuit scenario's voor gegevens verlies.
> 

> [!NOTE]
> Micro soft raadt u aan om [periodieke back-ups en herstel bewerkingen](service-fabric-backuprestoreservice-quickstart-azurecluster.md) te gebruiken voor het configureren van gegevens back-ups van betrouw bare stateful services en reliable actors. 
> 


Een service kan bijvoorbeeld een back-up van gegevens maken om te beschermen tegen de volgende scenario's:

- In het geval van een permanent verlies van een volledig Service Fabric cluster.
- Permanent verlies van een meerderheid van de replica's van een service partitie
- Administratieve fouten waarbij de status per ongeluk wordt verwijderd of beschadigd. Dit kan bijvoorbeeld gebeuren als een beheerder met voldoende bevoegdheden de service ten onrechte verwijdert.
- Fouten in de service die leiden tot beschadigde gegevens. Dit kan bijvoorbeeld gebeuren wanneer een service code-upgrade begint met het schrijven van fout gegevens naar een betrouw bare verzameling. In dat geval kunnen zowel de code als de gegevens worden teruggedraaid naar een eerdere status.
- Offline gegevens verwerking. Het kan handig zijn om gegevens offline te verwerken voor business intelligence die los van de service worden uitgevoerd waardoor de gegevens worden gegenereerd.

Met de functie voor back-up/herstel kunt u op de Reliable Services-API ingebouwde services maken en back-ups herstellen. Met de back-upapi's van het platform wordt de back-up (s) van de status van een service partitie toegestaan zonder dat lees-of schrijf bewerkingen worden geblokkeerd. Met de Restore-Api's kan de status van een service partitie worden hersteld van een gekozen back-up.

## <a name="types-of-backup"></a>Typen back-ups
Er zijn twee opties voor back-up: volledig en incrementeel.
Een volledige back-up is een back-up die alle gegevens bevat die nodig zijn om de status van de replica opnieuw te maken: controle punten en alle logboek records.
Omdat deze de controle punten en het logboek bevat, kan een volledige back-up zelf worden hersteld.

Het probleem met volledige back-ups doet zich voor wanneer de controle punten groot zijn.
Een replica met een status van 16 GB heeft bijvoorbeeld controle punten die ongeveer tot 16 GB optellen.
Als we een herstel punt hebben van vijf minuten, moet elke vijf minuten een back-up worden gemaakt van de replica.
Telkens wanneer er een back-up wordt gemaakt, moet er 16 GB aan controle punten worden gekopieerd, naast 50 MB (configureerbaar met `CheckpointThresholdInMB` ) aan logboeken.

![Voor beeld van volledige back-up.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

De oplossing voor dit probleem is incrementele back-ups, waarbij alleen back-ups de gewijzigde logboek records sinds de laatste back-up bevatten.

![Voor beeld van incrementele back-up.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Omdat incrementele back-ups alleen wijzigingen zijn sinds de laatste back-up (bevat niet de controle punten), zijn ze vaak sneller, maar ze kunnen niet zelf worden hersteld.
Als u een incrementele back-up wilt herstellen, is de volledige back-upketen vereist.
Een back-upketen is een keten van back-ups, te beginnen met een volledige back-up en gevolgd door een aantal aaneengesloten incrementele back-ups.

## <a name="backup-reliable-services"></a>Back-upReliable Services
De auteur van de service heeft volledige controle over wanneer back-ups moeten worden gemaakt en waar back-ups worden opgeslagen.

De service moet de overgenomen functie aanroepen om een back-up te starten `BackupAsync` .  
Back-ups kunnen alleen worden gemaakt op basis van primaire replica's en de schrijf status moet worden toegekend.

Zoals hieronder wordt weer gegeven, `BackupAsync` neemt in een `BackupDescription` object, waar een volledige of incrementele back-up, evenals een call back-functie, `Func<< BackupInfo, CancellationToken, Task<bool>>>` die wordt aangeroepen wanneer de back-upmap lokaal is gemaakt en gereed is om te worden verplaatst naar een externe opslag.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

De aanvraag voor het uitvoeren van een incrementele back-up kan mislukken met `FabricMissingFullBackupException` . Deze uitzonde ring geeft aan dat een van de volgende dingen gebeurt:

- de replica heeft nooit een volledige back-up gemaakt, omdat deze primair is geworden.
- enkele logboek records sinds de laatste back-up zijn afgekapt of
- replica heeft de `MaxAccumulatedBackupLogSizeInMB` limiet door gegeven.

Gebruikers kunnen de kans verg Roten dat er incrementele back-ups kunnen worden gemaakt door het configureren `MinLogSizeInMB` of `TruncationThresholdFactor` .
Als u deze waarden verhoogt, neemt het schijf gebruik per replica toe.
Zie [reliable Services-configuratie](service-fabric-reliable-services-configuration.md) voor meer informatie.

`BackupInfo` bevat informatie over de back-up, met inbegrip van de locatie van de map waar de runtime de back-up heeft opgeslagen ( `BackupInfo.Directory` ). De call back functie kan de `BackupInfo.Directory` naar een externe opslag of een andere locatie verplaatsen.  Deze functie retourneert ook een Boole-waarde die aangeeft of de back-upmap met succes kan worden verplaatst naar de doel locatie.

De volgende code laat zien hoe de `BackupCallbackAsync` methode kan worden gebruikt voor het uploaden van de back-up naar Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

In het vorige voor beeld `ExternalBackupStore` is de voor beeld-klasse die wordt gebruikt voor de interface met Azure Blob Storage, en `UploadBackupFolderAsync` is de methode waarmee de map wordt gecomprimeerd en geplaatst in de Azure Blob Store.

Opmerking:

  - Er kan op elk gewenst moment slechts één back-upbewerking per replica worden uitgevoerd. Meer dan één `BackupAsync` gesprek tegelijk leidt `FabricBackupInProgressException` tot het beperken van back-ups van de invlucht naar één.
  - Als er een failover wordt uitgevoerd voor een replica terwijl er een back-up wordt gemaakt, is de back-up mogelijk niet voltooid. Zodra de failover is voltooid, is het de verantwoordelijkheid van de service om de back-up opnieuw te starten door als nodig aan te roepen `BackupAsync` .

## <a name="restore-reliable-services"></a>Reliable Services herstellen
In het algemeen zijn de gevallen waarin het nodig is om een herstel bewerking uit te voeren in een van deze categorieën:

  - De gegevens van de service partitie zijn verloren gegaan. Bijvoorbeeld: de schijf voor twee van de drie replica's voor een partitie (met inbegrip van de primaire replica) wordt beschadigd of gewist. De nieuwe primaire gegevens moeten mogelijk worden teruggezet vanuit een back-up.
  - De volledige service gaat verloren. Een beheerder verwijdert bijvoorbeeld de volledige service en dus de service en de gegevens moeten worden hersteld.
  - De service heeft beschadigde toepassings gegevens gerepliceerd (bijvoorbeeld vanwege een toepassings fout). In dit geval moet de service worden bijgewerkt of teruggezet om de oorzaak van de beschadiging te verwijderen en moeten niet-beschadigde gegevens worden hersteld.

Hoewel er veel benaderingen kunnen zijn, bieden we enkele voor beelden over `RestoreAsync` het gebruik van om te herstellen van de bovenstaande scenario's.

## <a name="partition-data-loss-in-reliable-services"></a>Gegevens verlies in Reliable Services partitioneren
In dit geval detecteert de runtime automatisch het gegevens verlies en aanroepen van de `OnDataLossAsync` API.

De auteur van de service moet het volgende doen om het probleem te verhelpen:

  - Overschrijf de methode van de virtuele basis klasse `OnDataLossAsync` .
  - Zoek de meest recente back-up op de externe locatie die de back-ups van de service bevat.
  - Down load de meest recente back-up (en decomprimeer de back-up in de map back-up als deze is gecomprimeerd).
  - De- `OnDataLossAsync` methode biedt een `RestoreContext` . Roep de `RestoreAsync` API op de gegeven `RestoreContext` .
  - Retourneert waar als de herstel bewerking is geslaagd.

Hier volgt een voor beeld van de implementatie van de- `OnDataLossAsync` methode:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` door gegeven aan de `RestoreContext.RestoreAsync` aanroep bevat een lid met de naam `BackupFolderPath` .
Wanneer u één volledige back-up herstelt, `BackupFolderPath` moet dit worden ingesteld op het lokale pad van de map die uw volledige back-up bevat.
Bij het herstellen van een volledige back-up en een aantal incrementele back-ups `BackupFolderPath` moet worden ingesteld op het lokale pad van de map die niet alleen de volledige back-up bevat, maar ook alle incrementele back-ups.
`RestoreAsync` de aanroep kan worden gegenereerd `FabricMissingFullBackupException` als de `BackupFolderPath` gegeven geen volledige back-up bevat.
Het kan ook worden gegenereerd `ArgumentException` als `BackupFolderPath` een keten van incrementele back-ups is verbroken.
Bijvoorbeeld, als het de volledige back-up bevat, de eerste incrementele en derde incrementele back-up, maar geen tweede incrementele back-up.

> [!NOTE]
> De RestorePolicy is standaard ingesteld op veilig.  Dit betekent dat de `RestoreAsync` API mislukt met ArgumentException als wordt gedetecteerd dat de back-upmap een status bevat die ouder is dan of gelijk is aan de status die is opgenomen in deze replica.  `RestorePolicy.Force` kan worden gebruikt om deze veiligheids controle over te slaan. Dit is opgegeven als onderdeel van `RestoreDescription` .
> 

## <a name="deleted-or-lost-service"></a>Service verwijderd of verloren
Als een service wordt verwijderd, moet u eerst de service opnieuw maken voordat de gegevens kunnen worden hersteld.  Het is belang rijk om de service te maken met dezelfde configuratie, bijvoorbeeld voor het partitioneren van het schema, zodat de gegevens naadloos kunnen worden hersteld.  Zodra de service is ingeschakeld, moet de API voor het herstellen `OnDataLossAsync` van gegevens (hierboven) worden aangeroepen op elke partitie van deze service. Een manier om dit te bereiken, is door [FabricClient. TestManagementClient. StartPartitionDataLossAsync](/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartPartitionDataLossAsync_System_Guid_System_Fabric_PartitionSelector_System_Fabric_DataLossMode_) op elke partitie te gebruiken.  

Vanaf dit punt is de implementatie hetzelfde als het bovenstaande scenario. Elke partitie moet de meest recente relevante back-up herstellen vanuit de externe opslag. Een voor behoud is dat de partitie-ID nu is gewijzigd, omdat de runtime partitie-Id's dynamisch maakt. De service moet dus de juiste partitie gegevens en service naam opslaan om de juiste meest recente back-up te identificeren voor herstel vanaf elke partitie.

> [!NOTE]
> Het wordt niet aanbevolen om `FabricClient.ServiceManager.InvokeDataLossAsync` op elke partitie te gebruiken om de volledige service te herstellen, omdat de cluster status hierdoor mogelijk is beschadigd.
> 

## <a name="replication-of-corrupt-application-data"></a>Replicatie van beschadigde toepassings gegevens
Als de nieuwe geïmplementeerde toepassings upgrade een bug heeft, kan dit een beschadiging van gegevens tot gevolg hebben. Een upgrade van een toepassing kan bijvoorbeeld beginnen met het bijwerken van elke telefoonnummer record in een betrouw bare woorden lijst met een ongeldige netnummer.  In dit geval worden de ongeldige telefoon nummers gerepliceerd, omdat Service Fabric niet op de hoogte is van de aard van de gegevens die worden opgeslagen.

Het eerste wat u moet doen nadat u een dergelijke egregious-fout hebt gedetecteerd die gegevens beschadiging veroorzaakt, is om de service op toepassings niveau te blok keren en om, indien mogelijk, een upgrade uit te voeren naar de versie van de toepassings code die de fout niet bevat.  Zelfs nadat de service code is gecorrigeerd, kunnen de gegevens nog steeds zijn beschadigd en kunnen de gegevens worden hersteld.  In dergelijke gevallen is het mogelijk niet voldoende om de meest recente back-up te herstellen, omdat de meest recente back-ups mogelijk ook zijn beschadigd.  U moet dus de laatste back-up vinden die is gemaakt voordat de gegevens zijn beschadigd.

Als u niet zeker weet welke back-ups zijn beschadigd, kunt u een nieuw Service Fabric-cluster implementeren en de back-ups van betrokken partities herstellen, net als in het bovenstaande scenario ' verwijderde of verloren service '.  Voor elke partitie start u de back-ups van de meest recente naar de minst. Wanneer u een back-up hebt gevonden die niet is beschadigd, verplaatst of verwijdert u alle back-ups van deze partitie die recenter zijn (dan die back-up). Herhaal dit proces voor elke partitie. Wanneer wordt nu `OnDataLossAsync` aangeroepen op de partitie in het productie cluster, wordt de laatste back-up die is gevonden in de externe Store, de versie die is opgenomen in het bovenstaande proces.

Nu kunt u de stappen in de sectie verwijderde of verloren Services gebruiken om de status van de service terug te zetten naar de status voordat de code van de fout opsporing de status beschadigd had.

Opmerking:

  - Wanneer u herstelt, is er een kans dat de back-up die wordt hersteld ouder is dan de status van de partitie voordat de gegevens verloren zijn gegaan. Daarom moet u alleen herstellen als laatste redmiddel om zoveel mogelijk gegevens te herstellen.
  - De teken reeks voor het pad van de back-upmap en de paden van bestanden in de map back-up kan langer zijn dan 255 tekens, afhankelijk van het pad van de FabricDataRoot en de naam van het toepassings type. Dit kan tot gevolg hebben dat sommige .NET-methoden, zoals `Directory.Move` , om de `PathTooLongException` uitzonde ring te genereren. Een tijdelijke oplossing is het rechtstreeks aanroepen van Kernel32 Api's, zoals `CopyFile` .

## <a name="back-up-and-restore-reliable-actors"></a>Back-ups maken en herstellen Reliable Actors


Reliable Actors Framework is boven op Reliable Services gebouwd. De ActorService, die als host fungeert voor de actor (s) is een stateful betrouw bare service. Daarom is alle functionaliteit voor back-up en herstel in Reliable Services ook beschikbaar voor Reliable Actors (met uitzonde ring van gedrag dat specifiek is voor de State-provider). Omdat back-ups per partitie worden gemaakt, krijgen de statussen voor alle actors in die partitie een back-up (en wordt herstel op dezelfde manier uitgevoerd). Als u back-up/herstel wilt uitvoeren, moet de eigenaar van de service een aangepaste actor service-klasse maken die is afgeleid van de ActorService-klasse en vervolgens back-up/herstellen lijkt op Reliable Services zoals hierboven beschreven in de vorige secties.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Wanneer u een aangepaste actor service-klasse maakt, moet u deze ook registreren bij het registreren van de actor.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

De standaard status provider voor Reliable Actors is `KvsActorStateProvider` . Incrementele back-up is niet standaard ingeschakeld voor `KvsActorStateProvider` . U kunt incrementele back-up inschakelen door te maken `KvsActorStateProvider` met de juiste instelling in de constructor en deze vervolgens door te geven aan de ActorService-constructor, zoals wordt weer gegeven in het volgende code fragment:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Nadat incrementele back-up is ingeschakeld, kan het maken van een incrementele back-up mislukken met FabricMissingFullBackupException om een van de volgende redenen, en moet u een volledige back-up maken voordat u incrementele back-ups kunt maken:

  - De replica heeft nooit een volledige back-up gemaakt, omdat deze primair is geworden.
  - Enkele van de logboek records zijn afgekapt sinds de laatste back-up is gemaakt.

Wanneer incrementele back-up is ingeschakeld, `KvsActorStateProvider` gebruikt geen circulaire buffer voor het beheren van de logboek records en wordt deze regel matig afgekapt. Als er gedurende een periode van 45 minuten geen back-up wordt gemaakt door de gebruiker, worden de records in het logboek automatisch afgekapt. Dit interval kan worden geconfigureerd door `logTruncationIntervalInMinutes` op te geven in `KvsActorStateProvider` constructor (vergelijkbaar met het inschakelen van incrementele back-up). De logboek records kunnen ook worden afgekapt als voor de primaire replica een andere replica moet worden gemaakt door alle gegevens te verzenden.

Bij het herstellen van een back-upketen, vergelijkbaar met Reliable Services, moet de BackupFolderPath submappen bevatten met één submap met volledige back-up en andere submappen met incrementele back-ups. De Restore-API genereert FabricException met het juiste fout bericht als de validatie van de back-upketen mislukt. 

> [!NOTE]
> `KvsActorStateProvider` de optie RestorePolicy. safe wordt momenteel genegeerd. Ondersteuning voor deze functie is gepland in een toekomstige release.
> 

## <a name="testing-back-up-and-restore"></a>Back-ups testen en herstellen
Het is belang rijk om ervoor te zorgen dat er een back-up van essentiële gegevens wordt gemaakt en kan worden hersteld. U kunt dit doen door de `Start-ServiceFabricPartitionDataLoss` cmdlet in Power shell aan te roepen, waardoor gegevens verlies kan optreden in een bepaalde partitie om te testen of de functionaliteit voor het maken en herstellen van gegevens voor uw service werkt zoals verwacht.  Het is ook mogelijk gegevens verlies op programmatische wijze aan te roepen en uit die gebeurtenis te herstellen.

> [!NOTE]
> U vindt een voorbeeld implementatie van de functionaliteit voor back-up en herstel in de Web Reference-app op GitHub. Raadpleeg de `Inventory.Service` service voor meer informatie.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Onder de motorkap: meer informatie over back-up en herstel
Hier vindt u meer informatie over back-up en herstel.

### <a name="backup"></a>Backup
De betrouw bare status beheerder biedt de mogelijkheid om consistente back-ups te maken zonder lees-of schrijf bewerkingen te blok keren. Hiervoor wordt een controle punt en logboek persistentie mechanisme gebruikt.  De betrouw bare status beheerder bewaart op bepaalde punten onduidelijke controle punten om druk uit het transactionele logboek te ontlasten en herstel tijden te verbeteren.  Wanneer `BackupAsync` wordt aangeroepen, zorgt de betrouw bare status beheerder ervoor dat alle betrouw bare objecten hun meest recente controlepunt bestanden kopiëren naar een lokale back-upmap.  Vervolgens kopieert de betrouw bare status Manager alle logboek records, beginnend bij ' Start pointer ' naar de meest recente logboek record naar de map backup.  Omdat alle logboek records tot de meest recente logboek record zijn opgenomen in de back-up en de betrouw bare status beheerder behoudt logboek registratie, garandeert de betrouw bare status beheerder dat alle trans acties die zijn vastgelegd ( `CommitAsync` correct zijn geretourneerd) zijn opgenomen in de back-up.

Een trans actie die wordt vastgelegd na `BackupAsync` is aangeroepen, kan al dan niet een back-up zijn.  Zodra de lokale back-upmap is gevuld door het platform (dat wil zeggen, de lokale back-up is voltooid door de runtime), wordt de back-up van de service aangeroepen.  Deze retour aanroep is verantwoordelijk voor het verplaatsen van de map backup naar een externe locatie, zoals Azure Storage.

### <a name="restore"></a>Herstellen
De betrouw bare status beheerder biedt de mogelijkheid om vanaf een back-up terug te zetten met behulp van de `RestoreAsync` API.  
De `RestoreAsync` methode on `RestoreContext` kan alleen binnen de methode worden aangeroepen `OnDataLossAsync` .
De Boole-waarde die wordt geretourneerd door `OnDataLossAsync` geeft aan of de service de status van een externe bron heeft hersteld.
Als de `OnDataLossAsync` waarde True retourneert, worden alle andere replica's van deze primaire replica door service Fabric opnieuw opgebouwd. Service Fabric zorgt ervoor dat replica's die `OnDataLossAsync` de eerste overgang naar de primaire rol ontvangen, maar geen Lees status of schrijf status krijgen.
Dit betekent dat voor StatefulService Implementers `RunAsync` niet wordt aangeroepen voordat de implementatie `OnDataLossAsync` is voltooid.
Vervolgens `OnDataLossAsync` wordt de nieuwe primaire.
Totdat een service deze API met succes heeft voltooid (door het retour neren van waar of onwaar) en de relevante herconfiguratie te volt ooien, wordt de API één keer aangeroepen.

`RestoreAsync` eerst wordt de huidige status van de primaire replica waarop deze werd aangeroepen, neergezet. Vervolgens maakt de betrouw bare status beheerder alle betrouw bare objecten die voor komen in de back-upmap. Vervolgens worden de betrouw bare objecten in de back-upmap teruggezet op basis van hun controle punten. Ten slotte herstelt de betrouw bare status beheerder zijn eigen status uit de logboek records in de map backup en voert het herstel uit. Als onderdeel van het herstel proces worden bewerkingen vanaf het ' begin punt ' die vastgelegde logboek records hebben in de back-upmap, opnieuw afgespeeld naar de betrouw bare objecten. Met deze stap zorgt u ervoor dat de herstelde status consistent is.

## <a name="next-steps"></a>Volgende stappen
  - [Betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
  - [Snelstartgids Reliable Services](service-fabric-reliable-services-quick-start.md)
  - [Reliable Services meldingen](service-fabric-reliable-services-notifications.md)
  - [Reliable Services configuratie](service-fabric-reliable-services-configuration.md)
  - [Naslag informatie voor ontwikkel aars voor betrouw bare verzamelingen](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  - [Periodic backup and restore in Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md) (Periodieke back-up en herstel in Azure Service Fabric)
