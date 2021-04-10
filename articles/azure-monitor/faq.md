---
title: Azure Monitor Veelgestelde vragen | Microsoft Docs
description: Antwoorden op veelgestelde vragen over Azure Monitor.
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2020
ms.openlocfilehash: fa91644eab9d28ffb20de8ec8c0fe00488922b67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563375"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Veelgestelde vragen over Azure Monitor

Deze veelgestelde vragen over micro soft vindt u een lijst met veel gestelde antwoorden over Azure Monitor. Als u nog vragen hebt, gaat u naar het [discussie forum](/answers/questions/topics/single/24223.html) en plaatst u uw vragen. Wanneer een vraag regel matig wordt gesteld, voegen we deze toe aan dit artikel zodat het snel en eenvoudig kan worden gevonden.


## <a name="general"></a>Algemeen

### <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?
[Azure monitor](overview.md) is een service in azure die prestatie-en beschikbaarheids bewaking biedt voor toepassingen en services in azure, andere Cloud omgevingen of on-premises. Azure Monitor verzamelt gegevens uit meerdere bronnen in een gemeen schappelijk gegevens platform waar het kan worden geanalyseerd op trends en afwijkingen. Met uitgebreide functies in Azure Monitor kunt u snel kritieke situaties identificeren en erop reageren die van invloed kunnen zijn op uw toepassing.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Wat is het verschil tussen Azure Monitor, Log Analytics en Application Insights?
In september 2018, micro soft gecombineerd Azure Monitor, Log Analytics en Application Insights in één service om een krachtige end-to-end bewaking te bieden van uw toepassingen en de onderdelen waarop ze zijn gebaseerd. Functies in Log Analytics en Application Insights zijn niet gewijzigd, maar sommige functies zijn opnieuw ingesteld op Azure Monitor om de nieuwe scope beter weer te geven. De logboek gegevens engine en query taal van Log Analytics worden nu Azure Monitor-logboeken genoemd. Zie [Azure monitor terminologie-updates](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Wat betekent Azure Monitor kosten?
Functies van Azure Monitor die automatisch worden ingeschakeld, zoals het verzamelen van metrische gegevens en activiteiten logboeken, zijn gratis. Er zijn kosten verbonden aan andere functies, zoals logboek query's en waarschuwingen. Zie de [pagina met prijzen voor Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) voor gedetailleerde prijs informatie.

### <a name="how-do-i-enable-azure-monitor"></a>Azure Monitor Hoe kan ik inschakelen?
Azure Monitor is ingeschakeld wanneer u een nieuw Azure-abonnement maakt, het [activiteiten logboek](./essentials/platform-logs-overview.md) en de [metrische gegevens](essentials/data-platform-metrics.md) van het platform worden automatisch verzameld. Maak [Diagnostische instellingen](essentials/diagnostic-settings.md) voor het verzamelen van meer gedetailleerde informatie over de werking van uw Azure-resources en voeg [bewakings oplossingen](insights/solutions.md) en [inzichten](./monitor-reference.md) toe om extra analyses te bieden op verzamelde gegevens voor bepaalde services. 

### <a name="how-do-i-access-azure-monitor"></a>Hoe kan ik toegang tot Azure Monitor?
Toegang tot alle Azure Monitor-functies en-gegevens via het menu **monitor** in de Azure Portal. De sectie **bewaking** van het menu voor verschillende Azure-Services biedt toegang tot dezelfde hulpprogram ma's als gegevens die zijn gefilterd op een bepaalde resource. Azure Monitor gegevens zijn ook toegankelijk voor diverse scenario's met behulp van CLI, Power shell en een REST API.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Is er een on-premises versie van Azure Monitor?
Nee. Azure Monitor is een schaal bare Cloud service die grote hoeveel heden gegevens verwerkt en opslaat, hoewel Azure Monitor resources kan bewaken die on-premises en in andere Clouds zijn.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Kan Azure Monitor on-premises resources controleren?
Ja, naast het verzamelen van bewakings gegevens van Azure-resources, kunnen Azure Monitor gegevens verzamelen van virtuele machines en toepassingen in andere Clouds en on-premises. Zie [bronnen van bewakings gegevens voor Azure monitor](agents/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Is Azure Monitor geïntegreerd met System Center Operations Manager?
U kunt uw bestaande System Center Operations Manager-beheer groep verbinden met Azure Monitor om gegevens van agents te verzamelen in Azure Monitor Logboeken. Zo kunt u logboek query's en oplossingen gebruiken om gegevens te analyseren die zijn verzameld door agents. U kunt ook bestaande System Center Operations Manager agenten zo configureren dat gegevens rechtstreeks naar Azure Monitor worden verzonden. Zie [Operations Manager verbinding maken met Azure monitor](agents/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Welke IP-adressen Azure Monitor gebruiken?
Zie [IP-adressen die worden gebruikt door Application Insights en log Analytics](app/ip-addresses.md) voor een lijst met de IP-adressen en poorten die vereist zijn voor agents en andere externe bronnen voor toegang tot Azure monitor. 

## <a name="monitoring-data"></a>Bewakingsgegevens

### <a name="where-does-azure-monitor-get-its-data"></a>Waar Azure Monitor de gegevens ophalen?
Azure Monitor verzamelt gegevens uit verschillende bronnen, waaronder Logboeken en metrieken van Azure-platform en-resources, aangepaste toepassingen en agents die op virtuele machines worden uitgevoerd. Andere services, zoals Azure Security Center, en Network Watcher het verzamelen van gegevens in een Log Analytics-werk ruimte, zodat deze kunnen worden geanalyseerd met Azure Monitor gegevens. U kunt ook aangepaste gegevens naar Azure Monitor verzenden met behulp van de REST API voor Logboeken of metrieken. Zie [bronnen van bewakings gegevens voor Azure monitor](agents/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Welke gegevens worden er door Azure Monitor verzameld? 
Azure Monitor verzamelt gegevens uit een verscheidenheid aan bronnen in [Logboeken](logs/data-platform-logs.md) of [metrieken](essentials/data-platform-metrics.md). Elk type gegevens heeft zijn eigen relatieve voor delen, en alle typen bieden ondersteuning voor een bepaalde set functies in Azure Monitor. Er is één metrische Data Base voor elk Azure-abonnement, terwijl u meerdere Log Analytics-werk ruimten kunt maken voor het verzamelen van Logboeken, afhankelijk van uw vereisten. Zie [Azure monitor data platform](data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Is er een maximale hoeveelheid gegevens die ik kan verzamelen in Azure Monitor?
Er is geen limiet voor de hoeveelheid metrische gegevens die u kunt verzamelen, maar deze gegevens worden Maxi maal 93 dagen opgeslagen. Zie het [bewaren van metrische gegevens](essentials/data-platform-metrics.md#retention-of-metrics). Er is geen limiet voor het aantal logboek gegevens dat u kunt verzamelen, maar dit kan worden beïnvloed door de prijs categorie die u kiest voor de Log Analytics-werk ruimte. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Hoe kan ik toegang tot gegevens die door Azure Monitor zijn verzameld?
Inzichten en oplossingen bieden een aangepaste ervaring voor het werken met gegevens die zijn opgeslagen in Azure Monitor. U kunt rechtstreeks met logboek gegevens werken met behulp van een logboek query die is geschreven in Kusto query language (KQL). In de Azure Portal kunt u query's schrijven en uitvoeren en gegevens interactief analyseren met behulp van Log Analytics. Analyseer de metrische gegevens in de Azure Portal met de Metrics Explorer. Zie [logboek gegevens analyseren in azure monitor](logs/log-query-overview.md) en [aan de slag met Azure Metrics Explorer](essentials/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Oplossingen en inzichten

### <a name="what-is-an-insight-in-azure-monitor"></a>Wat is een inzicht in Azure Monitor?
Inzichten bieden een aangepaste bewakingservaring voor bepaalde Azure-services. Ze gebruiken dezelfde metrische gegevens en Logboeken als andere functies in Azure Monitor, maar kunnen er ook extra informatie verzamelen en een unieke ervaring bieden in de Azure Portal. Zie [inzichten in azure monitor](./monitor-reference.md).

Als u inzichten wilt weer geven in de Azure Portal, raadpleegt u de sectie **inzichten** in het menu **monitor** of in het gedeelte **bewaking** van het menu van de service.

### <a name="what-is-a-solution-in-azure-monitor"></a>Wat is een oplossing in Azure Monitor?
Bewakings oplossingen zijn verpakte sets van logica voor het bewaken van een bepaalde toepassing of service op basis van Azure Monitor-functies. Ze verzamelen logboek gegevens in Azure Monitor en bieden logboek query's en-weer gaven voor hun analyse met behulp van een algemene ervaring in de Azure Portal. Zie [oplossingen controleren in azure monitor](insights/solutions.md).

Als u oplossingen wilt weer geven in de Azure Portal, klikt u op **meer** in het gedeelte **inzichten** van het menu **monitor** . Klik op **toevoegen** om aanvullende oplossingen aan de werk ruimte toe te voegen.

## <a name="logs"></a>Logboeken

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Wat is het verschil tussen Azure Monitor logboeken en Azure Data Explorer?
Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Azure Monitor-Logboeken is gebaseerd op Azure Data Explorer en maakt gebruik van dezelfde Kusto query language (KQL) met enkele kleine verschillen. Zie [Azure monitor taal verschillen in de logboek query](/azure/data-explorer/kusto/query/).

### <a name="how-do-i-retrieve-log-data"></a>Hoe kan ik logboek gegevens ophalen?
Alle gegevens worden opgehaald uit een Log Analytics-werk ruimte met behulp van een logboek query die is geschreven met Kusto query language (KQL). U kunt uw eigen query's schrijven of oplossingen en inzichten gebruiken die logboek query's bevatten voor een bepaalde toepassing of service. Zie [overzicht van logboek query's in azure monitor](logs/log-query-overview.md).

### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>Kan ik gegevens verwijderen uit een Log Analytics-werk ruimte?
Gegevens worden uit een werk ruimte verwijderd op basis van de [Bewaar periode](logs/manage-cost-storage.md#change-the-data-retention-period). U kunt specifieke gegevens verwijderen voor privacy-of nalevings redenen. Zie [persoonlijke gegevens exporteren en verwijderen](logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) voor meer informatie.

### <a name="is-log-analytics-storage-immutable"></a>Is Log Analytics opslag onveranderbaar?
Gegevens in database opslag kunnen niet worden gewijzigd wanneer deze zijn opgenomen, maar kunnen worden verwijderd via het [  pad van de API voor het verwijderen van persoonlijke gegevens](./logs/personal-data-mgmt.md#delete). Hoewel de gegevens niet kunnen worden gewijzigd, moeten voor sommige certificeringen gegevens onveranderlijk worden bewaard en kunnen ze niet worden gewijzigd of verwijderd in de opslag. Gegevens Onveranderbaarheid kunnen worden bereikt met behulp van [gegevens export](./logs/logs-data-export.md) naar een opslag account dat is geconfigureerd als [onveranderlijke opslag](../storage/blobs/storage-blob-immutability-policies-manage.md).

### <a name="what-is-a-log-analytics-workspace"></a>Wat is een Log Analytics-werkruimte?
Alle door Azure Monitor verzamelde logboek gegevens worden opgeslagen in een Log Analytics-werk ruimte. Een werk ruimte is in feite een container waarin logboek gegevens worden verzameld uit verschillende bronnen. Mogelijk hebt u een enkele Log Analytics-werk ruimte voor al uw bewakings gegevens of hebt u vereisten voor meerdere werk ruimten. Zie [de implementatie van uw Azure monitor-logboeken ontwerpen](logs/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Kunt u een bestaande Log Analytics-werk ruimte verplaatsen naar een ander Azure-abonnement?
U kunt een werk ruimte verplaatsen tussen resource groepen of abonnementen, maar niet naar een andere regio. Zie [een log Analytics-werk ruimte verplaatsen naar een ander abonnement of een andere resource groep](logs/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Waarom kan ik query Explorer niet zien en knoppen Opslaan in Log Analytics?

De knoppen **query Verkenner**, **Opslaan** en **nieuwe waarschuwings regel** zijn niet beschikbaar wanneer het [query bereik](logs/scope.md) is ingesteld op een specifieke resource. Als u waarschuwingen wilt maken, een query wilt opslaan of laden, moet Log Analytics bereik zijn van een werk ruimte. Als u Log Analytics in de werkruimte context wilt openen, selecteert u **Logboeken** in het menu **Azure monitor** . De laatst gebruikte werk ruimte is geselecteerd, maar u kunt een andere werk ruimte selecteren. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](logs/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Waarom krijg ik de fout: ' micro soft. Insights van resource provider registreren voor dit abonnement om deze query in te scha kelen ' bij het openen van Log Analytics van een VM? 
Veel resource providers worden automatisch geregistreerd, maar u moet mogelijk bepaalde resource providers hand matig registreren. Het bereik voor registratie is altijd het abonnement. Zie [Resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) voor meer informatie.

### <a name="why-am-i-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Waarom krijg ik geen toegangs fout bericht bij het openen van Log Analytics vanaf een virtuele machine? 
Als u VM-logboeken wilt weer geven, moet u beschikken over de machtiging lezen voor de werk ruimten waarin de VM-logboeken worden opgeslagen. In deze gevallen moet uw beheerder u de machtigingen verlenen in Azure.

## <a name="metrics"></a>Metrische gegevens

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>Waarom worden metrische gegevens van het gast besturingssysteem van mijn virtuele machine van Azure niet weer gegeven in Metrics Explorer?
[Metrische platform gegevens](essentials/monitor-azure-resource.md#monitoring-data) worden automatisch verzameld voor Azure-resources. U moet enige configuratie uitvoeren voor het verzamelen van metrische gegevens uit het gast besturingssysteem van een virtuele machine. Voor een Windows-VM installeert u de diagnostische uitbrei ding en configureert u de Azure Monitor sink, zoals beschreven in [Windows Azure Diagnostics extension (WAD) installeren en configureren](agents/diagnostics-extension-windows-install.md). Voor Linux installeert u de telegrafa-agent zoals beschreven in [aangepaste metrische gegevens verzamelen voor een virtuele Linux-machine met de InfluxData-telegrafa-agent](essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="alerts"></a>Waarschuwingen

### <a name="what-is-an-alert-in-azure-monitor"></a>Wat is een waarschuwing in Azure Monitor?
Waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken. Er zijn meerdere soorten waarschuwingen:

- Metrische waarde voor metriek overschrijdt drempel.
- Logboek query-resultaten van een logboek query die overeenkomt met gedefinieerde criteria.
- Activiteiten logboek-activiteiten logboek gebeurtenis komt overeen met gedefinieerde criteria.
- Webtest: resultaten van de beschikbaarheids test voldoen aan de criteria die zijn gedefinieerd.


Zie [overzicht van waarschuwingen in Microsoft Azure](alerts/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Wat is een actie groep?
Een actie groep is een verzameling van meldingen en acties die kunnen worden geactiveerd door een waarschuwing. Meerdere waarschuwingen kunnen één actie groep gebruiken, zodat u algemene sets van meldingen en acties kunt benutten. Zie [actie groepen maken en beheren in de Azure Portal](alerts/action-groups.md).


### <a name="what-is-an-action-rule"></a>Wat is een actie regel?
Met een actie regel kunt u het gedrag wijzigen van een set waarschuwingen die overeenkomen met een bepaald criterium. Zo kunt u dergelijke vereisten doen als u waarschuwings acties wilt uitschakelen tijdens een onderhouds venster. U kunt ook een actie groep Toep assen op een set waarschuwingen in plaats van deze rechtstreeks toe te passen op de waarschuwings regels. Zie [actie regels](alerts/alerts-action-rules.md).

## <a name="agents"></a>Agents

### <a name="does-azure-monitor-require-an-agent"></a>Is Azure Monitor een agent nodig?
Een agent is alleen vereist voor het verzamelen van gegevens van het besturings systeem en workloads op virtuele machines. De virtuele machines kunnen zich in azure, een andere cloud omgeving of on-premises bevinden. Zie [overzicht van de Azure monitor agents](agents/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Wat is het verschil tussen de Azure Monitor agents?
Diagnostische Azure-extensie is voor virtuele machines van Azure en verzamelt gegevens voor Azure Monitor metrieken, Azure Storage en Azure Event Hubs. De Log Analytics-agent is voor virtuele machines in azure, een andere cloud omgeving of on-premises en verzamelt gegevens voor Azure Monitor-Logboeken. De afhankelijkheids agent vereist de Log Analytics agent en de verzamelde proces gegevens en-afhankelijkheden. Zie [overzicht van de Azure monitor agents](agents/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Maakt mijn agent verkeer gebruik van mijn ExpressRoute-verbinding?
Verkeer naar Azure Monitor maakt gebruik van het micro soft peering ExpressRoute-circuit. Raadpleeg de [ExpressRoute-documentatie](../expressroute/expressroute-faqs.md#supported-services) voor een beschrijving van de verschillende soorten ExpressRoute-verkeer. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Hoe kan ik controleren of de Log Analytics-agent met Azure Monitor kan communiceren?
Selecteer in het configuratie scherm op de computer van de agent **beveiliging & instellingen**, * * micro soft monitoring agent. Op het tabblad **Azure log Analytics (OMS)** wordt met een groen vinkje bevestigd dat de agent kan communiceren met Azure monitor. Een geel waarschuwings pictogram geeft aan dat de agent problemen ondervindt. Een veelvoorkomende reden is dat de service **micro soft Monitoring Agent** is gestopt. Gebruik service besturings beheer om de service opnieuw te starten.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Hoe kan ik Log Analytics agent niet meer communiceren met Azure Monitor?
Voor agents die rechtstreeks met Log Analytics zijn verbonden, opent u het configuratie scherm en selecteert u **instellingen voor beveiliging &**, **micro soft Monitoring Agent**. Verwijder op het tabblad **Azure log Analytics (OMS)** alle weer gegeven werk ruimten. In System Center Operations Manager verwijdert u de computer uit de lijst Log Analytics beheerde computers. Operations Manager werkt de configuratie van de agent bij naar Log Analytics niet meer. 

### <a name="how-much-data-is-sent-per-agent"></a>Hoeveel gegevens worden per agent verzonden?
De hoeveelheid gegevens die per agent wordt verzonden, is afhankelijk van:

* De oplossingen die u hebt ingeschakeld
* Het aantal logboeken en prestatie meter items dat wordt verzameld
* Het gegevens volume in de logboeken

Zie [gebruik en kosten beheren met Azure monitor logboeken](logs/manage-cost-storage.md) voor meer informatie.

Voor computers waarop de WireData-agent kan worden uitgevoerd, gebruikt u de volgende query om te zien hoeveel gegevens er worden verzonden:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Hoeveel netwerk bandbreedte wordt gebruikt door de micro soft Management Agent (MMA) bij het verzenden van gegevens naar Azure Monitor?
Band breedte is een functie voor de hoeveelheid gegevens die wordt verzonden. Gegevens worden gecomprimeerd wanneer ze via het netwerk worden verzonden.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Hoe kan ik een melding ontvangen wanneer het verzamelen van gegevens van de Log Analytics agent stopt?

Gebruik de stappen die worden beschreven in [een waarschuwing bij het maken van een nieuw logboek om een](alerts/alerts-metric.md) melding te ontvangen wanneer het verzamelen van gegevens wordt gestopt. Gebruik de volgende instellingen voor de waarschuwings regel:

- **Waarschuwings voorwaarde definiëren**: geef uw log Analytics-werk ruimte op als bron doel.
- **Waarschuwings criteria** 
   - **Signaal naam**: *aangepaste zoek opdracht in Logboeken*
   - **Zoek query**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Waarschuwings logica**: **gebaseerd op** het *aantal resultaten*, **waarde** *groter dan*, **drempel waarde** *0*
   - **Geëvalueerd op basis van**: **periode (in minuten)** *30*, **frequentie (in minuten)** *10*
- **Waarschuwingsdetails definiëren** 
   - **Naam**: *gegevens verzameling gestopt*
   - **Ernst**: *waarschuwing*

Geef een bestaande of nieuwe [actie groep](alerts/action-groups.md) op, zodat wanneer de logboek waarschuwing overeenkomt met criteria, u een melding ontvangt als er al meer dan 15 minuten een heartbeat ontbreekt.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Wat zijn de firewall vereisten voor Azure Monitor agents?
Zie [netwerk firewall vereisten](agents/log-analytics-agent.md#network-requirements)voor meer informatie over Firewall vereisten.


## <a name="visualizations"></a>Visualisaties

### <a name="why-cant-i-see-view-designer"></a>Waarom kan ik de weer gave Designer niet zien?

De weer gave Designer is alleen beschikbaar voor gebruikers die zijn toegewezen met Inzender machtigingen of een hoger in de Log Analytics-werk ruimte.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Configuratie problemen
*Ik ondervind problemen bij het instellen van mijn:*

* [.NET app](app/asp-net-troubleshoot-no-data.md)
* [Een app die al wordt uitgevoerd bewaken](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostische gegevens van Azure](agents/diagnostics-extension-to-application-insights.md)
* [Java-web-app](app/java-troubleshoot.md)

*Ik krijg geen gegevens van mijn server:*

* [Firewall-uitzonde ringen instellen](app/ip-addresses.md)
* [Een ASP.NET-Server instellen](app/monitor-performance-live-website-now.md)
* [Een Java-Server instellen](app/java-agent.md)

*Hoeveel Application Insights resources moet ik implementeren:*

* [Hoe kunt u uw Application Insights-implementatie ontwerpen: een versus veel Application Insights bronnen?](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Kan ik Application Insights gebruiken met...?

* [Web-apps op een IIS-server in een Azure VM-of Azure virtual machine-schaalset](app/azure-vm-vmss-apps.md)
* [Web-apps op een IIS-server, on-premises of in een VM](app/asp-net.md)
* [Java-web-apps](app/java-get-started.md)
* [Node.js-apps](app/nodejs.md)
* [Web-apps op Azure](app/azure-web-apps.md)
* [Cloud Services op Azure](app/cloudservices.md)
* [App-servers die worden uitgevoerd in docker](./azure-monitor-app-hub.yml)
* [Web-apps met één pagina](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows-bureau blad-app](app/windows-desktop.md)
* [Andere platforms](app/platforms.md)

### <a name="is-it-free"></a>Is dit gratis?

Ja, voor experimenteel gebruik. In het Basic-prijs plan kan uw toepassing elke maand gratis een bepaalde hoeveelheid gegevens verzenden. De gratis limiet is groot genoeg voor ontwikkeling en het publiceren van een app voor een klein aantal gebruikers. U kunt een limiet instellen om te voor komen dat er meer dan een opgegeven hoeveelheid gegevens wordt verwerkt.

Grotere hoeveel heden telemetrie worden in rekening gebracht op basis van de GB. We bieden enkele tips over het [beperken van uw kosten](app/pricing.md).

In het Enter prise-plan worden kosten in rekening gebracht voor elke dag dat elk webserver knooppunt telemetrie verzendt. Het is geschikt als u doorlopend exporteren wilt gebruiken op een grote schaal.

[Lees het prijs plan](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Hoeveel kost het?

* Open de **pagina gebruik en geschatte kosten** in een Application Insights resource. Er is een grafiek van recent gebruik. Als u wilt, kunt u een limiet voor gegevens volumes instellen.
* Open de [Blade Azure-facturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) om uw rekeningen voor alle resources weer te geven.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Wat doet Application Insights wijzigen in mijn project?
De details zijn afhankelijk van het type project. Voor een webtoepassing:

* Voegt deze bestanden toe aan uw project:
  * ApplicationInsights.config
  * ai.js
* Installeert deze NuGet-pakketten:
  * *Application Insights-API* : de core-API
  * *Application Insights-API voor webtoepassingen* : wordt gebruikt voor het verzenden van telemetrie van de server
  * *Application Insights-API voor Java script-toepassingen* : wordt gebruikt voor het verzenden van telemetrie van de client
* De pakketten bevatten de volgende assembly's:
  * Micro soft. ApplicationInsights
  * Micro soft. ApplicationInsights. platform
* Hiermee worden items ingevoegd in:
  * Web.config
  * packages.config
* (Alleen nieuwe projecten: als u [Application Insights toevoegt aan een bestaand project][start], moet u dit hand matig doen.) Hiermee worden fragmenten ingevoegd in de client-en server code om ze te initialiseren met de Application Insights Resource-ID. In een MVC-app wordt bijvoorbeeld code ingevoegd in de basis pagina weergaven/gedeeld/ \_ Layout. cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hoe kan ik upgrade van oudere SDK-versies?
Zie de [release opmerkingen](app/release-notes.md) voor de SDK die geschikt is voor uw type toepassing.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Hoe kan ik wijzigen met welke Azure-resource mijn project gegevens verzendt?
Klik in Solution Explorer met de rechter muisknop `ApplicationInsights.config` en kies **Application Insights bijwerken**. U kunt de gegevens verzenden naar een bestaande of nieuwe resource in Azure. De update wizard wijzigt de instrumentatie sleutel in ApplicationInsights.config, wat bepaalt waar de server-SDK uw gegevens verzendt. Tenzij u ' Alles bijwerken ' uitschakelt, wordt ook de sleutel gewijzigd waar deze wordt weer gegeven op uw webpagina's.

### <a name="do-new-azure-regions-require-the-use-of-connection-strings"></a>Is het gebruik van verbindings reeksen vereist voor nieuwe Azure-regio's?

Nieuwe Azure-regio's **vereisen** het gebruik van verbindings reeksen in plaats van instrumentatie sleutels. Met de [verbindings reeks](./app/sdk-connection-string.md) wordt de resource geïdentificeerd waaraan u de telemetriegegevens wilt koppelen. U kunt ook de eind punten wijzigen die door de resource worden gebruikt als een bestemming voor uw telemetrie. U moet de connection string kopiëren en toevoegen aan de code van uw toepassing of aan een omgevings variabele.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>Kan ik gebruiken `providers('Microsoft.Insights', 'components').apiVersions[0]` in mijn Azure Resource Manager-implementaties?

Het is niet raadzaam om deze methode te gebruiken voor het invullen van de API-versie. De nieuwste versie kan Preview-releases vertegenwoordigen, die wijzigingen van de breuk kunnen bevatten. Zelfs met nieuwere niet-Preview versies zijn de API-versies niet altijd achterwaarts compatibel met bestaande sjablonen, of in sommige gevallen is de API-versie mogelijk niet voor alle abonnementen beschikbaar.

### <a name="what-is-status-monitor"></a>Wat is Status Monitor?

Een bureau blad-app die u kunt gebruiken op uw IIS-webserver om Application Insights te configureren in web-apps. Er wordt geen telemetrie verzameld: u kunt deze stoppen wanneer u geen app configureert. 

[Meer informatie](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Welke telemetrie wordt door Application Insights verzameld?

Van server web apps:

* HTTP-aanvragen
* [Afhankelijkheden](app/asp-net-dependencies.md). Aanroepen naar: SQL data bases; HTTP-aanroepen naar externe services; Azure Cosmos DB, tabel, Blob Storage en wachtrij. 
* [Uitzonde ringen](app/asp-net-exceptions.md) en stack traceringen.
* [Prestatie meter items](app/performance-counters.md) : als u [status monitor](app/monitor-performance-live-website-now.md)gebruikt, wordt [Azure monitoring voor app Services](app/azure-web-apps.md), [Azure monitoring voor VM of virtual machine Scale set](app/azure-vm-vmss-apps.md)of de [Application Insights verzamelde Writer](app/java-collectd.md).
* [Aangepaste gebeurtenissen en metrische gegevens](app/api-custom-events-metrics.md) die u codeert.
* [Traceer logboeken](app/asp-net-trace-logs.md) als u de juiste Collector configureert.

Van [client webpagina's](app/javascript.md):

* [Aantal paginaweergaven](app/usage-overview.md)
* [Ajax-aanroepen](app/asp-net-dependencies.md) Aanvragen die afkomstig zijn van een script dat wordt uitgevoerd.
* Laad gegevens pagina weergave
* Aantal gebruikers en sessies
* [Geverifieerde gebruikers-Id's](app/api-custom-events-metrics.md#authenticated-users)

Van andere bronnen, als u deze configureert:

* [Diagnostische gegevens van Azure](agents/diagnostics-extension-to-application-insights.md)
* [Importeren naar Analytics](logs/data-collector-api.md)
* [Log Analytics](logs/data-collector-api.md)
* [Logstash](logs/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kan ik een telemetrie uitfilteren of wijzigen?

Ja, op de server kunt u het volgende schrijven:

* Telemetrie-processor om eigenschappen te filteren of toe te voegen aan geselecteerde telemetriegegevens voordat ze vanuit uw app worden verzonden.
* De initialisatie functie voor telemetrie om eigenschappen toe te voegen aan alle items van telemetrie.

Meer informatie voor [ASP.net](app/api-filtering-sampling.md) of [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hoe worden plaats, land/regio en andere geografische locatie gegevens berekend?

We zoeken het IP-adres (IPv4 of IPv6) van de webclient met behulp van [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Browser-telemetrie: we verzamelen het IP-adres van de afzender.
* Server-telemetrie: de Application Insights-module verzamelt het client-IP-adres. Als is ingesteld, wordt deze niet verzameld `X-Forwarded-For` .
* Raadpleeg dit [artikel](./app/ip-collection.md)voor meer informatie over hoe IP-adres en geolocatie gegevens worden verzameld in Application Insights.

U kunt de configureren `ClientIpHeaderTelemetryInitializer` om het IP-adres van een andere header te halen. In sommige systemen wordt het bijvoorbeeld verplaatst met een proxy, load balancer of CDN naar `X-Originating-IP` . [Meer informatie](https://apmtips.com/posts/2016-07-05-client-ip-address/).

U kunt [Power bi gebruiken](app/export-power-bi.md ) om uw aanvraag-telemetrie weer te geven op een kaart.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Hoe lang worden gegevens in de portal bewaard? Is het veilig?
Bekijk de [gegevens retentie en privacy][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Wat gebeurt er met de telemetrie van Application Insight wanneer een server of apparaat geen verbinding meer met Azure heeft?

Al onze Sdk's, inclusief de Web-SDK, bevatten ' reliable Trans Port ' of ' Robust Trans Port '. Wanneer de server of het apparaat verbinding met Azure verliest, wordt telemetrie [lokaal opgeslagen op het bestands systeem](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (Server-sdk's) of in HTML5-sessie opslag (Web-SDK). De SDK zal regel matig proberen deze telemetrie te verzenden totdat de opname service de ' verouderde ' (48-uur voor de logboeken, 30 minuten voor metrische gegevens) beschouwt. Verlopen telemetrie wordt verwijderd. In sommige gevallen, bijvoorbeeld wanneer lokale opslag vol is, wordt er geen nieuwe poging gedaan.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Kunnen persoons gegevens in de telemetrie worden verzonden?

Dit is mogelijk als uw code dergelijke gegevens verzendt. Dit kan ook gebeuren als variabelen in stack-traceringen persoons gegevens bevatten. Uw ontwikkel team moet risico beoordelingen uitvoeren om ervoor te zorgen dat persoons gegevens op de juiste wijze worden afgehandeld. Meer [informatie over gegevens retentie en privacy](app/data-retention-privacy.md).

**Alle** octetten van het webadres van de client zijn altijd ingesteld op 0 nadat de geografische locatie kenmerken zijn opgezocht.

De [Application Insights java script SDK](app/javascript.md) bevat standaard geen persoonlijke gegevens in de automatisch aanvullen. Sommige persoons gegevens die in uw toepassing worden gebruikt, kunnen echter door de SDK worden opgenomen (bijvoorbeeld volledige namen in `window.title` of account-id's in xhr URL-query parameters). Voeg een [telemetrie-initialisatie functie](app/api-filtering-sampling.md#javascript-web-applications)toe voor aangepaste gegevens maskering voor de persoon.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Mijn instrumentatie sleutel is zichtbaar in de bron van de webpagina.

* Dit is gebruikelijk bij het controleren van oplossingen.
* Het kan niet worden gebruikt om uw gegevens te stelen.
* Het kan worden gebruikt om uw gegevens te scheef stellen of waarschuwingen te activeren.
* We hebben niet gehoord dat een klant dergelijke problemen heeft gehad.

U kunt de volgende stappen uitvoeren:

* Gebruik twee afzonderlijke instrumentatie sleutels (afzonderlijke Application Insights resources) voor client-en Server gegevens. of
* Schrijf een proxy die op uw server wordt uitgevoerd en laat de webclient gegevens verzenden via die proxy.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Hoe kan ik raadpleegt u POST gegevens in diagnostische Zoek opdrachten?
Er worden geen POST gegevens automatisch geregistreerd, maar u kunt een TrackTrace-aanroep gebruiken: plaats de gegevens in de bericht parameter. Dit heeft een maximale grootte die groter is dan de limieten voor teken reeks eigenschappen, maar u kunt er niet op filteren.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Moet ik een of meerdere Application Insights bronnen gebruiken?

Gebruik één resource voor alle onderdelen of rollen in één bedrijfs systeem. Gebruik afzonderlijke resources voor ontwikkelings-, test-en release versies en voor onafhankelijke toepassingen.

* [Bekijk de discussie hier](app/separate-resources.md)
* [Voor beeld-Cloud service met werk nemers en webrollen](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hoe kan ik de instrumentatie sleutel dynamisch wijzigen?

* [Discussie hier](app/separate-resources.md)
* [Voor beeld-Cloud service met werk nemers en webrollen](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Wat zijn de gebruikers-en sessie aantallen?

* De Java script-SDK stelt een gebruikers cookie op de webclient in om terugkerende gebruikers te identificeren en een sessie cookie om activiteiten te groeperen.
* Als er geen script aan de client zijde is, kunt u [cookies instellen op de server](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/).
* Als een echte gebruiker uw site in verschillende browsers gebruikt of als u in-private/incognito Browse of verschillende computers gebruikt, worden deze meerdere keren geteld.
* Als u een aangemelde gebruiker op verschillende computers en browsers wilt identificeren, voegt u een aanroep toe aan [setAuthenticatedUserContext ()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="how-does-application-insights-generate-device-information-browser-os-language-model"></a>Hoe Application Insights apparaatgegevens genereren (browser, besturings systeem, taal, model)?

De browser geeft de teken reeks van de gebruikers agent door in de HTTP-header van de aanvraag en de Application Insights opname service gebruikt [ua parser](https://github.com/ua-parser/uap-core) om de velden te genereren die u ziet in de gegevens tabellen en-functies. Als gevolg hiervan kunnen Application Insights gebruikers deze velden niet wijzigen.

Af en toe kunnen deze gegevens ontbreken of onjuist zijn als de gebruiker of de onderneming het verzenden van gebruikers agent in browser instellingen uitschakelt. Daarnaast mag de [regex-expressie voor UA-parsers](https://github.com/ua-parser/uap-core/blob/master/regexes.yaml) niet alle apparaatgegevens bevatten of kan Application Insights mogelijk de nieuwste updates niet hebben toegepast.

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Heb ik alles in Application Insights ingeschakeld?
| Wat u moet zien | Hoe kan ik het downloaden? | Waarom u wilt |
| --- | --- | --- |
| Beschikbaarheids grafieken |[Webtests](app/monitor-web-app-availability.md) |Weet u zeker dat uw web-app actief is |
| Server app-prestaties: reactie tijden,... |[Application Insights toevoegen aan uw project](app/asp-net.md) of [AI-status monitor op server installeren](app/monitor-performance-live-website-now.md) (of uw eigen code schrijven om [afhankelijkheden bij te houden](app/api-custom-events-metrics.md#trackdependency)) |Prestatie problemen detecteren |
| Telemetrie van afhankelijkheid |[AI-Status Monitor op server installeren](app/monitor-performance-live-website-now.md) |Problemen met data bases of andere externe onderdelen vaststellen |
| Stack traceringen ophalen van uitzonde ringen |[TrackException-aanroepen invoegen in uw code](app/asp-net-exceptions.md) (maar sommige worden automatisch gerapporteerd) |Uitzonde ringen detecteren en diagnosticeren |
| Logboek traceringen zoeken |[Een logboek registratie adapter toevoegen](app/asp-net-trace-logs.md) |Diagnose uitzonde ringen, prestatie problemen |
| Basis beginselen van client gebruik: pagina weergaven, sessies,... |[Java script-initialisatie functie in webpagina's](app/javascript.md) |Gebruiksanalyse |
| Aangepaste metrische gegevens van client |[Aanroepen bijhouden op webpagina's](app/api-custom-events-metrics.md) |Gebruikers ervaring verbeteren |
| Aangepaste metrische gegevens voor de server |[Tracerings aanroepen op server](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Waarom zijn de aantallen in zoek-en metrische grafieken niet gelijk?

Door [steek proeven](app/sampling.md) wordt het aantal telemetriegegevens (aanvragen, aangepaste gebeurtenissen, enzovoort) verminderd die daad werkelijk vanuit uw app naar de portal worden verzonden. In de zoek opdracht ziet u het aantal items dat daad werkelijk is ontvangen. In metrische grafieken die een aantal gebeurtenissen weer geven, ziet u het aantal oorspronkelijke gebeurtenissen dat heeft plaatsgevonden. 

Elk item dat wordt verzonden `itemCount` , bevat een eigenschap die laat zien hoeveel oorspronkelijke gebeurtenissen een item vertegenwoordigt. Als u de bemonsterings bewerking wilt observeren, kunt u deze query uitvoeren in Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```

### <a name="how-do-i-move-an-application-insights-resource-to-a-new-region"></a>Hoe kan ik een Application Insights resource verplaatsen naar een nieuwe regio?

Het verplaatsen van bestaande Application Insights resources van de ene naar de andere regio wordt **momenteel niet ondersteund**. Historische gegevens die u hebt verzameld, kunnen niet naar een nieuwe regio **worden gemigreerd** . De enige gedeeltelijke tijdelijke oplossing is het volgende:

1. Maak een gloed nieuwe Application Insights resource ([klassiek](app/create-new-resource.md) of [werk ruimte gebaseerd](./app/create-workspace-resource.md)) in de nieuwe regio.
2. Maak alle unieke aanpassingen die specifiek zijn voor de oorspronkelijke resource in de nieuwe resource opnieuw.
3. Wijzig uw toepassing voor gebruik van de nieuwe regio bron [instrumentatie sleutel](app/create-new-resource.md#copy-the-instrumentation-key) of [Connection String](app/sdk-connection-string.md).  
4. Test om te bevestigen dat alles naar verwachting werkt met uw nieuwe Application Insights-resource. 
5. Op dit moment kunt u de oorspronkelijke resource verwijderen, waardoor **alle historische gegevens verloren gaan**. Of behoud de oorspronkelijke resource voor historische rapportage doeleinden voor de duur van de instellingen voor het bewaren van gegevens.

Unieke aanpassingen die vaak hand matig moeten worden gemaakt of moeten worden bijgewerkt voor de resource in de nieuwe regio, zijn onder andere, maar zijn niet beperkt tot:

- Aangepaste Dash boards en werkmappen opnieuw maken. 
- Het bereik van aangepaste logboek-en metrische waarschuwingen opnieuw maken of bijwerken. 
- Beschikbaarheids waarschuwingen opnieuw maken.
- Maak alle aangepaste Azure-functies op basis van op rollen gebaseerde toegangs beheer (Azure RBAC) opnieuw die vereist zijn voor uw gebruikers om toegang te krijgen tot de nieuwe resource. 
- Replicatie-instellingen met betrekking tot opname steekproef, gegevens retentie, dagelijks Cap en aangepaste metrische gegevens activering. Deze instellingen worden bepaald via het deel venster **gebruik en geschatte kosten** .
- Een integratie die afhankelijk is van API-sleutels zoals [release annotaties](./app/annotations.md), [Live Metrics Channel Secure Control](app/live-stream.md#secure-the-control-channel) , enzovoort. U moet nieuwe API-sleutels genereren en de bijbehorende integratie bijwerken. 
- Continue export in klassieke resources moet opnieuw worden geconfigureerd.
- Diagnostische instellingen in op werk ruimte gebaseerde resources moeten opnieuw worden geconfigureerd.

> [!NOTE]
> Als de resource die u in een nieuwe regio maakt, een klassieke resource vervangt, raden we u aan om de voor delen van het [maken van een nieuwe resource op basis van een werk ruimte](app/create-workspace-resource.md) te verkennen of [om uw bestaande resource naar een andere werk ruimte te migreren](app/convert-classic-resource.md). 

### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Application Insights configureren

U kunt [Power shell-scripts](app/powershell.md) met Azure Broncontrole schrijven om het volgende te doen:

* Application Insights resources maken en bijwerken.
* Stel het prijs plan in.
* Haal de instrumentatie sleutel op.
* Een waarschuwing voor metrische gegevens toevoegen.
* Voeg een beschikbaarheids test toe.

U kunt geen metrisch Explorer-rapport instellen of continue export instellen.

#### <a name="querying-the-telemetry"></a>Query's uitvoeren op de telemetrie

Gebruik de [rest API](https://dev.applicationinsights.io/) om [Analytics](./logs/log-query-overview.md) -query's uit te voeren.

### <a name="how-can-i-set-an-alert-on-an-event"></a>Hoe kan ik een waarschuwing instellen voor een gebeurtenis?

Azure-waarschuwingen zijn alleen op metrische gegevens. Maak een aangepaste metriek die een drempel waarde voor waarden kruist wanneer uw gebeurtenis zich voordoet. Stel vervolgens een waarschuwing in voor de metrische gegevens. U ontvangt een melding wanneer de metriek de drempel waarde in een van beide richtingen overschrijdt. u krijgt pas een melding als de eerste doorhaling, ongeacht of de aanvankelijke waarde hoog of laag is. Er is altijd een latentie van een paar minuten.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Zijn er kosten voor gegevens overdracht tussen een Azure-web-app en Application Insights?

* Als uw Azure-web-app wordt gehost in een Data Center waar zich een Application Insights verzamelings eindpunt bevindt, worden er geen kosten in rekening gebracht. 
* Als er geen eind punt van een verzameling in uw host Data Center is, worden door de telemetrie van uw app [uitgaande Azure-kosten](https://azure.microsoft.com/pricing/details/bandwidth/)in rekening gebracht.

Dit is niet afhankelijk van waar uw Application Insights-bron wordt gehost. Het hangt gewoon af van de distributie van onze eind punten.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kan ik telemetrie verzenden naar de Application Insights Portal?

We raden u aan om onze Sdk's te gebruiken en de [SDK API](app/api-custom-events-metrics.md)te gebruiken. Er zijn varianten van de SDK voor verschillende [platforms](app/platforms.md). Deze Sdk's behandelen buffering, compressie, beperking, nieuwe pogingen, enzovoort. Het [opname schema](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) en het [eindpunt protocol](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) zijn echter openbaar.

### <a name="can-i-monitor-an-intranet-web-server"></a>Kan ik een intranet webserver bewaken?

Ja, maar u moet het verkeer naar onze services toestaan door Firewall-uitzonde ringen of proxy omleidingen.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Bekijk [hier](app/ip-addresses.md)onze volledige lijst met Services en IP-adressen.

#### <a name="firewall-exception"></a>Firewall-uitzonde ring

Hiermee staat u toe dat uw webserver telemetrie naar onze eind punten verzendt. 

#### <a name="gateway-redirect"></a>Gateway omleiden

Verkeer van uw server naar een gateway op uw intranet routeren door eind punten in uw configuratie te overschrijven. Als deze eigenschappen van het eind punt niet aanwezig zijn in uw configuratie, gebruiken deze klassen de standaard waarden die hieronder worden weer gegeven in het voor beeld ApplicationInsights.config. 

Uw gateway moet verkeer routeren naar het basis adres van het eind punt. Vervang in uw configuratie de standaard waarden door `http://<your.gateway.address>/<relative path>` .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Voor beeld ApplicationInsights.config met standaard eindpunten:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider is beschikbaar vanaf v 2.6.0.



#### <a name="proxy-passthrough"></a>Passthrough van proxy

Passthrough van de proxy kan worden bereikt door een computer niveau of proxy op toepassings niveau te configureren.
Zie voor meer informatie het artikel van DOTNET op [DefaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Voor beeld Web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kan ik beschik baarheid-webtests op een intranet server uitvoeren?

Onze [webtests](app/monitor-web-app-availability.md) worden uitgevoerd op de aanwezigheids punten die over de hele wereld worden gedistribueerd. Er zijn twee oplossingen:

* Firewall deur: Hiermee worden aanvragen van de server met [de lange en de lijst met webtest agents](app/ip-addresses.md)toegestaan.
* Schrijf uw eigen code voor het verzenden van periodieke aanvragen naar uw server vanuit uw intranet. U kunt Visual Studio-webtests voor dit doel uitvoeren. De tester kan de resultaten naar Application Insights verzenden met behulp van de API TrackAvailability ().

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Hoe lang duurt het voordat telemetrie is verzameld?

De meeste Application Insights gegevens hebben een latentie van minder dan vijf minuten. Sommige gegevens kunnen langer duren. meestal grotere logboek bestanden. Zie de [Application INSIGHTS Sla](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)voor meer informatie.



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>HTTP 502-en 503-antwoorden worden niet altijd vastgelegd door Application Insights

de fouten "502 ongeldige gateway" en "503-Service niet beschikbaar" worden niet altijd vastgelegd door Application Insights. Als er alleen Java script aan de client zijde wordt gebruikt voor bewaking, is dit gedrag te verwachten omdat de fout melding wordt geretourneerd vóór de pagina met de HTML-header met het Java script-fragment voor bewaking dat wordt weer gegeven. 

Als het 502-of 503-antwoord is verzonden vanaf een server waarop bewaking aan server zijde is ingeschakeld, worden de fouten verzameld door de Application Insights SDK. 

Er zijn echter nog steeds gevallen waarin zelfs wanneer bewaking aan server zijde is ingeschakeld op de webserver van een toepassing dat er geen 502-of 503-fout wordt vastgelegd door Application Insights. Veel moderne webservers staan niet toe dat een client rechtstreeks communiceert, maar maakt gebruik van oplossingen zoals omgekeerde proxy's om informatie weer te geven tussen de client en de front-end webservers. 

In dit scenario kan een 502-of 503-antwoord worden geretourneerd naar een client vanwege een probleem met de reverse proxy-laag. Dit zou niet out-of-Box worden vastgelegd door Application Insights. Als u problemen op deze laag wilt detecteren, moet u mogelijk Logboeken van uw reverse proxy naar Log Analytics door sturen en een aangepaste regel maken om te controleren op 502/503-antwoorden. Raadpleeg voor meer informatie over veelvoorkomende oorzaken van 502-en 503-fouten het artikel over Azure App Service het [oplossen van problemen met ' 502 bad gateway ' en ' 503-Service niet beschikbaar '](../app-service/troubleshoot-http-502-http-503.md).     


## <a name="opentelemetry"></a>OpenTelemetry

### <a name="what-is-opentelemetry"></a>Wat is OpenTelemetry

Een nieuwe open-source standaard voor de waarneembaarheid. Meer informatie vindt u in [https://opentelemetry.io/](https://opentelemetry.io/) .

### <a name="why-is-microsoft--azure-monitor-investing-in-opentelemetry"></a>Waarom wordt micro soft/Azure Monitor investeren in OpenTelemetry?

We zijn ervan overtuigd dat onze klanten voor drie redenen beter zijn:
   1. Schakel ondersteuning in voor meer klant scenario's.
   2. Instrument zonder bang te zijn voor het vergren delen van leveranciers.
   3. Verbeter de transparantie en betrokkenheid van klanten.

Het kan ook worden uitgelijnd met de strategie van micro soft om [open source](https://opensource.microsoft.com/)te kunnen inzetten.

### <a name="what-additional-value-does-opentelemetry-give-me"></a>Wat is de extra waarde van OpenTelemetry?

Naast de bovenstaande redenen is OpenTelemetry efficiënter op schaal en biedt het consistente ontwerp/configuratie in verschillende talen.

### <a name="how-can-i-test-out-opentelemetry"></a>Hoe kan ik OpenTelemetry testen?

Meld u aan om lid te worden van onze Azure Monitor Application Insights early adopter Community op [https://aka.ms/AzMonOtel](https://aka.ms/AzMonOtel) .

### <a name="what-does-ga-mean-in-the-context-of-opentelemetry"></a>Wat betekent dat in de context van OpenTelemetry?

De OpenTelemetry-Community definieert [hier](https://medium.com/opentelemetry/ga-planning-f0f6d7b5302)algemeen beschikbaar (ga). OpenTelemetry "GA" betekent echter niet de functie pariteit met de bestaande Application Insights Sdk's. Azure Monitor blijven onze huidige Application Insights Sdk's aanbevelen voor klanten die behoefte hebben aan functies zoals [vooraf geaggregeerde metrische gegevens](app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics), metrische [gegevens](app/live-stream.md)over [adaptieve steek proeven](app/sampling.md#adaptive-sampling), [Profiler](app/profiler-overview.md)en [snap shot debugger](app/snapshot-debugger.md) totdat de OpenTelemetry sdk's het functie-verval bereiken.

### <a name="can-i-use-preview-builds-in-production-environments"></a>Kan ik preview-builds gebruiken in productie omgevingen?

Het wordt niet aanbevolen. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) -voor beelden voor meer informatie.

### <a name="whats-the-difference-between-opentelemetry-sdk-and-auto-instrumentation"></a>Wat is het verschil tussen OpenTelemetry SDK en automatische instrumentatie?

De OpenTelemetry-specificatie definieert [SDK](https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/glossary.md#telemetry-sdk). Kortom: ' SDK ' is een taalspecifiek pakket dat telemetrie-gegevens verzamelt over de verschillende onderdelen van uw toepassing en de gegevens verzendt naar Azure Monitor via een exporteur.

Het concept van automatische instrumentatie (soms byte code injectie, codeloos of op basis van een agent genoemd) verwijst naar de mogelijkheid om uw toepassing te instrumenteren zonder uw code te wijzigen. Bekijk bijvoorbeeld het [Leesmij-bestand voor OpenTelemetry Java auto-Instrumentation](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/master/README.md) voor meer informatie.

### <a name="whats-the-opentelemetry-collector"></a>Wat is de OpenTelemetry-Collector?

De OpenTelemetry-collector wordt beschreven in het [github Leesmij-bestand](https://github.com/open-telemetry/opentelemetry-collector#opentelemetry-collector). Micro soft gebruikt momenteel niet de OpenTelemetry Collector en is afhankelijk van directe exporteurs die naar de Application Insights van de Azure Monitor verzenden.

### <a name="whats-the-difference-between-opencensus-and-opentelemetry"></a>Wat is het verschil tussen opentellingen en OpenTelemetry?

[Opentelling](https://opencensus.io/) is de precursor van [OpenTelemetry](https://opentelemetry.io/). Micro soft heeft open- [tracering](https://opentracing.io/) en opentellingen geholpen om OpenTelemetry te maken, een enkele waarneem bare standaard voor de wereld. De huidige voor [productie aanbevolen PYTHON SDK](app/opencensus-python.md) van Azure monitor is gebaseerd op opentellingen, maar uiteindelijk worden alle sdk's van alle Azure monitor gebaseerd op OpenTelemetry.


## <a name="container-insights"></a>Container inzichten

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Wat staan *andere processen* onder de knooppunt weergave?

**Andere processen** zijn bedoeld om u inzicht te geven in de hoofd oorzaak van het hoge resource gebruik op het knoop punt. Hierdoor kunt u het gebruik onderscheiden tussen processen in container en niet-container processen.

Wat zijn deze **andere processen**? 

Dit zijn niet-container processen die op uw knoop punt worden uitgevoerd.  

Hoe kunnen we dit berekenen?

**Andere processen**  =  *Totaal gebruik van CAdvisor*  -  *Gebruik van container proces*

De **andere processen** omvatten:

- Zelf-beheerde of beheerde Kubernetes-processen die geen container zijn 

- Run-time processen van de container  

- Kubelet  

- Systeem processen die worden uitgevoerd op uw knoop punt 

- Andere niet-Kubernetes workloads die worden uitgevoerd op de node-hardware of-VM 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Ik zie geen afbeeldings-en naam eigenschap waarden die worden ingevuld wanneer ik de tabel ContainerLog zoek.

Voor Agent versie ciprod12042019 en hoger worden deze twee eigenschappen standaard niet ingevuld voor elke logboek regel om de kosten te minimaliseren voor logboek gegevens die worden verzameld. Er zijn twee opties voor het opvragen van de tabel die deze eigenschappen bevat met hun waarden:

#### <a name="option-1"></a>Optie 1 

Neem deel aan andere tabellen om deze eigenschaps waarden in de resultaten op te nemen.

Wijzig uw query's zodat de eigenschappen van afbeeldingen en ImageTag worden toegevoegd aan de tabel door lid te worden van de ```ContainerInventory``` eigenschap ContainerID. U kunt de eigenschap name (zoals deze eerder in de tabel is weer gegeven) in het ```ContainerLog``` veld ContaineName van de KubepodInventory-tabel toevoegen door lid te worden van de eigenschap ContainerID. Dit is de aanbevolen optie.

In het volgende voor beeld wordt uitgelegd hoe u deze veld waarden ophaalt met samen voegingen.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Optie 2

Schakel de verzameling voor deze eigenschappen voor elke container logboek regel opnieuw in.

Als de eerste optie niet handig is als gevolg van wijzigingen in de query, kunt u het verzamelen van deze velden opnieuw inschakelen door de instelling ```log_collection_settings.enrich_container_logs``` in de configuratie-instellingen voor de [gegevens verzameling](containers/container-insights-agent-config.md)in te scha kelen.

> [!NOTE]
> De tweede optie wordt niet aanbevolen voor grote clusters met meer dan 50 knoop punten, omdat hiermee de API-server aanroepen van elk knoop punt in het cluster worden gegenereerd om deze verrijking uit te voeren. Met deze optie wordt ook de gegevens grootte voor elke verzamelde logboek regel verhoogd.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Kan ik de metrische gegevens weer geven die zijn verzameld in Grafana?

Container Insights ondersteunt het weer geven van metrische gegevens die zijn opgeslagen in uw Log Analytics-werk ruimte in Grafana-Dash boards. We hebben een sjabloon die u kunt downloaden uit de Grafana van het [dash board](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) van micro soft om aan de slag te gaan en te verwijzen naar informatie over het zoeken naar extra gegevens van uw bewaakte clusters om te visualiseren in aangepaste Grafana-Dash boards. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-container-insights"></a>Kan ik mijn AKS-engine-cluster bewaken met container Insights?

Container Insights ondersteunt bewakings werkbelastingen die zijn geïmplementeerd op de AKS-Engine (voorheen bekend als ACS-Engine) cluster (s) die worden gehost op Azure. Zie voor meer informatie en een overzicht van de stappen die vereist zijn voor het inschakelen van controle voor dit scenario, [container Insights gebruiken voor AKS-engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Waarom zie ik geen gegevens in mijn Log Analytics-werk ruimte?

Als u elke dag op een bepaalde tijd geen gegevens kunt zien in de Log Analytics-werkruimte, hebt u mogelijk de standaardlimiet van 500 MB bereikt, of de daglimiet die is opgegeven om de dagelijks verzamelde hoeveelheid gegevens in de hand te houden. Wanneer de limiet voor een dag wordt bereikt, stopt de gegevensverzameling en wordt pas de volgende dag weer hervat. Zie [logboek gegevens gebruiken en kosten](logs/manage-cost-storage.md)voor meer informatie over het controleren van uw gegevens gebruik en het bijwerken van een andere prijs categorie op basis van uw verwachte gebruiks patronen. 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Wat zijn de container statussen die zijn opgegeven in de tabel ContainerInventory?

De tabel ContainerInventory bevat informatie over zowel gestopte als actieve containers. De tabel wordt gevuld met een werk stroom in de agent die de docker doorzoekt voor alle containers (actief en gestopt), en stuurt die gegevens door naar de Log Analytics-werk ruimte.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hoe kan ik oplossen van *ontbrekende registratie* fout van abonnement?

Als u de fout melding **abonnements registratie voor micro soft. OperationsManagement** ontvangt, kunt u deze oplossen door de resource provider **micro soft. OperationsManagement** te registreren in het abonnement waarin de werk ruimte is gedefinieerd. De documentatie voor hoe u dit doet, vindt u [hier](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-kubernetes-rbac-enabled-aks-clusters"></a>Is er ondersteuning voor Kubernetes RBAC ingeschakelde AKS-clusters?

De container bewakings oplossing biedt geen ondersteuning voor Kubernetes RBAC, maar wordt wel ondersteund met container Insights. Op de pagina oplossings Details wordt mogelijk niet de juiste informatie weer gegeven op de Blades waarin de gegevens voor deze clusters worden weer gegeven.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hoe kan ik logboek verzameling voor containers in de uitvoeren-naam ruimte inschakelen via helm?

De logboek verzameling van containers in de uitvoeren-naam ruimte is standaard uitgeschakeld. Logboek verzameling kan worden ingeschakeld door een omgevings variabele in te stellen op de omsagent. Zie de pagina [container Insights](https://aka.ms/azuremonitor-containers-helm-chart) github voor meer informatie. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hoe kan ik de omsagent bij naar de meest recente versie?

Zie [agent beheer](containers/container-insights-manage-agent.md)voor meer informatie over het bijwerken van de agent.

### <a name="why-are-log-lines-larger-than-16kb-split-into-multiple-records-in-log-analytics"></a>Waarom worden logboek regels groter dan 16 KB gesplitst in meerdere records in Log Analytics?

De agent gebruikt het [stuur programma voor logboek registratie van json-bestanden van docker](https://docs.docker.com/config/containers/logging/json-file/) om de stdout en stderr van containers vast te leggen. Dit stuur programma voor logboek registratie splitst logboek regels die [groter zijn dan 16 KB](https://github.com/moby/moby/pull/22982) in meerdere regels wanneer ze van stdout of stderr naar een bestand worden gekopieerd.

### <a name="how-do-i-enable-multi-line-logging"></a>Hoe kan ik logboek registratie met meerdere regels inschakelen?

Momenteel biedt container Insights geen ondersteuning voor logboek registratie in meerdere regels, maar er zijn tijdelijke oplossingen beschikbaar. U kunt alle services zo configureren dat ze worden geschreven in JSON-indeling en vervolgens docker/Moby als één regel schrijft.

U kunt uw logboek bijvoorbeeld als een JSON-object laten teruglopen, zoals in het onderstaande voor beeld voor een voor beeld node.js toepassing wordt weer gegeven:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Deze gegevens zien eruit als in het volgende voor beeld in Azure Monitor voor Logboeken wanneer u er een query op uitvoert:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Raadpleeg de volgende [github-koppeling](https://github.com/moby/moby/issues/22920)voor een gedetailleerde weer gave van het probleem.

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hoe kan ik Azure AD-fouten oplossen wanneer ik live-logboeken inschakel? 

Mogelijk wordt de volgende fout weer **gegeven: de antwoord-URL die in de aanvraag is opgegeven, komt niet overeen met de antwoord-url's die zijn geconfigureerd voor de toepassing: ' <-toepassings-id \> '**. De oplossing voor het oplossen ervan vindt u in het artikel de [container gegevens in realtime weer geven met behulp van container Insights](containers/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Waarom kan ik het cluster niet upgraden na het onboarden?

Als u na het inschakelen van container Insights voor een AKS-cluster de Log Analytics-werk ruimte verwijdert waarnaar de gegevens worden verzonden bij het upgraden van het cluster, mislukt het cluster. U kunt dit probleem omzeilen door de bewaking uit te scha kelen en vervolgens weer in te scha kelen naar een andere geldige werk ruimte in uw abonnement. Wanneer u de cluster upgrade opnieuw probeert uit te voeren, moet het proces worden uitgevoerd en voltooid.  

### <a name="which-ports-and-domains-do-i-need-to-openallow-for-the-agent"></a>Welke poorten en domeinen moet ik openen/toestaan voor de agent?

Zie de [netwerk firewall vereisten](containers/container-insights-onboard.md#network-firewall-requirements) voor de proxy-en firewall configuratie-informatie die is vereist voor de container agent met Azure, Azure US Government en Azure China 21vianet-Clouds.


## <a name="vm-insights"></a>VM Insights

### <a name="can-i-onboard-to-an-existing-workspace"></a>Kan ik onboarding uitvoeren op een bestaande werk ruimte?
Als uw virtuele machines al zijn verbonden met een Log Analytics-werk ruimte, kunt u deze werk ruimte blijven gebruiken bij onboarding naar VM Insights, mits deze deel uitmaakt van een van de [ondersteunde regio's](vm/vminsights-configure-workspace.md#supported-regions).


### <a name="can-i-onboard-to-a-new-workspace"></a>Kan ik onboarding uitvoeren op een nieuwe werk ruimte? 
Als uw Vm's momenteel niet zijn verbonden met een bestaande Log Analytics-werk ruimte, moet u een nieuwe werk ruimte maken om uw gegevens op te slaan. Het maken van een nieuwe standaardwerk ruimte wordt automatisch uitgevoerd als u een enkele virtuele Azure-machine voor VM-inzichten configureert via de Azure Portal.

Als u ervoor kiest de op scripts gebaseerde methode te gebruiken, worden deze stappen behandeld in het artikel [VM-inzichten inschakelen met Azure PowerShell of Resource Manager-sjabloon](./vm/vminsights-enable-powershell.md) . 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Wat moet ik doen als mijn VM al rapporteert aan een bestaande werk ruimte?
Als u al gegevens van uw virtuele machines verzamelt, is het mogelijk dat u deze al hebt geconfigureerd om gegevens te rapporteren aan een bestaande Log Analytics-werk ruimte.  Als die werk ruimte zich in een van de ondersteunde regio's bevindt, kunt u VM Insights inschakelen voor die bestaande werk ruimte.  Als de werk ruimte die u al gebruikt, zich niet in een van de ondersteunde regio's bevindt, kunt u op dit moment geen gebruik meer maken van VM Insights.  We werken actief ter ondersteuning van extra regio's.


### <a name="why-did-my-vm-fail-to-onboard"></a>Waarom is mijn VM niet op de onboarding uitgevoerd?
Wanneer een virtuele machine van Azure wordt geboardd vanuit de Azure Portal, gebeurt het volgende:

* Er wordt een standaard Log Analytics werkruimte gemaakt als deze optie is geselecteerd.
* De Log Analytics-agent wordt op virtuele machines van Azure geïnstalleerd met behulp van een VM-extensie, indien nodig.  
* De virtuele machine van de Azure Insights-toewijzings agent is geïnstalleerd op virtuele machines met een extensie, indien deze is vereist. 

Tijdens het onboarding-proces wordt gecontroleerd op de status van elk van de bovenstaande om een meldings status te retour neren in de portal. De configuratie van de werk ruimte en de installatie van de agent duurt doorgaans 5 tot 10 minuten. Het weer geven van bewakings gegevens in de portal duurt 5 tot 10 minuten.  

Als u onboarding hebt gestart en berichten ziet met de melding dat de virtuele machine moet worden uitgevoerd, kunt u Maxi maal 30 minuten voor de virtuele machine het proces volt ooien. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Ik zie geen enkele of alle gegevens in de prestatie grafieken voor mijn VM
Onze prestatie grafieken zijn bijgewerkt voor het gebruik van gegevens die zijn opgeslagen in de tabel *InsightsMetrics* .  Als u gegevens in deze grafieken wilt weer geven, moet u een upgrade uitvoeren om de nieuwe VM Insights-oplossing te gebruiken.  Raadpleeg onze Ga naar de [Veelgestelde vragen](vm/vminsights-ga-release-faq.md) voor meer informatie.

Als u geen prestatie gegevens in de tabel schijf of in een aantal prestatie grafieken ziet, zijn de prestatie meter items mogelijk niet geconfigureerd in de werk ruimte. Voer het volgende [Power shell-script](./vm/vminsights-enable-powershell.md)uit om het probleem op te lossen.


### <a name="how-is-vm-insights-map-feature-different-from-service-map"></a>Wat is het verschil tussen de functie van de VM Insights-kaart en de Servicetoewijzing?
De functie voor het toewijzen van de VM-inzichten is gebaseerd op Servicetoewijzing, maar heeft de volgende verschillen:

* U kunt de kaart weergave openen via de VM-Blade en vanuit VM Insights onder Azure Monitor.
* De verbindingen in de kaart kunnen nu worden geklikt en weer geven van de verbindings gegevens in het deel venster voor de geselecteerde verbinding.
* Er is een nieuwe API die wordt gebruikt om de kaarten te maken voor betere ondersteuning van complexere kaarten.
* Bewaakte Vm's zijn nu opgenomen in het knoop punt client groep en het ring diagram toont het aandeel van de bewaakte en niet-bewaakte virtuele machines in de groep.  Het kan ook worden gebruikt voor het filteren van de lijst met computers wanneer de groep wordt uitgevouwen.
* Bewaakte virtuele machines zijn nu opgenomen in de server poort groep knoop punten en het ring diagram toont het aandeel van de bewaakte en niet-bewaakte computers in de groep.  Het kan ook worden gebruikt voor het filteren van de lijst met computers wanneer de groep wordt uitgevouwen.
* De kaart stijl is bijgewerkt zodat deze consistent is met de app-toewijzing van Application Insights.
* De side panels zijn bijgewerkt en hebben niet de volledige set integraties die worden ondersteund in Servicetoewijzing-Updatebeheer, Wijzigingen bijhouden, beveiliging en Service Desk. 
* De optie voor het kiezen van groepen en machines die u wilt toewijzen, is bijgewerkt en ondersteunt nu abonnementen, resource groepen, Azure-schaal sets voor virtuele machines en Cloud Services.
* U kunt geen nieuwe Servicetoewijzing machine groepen maken in de functie van de VM Insights-kaart.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Waarom worden stippel lijnen weer gegeven in mijn Performance grafieken?
Dit kan een paar oorzaken hebben.  Als er sprake is van een onderbreking in het verzamelen van gegevens, worden de lijnen weer gegeven als gestippeld.  Als u de gegevens sampling frequentie hebt gewijzigd voor de prestatie meter items ingeschakeld (de standaard instelling is het verzamelen van gegevens elke 60 seconden), u kunt stippel lijnen in de grafiek zien als u een smalle periode voor de grafiek kiest en de sampling frequentie kleiner is dan de Bucket grootte die in de grafiek wordt gebruikt (bijvoorbeeld als de sampling frequentie elke 10 minuten is en elke Bucket in het diagram 5 minuten is).  Als u een breder tijds bereik kiest voor weer gave, moeten de grafiek lijnen worden weer gegeven als ononderbroken lijnen in plaats van punten in dit geval.

### <a name="are-groups-supported-with-vm-insights"></a>Worden groepen ondersteund met VM Insights?
Ja, nadat u de afhankelijkheids agent hebt geïnstalleerd, verzamelen we informatie van de virtuele machines om groepen weer te geven op basis van het abonnement, de resource groep, de schaal sets voor virtuele machines en Cloud Services.  Als u Servicetoewijzing hebt gebruikt en computer groepen hebt gemaakt, worden deze ook weer gegeven.  Computer groepen worden ook weer gegeven in het filter groepen als u deze hebt gemaakt voor de werk ruimte die u bekijkt. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hoe kan ik raadpleegt u de details van wat is de 95e percentiel regel in de samengevoegde prestatie grafieken?
Standaard wordt de lijst gesorteerd om u de virtuele machines weer te geven die de hoogste waarde voor het 95e percentiel hebben voor de geselecteerde metriek, met uitzonde ring van de beschik bare geheugen grafiek, waarin de computers met de laagste waarde van het vijfde percentiel worden weer gegeven.  Als u op de grafiek klikt, wordt de **bovenste N lijst**  weergave geopend met de juiste metriek geselecteerd.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hoe verwerken de kaart functie dubbele IP-adressen over verschillende vnets en subnetten?
Als u IP-bereiken dupliceert met Vm's of virtuele-machine schaal sets van Azure in subnetten en vnets, kan dit ertoe leiden dat de toewijzing van de VM-inzichten onjuiste gegevens weergeeft. Dit is een bekend probleem en we onderzoeken de opties om deze ervaring te verbeteren.

### <a name="does-map-feature-support-ipv6"></a>Ondersteunt de functie voor het toewijzen van IPv6?
De toewijzings functie ondersteunt momenteel alleen IPv4 en we onderzoeken de ondersteuning voor IPv6. We bieden ook ondersteuning voor IPv4 die in IPv6 is getunneld.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Wanneer ik een kaart voor een resource groep of een andere grote groep laad, kan de kaart moeilijk worden weer gegeven
We hebben verbeteringen aangebracht in de toewijzing van het verwerken van grote en complexe configuraties, maar het is ook mogelijk dat een kaart een groot aantal knoop punten, verbindingen en een knoop punt als een cluster kan hebben.  We streven ernaar om ondersteuning te blijven bieden om de schaal baarheid te verbeteren.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Waarom ziet de netwerk grafiek op het tabblad prestaties er anders uit dan de netwerk grafiek op de overzichts pagina van de Azure VM?

Op de overzichts pagina voor een Azure-VM worden grafieken weer gegeven op basis van de meting van de activiteit van de host in de gast-VM.  Voor de netwerk grafiek in het overzicht van de Azure-VM wordt alleen het netwerk verkeer weer gegeven dat wordt gefactureerd.  Dit omvat niet het verkeer tussen virtuele netwerken.  De gegevens en grafieken die worden weer gegeven voor de generatie van vm's, zijn gebaseerd op gegevens van de gast-VM en de netwerk grafiek bevat alle TCP/IP-verkeer dat binnenkomend en uitgaand is voor die VM, inclusief intervirtueel netwerk.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hoe wordt de reactie tijd gemeten voor gegevens die zijn opgeslagen in VMConnection en worden weer gegeven in het deel venster verbinding en in werkmappen?

De reactie tijd is een benadering. Omdat we de code van de toepassing niet instrumenteren, weten we niet echt wanneer een aanvraag begint en wanneer het antwoord wordt ontvangen. In plaats daarvan zien we dat er gegevens worden verzonden via een verbinding en dat de gegevens vervolgens weer worden teruggestuurd op deze verbinding. Onze agent houdt deze verzenden en ontvangen en probeert deze te koppelen: een volg orde van verzen dingen, gevolgd door een reeks ontvangen, wordt geïnterpreteerd als een aanvraag/antwoord-paar. De tijds duur tussen deze bewerkingen is de reactie tijd. Deze bevat de netwerk latentie en de verwerkings tijd van de server.

Deze benadering werkt goed voor protocollen die op aanvraag/antwoord zijn gebaseerd: een enkele aanvraag verloopt op de verbinding en er wordt één antwoord ontvangen. Dit is het geval voor HTTP (S) (zonder pipeline), maar niet aan andere protocollen is voldaan.

### <a name="are-there-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Zijn er beperkingen als ik aan het gratis prijs plan van Log Analytics?
Als u Azure Monitor hebt geconfigureerd met een Log Analytics-werk ruimte met behulp van de gratis prijs categorie, ondersteunt de functie voor het *maken* van de VM Insights-map alleen vijf verbonden computers die zijn verbonden met de werk ruimte. Als u vijf Vm's hebt verbonden met een gratis werk ruimte, verbreekt u de verbinding van een van de virtuele machines en maakt u later een nieuwe virtuele machine, de nieuwe virtuele machine wordt niet bewaakt en wordt niet weer gegeven op de pagina overzicht.  

Onder dit voor waarde wordt u gevraagd de optie **nu proberen** te kiezen wanneer u de virtuele machine opent en **inzichten** selecteert in het linkerdeel venster, zelfs nadat het al op de virtuele machine is geïnstalleerd.  U wordt echter niet gevraagd naar opties die normaal gesp roken optreden als deze virtuele machine niet is onboarding voor VM Insights. 

## <a name="sql-insights-preview"></a>SQL Insights (preview-versie)

### <a name="what-versions-of-sql-server-are-supported"></a>Welke versies van SQL Server worden ondersteund?
We ondersteunen SQL Server 2012 en alle nieuwere versies. Zie [ondersteunde versies](insights/sql-insights-overview.md#supported-versions) voor meer informatie.

### <a name="what-sql-resource-types-are-supported"></a>Welke SQL-resource typen worden ondersteund?
- Azure SQL Database
- Azure SQL Managed Instance
- SQL Server op Azure Virtual Machines (SQL Server uitgevoerd op virtuele machines die zijn geregistreerd bij de [SQL virtual machine](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) provider)
- Azure-Vm's (SQL Server die worden uitgevoerd op virtuele machines die niet zijn geregistreerd bij de SQL-provider voor [virtuele](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) machines)

Zie [ondersteunde versies](insights/sql-insights-overview.md#supported-versions) voor meer informatie en voor meer informatie over scenario's zonder ondersteuning of beperkte ondersteuning.

### <a name="what-operating-systems-for-the-virtual-machine-running-sql-server-are-supported"></a>Welke besturings systemen voor de virtuele machine met SQL Server worden ondersteund?
We ondersteunen alle besturings systemen die zijn opgegeven door de [Windows](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms) -en [Linux](../azure-sql/virtual-machines/linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create) -documentatie voor SQL Server op Azure virtual machines.

### <a name="what-operating-system-for-the-monitoring-virtual-machine-are-supported"></a>Welk besturings systeem voor de virtuele bewakings machine wordt ondersteund?
Ubuntu 18,04 is momenteel het enige besturings systeem dat wordt ondersteund voor de bewaking van de virtuele machine.

### <a name="where-will-the-monitoring-data-be-stored-in-log-analytics"></a>Waar worden de bewakings gegevens opgeslagen in Log Analytics?
Alle bewakings gegevens worden opgeslagen in de tabel **InsightsMetrics** . De kolom **oorsprong** bevat de waarde `solutions.azm.ms/telegraf/SqlInsights` . De kolom **naam ruimte** heeft waarden die beginnen met `sqlserver_` .

### <a name="how-often-is-data-collected"></a>Hoe vaak worden gegevens verzameld? 
De frequentie van het verzamelen van gegevens is aanpasbaar. Zie [gegevens die worden verzameld door SQL Insights](../insights/../azure-monitor/insights/sql-insights-overview.md#data-collected-by-sql-insights) voor meer informatie over de standaard frequenties en Zie [SQL-bewakings profiel maken](../insights/../azure-monitor/insights/sql-insights-enable.md#create-sql-monitoring-profile) voor instructies over het aanpassen van frequenties. 

## <a name="next-steps"></a>Volgende stappen
Als uw vraag hier niet wordt beantwoord, kunt u de volgende forums raadplegen voor aanvullende vragen en antwoorden.

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

Voor algemene feedback over Azure Monitor gaat u naar het [Feedback forum](https://feedback.azure.com/forums/34192--general-feedback).
