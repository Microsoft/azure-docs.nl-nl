---
ms.openlocfilehash: 07a8d792bbb17df1401b5892b09fb7ff2f5f8e52
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629381"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- De nieuwste versie van [.NET Core-clientbibliotheek](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturingssysteem.
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Controle van vereisten

- Voer in een terminal- of opdrachtvenster de opdracht `dotnet` uit om te controleren of de .NET-clientbibliotheek is geïnstalleerd.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `PhoneNumbersQuickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: **Program.cs**.

```console
dotnet new console -o PhoneNumbersQuickstart
```

Wijzig uw map in de zojuist gemaakte app-map en gebruik de opdracht `dotnet build` om uw toepassing te compileren.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u de Azure Communication PhoneNumbers-client bibliotheek voor .NET-pakket met behulp van de `dotnet add package` opdracht.

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0-beta.5
```

Voeg `using` boven aan **programma. cs** een instructie toe om de naam ruimten op te neemt.

```csharp
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
using Azure.Communication.PhoneNumbers.Models;
```

De `Main` hand tekening van de functie bijwerken om async te zijn.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>De client verifiëren

Telefoon nummer clients kunnen worden geverifieerd met connection string die zijn verkregen van een Azure-communicatie resources in [Azure Portal] [azure_portal].

```csharp
// Get a connection string to our Azure Communication resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

Telefoon nummer clients hebben ook de mogelijkheid om te verifiëren met Azure Active Directory-verificatie. Met deze optie, `AZURE_CLIENT_SECRET` `AZURE_CLIENT_ID` en `AZURE_TENANT_ID` omgevings variabelen moeten worden ingesteld voor verificatie.

```csharp
// Get an endpoint to our Azure Communication resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>Telefoon nummers beheren

### <a name="search-for-available-phone-numbers"></a>Zoeken naar beschik bare telefoon nummers

Als u telefoon nummers wilt kopen, moet u eerst zoeken naar beschik bare telefoon nummers. Als u wilt zoeken naar telefoon nummers, geeft u het netnummer, het type toewijzing, de mogelijkheden voor het [telefoon nummer](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), het [telefoon nummer](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)en de hoeveelheid op. Houd er rekening mee dat voor het type gratis telefoon nummer de area code optioneel is.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>Telefoon nummers kopen

Het resultaat van het zoeken naar telefoon nummers is een `PhoneNumberSearchResult` . Deze bevat een `SearchId` die kan worden door gegeven aan de aankoop nummers API om de getallen in de zoek opdracht op te halen. Houd er rekening mee dat het aanroepen van de telefoon nummers van de kopen-API ertoe leidt dat uw Azure-account in rekening wordt gebracht.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionAsync();
```

### <a name="get-phone-numbers"></a>Telefoon nummer (s) ophalen

Na een aankoop nummer kunt u het ophalen van de client.
```csharp
var getPhoneNumberResponse = await client.GetPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

U kunt ook alle gekochte telefoon nummers ophalen.
``` csharp
var purchasedPhoneNumbers = client.GetPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>Mogelijkheden voor telefoon nummer bijwerken

Met een aankoop nummer kunt u de mogelijkheden bijwerken.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>Telefoon nummer van release

U kunt een gekocht telefoon nummer vrijgeven.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionAsync();
````

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```console
dotnet run
```

## <a name="sample-code"></a>Voorbeeldcode

U kunt de voorbeeld-app downloaden uit [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers).
