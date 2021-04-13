---
ms.openlocfilehash: 424e70f4f3656be2196f4dbdfbddb852846b6897
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307435"
---
## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `ManagedIdentitiesQuickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: `Program.cs`.

```console
dotnet new console -o ManagedIdentitiesQuickstartQuickstart
```

Wijzig uw map in de zojuist gemaakte app-map en gebruik de opdracht `dotnet build` om uw toepassing te compileren.

```console
cd ManagedIdentitiesQuickstart
dotnet build
```

### <a name="install-the-sdk-packages"></a>De SDK-pakketten installeren

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-sdk-packages"></a>De SDK-pakketten gebruiken

Voeg de volgende `using` instructies toe om `Program.cs` de Azure Identity-en Azure Storage sdk's te gebruiken.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
using Azure;
```

## <a name="create-a-defaultazurecredential"></a>Een DefaultAzureCredential maken

We gebruiken de [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) voor deze Quick Start. Deze referentie is geschikt voor productie-en ontwikkelings omgevingen. Wanneer dit nodig is voor elke bewerking, wordt deze in de `Program.cs` klasse gemaakt. Voeg het volgende toe boven aan het bestand.

```csharp
     private DefaultAzureCredential credential = new DefaultAzureCredential();
```

## <a name="issue-a-token-with-managed-identities"></a>Een token met beheerde identiteiten uitgeven

Nu voegt u code toe die gebruikmaakt van de gemaakte referentie voor het uitgeven van een VoIP-toegangs token. We noemen deze code later op.

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

## <a name="send-an-sms-with-managed-identities"></a>Een SMS-bericht verzenden met beheerde identiteiten

Als een ander voor beeld van het gebruik van beheerde identiteiten, voegen we deze code toe die gebruikmaakt van dezelfde referentie voor het verzenden van een SMS:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
     }
```

## <a name="write-the-main-method"></a>Schrijf de methode Main

Je `Program.cs` zou al een hoofd methode moeten hebben. we gaan een code toevoegen waarmee we eerder gemaakte code kunnen aanroepen om het gebruik van beheerde identiteiten te demonstreren:

```csharp
     static void Main(string[] args)
     {
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          Uri endpoint = new("https://<RESOURCENAME>.communication.azure.com/");

          // We need an instance of the program class to use within this method.
          Program instance = new();

          Console.WriteLine("Retrieving new Access Token, using Managed Identities");
          Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
          Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

          Console.WriteLine("Sending SMS using Managed Identities");

          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.SendSms(endpoint, "<Your ACS Phone Number>", "<The Phone Number you'd like to send the SMS to.>", "Hello from Managed Identities");
          Console.WriteLine($"Sms id: {result.MessageId}");
          Console.WriteLine($"Send Result Successful: {result.Successful}");
     }
```

Het uiteindelijke `Program.cs` bestand moet er als volgt uitzien:

```csharp
class Program
     {
          private DefaultAzureCredential credential = new DefaultAzureCredential();
          static void Main(string[] args)
          {
               // You can find your endpoint and access key from your resource in the Azure portal
               // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
               Uri endpoint = new("https://acstestingrifox.communication.azure.com/");

               // We need an instance of the program class to use within this method.
               Program instance = new();

               Console.WriteLine("Retrieving new Access Token, using Managed Identities");
               Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
               Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

               Console.WriteLine("Sending SMS using Managed Identities");

               // You will need a phone number from your resource to send an SMS.
               SmsSendResult result = instance.SendSms(endpoint, "+18445504651", "+14256253982", "Hello from Managed Identities");
               Console.WriteLine($"Sms id: {result.MessageId}");
               Console.WriteLine($"Send Result Successful: {result.Successful}");
          }
          public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
          {
               var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
               var identityResponse = client.CreateUser();
               var identity = identityResponse.Value;

               var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

               return tokenResponse;
          }
          public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
          {
               SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
               SmsSendResult sendResult = smsClient.Send(
                    from: from,
                    to: to,
                    message: message,
                    new SmsSendOptions(enableDeliveryReport: true) // optional
               );

               return sendResult;
          }
    }
```

## <a name="run-the-program"></a>Het programma uitvoeren

U moet uw toepassing nu kunnen uitvoeren met `dotnet run` vanuit de map van uw toepassing. De uitvoer moet er als volgt uitzien:
```
Retrieving new Access Token, using Managed Identities
Retrieved Access Token: ey....
Sending SMS using Managed Identities
Sms id: Outgoing_..._noam
Send Result Successful: True
```
