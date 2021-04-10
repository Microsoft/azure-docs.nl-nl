---
title: "Zelf studie: een load balancer voor meerdere regio's maken met behulp van Azure CLI"
titleSuffix: Azure Load Balancer
description: Aan de slag met deze zelf studie een Azure Load Balancer voor meerdere regio's implementeren met behulp van Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: 83efb428a94d49b77ecd923d4868afe034374b5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225180"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Zelf studie: een Azure Load Balancer voor meerdere regio's maken met behulp van Azure CLI

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
    - Voor informatie over het maken van een regionale standaard load balancer en virtuele machines voor back-endservers, Zie [Quick Start: een open bare Load Balancer maken om taken te verdelen over vm's met behulp van Azure cli](quickstart-load-balancer-standard-public-cli.md).
        - Voeg de naam van de load balancers en virtuele machines in elke regio toe met een **-R1** en **-R2**. 
- Azure CLI is lokaal of Azure Cloud Shell geïnstalleerd.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze quickstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI

Meld u aan bij Azure CLI:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Een load balancer voor meerdere regio's maken

In deze sectie maakt u een kruis regio load balancer, een openbaar IP-adres en een regel voor taak verdeling.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep maken met [az group create](/cli/azure/group#az-group-create):

* Met de naam **myResourceGroupLB-CR**.
* In de locatie **westus** .

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een load balancer voor meerdere regio's met [AZ Network cross-Region-lb Create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create):

* Met de naam **myLoadBalancer-CR**.
* Een front-end **-groep met de naam myFrontEnd-CR**.
* Een back-end **-groep met de naam myBackEndPool-CR**.

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

Maak een load balancer regel met [AZ Network cross-Region-lb regel Create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* Met de naam **myhttprule als-CR**
* Luis teren op **poort 80** in de front **-myFrontEnd-CR-** groep.
* Netwerk verkeer met taak verdeling naar de back-endadresgroep **myBackEndPool-CR** verzenden met behulp van **poort 80**. 
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
> Als u deze stappen wilt uitvoeren, moet u ervoor zorgen dat er twee regionale load balancers met back-end-pools zijn geïmplementeerd in uw abonnement.  Zie voor meer informatie Quick Start **[: een open bare Load Balancer maken om taken te verdelen over vm's met behulp van Azure cli](quickstart-load-balancer-standard-public-cli.md)**.

### <a name="add-the-regional-frontends-to-load-balancer"></a>De regionale front-ends toevoegen aan load balancer

In deze sectie plaatst u de resource-Id's van twee regionale load balancers-front-ends in variabelen.  Vervolgens gebruikt u de variabelen om de frontends toe te voegen aan de back-end-adres groep van de load balancer van de Kruis regio.

Haal de bron-Id's op met [AZ Network lb frontend-IP show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

Gebruik [AZ Network Kruis-Region-lb adres-pool adres Voeg](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) toe om de front-ends toe te voegen die u in variabelen in de back-endadresgroep van de kruis regio Load Balancer hebt geplaatst:

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

1. Als u het open bare IP-adres van de load balancer wilt ophalen, gebruikt u [AZ Network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show):

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

Gebruik de opdracht [az group delete](/cli/azure/group#az-group-delete) om de resourcegroep, de load balancer en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

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
