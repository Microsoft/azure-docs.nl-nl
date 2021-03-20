---
title: 'Zelfstudie: REST bouwen met Azure Relay'
description: 'Zelfstudie: Een Azure Relay-hosttoepassing bouwen voor het weergeven van een op REST gebaseerde interface.'
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 0620f55650d0e4da0cd7a616649df952f3017455
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88922324"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Zelfstudie: Azure WCF Relay REST

In deze zelfstudie wordt beschreven hoe u een eenvoudige Azure Relay-hosttoepassing bouwt voor het weergeven van een op REST gebaseerde interface. Met REST kan een webclient, zoals een webbrowser, toegang krijgen tot de Service Bus-API's via HTTP-aanvragen.

In deze zelfstudie wordt het WCF (Windows Communication Foundation) REST-programmeermodel gebruikt om een REST-service in Azure Relay te maken. Zie [WCF REST Programming Model](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) (WCF REST-programmeermodel) en [Designing and Implementing Services](/dotnet/framework/wcf/designing-and-implementing-services) (Services ontwerpen en implementeren) voor meer informatie.

In deze zelfstudie gaat u de volgende taken uitvoeren:

> [!div class="checklist"]
>
> * Voldoen aan de vereisten voor deze zelfstudie.
> * Een Relay-naamruimte maken.
> * Een op REST gebaseerd WCF-servicecontract definiëren.
> * Een op REST gebaseerd WCF-contract implementeren.
> * De op REST gebaseerde WCF-service hosten en uitvoeren.
> * De service uitvoeren en testen.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* [Visual Studio 2015 of hoger](https://www.visualstudio.com). In de voorbeelden in deze zelfstudie wordt Visual Studio 2019 gebruikt.
* Azure-SDK voor .NET. Installeer de SDK via de [SDK-downloadpagina](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Een Relay-naamruimte maken

Als u de relayfuncties in Azure wilt gebruiken, moet u eerst een servicenaamruimte maken. Een naamruimte biedt een scoping container voor het verwerken van Azure-resources in uw toepassing. Volg [deze instructies](relay-create-namespace-portal.md) om een Relay-naamruimte te maken.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Een op REST gebaseerd WCF-servicecontract definiëren voor gebruik met Azure Relay

Wanneer u een service maakt in de stijl van WCF REST, moet u het contract definiëren. Het contract geeft aan welke bewerkingen door de host worden ondersteund. Een servicebewerking lijkt op een webservicemethode. Definieer een contract met een interface in C++, C# of Visual Basic. Elke methode in de interface komt overeen met een specifieke servicebewerking. Pas op elke interface het kenmerk [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) toe en op elke bewerking het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute). 

> [!TIP]
> Als een methode in een interface met het kenmerk [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) niet beschikt over het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), wordt die methode niet weergegeven. Het voorbeeld na de procedure bevat de code die voor deze taken wordt gebruikt.

Het belangrijkste verschil tussen een WCF-contract en een contract in REST-stijl is de toevoeging van een eigenschap aan het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Met deze eigenschap kunt u een methode in uw interface toewijzen aan een methode aan de andere kant van de interface. In dit voorbeeld wordt het kenmerk [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) gebruikt om een methode te koppelen aan `HTTP GET`. Hierdoor kan Service Bus opdrachten die naar de interface worden verzonden, correct ophalen en interpreteren.

### <a name="to-create-a-contract-with-an-interface"></a>Een contract met een interface maken

1. Start Microsoft Visual Studio als beheerder. Klik hiervoor met de rechtermuisknop op het pictogram van het Visual Studio-programma en selecteer vervolgens **Als administrator uitvoeren**.
1. Selecteer **Een nieuw project maken** in Visual Studio.
1. Kies in het venster **Een nieuw project maken** **Console-app (.NET Framework)** voor C# en selecteer **Volgende**.
1. Geef het project de naam *ImageListener*. Gebruik de standaardwaarde voor **Locatie** en selecteer vervolgens **Maken**.

   Voor een C#-project maakt Visual Studio een bestand *Program.cs*. Deze klasse bevat een lege `Main()`-methode, vereist voor de juiste opbouw van een consoletoepassingsproject.

1. Klik in **Solution Explorer** met de rechtermuisknop op het project **ImageListener** en selecteer vervolgens **NuGet-pakketten beheren**.
1. Selecteer **Bladeren**, en zoek en kies vervolgens **WindowsAzure.ServiceBus**. Selecteer **Installeren** en accepteer de gebruiksvoorwaarden.

    In deze stap worden verwijzingen toegevoegd naar Service Bus en *System.ServiceModel.dll*. Met dit pakket worden er automatisch verwijzingen toegevoegd aan de Service Bus-bibliotheken en aan `System.ServiceModel` van WCF.

1. Voeg expliciet een verwijzing naar `System.ServiceModel.Web.dll` toe aan het project. Klik in **Solution Explorer** met de rechtermuisknop op **Verwijzingen** onder de projectmap en selecteer **Verwijzing toevoegen**.
1. Selecteer in het venster **Verwijzing toevoegen** de optie **Framework** en typ *System.ServiceModel.Web* in het **zoekvak.** Schakel het selectievakje **System.ServiceModel.Web** in en klik vervolgens op **OK**.

Breng vervolgens de volgende codewijzigingen aan in het project:

1. Voeg aan het begin van het bestand `using`Program.cs *de volgende*-instructies toe.

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) is de naamruimte die programmatisch toegang biedt tot de basisfuncties van WCF. WCF Relay maakt gebruik van verschillende objecten en kenmerken van WCF om servicecontracten te definiëren. U gebruikt deze naamruimte in het merendeel van uw relaytoepassingen.
    * [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) helpt bij het definiëren van het kanaal. Dit is het object dat u gebruikt voor communicatie met Azure Relay en de clientwebbrowser.
    * [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) bevat de typen waarmee u webtoepassingen kunt maken.

1. Wijzig de naam van de naamruimte `ImageListener` in `Microsoft.ServiceBus.Samples`.

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Definieer meteen na de openingsaccolade van de naamruimtedeclaratie een nieuwe interface met de naam `IImageContract` en pas het kenmerk `ServiceContractAttribute` met de waarde `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1` toe op de interface. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    De naamruimtewaarde verschilt van de naamruimte die u in uw code gebruikt. De waarde voor Namespace is een unieke id voor dit contract en moet versie-informatie bevatten. Zie [Serviceversiebeheer](/dotnet/framework/wcf/service-versioning) voor meer informatie. Door de naamruimte expliciet op te geven, wordt voorkomen dat de standaardnaamruimtewaarde wordt toegevoegd aan de naam van het contract.

1. Declareer in de `IImageContract`-interface een methode voor de enkelvoudige bewerking die door het `IImageContract`-contract wordt weergegeven in de interface en pas het kenmerk `OperationContract` toe op de methode die u wilt weergeven als onderdeel van het openbare Service Bus-contract.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. Voeg in het kenmerk `OperationContract` de waarde `WebGet` toe.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Door het toevoegen van de waarde `WebGet` kan de relayservice HTTP GET-aanvragen routeren naar `GetImage` en de retourwaarden van `GetImage` omzetten in een `HTTP GETRESPONSE`-antwoord. Verderop in de zelfstudie gaat u een webbrowser gebruiken om toegang te krijgen tot deze methode en om de installatiekopie weer te geven in de browser.

1. Declareer direct na de `IImageContract`-definitie een kanaal dat de eigenschappen overneemt van de `IImageContract`- en `IClientChannel`-interface.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Een kanaal is het WCF-object waarmee de service en de client informatie aan elkaar doorgeven. U maakt het kanaal later in uw hosttoepassing. Azure Relay gebruikt dit kanaal om de HTTP GET-aanvragen van uw browser door te geven aan uw `GetImage`-implementatie. De relay gebruikt dit kanaal ook om de retourwaarde `GetImage` om te zetten in een `HTTP GETRESPONSE` voor de clientbrowser.

1. Selecteer **Bouwen** > **Oplossing bouwen** om de juistheid van uw werk tot nu toe te controleren.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Voorbeeld van de definitie van een WCF Relay-contract

Met de volgende code wordt een eenvoudige interface weergegeven waarmee een WCF Relay-contract wordt gedefinieerd.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>Een op REST gebaseerd WCF-servicecontract implementeren

Als u een WCF Relay-service in REST-stijl wilt maken, moet u eerst het contract maken met behulp van een interface. De volgende stap is het implementeren van de interface. Hiervoor moet u een klasse maken met de naam `ImageService` die de door de gebruiker gedefinieerde interface `IImageContract` implementeert. Nadat het contract is geïmplementeerd, configureert u de interface met een bestand *App.config*. Het configuratie bestand bevat de benodigde informatie voor de toepassing. Deze informatie omvat de naam van de service, de naam van het contract en het type protocol dat wordt gebruikt om te communiceren met de relay-service. Het voorbeeld na de procedure bevat de code die voor deze taken wordt gebruikt.

Net als bij de vorige stappen, is er weinig verschil tussen het implementeren van een contract in REST-stijl en een WCF Relay-contract.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Een Service Bus-contract in REST-stijl implementeren

1. Maak een nieuwe klasse met de naam `ImageService` direct nadat de `IImageContract`-interface is gedefinieerd. Met de klasse `ImageService` wordt de `IImageContract`-interface geïmplementeerd.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Net als bij andere interface-implementaties kunt u de definitie implementeren in een ander bestand. In deze zelfstudie wordt de implementatie echter weergegeven in hetzelfde bestand als de interfacedefinitie en de `Main()`-methode.

1. Pas het kenmerk [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) toe op de klasse `IImageService` om aan te geven dat de klasse een implementatie is van een WCF-contract.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Zoals eerder is vermeld, is deze naamruimte geen traditionele naamruimte, maar maakt deze deel uit van de WCF-architectuur waarmee het contract wordt geïdentificeerd. Zie [Data Contract Names](/dotnet/framework/wcf/feature-details/data-contract-names/) (Namen van gegevenscontracten) voor meer informatie.

1. Voeg een *JPG*-afbeelding toe aan uw project. Dit is een afbeelding die de service weergeeft in de ontvangende browser.

   1. Klik met de rechtermuisknop op het project en selecteer **Toevoegen**.
   1. Selecteer vervolgens **Bestaand item**.
   1. Gebruik het dialoogvenster **Bestaand item toevoegen** om naar een geschikt JPG-bestand te bladeren en selecteer vervolgens **Toevoegen**. Wanneer u het bestand toevoegt, selecteert u **Alle bestanden** in de vervolgkeuzelijst naast **Bestandsnaam**.

   In de rest van deze zelfstudie wordt ervan uitgegaan dat de naam van de afbeelding *image.jpg* is. Als u een ander bestand gebruikt, moet u de naam van de afbeelding wijzigen of moet u uw code aanpassen.

1. Als u wilt controleren of de service die wordt uitgevoerd het afbeeldingsbestand kan vinden, klikt u in **Solution Explorer** met de rechtermuisknop op het afbeeldingsbestand en kiest u vervolgens **Eigenschappen**. Stel **Naar uitvoermap kopiëren** in **Eigenschappen** in op **Kopiëren indien nieuwer**.

1. Gebruik de procedure in [Een contract met een interface maken](#to-create-a-contract-with-an-interface) om een verwijzing naar de assembly *System.Drawing.dll* toe te voegen aan het project.

1. Voeg de volgende gekoppelde `using`-instructies toe:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. Voeg in de klasse `ImageService` de volgende constructor toe waarmee de bitmap wordt geladen en voorbereid op verzending naar de clientbrowser:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Voeg direct na de vorige code de volgende `GetImage`-methode toe aan de klasse `ImageService` om een HTTP-bericht met de afbeelding te retourneren.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Deze implementatie maakt gebruik van `MemoryStream` om de afbeelding op te halen en deze voor te bereiden op streaming naar de browser. De streampositie start bij nul, de stream wordt gedeclareerd als *JPG* en de gegevens worden gestreamd.

1. Selecteer **Bouwen** > **Oplossing bouwen**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>De configuratie voor het uitvoeren van de webservice in Service Bus definiëren

1. Dubbelklik in **Solution Explorer** op **App.config** om het bestand te openen in de Visual Studio-editor.

    Het bestand *App.Config* bevat de servicenaam, het eindpunt en de binding. Het eindpunt is de locatie die door Azure Relay wordt weergegeven aan clients en hosts om met elkaar te communiceren. De binding is het type protocol dat wordt gebruikt om te communiceren. Hier is het belangrijkste verschil dat het geconfigureerde service-eindpunt verwijst naar een [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding)-binding.

1. Het `<system.serviceModel>` XML-element is een WCF-element waarmee een of meer services worden gedefinieerd. Hier wordt het gebruikt om de servicenaam en het eindpunt te definiëren. Voeg onder aan het `<system.serviceModel>`-element (maar nog wel binnen `<system.serviceModel>`) een `<bindings>`-element toe met de volgende inhoud:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Met deze inhoud definieert u de bindingen die in de toepassing worden gebruikt. U kunt meerdere bindingen definiëren, maar in deze zelfstudie definieert u er slechts één.

    Met de vorige code definieert u een [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding)-binding van WCF Relay met `relayClientAuthenticationType` ingesteld op `None`. Met deze instelling geeft u aan dat voor een eindpunt dat deze binding gebruikt, geen clientreferentie is vereist.

1. Na het `<bindings>`-element voegt u een `<services>`-element toe. Net zoals bij bindingen kunt u meerdere services definiëren in een enkel configuratiebestand. In deze zelfstudie definieert u slechts een service.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Met deze inhoud configureert u een service die de vooraf gedefinieerde standaardversie van de `webHttpRelayBinding` gebruikt. Deze service gebruikt ook de standaard `sbTokenProvider`, die in de volgende stap wordt gedefinieerd.

1. Maak na het `<services>`-element een `<behaviors>`-element met de volgende inhoud, waarbij u `SAS_KEY` vervangt door de SAS-sleutel (Shared Access Signature). Als u een SAS-sleutel wilt ophalen uit de [Azure-portal][Azure portal], raadpleegt u [Get management credentials](service-bus-relay-tutorial.md#get-management-credentials) (Beheerreferenties ophalen).

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. Vervang in *App.config*, in het `<appSettings>`-element, de hele verbindingsreekswaarde door de verbindingsreeks die u eerder hebt verkregen via de portal.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Selecteer **Bouwen** > **Oplossing bouwen** om de volledige oplossing op te bouwen.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Voorbeeld om een op REST gebaseerd WCF-servicecontract te implementeren

De volgende code toont het contract en de service-implementatie voor een op REST gebaseerde service die in Service Bus wordt uitgevoerd met de binding `WebHttpRelayBinding`.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

In het volgende voorbeeld wordt het aan de service gekoppelde bestand *App.config* weergegeven.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>De op REST gebaseerde WCF-service hosten voor het gebruik van Azure Relay

In dit gedeelte wordt beschreven hoe u met behulp van een consoletoepassing en WCF Relay een webservice uitvoert. Een volledig overzicht van de code die in dit gedeelte wordt geschreven, vindt u in het voorbeeld na de procedure.

### <a name="to-create-a-base-address-for-the-service"></a>Een basisadres voor de service maken

1. Maak in de declaratie van de functie `Main()` een variabele om de naamruimte van uw project op te slaan. Vergeet niet om `yourNamespace` te vervangen door de naam van de Relay-naamruimte die u eerder hebt gemaakt.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Service Bus gebruikt de naam van uw naamruimte om een unieke URI te maken.

1. Maak een `Uri`-exemplaar voor het basisadres van de service die is gebaseerd op de naamruimte.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>De webservicehost maken en configureren

Maak binnen `Main()` de webservicehost op basis van het URI-adres dat u eerder in dit gedeelte hebt gemaakt.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

De servicehost is het WCF-object waarmee de hosttoepassing wordt gestart. In dit voorbeeld wordt het type host dat u wilt maken (een `ImageService`), doorgegeven en ook het adres waarop u de hosttoepassing wilt weergeven.

### <a name="to-run-the-web-service-host"></a>De webservicehost uitvoeren

1. Voeg nog steeds in `Main()` de volgende regel toe om de service te openen.

    ```csharp
    host.Open();
    ```

    De service wordt nu uitgevoerd.

1. Geef een bericht weer waarin wordt aangegeven dat de service wordt uitgevoerd en hoe deze kan worden gestopt.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Sluit de servicehost wanneer u klaar bent.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Voorbeeld van het servicecontract en de implementatie ervan

Het volgende voorbeeld bevat het servicecontract en de implementatie uit de vorige stappen in de zelfstudie. Hierin wordt de service gehost in een consoletoepassing. Compileer de volgende code in een uitvoerbaar bestand met de naam *ImageListener.exe*.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>De service uitvoeren en testen

Nadat u de oplossing hebt opgebouwd, gaat u als volgt te werk om de toepassing uit te voeren:

1. Druk op F5 of blader naar de locatie van het uitvoerbare bestand, *ImageListener\bin\Debug\ImageListener.exe*, om de service uit te voeren. Zorg ervoor dat de app actief blijft. Dit is vereist voor het uitvoeren van de volgende stap.
1. Kopieer en plak het adres van de opdrachtprompt in een browser om de afbeelding te zien.
1. Druk als u klaar bent op Enter in het opdrachtpromptvenster om de app te sluiten.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een toepassing ontwikkeld die gebruikmaakt van de Azure Relay-service. Raadpleeg de volgende artikelen voor meer informatie:

* [Wat is Azure Relay?](relay-what-is-it.md)
* [Tutorial: Expose an on-premises WCF REST service to external client by using Azure WCF Relay](service-bus-relay-tutorial.md) (Zelfstudie: een on-premises WCF REST-service beschikbaar maken voor externe clients met behulp van Azure WCF Relay)

[Azure portal]: https://portal.azure.com
