---
title: Azure-SAML-protocol voor eenmalige afmelding
description: In dit artikel wordt het SAML-protocol single Sign-Out in Azure Active Directory beschreven.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 040e49ee870704107e1d4118f1484119d98a9ebf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781208"
---
# <a name="single-sign-out-saml-protocol"></a>Enkelvoudig Sign-Out SAML-Protocol

Azure Active Directory (Azure AD) ondersteunt de SAML 2,0-webbrowser voor eenmalige afmelding. Voor een juiste werking van eenmalige afmelding moet de **LogoutURL** voor de toepassing expliciet worden geregistreerd bij Azure ad tijdens de registratie van de toepassing. Als de app wordt [toegevoegd aan de Azure-app galerie](v2-howto-app-gallery-listing.md) , kan deze waarde standaard worden ingesteld. Anders moet de waarde worden bepaald en ingesteld door de persoon die de app toevoegt aan hun Azure AD-Tenant. Azure AD gebruikt de LogoutURL om gebruikers te omleiden nadat ze zijn afgemeld. 

Azure AD biedt ondersteuning voor omleidings binding (HTTP GET) en geen HTTP POST-binding.

In het volgende diagram ziet u de werk stroom van het proces voor eenmalige aanmelding van Azure AD.

![Azure AD-werk stroom voor eenmalige afmelding](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
De Cloud service stuurt een `LogoutRequest` bericht naar Azure AD om aan te geven dat een sessie is beëindigd. Het volgende fragment toont een voorbeeld `LogoutRequest` element.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Het `LogoutRequest` element dat naar Azure AD wordt verzonden, vereist de volgende kenmerken:

* `ID` -Hiermee wordt de afmeldings aanvraag geïdentificeerd. De waarde van `ID` mag niet beginnen met een getal. De gang bare procedure is het toevoegen van **id** aan de teken reeks representatie van een GUID.
* `Version` -Stel de waarde van dit element in op **2,0**. Deze waarde is verplicht.
* `IssueInstant` : Dit is een `DateTime` teken reeks met een UTC-waarde (Coordinate Universal Time) en een notatie voor de [retour Tour ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). Azure AD verwacht een waarde van dit type, maar dwingt dit niet af.

### <a name="issuer"></a>Verlener
Het `Issuer` element in een `LogoutRequest` moet exact overeenkomen met een van de **ServicePrincipalNames** in de Cloud service in azure AD. Dit is normaal gesp roken ingesteld op de **App-ID-URI** die is opgegeven tijdens de registratie van de toepassing.

### <a name="nameid"></a>Meid
De waarde van het `NameID` element moet exact overeenkomen met de `NameID` van de gebruiker die wordt afgemeld.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD stuurt een `LogoutResponse` als-antwoord naar een- `LogoutRequest` element. Het volgende fragment toont een voor beeld `LogoutResponse` .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD stelt de `ID` en `Version` `IssueInstant` waarden in het- `LogoutResponse` element in. Het element wordt ook ingesteld `InResponseTo` op de waarde van het `ID` kenmerk van de `LogoutRequest` die het antwoord heeft gereageerd.

### <a name="issuer"></a>Verlener
Azure AD stelt deze waarde in `https://login.microsoftonline.com/<TenantIdGUID>/` op \<TenantIdGUID> de locatie van de Tenant-id van de Azure AD-Tenant.

Als u de waarde van het `Issuer` element wilt evalueren, gebruikt u de waarde van de **App-ID-URI** die is geleverd tijdens de registratie van de toepassing.

### <a name="status"></a>Status
Azure AD gebruikt het `StatusCode` element in het- `Status` element om aan te geven dat de afmelding is geslaagd of mislukt. Wanneer de aanmeldings poging mislukt, `StatusCode` kan het element ook aangepaste fout berichten bevatten.
