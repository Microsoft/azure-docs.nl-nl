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
ms.openlocfilehash: 8488325613b05d54b352a19a06860e08f1779877
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063111"
---
# <a name="logging-in-msal-for-python"></a>Logboekregistratie in MSAL voor Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL voor python-logboek registratie

Logboek registratie in MSAL python maakt gebruik van het standaard logboek registratie mechanisme van python, bijvoorbeeld `logging.info("msg")` kunt u de MSAL-logboek registratie als volgt configureren (en in de [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)in actie zien):

### <a name="enable-debug-logging-for-all-modules"></a>Logboek registratie voor fout opsporing inschakelen voor alle modules

De logboek registratie in een python-script is standaard uitgeschakeld. Als u logboek registratie voor fout opsporing wilt inschakelen voor alle modules in het hele python-script, gebruikt u:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Alleen stilte MSAL-logboek registratie

Als u alleen logboek registratie van de MSAL-bibliotheek wilt uitvoeren en logboek registratie voor fout opsporing in alle andere modules in uw python-script wilt inschakelen, schakelt u de logboeken uit die door MSAL python worden gebruikt:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Persoonlijke en organisatie gegevens in python

MSAL voor python registreert geen persoonlijke gegevens of bedrijfs gegevens. Er is geen eigenschap om logboek registratie van persoonlijke of organisatie gegevens in of uit te scha kelen.

U kunt de standaard logboek registratie van python gebruiken om logboeken te maken wat u wilt, maar u bent zelf verantwoordelijk voor het veilig afhandelen van gevoelige gegevens en de volgende regelgevings vereisten.

Raadpleeg voor meer informatie over logboek registratie in python de  [logboek registratie van python: How-to](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [micro soft Identity platform-code voorbeelden](sample-v2-code.md)voor meer code voorbeelden.
