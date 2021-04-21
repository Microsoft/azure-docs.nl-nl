---
title: 'Quickstart: Hoge beschikbaarheid instellen met Azure Front Door Service - Azure CLI'
description: In deze quickstart ziet u hoe u met behulp van Azure Front Door een hoge beschikbaarheid en een geavanceerde wereldwijde webtoepassing maakt met Azure CLI.
services: front-door
author: duongau
manager: KumudD
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/19/2021
ms.author: duau
ms.openlocfilehash: 99204a2d4c3a2455f0916878fb09a348dc79ac7a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778751"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Quickstart: Een Front Door maken voor een wereldwijde webtoepassing met hoge beschikbaarheid met Azure CLI

Ga aan de slag met Azure Front Door met behulp van Azure CLI om een maximaal beschikbare, wereldwijde webtoepassing met hoge prestaties te maken.

De Front Door stuurt webverkeer door naar specifieke bronnen in een back-end-pool. U hebt het front-end-domein gedefinieerd, resources toegevoegd aan een back-end-pool en een regel voor doorsturen gemaakt. In dit artikel wordt gebruikgemaakt van een eenvoudige configuratie van één back-end-pool met twee web-app-resources en één regel voor doorsturen met behulp van de standaardpadafstemming "/*".

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Diagram van Front Door implementatieomgeving met behulp van de Azure CLI." border="false":::

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure CLI lokaal geïnstalleerd of Azure Cloud Shell
- Zorg ervoor dat de Front Door-extensie is toegevoegd aan uw Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze quickstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. U kunt een bestaande resourcegroep gebruiken of een nieuwe maken.

Voor deze quickstart hebt u twee resourcegroepen nodig. Een in *VS - centraal* en de tweede in *VS - zuid-centraal*.

Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDEast \
    --location eastus
```

## <a name="create-two-instances-of-a-web-app"></a>Twee instanties van een web-app maken

Voor deze quickstart hebt u twee instanties nodig van een webtoepassing die worden uitgevoerd in verschillende Azure-regio's. Beide webtoepassingsinstanties worden in de modus Actief/actief uitgevoerd, dus verkeer kan in beide instanties worden uitgevoerd.

Als u nog geen web-app hebt, voert u het volgende script uit om twee voorbeelden van web-apps in te stellen.

### <a name="create-app-service-plans"></a>App Service-abonnementen maken

Voordat u de web-apps kunt maken, hebt u twee App Service-plannen nodig, één in VS *-* centraal en de tweede in *VS - oost.*

Maak App Service-abonnementen met de opdracht [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true):

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanEastUS \
--resource-group myRGFDEast
```

### <a name="create-web-apps"></a>Web-apps maken

Als u de volgende opdrachten uitvoert, wordt in de vorige stap een web-app gemaakt in elk van de App Service-abonnementen. Web-app-namen moeten wereldwijd uniek zijn.

Een web-app maken met [az webapp create](/cli/azure/webapp#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso-1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso-2 \
--resource-group myRGFDEast \
--plan myAppServicePlanEastUS
```

Noteer de standaardhostnaam van elke web-app, zodat u de backend-adressen kunt definiëren wanneer u de Front Door in de volgende stap implementeert.

## <a name="create-the-front-door"></a>De Front Door maken

Maak een eenvoudige Front Door met de standaardinstellingen voor taakverdeling, statustest en routeringsregels door de volgende stappen uit te voeren:

Front Door maken met [az network front-door create](/cli/azure/ext/front-door/network/front-door#ext_front_door_az_network_front_door_create&preserve-view=true):

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso-1.azurewebsites.net webappcontoso-2.azurewebsites.net 
```

**--resource-group:** Geef een resourcegroep op waarin u de Front Door wilt implementeren.

**--name:** Geef een wereldwijd unieke naam op voor de Azure Front Door. 

**--accepted-protocols:** Geaccepteerde waarden zijn **http** en **https**. Als u beide wilt gebruiken, moet u deze scheiden door een spatie.

**--backend-address:** Geef de hostnaam van beide web-apps op, gescheiden door een spatie.

Nadat de implementatie is voltooid, noteert u de hostnaam in het gedeelte *frontEndpoints*.

## <a name="test-the-front-door"></a>De Front Door testen

Open een webbrowser en voer de hostnaam in via de opdrachten. De Front Door stuurt uw aanvraag door naar een van de back-endservers.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Front Door-testpagina":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met de Front Door hebt gemaakt niet meer nodig hebt, verwijdert u beide resourcegroepen. Wanneer u de resourcegroep verwijdert, verwijdert u ook de Front Door en alle resources die hieraan zijn gerelateerd. 

Gebruik [az group delete](/cli/azure/group#az_group_delete&preserve-view=true) om de resourcegroep te verwijderen:

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDEast
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:
* Front Door
* Twee web-apps

Ga verder naar de zelfstudies over Front Door voor informatie over het toevoegen van een aangepast domein aan uw Front Door.

> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen](front-door-custom-domain.md)
