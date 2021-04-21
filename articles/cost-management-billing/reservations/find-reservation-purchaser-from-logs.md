---
title: Een reserveringsinkoper zoeken in Azure Monitor logboeken
description: In dit artikel vindt u een koper van een reservering met informatie uit Azure Monitor logboeken.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: 965e90eed0690d57b6ad3cf3a1543b1329c0fe74
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773368"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Een koper van een reservering zoeken in Azure-logboeken

Dit artikel helpt u bij het vinden van een koper van een reservering met informatie uit uw directorylogboeken. In de directorylogboeken Azure Monitor de e-mail-ID's van gebruikers die reserveringsaankopen hebben gedaan.

## <a name="find-the-purchaser"></a>De koper zoeken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer **naar**  >  **Activiteitenlogboekactiviteit**  >  **bewaken.**  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Schermopname van navigatie naar activiteitenlogboek - Activiteit." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Selecteer **Mapactiviteit.** Als u een bericht ziet met de mededeling Dat u machtigingen nodig hebt om logboeken op *mapniveau* weer te geven, selecteert u de [koppeling](../../role-based-access-control/elevate-access-global-admin.md) voor meer informatie over het krijgen van machtigingen.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Schermopname van Directory-activiteit zonder machtiging om het logboek weer te geven." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Zodra u toestemming hebt, filtert **u tenantresourceprovider** **met Microsoft.Capacity.** Als het goed is, ziet u alle reserveringsgebeurtenissen voor de geselecteerde periode. Wijzig zo nodig de tijdspanne.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Schermopname van de gebruiker die de reservering heeft aangeschaft." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Indien nodig moet u mogelijk Kolommen **bewerken selecteren om** Gebeurtenis te selecteren die is **geïnitieerd door**.
   De gebruiker die de reservering heeft aangeschaft, wordt weergegeven onder **Gebeurtenis geïnitieerd door**.

## <a name="next-steps"></a>Volgende stappen

- Indien nodig kunnen factureringsbeheerders [eigenaar worden van een reservering](view-reservations.md#how-billing-administrators-can-view-or-manage-reservations).
