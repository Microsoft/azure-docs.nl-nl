---
title: Gebruikers die zijn gemarkeerd voor risico op het Azure Active Directory Portal | Microsoft Docs
description: Meer informatie over het beveiligingsrapport over gebruikers voor wie wordt aangegeven dat ze risico lopen in de Azure Active Directory-portal
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23bd7a26bc6215f50a2860ab4aac84f1b46301f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92308949"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Gebruikers herstellen voor wie wordt aangegeven dat ze risico lopen in de Azure Active Directory-portal

Met de beveiligings rapporten in de Azure Active Directory (Azure AD) kunt u de kans op gemanipuleerde gebruikers accounts in uw omgeving meten. Een gebruiker voor wie wordt aangegeven dat deze risico loopt is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Microsoft spant zich in om uw omgevingen veilig te houden. Als onderdeel van deze inspanningen controleert Microsoft doorlopend activiteiten die ongebruikelijk zijn of consistent met bekende aanvalspatronen. 

Als ongebruikelijke activiteiten die kunnen wijzen op ongeoorloofde toegang tot een aantal van uw gebruikers accounts, worden gedetecteerd, ontvangt u meldingen waarmee u actie kunt ondernemen. Dit betekent niet dat de eigen systemen van micro soft zijn aangetast.

## <a name="access-the-users-flagged-for-risk-report"></a>Gebruikers bekijken voor wie wordt aangegeven dat ze risico lopen

U kunt gebruikers voor wie wordt aangegeven dat ze risico lopen, bekijken via het [rapport gebruikers die risico](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) lopen in de Azure Portal. Als u geen Azure AD hebt, kunt u zich gratis aanmelden op [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) . 

Vanuit de gebruikers die zijn gemarkeerd voor risico rapport, kunt u de volgende acties uitvoeren voor elke gebruiker:

- Een tijdelijk wachtwoord genereren
- Vereisen dat gebruikers veilig hun wachtwoord opnieuw instellen de volgende keer dat ze zich aanmelden
- Het gebruikersrisico afwijzen zonder een herstelactie uit te voeren.

Zie gebruikers voor wie wordt aangegeven dat [ze risico lopen op beveiligings rapport](../identity-protection/overview-identity-protection.md)voor meer informatie.

### <a name="azure-ad-subscription-for-microsoft-365-customers"></a>Azure AD-abonnement voor Microsoft 365 klanten

U kunt ook uw Microsoft 365 referenties gebruiken om toegang te krijgen tot het **Azure-beheer centrum**. Nadat u de toegang tot Azure AD hebt geactiveerd, wordt u doorgestuurd naar de Azure AD-portal. Bij een basisabonnement worden minder details in rapporten verstrekt. Aanvullende gegevens en analyses zijn verkrijgbaar voor abonnees van Azure Premium.

Om toegang te krijgen tot de gebruikers die zijn **gemarkeerd voor risico** rapporten in het Microsoft 365-beheer centrum:

1.  Selecteer in het navigatie menu aan de linkerkant **beheer centrums**. 
2.  Selecteer **Azure AD**.
3.  Meld u aan bij **Azure Active Directory-beheercentrum**.
4.  Als er boven aan de pagina een banner wordt weer gegeven met **de melding de nieuwe portal**, selecteert u de koppeling.
4.  Selecteer in het navigatie menu aan de linkerkant **Azure Active Directory**. 
5.  Selecteer in het navigatie deel venster de optie gebruikers die zijn **gemarkeerd voor risico** vanuit het gedeelte **beveiliging** .

## <a name="remediation-actions"></a>Herstelacties

Onderneem de volgende acties om de betreffende accounts te herstellen en uw omgeving te beveiligen:

1.  [Controleer de juiste gegevens](https://aka.ms/MFAValid) voor multi-factor Authentication en self-service voor het opnieuw instellen van wacht woorden. 
2.  [Schakel multi-factor Authentication in](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication) voor alle gebruikers. 
3.  Gebruik dit [herstel script](https://aka.ms/remediate) voor elk betrokken account om automatisch de volgende stappen uit te voeren: 

    a. Wacht woord opnieuw instellen om het account te beveiligen en actieve sessies te beëindigen.

    b. Verwijder gemachtigden voor een postvak.

    c. Schakel regels voor het doorsturen van e-mail voor externe domeinen uit.

    d. Verwijder de eigenschap voor het globaal doorsturen van e-mail voor het postvak uit.

    e. Schakel MFA voor het account van de gebruiker in.

    f. Stel de wachtwoordcomplexiteit voor het account in op Hoog.

    g. Schakel postvakcontrole in.

    h. Maak een audit logboek voor de beheerder om dit te controleren.

4. Onderzoek uw Microsoft 365-Tenant en een andere IT-infra structuur, inclusief een overzicht van alle Tenant instellingen, gebruikers accounts en de configuratie-instellingen per gebruiker voor mogelijke wijzigingen. Controleer op de aanwezigheid van indicatoren voor persistentiemethoden, evenals indicatoren dat een indringer gebruik kan hebben gemaakt van een eerste aangrijpingspunt om VPN-referenties te verkrijgen, of toegang tot andere organisatieaccounts. 

5.  Als onderdeel van uw onderzoek moet u overwegen of u overheids instanties wilt informeren, waaronder het afdwingen van de regelgeving.

Daarnaast dient u:

- Lees en implementeer deze [richt lijnen voor het adresseren van ongebruikelijke activiteiten](/archive/blogs/office365security/how-to-fix-a-compromised-hacked-microsoft-office-365-account). 
- [Schakel de controle pijplijn](/archive/blogs/office365security/using-office-365-activity-data-to-improve-your-cybersecurity-stance-and-capability) in om u te helpen bij het analyseren van de activiteit in uw Tenant. Zodra het controle archief is voltooid, wordt het gevuld met activiteiten Logboeken. Op dit moment kunt u ook gebruikmaken van de [Zoek-en onderzoek resource van het beveiligings-en nalevings centrum](https://aka.ms/sccsearch). 
- Bekijk voor alle postvakken de machtigingen voor gemachtigden en de regels voor het doorsturen van e-mail. U kunt dit [PowerShell-script](https://aka.ms/delegateforwardrules) gebruiken om deze taak uit te voeren. 

## <a name="next-steps"></a>Volgende stappen

* [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)
* [Gebruikers voor wie wordt aangegeven dat ze risico lopen](../identity-protection/overview-identity-protection.md)