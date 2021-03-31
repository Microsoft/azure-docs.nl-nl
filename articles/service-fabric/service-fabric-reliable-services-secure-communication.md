---
title: 'Externe communicatie met C beveiligen met de service #'
description: Meer informatie over het beveiligen van communicatie tussen service op afstand voor betrouw bare C#-services die worden uitgevoerd in een Azure Service Fabric-cluster.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ba68df53f1f21b9ff360772fe1a60c93c8df74d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86252212"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Externe communicatie met beveiligde service in een C#-service
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java op Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Beveiliging is een van de belangrijkste aspecten van de communicatie. Het Reliable Services-toepassings raamwerk bevat enkele vooraf ontwikkelde communicatie stacks en hulpprogram ma's die u kunt gebruiken om de beveiliging te verbeteren. In dit artikel wordt beschreven hoe u de beveiliging verbetert wanneer u service remoting gebruikt in een C#-service. Het is gebaseerd op een bestaand [voor beeld](service-fabric-reliable-services-communication-remoting.md) waarin wordt uitgelegd hoe u externe toegang instelt voor betrouw bare services die zijn geschreven in C#. 

Voer de volgende stappen uit om een service te helpen beveiligen wanneer u service voor externe communicatie met C#-Services gebruikt:

1. Een interface maken, `IHelloWorldStateful` waarmee de methoden worden gedefinieerd die beschikbaar zijn voor een externe procedure aanroep van uw service. Uw service gebruikt `FabricTransportServiceRemotingListener` , die is gedeclareerd in de `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` naam ruimte. Dit is een `ICommunicationListener` implementatie die mogelijkheden biedt voor externe communicatie.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Luister instellingen en beveiligings referenties toevoegen.

    Zorg ervoor dat het certificaat dat u wilt gebruiken om uw service communicatie te beveiligen, is geïnstalleerd op alle knoop punten in het cluster. 
    
    > [!NOTE]
    > Op Linux-knoop punten moet het certificaat aanwezig zijn als PEM-bestanden in de */var/lib/sfcerts* -map. Zie [locatie en indeling van X. 509-certificaten op Linux-knoop punten](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes)voor meer informatie. 

    Er zijn twee manieren waarop u listener-instellingen en beveiligings referenties kunt opgeven:

   1. Geef ze rechtstreeks in de service code op:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Geef ze op met behulp van een [configuratie pakket](service-fabric-application-and-service-manifests.md):

       Voeg een benoemde `TransportSettings` sectie toe aan het settings.xml-bestand.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       In dit geval ziet de- `CreateServiceReplicaListeners` methode er als volgt uit:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Als u een `TransportSettings` sectie toevoegt in het settings.xml bestand, `FabricTransportRemotingListenerSettings` worden standaard alle instellingen van deze sectie geladen.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        In dit geval ziet de- `CreateServiceReplicaListeners` methode er als volgt uit:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Wanneer u methoden aanroept voor een beveiligde service met behulp van de externe stack, in plaats van de `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klasse te gebruiken om een Service proxy te maken, gebruikt u `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` . Passe in `FabricTransportRemotingSettings` , die bevat `SecurityCredentials` .

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Als de client code wordt uitgevoerd als onderdeel van een service, kunt u laden `FabricTransportRemotingSettings` vanuit het settings.xml-bestand. Maak een HelloWorldClientTransportSettings-sectie die vergelijkbaar is met de service code, zoals eerder wordt weer gegeven. Breng de volgende wijzigingen aan in de client code:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Als de client niet wordt uitgevoerd als onderdeel van een service, kunt u een client_name.settings.xml bestand maken op dezelfde locatie als de client_name.exe. Maak vervolgens een TransportSettings-sectie in dat bestand.

    Net als bij de service worden `TransportSettings` `FabricTransportRemotingSettings` alle instellingen uit deze sectie standaard geladen als u een sectie toevoegt in client settings.xml/client_name.settings.xml.

    In dat geval is de eerdere code nog verder vereenvoudigd:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Als volgende stap leest u [Web API with OWIN in reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md).
