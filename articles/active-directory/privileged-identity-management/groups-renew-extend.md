---
title: Vernieuw de verlopen groeps eigenaar van leden toewijzingen in Privileged Identity Management-Azure AD | Microsoft Docs
description: Meer informatie over het uitbreiden of vernieuwen van aan rollen toewijs bare groeps toewijzingen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87505989"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Uitgebreide toegangs groeps toewijzingen uitbreiden of vernieuwen (preview) in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) biedt besturings elementen voor het beheren van de toegangs-en toewijzings levenscyclus voor geprivilegieerde toegangs groepen. Beheerders kunnen rollen toewijzen met behulp van de begin-en eind datum-eigenschappen. Wanneer het einde van de toewijzing voldoet, stuurt Privileged Identity Management e-mail meldingen naar de betrokken gebruikers of groepen. Er worden ook e-mail meldingen verzonden naar beheerders van de bron om ervoor te zorgen dat de juiste toegang wordt gehandhaafd. Toewijzingen kunnen worden vernieuwd en blijven tot 30 dagen zichtbaar in de status verlopen, zelfs als de toegang niet is uitgebreid.

## <a name="who-can-extend-and-renew"></a>Wie kan uitbreiden en verlengen

Alleen beheerders van de resource kunnen de toewijzingen van groepen met uitgebreide toegang uitbreiden of vernieuwen. De betrokken gebruiker of groep kan aanvragen om toewijzingen uit te breiden die bijna verlopen en aanvragen om te vernieuwen van toewijzingen die al zijn verlopen.

## <a name="when-notifications-are-sent"></a>Wanneer meldingen worden verzonden

Privileged Identity Management stuurt e-mail meldingen naar beheerders en betrokken gebruikers van de toewijzingen van geprivilegieerde toegangs groepen die verlopen:

- Binnen 14 dagen vóór de verval datum
- Eén dag vóór de verval datum
- Wanneer een toewijzing verloopt

Beheerders ontvangen meldingen wanneer een gebruiker of groep aanvragen een verlopen of verlopen toewijzing uitbreidt of vernieuwt. Wanneer een beheerder de aanvraag heeft omgezet, worden alle beheerders en de aanvragende gebruiker op de hoogte gesteld van de goed keuring of weigering.

## <a name="extend-group-assignments"></a>Groeps toewijzingen uitbreiden

De volgende stappen beschrijven het proces voor het aanvragen, oplossen of beheren van een uitbrei ding of verlenging van een groeps toewijzing.

### <a name="self-extend-expiring-assignments"></a>Verlopende toewijzingen automatisch uitbreiden

Gebruikers die zijn toegewezen aan een groep met verhoogde toegang kunnen de verlopende groeps toewijzingen rechtstreeks uitbreiden vanuit het tabblad **in aanmerking komende** of **actief** op de pagina **toewijzingen** voor de groep. Gebruikers of groepen kunnen aanvragen om in aanmerking komende en actieve toewijzingen uit te breiden die in de komende 14 dagen verlopen.

![Pagina mijn rollen pagina's in aanmerking komende assgnments met een actie kolom](media/groups-renew-extend/self-extend-group-assignment.png)

Wanneer de eind datum van de toewijzing binnen 14 dagen valt, is de opdracht **uitbreiden** beschikbaar. Als u een uitbrei ding van een groeps toewijzing wilt aanvragen, selecteert u **uitbreiden** om het aanvraag formulier te openen.

![Deel venster groeps toewijzing uitbreiden met een reden en Details](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>We raden u aan om de details van de uitbrei ding op te nemen en te bepalen hoe lang de uitbrei ding moet worden verleend (als u deze informatie hebt).

Binnen een paar seconden ontvangen beheerders een e-mail melding waarin wordt gevraagd of de aanvraag voor de verlenging wordt gecontroleerd. Als er al een aanvraag om uit te breiden is ingediend, wordt er een Azure-melding weer gegeven in de portal.

Als u de status van de aanvraag wilt weer geven of deze wilt annuleren, opent u de pagina **aanvragen in behandeling** voor de groeps toewijzing.

![Groep privileged Access Group-aanvragen in behandeling met de koppeling die u wilt annuleren](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Door beheerder goedgekeurde uitbrei ding

Wanneer een gebruiker of groep een aanvraag indient om een groeps toewijzing uit te breiden, ontvangen beheerders een e-mail melding die de details van de oorspronkelijke toewijzing en de reden voor de aanvraag bevat. De melding bevat een rechtstreekse koppeling naar de aanvraag die de beheerder kan goed keuren of weigeren.

Naast het gebruik van de koppeling van e-mail kunnen beheerders aanvragen goed keuren of weigeren door naar de Privileged Identity Management beheer portal te gaan en **aanvragen goed keuren** te selecteren in het linkerdeel venster.

![Privileged Access Group Assignments-aanvragen goed keuren pagina lijst aanvragen en koppelingen om goed te keuren of te weigeren](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

Wanneer een beheerder **goed keuren** of **weigeren** selecteert, worden de details van de aanvraag weer gegeven, samen met een veld om een zakelijke rechtvaardiging voor de audit logboeken te bieden.

![Aanvraag voor groeps toewijzing goed keuren met de reden van de aanvrager, toewijzings type, begin tijd, eind tijd en reden](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

Bij het goed keuren van een aanvraag om een groeps toewijzing uit te breiden, kunnen resource beheerders een nieuwe begin datum, eind datum en toewijzings type kiezen. Het kan nodig zijn om het toewijzings type te wijzigen als de beheerder beperkte toegang wil verlenen om een specifieke taak uit te voeren (bijvoorbeeld één dag). In dit voor beeld kan de beheerder de toewijzing wijzigen van in **aanmerking komend** in **actief**. Dit betekent dat ze toegang kunnen bieden tot de aanvrager zonder dat ze het hoeven te activeren.

### <a name="admin-initiated-extension"></a>Door beheerder geïnitieerde uitbrei ding

Als een gebruiker die is toegewezen aan een groep geen uitbrei ding voor de groeps toewijzing vraagt, kan een beheerder namens de gebruiker een toewijzing uitbreiden. Voor beheer uitbreidingen van groeps toewijzing is geen goed keuring vereist, maar er worden meldingen verzonden naar alle andere beheerders nadat de toewijzing is uitgebreid.

Als u een groeps toewijzing wilt uitbreiden, bladert u naar de weer gave toewijzing in Privileged Identity Management. Zoek de toewijzing waarvoor een extensie is vereist. Selecteer vervolgens **uitbreiden** in de kolom actie.

![Pagina toewijzingen in aanmerking komende groeps toewijzingen met koppelingen om uit te breiden](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>Groeps toewijzingen vernieuwen

Het proces voor het vernieuwen van een verouderde groeps toewijzing is niet hetzelfde als het proces voor het aanvragen van een uitbrei ding. Met de volgende stappen kunnen toewijzingen en beheerders zo nodig de toegang tot verlopen toewijzingen vernieuwen.

### <a name="self-renew"></a>Zelf vernieuwen

Gebruikers die geen toegang meer hebben tot resources, hebben Maxi maal 30 dagen aan verlopen toewijzings geschiedenis. Hiertoe gaat u naar **mijn rollen** in het linkerdeel venster en selecteert u vervolgens het tabblad **verlopen toewijzingen** .

![Pagina mijn rollen-tabblad verlopen toewijzingen](media/groups-renew-extend/groups-renew-from-my-roles.png)

De lijst met toewijzingen die standaard in **aanmerking komende toewijzingen** worden weer gegeven. Gebruik de vervolg keuzelijst om te scha kelen tussen de in aanmerking komende en actieve toewijzingen.

Als u verlenging wilt aanvragen voor een van de groeps toewijzingen in de lijst, selecteert u de actie **vernieuwen** . Geef vervolgens een reden op voor de aanvraag. Het is handig om een duur te bieden naast eventuele aanvullende context of een zakelijke rechtvaardiging die de resource beheerder kan helpen om goed te keuren of te weigeren.

![Deel venster groeps toewijzing vernieuwen met vak reden weer geven](media/groups-renew-extend/groups-renew-request-form.png)

Nadat de aanvraag is verzonden, worden resource beheerders op de hoogte gesteld van een aanvraag in behandeling voor het vernieuwen van een groeps toewijzing.

### <a name="admin-approves"></a>Beheerder keurt

Resource beheerders hebben toegang tot de vernieuwings aanvraag via de koppeling in de e-mail melding of door Privileged Identity Management te openen via de Azure Portal en **aanvragen goed keuren** te selecteren in het linkerdeel venster.

Wanneer een beheerder **goed keuren** of **weigeren** selecteert, worden de details van de aanvraag samen met een veld weer gegeven om een zakelijke rechtvaardiging voor de audit logboeken te bieden.

Bij het goed keuren van een aanvraag voor het vernieuwen van een groeps toewijzing, moeten resource beheerders een nieuwe begin datum, eind datum en toewijzings type opgeven.

## <a name="next-steps"></a>Volgende stappen

- [Aanvragen goed keuren of weigeren voor gemachtigde toegangs groeps toewijzingen in Privileged Identity Management](groups-approval-workflow.md)
- [Instellingen voor geprivilegieerde toegangs groep configureren in Privileged Identity Management](groups-role-settings.md)
