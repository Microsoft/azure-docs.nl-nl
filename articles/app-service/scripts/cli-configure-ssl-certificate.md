---
title: 'CLI: TLS/SSL-certificaat uploaden en verbinden aan een app'
description: Meer informatie over het gebruik van de Azure CLI voor het automatiseren van de implementatie en het beheer van uw App Service-app. In dit voorbeeld ziet u hoe u een aangepast TLS/SSL-certificaat aan een app bindt.
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 5e4ee96da09f43c11e7de85bc306d3c5f24aec38
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94834860"
---
# <a name="bind-a-custom-tlsssl-certificate-to-an-app-service-app-using-cli"></a>Een aangepast TLS/SSL-certificaat verbinden aan een App Service-app met CLI

Met dit voorbeeldscript wordt een app met de bijbehorende resources in App Service gemaakt, waarna het TLS/SSL-certificaat van een aangepaste domeinnaam daaraan wordt verbonden. Voor dit voorbeeld hebt u het volgende nodig:

* Toegang tot de pagina voor DNS-configuratie van uw domeinregistrar.
* Een geldig PFX-bestand en het bijbehorende wachtwoord voor het TLS/SSL-certificaat dat u wilt uploaden en verbinden.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u Azure CLI versie 2.0 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Hiermee maakt u een App Service-plan. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Hiermee maakt u een App Service-app. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname?view=azure-cli-latest#az-webapp-config-hostname-add) | Hiermee wijst u een aangepast domein toe aan een App Service-app. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-upload) | Hiermee wordt een TLS/SSL-certificaat geüpload naar een App Service-app. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-bind) | Hiermee wordt een geüpload TLS/SSL-certificaat verbonden aan een App Service-app. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer voorbeelden van App Service CLI-scripts vindt u in de [documentatie van Azure App Service](../samples-cli.md).
