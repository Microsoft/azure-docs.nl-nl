---
title: Problemen met Azure-reserve ring tussen tenants oplossen
description: In dit artikel vindt u een reserve ring van eigen aren om een reserverings order van een Azure Active Directory Tenant (Directory) over te dragen naar een andere.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.openlocfilehash: 79473d57cc7504e7e6ef4ef68ba0cee74203f62b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055832"
---
# <a name="troubleshoot-reservation-transfers-between-tenants"></a>Problemen met reserverings overdracht tussen tenants oplossen

In dit artikel vindt u een reserve ring van eigen aren om een reserverings order van een Azure Active Directory Tenant (Directory) over te dragen naar een andere. Wanneer u de directory van een reserverings order wijzigt, wordt de toegang tot Azure RBAC voor de reserverings volgorde en afhankelijke reserve ringen verwijderd. Alleen u krijgt toegang na de wijziging. Als u de map wijzigt, wordt het eigendom van de facturering niet gewijzigd voor de reserverings order. De map wordt gewijzigd voor de bovenliggende reserverings volgorde en afhankelijke reserve ringen.

Er is geen reserverings reservering en annulering nodig om over te dragen tussen tenants.

Nadat u een reserve ring hebt overgedragen naar een andere Tenant, wilt u mogelijk ook extra eigen aren toevoegen aan de reserve ring. Zie [wie kan standaard een reserve ring beheren](view-reservations.md#who-can-manage-a-reservation-by-default)voor meer informatie.

Wanneer u een reserverings order overdraagt, worden alle reserve ringen onder de order door gegeven.

## <a name="transfer-a-reservation"></a>Een reserve ring overdragen

Voer de volgende stappen uit om een reserverings order te transporteren en is afhankelijk van de reserve ringen naar een andere Tenant.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Als u geen facturerings beheerder bent, maar u een reserverings eigenaar bent, navigeert u naar **reserve ringen** en gaat u verder met stap 6.
1. Navigeer naar **Cost Management en facturering**.
    - Als u een EA-beheerder bent, selecteert u in het linkermenu **facturerings bereik** en selecteert u in de lijst met facturerings bereiken een.
    - Als u de eigenaar van het facturerings Profiel van een micro soft-klant bent, selecteert u in het menu links **facturerings profielen**. Selecteer in de lijst met facturerings profielen een.
1. Selecteer **reserverings transacties** in het linkermenu. De lijst met reserverings transacties wordt weer gegeven.
1. Een banner aan de bovenkant van de pagina leest *nu dat beheerders van de facturering reserve ringen kunnen beheren. Klik hier om de reserve ringen te beheren.* Selecteer de banner.
1. De volledige lijst met reserve ringen voor uw EA-inschrijving of facturerings profiel wordt weer gegeven.
1. Selecteer de reserve ring die u wilt overdragen.
1. Selecteer in de reserverings details de reserverings Order-ID.
1. Selecteer in de reserverings volgorde **map wijzigen**.
1. Selecteer in het deel venster map wijzigen de Azure AD-map waarnaar u de reserve ring wilt overdragen en selecteer vervolgens **bevestigen**.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md) voor meer informatie over reserveringen.