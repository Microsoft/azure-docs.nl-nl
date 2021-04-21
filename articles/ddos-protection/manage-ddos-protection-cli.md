---
title: Een abonnement voor een Azure DDoS Protection maken en configureren met behulp van Azure CLI
description: Meer informatie over het maken van een DDoS Protection-abonnement met behulp van Azure CLI
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 8a8da50dc703d59dc16b5cb6253d39aeb33fd76d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777631"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Quickstart: Een Standard-Azure DDoS Protection maken en configureren met behulp van Azure CLI

Aan de slag met Azure DDoS Protection Standard met behulp van Azure CLI. 

Een DDoS-beveiligingsplan definieert een set virtuele netwerken met DDoS-beveiligingsstandaard ingeschakeld voor alle abonnementen. U kunt voor uw organisatie één DDoS-beschermingsplan configureren en virtuele netwerken vanuit meerdere abonnementen aan hetzelfde plan koppelen. 

In deze quickstart maakt u een DDoS-beveiligingsplan en koppelt u dit aan een virtueel netwerk. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure CLI lokaal geïnstalleerd of Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze quickstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>Een DDoS Protection maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. U kunt een bestaande resourcegroep gebruiken of een nieuwe maken.

Gebruik az group create om een [resourcegroep te maken.](/cli/azure/group#az_group_create) In dit voorbeeld geven we de resourcegroep de _naam MyResourceGroup_ en gebruiken we de locatie _VS -_ oost:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Maak nu een DDoS-beveiligingsplan met de _naam MyDdosProtectionPlan:_

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>DDoS-beveiliging inschakelen voor een virtueel netwerk

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>DDoS-beveiliging inschakelen voor een nieuw virtueel netwerk

U kunt DDoS-beveiliging inschakelen bij het maken van een virtueel netwerk. In dit voorbeeld geven we ons virtuele netwerk de _naam MyVnet:_ 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

U kunt een virtueel netwerk niet verplaatsen naar een andere resourcegroep of een ander abonnement wanneer DDoS Standard is ingeschakeld voor het virtuele netwerk. Als u een virtueel netwerk wilt verplaatsen met DDoS Standard ingeschakeld, schakelt u eerst DDoS Standard uit, verplaatst u het virtuele netwerk en schakelt u vervolgens DDoS Standard in. Na de overstap worden de drempelwaarden voor automatisch afgestemde beleid voor alle beveiligde openbare IP-adressen in het virtuele netwerk opnieuw ingesteld.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>DDoS-beveiliging inschakelen voor een bestaand virtueel netwerk

Wanneer [u een DDoS-beveiligingsplan maakt,](#create-a-ddos-protection-plan)kunt u een of meer virtuele netwerken aan het plan koppelen. Als u meer dan één virtueel netwerk wilt toevoegen, vermeldt u de namen of de ID's, gescheiden door spaties. In dit voorbeeld voegen we _MyVnet toe:_

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

U kunt ook DDoS-beveiliging inschakelen voor een bepaald virtueel netwerk:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>Valideren en testen

Controleer eerst de details van uw DDoS-beveiligingsplan:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Controleer of de opdracht de juiste details van uw DDoS-beveiligingsplan retourneert.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw resources bewaren voor de volgende zelfstudie. Verwijder de resourcegroep _MyResourceGroup_ als u deze niet meer nodig hebt. Wanneer u de resourcegroep verwijdert, verwijdert u ook het DDoS-beveiligingsplan en alle bijbehorende resources. 

Gebruik [az group delete](/cli/azure/group#az_group_delete) om de resourcegroep te verwijderen:

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Werk een bepaald virtueel netwerk bij om DDoS-beveiliging uit te schakelen:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

Als u een DDoS-beveiligingsplan wilt verwijderen, moet u er eerst alle virtuele netwerken van loskoppelen. 

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelfstudies voor meer informatie over het weergeven en configureren van telemetrie voor uw DDoS-beschermingsplan.

> [!div class="nextstepaction"]
> [DDoS-beschermingstelemetrie bekijken en configureren](telemetry.md)
