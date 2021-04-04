---
title: App Twilio verifiëren met Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Leer hoe u een voor beeld van een online-betalings toepassing in Azure AD B2C integreert met de Twilio-verificatie-API. Voldoen aan de PSD2-trans actie-vereisten (betalings Services-instructie 2) door middel van dynamische koppeling en sterke klant authenticatie.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994040"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>De Twilio-verificatie-app integreren met Azure Active Directory B2C

In dit overzicht leert u hoe u een voor beeld van een online-betalings toepassing in Azure Active Directory B2C (Azure AD B2C) integreert met de Twilio-verificatie-API. Met behulp van de Twilio-app controleren kunnen Azure AD B2C klanten voldoen aan de PSD2-trans actie vereisten (betalings Services-instructie 2) door middel van dynamische koppeling en sterke klant verificatie.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* [Een Azure AD B2C-Tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.
* Een [proef account](https://www.twilio.com/try-twilio) op Twilio.

## <a name="scenario-description"></a>Scenariobeschrijving

De volgende onderdelen vormen een Twilio-oplossing:

- .NET [PSD2 demo web app](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App), waarmee u zich kunt aanmelden of registreren en een dummy-trans actie met een hoog risico moet uitvoeren.
- Azure AD B2C gecombineerde [aanmeldings-en registratie beleid](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy).
- Azure AD B2C-beleid dat is geïntegreerd met Twilio verify-API met behulp van `id_token_hint` .
- .NET-Web-app, die `.well-known` als host fungeert voor een OpenIdConnect-eind punt om validatie van een te kunnen maken `id_token_hint` .


    ![twilio-stroom](media/partner-twilio/twilio-flow.png)

| Stap | Beschrijving |
|------|------|
| 1     | De gebruiker start het aanmelden of meldt zich aan bij de PSD2-demo-app. De gebruiker wordt geverifieerd via het Azure AD B2C gecombineerde aanmeldings-en registratie beleid. Er wordt een token geretourneerd naar de toepassing. Bij het aanmelden wordt het telefoon nummer van de gebruiker geverifieerd met behulp van SMS/Phone en geregistreerd op hun Azure AD B2C-account.     |
| 2     | De gebruiker initieert een trans actie met een hoog risico, zoals een overdracht van $50,00. Het huidige toegangs token van de gebruiker wordt geëvalueerd voor de PolicyId om te bepalen of de gebruiker al is geverifieerd via een Step-Up aangepast beleid.     |
| 3     | De toepassing registreert de transactie waarde en de begunstigde, $50,00 en John Splinter en genereert een ondertekend token. Dit token heet een `id_token_hint` en bevat de claim `amount:$500, payee:john doe` . De `id_token_hint` wordt samen met de aanvraag verzonden naar het aangepaste beleid Azure AD B2C, dat is geïntegreerd met Twilio.     |
| 4     | Azure AD B2C controleert de hand tekening van de id_token_hint door het Connect- `/.well-known` eind punt toepassingen OpenID Connect te controleren. Na het verifiëren worden de claims van dit token geëxtraheerd, met name de `amount` en `payee` . De gebruiker ziet een pagina om te controleren of hun mobiele telefoon nummer via SMS-bericht is.     |
| 5     | De gebruiker vraagt om zijn telefoon nummer te verifiëren via SMS-bericht en Azure AD B2C maakt een REST API aanvraag voor het Twilio van het API-eind punt. Ook worden de trans actie `amount` en `payee` als onderdeel van het PSD2-proces voor het genereren van de eenmalige wachtwoord code (OTP) verzonden. Twilio verzendt een SMS-bericht naar het geregistreerde telefoon nummer van de gebruiker.     |
| 6     |  De gebruiker voert de OTP ontvangen in hun SMS-bericht en verzendt deze naar Azure AD B2C. Azure AD B2C maakt een API-aanvraag met deze OTP om de Twilio-API te controleren om te controleren of de OTP juist is. Ten slotte wordt er een token aan de toepassing verleend, met een nieuwe PolicyId waarmee de gebruiker wordt getrapt.    |
|      |      |

## <a name="onboard-with-twilio"></a>Onboard met Twilio

1. Verkrijg een [proef account](https://www.twilio.com/try-twilio) op Twilio.

2. Koop een telefoon nummer op Twilio zoals beschreven in [dit artikel](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)

3. Ga naar de Twilio-console om de [API te controleren](https://www.twilio.com/console/verify/services) en volg de [instructies](https://www.twilio.com/docs/verify/verifying-transactions-psd2) om een service te maken en de optie PSD2 in te scha kelen.  

## <a name="configure-the-psd2-demo-app"></a>De PSD2-demo-app configureren

1. Open de oplossing B2C-WebAPI-DotNet en vervang de volgende waarden door uw eigen Tenant-specifieke waarden in de web.config:

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. De [Web-app](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) fungeert ook als host voor de id-token hint en het eind punt voor meta gegevens.
   - Maak uw Ondertekeningscertificaat zoals beschreven in deze [voorbeeld beschrijving](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate).
   - Werk de volgende regels op basis van uw certificaat bij in de web.config:
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. Upload de demo toepassing naar uw hosting provider of keuze. Richt lijnen voor de Azure App Service vindt u in [deze voorbeeld beschrijving](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service), inclusief instructies voor het uploaden van uw certificaat.

4. Werk de registratie van uw Azure AD B2C-toepassing bij door een antwoord-URL toe te voegen die gelijk is aan de URL waarop de toepassing wordt gehost.

5. Open de [beleids bestanden](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) en vervang alle exemplaren van  `contoso` met de naam van uw Tenant.

6. Zoek het Twilio REST API technische profiel **aangepaste SMS-inschrijven**. Werk de  `ServiceURL`   met uw Twilio-AccountSID en het van-nummer bij naar uw aangeschafte telefoon nummer.

7. Zoek de Twilio-REST API Technical Profiles, **TwilioRestAPI-verify-stap 1-4**   en **TwilioRestAPI-step2** en werk de  `ServiceURL`   met uw Twilio AccountSID bij.

## <a name="integrate-with-azure-ad-b2c"></a>Integreren met Azure AD B2C

Voeg de beleids bestanden toe aan Azure AD B2C:

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).

2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.

3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.

4. Navigeer naar **Azure AD B2C**  >  **Identity experience Framework**-  >  **beleids sleutels**.

5. Voeg een nieuwe sleutel toe met de naam **B2cRestTwilioClientId**. Selecteer **hand matig** en geef de waarde van de Twilio AccountSID op.

6. Voeg een nieuwe sleutel toe met de naam **B2cRestTwilioClientSecret**. Selecteer **hand matig** en geef de waarde van het TWILIO-verificatie token op.

7. Upload alle beleids bestanden naar uw Tenant.

8. Pas de teken reeks in de GenerateOTPMessageEnrol-claim transformatie voor uw SMS-registratie tekst aan.

## <a name="test-the-solution"></a>De oplossing testen

* Blader naar uw toepassing en test de acties voor aanmelden, registreren en verzenden van geld.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- Raadpleeg GitHub voor voor [beelden van Twilio-integratie code](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)  

- [Aangepast beleid in AAD B2C](custom-policy-overview.md)

- [Aan de slag met aangepast beleid in AAD B2C](custom-policy-get-started.md?tabs=applications)
