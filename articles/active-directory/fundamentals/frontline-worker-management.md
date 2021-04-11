---
title: Frontline worker-Azure Active Directory
description: Meer informatie over de beheer mogelijkheden voor Frontline-werk nemers die worden geboden via de portal mijn personeel.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 32cee4ca0558166c44ff83c5ce9d61360e79e535
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969176"
---
# <a name="frontline-worker-management"></a>Frontline worker-beheer

Frontline werk nemers om meer dan 80 procent van het wereld wijde personeel. Daarom hebben Frontline-werk nemers echter niet de hulp middelen om hun veeleisende taken iets eenvoudiger te maken. Frontline worker-werk beheer brengt digitale trans formatie over aan het hele Frontline-personeel. Het personeel kan managers, Frontline werk nemers, bewerkingen en IT bevatten.

Frontline worker-werk nemers bieden het Frontline personeel door de volgende activiteiten eenvoudiger te maken:
- Veelgebruikte IT-taken stroom lijnen met mijn mede werkers
- Eenvoudige onboarding van Frontline-werk nemers via vereenvoudigde verificatie
- Naadloos inrichten van gedeelde apparaten en beveiligde afmelding bij Frontline-werk nemers

## <a name="delegated-user-management-through-my-staff"></a>Gedelegeerd gebruikers beheer via mijn personeel

Azure Active Directory (Azure AD) biedt de mogelijkheid om gebruikers beheer te delegeren aan Frontline-managers via de [Portal mijn personeel](../roles/my-staff-configure.md), waardoor kost bare tijd kan worden bespaard en risico's kunnen worden gereduceerd. Door eenvoudig opnieuw instellen van wacht woorden en telefoon beheer rechtstreeks vanuit de Store of de fabriek in te scha kelen, kunnen managers toegang verlenen aan werk nemers zonder de aanvraag via de Help Desk, IT of bewerkingen te routeren.

![Gedelegeerd gebruikers beheer in de portal mijn personeel](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>Versneld voorbereiden met vereenvoudigde verificatie

Mijn personeel stelt frontline-managers ook in staat om de telefoon nummers van hun team leden te registreren voor [SMS-aanmelding](../authentication/howto-authentication-sms-signin.md). In veel verticale Frontline-werk nemers behouden een combi natie van lokale gebruikers naam en wacht woord, een oplossing die vaak omslachtig, kostbaar en fout gevoelig is. Wanneer verificatie met behulp van SMS-aanmelding wordt ingeschakeld, kunnen Frontline-werk rollen zich aanmelden met [eenmalige aanmelding (SSO)](../manage-apps/what-is-single-sign-on.md) voor micro soft-teams en andere apps met alleen hun telefoon nummer en een eenmalige wachtwoord code (OTP) die via SMS wordt verzonden. Zo kunt u zich eenvoudig en veilig aanmelden voor Frontline-werk rollen, zodat u snel toegang hebt tot de apps die ze het meest nodig hebben.

![SMS-aanmelding](media/concept-fundamentals-frontline-worker/sms-signin.png)

Frontline-beheerders kunnen ook een MHS-toepassing (Managed Home Screen) gebruiken om werk nemers toegang te geven tot een specifieke set toepassingen op hun door intune geregistreerde Android-apparaten. De toegewezen apparaten zijn Inge schreven bij de [modus gedeeld Azure AD-apparaat](../develop/msal-shared-devices.md). Wanneer het is geconfigureerd in de kiosk modus voor meerdere apps in de micro soft Endpoint Manager-console (MEM), wordt MHS automatisch als het standaard Start scherm op het apparaat gestart en wordt de eind gebruiker als het *enige* Start scherm weer gegeven. Zie How to [Configure micro soft Managed Home Screen app for Android Enter prise](/mem/intune/apps/app-configuration-managed-home-screen-app)(Engelstalig) voor meer informatie.

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Beveiligde afmelding bij Frontline-werk nemers van gedeelde apparaten

Veel bedrijven gebruiken gedeelde apparaten zodat Frontline-werk nemers het voorraad beheer en verkooppunt transacties kunnen uitvoeren, zonder de IT-belasting van het inrichten en bijhouden van afzonderlijke apparaten. Bij het afmelden van een gedeeld apparaat is het eenvoudig voor een Frontline-werk nemer om zich op een veilige manier af te melden bij alle apps op een gedeeld apparaat, voordat het opnieuw wordt door gegeven aan een hub of dit door te geven aan een team in de volgende ploeg. Micro soft teams is een van de apps die momenteel worden ondersteund op gedeelde apparaten en Hiermee kunnen Frontline-werk rollen taken weer geven die aan hen zijn toegewezen. Zodra een werk nemer zich afmeldt bij een gedeeld apparaat, worden alle bedrijfs gegevens gewist door intune en Azure AD, zodat het apparaat veilig kan worden overgedragen aan de volgende koppeling. U kunt ervoor kiezen om deze mogelijkheid te integreren in al uw line-of-Business [IOS](../develop/msal-ios-shared-devices.md) -en [Android](../develop/msal-android-shared-devices.md) -apps met behulp van de [micro soft-verificatie bibliotheek](../develop/msal-overview.md).

![Afmelding van gedeeld apparaat](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [de gebruikers documentatie van mijn personeel](../user-help/my-staff-team-manager.md)voor meer informatie over gedelegeerd gebruikers beheer.
- Raadpleeg de zelf studie over het [configureren van SAP SuccessFactors voor het inrichten van Active Directory gebruikers](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)voor inkomend gebruikers van SAP SuccessFactors.
- Zie de zelf studie over het [configureren van workday voor het automatisch inrichten van gebruikers](../saas-apps/workday-inbound-tutorial.md)voor de inkomende gebruikers inrichting van de werkdag.
