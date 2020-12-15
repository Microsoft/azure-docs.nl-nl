---
title: Protocollen voor toestemming van micro soft Identity platform-beheerder
description: Een beschrijving van autorisatie in het micro soft Identity platform-eind punt, met inbegrip van scopes, machtigingen en toestemming.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 7de97fd775853f64803ab62ac397e754d065e4df
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509322"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Toestemming van de beheerder op het micro soft Identity-platform

Sommige machtigingen vereisen toestemming van een beheerder voordat ze kunnen worden verleend binnen een Tenant.  U kunt ook het beheerders toestemming-eind punt gebruiken om machtigingen te verlenen aan een hele Tenant.

## <a name="recommended-sign-the-user-into-your-app"></a>Aanbevolen: Onderteken de gebruiker in uw app

Wanneer u een toepassing bouwt die gebruikmaakt van het door de beheerder toestemmings eindpunt, moet de app doorgaans een pagina of weer gave hebben waarin de beheerder de machtigingen van de app kan goed keuren. Deze pagina kan deel uitmaken van de registratie stroom van de app, een deel van de instellingen van de app of een specifieke stroom ' Connect ' zijn. In veel gevallen is het zinvol voor de app om deze weer gave ' verbinding maken ' alleen weer te geven nadat een gebruiker zich heeft aangemeld met een werk-of school Microsoft-account.

Wanneer u de gebruiker aan uw app ondertekent, kunt u de organisatie waarvan de beheerder deel uitmaakt, identificeren voordat u deze vraagt om de benodigde machtigingen goed te keuren. Hoewel het niet strikt nood zakelijk is, kan het u helpen een meer intuïtieve ervaring te creëren voor de gebruikers van uw organisatie. Als u de gebruiker wilt ondertekenen in, volgt u onze [zelf studies voor het micro soft Identity platform-protocol](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>De machtigingen van een adreslijst beheerder aanvragen

Wanneer u klaar bent om machtigingen aan te vragen bij de beheerder van uw organisatie, kunt u de gebruiker omleiden naar het micro soft Identity platform *Administrator toestemmings eindpunt*.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```

| Parameter | Voorwaarde | Beschrijving |
| ---: | ---: | :---: |
| `tenant` | Vereist | De Directory-Tenant waarvan u toestemming wilt aanvragen. Kan worden geleverd in de indeling GUID of beschrijvende naam of in `organizations` het algemeen, zoals wordt weer gegeven in het voor beeld. Gebruik ' common ' niet, omdat persoonlijke accounts geen toestemming van de beheerder kunnen bieden, behalve in de context van een Tenant. Gebruik, indien mogelijk, de Tenant-ID om te zorgen voor optimale compatibiliteit met persoonlijke accounts die tenants beheren. |
| `client_id` | Vereist | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen. |
| `redirect_uri` | Vereist |De omleidings-URI waar u het antwoord voor uw app wilt laten afhandelen. Het moet exact overeenkomen met een van de omleidings-Uri's die u hebt geregistreerd in de app-registratie Portal. |
| `state` | Aanbevolen | Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in de token reactie. Dit kan een teken reeks zijn van elke gewenste inhoud. Gebruik de status om informatie over de status van de gebruiker in de app te coderen voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin deze zijn aangemeld. |
|`scope` | Vereist | Hiermee wordt de set machtigingen gedefinieerd die worden aangevraagd door de toepassing. Dit kan statisch zijn (met behulp van/.default) of dynamische bereiken. Dit kan de OIDC-bereiken ( `openid` , `profile` ,) bevatten `email` . |


Op dit moment heeft Azure AD een Tenant beheerder nodig om zich aan te melden om de aanvraag te volt ooien. De beheerder wordt gevraagd om alle machtigingen die u in de para meter hebt aangevraagd goed te keuren `scope` .  Als u een statische ()- `/.default` waarde hebt gebruikt, werkt deze als het eind punt v 1.0-beheerder toestemming en vraagt u toestemming aan voor alle scopes die zijn gevonden in de vereiste machtigingen voor de app.

### <a name="successful-response"></a>Geslaagde reactie

Als de beheerder de machtigingen voor uw app goedkeurt, ziet het geslaagde antwoord er als volgt uit:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parameter | Beschrijving |
| ---: | :---: |
| `tenant`| De Directory-Tenant die uw toepassing heeft toegewezen aan de aangevraagde machtigingen, in GUID-indeling.|
| `state` | Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in het token antwoord. Dit kan een teken reeks zijn van elke gewenste inhoud. De status wordt gebruikt voor het coderen van informatie over de status van de gebruiker in de app voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden.|
| `scope` | De set machtigingen waaraan toegang is verleend voor de toepassing.|
| `admin_consent` | Wordt ingesteld op `True` .|

### <a name="error-response"></a>Fout bericht

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Toevoegen aan de para meters die worden weer gegeven in een geslaagde reactie, worden de volgende fout parameters weer gegeven.

| Parameter | Beschrijving |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error` | Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten.|
| `error_description` | Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een fout.|
| `tenant`| De Directory-Tenant die uw toepassing heeft toegewezen aan de aangevraagde machtigingen, in GUID-indeling.|
| `state` | Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in het token antwoord. Dit kan een teken reeks zijn van elke gewenste inhoud. De status wordt gebruikt voor het coderen van informatie over de status van de gebruiker in de app voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden.|
| `admin_consent` | Wordt ingesteld op `True` om aan te geven dat dit antwoord is opgetreden in een door de beheerder toestemmings stroom.|

## <a name="next-steps"></a>Volgende stappen
- Zie [een app converteren naar multi tenant](howto-convert-app-to-be-multi-tenant.md)
- Meer informatie over de [toestemming wordt ondersteund op de OAuth 2,0-protocol laag tijdens de autorisatie code toekenning stroom](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Meer informatie [over hoe een toepassing met meerdere tenants het toestemming raamwerk kan gebruiken voor het](./howto-convert-app-to-be-multi-tenant.md) implementeren van de toestemming ' gebruiker ' en ' beheerder ', zodat meer geavanceerde toepassings patronen met meerdere lagen worden ondersteund.
- Meer informatie over [Azure AD-toepassings toestemmings ervaringen](application-consent-experience.md)