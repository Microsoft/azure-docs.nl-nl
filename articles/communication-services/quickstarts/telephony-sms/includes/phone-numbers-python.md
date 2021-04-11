---
ms.openlocfilehash: 283d7d1c83dc4a68901c17c36b548e00e1044e34
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629355"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python](https://www.python.org/downloads/) 2.7, 3.5 of hoger.
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../../create-communication-resource.md).

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Open uw terminal-of opdracht venster en maak een nieuwe map voor uw app en navigeer er vervolgens naar.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Gebruik een tekst editor om een bestand te maken met de naam phone_numbers_sample. py in de hoofdmap van het project en voeg de volgende code toe. We voegen de resterende Snelstartgids-code toe in de volgende secties.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>Het pakket installeren

Blijf in de toepassingsmap en installeer met de opdracht `pip install` de clientbibliotheek voor het Azure Communications Services-beheer voor het Python-pakket.

```console
pip install azure-communication-phone-numbers
```

## <a name="authenticate-the-phone-numbers-client"></a>De telefoon nummers-client verifiëren

De `PhoneNumbersClient` is ingeschakeld om Azure Active Directory-verificatie te gebruiken. Het gebruik van het `DefaultAzureCredential` object is de eenvoudigste manier om aan de slag te gaan met Azure Active Directory en u kunt het installeren met behulp van de `pip install` opdracht. 

```console
pip install azure-identity
```

Voor het maken van een- `DefaultAzureCredential` object moet u `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` en `AZURE_TENANT_ID` al zijn ingesteld als omgevings variabelen met de bijbehorende waarden van uw geregistreerde Azure AD-toepassing.

Nadat u de bibliotheek hebt geïnstalleerd `azure-identity` , kunnen we door gaan met de verificatie van de client.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

Het gebruik van het eind punt en de toegangs sleutel van de communicatie bron voor verificatie is ook aantal.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure Portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>Functions

Zodra de `PhoneNumbersClient` verificatie is uitgevoerd, kunnen we aan de slag gaan met de verschillende functies die het kan doen.

### <a name="search-for-available-phone-numbers"></a>Zoeken naar beschik bare telefoon nummers

Als u telefoon nummers wilt kopen, moet u eerst zoeken naar alle beschik bare telefoon nummers. Als u wilt zoeken naar telefoon nummers, geeft u het netnummer, het toewijzings type, de mogelijkheden voor het [telefoon nummer](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), het [type telefoon nummer](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)en de hoeveelheid op (standaard aantal is ingesteld op 1). Houd er rekening mee dat voor het type gratis telefoon nummer de area code optioneel is.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = self.phone_number_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>Telefoon nummers kopen

Het resultaat van het zoeken naar telefoon nummers is een `PhoneNumberSearchResult` . Deze bevat een `searchId` die kan worden door gegeven aan de aankoop nummers API om de getallen in de zoek opdracht op te halen. Houd er rekening mee dat het aanroepen van de telefoon nummers van de kopen-API ertoe leidt dat uw Azure-account in rekening wordt gebracht.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient, 
    PhoneNumberCapabilityType, 
    PhoneNumberAssignmentType, 
    PhoneNumberType, 
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>Aankoop nummer (s) ophalen

Na een aankoop nummer kunt u het ophalen van de client. 

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

U kunt ook alle gekochte telefoon nummers ophalen.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>Mogelijkheden voor telefoon nummer bijwerken

U kunt de mogelijkheden van een eerder gekocht telefoon nummer bijwerken.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>Telefoon nummer van release

U kunt een gekocht telefoon nummer vrijgeven.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>De code uitvoeren

Navigeer vanuit een console prompt naar de map met het bestand phone_numbers_sample. py en voer vervolgens de volgende python-opdracht uit om de app uit te voeren.

```console
./phone_numbers_sample.py
```
