---
title: Fouten en uitzonderingen verwerken in MSAL voor Python
titleSuffix: Microsoft identity platform
description: Meer informatie over het afhandelen van fouten en uitzonde ringen, claim problemen voor voorwaardelijke toegang en nieuwe pogingen in MSAL voor python-toepassingen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761079"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Fouten en uitzonderingen verwerken in MSAL voor Python

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Fout afhandeling in MSAL voor python

In MSAL voor python worden de meeste fouten overgebracht als een retour waarde van de API-aanroep. De fout wordt weer gegeven als een woorden lijst met de JSON-reactie van het micro soft Identity-platform.

* Een geslaagd antwoord bevat de `"access_token"` sleutel. De indeling van het antwoord wordt gedefinieerd door het OAuth2-protocol. Zie [5,1 geslaagde reactie](https://tools.ietf.org/html/rfc6749#section-5.1) voor meer informatie
* Een fout bericht bevat `"error"` en is doorgaans `"error_description"` . De indeling van het antwoord wordt gedefinieerd door het OAuth2-protocol. Zie [5,2-fout](https://tools.ietf.org/html/rfc6749#section-5.2) melding voor meer informatie

Als er een fout wordt geretourneerd, `"error_description"` bevat de sleutel een bericht met een lees bare tekst. Deze bevat meestal een fout code voor micro soft Identity platform. Zie [fout codes voor verificatie en autorisatie](./reference-aadsts-error-codes.md)voor meer informatie over de verschillende fout codes.

In MSAL voor python zijn uitzonde ringen zeldzame, omdat de meeste fouten worden verwerkt door een fout waarde te retour neren. De `ValueError` uitzonde ring wordt alleen gegenereerd als er een probleem is met de manier waarop u de bibliotheek probeert te gebruiken, bijvoorbeeld wanneer de API-para meter (s) ongeldig zijn.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Volgende stappen

Overweeg [logboek registratie in MSAL voor python](msal-logging-python.md) in te scha kelen om problemen op te sporen en op te lossen.
