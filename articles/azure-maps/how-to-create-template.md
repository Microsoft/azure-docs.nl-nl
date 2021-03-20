---
title: Uw Azure Maps-account maken met een ARM-sjabloon | Microsoft Azure kaarten
description: Meer informatie over het maken van een Azure Maps-account met behulp van een Azure Resource Manager-sjabloon (ARM).
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92525117"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>Uw Azure Maps-account maken met een ARM-sjabloon

U kunt uw Azure Maps-account maken met behulp van een Azure Resource Manager ARM-sjabloon. Nadat u een account hebt, kunt u de Api's implementeren in uw website of mobiele toepassing.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van dit artikel, is het volgende vereist:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-maps-create/).

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

De Azure Maps-account resource is in deze sjabloon gedefinieerd:

* [**Micro soft. Maps/accounts**](/azure/templates/microsoft.maps/accounts): een Azure Maps-account maken.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon wordt een Azure Maps-account gemaakt.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

    ![ARM-sjabloon implementatie Portal](./media/how-to-create-template/create-account-using-template-portal.png)

    Tenzij deze is opgegeven, gebruikt u de standaard waarde om uw Azure Maps-account te maken.

    * **Abonnement**: selecteer een Azure-abonnement.
    * **Resourcegroep**: selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en klik op **OK**.
    * **Locatie**: selecteer een locatie. Bijvoorbeeld VS- **West 2**.
    * **Account naam**: Voer een naam in voor uw Azure Maps-account, dat wereld wijd uniek moet zijn.
    * **Prijs categorie**: Selecteer de juiste prijs categorie. de standaard waarde voor de sjabloon is s0.

3. Selecteer **Controleren + maken**. 
4. Bevestig de instellingen op de pagina controleren en klik op **maken**. Nadat uw Azure Maps is geïmplementeerd, ontvangt u een melding:

    ![Melding voor ARM-sjabloon implementatie Portal](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

De Azure Portal wordt gebruikt voor het implementeren van uw sjabloon. U kunt ook Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U kunt de Azure Portal gebruiken om uw Azure Maps account te controleren en uw sleutels weer te geven. U kunt ook het volgende Azure CLI-script gebruiken om uw account sleutels weer te geven.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, waarmee ook het Azure Maps-account wordt verwijderd. De resource groep verwijderen met behulp van Azure CLI:

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Ga verder met de volgende artikelen voor meer informatie over Azure Maps en Azure Resource Manager.

- Een Azure Maps- [demo toepassing](quick-demo-map-app.md) maken
- Meer informatie over [arm-sjablonen](../azure-resource-manager/templates/overview.md)