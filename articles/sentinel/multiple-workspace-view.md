---
title: Werk met Azure-Sentinel-incidenten in veel werk ruimten tegelijk | Microsoft Docs
description: Incidenten in meerdere werk ruimten gelijktijdig weer geven in azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83124166"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Werk met incidenten in veel werk ruimten tegelijk 

 Om optimaal te profiteren van de mogelijkheden van Azure Sentinel, raadt micro soft aan een omgeving met één werk ruimte te gebruiken. Er zijn echter enkele use-cases die meerdere werk ruimten vereisen, in sommige gevallen, bijvoorbeeld die van een [beheerde Security service provider (MSSP)](./multiple-tenants-service-providers.md) en de klanten, over meerdere tenants. Met de **weer gave van meerdere werk ruimten** kunt u beveiligings incidenten in meerdere werk ruimten tegelijk bekijken en ermee werken, zelfs via tenants, zodat u de volledige zicht baarheid en controle kunt krijgen over de reactie snelheid van uw organisatie.

## <a name="entering-multiple-workspace-view"></a>Een weer gave met meerdere werk ruimten invoeren

Wanneer u Azure Sentinel opent, wordt er een lijst weer gegeven met alle werk ruimten waarvoor u toegangs rechten hebt, in alle geselecteerde tenants en abonnementen. Links van elke werkruimte naam is een selectie vakje. Als u op de naam van één werk ruimte klikt, gaat u naar die werk ruimte. Als u meerdere werk ruimten wilt kiezen, klikt u op alle bijbehorende selectie vakjes en klikt u vervolgens op de knop **voor het weer geven van meerdere werk ruimte** aan de bovenkant van de pagina.

> [!IMPORTANT]
> De weer gave van meerdere werk ruimten ondersteunt momenteel Maxi maal 10 gelijktijdig weer gegeven werk ruimten. 
> 
> Als u meer dan 10 werk ruimten controleert, wordt er een waarschuwings bericht weer gegeven.

In de lijst met werk ruimten ziet u de map, het abonnement, de locatie en de resource groep die is gekoppeld aan elke werk ruimte. De Directory komt overeen met de Tenant.

   ![Meerdere werk ruimten kiezen](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Werken met incidenten

In de **weer gave van meerdere werk ruimten** is alleen het scherm **incidenten** beschikbaar. Het zoekt en werkt op de meeste manieren zoals het scherm reguliere **incidenten** . Er zijn enkele belang rijke verschillen, maar:

   ![Incidenten weer geven in meerdere werk ruimten](./media/multiple-workspace-view/incidents.png)

- De tellers boven aan de pagina- *Open incidenten*, *nieuwe incidenten*, worden *uitgevoerd*, enzovoort: de getallen voor alle geselecteerde werk ruimten collectief weer geven.

- U ziet incidenten van alle geselecteerde werk ruimten (tenants) in één geïntegreerde lijst. U kunt de lijst filteren op werk ruimte en map, naast de filters in het scherm reguliere **incidenten** .

- U hebt lees-en schrijf machtigingen nodig voor alle werk ruimten van waaruit u incidenten hebt geselecteerd. Als u alleen lees machtigingen voor sommige werk ruimten hebt, worden er waarschuwings berichten weer gegeven als u incidenten in deze werk ruimten selecteert. U kunt deze incidenten niet wijzigen of anderen die u hebt geselecteerd samen met die gevallen (zelfs als u wel machtigingen hebt voor de andere).

- Als u één incident kiest en op **volledige details weer geven** of **onderzoek** klikt, gaat u vervolgens naar de gegevens context van de werk ruimte van dat incident en geen andere.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u incidenten tegelijk kunt weer geven en bewerken in meerdere Azure Sentinel-werk ruimten. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

