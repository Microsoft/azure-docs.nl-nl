---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: danieldoolabh
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: dadoolab
ms.openlocfilehash: 442fff11c2ce95ca5cc665b016631cab9048ab50
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488298"
---
Ga aan de slag met Azure Communication Services door de sms-clientbibliotheek voor Python van Communications Services te gebruiken om sms-berichten te verzenden.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- [Python](https://www.python.org/downloads/) 2.7, 3.5 of hoger.
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een telefoonnummer met sms-functionaliteit. [Een telefoonnummer aanvragen](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controle van vereisten

- Voer in een terminal- of opdrachtvenster de opdracht `python --version` uit om te controleren of Python is geïnstalleerd.
- Als u de telefoonnummers wilt weergeven die zijn gekoppeld aan uw Communication Services-resource, meldt u zich aan bij [Azure Portal](https://portal.azure.com/), zoekt u uw Communication Services-resource en opent u het tabblad **telefoonnummers** vanuit het navigatiedeelvenster aan de linkerkant.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Open uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Gebruik een tekst editor om een bestand te maken met de naam **send-sms.py** in de hoofdmap van het project en voeg de structuur voor het programma toe, waaronder de basale verwerking van uitzonderingen. In de volgende secties voegt u alle broncode voor deze quickstart toe aan dit bestand.

```python
import os
from azure.communication.sms import PhoneNumber
from azure.communication.sms import SendSmsOptions
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Het pakket installeren

Blijf in de toepassingsmap en installeer met de opdracht `pip install` de Azure Communications Services sms-clientbibliotheek voor het Python-pakket.

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services sms-clientbibliotheek voor Python.

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Deze klasse is vereist voor alle sms-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om sms-berichten te verzenden. |
| SendSmsOptions | Deze klasse biedt opties voor het configureren van leveringsrapporten. Als enable_delivery_report is ingesteld op True, wordt een gebeurtenis verzonden wanneer de levering is geslaagd |

## <a name="authenticate-the-client"></a>De client verifiëren

Breng een **SmsClient** tot stand met uw verbindingsreeks. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../../create-communication-resource.md#store-your-connection-string).

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-an-sms-message"></a>Een sms-bericht verzenden

Een sms-bericht verzenden door de verzendmethode aan te roepen. Voeg deze code toe aan het einde van het `try`-block in **send-sms.py**:

```python

# calling send() with sms values
sms_response = sms_client.send(
        from_phone_number=PhoneNumber("<leased-phone-number>"),
        to_phone_numbers=[PhoneNumber("<to-phone-number>")],
        message="Hello World via SMS",
        send_sms_options=SendSmsOptions(enable_delivery_report=True)) # optional property

```

U moet `<leased-phone-number>` vervangen door een telefoonnummer met sms-functionaliteit dat is gekoppeld aan uw communicatieservice en `<to-phone-number>` met het telefoonnummer waarnaar u een bericht wilt verzenden. 

De parameter `send_sms_options` is een optionele parameter die u kunt gebruiken voor het configureren van leveringsrapporten. Dit is handig voor scenario's waarin u gebeurtenissen wilt verzenden wanneer sms-berichten worden bezorgd. Raadpleeg de quickstart [Sms-gebeurtenissen verwerken](../handle-sms-events.md) voor het configureren van leveringsrapporten voor uw sms-berichten.

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `python`.

```console
python send-sms.py
```
