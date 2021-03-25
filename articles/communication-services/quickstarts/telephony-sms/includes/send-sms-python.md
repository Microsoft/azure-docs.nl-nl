---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: 727e2166bad7f0d8980ffe4fa18c292a206c37d7
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110334"
---
Ga aan de slag met Azure Communication Services met behulp van de SMS-SDK voor SMS Services voor het verzenden van berichten.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- [Python](https://www.python.org/downloads/) 2,7 of 3.6 +.
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
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u de Azure Communication Services SMS SDK voor python-pakket met behulp van de `pip install` opdracht.

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de SMS-SDK voor Azure Communication Services voor python.

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Deze klasse is vereist voor alle sms-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om sms-berichten te verzenden.                                                                                                                 |
| SmsSendResult               | Deze klasse bevat het resultaat van de SMS-service.                                          |

## <a name="authenticate-the-client"></a>De client verifiëren

Breng een **SmsClient** tot stand met uw verbindingsreeks. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../../create-communication-resource.md#store-your-connection-string).

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
Ter vereenvoudiging maken we gebruik van verbindings reeksen in deze Snelstartgids, maar in productie omgevingen raden wij u aan [beheerde identiteiten](../../../quickstarts/managed-identity.md) te gebruiken, omdat ze op schaal veiliger en beheersbaar zijn.


## <a name="send-a-11-sms-message"></a>Een SMS-bericht van 1:1 verzenden

Als u een SMS-bericht naar één ontvanger wilt verzenden, roept u de ```send``` methode aan vanuit de **SmsClient** met één ontvanger-telefoon nummer. U kunt ook aanvullende para meters door geven om op te geven of het leverings rapport moet worden ingeschakeld en aangepaste tags moet worden ingesteld. Voeg deze code toe aan het einde van het `try`-block in **send-sms.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

U moet `<from-phone-number>` vervangen door een telefoonnummer met sms-functionaliteit dat is gekoppeld aan uw communicatieservice en `<to-phone-number>` met het telefoonnummer waarnaar u een bericht wilt verzenden. 

> [!WARNING]
> Telefoonnummers moeten worden opgegeven in de internationale standaardindeling E.164. (bijvoorbeeld: + 12223334444).

## <a name="send-a-1n-sms-message"></a>Een SMS-bericht van 1: N verzenden

Als u een SMS-bericht naar een lijst met ontvangers wilt verzenden, roept u de ```send``` methode aan vanuit de **SmsClient** met een lijst met telefoon nummers van de ontvanger. U kunt ook aanvullende para meters door geven om op te geven of het leverings rapport moet worden ingeschakeld en aangepaste tags moet worden ingesteld. Voeg deze code toe aan het einde van het `try`-block in **send-sms.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Vervang door `<from-phone-number>` een SMS-telefoon nummer dat is gekoppeld aan uw communicatie service en `<to-phone-number-1>` en `<to-phone-number-2>` met de telefoon nummers waarnaar u een bericht wilt verzenden. 

## <a name="optional-parameters"></a>Optionele parameters

De parameter `enable_delivery_report` is een optionele parameter die u kunt gebruiken voor het configureren van leveringsrapporten. Dit is handig voor scenario's waarin u gebeurtenissen wilt verzenden wanneer sms-berichten worden bezorgd. Raadpleeg de quickstart [Sms-gebeurtenissen verwerken](../handle-sms-events.md) voor het configureren van leveringsrapporten voor uw sms-berichten.

De `tag` para meter is een optionele para meter die u kunt gebruiken voor het configureren van aangepaste labels.

## <a name="run-the-code"></a>De code uitvoeren
Voer de toepassing op vanuit uw toepassingsmap met de opdracht `python`.

```console
python send-sms.py
```

Het complete python-script moet er ongeveer als volgt uitzien:

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
