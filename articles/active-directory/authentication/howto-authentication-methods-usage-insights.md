---
title: Gebruik van verificatie methoden & Insights-Azure Active Directory
description: Rapportage over de selfservice voor wachtwoord herstel van Azure AD en het gebruik van Multi-Factor Authentication authenticatie methode
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8633ae0d23650eb12ee2e1fdcd355864dfd8b6c3
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743629"
---
# <a name="authentication-methods-usage--insights-preview"></a>Gebruik van de verificatie methoden & Insights (preview-versie)

Gebruik & Insights maakt het mogelijk om te begrijpen hoe verificatie methoden voor functies zoals Azure AD Multi-Factor Authentication en self-service voor wachtwoord herstel in uw organisatie werken. Deze rapportage mogelijkheid biedt uw organisatie de mogelijkheid om te begrijpen welke methoden worden geregistreerd en hoe ze worden gebruikt.

## <a name="permissions-and-licenses"></a>Machtigingen en licenties

De volgende rollen hebben toegang tot het gebruik en inzichten:

- Hoofdbeheerder
- Beveiligingslezer
- Beveiligingsbeheer
- Rapportenlezer

Er is geen aanvullende licentie vereist voor toegang tot gebruik en inzichten. De licentie gegevens voor Azure AD Multi-Factor Authentication en self-service voor het opnieuw instellen van wacht woorden (SSPR) zijn te vinden op de [Azure Active Directory-prijs site](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Uitleg

Voor toegang tot het gebruik van de verificatie methode en inzichten:

1. Blader naar [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory** het gebruik van  >  **wacht woord opnieuw instellen**  >  **& inzichten**.
1. Vanuit de overzichten van de **registratie** of het **gebruik** kunt u ervoor kiezen om de vooraf gefilterde rapporten te openen om te filteren op basis van uw behoeften.

![Overzicht van gebruiks & Insights](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Als u rechtstreeks toegang wilt krijgen tot gebruik & Insights, gaat u naar [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) . Met deze koppeling gaat u naar het registratie overzicht.

De tegels gebruikers die zijn geregistreerd, gebruikers ingeschakeld en gebruikers kunnen de volgende registratie gegevens voor uw gebruikers tonen:

- Geregistreerd: een gebruiker wordt als geregistreerd beschouwd als ze (of een beheerder) voldoende verificatie methoden hebben geregistreerd om te voldoen aan het SSPR-of Multi-Factor Authentication-beleid van uw organisatie.
- Ingeschakeld: een gebruiker wordt als ingeschakeld beschouwd als ze binnen het bereik van het SSPR-beleid vallen. Als SSPR is ingeschakeld voor een groep, wordt de gebruiker als ingeschakeld beschouwd als ze zich in die groep bevinden. Als SSPR is ingeschakeld voor alle gebruikers, worden alle gebruikers in de Tenant (exclusief gasten) als ingeschakeld beschouwd.
- Compatibel: een gebruiker wordt beschouwd als mogelijk als deze beide zijn geregistreerd en ingeschakeld. Deze status betekent dat ze SSPR op elk gewenst moment kunnen uitvoeren, indien nodig.

Als u op een van deze tegels of de weer gegeven inzichten klikt, gaat u naar een vooraf gefilterde lijst met registratie gegevens.

Het **rapport registraties** op het tabblad **registratie** toont het aantal geslaagde en mislukte verificatie methode registraties op basis van de verificatie methode. In de grafiek **opnieuw instellen** op het tabblad **gebruik** wordt het aantal geslaagde en mislukte authenticaties weer gegeven tijdens de verificatie methode voor het opnieuw instellen van het wacht woord.

Als u op een van de grafieken klikt, gaat u naar een vooraf gefilterde lijst met registratie-of reset-gebeurtenissen.

Met behulp van het besturings element in de rechter bovenhoek kunt u het datum bereik wijzigen voor de controle gegevens die in de registraties worden weer gegeven en grafieken opnieuw instellen op 24 uur, 7 dagen of 30 dagen.

### <a name="registration-details"></a>Registratie Details

Door te klikken op de gebruikers die zijn **geregistreerd**, **gebruikers ingeschakeld** of door **gebruikers geschikte** tegels of inzichten, wordt u naar de registratie gegevens geleid.

In het rapport registratie Details wordt de volgende informatie weer gegeven voor elke gebruiker:

- Naam
- Gebruikersnaam
- Registratie status (alle, geregistreerd, niet geregistreerd)
- Ingeschakelde status (alle, ingeschakeld, niet ingeschakeld)
- Status mogelijk (alles, mogelijk, niet mogelijk)
- Methoden (app-melding, app-code, telefoon gesprek, SMS, E-mail, beveiligings vragen)

Met de besturings elementen aan de bovenkant van de lijst kunt u zoeken naar een gebruiker en de lijst met gebruikers filteren op basis van de weer gegeven kolommen.

### <a name="reset-details"></a>Details opnieuw instellen

Als u op de registraties klikt of opnieuw instelt, worden de gegevens opnieuw ingesteld.

Het rapport opnieuw instellen Details toont de registratie-en reset gebeurtenissen van de afgelopen 30 dagen, inclusief:

- Naam
- Gebruikersnaam
- Functie (alle, registratie, opnieuw instellen)
- Verificatie methode (app-melding, app-code, telefoon gesprek, Office-oproep, SMS, E-mail, beveiligings vragen)
- Status (alle, geslaagd, mislukt)

Met de besturings elementen aan de bovenkant van de lijst kunt u zoeken naar een gebruiker en de lijst met gebruikers filteren op basis van de weer gegeven kolommen.

## <a name="limitations"></a>Beperkingen

De gegevens die in deze rapporten worden weer gegeven, worden tot 60 minuten vertraagd. Het veld ' laatst vernieuwd ' komt voor in de Azure Portal om te bepalen hoe recent uw gegevens zijn.

Gebruik en Insights-gegevens zijn geen vervanging voor de activiteiten rapporten van Azure AD Multi-Factor Authentication of informatie die is opgenomen in het Azure AD-aanmeld rapport.

Het rapport kan momenteel niet worden gefilterd om externe gebruikers uit te sluiten.

## <a name="next-steps"></a>Volgende stappen

- [Werken met de verificatie methoden gebruiks rapport-API](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Verificatie methoden kiezen voor uw organisatie](concept-authentication-methods.md)
- [Gecombineerde registratie-ervaring](concept-registration-mfa-sspr-combined.md)