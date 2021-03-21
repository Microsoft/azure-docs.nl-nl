---
title: Achtergrond taken uitvoeren met webjobs
description: Meer informatie over het gebruik van webjobs om achtergrond taken uit te voeren in Azure App Service. Kies uit verschillende script indelingen en voer deze uit met CRON-expressies.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0d49323e2bc3c0522b1fb9ad49ffcc14f476e2dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452795"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Achtergrond taken uitvoeren met webjobs in Azure App Service

Het concept van het uitvoeren van [achtergrond taken](./webjobs-create-ieux-conceptual.md) op Azure wordt meegeleverd met Azure-app service-webtaken. Meer informatie over het implementeren van <abbr title="Een programma of script in hetzelfde exemplaar als een web-app, API-app of mobiele app.">WebJobs</abbr> door gebruik te maken van de [Azure Portal](https://portal.azure.com) om een uitvoerbaar bestand of script te uploaden. 

Er zijn drie ondersteunde webjobs:

* **Doorlopend**: wordt onmiddellijk gestart, normaal gesp roken uitgevoerd in een oneindige lus.
* **Gepland**: begint bij geplande trigger
* **Hand matig**: begint met hand matige trigger

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> Volgende stappen

* [Meer informatie over achtergrond taken als webjobs](./webjobs-create-ieux-conceptual.md)
* [De logboek geschiedenis van webjobs weer geven](./webjobs-create-ieux-view-log.md)

* De [Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) gebruiken om veel programmeer taken te vereenvoudigen

* Leer hoe u [webjobs kunt ontwikkelen en implementeren met Visual Studio](webjobs-dotnet-deploy-vs.md)
