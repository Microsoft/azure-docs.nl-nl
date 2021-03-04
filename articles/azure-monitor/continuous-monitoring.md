---
title: Doorlopende bewaking met Azure Monitor | Microsoft Docs
description: Hierin worden specifieke stappen beschreven voor het gebruik van Azure Monitor om doorlopende bewaking in te scha kelen voor uw werk stromen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 2fe9d7e4da8950cee2dcb2fbcfb26355ace7285d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044014"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Doorlopende bewaking met Azure Monitor

Doorlopende bewaking verwijst naar het proces en de technologie die vereist is voor het opnemen van bewaking in elke fase van uw DevOps en de levens cyclus van IT-activiteiten. Het helpt voortdurend de status, prestaties en betrouw baarheid van uw toepassing en infra structuur te garanderen wanneer deze van ontwikkeling naar productie wordt verplaatst. Doorlopende bewaking bouwt voort op de concepten van continue integratie en continue implementatie (CI/CD) waarmee u sneller en betrouwbaarder software kunt ontwikkelen en leveren om uw gebruikers een voortdurende waarde te bieden.

[Azure monitor](overview.md) is de geïntegreerde bewakings oplossing in azure waarmee u de prestaties van de volledige stack in de Cloud en on-premises kunt analyseren. Het werkt probleemloos met [Visual Studio en Visual Studio code](https://visualstudio.microsoft.com/) tijdens het ontwikkelen en testen en integreert met [Azure DevOps](/azure/devops/user-guide/index) voor release beheer en beheer van werk items tijdens de implementatie en bewerkingen. Het kan zelfs worden geïntegreerd in de ITSM-en SIEM-hulpprogram ma's van uw keuze om problemen en incidenten in uw bestaande IT-processen bij te houden.

In dit artikel worden specifieke stappen beschreven voor het gebruik van Azure Monitor om doorlopende bewaking in uw werk stromen mogelijk te maken. Het artikel bevat koppelingen naar andere documentatie met informatie over het implementeren van verschillende functies.


## <a name="enable-monitoring-for-all-your-applications"></a>Bewaking inschakelen voor al uw toepassingen
U moet bewaking inschakelen voor al uw webtoepassingen en-services om de beschikbaarheid van uw hele omgeving te verkrijgen. Op deze manier kunt u eenvoudig end-to-end-trans acties en verbindingen over alle onderdelen visualiseren.

- [Azure DevOps projects](../devops-project/overview.md) bieden u een vereenvoudigde ervaring met uw bestaande code en git-opslag plaats, of u kunt kiezen uit een van de voorbeeld toepassingen om een doorlopende integratie (CI) en continue levering (cd)-pijp lijn te maken naar Azure.
- Door [doorlopende bewaking in uw DevOps-release pijplijn](./app/continuous-monitoring.md) kunt u uw implementatie op basis van bewakings gegevens dekoppelen of terugdraaien.
- Met [status monitor](./app/monitor-performance-live-website-now.md) kunt u een live .net-app op Windows met Azure-toepassing inzichten instrumenteren zonder dat u uw code hoeft te wijzigen of opnieuw te implementeren.
- Als u toegang hebt tot de code voor uw toepassing, schakelt u volledige controle in met [Application Insights](./app/app-insights-overview.md) door de Azure monitor Application Insights SDK voor [.net](./app/asp-net.md), [.net core](./app/asp-net-core.md), [Java](./app/java-get-started.md), [Node.js](./app/nodejs-quick-start.md)of [andere programmeer talen](./app/platforms.md)te installeren. Hiermee kunt u aangepaste gebeurtenissen, metrische gegevens of pagina weergaven opgeven die relevant zijn voor uw toepassing en uw bedrijf.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Bewaking inschakelen voor uw hele infra structuur
Toepassingen zijn alleen betrouwbaar als de onderliggende infra structuur. Als u bewaking hebt ingeschakeld in uw hele infra structuur, kunt u de volledige waarneming optimaal benutten en kunt u gemakkelijker een mogelijke hoofd oorzaak ontdekken wanneer er iets mislukt. Azure Monitor helpt u bij het volgen van de status en prestaties van uw volledige hybride infra structuur, inclusief bronnen zoals Vm's, containers, opslag en netwerk.

- U krijgt automatisch [platform metrieken, activiteiten logboeken en Diagnostische logboeken](agents/data-sources.md) van uw Azure-resources zonder configuratie.
- Schakel diep gaande bewaking in voor Vm's met [VM Insights](vm/vminsights-overview.md).
-  Schakel diep gaande bewaking in voor AKS-clusters met [Azure monitor voor containers](containers/container-insights-overview.md).
- Voeg [bewakings oplossingen](./monitor-reference.md) toe voor verschillende toepassingen en services in uw omgeving.


[Infra structuur als code](/azure/devops/learn/what-is-infrastructure-as-code) is het beheer van de infra structuur in een beschrijvende model, waarbij dezelfde versie wordt gebruikt als voor de DevOps teams voor de bron code. Het voegt de betrouw baarheid en schaal baarheid aan uw omgeving toe en maakt het u mogelijk vergelijk bare processen te gebruiken waarmee uw toepassingen worden beheerd.

-  Gebruik [Resource Manager-sjablonen](./logs/resource-manager-workspace.md) om bewaking in te scha kelen en waarschuwingen in te stellen voor een grote set resources.
- Gebruik [Azure Policy](../governance/policy/overview.md) om verschillende regels voor uw resources af te dwingen. Dit zorgt ervoor dat deze resources compatibel blijven met uw bedrijfs standaarden en service overeenkomsten. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Resources in azure-resource groepen combi neren
Een typische toepassing op Azure bevat nu meerdere resources, zoals Vm's en App Services of micro services die worden gehost op Cloud Services, AKS-clusters of Service Fabric. Deze toepassingen maken vaak gebruik van afhankelijkheden zoals Event Hubs, opslag, SQL en Service Bus.

- Combi neer resources in azure-resource groepen voor een volledige zicht baarheid van alle resources die deel uitmaken van uw verschillende toepassingen. [Azure monitor voor resource groepen](./insights/resource-group-insights.md) biedt een eenvoudige manier om de status en prestaties van uw volledige toepassing met volledige stacks bij te houden en in te zoomen op de verschillende onderdelen voor onderzoek of fout opsporing.

## <a name="ensure-quality-through-continuous-deployment"></a>Gegarandeerde kwaliteit door doorlopende implementatie
Continue integratie/continue implementatie biedt u de mogelijkheid om automatisch code wijzigingen in uw toepassing te integreren en te implementeren op basis van de resultaten van geautomatiseerd testen. Het zorgt voor het stroom lijnen van het implementatie proces en garandeert de kwaliteit van eventuele wijzigingen voordat ze in de productie omgeving worden verplaatst.


- Gebruik [Azure-pijp lijnen](/azure/devops/pipelines) om continue implementatie te implementeren en uw hele proces te automatiseren vanuit code doorvoer naar productie op basis van uw CI/cd-tests.
- Gebruik [kwaliteits poorten](/azure/devops/pipelines/release/approvals/gates) om de bewaking te integreren in uw pre-implementatie of na de implementatie. Dit zorgt ervoor dat u voldoet aan de belangrijkste status/prestatie meters (Kpi's), omdat uw toepassingen van dev naar productie worden verplaatst en eventuele verschillen in de infrastructuur omgeving of schaal geen negatieve invloed hebben op uw Kpi's.
- [Onderhoud afzonderlijke bewakings instanties](./app/separate-resources.md) tussen uw verschillende implementatie omgevingen, zoals dev, test, Canarische en productie. Op deze manier zorgt u ervoor dat verzamelde gegevens relevant zijn voor de bijbehorende toepassingen en infra structuur. Als u gegevens tussen omgevingen moet correleren, kunt u gebruikmaken [van grafieken met meerdere bronnen in Metrics Explorer](./essentials/metrics-charts.md) of [query's voor meerdere resources maken in azure monitor](logs/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Met acties actie bare waarschuwingen maken
Een kritiek aspect van bewaking is het proactief om beheerders op de hoogte te stellen van eventuele actuele en voorspelde problemen. 

- Maak [waarschuwingen in azure monitor](./alerts/alerts-overview.md) op basis van Logboeken en metrische gegevens om voorspel bare fout statussen te identificeren. Het is belang rijk om ervoor te zorgen dat alle waarschuwingen kunnen betekenen dat ze werkelijke kritieke voor waarden vertegenwoordigen en om te voor komen dat er valse positieven worden gereduceerd. Gebruik [Dynamische drempel waarden](alerts/alerts-dynamic-thresholds.md) voor het automatisch berekenen van basis lijnen op metrische gegevens in plaats van uw eigen statische drempels te definiëren. 
- Definieer acties voor waarschuwingen voor het gebruik van de meest efficiënte manier om uw beheerders op de hoogte te stellen. Beschik bare [acties voor meldingen](alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) zijn SMS, e-mails, Push meldingen of telefoon gesprekken.
- Gebruik geavanceerde acties om [verbinding te maken met uw ITSM-hulp programma](alerts/itsmc-overview.md) of andere systemen voor waarschuwings beheer via [webhooks](alerts/activity-log-alerts-webhook.md).
- Scenario's herstellen die in waarschuwingen zijn geïdentificeerd, evenals [Azure Automation runbooks](../automation/automation-webhooks.md) of [Logic apps](/connectors/custom-connectors/create-webhook-trigger) die vanuit een waarschuwing kunnen worden gestart met behulp van webhooks. 
- Gebruik automatisch [schalen](./autoscale/tutorial-autoscale-performance-schedule.md) om uw reken resources dynamisch te verhogen en te verlagen op basis van de verzamelde metrische gegevens.

## <a name="prepare-dashboards-and-workbooks"></a>Dash boards en werkmappen voorbereiden
Zorg ervoor dat uw ontwikkeling en bewerkingen toegang hebben tot dezelfde telemetrie en hulpprogram ma's voor het weer geven van patronen in uw hele omgeving en om uw gemiddelde tijd te minimaliseren (MTTD) en de gemiddelde tijd om te herstellen (MTTR).

- Bereid [aangepaste Dash boards](./app/tutorial-app-dashboards.md) voor op basis van algemene metrische gegevens en logboeken voor de verschillende rollen in uw organisatie. Dash boards kunnen gegevens van alle Azure-resources combi neren.
- [Werkmappen](./visualize/workbooks-overview.md) voorbereiden om te zorgen voor kennis delen tussen ontwikkeling en bewerkingen. Deze kunnen worden voor bereid als dynamische rapporten met metrische grafieken en logboek query's, of zelfs als richt lijnen voor probleem oplossing die door ontwikkel aars worden ondersteund om de ondersteuning of bewerkingen van klanten te helpen bij het verwerken van basis problemen.

## <a name="continuously-optimize"></a>Continu optimaliseren
 Bewaking is een van de fundamentele aspecten van de populaire build-Measure-leer filosofie, waarmee u uw Kpi's en gebruikers gedrags gegevens voortdurend kunt bijhouden en deze vervolgens kunt optimaliseren door plannings iteraties. Azure Monitor helpt u metrische gegevens en logboeken te verzamelen die relevant zijn voor uw bedrijf en om zo nodig nieuwe gegevens punten in de volgende implementatie toe te voegen.

- Gebruik hulpprogram ma's in Application Insights om het [gedrag van eind gebruikers en betrokkenheid](./app/tutorial-users.md)bij te houden.
- Gebruik [impact analyse](./app/usage-impact.md) om u te helpen bepalen op welke gebieden u zich wilt richten op belang rijke kpi's.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de verschillen onderdelen van [Azure monitor](overview.md).
- [Voeg doorlopende bewaking](./app/continuous-monitoring.md) toe aan uw release pijplijn.