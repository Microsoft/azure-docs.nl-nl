---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006867"
---
De container heeft de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Registreert facturerings gegevens.|
|No|[ApplicationInsights](#applicationinsights-setting)|Hiermee kunt u ondersteuning voor [Azure-toepassing Insights](/azure/application-insights) -telemetrie toevoegen aan uw container.|
|Yes|[Facturering](#billing-configuration-setting)|Hiermee geeft u de eindpunt-URI op van de service resource op Azure.|
|Yes|[Houdt](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|No|[Fluentd](#fluentd-settings)|Schrijft logboek en, eventueel, metrische gegevens naar een vloeiende server.|
|No|HTTP-proxy|Hiermee configureert u een HTTP-proxy voor het maken van uitgaande aanvragen.|
|No|[Logboekregistratie](#logging-settings)|Biedt ASP.NET Core ondersteuning voor logboek registratie voor uw container. |
|No|[Koppelt](#mount-settings)|Leest en schrijft gegevens van de hostcomputer naar de container en van de container terug naar de hostcomputer.|