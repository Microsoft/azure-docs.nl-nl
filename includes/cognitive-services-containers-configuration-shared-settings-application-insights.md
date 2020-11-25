---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001152"
---
Met deze `ApplicationInsights` instelling kunt u ondersteuning voor [Azure-toepassing Insights](/azure/application-insights) -telemetrie toevoegen aan de container. Application Insights biedt uitgebreide controle over uw container. U kunt uw container eenvoudig controleren op Beschik baarheid, prestaties en gebruik. U kunt ook snel fouten in uw container identificeren en onderzoeken.

In de volgende tabel worden de configuratie-instellingen beschreven die in de sectie worden ondersteund `ApplicationInsights` .

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Nee| `InstrumentationKey` | Tekenreeks | De instrumentatie sleutel van het Application Insights exemplaar waarnaar de telemetriegegevens voor de container worden verzonden. Zie [Application Insights voor ASP.net core](../articles/azure-monitor/app/asp-net-core.md)voor meer informatie. <br><br>Voorbeeld:<br>`InstrumentationKey=123456789`|