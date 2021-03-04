---
title: Een beheergroep registreren bij Azure Security Center
description: Meer informatie over het gebruik van een geleverde Azure Policy-definitie om Azure Security Center in te schakelen voor alle abonnementen in een beheergroep.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: memildin
ms.openlocfilehash: 9db276425d510316c41126f8e942a1e2a362ea35
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101287"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Security Center inschakelen voor alle abonnementen in een beheergroep

U kunt Azure Policy gebruiken om Azure Security Center in te schakelen voor alle Azure-abonnementen binnen dezelfde MG (beheergroep). Dit is handiger dan wanneer u ze afzonderlijk opent vanuit de portal, en werkt ook als de abonnementen verschillende eigenaars hebben. 

Een beheergroep en alle bijbehorende abonnementen registreren:

1. Open Azure Policy als een gebruiker met machtigingen voor **Beveiligingsbeheerder**, en zoek naar de definitie **Azure Security Center inschakelen in uw abonnement**.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="De Azure Policy-definitie Azure Security Center inschakelen voor uw abonnement":::

1. Selecteer **Toewijzen** en zorg ervoor dat u het bereik instelt op het MG-niveau.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="De definitie Azure Security Center inschakelen voor uw abonnement toewijzen":::

    > [!TIP]
    > Behalve het bereik zijn er geen vereiste parameters.

1. Selecteer **Een hersteltaak maken** om ervoor te zorgen dat alle bestaande abonnementen waarvoor Security Center niet is ingeschakeld, worden geregistreerd.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Een hersteltaak maken voor de Azure Policy-definitie Azure Security Center inschakelen voor uw abonnement":::

1. Wanneer de definitie is toegewezen, gebeurt het volgende:

    1. Alle abonnementen in de MG die nog niet zijn geregistreerd bij Security Center, worden gedetecteerd.
    1. Deze abonnementen worden gemarkeerd als niet-compatibel.
    1. Alle geregistreerde abonnementen (ongeacht of Azure Defender is in- of uitgeschakeld) worden gemarkeerd als compatibel.

    Met de hersteltaak wordt Security Center vervolgens gratis ingeschakeld voor de niet-compatibele abonnementen.

> [!IMPORTANT]
> Met de beleidsdefinitie wordt Security Center alleen ingeschakeld voor **bestaande** abonnementen. Als u onlangs gemaakte abonnementen wilt registreren, opent u het tabblad Naleving, selecteert u de relevante niet-compatibele abonnementen, en maakt u een hersteltaak. Herhaal deze stap wanneer u een of meer nieuwe abonnementen hebt die u wilt bewaken met Security Center.

## <a name="optional-modifications"></a>Optionele wijzigingen

Er zijn verschillende manieren waarop u de Azure Policy-definitie kunt wijzigen: 

- **Naleving verschillend definiëren**: met het geleverde beleid worden alle abonnementen in de MG geclassificeerd die nog niet als niet-compatibel zijn geregistreerd bij Security Center. U kunt ervoor kiezen dit in te stellen voor alle abonnementen zonder Azure Defender.

    Met de geleverde definitie wordt *een van de* prijsinstellingen hieronder gedefinieerd als compatibel. Dit betekent dat een abonnement dat is ingesteld op Standard of Gratis, compatibel is.

    > [!TIP]
    > Wanneer een Azure Defender-plan is ingeschakeld, wordt het beschreven in een beleids definitie die is ingesteld op de standaard instelling. Wanneer het is uitgeschakeld, is het Gratis. Zie [Security Center Free VS Azure Defender enabled](security-center-pricing.md)(Engelstalig) voor meer informatie over de verschillen tussen deze plannen. 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Als u deze optie wijzigt in het volgende, worden alleen abonnementen die zijn ingesteld op Standard, geclassificeerd als compatibel:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Definieer enkele Azure Defender-abonnementen die moeten worden toegepast bij het inschakelen van Security Center**: op basis van het geleverde beleid wordt Security Center ingeschakeld zonder een van de optionele Azure Defender-abonnementen. U kunt ervoor kiezen om een of meer hiervan in te schakelen.

    De sectie `deployment` van de geleverde definitie bevat een parameter `pricingTier`. Dit is standaard ingesteld op `free`, maar u kunt dit wijzigen. 


## <a name="next-steps"></a>Volgende stappen:

Nu u een volledige beheergroep hebt geregistreerd, schakelt u geavanceerde bescherming van Azure Defender in. 

> [!div class="nextstepaction"]
> [Azure Defender inschakelen](enable-azure-defender.md)
