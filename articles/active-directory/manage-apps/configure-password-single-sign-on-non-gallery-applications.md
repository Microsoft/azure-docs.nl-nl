---
title: Informatie over eenmalige aanmelding op basis van wacht woorden (SSO) voor apps in Azure Active Directory
description: Informatie over eenmalige aanmelding op basis van wacht woorden (SSO) voor apps in Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: bac04bd70469d7b9c4d9485b6a87fd7133967154
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255302"
---
# <a name="understand-password-based-single-sign-on"></a>Informatie over eenmalige aanmelding op basis van wacht woorden

In de [Quick](view-applications-portal.md) start-serie op toepassings beheer hebt u geleerd hoe u Azure AD als id-provider (IDP) gebruikt voor een toepassing. In de hand leiding Snelstartgids configureert u op SAML gebaseerde SSO-of OIDC. Een andere optie is SSO op basis van een wacht woord. In dit artikel vindt u meer informatie over de optie voor eenmalige aanmelding op basis van wacht woorden. 

Deze optie is beschikbaar voor elke website met een HTML-aanmeldings pagina. Eenmalige aanmelding op basis van wacht woorden is ook bekend als wachtwoord kluis. Met op wacht woord gebaseerde SSO kunt u gebruikers toegang en-wacht woorden beheren voor webtoepassingen die geen ondersteuning bieden voor identiteits Federatie. Het is ook handig wanneer meerdere gebruikers één account moeten delen, zoals de accounts voor sociale media-apps van uw organisatie.

SSO op basis van wacht woorden is een uitstekende manier om snel toepassingen te integreren in azure AD en biedt de volgende voor delen:

- Eenmalige aanmelding inschakelen voor uw gebruikers door gebruikers namen en wacht woorden veilig op te slaan en opnieuw af te spelen

- Ondersteunings toepassingen waarvoor meerdere aanmeldings velden zijn vereist voor toepassingen waarvoor meer dan alleen gebruikers naam en wacht woord zijn vereist om zich aan te melden

- De labels van de gebruikers naam en het wacht woord aanpassen die uw gebruikers zien op [mijn apps](../user-help/my-apps-portal-end-user-access.md) wanneer ze hun referenties invoeren

- Stel uw gebruikers in staat om hun eigen gebruikers namen en wacht woorden op te geven voor bestaande toepassings accounts die ze hand matig invoeren.

- Een lid van de bedrijfs groep toestaan om de gebruikers namen en wacht woorden op te geven die aan een gebruiker zijn toegewezen met behulp van de [self-service toepassings toegangs](./manage-self-service-access.md) functie

-   Een beheerder toestaan om een gebruikers naam en wacht woord op te geven die moeten worden gebruikt door gebruikers of groepen wanneer ze zich aanmelden bij de toepassing met de functie voor het bijwerken van referenties 

## <a name="before-you-begin"></a>Voordat u begint

Het gebruik van Azure AD als uw ID-provider (IdP) en het configureren van eenmalige aanmelding (SSO) kan eenvoudig of complex zijn, afhankelijk van de toepassing die wordt gebruikt. Sommige toepassingen kunnen met slechts een paar acties worden geconfigureerd. Andere gebruikers moeten een gedetailleerde configuratie hebben. Volg de Quick Start- [serie](view-applications-portal.md) op toepassings beheer om kennis snel te vinden. Als de toepassing die u wilt toevoegen, eenvoudig is, hoeft u dit artikel waarschijnlijk niet te lezen. Als voor de toepassing die u wilt toevoegen aangepaste configuratie vereist is en u op wacht woord gebaseerde SSO wilt gebruiken, is dit artikel voor u.

> [!IMPORTANT] 
> Er zijn enkele scenario's waarbij de optie voor **eenmalige aanmelding** niet voor komt in de navigatie voor een toepassing in **bedrijfs toepassingen**. 
>
> Als de toepassing is geregistreerd met behulp van **app-registraties** , is de mogelijkheid voor eenmalige aanmelding standaard geconfigureerd voor het gebruik van OIDC OAuth. In dit geval wordt de optie **voor eenmalige aanmelding** niet weer gegeven in de navigatie onder **bedrijfs toepassingen**. Wanneer u **app-registraties** gebruikt om uw aangepaste app toe te voegen, configureert u de opties in het manifest bestand. Zie [Azure Active Directory app-manifest](../develop/reference-app-manifest.md)voor meer informatie over het manifest bestand. Zie [verificatie en autorisatie met behulp van micro soft Identity platform](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)voor meer informatie over SSO-standaarden. 
>
> Andere scenario's waarin **eenmalige aanmelding** ontbreekt in de navigatie, zijn, wanneer een toepassing wordt gehost in een andere Tenant of als uw account niet over de vereiste machtigingen (globale beheerder, Cloud toepassings beheerder, toepassings beheerder of eigenaar van de service-principal) beschikt. Machtigingen kunnen ook leiden tot een scenario waarin u **eenmalige aanmelding** kunt openen, maar niet kunt opslaan. Zie (voor meer informatie over Azure AD-beheerders rollen https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Basisconfiguratie

In de [Quick](view-applications-portal.md)start-serie hebt u geleerd hoe u een app kunt toevoegen aan uw Tenant, waardoor Azure AD weet dat deze wordt gebruikt als de ID-provider (IDP) voor de app. Sommige apps zijn al vooraf geconfigureerd en worden weer gegeven in de Azure AD-galerie. Andere apps bevinden zich niet in de galerie en u moet een algemene app maken en deze hand matig configureren. Afhankelijk van de app is de SSO-optie op basis van wacht woorden mogelijk niet beschikbaar. Als u de lijst met op wacht woord gebaseerde opties niet ziet op de pagina voor eenmalige aanmelding voor de app, is deze niet beschikbaar.

> [!IMPORTANT]
> De browser uitbreiding van mijn apps is vereist voor SSO op basis van een wacht woord. Zie [een implementatie van mijn apps plannen](my-apps-deployment-plan.md)voor meer informatie.

De configuratie pagina voor SSO op basis van een wacht woord is eenvoudig. Het bevat alleen de URL van de aanmeldings pagina die door de app wordt gebruikt. Deze teken reeks moet de pagina zijn die het invoergegevens veld van de gebruikers naam bevat.

Nadat u de URL hebt ingevoerd, selecteert u **Opslaan**. Azure AD parseert de HTML-code van de aanmeldings pagina voor de invoer velden gebruikers naam en wacht woord. Als de poging slaagt, bent u klaar.
 
De volgende stap is het [toewijzen van gebruikers of groepen aan de toepassing](./assign-user-or-group-access-portal.md). Nadat u gebruikers en groepen hebt toegewezen, kunt u referenties opgeven die moeten worden gebruikt voor een gebruiker wanneer deze zich aanmeldt bij de toepassing. Selecteer **gebruikers en groepen**, schakel het selectie vakje in voor de rij van de gebruiker of groep en selecteer vervolgens **referenties bijwerken**. Voer ten slotte de gebruikers naam en het wacht woord in die moeten worden gebruikt voor de gebruiker of groep. Als u dit niet doet, wordt gebruikers gevraagd de referenties zelf in te voeren bij het starten.
 

## <a name="manual-configuration"></a>Handmatige configuratie

Als het parseren van de Azure AD-poging mislukt, kunt u hand matig aanmelden configureren.

1. Onder **\<application name> configuratie** selecteert u **\<application name> instellingen voor eenmalige aanmelding met een wacht woord configureren** om de aanmeldings pagina **configureren** weer te geven. 

2. Selecteer **aanmeldings velden hand matig detecteren**. Aanvullende instructies voor het beschrijven van de hand matige detectie van aanmeldings velden worden weer gegeven.

   ![Hand matige configuratie van op wacht woord gebaseerde eenmalige aanmelding](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selecteer **aanmeldings velden voor vastleggen**. De pagina vastleg status wordt geopend op een nieuw tabblad, waarin wordt weer gegeven **dat het vastleggen van meta gegevens op het bericht wordt uitgevoerd**.

4. Als het selectie vakje ' **mijn apps vereist** ' wordt weer gegeven op een nieuw tabblad, selecteert u **nu installeren** om de extensie browser voor **beveiligde aanmelding van mijn apps** te installeren. (Voor de browser extensie is micro soft Edge, Chrome of Firefox vereist.) Vervolgens installeert, start en activeert u de extensie en vernieuwt u de pagina vastleg status.

   De browser uitbreiding opent vervolgens een ander tabblad met de ingevoerde URL.
5. Ga op het tabblad met de ingevoerde URL naar het aanmeldings proces. Vul de velden gebruikers naam en wacht woord in en probeer u aan te melden. (U hoeft het juiste wacht woord niet op te geven.)

   U wordt gevraagd om de vastgelegde aanmeldings velden op te slaan.
6. Selecteer **OK**. De browser uitbreiding werkt de pagina vastleg status bij met de **meta gegevens van het bericht voor de toepassing**. Het tabblad browser wordt gesloten.

7. Selecteer op de pagina Azure AD **-aanmelding configureren** de optie **OK, ik kan me aanmelden bij de app**.

8. Selecteer **OK**.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](./assign-user-or-group-access-portal.md)
- [Automatische inrichting van gebruikersaccounts configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
