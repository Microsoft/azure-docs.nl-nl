---
title: Verkeer doorverrouteeren voor toepassingen met een ha - Azure CLI - Traffic Manager
description: Voorbeeld van Azure CLI-script - Verkeer omgeleid voor hoge beschikbaarheid van toepassingen
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: twooley
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: duau
ms.openlocfilehash: 151cfc4ffb984b25902ae6d5dd69516a5e974c93
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788329"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Verkeer omgeleid voor hoge beschikbaarheid van toepassingen met behulp van Azure CLI

Met dit script maakt u een resourcegroep, twee App Service-plannen, twee web-apps, een Traffic Manager-profiel en twee Traffic Manager-eindpunten. Traffic Manager leidt verkeer naar de toepassing in één regio als primaire regio en naar de secundaire regio wanneer de toepassing in de primaire regio niet beschikbaar is. Voordat u het script gaat uitvoeren, moet u de waarden MyWebApp, MyWebAppL1 en MyWebAppL2 wijzigen in unieke waarden in Azure. Nadat het script is uitgevoerd, hebt u toegang tot de app in de primaire regio met de URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, App Service app en alle gerelateerde resources te verwijderen.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, web-app, Traffic Manager-profiel en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az appservice plan create](/cli/azure/appservice/plan) | Hiermee maakt u een App Service-plan. Dit is net als een serverfarm voor uw Azure-web-app. |
| [az webapp web create](/cli/azure/webapp#az_webapp_create) | Hiermee maakt u een Azure-web-app binnen App Service abonnement. |
| [az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile) | Hiermee maakt u een Azure Traffic Manager-profiel. |
| [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint) | Hiermee voegt u een eindpunt toe aan een Azure Traffic Manager-profiel. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer App Service CLI-scriptvoorbeelden vindt u in de [Azure-netwerken documentatie.](../cli-samples.md)