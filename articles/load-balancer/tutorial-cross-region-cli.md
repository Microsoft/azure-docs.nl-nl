---
title: 'Zelfstudie: Een regio-overschrijdende load balancer azure CLI'
titleSuffix: Azure Load Balancer
description: Ga aan de slag met deze zelfstudie om een regio-overschrijdende Azure Load Balancer azure CLI te gebruiken.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: ca4134ff25dc9915f256b5a7bdd9404021b60a8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791909"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Zelfstudie: Een regio-overschrijdende Azure Load Balancer azure CLI

Een load balancer voor meerdere regio's zorgt ervoor dat een service wereldwijd beschikbaar is in meerdere Azure-regio's. Als de ene regio uitvalt, wordt het verkeer doorgestuurd naar de dichtstbijzijnde regionale load balancer.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een load balancer voor meerdere regio's maken.
> * Maak een load balancer-regel.
> * Een back-end-pool met twee regionale load balancers maken.
> * De load balancer testen.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.
- Twee Azure Load Balancers met een **standaard**-SKU met back-end-pools die in twee verschillende Azure-regio's zijn geïmplementeerd.
    - Zie [Quickstart:](quickstart-load-balancer-standard-public-cli.md)Een openbare load balancer maken om de VM's te laden met behulp van Azure CLI voor meer informatie over het maken van een regionale standaard load balancer en virtuele machines voor back-endpools.
        - De naam van de load balancers en virtuele machines in elke regio met **een -R1** en **-R2.** 
- Azure CLI lokaal of lokaal Azure Cloud Shell.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze quickstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI

Meld u aan bij Azure CLI:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Een load balancer voor meerdere regio's maken

In deze sectie maakt u een regio-overschrijdende load balancer, openbaar IP-adres en taakverdelingsregel.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create):

* Met **de naam myResourceGroupLB-CR.**
* Op de **locatie westus.**

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een regio-overschrijdende load balancer [met az network cross-region-lb create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create):

* Met de **naam myLoadBalancer-CR**.
* Een front-endpool met **de naam myFrontEnd-CR.**
* Een back-endpool met de **naam myBackEndPool-CR.**

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Load balancer-regel maken

Met een load balancer-regel wordt het volgende gedefinieerd:

* De front-end-IP-configuratie voor het binnenkomende verkeer.
* De back-end-IP-adresgroep voor het ontvangen van verkeer.
* De vereiste bron- en doelpoort. 

Maak een load balancer met [az network cross-region-lb rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* Met **de naam myHTTPRule-CR**
* Luistert op **poort 80** in de front-endpool **myFrontEnd-CR.**
* Het verzenden van netwerkverkeer met load balanced naar de back-endadresgroep **myBackEndPool-CR** met **behulp van poort 80.** 
* Protocol: **TCP**.

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Back-endpool maken

In deze sectie voegt u twee regionale standaard load balancers toe aan de back-end-pool van de load balancer voor meerdere regio's.

> [!IMPORTANT]
> Als u deze stappen wilt uitvoeren, moet u ervoor zorgen dat er twee regionale load balancers met back-end-pools zijn geïmplementeerd in uw abonnement.  Zie voor meer informatie **[Quickstart: Create a public load balancer to load balance VMs using Azure CLI (Snelstart:](quickstart-load-balancer-standard-public-cli.md)** een openbare load balancer om de VM's te laden met behulp van Azure CLI).

### <a name="add-the-regional-frontends-to-load-balancer"></a>Voeg de regionale front-load balancer

In deze sectie gaat u de resource-ID's van twee regionale load balancers-front-enden in variabelen plaatsen.  Vervolgens gebruikt u de variabelen om de front-ends toe te voegen aan de back-endadresgroep van de regio-overschrijdende load balancer.

Haal de resource-ID's [op met az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

Gebruik [az network cross-region-lb address-pool address add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) om de front-ends toe te voegen die u in variabelen hebt geplaatst in de back-endpool van de regio-overschrijdende load balancer:

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>Load balancer testen

In deze sectie gaat u de load balancer voor meerdere regio's testen. U maakt verbinding met het openbare IP-adres in een webbrowser.  U stopt de virtuele machines in een van de back-end-pools van de regionale load balancer en bekijkt de failover.

1. Als u het openbare IP-adres van de load balancer, gebruikt [u az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

3. Stop de virtuele machines in de back-end-pool van een van de regionale load balancers.

4. Vernieuw de webbrowser en bekijk de failover van de verbinding naar de andere regionale load balancer.

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [az group delete](/cli/azure/group#az_group_delete) om de resourcegroep, de load balancer en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

* Een load balancer voor meerdere regio's gemaakt.
* Een load balancer-regel toegevoegd.
* Regionale standaard load balancers toegevoegd aan de back-end-pool van de load balancer voor meerdere regio's.
* De load balancer getest.

Ga naar het volgende artikel om te lezen hoe u dit doet...
> [!div class="nextstepaction"]
> [Taken over VM's in meerdere beschikbaarheidszones verdelen](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
