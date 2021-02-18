---
title: Automatisch schalen in azure met behulp van een aangepaste metrische gegevens
description: Meer informatie over hoe u uw resource kunt schalen op basis van aangepaste metrische gegevens in Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 8e744e6a91eb6fbe23a6b45f95c39b1acfdcb61f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610932"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Aan de slag met automatisch schalen op basis van aangepaste metrische gegevens in azure
In dit artikel wordt beschreven hoe u uw resource kunt schalen op basis van een aangepaste metriek in Azure Portal.

Azure Monitor automatisch schalen alleen van toepassing is op [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-web apps](https://azure.microsoft.com/services/app-service/web/), [Azure Data Explorer-cluster](https://azure.microsoft.com/services/data-explorer/)   
Integratieserviceomgeving-en [API Management-Services](../../api-management/api-management-key-concepts.md).

## <a name="lets-get-started"></a>Hiermee kunt u aan de slag
In dit artikel wordt ervan uitgegaan dat u een web-app met Application Insights hebt geconfigureerd. Als u er nog geen hebt, kunt u [Application Insights instellen voor uw ASP.net-website][1]

- [Azure Portal][2] openen
- Klik op Azure Monitor pictogram in het navigatie deel venster links.
  ![Azure Monitor starten][3]
- Klik op instelling voor automatisch schalen om alle resources weer te geven waarvoor automatische schaal baarheid van toepassing is, samen met de huidige ![ Automatische schaal status automatisch schalen in azure monitor][4]
- Open de Blade automatisch schalen in Azure Monitor en selecteer een resource die u wilt schalen
  > Opmerking: in de volgende stappen wordt een app service-plan gebruikt dat is gekoppeld aan een web-app die app Insights heeft geconfigureerd.
- In de Blade schaal instelling voor de resource ziet u dat het huidige aantal exemplaren 1 is. Klik op automatisch schalen inschakelen.
  ![Schaal instelling voor nieuwe web-app][5]
- Geef een naam op voor de schaal instelling en klik op ' een regel toevoegen '. U ziet de opties voor de schaal regel die als context deel venster worden weer gegeven aan de rechter kant. Standaard wordt de optie voor het schalen van het aantal instanties door 1 ingesteld als het CPU-percentage van de resource groter is dan 70%. Wijzig de metrische bron boven aan ' Application Insights ', selecteer de app Insights-resource in de vervolg keuzelijst resource en selecteer vervolgens de aangepaste metriek op basis waarvan u de schaal wilt aanpassen.
  ![Schalen op aangepaste metrische gegevens][6]
- Net als bij de bovenstaande stap voegt u een schaal regel toe die wordt geschaald en verlaagt u het aantal schalen met 1 als de aangepaste metriek onder een drempel waarde ligt.
  ![Schalen op basis van CPU][7]
- Stel de limieten van het exemplaar in. Als u bijvoorbeeld wilt schalen tussen 2-5-instanties, afhankelijk van de aangepaste fluctuaties van de metriek, stelt u ' minimum ' in ' 2 ', ' maximum ' op ' 5 ' en ' standaard ' in ' 2 '
  > Opmerking: als er een probleem is opgetreden bij het lezen van de metrische gegevens van de resource en de huidige capaciteit lager is dan de standaard capaciteit, moet automatisch schalen worden uitgebreid naar de standaard waarde om te zorgen voor de beschik baarheid van de resource. Als de huidige capaciteit al hoger is dan standaard capaciteit, kan automatisch schalen niet worden geschaald.
- Klik op opslaan

Gefeliciteerd. U hebt nu de schaal instelling gemaakt om uw web-app automatisch te schalen op basis van een aangepaste metriek.

> Opmerking: dezelfde stappen zijn van toepassing om aan de slag te gaan met een VMSS of Cloud service-rol.

<!--Reference-->
[1]: ../app/asp-net.md
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png
