---
ms.openlocfilehash: 9da0cbc3777359994fac3778dcc126c844754861
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657635"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Beheerde identiteit toevoegen aan uw oplossing voor communicatie Services

### <a name="install-the-client-library-packages"></a>De client bibliotheek pakketten installeren

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>De client bibliotheek pakketten gebruiken

Voeg het volgende toe `import` aan uw code voor gebruik van de Azure-identiteit.

```python
from azure.identity import DefaultAzureCredential
```

In de onderstaande voor beelden wordt gebruikgemaakt van de [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential). Deze referentie is geschikt voor productie-en ontwikkelings omgevingen.

Als u de toepassing wilt registreren in de ontwikkel omgeving en omgevings variabelen wilt instellen, raadpleegt u [toegang verlenen met beheerde identiteit](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token"></a>Een identiteit maken en een token uitgeven

In het volgende code voorbeeld ziet u hoe u een service-client object met beheerde identiteit maakt en vervolgens de client gebruikt voor het uitgeven van een token voor een nieuwe gebruiker:

```python
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Een SMS-bericht verzenden met een door Azure beheerde identiteit

In het volgende code voorbeeld ziet u hoe u een service-client object met door Azure beheerde identiteit maakt en vervolgens de client gebruikt voor het verzenden van een SMS-bericht:

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over verificatie](../../concepts/authentication.md)

U kunt ook het volgende doen:

- [Meer informatie over toegangs beheer op basis van rollen](../../../../articles/role-based-access-control/index.yml)
- [Meer informatie over Azure Identity Library voor python](/net/api/overview/azure/identity-readme)
- [Tokens voor gebruikerstoegang maken](../../quickstarts/access-tokens.md)
- [Een sms-bericht verzenden](../../quickstarts/telephony-sms/send.md)
- [Meer informatie over sms](../../concepts/telephony-sms/concepts.md)