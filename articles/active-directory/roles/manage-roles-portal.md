---
title: Azure AD-rollen toewijzen aan gebruikers-Azure Active Directory
description: Meer informatie over het verlenen van toegang aan gebruikers in Azure Active Directory door het toewijzen van Azure AD-rollen.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466639"
---
# <a name="assign-azure-ad-roles-to-users"></a>Azure AD-rollen toewijzen aan gebruikers

U kunt nu alle leden van de beheerders rollen weer geven en beheren in het Azure AD-beheer centrum. Als u regel matig roltoewijzingen beheert, hebt u waarschijnlijk de voor keur. In dit artikel wordt beschreven hoe u Azure AD-rollen toewijst met behulp van het Azure AD-beheer centrum.

## <a name="assign-a-role"></a>Een rol toewijzen

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met beheerders machtigingen voor de globale beheerder of de rol van bevoegdheden.

1. Selecteer **Azure Active Directory**.

1. Selecteer **rollen en beheerders** om de lijst met alle beschik bare rollen weer te geven.

    ![Scherm afbeelding van de pagina rollen en beheerders](./media/manage-roles-portal/roles-and-administrators.png)

1. Selecteer een rol om de bijbehorende toewijzingen te bekijken.

    Om u te helpen de gewenste functie te vinden, kunt u in azure AD subsets van de rollen weer geven op basis van Rolgroepen. Bekijk het **type** filter om alleen de rollen in het geselecteerde type weer te geven.

1. Selecteer **toewijzingen toevoegen** en selecteer vervolgens de gebruikers die u aan deze rol wilt toewijzen.

    Als u iets anders ziet in de volgende afbeelding, leest u de opmerking in [privileged Identity Management (PIM)](#privileged-identity-management-pim) om te controleren of u PIM gebruikt.

    ![lijst met machtigingen voor een beheerdersrol](./media/manage-roles-portal/add-assignments.png)

1. Selecteer **toevoegen** om de rol toe te wijzen.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

U kunt **beheren in PIM** selecteren voor aanvullende beheer mogelijkheden met behulp van [Azure AD privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Beheerders van geprivilegieerde rollen kunnen de toewijzingen ' permanent ' (altijd actief in de rol) wijzigen in in aanmerking komende (in de rol alleen bij verhoogde bevoegdheid). Als u niet beschikt over Privileged Identity Management, kunt u nog steeds **beheren in PIM** selecteren om u aan te melden voor een proef versie. Voor Privileged Identity Management is een [Azure AD Premium P2-licentie plan](../privileged-identity-management/subscription-requirements.md)vereist.

![Scherm opname van de pagina ' gebruikers beheerder-toewijzingen ' met de actie ' beheren in PIM ' geselecteerd](./media/manage-roles-portal/member-list-pim.png)

Als u een globale beheerder of een beheerder van een bevoegde rol bent, kunt u eenvoudig leden toevoegen of verwijderen, de lijst filteren of een lid selecteren om hun actieve toegewezen rollen te zien.

> [!Note]
> Als u een Azure AD Premium P2-licentie hebt en u Privileged Identity Management al gebruikt, worden alle beheer taken voor rollen uitgevoerd in bevoegdheden voor identiteits beheer en niet in azure AD.
>
> ![Azure AD-rollen die worden beheerd in PIM voor gebruikers die PIM al gebruiken en een Premium P2-licentie hebben](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>Volgende stappen

* U kunt dit met ons delen op het forum voor [Azure AD-beheerders](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Zie [ingebouwde rollen van Azure AD](permissions-reference.md)voor meer informatie over rollen.
* Zie voor standaard gebruikers machtigingen een [vergelijking van de standaard machtigingen voor gast-en gebruikers rechten](../fundamentals/users-default-permissions.md).
