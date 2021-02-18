---
title: Prestatie vermindering oplossen
description: Meer informatie over het oplossen van problemen met trage prestaties in Azure App Service, waaronder het gedrag van de bewakings app, het verzamelen van gegevens en het beperken van het probleem.
tags: top-support-issue
keywords: prestaties van web-apps, trage app, trage app
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: e048e4c17092c71a5adaf29f342546b9258837c5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586237"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Problemen met prestatie problemen met langzame apps in Azure App Service oplossen
Dit artikel helpt u bij het oplossen van problemen met prestatie problemen met langzame apps in [Azure app service](./overview.md).

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en de stack overflow-forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en klik op **Ontvang ondersteuning**.

## <a name="symptom"></a>Symptoom
Wanneer u door de app bladert, worden de pagina's langzaam geladen en soms is de time-out.

## <a name="cause"></a>Oorzaak
Dit probleem wordt vaak veroorzaakt door problemen op toepassings niveau, zoals:

* netwerk aanvragen nemen lange tijd in beslag
* toepassings code of database query's zijn inefficiënt
* toepassing met hoge geheugen/CPU
* toepassing is vastgelopen vanwege een uitzonde ring

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
Probleem oplossing kan worden onderverdeeld in drie afzonderlijke taken, in sequentiële volg orde:

1. [Gedrag van de toepassing observeren en bewaken](#observe)
2. [Gegevens verzamelen](#collect)
3. [Het probleem oplossen](#mitigate)

[App service](overview.md) biedt u verschillende opties bij elke stap.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. gedrag van de toepassing observeren en bewaken
#### <a name="track-service-health"></a>Service status bijhouden
Microsoft Azure bekendmaking telkens wanneer er sprake is van een onderbreking van de service of prestaties. U kunt de status van de service op het [Azure Portal](https://portal.azure.com/)volgen. Zie [service status bijhouden](../service-health/service-notifications.md)voor meer informatie.

#### <a name="monitor-your-app"></a>Uw app controleren
Met deze optie kunt u nagaan of uw toepassing problemen ondervindt. Klik in de Blade van uw app op de tegel **aanvragen en fouten** . Op de Blade **metrische gegevens** ziet u alle metrische gegevens die u kunt toevoegen.

Enkele van de metrische gegevens die u mogelijk wilt bewaken voor uw app, zijn

* Gemiddelde werkset geheugen
* Gemiddelde reactie tijd
* CPU-tijd
* Werkset geheugen
* Aanvragen

![prestaties van apps bewaken](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Zie voor meer informatie:

* [Apps in Azure App Service bewaken](web-sites-monitor.md)
* [Waarschuwingen ontvangen](../azure-monitor/alerts/alerts-overview.md)

#### <a name="monitor-web-endpoint-status"></a>Status van web-eind punt bewaken
Als uw app wordt uitgevoerd in de prijs categorie **Standard** , kunt u met app service twee eind punten van drie geografische locaties bewaken.

Met eindpunt controle configureert u webtests van geografisch gedistribueerde locaties die de reactie tijd en de uptime van weburl's testen. De test voert een HTTP GET-bewerking uit op de web-URL om de reactie tijd en de uptime van elke locatie te bepalen. Elke geconfigureerde locatie voert elke vijf minuten een test uit.

De uptime wordt bewaakt met behulp van HTTP-antwoord codes en de reactie tijd wordt gemeten in milliseconden. Een bewakings test mislukt als de HTTP-antwoord code groter is dan of gelijk is aan 400 of als het antwoord meer dan 30 seconden duurt. Een eind punt wordt als beschikbaar beschouwd als de bewakings tests van alle opgegeven locaties slagen.

Als u deze wilt instellen, raadpleegt u [apps controleren in azure app service](web-sites-monitor.md).

Zie ook voor het [bewaren van Azure web sites up-to-endpoint bewaking-met Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) voor een video op endpoint-bewaking.

#### <a name="application-performance-monitoring-using-extensions"></a>Bewaking van toepassings prestaties met behulp van uitbrei dingen
U kunt ook de prestaties van uw toepassingen bewaken met behulp van een *site-uitbrei ding*.

Elke App Service-app biedt een uitbreidbaar beheer eindpunt waarmee u een krachtige set hulpprogram ma's kunt gebruiken die als site-extensies worden geïmplementeerd. Uitbrei dingen zijn: 

- Bron code-editors zoals [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Beheer hulpprogramma's voor verbonden resources, zoals een MySQL-data base die is verbonden met een app.

[Azure-toepassing Insights](https://azure.microsoft.com/services/application-insights/) is een site-uitbrei ding voor prestatie bewaking die ook beschikbaar is. Als u Application Insights wilt gebruiken, bouwt u uw code opnieuw met een SDK. U kunt ook een extensie installeren die toegang biedt tot aanvullende gegevens. Met de SDK kunt u code schrijven om het gebruik en de prestaties van uw app in meer detail te bewaken. Zie [prestaties van webtoepassingen controleren](../azure-monitor/app/app-insights-overview.md)voor meer informatie.

<a name="collect"></a>

### <a name="2-collect-data"></a>2. gegevens verzamelen
App Service biedt diagnostische functionaliteit voor het vastleggen van logboek gegevens van zowel de webserver als de webtoepassing. De informatie wordt onderverdeeld in webserver diagnostiek en toepassings diagnoses.

#### <a name="enable-web-server-diagnostics"></a>Diagnostische gegevens van webserver inschakelen
U kunt de volgende soorten logboeken in-of uitschakelen:

* **Gedetailleerde fout registratie** : gedetailleerde fout informatie voor HTTP-status codes die duiden op een fout (status code 400 of hoger). Dit kan informatie bevatten die u kan helpen om te bepalen waarom de server de fout code heeft geretourneerd.
* **Tracering van mislukte aanvragen** -gedetailleerde informatie over mislukte aanvragen, inclusief een tracering van de IIS-onderdelen die worden gebruikt om de aanvraag te verwerken en de tijd die in elk onderdeel is gemaakt. Dit kan handig zijn als u probeert de prestaties van de app te verbeteren of te isoleren wat een specifieke HTTP-fout veroorzaakt.
* **Logboek registratie van webserver** -informatie over http-trans acties met de uitgebreide W3C-indeling van logboek bestand. Dit is handig bij het bepalen van de algemene metrische gegevens van de app, zoals het aantal verwerkte aanvragen of hoeveel aanvragen van een specifiek IP-adres.

#### <a name="enable-application-diagnostics"></a>Application Diagnostics inschakelen
Er zijn verschillende opties voor het verzamelen van prestatie gegevens van toepassingen van App Service, het profiel van uw toepassing in Visual Studio of het wijzigen van de toepassings code om meer informatie en traceringen te registreren. U kunt de opties kiezen op basis van de mate van toegang tot de toepassing en wat u hebt geconstateerd via de controle hulpprogramma's.

##### <a name="use-application-insights-profiler"></a>Application Insights Profiler gebruiken
U kunt de Application Insights Profiler inschakelen om te beginnen met het vastleggen van gedetailleerde prestatie traceringen. U kunt traceringen die Maxi maal vijf dagen geleden zijn vastgelegd, benaderen wanneer u problemen in het verleden wilt onderzoeken. U kunt deze optie kiezen op voor waarde dat u toegang hebt tot de Application Insights resource van de app op Azure Portal.

Application Insights Profiler biedt statistieken over de reactie tijd voor elke webaanroep en traceringen die aangeven welke regel code de langzame reacties heeft veroorzaakt. Soms is de App Service-app traag omdat bepaalde code niet op een uitvoerende manier wordt geschreven. Voor beelden zijn sequentiële code die kan worden uitgevoerd in parallelle en ongewenste database vergrendelings conflicten. Door deze knel punten in de code te verwijderen, worden de prestaties van de app verhoogd, maar ze zijn moeilijk te detecteren zonder dat er uitgebreide traceringen en logboeken hoeven worden ingesteld. Met de traceringen die door Application Insights Profiler worden verzameld, kunt u de code regels identificeren waarmee de toepassing wordt vertraagd en deze uitdaging voor App Service-apps wordt verholpen.

 Zie voor meer informatie [Live-apps profileren in azure app service met Application Insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Externe profile ring gebruiken
In azure app service kunnen Web apps, API apps, mobiele back-ends en webjobs op afstand worden profileeerd. Kies deze optie als u toegang hebt tot de app-resource en u weet hoe u het probleem moet reproduceren of als u het precieze tijds interval weet dat het prestatie probleem optreedt.

Externe profile ring is handig als het CPU-gebruik van het proces hoog is en het proces langzamer is dan verwacht, of de latentie van HTTP-aanvragen hoger is dan normaal, u kunt uw proces op afstand profileren en de CPU-sampling-bemonsterings stacks ophalen om de proces activiteit en de code-Hot paden te analyseren.

Zie [ondersteuning voor externe profilering in azure app service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service)voor meer informatie.

##### <a name="set-up-diagnostic-traces-manually"></a>Diagnostische traceringen hand matig instellen
Als u toegang hebt tot de bron code van de webtoepassing, kunt u met Application Diagnostics gegevens vastleggen die zijn gemaakt door een webtoepassing. ASP.NET-toepassingen kunnen de- `System.Diagnostics.Trace` klasse gebruiken om informatie te registreren in het logboek voor toepassings diagnose. U moet echter de code wijzigen en uw toepassing opnieuw implementeren. Deze methode wordt aanbevolen als uw app wordt uitgevoerd in een test omgeving.

Zie [Diagnostische logboek registratie inschakelen voor apps in azure app service](troubleshoot-diagnostic-logs.md)voor gedetailleerde instructies voor het configureren van uw toepassing voor logboek registratie.

#### <a name="use-the-diagnostics-tool"></a>Het hulp programma voor diagnostische gegevens gebruiken
App Service biedt een intelligente en interactieve ervaring om u te helpen bij het oplossen van problemen met uw app zonder dat hiervoor configuratie is vereist. Wanneer u problemen ondervindt met uw app, wordt in het diagnostische hulp programma aangegeven wat er mis is met de juiste informatie om snel te kunnen werken en het probleem op te lossen.

Om toegang te krijgen tot App Service diagnostische gegevens, gaat u naar uw App Service app of App Service Environment in de [Azure Portal](https://portal.azure.com). Klik in de linkernavigatiebalk op **problemen vaststellen en oplossen**.

#### <a name="use-the-kudu-debug-console"></a>De kudu-console voor fout opsporing gebruiken
App Service wordt geleverd met een console voor fout opsporing die u kunt gebruiken voor het opsporen van fouten, het verkennen, uploaden van bestanden en JSON-eind punten voor het verkrijgen van informatie over uw omgeving. Deze console wordt de *kudu-console* of het *SCM-dash board* voor uw app genoemd.

U kunt dit dash board openen door naar de koppeling te gaan **https:// &lt; uw app-naam>. scm.azurewebsites.net/**.

Enkele van de dingen die kudu biedt:

* omgevings instellingen voor uw toepassing
* logboek stroom
* diagnostische dump
* console voor fout opsporing, waarin u Power shell-cmdlets en Basic DOS-opdrachten kunt uitvoeren.

Een andere handige functie van kudu is dat, als uw toepassing uitzonde ringen voor de eerste kans verkrijgt, u kudu en het hulp programma SysInternals kunt gebruiken Procdump om geheugen dumps te maken. Deze geheugen dumps zijn moment opnamen van het proces en kunnen u vaak helpen om complexere problemen met uw app op te lossen.

Zie [Azure DevOps-hulpprogram ma's waarover u moet weten](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)voor meer informatie over de beschik bare functies in kudu.

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. het probleem oplossen
#### <a name="scale-the-app"></a>De app schalen
In Azure App Service kunt u voor betere prestaties en door Voer de schaal aanpassen waarop u uw toepassing uitvoert. Voor het omhoog schalen van een app zijn twee gerelateerde acties vereist: het wijzigen van uw App Service plan in een hogere prijs categorie en het configureren van bepaalde instellingen nadat u bent overgeschakeld naar de hogere prijs categorie.

Zie [een app schalen in azure app service](manage-scale-up.md)voor meer informatie over schalen.

Daarnaast kunt u ervoor kiezen om uw toepassing uit te voeren op meer dan één exemplaar. Als u niet alleen uitschalen hebt, beschikt u niet alleen over meer verwerkings mogelijkheden, maar biedt u ook een zekere mate van fout tolerantie. Als het proces wordt uitgevoerd op één instantie, blijven de andere instanties aanvragen verwerken.

U kunt de schaal instellen op hand matig of automatisch.

#### <a name="use-autoheal"></a>Automatisch herstellen gebruiken
Met auto Retoucheer wordt het werk proces voor uw app gerecycled op basis van de instellingen die u kiest (zoals configuratie wijzigingen, aanvragen, limieten op basis van geheugen of de tijd die nodig is om een aanvraag uit te voeren). In de meeste gevallen is het recyclen van het proces de snelste manier om een probleem op te lossen. U kunt de app altijd direct vanuit de Azure Portal opnieuw opstarten, maar automatisch herstellen voor u. U hoeft alleen maar enkele triggers toe te voegen aan het hoofd web.config voor uw app. Deze instellingen werken op dezelfde manier, zelfs als uw toepassing geen .NET-app is.

Zie voor meer informatie [automatisch herstel van Azure web sites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>De app opnieuw starten
Het opnieuw starten is vaak de eenvoudigste manier om te herstellen vanuit eenmalige problemen. Op de [Azure Portal](https://portal.azure.com/)op de Blade van uw app hebt u de opties om uw app te stoppen of opnieuw op te starten.

 ![app opnieuw starten om prestatie problemen op te lossen](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

U kunt uw app ook beheren met behulp van Azure PowerShell. Zie [Azure PowerShell gebruiken met Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md) voor meer informatie.