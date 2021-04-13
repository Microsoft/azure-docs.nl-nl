---
ms.openlocfilehash: 42d079a2aa98549b12aafecdd8d58f3361db8b4d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307434"
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
Open een nieuw bestand in de map gemaakt en sla het op in `managed-identity.py` dit bestand.

### <a name="use-the-sdk-packages"></a>De SDK-pakketten gebruiken

Voeg de volgende `import` instructies toe aan de bovenkant van het bestand om de geïnstalleerde sdk's te gebruiken.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>Een DefaultAzureCredential maken

We maken gebruik van de [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Deze referentie is geschikt voor productie-en ontwikkelings omgevingen. Zoals we in deze Snelstartgids gebruiken, maken we deze boven aan het bestand.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Maak een identiteit en geef een token uit met beheerde identiteiten.

Nu voegt u code toe die gebruikmaakt van de gemaakte referentie voor het uitgeven van een VoIP-toegangs token. We noemen deze code later op:

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-managed-identities"></a>Een SMS-bericht verzenden met beheerde identiteiten
Als een ander voor beeld van het gebruik van beheerde identiteiten, voegen we deze code toe die gebruikmaakt van dezelfde referentie voor het verzenden van een SMS:

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

## <a name="write-our-main-code"></a>Schrijf onze hoofd code

Met onze functies die we hebben gemaakt, kunnen we nu de hoofd code schrijven die de functies aanroept die we eerder hebben geschreven.

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

Het uiteindelijke `managed-identity.py` bestand ziet er ongeveer als volgt uit:

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

Als alles is voltooid, kunt u het bestand uitvoeren door `python managed-identity.py` in te voeren vanuit de map van uw project. Als alles goed is geworden, zou het volgende moeten worden weer gegeven.

```Bash
    $ python managed-identity.py
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
