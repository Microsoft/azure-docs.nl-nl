---
title: Flexibele ervaring voor eind gebruikers met behulp van Azure AD B2C | Microsoft Docs
description: Methoden voor het bouwen van de flexibiliteit in de ervaring van de eind gebruiker met behulp van Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b47a4a79fd423806693e86aef1edd132d844069e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557901"
---
# <a name="resilient-end-user-experience"></a>Flexibele ervaring voor eind gebruikers

De ervaring voor het registreren en aanmelden van eind gebruikers bestaat uit de volgende elementen:

- De interfaces waarmee de gebruiker communiceert, zoals CSS, HTML en Java script

- De gebruikers stromen en aangepaste beleids regels die u maakt, zoals aanmelden, aanmelden en profiel bewerken

- De id-providers (id) voor uw toepassing, zoals gebruikers naam en wacht woord voor het lokale account, Outlook, Facebook en Google

![Afbeelding toont onderdelen van de eind gebruiker](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>Kiezen tussen gebruikers stroom en aangepast beleid  

Azure AD B2C biedt ingebouwde Configureer bare [gebruikers stromen](../../active-directory-b2c/user-flow-overview.md)om u te helpen bij het instellen van de meest voorkomende identiteits taken. U kunt ook uw eigen [aangepaste beleids regels](../../active-directory-b2c/custom-policy-overview.md)maken. Dit biedt u maximale flexibiliteit. Het is echter raadzaam aangepaste beleids regels alleen te gebruiken om complexe scenario's te verhelpen.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>Hoe gebruikers stromen en aangepaste beleids regels kunnen bepalen

Kies ingebouwde gebruikers stromen als aan uw bedrijfs vereisten kan worden voldaan. Aangezien micro soft uitgebreid is getest, kunt u de test die nodig is voor het valideren van functionaliteit op beleids niveau, prestaties of schalen van deze identiteits gebruikers stromen, minimaliseren. U moet uw toepassingen nog steeds testen op functionaliteit, prestaties en schaal.

Als u [aangepaste beleids regels wilt kiezen](../../active-directory-b2c/custom-policy-get-started.md) vanwege uw bedrijfs vereisten, moet u ervoor zorgen dat u een test op beleids niveau uitvoert voor functionele, prestatie of schaal baarheid naast het testen op toepassings niveau.

Zie het artikel waarin de [gebruikers stromen en aangepaste beleids](../../active-directory-b2c/custom-policy-overview.md#comparing-user-flows-and-custom-policies) regels worden vergeleken om u te helpen beslissen.

## <a name="choose-multiple-idps"></a>Meerdere id kiezen

Wanneer u een [externe ID-provider](../../active-directory-b2c/technical-overview.md#external-identity-providers) gebruikt, zoals Facebook, moet u ervoor zorgen dat er een terugval plan is voor het geval de externe provider niet beschikbaar is.

### <a name="how-to-set-up-multiple-idps"></a>Meerdere id instellen

Als onderdeel van het registratie proces van de externe ID-provider, moet u een geverifieerde identiteits claim zoals het mobiele of e-mail adres van de gebruiker opnemen. De geverifieerde claims door voeren naar de onderliggende Azure AD B2C Directory-instantie. Als de externe provider niet beschikbaar is, keert u terug naar de geverifieerde identiteits claim en terugvallen op het telefoon nummer als verificatie methode. Een andere mogelijkheid is de gebruiker een eenmalige wachtwoord code te sturen waarmee de gebruiker zich kan aanmelden.

 Voer de volgende stappen uit om [alternatieve verificatie paden te maken](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter):

 1. Configureer uw aanmeldings beleid om registreren via lokaal account en externe ID toe te staan.

 2. Configureer een profiel beleid zodat gebruikers [de andere identiteit aan hun account kunnen koppelen](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) nadat ze zich hebben aangemeld.

 3. Waarschuw en sta gebruikers toe om [over te scha kelen naar een alternatieve IDP](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) tijdens een storing.

## <a name="availability-of-multi-factor-authentication"></a>Beschik baarheid van multi-factor Authentication

Wanneer u een [telefoon service voor multi-factor Authentication (MFA)](../../active-directory-b2c/phone-authentication-user-flows.md)gebruikt, moet u rekening houden met een alternatieve service provider. De lokale telecommunicatie of Phone-service provider kan onderbrekingen ondervinden in hun service.

### <a name="how-to-choose-an-alternate-mfa"></a>Een alternatieve MFA kiezen  

De Azure AD B2C-service maakt gebruik van een ingebouwde op telefoon gebaseerde MFA-provider, voor het leveren van eenmalige wachtwoord code (OTPs). Het is in de vorm van een spraak oproep en SMS-bericht voor het vooraf geregistreerde telefoon nummer van de gebruiker. De volgende alternatieve methoden zijn beschikbaar voor verschillende scenario's:

Wanneer u **gebruikers stromen** gebruikt, zijn er twee manieren om een tolerantie te bouwen:

- **Configuratie van de gebruikers stroom wijzigen**: als u een onderbreking in de op telefoon gebaseerde otp-levering wilt detecteren, wijzigt u de otp-leverings methode van de telefoon op basis van e-mail en implementeert u de gebruikers stroom opnieuw, zodat de toepassingen ongewijzigd blijven.

![scherm opname toont aanmeldings registratie](media/resilient-end-user-experiences/create-sign-in.png)

- **Wijzig toepassingen**: voor elke identiteits taak, zoals aanmelden en aanmelden, definieert u twee sets gebruikers stromen. Configureer eerste set voor het gebruik van OTP op basis van een telefoon en de tweede voor OTP op basis van e-mail. Bij het detecteren van een onderbreking in de telefonische levering op basis van de telefoon, wijzigt en implementeert u de toepassingen om over te scha kelen van de eerste set gebruikers stromen naar de tweede, waardoor de gebruikers ongewijzigd blijven.  

Wanneer u **aangepaste beleids regels** gebruikt, zijn er vier methoden om een tolerantie te bouwen. De onderstaande lijst is in de volg orde van complexiteit en u moet bijgewerkte beleids regels opnieuw implementeren.

- **Gebruikers selectie inschakelen van otp op basis van een telefoon of otp op basis van e-mail**: Hiermee worden beide opties beschikbaar gemaakt aan de gebruikers en kunnen gebruikers zelf een van de opties selecteren. U hoeft geen wijzigingen aan te brengen in de beleids regels of toepassingen.

- **Dynamisch scha kelen tussen otp op basis van telefoon-en e-mail adres**: zowel telefoon-als e-mail gegevens verzamelen bij het aanmelden. Definieer het aangepaste beleid vooraf om te scha kelen tijdens een onderbreking van de telefoon, op basis van telefonische levering via e-mail. U hoeft geen wijzigingen aan te brengen in de beleids regels of toepassingen.

- **Een verificator-app gebruiken**: aangepast beleid bijwerken om een [verificator-app](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp)te gebruiken. Als uw normale MFA op telefoon of op basis van een e-mail adres is gebaseerd, implementeert u uw aangepaste beleids regels om te scha kelen naar het gebruik van de verificator-app.

>[!Note]
>Gebruikers moeten de verificatie van de verificator-app configureren tijdens de registratie.

- **Beveiligings vragen gebruiken**: als geen van de bovenstaande methoden van toepassing is, implementeert u beveiligings vragen als back-up. Stel beveiligings vragen voor gebruikers in tijdens het voorbereiden of bewerken van profielen en sla de antwoorden op in een andere data base dan de map. Deze methode voldoet niet aan de MFA-vereiste van ' iets dat u hebt ' bijvoorbeeld telefoon, maar biedt een secundaire ' iets dat u wel kent '.

## <a name="use-a-content-delivery-network"></a>Een Content Delivery Network gebruiken

Content Delivery Networks (Cdn's) zijn beter en goed koper dan BLOB Stores voor opslag van gebruikers interface voor aangepaste gebruikers stromen. De inhoud van de webpagina wordt sneller geleverd vanuit een geografisch gedistribueerd netwerk van Maxi maal beschik bare servers.  

Test regel matig de beschik baarheid van uw CDN en de prestaties van inhouds distributie via end-to-end scenario en belasting testen. Als u van plan bent om een aanstaande piek te belasten vanwege promotie-of vakantie verkeer, kunt u uw schattingen herzien om de belasting te testen.
  
## <a name="next-steps"></a>Volgende stappen

- [Flexibiliteits bronnen voor Azure AD B2C-ontwikkel aars](resilience-b2c.md)
  
  - [Robuuste interfaces met externe processen](resilient-external-processes.md)
  - [Flexibiliteit door middel van aanbevolen procedures voor ontwikkel aars](resilience-b2c-developer-best-practices.md)
  - [Flexibiliteit door middel van bewaking en analyse](resilience-with-monitoring-alerting.md)
- [Flexibiliteit in uw verificatie-infra structuur maken](resilience-in-infrastructure.md)
- [Verhoog de flexibiliteit van verificatie en autorisatie in uw toepassingen](resilience-app-development-overview.md)