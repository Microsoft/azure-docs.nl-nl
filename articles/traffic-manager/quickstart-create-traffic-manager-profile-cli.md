---
title: 'Quickstart: Een profiel maken voor de hoge beschikbaarheid van toepassingen - Azure CLI - Azure Traffic Manager'
description: In dit quickstartartikel wordt beschreven hoe u een Traffic Manager-profiel maakt met Azure CLI, voor het bouwen van een webtoepassing met hoge beschikbaarheid.
services: traffic-manager
author: duongau
mnager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8871392bca12078364c2be9b7104bf2a1dc20cb3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066569"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Quickstart: Een Traffic Manager-profiel maken voor webtoepassingen met hoge beschikbaarheid met behulp van Azure CLI

In deze quickstart wordt beschreven hoe u een Traffic Manager-profiel maakt die hoge beschikbaarheid van uw webtoepassing biedt.

In deze quickstart maakt u twee exemplaren van een webtoepassing. Ze worden elk in een andere Azure-regio uitgevoerd. U maakt een Traffic Manager-profiel op basis van [eindpuntprioriteit](traffic-manager-routing-methods.md#priority-traffic-routing-method). het profiel stuurt gebruikersverkeer door naar de primaire site waar de webtoepassing wordt uitgevoerd. Traffic Manager bewaakt de webtoepassing continu. Als de primaire site niet beschikbaar is, biedt Traffic Manager automatische failover voor de back-upsite.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep maken met [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak met behulp van [az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-create) een Traffic Manager-profiel waarmee gebruikersverkeer wordt doorgestuurd op basis van eindpuntprioriteit.

Vervang in het volgende voorbeeld **<profile_name>** met een unieke Traffic Manager-profielnaam.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Web-apps maken

Voor deze quickstart moeten twee exemplaren van een webtoepassing worden geïmplementeerd in twee verschillende Azure-regio's (*VS - oost* en *Europa - west*). Elk exemplaar dient als primair en failover-eindpunt voor Traffic Manager.

### <a name="create-web-app-service-plans"></a>Web-app-serviceplannen maken
Maak web-app-serviceplannen met [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) voor de twee exemplaren van de webtoepassing die u in twee verschillende Azure-regio's gaat implementeren.

Vervang in het volgende voorbeeld **<appspname_eastus>** en **<appspname_westeurope>** door een unieke App Service-plannaam

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Een web-app maken in het App Service-plan
Maak twee exemplaren van de webtoepassing met [az webapp create](/cli/azure/webapp#az-webapp-create) in de App Service-plannen in de Azure-regio's *US - oost* en *Europa - west*.

Vervang in het volgende voorbeeld **<app1name_eastus>** en **<app2name_westeurope> door een unieke app-naam en vervang** **<appspname_eastus>** en **<appspname_westeurope>** door de naam die is gebruikt om App Service-plannen in de vorige sectie te maken.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen
Voeg de twee web-apps als volgt toe aan het Traffic Manager-profiel als Traffic Manager-eindpunten met behulp van [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-create):

- Bepaal de web-app-id en voeg de web-app toe die zich in de Azure-regio *VS - oost* bevindt als primair eindpunt om alle gebruikersverkeer te routeren. 
- Bepaal de web-app-id en voeg de web-app toe die zich in de Azure-regio *Europa - west* bevindt als het failover-eindpunt. 

Als het primaire eindpunt niet beschikbaar is, wordt het verkeer automatisch naar het failover-eindpunt gerouteerd.

In het volgende voorbeeld vervangt u **<app1name_eastus>** en **<app2name_westeurope>** door de app-namen die u in de vorige sectie voor elke regio hebt gemaakt. Vervang vervolgens **<profile_name>** door de profielnaam die u in de vorige sectie hebt gebruikt. 

**Eindpunt US - oost**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Noteer de id die wordt weergegeven in de uitvoer en gebruik die in de volgende opdracht om het eindpunt toe te voegen:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Eindpunt Europa - west**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Noteer de id die wordt weergegeven in de uitvoer en gebruik die in de volgende opdracht om het eindpunt toe te voegen:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Uw Traffic Manager-profiel testen

In deze sectie controleert u de domeinnaam van uw Traffic Manager-profiel. Tevens configureert u het primaire eindpunt zodanig dat het niet beschikbaar is. Ten slotte kunt u zien dat de web-app nog steeds beschikbaar is. Dat komt omdat Traffic Manager het verkeer naar het failover-eindpunt stuurt.

In het volgende voorbeeld vervangt u **<app1name_eastus>** en **<app2name_westeurope>** door de app-namen die u in de vorige sectie voor elke regio hebt gemaakt. Vervang vervolgens **<profile_name>** door de profielnaam die u in de vorige sectie hebt gebruikt.

### <a name="determine-the-dns-name"></a>DNS-naam bepalen

Bepaal de DNS-naam van het Traffic Manager-profiel met behulp van [az network traffic-manager profile show](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Kopieer de waarde **RelativeDnsName**. De DNS-naam van uw Traffic Manager-profiel is *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien
1. Voer in een webbrowser de DNS-naam van het Traffic Manager-profiel (*http://<* relativednsname *>.trafficmanager.net*) in om de standaardwebsite van uw web-app te bekijken.

    > [!NOTE]
    > In dit quickstartscenario worden alle aanvragen gerouteerd naar het primaire eindpunt. Het is ingesteld op **Priority 1**.
2. Als u een Traffic Manager-failover in actie wilt bekijken, schakelt u de primaire site uit met behulp van [az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Kopieer de DNS-naam van het Traffic Manager-profiel (*http://<* relativednsname *>.trafficmanager.net*) om de website in een nieuwe webbrowsersessie te bekijken.
4. Controleer of de web-app nog beschikbaar is.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder met [az group delete](/cli/azure/group#az-group-delete) de resourcegroepen, de webtoepassingen en alle gerelateerde resources als u klaar bent.

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Traffic Manager-profiel gemaakt die een hoge beschikbaarheid van uw webtoepassing biedt. Voor meer informatie over het routeren van verkeer gaat u door naar de zelfstudies voor Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Traffic Manager-zelfstudies)