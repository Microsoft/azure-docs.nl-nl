---
title: Logboek registratie van fouten en uitzonde ringen in MSAL voor python
titleSuffix: Microsoft identity platform
description: Meer informatie over het vastleggen van fouten en uitzonde ringen in MSAL voor python
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578750"
---
# <a name="logging-in-msal-for-python"></a>Logboekregistratie in MSAL voor Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL voor python-logboek registratie

Logboek registratie in MSAL voor python maakt gebruik [van de logboek registratie module in de python-standaard bibliotheek](https://docs.python.org/3/library/logging.html). U kunt MSAL-logboek registratie als volgt configureren (en in de [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)weer geven in actie):

### <a name="enable-debug-logging-for-all-modules"></a>Logboek registratie voor fout opsporing inschakelen voor alle modules

De logboek registratie in een python-script is standaard uitgeschakeld. Als u uitgebreide logboek registratie wilt inschakelen voor **alle** python-modules in uw script, gebruikt u `logging.basicConfig` met een niveau van `logging.DEBUG` :

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Hiermee worden alle logboek berichten die zijn opgegeven in de logboek module, afgedrukt op de standaard uitvoer.

### <a name="configure-msal-logging-level"></a>MSAL-logboek registratie niveau configureren

U kunt het logboek registratie niveau van de MSAL voor python-logboek provider configureren met behulp van de- `logging.getLogger()` methode met de logger naam `"msal"` :

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>MSAL-logboek registratie configureren met Azure-app Insights

Python-logboeken worden toegewezen aan een logboek-handler. Dit is standaard de `StreamHandler` . Als u MSAL-logboeken wilt verzenden naar een Application Insights met een instrumentatie sleutel, gebruikt u de `AzureLogHandler` door de `opencensus-ext-azure` bibliotheek gegeven.

Als u wilt installeren, `opencensus-ext-azure` voegt u het `opencensus-ext-azure` pakket van PyPI toe aan de installatie van uw afhankelijkheden of PIP:

```console
pip install opencensus-ext-azure
```

Wijzig vervolgens de standaard-handler van de `"msal"` logboek provider in een exemplaar van `AzureLogHandler` met een instrumentatie sleutel die in de `APP_INSIGHTS_KEY` omgevings variabele is ingesteld:

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Persoonlijke en organisatie gegevens in python

MSAL voor python registreert geen persoonlijke gegevens of bedrijfs gegevens. Er is geen eigenschap om logboek registratie van persoonlijke of organisatie gegevens in of uit te scha kelen.

U kunt de standaard logboek registratie van python gebruiken om logboeken te maken wat u wilt, maar u bent zelf verantwoordelijk voor het veilig afhandelen van gevoelige gegevens en de volgende regelgevings vereisten.

Raadpleeg voor meer informatie over logboek registratie in python de  [logboek registratie van python: How-to](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [micro soft Identity platform-code voorbeelden](sample-v2-code.md)voor meer code voorbeelden.
