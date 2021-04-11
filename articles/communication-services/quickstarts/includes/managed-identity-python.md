---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450620"
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

Zie [toegang verlenen met beheerde identiteit](../managed-identity-from-cli.md) voor een eenvoudige manier om met beheerde identiteits verificatie in te springen

Zie de [Azure Identity client-bibliotheek voor python](https://docs.microsoft.com/python/api/overview/azure/identity-readme) voor een uitgebreidere bespreking van de werking van het DefaultAzureCredential-object en hoe u dit kunt gebruiken op een manier die niet in deze Snelstartgids is opgegeven.

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

### <a name="list-all-your-purchased-phone-numbers"></a>Alle gekochte telefoon nummers weer geven

In het volgende code voorbeeld ziet u hoe u een service-client object met een door Azure beheerde identiteit maakt, en vervolgens de client gebruikt om alle gekochte telefoon nummers te zien die de resource heeft:

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```