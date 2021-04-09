---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 1627ba106ed9a035627f425d1c8fce47e5794762
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958186"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- De nieuwste versie [.net core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturings systeem.
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../create-communication-resource.md).

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `AccessTokensQuickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: **Program.cs**.

```console
dotnet new console -o AccessTokensQuickstart
```

Wijzig uw map in de zojuist gemaakte app-map en gebruik de opdracht `dotnet build` om uw toepassing te compileren.

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u het Azure Communication Services-identiteits bibliotheek voor .NET-pakket met behulp van de `dotnet add package` opdracht.

```console
dotnet add package Azure.Communication.Identity --version 1.0.0
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Open het bestand **Program.cs** in een teksteditor
1. Voeg een `using`-instructie toe voor het insluiten van de `Azure.Communication.Identity` naamruimte
1. De declaratie van de `Main`-methode bijwerken ter ondersteuning van async-code

Gebruik de volgende code om te beginnen:

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Identity;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>De client verifiëren

Initialiseer een `CommunicationIdentityClient` met uw verbindingsreeks. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../create-communication-resource.md#store-your-connection-string).

Voeg de volgende code aan de `Main` methode toe:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

U kunt ook het eind punt en de toegangs sleutel afzonderlijk scheiden.
```csharp
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
string endpoint = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ENDPOINT");
string accessKey = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ACCESSKEY");
var client = new CommunicationIdentityClient(new Uri(endpoint), new AzureKeyCredential(accessKey));
```

Als u beheerde identiteit hebt ingesteld, raadpleegt u beheerde identiteiten [gebruiken](../managed-identity.md). u kunt ook verifiëren met beheerde identiteit.
```csharp
TokenCredential tokenCredential = new DefaultAzureCredential();
var client = new CommunicationIdentityClient(endpoint, tokenCredential);
```

## <a name="create-an-identity"></a>Een identiteit maken

Azure Communication Services onderhoudt een lichte identiteitsmap. Gebruik de methode `createUser` om een nieuwe vermelding in de map te maken met een unieke `Id`. Sla de ontvangen identiteit op met een toewijzing aan gebruikers van uw toepassing. U kunt dit bijvoorbeeld doen door ze op te slaan in de database van uw toepassingsserver. De identiteit is later vereist voor het uitgeven van toegangstokens.

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>Toegangstokens voor de identiteit verlenen

Gebruik de methode `GetToken` om een toegangstoken voor de al bestaande Communication Services-identiteit uit te geven. Met de parameter `scopes` wordt een set primitieven gedefinieerd, waarmee dit toegangstoken wordt geautoriseerd. Raadpleeg de [lijst met ondersteunde acties](../../concepts/authentication.md). Er kan een nieuw exemplaar van de parameter `communicationUser` worden samengesteld op basis van de tekenreeksweergave van de Azure Communication Service-identiteit.

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Toegangstokens zijn kortdurende referenties die opnieuw moeten worden uitgegeven. Als u dit niet doet, kan dit leiden tot onderbrekingen van de gebruikerservaring van uw toepassing. De antwoordeigenschap `expiresOn` geeft de levensduur van het toegangstoken aan.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Een identiteit maken en binnen dezelfde aanvraag een toegangs token uitgeven

Gebruik de `CreateUserAndTokenAsync` methode voor het maken van een communicatie Services-identiteit en het uitgeven van een toegangs token. Met de parameter `scopes` wordt een set primitieven gedefinieerd, waarmee dit toegangstoken wordt geautoriseerd. Raadpleeg de [lijst met ondersteunde acties](../../concepts/authentication.md).

```csharp
// Issue an identity and an access token with the "voip" scope for the new identity
var identityAndTokenResponse = await client.CreateUserAndTokenAsync(scopes: new[] { CommunicationTokenScope.VoIP });
var identity = identityAndTokenResponse.Value.User;
var token = identityAndTokenResponse.Value.AccessToken.Token;
var expiresOn = identityAndTokenResponse.Value.AccessToken.ExpiresOn;

Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

## <a name="refresh-access-tokens"></a>Toegangstokens vernieuwen

Als u een toegangstoken wilt vernieuwen, geeft u een exemplaar van het `CommunicationUserIdentifier`-object door aan `GetTokenAsync`. Als u deze `Id` hebt opgeslagen en een nieuwe `CommunicationUserIdentifier` wilt maken, kunt u dit doen door uw opgeslagen `Id` als volgt door te geven aan de `CommunicationUserIdentifier`-constructor:

```csharp
// In this example, userId is a string containing the Id property of a previously-created CommunicationUser
var identityToRefresh = new CommunicationUserIdentifier(userId);
var tokenResponse = await client.GetTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>Toegangstokens intrekken

In sommige gevallen kunt u toegangstokens expliciet intrekken. Wanneer de gebruiker van een toepassing bijvoorbeeld het wachtwoord wijzigt dat wordt gebruikt voor verificatie bij uw service. Met de methode `RevokeTokensAsync` worden alle actieve toegangstokens die zijn verleend aan de identiteit ongeldig gemaakt.

```csharp
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>Een identiteit verwijderen

Als u een identiteit verwijdert, worden alle actieve toegangstokens ingetrokken en wordt voorkomen dat er toegangstokens voor de identiteiten worden uitgegeven. Ook wordt alle persistente inhoud verwijderd die aan de identiteit is gekoppeld.

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```console
dotnet run
```
