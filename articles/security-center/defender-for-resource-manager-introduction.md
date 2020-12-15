---
title: 'Azure Defender voor Resource Manager: voordelen en functies'
description: Meer informatie over de voordelen en functies van Azure Defender voor Resource Manager
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5594e8225b615bf6e1af5077bd68422edbbeec19
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905448"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>Inleiding op Azure Defender voor Resource Manager

[Azure Resource Manager](../azure-resource-manager/management/overview.md) is de implementatie- en beheerservice voor Azure. Het biedt een beheerlaag waarmee u resources in uw Azure-account kunt maken, bijwerken en verwijderen. U kunt beheerfuncties gebruiken, zoals toegangscontrole, vergrendelingen en tags, om uw resources te beveiligen en te organiseren na de implementatie.

De cloudbeheerlaag is een cruciale service die is verbonden met al uw cloudresources. Daarom is het ook een potentieel doel voor aanvallers. Vandaar wordt aangeraden dat beveiligingsteams de resourcebeheerlaag nauwkeurig bewaken. 

Azure Defender voor Resource Manager bewaakt automatisch de beheerbewerkingen voor resources in uw organisatie, ongeacht of ze worden uitgevoerd via Azure Portal, Azure REST API's, de Azure CLI of andere programmatische Azure-clients. Azure Defender voert geavanceerde beveiligingsanalyses uit om bedreigingen te detecteren en u te waarschuwen over verdachte activiteiten.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Prijzen:|Voor **Azure Defender voor Resource Manager** gelden de prijzen zoals op [de pagina Prijzen](security-center-pricing.md) wordt getoond|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Wat zijn de voordelen van Azure Defender voor Resource Manager?

Azure Defender voor Resource Manager beschermt tegen problemen, waaronder:

- **Verdachte bewerkingen in resourcebeheer**, zoals bewerkingen vanaf verdachte IP-adressen, het uitschakelen van antimalware en verdachte scripts die in VM-extensies worden uitgevoerd
- **Gebruik van exploitatietoolkits**, zoals Microburst of PowerZure
- **Zijdelingse verplaatsing** van de Azure-beheerlaag naar het gegevensvlak van Azure-resources

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Overzichtsdiagram van Azure Resource Manager":::

Een volledige lijst met waarschuwingen die door Azure Defender voor Resource Manager wordt verstrekt, vindt u op de [pagina met waarschuwingen](alerts-reference.md#alerts-resourcemanager).


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Waarschuwingen van Azure Defender voor Resource Manager onderzoeken

Beveiligingswaarschuwingen van Azure Defender voor Resource Manager zijn gebaseerd op bedreigingen die zijn gedetecteerd door Azure Resource Manager-bewerkingen te bewaken. Azure Defender maakt gebruik van interne logboekbronnen van Azure Resource Manager en van het Azure Activity-logboek, een platformlogboek in Azure dat inzicht biedt in gebeurtenissen op abonnementsniveau.

Meer informatie over het [Azure Activity-logboek](../azure-monitor/platform/activity-log.md).

Beveiligingswaarschuwingen onderzoeken vanuit Azure Defender voor Resource Manager:

1. Open het Azure Activity-logboek.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Het Azure Activity-logboek openen":::

1. Filter de gebeurtenissen op:
    - Het abonnement dat in de waarschuwing wordt vermeld
    - Het tijdsbestek van de gedetecteerde activiteit
    - Het gerelateerde gebruikersaccount (indien van toepassing)

1. Let op verdachte activiteiten.

> [!TIP]
> Voor een betere, grondiger onderzoekservaring kunt u uw Azure Activity-logboeken streamen naar Azure Sentinel, zoals beschreven in [Verbinding maken met gegevens vanuit het Azure Activity-logboek](../sentinel/connect-azure-activity.md).



## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over Azure Defender voor Resource Manager. Raadpleeg het volgende artikel voor gerelateerd materiaal: 

- Beveiligingswaarschuwingen kunnen worden gegenereerd door Security Center of door Security Center worden ontvangen vanuit andere beveiligingsproducten. Als u al deze waarschuwingen wilt exporteren naar Azure Sentinel, een externe SIEM of een ander extern hulpprogramma, volgt u de instructies in [Waarschuwingen naar een SIEM exporteren](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Azure Defender inschakelen](security-center-pricing.md)