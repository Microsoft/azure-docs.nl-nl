---
title: Werken met betrouwbare verzamelingen
description: Meer informatie over de aanbevolen procedures voor het werken met betrouw bare verzamelingen binnen een Azure Service Fabric-toepassing.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 908f24cc22c969a34a513ff3fd3ceaa788420620
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787872"
---
# <a name="working-with-reliable-collections"></a>Werken met betrouwbare verzamelingen
Service Fabric biedt een stateful programmeer model dat via betrouw bare verzamelingen beschikbaar is voor .NET-ontwikkel aars. Service Fabric biedt met name betrouw bare woorden lijst en betrouw bare wachtrij klassen. Wanneer u deze klassen gebruikt, wordt de status gepartitioneerd (voor schaal baarheid), gerepliceerd (voor Beschik baarheid) en trans actie binnen een partitie (voor ACID-semantiek). Laten we een typisch voor beeld van een betrouwbaar woordenlijst object bekijken en zien wat het daad werkelijk doet.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
   await Task.Delay(100);
}
```

Alle bewerkingen in betrouw bare woordenlijst objecten (met uitzonde ring van ClearAsync, die niet ongedaan kan worden gemaakt), vereisen een ITransaction-object. Dit object is gekoppeld aan alle wijzigingen die u probeert aan te brengen in een betrouw bare woorden lijst en/of betrouw bare wachtrij objecten binnen één partitie. U verkrijgt een ITransaction-object door de StateManager's CreateTransaction-methode van de partitie aan te roepen.

In de bovenstaande code wordt het ITransaction-object door gegeven aan de methode AddAsync van een betrouw bare woorden lijst. Intern, woordenlijst methoden die een sleutel accepteren, nemen een lezer/schrijf vergrendeling die is gekoppeld aan de sleutel. Als de methode de waarde van de sleutel wijzigt, neemt de methode een schrijf vergrendeling op de sleutel en als de methode alleen de waarde van de sleutel leest, wordt er een lees vergrendeling op de sleutel uitgevoerd. Omdat AddAsync de waarde van de sleutel wijzigt in de nieuwe, door gegeven waarde, wordt de schrijf vergrendeling van de sleutel genomen. Als twee (of meer) threads tegelijkertijd waarden met dezelfde sleutel kunnen toevoegen, wordt met één thread de schrijf vergrendeling opgehaald en worden de andere threads geblokkeerd. Methoden blok keren standaard Maxi maal vier seconden om de vergren deling te verkrijgen. na 4 seconden genereert de methoden een TimeoutException. Als u liever een expliciete time-outwaarde wilt door geven, kunt u een methode overbelasten.

Normaal gesp roken schrijft u uw code om te reageren op een TimeoutException door deze te laten opvangen en de gehele bewerking opnieuw uit te voeren (zoals weer gegeven in de bovenstaande code). In deze eenvoudige code wordt de taak gewoon aangeroepen. Stel elke keer een vertraging van 100 milliseconden in. Maar in werkelijkheid is het mogelijk dat u beter kunt werken met een bepaalde vorm van exponentiële uitstel vertraging.

Zodra de vergren deling is verkregen, voegt AddAsync de sleutel-en waarde-object verwijzingen toe aan een interne tijdelijke woorden lijst die is gekoppeld aan het ITransaction-object. Dit wordt gedaan om u te voorzien van lees-uw eigen schrijf semantiek. Dat wil zeggen dat nadat u AddAsync aanroept, een later aanroepen naar TryGetValueAsync met hetzelfde ITransaction-object de waarde retourneert, zelfs als u de trans actie nog niet hebt doorgevoerd.

> [!NOTE]
> Het aanroepen van TryGetValueAsync met een nieuwe trans actie retourneert een verwijzing naar de laatste toegezegde waarde. Wijzig deze verwijzing niet rechtstreeks, omdat hiermee het mechanisme voor het persistent maken en repliceren van de wijzigingen wordt omzeild. We raden u aan de waarden alleen-lezen in te stellen, zodat de waarde voor een sleutel alleen kan worden gewijzigd met behulp van betrouw bare Dictionary-Api's.

Vervolgens worden uw sleutel-en waarde-objecten naar byte-matrices geserialiseerd en worden deze byte-matrices toegevoegd aan een logboek bestand op het lokale knoop punt. Ten slotte verzendt AddAsync de byte matrices naar alle secundaire replica's zodat ze dezelfde sleutel/waarde-informatie hebben. Hoewel de sleutel/waarde-informatie naar een logboek bestand is geschreven, wordt de informatie niet beschouwd als onderdeel van de woorden lijst tot de trans actie waaraan ze zijn gekoppeld, is doorgevoerd.

In de bovenstaande code voert de aanroep van CommitAsync alle bewerkingen van de trans actie door. In het bijzonder worden door voeren gegevens toegevoegd aan het logboek bestand op het lokale knoop punt en wordt ook de doorvoer record naar alle secundaire replica's verzonden. Zodra een quorum (meerderheid) van de replica's heeft gereageerd, worden alle gegevens wijzigingen als permanent beschouwd en worden alle vergren delingen die zijn gekoppeld aan sleutels die via het ITransaction-object zijn gemanipuleerd, zodanig vrijgegeven dat andere threads/trans acties dezelfde sleutels en hun waarden kunnen manipuleren.

Als CommitAsync niet wordt aangeroepen (meestal vanwege een uitzonde ring die wordt gegenereerd), wordt het ITransaction-object verwijderd. Wanneer een niet-doorgevoerde ITransaction-object wordt verwijderd, voegt Service Fabric gegevens toe aan het logboek bestand van het lokale knoop punt en hoeft niets te worden verzonden naar een van de secundaire replica's. En vervolgens worden alle vergren delingen die zijn gekoppeld aan sleutels die via de trans actie zijn gemanipuleerd, vrijgegeven.

## <a name="volatile-reliable-collections"></a>Vluchtige betrouw bare verzamelingen 
In sommige werk belastingen, zoals een gerepliceerde cache, kan incidenteel gegevens verlies worden toegestaan. Door persistentie van de gegevens naar de schijf te voor komen, kan er betere latentie en door voer worden toegestaan bij het schrijven naar betrouw bare woorden lijsten. Als er sprake is van een gebrek aan persistentie, is het verlies van gegevens verloren als er quorum verlies optreedt. Omdat quorum verlies een zeldzame gebeurtenis is, is het mogelijk dat de betere prestaties de zeldzame kans op gegevens verlies voor die werk belastingen zijn.

Op dit moment is volatiele ondersteuning alleen beschikbaar voor betrouw bare woorden lijsten en betrouw bare wacht rijen en niet ReliableConcurrentQueues. Raadpleeg de lijst met [aanvullende opmerkingen](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) om te informeren of u vluchtige verzamelingen wilt gebruiken.

Als u vluchtige ondersteuning wilt inschakelen in uw service, stelt ```HasPersistedState``` u de markering in Service type declaratie in op ```false``` , bijvoorbeeld:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>Bestaande permanente Services kunnen niet vluchtig worden gemaakt en vice versa. Als u dit wilt doen, moet u de bestaande service verwijderen en vervolgens de service implementeren met de bijgewerkte vlag. Dit betekent dat u een volledig gegevens verlies moet opdoen als u de markering wilt wijzigen ```HasPersistedState``` . 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Veelvoorkomende Valk uilen en hoe u deze voor komt
Nu u begrijpt hoe de betrouw bare verzamelingen intern werken, laten we eens kijken naar enkele veelvoorkomende toepassingen. Zie de onderstaande code:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Wanneer u werkt met een reguliere .NET-woorden lijst, kunt u een sleutel/waarde toevoegen aan de woorden lijst en vervolgens de waarde van een eigenschap wijzigen (zoals LastLogin). Deze code werkt echter niet goed met een betrouw bare woorden lijst. Houd er rekening mee dat de aanroep van AddAsync de sleutel/waarde-objecten naar byte-matrices opmaakt en de matrices vervolgens opslaat in een lokaal bestand en deze ook verzendt naar de secundaire replica's. Als u een eigenschap later wijzigt, wordt de waarde van de eigenschap alleen in het geheugen gewijzigd. Dit heeft geen invloed op het lokale bestand of de gegevens die naar de replica's worden verzonden. Als het proces vastloopt, wordt de inhoud van het geheugen verwijderd. Wanneer een nieuw proces wordt gestart of als een andere replica primair wordt, is de oude eigenschaps waarde beschikbaar.

Ik kan niet lang genoeg bepalen hoe eenvoudig het is om de hierboven weer gegeven soort fout te maken. En u krijgt alleen meer informatie over de fout als/wanneer het proces uitvalt. De juiste manier om de code te schrijven, is eenvoudigweg om de twee regels om te keren:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Hier volgt nog een voor beeld van een veelvoorkomende fout:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Opnieuw, met gewone .NET-woorden lijsten, is de bovenstaande code goed en is een gemeen schappelijk patroon: de ontwikkelaar gebruikt een sleutel om een waarde op te zoeken. Als de waarde bestaat, wijzigt de ontwikkelaar de waarde van een eigenschap. Met betrouw bare verzamelingen vertoont deze code echter hetzelfde probleem als reeds besproken: **u mag een object niet wijzigen nadat u het hebt opgegeven voor een betrouw bare verzameling.**

De juiste manier om een waarde in een betrouw bare verzameling bij te werken, is om een verwijzing naar de bestaande waarde op te halen en te beschouwen dat het object waarnaar wordt verwezen door deze referentie onveranderbaar is. Maak vervolgens een nieuw object dat een exacte kopie van het oorspronkelijke object is. Nu kunt u de status van dit nieuwe object wijzigen en het nieuwe object naar de verzameling schrijven, zodat het wordt geserialiseerd naar byte matrices, toegevoegd aan het lokale bestand en verzonden naar de replica's. Na het door voeren van de wijziging (en), de objecten in het geheugen, het lokale bestand en alle replica's hebben dezelfde exacte status. Alles is goed.

De volgende code toont de juiste manier om een waarde in een betrouw bare verzameling bij te werken:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Onveranderbare gegevens typen definiëren om een programmeer fout te voor komen
In het ideale geval laten we de compiler fouten melden wanneer u per ongeluk code produceert die mutates status van een object die u zou moeten overwegen onveranderbaar te zijn. Maar de C#-compiler heeft hiervoor niet de mogelijkheid. Om mogelijke programmeer fouten te voor komen, raden we u ten zeerste aan de typen die u gebruikt met betrouw bare verzamelingen te definiëren als onveranderlijke typen. Dit betekent met name dat u de kern waardetypen (zoals getallen [Int32, UInt64, etc.], DateTime, GUID, time span en like) aanhoudt. U kunt ook een teken reeks gebruiken. Het is raadzaam om verzamelings eigenschappen te voor komen als serialisatie en deserialisatie van deze gegevens. Als u echter verzamelings eigenschappen wilt gebruiken, wordt het gebruik van aanbevolen. NET onveranderlijke verzamelingen bibliotheek ([System. Collections. onveranderbaar](https://www.nuget.org/packages/System.Collections.Immutable/)). Deze tape wisselaar kan worden gedownload van https://nuget.org . We raden u ook aan om uw klassen te verzegelen en de velden alleen-lezen waar mogelijk te maken.

In het onderstaande type user info wordt gedemonstreerd hoe u een onveranderbaar type kunt definiëren met voor noemde aanbevelingen.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Het type ItemId is ook een onveranderbaar type, zoals hier wordt weer gegeven:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Schema versie beheer (upgrades)
Interne, betrouw bare verzamelingen serialiseren uw objecten met. De DataContractSerializer van het NET. De geserialiseerde objecten worden bewaard op de lokale schijf van de primaire replica en worden ook verzonden naar de secundaire replica's. Als uw service is verouderd, wilt u waarschijnlijk het soort gegevens (schema) wijzigen dat vereist is voor uw service. Benadert de versie van uw gegevens met geweldige zorg. Eerst moet u de oude gegevens altijd kunnen deserialiseren. Dit betekent met name dat uw deserialisatie code oneindig achterwaarts compatibel moet zijn: versie 333 van uw service code moet kunnen worden gebruikt voor gegevens die in een betrouw bare verzameling worden geplaatst met versie 1 van uw service code van 5 jaar geleden.

Daarnaast wordt de service code één upgrade domein tegelijk geüpgraded. Tijdens een upgrade hebt u dus twee verschillende versies van de service code die gelijktijdig worden uitgevoerd. U moet voor komen dat de nieuwe versie van uw service code het nieuwe schema gebruiken als oude versies van uw service code het nieuwe schema mogelijk niet kan verwerken. Als dat mogelijk is, moet u elke versie van uw service zo ontwerpen dat deze compatibel is met één versie. Dit betekent met name dat v1 van uw service code alle schema-elementen moet kunnen negeren die niet expliciet wordt verwerkt. Het moet echter alle gegevens kunnen opslaan die niet expliciet bekend zijn en waar ze een back-up van maken wanneer ze een woorden lijst sleutel of-waarde bijwerken.

> [!WARNING]
> Hoewel u het schema van een sleutel kunt wijzigen, moet u ervoor zorgen dat de hash-code van uw sleutel en de algoritmen stabiel zijn. Als u de werking van een van deze algoritmen wijzigt, kunt u de sleutel niet meer in de betrouw bare woorden lijst opzoeken.
> .NET-teken reeksen kunnen worden gebruikt als sleutel, maar u kunt de teken reeks zelf gebruiken als de sleutel. gebruik niet het resultaat van string. GetHashCode als de sleutel.

U kunt ook uitvoeren wat meestal een twee upgrades wordt genoemd. Bij een upgrade met twee fasen voert u een upgrade uit van v1 naar v2: v2 bevat de code die weet hoe u kunt omgaan met de nieuwe schema wijziging, maar deze code wordt niet uitgevoerd. Wanneer de v2-code v1-gegevens leest, wordt deze op IT toegepast en worden er v1-gegevens wegge schreven. Nadat de upgrade voor alle upgrade domeinen is voltooid, kunt u een signaal verzenden naar de actieve v2-instanties die de upgrade is voltooid. (Een manier om dit aan te geven, is om een configuratie-upgrade uit te vouwen. Dit is een upgrade van twee fasen.) De v2-exemplaren kunnen nu v1-gegevens lezen, deze converteren naar v2-gegevens, hierop op de app worden uitgevoerd en deze als v2-gegevens schrijven. Wanneer andere instanties v2-gegevens lezen, hoeven ze niet te worden geconverteerd, ze worden gewoon op de app uitgevoerd en kunnen v2-gegevens worden geschreven.

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over het maken van forward compatibele gegevens contracten raadpleegt u [Forward-compatibele gegevens contracten](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts)

Zie [Data contract versie beheer](/dotnet/framework/wcf/feature-details/data-contract-versioning) voor meer informatie over best practices voor het versie nummer van gegevens contracten

Zie voor meer informatie over het implementeren van gegevens contracten van versie [tolerant serialisatie](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks) .

Zie [IExtensibleDataObject](/dotnet/api/system.runtime.serialization.iextensibledataobject) voor meer informatie over het bieden van een gegevens structuur die in meerdere versies kan worden gebruikt.
