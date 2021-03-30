---
title: Een app schalen in ASE v1
description: Een app schalen in een App Service Environment. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0e665ec27da0a898e754817f946b965ac7360fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86220555"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Apps schalen in een App Service Environment v1
In de Azure App Service zijn doorgaans drie dingen die u kunt schalen:

* prijs plan
* grootte van werk nemer 
* aantal exemplaren.

In een ASE hoeft u het prijs plan niet te selecteren of te wijzigen.  In termen van mogelijkheden is het een niveau voor premium-prijs capaciteit.  

Met betrekking tot de grootte van werk nemers kan de ASE-beheerder de grootte toewijzen van de reken resource die moet worden gebruikt voor elke werk groep.  Dit betekent dat u werk groep 1 met P4-reken resources en werk groep 2, indien gewenst, kunt voorzien van P1-reken resources.  Ze hoeven zich niet in de juiste volg orde te bevinden.  Zie het document hier [Azure app service prijzen][AppServicePricing]voor meer informatie over de grootten en hun prijzen.  Hiermee blijven de schaal opties voor web-apps en App Service plannen in een App Service Environment:

* selectie van werknemers groep
* aantal exemplaren

Het wijzigen van een item wordt uitgevoerd via de juiste gebruikers interface die wordt weer gegeven voor uw ASE-gehoste App Service plannen.  

![Scherm afbeelding die laat zien waar u de details van het Scale-service plan en het service plan voor de worker-groep kunt weer geven.][1]

U kunt uw ASP niet meer schalen dan het aantal beschik bare reken resources in de werk groep waarin uw ASP zich bevindt.  Als u reken resources in die werk groep nodig hebt, moet u uw ASE-beheerder vragen om deze toe te voegen.  Lees de informatie hier voor informatie over het opnieuw configureren van uw ASE: [een app service omgeving configureren][HowtoConfigureASE].  U kunt ook profiteren van de ASE-functies voor automatisch schalen om capaciteit toe te voegen op basis van planning of metrische gegevens.  Zie voor meer informatie over het configureren van automatisch schalen voor de ASE-omgeving zelf [het automatisch schalen configureren voor een app service Environment][ASEAutoscale].

U kunt meerdere app service-plannen maken met behulp van reken resources uit verschillende werk groepen of u kunt dezelfde werk groep gebruiken.  Als u bijvoorbeeld (10) beschik bare reken resources in werk groep 1 hebt, kunt u ervoor kiezen om één app service-plan te maken met behulp van (6) reken resources en een tweede app service-plan dat gebruikmaakt van (4) reken resources.

### <a name="scaling-the-number-of-instances"></a>Het aantal exemplaren schalen
Wanneer u uw web-app voor het eerst in een App Service Environment maakt, begint deze met 1 instantie.  U kunt vervolgens uitschalen naar aanvullende instanties om extra reken resources te bieden voor uw app.   

Als uw ASE voldoende capaciteit heeft, is dit tamelijk eenvoudig.  U gaat naar uw App Service-abonnement dat de sites bevat die u omhoog wilt schalen en schalen selecteren.  Hiermee opent u de gebruikers interface waar u de schaal voor uw ASP hand matig kunt instellen of regels voor automatisch schalen voor uw ASP wilt configureren.  Als u uw app hand matig wilt schalen **,** stelt u * in op _ *_een aantal exemplaren dat hand matig wordt ingevoerd_* *.  Sleep de schuif regelaar naar het gewenste aantal of typ deze in het vak naast de schuif regelaar.  

![Scherm afbeelding die laat zien waar u de schaal voor uw ASP kunt instellen of regels voor automatisch schalen voor uw ASP wilt configureren.][2] 

De regels voor automatisch schalen voor een ASP in een ASE werken op dezelfde manier als normaal.  U kunt ***CPU-percentage** _ selecteren onder _*_schalen op_*_ en regels voor automatisch schalen maken voor uw ASP op basis van het CPU-percentage of u kunt complexere regels maken met behulp van de regels van het _schema en de prestaties_* *.  Meer informatie over het configureren van automatisch schalen vindt u in de hand leiding hier [schaalt u een app in azure app service][AppScale]. 

### <a name="worker-pool-selection"></a>Selectie van werknemers groep
Zoals eerder is vermeld, wordt de selectie van de werknemers groep geopend vanuit de ASP-gebruikers interface.  Open de Blade voor de ASP die u wilt schalen en selecteer werknemers groep.  U ziet alle worker-groepen die u in uw App Service Environment hebt geconfigureerd.  Als u slechts één werk groep hebt, ziet u alleen de ene groep die wordt vermeld.  Als u wilt wijzigen in welke werk groep uw ASP zich bevindt, selecteert u alleen de werk groep waaraan u het App Service plan wilt verplaatsen.  

![Scherm afbeelding die laat zien waar u kunt wijzigen in welke werk groep uw ASP zich bevindt.][3]

Voordat u uw ASP van de ene werk groep naar het andere verplaatst, is het belang rijk om ervoor te zorgen dat u over voldoende capaciteit beschikt voor uw ASP.  In de lijst met werk groepen is niet alleen de naam van de werk groep vermeld, maar u kunt ook zien hoeveel werk nemers er beschikbaar zijn in die werknemers groep.  Zorg ervoor dat er voldoende instanties beschikbaar zijn om uw App Service-abonnement te bevatten.  Als u meer reken resources nodig hebt in de werk groep waarnaar u wilt verplaatsen, kunt u de ASE-beheerder vragen om deze toe te voegen.  

> [!NOTE]
> Als u een ASP-item verplaatst van een werk groep, worden de apps in die ASP koud gestart.  Dit kan ertoe leiden dat aanvragen langzaam worden uitgevoerd als uw app koud wordt gestart op de nieuwe reken resources.  De koude start kan worden vermeden door gebruik te maken van de [toepassing opwarmen][AppWarmup] in azure app service.  De module voor het initialiseren van toepassingen die in het artikel wordt beschreven, werkt ook voor koude start, omdat het initialisatie proces ook wordt aangeroepen wanneer apps koud worden gestart op nieuwe reken resources. 
> 
> 

## <a name="getting-started"></a>Aan de slag
Zie [een app service Environment maken][HowtoCreateASE] om aan de slag te gaan met app service omgevingen

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
