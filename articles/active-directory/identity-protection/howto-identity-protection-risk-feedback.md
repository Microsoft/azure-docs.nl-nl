---
title: Feedback geven over Risico's in Azure Active Directory Identity Protection
description: Hoe en waarom moet u feedback geven over risico detecties voor identiteits beveiliging.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939ffc9e2a8fb8ce3a25dc212e3df34dc6bb2ec1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88950354"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Procedure: risico feedback in Azure AD Identity Protection geven

Met Azure AD Identity Protection kunt u feedback geven over de risico beoordeling. In het volgende document vindt u de scenario's waarin u feedback wilt geven over de risico beoordeling van Azure AD Identity Protection en hoe deze worden opgenomen.

## <a name="what-is-a-detection"></a>Wat is een detectie?

Een detectie van identiteits beveiliging is een indicator van verdachte activiteiten van een risico voor identiteits Risico's. Deze verdachte activiteiten worden risico detecties genoemd. Deze detecties op basis van id's kunnen worden gebaseerd op heuristiek, machine learning of kunnen afkomstig zijn van partner producten. Deze detecties worden gebruikt om het risico en de gebruikers Risico's van het aanmelden te bepalen.

* Gebruikers risico duidt op de kans dat een identiteit wordt aangetast.
* Aanmeldings risico geeft aan dat de kans dat een aanmelding is aangetast (bijvoorbeeld omdat de aanmelding niet is geautoriseerd door de eigenaar van de identiteit).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Waarom zou ik risico feedback kunnen geven op risico beoordelingen van Azure AD? 

Er zijn verschillende redenen waarom u Azure AD-risico feedback moet geven:

- **U hebt de gebruiker van Azure AD of de risico beoordeling voor het aanmelden onjuist aangetroffen**. Bijvoorbeeld, een aanmelding die wordt weer gegeven in het rapport Risk ante aanmeldingen is onschadelijk en alle detecties voor die aanmelding waren valse positieven.
- **U hebt gevalideerd dat de evaluatie van de gebruiker van Azure AD of het risico voor aanmelding is** geslaagd. Een voor beeld dat wordt weer gegeven in het rapport Risk ante aanmeldingen was inderdaad schadelijk en u wilt dat Azure AD weet dat alle detecties voor die aanmelding op de echte positieven zijn.
- **U hebt het risico op die gebruiker buiten Azure AD Identity Protection hersteld** en u wilt het risico niveau van de gebruiker bijwerken.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Hoe maakt Azure AD gebruik van mijn risico feedback?

Azure AD gebruikt uw feedback om het risico van de onderliggende gebruiker en/of het aanmelden en de nauw keurigheid van deze gebeurtenissen bij te werken. Deze feedback helpt u bij het beveiligen van de eind gebruiker. Als u bijvoorbeeld hebt bevestigd dat er is geknoeid met de aanmelding, verhoogt Azure AD direct het risico van de gebruiker en het geaggregeerde risico van de aanmelding (niet realtime risico) tot hoog. Als deze gebruiker is opgenomen in het beleid voor gebruikers Risico's om gebruikers met een hoog risico af te dwingen hun wacht woord veilig opnieuw in te stellen, wordt de gebruiker automatisch hersteld wanneer deze zich de volgende keer aanmeldt.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Hoe geef ik feedback over Risico's en wat er gebeurt onder de motorkap?

Hier volgen de scenario's en mechanismen voor het geven van risico feedback voor Azure AD.

| Scenario | Hoe kunt u feedback geven? | Wat gebeurt er onder de motorkap? | Notities |
| --- | --- | --- | --- |
| **Het aanmelden is niet aangetast (fout positief)** <br> In het rapport Risk ante aanmeldingen wordt een aanmeldings risico (risico status = gevaar) weer gegeven, maar de aanmelding is niet aangetast. | Selecteer de aanmelding en klik op aanmelden bevestigen veilig. | Azure AD verplaatst het aggregatie risico van het aanmelden naar geen [risico status = bevestigd veilig; Risico niveau (aggregatie) =-] en maakt de impact op het risico van de gebruiker ongedaan. | Op dit moment is de optie veilig aanmelden bevestigen alleen beschikbaar in het rapport Risk ante aanmeldingen. |
| **Aanmelding is aangetast (ware positieve)** <br> In het rapport Risk ante aanmeldingen ziet u een aanmeld risico (risico status = loopt risico) met laag risico [risico niveau (aggregatie) = low] en die aanmelding werd inderdaad aangetast. | Selecteer de aanmelding en klik op bevestigen dat er is geknoeid. | Azure AD verplaatst het aggregatie risico van het aanmelden en het gebruikers risico op hoog [risico status = bevestigd is aangetast; Risico niveau = hoog]. | Op dit moment is de optie voor misbruik van een aanmelding bevestigen alleen beschikbaar in het rapport Risk ante aanmeldingen. |
| **Gebruikers vervalsen (waar positief)** <br> In het rapport Risk ante gebruikers wordt een gebruiker weer gegeven met een risico status (risico staat = risico), met laag risico [risico niveau = laag] en die gebruiker inderdaad aangetast werd. | Selecteer de gebruiker en klik op ' Bevestig de gebruiker '. | Azure AD verplaatst het risico van de gebruiker naar hoog [Risk status = bevestigd is aangetast; Risico niveau = hoog] en voegt een nieuwe detectie toe die door de beheerder is bevestigd. | De optie ' verkraakte gebruikers bevestigen ' is momenteel alleen beschikbaar in het rapport Risk ante gebruikers. <br> De detectie ' door de beheerder bevestigde gebruiker heeft een vermoedt ' wordt weer gegeven op het tabblad risico detecties die niet zijn gekoppeld aan een aanmelding in het rapport Risk ante gebruikers. |
| **Door de gebruiker opgelost buiten Azure AD Identity Protection (ware positieve + opgelost)** <br> In het rapport Risk ante gebruikers wordt een gebruiker met een risico weer gegeven en de gebruiker is vervolgens buiten Azure AD Identity Protection hersteld. | 1. Selecteer de gebruiker en klik op geïnfecteerde gebruiker bevestigen. (Dit proces bevestigt bij Azure AD dat de gebruiker inderdaad heeft geknoeid.) <br> 2. wacht op het risico niveau van de gebruiker om naar hoog te gaan. (Deze tijd geeft Azure AD de tijd die nodig is om de bovenstaande feedback te nemen naar de risico-engine.) <br> 3. Selecteer de gebruiker en klik op gebruikers risico negeren. (Dit proces bevestigt bij Azure AD dat de gebruiker niet meer heeft geknoeid.) |  Azure AD verplaatst het gebruikers risico naar geen [risico status = genegeerd; Risico niveau =-] en sluit het risico op alle bestaande aanmeldingen die actief risico lopen. | Als u op ' gebruikers risico negeren ' klikt, wordt het risico voor de gebruiker en eerdere aanmeldingen gesloten. Deze actie kan niet ongedaan worden gemaakt. |
| **De gebruiker is niet aangetast (fout positief)** <br> Het rapport Risk ante gebruikers wordt weer gegeven met een gebruiker met een risico, maar de gebruiker wordt niet geknoeid. | Selecteer de gebruiker en klik op gebruikers risico negeren. (Dit proces bevestigt bij Azure AD dat de gebruiker niet heeft geknoeid.) | Azure AD verplaatst het gebruikers risico naar geen [risico status = genegeerd; Risico niveau =-]. | Als u op ' gebruikers risico negeren ' klikt, wordt het risico voor de gebruiker en eerdere aanmeldingen gesloten. Deze actie kan niet ongedaan worden gemaakt. |
| Ik wil het risico van de gebruiker sluiten, maar ik weet niet zeker of de gebruiker is aangetast/veilig. | Selecteer de gebruiker en klik op gebruikers risico negeren. (Dit proces bevestigt bij Azure AD dat de gebruiker niet meer heeft geknoeid.) | Azure AD verplaatst het gebruikers risico naar geen [risico status = genegeerd; Risico niveau =-]. | Als u op ' gebruikers risico negeren ' klikt, wordt het risico voor de gebruiker en eerdere aanmeldingen gesloten. Deze actie kan niet ongedaan worden gemaakt. U kunt de gebruiker het beste herstellen door te klikken op wacht woord opnieuw instellen of de gebruiker vragen om hun referenties veilig opnieuw in te stellen of te wijzigen. |

Feedback over gebruikers risico detecties in identiteits beveiliging wordt offline verwerkt en kan enige tijd duren om bij te werken. De kolom risico verwerkings status biedt de huidige status van de feedback verwerking.

![Status van risico verwerking voor rapport Risk ante gebruikers](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Volgende stappen

- [Naslag informatie over Azure Active Directory Identity Protection risico detectie](./concept-identity-protection-risks.md)