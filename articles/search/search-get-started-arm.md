---
title: Een Azure Resource Manager-sjabloon gebruiken om uw service te implementeren
titleSuffix: Azure Cognitive Search
description: U kunt snel een instantie van de Azure Cognitive Search-service implementeren met behulp van de Azure Resource Manager-sjabloon.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 11/17/2020
ms.openlocfilehash: 12aa7f123ec5e417d710636f2afb4c60231cefc1
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94746547"
---
# <a name="quickstart-deploy-cognitive-search-using-an-arm-template"></a>Quickstart: Cognitive Search implementeren met behulp van een ARM-sjabloon

In dit artikel wordt stapsgewijs uitgelegd hoe u met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon) een Azure Cognitive Search-resource implementeert in de Azure-portal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json":::

De Azure-resource die in deze sjabloon is gedefinieerd:

- [Microsoft.Search/searchServices](/azure/templates/Microsoft.Search/searchServices): een Azure Cognitive Search-service maken

## <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon maakt u een Azure Cognitive Search-resource.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

In de portal wordt een formulier weergegeven waarmee u eenvoudig parameterwaarden kunt opgeven. Sommige parameters worden vooraf ingevuld met de standaardwaarden uit de sjabloon. U moet uw abonnement, de resourcegroep, locatie en servicenaam opgeven. Als u Cognitive Services wilt gebruiken in een pijplijn voor [AI-verrijking](cognitive-search-concept-intro.md), bijvoorbeeld om binaire afbeeldingsbestanden voor tekst te analyseren, kiest u een locatie die zowel Cognitive Search als Cognitive Services biedt. Beide services moeten zich in dezelfde regio bevinden voor AI-verrijkingsworkloads. Wanneer u het formulier hebt ingevuld, moet u akkoord gaan met de voorwaarden en vervolgens de knop Kopen selecteren om uw implementatie te voltooien.

> [!div class="mx-imgBorder"]
> ![Weergave van sjabloon in Azure Portal](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Wanneer de implementatie is voltooid, hebt u toegang tot uw nieuwe resourcegroep en nieuwe zoekservice in de portal.

## <a name="clean-up-resources"></a>Resources opschonen

Andere Cognitive Search-snelstarts en zelfstudies zijn gebaseerd op deze quickstart. Als u van plan bent om volgende quickstarts en zelfstudies te gaan gebruiken, wilt u deze resource misschien bewaren. Als u de resourcegroep niet meer nodig hebt, kunt u deze verwijderen. Hierdoor worden ook de Cognitive Search-service en de gerelateerde resources verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Cognitive Search-service gemaakt met behulp van een ARM-sjabloon en de implementatie gevalideerd. Als u meer wilt weten over Cognitive Search en Azure Resource Manager vindt u meer informatie in de onderstaande artikelen.

- Lees een [overzicht van Azure Cognitive Search](search-what-is-azure-search.md).
- [Een index maken](search-get-started-portal.md) voor uw zoekservice.
- [Een demo-app maken](search-create-app-portal.md) met behulp van de portalwizard.
- [Een vaardighedenset maken](cognitive-search-quickstart-blob.md) om informatie op te halen uit uw gegevens.
