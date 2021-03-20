---
title: Toegang tot de app & bronnen beheren met groepen-Azure AD
description: Meer informatie over het beheren van de toegang tot de Cloud-apps, on-premises apps en resources in uw organisatie met behulp van Azure Active Directory groepen.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 735f838ad4532b140eddcb4ce1ced24fba9a81be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369111"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Toegang tot apps en resources beheren met Azure Active Directory-groepen
Met Azure Active Directory (Azure AD) kunt u groepen gebruiken om de toegang tot uw Cloud-apps, on-premises apps en uw resources te beheren. Uw resources kunnen deel uitmaken van de Azure AD-organisatie, zoals machtigingen voor het beheren van objecten via rollen in azure AD, of externe naar de organisatie, zoals voor SaaS-apps (Software as a Service), Azure-Services, share point-sites en on-premises resources.

>[!NOTE]
> In de Azure Portal ziet u enkele groepen waarvan u de lidmaatschaps-en groeps gegevens niet kunt beheren in de portal:
>
> - Groepen die zijn gesynchroniseerd vanuit on-premises Active Directory, kunnen alleen worden beheerd in on-premises Active Directory.
> - Andere groeps typen, zoals distributie lijsten en beveiligings groepen met e-mail functionaliteit, worden alleen beheerd in Exchange-beheer centrum of Microsoft 365 beheer centrum. U moet zich aanmelden bij Exchange-beheer centrum of Microsoft 365-beheer centrum om deze groepen te beheren.

## <a name="how-access-management-in-azure-ad-works"></a>Hoe toegangs beheer in azure AD werkt

Azure AD helpt u bij het verlenen van toegang tot de resources van uw organisatie door toegangs rechten te bieden voor één gebruiker of voor een volledige Azure AD-groep. Met behulp van groepen kan de resource-eigenaar (of de Azure AD-Directory-eigenaar) een reeks toegangsmachtigingen toewijzen aan alle leden van de groep, in plaats van de rechten één voor één te verstrekken. De resource of de directory-eigenaar kan ook beheer rechten geven voor de leden lijst aan iemand anders, zoals een Afdelings Manager of een helpdesk beheerder, zodat deze persoon, indien nodig, leden kan toevoegen en verwijderen. Zie [groeps eigenaren beheren](active-directory-accessmanagement-managing-group-owners.md) voor meer informatie over het beheren van groeps eigenaren

![Schema van toegangsbeheer in Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Manieren om toegangs rechten toe te wijzen

Er zijn vier manieren om toegangs rechten voor resources aan uw gebruikers toe te wijzen:

- **Directe toewijzing.** De resource-eigenaar wijst de gebruiker rechtstreeks toe aan de resource.

- **Groeps toewijzing.** De resource-eigenaar wijst een Azure AD-groep toe aan de resource, die automatisch alle leden van de groep toegang geeft tot de resource. Groepslid maatschap wordt beheerd door de eigenaar van de groep en de eigenaar van de resource, waardoor de eigenaar leden toevoegen aan of verwijderen uit de groep. Zie [procedure: een groep toevoegen aan of verwijderen uit een andere groep met behulp van de Azure Active Directory-Portal](active-directory-groups-membership-azure-portal.md)voor meer informatie over het toevoegen of verwijderen van groepslid maatschap. 

- **Toewijzing op basis van een regel.** De resource-eigenaar maakt een groep en maakt gebruik van een regel om te definiëren welke gebruikers zijn toegewezen aan een specifieke resource. De regel is gebaseerd op kenmerken die aan afzonderlijke gebruikers zijn toegewezen. De resource-eigenaar beheert de regel en bepaalt welke kenmerken en waarden zijn vereist om toegang tot de bron toe te staan. Zie [een dynamische groep maken en status controleren](../enterprise-users/groups-create-rule.md)voor meer informatie.

    U kunt deze korte video ook bekijken voor een snelle uitleg over het maken en gebruiken van dynamische groepen:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Toewijzing van externe instanties.** De toegang is afkomstig uit een externe bron, zoals een on-premises Directory of een SaaS-app. In dit geval wijst de eigenaar van de resource een groep toe om toegang tot de resource te bieden en vervolgens beheert de externe bron de groeps leden.

   ![Schema van toegangsbeheer](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Kunnen gebruikers lid worden van groepen zonder dat ze worden toegewezen?
Met de groeps eigenaar kunnen gebruikers hun eigen groepen vinden om lid te worden, in plaats van ze toe te wijzen. De eigenaar kan de groep ook zo instellen dat alle gebruikers die lid zijn van of moeten worden goedgekeurd automatisch worden geaccepteerd.

Nadat een gebruiker is gevraagd lid te worden van een groep, wordt de aanvraag doorgestuurd naar de groeps eigenaar. Als het vereist is, kan de eigenaar de aanvraag goed keuren en wordt de gebruiker op de hoogte gesteld van het groepslid maatschap. Als u echter meerdere eigen aren hebt en een van de eigen aars afkeurt, wordt de gebruiker hiervan op de hoogte gesteld, maar wordt deze niet aan de groep toegevoegd. Voor meer informatie en instructies over hoe u uw gebruikers wilt laten lid worden van groepen, Zie [Azure AD instellen, zodat gebruikers kunnen aanvragen om lid te worden van groepen](../enterprise-users/groups-self-service-management.md)

## <a name="next-steps"></a>Volgende stappen
Nu u een beetje van een kennis hebt van toegangs beheer met groepen, begint u met het beheren van uw resources en apps.

- [Een nieuwe groep maken met behulp van Azure Active Directory](active-directory-groups-create-azure-portal.md) of [een nieuwe groep maken en beheren met Power shell-cmdlets](../enterprise-users/groups-settings-v2-cmdlets.md)

- [Groepen gebruiken om toegang toe te wijzen aan een geïntegreerde SaaS-app](../enterprise-users/groups-saasapps.md)

- [Een on-premises groep naar Azure synchroniseren met Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
