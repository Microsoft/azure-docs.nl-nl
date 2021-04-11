---
ms.openlocfilehash: a3771a21914831f249696fc3d733c5ea935c2c7e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251390"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Beheerde identiteit toevoegen aan uw oplossing voor communicatie Services (JS)

### <a name="install-the-sdk-packages"></a>De SDK-pakketten installeren

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-sdk-packages"></a>De SDK-pakketten gebruiken

Voeg de volgende- `import` instructies toe aan uw code om de Azure Identity-en Azure Storage sdk's te gebruiken.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

In de onderstaande voor beelden wordt gebruikgemaakt van de [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential). Deze referentie is geschikt voor productie-en ontwikkelings omgevingen.

Zie [toegang verlenen met beheerde identiteit](../managed-identity-from-cli.md) voor een eenvoudige manier om met beheerde identiteits verificatie in te springen

Zie de [Azure Identity client-bibliotheek voor Java script](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) voor een uitgebreidere bespreking van de werking van het DefaultAzureCredential-object en hoe u dit kunt gebruiken op manieren die niet zijn opgegeven in deze Snelstartgids.

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Een identiteit maken en een token uitgeven met een beheerde identiteit

In het volgende code voorbeeld ziet u hoe u een service-client object met beheerde identiteit maakt en vervolgens de client gebruikt voor het uitgeven van een token voor een nieuwe gebruiker:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Een SMS-bericht verzenden met een beheerde identiteit

In het volgende code voorbeeld ziet u hoe u een service-client object met beheerde identiteit maakt en vervolgens de-client gebruikt om een SMS-bericht te verzenden:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = {
          from: fromNumber,
          to: [toNumber],
          message: message
     };
     const response = await smsClient.send(
          sendRequest,
          {} //Optional SendOptions
          );
}
```