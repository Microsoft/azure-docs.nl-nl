---
title: Onjuiste configuraties voor komen met Azure Security Center
description: Meer informatie over het gebruik van de opties voor afdwingen en weigeren van Security Center op de pagina met details van aanbevelingen
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 5d172a73674195e7f64f5ef02322e2bd2d6314df
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439524"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Onjuiste configuraties voorkomen met afdwingen/weigeren

Onjuiste beveiligingsconfiguraties zijn een belangrijke oorzaak van beveiligingsincidenten. Security Center biedt nu de mogelijkheid om onjuiste configuratie van nieuwe resources met betrekking tot specifieke aanbevelingen te helpen *voorkomen*. 

Deze functie kan u helpen uw workloads veilig te houden en uw beveiligingsscore stabiel te houden.

Het afdwingen van een veilige configuratie op basis van een specifieke aanbeveling wordt aangeboden in twee modi:

- Met behulp van de optie **Weigeren** van Azure Policy, kunt u het maken van resources die niet in orde zijn, stoppen
- Met de optie **Afdwingen** kunt u profiteren van het effect **DeployIfNotExist** van Azure Policy en niet-compatibele resources automatisch herstellen wanneer deze worden gemaakt

Deze vindt u boven aan de pagina Resource Details voor geselecteerde beveiligings aanbevelingen (Zie [aanbevelingen met opties voor weigeren/afdwingen](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Het maken van resources voor komen

1. Open de aanbeveling waaraan uw nieuwe resources moeten voldoen en selecteer de knop **weigeren** boven aan de pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="De pagina aanbeveling met de knop weigeren gemarkeerd":::

    In het deel venster configuratie worden de scope opties weer gegeven. 

1. Stel het bereik in door het relevante abonnement of beheer groep te selecteren.

    > [!TIP]
    > U kunt de drie punten aan het einde van de rij gebruiken om één abonnement te wijzigen of de selectie vakjes gebruiken om meerdere abonnementen of groepen te selecteren. Selecteer vervolgens **wijzigen in weigeren**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Het bereik instellen voor Azure Policy weigeren":::


## <a name="enforce-a-secure-configuration"></a>Een veilige configuratie afdwingen

1. Open de aanbeveling voor het implementeren van een sjabloon implementatie voor als nieuwe resources niet voldoen en selecteer de knop **afdwingen** boven aan de pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="De pagina aanbeveling met de knop afdwingen gemarkeerd":::

    Het configuratie venster wordt geopend met alle configuratie opties voor het beleid. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Configuratie opties afdwingen":::

1. Stel het bereik, de naam van de toewijzing en andere relevante opties in.

1. Selecteer **Controleren + maken**.

## <a name="recommendations-with-denyenforce-options"></a>Aanbevelingen met opties voor weigeren/afdwingen

Deze aanbevelingen kunnen worden gebruikt in combi natie met de optie **weigeren** :

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

Deze aanbevelingen kunnen worden gebruikt in combi natie met de optie **afdwingen** :

- Controle op SQL-servers moet zijn ingeschakeld
- Azure Backup moet zijn ingeschakeld voor virtuele machines
- Azure Defender voor SQL moet zijn ingeschakeld voor uw SQL-servers
- Diagnostische logboeken in Azure Stream Analytics moeten zijn ingeschakeld
- Diagnostische logboeken in Batch-accounts moeten worden ingeschakeld
- Diagnostische logboeken in Data Lake Analytics moeten zijn ingeschakeld
- Diagnostische logboeken in Event Hub moeten zijn ingeschakeld
- Diagnostische logboeken in Key Vault moeten zijn ingeschakeld
- Diagnostische logboeken in Logic Apps moeten zijn ingeschakeld
- Diagnostische logboeken in zoekservices moeten zijn ingeschakeld
- Diagnostische logboeken in Service Bus moeten zijn ingeschakeld
