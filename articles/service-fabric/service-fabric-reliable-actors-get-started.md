---
title: Een service op basis van een actor maken op Azure Service Fabric
description: Meer informatie over het maken, opsporen en implementeren van uw eerste op actor gebaseerde service in C# met behulp van Service Fabric Reliable Actors.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 225ccb67153a33ed47af68ebb1549dce37426278
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96573458"
---
# <a name="getting-started-with-reliable-actors"></a>Aan de slag met Reliable Actors
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-actors-get-started.md)
> * [Java op Linux](./service-fabric-create-your-first-linux-application-with-java.md)

In dit artikel wordt uitgelegd hoe u een eenvoudige reliable actor-toepassing maakt en opspoort in Visual Studio. Zie [Introduction to Service Fabric reliable actors](service-fabric-reliable-actors-introduction.md)voor meer informatie over reliable actors.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u de Service Fabric ontwikkel omgeving, waaronder Visual Studio, op uw computer hebt geïnstalleerd. Zie [How to set the Development Environment](service-fabric-get-started.md)(Engelstalig) voor meer informatie.

## <a name="create-a-new-project-in-visual-studio"></a>Een nieuw project maken in Visual Studio

Start Visual Studio 2019 of hoger als beheerder en maak een nieuw **service Fabric-toepassings** project:

![Service Fabric-hulpprogram ma's voor Visual Studio-nieuw project][1]

Kies in het volgende dialoog venster **actor service** onder **.net Core 2,0** en voer een naam in voor de service.

![Service Fabric project sjablonen][5]

Het gemaakte project toont de volgende structuur:

![Service Fabric project structuur][2]

## <a name="examine-the-solution"></a>De oplossing onderzoeken

De oplossing bevat drie projecten:

* **Het toepassings project (mijn toepassing)**. In dit project worden alle services tegelijk verpakt voor implementatie. Het bevat de *ApplicationManifest.xml* -en Power shell-scripts voor het beheren van de toepassing.

* **Het interface project (HelloWorld. interfaces)**. Dit project bevat de interface definitie voor de actor. Actor-interfaces kunnen in elk project met een wille keurige naam worden gedefinieerd.  De interface definieert het actor-contract dat wordt gedeeld door de actor-implementatie en de clients die de actor aanroepen.  Omdat client projecten hiervan afhankelijk kunnen zijn, is het doorgaans zinvol om deze te definiëren in een assembly die is gescheiden van de actor-implementatie.

* **Het actor service-project (HelloWorld)**. Dit project definieert de Service Fabric-service die de actor gaat hosten. Het bevat de implementatie van de actor, *HelloWorld. cs*. Een actor-implementatie is een klasse die is afgeleid van het basis type `Actor` en de interfaces implementeert die in het project *MyActor. interfaces* zijn gedefinieerd. Een actor-klasse moet ook een constructor implementeren die een `ActorService` exemplaar accepteert en een `ActorId` en deze door geven aan de basis `Actor` klasse.
    
    Dit project bevat ook *programma. cs*, dat actor klassen registreert met de service Fabric runtime met behulp van `ActorRuntime.RegisterActorAsync<T>()` . De `HelloWorld` klasse is al geregistreerd. Eventuele extra actor-implementaties die aan het project worden toegevoegd, moeten ook worden geregistreerd in de- `Main()` methode.

## <a name="customize-the-helloworld-actor"></a>De acteur van HelloWorld aanpassen

De project sjabloon definieert een aantal methoden in de `IHelloWorld` interface en implementeert deze in de `HelloWorld` actor-implementatie.  Vervang deze methoden zodat de actor-service een eenvoudige teken reeks ' Hallo wereld ' retourneert.

Vervang in het project *HelloWorld. interfaces* in het bestand *IHelloWorld. cs* de interface definitie als volgt:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

Vervang in het project **HelloWorld** in **HelloWorld. cs** de volledige klassedefinitie als volgt:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Druk op **CTRL-SHIFT-B** om het project te bouwen en te controleren of alles compileert.

## <a name="add-a-client"></a>Een client toevoegen

Maak een eenvoudige console toepassing om de actor-service aan te roepen.

1. Klik met de rechter muisknop op de oplossing in Solution Explorer >  >  **Nieuw project toevoegen...**.

2. Kies onder de **.net core** -project typen de optie **console-app (.net core)**.  Geef het project de naam *ActorClient*.
    
    ![Dialoog venster Nieuw project toevoegen][6]    
    
    > [!NOTE]
    > Een console toepassing is niet het type app dat u normaal gesp roken gebruikt als een-client in Service Fabric, maar dit is een handig voor beeld voor fout opsporing en testen met behulp van het lokale Service Fabric cluster.

3. De console toepassing moet een 64-bits toepassing zijn om de compatibiliteit met het interface project en andere afhankelijkheden te behouden.  Klik in Solution Explorer met de rechter muisknop op het project **ActorClient** en klik vervolgens op **Eigenschappen**.  Stel op het tabblad **bouwen** **platform doel** in op **x64**.
    
    ![Eigenschappen bouwen][8]

4. Voor het client project is het NuGet-pakket reliable actors vereist.  Klik op **extra**  >  **NuGet package manager**  >  **Package Manager-console**.  Voer in de Package Manager-console de volgende opdracht in:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Het NuGet-pakket en alle bijbehorende afhankelijkheden worden geïnstalleerd in het ActorClient-project.

5. Het client project vereist ook een verwijzing naar het interfaces-project.  Klik in het project ActorClient met de rechter muisknop op **afhankelijkheden** en klik vervolgens op **project verwijzing toevoegen...**.  Selecteer **projecten > oplossing** (als deze nog niet is geselecteerd) en tik vervolgens op het selectie vakje naast **HelloWorld. interfaces**.  Klik op **OK**.
    
    ![Dialoog venster referentie toevoegen][7]

6. Vervang in het project ActorClient de volledige inhoud van *programma. cs* door de volgende code:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Uitvoeren en fout opsporing

Druk op **F5** om de toepassing lokaal te bouwen, te implementeren en uit te voeren in het service Fabric Development-cluster.  Tijdens het implementatie proces ziet u de voortgang in het **uitvoer** venster.

![Uitvoer venster van Service Fabric fout opsporing][3]

Wanneer de uitvoer de tekst bevat, *de toepassing gereed is, is* het mogelijk om de service te testen met behulp van de ActorClient-toepassing.  Klik in Solution Explorer met de rechter muisknop op het project **ActorClient** en vervolgens op **debug**  >  **Start new instance**.  De opdracht regel toepassing moet de uitvoer van de actor-service weer geven.

![Toepassingsuitvoer][9]

> [!TIP]
> De Service Fabric actors-runtime verzendt enkele [gebeurtenissen en prestatie meter items die betrekking hebben op actor-methoden](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Ze zijn handig voor diagnostische en prestatie bewaking.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [hoe reliable actors het service Fabric platform te gebruiken](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png
