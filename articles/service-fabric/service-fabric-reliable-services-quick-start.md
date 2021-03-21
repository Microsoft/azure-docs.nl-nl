---
title: 'Uw eerste Service Fabric-toepassing maken in C #'
description: Inleiding tot het maken van een Microsoft Azure Service Fabric-toepassing met stateless en stateful Services.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev, devx-track-csharp
ms.openlocfilehash: 45341c98a40cbcabfa8b96f2016f02f1755fe2b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791524"
---
# <a name="get-started-with-reliable-services"></a>Aan de slag met Reliable Services

> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-quick-start.md)
> * [Java op Linux](service-fabric-reliable-services-quick-start-java.md)

Een Azure Service Fabric-toepassing bevat een of meer services waarop de code wordt uitgevoerd. Deze hand leiding laat zien hoe u stateless en stateful Service Fabric-toepassingen kunt maken met [reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Basisbegrippen

Om aan de slag te gaan met Reliable Services hoeft u slechts enkele basis concepten te begrijpen:

* **Service type**: dit is de service-implementatie. Het wordt gedefinieerd door de klasse die u schrijft `StatelessService` , en alle andere code of afhankelijkheden die erin worden gebruikt, samen met een naam en versie nummer.
* **Benoemd service-exemplaar**: als u uw service wilt uitvoeren, maakt u benoemde instanties van uw service type, net zoals u object instanties van een klassetype maakt. Een service-exemplaar heeft een naam in de vorm van een URI met behulp van de ' Fabric:/' schema, zoals ' Fabric:/Mijntoep/MyService '.
* **Servicehost: de** benoemde service-exemplaren die u maakt, moeten binnen een hostproces worden uitgevoerd. De servicehost is slechts een proces waarbij exemplaren van uw service kunnen worden uitgevoerd.
* **Service registratie**: registratie brengt alles samen. Het Service type moet worden geregistreerd bij de Service Fabric runtime in een servicehost om Service Fabric toe te staan dat er exemplaren van worden gemaakt om te worden uitgevoerd.  

## <a name="create-a-stateless-service"></a>Een stateless service maken

Een stateless service is een type service dat momenteel de norm is in Cloud toepassingen. Het wordt beschouwd als stateless omdat de service zelf geen gegevens bevat die op betrouw bare wijze moeten worden opgeslagen of Maxi maal beschikbaar moeten worden gemaakt. Als een exemplaar van een stateless service afgesloten, gaat de interne status verloren. In dit type service moet de status persistent worden gemaakt voor een externe opslag, zoals Azure Tables of SQL Database, om deze Maxi maal beschikbaar en betrouwbaar te maken.

Start Visual Studio 2017 of Visual Studio 2019 als beheerder en maak een nieuw Service Fabric toepassings project met de naam *HelloWorld*:

![Het dialoog venster Nieuw project gebruiken om een nieuwe Service Fabric-toepassing te maken](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Maak vervolgens een stateless service project met behulp van **.net Core 2,0** met de naam *HelloWorldStateless*:

![Maak in het tweede dialoog venster een stateless service project](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Uw oplossing bevat nu twee projecten:

* *HelloWorld*. Dit is het *toepassings* project dat uw *Services* bevat. Het bevat ook het toepassings manifest waarmee de toepassing wordt beschreven, evenals een aantal Power shell-scripts die u helpen bij het implementeren van uw toepassing.
* *HelloWorldStateless*. Dit is het service project. Het bevat de stateless service-implementatie.

## <a name="implement-the-service"></a>De service implementeren

Open het bestand **HelloWorldStateless. cs** in het service project. In Service Fabric kan een service elke bedrijfs logica uitvoeren. De service-API biedt twee toegangs punten voor uw code:

* Een open-end-invoer punt methode met de naam *RunAsync*, waar u werk belastingen kunt uitvoeren, inclusief langlopende Compute-workloads.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Een communicatie punt waar u de communicatie stack van uw keuze kunt aansluiten, zoals ASP.NET Core. Hier kunt u beginnen met het ontvangen van aanvragen van gebruikers en andere services.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

In deze zelf studie gaat u de focus op de methode van het `RunAsync()` toegangs punt. Hier kunt u direct beginnen met het uitvoeren van uw code.
De project sjabloon bevat een voorbeeld implementatie van `RunAsync()` waarmee een aantal rollen wordt verhoogd.

> [!NOTE]
> Zie [service communicatie met ASP.net core](service-fabric-reliable-services-communication-aspnetcore.md) voor meer informatie over het werken met een communicatie stack

### <a name="runasync"></a>RunAsync

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

Het platform roept deze methode aan wanneer een exemplaar van een service wordt geplaatst en gereed is om te worden uitgevoerd. Voor een stateless service, dat wil zeggen wanneer het service-exemplaar wordt geopend. Er wordt een annulerings token gegeven om te coördineren wanneer uw service-exemplaar moet worden gesloten. In Service Fabric kan deze open/close-cyclus van een service-exemplaar veel keren voor komen gedurende de levens duur van de service als geheel. Dit kan verschillende oorzaken hebben, waaronder:

* Het systeem verplaatst uw service-exemplaren voor resource verdeling.
* Er treden fouten op in uw code.
* Er wordt een upgrade uitgevoerd voor de toepassing of het systeem.
* De onderliggende hardware ondervindt een storing.

Deze indeling wordt beheerd door het systeem om ervoor te zorgen dat uw service Maxi maal beschikbaar is en op de juiste wijze is gebalanceerd.

`RunAsync()` moet synchroon niet worden geblokkeerd. Uw implementatie van RunAsync moet een taak retour neren of wachten op een langlopende of blokkerende bewerking zodat de runtime kan door gaan. In de `while(true)` lus in het vorige voor beeld wordt een taak geretourneerd `await Task.Delay()` gebruikt. Als uw werk belasting synchroon moet worden geblokkeerd, moet u een nieuwe taak plannen `Task.Run()` in uw `RunAsync` implementatie.

Het annuleren van uw werk belasting is een gezamenlijke inspanning die wordt georganiseerd door het door gegeven annulerings token. Het systeem wacht totdat de taak is voltooid (door voltooiing, annulering of fout) voordat deze wordt verplaatst. Het is belang rijk om het annulerings token te voldoen, werk te volt ooien en `RunAsync()` zo snel mogelijk af te sluiten wanneer de systeem aanvragen worden geannuleerd.

In dit stateless service voor beeld wordt het aantal opgeslagen in een lokale variabele. Maar omdat dit een stateless service is, bestaat de opgeslagen waarde alleen voor de huidige levens duur van het service-exemplaar. Wanneer de service wordt verplaatst of opnieuw wordt gestart, gaat de waarde verloren.

## <a name="create-a-stateful-service"></a>Een stateful service maken

Service Fabric introduceert een nieuwe soort service die stateful is. Een stateful service kan de status op betrouw bare wijze in de service zelf onderhouden, naast de code die deze gebruikt. De status wordt Maxi maal beschikbaar gemaakt door Service Fabric zonder dat de status in een externe opslag moet worden bewaard.

Als u een item waarde wilt converteren van stateless naar Maxi maal beschikbaar en permanent, zelfs wanneer de service wordt verplaatst of opnieuw wordt opgestart, hebt u een stateful service nodig.

In dezelfde *HelloWorld* -toepassing kunt u een nieuwe service toevoegen door met de rechter muisknop op de verwijzingen naar Services in het toepassings project te klikken en **toevoegen > nieuwe service Fabric service** te selecteren.

![Een service toevoegen aan uw Service Fabric-toepassing](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecteer **.net Core 2,0-> stateful service** en noem deze *HelloWorldStateful*. Klik op **OK**.

![In het dialoog venster Nieuw project kunt u een nieuw Service Fabric maken stateful service](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Uw toepassing moet nu twee services hebben: de stateless service *HelloWorldStateless* en de stateful service *HelloWorldStateful*.

Een stateful service heeft dezelfde toegangs punten als een stateless service. Het belangrijkste verschil is de beschik baarheid van een *State-provider* die de status betrouwbaar kan opslaan. Service Fabric wordt geleverd met de implementatie van een status provider, die [reliable verzamelingen](service-fabric-reliable-services-reliable-collections.md)wordt genoemd, waarmee u gerepliceerde gegevens structuren kunt maken met behulp van betrouw bare status Manager. Een stateful betrouw bare service maakt standaard gebruik van deze State-provider.

Open **HelloWorldStateful. cs** in *HelloWorldStateful*, dat de volgende RunAsync-methode bevat:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync

`RunAsync()` werkt op dezelfde manier als stateful en stateless Services. In een stateful service voert het platform echter namens u extra werkzaamheden uit voordat het wordt uitgevoerd `RunAsync()` . Dit werk kan erop kunnen toezien dat de betrouw bare status Manager en de betrouw bare verzamelingen klaar zijn voor gebruik.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Betrouw bare verzamelingen en de betrouw bare status Manager

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2) is een woorden lijst-implementatie die u kunt gebruiken om de status van de service op een betrouw bare manier op te slaan. Met Service Fabric en betrouw bare verzamelingen kunt u gegevens rechtstreeks in uw service opslaan zonder dat hiervoor een externe permanente opslag nodig is. Betrouw bare verzamelingen maken uw gegevens Maxi maal beschikbaar. Service Fabric dit te bereiken door meerdere *replica's* van uw service voor u te maken en te beheren. Het biedt ook een API die de complexiteit van het beheer van deze replica's en hun status overgangen opstelt.

In betrouw bare verzamelingen kan elk .NET-type worden opgeslagen, inclusief uw aangepaste typen, met een aantal voor behoud:

* Service Fabric uw status Maxi maal beschikbaar maakt door de status van de knoop punten te *repliceren* , en met betrouw bare verzamelingen worden uw gegevens opgeslagen op een lokale schijf op elke replica. Dit betekent dat alles dat is opgeslagen in betrouw bare verzamelingen *serialiseerbaar* moet zijn. Standaard gebruiken betrouw bare verzamelingen [Data](/dotnet/api/system.runtime.serialization.datacontractattribute) contract voor serialisatie, dus is het belang rijk om ervoor te zorgen dat uw typen worden [ondersteund door de Serialisatiefunctie voor het gegevens contract](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) wanneer u de standaardserialisatiefunctie gebruikt.
* Objecten worden gerepliceerd voor hoge Beschik baarheid wanneer u trans acties doorvoert voor betrouw bare verzamelingen. Objecten die zijn opgeslagen in betrouw bare verzamelingen, worden in het lokale geheugen in uw service bewaard. Dit betekent dat u een lokale verwijzing naar het object hebt.
  
   Het is belang rijk dat u geen lokale exemplaren van deze objecten mutate zonder een update-bewerking uit te voeren op de betrouw bare verzameling in een trans actie. Dit komt doordat wijzigingen in lokale exemplaren van objecten niet automatisch worden gerepliceerd. U moet het object opnieuw toevoegen aan de woorden lijst of een van de *Update* methoden gebruiken in de woorden lijst.

De betrouw bare status beheerder beheert betrouw bare verzamelingen voor u. U kunt op elk gewenst moment een betrouw bare status Manager vragen voor een betrouw bare verzameling op naam en op elke plaats in uw service. De betrouw bare status beheerder zorgt ervoor dat u een referentie back-up krijgt. Het is niet raadzaam om verwijzingen naar betrouw bare verzamelings instanties in variabelen of eigenschappen van klassen leden op te slaan. Er moet speciale aandacht worden besteed om ervoor te zorgen dat de verwijzing op elk moment in de levens cyclus van de service wordt ingesteld op een exemplaar. De betrouw bare status manager behandelt dit werk voor u en is geoptimaliseerd voor herhaalde bezoeken.

### <a name="transactional-and-asynchronous-operations"></a>Transactionele en asynchrone bewerkingen

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Voor betrouw bare verzamelingen gelden veel dezelfde bewerkingen `System.Collections.Generic` als voor hun en `System.Collections.Concurrent` tegen Hangers, met uitzonde ring van language integrated query (LINQ). Bewerkingen voor betrouw bare verzamelingen zijn asynchroon. Dit is omdat schrijf bewerkingen met betrouw bare verzamelingen I/O-bewerkingen uitvoeren om gegevens te repliceren en te persistent maken op schijf.

Betrouw bare verzamelings bewerkingen zijn *Transactioneel*, zodat u de status consistent kunt blijven tussen meerdere betrouw bare verzamelingen en bewerkingen. U kunt bijvoorbeeld een werk item uit een betrouw bare wachtrij verwijderen, een bewerking hierop uitvoeren en het resultaat opslaan in een betrouw bare woorden lijst, allemaal binnen één trans actie. Dit wordt behandeld als een Atomic-bewerking en garandeert dat de gehele bewerking slaagt of dat de hele bewerking wordt teruggedraaid. Als er een fout optreedt nadat u het item in de wachtrij hebt geplaatst, maar voordat u het resultaat opslaat, wordt de hele trans actie teruggedraaid en blijft het item in de wachtrij voor verwerking.

## <a name="run-the-application"></a>De toepassing uitvoeren
We gaan nu terug naar de toepassing *HelloWorld* . U kunt nu uw services bouwen en implementeren. Wanneer u op **F5** drukt, wordt uw toepassing op uw lokale cluster gebouwd en geïmplementeerd.

Nadat de services zijn gestart, kunt u de gegenereerde gebeurtenissen van het Event Tracing for Windows (ETW) bekijken in een venster met **diagnostische gebeurtenissen** . Houd er rekening mee dat de weer gegeven gebeurtenissen afkomstig zijn van zowel de stateless service als de stateful service in de toepassing. U kunt de stroom onderbreken door te klikken op de knop **pause** . U kunt vervolgens de details van een bericht bekijken door dat bericht uit te breiden.

> [!NOTE]
> Voordat u de toepassing uitvoert, moet u ervoor zorgen dat er een lokaal ontwikkelings cluster wordt uitgevoerd. Bekijk de aan de slag- [hand leiding](service-fabric-get-started.md) voor informatie over het instellen van uw lokale omgeving.
> 
> 

![Diagnostische gebeurtenissen in Visual Studio weer geven](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Volgende stappen
[Fouten opsporen in uw Service Fabric-toepassing in Visual Studio](service-fabric-debugging-your-application.md)

[Aan de slag: Service Fabric Web API-services met OWIN self-hosting](./service-fabric-reliable-services-communication-aspnetcore.md)

[Meer informatie over betrouw bare verzamelingen](service-fabric-reliable-services-reliable-collections.md)

[Een app implementeren](service-fabric-deploy-remove-applications.md)

[Toepassingsupgrade](service-fabric-application-upgrade.md)

[Naslag informatie voor ontwikkel aars voor Reliable Services](/previous-versions/azure/dn706529(v=azure.100))
