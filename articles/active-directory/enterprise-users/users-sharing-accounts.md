---
title: Accounts en referenties delen-Azure Active Directory | Microsoft Docs
description: Hierin wordt beschreven hoe Azure Active Directory organisaties in staat stelt om accounts veilig te delen voor on-premises apps en Cloud Services voor consumenten.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb088d56879ebdf5d439c913ac47a701db5c4a60
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576244"
---
# <a name="sharing-accounts-with-azure-ad"></a>Accounts delen met Azure AD

## <a name="overview"></a>Overzicht

Soms moeten organisaties één gebruikers naam en wacht woord voor meerdere personen gebruiken. dit gebeurt meestal in twee gevallen:

* Bij het openen van toepassingen waarvoor een unieke aanmelding en een uniek wacht woord is vereist voor elke gebruiker, ongeacht of deze on-premises apps of Cloud Services van de consument zijn (bijvoorbeeld zakelijke sociale media-accounts).
* Bij het maken van omgevingen met meerdere gebruikers. Mogelijk hebt u één lokaal account met verhoogde bevoegdheden en wordt dit gebruikt om de kern installatie, het beheer en de herstel activiteiten uit te voeren. Bijvoorbeeld het lokale account ' globale beheerder ' voor Microsoft 365 of het hoofd account in Sales Force.

Deze accounts worden gewoonlijk gedeeld door de referenties (gebruikers naam en wacht woord) te distribueren naar de juiste personen of op een gedeelde locatie op te slaan waar meerdere vertrouwde agents toegang toe hebben.

Het traditionele model voor delen heeft verschillende nadelen:

* Als u toegang tot nieuwe toepassingen wilt inschakelen, moet u referenties distribueren naar iedereen die toegang nodig heeft.
* Elke gedeelde toepassing kan een eigen unieke set met gedeelde referenties vereisen, zodat gebruikers meerdere sets referenties kunnen onthouden. Wanneer gebruikers veel referenties moeten onthouden, neemt het risico toe dat ze een risico volle prak tijken nemen. (bijvoorbeeld het schrijven van wacht woorden).
* U kunt niet bepalen wie toegang heeft tot een toepassing.
* U kunt niet bepalen wie *toegang heeft gehad tot* een toepassing.
* Wanneer u de toegang tot een toepassing wilt verwijderen, moet u de referenties bijwerken en opnieuw distribueren naar iedereen die toegang tot die toepassing nodig heeft.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory account delen

Azure AD biedt een nieuwe benadering voor het gebruik van gedeelde accounts die deze nadelen elimineren.

De Azure AD-beheerder configureert welke toepassingen een gebruiker kan openen met behulp van het toegangs venster en kiest het type eenmalige aanmelding dat het meest geschikt is voor die toepassing. Een van deze typen, *op wacht woord gebaseerde eenmalige aanmelding*, laat Azure AD Act als een soort ' Broker ' tijdens het aanmeldings proces voor die app.

Gebruikers melden zich aan met hun organisatie account. Dit account is hetzelfde als het wordt regel matig gebruikt om toegang te krijgen tot hun bureau blad of e-mail adres. Ze kunnen alleen de toepassingen detecteren en openen die aan hen zijn toegewezen. Bij gedeelde accounts kan deze lijst met toepassingen een wille keurig aantal gedeelde referenties bevatten. De eind gebruiker hoeft de verschillende accounts die mogelijk worden gebruikt, niet te onthouden of te noteren.

Gedeelde accounts verhogen niet alleen het toezicht en verbeteren de bruikbaarheid van uw veiligheid. Gebruikers met machtigingen voor het gebruik van de referenties zien het gedeelde wacht woord niet, maar krijgen wel machtigingen om het wacht woord te gebruiken als onderdeel van een georganisatiede verificatie stroom. Daarnaast biedt sommige SSO-toepassingen voor eenmalige aanmelding u de mogelijkheid om met behulp van Azure AD regel matig rollover (update)-wacht woorden te gebruiken. Het systeem maakt gebruik van grote, complexe wacht woorden die de beveiliging van het account verhogen. De beheerder kan eenvoudig toegang tot een toepassing verlenen of intrekken, weet wie toegang heeft tot het account en wie het in het verleden heeft bezocht.

Azure AD biedt ondersteuning voor gedeelde accounts voor een Enter prise Mobility Suite (EMS) of Azure AD Premium licentie abonnement, in alle typen wacht woord-toepassingen voor eenmalige aanmelding. U kunt accounts delen voor duizenden vooraf geïntegreerde toepassingen in de toepassings galerie en u kunt uw eigen toepassing voor wachtwoord verificatie toevoegen met [aangepaste SSO-apps](../manage-apps/what-is-single-sign-on.md).

Azure AD-functies die het delen van accounts mogelijk maken omvatten:

* [Eenmalige aanmelding met wacht woord](../manage-apps/sso-options.md#password-based-sso)
* Aanmeldings agent voor eenmalige aanmelding
* [Groeps toewijzing](groups-self-service-management.md)
* Aangepaste wacht woord-apps
* [App-gebruiks Dashboard/-rapporten](../authentication/howto-sspr-reporting.md)
* Portals voor toegang door eind gebruikers
* [App-proxy](../manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)

## <a name="sharing-an-account"></a>Een account delen

Als u Azure AD wilt gebruiken om een account te delen, moet u het volgende doen:

* Een app- [Galerie](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) voor toepassingen of een [aangepaste toepassing](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/) toevoegen
* De toepassing configureren voor een wacht woord single Sign-On (SSO)
* Gebruik [toewijzing op basis van een groep](groups-saasapps.md) en selecteer de optie om een gedeelde referentie in te voeren

U kunt uw gedeelde account ook veiliger maken met Multi-Factor Authentication (MFA) (meer informatie over het [beveiligen van toepassingen met Azure AD](../authentication/concept-mfa-howitworks.md)) en u kunt de mogelijkheid voor het beheren van toegang tot de toepassing overdragen met behulp van het selfservice groeps beheer van [Azure AD](groups-self-service-management.md) .

## <a name="next-steps"></a>Volgende stappen

* [Application Management in Azure Active Directory](../manage-apps/what-is-application-management.md) (Toepassingsbeheer in Azure Active Directory)
* [Apps beveiligen met voorwaardelijke toegang](../../active-directory-b2c/overview.md)
* [Self-service voor groeps beheer/SSAA](groups-self-service-management.md)