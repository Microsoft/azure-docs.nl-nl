---
title: Overzicht van Azure Relay .NET-standaard-Api's | Microsoft Docs
description: In dit artikel wordt een overzicht gegeven van een aantal belang rijke Azure Relay Hybride verbindingen .NET Standard API.
ms.topic: article
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 724fb1a62b82036b4a0fa8b9f4f3608293f608a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625128"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Overzicht van Azure Relay Hybride verbindingen .NET Standard-API

In dit artikel vindt u een overzicht van een aantal van de belangrijkste Azure Relay Hybride verbindingen .NET Standard- [client-api's](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Klasse van opbouw functie voor doorstuur verbindingsteken reeks

De klasse [RelayConnectionStringBuilder][RelayConnectionStringBuilder] maakt verbindings reeksen die specifiek zijn voor relay hybride verbindingen. U kunt deze gebruiken om de indeling van een connection string te controleren of een volledig nieuwe connection string te bouwen. Raadpleeg de volgende code voor een voor beeld:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

U kunt een connection string ook rechtstreeks door geven aan de- `RelayConnectionStringBuilder` methode. Met deze bewerking kunt u controleren of de connection string een geldige indeling heeft. Als een van de para meters ongeldig is, genereert de constructor een `ArgumentException` .

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Hybride verbindings stroom

De klasse [HybridConnectionStream][HCStream] is het primaire object dat wordt gebruikt voor het verzenden en ontvangen van gegevens van een Azure relay eind punt, of u nu werkt met een [HybridConnectionClient][HCClient]of een [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Een hybride verbindings stroom ophalen

#### <a name="listener"></a>Listener

Met behulp van een [HybridConnectionListener][HCListener] -object kunt u een `HybridConnectionStream` object als volgt verkrijgen:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Client

Met behulp van een [HybridConnectionClient][HCClient] -object kunt u een `HybridConnectionStream` object als volgt verkrijgen:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Gegevens ontvangen

De klasse [HybridConnectionStream][HCStream] maakt communicatie in twee richtingen mogelijk. In de meeste gevallen ontvangt u voortdurend van de stroom. Als u tekst uit de stroom wilt lezen, kunt u ook een [StreamReader niet](/dotnet/api/system.io.streamreader) -object gebruiken, waarmee u de gegevens gemakkelijker kunt parseren. U kunt bijvoorbeeld gegevens als tekst lezen, in plaats van als `byte[]` .

Met de volgende code worden afzonderlijke tekst regels van de stroom gelezen totdat een annulering wordt aangevraagd:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Gegevens verzenden

Zodra er een verbinding tot stand is gebracht, kunt u een bericht verzenden naar het relay-eind punt. Omdat het verbindings object [Stream](/dotnet/api/system.io.stream)overneemt, verzendt u uw gegevens als een `byte[]` . In het volgende voor beeld ziet u hoe u dit doet:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Als u echter rechtstreeks tekst wilt verzenden, zonder dat u de teken reeks elke keer hoeft te coderen, kunt u het object laten teruglopen `hybridConnectionStream` met een [StreamWriter](/dotnet/api/system.io.streamwriter) -object.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over Azure Relay naar deze koppelingen:

* [Naslag informatie over micro soft. Azure. relay](/dotnet/api/microsoft.azure.relay)
* [Wat is Azure Relay?](relay-what-is-it.md)
* [Beschik bare relay-Api's](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
