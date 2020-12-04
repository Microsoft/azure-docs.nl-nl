---
title: Beheer van Reliable Actors status
description: Hierin wordt beschreven hoe de status van Reliable Actors beheerd, persistent wordt gemaakt en wordt gerepliceerd voor hoge Beschik baarheid.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: badfc490f26b71881e7970c2c0be3472abfec25a
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575598"
---
# <a name="reliable-actors-state-management"></a>Beheer van Reliable Actors status
Reliable Actors zijn objecten met één thread die zowel logica als status kunnen inkapselen. Omdat actors worden uitgevoerd op Reliable Services, kunnen ze de status betrouwbaar onderhouden met behulp van dezelfde persistentie-en replicatie mechanismen. Op deze manier verliezen actors hun status na storingen, na het opnieuw activeren na het opschonen van de garbagecollection, of wanneer ze tussen knoop punten in een cluster worden verplaatst vanwege de resource verdeling of-upgrades.

## <a name="state-persistence-and-replication"></a>Status persistentie en replicatie
Alle Reliable Actors worden beschouwd als *stateful* omdat elk actor-exemplaar wordt toegewezen aan een unieke id. Dit betekent dat herhaalde aanroepen naar dezelfde actor-ID worden doorgestuurd naar hetzelfde actor-exemplaar. In een stateless systeem worden client aanroepen daarentegen niet altijd elke keer naar dezelfde server gerouteerd. Daarom zijn actor Services altijd stateful Services.

Hoewel actors als stateful worden beschouwd, betekent dat niet dat ze status betrouwbaar moeten opslaan. Actors kunnen het niveau van status persistentie en replicatie bepalen op basis van de vereisten voor de opslag van gegevens:

* **Permanente status**: de status wordt persistent gemaakt op de schijf en wordt gerepliceerd naar drie of meer replica's. Persistente status is de meest duurzame status opslag optie, waar de status kan voortduren door de onderbreking van het volledige cluster.
* **Vluchtige status**: de status wordt gerepliceerd naar drie of meer replica's en wordt alleen in het geheugen bewaard. Een vluchtige status biedt een tolerantie tegen storingen in knoop punten en actor-fouten, en tijdens upgrades en resource verdeling. De status wordt echter niet opgeslagen op schijf. Als alle replica's in één keer verloren gaan, gaat de status ook verloren.
* **Geen persistente status**: de status wordt niet gerepliceerd of naar de schijf geschreven, alleen voor Actors die geen betrouw bare status hoeven te onderhouden.

Elk niveau van persistentie is gewoon een andere *State-provider* en *replicatie* configuratie van uw service. Of de status naar de schijf wordt geschreven, is afhankelijk van de State-provider, het onderdeel in een betrouw bare service waarin de status wordt opgeslagen. Replicatie is afhankelijk van het aantal replica's waarvoor een service is geïmplementeerd. Net als bij Reliable Services kunnen zowel de status provider als het aantal replica's eenvoudig hand matig worden ingesteld. Het actor-Framework bevat een kenmerk dat, wanneer dit wordt gebruikt voor een actor, automatisch een standaard status provider selecteert en automatisch instellingen voor replica aantal genereert om een van deze drie persistentie-instellingen te krijgen. Het kenmerk StatePersistence wordt niet overgenomen door de afgeleide klasse, elk actor type moet het StatePersistence niveau opgeven.

### <a name="persisted-state"></a>Permanente status
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Deze instelling maakt gebruik van een State-provider waarmee gegevens op schijf worden opgeslagen en het aantal service replica's automatisch wordt ingesteld op 3.

### <a name="volatile-state"></a>Vluchtige status
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Deze instelling maakt gebruik van een status provider met alleen geheugen en stelt het aantal replica's in op 3.

### <a name="no-persisted-state"></a>Status niet persistent
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Deze instelling maakt gebruik van een status provider met alleen geheugen en stelt het aantal replica's in op 1.

### <a name="defaults-and-generated-settings"></a>Standaard waarden en gegenereerde instellingen
Wanneer u het kenmerk gebruikt `StatePersistence` , wordt tijdens runtime automatisch een status provider geselecteerd wanneer de actor-service wordt gestart. Het aantal replica's wordt echter ingesteld op het tijdstip van compilatie door de Visual Studio actor build-hulpprogram ma's. De build-hulpprogram ma's genereren automatisch een *standaard service* voor de actor-service in ApplicationManifest.xml. Er zijn para meters gemaakt voor de minimale grootte van de **replicaset** en de grootte van de **doel replicaset**.

U kunt deze para meters hand matig wijzigen. Maar telkens wanneer het `StatePersistence` kenmerk wordt gewijzigd, worden de para meters ingesteld op de standaard waarden voor de replicaset voor het geselecteerde `StatePersistence` kenmerk, waarbij alle vorige waarden worden overschreven. Met andere woorden: de waarden die u in ServiceManifest.xml instelt, worden tijdens het bouwen *alleen* overschreven wanneer u de `StatePersistence` waarde van het kenmerk wijzigt.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Status Manager
Elk actor-exemplaar heeft een eigen status Manager: een gegevens structuur op basis van een woorden lijst die op betrouw bare wijze sleutel/waarde-paren opslaat. De status Manager is een wrapper rond een State-provider. U kunt deze gebruiken om gegevens op te slaan, ongeacht welke persistentie-instelling wordt gebruikt. Het biedt geen garanties dat een actieve actor-service kan worden gewijzigd van een veranderlijke status instelling (in-memory-only) naar een blijvende status instelling via een rolling upgrade bij het bewaren van gegevens. Het is echter mogelijk om het aantal replica's voor een actieve service te wijzigen.

State Manager-sleutels moeten teken reeksen zijn. Waarden zijn Gene riek en kunnen elk type zijn, inclusief aangepaste typen. Waarden die zijn opgeslagen in de status Manager moeten gegevens contract serialiseerbaar zijn omdat ze via het netwerk kunnen worden verzonden naar andere knoop punten tijdens de replicatie en kunnen worden geschreven naar de schijf, afhankelijk van de instelling van de status persistentie van een actor.

De status Manager stelt algemene woordenboek methoden beschikbaar voor het beheren van de status, vergelijkbaar met die in de betrouw bare woorden lijst.

Voor voor beelden van het beheren van de actor status, Lees [toegang, opslaan en verwijderen reliable actors status](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Aanbevolen procedures
Hier volgen enkele aanbevolen procedures en tips voor het oplossen van problemen met het beheren van uw actor status.

### <a name="make-the-actor-state-as-granular-as-possible"></a>De actor status zo nauw keurig mogelijk maken
Dit is essentieel voor het prestatie-en resource gebruik van uw toepassing. Wanneer er een schrijf-of-update is naar de ' naam status ' van een actor, wordt de gehele waarde die overeenkomt met de ' named ', geserialiseerd en via het netwerk verzonden naar de secundaire replica's.  Met de secundaire zones schrijft u deze naar een lokale schijf en beantwoordt u deze aan de primaire replica. Wanneer de primaire bevestigingen ontvangt van een quorum van secundaire replica's, wordt de status naar de lokale schijf geschreven. Stel bijvoorbeeld dat de waarde een klasse is die 20 leden en een grootte van 1 MB heeft. Zelfs als u slechts een van de klassen leden hebt gewijzigd met een grootte van 1 KB, kunt u de kosten van serialisatie en netwerk-en schijf schrijf bewerkingen voor de volledige 1 MB betalen. En als de waarde een verzameling is (zoals een lijst, matrix of woorden lijst), betaalt u de kosten voor de volledige verzameling, zelfs als u een van de leden van de groep wijzigt. De StateManager-interface van de actor-klasse is als een woorden lijst. U moet de gegevens structuur die de actor status boven op deze woorden lijst weergeeft, altijd model leren.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>De levens cyclus van de actor goed beheren
U moet beleid wissen over het beheren van de grootte van de status in elke partitie van een actor service. Uw actor service moet een vast aantal actors hebben en deze zo veel mogelijk opnieuw gebruiken. Als u voortdurend nieuwe actors maakt, moet u deze verwijderen zodra ze klaar zijn met hun werk. Het actor-Framework bevat enkele meta gegevens over elke actor die bestaat. Als u de status van een actor verwijdert, worden de meta gegevens over die actor niet verwijderd. U moet de actor verwijderen (Zie [actors en hun status](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)verwijderen) om alle gegevens te verwijderen die zijn opgeslagen in het systeem. Als extra controle moet u een query uitvoeren op de actor-service (Zie [actors opsommen](service-fabric-reliable-actors-enumerate.md)) eenmaal in een tijdje om te controleren of het aantal actors binnen het verwachte bereik valt.
 
Als u ooit ziet dat de database bestands grootte van een actor service groter wordt dan de verwachte grootte, controleert u of u de voor gaande richt lijnen volgt. Als u deze richt lijnen volgt en er nog steeds problemen met database bestands grootte ondervindt, moet u [een ondersteunings ticket openen](service-fabric-support.md) met het product team om hulp te krijgen.

## <a name="next-steps"></a>Volgende stappen

De status die is opgeslagen in Reliable Actors moet worden geserialiseerd voordat deze naar de schijf wordt geschreven en kan worden gerepliceerd voor hoge Beschik baarheid. Meer informatie over het [serialiseren van het type actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Klik vervolgens op meer informatie over [actor Diagnostics en prestatie bewaking](service-fabric-reliable-actors-diagnostics.md).
