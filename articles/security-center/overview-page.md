---
title: Het hoofddashboard of de overzichtspagina van Azure Security Center
description: Meer informatie over de functies van de overzichtspagina van Security Center
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 768d69b626a870910d6734e1a1ddc29871f96afb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099774"
---
# <a name="azure-security-centers-overview-page"></a>Overzichtspagina van Azure Security Center

Wanneer u Azure Security Center opent, wordt als eerste de overzichtspagina weergegeven. 

Dit interactieve dash board biedt een uniforme weer gave van de beveiligings postuur van uw hybride Cloud werkbelastingen. Bovendien worden er naast andere informatie beveiligingswaarschuwingen en informatie over de dekking weergegeven.

U kunt elk element op de pagina selecteren voor meer gedetailleerde informatie.

:::image type="content" source="media/overview-page/overview.png" alt-text="Overzichtspagina van Security Cente":::

## <a name="features-of-the-overview-page"></a>Functies van de overzichtspagina

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Bovenste balk van de overzichtspagina van Security Center":::

De **bovenste menubalk** biedt het volgende:
- **Abonnementen**: u kunt de lijst met abonnementen bekijken en filteren door deze knop te selecteren. Security Center past de weergave aan om het beveiligingspostuur van de geselecteerde abonnementen weer te geven.
- **Wat is er nieuw**: hiermee opent u de [opmerkingen bij de release](release-notes.md) zodat u op de hoogte blijft van nieuwe functies, foutoplossingen en afgeschafte functionaliteit.
- **Nummers op hoog niveau** voor de verbonden cloudaccounts, om de context van de informatie in de onderstaande hoofdtegels weer te geven. Evenals het aantal geschatte resources, actieve aanbevelingen en beveiligings waarschuwingen. Selecteer het aantal geschatte resources om toegang te krijgen tot de [Asset Inventory](asset-inventory.md). Meer informatie over het verbinden van uw [AWS-accounts](quickstart-onboard-aws.md) en uw [GCP-projecten](quickstart-onboard-gcp.md).


Midden op de pagina bevinden zich **vier centrale tegels**, die elk gekoppeld zijn aan een speciaal dashboard voor meer informatie:
- **Veiligheidsscore**: het Security Center controleert uw resources, abonnementen en organisatie doorlopend op beveiligingsproblemen. Vervolgens worden alle bevindingen tot één enkele score samengevoegd, zodat u in een oogopslag uw huidige beveiligingssituatie kunt zien: hoe hoger de score, hoe lager het geïdentificeerde risiconiveau is. [Meer informatie](secure-score-security-controls.md).
- **Reglementaire naleving** -Security Center biedt inzicht in uw nalevings postuur op basis van doorlopende evaluaties van uw Azure-omgeving. Security Center analyseert risicofactoren in uw hybride cloudomgeving overeenkomstig best practices voor beveiliging. Deze evaluaties worden vanuit een ondersteunde set standaarden toegewezen aan nalevingscontroles. [Meer informatie](security-center-compliance-dashboard.md).
- **Azure Defender** : dit is het Cloud werkbelasting platform (CWPP) geïntegreerd in Security Center voor geavanceerde, intelligente beveiliging van uw Azure-en hybride werk belastingen. De tegel toont de dekking van uw verbonden resources (voor de momenteel geselecteerde abonnementen) en de recente waarschuwingen, met een kleurcode voor de mate van ernst. [Meer informatie](azure-defender.md).
- **Firewall Manager** : in deze tegel ziet u de status van uw hubs en netwerken van [Azure firewall Manager](../firewall-manager/overview.md). 


Het deelvenster **Inzichten** biedt aangepaste items voor uw omgeving, waaronder:
- Uw resources die het vaakst zijn aangevallen
- Uw beveiligingsmechanismen met de grootste kans om uw beveiligingsscore te verhogen
- De actieve aanbevelingen met de resources die het meest worden beïnvloed
- Recente blogposts van Azure Security Center-experts

## <a name="next-steps"></a>Volgende stappen

Op deze pagina maakt u kennis met de overzichtspagina van Security Center. Zie voor verwante informatie:

- [Verken en beheer uw resources met hulpprogramma's voor assetvoorraad en -beheer](asset-inventory.md)
- [Beveiligingsscore in Azure Security Center](secure-score-security-controls.md)