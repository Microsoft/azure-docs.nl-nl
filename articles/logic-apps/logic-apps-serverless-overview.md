---
title: 'Overzicht: Azure Serverloze voor Cloud-apps en-oplossingen'
description: Meer informatie over het maken van Cloud-apps en-oplossingen zonder de infra structuur te gebruiken met behulp van Azure Logic Apps en Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 470857883144f4bf8c21a1921afacb7ba3f5ca00
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788092"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure Serverloze: overzicht voor het bouwen van Cloud-apps en-oplossingen met Azure Logic Apps en Azure Functions

[Serverloze](https://azure.microsoft.com/solutions/serverless/) apps bieden voor delen zoals verhoogde ontwikkelings snelheid, gereduceerde code, eenvoud en schaal. In dit artikel worden de verschillende kenmerken van serverloze oplossingen en Azure serverloze aanbiedingen besproken.

## <a name="what-is-serverless"></a>Wat is serverloze?

Serverloos betekent niet dat er geen servers zijn, maar dat ontwikkel aars geen zorgen hoeven te maken over servers. Een groot deel van traditionele toepassings ontwikkeling is het beantwoorden van vragen over schaal-, hosting-en bewakings oplossingen om te voldoen aan de vereisten van de toepassing. Met serverloze worden deze vragen gezien als onderdeel van de oplossing. Daarnaast worden serverloze apps gefactureerd op basis van een op verbruik gebaseerd abonnement. Als de app nooit wordt gebruikt, worden er geen kosten in rekening gebracht. Deze functies helpen ontwikkel aars zich alleen te concentreren op de bedrijfs logica van een oplossing.

De basis-Azure-Services voor serverloze zijn [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/) en [Azure functions](https://azure.microsoft.com/services/functions/). Beide oplossingen volgen de eerder beschreven principes en helpen ontwikkel aars om robuuste Cloud-apps met minimale code te bouwen.

## <a name="what-is-azure-logic-apps"></a>Wat is Azure Logic Apps?

[Azure Logic apps](logic-apps-overview.md) biedt een manier om schaal bare integraties en werk stromen in de cloud te vereenvoudigen en te implementeren. Deze service biedt een visuele ontwerper om uw proces te model leren en te automatiseren als een reeks stappen die een werk stroom wordt genoemd. Er zijn veel [connectors](../connectors/apis-list.md) in Cloud Services en on-premises systemen waarmee snel een serverloze app kan worden verbonden met andere api's. Elke logische app begint met een trigger, bijvoorbeeld ' wanneer een account wordt toegevoegd aan Dynamics CRM '. Nadat de trigger is geactiveerd, kan de werk stroom combi Naties van acties, conversies en voorwaardelijke logica uitvoeren. Logic Apps is een fantastische keuze bij het indelen van verschillende Azure Functions in een proces, met name wanneer het proces interactie met een extern systeem of API vereist.

Begin met [het maken van uw eerste logische app](quickstart-create-first-logic-app-workflow.md)om aan de slag te gaan met Logic apps. Zie voor meer technische informatie over Logic Apps de [naslag voor ontwikkel aars](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Wat is Azure Functions?

Azure Functions is een service waarmee u eenvoudig stukjes code of ' functions ' in de cloud kunt uitvoeren. U kunt alleen de code schrijven die nodig is voor het huidige probleem, zonder dat u zich zorgen hoeft te maken over een volledige app of de vereiste infra structuur. Met functies kunnen ontwikkel aars nog productiever worden, en kunt u uw gewenste programmeer taal gebruiken, zoals C#, F #, Node.js, python of PHP. U betaalt alleen voor de tijd dat uw code wordt uitgevoerd en Azure wordt zo nodig geschaald.

Begin met [het maken van uw eerste Azure-functie](../azure-functions/functions-get-started.md)om aan de slag te gaan met Azure functions. Zie Naslag informatie voor [ontwikkel aars](../azure-functions/functions-reference.md)voor meer technische gegevens over functies.

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Hoe kan ik serverloze apps bouwen en implementeren in azure?

Azure biedt uitgebreide hulp middelen voor het ontwikkelen, implementeren en beheren van serverloze apps. U kunt apps rechtstreeks bouwen in de Azure Portal, met [hulpprogram ma's in Visual Studio](logic-apps-serverless-get-started-vs.md)of [Visual Studio code](quickstart-create-logic-apps-visual-studio-code.md). Nadat u uw app hebt gemaakt, kunt u [die app snel implementeren met Azure Resource Manager sjablonen](logic-apps-deploy-azure-resource-manager-templates.md). Azure biedt ook bewaking, die u via de Azure Portal kunt openen, via de API of Sdk's, of met geïntegreerde hulp middelen voor Azure Monitor logboeken en Application Insights.

## <a name="next-steps"></a>Volgende stappen

* [Een serverloze app maken in Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Een dash board voor klanten inzichten maken met serverloos](logic-apps-scenario-social-serverless.md)
* [Implementatie van logische apps automatiseren](logic-apps-azure-resource-manager-templates-overview.md)