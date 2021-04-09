---
title: Azure Active Directory REST API-verificatie
description: Gebruik Azure Active Directory om te verifiëren bij Azure-app configuratie met behulp van de REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930514"
---
# <a name="azure-active-directory-authentication"></a>Verificatie via Azure Active Directory

U kunt HTTP-aanvragen verifiëren met behulp `Bearer` van het verificatie schema met een token dat is verkregen van Azure Active Directory (Azure AD). U moet deze aanvragen verzenden via Transport Layer Security (TLS).

## <a name="prerequisites"></a>Vereisten

U moet de principal die wordt gebruikt voor het aanvragen van een Azure AD-token aan een van de toepasselijke [Azure-app configuratie rollen](./rest-api-authorization-azure-ad.md)toewijzen.

Geef elke aanvraag met alle HTTP-headers die vereist zijn voor authenticatie. Dit is de minimale vereiste:

|  Aanvraagheader | Beschrijving  |
| --------------- | ------------ |
| `Authorization` | Verificatie-informatie vereist voor het `Bearer` schema. |

**Voorbeeld:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD-token aanschaft

Voordat u een Azure AD-token aanschaft, moet u bepalen welke gebruiker u wilt verifiëren, op welke doel groep u het token aanvraagt en welke Azure AD-eind punt (instantie) u wilt gebruiken.

### <a name="audience"></a>Doelgroep

Vraag de Azure AD-token aan met een juiste doel groep. Gebruik een van de volgende doel groepen voor Azure-app configuratie. De doel groep kan ook worden aangeduid als de *bron* waarvoor het token wordt aangevraagd.

- {configurationStoreName}. azconfig. io
- *. azconfig.io

> [!IMPORTANT]
> Wanneer de aangevraagde doel groep is `{configurationStoreName}.azconfig.io` , moet deze exact overeenkomen met de `Host` aanvraag header (hoofdletter gevoelig) die wordt gebruikt om de aanvraag te verzenden.

### <a name="azure-ad-authority"></a>Azure AD-instantie

De Azure AD-instantie is het eind punt dat u gebruikt voor het verkrijgen van een Azure AD-token. Het is in de vorm van `https://login.microsoftonline.com/{tenantId}` . Het `{tenantId}` segment verwijst naar de id van de Azure AD-Tenant waarmee de gebruiker of toepassing die de verificatie uitvoert deel uitmaakt.

### <a name="authentication-libraries"></a>Verificatiebibliotheken

Azure biedt een set Bibliotheken, genaamd Azure Active Directory-verificatie Bibliotheken, om het proces van het ophalen van een Azure AD-token te vereenvoudigen. Azure bouwt deze bibliotheken voor meerdere talen. Raadpleeg de [documentatie](../active-directory/azuread-dev/active-directory-authentication-libraries.md) voor meer informatie.

## <a name="errors"></a>Fouten

U kunt de volgende fouten tegen komen.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Reden:** U hebt de header van de autorisatie aanvraag niet door gegeven aan het `Bearer` schema.

**Oplossing:** Geef een geldige `Authorization` HTTP-aanvraag header op.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Reden:** Het Azure AD-token is niet geldig.

**Oplossing:** Verkrijg een Azure AD-token van de Azure AD-instantie en zorg ervoor dat u de juiste doel groep hebt gebruikt.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Reden:** Het Azure AD-token is niet geldig.

**Oplossing:** Verkrijg een Azure AD-token van de Azure AD-instantie. Zorg ervoor dat de Azure AD-Tenant is gekoppeld aan het abonnement waarvan het configuratie archief deel uitmaakt. Deze fout kan optreden als de principal deel uitmaakt van meer dan één Azure AD-Tenant.