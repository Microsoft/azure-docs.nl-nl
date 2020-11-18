---
title: Azure CLI-vereisten voor Azure HPC-cache
description: Setup-stappen voordat u Azure CLI kunt gebruiken om een Azure HPC-cache te maken of te wijzigen
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654453"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure CLI for Azure DNS HPC Cache instellen

Volg deze stappen om uw omgeving voor te bereiden voordat u Azure CLI gebruikt voor het maken of beheren van een HPC-cache van Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Voor de Azure HPC-cache is versie 2,7 of hoger van de Azure-CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="set-default-resource-group-optional"></a>Standaard resource groep instellen (optioneel)

Voor de meeste HPC-cache opdrachten moet u de resource groep van de cache door geven. U kunt de standaard resource groep instellen met behulp van [AZ configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Volgende stappen

Nadat u de Azure CLI-extensie hebt geïnstalleerd en u zich hebt aangemeld, kunt u Azure CLI gebruiken voor het maken en beheren van Azure HPC-cache systemen.

* [Een HPC-cache van Azure maken](hpc-cache-create.md)
* [Documentatie voor Azure CLI HPC-cache](/cli/azure/ext/hpc-cache/hpc-cache)
