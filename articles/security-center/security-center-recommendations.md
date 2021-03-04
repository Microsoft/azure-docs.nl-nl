---
title: Aanbevelingen voor beveiliging in Azure Security Center
description: In dit document wordt uitgelegd hoe aanbevelingen in Azure Security Center u helpen uw Azure-resources te beveiligen en te blijven voldoen aan het beveiligings beleid.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 57760443746e111750e74ef55fc18729f6ba32c4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100335"
---
# <a name="review-your-security-recommendations"></a>Uw beveiligings aanbevelingen controleren

In dit onderwerp wordt uitgelegd hoe u de aanbevelingen in Azure Security Center kunt bekijken en begrijpen om u te helpen uw Azure-resources te beveiligen.

## <a name="monitor-recommendations"></a>Aanbevelingen controleren <a name="monitor-recommendations"></a>

Security Center analyseert de beveiligings status van uw resources om mogelijke beveiligings problemen te identificeren. 

1. Open in het menu van Security Center de pagina **aanbevelingen** om de aanbevelingen te zien die van toepassing zijn op uw omgeving. Aanbevelingen zijn onderverdeeld in beveiligings controles.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Aanbevelingen gegroepeerd op beveiligingsbeheer" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Als u aanbevelingen wilt vinden die specifiek zijn voor het resource type, de ernst, de omgeving of andere criteria die belang rijk voor u zijn, gebruikt u de optionele filters boven de lijst met aanbevelingen.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filters voor het verfijnen van de lijst met Azure Security Center aanbevelingen":::

1. Vouw een besturings element uit en selecteer een specifieke aanbeveling om de pagina met aanbevelings details weer te geven.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Pagina aanbevelings Details." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    De pagina bevat:

    1. Voor ondersteunde aanbevelingen toont de bovenste werk balk een of meer van de volgende knoppen:
        - **Afdwingen** en **weigeren** (Zie [niet-onjuiste configuraties met afdwingen/aanbevelingen weigeren](prevent-misconfigurations.md))
        - **Bekijk de beleids definitie** om rechtstreeks naar het Azure Policy item voor het onderliggende beleid te gaan
    1. **Ernst indicator**
    1. **Interval voor versheid** (indien van toepassing)
    1. **Aantal vrijgestelde resources** als er uitzonde ringen voor deze aanbeveling bestaan, wordt hier het aantal resources weer gegeven dat is uitgesloten
    1. **Beschrijving** : een korte beschrijving van het probleem
    1. Herstels **tappen** : een beschrijving van de hand matige stappen die nodig zijn om het beveiligings probleem op de betrokken resources op te lossen. Voor aanbevelingen met snelle oplossing kunt u **herstel logica weer geven** selecteren voordat u de voorgestelde oplossing toepast op uw resources. 
    1. **Betrokken resources** : uw resources worden in tabbladen gegroepeerd.
        - **Goede resources** : relevante bronnen die niet van invloed zijn op of waarop u het probleem al hebt opgelost.
        - **Slechte bronnen** : bronnen die nog steeds worden beïnvloed door het geïdentificeerde probleem.
        - **Niet-toepasselijke resources** : resources waarvoor de aanbeveling geen definitief antwoord kan geven. Het tabblad niet van toepassing bevat ook de redenen voor elke resource. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Er zijn geen toepasselijke resources om redenen.":::
    1. Actie knoppen om de aanbeveling te herstellen of een logische app te activeren.

## <a name="preview-recommendations"></a>Preview-aanbevelingen

Aanbevelingen die als **Preview** zijn gemarkeerd, zijn niet opgenomen in de berekeningen van uw beveiligde Score.

Ze moeten, waar mogelijk, nog steeds worden hersteld. Wanneer de preview-periode is afgelopen, worden ze dus meegerekend in uw score.

Een voorbeeld van een preview-aanbeveling:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Aanbeveling met de preview-markering":::
 
## <a name="next-steps"></a>Volgende stappen

In dit document hebt u kennis gemaakt met beveiligings aanbevelingen in Security Center. Voor verwante informatie:

- [Aanbevelingen herstellen](security-center-remediate-recommendations.md): meer informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en resource groepen.
- [Voorkom onjuiste configuraties met de aanbevelingen afdwingen/weigeren](prevent-misconfigurations.md).
- [Automatisch reacties op Security Center triggers automatiseren](workflow-automation.md)--reacties op aanbevelingen automatiseren
- [Een resource uitsluiten van een aanbeveling](exempt-resource.md)
- [Aanbevelingen voor beveiliging: een naslaggids](recommendations-reference.md)