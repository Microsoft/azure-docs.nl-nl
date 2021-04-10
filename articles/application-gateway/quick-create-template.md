---
title: 'Quickstart: Webverkeer omleiden met behulp van een Resource Manager-sjabloon'
titleSuffix: Azure Application Gateway
description: In deze quickstart leert u hoe u een Resource Manager-sjabloon kunt gebruiken om een Azure-toepassingsgateway te maken om webverkeer om te leiden naar virtuele machines in een back-endpool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/20/2021
ms.author: victorh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: 05e7cd8d7018759a3c8670a610b4c98ac0a86a7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660052"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>Quickstart: Webverkeer omleiden met Azure Application Gateway - ARM-sjabloon

In deze quickstart gebruikt u een ARM-sjabloon (Azure Resource Manager-sjabloon) om een Azure Application Gateway te maken. Vervolgens test u de toepassingsgateway om er zeker van te zijn dat deze juist werkt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

U kunt deze quickstart ook voltooien met via de [Azure-portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md) of [Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>De sjabloon controleren

Met deze sjabloon wordt een eenvoudige configuratie gemaakt met een openbaar front-end-IP-adres, een basislistener om een enkele site op de toepassingsgateway te hosten, een eenvoudige regel voor het doorsturen van aanvragen, en twee virtuele machines in de back-endpool.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : een voor de toepassingsgateway en twee voor de virtuele machines.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : twee virtuele machines
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : twee voor de virtuele machines
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : om IIS en de webpagina’s te configureren

## <a name="deploy-the-template"></a>De sjabloon implementeren

Het ARM-sjabloon implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure, en open de sjabloon. Met de sjabloon maakt u een toepassingsgateway, de netwerkinfrastructuur, en twee virtuele machines in de back-endpool waarop IIS wordt uitgevoerd.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Selecteer of maak een resourcegroep, typ de gebruikersnaam en het wachtwoord voor de beheerder van de virtuele machine.
3. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**.

   De implementatie kan 20 minuten of langer duren.

## <a name="validate-the-deployment"></a>De implementatie valideren

IIS is niet vereist om de toepassingsgateway te maken, maar is geïnstalleerd om te controleren of het maken van de toepassingsgateway in Azure is geslaagd. Gebruik IIS om de toepassingsgateway te testen:

1. Zoek het openbare IP-adres voor de toepassingsgateway op de bijbehorende pagina **Overzicht**.![Noteer het openbare IP-adres van de toepassingsgateway](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png). Of selecteer **Alle bronnen**, voer in het zoekvak *myAGPublicIPAddress* in en selecteer het adres vervolgens in de zoekresultaten. Het openbare IP-adres wordt weergegeven op de pagina **Overzicht**.
2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser om het IP-adres te bekijken.
3. Controleer het antwoord. Een geldig antwoord verifieert dat de toepassingsgateway is gemaakt en verbinding kan maken met de back-end.

   ![Toepassingsgateway testen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Vernieuw de browser meerdere keren. Als het goed is, ziet u nu verbindingen naar myVM1 en myVM2.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de bij de toepassingsgateway gemaakte resources niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u de toepassingsgateway en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)
