---
title: Geavanceerde onderwerpen over toepassings upgrades
description: In dit artikel vindt u een aantal geavanceerde onderwerpen met betrekking tot het bijwerken van een Service Fabric-toepassing.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6604300328f2d243077ba341a9028221438dce9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98792045"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric toepassings upgrade: geavanceerde onderwerpen

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Service typen toevoegen of verwijderen tijdens een toepassings upgrade

Als een nieuw service type wordt toegevoegd aan een gepubliceerde toepassing als onderdeel van een upgrade, wordt het nieuwe service type toegevoegd aan de geïmplementeerde toepassing. Een dergelijke upgrade heeft geen invloed op een van de service-exemplaren die al deel uitmaken van de toepassing, maar er moet een exemplaar van het toegevoegde service type worden gemaakt voor het nieuwe service type om actief te zijn (Zie [New-ServiceFabricService](/powershell/module/servicefabric/new-servicefabricservice)).

Daarnaast kunnen service typen uit een toepassing worden verwijderd als onderdeel van een upgrade. Alle service-exemplaren van het Service type to-to-remove moeten worden verwijderd voordat u kunt door gaan met de upgrade (Zie [Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Voor komen dat verbinding wordt verbroken tijdens stateless service geplande downtime

Voor geplande downtime van stateless instanties, zoals het upgraden van toepassingen of clusters of het deactiveren van knoop punten, kunnen verbindingen worden verwijderd wanneer het weer gegeven eind punt wordt verwijderd nadat het exemplaar is uitgeschakeld, wat resulteert in geforceerde-verbinding.

Om dit te voor komen, moet u de functie *RequestDrain* configureren door een *vertraagde tijds duur* voor het sluiten van een exemplaar toe te voegen in de service configuratie zodat bestaande aanvragen in het cluster kunnen worden geafvoer op de weer gegeven eind punten. Dit wordt bereikt omdat het eind punt dat door het stateless exemplaar wordt geadverteerd, wordt verwijderd *voordat* de vertraging begint voordat het exemplaar wordt gesloten. Deze vertraging maakt het mogelijk om bestaande aanvragen op de juiste wijze af te zuigen voordat het exemplaar wordt uitgeschakeld. Clients wordt op de hoogte gesteld van de wijziging van het eind punt door een call back-functie op het moment dat de vertraging wordt gestart, zodat ze het eind punt opnieuw kunnen oplossen en voor komen dat nieuwe aanvragen naar het exemplaar worden verzonden. Deze aanvragen kunnen afkomstig zijn van clients die gebruikmaken van een [reverse proxy](./service-fabric-reverseproxy.md) of het gebruik van service-eindpunt resolutie-api's met het notification model ([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) voor het bijwerken van de eind punten.

### <a name="service-configuration"></a>Serviceconfiguratie

Er zijn verschillende manieren om de vertraging aan de kant van de service te configureren.

 * Geef **bij het maken van een nieuwe service** een `-InstanceCloseDelayDuration` :

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * Wijs **tijdens het definiëren van de service in het gedeelte standaard instellingen in het manifest van de toepassing** de `InstanceCloseDelayDurationSeconds` eigenschap toe:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Wanneer u een bestaande service bijwerkt**, geeft u een `-InstanceCloseDelayDuration` :

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **Wanneer u een bestaande service via de arm-sjabloon maakt of bijwerkt**, geeft u de `InstanceCloseDelayDuration` waarde op (mini maal ondersteunde API-versie: 2019-11-01-preview):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2019-11-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>Clientconfiguratie

Als u een melding wilt ontvangen wanneer een eind punt is gewijzigd, moeten clients een call back registreren Zie [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
De wijzigings melding geeft aan dat de eind punten zijn gewijzigd. de client moet de eind punten opnieuw omzetten en de eind punten die niet meer worden geadverteerd, niet gebruiken, omdat ze binnenkort uitkomen.

### <a name="optional-upgrade-overrides"></a>Optionele upgrade onderdrukkingen

Naast het instellen van de standaard vertragings duur per service, kunt u ook de vertraging negeren tijdens het upgraden van de toepassing/cluster met behulp van dezelfde ( `InstanceCloseDelayDurationSec` )-optie:

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

De genegeerde vertragings duur is alleen van toepassing op het aangeroepen upgrade-exemplaar en wijzigt de afzonderlijke service vertragings configuraties niet. U kunt dit bijvoorbeeld gebruiken om een vertraging van op te geven `0` om vooraf geconfigureerde upgrade vertragingen over te slaan.

> [!NOTE]
> * De instellingen voor afvoer aanvragen kunnen niet voor komen dat de Azure Load Balancer nieuwe aanvragen verzendt naar de eind punten die worden verwijderd.
> * Een oplossings mechanisme op basis van een klacht leidt niet tot een probleemloze afvoer van aanvragen, omdat er na een storing een service oplossing wordt geactiveerd. Zoals eerder beschreven, moet dit in plaats daarvan worden uitgebreid om u te abonneren op de wijzigings meldingen voor eind punten met behulp van [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
> * De instellingen worden niet gehonoreerd wanneer de upgrade van invloed is op een Wanneer de replica's tijdens de upgrade niet worden uitgevoerd.
>
>

> [!NOTE]
> Deze functie kan worden geconfigureerd in bestaande services met Update-ServiceFabricService cmdlet of de ARM-sjabloon zoals hierboven wordt vermeld, wanneer de cluster code versie 7.1.XXX of hoger is.
>
>

## <a name="manual-upgrade-mode"></a>Modus hand matig bijwerken

> [!NOTE]
> De *bewaakte* upgrade modus wordt aanbevolen voor alle service Fabric upgrades.
> De *UnmonitoredManual* -upgrade modus mag alleen worden overwogen voor mislukte of onderbroken upgrades. 
>
>

In de *bewaakte* modus past service Fabric status beleid toe om ervoor te zorgen dat de toepassing in orde is terwijl de upgrade wordt uitgevoerd. Als het status beleid wordt geschonden, wordt de upgrade opgeschort of automatisch teruggedraaid, afhankelijk van de opgegeven *FailureAction*.

In de *UnmonitoredManual* -modus heeft de toepassings beheerder de volledige controle over de voortgang van de upgrade. Deze modus is handig bij het Toep assen van aangepaste beleids regels voor status evaluatie of het uitvoeren van niet-conventionele upgrades om de status controle volledig over te slaan (de toepassing is al in gegevens verlies). Een upgrade die in deze modus wordt uitgevoerd, wordt vanzelf onderbroken na het volt ooien van elk UD en moet expliciet worden hervat met behulp van [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade). Wanneer een upgrade is onderbroken en klaar is om door de gebruiker te worden hervat, wordt in de upgrade status *RollforwardPending* weer gegeven (Zie [UpgradeState](/dotnet/api/system.fabric.applicationupgradestate)).

Ten slotte is de *UnmonitoredAuto* -modus handig voor het uitvoeren van snelle upgrade iteraties tijdens service ontwikkeling of tests, omdat er geen gebruikers invoer is vereist en er geen beleids regels voor de status van de toepassing worden geëvalueerd.

## <a name="upgrade-with-a-diff-package"></a>Upgrade uitvoeren met een diff-pakket

In plaats van een volledig toepassings pakket in te richten, kunnen ook upgrades worden uitgevoerd door diff-pakketten in te richten die alleen de bijgewerkte code/config/data-pakketten bevatten, samen met het volledige toepassings manifest en de service manifesten te volt ooien. Volledige toepassings pakketten zijn alleen vereist voor de eerste installatie van een toepassing naar het cluster. Volgende upgrades kunnen van toepassing zijn: volledige toepassings pakketten of diff-pakketten.  

Verwijzingen in het manifest van de toepassing of service manifesten van een diff-pakket dat niet kan worden gevonden in het toepassings pakket, worden automatisch vervangen door de momenteel ingerichte versie.

Scenario's voor het gebruik van een diff-pakket zijn:

* Wanneer u een groot toepassings pakket hebt dat verwijst naar verschillende service manifest bestanden en/of verschillende code pakketten, configuratie pakketten of gegevens pakketten.
* Wanneer u een implementatie systeem hebt waarmee de build-indeling rechtstreeks vanuit het bouw proces van uw toepassing wordt gegenereerd. In dit geval, zelfs als de code nog niet is gewijzigd, krijgen recent gebouwde assembly's een andere controlesom. Als u een volledig toepassings pakket gebruikt, moet u de versie op alle code pakketten bijwerken. Als u een diff-pakket gebruikt, geeft u alleen de bestanden op die zijn gewijzigd en de manifest bestanden waarin de versie is gewijzigd.

Wanneer een toepassing wordt bijgewerkt met behulp van Visual Studio, wordt er automatisch een diff-pakket gepubliceerd. Als u een diff-pakket hand matig wilt maken, moeten het manifest van de toepassing en de service manifesten worden bijgewerkt, maar alleen de gewijzigde pakketten moeten worden opgenomen in het uiteindelijke toepassings pakket.

Laten we bijvoorbeeld beginnen met de volgende toepassing (versie nummers die zijn verschaft voor een begrijpelijke uitleg):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

We gaan ervan uit dat u alleen het code pakket van Service1 wilt bijwerken met behulp van een diff-pakket. De bijgewerkte toepassing heeft de volgende versie wijzigingen:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

In dit geval werkt u het toepassings manifest bij naar 2.0.0 en het service manifest voor Service1 in overeenstemming met de code pakket update. De map voor uw toepassings pakket zou de volgende structuur hebben:

```text
app1/
  service1/
    code/
```

Met andere woorden, maak normaal een volledig toepassings pakket en verwijder vervolgens alle mappen met code/config/gegevens pakketten waarvoor de versie niet is gewijzigd.

## <a name="upgrade-application-parameters-independently-of-version"></a>Toepassings parameters onafhankelijk van versie bijwerken

Soms is het wenselijk om de para meters van een Service Fabric toepassing te wijzigen zonder de manifest versie te wijzigen. Dit kan handig worden gedaan met behulp van de vlag **-ApplicationParameter** met de **Start-ServiceFabricApplicationUpgrade** Azure service Fabric Power shell-cmdlet. Stel dat er een Service Fabric toepassing met de volgende eigenschappen:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Voer nu een upgrade uit voor de toepassing met behulp van de cmdlet **Start-ServiceFabricApplicationUpgrade** . In dit voor beeld wordt een bewaakte upgrade weer gegeven, maar een niet-bewaakte upgrade kan ook worden gebruikt. Zie de [Naslag Gids voor Azure service Fabric Power shell-modules](/powershell/module/servicefabric/start-servicefabricapplicationupgrade#parameters) voor een volledige beschrijving van de vlaggen die door deze cmdlet worden geaccepteerd

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Nadat u de upgrade hebt uitgevoerd, controleert u of de toepassing de bijgewerkte para meters en dezelfde versie heeft:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Upgrades van toepassingen terugdraaien

Hoewel upgrades kunnen worden doorgevoerd in een van de drie modi (*bewaakt*, *UnmonitoredAuto* of *UnmonitoredManual*), kunnen ze alleen worden teruggedraaid in de modus *UnmonitoredAuto* of *UnmonitoredManual* . Terugdraaien in de *UnmonitoredAuto* -modus werkt op dezelfde manier als met de uitzonde ring dat de standaard waarde van *UpgradeReplicaSetCheckTimeout* verschilt-Zie [para meters](service-fabric-application-upgrade-parameters.md)voor de upgrade van de toepassing. Terugdraaien in de *UnmonitoredManual* -modus werkt op dezelfde manier als vooruit draaien: de terugdraai actie wordt na elke UD onderbroken en moet expliciet worden hervat met behulp van [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade) om door te gaan met het terugdraaien.

Terugdraai bewerkingen kunnen automatisch worden geactiveerd wanneer het status beleid van een upgrade in de *bewaakte* modus met een *FailureAction* van het *terugdraaien* is geschonden (Zie [para meters](service-fabric-application-upgrade-parameters.md)voor de upgrade van de toepassing) of expliciet gebruikmaakt van [Start-ServiceFabricApplicationRollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback).

Tijdens het terugdraaien kan de waarde van *UpgradeReplicaSetCheckTimeout* en de modus op elk gewenst moment nog steeds worden gewijzigd met behulp van [Update-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/update-servicefabricapplicationupgrade).

## <a name="next-steps"></a>Volgende stappen
Als u een [upgrade uitvoert van uw toepassing met behulp van Visual Studio](service-fabric-application-upgrade-tutorial.md) , wordt u begeleid bij een toepassings upgrade met Visual Studio.

Als u uw toepassing bijwerkt [met Power shell](service-fabric-application-upgrade-tutorial-powershell.md) , kunt u een toepassings upgrade uitvoeren met behulp van Power shell.

Bepalen hoe uw toepassing wordt bijgewerkt met behulp van [upgrade parameters](service-fabric-application-upgrade-parameters.md).

Maak uw toepassings upgrades compatibel door te leren hoe u [gegevens serialisatie](service-fabric-application-upgrade-data-serialization.md)gebruikt.

Corrigeer veelvoorkomende problemen in toepassings upgrades door te verwijzen naar de stappen in [Troubleshooting Application upgrades](service-fabric-application-upgrade-troubleshooting.md).
