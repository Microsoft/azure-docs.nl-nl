---
title: Bewaak toepassingen op Azure Kubernetes service (AKS) met Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor naadloos kan worden geïntegreerd met uw toepassing die wordt uitgevoerd op Kubernetes, en u kunt de problemen met uw apps in geen enkel moment herkennen.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87075299"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Bewaking van nul-toepassings controle voor Kubernetes-Azure Monitor Application Insights

> [!IMPORTANT]
>  Op dit moment kunt u bewaking inschakelen voor uw java-apps die worden uitgevoerd op Kubernetes zonder uw code te gebruiken. Gebruik de [zelfstandige Java-Agent](./java-in-process-agent.md). Hoewel de oplossing om toepassings bewaking naadloos in te scha kelen in de Works voor andere talen, gebruikt u de Sdk's om uw apps te bewaken die worden uitgevoerd op AKS: [ASP.net core](./asp-net-core.md), [ASP.net](./asp-net.md), [Node.js](./nodejs.md), [Java script](./javascript.md)en [python](./opencensus-python.md).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Toepassings bewaking zonder de code te instrumenteren
Momenteel kunt u met alleen Java toepassings bewaking inschakelen zonder de code te instrumenteren. Als u toepassingen in andere talen wilt bewaken, gebruikt u de Sdk's. 

## <a name="java"></a>Java
Zodra de Java-Agent is ingeschakeld, worden er automatisch een groot aantal aanvragen, afhankelijkheden, logboeken en metrische gegevens van de meest gebruikte bibliotheken en frameworks verzameld.

Volg [de gedetailleerde instructies](./java-in-process-agent.md) voor het bewaken van uw java-apps die worden uitgevoerd in Kubernetes-apps, evenals andere omgevingen. 

## <a name="other-languages"></a>Andere talen

Voor de toepassingen in andere talen raden we u aan de Sdk's te gebruiken:
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure monitor](../overview.md) en [Application Insights](./app-insights-overview.md)
* Bekijk een overzicht van [gedistribueerde tracering](./distributed-tracing.md) en zie welke [toepassings toewijzing](./app-map.md?tabs=net) voor uw bedrijf kan worden uitgevoerd
