---
title: Een VNet-Tik maken, wijzigen of verwijderen-Azure CLI
description: Meer informatie over het maken, wijzigen of verwijderen van een virtueel netwerk Tik met behulp van de Azure CLI.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e3a56e4a6eb1fb6eb633021178ef78f8ac7287d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014785"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Een virtueel netwerk gebruiken Tik op de Azure CLI

Met het virtuele netwerk van Azure (Terminal Access Point) kunt u het netwerk verkeer van de virtuele machine continu streamen naar een netwerk pakket verzamelaar of een analyse programma. Het hulp programma Collector of Analytics wordt verschaft door een [virtuele netwerk apparaat](https://azure.microsoft.com/solutions/network-appliances/) -partner. Zie [partner oplossingen](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions)voor een lijst met partner oplossingen die zijn gevalideerd om te werken met Virtual Network tikken. 

## <a name="create-a-virtual-network-tap-resource"></a>Een virtueel netwerk maken tik op resource

Lees de [vereisten](virtual-network-tap-overview.md#prerequisites) voordat u een virtueel netwerk tikt op resource. U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud shell](https://shell.azure.com/bash), of door de Azure-opdracht regel interface (CLI) uit te voeren vanaf uw computer. De Azure Cloud Shell is een gratis interactieve shell, waarvoor geen Azure CLI op uw computer hoeft te worden geïnstalleerd. U moet zich aanmelden bij Azure met een account met de juiste [machtigingen](virtual-network-tap-overview.md#permissions). Voor dit artikel is de Azure CLI-versie 2.0.46 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Virtual Network TIKT is momenteel beschikbaar als een uitbrei ding. Als u de extensie wilt installeren, moet u deze uitvoeren `az extension add -n virtual-network-tap` . Als u de Azure CLI lokaal uitvoert, moet u ook uitvoeren `az login` om een verbinding te maken met Azure.

1. Haal de ID van uw abonnement op in een variabele die in een latere stap wordt gebruikt:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Stel de abonnements-id in die u gaat gebruiken voor het maken van een virtueel netwerk Tik op resource.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Registreer de abonnements-ID die u gebruikt voor het maken van een virtueel netwerk Tik op resource opnieuw. Als er een registratie fout optreedt wanneer u een TAP-resource maakt, voert u de volgende opdracht uit:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Als de bestemming voor het virtuele netwerk is, wordt de netwerk interface op het virtuele netwerk apparaat voor de collector of het analyse hulpprogramma

   - Haal de IP-configuratie van de netwerk interface van het virtuele netwerk apparaat op in een variabele die in een latere stap wordt gebruikt. De ID is het eind punt waarmee het Tik-verkeer wordt geaggregeerd. In het volgende voor beeld wordt de ID van de *ipconfig1* IP-configuratie opgehaald voor een netwerk interface met de naam *myNetworkInterface*, in een resource groep met de naam *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Maak het virtuele netwerk Tik in de Azure-regio westcentralus met de ID van de IP-configuratie als doel en een optionele poort eigenschap. De poort specificeert de doel poort op de IP-configuratie van de netwerk interface, waarbij het tikken verkeer wordt ontvangen:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Als het doel voor de virtuele netwerk kraan een interne load balancer van Azure is:
  
   - Haal de front-end-IP-configuratie van de interne Azure-load balancer op in een variabele die in een latere stap wordt gebruikt. De ID is het eind punt waarmee het Tik-verkeer wordt geaggregeerd. In het volgende voor beeld wordt de ID van de *frontendipconfig1* front-end-IP-configuratie opgehaald voor een Load Balancer met de naam *myInternalLoadBalancer*, in een resource groep met de naam *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Het virtuele netwerk maken tik met de ID van de front-end-IP-configuratie als doel en een optionele poort eigenschap. De poort specificeert de doel poort op de front-end-IP-configuratie waar het tikken verkeer wordt ontvangen:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Het maken van het virtuele netwerk bevestigen:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Een tik-configuratie toevoegen aan een netwerk interface

1. Haal de ID op van een bestaand virtueel netwerk Tik op resource. In het volgende voor beeld wordt een virtueel netwerk met de naam *myTap* in een resource groep met de naam *myResourceGroup* opgehaald:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Maak een tik-configuratie op de netwerk interface van de bewaakte virtuele machine. In het volgende voor beeld wordt een tik-configuratie gemaakt voor een netwerk interface met de naam *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Het maken van de TAP-configuratie bevestigen:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>De TAP-configuratie op een netwerk interface verwijderen

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Een lijst met virtuele netwerk kranen in een abonnement

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Een virtueel netwerk kraan in een resource groep verwijderen

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
