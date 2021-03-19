---
title: Aangepaste acties en resources maken
description: Deze zelfstudie gaat over het maken van aangepaste acties en resources in Azure Resource Manager. Daarnaast ziet u hoe aangepaste werkstromen compatibel zijn met Azure Resource Manager-sjablonen, Azure CLI, Azure Policy en het Azure-activiteitenlogboek.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75649895"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Aangepaste acties en resources in Azure maken

Een aangepaste provider is een contract tussen Azure en een eindpunt. Met aangepaste providers kunt u werkstromen in Azure wijzigen door nieuwe API's aan Azure Resource Manager toe te voegen. Met deze aangepaste API's kan Resource Manager gebruikmaken van nieuwe implementatie- en beheermogelijkheden.

In deze zelfstudie wordt met een eenvoudig voorbeeld uitgelegd hoe nieuwe acties en resources aan Azure kunnen worden toegevoegd en hoe u deze kunt integreren.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure Functions instellen voor aangepaste Azure-providers

In deel één van deze zelfstudie wordt beschreven hoe u een Azure-functie-app instelt voor gebruik met aangepaste providers:

- [Azure Functions instellen voor aangepaste Azure-providers](./tutorial-custom-providers-function-setup.md)

Aangepaste providers kunnen werken met elke openbare URL.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Een RESTful-eindpunt ontwerpen voor aangepaste providers

In deel twee van deze zelfstudie wordt beschreven hoe u een RESTful-eindpunt kunt ontwerpen voor aangepaste providers:

- [Een RESTful-eindpunt ontwerpen voor aangepaste providers](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Een aangepaste provider maken en gebruiken

In deel drie van deze zelfstudie wordt beschreven hoe u een aangepaste provider maakt en de aangepaste acties en resources gebruikt:

- [Een aangepaste provider maken en gebruiken](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u kennisgemaakt met aangepaste providers en hoe u er een kunt maken. Als u door wilt gaan met de volgende zelfstudie, gaat u naar [Zelfstudie: Azure Functions instellen voor aangepaste Azure-providers](./tutorial-custom-providers-function-setup.md).

Als u referenties of een quickstart zoekt, vindt u hier enkele nuttige koppelingen:

- [Snelstart: een aangepaste Azure-provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Procedure: Aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedure: Aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
