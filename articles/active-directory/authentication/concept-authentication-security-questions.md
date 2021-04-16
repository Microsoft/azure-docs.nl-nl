---
title: Verificatiemethode voor beveiligingsvragen - Azure Active Directory
description: Meer informatie over het gebruik van beveiligingsvragen in Azure Active Directory om aanmeldingsgebeurtenissen te verbeteren en beveiligen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 841391778e0fb8c00f503aa0cc79b5562661e309
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530343"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Verificatiemethoden in Azure Active Directory - beveiligingsvragen

Beveiligingsvragen worden niet gebruikt als verificatiemethode tijdens een aanmeldingsgebeurtenis. In plaats daarvan kunnen beveiligingsvragen worden gebruikt tijdens het selfservice voor wachtwoord opnieuw instellen (SSPR) om te bevestigen wie u bent. Beheerdersaccounts kunnen geen beveiligingsvragen gebruiken als verificatiemethode met SSPR.

Wanneer gebruikers zich registreren voor SSPR, wordt hen gevraagd de te gebruiken verificatiemethoden te kiezen. Als ze ervoor kiezen om beveiligingsvragen te gebruiken, kiezen ze uit een set vragen om om te vragen en geven ze vervolgens hun eigen antwoorden.

![Schermopname van de Azure Portal met verificatiemethoden en opties voor beveiligingsvragen](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Beveiligingsvragen worden privé en veilig opgeslagen in een gebruikersobject in de directory en kunnen alleen worden beantwoord door gebruikers tijdens de registratie. Een beheerder kan de vragen of antwoorden van een gebruiker niet lezen of wijzigen.

Beveiligingsvragen kunnen minder veilig zijn dan andere methoden, omdat sommige mensen mogelijk de antwoorden op vragen van een andere gebruiker weten. Als u beveiligingsvragen gebruikt met SSPR, is het raadzaam deze te gebruiken in combinatie met een andere methode. Een gebruiker kan worden gevraagd de Microsoft Authenticator-app of telefoonverificatie te gebruiken om zijn of haar identiteit te verifiëren tijdens het SSPR-proces en alleen beveiligingsvragen kiezen als hij of zij geen telefoon of geregistreerd apparaat bij zich heeft.

## <a name="predefined-questions"></a>Vooraf gedefinieerde vragen

De volgende vooraf gedefinieerde beveiligingsvragen zijn beschikbaar voor gebruik als verificatiemethode met SSPR. Al deze beveiligingsvragen worden vertaald en gelokaliseerd in de volledige set Microsoft 365 talen op basis van de taal van de browser van de gebruiker:

* In welke stad hebt u kennis gemaakt met uw eerste partner/partner?
* In welke stad hebben uw ouders vergaderd?
* In welke stad woont uw dichtstbijzijnde sibling?
* In welke stad is uw vader geboren?
* In welke stad was uw eerste taak?
* In welke stad is uw moeder geboren?
* In welke stad was u op het nieuwe jaar 2000?
* Wat is de achternaam van uw favoriete docent op de middelbare school?
* Wat is de naam van een universiteit waarvoor u een aanvraag hebt gedaan, maar die u niet hebt gebruikt?
* Wat is de naam van de plaats waar u uw eerste ontvangst hebt gehouden?
* Wat is de middelste naam van uw vader?
* Wat is uw favoriete eten?
* Wat is de voor- en achternaam van uw moeder?
* Wat is de middelste naam van uw moeder?
* Wat is de verjaardagsmaand en het jaar van uw oudste sibling? (bijvoorbeeld november 1985)
* Wat is de middelste naam van uw oudste sibling?
* Wat is de voor- en achternaam van uw paternale moeder?
* Wat is de middelste naam van uw favoriete sibling?
* Op welke school hebt u de zesde klas gedaan?
* Wat was de voor- en achternaam van uw beste vriend?
* Wat was de voor- en achternaam van uw eerste partner?
* Wat was de achternaam van uw favoriete docent op de middelbare school?
* Wat was het make- en model van uw eerste auto of motor?
* Wat is de naam van de eerste school die u hebt bezocht?
* Wat was de naam van het ziekenhuis waarin u bent geboren?
* Wat was de naam van de straat van uw eerste huis?
* Wat was de naam van uw held?
* Wat was de naam van uw favoriete dier?
* Wat was de naam van uw eerste huisdier?
* Wat was uw favoriete bijnaam?
* Wat was uw favoriete sport op de middelbare school?
* Wat was uw eerste taak?
* Wat zijn de laatste vier cijfers van uw telefoonnummer?
* Wat wilde u zijn toen u nog jong was?
* Wie is de beroemde persoon die u ooit hebt gezien?

## <a name="custom-security-questions"></a>Aangepaste beveiligingsvragen

Voor extra flexibiliteit kunt u uw eigen aangepaste beveiligingsvragen definiëren. De maximale lengte van een aangepaste beveiligingsvraag is 200 tekens.

Aangepaste beveiligingsvragen worden niet automatisch gelokaliseerd, zoals bij de standaardbeveiligingsvragen. Alle aangepaste vragen worden weergegeven in dezelfde taal als ze zijn ingevoerd in de gebruikersinterface met beheerdersaccounts, zelfs als de taal van de browser van de gebruiker anders is. Als u gelokaliseerde vragen nodig hebt, moet u de vooraf gedefinieerde vragen gebruiken.

## <a name="security-question-requirements"></a>Vereisten voor beveiligingsvraag

Voor zowel standaard- als aangepaste beveiligingsvragen gelden de volgende vereisten en beperkingen:

* De minimale limiet voor het antwoordteken is drie tekens.
* De maximumlimiet voor het antwoordteken is 40 tekens.
* Gebruikers kunnen dezelfde vraag niet meer dan één keer beantwoorden.
* Gebruikers kunnen niet hetzelfde antwoord geven op meer dan één vraag.
* Elke tekenset kan worden gebruikt om de vragen en antwoorden te definiëren, inclusief Unicode-tekens.
* Het aantal gedefinieerde vragen moet groter zijn dan of gelijk zijn aan het aantal vragen dat nodig is om te registreren.

## <a name="next-steps"></a>Volgende stappen

Zie de zelfstudie voor selfservice voor wachtwoord opnieuw instellen [(SSPR) om][tutorial-sspr]aan de slag te gaan.

Zie How [Azure AD self-service password reset works][concept-sspr](Hoe self-service voor wachtwoord opnieuw instellen van Azure AD werkt) voor meer informatie over SSPR-concepten.

Meer informatie over het configureren van verificatiemethoden met behulp [van Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
