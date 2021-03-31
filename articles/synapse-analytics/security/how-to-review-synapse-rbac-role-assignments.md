---
title: Synapse RBAC-roltoewijzingen controleren in Synapse Studio
description: In dit artikel wordt beschreven hoe u Synapse RBAC-roltoewijzingen kunt controleren met behulp van Synapse Studio
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9065ca9c7638f3d2bda36e4831b81963936f4b45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102152"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>Synapse RBAC-roltoewijzingen controleren

Synapse RBAC-rollen worden gebruikt om machtigingen toe te wijzen aan gebruikers, groepen en andere beveiligings-principals om toegang tot en gebruik van Synapse-resources mogelijk te maken.  [Meer informatie](./synapse-workspace-synapse-rbac.md)

In dit artikel wordt uitgelegd hoe u de huidige roltoewijzingen voor een werk ruimte kunt controleren.

Met elke Synapse RBAC-rol kunt u Synapse RBAC-roltoewijzingen weer geven voor alle bereiken, met inbegrip van toewijzingen voor objecten waartoe u geen toegang hebt. Alleen een Synapse-beheerder kan Synapse RBAC-toegang verlenen.  

>[!Note]
>Gast gebruikers (gebruikers van een andere AD-Tenant) kunnen roltoewijzingen niet zien of beheren, zelfs niet als ze de Synapse-beheerdersrol hebben toegewezen.    

## <a name="open-synapse-studio"></a>Synapse Studio openen  

Als u roltoewijzingen wilt controleren, [opent u eerst de Synapse Studio](https://web.azuresynapse.net/) en selecteert u uw werk ruimte. 

![Aanmelden bij werk ruimte](./media/common/login-workspace.png) 
 
 Nadat u uw werk ruimte hebt geopend, selecteert u de hub **beheren** aan de linkerkant, vouwt u de sectie **beveiliging** uit en selecteert u **toegangs beheer**. 

 ![Selecteer Access Control in de sectie Beveiliging links](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>Toewijzing van werk ruimte-rollen controleren

In het scherm toegangs beheer worden alle huidige roltoewijzingen voor de werk ruimte weer gegeven, gegroepeerd op rol. Elke toewijzing bevat de principal-naam, het Principal-type, de rol en het bereik ervan.

![Access Control scherm](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

Als aan een principal dezelfde rol wordt toegewezen in verschillende bereiken, ziet u meerdere toewijzingen voor de principal, één voor elk bereik.  

Als een rol is toegewezen aan een beveiligings groep, ziet u de rollen die expliciet aan de groep zijn toegewezen, maar niet de rollen die zijn overgenomen van bovenliggende groepen.  

U kunt de lijst filteren op Principal-naam of e-mail adres en selectief de object typen, rollen en bereiken filteren. Voer uw naam of e-mail alias in het naam filter in om de rollen weer te geven die aan u zijn toegewezen. Alleen een Synapse-beheerder kan uw rollen wijzigen.

>[!Important] 
>Als u direct of indirect een lid bent van een groep waaraan rollen zijn toegewezen, hebt u mogelijk machtigingen die niet worden weer gegeven.

>[!tip]
>U kunt uw groepslid maatschappen vinden met behulp van Azure Active Directory in de Azure Portal.  

Als u een nieuwe werk ruimte maakt, krijgt u en de MSI-Service-Principal van de werk ruimte automatisch de beheerdersrol Synapse in het bereik van de werk ruimte.

## <a name="next-steps"></a>Volgende stappen

Meer informatie [over het beheren van Synapse RBAC-roltoewijzingen](./how-to-manage-synapse-rbac-role-assignments.md).

Meer informatie over [welke rol u nodig hebt om specifieke taken uit te voeren](./synapse-workspace-understand-what-role-you-need.md)