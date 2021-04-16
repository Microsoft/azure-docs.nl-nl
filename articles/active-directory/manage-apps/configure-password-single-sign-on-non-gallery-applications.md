---
title: Informatie over eenmalige aanmelding (SSO) op basis van wachtwoorden voor apps in Azure Active Directory
description: Informatie over eenmalige aanmelding (SSO) op basis van wachtwoorden voor apps in Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: iangithinji
ms.openlocfilehash: ffa517f068dbc13f2734630216466373d9014ae6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374553"
---
# <a name="understand-password-based-single-sign-on"></a>Op wachtwoorden gebaseerde een een-op-een-aanmelding begrijpen

In de [quickstart-reeks](view-applications-portal.md) over toepassingsbeheer hebt u geleerd hoe u Azure AD gebruikt als id-provider (IdP) voor een toepassing. In de quickstart configureert u eenmalige aanmelding op basis van SAML of OIDC. Een andere optie is eenmalige aanmelding op basis van een wachtwoord. In dit artikel wordt de optie voor eenmalige aanmelding op basis van wachtwoorden uitgebreider besproken. 

Deze optie is beschikbaar voor elke website met een HTML-aanmeldingspagina. Eenmalige aanmelding op basis van een wachtwoord wordt ook wel wachtwoordkluis genoemd. Met eenmalige aanmelding op basis van wachtwoorden kunt u gebruikerstoegang en wachtwoorden beheren voor webtoepassingen die geen ondersteuning bieden voor identiteitsfederatie. Het is ook handig wanneer meerdere gebruikers één account moeten delen, zoals de accounts voor sociale media-apps van uw organisatie.

Eenmalige aanmelding op basis van een wachtwoord is een uitstekende manier om snel aan de slag te gaan met het snel integreren van toepassingen in Azure AD en stelt u in staat het volgende te doen:

- Een aanmelding voor uw gebruikers inschakelen door gebruikersnamen en wachtwoorden veilig op te slaan en opnieuw af te spelen

- Ondersteuningstoepassingen waarvoor meerdere aanmeldingsvelden zijn vereist voor toepassingen waarvoor meer dan alleen de velden gebruikersnaam en wachtwoord zijn vereist om u aan te melden

- Pas de labels aan van de gebruikersnaam- en wachtwoordvelden die uw gebruikers zien [op](../user-help/my-apps-portal-end-user-access.md) Mijn apps wanneer ze hun referenties invoeren

- Sta uw gebruikers toe hun eigen gebruikersnamen en wachtwoorden op te geven voor bestaande toepassingsaccounts die ze handmatig typen.

- Een lid van de bedrijfsgroep toestaan de gebruikersnamen en wachtwoorden op te geven die zijn toegewezen aan een gebruiker met behulp van de functie Toegang tot [selfservicetoepassing](./manage-self-service-access.md)

-   Een beheerder toestaan om een gebruikersnaam en wachtwoord op te geven die moeten worden gebruikt door personen of groepen wanneer ze zich aanmelden bij de toepassing met de functie Referenties bijwerken 

## <a name="before-you-begin"></a>Voordat u begint

Het gebruik van Azure AD als id-provider (IdP) en het configureren van eenmalige aanmelding (SSO) kan eenvoudig of complex zijn, afhankelijk van de toepassing die wordt gebruikt. Sommige toepassingen kunnen worden geconfigureerd met slechts een paar acties. Voor andere is een diepgaande configuratie vereist. Als u snel kennis wilt maken, doorloop dan de [quickstart-reeks](view-applications-portal.md) over toepassingsbeheer. Als de toepassing die u toevoegt eenvoudig is, hoeft u dit artikel waarschijnlijk niet te lezen. Als voor de toepassing die u toevoegt een aangepaste configuratie is vereist en u eenmalige aanmelding op basis van een wachtwoord moet gebruiken, is dit artikel voor u bedoeld.

> [!IMPORTANT] 
> Er zijn enkele scenario's waarin de optie **voor een een aanmelding** niet in de navigatie voor een toepassing in **Bedrijfstoepassingen staat.** 
>
> Als de toepassing is geregistreerd met **behulp App-registraties** is de mogelijkheid voor een aanmelding standaard geconfigureerd voor het gebruik van OIDC OAuth. In dit geval wordt **de optie Voor een aanmelding** niet in de navigatie onder **Bedrijfstoepassingen weer geven.** Wanneer u **App-registraties** aangepaste app toevoegt, configureert u opties in het manifestbestand. Zie voor meer informatie over het manifestbestand [Azure Active Directory app-manifest.](../develop/reference-app-manifest.md) Zie Verificatie en autorisatie met Microsoft Identity Platform voor meer informatie over [SSO-standaarden.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Andere scenario's waarbij een een aanmelding ontbreekt in de navigatie, zijn onder andere wanneer een toepassing wordt gehost in een andere tenant of als uw account niet over de vereiste machtigingen (globale beheerder, Cloud Application Administrator, toepassingsbeheerder of eigenaar van de **service-principal)** is. Machtigingen kunnen ook een scenario veroorzaken waarin u een **een aanmelding kunt** openen, maar niet kunt opslaan. Zie voor meer informatie over Azure AD-beheerdersrollen. https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)


## <a name="basic-configuration"></a>Basisconfiguratie

In de [quickstart-reeks](view-applications-portal.md)hebt u geleerd hoe u een app toevoegt aan uw tenant, zodat Azure AD weet dat deze wordt gebruikt als id-provider (IdP) voor de app. Sommige apps zijn al vooraf geconfigureerd en worden in de Azure AD-galerie weer geven. Andere apps staan niet in de galerie en u moet een algemene app maken en deze handmatig configureren. Afhankelijk van de app is de optie voor eenmalige aanmelding op basis van een wachtwoord mogelijk niet beschikbaar. Als u de optielijst op basis van een wachtwoord niet ziet op de pagina voor een aanmelding voor de app, is deze niet beschikbaar.

> [!IMPORTANT]
> De Mijn apps browserextensie is vereist voor eenmalige aanmelding op basis van een wachtwoord. Zie Plan a [Mijn apps deployment (Een Mijn apps plannen) voor meer informatie.](my-apps-deployment-plan.md)

De configuratiepagina voor eenmalige aanmelding op basis van een wachtwoord is eenvoudig. Deze bevat alleen de URL van de aanmeldingspagina die door de app wordt gebruikt. Deze tekenreeks moet de pagina zijn die het invoerveld voor de gebruikersnaam bevat.

Nadat u de URL hebt opgegeven, selecteert u **Opslaan.** Azure AD parseert de HTML van de aanmeldingspagina voor invoervelden voor gebruikersnaam en wachtwoord. Als de poging is gelukt, bent u klaar.
 
De volgende stap is het [toewijzen van gebruikers of groepen aan de toepassing](./assign-user-or-group-access-portal.md). Nadat u gebruikers en groepen hebt toegewezen, kunt u referenties verstrekken die voor een gebruiker moeten worden gebruikt wanneer ze zich aanmelden bij de toepassing. Selecteer **Gebruikers en groepen,** schakel het selectievakje in voor de rij van de gebruiker of groep en selecteer **vervolgens Referenties bijwerken.** Voer ten slotte de gebruikersnaam en het wachtwoord in die moeten worden gebruikt voor de gebruiker of groep. Als u dit niet doen, wordt gebruikers gevraagd om de referenties zelf in te voeren bij het starten.
 

## <a name="manual-configuration"></a>Handmatige configuratie

Als de parseringspoging van Azure AD mislukt, kunt u de aanmelding handmatig configureren.

1. Selecteer **\<application name> onder Configuratie** de optie **Configure Password Single \<application name> Sign-on Settings** om de pagina Aanmelding **configureren weer te** geven. 

2. Selecteer **De aanmeldingsvelden handmatig detecteren.** Er worden aanvullende instructies weergegeven waarin de handmatige detectie van aanmeldingsvelden wordt beschreven.

   ![Handmatige configuratie van een aanmelding op basis van een wachtwoord](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selecteer **Aanmeldingsvelden vastleggen.** Er wordt een pagina met de status van de opname geopend op een nieuw tabblad, waarop wordt weergegeven dat het vastleggen van **metagegevens van het bericht wordt uitgevoerd.**

4. Als het **Mijn apps extensie** vereist wordt weergegeven op een  nieuw tabblad, selecteert u Nu installeren om de browserextensie Mijn apps **beveiligde aanmeldingsextensie** te installeren. (Voor de browserextensie Microsoft Edge, Chrome of Firefox vereist.) Vervolgens installeert, start en inschakelen van de extensie en vernieuwt u de pagina Status vastleggen.

   De browserextensie opent vervolgens een ander tabblad met de ingevoerde URL.
5. Ga op het tabblad met de ingevoerde URL door het aanmeldingsproces. Vul de velden gebruikersnaam en wachtwoord in en probeer u aan te melden. (U hoeft niet het juiste wachtwoord op te geven.)

   U wordt gevraagd de vastgelegde aanmeldingsvelden op te slaan.
6. Selecteer **OK**. De browserextensie werkt de pagina met de capture-status bij met het bericht **Metagegevens is bijgewerkt voor de toepassing**. Het browsertabblad wordt gesloten.

7. Selecteer op de **pagina Azure AD-aanmelding configureren** de optie OK. Ik heb me kunnen aanmelden **bij de app.**

8. Selecteer **OK**.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](./assign-user-or-group-access-portal.md)
- [Automatische inrichting van gebruikersaccounts configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
