---
title: Integratie met micro soft Identity platform | Azure
titleSuffix: Microsoft identity platform
description: Ontdek de voor delen van het integreren van uw toepassing met micro soft Identity platform en ontvang resources voor functies zoals vereenvoudigde aanmelding, identiteits beheer, multi-factor Authentication en toegangs beheer.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: deb923a52e5d6cd5384dbf94d2249572b25b1a61
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063838"
---
# <a name="integrating-with-microsoft-identity-platform"></a>Integreren met micro soft Identity platform

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

In dit artikel vindt u informatie over de voor delen van het integreren van uw toepassing met micro soft Identity platform en het ophalen van bronnen voor integratie. Micro soft Identity platform en Azure Active Directory (AD) biedt organisaties een identiteits beheer op ondernemings niveau voor Cloud toepassingen. Micro soft Identity platform Integration geeft uw gebruikers een gestroomlijnd aanmeldings proces en helpt uw toepassing te voldoen aan het IT-beleid.

## <a name="how-to-integrate"></a>Het integreren van

Uw toepassing kan op verschillende manieren worden geïntegreerd met het micro soft Identity-platform. Profiteer van zoveel of slechts enkele van deze scenario's als geschikt voor uw toepassing.

### <a name="support-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>Ondersteuning van micro soft Identity platform als een manier om u aan te melden bij uw toepassing

**Verminder de wrijving van het probleem en verminder de ondersteunings kosten.** Door micro soft Identity platform te gebruiken om u aan te melden bij uw toepassing, hebben uw gebruikers niet meer dan een naam en wacht woord om te onthouden. Als ontwikkelaar hebt u nog een minder wacht woord om op te slaan en te beveiligen. Het is niet mogelijk om verg eten wacht woorden opnieuw in te voeren. Dit kan alleen aanzienlijk zijn. Micro soft Identity platform-bevoegdheden melden zich aan voor enkele van de populairste Cloud toepassingen van de wereld, waaronder Microsoft 365 en Microsoft Azure. Met honderden miljoenen gebruikers van miljoenen organisaties is uw gebruiker al aangemeld bij het micro soft Identity-platform. Meer informatie over het [toevoegen van ondersteuning voor micro soft Identity-platform Sign in](./authentication-vs-authorization.md).

**Vereenvoudig de registratie voor uw toepassing.**  Wanneer u zich aanmeldt voor uw toepassing, kan micro soft Identity platform essentiële informatie over een gebruiker verzenden, zodat u uw aanmeldings formulier vooraf kunt invullen of het volledig wilt elimineren. Gebruikers kunnen zich aanmelden voor uw toepassing met behulp van hun Azure AD-account via een vertrouwde instemming-ervaring die vergelijkbaar is met die van sociale media en mobiele toepassingen. Elke gebruiker kan zich registreren en aanmelden bij een toepassing die is geïntegreerd met het micro soft-identiteits platform zonder dat hiervoor een rol is vereist. Meer informatie over [het aanmelden van uw toepassing voor aanmelding bij een Azure ad-account](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Bladeren naar gebruikers, het inrichten van gebruikers beheren en de toegang tot uw toepassing controleren

**Blader naar gebruikers in de Directory.**  Gebruik de Microsoft Graph-API om gebruikers te helpen bij het zoeken en zoeken naar andere personen in hun organisatie bij het uitnodigen van anderen of het verlenen van toegang, in plaats van dat ze e-mail adressen moeten typen. Gebruikers kunnen bladeren door gebruik te maken van een vertrouwde Adresboek stijl interface, inclusief het weer geven van de details van de organisatie hiërarchie. Meer informatie over de [Microsoft Graph-API](/graph/overview).

**Active Directory groepen en distributie lijsten opnieuw gebruiken die uw klant al beheert.**  Azure AD bevat de groepen die uw klant al gebruikt voor het distribueren van e-mail en het beheren van de toegang. Gebruik de Microsoft Graph-API om deze groepen opnieuw te gebruiken in plaats van uw klant te verplichten om een afzonderlijke set groepen in uw toepassing te maken en te beheren. Groeps informatie kan ook worden verzonden naar uw toepassing in aanmeldings tokens. Meer informatie over de [Microsoft Graph-API](/graph/overview).

**Gebruik het micro soft Identity-platform om te bepalen wie toegang heeft tot uw toepassing.**  Beheerders en eigen aren van toepassingen in azure AD kunnen toegang tot toepassingen toewijzen aan specifieke gebruikers en groepen. Met de Microsoft Graph-API kunt u deze lijst lezen en gebruiken om het inrichten en het ongedaan maken van de inrichting van resources en toegang in uw toepassing te beheren.

**Gebruik het micro soft-identiteits platform voor rollen op basis van Access Control.**  Beheerders en eigen aren van toepassingen kunnen gebruikers en groepen toewijzen aan rollen die u definieert wanneer u uw toepassing registreert in het micro soft Identity-platform. De functie gegevens worden naar uw toepassing verzonden in aanmeldings tokens en kunnen ook worden gelezen met behulp van de Microsoft Graph-API. Meer informatie over het [gebruik van micro soft Identity platform voor autorisatie](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Toegang krijgen tot profiel, agenda, e-mail, contact personen, bestanden en meer voor gebruikers

**Micro soft Identity platform is de autorisatie server voor Microsoft 365 en andere zakelijke services van micro soft.**  Als u het micro soft Identity-platform voor het aanmelden bij uw toepassing ondersteunt of als ondersteuning voor het koppelen van uw huidige gebruikers accounts aan Azure AD-gebruikers accounts met behulp van OAuth 2,0, kunt u lees-en schrijf toegang aanvragen voor het profiel van een gebruiker, agenda, e-mail, contact personen, bestanden en andere informatie. U kunt eenvoudig gebeurtenissen naar de agenda van de gebruiker schrijven en bestanden in OneDrive lezen of schrijven. Meer informatie over [de Microsoft 365-api's](/graph/overview).

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>Promoot uw toepassing in Azure en Microsoft 365 marketplaces

**Promoot uw toepassing tot miljoenen organisaties die al gebruikmaken van Azure AD.**  Gebruikers die deze markt plaatsen zoeken en bezoeken, maken al gebruik van een of meer Cloud Services, waardoor ze gekwalificeerde Cloud service-klanten zijn. Meer informatie over het promoten van uw toepassing in [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Wanneer gebruikers zich aanmelden voor uw toepassing, wordt deze weer gegeven in hun Azure AD-toegangs venster en Microsoft 365 app-start programma.**  Gebruikers kunnen snel en eenvoudig terugkeren naar uw toepassing, waardoor de gebruikers betrokkenheid wordt verbeterd. Meer informatie over het [Azure AD-toegangs venster](../user-help/my-apps-portal-end-user-access.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Beveiligde apparaat-naar-service-en service-to-service-communicatie

**Het gebruik van het micro soft Identity-platform voor identiteits beheer van services en apparaten vermindert de code die u moet schrijven en stelt deze in staat om toegang te beheren.**  Services en apparaten kunnen tokens ophalen van het micro soft Identity-platform met behulp van OAuth en deze tokens gebruiken om toegang te krijgen tot Web-Api's. Met micro soft Identity platform kunt u voor komen dat complexe verificatie code wordt geschreven. Omdat de identiteiten van de services en apparaten zijn opgeslagen in azure AD, kan deze sleutels en intrekken op één plek beheren, in plaats van dat ze afzonderlijk in uw toepassing hoeven te worden uitgevoerd.

## <a name="benefits-of-integration"></a>Voor delen van integratie

Integratie met micro soft Identity platform wordt geleverd met voor delen waarbij u geen extra code hoeft te schrijven.

### <a name="integration-with-enterprise-identity-management"></a>Integratie met Enter prise Identity Management

**Help uw toepassing te voldoen aan het IT-beleid.**  Organisaties integreren hun bedrijfsidentiteits beheersysteem met het micro soft Identity-platform, dus wanneer een persoon een organisatie verlaat, wordt de toegang tot uw toepassing automatisch verbroken zonder dat er extra stappen hoeven te worden ondernomen. Hiermee kan worden beheerd wie toegang heeft tot uw toepassing en bepalen welk toegangs beleid vereist is, bijvoorbeeld multi-factor Authentication, waardoor code niet meer hoeft te worden geschreven om te voldoen aan complexe bedrijfs beleidsregels. Azure AD biedt beheerders een gedetailleerd controle logboek van wie zich heeft aangemeld bij uw toepassing, zodat het gebruik kan worden bijgehouden.

**Azure AD breidt Active Directory uit naar de Cloud, zodat uw toepassing kan worden geïntegreerd met AD.**  Veel organisaties over de hele wereld gebruiken Active Directory als hun belangrijkste aanmeld-en identiteits beheersysteem en vereisen dat hun toepassingen werken met AD. Integreren met Azure AD integreert uw app met Active Directory.

### <a name="advanced-security-features"></a>Geavanceerde beveiligings functies

**Multi-factor Authentication.**  Micro soft Identity platform biedt systeem eigen multi-factor Authentication. IT-beheerders kunnen multi-factor Authentication vereisen voor toegang tot uw toepassing, zodat u deze ondersteuning niet zelf hoeft te coderen. Meer informatie over [multi-factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Afwijkende aanmeldings detectie.**  Micro soft Identity platform verwerkt meer dan een miljard aanmeldingen per dag, terwijl u machine learning algoritmen gebruikt om verdachte activiteiten te detecteren en IT-beheerders op de hoogte te stellen van mogelijke problemen. Als u zich aanmeldt bij micro soft Identity-platform, wordt deze bescherming voor deel van uw toepassing. Meer informatie over het [weer geven van Azure Active Directory Access-rapport](../reports-monitoring/overview-reports.md).

**Voorwaardelijke toegang.**  Naast multi-factor Authentication kunnen beheerders specifieke voor waarden vereisen voordat gebruikers zich kunnen aanmelden bij uw toepassing. Voor waarden die kunnen worden ingesteld, zijn onder andere het IP-adres bereik van client apparaten, lidmaatschap van de opgegeven groepen en de status van het apparaat dat wordt gebruikt voor toegang. Meer informatie over [voorwaardelijke toegang van Azure Active Directory](../conditional-access/overview.md).

### <a name="easy-development"></a>Eenvoudige ontwikkeling

**Industrie standaard protocollen.**  Micro soft hecht veel belang aan de ondersteuning van industrie normen. Het micro soft Identity-platform biedt ondersteuning voor de industrie standaard OAuth 2,0 en OpenID Connect Connect 1,0-protocollen. Meer informatie over [verificatie protocollen voor micro soft-identiteits platform](active-directory-v2-protocols.md).

**Open-source-bibliotheken.**  Micro soft biedt volledig ondersteunde open source-bibliotheken voor populaire talen en platforms om de ontwikkeling te versnellen. De bron code wordt in licentie gegeven onder Apache 2,0 en u kunt de projecten splitsen en er een bijdrage aan leveren. Meer informatie over de [micro soft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Wereld wijde aanwezigheid en hoge Beschik baarheid

**Azure AD is geïmplementeerd in data centers over de hele wereld en wordt beheerd en gecontroleerd rond de klok.**  Azure AD is het identiteits beheersysteem voor Microsoft Azure en Microsoft 365 en wordt geïmplementeerd in 28 data centers over de hele wereld. Directory gegevens worden gegarandeerd gerepliceerd naar ten minste drie data centers. Globale load balancers zorgen ervoor dat gebruikers toegang krijgen tot het dichtstbijzijnde exemplaar van Azure AD dat hun gegevens bevat, en aanvragen automatisch opnieuw routeren naar andere Data Centers als er een probleem wordt gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

[Aan de slag](v2-overview.md#getting-started)met het schrijven van code.

[Gebruikers aanmelden met het micro soft Identity-platform](./authentication-vs-authorization.md)