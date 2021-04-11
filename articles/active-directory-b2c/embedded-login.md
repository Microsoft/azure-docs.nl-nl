---
title: Azure Active Directory B2C gebruikers interface insluiten in uw app met een aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over het insluiten van Azure Active Directory B2C gebruikers interface in uw app met een aangepast beleid
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ccad323c1834894367cca0ef0d3f98eb1b1b1ec3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639916"
---
# <a name="embedded-sign-in-experience"></a>Inge sloten aanmeldings ervaring

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Voor een eenvoudigere aanmeld ervaring kunt u voor komen dat gebruikers worden omgeleid naar een afzonderlijke aanmeldings pagina of een pop-upvenster genereren. Met behulp van het inline frame `<iframe>` -element kunt u de Azure AD B2C aanmeldings gebruikers interface rechtstreeks insluiten in uw webtoepassing.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Aanmelding voor Inge sloten webtoepassing

Het inline frame-element `<iframe>` wordt gebruikt om een document in een HTML5-webpagina in te sluiten. U kunt het IFRAME-element gebruiken om de Azure AD B2C aanmeld gebruikers interface rechtstreeks in uw webtoepassing in te sluiten, zoals in het volgende voor beeld wordt weer gegeven:

![Aanmelden met zwevende DIV-ervaring](media/embedded-login/login-hovering.png)

Houd bij het gebruik van iframe rekening met het volgende:

- Inge sloten aanmelding ondersteunt alleen lokale accounts. De meeste leveranciers van sociale identiteiten (bijvoorbeeld Google en Facebook) blok keren dat hun aanmeldings pagina's worden weer gegeven in inline frames.
- Omdat Azure AD B2C sessie cookies binnen een IFRAME worden beschouwd als cookies van derden, blok keren of wissen van bepaalde browsers (bijvoorbeeld Safari of Chrome in Incognito-modus), worden deze cookies geblokkeerd, wat een ongewenste gebruikers ervaring oplevert. U kunt dit probleem voor komen door ervoor te zorgen dat de domein naam van uw toepassing en uw Azure AD B2C domein *dezelfde oorsprong* hebben. Als u dezelfde oorsprong wilt gebruiken, [schakelt u aangepaste domeinen](custom-domain.md) voor Azure AD B2C Tenant in en configureert u uw web-app met dezelfde oorsprong. Een toepassing die wordt gehost op, https://app.contoso.com heeft bijvoorbeeld dezelfde oorsprong als Azure AD B2C uitgevoerd op https://login.contoso.com .

## <a name="prerequisites"></a>Vereisten

* Voer de stappen uit in het [Active Directory B2C aan de slag met aangepaste beleids regels](custom-policy-get-started.md).
* [Schakel aangepaste domeinen in](custom-domain.md) voor uw beleid.

## <a name="configure-your-policy"></a>Uw beleid configureren

Als u wilt toestaan dat uw Azure AD B2C-gebruikers interface wordt Inge sloten in een IFRAME, moeten een beveiligings beleid voor inhoud `Content-Security-Policy` en frame opties `X-Frame-Options` worden opgenomen in de Azure AD B2C http-antwoord headers. Met deze headers kan de Azure AD B2C gebruikers interface worden uitgevoerd onder de domein naam van uw toepassing.

Voeg een **JourneyFraming** -element toe in het element [RelyingParty](relyingparty.md) .  Het **UserJourneyBehaviors** -element moet volgen op de **DefaultUserJourney**. Het **UserJourneyBehaviors** -element moet er als volgt uitzien:

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

Het kenmerk **Sources** bevat de URI van uw webtoepassing. Voeg een spatie toe tussen Uri's. Elke URI moet voldoen aan de volgende vereisten:

- De URI moet worden vertrouwd en eigendom zijn van uw toepassing.
- De URI moet het HTTPS-schema gebruiken.  
- De volledige URI van de web-app moet worden opgegeven. Jokertekens worden niet ondersteund.

Daarnaast is het raadzaam dat u ook uw eigen domein naam blokkeert zodat deze wordt Inge sloten in een IFRAME door respectievelijk de headers Content-Security-Policy en X-frame-Options in te stellen op de pagina's van uw toepassing. Dit verkleint de beveiligings problemen voor oudere browsers die betrekking hebben op genest insluiten van iframes.

## <a name="adjust-policy-user-interface"></a>Gebruikers interface voor beleid aanpassen

Met Azure AD B2C [aanpassing van de gebruikers interface](customize-ui.md)hebt u bijna volledige controle over de HTML-en CSS-inhoud die aan gebruikers wordt gepresenteerd. Volg de stappen voor het aanpassen van een HTML-pagina met behulp van inhouds definities. Als u de Azure AD B2C gebruikers interface in de iframe-grootte wilt passen, biedt u een schone HTML-pagina zonder achtergrond en extra spaties.  

Met de volgende CSS-code worden de Azure AD B2C HTML-elementen verborgen en wordt de grootte van het paneel aangepast om het iframe in te vullen.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

In sommige gevallen wilt u mogelijk op de hoogte brengen van de Azure AD B2C pagina die momenteel wordt weer gegeven. Wanneer een gebruiker bijvoorbeeld de registratie optie selecteert, wilt u mogelijk dat de toepassing reageert door de koppelingen te verbergen om u aan te melden met een sociaal account of de iframe-grootte aan te passen.

Om uw toepassing op de hoogte te stellen van de huidige Azure AD B2C pagina, [schakelt u uw beleid in voor Java script](./javascript-and-page-layout.md)en gebruikt u HTML5 post-berichten. Met de volgende Java script-code wordt een post-bericht verzonden naar de app met `signUp` :

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Een webtoepassing configureren

Wanneer een gebruiker de knop Aanmelden selecteert, genereert de [Web-app](code-samples.md#web-apps-and-apis) een autorisatie aanvraag waarmee de gebruiker zich Azure AD B2C aanmelden. Nadat het aanmelden is voltooid, retourneert Azure AD B2C een ID-token of autorisatie code naar de geconfigureerde omleidings-URI in uw toepassing.

Ter ondersteuning van Inge sloten aanmelding wordt de eigenschap van de iframe **src** naar de aanmeldings controller verwezen, bijvoorbeeld `/account/SignUpSignIn` , waarmee de autorisatie aanvraag wordt gegenereerd en de gebruiker wordt omgeleid naar Azure AD B2C beleid.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

Nadat het ID-token is ontvangen en gevalideerd door de toepassing, is de autorisatie stroom voltooid en wordt de gebruiker door de toepassing herkend en vertrouwd. Omdat de autorisatie stroom in het iframe plaatsvindt, moet u de hoofd pagina opnieuw laden. Nadat de pagina opnieuw is geladen, verandert de aanmeldings knop in afmelden en de gebruikers naam wordt weer gegeven in de gebruikers interface.  

Hieronder ziet u een voor beeld van hoe de aanmeldings omleidings-URI de hoofd pagina kan vernieuwen:

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Aanmelden met sociale accounts toevoegen aan een web-app

Sociale id-providers blok keren hun aanmeldings pagina's van weer gave in inline-frames. U kunt een afzonderlijk beleid voor sociale accounts gebruiken, maar u kunt ook één beleid gebruiken voor zowel aanmelden als aanmelden met lokale en sociale accounts. Vervolgens kunt u de `domain_hint` query reeks parameter gebruiken. De para meter domein hint neemt de gebruiker rechtstreeks op de aanmeldings pagina van de sociaal-ID-provider.

Voeg in uw toepassing de knoppen aanmelden met sociale account toe. Wanneer een gebruiker op een van de knoppen van het sociaal-account klikt, moet het besturings element de naam van het beleid wijzigen of de para meter domein Hint instellen.

<!-- TBD: add a diagram -->

De omleidings-URI kan dezelfde omleidings-URI zijn die wordt gebruikt door de IFRAME. U kunt de pagina opnieuw laden overs Laan.

## <a name="configure-a-single-page-application"></a>Een toepassing met één pagina configureren

Voor een toepassing met één pagina moet u ook een tweede HTML-pagina die wordt geladen in het iframe. Deze aanmeldings pagina host de verificatie bibliotheek code die de autorisatie code genereert en retourneert het token.

Wanneer de toepassing met één pagina het toegangs token nodig heeft, gebruikt u Java script-code om het toegangs token op te halen uit de IFRAME en het object waarin het is opgenomen.

> [!NOTE]
> Het uitvoeren van MSAL 2,0 in een iframe wordt momenteel niet ondersteund.

De volgende code is een voor beeld dat wordt uitgevoerd op de hoofd pagina en de Java script-code van een IFRAME aanroept:

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende verwante artikelen:

- [Aanpassing van de gebruikersinterface](customize-ui.md)
- Verwijzing naar [RelyingParty](relyingparty.md) -element
- [Uw beleid voor Java script inschakelen](./javascript-and-page-layout.md)
- [Codevoorbeelden](code-samples.md)

::: zone-end
