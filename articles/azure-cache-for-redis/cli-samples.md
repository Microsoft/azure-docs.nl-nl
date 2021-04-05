---
title: Azure-cache beheren voor redis met Azure CLI
description: 'Azure CLI-voor beelden voor het beheren van Azure cache voor redis: een cache maken, een cache verwijderen, Details van de cache ophalen, hostnaam, poorten en sleutels, verbinding maken met een web-app.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 32530982bc2a9d1b5deb31b3bc71460462352258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536416"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Azure-cache beheren voor redis met Azure CLI

De volgende tabel bevat koppelingen naar bash-scripts die zijn gebouwd met behulp van de Azure CLI.

| Cache maken | Description |
| ------------ | ----------- |
| [Een cache maken](./scripts/create-cache.md) | Hiermee maakt u een resource groep en een Azure-cache van de Basic-laag voor redis. |
| [Een Premium-cache maken met clustering](./scripts/create-premium-cache-cluster.md) | Hiermee maakt u een resource groep en een Premium-laag cache waarvoor Clustering is ingeschakeld.|
| [Details van cache ophalen](./scripts/show-cache.md) | Hiermee worden gegevens opgehaald van een Azure-cache voor een redis-instantie, met inbegrip van de inrichtings status. |
| [De hostnaam, poorten en sleutels ophalen](./scripts/cache-keys-ports.md) | Hiermee worden de hostnaam, poorten en sleutels voor een Azure-cache voor redis-exemplaar opgehaald. |
|**Web-app plus cache**| **Beschrijving**|
| [Een web-app verbinden met Azure Cache voor Redis](./../app-service/scripts/cli-connect-to-redis.md) | Hiermee maakt u een Azure-web-app en een Azure-cache voor redis. vervolgens worden de verbindings gegevens van de redis toegevoegd aan de app-instellingen. |
|**Cache verwijderen**| **Beschrijving** |
| [Een cache verwijderen](./scripts/delete-cache.md) | Hiermee wordt een Azure-cache voor redis-exemplaar verwijderd  |

Zie [de Azure cli installeren](/cli/azure/install-azure-cli) en aan de [slag met Azure cli](/cli/azure/get-started-with-azure-cli)voor meer informatie over de Azure cli.