---
title: Migratie handleiding voor python ADAL naar MSAL | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe u uw python-app voor Azure Active Directory Authentication Library (ADAL) kunt migreren naar de micro soft Authentication Library (MSAL) voor python.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 731c608b8ac280a3a3c4e77c3a94533d258cb85d
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618039"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Migratie handleiding voor ADAL naar MSAL voor python

Dit artikel bevat een overzicht van wijzigingen die u moet aanbrengen voor het migreren van een app die gebruikmaakt van de Azure Active Directory Authentication Library (ADAL) om de micro soft Authentication Library (MSAL) te gebruiken.

## <a name="difference-highlights"></a>Verschil hooglichten

ADAL werkt met het Azure Active Directory (Azure AD) v 1.0-eind punt. De micro soft Authentication Library (MSAL) werkt met het micro soft Identity-platform, voorheen bekend als het Azure Active Directory v 2.0-eind punt. Het micro soft-identiteits platform wijkt af van Azure AD v 1.0 in dat geval:

Steun
  - Werk- en schoolaccounts (door Azure AD ingerichte accounts)
  - Persoonlijke accounts (zoals Outlook.com of Hotmail.com)
  - Uw klanten die hun eigen e-mail adres of sociale identiteit (zoals LinkedIn, Facebook, Google) meenemen via de Azure AD B2C aanbieding

- Is standaarden compatibel met:
  - OAuth v 2.0
  - OpenID Connect Connect (OIDC)

Zie [Wat is er anders in het micro soft Identity platform (v 2.0)-eind punt?](../azuread-dev/azure-ad-endpoint-comparison.md) voor meer informatie.

### <a name="scopes-not-resources"></a>Scopes die geen resources zijn

ADAL python schaft tokens voor bronnen aan, maar MSAL python verkrijgt tokens voor scopes. Het API-Opper vlak in MSAL python heeft geen resource parameter meer. U moet bereiken opgeven als een lijst met teken reeksen die de gewenste machtigingen en benodigde bronnen declareren. Zie [Microsoft Graph scopes](/graph/permissions-reference)voor een voor beeld van bereiken.

U kunt het `/.default` bereik achtervoegsel toevoegen aan de resource om uw apps van het v 1.0-eind punt (ADAL) te migreren naar het micro soft Identity platform-eind punt (MSAL). Bijvoorbeeld: voor de resource waarde van `https://graph.microsoft.com` is de equivalente bereik waarde `https://graph.microsoft.com/.default` .  Als de bron zich niet in het URL-formulier bevindt, maar een resource-ID van het formulier `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , kunt u de waarde van bereik nog steeds gebruiken als `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Voor meer informatie over de verschillende typen bereiken, verwijzen wij u naar [machtigingen en toestemming in het micro soft-identiteits platform](./v2-permissions-and-consent.md) en de [scopes voor een web API die v 1.0-tokens accepteert](./msal-v1-app-scopes.md) .

### <a name="error-handling"></a>Foutafhandeling

Azure Active Directory Authentication Library (ADAL) voor python gebruikt de uitzonde ring `AdalError` om aan te geven dat er een probleem is. MSAL voor python gebruikt in plaats daarvan fout codes. Zie  [MSAL voor python-fout afhandeling](./msal-handling-exceptions.md?tabs=python)voor meer informatie.

### <a name="api-changes"></a>API-wijzigingen

De volgende tabel bevat een overzicht van een API in ADAL voor python en de versie die moet worden gebruikt in MSAL voor python:

| ADAL voor python-API  | MSAL voor python-API |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication of ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N.v.t.  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N.v.t. |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) en [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) en [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N.v.t. | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [Token cache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N.v.t. | Cache met persistentie, beschikbaar via [MSAL-extensies](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Bestaande vernieuwings tokens migreren voor MSAL python

De micro soft Authentication Library (MSAL) abstracten het concept van vernieuwings tokens. MSAL python biedt standaard een token cache in het geheugen, zodat u geen vernieuwings tokens hoeft op te slaan, op te zoeken of bij te werken. Gebruikers zien ook minder aanmeldings prompts, omdat het vernieuwen van tokens doorgaans kan worden bijgewerkt zonder tussen komst van de gebruiker. Zie [aangepaste token cache serialisatie in MSAL voor python](msal-python-token-cache-serialization.md)voor meer informatie over de token cache.

De volgende code helpt u bij het migreren van uw vernieuwings tokens die worden beheerd door een andere OAuth2-bibliotheek (inclusief maar niet beperkt tot ADAL python) die moeten worden beheerd door MSAL voor python. Een reden voor het migreren van deze vernieuwings tokens is om te voor komen dat bestaande gebruikers zich opnieuw moeten aanmelden wanneer u uw app migreert naar MSAL voor python.

De methode voor het migreren van een vernieuwings token is het gebruik van MSAL voor python om een nieuw toegangs token te verkrijgen met het vorige vernieuwings token. Wanneer het nieuwe vernieuwings token wordt geretourneerd, worden deze in de cache opgeslagen met MSAL voor python.
Sinds MSAL python 1.3.0 bieden we voor dit doel een API in MSAL.
Raadpleeg het volgende code fragment, dat wordt vermeld bij [een voltooid voor beeld van het migreren van vernieuwings tokens met MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67)

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to Microsoft identity platform v2,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>Volgende stappen

Zie [vergelijking van v 1.0 en v 2.0](../azuread-dev/azure-ad-endpoint-comparison.md)voor meer informatie.
