---
title: Invoeg toepassing voor Application Gateway ingangs controller uitschakelen en opnieuw inschakelen voor Azure Kubernetes service-cluster
description: Dit artikel bevat informatie over het uitschakelen en opnieuw inschakelen van de AGIC-invoeg toepassing voor uw AKS-cluster
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807941"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>De AGIC-invoeg toepassing voor uw AKS-cluster uitschakelen en opnieuw inschakelen
Application Gateway ingangs controller (AGIC) die is geïmplementeerd als een AKS-invoeg toepassing, kunt u de invoeg toepassing inschakelen en uitschakelen met één regel in azure CLI. De levens cyclus van de Application Gateway verschilt wanneer u de AGIC-invoeg toepassing uitschakelt, afhankelijk van of de Application Gateway is gemaakt door de AGIC-invoeg toepassing, of dat deze los van de AGIC-invoeg toepassing is geïmplementeerd. U kunt dezelfde opdracht uitvoeren om de AGIC-invoeg toepassing opnieuw in te scha kelen als u deze ooit uitschakelt of als u de AGIC-invoeg toepassing wilt inschakelen met behulp van een bestaand AKS-cluster en Application Gateway.

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>De invoeg toepassing AGIC uitschakelen met de bijbehorende Application Gateway 
Als de AGIC-invoeg toepassing automatisch de Application Gateway voor u heeft geïmplementeerd toen u alles omhoog instelt, wordt de Application Gateway standaard door het uitschakelen van de AGIC-invoeg toepassing verwijderd op basis van een aantal criteria. Er zijn twee criteria die door de AGIC-invoeg toepassing worden gecontroleerd om te bepalen of de bijbehorende Application Gateway moeten worden verwijderd wanneer u deze uitschakelt:
- Is het Application Gateway dat de invoeg toepassing AGIC is gekoppeld aan de implementatie in de resource groep MC_ *-knoop punt? 
- Heeft de Application Gateway waaraan de AGIC-invoeg toepassing is gekoppeld, de tag "gemaakt door: ingangs-appgw"? De tag wordt door AGIC gebruikt om te bepalen of de Application Gateway door de invoeg toepassing is geïmplementeerd. 

Als aan beide criteria wordt voldaan, verwijdert de invoeg toepassing AGIC de Application Gateway die is gemaakt wanneer de invoeg toepassing wordt uitgeschakeld. het open bare IP-adres of het subnet waarin de Application Gateway is geïmplementeerd, wordt echter niet verwijderd. Als niet aan de eerste criteria wordt voldaan, maakt het niet uit of de Application Gateway de code ' gemaakt door: ingangs-appgw ' heeft. Als u de invoeg toepassing uitschakelt, wordt de Application Gateway niet verwijderd. Op dezelfde manier geldt dat als niet aan de tweede criteria wordt voldaan, dat wil zeggen de Application Gateway dat label niet wordt verwijderd. vervolgens wordt de Application Gateway in de resource groep MC_ * knoop punt niet door de invoeg toepassing uitgeschakeld. 

> [!TIP] 
> Als u niet wilt dat de Application Gateway worden verwijderd wanneer u de invoeg toepassing uitschakelt, maar voldoet aan beide criteria, verwijdert u de tag ' gemaakt door: ingress-appgw ' om te voor komen dat de invoeg toepassing de Application Gateway verwijdert. 

Als u de AGIC-invoeg toepassing wilt uitschakelen, voert u de volgende opdracht uit: 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>AGIC-invoeg toepassing inschakelen op bestaande Application Gateway en AKS-cluster
Als u de AGIC-invoeg toepassing ooit hebt uitgeschakeld en de invoeg toepassing opnieuw moet inschakelen, of als u de invoeg toepassing wilt inschakelen met behulp van een bestaand Application Gateway-en AKS-cluster, voert u de volgende opdracht uit:

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over het inschakelen van de invoeg toepassing voor AGIC met behulp van een bestaand Application Gateway-en AKS-cluster de [implementatie van AGIC-invoeg toepassing Brownfield](tutorial-ingress-controller-add-on-existing.md).