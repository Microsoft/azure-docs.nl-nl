---
title: Een Azure Resource Manager-sjabloon gebruiken om een privé-eindpunt voor uw web-app te implementeren
description: Meer informatie over het gebruik van een ARM-sjabloon voor het implementeren van een privé-eindpunt voor uw web-app.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89652021"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Een App Service-app maken en een privé-eindpunt implementeren met behulp van een Azure Resource Manager-sjabloon

In deze quickstart gebruikt u een Azure Resource Manager-sjabloon (ARM) om een web-app te maken en deze open te stellen met een privé-eindpunt.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Vereiste

U hebt een Azure-account met een actief abonnement nodig. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

Met deze sjabloon maakt u een privé-eindpunt voor een Azure-web-app.

### <a name="review-the-template"></a>De sjabloon controleren

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>De sjabloon implementeren

Dit is hoe u de Azure Resource Manager-sjabloon implementeert in Azure:

1. Selecteer de volgende koppeling om u aan te melden bij Azure en de sjabloon te openen:  [Implementeren in Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json). Met de sjabloon maakt u het virtuele netwerk, de web-app, het privé-eindpunt en de privé-DNS-zone.
2. Selecteer of maak een resourcegroep.
3. Voer de naam van uw web-app, App Service-plan en privé-eindpunt in.
5. Lees de verklaring met betrekking tot de algemene voorwaarden. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** > **Kopen** als u akkoord gaat. De implementatie kan enkele minuten duren.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met het privé-eindpunt hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u het privé-eindpunt en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

- Meer Azure Resource Manager-sjablonen voor Azure App Service-web-apps vindt u in de [voorbeelden van ARM-sjablonen](../samples-resource-manager-templates.md).
