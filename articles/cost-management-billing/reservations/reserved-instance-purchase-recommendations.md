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
ms.openlocfilehash: 4f6187ccb143f065fed236495128add7a2ab1ee4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928531"
---
# <a name="reservation-recommendations"></a>Aanbevelingen voor reserveringen

Aankoopaanbevelingen voor gereserveerde Azure-instanties (RI) worden aangeboden via de Azure Consumption [Reservation Recommendation-API](/rest/api/consumption/reservationrecommendations), [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) en de ervaring voor de aankoop van reserveringen in de Azure-portal.

In de volgende stappen wordt gedefinieerd hoe de aanbevelingen worden berekend:

1. De aanbevelingsengine evalueert het gebruik per uur voor uw resources in het gegeven bereik van de afgelopen 7, 30 en 60 dagen.
2. Op basis van de gebruiksgegevens simuleert de engine uw kosten met en zonder reserveringen.
3. De kosten worden gesimuleerd voor verschillende hoeveelheden. De hoeveelheid waarvoor de besparingen het grootst zijn, wordt aanbevolen.
4. Als uw resources regelmatig worden afgesloten, vindt de simulatie geen besparingen en worden er geen aankoopaanbevelingen gedaan.
5. De aanbevelingen voor de aanbeveling bevatten speciale kortingen die u mogelijk hebt op de gebruiks tarieven op aanvraag.

## <a name="recommendations-in-the-azure-portal"></a>Aanbevelingen in de Azure-portal

Aanbevelingen voor reserveringsaankopen worden ook weergegeven in de Azure-portal in de aankoopervaring. Aanbevelingen worden weergegeven met de **Aanbevolen hoeveelheid**. De hoeveelheid die Azure aanbeveelt, zal de best mogelijke besparingen opleveren na aankoop. U kunt elk gewenst aantal kopen als u een ander aantal koopt dat uw besparingen niet optimaal zijn.

Laten we enkele voorbeelden bekijken om te zien waarom dat zo is.

In de volgende voorbeeldafbeelding voor de geselecteerde aanbeveling beveelt Azure een aankoophoeveelheid van 6 aan.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Voorbeeld van een aanbeveling voor een reserveringsaankoop" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Meer informatie over de aanbeveling wordt weer gegeven wanneer u **Details weer geven** selecteert. In de volgende afbeelding worden details over de aanbeveling weergegeven. De aanbevolen hoeveelheid wordt berekend voor het hoogst mogelijke gebruik en is gebaseerd op uw historische gebruik. Uw aanbeveling is mogelijk niet voor 100% gebruik als uw gebruik inconsistent is. In het voor beeld ziet u dat het gebruik in de loop van de tijd schommelt. De kosten van de reservering, mogelijke besparingen en het gebruikspercentage worden weergegeven.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Voor beeld van Details voor een aanbeveling met een reserve ring-aankoop " :::

De grafiek en de geschatte waarden veranderen wanneer u het aanbevolen aantal verhoogt. Door de reserverings hoeveelheid te verhogen, worden uw besparingen gereduceerd omdat het gebruik van de gereduceerde reserve ring wordt beëindigd. Met andere woorden, u zult betalen voor reserveringen die niet volledig worden gebruikt.

Als u de reserverings hoeveelheid verlaagt, worden uw besparingen ook verminderd. Hoewel het gebruik hoger zal zijn, zullen er waarschijnlijk perioden zijn wanneer uw reserveringen uw gebruik niet volledig zullen dekken. Als het gebruik uw reserveringshoeveelheid overschrijdt, worden daar duurdere Pay-As-You-Go-bronnen voor gebruikt. De volgende voorbeeldafbeelding illustreert dit. We hebben de reserveringshoeveelheid handmatig verlaagd naar 4. Het reserverings gebruik wordt verhoogd, maar de totale besparingen worden verlaagd, omdat de kosten voor betalen naar gebruik aanwezig zijn.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Voorbeeld van gewijzigde details van een aanbeveling voor een reserveringsaankoop" :::

Probeer reserveringen zo dicht mogelijk bij de aanbeveling te kopen voor optimale besparingen.

## <a name="recommendations-in-azure-advisor"></a>Aanbevelingen in Azure Advisor

Aanbevelingen voor reserveringsaankopen zijn beschikbaar in Azure Advisor. Houd rekening met de volgende belangrijke punten:

- Advisor heeft alleen aanbevelingen voor bereiken van één abonnement. Als u aanbevelingen voor het volledige facturerings bereik (facturerings account of facturerings profiel) wilt weer geven, gaat u als volgt te werk:
  -  In de Azure portal gaat u naar **reserve ringen**  >  **toevoegen** en selecteert u vervolgens het type waarvoor u de aanbevelingen wilt zien.
- Bij aanbevelingen die beschikbaar zijn in Advisor kunt u de afgelopen 30 dagen gebruiken.
- De aanbevolen hoeveelheid en besparingen zijn voor een reserve ring van drie jaar, indien beschikbaar. Als een reserve ring van drie jaar niet wordt verkocht voor de service, wordt de aanbeveling berekend met behulp van de reserverings prijs van één jaar.
- De aanbevelingen voor de aanbeveling bevatten speciale kortingen die u mogelijk hebt op de gebruiks tarieven op aanvraag.
- Als u een reserve ring voor een gedeeld bereik aanschaft, kan het tot vijf dagen duren voordat de aanbevelingen voor de Advisor-reserve ring worden verwijderd.

## <a name="other-expected-api-behavior"></a>Ander verwacht API-gedrag

- Wanneer u een lookback-periode van zeven dagen gebruikt, krijgt u mogelijk geen aanbevelingen wanneer VM's langer dan een dag worden afgesloten.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over de [toepassing van de Azure-reserveringskorting op virtuele machines](../manage/understand-vm-reservation-charges.md).
