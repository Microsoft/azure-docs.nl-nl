---
ms.openlocfilehash: 3fcfb364ebffdd3643e803922cbe4f3dd0d87935
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511598"
---
## <a name="setting-up"></a>Instellen

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Open uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

### <a name="install-the-sdk-packages"></a>De SDK-pakketten installeren

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>Een nieuw bestand maken
Open en sla een nieuw bestand op in de gemaakte map met de naam `managed-identity.py` . We plaatsen onze code in dit bestand.

### <a name="use-the-sdk-packages"></a>De SDK-pakketten gebruiken

Voeg bovenaan `import` het bestand de volgende -instructies toe om de SDK's te gebruiken die we hebben geïnstalleerd.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>Een DefaultAzureCredential maken

We gebruiken de [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Deze referentie is geschikt voor productie- en ontwikkelomgevingen. Omdat we deze in deze quickstart gaan gebruiken, maken we deze bovenaan het bestand.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Een identiteit maken en een token uitgeven met beheerde identiteiten

Nu gaan we code toevoegen die gebruikmaakt van de gemaakte referentie om een VoIP-toegangs token uit te geven. We noemen deze code later:

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-managed-identities"></a>Een sms verzenden met beheerde identiteiten
Als een ander voorbeeld van het gebruik van beheerde identiteiten, voegen we deze code toe die dezelfde referentie gebruikt om een sms te verzenden:

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>Onze hoofdcode schrijven

Nu de functies zijn gemaakt, kunnen we de hoofdcode schrijven die de functies aanroept die we eerder hebben geschreven.

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

Het `managed-identity.py` uiteindelijke bestand ziet er als het volgende uit:

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>Het programma uitvoeren

Als alles is voltooid, kunt u het bestand uitvoeren door in te `python managed-identity.py` voeren vanuit de map van uw project. Als alles goed is gegaan, ziet u iets dat lijkt op het volgende.

```Bash
    $ python managed-identity.py
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
