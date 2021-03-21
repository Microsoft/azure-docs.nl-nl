---
ms.openlocfilehash: c1b74b43c6ef884c68282dcaaae8dfc9a5541453
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622101"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Beheerde identiteit toevoegen aan uw oplossing voor communicatie Services (.NET)

### <a name="install-the-client-library-packages"></a>De client bibliotheek pakketten installeren

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0-beta.5
dotnet add package Azure.Communication.Sms  --version 1.0.0-beta.4
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>De client bibliotheek pakketten gebruiken

Voeg de volgende `using` instructies toe aan uw code voor het gebruik van de identiteits-en Azure Storage-client bibliotheken van Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

In de onderstaande voor beelden wordt gebruikgemaakt van de [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Deze referentie is geschikt voor productie-en ontwikkelings omgevingen.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`en `AZURE_TENANT_ID` omgevings variabelen zijn nodig voor het maken van een `DefaultAzureCredential` object. Als u een geregistreerde toepassing in de ontwikkel omgeving wilt maken en omgevings variabelen wilt instellen, raadpleegt u [toegang verlenen met beheerde identiteit](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Een identiteit maken en een token uitgeven met een beheerde identiteit

In het volgende code voorbeeld ziet u hoe u een service-client object met Azure Active Directory-tokens maakt.

Vervolgens gebruikt u de-client om een token uit te geven voor een nieuwe gebruiker:

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Een SMS-bericht verzenden met een beheerde identiteit

In het volgende code voorbeeld ziet u hoe u een SMS-service-client object met beheerde identiteit maakt, en vervolgens de client gebruikt voor het verzenden van een SMS-bericht:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```

