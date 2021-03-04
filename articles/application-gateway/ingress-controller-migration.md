---
title: Migreren van Azure-toepassing gateway ingangs controller helm naar AGIC-invoeg toepassing
description: In dit artikel vindt u instructies voor het migreren van AGIC die zijn geïmplementeerd via helm naar AGIC geïmplementeerd als een AKS-invoeg toepassing
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050831"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>Migreren van AGIC helm naar AGIC-invoeg toepassing 

Als u AGIC al hebt geïmplementeerd via helm, maar u wilt migreren naar AGIC geïmplementeerd als een AKS-invoeg toepassing, kunt u aan de hand van de volgende stappen het migratie proces door lopen. 

## <a name="prerequisites"></a>Vereisten 
Voordat u het migratie proces start, zijn er enkele dingen die u kunt controleren. 
  - Gebruikt u functies van AGIC helm die [momenteel niet worden ondersteund met AGIC-invoeg toepassing](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)?
  - Gebruikt u meer dan één AGIC helm-implementatie per AKS-cluster? 
  - Gebruikt u meerdere AGIC helm-implementaties om een Application Gateway te richten? 

Als u Ja hebt gereageerd op een van de bovenstaande vragen, wordt uw gebruiks aanvraag nog niet door AGIC-invoeg toepassing ondersteund. u kunt het beste ook AGIC helm blijven gebruiken. Als dat niet het geval is, gaat u verder met het migratie proces hieronder tijdens kantoor uren. 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>Zoek de Application Gateway Resource-ID op die AGIC helm momenteel is gericht 
Ga naar de Application Gateway waarop uw AGIC helm-implementatie is gericht. Kopieer de resource-ID van die Application Gateway en sla deze op. U hebt de resource-ID nodig in een latere stap. U kunt de resource-ID vinden in de portal, op het tabblad Eigenschappen van uw Application Gateway of via Azure CLI. In het volgende voor beeld wordt de Application Gateway Resource-ID opgeslagen in *appgwId* voor een gateway met de naam *myApplicationGateway* in de resource groep *myResourceGroup*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>AGIC helm uit uw AKS-cluster verwijderen
Verwijder uw AGIC helm-implementatie via Azure CLI vanuit uw cluster. U moet eerst de AGIC helm-implementatie verwijderen voordat u de invoeg toepassing AGIC AKS kunt inschakelen. Houd er rekening mee dat alle wijzigingen die zich voordoen in uw AKS-cluster tussen het moment van het verwijderen van uw AGIC helm-implementatie en de tijd dat u de invoeg toepassing AGIC inschakelt, niet worden weer gegeven op uw Application Gateway en dat het migratie proces daarom buiten kantoor uren moet worden uitgevoerd om de impact te minimaliseren. Application Gateway blijft de laatste configuratie door AGIC toegepast, dus de bestaande routerings regels worden niet beïnvloed. 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>AGIC-invoeg toepassing inschakelen met behulp van uw bestaande Application Gateway 
U kunt nu de invoeg toepassing AGIC in uw AKS-cluster inschakelen om uw bestaande Application Gateway te richten via Azure CLI of portal. Voer de volgende Azure CLI-opdracht uit om de AGIC-invoeg toepassing in uw AKS-cluster in te scha kelen. In het voor beeld wordt de invoeg toepassing ingeschakeld in een cluster met de naam *myCluster*, in een resource groep met de naam *myResourceGroup*, met behulp van de Application Gateway resource-id *appgwId* eerder in de vorige stap hebt opgeslagen. 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

U kunt ook naar uw AKS-cluster in de portal gaan met behulp van deze [koppeling](https://portal.azure.com/?feature.aksagic=true) en de AGIC-invoeg toepassing inschakelen op het tabblad netwerk van uw cluster. Selecteer uw bestaande Application Gateway in het vervolg keuzemenu wanneer u kiest welke Application Gateway de invoeg toepassing moet worden gericht. 

![Controller portal voor Application Gateway ingang](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>Volgende stappen
- [**Problemen met de Application Gateway-controller oplossen**](ingress-controller-troubleshoot.md): probleemoplossings gids voor AGIC 
- [**Application Gateway ingangs controller aantekeningen**](ingress-controller-annotations.md): lijst met annotaties op AGIC 