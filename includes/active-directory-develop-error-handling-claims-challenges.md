---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760691"
---
## <a name="conditional-access-and-claims-challenges"></a>Voorwaardelijke toegang en claim uitdagingen

Wanneer tokens op de achtergrond worden opgehaald, kan uw toepassing fouten ontvangen wanneer een [Challenge voor voorwaardelijke toegang](../articles/active-directory/develop/v2-conditional-access-dev-guide.md) , zoals MFA-beleid, is vereist voor een API die u probeert te openen.

Het patroon voor het afhandelen van deze fout is het interactief verkrijgen van een token met behulp van MSAL. Hiermee wordt de gebruiker gevraagd om te voldoen aan het vereiste beleid voor voorwaardelijke toegang.

In bepaalde gevallen bij het aanroepen van een API waarvoor voorwaardelijke toegang is vereist, kunt u een claim Challenge ontvangen in de fout van de API. Als het beleid voor voorwaardelijke toegang een beheerd apparaat (intune) heeft, is de fout bijvoorbeeld [AADSTS53000: uw apparaat moet worden beheerd om toegang te krijgen tot deze bron](../articles/active-directory/develop/reference-aadsts-error-codes.md) of iets dergelijks. In dit geval kunt u de claims door geven in de aanroep van het Acquire-token, zodat de gebruiker wordt gevraagd om te voldoen aan het juiste beleid.
