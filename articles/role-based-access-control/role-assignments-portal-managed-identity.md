---
title: Een roltoewijzing voor een beheerde identiteit toevoegen (preview)-Azure RBAC
description: Meer informatie over het toevoegen van een roltoewijzing door te beginnen met de beheerde identiteit en vervolgens het bereik en de rol te selecteren met behulp van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: a01246c0cf35653f4d13262183cf9df28b056c69
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122088"
---
# <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Een roltoewijzing voor een beheerde identiteit toevoegen (preview)

U kunt roltoewijzingen voor een beheerde identiteit toevoegen met behulp van de pagina **toegangs beheer (IAM)** zoals beschreven in [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](role-assignments-portal.md). Wanneer u de pagina toegangs beheer (IAM) gebruikt, begint u met het bereik en selecteert u vervolgens de beheerde identiteit en rol. In dit artikel wordt een alternatieve manier beschreven om roltoewijzingen toe te voegen voor een beheerde identiteit. Met deze stappen begint u met de beheerde identiteit en selecteert u vervolgens het bereik en de rol.

> [!IMPORTANT]
> Als u een roltoewijzing voor een beheerde identiteit toevoegt met behulp van deze alternatieve stappen, is momenteel een preview-versie beschikbaar.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

Volg deze stappen om een rol toe te wijzen aan een door het systeem toegewezen beheerde identiteit door te beginnen met de beheerde identiteit.

1. Open een door het systeem toegewezen beheerde identiteit in het Azure Portal.

1. Klik in het linkermenu op **identiteit**.

    ![Door het systeem toegewezen beheerde identiteit](./media/shared/identity-system-assigned.png)

1. Klik onder **machtigingen** op **Azure-roltoewijzingen**.

    Als er al rollen zijn toegewezen aan de geselecteerde door het systeem toegewezen beheerde identiteit, ziet u de lijst met roltoewijzingen. Deze lijst bevat alle roltoewijzingen waarvoor u lees machtigingen hebt.

    ![Roltoewijzingen voor een door het systeem toegewezen beheerde identiteit](./media/shared/role-assignments-system-assigned.png)

1. Als u het abonnement wilt wijzigen, klikt u op de lijst met **abonnementen** .

1. Klik op **roltoewijzing toevoegen (preview)**.

1. Gebruik de vervolg keuzelijsten om de set resources te selecteren waarop de roltoewijzing van toepassing is, zoals **abonnement**, **resource groep** of resource.

    Als u geen schrijf machtigingen voor de roltoewijzing voor het geselecteerde bereik hebt, wordt een inline-bericht weer gegeven. 

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

   ![Deel venster roltoewijzing toevoegen voor door het systeem toegewezen beheerde identiteit](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken is de beheerde identiteit toegewezen aan de rol bij de geselecteerde scope.

## <a name="user-assigned-managed-identity"></a>Door de gebruiker toegewezen beheerde identiteit

Volg deze stappen om een rol toe te wijzen aan een door de gebruiker toegewezen beheerde identiteit door te beginnen met de beheerde identiteit.

1. Open een door de gebruiker toegewezen beheerde identiteit in het Azure Portal.

1. Klik in het linkermenu op **Azure Role Assignments**.

    Als er al rollen zijn toegewezen aan de geselecteerde door de gebruiker toegewezen beheerde identiteit, ziet u de lijst met roltoewijzingen. Deze lijst bevat alle roltoewijzingen waarvoor u lees machtigingen hebt.

    ![Roltoewijzingen voor een door de gebruiker toegewezen beheerde identiteit](./media/shared/role-assignments-user-assigned.png)

1. Als u het abonnement wilt wijzigen, klikt u op de lijst met **abonnementen** .

1. Klik op **roltoewijzing toevoegen (preview)**.

1. Gebruik de vervolg keuzelijsten om de set resources te selecteren waarop de roltoewijzing van toepassing is, zoals **abonnement**, **resource groep** of resource.

    Als u geen schrijf machtigingen voor de roltoewijzing voor het geselecteerde bereik hebt, wordt een inline-bericht weer gegeven. 

1. Selecteer in de vervolgkeuzelijst **Rol** een rol, zoals **Inzender voor virtuele machines**.

   ![Deel venster roltoewijzing toevoegen voor een door de gebruiker toegewezen beheerde identiteit](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogen blikken is de beheerde identiteit toegewezen aan de rol bij de geselecteerde scope.

## <a name="next-steps"></a>Volgende stappen

- [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)
- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure-portal](role-assignments-portal.md)
- [Azure-roltoewijzingen vermelden met behulp van Azure Portal](role-assignments-list-portal.md)
