---
title: 'CLI: Privé-eindpunt voor web-app implementeren met Azure CLI'
description: Meer informatie over het gebruik van de Azure CLI voor het implementeren van een privé-eindpunt voor uw web-app
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acebc75b579b13ebb2cfad0e18057245781165ad
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175274"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Een App Service-app maken en een privé-eindpunt implementeren met behulp van Azure CLI

Met dit voorbeeldscript wordt een app gemaakt in App Service, inclusief de bijbehorende resources, en wordt vervolgens een privé-eindpunt geïmplementeerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een resource kunt maken, moet u een resourcegroep maken waar de web-app, het virtuele netwerk en andere netwerkcomponenten worden gehost. Maak een resourcegroep maken met [az group create](/cli/azure/group). In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *francecentral*:

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>Een App Service-plan maken

U moet een App Service-plan maken om uw web-app te hosten.
Maak een App Service-plan met de opdracht [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create).
In dit voorbeeld wordt een App Service-plan met de naam *myAppServicePlan* gemaakt op de locatie *francecentral* met de SKU *P1V2* en slechts één werkrol: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Een web-app maken

Nu u een App Service-plan hebt, kunt u een web-app implementeren.
Een web-app maken met [AZ appservice plan Create] (/CLI/Azure/webapp # AZ-webapp-Create.
In dit voorbeeld wordt een web-app met de naam *mySiteName* gemaakt in het plan met de naam *myAppServicePlan*

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Een VNet maken

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet). In dit voorbeeld wordt een standaard Virtual Network gemaakt met de naam *myVNet*, met één subnet genaamd *mySubnet*:

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Het subnet configureren 

U moet het subnet bijwerken om netwerkbeleid voor privé-eindpunten uit te schakelen. Update een subnet-configuratie met de naam *mySubnet* met [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>Privé-eindpunt maken

Maak het privé-eindpunt voor uw web-app met [az network private-endpoint create](/cli/azure/network/private-endpoint). In dit voorbeeld wordt een privé-eindpunt met de naam *myPrivateEndpoint* gemaakt in het VNet *myVNet* in het subnet *mySubnet*, met een verbinding genaamd *myConnectionName* met de resource-id van mijn web-app /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp; de groepsparameter is *sites* voor het type web-app. 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>De privé-zone configureren

Aan het einde moet u een privé-DNS-zone met de naam *privatelink.azurewebsites.net* maken die is gekoppeld aan het VNet om de DNS-naam van de web-app om te zetten.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.
- Meer voorbeelden van App Service CLI-scripts vindt u in de [documentatie van Azure App Service](../samples-cli.md).
