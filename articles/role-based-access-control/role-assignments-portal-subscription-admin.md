---
title: Een gebruiker toewijzen als beheerder van een Azure-abonnement-Azure RBAC
description: Meer informatie over hoe u een beheerder van een Azure-abonnement kunt maken met behulp van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556833"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Een gebruiker toewijzen als beheerder van een Azure-abonnement

Als u een gebruiker een beheerder van een Azure-abonnement wilt maken, wijst u de rol [Eigenaar](built-in-roles.md#owner) aan deze persoon toe voor het abonnementsbereik. De rol eigenaar geeft de gebruiker volledige toegang tot alle resources in het abonnement, met inbegrip van de machtiging om toegang tot anderen te verlenen. Deze stappen zijn hetzelfde als die voor elke andere functietoewijzing.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Stap 1: het abonnement openen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek in het zoekvak bovenaan op abonnementen.

    ![Azure Portal zoeken naar resource groep](./media/shared/sub-portal-search.png)

1. Klik op het abonnement dat u wilt gebruiken.

    Hieronder ziet u een voor beeld van een abonnement.

    ![Overzicht van resourcegroep](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Stap 2: het deel venster toewijzing van rol toevoegen openen

**Toegangs beheer (IAM)** is de pagina die u doorgaans gebruikt om rollen toe te wijzen om toegang te verlenen tot Azure-resources. Het wordt ook wel ' identiteits-en toegangs beheer (IAM) genoemd en wordt weer gegeven op verschillende locaties in de Azure Portal.

1. Klik op **Toegangsbeheer (IAM)**.

    Hieronder ziet u een voor beeld van de pagina toegangs beheer (IAM) voor een abonnement.

    ![De pagina toegangs beheer (IAM) voor een resource groep](./media/shared/sub-access-control.png)

1. Klik op **het tabblad roltoewijzingen om de roltoewijzingen** in dit bereik weer te geven.

1. Klik **op**  >  **toewijzing van roltoewijzing** toevoegen.
   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Roltoewijzing toevoegen](./media/shared/add-role-assignment-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

   ![Deelvenster Roltoewijzing toevoegen](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>Stap 3: de rol van eigenaar selecteren

De rol [eigenaar](built-in-roles.md#owner) verleent volledige toegang tot het beheer van alle resources, inclusief de mogelijkheid om rollen toe te wijzen in azure RBAC. U moet een maximum van 3 abonnements eigenaren hebben om de kans te verkleinen dat er inbreuk is op een verzwakte eigenaar.

- Selecteer de rol **eigenaar** in de lijst **rol** .

   ![De rol eigenaar selecteren in het deel venster roltoewijzing toevoegen](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>Stap 4: selecteren wie toegang moet hebben

1. Selecteer in de lijst **toegang toewijzen aan** de optie **gebruiker, groep of Service-Principal**.

1. Zoek in de sectie **selecteren** naar de gebruiker door een teken reeks in te voeren of door de lijst te bladeren.

   ![Gebruiker selecteren in roltoewijzing toevoegen](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Wanneer u de gebruiker hebt gevonden, klikt u erop om deze te selecteren.

## <a name="step-5-assign-role"></a>Stap 5: rol toewijzen

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken is de gebruiker toegewezen aan de rol bij de geselecteerde scope.

1. Controleer op **het tabblad roltoewijzingen of de roltoewijzing** in de lijst wordt weer geven.

    ![Roltoewijzing van rol toevoegen is opgeslagen](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure-rollen toewijzen met behulp van de Azure Portal](role-assignments-portal.md)
- [Azure-roltoewijzingen vermelden met behulp van Azure Portal](role-assignments-list-portal.md)
- [Uw resources organiseren met Azure-beheergroepen](../governance/management-groups/overview.md)
