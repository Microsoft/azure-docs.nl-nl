---
title: Veelvoorkomende implementatiefouten oplossen
titleSuffix: Azure Load Balancer
description: Hierin wordt beschreven hoe u veelvoorkomende fouten oplost bij het implementeren van Azure load balancers
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: ae3899a61e942695fed1e3da5fc543b298a42e24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94695551"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Veelvoorkomende fouten bij Azure-implementatie met Azure Load Balancer oplossen

In dit artikel worden enkele veelvoorkomende Azure Load Balancer implementatie fouten beschreven en vindt u informatie over het oplossen van de fouten. Als u op zoek bent naar informatie over een fout code en deze informatie niet in dit artikel wordt verstrekt, laat het ons dan weten. Onder aan deze pagina kunt u feedback geven. De feedback wordt bijgehouden met GitHub-problemen.

## <a name="error-codes"></a>Foutcodes

| Foutcode | Details en risico beperking |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| Zowel de open bare IP-SKU als de Load Balancer SKU moeten overeenkomen. Zorg ervoor dat Azure Load Balancer en open bare IP Sku's overeenkomen. De standaard-SKU wordt aanbevolen voor productie werkbelastingen. Meer informatie over de [verschillen in sku's](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | Virtuele-machine schaal sets zijn standaard ingesteld op Basic load balancers als SKU niet is opgegeven of zonder standaard open bare Ip's wordt geïmplementeerd. Implementeer de schaalset voor virtuele machines opnieuw met standaard open bare IP-adressen voor de afzonderlijke instanties om ervoor te zorgen Standard Load Balancer is geselecteerd of Selecteer eenvoudigweg een standaard LB bij het implementeren van de schaalset voor virtuele machines vanuit de Azure Portal. |
|MaxAvailabilitySetsInLoadBalancerReached | De back-end-groep van een Load Balancer kan Maxi maal 150 beschikbaarheids sets bevatten. Als er geen beschikbaarheids sets expliciet zijn gedefinieerd voor uw virtuele machines in de back-endadresgroep, wordt elke virtuele machine in een eigen Beschikbaarheidsset gezet. Implementatie van 150 zelfstandige Vm's zou er dus toe ophouden dat er 150-beschikbaarheids sets zouden zijn, waardoor de limiet wordt bereikt. U kunt een beschikbaarheidsset implementeren en er extra Vm's aan toevoegen als tijdelijke oplossing. |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | Voor basis-SKU load balancer moeten de netwerk interface en de load balancer dezelfde beschikbaarheidsset hebben. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| U kunt niet meer dan één regel voor een gegeven load balancer type (intern, openbaar) met dezelfde back-end-poort en een protocol waarnaar wordt verwezen door dezelfde virtuele-machine schaalset. Werk uw regel bij om deze dubbele regel te maken. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| U kunt niet meer dan één regel voor een gegeven load balancer type (intern, openbaar) met dezelfde back-end-poort en een protocol waarnaar wordt verwezen door dezelfde virtuele-machine schaalset. Werk de regel parameters bij om het maken van deze dubbele regel te wijzigen. |
|AnotherInternalLoadBalancerExists| U kunt slechts één Load Balancer van het type interne verwijzen naar dezelfde set Vm's/netwerk interfaces in de back-end van de Load Balancer. Werk uw implementatie bij om ervoor te zorgen dat u slechts één Load Balancer van hetzelfde type maakt. |
|CannotUseInactiveHealthProbe| U kunt geen sonde hebben die niet wordt gebruikt door een regel die is geconfigureerd voor de status van virtuele-machine schaal sets. Zorg ervoor dat de test die is ingesteld, actief wordt gebruikt. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| U kunt niet meerdere load balancers van hetzelfde type (intern, openbaar) hebben. U kunt Maxi maal één intern en één open bare Load Balancer hebben. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Basis Load Balancer wordt niet ondersteund voor groepen virtuele-machine schaal sets of meerdere-beschikbaarheids zones voor de virtuele machine in een andere locatie. Gebruik in plaats daarvan Standard Load Balancer. |
|MarketplacePurchaseEligibilityFailed | Schakel over naar het juiste beheerders account om aankopen in te scha kelen omdat het abonnement een EA-abonnement is. Lees [hier](../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase) meer informatie. |
|ResourceDeploymentFailure| Als uw load balancer de status Mislukt heeft, voert u de volgende stappen om dit te verhelpen:<ol><li>Ga naar https://resources.azure.com en meld u aan met uw Azure Portal referenties.</li><li>Selecteer **Read/Write** (Lezen/schrijven).</li><li>Vouw aan de linkerkant **Subscriptions** (Abonnementen) uit en vouw vervolgens het abonnement uit met de load balancer die u wilt bijwerken.</li><li>Vouw **ResourceGroups** uit en vouw vervolgens de resourcegroep uit met de load balancer die u wilt bijwerken.</li><li>Selecteer **micro soft. Network**  >  **LoadBalancers** en selecteer vervolgens het Load Balancer dat u wilt bijwerken, **LoadBalancer_1**.</li><li>Selecteer op de pagina weer geven voor **LoadBalancer_1** de optie   >  **bewerkings bewerking** ophalen.</li><li>De waarde van **ProvisioningState** bijwerken van **mislukt** naar **geslaagd**.</li><li>Selecteer **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Volgende stappen

* Bekijk de Azure Load Balancer [SKU-vergelijkings tabel](./skus.md)
* Meer informatie over [Azure Load Balancer limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)