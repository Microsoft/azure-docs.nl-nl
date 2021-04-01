---
title: Aanvraag proces & meldingen-beheer van rechten van Azure AD
description: Meer informatie over het aanvraag proces voor een toegangs pakket en wanneer e-mail meldingen worden verzonden in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb84eae2db01180865db54250598f45c7b2ddca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746232"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Aanvraag proces en e-mail meldingen in het beheer van rechten van Azure AD

Wanneer een gebruiker een aanvraag indient bij een toegangs pakket, begint een proces met het leveren van die toegangs aanvraag. Het Azure AD-rechts beheer verzendt e-mail meldingen naar goed keurders en aanvragers wanneer er belang rijke gebeurtenissen optreden tijdens het proces. In dit artikel worden het aanvraag proces en de e-mail meldingen beschreven die worden verzonden.

## <a name="request-process"></a>Aanvraag proces

Een gebruiker die toegang tot een toegangs pakket nodig heeft, kan een toegangs aanvraag indienen. Afhankelijk van de configuratie van het beleid, kan de aanvraag een goed keuring vereisen. Wanneer een aanvraag wordt goedgekeurd, begint een proces met het toewijzen van de gebruikers toegang tot elke resource in het toegangs pakket. In het volgende diagram ziet u een overzicht van het proces en de verschillende statussen:

![Diagram goedkeurings proces](./media/entitlement-management-process/request-process.png)

| Staat | Beschrijving |
| --- | --- |
| Ingediend | Gebruiker een aanvraag indient. |
| Goed keuring in behandeling | Als voor het beleid voor een toegangs pakket goed keuring is vereist, wordt een aanvraag verplaatst naar wachtende goed keuring. |
| Verlopen | Als geen goed keurders een aanvraag binnen de time-out van de goedkeurings aanvraag goed keuren, verloopt de aanvraag. Als u het opnieuw wilt proberen, moet de gebruiker de aanvraag opnieuw verzenden. |
| Geweigerd | Goed keurder weigert een aanvraag. |
| Goedgekeurd | Goed keurder keurt een aanvraag goed. |
| Leveren | Aan de gebruiker is **geen** toegang toegewezen tot alle resources in het toegangs pakket. Als dit een externe gebruiker is, heeft de gebruiker mogelijk nog geen toegang tot de resource directory. Ze hebben de toestemming ook mogelijk niet geaccepteerd. |
| Afgeleverd | Aan de gebruiker is toegang toegewezen tot alle resources in het toegangs pakket. |
| Toegang uitgebreid | Als de uitbrei dingen in het beleid zijn toegestaan, wordt de toewijzing door de gebruiker uitgebreid. |
| Toegang is verlopen | De toegang van de gebruiker tot het toegangs pakket is verlopen. Als u opnieuw toegang wilt krijgen, moet de gebruiker een aanvraag indienen. |

## <a name="email-notifications"></a>E-mailmeldingen

Als u een goed keurder bent, worden er e-mail meldingen verzonden wanneer u een toegangs aanvraag moet goed keuren. U ontvangt ook meldingen wanneer een toegangs aanvraag is voltooid. U ontvangt ook e-mail meldingen die de status van uw aanvraag aangeven als u een aanvrager bent.

In de volgende diagrammen ziet u wanneer deze e-mail meldingen worden verzonden naar de goed keurders of de aanvrager. Raadpleeg de [tabel e-mail meldingen](entitlement-management-process.md#email-notifications-table) om het overeenkomende nummer te vinden voor de e-mail meldingen die in de diagrammen worden weer gegeven.

### <a name="first-approvers-and-alternate-approvers"></a>Eerste goed keurders en alternatieve goed keurders
In het volgende diagram ziet u de ervaring van eerste goed keurders en alternatieve goed keurders en de e-mail meldingen die ze ontvangen tijdens het aanvraag proces:

![Eerste en alternatieve goed keurders proces stroom](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Aanvragers
In het volgende diagram ziet u de ervaring van aanvragers en de e-mail meldingen die worden ontvangen tijdens het aanvraag proces:

![Proces stroom van de aanvrager](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>2-fase goedkeuring
In het volgende diagram ziet u de ervaring van fase 1 en fase 2-goed keurders en de e-mail meldingen die worden ontvangen tijdens het aanvraag proces:

![goedkeurings proces voor twee fasen stroom](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabel met e-mail meldingen
De volgende tabel bevat meer details over elk van deze e-mail meldingen. Als u deze e-mail berichten wilt beheren, kunt u regels gebruiken. In Outlook kunt u bijvoorbeeld regels maken om de e-mail berichten naar een map te verplaatsen als het onderwerp woorden uit deze tabel bevat:

| # | E-mail onderwerp | Na verzen ding | Verzonden naar |
| --- | --- | --- | --- |
| 1 | Actie vereist: doorgestuurde aanvraag goed keuren of weigeren voor *[date]* | Deze e-mail wordt verzonden naar fase-1 alternatieve goed keurders (nadat de aanvraag is geëscaleerd) om actie te ondernemen. | Fase-1 alternatieve goed keurders |
| 2 | Actie vereist: aanvraag goed keuren of weigeren voor *[date]* | Deze e-mail wordt verzonden naar de eerste goed keurder als escalatie is uitgeschakeld, om actie te ondernemen. | Eerste goed keurder |
| 3 | Herinnering: de aanvraag goed keuren of weigeren voor *[date]* voor *[aanvrager]* | Deze herinnerings-e-mail wordt verzonden naar de eerste fiatteur als escalatie is uitgeschakeld. In het e-mail bericht wordt gevraagd om actie te ondernemen als dat niet het geval is. | Eerste goed keurder |
| 4 | De aanvraag wordt goedgekeurd of geweigerd door *[time]* op *[date]* | Deze e-mail wordt verzonden naar de eerste goed keurder (als escalatie is ingeschakeld) om actie te ondernemen. | Eerste goed keurder |
| 5 | Herinnering voor actie vereist: Hiermee wordt de aanvraag goedgekeurd of geweigerd voor [ *date]* voor *[aanvrager]* | Deze herinnerings-e-mail wordt verzonden naar de eerste fiatteur als escalatie is ingeschakeld. In het e-mail bericht wordt gevraagd om actie te ondernemen als dat niet het geval is. | Eerste goed keurder |
| 6 | De aanvraag is verlopen voor *[access_package]* | Deze e-mail wordt verzonden naar de eerste goed keurder en fase 1 alternatieve goed keurders nadat de aanvraag is verlopen. | Eerste goed keurder, fase-1 alternatieve goed keurders |
| 7 | Aanvraag goedgekeurd voor *[aanvrager]* naar *[access_package]* | Deze e-mail wordt verzonden naar de eerste goed keurder en fase 1 alternatieve goed keurders wanneer de aanvraag is voltooid. | Eerste goed keurder, fase-1 alternatieve goed keurders |
| 8 | Aanvraag goedgekeurd voor *[aanvrager]* naar *[access_package]* | Deze e-mail wordt verzonden naar de eerste goed keurder en fase 1 alternatieve goed keurders van een aanvraag met twee fasen wanneer de fase-1-aanvraag is goedgekeurd. | Eerste goed keurder, fase-1 alternatieve goed keurders |
| 9 | Aanvraag geweigerd tot *[access_package]* | Dit e-mail bericht wordt naar de aanvrager verzonden wanneer de aanvraag wordt geweigerd | Requestor |
| 10 | Uw aanvraag is verlopen voor *[access_package]* | Deze e-mail wordt aan het einde van een aanvraag met één of twee fasen verzonden naar de aanvrager. In het e-mail bericht wordt de aanvrager gewaarschuwd dat de aanvraag is verlopen. | Requestor |
| 11 | Actie vereist: aanvraag goed keuren of weigeren voor *[date]* | Deze e-mail wordt verzonden naar de tweede goed keurder als escalatie is uitgeschakeld, om actie te ondernemen. | Tweede goed keurder |
| 12 | Herinnering voor actie vereist: de aanvraag goed keuren of weigeren voor *[date]* | Deze herinnerings-e-mail wordt verzonden naar de tweede goed keurder als de escalatie is uitgeschakeld. De melding vraagt hen om actie te ondernemen als ze nog niet zijn uitgevoerd. | Tweede goed keurder |
| 13 | Actie vereist: de aanvraag goed keuren of weigeren voor [ *date]* voor *[aanvrager]* | Deze e-mail wordt verzonden naar de tweede goed keurder als escalatie is ingeschakeld, om actie te ondernemen. | Tweede goed keurder |
| 14 | Herinnering voor actie vereist: Hiermee wordt de aanvraag goedgekeurd of geweigerd voor [ *date]* voor *[aanvrager]* | Deze herinnerings-e-mail wordt verzonden naar de tweede goed keurder als escalatie is ingeschakeld. De melding vraagt hen om actie te ondernemen als ze nog niet zijn uitgevoerd. | Tweede goed keurder |
| 15 | Actie vereist: doorgestuurde aanvraag goed keuren of weigeren voor *[date]* | Deze e-mail wordt verzonden naar fase 2 alternatieve goed keurders, als escalatie is ingeschakeld, om actie te ondernemen. | Fase-2 alternatieve goed keurders |
| 16 | Aanvraag goedgekeurd voor *[aanvrager]* naar *[access_package]* | Deze e-mail wordt verzonden naar de tweede goed keurder en fase 2 alternatieve goed keurders bij het goed keuren van de aanvraag. | Tweede goed keurder, fase 2 alternatieve goed keurders |
| 17 | Een aanvraag is verlopen voor *[access_package]* | Deze e-mail wordt verzonden naar de tweede goed keurder of alternatieve goed keurders, nadat de aanvraag is verlopen. | Tweede goed keurder, fase 2 alternatieve goed keurders |
| 18 | U hebt nu toegang tot *[access_package]* | Dit e-mail bericht wordt naar de eind gebruikers verzonden om hun toegang te gebruiken. | Requestor |
| 19 | Toegang uitbreiden voor *[access_package]* op *[date]* | Deze e-mail wordt verzonden naar de eind gebruikers voordat hun toegang verloopt. | Requestor |
| 20 | De toegang is beëindigd voor *[access_package]* | Deze e-mail wordt verzonden naar de eind gebruikers nadat de toegang is verlopen. | Requestor |

### <a name="access-request-emails"></a>E-mail berichten voor toegang

Wanneer een aanvrager een toegangs aanvraag indient voor een toegangs pakket dat is geconfigureerd om goed keuring te vereisen, ontvangt alle goed keurders die zijn toegevoegd aan het beleid een e-mail melding met de details van de aanvraag. De details in het e-mail bericht zijn onder andere: naam van de aanvrager en zakelijke rechtvaardiging; en de aangevraagde start-en eind datum van toegang (indien opgegeven). De details worden ook vermeld wanneer de aanvraag is verzonden en wanneer de aanvraag verloopt.

Het e-mail bericht bevat een koppeling goed keurders kunnen klikken om naar mijn toegang te gaan om de toegangs aanvraag goed te keuren of te weigeren. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar de eerste fiatteur of tweede goed keurder (als de 2-fase goedkeuring is ingeschakeld) om een toegangs aanvraag te volt ooien:

![Aanvraag voor toegang tot pakket e-mail goed keuren](./media/entitlement-management-shared/approver-request-email.png)

Goed keurders kunnen ook een herinnerings-e-mail ontvangen. Het e-mail bericht vraagt de goed keurder een beslissing te nemen over de aanvraag. Hier volgt een voor beeld van een e-mail melding die de goed keurder ontvangt om actie te ondernemen:

![E-mail voor herinnerings toegang](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>E-mails met alternatieve goed keurders aanvragen

Als de instelling alternatieve goed keurders is ingeschakeld en de aanvraag nog steeds in behandeling is, wordt deze doorgestuurd. Alternatieve goed keurders ontvangen een e-mail om de aanvraag goed te keuren of te weigeren. U kunt alternatieve goed keurders inschakelen in fase-1 en fase 2. Hier volgt een voor beeld van een e-mail bericht van de melding dat de alternatieve goed keurders worden ontvangen:

![E-mail van alternatieve goed keurders aanvragen](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Zowel de goed keurder als de alternatieve goed keurders kunnen de aanvraag goed keuren of weigeren.

### <a name="approved-or-denied-emails"></a>Goedgekeurde of geweigerde e-mail berichten

 Wanneer een goed keurder een toegangs aanvraag ontvangt die door een aanvrager is ingediend, kan hij of zij de toegangs aanvraag goed keuren of weigeren. De fiatteur moet een zakelijke rechtvaardiging toevoegen voor hun beslissing. Hier volgt een voor beeld van een e-mail bericht dat wordt verzonden naar goed keurders en alternatieve goed keurders nadat een aanvraag is goedgekeurd:

![Goedgekeurde aanvraag voor toegang tot pakket-e-mail](./media/entitlement-management-process/approver-request-email-approved.png)

Wanneer een toegangs aanvraag wordt goedgekeurd en de toegang is ingericht, wordt er een e-mail melding verzonden naar de aanvrager dat ze nu toegang tot het toegangs pakket hebben. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een aanvrager wanneer ze toegang krijgen tot een toegangs pakket:

![E-mail adres goedgekeurde aanvrager toegang](./media/entitlement-management-process/requestor-email-approved.png)

Wanneer een toegangs aanvraag wordt geweigerd, wordt een e-mail melding verzonden naar de aanvrager. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een aanvrager wanneer hun toegangs aanvraag wordt geweigerd:

![E-mail afgewezen aanvraag geweigerd](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>2-fase goedkeurings aanvragen e-mail berichten

Als de 2-fase goedkeuring is ingeschakeld, moeten ten minste twee goed keurders de aanvraag goed keuren, één van elke fase voordat de aanvrager toegang kan krijgen.

Tijdens fase 1 ontvangt de eerste goed keurder het e-mail bericht voor de toegangs aanvraag en een beslissing nemen. Als de aanvraag wordt goedgekeurd, ontvangt alle eerste goed keurders en alternatieve goed keurders in fase 1 (als escalatie is ingeschakeld) een melding dat fase-1 is voltooid. Hier volgt een voor beeld van een e-mail bericht dat wordt verzonden wanneer fase-1 is voltooid:

![e-mail adres voor toegang tot 2 werk plaatsen](./media/entitlement-management-process/approver-request-email-2stage.png)

Nadat de eerste of alternatieve goed keurders de aanvraag in fase 1 hebben goedgekeurd, wordt fase 2 gestart. Tijdens fase 2 ontvangt de tweede goed keurder de e-mail melding voor de toegangs aanvraag. Nadat de tweede goed keurder of alternatieve goed keurders in fase 2 (als escalatie is ingeschakeld) besluiten om de aanvraag goed te keuren of te weigeren, worden e-mail meldingen verzonden naar de eerste en tweede goed keurders, en alle alternatieve goed keurders in fase-1 en fase 2, evenals de aanvrager.

### <a name="expired-access-request-emails"></a>Verlopen e-mail berichten voor toegang

Toegangs aanvragen kunnen verlopen als geen fiatteur de aanvraag heeft goedgekeurd of geweigerd. 

Wanneer de aanvraag de geconfigureerde verloop datum bereikt en verloopt, kan deze niet meer worden goedgekeurd of geweigerd door de goed keurders. Hier volgt een voor beeld van een e-mail bericht van de melding die wordt verzonden naar alle eerste, tweede (indien goed keuring in twee fasen is ingeschakeld) en alternatieve goed keurders:

![E-mail met verlopen toegang aanvragen voor goed keurders](./media/entitlement-management-process/approver-request-email-expired.png)

Er wordt ook een e-mail melding verzonden naar de aanvrager, waarin wordt gemeld dat hun toegangs aanvraag is verlopen en dat de toegangs aanvraag opnieuw moet worden ingediend. In het volgende diagram ziet u de ervaring van de aanvrager en de e-mail meldingen die ze ontvangen wanneer ze aanvragen om toegang uit te breiden:

![De aanvrager breidt de toegangs proces stroom uit](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een aanvrager wanneer hun toegangs aanvraag is verlopen:

![E-mail met verlopen verzoeken voor aanvrager](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Volgende stappen

- [Toegang aanvragen tot een toegangs pakket](entitlement-management-request-access.md)
- [Toegangs aanvragen goed keuren of weigeren](entitlement-management-request-approve.md)
