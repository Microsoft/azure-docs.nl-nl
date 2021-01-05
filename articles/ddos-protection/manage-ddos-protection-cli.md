---
title: Een Azure DDoS Protection-abonnement maken en configureren met behulp van Azure CLI
description: Meer informatie over het maken van een DDoS Protection-abonnement met behulp van Azure CLI
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 6c628d93c112a770c85a10d0eff958614a7cf4cb
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814156"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Snelstartgids: Azure DDoS Protection Standard maken en configureren met behulp van Azure CLI

Ga aan de slag met Azure DDoS Protection Standard met behulp van Azure CLI. 

In een DDoS-beschermings plan wordt een set virtuele netwerken gedefinieerd waarvoor DDoS-beschermings standaard is ingeschakeld, via abonnementen. U kunt één DDoS-beveiligings plan configureren voor uw organisatie en virtuele netwerken vanuit meerdere abonnementen koppelen aan hetzelfde abonnement. 

In deze Quick Start maakt u een DDoS-beschermings plan en koppelt u het aan een virtueel netwerk. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure CLI lokaal geïnstalleerd of Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze quickstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>Een DDoS Protection Plan maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. U kunt een bestaande resourcegroep gebruiken of een nieuwe maken.

Gebruik [AZ Group Create](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-create)om een resource groep te maken. In dit voor beeld noemen we de _MyResourceGroup_ van de resource groep en gebruiken we de locatie _VS-Oost_ :

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Maak nu een DDoS-beveiligings plan met de naam _MyDdosProtectionPlan_:

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>DDoS-beveiliging inschakelen voor een virtueel netwerk

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>DDoS-beveiliging inschakelen voor een nieuw virtueel netwerk

U kunt DDoS-beveiliging inschakelen bij het maken van een virtueel netwerk. In dit voor beeld noemen we onze virtuele netwerk _MyVnet_: 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
```

U kunt een virtueel netwerk niet verplaatsen naar een andere resource groep of een ander abonnement wanneer DDoS standaard is ingeschakeld voor het virtuele netwerk. Als u een virtueel netwerk wilt verplaatsen waarbij DDoS Standard is ingeschakeld, moet u eerst DDoS-standaard uitschakelen, het virtuele netwerk verplaatsen en vervolgens DDoS Standard inschakelen. Na de verplaatsing worden de automatisch afgestemde beleids drempels voor alle beveiligde open bare IP-adressen in het virtuele netwerk opnieuw ingesteld.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>DDoS-beveiliging inschakelen voor een bestaand virtueel netwerk

Wanneer u [een DDoS-beschermings plan maakt](#create-a-ddos-protection-plan), kunt u een of meer virtuele netwerken koppelen aan het plan. Als u meer dan één virtueel netwerk wilt toevoegen, kunt u gewoon de namen of Id's van een door spaties gescheiden lijst weer geven. In dit voor beeld voegen we _MyVnet_ toe:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnet MyVnet
```

U kunt ook DDoS-beveiliging inschakelen voor een bepaald virtueel netwerk:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection true
```

## <a name="validate-and-test"></a>Valideren en testen

Controleer eerst de details van uw DDoS-beveiligings plan:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Controleer of de opdracht de juiste gegevens van uw DDoS-beveiligings plan retourneert.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw resources voor de volgende zelf studie blijven gebruiken. Als u deze niet meer nodig hebt, verwijdert u de resource groep _MyResourceGroup_ . Wanneer u de resource groep verwijdert, verwijdert u ook het DDoS-beschermings plan en alle gerelateerde resources. 

Gebruik [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az_group_delete) om de resourcegroep te verwijderen:

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Een gegeven virtueel netwerk bijwerken om DDoS-beveiliging uit te scha kelen:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection false
```

Als u een DDoS-beschermings plan wilt verwijderen, moet u eerst alle virtuele netwerken loskoppelen. 

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studies voor meer informatie over het weer geven en configureren van telemetrie voor uw DDoS-beveiligings plan.

> [!div class="nextstepaction"]
> [DDoS-beschermingstelemetrie bekijken en configureren](telemetry.md)