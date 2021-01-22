---
title: Overzicht van Azure Active Directory-verificatie
description: Krijg meer informatie over de verschillende verificatiemethoden en beveiligingsfuncties voor aanmeldingen van gebruikers met Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/20/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae7ae239493c999d98100bf9dee4f4f0183addf2
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660810"
---
# <a name="what-is-azure-active-directory-authentication"></a>Wat is Azure Active Directory-verificatie?

Een van de belangrijkste functies van een identiteitsplatform is het *verifiëren* van referenties wanneer een gebruiker zich bij een apparaat, toepassing of service aanmeldt. In Azure Active Directory (Azure AD) betreft verificatie meer dan alleen de controle van een gebruikersnaam en wachtwoord. Voor een betere beveiliging en minder behoefte aan hulp van een helpdesk, omvat Azure AD-verificatie de volgende onderdelen:

* Self-service voor wachtwoord opnieuw instellen
* Azure AD Multi-Factor Authentication
* Hybride integratie om wachtwoordwijzigingen terug te schrijven naar de on-premises omgeving
* Hybride integratie om beveiligingsbeleid voor wachtwoorden af te dwingen voor een on-premises omgeving
* Verificatie zonder wachtwoord

Bekijk onze korte video voor meer informatie over deze verificatie onderdelen.

## <a name="improve-the-end-user-experience"></a>De ervaring voor de eindgebruiker verbeteren

Azure AD helpt de identiteit van gebruikers beschermen en hun aanmeldervaring vereenvoudigen. Met behulp van functies zoals een self-service voor wachtwoordherstel kunnen gebruikers wachtwoorden via een webbrowser bijwerken of wijzigen, vanaf elk apparaat. Deze functie is met name handig wanneer gebruikers hun wachtwoord zijn vergeten of als hun account is vergrendeld. Zonder te hoeven wachten op ondersteuning van een helpdesk of beheerder kunnen gebruikers zelf de blokkering opheffen en verdergaan met hun werk.

Met Azure AD Multi-Factor Authentication kunnen gebruikers een extra verificatiemethode kiezen tijdens het aanmelden, zoals worden gebeld of een melding krijgen via een mobiele app. Dankzij deze mogelijkheid is er geen vaste vorm van secundaire verificatie meer vereist, zoals een hardwaretoken. Als gebruikers momenteel niet één vorm van aanvullende verificatie hebben, kunnen ze een andere methode kiezen en doorgaan met hun werk.

![Gebruikte verificatiemethoden op het aanmeldscherm](media/concept-authentication-methods/overview-login.png)

Door verificatie zonder wachtwoord hoeven gebruikers helemaal geen veilig wachtwoord meer te maken en te onthouden. Mogelijkheden zoals Windows Hello for Business of FIDO2-beveiligingssleutels stellen gebruikers in staat zich bij een apparaat of toepassing aan te melden zonder een wachtwoord. Deze mogelijkheid kan de complexiteit van het beheren van wachtwoorden in verschillende omgevingen verminderen.

## <a name="self-service-password-reset"></a>Self-service voor wachtwoord opnieuw instellen

Self-service voor wachtwoordherstel biedt gebruikers de mogelijkheid hun wachtwoord te wijzigen of opnieuw in te stellen zonder tussenkomst van een beheerder of helpdesk. Als het account van een gebruiker is vergrendeld of als deze zijn of haar wachtwoord is vergeten, kan de gebruiker de vergrendeling aan de hand van instructies zelf ongedaan maken en weer aan het werk gaan. Op deze manier wordt het aantal telefoontjes naar de helpdesk en productieverlies verminderd wanneer een gebruiker zich niet kan aanmelden bij een apparaat of toepassing.

De self-service voor wachtwoordherstel werkt in de volgende scenario's:

* **Wachtwoord wijzigen**: wanneer gebruikers hun wachtwoord wel weten, maar deze in een nieuw wachtwoord willen wijzigen.
* **Wachtwoord opnieuw instellen**: wanneer gebruikers zich niet kunnen aanmelden, wanneer ze bijvoorbeeld hun wachtwoord zijn vergeten en hun wachtwoord opnieuw willen instellen.
* **Account ontgrendelen**: wanneer gebruikers zich niet kunnen aanmelden omdat hun account is vergrendeld en ze hun account willen ontgrendelen.

Wanneer gebruikers hun wachtwoord bijwerken of opnieuw instellen met behulp van de self-service voor wachtwoordherstel, kan dat wachtwoord ook worden teruggeschreven naar een on-premises Active Directory-omgeving. Door het kunnen terugschrijven van wachtwoorden kunnen gebruikers direct gebruikmaken van hun bijgewerkte referenties met on-premises apparaten en toepassingen.

## <a name="azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication

Meervoudige verificatie is een proces waarbij gebruikers tijdens het aanmeldproces om een aanvullende vorm van identificatie wordt gevraagd, zoals het invoeren van een code op hun mobiele telefoon of het uitvoeren van een vingerafdrukscan.

Als u alleen een wachtwoord gebruikt om gebruikers te verifiëren, is dit een onveilige aanvalsvector. Als het wachtwoord zwak is of elders is weergegeven, is het dan wel echt de gebruiker die zich met de gebruikersnaam en het wachtwoord aanmeldt, of is het een aanvaller? Wanneer u een tweede vorm van verificatie vereist, neemt de beveiliging toe omdat deze aanvullende factor niet eenvoudig door een aanvaller kan worden verkregen of gedupliceerd.

![Conceptafbeelding van de verschillende vormen van meervoudige verificatie](./media/concept-mfa-howitworks/methods.png)

Azure AD Multi-Factor Authentication werkt door twee van de volgende verificatiemethoden te vereisen:

* Iets dat u weet, zoals een wachtwoord.
* Iets dat u hebt, zoals een vertrouwd apparaat dat niet eenvoudig kan worden gedupliceerd, zoals een telefoon of hardwaresleutel.
* Iets dat u bent: biometrische gegevens zoals een vingerafdruk of gezichtsscan.

Gebruikers kunnen zich registreren voor zowel de self-service voor wachtwoordherstel als Azure AD Multi-Factor Authentication. Dit gebeurt in één stap, voor een eenvoudigere onboardingervaring. Beheerders kunnen definiëren welke vormen van secundaire verificatie kunnen worden gebruikt. Azure AD Multi-Factor Authentication kan ook vereist zijn wanneer gebruikers een self-service voor wachtwoordherstel uitvoeren om dit proces nog verder te beveiligen.

## <a name="password-protection"></a>Wachtwoordbeveiliging

Standaard blokkeert Azure AD zwakke wachtwoorden zoals *Wachtwoord1*. Een wereldwijde lijst met verboden, bekende zwakke wachtwoorden wordt automatisch bijgewerkt en afgedwongen. Als Azure AD-gebruikers proberen hun wachtwoord in te stellen op een van deze zwakke wachtwoorden, krijgen ze een melding dat ze een veiliger wachtwoord moeten kiezen.

U kunt de beveiliging verhogen door aangepaste beleidsregels voor wachtwoordbeveiliging te definiëren. Voor deze beleidsregels kunnen filters worden gebruikt voor het blokkeren van variaties op een wachtwoord met een naam zoals *Contoso* of een locatie zoals *Londen*.

Voor hybride beveiliging kunt u Azure AD-wachtwoordbeveiliging integreren in een on-premises Active Directory-omgeving. De wereldwijde lijst met verboden wachtwoorden en de aangepaste beleidsregels voor wachtwoordbeveiliging van Azure AD worden naar een onderdeel gestuurd dat in de on-premises omgeving is geïnstalleerd; deze worden door domeincontrollers gebruikt om wijzigingen van wachtwoorden te verwerken. Door deze hybride methode weet u zeker dat het gebruik van sterke wachtwoorden wordt afgedwongen, ongeacht hoe of waar gebruikers hun referenties wijzigen.

## <a name="passwordless-authentication"></a>Verificatie zonder wachtwoord

Het einddoel voor veel omgevingen is het verwijderen van het gebruik van wachtwoorden als onderdelen van aanmeldingen. Functies zoals Azure-wachtwoordbeveiliging of Azure AD Multi-Factor Authentication helpen de beveiliging verbeteren, maar een gebruikersnaam en wachtwoord blijven een zwakke vorm van verificatie die kan worden blootgesteld of worden aangevallen.

![Beveiliging versus gemak met het verificatieproces op weg naar een leven zonder wachtwoorden](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Wanneer u zich aanmeldt met een wacht woordloze methode, worden er referenties gegeven met behulp van methoden als biometrie met Windows hello voor bedrijven of een FIDO2-beveiligings sleutel. Deze verificatiemethoden kunnen niet eenvoudig door een aanvaller worden gedupliceerd.

Azure AD biedt verschillende manieren om verificatie via het systeem uit te voeren met methoden zonder wachtwoord om de aanmeldervaring voor gebruikers te vereenvoudigen en het risico van aanvallen te verminderen.

## <a name="next-steps"></a>Volgende stappen

Zie de [zelfstudie voor SSPR (self-service voor wachtwoordherstel)][tutorial-sspr] en [Azure AD Multi-Factor Authentication][tutorial-azure-mfa] om aan de slag te gaan.

Zie [Zo werkt de self-service voor wachtwoordherstel van Azure AD][concept-sspr] voor meer informatie over concepten voor de self-service voor het opnieuw instellen van wachtwoorden.

Zie [Werking van Azure AD Multi-Factor Authentication][concept-mfa] voor meer informatie over concepten voor meervoudige verificatie.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
