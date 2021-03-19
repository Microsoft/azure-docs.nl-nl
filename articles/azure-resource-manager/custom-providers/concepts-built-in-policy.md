---
title: Koppelingen implementeren met behulp van beleid
description: Meer informatie over het implementeren van koppelingen voor een aangepaste provider met behulp van Azure Policy-service.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 00cd1d39c0110aac9ea96f73127e83197976c95a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82190126"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Koppelingen voor een aangepaste provider implementeren met behulp van Azure Policy

Azure-beleid kan worden gebruikt voor het implementeren van koppelingen om resources te koppelen aan een aangepaste provider. In dit artikel wordt een ingebouwd beleid beschreven waarmee koppelingen worden geïmplementeerd en hoe u dit beleid kunt gebruiken.

## <a name="built-in-policy-to-deploy-associations"></a>Ingebouwd beleid voor het implementeren van koppelingen

Het implementeren van koppelingen voor een aangepaste provider is een ingebouwd beleid dat kan worden gebruikt voor het implementeren van een koppeling om een resource te koppelen aan een aangepaste provider. Het beleid accepteert drie para meters:

- Aangepaste provider-id: deze ID is de resource-ID van de aangepaste provider waaraan de resources moeten worden gekoppeld.
- Resource typen die u wilt koppelen: deze resource typen zijn de lijst met resource typen die aan de aangepaste provider moeten worden gekoppeld. U kunt meerdere resource typen aan een aangepaste provider koppelen met hetzelfde beleid.
- Voor voegsel van koppelings naam: deze teken reeks is het voor voegsel dat moet worden toegevoegd aan de naam van de koppelings bron die wordt gemaakt. De standaard waarde is ' DeployedByPolicy '.

Het beleid maakt gebruik van DeployIfNotExists-evaluatie. Het wordt uitgevoerd nadat een resource provider een aanvraag voor het maken of bijwerken van een resource heeft afgehandeld en de evaluatie de status code geslaagd heeft geretourneerd. Daarna wordt de koppelings bron geïmplementeerd met een sjabloon implementatie.
Zie voor meer informatie over koppelingen [Azure Custom providers resource onboarding](./concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Het ingebouwde beleid voor het implementeren van koppelingen gebruiken 

### <a name="prerequisites"></a>Vereisten
Als de aangepaste provider machtigingen nodig heeft voor het bereik van het beleid om een actie uit te voeren, werkt de beleids implementatie van koppelings bron niet zonder de machtigingen toe te kennen.

### <a name="policy-assignment"></a>Beleidstoewijzing
Als u het ingebouwde beleid wilt gebruiken, maakt u een beleids toewijzing en wijst u de Deploy-koppelingen voor een aangepast provider beleid toe. Het beleid identificeert vervolgens niet-compatibele resources en implementeert de koppeling voor deze resources.

![Ingebouwd beleid toewijzen](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Ondersteuning vragen

Als u vragen hebt over het ontwikkelen van aangepaste Azure-resource providers, vraagt u deze op [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Misschien is een soortgelijke vraag al eerder gesteld en beantwoord, dus controleer dit eerst vóór u een vraag stelt. Voeg de tag ```azure-custom-providers``` toe om snel antwoord te krijgen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel vindt u informatie over het gebruik van ingebouwd beleid voor het implementeren van koppelingen. Zie voor meer informatie de volgende artikelen:

- [Concepten: voor bereiding van resources in azure Custom providers](./concepts-resource-onboarding.md)
- [Zelf studie: onboarding van resources met aangepaste providers](./tutorial-resource-onboarding.md)
- [Zelf studie: aangepaste acties en resources maken in azure](./tutorial-get-started-with-custom-providers.md)
- [Snelstartgids: een aangepaste resource provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Procedure: aangepaste acties toevoegen aan een Azure-REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedure: aangepaste resources toevoegen aan een Azure-REST API](./custom-providers-resources-endpoint-how-to.md)
