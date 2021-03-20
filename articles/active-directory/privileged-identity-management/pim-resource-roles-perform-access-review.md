---
title: Toegang tot Azure-resource rollen controleren in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het controleren van de toegang tot Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8091890d174ae68f012a6ec24685a0e705100f8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743674"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Toegang tot Azure-resource rollen controleren in Privileged Identity Management

Met Privileged Identity Management (PIM)-toegangs beoordelingen kunt u de toegang tot beschermde rollen in Azure Active Directory (Azure AD) helpen beveiligen. In dit artikel worden de stappen beschreven voor het volt ooien van uw geprivilegieerde roltoewijzingen in een Azure AD-toegangs beoordeling.

Als u bent toegewezen aan een administratieve rol, moet u mogelijk een toegangs beoordeling door de beheerder volt ooien om te bevestigen dat u een rol nodig hebt. De bevestigings aanvraag kan een e-mail bericht met een koppeling zijn of u kunt bevestigen in de [Azure Portal](https://portal.azure.com).

Als u een bevoegde rol beheerder bent die geïnteresseerd is in toegangs beoordelingen, kunt u meer informatie vinden [over het starten van een toegangs beoordeling](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Toegang goed keuren of weigeren

U kunt toegang goed keuren of weigeren op basis van het al dan niet gebruiken van deze rol. Kies **goed keuren** als u de rol wilt blijven gebruiken of **weigeren** als u de toegang niet meer nodig hebt. De status wordt pas gewijzigd nadat de revisor de resultaten heeft toegepast.

Volg deze stappen om de toegangs beoordeling te vinden en te volt ooien:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Selecteer **Azure Active Directory** en open **privileged Identity Management**.
1. Selecteer **toegang controleren**.

   ![Scherm opname van Privileged Identity Management toepassing, met de Blade toegang controleren geselecteerd](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Selecteer de beoordeling die u wilt volt ooien.
1. Kies **goed keuren** of **weigeren**. Voer in het **vak een reden opgeven** een zakelijke rechtvaardiging voor uw beslissing in, indien nodig.

   ![Scherm afbeelding van de pagina met details van de beoordeling](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Volgende stappen

- [Een toegangs beoordeling van mijn Azure AD-rollen uitvoeren in Privileged Identity Management](pim-how-to-perform-security-review.md)
