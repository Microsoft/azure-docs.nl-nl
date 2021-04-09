---
title: Reliable Services meldingen
description: Conceptuele documentatie voor Service Fabric Reliable Services meldingen voor betrouw bare status Manager en betrouw bare woorden lijst
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: f5b48cc6cca2e143c48ed7bdfc99de936be2a227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784576"
---
# <a name="reliable-services-notifications"></a>Reliable Services meldingen
Met meldingen kunnen clients de wijzigingen bijhouden die worden aangebracht in een object waarin ze zijn geïnteresseerd. Twee typen objecten ondersteunen meldingen: *betrouw bare status Manager* en *betrouw bare woorden lijst*.

Veelvoorkomende redenen voor het gebruik van meldingen zijn:

* Het bouwen van gerealiseerde weer gaven, zoals secundaire indexen of geaggregeerde gefilterde weer gaven van de status van de replica. Een voor beeld is een gesorteerde index van alle sleutels in een betrouw bare woorden lijst.
* Het verzenden van bewakings gegevens, zoals het aantal gebruikers dat het afgelopen uur is toegevoegd.

Meldingen worden gestart als onderdeel van het Toep assen van bewerkingen. Als gevolg hiervan moeten meldingen zo snel mogelijk worden verwerkt en synchrone gebeurtenissen mogen geen dure bewerkingen omvatten.

## <a name="reliable-state-manager-notifications"></a>Betrouw bare status Manager-meldingen
Betrouw bare status beheerder biedt meldingen voor de volgende gebeurtenissen:

* Transactie
  * Doorvoeren
* Status Manager
  * Opnieuw bouwen
  * Toevoeging van een betrouw bare status
  * Verwijdering van een betrouw bare status

Betrouw bare status manager houdt de huidige invlucht transacties bij. De enige wijziging in de transactie status waardoor een melding wordt gestart, is een trans actie die wordt vastgelegd.

Betrouw bare status beheerder houdt een verzameling betrouw bare statussen, zoals een betrouw bare dictionary en een betrouw bare wachtrij. Betrouw bare status Manager wordt gebrand meldingen wanneer deze verzameling wordt gewijzigd: er wordt een betrouw bare status toegevoegd of verwijderd, of de volledige verzameling wordt opnieuw opgebouwd.
De betrouw bare status beheer-verzameling wordt in drie gevallen opnieuw opgebouwd:

* Herstel: wanneer een replica wordt gestart, wordt de vorige status van de schijf hersteld. Aan het einde van de herstel bewerking wordt **NotifyStateManagerChangedEventArgs** gebruikt om een gebeurtenis te starten die de set betrouw bare statussen bevat.
* Volledige kopie: voordat een replica kan worden toegevoegd aan de configuratieset, moet deze zijn gebouwd. Soms moet er een volledige kopie van de status van een betrouw bare status Manager van de primaire replica worden toegepast op de niet-actieve secundaire replica. Betrouw bare status Manager voor de secundaire replica maakt gebruik van **NotifyStateManagerChangedEventArgs** om een gebeurtenis te starten die de set betrouw bare statussen bevat die het verkregen is van de primaire replica.
* Herstellen: in scenario's voor herstel na nood gevallen kan de status van de replica worden hersteld vanuit een back-up via **RestoreAsync**. In dergelijke gevallen maakt een betrouw bare status Manager voor de primaire replica gebruik van **NotifyStateManagerChangedEventArgs** om een gebeurtenis te starten die de set betrouw bare statussen bevat die deze van de back-up heeft hersteld.

Als u zich wilt registreren voor transactie meldingen en/of status Manager-meldingen, moet u zich registreren bij de **TransactionChanged** -of **StateManagerChanged** -gebeurtenissen op betrouw bare status Manager. Een gemeen schappelijke plaats voor registratie bij deze gebeurtenis-handlers is de constructor van uw stateful service. Wanneer u zich registreert bij de constructor, mist u geen meldingen die worden veroorzaakt door een wijziging tijdens de levens duur van **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

De gebeurtenis-handler **TransactionChanged** maakt gebruik van **NotifyTransactionChangedEventArgs** om details over de gebeurtenis op te geven. Het bevat de eigenschap Action (bijvoorbeeld **NotifyTransactionChangedAction. commit**) waarmee het type wijziging wordt opgegeven. Het bevat ook de trans actie-eigenschap die een verwijzing geeft naar de trans actie die is gewijzigd.

> [!NOTE]
> Vandaag worden **TransactionChanged** -gebeurtenissen alleen gegenereerd als de trans actie is doorgevoerd. De actie is vervolgens gelijk aan **NotifyTransactionChangedAction. commit**. Maar in de toekomst kunnen gebeurtenissen worden gegenereerd voor andere typen transactie status wijzigingen. U wordt aangeraden de actie te controleren en de gebeurtenis alleen te verwerken als deze wordt verwacht.
> 
> 

Hier volgt een voor beeld van een **TransactionChanged** -gebeurtenis-handler.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

De gebeurtenis-handler **StateManagerChanged** maakt gebruik van **NotifyStateManagerChangedEventArgs** om details over de gebeurtenis op te geven.
**NotifyStateManagerChangedEventArgs** heeft twee subklassen: **NotifyStateManagerRebuildEventArgs** en **NotifyStateManagerSingleEntityChangedEventArgs**.
U gebruikt de actie-eigenschap in **NotifyStateManagerChangedEventArgs** om **NotifyStateManagerChangedEventArgs** naar de juiste subklasse te casten:

* **NotifyStateManagerChangedAction. Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction. add** en **NotifyStateManagerChangedAction. Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Hier volgt een voor beeld van een **StateManagerChanged** -meldings-handler.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Betrouw bare woordenlijst meldingen
Betrouw bare woorden lijst biedt meldingen voor de volgende gebeurtenissen:

* Rebuild: wordt aangeroepen wanneer de status van de **ReliableDictionary** is hersteld van een herstelde of gekopieerde lokale status of back-up.
* Clear: wordt aangeroepen wanneer de status van **ReliableDictionary** is gewist via de methode **ClearAsync** .
* Add: wordt aangeroepen wanneer een item is toegevoegd aan **ReliableDictionary**.
* Update: wordt aangeroepen wanneer een item in **IReliableDictionary** is bijgewerkt.
* Remove: aangeroepen wanneer een item in **IReliableDictionary** is verwijderd.

Als u betrouw bare woordenlijst meldingen wilt ontvangen, moet u zich registreren bij de **DictionaryChanged** -gebeurtenis-handler op **IReliableDictionary**. Een veelvoorkomende plaats voor registratie bij deze gebeurtenis-handlers vindt u in de **ReliableStateManager. StateManagerChanged** -melding toevoegen.
Wanneer **IReliableDictionary** wordt toegevoegd aan **IReliableStateManager** , weet u zeker dat u geen meldingen meer mist.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** is de voorbeeld methode die het voor beeld van de voor gaande **OnStateManagerChangedHandler** -voor beelden aanroept.
> 
> 

Met de voor gaande code wordt de **IReliableNotificationAsyncCallback** -interface ingesteld, samen met **DictionaryChanged**. Omdat **NotifyDictionaryRebuildEventArgs** een **IAsyncEnumerable** -interface bevat, die asynchroon moet worden geïnventariseerd, worden meldingen die opnieuw worden opgebouwd, geactiveerd via **RebuildNotificationAsyncCallback** in plaats van **OnDictionaryChangedHandler**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> In de voor gaande code, als onderdeel van het verwerken van de melding over het opnieuw opbouwen, wordt eerst de status beheerd verzameld gewist. Omdat de betrouw bare verzameling opnieuw wordt gemaakt met een nieuwe status, zijn alle eerdere meldingen niet relevant.
> 
> 

De gebeurtenis-handler **DictionaryChanged** maakt gebruik van **NotifyDictionaryChangedEventArgs** om details over de gebeurtenis op te geven.
**NotifyDictionaryChangedEventArgs** heeft vijf subklassen. Gebruik de eigenschap Action in **NotifyDictionaryChangedEventArgs** om **NotifyDictionaryChangedEventArgs** naar de juiste subklasse te casten:

* **NotifyDictionaryChangedAction. Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction. Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction. add**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction. update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction. Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Aanbevelingen
* *Voltooi de* meldings gebeurtenissen zo snel mogelijk.
* Voer *geen* dure bewerkingen uit (bijvoorbeeld I/O-bewerkingen) als onderdeel van synchrone gebeurtenissen.
* *Controleer het* actie type voordat u de gebeurtenis verwerkt. Nieuwe actie typen kunnen in de toekomst worden toegevoegd.

Hier volgen enkele dingen die u moet onthouden:

* Meldingen worden gestart als onderdeel van het uitvoeren van een bewerking. Een herstel melding wordt bijvoorbeeld geactiveerd als de laatste stap van een herstel bewerking. Een herstel bewerking kan pas worden voltooid als de meldings gebeurtenis is verwerkt.
* Omdat meldingen worden geactiveerd als onderdeel van het Toep assen van bewerkingen, zien clients alleen meldingen voor lokaal doorgevoerde bewerkingen. En omdat bewerkingen alleen worden gegarandeerd lokaal worden doorgevoerd (met andere woorden, geregistreerd), kunnen ze in de toekomst mogelijk niet worden uitgevoerd.
* Op het pad opnieuw uitvoeren wordt één melding gestart voor elke toegepaste bewerking. Dit betekent dat als de trans actie T1 Create (X), Delete (X) en Create (X) bevat, u één melding krijgt voor het maken van X, een voor het verwijderen en één voor het maken, in die volg orde.
* Voor trans acties die meerdere bewerkingen bevatten, worden bewerkingen toegepast in de volg orde waarin ze zijn ontvangen op de primaire replica van de gebruiker.
* Als onderdeel van de verwerking van ONWAAR voortgang, zijn bepaalde bewerkingen mogelijk ongedaan. Er worden meldingen gegenereerd voor dergelijke bewerkingen voor ongedaan maken. de status van de replica wordt teruggedraaid naar een stabiel punt. Een belang rijk verschil bij het ongedaan maken van meldingen is dat gebeurtenissen die dubbele sleutels hebben, worden geaggregeerd. Als bijvoorbeeld T1 trans actie ongedaan wordt gemaakt, ziet u één melding om te verwijderen (X).

## <a name="next-steps"></a>Volgende stappen
* [Betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
* [Reliable Services snel starten](service-fabric-reliable-services-quick-start.md)
* [Back-up en herstel (nood herstel) Reliable Services](service-fabric-reliable-services-backup-restore.md)
* [Naslag informatie voor ontwikkel aars voor betrouw bare verzamelingen](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
