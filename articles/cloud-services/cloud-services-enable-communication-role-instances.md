---
title: Communicatie voor rollen in Cloud Services (klassiek) | Microsoft Docs
description: Voor rolinstanties in Cloud Services kunnen eind punten (http, HTTPS, TCP, UDP) worden gedefinieerd die communiceren met de buiten-of andere rolinstanties.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 82aa1579a1f7feb36732153341e1eacf266a7218
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743029"
---
# <a name="enable-communication-for-role-instances-in-azure-cloud-services-classic"></a>Communicatie inschakelen voor rolinstanties in azure Cloud Services (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Cloud service rollen communiceren via interne en externe verbindingen. Externe verbindingen worden **invoer eindpunten** genoemd terwijl interne verbindingen **interne eind punten** worden genoemd. In dit onderwerp wordt beschreven hoe u de [service definitie](cloud-services-model-and-package.md#csdef) wijzigt om eind punten te maken.

## <a name="input-endpoint"></a>Invoer eindpunt
Het invoer eindpunt wordt gebruikt wanneer u een poort aan de buiten kant beschikbaar wilt stellen. U geeft het protocol type en de poort van het eind punt op die vervolgens worden toegepast voor de externe en interne poorten voor het eind punt. Als u wilt, kunt u een andere interne poort voor het eind punt opgeven met het kenmerk [localPort](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) .

Het invoer eindpunt kan gebruikmaken van de volgende protocollen: **http, HTTPS, TCP, UDP**.

Als u een invoer eindpunt wilt maken, voegt u het onderliggende element **InputEndpoint** toe aan het **eind punt** -element van een web-of worker-rol.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Invoer eindpunt van instantie
Invoer eindpunten voor instanties zijn vergelijkbaar met invoer eindpunten, maar u kunt met behulp van door sturen via poort op het load balancer toewijzen van specifieke open bare poorten voor elke afzonderlijke rolinstantie. U kunt één open bare poort of een bereik van poorten opgeven.

Het invoer eindpunt van het exemplaar kan alleen **TCP** of **UDP** gebruiken als protocol.

Als u een invoer eindpunt voor een instantie wilt maken, voegt u het onderliggende **InstanceInputEndpoint** -element toe aan het **eind punt** van een web-of werk rollen.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Intern eind punt
Er zijn interne eind punten beschikbaar voor communicatie tussen exemplaren. De poort is optioneel en als deze wordt wegge laten, wordt een dynamische poort toegewezen aan het eind punt. Een poort bereik kan worden gebruikt. Er geldt een limiet van vijf interne eind punten per rol.

Het interne eind punt kan gebruikmaken van de volgende protocollen: **http, TCP, UDP**.

Als u een intern invoer eindpunt wilt maken, voegt u het onderliggende **InternalEndpoint** -element toe aan het **eind punt** -element van een web-of worker-rol.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

U kunt ook een poort bereik gebruiken.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Werk rollen versus webrollen
Er is een klein verschil met eind punten wanneer u werkt met zowel werk nemers als webrollen. De webrole moet mini maal één invoer eindpunt hebben met het **http-** protocol.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>De .NET-SDK gebruiken om toegang te krijgen tot een eind punt
De door Azure beheerde bibliotheek biedt methoden om rolinstanties te communiceren tijdens runtime. Van code die binnen een rolinstantie wordt uitgevoerd, kunt u informatie ophalen over het bestaan van andere rolinstanties en hun eind punten, evenals informatie over de huidige rolinstantie.

> [!NOTE]
> U kunt alleen informatie ophalen over rolinstanties die worden uitgevoerd in uw Cloud service en die ten minste één intern eind punt definiëren. U kunt geen gegevens ophalen over rolinstanties die worden uitgevoerd in een andere service.
> 
> 

U kunt de eigenschap [instances](/previous-versions/azure/reference/ee741904(v=azure.100)) gebruiken om exemplaren van een rol op te halen. Gebruik eerst de [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) om een verwijzing naar de huidige rolinstantie te retour neren en gebruik vervolgens de eigenschap [Role](/previous-versions/azure/reference/ee741918(v=azure.100)) om een verwijzing naar de rol zelf te retour neren.

Wanneer u via de .NET SDK verbinding maakt met een rolinstantie, is het relatief eenvoudig om toegang te krijgen tot de eindpunt gegevens. Nadat u al verbinding hebt gemaakt met een specifieke functie omgeving, kunt u bijvoorbeeld de poort van een specifiek eind punt verkrijgen met de volgende code:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

De eigenschap **instances** retourneert een verzameling **RoleInstance** -objecten. Deze verzameling bevat altijd het huidige exemplaar. Als de rol geen intern eind punt definieert, bevat de verzameling het huidige exemplaar, maar geen andere exemplaren. Het aantal rolinstanties in de verzameling zal altijd 1 zijn als er geen intern eind punt voor de rol is gedefinieerd. Als de rol een intern eind punt definieert, worden de instanties gedetecteerd tijdens runtime. het aantal exemplaren in de verzameling komt overeen met het aantal exemplaren dat is opgegeven voor de rol in het service configuratie bestand.

> [!NOTE]
> De door Azure beheerde bibliotheek biedt geen manier om de status van andere rolinstanties te bepalen, maar u kunt dergelijke status beoordelingen zelf implementeren als uw service deze functionaliteit nodig heeft. U kunt [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) gebruiken om informatie te verkrijgen over actieve rolinstanties.
> 
> 

Om het poort nummer voor een intern eind punt op een rolinstantie te bepalen, kunt u de [`InstanceEndpoints`](/previous-versions/azure/reference/ee741917(v=azure.100)) eigenschap gebruiken om een Dictionary-object te retour neren met de namen van eind punten en de bijbehorende IP-adressen en poorten. De [`IPEndpoint`](/previous-versions/azure/reference/ee741919(v=azure.100)) eigenschap retourneert het IP-adres en de poort voor een opgegeven eind punt. De `PublicIPEndpoint` eigenschap retourneert de poort voor een eind punt met gelijke taak verdeling. Het IP-adres gedeelte van de `PublicIPEndpoint` eigenschap wordt niet gebruikt.

Hier volgt een voor beeld waarin rolinstanties worden herhaald.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Hier volgt een voor beeld van een werknemersrol waarmee het eind punt wordt opgehaald dat door de service definitie wordt weer gegeven en luistert naar verbindingen.

> [!WARNING]
> Deze code werkt alleen voor een geïmplementeerde service. Bij het uitvoeren van de Azure Compute-emulator worden service configuratie-elementen die directe poort eindpunten maken (**InstanceInputEndpoint** -elementen), genegeerd.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Regels voor netwerk verkeer voor het beheren van de communicatie van rollen
Nadat u interne eind punten hebt gedefinieerd, kunt u regels voor netwerk verkeer toevoegen (op basis van de eind punten die u hebt gemaakt) om te bepalen hoe rolinstanties met elkaar kunnen communiceren. Het volgende diagram toont enkele veelvoorkomende scenario's voor het beheren van de communicatie van rollen:

![Scenario's voor regels voor netwerk verkeer](./media/cloud-services-enable-communication-role-instances/scenarios.png "Scenario's voor regels voor netwerk verkeer")

In het volgende code voorbeeld ziet u roldefinities voor de functies die in het vorige diagram worden weer gegeven. Elke roldefinitie bevat ten minste één intern eind punt dat is gedefinieerd:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Beperking van de communicatie tussen rollen kan optreden met interne eind punten van zowel vaste als automatisch toegewezen poorten.
> 
> 

Nadat een intern eind punt is gedefinieerd, kan de communicatie standaard stromen van elke rol naar het interne eind punt van een rol zonder beperkingen. Als u de communicatie wilt beperken, moet u een **NetworkTrafficRules** -element toevoegen aan het element **ServiceDefinition** in het service definitie bestand.

### <a name="scenario-1"></a>Scenario 1
Sta alleen netwerk verkeer van **WebRole1** toe aan **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scenario 2
Hiermee wordt alleen netwerk verkeer van **WebRole1** naar **WorkerRole1** en **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scenario 3
Hiermee wordt alleen netwerk verkeer van **WebRole1** naar **WorkerRole1** en **WorkerRole1** naar **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scenario 4
Hiermee wordt alleen netwerk verkeer van **WebRole1** naar **WorkerRole1**, **WebRole1** tot **WorkerRole2** en **WorkerRole1** naar **WorkerRole2** toegestaan.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Een verwijzing naar een XML-schema voor de elementen die hierboven worden gebruikt, vindt u [hier](/previous-versions/azure/reference/gg557551(v=azure.100)).

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het Cloud service [model](cloud-services-model-and-package.md).




