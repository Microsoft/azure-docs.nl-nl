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
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 3b2f111f83dbd731b69671e58d4bf9dc648a596f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526494"
---
# <a name="security-recommendations-in-azure-security-center"></a>Aanbevelingen voor beveiliging in Azure Security Center 

In dit onderwerp wordt uitgelegd hoe u de aanbevelingen in Azure Security Center kunt bekijken en begrijpen om u te helpen uw Azure-resources te beveiligen.


## <a name="what-are-security-recommendations"></a>Wat zijn beveiligings aanbevelingen?

Security Center analyseert periodiek de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Vervolgens krijgt u aanbevelingen voor het oplossen van deze beveiligingsproblemen.

Aanbevelingen zijn acties die u kunt uitvoeren om uw resources te beveiligen en te beschermen. 

Elke aanbeveling biedt u het volgende:

- Een korte beschrijving van het probleem
- De herstel stappen die moeten worden uitgevoerd om de aanbeveling te implementeren
- De betrokken resources

## <a name="how-does-microsoft-decide-what-needs-securing-and-hardening"></a>Hoe beslist micro soft wat er nodig is om te beveiligen en te beschermen?

De aanbevelingen van Security Center zijn gebaseerd op de beveiligings benchmark van Azure. Bijna elke aanbeveling heeft een onderliggend beleid dat is afgeleid van een vereiste in de Bench Mark.

Azure Security Benchmark is de door Microsoft ontworpen, Azure-specifieke set richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingsframeworks. Dit algemeen gerespecteerde Bench Mark bouwt voort op de besturings elementen van het [Center voor Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) met een focus op Cloud gerichte beveiliging. Meer informatie over [Azure Security Benchmark](../security/benchmarks/introduction.md).

Wanneer u de details van een aanbeveling bekijkt, is het vaak handig om het onderliggende beleid te kunnen zien. Voor elke aanbeveling die wordt ondersteund door een beleid, gebruikt u de koppeling **beleids definitie weer geven** op de pagina aanbevelings Details om rechtstreeks naar het Azure Policy item voor het relevante beleid te gaan:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Koppeling naar Azure Policy pagina voor het specifieke beleid dat een aanbeveling ondersteunt":::

Gebruik deze koppeling om de beleids definitie weer te geven en de evaluatie logica te controleren. 

Als u de lijst met aanbevelingen in onze [Naslag Gids voor beveiligings aanbevelingen](recommendations-reference.md)bekijkt, ziet u ook koppelingen naar de pagina met beleids definities:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Toegang tot de Azure Policy-pagina voor een specifiek beleid rechtstreeks op de pagina met Azure Security Center aanbevelingen":::

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