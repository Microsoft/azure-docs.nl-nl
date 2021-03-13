---
ms.openlocfilehash: 3626ca4cc3e7377f1c6778bc77e5e48ef0dcad0c
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103439116"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Beheerde identiteit toevoegen aan uw oplossing voor communicatie Services (JS)

### <a name="install-the-client-library-packages"></a>De client bibliotheek pakketten installeren

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-client-library-packages"></a>De client bibliotheek pakketten gebruiken

Voeg de volgende `import` instructies toe aan uw code voor het gebruik van de identiteits-en Azure Storage-client bibliotheken van Azure.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

In de onderstaande voor beelden wordt gebruikgemaakt van de [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). Deze referentie is geschikt voor productie-en ontwikkelings omgevingen.

Als u de toepassing wilt registreren in de ontwikkel omgeving en omgevings variabelen wilt instellen, raadpleegt u [toegang verlenen met beheerde identiteit](../managed-identity-from-cli.md)  

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

