---
title: Verificatie en autorisatie
description: Meer informatie over de ingebouwde ondersteuning voor verificatie en autorisatie in Azure App Service en Azure Functions en hoe deze uw app kan beveiligen tegen onbevoegde toegang.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 03/29/2021
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 1b6e600fcaf32a115af14be2444144fee099d635
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075335"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Verificatie en autorisatie in Azure App Service en Azure Functions

Azure App Service biedt ingebouwde verificatie-en autorisatie mogelijkheden (ook wel ' eenvoudige verificatie ' genoemd), zodat u gebruikers kunt aanmelden en toegang hebt tot gegevens door Mini maal of geen code in uw web-app, een resterende API en een mobiele back-end te schrijven en ook [Azure functions](../azure-functions/functions-overview.md). In dit artikel wordt beschreven hoe App Service de verificatie en autorisatie voor uw app vereenvoudigt.

## <a name="why-use-the-built-in-authentication"></a>Waarom de ingebouwde verificatie gebruiken?

U hoeft deze functie niet te gebruiken voor verificatie en autorisatie. U kunt gebruikmaken van de gebundelde beveiligings functies in uw eigen keuze of u kunt uw eigen hulpprogram ma's schrijven. U moet er echter voor zorgen dat uw oplossing up-to-date blijft met de nieuwste updates voor beveiliging, het protocol en de browser.

Het implementeren van een veilige oplossing voor verificatie (gebruikers aanmelden) en autorisatie (waardoor toegang tot beveiligde gegevens wordt geboden) kan aanzienlijk duren. Zorg ervoor dat u de best practices en standaarden van de branche volgt en uw implementatie up-to-date blijft. Met de ingebouwde verificatie functie voor App Service en Azure Functions kunt u tijd en moeite besparen door out-of-the-box-verificatie met federatieve id-providers te bieden, zodat u zich kunt concentreren op de rest van uw toepassing.

- Met Azure App Service kunt u diverse verificatie mogelijkheden integreren in uw web-app of API zonder ze zelf te implementeren.
- Het is rechtstreeks gebouwd op het platform en vereist geen specifieke taal, SDK, beveiligings expertise of zelfs code die u kunt gebruiken.
- U kunt integreren met meerdere aanmeldings providers. Bijvoorbeeld: Azure AD, Facebook, Google, Twitter.

## <a name="identity-providers"></a>Id-providers

App Service maakt gebruik van [federatieve identiteiten](https://en.wikipedia.org/wiki/Federated_identity), waarin een id-provider van derden de gebruikers identiteiten en de verificatie stroom voor u beheert. De volgende id-providers zijn standaard beschikbaar:

| Provider | Aanmeldings eindpunt | How-To richtlijnen |
| - | - | - |
| [Micro soft Identity-platform](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [Aanmelden bij micro soft Identity platform App Service](configure-authentication-provider-aad.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [App Service Facebook-aanmelding](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [Google-aanmelding App Service](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [App Service Twitter-aanmelding](configure-authentication-provider-twitter.md) |
| Elke [OpenID Connect Connect](https://openid.net/connect/) provider (preview-versie) | `/.auth/login/<providerName>` | [App Service OpenID Connect Connect-aanmelding](configure-authentication-provider-openid-connect.md) |

Wanneer u verificatie en autorisatie met een van deze providers inschakelt, is het aanmeldings eindpunt beschikbaar voor gebruikers verificatie en voor validatie van verificatie tokens van de provider. U kunt uw gebruikers een wille keurig aantal van deze aanmeldings opties geven.

## <a name="considerations-for-using-built-in-authentication"></a>Overwegingen voor het gebruik van ingebouwde verificatie

Als u deze functie inschakelt, worden alle aanvragen naar uw toepassing automatisch omgeleid naar HTTPS, ongeacht de App Service configuratie-instelling voor het afdwingen van HTTPS. U kunt dit uitschakelen met de  `requireHttps` instelling in de v2-configuratie. We raden u echter aan om met HTTPS te blijven werken en u moet ervoor zorgen dat er geen beveiligings tokens meer worden verzonden via niet-beveiligde HTTP-verbindingen.

App Service kunnen worden gebruikt voor verificatie met of zonder het beperken van de toegang tot de inhoud en Api's van uw site. Als u app-toegang alleen wilt beperken voor geverifieerde gebruikers, stelt **u een actie in die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd** om u aan te melden met een van de geconfigureerde id-providers. Als u de toegang wilt verifiëren maar niet wilt beperken, stelt **u de actie in wanneer de aanvraag niet is geverifieerd** voor anonieme aanvragen toestaan (geen actie).

> [!NOTE]
> U moet elke app een eigen machtiging en toestemming geven. Vermijd het delen van machtigingen tussen omgevingen door afzonderlijke app-registraties voor afzonderlijke implementatie sleuven te gebruiken. Bij het testen van nieuwe code kunt u deze procedure gebruiken om te voor komen dat problemen van invloed zijn op de productie-app.

## <a name="how-it-works"></a>Uitleg

[Functie architectuur in Windows (niet-container implementatie)](#feature-architecture-on-windows-non-container-deployment)

[Functie architectuur in Linux en containers](#feature-architecture-on-linux-and-containers)

[Verificatie stroom](#authentication-flow)

[Autorisatie gedrag](#authorization-behavior)

[Gebruikers-en toepassings claims](#user-and-application-claims)

[Tokenarchief](#token-store)

[Logboek registratie en tracering](#logging-and-tracing)

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Functie architectuur in Windows (niet-container implementatie)

De module authenticatie en autorisatie wordt uitgevoerd in dezelfde sandbox als de code van uw toepassing. Wanneer deze is ingeschakeld, stuurt elke binnenkomende HTTP-aanvraag deze door voordat deze wordt verwerkt door de code van uw toepassing.

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="Een architectuur diagram met de aanvragen die worden onderschept door een proces in de sandbox van de site die communiceert met id-providers voordat verkeer naar de geïmplementeerde site wordt toegestaan" lightbox="media/app-service-authentication-overview/architecture.png":::

Deze module verwerkt diverse dingen voor uw app:

- Verifieert gebruikers met de opgegeven provider
- Valideert, slaat en vernieuwt tokens
- Beheert de geverifieerde sessie
- Injecteert identiteits gegevens in aanvraag headers

De module wordt afzonderlijk van uw toepassings code uitgevoerd en wordt geconfigureerd met behulp van app-instellingen. Er zijn geen Sdk's, specifieke talen of wijzigingen in de toepassings code vereist. 

#### <a name="feature-architecture-on-linux-and-containers"></a>Functie architectuur in Linux en containers

De module verificatie en autorisatie wordt uitgevoerd in een afzonderlijke container, geïsoleerd van uw toepassings code. Met behulp van wat het [ambassadeur-patroon](/azure/architecture/patterns/ambassador)wordt genoemd, communiceert het met het binnenkomende verkeer om Vergelijk bare functionaliteit uit te voeren als in Windows. Omdat de service niet in-process wordt uitgevoerd, is geen directe integratie met specifieke taal raamwerken mogelijk. de relevante informatie die uw app nodig heeft, wordt echter door gegeven met behulp van aanvraag headers, zoals hieronder wordt uitgelegd.

#### <a name="authentication-flow"></a>Verificatiestroom

De verificatie stroom is hetzelfde voor alle providers, maar verschilt afhankelijk van of u zich wilt aanmelden met de SDK van de provider:

- Zonder provider-SDK: de toepassing delegeert federatieve aanmelding bij App Service. Dit is meestal het geval bij browser-apps, waarmee de aanmeldings pagina van de provider kan worden weer gegeven aan de gebruiker. De server code beheert het aanmeldings proces, zodat dit ook wel _Server gerichte stroom_ of _Server stroom_ wordt genoemd. Dit geldt voor browser-apps. Dit geldt ook voor systeem eigen apps waarmee gebruikers worden ondertekend met de Mobile Apps client-SDK, omdat de SDK een webweergave opent om gebruikers met App Service-verificatie te ondertekenen.
- Met de provider-SDK: de toepassing ondertekent gebruikers hand matig aan de provider en stuurt het verificatie token naar App Service voor validatie. Dit is meestal het geval bij apps zonder browser, waarmee de aanmeldings pagina van de provider niet kan worden weer gegeven aan de gebruiker. De toepassings code beheert het aanmeldings proces, dus ook wel _client gerichte stroom_ of _client stroom_. Dit is van toepassing op REST Api's, [Azure functions](../azure-functions/functions-overview.md)en Java script-clients, evenals browser-apps die meer flexibiliteit in het aanmeldings proces hebben. Dit geldt ook voor systeem eigen mobiele apps waarmee gebruikers worden aangemeld met de SDK van de provider.

Aanroepen van een vertrouwde browser-app in App Service naar een andere REST API in App Service of [Azure functions](../azure-functions/functions-overview.md) kan worden geverifieerd met behulp van de server gerichte stroom. Zie voor meer informatie [verificatie en autorisatie aanpassen in app service](app-service-authentication-how-to.md).

In de volgende tabel ziet u de stappen van de verificatie stroom.

| Stap | Zonder provider-SDK | Met provider-SDK |
| - | - | - |
| 1. gebruiker ondertekenen in | Stuurt de client door naar `/.auth/login/<provider>` . | Client code tekent de gebruiker rechtstreeks met de SDK van de provider en ontvangt een verificatie token. Zie de documentatie van de provider voor meer informatie. |
| 2. na verificatie | Provider stuurt de client om naar `/.auth/login/<provider>/callback` . | Client code [boekt token van provider](app-service-authentication-how-to.md#validate-tokens-from-providers) naar `/.auth/login/<provider>` voor validatie. |
| 3. een geverifieerde sessie tot stand brengen | App Service voegt een geverifieerde cookie toe aan het antwoord. | App Service retourneert een eigen verificatie token naar client code. |
| 4. geauthenticeerde inhoud verwerken | Client bevat verificatie cookie in volgende aanvragen (automatisch verwerkt door browser). | Client code geeft een verificatie token weer in de `X-ZUMO-AUTH` header (automatisch verwerkt door Mobile apps client-sdk's). |

App Service kunt voor client browsers automatisch alle niet-geverifieerde gebruikers door sturen naar `/.auth/login/<provider>` . U kunt gebruikers ook voorzien van een of meer `/.auth/login/<provider>` koppelingen om u aan te melden bij uw app met behulp van de gewenste provider.

<a name="authorization"></a>

#### <a name="authorization-behavior"></a>Autorisatie gedrag

In de [Azure Portal](https://portal.azure.com)kunt u app service configureren met een aantal gedragingen wanneer de inkomende aanvraag niet is geverifieerd. In de volgende koppen worden de opties beschreven.

**Niet-geverifieerde aanvragen toestaan**

Met deze optie wordt de autorisatie van niet-geverifieerde verkeer naar uw toepassings code uitgesteld. App Service worden voor geverifieerde aanvragen ook de verificatie gegevens in de HTTP-headers door gegeven.

Deze optie biedt meer flexibiliteit bij het verwerken van anonieme aanvragen. U kunt bijvoorbeeld [meerdere aanmeldings providers](app-service-authentication-how-to.md#use-multiple-sign-in-providers) aan uw gebruikers aanbieden. U moet echter code schrijven.

**Verificatie vereisen**

Met deze optie worden niet-geverifieerde verkeer naar uw toepassing geweigerd. Deze afwijzing kan een omleidings actie zijn naar een van de geconfigureerde id-providers. In deze gevallen wordt een browser-client omgeleid naar `/.auth/login/<provider>` voor de provider die u kiest. Als de anonieme aanvraag afkomstig is van een systeem eigen mobiele app, is het geretourneerde antwoord een `HTTP 401 Unauthorized` . U kunt de weigering ook configureren als een `HTTP 401 Unauthorized` of `HTTP 403 Forbidden` voor alle aanvragen.

Met deze optie hoeft u geen verificatie code in uw app te schrijven. Nauw keurige autorisatie, zoals Role-specifieke autorisatie, kan worden verwerkt door de claims van de gebruiker te controleren (Zie [gebruikers claims voor toegang](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps die een openbaar beschik bare start pagina willen, zoals in veel toepassingen met één pagina.

> [!NOTE]
> Standaard kan elke gebruiker in uw Azure AD-Tenant een token aanvragen voor uw toepassing vanuit Azure AD. U kunt [de toepassing in azure AD configureren](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) als u de toegang tot uw app wilt beperken tot een gedefinieerde groep gebruikers.


#### <a name="user-and-application-claims"></a>Gebruikers-en toepassings claims

Voor alle taal Frameworks maakt App Service de claims in het inkomende token (of dit van een geverifieerde eind gebruiker of een client toepassing afkomstig is) die beschikbaar zijn voor uw code door ze in de aanvraag headers in te voeren. Voor ASP.NET 4,6-apps vult App Service [claimsprincipal is. current](/dotnet/api/system.security.claims.claimsprincipal.current) in met de claims van de geverifieerde gebruiker, zodat u het standaard .net-code patroon kunt volgen, inclusief het `[Authorize]` kenmerk. Op dezelfde manier wordt voor PHP-apps App Service de `_SERVER['REMOTE_USER']` variabele ingevuld. Voor java-apps zijn de claims [toegankelijk via de Tomcat-servlet](configure-language-java.md#authenticate-users-easy-auth).

Voor [Azure functions](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` is niet ingevuld voor .net-code, maar u kunt wel de gebruikers claims in de aanvraag headers vinden of het `ClaimsPrincipal` object ophalen uit de context van de aanvraag of zelfs via een bindings parameter. Zie [werken met client identiteiten](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) voor meer informatie.

Zie voor meer informatie [gebruikers claims voor toegang](app-service-authentication-how-to.md#access-user-claims).

Op dit moment biedt ASP.NET Core momenteel geen ondersteuning voor het vullen van de huidige gebruiker met de functie voor verificatie/autorisatie. Het is echter wel mogelijk dat [middleware-onderdelen van derden worden geopend](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) om deze ruimte te vullen.

#### <a name="token-store"></a>Tokenarchief

App Service biedt een ingebouwde token opslag. Dit is een opslag plaats van tokens die zijn gekoppeld aan de gebruikers van uw web-apps, Api's of systeem eigen mobiele apps. Wanneer u verificatie met een wille keurige provider inschakelt, is deze token opslag direct beschikbaar voor uw app. Als uw toepassings code toegang moet hebben tot gegevens van deze providers namens de gebruiker, zoals:

- plaatsen op de Facebook-tijd lijn van de geverifieerde gebruiker
- de zakelijke gegevens van de gebruiker lezen met behulp van de Microsoft Graph-API

Normaal gesp roken moet u code schrijven om deze tokens in uw toepassing te verzamelen, op te slaan en te vernieuwen. Met de token opslag haalt u alleen [de tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) op wanneer u deze nodig hebt en [weet u app service deze te vernieuwen](app-service-authentication-how-to.md#refresh-identity-provider-tokens) wanneer ze ongeldig worden. 

De ID-tokens, toegangs tokens en vernieuwings tokens worden in de cache opgeslagen voor de geauthenticeerde sessie en ze zijn alleen toegankelijk voor de bijbehorende gebruiker.  

Als u niet met tokens in uw app hoeft te werken, kunt u de token opslag uitschakelen op de pagina **verificatie/autorisatie** van uw app.

#### <a name="logging-and-tracing"></a>Logboek registratie en tracering

Als u [toepassings logboeken inschakelt](troubleshoot-diagnostic-logs.md), worden de verificatie-en autorisatie traceringen rechtstreeks in uw logboek bestanden weer geven. Als er een verificatie fout wordt weer geven die niet werd verwacht, kunt u alle gegevens gemakkelijk vinden door te kijken in uw bestaande toepassings Logboeken. Als u [tracering van mislukte aanvragen](troubleshoot-diagnostic-logs.md)inschakelt, kunt u precies zien welke rol de authenticatie-en autorisatie module mogelijk heeft afgespeeld in een mislukte aanvraag. Zoek in de traceer logboeken naar een module met de naam `EasyAuthModule_32/64` .

## <a name="more-resources"></a>Meer bronnen

- [Instructies: uw App Service of Azure Functions app configureren voor het gebruik van Azure AD-aanmelding](configure-authentication-provider-aad.md)
- [Geavanceerd gebruik van verificatie en autorisatie in Azure App Service](app-service-authentication-how-to.md)

Voor beelden
- [Zelfstudie: Verificatie toevoegen aan uw web-app die wordt uitgevoerd in Azure App Service](scenario-secure-app-authentication-app-service.md)
- [Zelf studie: gebruikers met end-to-end verifiëren en autoriseren in Azure App Service (Windows of Linux)](tutorial-auth-aad.md)
- [.NET core-integratie van Azure AppService EasyAuth (derde partij)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [Azure App Service-verificatie voor het werken met .NET core (derden)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)
