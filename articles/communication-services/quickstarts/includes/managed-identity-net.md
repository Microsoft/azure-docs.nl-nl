---
ms.openlocfilehash: 8295849a7177eab774517816a239472677689434
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021483"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Beheerde identiteit toevoegen aan uw oplossing voor communicatie Services (.NET)

### <a name="install-the-client-library-packages"></a>De client bibliotheek pakketten installeren

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>De client bibliotheek pakketten gebruiken

Voeg de volgende `using` instructies toe aan uw code voor het gebruik van de identiteits-en Azure Storage-client bibliotheken van Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

In de onderstaande voor beelden wordt gebruikgemaakt van de [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Deze referentie is geschikt voor productie-en ontwikkelings omgevingen.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`en `AZURE_TENANT_ID` omgevings variabelen zijn nodig voor het maken van een `DefaultAzureCredential` object. Als u een geregistreerde toepassing in de ontwikkel omgeving wilt maken en omgevings variabelen wilt instellen, raadpleegt u [toegang verlenen met beheerde identiteit](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Een identiteit maken en een token uitgeven met een beheerde identiteit

In het volgende code voorbeeld ziet u hoe u een service-client object met Azure Active Directory-tokens maakt.

Vervolgens gebruikt u de-client om een token uit te geven voor een nieuwe gebruiker:

```csharp
     public async Task<Response<AccessToken>> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
          var identity = identityResponse.Value;

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Een SMS-bericht verzenden met een beheerde identiteit

In het volgende code voorbeeld ziet u hoe u een SMS-service-client object met beheerde identiteit maakt, en vervolgens de client gebruikt voor het verzenden van een SMS-bericht:

```csharp
     public async Task SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );
      }
```

