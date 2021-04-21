---
title: Voorbeeld van Azure CLI-script - App Service integreren met Application Gateway | Microsoft Docs
description: Voorbeeld van Azure CLI-script - App Service integreren met Application Gateway
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: d30cc27fc3c546619e85bb9aabd0b31c10102e96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787805"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>App Service integreren met Application Gateway met behulp van CLI

Met dit voorbeeldscript maakt u een Azure App Service web-app, een virtueel Azure-netwerk en een toepassingsgateway. Vervolgens wordt het verkeer voor de web-app beperkt tot alleen afkomstig uit het subnet van de toepassingsgateway.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0.74 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, App Service-app, Cosmos DB en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [`az network vnet create`](/cli/azure/network/vnet#az_network_vnet_create) | Hiermee maakt u een virtueel netwerk. |
| [`az network public-ip create`](/cli/azure/network/public-ip#az_network_public_ip_create) | Hiermee maakt u een openbaar IP-adres. |
| [`az network public-ip show`](/cli/azure/network/public-ip#az_network_public_ip_show) | Details van een openbaar IP-adres weergeven. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Hiermee maakt u een App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Hiermee maakt u een App Service-web-app. |
| [`az webapp show`](/cli/azure/webapp#az_webapp_show) | Details van een App Service-web-app weergeven. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az_webapp_config_access_restriction_add) | Hiermee voegt u een toegangsbeperking toe aan de App Service-web-app. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az_network_application_gateway_create) | Hiermee maakt u een toepassingsgateway. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az_network-application-gateway-http_settings_update) | Hiermee werkt u HTTP-instellingen van Application Gateway bij. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer voorbeelden van App Service CLI-scripts vindt u in de [documentatie van Azure App Service](../samples-cli.md).
