---
title: Bouw de landings pagina voor uw gratis of proef SaaS-aanbieding in de commerciële Marketplace
description: Meer informatie over het maken van een landings pagina voor uw gratis of een SaaS-aanbieding.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: e7cee47e90e6484a4258ba82e47af03725c41d34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559287"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>Bouw de landings pagina voor uw gratis of proef SaaS-aanbieding in de commerciële Marketplace

Dit artikel begeleidt u bij het bouwen van een landings pagina voor een gratis of een SaaS-app die wordt verkocht op de micro soft Commercial Marketplace.

## <a name="overview"></a>Overzicht

U kunt de landings pagina beschouwen als de ' lobby ' voor uw SaaS-aanbieding (Software as a Service). Nadat de klant heeft gekozen om uw app op te halen, stuurt de commerciële Marketplace deze door naar de landings pagina om het abonnement te activeren en te configureren voor uw SaaS-toepassing. Wanneer u een SaaS-aanbieding (Software as a Service) maakt in het partner centrum, kunt u kiezen of u wilt [verkopen via micro soft](plan-saas-offer.md#listing-options). Als u uw aanbieding alleen in de micro soft Commercial Marketplace wilt aanbieden en niet via micro soft wilt verkopen, kunt u opgeven hoe potentiële klanten kunnen communiceren met de aanbieding. Wanneer u de optie **nu downloaden (gratis)** of lijst met **gratis proef versie** inschakelt, moet u een URL voor de landings pagina opgeven waarnaar de gebruiker toegang kan krijgen tot het gratis abonnement of de proef versie.

Het doel van de landings pagina is eenvoudigweg om de gebruiker te ontvangen, zodat deze de gratis proef versie of een gratis abonnement kan activeren. Met Azure Active Directory (Azure AD) en Microsoft Graph kunt u eenmalige aanmelding (SSO) voor de gebruiker inschakelen en belang rijke gegevens over de gebruiker ontvangen waarmee u hun gratis proef versie of gratis abonnement, inclusief de naam, het e-mail adres en de organisatie.

Omdat de informatie die nodig is om het abonnement te activeren beperkt is en wordt geleverd door Azure AD en Microsoft Graph, hoeft u geen informatie te vragen die meer nodig is dan basis toestemming. Als u gebruikers gegevens nodig hebt waarvoor extra toestemming voor uw toepassing is vereist, moet u deze informatie aanvragen nadat de activering van het abonnement is voltooid. Dit maakt het mogelijk dat de gebruiker zichzelf kan activeren en het risico van het onbreken vermindert.

De landings pagina bevat doorgaans de volgende informatie en vermeldings opties:

- Geef de naam en de details van de gratis proef versie of het gratis abonnement. Geef bijvoorbeeld de gebruiks limieten of de duur van een proef versie op.
- Geef de account gegevens van de gebruiker weer, inclusief de voor-en achternaam, de organisatie en het e-mail adres.
- Vraag de gebruiker om de details van een ander account te bevestigen of te vervangen.
- Begeleidt de gebruiker bij de volgende stappen na de activering. U kunt bijvoorbeeld een welkomst-e-mail ontvangen, het abonnement beheren, ondersteuning verkrijgen of de documentatie lezen.

In de volgende secties in dit artikel vindt u instructies voor het maken van een landings pagina:

1. [Maak een Azure AD-App-registratie](#create-an-azure-ad-app-registration) voor de landings pagina.
2. [Gebruik een code voorbeeld als uitgangs punt](#use-a-code-sample-as-a-starting-point) voor uw app.
3. [Lees de informatie van claims die zijn gecodeerd in het ID-token](#read-information-from-claims-encoded-in-the-id-token)dat is ontvangen van Azure AD nadat u zich hebt aangemeld en dat is verzonden met de aanvraag.
4. [Gebruik de Microsoft Graph-API](#use-the-microsoft-graph-api) om indien nodig aanvullende informatie te verzamelen.

## <a name="create-an-azure-ad-app-registration"></a>Een Azure AD-App-registratie maken

De commerciële Marketplace is volledig geïntegreerd met Azure AD. Gebruikers ontvangen op de Marketplace die is geverifieerd met een [Azure ad-account of Microsoft-account (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Nadat u een gratis of gratis proef abonnement hebt gekocht via uw aanbieding met alleen een lijst, gaat de gebruiker van de commerciële Marketplace naar de URL van de landings pagina om het abonnement te activeren en te beheren in uw SaaS-toepassing. U moet ervoor zorgen dat de gebruiker zich bij uw toepassing aanmeldt met Azure AD SSO. (De URL van de landings pagina is opgegeven op de pagina [technische configuratie](plan-saas-offer.md#technical-information) van de aanbieding.

De eerste stap bij het gebruik van de identiteit is ervoor te zorgen dat uw landings pagina is geregistreerd als een Azure AD-toepassing. Door de toepassing te registreren, kunt u Azure AD gebruiken voor het verifiëren van gebruikers en het aanvragen van toegang tot gebruikers resources. Het kan worden beschouwd als de definitie van de toepassing, zodat de service weet hoe tokens aan de app kunnen worden verstrekt op basis van de instellingen van de app.

### <a name="register-a-new-application-using-the-azure-portal"></a>Een nieuwe toepassing registreren via de Azure Portal

Volg de instructies voor het [registreren van een nieuwe toepassing](../active-directory/develop/quickstart-register-app.md)om aan de slag te gaan. Als u wilt dat gebruikers van andere bedrijven de app kunnen bezoeken, moet u **accounts kiezen in elke organisatie Directory (een Azure AD-adres lijst, multi tenant) en persoonlijke micro soft-accounts (zoals Skype of Xbox)** wanneer u wordt gevraagd wie de toepassing mag gebruiken.

Als u van plan bent om de Microsoft Graph-API te doorzoeken, [moet u uw nieuwe toepassing configureren voor toegang tot Web-api's](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Wanneer u de API-machtigingen voor deze toepassing selecteert, is de standaard instelling **gebruiker. Read** voldoende voor het verzamelen van basis informatie over de gebruiker om het voorbereidings proces soepel en automatisch uit te voeren. U kunt geen API-machtigingen aanvragen waaraan **beheerders toestemming** wordt gevraagd, omdat alle niet-beheerders gebruikers de landings pagina niet kunnen bezoeken.

Als u verhoogde machtigingen nodig hebt als onderdeel van het voorbereidings-of inrichtings proces, kunt u overwegen om de functionaliteit voor [incrementele toestemming](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) van Azure ad te gebruiken, zodat alle gebruikers die vanaf de Marketplace worden verzonden, in eerste instantie met de landings pagina kunnen communiceren.

## <a name="use-a-code-sample-as-a-starting-point"></a>Een voor beeld van een code gebruiken als uitgangs punt

Micro soft heeft verschillende voor beelden gegeven van apps die een eenvoudige website implementeren waarvoor Azure AD-aanmelding is ingeschakeld. Nadat uw toepassing is geregistreerd in azure AD, biedt de Blade **Quick** start een lijst met algemene toepassings typen en ontwikkelings Stacks (afbeelding 1). Kies het abonnement dat overeenkomt met uw omgeving en volg de instructies voor downloaden en instellen.

***Afbeelding 1: de Blade Quick Start in de Azure Portal***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Illustreert de Blade Quick Start in de Azure Portal.":::

Nadat u de code hebt gedownload en uw ontwikkel omgeving hebt ingesteld, wijzigt u de configuratie-instellingen in de app zodat deze overeenkomen met de toepassings-ID, Tenant-ID en het client geheim dat u in de vorige procedure hebt vastgelegd. Houd er rekening mee dat de exacte stappen verschillen, afhankelijk van het voor beeld dat u gebruikt.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Lees de informatie van claims die zijn gecodeerd in het ID-token

Als onderdeel van de [OpenID Connect Connect](../active-directory/develop/v2-protocols-oidc.md) flow voegt Azure AD een [id-token](../active-directory/develop/id-tokens.md) toe aan de aanvraag wanneer de gebruiker naar de landings pagina wordt verzonden. Dit token bevat meerdere basis informatie die nuttig kan zijn bij het activerings proces, met inbegrip van de informatie die in deze tabel wordt weer gegeven.

| Waarde | Beschrijving |
| ------------ | ------------- |
| aud | Beoogde doel groep voor dit token. In dit geval moet deze overeenkomen met uw toepassings-ID en worden gevalideerd. |
| preferred_username | Primaire gebruikers naam van de bezochte gebruiker. Dit kan een e-mail adres, telefoon nummer of andere id zijn. |
| e-mail | E-mail adres van de gebruiker. Houd er rekening mee dat dit veld mogelijk leeg is. |
| naam | Lees bare waarde waarmee het onderwerp van het token wordt geïdentificeerd. In dit geval is de naam van de gebruiker. |
| nogmaals | Id in het micro soft-identiteits systeem waarmee de gebruiker in verschillende toepassingen kan worden geïdentificeerd. Microsoft Graph wordt deze waarde geretourneerd als de eigenschap ID voor een gegeven gebruikers account. |
| TID | De id die de Azure AD-Tenant vertegenwoordigt waarvan de gebruiker afkomstig is. In het geval van een MSA-identiteit is dit altijd `9188040d-6c67-4c5b-b112-36a304b66dad` . Zie de opmerking in de volgende sectie: use Microsoft Graph API (Engelstalig) voor meer informatie. |
| sub | De unieke id van de gebruiker in deze specifieke toepassing. |
|||

## <a name="use-the-microsoft-graph-api"></a>De Microsoft Graph API gebruiken

Het ID-token bevat basis informatie voor het identificeren van de gebruiker, maar uw activerings proces vereist mogelijk aanvullende details, zoals het bedrijf van de gebruiker, om het voorbereidings proces te volt ooien. Gebruik de [Microsoft Graph-API](/graph/use-the-api) om deze informatie aan te vragen om te voor komen dat de gebruiker deze gegevens opnieuw invoert. De standaard **gebruiker. Lees** machtigingen zijn standaard de volgende informatie:

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

Aanvullende eigenschappen, zoals de naam van het bedrijf van de gebruiker of de locatie van de gebruiker (land), kunnen worden geselecteerd voor opname in de aanvraag. Zie [Eigenschappen voor het resource type van de gebruiker](/graph/api/resources/user#properties)voor meer informatie.

De meeste apps die zijn geregistreerd bij Azure AD, hebben gedelegeerde machtigingen voor het lezen van de gegevens van de gebruiker uit de Azure AD-Tenant van hun bedrijf. Elke aanvraag voor het Microsoft Graph van die informatie moet vergezeld gaan van een toegangs token als verificatie. Specifieke stappen voor het genereren van het toegangs token zijn afhankelijk van de technologie stack die u gebruikt, maar de voorbeeld code bevat een voor beeld. Zie [toegang namens een gebruiker verkrijgen](/graph/auth-v2-user)voor meer informatie.

> [!NOTE]
> Accounts van de MSA-Tenant (met Tenant-ID `9188040d-6c67-4c5b-b112-36a304b66dad` ) retour neren niet meer informatie dan al is verzameld met het ID-token. U kunt deze aanroep overs laan voor de Graph API voor deze accounts.

## <a name="next-steps"></a>Volgende stappen
- [Een SaaS-aanbieding maken in de commerciële Marketplace](create-new-saas-offer.md)