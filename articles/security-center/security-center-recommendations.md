---
title: Aanbevelingen voor beveiliging in Azure Security Center
description: In dit document wordt uitgelegd hoe aanbevelingen in Azure Security Center u helpen uw Azure-resources te beveiligen en te blijven voldoen aan het beveiligings beleid.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/25/2020
ms.author: memildin
ms.openlocfilehash: 115d89783a849a9c4c7adb2fceceaf8d1575c785
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795977"
---
# <a name="security-recommendations-in-azure-security-center"></a>Aanbevelingen voor beveiliging in Azure Security Center 
In dit onderwerp wordt uitgelegd hoe u de aanbevelingen in Azure Security Center kunt bekijken en begrijpen om u te helpen uw Azure-resources te beveiligen.


## <a name="what-are-security-recommendations"></a>Wat zijn beveiligings aanbevelingen?

Aanbevelingen zijn acties die u kunt uitvoeren om uw resources te beveiligen.

Security Center analyseert periodiek de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Vervolgens krijgt u aanbevelingen voor het oplossen van deze beveiligingsproblemen.

Elke aanbeveling biedt u het volgende:

- Een korte beschrijving van het probleem
- De herstel stappen die moeten worden uitgevoerd om de aanbeveling te implementeren
- De betrokken resources

## <a name="monitor-recommendations"></a>Aanbevelingen controleren <a name="monitor-recommendations"></a>

Security Center analyseert de beveiligings status van uw resources om mogelijke beveiligings problemen te identificeren. 

1. Open in het menu van Security Center de pagina **aanbevelingen** om de aanbevelingen te zien die van toepassing zijn op uw omgeving. Aanbevelingen zijn onderverdeeld in beveiligings controles.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Aanbevelingen gegroepeerd op beveiligings beheer" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Als u aanbevelingen wilt vinden die specifiek zijn voor het resource type, de ernst, de omgeving of andere criteria die belang rijk voor u zijn, gebruikt u de optionele filters boven de lijst met aanbevelingen.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filters voor het verfijnen van de lijst met Azure Security Center aanbevelingen":::

1. Vouw een besturings element uit en selecteer een specifieke aanbeveling om de pagina met aanbevelings details weer te geven.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Pagina aanbevelings Details." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    De pagina bevat:

    1. Knoppen op ondersteunde aanbevelingen **afdwingen** en **weigeren** (Zie [geen onjuiste configuraties met afdwingen/weigeren](prevent-misconfigurations.md))
    1. **Ernst indicator**
    1. **Interval voor versheid**  (indien van toepassing) 
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