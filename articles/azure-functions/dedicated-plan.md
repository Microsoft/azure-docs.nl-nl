---
title: Toegewezen hosting Azure Functions
description: Meer informatie over de voor delen van het uitvoeren van Azure Functions op een speciaal App Service hosting abonnement.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 0ebf83aa919d91f161b247539ae20873242a8ed8
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937621"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Toegewezen hosting plannen voor Azure Functions

In dit artikel vindt u informatie over het hosten van uw functie-app in een App Service plan, met inbegrip van een App Service Environment (ASE). Zie het [artikel hosting plan](functions-scale.md)voor andere hosting opties.

Een App Service plan definieert een set reken resources voor het uitvoeren van een app. Deze reken bronnen zijn vergelijkbaar met de [_server farm_](https://wikipedia.org/wiki/Server_farm) in conventionele hosting. Een of meer functie-apps kunnen worden geconfigureerd om te worden uitgevoerd op dezelfde computer bronnen (App Service-abonnement) als andere App Service-apps, zoals web apps. Deze abonnementen omvatten Basic-, Standard-, Premium-en geïsoleerde Sku's. Zie het [overzicht van Azure app service plannen](../app-service/overview-hosting-plans.md)voor meer informatie over de werking van het app service plan.

Houd rekening met een App Service-abonnement in de volgende situaties:

* U hebt al bestaande, te weinig gebruikte virtuele machines waarop andere exemplaren van App Service worden uitgevoerd.
* U wilt een aangepaste installatie kopie opgeven waarop uw functies moeten worden uitgevoerd.

## <a name="billing"></a>Billing

U betaalt voor functie-apps in een App Service plan zoals u zou doen voor andere App Service resources. Dit wijkt af van Azure Functions [verbruiks plan](consumption-plan.md) of het [abonnement op Premium-abonnementen](functions-premium-plan.md) , waarvoor kosten onderdelen op basis van verbruik zijn. U wordt alleen gefactureerd voor het abonnement, ongeacht het aantal functie-apps of web-apps dat in het abonnement wordt uitgevoerd. Zie de pagina met prijzen voor [app service](https://azure.microsoft.com/pricing/details/app-service/windows/)voor meer informatie. 

## <a name="always-on"></a><a name="always-on"></a> Altijd aan

Als u uitvoert met een App Service-abonnement, moet u de instelling **altijd aan** inschakelen, zodat de functie-app correct wordt uitgevoerd. Bij een App Service-abonnement wordt de runtime van functions na een paar minuten inactiviteit niet-actief, zodat alleen HTTP-triggers uw functies kunnen activeren. De optie **altijd aan** is alleen beschikbaar voor een app service plan. In een verbruiks abonnement activeert het platform automatisch functie-apps.

Zelfs met Always ingeschakeld, wordt de time-out voor de uitvoering van afzonderlijke functies bepaald door de `functionTimeout` instelling in het [host.jsvan](functions-host-json.md#functiontimeout) het project bestand.

## <a name="scaling"></a>Schalen

Met een App Service-abonnement kunt u hand matig uitschalen door meer VM-exemplaren toe te voegen. U kunt automatisch schalen ook inschakelen, maar automatisch schalen is langzamer dan de elastische schaal van het Premium-abonnement. Zie [aantal exemplaren hand matig of automatisch schalen](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)voor meer informatie. U kunt ook omhoog schalen door een ander App Service plan te kiezen. Zie [een app omhoog schalen in azure](../app-service/manage-scale-up.md)voor meer informatie. 

> [!NOTE] 
> Wanneer u Java script-functies (Node.js) uitvoert op een App Service-abonnement, moet u een abonnement kiezen dat minder Vcpu's heeft. Zie voor meer informatie [single-core app service-abonnementen kiezen](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>App Service-omgevingen

Met een [app service Environment](../app-service/environment/intro.md) (ASE) kunt u uw functies volledig isoleren en profiteren van meer exemplaren dan een app service plan. Zie als u aan de slag wilt gaan.

Als u de functie-app in een virtueel netwerk wilt uitvoeren, kunt u dit doen met behulp van het [Premium-abonnement](functions-premium-plan.md). Zie [Azure functions toegang tot persoonlijke sites instellen](functions-create-private-site-access.md)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

+ [Opties voor Azure Functions hosten](functions-scale.md)
+ [Overzicht van Azure App Service-plan](../app-service/overview-hosting-plans.md)