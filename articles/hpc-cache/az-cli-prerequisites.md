---
title: Azure CLI-vereisten voor Azure HPC Cache
description: Installatiestappen voordat u Azure CLI kunt gebruiken voor het maken of wijzigen van een Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 30621eceefd69cd3e08de137bb34f1079a17a406
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780475"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure CLI for Azure DNS HPC Cache instellen

Volg deze stappen om uw omgeving voor te bereiden voordat u Azure CLI gebruikt om een Azure HPC Cache.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache versie 2.7 of hoger van de Azure CLI is vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="set-default-resource-group-optional"></a>Standaardresourcegroep instellen (optioneel)

Voor de meeste hpc-cache-opdrachten moet u de resourcegroep van de cache doorgeven. U kunt de standaardresourcegroep instellen met az [configure](/cli/azure/reference-index#az_configure).

## <a name="next-steps"></a>Volgende stappen

Nadat u de Azure CLI-extensie hebt geïnstalleerd en u zich hebt aanmelden, kunt u Azure CLI gebruiken voor het maken en beheren Azure HPC Cache systemen.

* [Een Azure HPC Cache](hpc-cache-create.md)
* [Documentatie voor Azure CLI hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache)
