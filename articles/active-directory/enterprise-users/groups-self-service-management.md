---
title: Selfservice voor groepsbeheer instellen - Azure Active Directory | Microsoft Docs
description: Beveiligings groepen of Microsoft 365 groepen maken en beheren in Azure Active Directory en beveiligings groep of Microsoft 365 groepslid maatschappen aanvragen
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6b2b8e3374c362f937aa5cfe106e8da9f9aa39f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547998"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Self-service groeps beheer instellen in Azure Active Directory 

U kunt gebruikers in staat stellen om hun eigen beveiligings groepen of Microsoft 365 groepen te maken en te beheren in Azure Active Directory (Azure AD). De eigenaar van de groep kan lidmaatschaps aanvragen goed keuren of weigeren en kan het beheer van groepslid maatschap overdragen. Self-service groeps beheer functies zijn niet beschikbaar voor beveiligings groepen met e-mail functionaliteit of distributie lijsten.

## <a name="self-service-group-membership-defaults"></a>Standaard waarden voor groepslid maatschap van self-service

Wanneer beveiligings groepen worden gemaakt in de Azure Portal of Azure AD Power shell gebruiken, kunnen alleen de eigen aren van de groep het lidmaatschap bijwerken. Beveiligings groepen die zijn gemaakt door self-service in het [toegangs venster](https://account.activedirectory.windowsazure.com/r#/joinGroups) en alle Microsoft 365 groepen kunnen worden toegevoegd voor alle gebruikers, hetzij door een eigenaar goedgekeurd of automatisch goedgekeurd. In het toegangs venster kunt u de lidmaatschaps opties wijzigen wanneer u de groep maakt.

Groepen die zijn gemaakt in | Standaard gedrag van beveiligings groep | Standaard gedrag van Microsoft 365-groep
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | Alleen eigen aars kunnen leden toevoegen<br>Zichtbaar, maar niet beschikbaar voor deelname in het toegangs venster | Openen om lid te worden van alle gebruikers
[Azure-portal](https://portal.azure.com) | Alleen eigen aars kunnen leden toevoegen<br>Zichtbaar, maar niet beschikbaar voor deelname in het toegangs venster<br>De eigenaar wordt niet automatisch toegewezen bij het maken van een groep | Openen om lid te worden van alle gebruikers
[Toegangs venster](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Openen om lid te worden van alle gebruikers<br>Lidmaatschaps opties kunnen worden gewijzigd wanneer de groep wordt gemaakt | Openen om lid te worden van alle gebruikers<br>Lidmaatschaps opties kunnen worden gewijzigd wanneer de groep wordt gemaakt

## <a name="self-service-group-management-scenarios"></a>Scenario's voor Self-service voor groeps beheer

* **Gedelegeerd groepsbeheer** Een voorbeeld is een beheerder die toegang beheert tot een SaaS-toepassing die het bedrijf gebruikt. Het beheren van deze gebruiksrechten is omslachtig en daarom vraagt de beheerder de eigenaar van het bedrijf om een nieuwe groep te maken. De beheerder wijst de toegang voor de toepassing toe aan de nieuwe groep en voegt alle personen die al toegang hebben tot de toepassing toe aan de groep. De bedrijfseigenaar kan meer gebruikers toevoegen en deze gebruikers worden automatisch ingericht op de toepassing. De bedrijfseigenaar hoeft niet op de beheerder te wachten om de toegang voor gebruikers te beheren. Als de beheerder dezelfde machtiging verleent aan een manager in een andere bedrijfs groep, kan die persoon ook de toegang beheren voor hun eigen groeps leden. De eigenaar van het bedrijf en de Manager kunnen elkaars groepslid maatschappen niet zien of beheren. De beheerder kan nog steeds alle gebruikers die toegang tot de toepassing hebben zien en zonodig de toegangsrechten blokkeren.
* **Self-service voor groepsbeheer** Een voorbeeld van dit scenario zijn twee gebruikers die allebei SharePoint Online-sites hebben die ze onafhankelijk hebben ingesteld. Ze willen elkaars teams toegang geven tot hun sites. Hiervoor kunnen ze in Azure AD een groep maken en in SharePoint selecteert elk van hen de groep waartoe ze op hun sites toegang willen verlenen. Wanneer iemand toegang wil, kan dit worden aangevraagd bij het toegangspaneel en na goedkeuring wordt automatisch toegang verleend tot beide SharePoint Online-sites. Later beslist één van hen dat alle personen die de site openen ook toegang moeten krijgen tot een specifieke SaaS-toepassing. De beheerder van de SaaS-toepassing kan toegangsrechten toevoegen voor de toepassing aan de SharePoint Online-site. Vanaf dan verlenen alle goedgekeurde verzoeken toegang tot de twee SharePoint Online-sites en ook tot deze SaaS-toepassing.

## <a name="make-a-group-available-for-user-self-service"></a>Een groep beschikbaar maken voor self-service door gebruikers

1. Meld u aan bij het [beheercentrum van Azure AD](https://aad.portal.azure.com) met een account met globale beheerdersrechten voor de directory.
1. Selecteer **groepen** en selecteer vervolgens **algemene** instellingen.
1. Stel **eigen aren in het toegangs venster voor het beheren van groepslid maatschappen in** op **Ja**.
1. Stel **toegang beperken tot groepen in het toegangs venster in** op **Nee**.
1. Als u instelt dat **gebruikers beveiligings groepen kunnen maken in azure-portals** of **gebruikers kunnen Microsoft 365 groepen maken in azure-portals**

    - **Ja**: alle gebruikers in uw Azure AD-organisatie mogen nieuwe beveiligings groepen maken en leden toevoegen aan deze groepen. Deze nieuwe groepen zullen ook verschijnen in het Toegangsvenster voor alle andere gebruikers. Als de beleids instelling voor de groep toestaat, kunnen andere gebruikers aanvragen maken om lid te worden van deze groepen
    - **Nee**: gebruikers kunnen geen groepen maken en kunnen bestaande groepen waarvan ze een eigenaar zijn, niet wijzigen. Ze kunnen echter nog steeds de lidmaatschappen van die groepen beheren en aanvragen van andere gebruikers om lid te worden van hun groep goedkeuren.

U kunt ook **eigen aren gebruiken die leden als groeps eigenaren kunnen toewijzen in de Azure Portal** om meer nauw keurigere toegangs controle te verkrijgen over selfservice groeps beheer voor uw gebruikers.

Wanneer gebruikers groepen kunnen maken, mogen alle gebruikers in uw organisatie nieuwe groepen maken en kunnen ze als standaard eigenaar leden toevoegen aan deze groepen. U kunt geen personen opgeven die hun eigen groepen kunnen maken. U kunt alleen individuen opgeven om een groeps eigenaar te maken van een andere groep.

> [!NOTE]
> Een Azure Active Directory Premium-licentie (P1 of P2) is vereist voor gebruikers om deel te nemen aan een beveiligings groep of Microsoft 365 groep en voor eigen aars om lidmaatschaps aanvragen goed te keuren of te weigeren. Zonder een Azure Active Directory Premium licentie kunnen gebruikers nog steeds hun groepen beheren in het toegangs paneel, maar ze kunnen geen groep maken waarvoor eigenaars goed keuring is vereist in het toegangs venster en ze kunnen niet aanvragen om lid te worden van een groep.

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

* [Toegang tot resources beheren met Azure Active Directory-groepen](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory cmdlets voor het configureren van groepsinstellingen](../enterprise-users/groups-settings-cmdlets.md)
* [Application Management in Azure Active Directory](../manage-apps/what-is-application-management.md) (Toepassingsbeheer in Azure Active Directory)
* [Wat is Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Uw on-premises identiteiten integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
