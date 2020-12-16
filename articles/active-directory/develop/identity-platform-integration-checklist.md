---
title: Aanbevolen procedures voor micro soft Identity platform | Azure
description: Meer informatie over aanbevolen procedures, aanbevelingen en algemene toezichts bij het integreren met het micro soft Identity-platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 47c3c091e767cac63bb814469397ef1a0aeca3a4
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516494"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Best practices en aanbevelingen voor het Microsoft-identiteitsplatform

In dit artikel worden aanbevolen procedures, aanbevelingen en algemene toezichten besproken wanneer u integreert met het micro soft Identity-platform.  Deze controle lijst leidt u naar een hoogwaardige en veilige integratie. Lees deze lijst regel matig om ervoor te zorgen dat u de kwaliteit en beveiliging behoudt van de integratie van uw app met het identiteits platform. De controle lijst is niet bedoeld om uw hele toepassing te controleren. De inhoud van de controle lijst kan worden gewijzigd terwijl er verbeteringen aan het platform worden aangebracht.

Als u net aan de slag gaat, raadpleegt u de [documentatie voor micro soft Identity platform](index.yml) voor meer informatie over de basis principes van verificatie, toepassings scenario's in het micro soft-identiteits platform en nog veel meer.

Gebruik de volgende controle lijst om ervoor te zorgen dat uw toepassing effectief wordt geïntegreerd met het [micro soft Identity-platform](./index.yml).

> [!TIP]
> De *integratie-assistent* in de Azure Portal kan u helpen bij het Toep assen van veel van deze aanbevolen procedures en aanbevelingen. Selecteer een van de [app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in de Azure Portal en selecteer vervolgens het menu-item van de **integratie-assistent** om aan de slag te gaan met de-assistent.

## <a name="basics"></a>Basisbeginselen

![in ](./media/active-directory-integration-checklist/checkbox-two.svg) het selectie vakje [wordt het micro soft-platform beleid](/legal/microsoft-identity-platform/terms-of-use)gelezen en begrepen. Zorg ervoor dat uw toepassing voldoet aan de voor waarden die worden beschreven, zoals ze zijn ontworpen om gebruikers en het platform te beveiligen.

## <a name="ownership"></a>Eigendom

![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) Zorg ervoor dat de informatie die is gekoppeld aan het account dat u hebt gebruikt voor het registreren en beheren van apps up-to-date is.

## <a name="branding"></a>Branding

![het selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) voldoet aan de [huisstijl richtlijnen voor toepassingen](howto-add-branding-in-azure-ad-apps.md).

![het selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) bevat een duidelijke naam en logo voor uw toepassing. Deze informatie wordt weer gegeven op de [toestemming prompt van uw toepassing](application-consent-experience.md). Zorg ervoor dat uw naam en logo representatief zijn voor uw bedrijf/product zodat gebruikers weloverwogen beslissingen kunnen nemen. Zorg ervoor dat u geen enkele handels merk hebt geschonden.

## <a name="privacy"></a>Privacy

![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) Geef koppelingen op met de service voorwaarden en privacyverklaring van uw app.

## <a name="security"></a>Beveiliging

![selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) omleidings-uri's beheren: <ul><li>Behoud het eigendom van alle omleidings-Uri's en behoud de DNS-records voor deze.</li><li>Gebruik geen joker tekens (*) in uw Uri's.</li><li>Zorg ervoor dat alle Uri's beveiligd en versleuteld zijn (bijvoorbeeld met behulp van HTTPS-schema's) voor web-apps.</li><li>Gebruik voor open bare clients platformspecifieke omleidings-Uri's, indien van toepassing (voornamelijk voor iOS en Android). Gebruik anders omleidings-Uri's met een grote hoeveelheid wille keurigheid om conflicten te voor komen bij het terugbellen naar uw app.</li><li>Als uw app wordt gebruikt vanuit een geïsoleerde webagent, kunt u gebruiken `https://login.microsoftonline.com/common/oauth2/nativeclient` .</li><li>Alle ongebruikte of overbodige omleidings-Uri's regel matig controleren en bijsnijden.</li></ul>

![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) Als uw app is geregistreerd in een directory, kunt u de lijst met app-registratie-eigen aren minimaliseren en hand matig bewaken.

![het selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) biedt geen ondersteuning voor de [impliciete toekennings stroom OAuth2](v2-oauth2-implicit-grant-flow.md) , tenzij expliciet vereist. Meer informatie over het geldige [scenario.](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)

![selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) verplaatsen buiten gebruikers naam/wacht woord. Gebruik geen [wacht woord](v2-oauth-ropc.md)voor de ROPC van de resource-eigenaar, waarmee de wacht woorden van gebruikers rechtstreeks worden verwerkt. Deze stroom vereist een hoge mate van vertrouwen en gebruikers belichting en mag alleen worden gebruikt als andere, veiliger, stromen niet kunnen worden gebruikt. Deze stroom is nog steeds nodig in sommige scenario's (zoals DevOps), maar houd er wel rekening mee dat het gebruik ervan beperkingen oplegt aan uw toepassing.  Lees [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md)voor meer moderne benaderingen.

![selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) beveiligen en beheren van uw vertrouwelijke app-referenties voor web-apps, Web-api's en daemon-apps. [Certificaat referenties](active-directory-certificate-credentials.md)gebruiken, niet wachtwoord referenties (client geheimen). Als u een wachtwoord referentie moet gebruiken, hoeft u deze niet hand matig in te stellen. Sla geen referenties op in code of config en laat deze nooit door de mens worden verwerkt. Gebruik, indien mogelijk, [beheerde identiteiten voor Azure-resources](../managed-identities-azure-resources/overview.md) of [Azure Key Vault](../../key-vault/general/basic-concepts.md) om uw referenties op te slaan en regel matig te draaien.

![selectie vakje Controleer ](./media/active-directory-integration-checklist/checkbox-two.svg) of uw toepassing de machtigingen voor de minste bevoegdheden aanvraagt. Vraag alleen om machtigingen die uw toepassing absoluut nodig heeft en alleen wanneer u ze nodig hebt. Meer informatie over de verschillende [soorten machtigingen](v2-permissions-and-consent.md#permission-types). Gebruik indien nodig alleen toepassings machtigingen. Gebruik waar mogelijk gedelegeerde machtigingen. Zie deze [Naslag informatie voor machtigingen](/graph/permissions-reference)voor een volledige lijst met Microsoft Graph machtigingen.

![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) Als u een API wilt beveiligen met behulp van het micro soft-identiteits platform, kunt u de machtigingen die ze moeten openbaren zorgvuldig door lopen. Bedenk wat de juiste granulatie voor uw oplossing is en welke machtiging (en) toestemming van de beheerder nodig heeft. Controleer op de verwachte machtigingen in de inkomende tokens voordat u autorisatie beslissingen neemt.

## <a name="implementation"></a>Implementatie

![selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) gebruik moderne verificatie oplossingen (OAuth 2,0, [OpenID Connect Connect](v2-protocols-oidc.md)) om gebruikers veilig aan te melden.

![selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) niet rechtstreeks Program meren op protocollen zoals OAuth 2,0 en open id. Maak in plaats daarvan gebruik van de [micro soft Authentication Library (MSAL)](msal-overview.md). De MSAL-bibliotheken beveiligen beveiligings protocollen veilig in een gemakkelijk te gebruiken bibliotheek en u krijgt ingebouwde ondersteuning voor scenario's voor [voorwaardelijke toegang](../conditional-access/overview.md) , [SSO (single sign-on)](../manage-apps/what-is-single-sign-on.md)voor het apparaat en ingebouwde ondersteuning voor het opslaan van tokens. Zie de lijst met door micro soft ondersteunde [client bibliotheken](reference-v2-libraries.md#microsoft-supported-client-libraries) en middleware- [bibliotheken](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) en de lijst met [compatibele client bibliotheken](reference-v2-libraries.md#compatible-client-libraries)van derden voor meer informatie.<br/><br/>Als u code nodig hebt voor de verificatie protocollen, moet u een methodologie zoals [micro soft sdl](https://www.microsoft.com/sdl/default.aspx)volgen. Let op de beveiligings overwegingen in de standaard specificaties van elk protocol.

![selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) Migreer bestaande apps van [Azure Active Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) naar [micro soft-verificatie bibliotheek](msal-overview.md). MSAL is de nieuwste identiteits platform oplossing van micro soft en verdient de voor keur aan ADAL. Het is beschikbaar op .NET, java script, Android, iOS, macOS en is ook in open bare Preview voor python en Java. Meer informatie over het migreren van [ADAL.net](msal-net-migration.md)-, [ADAL.js](msal-compare-msal-js-and-adal-js.md)-en [ADAL.net-en IOS Broker](msal-net-migration-ios-broker.md) -apps.

![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) Configureer voor mobiele apps elk platform met de ervaring voor het registreren van toepassingen. Om ervoor te zorgen dat uw toepassing kan profiteren van de Microsoft Authenticator of micro soft Bedrijfsportal voor eenmalige aanmelding, moet uw app een ' Broker omleidings-URI ' hebben geconfigureerd. Hiermee kan micro soft de controle over de toepassing terugsturen na verificatie. Bij het configureren van elk platform helpt de app registratie-ervaring u door het proces. Gebruik de Snelstartgids om een werkend voor beeld te downloaden. Gebruik op iOS, indien mogelijk, brokers en systeem-webweergave.

![In het selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) in web apps of Web-api's, een token cache per account blijven.  Voor web-apps moet de token cache worden gesleuteld met de account-ID.  Voor web-Api's moet het account worden gefilterd op de hash van het token dat wordt gebruikt om de API aan te roepen. MSAL.NET biedt aangepaste token cache-serialisatie in de subplatformen .NET Framework en .NET core. Uit veiligheids overwegingen is het aan te raden één cache per gebruiker te serialiseren. Meer informatie over de [serialisatie van token cache](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)vindt u hier.

![selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) als de gegevens die uw app nodig heeft, beschikbaar zijn via [Microsoft Graph](https://developer.microsoft.com/graph), kunt u machtigingen voor deze gegevens aanvragen met behulp van het Microsoft Graph-eind punt in plaats van de afzonderlijke API.

![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) Bekijk de toegangs token waarde niet of probeer deze te parseren als een client.  Ze kunnen waarden wijzigen, indelingen of zelfs worden versleuteld zonder waarschuwing: gebruik altijd de id_token als uw client iets moet weten over de gebruiker of Microsoft Graph kan worden aangeroepen.  Alleen web-Api's moeten toegangs tokens parseren (omdat ze de indelingen definiëren en de versleutelings sleutels instellen).

## <a name="end-user-experience"></a>Ervaring voor de eindgebruiker

![in het selectie vakje wordt ](./media/active-directory-integration-checklist/checkbox-two.svg) [de toestemming van de](application-consent-experience.md) hand leiding begrepen en worden de onderdelen van de toestemming prompt van uw app geconfigureerd zodat eind gebruikers en beheerders voldoende informatie hebben om te bepalen of ze uw app vertrouwen.

![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) Beperk het aantal keren dat een gebruiker aanmeldings referenties moet invoeren terwijl uw app wordt gebruikt door een stille verificatie (Silent token Acquisition) voor interactieve stromen uit te voeren.

![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) Gebruik ' prompt = toestemming ' niet voor elke aanmelding. Gebruik prompt = toestemming als u hebt vastgesteld dat u moet worden gevraagd om toestemming te geven voor aanvullende machtigingen (bijvoorbeeld als u de vereiste machtigingen van uw app hebt gewijzigd).

![](./media/active-directory-integration-checklist/checkbox-two.svg)indien van toepassing, verrijkt u uw toepassing met gebruikers gegevens. Het gebruik van de [Microsoft Graph-API](https://developer.microsoft.com/graph) is een eenvoudige manier om dit te doen. Het hulp programma [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) waarmee u aan de slag kunt gaan.

![Als u het selectie vakje inschakelt ](./media/active-directory-integration-checklist/checkbox-two.svg) , wordt de volledige set machtigingen geregistreerd die uw app nodig heeft, zodat beheerders eenvoudig toestemming kunnen verlenen aan hun Tenant. Gebruik [incrementele toestemming](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) op het moment van uitvoering om gebruikers te helpen begrijpen waarom uw app machtigingen aanvraagt of gebruikers Verwar ring wanneer deze worden aangevraagd bij de eerste keer starten.

![selectie vakje ](./media/active-directory-integration-checklist/checkbox-two.svg) implementeren van een schone eenmalige [afmeldings ervaring](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Het is een privacy-en beveiligings vereiste, en zorgt voor een goede gebruikers ervaring.

## <a name="testing"></a>Testen

![de selectie vakjes ](./media/active-directory-integration-checklist/checkbox-two.svg) testen voor [beleids regels voor voorwaardelijke toegang](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) die van invloed kunnen zijn op de mogelijkheid van gebruikers om uw toepassing te gebruiken.

![selectievakje](./media/active-directory-integration-checklist/checkbox-two.svg) Test uw toepassing met alle mogelijke accounts die u wilt ondersteunen (bijvoorbeeld werk-of school accounts, persoonlijke micro soft-accounts, onderliggende accounts en soevereine accounts).

## <a name="additional-resources"></a>Aanvullende bronnen

Gedetailleerde informatie over v2.0 verkennen:

* [Overzicht van micro soft Identity platform (v 2.0)](v2-overview.md)
* [Naslag informatie over micro soft Identity platform-protocollen](active-directory-v2-protocols.md)
* [Naslaginformatie voor Access-tokens](access-tokens.md)
* [Naslaginformatie voor id-tokens](id-tokens.md)
* [Naslag informatie over verificatie bibliotheken](reference-v2-libraries.md)
* [Machtigingen en toestemming in het micro soft Identity-platform](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
