---
ms.openlocfilehash: cefdf77052e559853cc85d129799e288032186b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645354"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Beheerde identiteit toevoegen aan uw oplossing voor communicatie Services

### <a name="install-the-sdk-packages"></a>De SDK-pakketten installeren

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>De SDK-pakketten gebruiken

Voeg het volgende toe `import` aan uw code voor gebruik van de Azure-identiteit.

```python
from azure.identity import DefaultAzureCredential
```

In de onderstaande voor beelden wordt gebruikgemaakt van de [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Deze referentie is geschikt voor productie-en ontwikkelings omgevingen.

Als u de toepassing wilt registreren in de ontwikkel omgeving en omgevings variabelen wilt instellen, raadpleegt u [toegang verlenen met beheerde identiteit](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token"></a>Een identiteit maken en een token uitgeven

In het volgende code voorbeeld ziet u hoe u een service-client object met beheerde identiteit maakt en vervolgens de client gebruikt voor het uitgeven van een token voor een nieuwe gebruiker:

```python
from azure.communication.identity import CommunicationIdentityClient 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Een SMS-bericht verzenden met een door Azure beheerde identiteit

In het volgende code voorbeeld ziet u hoe u een service-client object met door Azure beheerde identiteit maakt en vervolgens de client gebruikt voor het verzenden van een SMS-bericht:

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```
