---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98674223"
---
## <a name="add-a-user-journey"></a>Een gebruikers traject toevoegen 

De ID-provider is op dit moment ingesteld, maar is nog niet beschikbaar op de aanmeldings pagina's. Als u niet beschikt over uw eigen aangepaste gebruikers traject, maakt u een duplicaat van een bestaande sjabloon voor een gebruiker, anders gaat u verder met de volgende stap. 

1. Open het *TrustFrameworkBase.xml* -bestand in het Starter Pack.
1. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat bevat `Id="SignUpOrSignIn"` .
1. Open de *TrustFrameworkExtensions.xml* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
1. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
1. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `Id="CustomSignUpSignIn"`.

## <a name="add-the-identity-provider-to-a-user-journey"></a>De ID-provider toevoegen aan een gebruikers traject 

Nu u een gebruikers reis hebt, voegt u de nieuwe ID-provider aan de gebruikers reis toe. U voegt eerst een aanmeldings knop toe en koppelt vervolgens de knop aan een actie. De actie is het technische profiel dat u eerder hebt gemaakt.

1. Zoek het deel van de Orchestration-stap dat bevat `Type="CombinedSignInAndSignUp"` , of `Type="ClaimsProviderSelection"` in de gebruikers traject. Doorgaans is dit de eerste indelings stap. Het **ClaimsProviderSelections** -element bevat een lijst met id-providers waarmee een gebruiker zich kan aanmelden. De volg orde van de elementen bepaalt de volg orde van de aanmeldings knoppen die aan de gebruiker worden gepresenteerd. Voeg een **ClaimsProviderSelection** XML-element toe. Stel de waarde van **TargetClaimsExchangeId** in op een beschrijvende naam.

1. Voeg in de volgende Orchestration-stap een **ClaimsExchange** -element toe. Stel de **id** in op de waarde van de uitwisselings-id van de doel claim. werk de waarde van **TechnicalProfileReferenceId** bij naar de id van het technische profiel dat u eerder hebt gemaakt.

In het volgende XML-bestand ziet u de eerste twee indelings stappen van een gebruikers traject met de ID-provider: