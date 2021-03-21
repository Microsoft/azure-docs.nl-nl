---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006867"
---
De container heeft de volgende configuratie-instellingen:

|Vereist|Instelling|Doel|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Registreert facturerings gegevens.|
|Nee|[ApplicationInsights](#applicationinsights-setting)|Hiermee kunt u ondersteuning voor [Azure-toepassing Insights](/azure/application-insights) -telemetrie toevoegen aan uw container.|
|Ja|[Facturering](#billing-configuration-setting)|Hiermee geeft u de eindpunt-URI op van de service resource op Azure.|
|Ja|[Houdt](#eula-setting)| Geeft aan dat u de licentie voor de container hebt geaccepteerd.|
|Nee|[Fluentd](#fluentd-settings)|Schrijft logboek en, eventueel, metrische gegevens naar een vloeiende server.|
|Nee|HTTP-proxy|Hiermee configureert u een HTTP-proxy voor het maken van uitgaande aanvragen.|
|Nee|[Logboekregistratie](#logging-settings)|Biedt ASP.NET Core ondersteuning voor logboek registratie voor uw container. |
|Nee|[Koppelt](#mount-settings)|Leest en schrijft gegevens van de hostcomputer naar de container en van de container terug naar de hostcomputer.|