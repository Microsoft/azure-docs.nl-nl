---
title: Terminologie-updates Azure Monitor | Microsoft Docs
description: Hierin worden de recente terminologie wijzigingen beschreven die zijn aangebracht in azure monitoring services.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: fea85a85db1e555adea63f5b65b09a9285d714da
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031970"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor naamgevings-en terminologie wijzigingen
Er zijn belang rijke wijzigingen aangebracht in Azure Monitor recent, waarbij verschillende services worden geconsolideerd om de controle van Azure-klanten te vereenvoudigen. In dit artikel worden de recente naam en terminologie wijzigingen in Azure Monitor documentatie beschreven.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Oktober 2019-diagnostisch logboek naar resource logboek
' Diagnostische logboeken ' zijn gewijzigd in ' resource logs ' om beter te kunnen overeenkomen met wat er daad werkelijk wordt verzameld. De term Diagnostische instellingen blijft hetzelfde.  

## <a name="february-2019---log-analytics-terminology"></a>Log Analytics met terminologie van februari 2019
Na de consolidatie van verschillende services onder Azure Monitor, nemen we de volgende stap in de hand van de terminologie in onze documentatie om de Azure Monitor-service en de verschillende onderdelen beter te beschrijven. 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor logboek gegevens worden nog steeds opgeslagen in een Log Analytics-werk ruimte en worden nog steeds verzameld en geanalyseerd door dezelfde Log Analytics-service, maar we wijzigen de term _log Analytics_ op veel plaatsen in _Azure monitor logboeken_. Deze term weerspiegelt de rol in Azure Monitor en biedt een betere consistentie met [metrische gegevens in azure monitor](essentials/data-platform-metrics.md).

De term _log Analytics_ is nu voornamelijk van toepassing op de pagina in de Azure Portal gebruikt om query's te schrijven en uit te voeren en logboek gegevens te analyseren. Het is het functionele equivalent van [Metrics Explorer](essentials/metrics-charts.md), de pagina in de Azure Portal gebruikt voor het analyseren van metrische gegevens.

### <a name="log-analytics-workspaces"></a>Log Analytics-werkruimten
[Werk ruimten](logs/manage-access.md) die logboek gegevens bevatten in azure monitor worden nog steeds log Analytics werk ruimten genoemd. De naam van het menu **log Analytics** in het Azure Portal is gewijzigd in **log Analytics werk ruimten** en is waar u [nieuwe werk ruimten maakt](logs/quick-create-workspace.md) en gegevens bronnen configureert. Analyseer uw logboeken en andere bewakings gegevens in **Azure monitor** en configureer uw werk ruimte in **log Analytics-werk ruimten**.

### <a name="management-solutions"></a>Beheeroplossingen
De naam van [beheer oplossingen](insights/solutions.md) is gewijzigd voor het _bewaken van oplossingen_, waardoor de functionaliteit ervan beter wordt beschreven.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Augustus 2018-consolidatie van bewakings Services in Azure Monitor
Log Analytics en Application Insights zijn samengevoegd in Azure Monitor om één geïntegreerde ervaring te bieden voor het bewaken van Azure-resources en hybride omgevingen. Er is geen functionaliteit van deze services verwijderd en gebruikers kunnen dezelfde scenario's uitvoeren als ze altijd hebben voltooid zonder verlies of inbreuk op functies.

De documentatie voor elk van deze services is geconsolideerd in één set inhoud voor Azure Monitor. Dit helpt de lezer bij het vinden van alle inhoud voor een bepaald bewakings scenario op één locatie, in plaats van te verwijzen naar meerdere sets inhoud. Naarmate de geconsolideerde service zich ontwikkelt, wordt de inhoud geïntegreerd.

Andere functies die worden beschouwd als onderdeel van Log Analytics, zoals agents en weer gaven, zijn ook gewijzigd als functies van Azure Monitor. De functionaliteit van de functie is nog niet gewijzigd in de Azure Portal.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>2018 april-buiten gebruik stellen van het merk van Operations Management Suite
Operations Management Suite (OMS) is een bundeling van de volgende Azure Management-Services voor licentie doeleinden:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Siteherstel

Er zijn [nieuwe prijzen geïntroduceerd voor deze services](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)en de OMS-bundel is niet langer beschikbaar voor nieuwe klanten. Geen van de services die deel uitmaken van OMS, zijn gewijzigd, met uitzonde ring van de consolidatie in Azure Monitor hierboven beschreven. 




## <a name="next-steps"></a>Volgende stappen

- Lees een [overzicht van Azure monitor](overview.md) waarin de verschillende onderdelen en functies worden beschreven.
- Meer informatie over de [overgang van de OMS-Portal](./logs/oms-portal-transition.md).
