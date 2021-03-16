---
title: Een reserve ring van Azure Monitor-logboeken zoeken
description: Dit artikel helpt u bij het vinden van een reserve ring-inkoper met informatie uit Azure Monitor Logboeken.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: eedb5e7a55b50a353fd16498500b891e289e61e5
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103477046"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Een reserverings reservering zoeken vanuit Azure-logboeken

Dit artikel helpt u bij het vinden van een reserve ring-inkoper met informatie uit uw directory-Logboeken. De Directory-logboeken van Azure Monitor toont de e-mail-Id's van de gebruikers die reserverings aankopen hebben gedaan.

## <a name="find-the-purchaser"></a>De inkoper zoeken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer om activiteiten voor  >  **activiteiten logboek** te bewaken  >  .  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Scherm afbeelding met navigatie naar activiteiten logboek-activiteit." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Selecteer **Directory-activiteit**. Als er een bericht wordt weer gegeven met de mede deling dat *u gemachtigd bent om logboeken op Directory-niveau weer te geven*, selecteert u de [koppeling](../../role-based-access-control/elevate-access-global-admin.md) om te leren hoe u machtigingen kunt krijgen.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Scherm opname van de Directory activiteit zonder toestemming voor het weer geven van het logboek." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Als u gemachtigd bent, filtert u **Tenant resource provider** met **micro soft. capacity**. Als het goed is, ziet u alle gebeurtenissen met betrekking tot reserve ring voor de geselecteerde tijds periode. Wijzig, indien nodig, de tijds Panne.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Scherm opname van de gebruiker die de reserve ring heeft gekocht." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Als dat nodig is, moet u mogelijk **kolommen bewerken** om de gebeurtenis te selecteren die is **gestart door**.
   De gebruiker die de reserverings aankoop heeft gedaan, wordt weer gegeven onder **gebeurtenis gestart door**.

## <a name="next-steps"></a>Volgende stappen

- Facturerings beheerders kunnen, indien nodig, [eigenaar worden van een reserve ring](view-reservations.md#how-billing-administrators-view-or-manage-reservations).