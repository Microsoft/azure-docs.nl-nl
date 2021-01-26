---
title: Timers en herinneringen Reliable Actors
description: Inleiding tot timers en herinneringen voor Service Fabric Reliable Actors, met inbegrip van de richt lijnen voor het gebruik ervan.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: f77eb29c9146fe66d5d2b6073c33e30fbab649c2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791791"
---
# <a name="actor-timers-and-reminders"></a>Actor timers en herinneringen
Actors kunnen periodiek werk op zichzelf plannen door timers of herinneringen te registreren. Dit artikel laat u zien hoe u timers en herinneringen kunt gebruiken en hoe u de verschillen tussen deze time-out.

## <a name="actor-timers"></a>Actor timers
Actor timers bieden een eenvoudige wrapper rond een .NET-of Java-tijd om ervoor te zorgen dat de call back-methoden de op zichzelf gebaseerde gelijktijdigheids garanties eerbiedigen die door de actors-runtime worden geboden.

Actors kunnen gebruikmaken `RegisterTimer` van de methoden (c#) of (Java) en (Java) `registerTimer` en ( `UnregisterTimer` c#) of de methode voor het `unregisterTimer` registreren van hun timers en de registratie ervan ongedaan maken. In het onderstaande voor beeld ziet u het gebruik van timer-Api's. De Api's zijn vergelijkbaar met de .NET-timer of Java-timer. In dit voor beeld, wanneer de timer is voltooid, roept de actors-runtime de `MoveObject` (C#) of `moveObject` (Java)-methode aan. De methode is gegarandeerd om te voldoen aan de op te slaan gebaseerde gelijktijdigheid. Dit betekent dat er geen andere actor-methoden of timer/herinnering-retour aanroepen worden uitgevoerd totdat deze retour aanroep is voltooid.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

De volgende periode van de timer begint nadat de retour aanroep is voltooid. Dit betekent dat de timer wordt gestopt tijdens het uitvoeren van de call back en wordt gestart wanneer de call back is voltooid.

Met de functie actors worden wijzigingen opgeslagen die zijn aangebracht in de status Manager van de actor wanneer de call back is voltooid. Als er een fout optreedt bij het opslaan van de status, wordt dat actor-object gedeactiveerd en wordt er een nieuw exemplaar geactiveerd.

In tegens telling tot [herinneringen](#actor-reminders)kunnen timers niet worden bijgewerkt. Als `RegisterTimer` opnieuw wordt aangeroepen, wordt een nieuwe timer geregistreerd.

Alle timers worden gestopt wanneer de actor wordt gedeactiveerd als onderdeel van de garbagecollection. Er worden geen timer retour aanroepen uitgevoerd. De actors-runtime bewaart ook geen informatie over de timers die werden uitgevoerd vóór het deactiveren. Het is aan de actor om timers te registreren die nodig zijn wanneer deze in de toekomst opnieuw wordt geactiveerd. Zie de sectie over [actor-garbage](service-fabric-reliable-actors-lifecycle.md)Collection voor meer informatie.

## <a name="actor-reminders"></a>Herinneringen voor Actors
Herinneringen zijn een mechanisme voor het activeren van permanente retour aanroepen op een actor op opgegeven tijden. De functionaliteit is vergelijkbaar met timers. Maar in tegens telling tot timers worden herinneringen onder alle omstandigheden geactiveerd totdat de actor expliciet de registratie ervan ongedaan maakt of de actor expliciet is verwijderd. In het bijzonder worden herinneringen geactiveerd voor het deactiveren van actors en failovers, omdat de actors-runtime informatie persistent maakt over de herinneringen van de actor met de actor State-provider. Ook als timers kunnen bestaande herinneringen worden bijgewerkt door de registratie methode () opnieuw aan te roepen `RegisterReminderAsync` met dezelfde *aanmaningskop*.

> [!NOTE]
> De betrouw baarheid van herinneringen is gebonden aan de betrouw baarheid van de status van de actor State-provider. Dit betekent dat voor actors waarvan de status persistentie is ingesteld op *none*, de herinneringen na een failover niet worden geactiveerd.

Als u een herinnering wilt registreren, roept een actor de [`RegisterReminderAsync`](/dotnet/api/microsoft.servicefabric.actors.runtime.actorbase.registerreminderasync#remarks) methode aan die is gegeven in de basis klasse, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

In dit voor beeld `"Pay cell phone bill"` is de naam van de herinnering. Dit is een teken reeks die door de actor wordt gebruikt om een herinnering te identificeren. `BitConverter.GetBytes(amountInDollars)`(C#) is de context die is gekoppeld aan de herinnering. Deze wordt teruggestuurd naar de actor als een argument voor de retour aanroep van de herinnering `IRemindable.ReceiveReminderAsync` (C#) of `Remindable.receiveReminderAsync` (Java).

Actors die gebruikmaken van herinneringen moeten de `IRemindable` interface implementeren, zoals wordt weer gegeven in het onderstaande voor beeld.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Wanneer een herinnering wordt geactiveerd, roept de Reliable Actors runtime de  `ReceiveReminderAsync` methode (C#) of `receiveReminderAsync` (Java) aan voor de actor. Een actor kan meerdere herinneringen registreren en de `ReceiveReminderAsync` methode (C#) of `receiveReminderAsync` (Java) wordt aangeroepen wanneer een van deze herinneringen wordt geactiveerd. De actor kan de naam van de herinnering gebruiken die wordt door gegeven aan de `ReceiveReminderAsync` methode (C#) of `receiveReminderAsync` (Java) om te bepalen welke herinnering is geactiveerd.

Met de functie actors wordt de status van de actor opgeslagen wanneer de `ReceiveReminderAsync` aanroep (C#) of `receiveReminderAsync` (Java) is voltooid. Als er een fout optreedt bij het opslaan van de status, wordt dat actor-object gedeactiveerd en wordt er een nieuw exemplaar geactiveerd.

Als u de registratie van een herinnering ongedaan wilt maken, roept een actor de `UnregisterReminderAsync` methode (C#) of `unregisterReminderAsync` (Java) aan, zoals wordt weer gegeven in de onderstaande voor beelden.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Zoals hierboven wordt weer gegeven, `UnregisterReminderAsync` accepteert de (c#) of `unregisterReminderAsync` (Java)-methode een `IActorReminder` (c#) of (Java)- `ActorReminder` interface. De klasse actor Base ondersteunt een `GetReminder` (c#) of `getReminder` (Java)-methode die kan worden gebruikt voor het ophalen van de `IActorReminder` (c#) of `ActorReminder` (Java)-interface door door gegeven in de naam van de herinnering. Dit is handig omdat de actor de `IActorReminder` Interface (c#) of Java niet hoeft te behouden `ActorReminder` die is geretourneerd door de aanroep van de `RegisterReminder` methode (c#) of `registerReminder` Java.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over betrouw bare actor gebeurtenissen en herbetreedbaarheid:
* [Actor gebeurtenissen](service-fabric-reliable-actors-events.md)
* [Actor herbetreedbaarheid](service-fabric-reliable-actors-reentrancy.md)
