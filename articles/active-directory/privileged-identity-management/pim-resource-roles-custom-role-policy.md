---
title: Aangepaste Azure-rollen gebruiken in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het gebruik van aangepaste rollen van Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24b7845ec66a85e6ced4f1df9caec409a94016bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88782597"
---
# <a name="use-azure-custom-roles-in-privileged-identity-management"></a>Aangepaste Azure-rollen gebruiken in Privileged Identity Management

U moet mogelijk strikte Privileged Identity Management PIM-instellingen Toep assen op sommige gebruikers in een geprivilegieerde rol in uw Azure Active Directory (Azure AD)-organisatie, terwijl er meer autonomie voor anderen beschikbaar is. Denk bijvoorbeeld aan een scenario waarin uw organisatie verschillende contract Associates inhuurt om te helpen bij het ontwikkelen van een toepassing die wordt uitgevoerd in een Azure-abonnement.

Als resource beheerder wilt u dat werk nemers in aanmerking komen voor toegang zonder dat hiervoor goed keuring is vereist. Alle contract Associates moeten echter worden goedgekeurd wanneer ze toegang aanvragen tot de resources van de organisatie.

Volg de stappen die worden beschreven in de volgende sectie voor het instellen van gerichte Privileged Identity Management instellingen voor Azure-resource rollen.

## <a name="create-the-custom-role"></a>De aangepaste rol maken

Als u een aangepaste rol voor een resource wilt maken, volgt u de stappen die worden beschreven in [aangepaste Azure-rollen](../../role-based-access-control/custom-roles.md).

Wanneer u een aangepaste rol maakt, moet u een beschrijvende naam toevoegen, zodat u gemakkelijk kunt onthouden welke ingebouwde rol u wilde dupliceren.

> [!NOTE]
> Zorg ervoor dat de aangepaste rol een duplicaat is van de ingebouwde rol die u wilt dupliceren en dat het bereik overeenkomt met de ingebouwde rol.

## <a name="apply-pim-settings"></a>PIM-instellingen Toep assen

Nadat de functie is gemaakt in uw Azure AD-organisatie, gaat u naar de pagina **privileged Identity Management-Azure-resources** in de Azure Portal. Selecteer de resource waarop de rol van toepassing is.

![Het deel venster Privileged Identity Management-Azure-resources](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Configureer privileged Identity Management rolinstellingen](pim-resource-roles-configure-role-settings.md) die moeten worden toegepast op deze leden van de rol.

Wijs tot slot [rollen](pim-resource-roles-assign-roles.md) toe aan de afzonderlijke groep leden waarop u deze instellingen wilt Toep assen.

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure-resource-rollen configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Aangepaste rollen in Azure](../../role-based-access-control/custom-roles.md)