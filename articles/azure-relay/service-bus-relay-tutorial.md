---
title: Een on-premises WCF REST-service zichtbaar maken voor clients met Azure Relay
description: In deze zelfstudie wordt beschreven hoe u een on-premises WCF REST-service zichtbaar maakt voor een externe client met behulp van Azure WCF Relay.
ms.topic: tutorial
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 7669bc07ad91933cd31bd2ccd10eaf830d98de7c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710784"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Zelfstudie: Een on-premises WCF REST-service zichtbaar maken voor een externe client met behulp van Azure WCF Relay

In deze zelfstudie wordt beschreven hoe u een WCF Relay-clienttoepassing en -service maakt met behulp van Azure Relay. Zie [Aan de slag met Service Bus-wachtrijen](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) voor een vergelijkbare zelfstudie waarin gebruik wordt gemaakt van [Service Bus-wachtrijen](../service-bus-messaging/service-bus-messaging-overview.md).

In deze zelfstudie doorloopt u de verschillende stappen die nodig zijn om een WCF Relay-client en -servicetoepassing te maken. Net als bij de oorspronkelijke WCF is een service een middel met één of meer eindpunten. Elk eindpunt verwijst naar één of meer servicebewerkingen. Het eindpunt van een service bevat het adres van de service, een binding met de informatie die een client moet doorgeven aan de service en een contract waarin staat welke functionaliteit de service biedt aan de clients. Het belangrijkste verschil tussen WCF en WCF Relay is dat het eindpunt in de cloud beschikbaar is in plaats van lokaal op uw computer.

Nadat u alle secties in de juiste volgorde in deze zelfstudie hebt doorlopen, hebt u een actieve service. Ook hebt u een-client die de bewerkingen van de service kan aanroepen. 

In deze zelfstudie gaat u de volgende taken uitvoeren:

> [!div class="checklist"]
>
> * Voldoen aan de vereisten voor deze zelfstudie.
> * Een Relay-naamruimte maken.
> * Een WCF-servicecontract maken.
> * Het WCF-contract implementeren.
> * De WCF-service hosten en uitvoeren om u te registreren bij de Relay-service.
> * Een WCF-client maken voor het servicecontract.
> * De WCF-client configureren.
> * De WCF-client implementeren.
> * De toepassingen uitvoeren.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* [Visual Studio 2015 of hoger](https://www.visualstudio.com). In de voorbeelden in deze zelfstudie wordt Visual Studio 2019 gebruikt.
* Azure-SDK voor .NET. Installeer de SDK via de [SDK-downloadpagina](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Een Relay-naamruimte maken

De eerste stap is het maken van een naamruimte en ophalen van een SAS-sleutel ([Shared Access Signature](../service-bus-messaging/service-bus-sas.md)). Een naamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via de relayservice. Een SAS-sleutel wordt automatisch door het systeem gegenereerd wanneer een servicenaamruimte wordt gemaakt. De combinatie van servicenaamruimte en SAS-sleutel biedt Service Bus de benodigde referenties voor het verifiëren van toegang tot een toepassing.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Een WCF-servicecontract definiëren

Het servicecontract geeft aan welke bewerkingen door de service worden ondersteund. Bewerkingen zijn webservicemethoden of -functies. Contracten worden gemaakt door een C++-, C#- of Visual Basic-interface te definiëren. Elke methode in de interface komt overeen met een specifieke servicebewerking. Op elke interface moet het kenmerk [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) worden toegepast en op elke bewerking moet het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) worden toegepast. Als een methode in een interface met het kenmerk [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) niet beschikt over het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), wordt die methode niet weergegeven. In het voorbeeld na de procedure wordt de code voor deze taken weergegeven. Zie [Services ontwerpen en implementeren](/dotnet/framework/wcf/designing-and-implementing-services) voor meer informatie over contracten en services.

### <a name="create-a-relay-contract-with-an-interface"></a>Een relay-contract met een interface maken

1. Start Microsoft Visual Studio als beheerder. Klik hiervoor met de rechtermuisknop op het pictogram van het Visual Studio-programma en selecteer vervolgens **Als administrator uitvoeren**.
1. Selecteer **Een nieuw project maken** in Visual Studio.
1. Kies in het venster **Een nieuw project maken** **Console-app (.NET Framework)** voor C# en selecteer **Volgende**.
1. Noem het project *EchoService* en selecteer **Maken**.

   ![Een console-app maken][2]

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en klik op **NuGet-pakketten beheren**. Selecteer in de **NuGet Package Manager** de optie **Bladeren** en zoek en kies vervolgens **WindowsAzure.ServiceBus**. Selecteer **Installeren** en accepteer de gebruiksvoorwaarden.

    ![Service Bus-pakket][3]

   Met dit pakket worden er automatisch verwijzingen toegevoegd aan de Service Bus-bibliotheken en aan `System.ServiceModel` van WCF. [System.ServiceModel](/dotnet/api/system.servicemodel) is de naamruimte die programmatisch toegang biedt tot de basisfuncties van WCF. Service Bus maakt gebruik van veel van de objecten en kenmerken van WCF om servicecontracten te definiëren.

1. Voeg de volgende `using`-instructies toe aan het begin van *Program.cs*:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Verander de standaardnaam `EchoService` van de naamruimte in `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > In deze zelfstudie wordt gebruikgemaakt van de C#-naamruimte `Microsoft.ServiceBus.Samples`. Dit is de naamruimte van het via het contract beheerde type dat wordt gebruikt in het configuratiebestand in de sectie [De WCF-client configureren](#configure-the-wcf-client). U kunt bij het maken van dit voorbeeld elke gewenste naamruimte opgeven. Voor de zelfstudie moet u echter wel de naamruimten van het contract en de service naar behoren wijzigen in het configuratiebestand van de toepassing. De opgegeven naamruimte in het bestand *App.config* moet gelijk zijn aan de opgegeven naamruimte in de C#-bestanden.
   >

1. Direct na de `Microsoft.ServiceBus.Samples`-naamruimtedeclaratie, maar in de naamruimte, definieert u een nieuwe interface met de naam `IEchoContract`. Pas het kenmerk `ServiceContractAttribute` toe op de interface met een naamruimtewaarde van `https://samples.microsoft.com/ServiceModel/Relay/`. Plak het volgende codefragment na de naamruimtedeclaratie:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    De naamruimtewaarde verschilt van de naamruimte die u in uw code gebruikt. In plaats daarvan wordt de naamruimtewaarde gebruikt als een unieke id voor dit contract. Door de naamruimte expliciet op te geven, wordt voorkomen dat de standaardnaamruimtewaarde wordt toegevoegd aan de naam van het contract.

   > [!NOTE]
   > De naamruimte van het servicecontract bevat meestal een schematische naam die ook versie-informatie bevat. Als u versie-informatie in de naamruimte van het servicecontract gebruikt, kunnen services grote wijzigingen isoleren door een nieuw servicecontract te definiëren met een nieuwe naamruimte en het beschikbaar te maken op een nieuw eindpunt. Op deze manier kunnen clients het oude servicecontract blijven gebruiken zonder dat het hoeft te worden bijgewerkt. De versie-informatie kan bestaan uit een datum of een buildnummer. Zie [Serviceversiebeheer](/dotnet/framework/wcf/service-versioning) voor meer informatie. Voor deze zelfstudie bevat de naam van de servicecontractnaamruimte geen versie-informatie.
   >

1. Declareer in de `IEchoContract`-interface een methode voor de enkelvoudige bewerking die door het `IEchoContract`-contract wordt weergegeven in de interface en pas het kenmerk `OperationContractAttribute` toe op de methode die u wilt weergeven als onderdeel van het openbare WCF Relay-contract, zoals volgt:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Direct na het definiëren van de `IEchoContract`-interface declareert u een kanaal dat eigenschappen overneemt van de interfaces `IEchoContract` en `IClientChannel`. Ga als volgt te werk:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Een kanaal is het WCF-object waarmee de host en de client informatie aan elkaar doorgeven. Later schrijft u code voor het kanaal om informatie als echo te verzenden tussen de twee toepassingen.

1. Selecteer **Bouwen** > **Oplossing bouwen** of selecteer Ctrl + Shift + B om de juistheid van uw werk tot nu toe te controleren.

### <a name="example-of-a-wcf-contract"></a>Voorbeeld van een WCF-contract

Met de volgende code wordt een eenvoudige interface weergegeven waarmee een WCF Relay-contract wordt gedefinieerd.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Nu de interface is gemaakt, kunt u de interface implementeren.

## <a name="implement-the-wcf-contract"></a>Het WCF-contract implementeren

Als u een Azure-relay wilt maken, moet u eerst het contract maken met behulp van een interface. Zie de vorige sectie voor meer informatie over het maken van de interface. In de volgende procedure wordt de interface geïmplementeerd. Voor deze taak moet u een klasse maken met de naam `EchoService` die de door de gebruiker gedefinieerde `IEchoContract`-interface implementeert. Nadat de interface is geïmplementeerd, configureert u de interface met een *App.config*-configuratiebestand. Het configuratie bestand bevat de benodigde informatie voor de toepassing. Deze informatie omvat de naam van de service, de naam van het contract en het type protocol dat wordt gebruikt om te communiceren met de relay-service. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt. Zie [Servicecontracten implementeren](/dotnet/framework/wcf/implementing-service-contracts) voor algemene informatie over het implementeren van een servicecontract.

1. Maak een nieuwe klasse met de naam `EchoService` direct nadat de `IEchoContract`-interface is gedefinieerd. Met de klasse `EchoService` wordt de `IEchoContract`-interface geïmplementeerd.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Net als bij andere interface-implementaties kunt u de definitie implementeren in een ander bestand. In deze zelfstudie staat de implementatie echter in hetzelfde bestand als de interfacedefinitie en de `Main()`-methode.

1. Pas het kenmerk [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) toe op de `IEchoContract`-interface. Aan het kenmerk ziet u wat de servicenaam en naamruimte zijn. Wanneer u dit hebt gedaan, wordt de klasse `EchoService` als volgt weergegeven:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementeer de `Echo`-methode die is gedefinieerd in de interface `IEchoContract` in de klasse `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Selecteer **Bouwen** > **Oplossing bouwen** of selecteer CTRL + SHIFT + B.

### <a name="define-the-configuration-for-the-service-host"></a>De configuratie voor de servicehost definiëren

Het configuratiebestand lijkt op een WCF-configuratiebestand. Het bevat de servicenaam, het eindpunt en de binding. Het eindpunt is de locatie die door Azure Relay wordt weergegeven aan clients en hosts om met elkaar te communiceren. De binding is het type protocol dat wordt gebruikt om te communiceren. Het belangrijkste verschil is dat het geconfigureerde service-eindpunt verwijst naar een [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding)-binding die geen deel uitmaakt van het .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) is één van de bindingen die is gedefinieerd door de service.

1. Dubbelklik in **Solution Explorer** op **App.config** om het bestand te openen in de Visual Studio-editor.
1. Vervang in het element `<appSettings>` de tijdelijke aanduidingen door de naam van uw servicenaamruimte en de SAS-sleutel die u in een eerdere stap hebt gekopieerd.
1. In de `<system.serviceModel>`-tags voegt u een `<services>`-element toe. U kunt meerdere relay-toepassingen definiëren in een enkel configuratiebestand. In deze zelfstudie wordt er echter maar één gedefinieerd.

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Voeg in het `<services>`-element een `<service>`-element toe om de naam van de service te definiëren.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Geef in het `<service>`-element de locatie op van het eindpuntcontract en het type binding voor het eindpunt.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Het eindpunt definieert waar de client zoekt naar de hosttoepassing. Later in de zelfstudie wordt deze stap gebruikt om een URI te maken waarmee de host volledig beschikbaar wordt gemaakt via Azure Relay. Aan de hand van de binding weet u dat TCP wordt gebruikt als protocol voor communicatie met de relay-service.

1. Selecteer **Bouwen** > **Oplossing bouwen** of selecteer Ctrl + Shift + B om de juistheid van uw werk tot nu toe te controleren.

### <a name="example-of-implementation-of-a-service-contract"></a>Voorbeeld van implementatie van het servicecontract

In de volgende code staat de implementatie van het servicecontract.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

In de volgende code staat de basisindeling van het aan de servicehost gekoppelde bestand *App.config*.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>De WCF-service hosten en uitvoeren om u te registreren bij de relay-service

In deze stap wordt beschreven hoe u een Azure Relay-service uitvoert.

### <a name="create-the-relay-credentials"></a>De relay-referenties maken

1. Maak in `Main()` twee variabelen waarin de naamruimte en de SAS-sleutel uit het consolevenster worden opgeslagen.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    De SAS-sleutel wordt later gebruikt voor toegang tot uw project. De naamruimte wordt als parameter doorgegeven aan `CreateServiceUri` voor het maken van een service-URI.

1. Met een [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior)-object geeft u aan dat u een SAS-sleutel gaat gebruiken als referentietype. Voeg de volgende code toe direct na de code die u in de vorige stap hebt toegevoegd.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Een basisadres voor de service maken

Na de code die u hebt toegevoegd in de vorige sectie, maakt u een `Uri`-exemplaar voor het basisadres van de service. Deze URI bevat het Service Bus-schema, de naamruimte en het pad naar de service-interface.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

De waarde 'sb' is een afkorting voor het Service Bus-schema. Dit geeft aan dat TCP als protocol wordt gebruikt. Dit schema is ook eerder aangegeven in het configuratiebestand, toen [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) is opgegeven als binding.

De URI voor deze zelfstudie is `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>De servicehost maken en configureren

1. Stel terwijl u werkt in `Main()` de connectiviteitsmodus in op `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    In de connectiviteitsmodus wordt het protocol beschreven dat de service gebruikt om te communiceren met de relay-service: HTTP of TCP. Met de standaardinstelling `AutoDetect` probeert de service via TCP verbinding te maken met Azure Relay als deze optie beschikbaar is. HTTP wordt gebruikt als TCP niet beschikbaar is. Dit resultaat wijkt af van het protocol dat de service opgeeft voor clientcommunicatie. Dat protocol wordt bepaald op basis van de gebruikte binding. Een service kan bijvoorbeeld gebruikmaken van de binding [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding), wat betekent dat het eindpunt met clients communiceert via HTTP. Voor diezelfde service zou `ConnectivityMode.AutoDetect` worden opgegeven, zodat de service via TCP met Azure Relay communiceert.

1. Maak de servicehost met de URI die u eerder in dit gedeelte hebt gemaakt.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    De servicehost is het WCF-object dat de service start. Hier geeft u aan welk type service u wilt maken (een `EchoService`-type) en het adres waarmee u de service beschikbaar wilt maken.

1. Boven aan het bestand *Program.cs* voegt u verwijzingen toe naar [System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) en [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Configureer vervolgens in `Main()` het eindpunt voor openbare toegang.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Met deze stap laat u de relay-service weten dat uw toepassing openbaar te vinden is door de Atom-feed voor uw project te doorzoeken. Als u `DiscoveryType` instelt op `private`, kan een client nog steeds toegang krijgen tot de service. De service wordt echter niet weergegeven wanneer er wordt gezocht naar de `Relay`-naamruimte. In plaats daarvan moet de client op voorhand al op de hoogte zijn van het eindpuntpad.

1. Pas de servicereferenties toe op de service-eindpunten die zijn opgegeven in het bestand *App.config*:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Zoals eerder vermeld, kan het zijn dat u meerdere services en eindpunten hebt opgegeven in het configuratiebestand. Als dit het geval is, is met de code het hele configuratiebestand doorzocht naar eindpunten waarop uw referenties kunnen worden toegepast. Voor deze zelfstudie heeft het configuratiebestand slechts één eindpunt.

### <a name="open-the-service-host"></a>De servicehost openen

1. Voeg nog steeds in `Main()` de volgende regel toe om de service te openen.

    ```csharp
    host.Open();
    ```

1. Stel de gebruiker ervan op de hoogte dat de service wordt uitgevoerd en leg uit hoe de service kan worden afgesloten.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Sluit de servicehost wanneer u klaar bent.

    ```csharp
    host.Close();
    ```

1. Selecteer Ctrl + Shift + B om het project op te bouwen.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Voorbeeld waarin een service in een consoletoepassing wordt gehost

Uw voltooide servicecode moet er als volgt uitzien. De code bevat het servicecontract en de implementatie uit de vorige stappen in de zelfstudie. Hierin wordt de service gehost in een consoletoepassing.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Een WCF-client maken voor het servicecontract

De volgende taak bestaat uit het maken van een clienttoepassing en het definiëren van het servicecontract dat u later gaat implementeren. Veel van deze stappen lijken op de stappen voor het maken van een service: een contract opgeven, een *App.config*-bestand bewerken, referenties gebruiken om verbinding te maken met de relay-service, enzovoort. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

1. Maak een nieuw project in de huidige Visual Studio-oplossing voor de client:

   1. Klik in **Solution Explorer** met de rechtermuisknop op de oplossing (niet het project) en selecteer **Toevoegen** > **Nieuw project**.
   1. Selecteer in **Een nieuw project toevoegen** de optie **Console-app (.NET Framework)** voor C# en selecteer **Volgende**.
   1. Noem het project *EchoClient* en selecteer **Maken**.

1. Dubbelklik in **Solution Explorer** in het project **EchoClient** op **Program.cs** om het bestand in de editor te openen, als het nog niet was geopend.
1. Verander de standaardnaam `EchoClient` van de naamruimte in `Microsoft.ServiceBus.Samples`.
1. Installeer het [Service Bus-pakket NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus):

   1. Klik in **Solution Explorer** met de rechtermuisknop op het project **EchoClient** en selecteer **NuGet-pakketten beheren**.
   1. Selecteer **Bladeren**, en zoek en selecteer vervolgens **WindowsAzure.ServiceBus**. Selecteer **Installeren** en accepteer de gebruiksvoorwaarden.

      ![Service Bus-pakket installeren][4]

1. Voeg een `using`-instructie toe voor de naamruimte [System.ServiceModel](/dotnet/api/system.servicemodel) in het bestand *Program.cs*.

    ```csharp
    using System.ServiceModel;
    ```

1. Voeg de definitie van het servicecontract toe aan de naamruimte, zoals wordt weergegeven in het volgende voorbeeld. Deze definitie is identiek aan de definitie die is gebruikt in het project **Service**. Voeg deze code toe aan de bovenkant van de `Microsoft.ServiceBus.Samples`-naamruimte.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Selecteer Ctrl + Shift + B om de client op te bouwen.

### <a name="example-of-the-echoclient-project"></a>Voorbeeld van het project EchoClient

In de volgende code ziet u de huidige status van het bestand *Program.cs* in het project **EchoClient**.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>De WCF-client configureren

In deze stap maakt u een *App.config*-bestand voor een eenvoudige clienttoepassing die de service gebruikt die u eerder in deze zelfstudie hebt gemaakt. Dit *App.config*-bestand definieert het contract, de binding en de naam van het eindpunt. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

1. Dubbelklik in **Solution Explorer** in het project **EchoClient** op het bestand **App.config** om dit te openen in de Visual Studio-editor.
1. Vervang in het element `<appSettings>` de tijdelijke aanduidingen door de naam van uw servicenaamruimte en de SAS-sleutel die u in een eerdere stap hebt gekopieerd.
1. In het `system.serviceModel`-element voegt u een `<client>`-element toe.

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Deze code declareert dat u een clienttoepassing van het type WCF maakt.

1. Geef in het `client`-element de naam, het contract en het type binding op voor het eindpunt.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Deze code definieert de naam van het eindpunt. Ook definieert de code het contract dat in de service us gedefinieerd en het feit dat de clienttoepassing gebruikmaakt van TCP om met Azure Relay te communiceren. De naam van het eindpunt wordt in de volgende stap gebruikt om deze eindpuntconfiguratie te koppelen aan de service-URI.

1. Klik op **Bestand** > **Alles opslaan**.

### <a name="example-of-the-appconfig-file"></a>Voorbeeld van het bestand App.config

In de volgende code staat het *App.config*-bestand voor de Echo-client.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>De WCF-client implementeren

In deze sectie implementeert u een eenvoudige clienttoepassing die de service gebruikt die u eerder in deze zelfstudie hebt gemaakt. De service en client voeren veel van dezelfde bewerkingen uit om Azure Relay te openen:

* De connectiviteitsmodus wordt ingesteld.
* De URI wordt gemaakt waarmee de hostservice wordt gezocht.
* De beveiligingsreferenties worden gedefinieerd.
* De referenties worden toegepast op de verbinding.
* De verbinding wordt geopend.
* De toepassingsspecifieke taken worden uitgevoerd.
* De verbinding wordt gesloten.

Een van de belangrijkste verschillen is echter dat de clienttoepassing een kanaal gebruikt om verbinding te maken met de relay-service. De service roept **ServiceHost** aan. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

### <a name="implement-a-client-application"></a>Een clienttoepassing implementeren

1. Stel de connectiviteitsmodus in op `AutoDetect`. Voeg de volgende code toe in de `Main()`-methode van de **EchoClient**-toepassing.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Geef variabelen op voor de waarden van de naamruimte van de service. Geef ook de SAS-sleutel op die in de console wordt weergegeven.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Maak de URI die de locatie definieert van de host in uw relay-project.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Maak het referentieobject voor het eindpunt van uw servicenaamruimte.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Maak de kanaalfactory die de configuratie laadt die wordt beschreven in het *App.config*-bestand.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Een kanaalfactory is een WCF-object dat een kanaal maakt waardoor de service- en clienttoepassingen communiceren.

1. De referenties toepassen.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Maak en open het kanaal naar de service.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Schrijf de algemene gebruikersinterface en de functionaliteit voor de echo.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Voor de code wordt het exemplaar van het kanaalobject gebruikt als proxy voor de service.

1. Sluit het kanaal en sluit de factory.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Voorbeeldcode voor deze zelfstudie

Uw voltooide code moet er als volgt uitzien. Deze code laat zien hoe u een clienttoepassing maakt, hoe u de bewerkingen van de service aanroept en hoe u de client sluit nadat het aanroepen van de bewerkingen is voltooid.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>De toepassingen uitvoeren

1. Selecteer Ctrl + Shift + B om de oplossing te bouwen. Met deze actie worden zowel het clientproject als het serviceproject opgebouwd die u in de vorige stappen hebt gemaakt.
1. Voordat u de clienttoepassing uitvoert, moet u ervoor zorgen dat de servicetoepassing wordt uitgevoerd. Klik in **Solution Explorer** met de rechtermuisknop op de oplossing **EchoService** en selecteer **Eigenschappen**.
1. Kies **Eigenschappenvensters**, **Algemene eigenschappen** > **Opstartproject** en kies vervolgens **Meerdere opstartprojecten**. Controleer of **EchoService** bovenaan staat in de lijst.
1. Stel het vak **Actie** van de projecten **EchoService** en **EchoClient** in op **Start**.

    ![Vensters met projecteigenschappen][5]

1. Selecteer **Projectafhankelijkheden**. Selecteer in **Projecten** de optie **EchoClient**. Zorg dat voor **Is afhankelijk van** de optie **EchoService** is geselecteerd.

    ![Projectafhankelijkheden][6]

1. Selecteer **OK** om **Eigenschappenvensters** te sluiten.
1. Selecteer F5 om beide projecten uit te voeren.
1. Beide consolevensters worden geopend en u wordt gevraagd de naam van de naamruimte op te geven. De service moet eerst worden uitgevoerd. Voer daarom in het **EchoService**-consolevenster de naamruimte in en selecteer vervolgens Enter.
1. Daarna wordt u gevraagd om uw SAS-sleutel. Voer de SAS-sleutel in en selecteer Enter.

    Hier volgt een voorbeeld van de uitvoer van het consolevenster. De waarden hier zijn slechts voorbeelden.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    De servicetoepassing geeft in het consolevenster het adres weer dat wordt gebruikt, zoals in het volgende voorbeeld te zien is.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. Voer in het consolevenster **EchoClient** dezelfde gegevens in als de gegevens die u eerder al hebt ingevoerd voor de servicetoepassing. Voer dezelfde servicenaamruimte en SAS-sleutel in voor de clienttoepassing.
1. Wanneer u deze waarden hebt ingevoerd, opent de client een kanaal naar de service en wordt u gevraagd om tekst in te voeren, zoals te zien is in het volgende console-uitvoervoorbeeld.

    `Enter text to echo (or [Enter] to exit):`

    Voer tekst in om naar de servicetoepassing te verzenden en selecteer Enter. Deze tekst wordt naar de service verzonden via een Echo-servicebewerking en wordt in het serviceconsolevenster weergegeven, zoals in de volgende voorbeelduitvoer te zien is.

    `Echoing: My sample text`

    De clienttoepassing ontvangt de geretourneerde waarde van de `Echo`-bewerking: de oorspronkelijke tekst. Deze wordt vervolgens weergegeven in het consolevenster. De volgende tekst is een voorbeeld van de uitvoer van het clientconsolevenster.

    `Server echoed: My sample text`

1. U kunt op deze manier doorgaan met het verzenden van berichten van de client naar de service. Wanneer u klaar bent, selecteert u Enter in de client- en serviceconsolevensters om beide toepassingen te sluiten.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Een on-premises WCF REST-service zichtbaar maken voor een client buiten uw netwerk](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
