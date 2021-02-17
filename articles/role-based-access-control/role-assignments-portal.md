---
title: Azure-rollen toewijzen met behulp van de Azure Portal-Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: e25bbe4e1a96e4efaaa13732aea571d26d4b006e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555293"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Azure-rollen toewijzen met behulp van de Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] In dit artikel wordt beschreven hoe u rollen toewijst met behulp van de Azure Portal.

Zie [beheerders rollen weer geven en toewijzen in azure Active Directory](../active-directory/roles/manage-roles-portal.md)als u beheerders rollen wilt toewijzen in azure Active Directory.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-identify-the-needed-scope"></a>Stap 1: het benodigde bereik identificeren

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Zie [inzicht in bereik](scope-overview.md)voor meer informatie over het bereik.

![Bereikniveaus voor Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek in het zoekvak bovenaan het bereik dat u toegang wilt verlenen. Zoek bijvoorbeeld naar **beheer groepen**, **abonnementen**, **resource groepen** of een specifieke resource.

    ![Azure Portal zoeken naar resource groep](./media/shared/rg-portal-search.png)

1. Klik op de specifieke resource voor dat bereik.

    Hieronder ziet u een voorbeeld van een resourcegroep.

    ![Overzicht van resourcegroep](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Stap 2: het deel venster toewijzing van rol toevoegen openen

**Toegangs beheer (IAM)** is de pagina die u doorgaans gebruikt om rollen toe te wijzen om toegang te verlenen tot Azure-resources. Het wordt ook wel ' identiteits-en toegangs beheer (IAM) genoemd en wordt weer gegeven op verschillende locaties in de Azure Portal.

1. Klik op **Toegangsbeheer (IAM)**.

    Hieronder ziet u een voorbeeld van de pagina IAM (Toegangsbeheer) voor een resourcegroep.

    ![De pagina toegangs beheer (IAM) voor een resource groep](./media/shared/rg-access-control.png)

1. Klik op **het tabblad roltoewijzingen om de roltoewijzingen** in dit bereik weer te geven.

1. Klik **op**  >  **toewijzing van roltoewijzing** toevoegen.
   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Roltoewijzing toevoegen](./media/shared/add-role-assignment-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

   ![Deelvenster Roltoewijzing toevoegen](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-appropriate-role"></a>Stap 3: de juiste rol selecteren

1. Zoek in de lijst met **rollen** de rol die u wilt toewijzen.

    Om u te helpen bij het bepalen van de juiste rol, kunt u de muis aanwijzer boven het info pictogram klikken om een beschrijving voor de rol weer te geven. Zie het artikel [ingebouwde rollen van Azure](built-in-roles.md) voor meer informatie.

   ![Rol selecteren in roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment-role.png)

1. Klik om de rol te selecteren.

## <a name="step-4-select-who-needs-access"></a>Stap 4: selecteren wie toegang moet hebben

1. Selecteer in de lijst **toegang toewijzen aan** het type beveiligings-principal waaraan u toegang wilt toewijzen.

    | Type | Description |
    | --- | --- |
    | **Gebruiker, groep of Service-Principal** | Als u de rol wilt toewijzen aan een gebruiker, groep of Service-Principal (toepassing), selecteert u dit type. |
    | **Door de gebruiker toegewezen beheerde identiteit** | Als u de rol wilt toewijzen aan een door de [gebruiker toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md), selecteert u dit type. |
    | *Door het systeem toegewezen beheerde identiteit* | Als u de rol wilt toewijzen aan een door het [systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md), selecteert u het exemplaar van de Azure-service waar de beheerde identiteit zich bevindt. |

   ![Selecteer type beveiligingsprincipal in roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment-type.png)

1. Als u een door de gebruiker toegewezen beheerde identiteit of een door het systeem toegewezen beheerde identiteit hebt geselecteerd, selecteert u het **abonnement** waarin de beheerde identiteit zich bevindt.

1. Zoek in de sectie **selecteren** naar de beveiligingsprincipal door een teken reeks in te voeren of door de lijst te bladeren.

   ![Gebruiker selecteren in roltoewijzing toevoegen](./media/role-assignments-portal/add-role-assignment-user.png)

1. Zodra u de beveiligingsprincipal hebt gevonden, klikt u erop om deze te selecteren.

## <a name="step-5-assign-role"></a>Stap 5: rol toewijzen

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken wordt de rol bij de geselecteerde scope toegewezen aan de beveiligingsprincipal.

1. Controleer op **het tabblad roltoewijzingen of de roltoewijzing** in de lijst wordt weer geven.

    ![Roltoewijzing van rol toevoegen is opgeslagen](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="next-steps"></a>Volgende stappen

- [Een gebruiker toewijzen als beheerder van een Azure-abonnement](role-assignments-portal-subscription-admin.md)
- [Azure-roltoewijzingen verwijderen](role-assignments-remove.md)
- [Problemen met Azure RBAC oplossen](troubleshooting.md)
