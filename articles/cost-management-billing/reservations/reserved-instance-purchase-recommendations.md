---
title: Aanbevelingen voor Azure-reserveringen
description: Ontdek meer over aanbevelingen voor Azure-reserveringen.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: d70580a34e832d6465571adbc8f0524abeba609a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767921"
---
# <a name="reservation-recommendations"></a>Aanbevelingen voor reserveringen

Aankoopaanbevelingen voor gereserveerde Azure-instanties (RI) worden aangeboden via de Azure Consumption [Reservation Recommendation-API](/rest/api/consumption/reservationrecommendations), [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) en de ervaring voor de aankoop van reserveringen in de Azure-portal.

In de volgende stappen wordt gedefinieerd hoe de aanbevelingen worden berekend:

1. De aanbevelingsengine evalueert het gebruik per uur voor uw resources in het gegeven bereik van de afgelopen 7, 30 en 60 dagen.
2. Op basis van de gebruiksgegevens simuleert de engine uw kosten met en zonder reserveringen.
3. De kosten worden gesimuleerd voor verschillende hoeveelheden. De hoeveelheid waarvoor de besparingen het grootst zijn, wordt aanbevolen.
4. Als uw resources regelmatig worden afgesloten, vindt de simulatie geen besparingen en worden er geen aankoopaanbevelingen gedaan.
5. De aanbevelingsberekeningen omvatten speciale kortingen die u mogelijk hebt op uw gebruikstarieven op aanvraag.

## <a name="recommendations-in-the-azure-portal"></a>Aanbevelingen in de Azure-portal

Aanbevelingen voor reserveringsaankopen worden ook weergegeven in de Azure-portal in de aankoopervaring. Aanbevelingen worden weergegeven met de **Aanbevolen hoeveelheid**. De hoeveelheid die Azure aanbeveelt, zal de best mogelijke besparingen opleveren na aankoop. Hoewel u elke hoeveelheid kunt kopen die u wilt, zijn uw besparingen niet optimaal als u een andere hoeveelheid koopt.

Laten we enkele voorbeelden bekijken om te zien waarom dat zo is.

In de volgende voorbeeldafbeelding voor de geselecteerde aanbeveling beveelt Azure een aankoophoeveelheid van 6 aan.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Voorbeeld van een aanbeveling voor een reserveringsaankoop" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Meer informatie over de aanbeveling wordt weergegeven wanneer u **Details bekijken selecteert.** In de volgende afbeelding worden details over de aanbeveling weergegeven. De aanbevolen hoeveelheid wordt berekend voor het hoogst mogelijke gebruik en is gebaseerd op uw historische gebruik. Uw aanbeveling is mogelijk niet voor 100% gebruik als uw gebruik inconsistent is. In het voorbeeld ziet u dat het gebruik in de tijd fluctueerde. De kosten van de reservering, mogelijke besparingen en het gebruikspercentage worden weergegeven.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Voorbeeld met details voor een aanbeveling voor een reserveringsaankoop " :::

De grafiek en geschatte waarden veranderen wanneer u de aanbevolen hoeveelheid verhoogt. Door de reserveringshoeveelheid te verhogen, worden uw besparingen verminderd omdat u uiteindelijk minder reserveringsgebruik hebt. Met andere woorden, u zult betalen voor reserveringen die niet volledig worden gebruikt.

Als u de reserveringshoeveelheid verlaagt, worden uw besparingen ook verlaagd. Hoewel het gebruik hoger zal zijn, zullen er waarschijnlijk perioden zijn wanneer uw reserveringen uw gebruik niet volledig zullen dekken. Als het gebruik uw reserveringshoeveelheid overschrijdt, worden daar duurdere Pay-As-You-Go-bronnen voor gebruikt. De volgende voorbeeldafbeelding illustreert dit. We hebben de reserveringshoeveelheid handmatig verlaagd naar 4. Het reserveringsgebruik wordt verhoogd, maar de totale besparingen zijn lager omdat er kosten voor betalen per gebruik aanwezig zijn.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Voorbeeld van gewijzigde details van een aanbeveling voor een reserveringsaankoop" :::

Probeer reserveringen zo dicht mogelijk bij de aanbeveling te kopen voor optimale besparingen.

## <a name="recommendations-in-azure-advisor"></a>Aanbevelingen in Azure Advisor

Aanbevelingen voor reserveringsaankopen zijn beschikbaar in Azure Advisor. Houd rekening met de volgende belangrijke punten:

- Advisor heeft alleen aanbevelingen voor bereiken van één abonnement. Als u aanbevelingen wilt zien voor het hele factureringsbereik (factureringsaccount of factureringsprofiel), doet u het volgende:
  -  Navigeer Azure Portal naar Reserveringen toevoegen en selecteer vervolgens het type dat u  >   de aanbevelingen wilt zien.
- Aanbevelingen die beschikbaar zijn in Advisor houden rekening met uw gebruikstrend van de afgelopen 30 dagen.
- De aanbevolen hoeveelheid en besparingen zijn voor een reservering van drie jaar, indien beschikbaar. Als er geen reservering van drie jaar wordt verkocht voor de service, wordt de aanbeveling berekend met behulp van de reserveringsprijs van één jaar.
- De aanbevelingsberekeningen omvatten speciale kortingen die u mogelijk hebt op uw gebruikstarieven op aanvraag.
- Als u een reservering met gedeeld bereik aanschaft, kan het tot vijf dagen duren voordat aankoopaanbevelingen voor Advisor-reserveringen zijn verdwenen.

## <a name="other-expected-api-behavior"></a>Ander verwacht API-gedrag

- Wanneer u een lookback-periode van zeven dagen gebruikt, krijgt u mogelijk geen aanbevelingen wanneer VM's langer dan een dag worden afgesloten.

## <a name="next-steps"></a>Volgende stappen
- Aanbevelingen [voor reserveringen krijgen met behulp van REST API's.](/rest/api/consumption/reservationrecommendations/list)
- Lees meer over de [toepassing van de Azure-reserveringskorting op virtuele machines](../manage/understand-vm-reservation-charges.md).
