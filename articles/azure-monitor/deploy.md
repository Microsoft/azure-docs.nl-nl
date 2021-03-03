---
title: Azure Monitor implementeren
description: Beschrijft de verschillende stappen die vereist zijn voor een volledige implementatie van Azure Monitor voor het bewaken van alle resources in uw Azure-abonnement.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: f5855d7ab1f7ba8e11334f1373fb10166f47003a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708251"
---
# <a name="deploy-azure-monitor"></a>Azure Monitor implementeren
Het inschakelen van Azure Monitor voor het bewaken van al uw Azure-resources is een combi natie van het configureren van Azure Monitor onderdelen en het configureren van Azure-resources voor het genereren van bewakings gegevens voor Azure Monitor voor het verzamelen van In dit artikel worden de verschillende stappen beschreven die nodig zijn voor een volledige implementatie van Azure Monitor met behulp van een gemeen schappelijke configuratie om alle resources in uw Azure-abonnement te controleren. De basis beschrijvingen voor elke stap worden verstrekt met koppelingen naar andere documentatie voor gedetailleerde configuratie vereisten.

> [!IMPORTANT]
> De functies van Azure Monitor en hun configuratie variëren, afhankelijk van uw bedrijfs vereisten, in overeenstemming met de kosten van de ingeschakelde functies. In elke stap hieronder wordt aangegeven of er potentiële kosten zijn en moet u deze kosten evalueren voordat u verdergaat met deze stap. Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor gedetailleerde prijs informatie.

## <a name="configuration-goals"></a>Configuratie doelen
Het doel van een volledige implementatie van Azure Monitor is het verzamelen van alle beschik bare gegevens van al uw cloud resources en-toepassingen en zo veel mogelijk functies in Azure Monitor op basis van die gegevens.

De gegevens die door Azure Monitor worden verzameld, worden verzonden naar [Azure monitor metrieken](essentials/data-platform-metrics.md) of [Azure monitor logboeken](logs/data-platform-logs.md). Er worden verschillende soorten gegevens opgeslagen en verschillende soorten analyse en waarschuwingen. Zie [Azure monitor metrische gegevens en logboeken vergelijken](data-platform.md) voor een vergelijking van de twee en [overzicht van waarschuwingen in Microsoft Azure](alerts/alerts-overview.md) voor een beschrijving van verschillende waarschuwings typen. 

Sommige gegevens kunnen worden verzonden naar zowel metrieken als Logboeken om gebruik te maken van verschillende functies. In dergelijke gevallen moet u deze mogelijk afzonderlijk configureren. Metrische gegevens worden bijvoorbeeld automatisch verzonden door Azure-resources naar metrieken, die ondersteuning bieden voor Metrics Explorer en metrische waarschuwingen. U moet een diagnostische instelling voor elke resource maken om dezelfde metrische gegevens naar Logboeken te verzenden, waarmee u prestatie trends met andere logboek gegevens kunt analyseren met behulp van Log Analytics. In de volgende secties wordt aangegeven waar gegevens worden verzonden en wordt elke stap opgenomen die nodig is om gegevens te verzenden naar alle mogelijke locaties.

Mogelijk hebt u aanvullende vereisten, zoals het bewaken van resources buiten Azure en het verzenden van gegevens buiten Azure Monitor. Vereisten zoals deze kunnen worden bereikt met aanvullende configuratie van de functies die in dit artikel worden beschreven. Volg de koppelingen naar documentatie in elke stap voor aanvullende configuratie opties.

## <a name="collect-data-from-azure-resources"></a>Gegevens verzamelen van Azure-resources

> [!NOTE]
> Zie [Azure-resources bewaken met Azure monitor](essentials/monitor-azure-resource.md) voor een volledige hand leiding voor het bewaken van virtuele machines met Azure monitor.

Sommige bewakings activiteiten van Azure-resources zijn automatisch beschikbaar zonder configuratie vereist, terwijl u configuratie stappen moet uitvoeren om extra bewakings gegevens te verzamelen. In de volgende tabel ziet u de configuratie stappen die nodig zijn om alle beschik bare gegevens van uw Azure-resources te verzamelen, waaronder de stappen die worden verzonden naar Azure Monitor metrische gegevens en Azure Monitor Logboeken. In de volgende secties wordt elke stap in meer detail beschreven.

[![Azure-resource bewaking ](media/deploy/deploy-azure-resources.png) implementeren](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>Geen configuratie
De volgende functies van Azure Monitor zijn ingeschakeld zonder configuratie vereist wanneer u een Azure-abonnement maakt. Er zijn geen kosten verbonden aan deze bewaking.

[Azure Active Directory-logboeken](../active-directory/reports-monitoring/overview-reports.md) : bevat geschiedenis van het Tenant niveau van aanmeldings activiteit en controle spoor van wijzigingen die zijn aangebracht in azure Active Directory. Zie [controle activiteiten rapporten in de](../active-directory/reports-monitoring/concept-audit-logs.md) rapporten van de Azure Active Directory Portal en [aanmeldings activiteiten in de Azure Active Directory-Portal](../active-directory/reports-monitoring/concept-sign-ins.md) voor meer informatie over de Azure Active Directory logboeken en hoe u deze kunt weer geven in de Azure Portal.

[Activiteiten logboek](essentials/platform-logs-overview.md) : biedt inzicht in gebeurtenissen van beheer groepen en op abonnements niveau die in azure hebben plaatsgevonden. Gebeurtenissen worden automatisch naar het activiteiten logboek geschreven wanneer u een nieuwe Azure-resource maakt, een resource wijzigt of een belang rijke activiteit uitvoert. U kunt gebeurtenissen weer geven in de Azure Portal en waarschuwingen in het activiteiten logboek maken wanneer bepaalde gebeurtenissen worden gemaakt. Zie [Azure-activiteiten logboek](essentials/activity-log.md) voor meer informatie over het activiteiten logboek en hoe u het kunt weer geven in de Azure Portal.

[Metrische platform gegevens](essentials/metrics-supported.md) : worden automatisch verzameld van Azure-services naar [Azure monitor metrische gegevens](essentials/data-platform-metrics.md). Deze gegevens worden vaak weer gegeven op de pagina **overzicht** in de Azure portal voor verschillende services. Zie [aan de slag met Azure Metrics Explorer](essentials/metrics-getting-started.md) voor meer informatie over het analyseren van platform metrieken in de Azure Portal. 


### <a name="create-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken
U hebt ten minste één Log Analytics-werk ruimte nodig om [Azure monitor-logboeken](logs/data-platform-logs.md)in te scha kelen. Dit is vereist voor het verzamelen van gegevens als logboeken van Azure-resources, het verzamelen van gegevens van het gast besturingssysteem van Azure virtual machines en voor de meeste Azure monitor inzichten. Andere services, zoals Azure Sentinel en Azure Security Center, maken ook gebruik van een Log Analytics-werk ruimte en kunnen delen die u gebruikt voor Azure Monitor. U kunt beginnen met één werk ruimte om deze controle te ondersteunen, maar Zie  [de implementatie van uw Azure monitor-logboeken ontwerpen](logs/design-logs-deployment.md) voor hulp bij het gebruik van meerdere werk ruimten.

Er zijn geen kosten verbonden aan het maken van een Log Analytics-werk ruimte, maar er kunnen kosten in rekening worden gebracht zodra u de gegevens hebt geconfigureerd die u wilt verzamelen. Zie [gebruik en kosten beheren met Azure monitor logboeken](logs/manage-cost-storage.md) voor meer informatie.  

Zie [een log Analytics-werk ruimte maken in de Azure Portal](logs/quick-create-workspace.md) om een eerste log Analytics-werk ruimte te maken. Zie [toegang beheren tot logboek gegevens en-werk ruimten in azure monitor](logs/manage-access.md) om de toegang te configureren. 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>Diagnostische instelling maken om Tenant-en abonnements logboeken te verzamelen
Hoewel de [Azure Active Directory logboeken](../active-directory/reports-monitoring/overview-reports.md) voor uw Tenant en het [activiteiten logboek](essentials/platform-logs-overview.md) voor uw abonnement automatisch worden verzameld, kunt u deze gebeurtenissen met andere logboek gegevens met behulp van logboek query's in Log Analytics analyseren met een log Analytics werk ruimte. Hierdoor kunt u ook waarschuwingen voor logboek query's maken. Dit is de enige manier om te waarschuwen voor Azure Active Directory logboeken en complexere logica te bieden dan waarschuwingen in het activiteiten logboek.

Er zijn geen kosten voor het verzenden van het activiteiten logboek naar een werk ruimte, maar er zijn een gegevens opname-en retentie voor de logboeken van Azure Active Directory. 

Zie [Azure AD-logboeken integreren met Azure monitor-logboeken](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) en [Diagnostische instellingen maken voor het verzenden van platform logboeken en-metrische gegevens naar verschillende bestemmingen](essentials/diagnostic-settings.md) om een diagnostische instelling voor uw Tenant en abonnement te maken voor het verzenden van logboek vermeldingen naar uw log Analytics-werk ruimte. 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>Diagnostische instelling maken voor het verzamelen van bron logboeken en platform metrieken
Resources in azure genereren automatisch [resource logboeken](essentials/platform-logs-overview.md) met details van bewerkingen die in de resource worden uitgevoerd. In tegens telling tot platform metrieken moet u bron logboeken configureren om te worden verzameld. Een diagnostische instelling maken om deze te verzenden naar een Log Analytics-werk ruimte om ze te combi neren met de andere gegevens die worden gebruikt met Azure Monitor-Logboeken. Dezelfde diagnostische instelling kan worden gebruikt om ook de platform metrieken voor de meeste resources naar dezelfde werk ruimte te verzenden, waarmee u metrische gegevens kunt analyseren met behulp van logboek query's met andere verzamelde gegevens.

Er zijn kosten verbonden aan deze verzameling. Raadpleeg [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/) voordat u een groot aantal resources implementeert. Zie ook het [gebruik en de kosten beheren met Azure monitor logboeken](logs/manage-cost-storage.md) voor meer informatie over het optimaliseren van de kosten van uw logboek verzameling.

Zie [Diagnostische instelling maken voor het verzamelen van resource logboeken en metrische gegevens in azure](essentials/diagnostic-settings.md#create-in-azure-portal) om een diagnostische instelling voor een Azure-resource te maken. Omdat voor elke Azure-resource een diagnostische instelling moet worden gemaakt, raadpleegt u [Azure monitor op schaal implementeren](deploy-scale.md) voor meer informatie over het gebruik van [Azure-beleid](../governance/policy/overview.md) , zodat de instellingen automatisch worden gemaakt telkens wanneer een Azure-resource wordt gemaakt.

### <a name="enable-insights-and-solutions"></a>Inzichten en oplossingen inschakelen
Inzichten en oplossingen bieden gespecialiseerde bewaking voor een bepaalde service of oplossing. Inzichten gebruiken meer recente functies van Azure Monitor, zoals werkmappen, zodat u een inzicht kunt krijgen als deze beschikbaar is voor uw service. Ze zijn automatisch beschikbaar in elk Azure-abonnement, maar er is een configuratie vereist voor de volledige functionaliteit. Normaal gesp roken gebruiken ze platform metrieken en bronnen logboeken die u eerder hebt geconfigureerd en kunnen er aanvullende gegevens worden verzameld.

Oplossingen moeten worden toegevoegd aan elk abonnement en alleen worden gebruikt met gegevens uit Azure Monitor logboeken en kunnen extra logboek gegevens verzamelen.

Er zijn geen kosten voor oplossingen of inzichten, maar u kunt wel betalen voor de gegevens die ze verzamelen.

Zie [wat wordt bewaakt door Azure monitor?](monitor-reference.md) voor een lijst met beschik bare inzichten en oplossingen in azure monitor. Raadpleeg de documentatie voor elk van de unieke configuratie-en prijs informatie. 

## <a name="collect-data-from-virtual-machines"></a>Gegevens verzamelen van virtuele machines

> [!NOTE]
> Zie [Azure virtual machines bewaken met Azure monitor](vm/monitor-vm-azure.md) voor een volledige hand leiding voor het bewaken van virtuele machines met Azure monitor. 

Virtuele machines genereren soort gelijke gegevens als andere Azure-resources, maar u hebt een agent nodig voor het verzamelen van gegevens van het gast besturingssysteem. Zie [overzicht van Azure monitor agents](agents/agents-overview.md) voor een vergelijking van de agents die worden gebruikt door Azure monitor. 

[VM Insights](vm/vminsights-overview.md) maakt gebruik van de log Analytics agent en de afhankelijkheids agent voor het verzamelen van gegevens van het gast besturingssysteem van virtuele machines, zodat u deze agents kunt implementeren als onderdeel van de implementatie van dit inzicht. Hiermee wordt de Log Analytics-agent ingeschakeld voor andere services die deze gebruiken, zoals Azure Security Center.


[![Azure VM ](media/deploy/deploy-azure-vm.png) implementeren](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-vm-insights"></a>Werk ruimte configureren voor VM Insights
Voor VM-inzichten is een Log Analytics-werk ruimte vereist die normaal gesp roken hetzelfde is als het item dat is gemaakt voor het verzamelen van gegevens van andere Azure-resources. Voordat u virtuele machines inschakelt, moet u de oplossing toevoegen die vereist is voor de VM-inzichten in de werk ruimte.

Zie [log Analytics werkruimte configureren voor VM Insights](vm/vminsights-configure-workspace.md) voor meer informatie over het configureren van uw log Analytics-werk ruimte voor VM Insights.

### <a name="enable-vm-insights-on-each-virtual-machine"></a>VM Insights op elke virtuele machine inschakelen
Zodra een werk ruimte is geconfigureerd, kunt u elke virtuele machine inschakelen door de Log Analytics agent en de afhankelijkheids agent te installeren. Er zijn meerdere methoden om deze agents te installeren, met inbegrip van Azure Policy waarmee u elke virtuele machine automatisch kunt configureren wanneer deze wordt gemaakt. Prestatie gegevens en proces gegevens die door VM Insights worden verzameld, worden opgeslagen in Azure Monitor Logboeken.

Zie [overzicht van VM Insights inschakelen](vm/vminsights-enable-overview.md) voor opties om de agents op uw virtuele machines te implementeren en deze in te scha kelen voor bewaking.

### <a name="configure-workspace-to-collect-events"></a>Werk ruimte configureren voor het verzamelen van gebeurtenissen
Met VM Insights worden prestatie gegevens en de details en afhankelijkheden van processen verzameld van het gast besturingssysteem van elke virtuele machine. De Log Analytics-agent kan ook logboeken van de gast verzamelen, met inbegrip van het gebeurtenis logboek van Windows en syslog van Linux. De configuratie voor deze logboeken wordt opgehaald uit de Log Analytics werk ruimte waarmee deze is verbonden. U hoeft de werk ruimte slechts eenmaal te configureren en telkens wanneer een agent verbinding maakt, worden eventuele configuratie wijzigingen gedownload. 

Raadpleeg [agent-gegevens bronnen in azure monitor](agents/agent-data-sources.md) voor meer informatie over het configureren van uw log Analytics-werk ruimte voor het verzamelen van aanvullende gegevens van de virtuele machines van de agent.

> [!NOTE]
> U kunt de werk ruimte ook configureren voor het verzamelen van prestatie meter items, maar dit is waarschijnlijk redundant met de prestatie gegevens die worden verzameld door VM Insights. De prestatie gegevens die door de werk ruimte worden verzameld, worden opgeslagen in de *prestatie* tabel en de prestatie gegevens die door de VM-inzichten worden verzameld, worden opgeslagen in de tabel *InsightsMetrics* . Configureer de prestatie verzameling alleen in de werk ruimte als u prestatie meter items nodig hebt die niet al zijn verzameld door VM Insights.

### <a name="diagnostic-extension-and-telegraf-agent"></a>Diagnostische uitbrei ding en telegrafie agent
VM Insights maakt gebruik van de Log Analytics-agent waarmee prestatie gegevens worden verzonden naar een Log Analytics-werk ruimte, maar niet op Azure Monitor meet waarden. Als u deze gegevens naar metrieken verzendt, kan deze worden geanalyseerd met Metrics Explorer en worden gebruikt met metrische waarschuwingen. Hiervoor is de diagnostische uitbrei ding voor Windows en de Telegraf-agent in Linux vereist.

Zie de [Windows Azure Diagnostics-extensie (WAD) installeren en configureren](agents/diagnostics-extension-windows-install.md) en [aangepaste metrische gegevens voor een virtuele Linux-machine met de InfluxData-telegrafie-agent verzamelen](essentials/collect-custom-metrics-linux-telegraf.md) voor meer informatie over het installeren en configureren van deze agents.


## <a name="monitor-applications"></a>Toepassingen bewaken
Azure Monitor bewaakt uw aangepaste toepassingen met behulp van [Application Insights](app/app-insights-overview.md), die u moet configureren voor elke toepassing die u wilt bewaken. Het configuratie proces verschilt, afhankelijk van het type toepassing dat wordt bewaakt en het type bewaking dat u wilt uitvoeren. Gegevens die door Application Insights worden verzameld, worden opgeslagen in Azure Monitor metrieken, Azure Monitor logboeken en Azure Blob-opslag, afhankelijk van de functie. Prestatie gegevens worden opgeslagen in zowel Azure Monitor metrieken als Azure Monitor logboeken zonder dat er aanvullende configuratie is vereist.

### <a name="create-an-application-resource"></a>Een toepassings bron maken
U moet een resource in Application Insights maken voor elke toepassing die u wilt bewaken. Logboek gegevens die door Application Insights worden verzameld, worden opgeslagen in Azure Monitor logboeken voor een toepassing op basis van een werk ruimte. Logboek gegevens voor klassieke toepassingen worden gescheiden van uw Log Analytics-werk ruimte opgeslagen, zoals beschreven in de [gegevens structuur](logs/data-platform-logs.md#data-structure).

 Wanneer u de toepassing maakt, moet u selecteren of u klassiek of op werk ruimte gebaseerde wilt gebruiken. Zie [een Application Insights resource maken](app/create-new-resource.md) om een klassieke toepassing te maken. Zie [Application Insights resources op basis van een werk ruimte (preview)](app/create-workspace-resource.md) om een op werk ruimte gebaseerde toepassing te maken.

### <a name="configure-codeless-or-code-based-monitoring"></a>Op code of op code gebaseerde bewaking configureren
Als u controle wilt inschakelen voor een toepassing, moet u beslissen of u codeloos of op code gebaseerde bewaking wilt gebruiken. Het configuratie proces varieert afhankelijk van dit besluit en het type toepassing dat u wilt bewaken.

**Bewaking op code** is het eenvoudigst te implementeren en kan na de code ontwikkeling worden geconfigureerd. Er zijn geen updates nodig voor uw code. Raadpleeg de volgende bronnen voor meer informatie over het inschakelen van bewaking, afhankelijk van de toepassing.

- [Toepassingen die worden gehost op Azure Web Apps](app/azure-web-apps.md)
- [Java-toepassingen](app/java-in-process-agent.md)
- [ASP.NET-toepassingen die worden gehost in IIS op een Azure VM of een virtuele-machineschaalset van Azure](app/azure-vm-vmss-apps.md)
- [ASP.NET-toepassingen die worden gehost in IIS op on-premises VM](app/monitor-performance-live-website-now.md)


**Op code gebaseerde bewaking** is beter aanpasbaar en verzamelt extra telemetrie, maar u moet een afhankelijkheid van uw code toevoegen aan de Application Insights SDK NuGet-pakketten. Raadpleeg de volgende bronnen voor meer informatie over het inschakelen van bewaking, afhankelijk van uw toepassing.

- [ASP.NET-toepassingen](app/asp-net.md)
- [ASP.NET Core-toepassingen](app/asp-net-core.md)
- [.NET Console-toepassingen](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Andere platforms](app/platforms.md)

### <a name="configure-availability-testing"></a>Beschikbaarheids testen configureren
Beschikbaarheids tests in Application Insights zijn terugkerende tests waarmee de beschik baarheid en reactie tijd van uw toepassing met regel matige tussen pozen van punten over de hele wereld worden gecontroleerd. U kunt een eenvoudige ping-test maken voor gratis of een reeks webaanvragen maken om gebruikers transacties te simuleren waaraan kosten zijn gekoppeld. 

Zie [de beschik baarheid van alle websites bewaken voor een](app/monitor-web-app-availability.md) samen vatting van de verschillende soorten tests en Details over het maken hiervan.

### <a name="configure-profiler"></a>Profiler configureren
Profiler in Application Insights biedt prestatie traceringen voor .NET-toepassingen. Het helpt u bij het identificeren van het ' hot ' codepad dat het langst duurt wanneer een bepaalde webaanvraag wordt verwerkt. Het proces voor het configureren van de Profiler varieert, afhankelijk van het type toepassing. 

Zie [productie toepassingen in azure](app/profiler-overview.md) profileren met Application Insights voor meer informatie over het configureren van Profiler.

### <a name="configure-snapshot-debugger"></a>Snapshot Debugger configureren
Snapshot Debugger in Application Insights wordt de uitzonde ring telemetrie van uw .NET-toepassing gecontroleerd en worden moment opnamen verzameld op uw belangrijkste uitzonde ringen, zodat u over de benodigde informatie beschikt om problemen in de productie op te sporen. Het proces voor het configureren van Snapshot Debugger varieert, afhankelijk van het type toepassing. 

Zie [moment opnamen van fout opsporing op uitzonde ringen in .net-apps](app/snapshot-debugger.md) voor meer informatie over het configureren van Snapshot debugger.


## <a name="visualize-data"></a>Gegevens visualiseren
Inzichten en oplossingen bevatten hun eigen werkmappen en weer gaven voor het analyseren van de gegevens. Daarnaast kunt u uw eigen [Visualisaties](visualizations.md) maken, inclusief werkmappen voor Azure monitor gegevens en dash boards om Azure monitor gegevens te combi neren met gegevens uit andere services in Azure.


### <a name="create-workbooks"></a>Werkmappen maken
Met [werkmappen](visualize/workbooks-overview.md) in azure monitor kunt u uitgebreide visuele rapporten maken in de Azure Portal. U kunt verschillende gegevens sets combi neren van Azure Monitor metrieken en Azure Monitor Logboeken om een uniforme interactieve ervaring te creëren. U kunt een galerie met werkmappen openen op het tabblad **werkmappen** van het menu Azure monitor. 

Zie [Azure monitor werkmappen](visualize/workbooks-overview.md) voor meer informatie over het maken van aangepaste werkmappen.

### <a name="create-dashboards"></a>Dashboards maken
[Azure-Dash boards](../azure-portal/azure-portal-dashboards.md) zijn de primaire Dash boarding-technologie voor Azure en bieden u de mogelijkheid om Azure monitor gegevens te combi neren met gegevens uit andere services om een enkel glas venster te bieden voor uw Azure-infra structuur. Zie [Dash boards van log Analytics gegevens maken en delen](visualize/tutorial-logs-dashboards.md) voor meer informatie over het maken van een dash board dat gegevens uit Azure monitor Logboeken bevat. 

Zie [aangepaste KPI-Dash boards maken met behulp van Azure-toepassing Insights](app/tutorial-app-dashboards.md) voor meer informatie over het maken van een dash board dat gegevens bevat van Application Insights. 

## <a name="alerts"></a>Waarschuwingen
Waarschuwingen in Azure Monitor proactief u op de hoogte te stellen van belang rijke gegevens of patronen die in uw bewakings gegevens zijn geïdentificeerd. Er worden door sommige inzichten waarschuwingen zonder configuratie gegenereerd. Voor andere scenario's moet u [waarschuwings regels](alerts/alerts-overview.md) maken die de gegevens bevatten die moeten worden geanalyseerd en de criteria voor het genereren van een waarschuwing, en actie groepen waarmee de actie wordt gedefinieerd die moet worden uitgevoerd wanneer er een waarschuwing wordt gegenereerd. 


### <a name="create-action-groups"></a>Actiegroepen maken
[Actie groepen](alerts/action-groups.md) zijn een verzameling meldings voorkeuren die worden gebruikt door waarschuwings regels om te bepalen welke actie moet worden uitgevoerd wanneer een waarschuwing wordt geactiveerd. Voor beelden van acties zijn het verzenden van een e-mail of tekst, het aanroepen van een webhook of het verzenden van gegevens naar een ITSM-hulp programma. Elke waarschuwings regel vereist ten minste één actie groep en één actie groep kan worden gebruikt door meerdere waarschuwings regels.

Zie [actie groepen maken en beheren in de Azure Portal](alerts/action-groups.md) voor meer informatie over het maken van een actie groep en een beschrijving van de verschillende acties die kunnen worden opgenomen.


### <a name="create-alert-rules"></a>Waarschuwingsregels maken
Er zijn meerdere typen waarschuwings regels die worden gedefinieerd door het type gegevens dat ze gebruiken. Elk heeft verschillende mogelijkheden en een andere prijs. De basis strategie die u moet volgen, is het gebruik van het type waarschuwings regel met de laagste kosten die de logica biedt die u nodig hebt.

- [Regels voor activiteiten logboeken](alerts/activity-log-alerts.md). Hiermee wordt een waarschuwing gemaakt als reactie op een nieuwe gebeurtenis in het activiteiten logboek die overeenkomt met de opgegeven voor waarden. Er zijn geen kosten voor deze waarschuwingen, dus het moet uw eerste keuze zijn. Zie [waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure monitor](alerts/alerts-activity-log.md) voor meer informatie over het maken van een waarschuwing voor een activiteiten logboek.
- [Metrische waarschuwings regels](alerts/alerts-metric-overview.md). Maakt een waarschuwing als reactie op een of meer meet waarden die een drempel waarde overschrijden. Metrische waarschuwingen zijn stateful, wat betekent dat de waarschuwing automatisch wordt gesloten wanneer de waarde onder de drempel daalt en er alleen meldingen worden verzonden wanneer de status wordt gewijzigd. Er zijn kosten voor metrische waarschuwingen, maar dit is aanzienlijk minder dan logboek waarschuwingen. Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](alerts/alerts-metric.md) voor meer informatie over het maken van een metrische waarschuwing.
- [Waarschuwings regels in logboek registreren](alerts/alerts-unified-log.md). Hiermee wordt een waarschuwing gemaakt wanneer de resultaten van een plannings query overeenkomen met de opgegeven criteria. Dit zijn de duur van de waarschuwings regels, maar ze staan de meest complexe criteria toe. Zie [logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](alerts/alerts-log.md) voor meer informatie over het maken van een waarschuwing voor een logboek query.
- Met [toepassings waarschuwingen](app/monitor-web-app-availability.md) kunt u proactieve prestaties en beschik baarheid testen van uw webtoepassing uitvoeren. U kunt gratis een ping-test uitvoeren, maar er zijn kosten voor complexere tests. Zie [de beschik baarheid van een website bewaken](app/monitor-web-app-availability.md) voor een beschrijving van de verschillende tests en Details over het maken hiervan.


## <a name="next-steps"></a>Volgende stappen

- Zie [Azure monitor op schaal implementeren met behulp van Azure Policy](deploy-scale.md).