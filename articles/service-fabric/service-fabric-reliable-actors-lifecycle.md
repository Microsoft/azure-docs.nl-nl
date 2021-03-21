---
title: Overzicht van de levens cyclus van Azure Service Fabric actor
description: Hierin worden de Service Fabric betrouw bare actor levenscyclus, garbage collection en hand matig verwijderen van actors en hun status uitgelegd
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 88db4bb2376cbc418d6954e274a18a6c18a280d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576040"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Actor-levens cyclus, automatische garbage collection en hand matig verwijderen
Er wordt een actor geactiveerd wanneer een aanroep voor de eerste keer wordt uitgevoerd naar een van de methoden. Een actor wordt gedeactiveerd (garbagecollection door de actors-runtime) als deze niet wordt gebruikt voor een Configureer bare periode. Een actor en de status kunnen op elk gewenst moment hand matig worden verwijderd.

## <a name="actor-activation"></a>Actor-activering
Wanneer een actor wordt geactiveerd, gebeurt het volgende:

* Wanneer een aanroep voor een actor komt en er nog niet actief is, wordt er een nieuwe actor gemaakt.
* De status van de actor wordt geladen als de status wordt gehandhaafd.
* De `OnActivateAsync` (C#) of `onActivateAsync` (Java)-methode (die kan worden overschreven in de actor-implementatie) wordt aangeroepen.
* De actor wordt nu als actief beschouwd.

## <a name="actor-deactivation"></a>Actor deactivering
Wanneer een actor wordt gedeactiveerd, gebeurt het volgende:

* Wanneer een actor gedurende een bepaalde periode niet wordt gebruikt, wordt deze verwijderd uit de actieve tabel actors.
* De `OnDeactivateAsync` (C#) of `onDeactivateAsync` (Java)-methode (die kan worden overschreven in de actor-implementatie) wordt aangeroepen. Hiermee worden alle timers voor de actor gewist. Actor-bewerkingen zoals status wijzigingen mogen niet worden aangeroepen vanuit deze methode.

> [!TIP]
> De Fabric actors-runtime verzendt enkele [gebeurtenissen met betrekking tot de activering en deactivering van actor](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Ze zijn handig voor diagnostische en prestatie bewaking.
>
>

### <a name="actor-garbage-collection"></a>Verzameling actor-garbagecollection
Wanneer een actor wordt gedeactiveerd, worden verwijzingen naar het actor-object vrijgegeven en kunnen deze permanent worden verzameld door de Common Language Runtime (CLR) of Java Virtual Machine (JVM) garbage collector. Garbagecollection ruimt alleen het actor-object op. de status die is opgeslagen in de status Manager van de actor wordt **niet** verwijderd. De volgende keer dat de actor wordt geactiveerd, wordt er een nieuw actor-object gemaakt en de status ervan wordt hersteld.

Wat is de waarde die wordt gebruikt voor het deactiveren en opschonen van de garbagecollection?

* Een gesprek ontvangen
* `IRemindable.ReceiveReminderAsync` de methode die wordt aangeroepen (alleen van toepassing als de actor gebruikmaakt van herinneringen)

> [!NOTE]
> Als de actor timers gebruikt en de retour aanroep wordt aangeroepen, wordt deze **niet** geteld als ' wordt gebruikt '.
>
>

Voordat we de details van deactivering gaan bekijken, is het belang rijk om de volgende voor waarden te definiëren:

* *Scan interval*. Dit is het interval waarmee de actors-runtime de actieve actors tabel scant voor Actors die kunnen worden gedeactiveerd en opgeruimd. De standaard waarde voor dit is 1 minuut.
* *Time-out voor inactiviteit*. Dit is de hoeveelheid tijd die een actor moet blijven ongebruikt (inactief) voordat deze kan worden gedeactiveerd en opgeruimd. De standaard waarde voor dit is 60 minuten.

Normaal gesp roken hoeft u deze standaard waarden niet te wijzigen. Indien nodig kunnen deze intervallen echter worden gewijzigd via `ActorServiceSettings` bij het registreren van uw [actor service](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Voor elke actieve actor houdt actor runtime bij hoe lang het inactief is (d.w.z. niet gebruikt). De actor-runtime controleert elk van de actors `ScanIntervalInSeconds` om te zien of deze permanent kan worden opgeruimd en gemarkeerd als deze niet actief is geweest `IdleTimeoutInSeconds` .

Telkens wanneer een actor wordt gebruikt, wordt de niet-actieve tijd opnieuw ingesteld op 0. Daarna kan de actor alleen permanent worden verzameld als deze opnieuw inactief blijft `IdleTimeoutInSeconds` . Het intrekken van een actor als een actor-interface methode of een actor-retour aanroep wordt uitgevoerd. Een actor wordt **niet** beschouwd als gebruikt als de timer-retour aanroep wordt uitgevoerd.

In het volgende diagram ziet u de levens cyclus van één actor om deze concepten te illustreren.

![Voor beeld van niet-actieve tijd][1]

In het voor beeld wordt de impact van actor-methode aanroepen, herinneringen en timers weer gegeven voor de levens duur van deze actor. De volgende punten over het voor beeld zijn vermeld:

* Scan interval en IdleTimeout zijn respectievelijk ingesteld op 5 en 10. (De eenheden zijn hier niet van belang, omdat ons doel alleen is om het concept te illustreren.)
* De scan voor Actors die moeten worden opgeruimd, gebeurt op T = 0, 5, 10, 15, 20, 25, zoals gedefinieerd door het Scan interval van 5.
* Een periodieke timer wordt geactiveerd bij T = 4, 8, 12, 16, 20, 24 en de retour aanroep wordt uitgevoerd. Dit heeft geen invloed op de niet-actieve tijd van de actor.
* Een actor-methode aanroep op T = 7 stelt de niet-actieve tijd in op 0 en vertraagt de garbage collection van de actor.
* Een actor-retour aanroep wordt uitgevoerd op T = 14 en vertraagt de garbage collection van de actor.
* Tijdens de garbagecollection-scan op T = 25 wordt de niet-actieve tijd van de actor langer dan de niet-actieve time-out van 10, en wordt de actor permanent verzameld.

Een actor wordt nooit opgeruimd tijdens het uitvoeren van een van de methoden, ongeacht de hoeveelheid tijd die nodig is om deze methode uit te voeren. Zoals eerder vermeld, voor komt de uitvoering van actor-interface methoden en herinnerings retour aanroepen garbagecollection door de niet-actieve tijd van de actor opnieuw in te stellen op 0. Bij het uitvoeren van timer-Call backs wordt de niet-actieve tijd niet opnieuw ingesteld op 0. De garbage collection van de actor wordt echter uitgesteld tot de timer-Call back is voltooid.

## <a name="manually-deleting-actors-and-their-state"></a>Actors en hun status hand matig verwijderen
Garbagecollection van gedeactiveerde actors reinigt alleen het actor-object, maar verwijdert geen gegevens die zijn opgeslagen in de status Manager van een actor. Wanneer een actor opnieuw wordt geactiveerd, worden de bijbehorende gegevens opnieuw beschikbaar gemaakt via de status Manager. In gevallen waarin actors gegevens opslaan in de status Manager en worden gedeactiveerd, maar nooit opnieuw worden geactiveerd, kan het nodig zijn om de gegevens op te schonen.  Voor voor beelden van hoe actors moeten worden verwijderd, leest u [actors verwijderen en hun status](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Volgende stappen
* [Actor timers en herinneringen](service-fabric-reliable-actors-timers-reminders.md)
* [Actor gebeurtenissen](service-fabric-reliable-actors-events.md)
* [Actor herbetreedbaarheid](service-fabric-reliable-actors-reentrancy.md)
* [De functie voor het controleren van actor en prestaties](service-fabric-reliable-actors-diagnostics.md)
* [Referentie documentatie voor actor-API](/previous-versions/azure/dn971626(v=azure.100))
* [C#-voorbeeld code](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-voorbeeld code](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
