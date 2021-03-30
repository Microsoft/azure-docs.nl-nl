---
title: Bevoorrechte Azure AD-groepen beheren in Privileged Identity Management (PIM) | Microsoft Docs
description: Leden en eigenaren van groepen met uitgebreide toegang beheren in Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759781fd61cd42d05f2823d390e99d128dd2fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512389"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>Mogelijkheden voor het beheer van Azure AD-groepen met uitgebreide toegang (preview-versie)

In Privileged Identity Management (PIM) kunt u nu de geschiktheid voor lidmaatschap of eigendom van groepen met uitgebreide toegang toewijzen. Met deze preview-versie kunt u ingebouwde Azure AD-rollen (Azure Active Directory) toewijzen aan cloudgroepen en PIM gebruiken voor het beheren van de geschiktheid en activering van groepsleden en eigenaren. Zie [Cloudgroepen gebruiken voor het beheren van roltoewijzingen in Azure Active Directory (preview)](../roles/groups-concept.md) voor meer informatie over groepen waarin in Azure AD rollen kunnen worden toegewezen.

>[!Important]
> Als u een bevoorrechte toegangsgroep wilt toewijzen aan een rol voor beheerderstoegang tot Exchange, het beveiligings- en nalevingscentrum of SharePoint, gebruikt u de Azure AD-portal **Rollen en beheerders** en niet de interface voor bevoorrechte toegangsgroepen om ervoor te zorgen dat de gebruiker of groep in aanmerking komt voor activering in de groep.

## <a name="require-different-policies-for-each-role-assignable-group"></a>Verschillende beleidsregels vereisen voor elke groep waaraan rollen kunnen worden toegewezen

Sommige organisaties gebruiken hulpprogramma's zoals samenwerking via Azure AD Business-to-Business (B2B) om hun partners uit te nodigen als gasten in hun Azure AD-organisatie. In plaats van slechts één Just-In-Time-beleid voor alle toewijzingen aan een bevoorrechte rol, kunt u twee verschillende groepen met uitgebreide toegang maken, met elk hun eigen beleid. U kunt minder strikte vereisten afdwingen voor uw vertrouwde medewerkers en strengere vereisten, zoals een goedkeuringswerkstroom voor uw partners, wanneer ze activering aanvragen in hun toegewezen groep.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Meerdere roltoewijzingen in één aanvraag activeren

Met de preview voor groepen met uitgebreide toegang kunt u workloadspecifieke beheerders snelle toegang geven tot meerdere rollen met één Just-In-Time-aanvraag. Stelt u zich voor dat uw beheerders uit laag 3 Just-In-Time-toegang nodig hebben tot de rollen Exchange-beheerder, Office Apps-beheerder, Team-beheerder en Zoekbeheerder om incidenten dagelijks grondig te onderzoeken. Vóór vandaag zouden hiervoor vier opeenvolgende aanvragen nodig zijn. Dit is een proces dat enige tijd in beslag neemt. In plaats daarvan kunt u een groep maken met de naam 'Office-beheerders laag 3' waaraan rollen kunnen worden toegewezen, deze toewijzen aan elk van de vier eerder genoemde rollen (of ingebouwde rollen van Azure AD) en deze inschakelen voor bevoegde toegang in de sectie Activiteit van de groep. Wanneer bevoegde toegang eenmaal is ingeschakeld, kunt u de Just-In-Time-instellingen voor leden van de groep configureren en uw beheerders en eigenaren toewijzen als hiervoor in aanmerking komend. Wanneer het lidmaatschap van de beheerders wordt verhoogd naar de groep, worden ze lid van alle vier de Azure AD-rollen.

## <a name="extend-and-renew-group-assignments"></a>Groepstoewijzingen verlengen en vernieuwen

Nadat u de tijdgebonden eigenaar- of lidtoewijzingen hebt ingesteld, is de eerste vraag die in u opkomt: wat gebeurt er als een toewijzing verloopt? In deze nieuwe versie bieden we twee opties voor dit scenario:

- Verlengen: wanneer een roltoewijzing bijna is verlopen, kan de gebruiker Privileged Identity Management gebruiken om een verlenging aan te vragen voor de roltoewijzing
- Vernieuwen: wanneer een roltoewijzing al is verlopen, kan de gebruiker Privileged Identity Management gebruiken om vernieuwing aan te vragen voor de roltoewijzing

Voor beide door de gebruiker geïnitieerde acties is goedkeuring van een Globale beheerder of een Beheerder voor bevoorrechte rollen vereist. Beheerders hoeven het verlopen van toewijzingen niet meer zelf te beheren. Ze hoeven alleen te wachten op de aanvraag voor verlenging of vernieuwing en deze goedkeuren als de aanvraag geldig is.

## <a name="next-steps"></a>Volgende stappen

- [Een eigenaar of lid van een groep met uitgebreide toegang toewijzen](groups-assign-member-owner.md)
- [Activeringsaanvragen voor leden en eigenaren van een groep met uitgebreide toegang goedkeuren of weigeren](groups-approval-workflow.md)
