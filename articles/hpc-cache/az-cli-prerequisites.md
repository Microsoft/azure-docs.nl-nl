---
title: Azure CLI-vereisten voor Azure HPC Cache
description: Installatiestappen voordat u Azure CLI kunt gebruiken voor het maken of wijzigen van een Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 0b8e1158bc60c4cceea508db988000fe952a90a4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864285"
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
* [Documentatie voor Azure CLI hpc-cache](/cli/azure/hpc-cache)
