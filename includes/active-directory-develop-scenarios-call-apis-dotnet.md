---
title: bestand opnemen
description: bestand opnemen
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76309032"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Eigenschappen van AuthenticationResult in MSAL.NET

De methoden voor het verkrijgen van tokens retour neren `AuthenticationResult` . Voor asynchrone methoden `Task<AuthenticationResult>` retourneert.

In MSAL.NET wordt het `AuthenticationResult` volgende getoond:

- `AccessToken` voor de Web-API voor toegang tot bronnen. Deze para meter is een teken reeks, meestal een met base-64-gecodeerde JWT. De client mag nooit kijken in het toegangs token. De indeling is niet gegarandeerd stabiel en kan worden versleuteld voor de resource. Het schrijven van code die afhankelijk is van de inhoud van toegangs tokens op de client is een van de grootste bronnen van fouten en client logica-onderbrekingen. Zie [toegangs tokens](../articles/active-directory/develop/access-tokens.md)voor meer informatie.
- `IdToken` voor de gebruiker. Deze para meter is een gecodeerde JWT. Zie [id-tokens](../articles/active-directory/develop/id-tokens.md)voor meer informatie.
- `ExpiresOn` Hiermee wordt aangegeven op welke datum en tijd het token verloopt.
- `TenantId` bevat de Tenant waarin de gebruiker is gevonden. Voor gast gebruikers in Azure Active Directory-B2B-scenario's (Azure AD) is de Tenant-ID de gast-Tenant, niet de unieke Tenant.
Wanneer het token wordt geleverd voor een gebruiker, `AuthenticationResult` bevat ook informatie over deze gebruiker. Voor vertrouwelijke client stromen waarbij tokens worden aangevraagd zonder gebruiker voor de toepassing, zijn deze gebruikers gegevens null.
- De `Scopes` waarvoor het token is uitgegeven.
- De unieke ID voor de gebruiker.

### <a name="iaccount"></a>IAccount

MSAL.NET definieert het begrip van een account via de `IAccount` interface. Deze laatste wijziging biedt de juiste semantiek. Dezelfde gebruiker kan meerdere accounts hebben in verschillende Azure AD-directory's. Daarnaast biedt MSAL.NET betere informatie in het geval van gast scenario's, omdat informatie over het privé-account wordt verstrekt.
In het volgende diagram ziet u de structuur van de `IAccount` interface.

![IAccount-interface structuur](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

De `AccountId` klasse identificeert een account in een specifieke Tenant met de eigenschappen die in de volgende tabel worden weer gegeven.

| Eigenschap | Beschrijving |
|----------|-------------|
| `TenantId` | Een teken reeks representatie voor een GUID. Dit is de ID van de Tenant waar het account zich bevindt. |
| `ObjectId` | Een teken reeks representatie voor een GUID. Dit is de ID van de gebruiker die eigenaar is van het account in de Tenant. |
| `Identifier` | De unieke id voor het account. `Identifier` is de samen voeging van `ObjectId` en `TenantId` gescheiden door een komma. Deze zijn niet base 64-code ring. |

De `IAccount` interface vertegenwoordigt informatie over één account. Dezelfde gebruiker kan zich in verschillende tenants bevinden, wat betekent dat een gebruiker meerdere accounts kan hebben. De leden ervan worden weer gegeven in de volgende tabel.

| Eigenschap | Beschrijving |
|----------|-------------|
| `Username` | Een teken reeks die de waarde unplayable bevat in de indeling UserPrincipalName (UPN), bijvoorbeeld john.doe@contoso.com . Deze teken reeks kan null zijn, in tegens telling tot HomeAccountId en HomeAccountId. Identifier, die niet null zijn. Met deze eigenschap wordt de `DisplayableId` eigenschap van `IUser` in eerdere versies van MSAL.net vervangen. |
| `Environment` | Een teken reeks die de ID-provider bevat voor dit account, bijvoorbeeld `login.microsoftonline.com` . Deze eigenschap vervangt het `IdentityProvider` eigendom van `IUser` , behalve dat `IdentityProvider` ook informatie over de Tenant bevat, naast de cloud omgeving. Hier is de waarde alleen de host. |
| `HomeAccountId` | De account-ID van het thuis account voor de gebruiker. Met deze eigenschap wordt de gebruiker op unieke wijze in azure AD-tenants geïdentificeerd. |

### <a name="use-the-token-to-call-a-protected-api"></a>Het token gebruiken om een beveiligde API aan te roepen

Nadat `AuthenticationResult` het is geretourneerd door MSAL in `result` , voegt u deze toe aan de http-autorisatie-header voordat u de aanroep voor toegang tot de beveiligde web-API uitvoert.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```