---
title: Hosting van Azure Functions-verbruiks abonnement
description: Meer informatie over hoe u met Azure function plan hosting uw code kunt uitvoeren in een omgeving die dynamisch wordt geschaald, maar u betaalt alleen voor resources die worden gebruikt tijdens de uitvoering.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760547"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Hosting van Azure Functions-verbruiks abonnement

Wanneer u het verbruiks abonnement gebruikt, worden instanties van de Azure Functions-host dynamisch toegevoegd en verwijderd op basis van het aantal binnenkomende gebeurtenissen. Het verbruiks abonnement is de volledig <em>serverloze</em> Hosting optie voor Azure functions.

## <a name="benefits"></a>Voordelen

Het verbruiks abonnement wordt automatisch geschaald, zelfs tijdens het aantal Peri Oden met een hoge belasting. Bij het uitvoeren van functions in een verbruiks abonnement worden alleen reken bronnen in rekening gebracht wanneer uw functies worden uitgevoerd. Bij een verbruiksabonnement treedt er na een ingestelde periode een time-out op voor een functie-uitvoering.

Zie [functie schaal en hosting opties](functions-scale.md)voor een vergelijking van het verbruiks plan voor het andere plan en de hosting typen.

## <a name="billing"></a>Billing

De facturering is dan ook gebaseerd op het aantal uitvoeringen, de uitvoeringstijd en het gebruikte geheugen. Het gebruik wordt geaggregeerd voor alle functies in een functie-app. Zie de pagina met prijzen voor [Azure functions](https://azure.microsoft.com/pricing/details/functions/)voor meer informatie.

Zie voor meer informatie over het schatten van kosten bij het uitvoeren van een verbruiks abonnement de [kosten van verbruiks plan](functions-consumption-costs.md).

## <a name="create-a-consumption-plan-function-app"></a>Een functie-app voor een verbruiks plan maken

Wanneer u een functie-app maakt in de Azure Portal, is het verbruiks abonnement de standaard instelling. Wanneer u Api's gebruikt om een functie-app te maken, hoeft u niet eerst een App Service-abonnement te maken, zoals u dat ook met Premium en speciale abonnementen kunt doen.

Gebruik de volgende koppelingen voor meer informatie over het maken van een serverloze functie-app in een verbruiks abonnement, hetzij programmatisch, hetzij in de Azure Portal:

+ [Azure-CLI](./scripts/functions-cli-create-serverless.md)
+ [Azure-portal](./functions-get-started.md)
+ [Azure Resource Manager-sjabloon](functions-create-first-function-resource-manager.md)

U kunt ook functie-apps maken in een verbruiks abonnement wanneer u een functions-project publiceert vanuit [Visual Studio code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) of [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure).

## <a name="multiple-apps-in-the-same-plan"></a>Meerdere apps in hetzelfde abonnement

Functie-apps in dezelfde regio kunnen worden toegewezen aan hetzelfde verbruiks plan. Er is geen nadeel of gevolgen voor het uitvoeren van meerdere apps in hetzelfde verbruiks abonnement. Het toewijzen van meerdere apps aan hetzelfde verbruiks plan heeft geen invloed op de flexibiliteit, schaal baarheid of betrouw baarheid van elke app.

## <a name="next-steps"></a>Volgende stappen

+ [Opties voor Azure Functions hosten](functions-scale.md)
+ [Op gebeurtenissen gebaseerd schalen in Azure Functions](event-driven-scaling.md)
