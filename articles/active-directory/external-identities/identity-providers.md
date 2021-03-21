---
title: Id-providers voor externe identiteiten-Azure AD
description: Informatie over het gebruik van Azure AD als uw standaard id-provider voor delen met externe gebruikers.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdef929b27c636b3908dd7a88eb93adc2382a53f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687737"
---
# <a name="identity-providers-for-external-identities"></a>Id-providers voor externe identiteiten

> [!NOTE]
> Enkele van de functies die in dit artikel worden vermeld, zijn open bare preview-functies van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Een *id-provider* maakt, onderhoudt en beheert id-gegevens en biedt tegelijkertijd verificatieservices voor toepassingen. Wanneer u uw apps en resources deelt met externe gebruikers, is Azure AD de standaard id-provider voor delen. Dit betekent dat wanneer u externe gebruikers uitnodigt die al over een Azure AD of Microsoft-account beschikken, ze automatisch kunnen aanmelden zonder verdere configuratie van uw onderdeel.

Naast Azure AD-accounts biedt externe identiteiten diverse id-providers.

- **Micro soft-accounts** (preview): gast gebruikers kunnen hun eigen persoonlijke Microsoft-account (MSA) gebruiken voor het inwisselen van uw B2B-samenwerkings uitnodigingen. Bij het instellen van een self-service voor het aanmelden van gebruikers, kunt u een [micro soft-account (preview)](microsoft-account.md) toevoegen als een van de toegestane id-providers. Er is geen aanvullende configuratie nodig om deze id-provider beschikbaar te maken voor gebruikers stromen.

- **Eenmalige e-mail wachtwoord code** (preview-versie): wanneer een uitnodiging wordt ingewisseld of toegang krijgt tot een gedeelde resource, kan een gast gebruiker een tijdelijke code aanvragen, die wordt verzonden naar hun e-mail adres. Vervolgens voeren ze deze code in om door te gaan met aanmelden. Met de functie voor eenmalige e-mail code worden B2B-gast gebruikers geverifieerd wanneer ze niet via een andere manier kunnen worden geverifieerd. Bij het instellen van een self-service voor het registreren van een gebruikers stroom kunt u een **e-mail One-Time wachtwoord code (preview)** toevoegen als een van de toegestane id-providers. Sommige instellingen zijn vereist. Zie [verificatie met eenmalige e-mail wachtwoord code](one-time-passcode.md).

- **Google**: met Google Federation kunnen externe gebruikers uitnodigingen van u inwisselen door zich aan te melden bij uw apps met hun eigen Gmail-accounts. Google Federation kan ook worden gebruikt in uw Self-service registratie gebruikers stromen. Zie [Google als id-provider toevoegen](google-federation.md).
   > [!IMPORTANT]
   > **Vanaf 4 januari 2021** wordt [ondersteuning voor WebView-aanmelding afgeschaft](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) in Google. Als u gebruikmaakt van Google-federatie of selfserviceregistratie met Gmail, moet u [de compatibiliteit van uw systeemeigen Line-of-Business-toepassingen testen](google-federation.md#deprecation-of-webview-sign-in-support).

- **Facebook**: wanneer u een app bouwt, kunt u self-service registratie configureren en Facebook-Federatie inschakelen zodat gebruikers zich kunnen aanmelden voor uw app met hun eigen Facebook-accounts. Facebook kan alleen worden gebruikt voor Self-service-aanmeld gebruikers stromen en is niet beschikbaar als aanmeldings optie wanneer gebruikers uitnodigingen van u inwisselen. Zie [Facebook toevoegen als een id-provider](facebook-federation.md).

- **Directe Federatie**: u kunt ook directe Federatie instellen met een externe ID-provider die de SAML-of WS-Fed protocollen ondersteunt. Met directe Federatie kunnen externe gebruikers uitnodigingen van u inwisselen door zich aan te melden bij uw apps met hun bestaande sociale of zakelijke accounts. Zie [direct Federatie instellen](direct-federation.md)voor meer informatie.
   > [!NOTE]
   > Directe Federatie-id-providers kunnen niet worden gebruikt in uw Self-service registratie gebruikers stromen.

## <a name="adding-social-identity-providers"></a>Sociale id-providers toevoegen

Azure AD is standaard ingeschakeld voor Self-service registratie, zodat gebruikers altijd de mogelijkheid hebben om zich aan te melden met een Azure AD-account. U kunt echter andere id-providers inschakelen, waaronder Social id-providers zoals Google of Facebook. Als u sociale id-providers wilt instellen in uw Azure AD-Tenant, maakt u een toepassing bij de ID-provider en configureert u de referenties. U ontvangt een client-of App-ID en een client-of app-geheim, dat u vervolgens kunt toevoegen aan uw Azure AD-Tenant.

Zodra u een id-provider aan uw Azure AD-Tenant hebt toegevoegd:

- Wanneer u een externe gebruiker uitnodigt voor apps of resources in uw organisatie, kan de externe gebruiker zich aanmelden met een eigen account bij die id-provider.
- Wanneer u [Aanmelden via selfservice](self-service-sign-up-overview.md) voor uw apps inschakelt, kunnen externe gebruikers zich aanmelden voor uw apps met hun eigen accounts met de id-providers die u hebt toegevoegd. Ze kunnen een keuze maken uit de opties voor sociale id-providers die u beschikbaar hebt gemaakt op de registratie pagina:

   ![Scherm afbeelding van het aanmeldings scherm met Google-en Facebook-opties](media/identity-providers/sign-in-with-social-identity.png)

Voor een optimale aanmeldings ervaring kunt u waar mogelijk met id-providers communiceren, zodat u uw uitgenodigde gasten een naadloze aanmeldings ervaring krijgt wanneer ze toegang hebben tot uw apps.  

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het toevoegen van id-providers voor aanmelding bij uw toepassingen:

- [Verificatie met eenmalige e-mail code toevoegen](one-time-passcode.md)
- [Google toevoegen](google-federation.md) als een toegestane sociale ID-provider
- [Facebook toevoegen](facebook-federation.md) als een toegestane sociale ID-provider
- [Stel directe Federatie](direct-federation.md) in met een wille keurige organisatie waarvan de ID-provider ondersteuning biedt voor het SAML 2,0-of WS-Fed-protocol. Houd er rekening mee dat directe Federatie geen optie is voor Self-service registratie gebruikers stromen.
