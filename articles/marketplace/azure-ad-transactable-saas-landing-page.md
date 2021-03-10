---
title: Bouw de landings pagina voor uw voor transactable SaaS-aanbieding in de commerciële Marketplace
description: Meer informatie over het bouwen van een landings pagina voor uw voor transactable SaaS-aanbieding.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 4bfc29472373a53bcebb2ba59134d1f3702d4793
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549869"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>Bouw de landings pagina voor uw voor transactable SaaS-aanbieding in de commerciële Marketplace

Dit artikel begeleidt u bij het bouwen van een landings pagina voor een transactable SaaS-app die wordt verkocht op de micro soft Commercial Marketplace.

## <a name="overview"></a>Overzicht

U kunt de landings pagina beschouwen als de ' lobby ' voor uw SaaS-aanbieding (Software as a Service). Nadat de koper zich op een aanbieding heeft geabonneerd, stuurt de commerciële Marketplace deze door naar de landings pagina om het abonnement te activeren en te configureren voor uw SaaS-toepassing. U kunt dit beschouwen als een bevestiging van een bestelling waarmee de koper kan zien wat ze hebben gekocht en de account gegevens hebben bevestigd. Met behulp van Azure Active Directory (Azure AD) en Microsoft Graph kunt u eenmalige aanmelding (SSO) voor de koper inschakelen en belang rijke informatie over de koper ontvangen waarmee u het abonnement, inclusief de naam, het e-mail adres en de organisatie.

Omdat de informatie die nodig is om het abonnement te activeren beperkt is en wordt geleverd door Azure AD en Microsoft Graph, hoeft u geen informatie te vragen die meer nodig is dan basis toestemming. Als u gebruikers gegevens nodig hebt waarvoor extra toestemming voor uw toepassing is vereist, moet u deze informatie aanvragen nadat de activering van het abonnement is voltooid. Op deze manier wordt het activerings proces voor de koper gereduceerd en vermindert het risico van het onbreken.

De landings pagina bevat doorgaans het volgende:

- Geef de naam op van de aanbieding en het aangeschafte abonnement, evenals de facturerings voorwaarden.
- De account gegevens van de koper presen teren, inclusief de voor-en achternaam, de organisatie en het e-mail adres.
- Vraag de koper om de details van een ander account te bevestigen of te vervangen.
- Richt de koper bij de volgende stappen na de activering. U kunt bijvoorbeeld een welkomst-e-mail ontvangen, het abonnement beheren, ondersteuning verkrijgen of de documentatie lezen.

> [!NOTE]
> De koper wordt ook omgeleid naar de landings pagina bij het beheer van hun abonnement na activering. Nadat het abonnement van de koper is geactiveerd, moet u SSO gebruiken om de gebruiker in staat te stellen zich aan te melden. U wordt aangeraden de gebruiker te verwijzen naar een account profiel of configuratie pagina.

De volgende secties leiden u door het proces van het bouwen van een landings pagina:

1. [Maak een Azure AD-App-registratie](#create-an-azure-ad-app-registration) voor de landings pagina.
1. [Gebruik een code voorbeeld als uitgangs punt](#use-a-code-sample-as-a-starting-point) voor uw app.
1. [Gebruik twee Azure AD-apps om de beveiliging in productie te verbeteren](#use-two-azure-ad-apps-to-improve-security-in-production).
1. [Los het Marketplace-aankoop identificatie token](#resolve-the-marketplace-purchase-identification-token) op dat is toegevoegd aan de URL door de commerciële Marketplace.
1. [Lees de informatie van claims die zijn gecodeerd in het ID-token](#read-information-from-claims-encoded-in-the-id-token)dat is ontvangen van Azure AD nadat u zich hebt aangemeld en dat is verzonden met de aanvraag.
1. [Gebruik de Microsoft Graph-API](#use-the-microsoft-graph-api) om indien nodig aanvullende informatie te verzamelen.

## <a name="create-an-azure-ad-app-registration"></a>Een Azure AD-App-registratie maken

De commerciële Marketplace is volledig geïntegreerd met Azure AD. Kopers ontvangen op de Marketplace die is geverifieerd met een [Azure ad-account of Microsoft-account (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Na de aankoop gaat de koper van de commerciële Marketplace naar de URL van de landings pagina om het abonnement van uw SaaS-toepassing te activeren en te beheren. U moet ervoor zorgen dat de koper zich bij uw toepassing aanmeldt met Azure AD SSO. (De URL van de landings pagina is opgegeven op de pagina [technische configuratie](plan-saas-offer.md#technical-information) van de aanbieding.

De eerste stap bij het gebruik van de identiteit is ervoor te zorgen dat uw landings pagina is geregistreerd als een Azure AD-toepassing. Door de toepassing te registreren, kunt u Azure AD gebruiken voor het verifiëren van gebruikers en het aanvragen van toegang tot gebruikers resources. Het kan worden beschouwd als de definitie van de toepassing, zodat de service weet hoe tokens aan de app kunnen worden verstrekt op basis van de instellingen van de app.

### <a name="register-a-new-application-using-the-azure-portal"></a>Een nieuwe toepassing registreren via de Azure Portal

Volg de instructies voor het [registreren van een nieuwe toepassing](../active-directory/develop/quickstart-register-app.md)om aan de slag te gaan. Als u gebruikers van andere bedrijven de app wilt laten bezoeken, moet u een van de multi tenant-opties kiezen wanneer u wordt gevraagd wie de toepassing mag gebruiken.

Als u van plan bent om de Microsoft Graph-API te doorzoeken, [moet u uw nieuwe toepassing configureren voor toegang tot Web-api's](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Wanneer u de API-machtigingen voor deze toepassing selecteert, is de standaard instelling **gebruiker. Read** voldoende voor het verzamelen van basis informatie over de koper om het voorbereidings proces soepel en automatisch uit te voeren. U kunt geen API-machtigingen aanvragen waaraan **beheerders toestemming** wordt gevraagd, omdat alle niet-beheerders gebruikers de landings pagina niet kunnen bezoeken.

Als u verhoogde machtigingen nodig hebt als onderdeel van het voorbereidings-of inrichtings proces, kunt u overwegen om de functionaliteit voor [incrementele toestemming](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) van Azure ad te gebruiken, zodat alle kopers die vanaf de Marketplace worden verzonden, in eerste instantie met de landings pagina kunnen communiceren.

## <a name="use-a-code-sample-as-a-starting-point"></a>Een voor beeld van een code gebruiken als uitgangs punt

Er zijn verschillende voor beelden van apps die een eenvoudige website implementeren waarvoor Azure AD-aanmelding is ingeschakeld. Nadat uw toepassing is geregistreerd in azure AD, biedt de Blade **Quick** start een lijst met algemene toepassings typen en-ontwikkelings stacks, zoals wordt weer gegeven in afbeelding 1. Kies het abonnement dat overeenkomt met uw omgeving en volg de instructies voor downloaden en instellen.

***Afbeelding 1: de Blade Quick Start in de Azure Portal***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Illustreert de Blade Quick Start in de Azure Portal.":::

Nadat u de code hebt gedownload en uw ontwikkel omgeving hebt ingesteld, wijzigt u de configuratie-instellingen in de app zodat deze overeenkomen met de toepassings-ID, Tenant-ID en het client geheim dat u in de vorige procedure hebt vastgelegd. Houd er rekening mee dat de exacte stappen verschillen, afhankelijk van het voor beeld dat u gebruikt.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>Gebruik twee Azure AD-apps om de beveiliging in productie te verbeteren

Dit artikel biedt een vereenvoudigde versie van de architectuur voor het implementeren van een landings pagina voor uw SaaS-aanbieding voor uw commerciële markt plaats. Wanneer u de pagina in productie uitvoert, raden we u aan de beveiliging te verbeteren door te communiceren met de SaaS-fulfillment-Api's, alleen via een andere, beveiligde toepassing. Hiervoor moet u twee nieuwe toepassingen maken:

- Eerst wordt de multi tenant-toepassing voor de landings pagina beschreven tot en met dit punt, behalve zonder de functionaliteit om contact op te nemen met de SaaS-fulfillment-Api's. Deze functionaliteit wordt geoffload naar een andere toepassing, zoals hieronder wordt beschreven.
- Ten tweede, een toepassing voor de communicatie met de SaaS-fulfillment-Api's. Deze toepassing moet één Tenant zijn, alleen voor gebruik door uw organisatie en er kan een toegangs beheer lijst worden ingesteld om de toegang tot de Api's alleen van deze app te beperken.

Hierdoor kan de oplossing worden gebruikt in scenario's die het beginsel [van de schei ding van](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) het probleem observeren. De landings pagina gebruikt bijvoorbeeld de eerste geregistreerde Azure AD-app om u aan te melden bij de gebruiker. Nadat de gebruiker is aangemeld, gebruikt de landings pagina de tweede Azure AD om een toegangs token aan te vragen voor het aanroepen van de API voor SaaS-uitvoering en het aanroepen van de bewerking voor het oplossen.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>De Marketplace-aankoop identificatie token oplossen

Wanneer de koper naar de landings pagina wordt verzonden, wordt een token toegevoegd aan de URL-para meter. Dit token wijkt af van het door Azure AD uitgegeven token en het toegangs token dat wordt gebruikt voor service-naar-service-authenticatie en wordt gebruikt als invoer voor de Api's van de [SaaS-fulfillment](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) -aanroep om de details van het abonnement op te halen. Net als bij alle aanroepen van de SaaS-fulfillment-Api's wordt uw service-naar-service-aanvraag geverifieerd met een toegangs token dat is gebaseerd op de Azure AD-toepassings-ID van de app voor service-naar-service-authenticatie.

> [!NOTE]
> In de meeste gevallen is het raadzaam om deze aanroep van een tweede, single-tenant toepassing te maken. Zie [twee Azure AD-apps gebruiken om de beveiliging in de productie](#use-two-azure-ad-apps-to-improve-security-in-production) eerder in dit artikel te verbeteren.

### <a name="request-an-access-token"></a>Een toegangstoken aanvragen

Als u uw toepassing wilt verifiëren met de SaaS-fulfillment-Api's, hebt u een toegangs token nodig dat kan worden gegenereerd door het Azure AD OAuth-eind punt aan te roepen. Zie [het autorisatie token van de uitgever ophalen](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

### <a name="call-the-resolve-endpoint"></a>Het oplossen van het eind punt aanroepen

De SaaS-fulfillment-Api's implementeren het [omzettings](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) eindpunt dat kan worden aangeroepen om de geldigheid van het Marketplace-token te bevestigen en om informatie over het abonnement te retour neren.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Lees de informatie van claims die zijn gecodeerd in het ID-token

Als onderdeel van de [OpenID Connect Connect](../active-directory/develop/v2-protocols-oidc.md) flow voegt Azure AD een [id-token](../active-directory/develop/id-tokens.md) toe aan de aanvraag wanneer de koper naar de landings pagina wordt verzonden. Dit token bevat meerdere basis informatie die nuttig kan zijn bij het activerings proces, met inbegrip van de informatie die in deze tabel wordt weer gegeven.

| Waarde | Beschrijving |
| ------------ | ------------- |
| aud | Beoogde doel groep voor dit token. In dit geval moet deze overeenkomen met uw toepassings-ID en worden gevalideerd. |
| preferred_username | Primaire gebruikers naam van de bezochte gebruiker. Dit kan een e-mail adres, telefoon nummer of andere id zijn. |
| e-mail | E-mail adres van de gebruiker. Houd er rekening mee dat dit veld mogelijk leeg is. |
| naam | Lees bare waarde waarmee het onderwerp van het token wordt geïdentificeerd. In dit geval is de naam van de koper. |
| nogmaals | Id in het micro soft-identiteits systeem waarmee de gebruiker in verschillende toepassingen kan worden geïdentificeerd. Microsoft Graph wordt deze waarde geretourneerd als de eigenschap ID voor een gegeven gebruikers account. |
| TID | De id die de Azure AD-Tenant vertegenwoordigt waarvan de koper afkomstig is. In het geval van een MSA-identiteit is dit altijd ``9188040d-6c67-4c5b-b112-36a304b66dad`` . Zie de opmerking in de volgende sectie: de Microsoft Graph-API gebruiken voor meer informatie. |
| sub | De unieke id van de gebruiker in deze specifieke toepassing. |
|||

## <a name="use-the-microsoft-graph-api"></a>De Microsoft Graph API gebruiken

Het ID-token bevat basis informatie voor het identificeren van de koper, maar uw activerings proces vereist mogelijk aanvullende gegevens, zoals het bedrijf van de koper, om het voorbereidings proces te volt ooien. Gebruik de [Microsoft Graph-API](/graph/use-the-api) om deze informatie aan te vragen om te voor komen dat de gebruiker deze gegevens opnieuw invoert. De standaard **gebruiker. Lees** machtigingen zijn standaard de volgende informatie.

| Waarde | Beschrijving |
| ------------ | ------------- |
| displayName | De naam die wordt weer gegeven in het adres boek voor de gebruiker. |
| givenName | De voor naam van de gebruiker. |
| jobTitle | De functie van de gebruiker. |
| mail | SMTP-adres voor de gebruiker. |
| mobilePhone | Het primaire mobiele telefoon nummer voor de gebruiker. |
| preferredLanguage | ISO 639-1-code voor de voorkeurs taal van de gebruiker. |
| surname | De achternaam van de gebruiker. |
|||

Aanvullende eigenschappen, zoals de naam van het bedrijf van de gebruiker of de locatie van de gebruiker (land), kunnen worden geselecteerd voor opname in de aanvraag. Zie [Eigenschappen voor het resource type van de gebruiker](/graph/api/resources/user#properties) voor meer informatie.

De meeste apps die zijn geregistreerd bij Azure AD, hebben gedelegeerde machtigingen voor het lezen van de gegevens van de gebruiker uit de Azure AD-Tenant van hun bedrijf. Elke aanvraag voor het Microsoft Graph van die informatie moet vergezeld gaan van een toegangs token voor verificatie. Specifieke stappen voor het genereren van het toegangs token zijn afhankelijk van de technologie stack die u gebruikt, maar de voorbeeld code bevat een voor beeld. Zie [toegang namens een gebruiker verkrijgen](/graph/auth-v2-user)voor meer informatie.

> [!NOTE]
> Accounts van de MSA-Tenant (met Tenant-ID ``9188040d-6c67-4c5b-b112-36a304b66dad`` ) retour neren niet meer informatie dan al is verzameld met het ID-token. U kunt deze aanroep overs laan voor de Graph API voor deze accounts.

## <a name="next-steps"></a>Volgende stappen

- [Een SaaS-aanbieding maken in de commerciële Marketplace](create-new-saas-offer.md)