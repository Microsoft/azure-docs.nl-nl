---
title: Verificatie methode voor beveiligings vragen-Azure Active Directory
description: Meer informatie over het gebruik van beveiligings vragen in Azure Active Directory om aanmeldings gebeurtenissen te verbeteren en te beveiligen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7229dabd690e5932fbd297992e09782eda85a002
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744139"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Verificatie methoden in Azure Active Directory-beveiligings vragen

Beveiligings vragen worden niet als een verificatie methode gebruikt tijdens een aanmeldings gebeurtenis. In plaats daarvan kunnen beveiligings vragen worden gebruikt tijdens het SSPR-proces (self-service voor wachtwoord herstel) om te bevestigen wie u bent. Beheerders accounts kunnen geen beveiligings vragen als verificatie methode gebruiken met SSPR.

Wanneer gebruikers zich registreren voor SSPR, wordt u gevraagd de verificatie methoden te kiezen die u wilt gebruiken. Als ze ervoor kiezen om beveiligings vragen te gebruiken, kiezen ze uit een set vragen om te vragen en ze vervolgens hun eigen antwoorden te geven.

![Scherm afbeelding van de Azure Portal waarin de verificatie methoden en-opties voor beveiligings vragen worden weer gegeven](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Beveiligings vragen worden privé opgeslagen en beveiligd op een gebruikers object in de Directory en kunnen alleen worden beantwoord door gebruikers tijdens de registratie. Het is niet mogelijk dat een beheerder de vragen of antwoorden van een gebruiker kan lezen of wijzigen.

Beveiligings vragen kunnen minder veilig zijn dan andere methoden omdat sommige mensen de antwoorden op de vragen van een andere gebruiker kunnen kennen. Als u beveiligings vragen met SSPR gebruikt, is het raadzaam deze te gebruiken in combi natie met een andere methode. Een gebruiker kan worden gevraagd om de Microsoft Authenticator app of telefoon verificatie te gebruiken om hun identiteit te verifiëren tijdens het SSPR-proces en om alleen beveiligings vragen te kiezen als ze hun telefoon of geregistreerd apparaat niet hebben.

## <a name="predefined-questions"></a>Vooraf gedefinieerde vragen

De volgende vooraf gedefinieerde beveiligings vragen zijn beschikbaar voor gebruik als een verificatie methode met SSPR. Al deze beveiligings vragen worden vertaald en gelokaliseerd in de volledige set Microsoft 365 talen op basis van de browser land instelling van de gebruiker:

* In welke stad hebt u uw eerste partner ontmoet?
* In welke stad hebben uw ouders elkaar ontmoet?
* In welke stad woont uw dichtstbijzijnde broer of zus?
* In welke stad is uw vader geboren?
* In welke stad is uw eerste taak?
* In welke stad is uw moeder geboren?
* In welke plaats was u op 2000 van het nieuwjaarsdag?
* Wat is de achternaam van uw favoriete docent op de hoogste school?
* Wat is de naam van een college dat u hebt toegepast, maar niet?
* Wat is de naam van de plaats waar u uw eerste bruilofts receptie hebt gehouden?
* Wat is de tweede naam van uw vader?
* Wat is uw favoriete levens middelen?
* Wat zijn de voor-en achternaam van uw en achternaam?
* Wat is de tweede naam van uw moeder?
* Wat is de maand en het jaar van uw oudste broer of zus? (bijvoorbeeld november 1985)
* Wat is de tweede naam van uw oudste broer of zus?
* Wat zijn de voor-en achternaam van uw Paternal groot vader?
* Wat is de tweede naam van uw jongste broer of zus?
* Op welke school hebt u de zesde kwaliteit bijgewoond?
* Wat zijn de voor-en achternaam van de beste vriend van uw jeugd?
* Wat zijn de voor-en achternaam van uw eerste belang rijke andere?
* Wat is de achternaam van uw favoriete school docent?
* Wat was het merk en model van uw eerste auto of motor?
* Wat is de naam van de eerste school die u hebt bijgewoond?
* Wat is de naam van het zieken huis waarin u bent geboren?
* Wat is de naam van de straat van uw eerste huis land?
* Wat is de naam van uw jeugd held?
* Wat is de naam van uw favoriete dierlijke dier?
* Wat is de naam van uw eerste huis dier?
* Wat was de bijnaam van uw kinderen?
* Wat was uw favoriete sport op de hoge school?
* Wat is uw eerste taak?
* Wat zijn de laatste vier cijfers van het telefoon nummer van uw kinderen?
* Wanneer u jonger was, wat wilt u doen wanneer u ingroeit?
* Wie is de meest beroemde persoon die u ooit hebt ontmoet?

## <a name="custom-security-questions"></a>Aangepaste beveiligings vragen

Voor extra flexibiliteit kunt u uw eigen aangepaste beveiligings vragen definiëren. De maximale lengte van een aangepaste beveiligings vraag is 200 tekens.

Aangepaste beveiligings vragen worden niet automatisch gelokaliseerd zoals bij de standaard beveiligings vragen. Alle aangepaste vragen worden weer gegeven in dezelfde taal als die in de gebruikers interface voor beheer, zelfs als de browser land instelling van de gebruiker verschillend is. Als u gelokaliseerde vragen nodig hebt, moet u de vooraf gedefinieerde vragen gebruiken.

## <a name="security-question-requirements"></a>Vereisten voor beveiligings vragen

Voor zowel standaard als aangepaste beveiligings vragen gelden de volgende vereisten en beperkingen:

* De minimale antwoord teken limiet is drie tekens.
* De maximale antwoord teken limiet is 40 tekens.
* Gebruikers kunnen niet meer dan één keer dezelfde vraag beantwoorden.
* Gebruikers kunnen niet hetzelfde antwoord op meer dan één vraag opgeven.
* Elke tekenset kan worden gebruikt voor het definiëren van de vragen en de antwoorden, met inbegrip van Unicode-tekens.
* Het aantal gedefinieerde vragen moet groter zijn dan of gelijk zijn aan het aantal vragen dat moet worden geregistreerd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [zelf studie voor selfservice voor wachtwoord herstel (SSPR)][tutorial-sspr]om aan de slag te gaan.

Zie [hoe Azure AD self-service password reset werkt][concept-sspr]voor meer informatie over SSPR-concepten.

Meer informatie over het configureren van verificatie methoden met behulp van de [Microsoft Graph rest API bèta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
