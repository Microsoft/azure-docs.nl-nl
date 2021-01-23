---
title: Verhoog de flexibiliteit van verificatie en autorisatie in client toepassingen die u ontwikkelt
titleSuffix: Microsoft identity platform
description: Richt lijnen voor het verg Roten van de tolerantie van verificatie en autorisatie in client toepassing met behulp van het micro soft Identity platform
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: b32f9dd10d9bd03a7e446616d9941e7bd1a9c3ed
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724905"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>Verhoog de flexibiliteit van verificatie en autorisatie in client toepassingen die u ontwikkelt

Deze sectie bevat richt lijnen voor het bouwen van flexibiliteit in client toepassingen die gebruikmaken van het micro soft-identiteits platform en Azure Active Directory voor het aanmelden van gebruikers en het uitvoeren van acties namens deze gebruikers.

## <a name="use-the-microsoft-authentication-library-msal"></a>De micro soft Authentication Library (MSAL) gebruiken

De [micro soft Authentication Library (MSAL)](../develop/msal-overview.md) is een belang rijk onderdeel van het [micro soft-identiteits platform](../develop/index.yml). Het vereenvoudigt en beheert de aanschaf, het beheer, de caching en het vernieuwen van tokens, en maakt gebruik van aanbevolen procedures voor tolerantie. MSAL is ontworpen om een veilige oplossing te bieden zonder ontwikkel aars die zich zorgen hoeven te maken over de implementatie details.

MSAL slaat tokens op en gebruikt een patroon voor het ophalen van Silent-tokens. Ook wordt de token cache automatisch geserialiseerd op platforms die systeem eigen beveiligde opslag bieden, zoals Windows UWP, iOS en Android. Ontwikkel aars kunnen het serialisatie gedrag aanpassen bij het gebruik van [micro soft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization), [MSAL.net](../develop/msal-net-token-cache-serialization.md), [MSAL voor Java](../develop/msal-java-token-cache-serialization.md)en [MSAL voor python](../develop/msal-python-token-cache-serialization.md).

![Afbeelding van een apparaat met en toepassing met behulp van MSAL om micro soft Identity aan te roepen](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

Bij het gebruik van MSAL, wordt het in de cache opslaan van tokens, vernieuwen en Silent-aanschaf automatisch ondersteund. U kunt eenvoudige patronen gebruiken om de tokens te verkrijgen die nodig zijn voor moderne verificatie. We ondersteunen veel talen en u kunt een voor beeld vinden dat overeenkomt met uw taal en scenario op onze pagina met voor [beelden](../develop/sample-v2-code.md) .

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[Ondersteunen](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

MSAL kan in sommige gevallen proactief tokens vernieuwen. Wanneer de micro soft-identiteit een langlopend token uitgeeft, kan deze informatie naar de client verzenden voor de optimale tijd voor het vernieuwen van het token ("vernieuwen \_ in"). MSAL zal het token proactief vernieuwen op basis van deze gegevens. De app wordt uitgevoerd terwijl het oude token geldig is, maar zal een langere periode hebben om een nieuwe geslaagde token verwerving te maken.

### <a name="stay-up-to-date"></a>Op de hoogte blijven

Ontwikkel aars moeten een proces voor het bijwerken van de nieuwste MSAL-versie hebben. Verificatie maakt deel uit van de app-beveiliging en uw app moet op de hoogte blijven van de beveiligings verbeteringen die zijn opgenomen in nieuwe MSAL-releases. Dit is in het algemeen een goede gewoonte voor bibliotheken onder voortdurende ontwikkeling. Dit zorgt ervoor dat u beschikt over de meest recente datum code met betrekking tot app-tolerantie. Omdat de micro soft-identiteit voortdurend wordt verinnovatied op manieren om toepassingen flexibeler te maken, zijn apps die gebruikmaken van de meest recente MSAL, de meest voor bereiding op deze innovaties.

[Controleer de nieuwste MSAL.js versie en opmerkingen bij de release](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Controleer de meest recente versie van MSAL .NET en release opmerkingen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[Controleer de meest recente versie van MSAL python en de release opmerkingen](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[Controleer de meest recente MSAL Java-versie en release opmerkingen](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[Controleer de meest recente versie van MSAL iOS en macOS en release opmerkingen](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[Controleer de meest recente versie van MSAL Android en release opmerkingen](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[De meest recente versie van de MSAL hoek en release opmerkingen controleren](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Controleer de nieuwste versie van micro soft. Identity. Web en release-opmerkingen](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>Flexibele patronen gebruiken voor het verwerken van tokens

Als u geen gebruik maakt van MSAL, kunt u deze flexibele patronen gebruiken voor het verwerken van tokens. Deze aanbevolen procedures worden automatisch geïmplementeerd door de MSAL-bibliotheek. 

Over het algemeen roept een toepassing die gebruikmaakt van moderne verificatie een eind punt aan om tokens op te halen die de gebruiker verifiëren of de toepassing autoriseren om beveiligde Api's aan te roepen. MSAL is bedoeld om de details van de verificatie te verwerken en verschillende patronen te implementeren om de tolerantie van dit proces te verbeteren. Gebruik de richt lijnen in deze sectie om aanbevolen procedures te implementeren als u ervoor kiest om een andere bibliotheek dan MSAL te gebruiken. Als u MSAL gebruikt, kunt u al deze aanbevolen procedures gratis uitvoeren, omdat MSAL deze automatisch implementeert.

### <a name="cache-tokens"></a>Cache-tokens

Apps moeten tokens die zijn ontvangen van de micro soft-identiteit correct in de cache opslaan. Wanneer uw app tokens ontvangt, bevat het HTTP-antwoord dat de tokens bevat ook een eigenschap "expires_in" waarmee wordt aangegeven hoe lang het cache geheugen moet worden opgeslagen en hoe het token opnieuw moet worden gebruikt. Het is belang rijk dat toepassingen de eigenschap ' expires_in ' gebruiken om de levens duur van het token te bepalen. De toepassing moet nooit proberen een API-toegangs token te decoderen.

![Een toepassing die een aanroep naar een micro soft-identiteit maakt, maar de aanroep gaat via een token cache op het apparaat waarop de toepassing wordt uitgevoerd.](media/resilience-client-app/token-cache.png)

Het gebruik van het token in de cache voor komt onnodig verkeer tussen uw app en micro soft-identiteit, en maakt uw app minder gevoelig voor het verkrijgen van fouten in tokens door het aantal aanroepen van token-aanschaf te verminderen. Tokens in de cache verbeteren ook de prestaties van uw toepassing omdat de app moet blok keren op het verkrijgen van tokens minder. Uw gebruiker kan aangemeld blijven bij uw toepassing voor de lengte van de levens duur van dat token.

### <a name="serialize-and-persist-tokens"></a>Tokens serialiseren en persistent maken

Apps moeten hun token cache veilig serialiseren om de tokens tussen exemplaren van de app op te slaan. Tokens kunnen opnieuw worden gebruikt zolang ze binnen hun geldige levens duur vallen. [Tokens vernieuwen](../develop/v2-oauth2-auth-code-flow.md#refresh-the-access-token)en, steeds meer [toegangs tokens](../develop/access-tokens.md), worden uitgegeven voor veel uren. Deze geldige tijd kan een gebruiker meerdere keren in de start zetten van uw toepassing. Wanneer uw app wordt gestart, wordt gecontroleerd of er een geldig toegangs-of vernieuwings token is dat kan worden gebruikt. Hierdoor worden de tolerantie en prestaties van de app verhoogd omdat overbodige aanroepen naar micro soft-identiteit worden voor komen.

![Een toepassing die een micro soft-identiteit aanroept, maar de aanroep gaat via een token cache en een token opslag op het apparaat waarop de toepassing wordt uitgevoerd.](media/resilience-client-app/token-store.png)

De permanente token opslag moet worden beheerd en versleuteld met de gebruikers-of proces identiteit van de eigenaar. Op platforms zoals mobiel, Windows en Mac moet de ontwikkelaar gebruikmaken van ingebouwde mogelijkheden voor het opslaan van referenties.

### <a name="acquire-tokens-silently"></a>Tokens op de achtergrond verkrijgen

Het proces van het verifiëren van een gebruiker of het ophalen van een autorisatie voor het aanroepen van een API kan meerdere stappen in micro soft Identity vereisen. Als de gebruiker zich bijvoorbeeld voor de eerste keer aanmeldt, moeten ze mogelijk referenties invoeren en een multi-factor Authentication uitvoeren via een tekst bericht. Elke stap voegt een afhankelijkheid toe aan de resource die de service levert. De beste ervaring voor de gebruiker en de gebruikers met de minste afhankelijkheden is om een token op de achtergrond te verkrijgen om deze extra stappen te vermijden voordat gebruikers interactie wordt aangevraagd.

![Diagram van de verschillende services in micro soft-identiteiten die mogelijk moeten worden uitgevoerd om het proces van verificatie of autorisatie van een gebruiker te volt ooien](media/resilience-client-app/external-dependencies.png)

Het ophalen van een token op de achtergrond begint met het gebruik van een geldig token uit de token cache van de app. Als er geen geldig token beschikbaar is, moet de app proberen een token op te halen met behulp van een vernieuwings token, indien beschikbaar, en het eind punt van de token. Als geen van deze opties beschikbaar is, moet de app een token aanschaffen met behulp van de para meter ' prompt = none '. Hiermee wordt het autorisatie-eind punt gebruikt, maar geen gebruikers interface weer gegeven voor de gebruiker. Als de micro soft-identiteit een token kan bieden aan de app zonder interactie met de gebruiker, wordt dit door. Als geen van deze methoden een token als resultaat heeft, moet een gebruiker interactief opnieuw verifiëren.

> [!NOTE]
> Over het algemeen moeten apps het gebruik van prompts als "aanmelden" en "toestemming" voor komen, omdat deze gebruikers interactie afdwingen, zelfs wanneer er geen interactie vereist is.

## <a name="handle-service-responses-properly"></a>Service Reacties correct afhandelen

Hoewel toepassingen alle fout reacties moeten verwerken, zijn er enkele reacties die van invloed kunnen zijn op de tolerantie. Als uw toepassing een HTTP 429-respons code ontvangt, worden er te veel aanvragen van micro soft-identiteit voor uw aanvragen beperkt. Als uw app te veel aanvragen blijft uitvoeren, wordt er nog steeds beperkt om te voor komen dat uw app tokens ontvangt. Uw toepassing mag niet opnieuw proberen om een token te verkrijgen tot na de tijd, in seconden, in het Retry-After antwoord veld is verstreken. Het ontvangen van een 429-antwoord is vaak een indicatie dat de toepassing niet in de cache opslaat en tokens correct opnieuw gebruikt. Ontwikkel aars moeten zien hoe tokens in de cache worden opgeslagen en opnieuw worden gebruikt in de toepassing.

Wanneer een toepassing een HTTP 5xx-respons code ontvangt, mag de app geen snelle herhalings lus invoeren. Wanneer dit het geval is, moet de toepassing dezelfde Retry-After verwerking hebben als voor een 429-antwoord. Als er geen Retry-After header door het antwoord wordt gegeven, raden we u aan om een exponentiële back-up uit te voeren met de eerste poging ten minste 5 seconden na het antwoord.

Wanneer een aanvraag een time-out optreedt voor toepassingen, moet de toepassing niet direct opnieuw proberen. Implementeer een exponentiële back-uit met de eerste poging ten minste 5 seconden na het antwoord.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>Opties evalueren voor het ophalen van gegevens met betrekking tot autorisatie

Veel toepassingen en Api's hebben specifieke informatie over de gebruiker nodig om autorisatie beslissingen te nemen. Er zijn een aantal manieren waarop een toepassing deze gegevens kan ophalen. Elke methode heeft zijn voor delen en nadelen. Ontwikkel aars moeten deze wegen om te bepalen welke strategie het meest geschikt is voor flexibiliteit in hun app.

### <a name="tokens"></a>Tokens

ID-tokens en toegangs tokens bevatten standaard claims die informatie geven over het onderwerp. Deze worden beschreven in de [micro soft Identity platform ID-tokens](../develop/id-tokens.md) en [toegangs tokens voor micro soft Identity platform](../develop/access-tokens.md). Als de informatie die uw app nodig heeft, al in het token is, is de meest efficiënte techniek voor het ophalen van die gegevens het gebruik van token claims, omdat de overgehoord van een extra netwerk aanroep wordt opgeslagen om informatie afzonderlijk op te halen. Minder netwerk aanroepen betekenen een hogere algehele tolerantie voor de toepassing.

> [!NOTE]
> Sommige toepassingen roepen het user info-eind punt aan om claims op te halen over de gebruiker die is geverifieerd. De informatie die beschikbaar is in het ID-token dat uw app kan ontvangen, is een superset van de informatie die kan worden opgehaald uit het user info-eind punt. Uw app moet het ID-token gebruiken om informatie over de gebruiker op te halen in plaats van het user info-eind punt aan te roepen.

Een app-ontwikkelaar kan standaard token claims uitbreiden met [optionele claims](../develop/active-directory-optional-claims.md). Een veelvoorkomende optionele claim is [groepen](../develop/active-directory-optional-claims.md#configuring-groups-optional-claims). Er zijn verschillende manieren om groeps claims toe te voegen. De optie toepassings groep bevat alleen groepen die zijn toegewezen aan de toepassing. De opties ' alle ' of ' beveiligings groepen ' bevatten groepen van alle apps in dezelfde Tenant, waarmee u veel groepen aan het token kunt toevoegen. Het is belang rijk dat u het effect in uw geval evalueert, omdat het mogelijk de efficiency kan afleiden die is ontstaan door groepen in het token aan te vragen door de tokens te veroorzaken en zelfs extra aanroepen te vereisen om de volledige lijst met groepen te verkrijgen.

In plaats van groepen in uw token te gebruiken, kunt u in plaats daarvan app-rollen gebruiken en toevoegen. Ontwikkel aars kunnen [app-rollen](../develop/howto-add-app-roles-in-azure-ad-apps.md) definiëren voor hun apps en api's die de klant kan beheren vanuit hun directory met behulp van de portal of api's. IT-professionals kunnen vervolgens rollen toewijzen aan verschillende gebruikers en groepen om te bepalen wie toegang heeft tot de inhoud en functionaliteit. Wanneer er een token voor de toepassing of API wordt uitgegeven, zijn de rollen die aan de gebruiker zijn toegewezen, beschikbaar in de rol claim in het token. Als u deze gegevens rechtstreeks in een token ophaalt, kunnen er extra Api's worden opgeslagen.

Ten slotte kunnen IT-beheerders ook claims toevoegen op basis van specifieke informatie in een Tenant. Een onderneming kan bijvoorbeeld een uitbrei ding hebben die een specifieke gebruikers-ID voor de onderneming heeft.

In alle gevallen kan het toevoegen van gegevens uit de Directory rechtstreeks aan een token efficiënt zijn en de hertolerantie van apps verhogen door het aantal afhankelijkheden van de app te verminderen. Aan de andere kant worden geen tolerantie problemen opgelost zodat er geen token kan worden verkregen. Voeg alleen optionele claims toe voor de belangrijkste scenario's van uw toepassing. Als de app alleen informatie vereist voor de beheer functionaliteit, is het voor de toepassing het beste om die informatie alleen naar behoefte te verkrijgen.

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph biedt een uniform API-eind punt voor toegang tot de Microsoft 365 gegevens waarmee de patronen van productiviteit, identiteit en beveiliging in een organisatie worden beschreven. Toepassingen die gebruikmaken van Microsoft Graph kunnen een van de gegevens in Microsoft 365 gebruiken voor autorisatie beslissingen.

Apps hebben slechts één token nodig voor toegang tot alle Microsoft 365. Dit is robuuster dan het gebruik van de oudere Api's die specifiek zijn voor Microsoft 365 onderdelen zoals micro soft Exchange of micro soft share point, waar meerdere tokens zijn vereist.

Wanneer u Microsoft Graph-Api's gebruikt, raden we u aan om een [Microsoft Graph SDK](/graph/sdks/sdks-overview)te gebruiken. De Microsoft Graph Sdk's zijn ontworpen om het bouwen van hoogwaardige, efficiënte en flexibele toepassingen te vereenvoudigen die toegang hebben tot Microsoft Graph.

Ontwikkel aars moeten overwegen om de claims die beschikbaar zijn in een token als alternatief voor sommige Microsoft Graph-aanroepen te gebruiken voor autorisatie beslissingen. Zoals hierboven vermeld, kunnen ontwikkel aars groepen, app-rollen en optionele claims in hun tokens aanvragen. In het kader van tolerantie moeten voor het gebruik van Microsoft Graph voor autorisatie extra netwerk aanroepen worden gebruikt die afhankelijk zijn van micro soft-identiteit (om het token te verkrijgen voor toegang tot Microsoft Graph), evenals Microsoft Graph zichzelf. Als uw toepassing echter al gebruikmaakt van Microsoft Graph als gegevenslaag, is het niet meer nodig om de grafiek voor autorisatie te gebruiken.

## <a name="use-broker-authentication-on-mobile-devices"></a>Broker-verificatie op mobiele apparaten gebruiken

Op mobiele apparaten wordt met behulp van een verificatie Broker, zoals Microsoft Authenticator, de tolerantie verbeterd. De Broker voegt voor delen toe boven wat er beschikbaar is met andere opties, zoals de systeem browser of een Inge sloten webweergave. De verificatie Broker kan gebruikmaken van een [primair vernieuwings token](../devices/concept-primary-refresh-token.md) (PRT) dat claims bevat over de gebruiker en het apparaat en kan worden gebruikt om verificatie tokens op te halen voor toegang tot andere toepassingen vanaf het apparaat. Wanneer een PRT wordt gebruikt om toegang tot een toepassing aan te vragen, worden de apparaat-en MFA-claims vertrouwd door Azure AD. Dit verhoogt de flexibiliteit door extra stappen te vermijden om het apparaat opnieuw te verifiëren. Gebruikers worden niet aangevraagd met meerdere MFA-prompts op hetzelfde apparaat, waardoor de flexibiliteit wordt verg root door afhankelijkheden op externe services te verminderen en de gebruikers ervaring te verbeteren.

![Een toepassing die een micro soft-identiteit aanroept, maar de aanroep gaat via een token cache, evenals een token archief en een verificatie Broker op het apparaat waarop de toepassing wordt uitgevoerd.](media/resilience-client-app/authentication-broker.png)

Broker-verificatie wordt automatisch ondersteund door MSAL. Op de volgende pagina's vindt u meer informatie over het gebruik van brokered-verificatie:

- [Eenmalige aanmelding configureren in macOS en iOS](../develop/single-sign-on-macos-ios.md#sso-through-authentication-broker-on-ios)
- [SSO van meerdere apps inschakelen op Android met behulp van MSAL](../develop/msal-android-single-sign-on.md)

## <a name="adopt-continuous-access-evaluation"></a>Evaluatie van continue toegang aannemen

De [evaluatie van continue toegang (CAE)](../conditional-access/concept-continuous-access-evaluation.md) is een recente ontwikkeling waarmee de beveiliging en tolerantie van toepassingen met een lange levens duur kunnen worden verbeterd. CAE is een nieuwe industrie standaard die wordt ontwikkeld in de werk groep gedeelde signalen en gebeurtenissen van de OpenID Connect Foundation. Met CAE kan een toegangs token worden ingetrokken op basis van [kritieke gebeurtenissen](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) en [beleids evaluatie](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview), in plaats van een korte levens duur van tokens. Voor sommige resource-Api's, omdat het risico en het beleid in realtime worden geëvalueerd, kan CAE de token levensduur aanzienlijk verhogen tot 28 uur. Als resource-Api's en-toepassingen CAE gebruiken, kunnen micro soft-identiteiten toegangs tokens uitgeven die ingetrokken zijn en gedurende langere tijd geldig zijn. Deze tokens met lange levens duur worden proactief vernieuwd door MSAL.

Hoewel CAE zich in vroege stadia bevindt, is het mogelijk om [vandaag nog client toepassingen te ontwikkelen die van CAE profiteren](../develop/app-resilience-continuous-access-evaluation.md) wanneer de bronnen (api's) die de toepassing gebruikt, de CAE gebruiken. Naarmate er meer bronnen worden gebruikt, kan uw toepassing ook gebindte tokens verkrijgen voor deze resources. Met de Microsoft Graph-API en de [Microsoft Graph sdk's](/graph/sdks/sdks-overview)wordt een voor beeld van de CAE-mogelijkheden voor Early 2021. Als je wilt deel nemen aan de open bare preview van Microsoft Graph met CAE, kun je ons laten weten dat je hier geïnteresseerd bent: [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) .

Als u resource-Api's ontwikkelt, raden we u aan deel te nemen aan de [gedeelde signalen en gebeurtenissen WG](https://openid.net/wg/sse/). We werken samen met deze groep om het delen van beveiligings gebeurtenissen tussen micro soft-identiteiten en resource providers mogelijk te maken.

## <a name="next-steps"></a>Volgende stappen

- [Api's voor continue toegang voor evaluatie gebruiken in uw toepassingen](../develop/app-resilience-continuous-access-evaluation.md)
- [Flexibiliteit bouwen in daemon-toepassingen](resilience-daemon-app.md)
- [Maak flexibiliteit in uw infra structuur voor identiteits-en toegangs beheer](resilience-in-infrastructure.md)
- [Maak flexibiliteit in uw CIAM-systemen](resilience-b2c.md)