---
title: 'Quickstart: Node.js bewaken met Azure Monitor Application Insights'
description: In dit artikel vindt u instructies om een Node.js-web-app snel in te stellen voor controle door Azure Monitor Application Insights
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 96a60c88997226516d451124280053d87065e5b6
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106381940"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Quickstart: Beginnen met het bewaken van uw Node.js-webtoepassing met Azure Application Insights

In deze quickstart voegt u Application Insights SDK versie 0.22 voor Node.js toe aan een bestaande Node.js-webtoepassing.

Met Azure Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een webtoepassing controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. Vanaf de release van versie 0.20 van de SDK kunt u veelgebruikte pakketten van derden controleren, inclusief MongoDB, MySQL en Redis.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Een werkende Node. js-toepassing.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Met Application Insights kunnen telemetriegegevens worden verzameld vanuit elke toepassing met een internetverbinding, ongeacht of deze on-premises wordt uitgevoerd of in de cloud. Gebruik de volgende stappen om deze gegevens te bekijken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer **Een resource maken** > **Hulpprogramma's voor ontwikkelaars** > **Application Insights**.

   ![Een Application Insights-resource toevoegen](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights-resource maakt, kunt u meer informatie vinden door naar het document [Een Application Insights-resource maken](../app/create-new-resource.md) te gaan.

   Er wordt een configuratiepagina weergegeven. Gebruik de volgende tabel om de invoervelden in te vullen. 

    | Instellingen        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | Naam die de app identificeert die u wilt controleren |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe resourcegroep waarin AppInsights-gegevens worden gehost. Maak een nieuwe resourcegroep of gebruik een bestaande. |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

3. Selecteer **Maken**.

## <a name="configure-appinsights-sdk"></a>AppInsights-SDK configureren

1. Selecteer **Overzicht** en kopieer de **Instrumentatiesleutel** van uw toepassing.

   ![De Application Insights-instrumentatiesleutel bekijken](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Voeg de Application Insights-SDK voor Node.js toe aan uw toepassing. Voer deze opdracht uit vanuit de hoofdmap van uw app:

   ```bash
   npm install applicationinsights --save
   ```

3. Bewerk het eerste *.js*-bestand van uw app en voeg de twee onderstaande regels toe aan het bovenste deel van uw script. Als u de [Node.js Quick Start-app](../../app-service/quickstart-nodejs.md) gebruikt, wijzigt u het bestand *index.js*. Vervang `<instrumentation_key>` door uw instrumentatiesleutel van Application Insights. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Start de app opnieuw.

> [!NOTE]
> Het duurt 3-5 minuten voordat de gegevens worden weergegeven in de portal. Als deze app een test-app met weinig verkeer is, houd er dan rekening mee dat de meeste metrische gegevens alleen vastgelegd wanneer er actieve aanvragen en bewerkingen zijn.

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. U kunt de pagina **Overzicht** van Application Insights in Azure Portal, waar u de instrumentatiesleutel hebt opgehaald, nu opnieuw openen om de details te bekijken van de toepassing die momenteel wordt uitgevoerd.

   ![Menu Overzicht van Application Insights](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Selecteer **Toepassingsoverzicht** voor een visueel overzicht van de afhankelijkheidsrelaties tussen de onderdelen van de toepassing. Voor elk onderdeel worden KPI's weergegeven, zoals belasting, prestaties, fouten en waarschuwingen.

   ![Toepassingskaart van Application Insights](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Selecteer het **App Analytics**-pictogram ![Pictogram Toepassingskaart](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **Weergeven in Analytics**.  Door deze actie wordt **Application Insights Analytics** geopend. Dit biedt een querytaal met opmaak voor het analyseren van alle gegevens die zijn verzameld met Application Insights. In dit geval wordt er een query gegenereerd waarmee het aantal aanvragen wordt weergegeven als een grafiek. U kunt uw eigen query's schrijven om andere gegevens te analyseren.

   ![Analysegrafieken van Application Insights](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Ga terug naar de pagina **Overzicht** en bekijk de KPI-grafieken.  Dit dashboard biedt statistische gegevens over de toepassingsstatus, waaronder het aantal inkomende aanvragen, de duur van deze aanvragen en eventuele fouten die optreden.

   ![Tijdlijngrafieken voor Application Insights statusoverzicht](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   Als u de grafiek **Laadtijd voor paginaweergave** wilt vullen met **telemetriegegevens aan de clientzijde**, voegt u dit script toe aan elke pagina die u wilt bijhouden:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. Selecteer aan de linkerkant **Metrische gegevens**. Gebruik Metrics Explorer om de status en het gebruik van uw resource te onderzoeken. Selecteer **Nieuwe grafiek toevoegen** om aanvullende aangepaste weergaven te maken, of selecteer **Bewerken** om type, hoogte, kleurenpalet, groeperingen en metrische gegevens van bestaande grafieken te wijzigen. U kunt bijvoorbeeld een grafiek maken die de gemiddelde laadtijd van browserpagina's weergeeft door 'Browser page load time' (gemiddelde laadtijd browserpagina) te selecteren uit de vervolgkeuzelijst met metrische gegevens en 'Avg' (gemiddelde) bij aggregatie. Ga naar [Aan de slag met Azure Metrics Explorer](../essentials/metrics-getting-started.md) voor meer informatie over Azure Metrics Explorer.

   ![Grafiek met metrische gegevens van Application Insights-server](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Bekijk de [extra Node.js-documentatie voor AppInsights](../app/nodejs.md) voor meer informatie over het bewaken van Node.js.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources verwijderen wanneer u klaar bent met testen. Volg de onderstaande stappen om dit te doen.

> [!NOTE]
> Als u een bestaande resourcegroep hebt gebruikt, werken de onderstaande instructies niet en hoeft u alleen de afzonderlijke Application Insights-resource te verwijderen. Houd er rekening mee dat wanneer u een resourcegroep verwijdert, alle onderliggende resources die lid zijn van die groep worden verwijderd.

1. Selecteer in het menu links in de Azure-portal **Resourcegroepen** en selecteer vervolgens **myResourceGroup**.
2. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, voer **myResourceGroup** in het tekstvak in en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Prestatieproblemen zoeken en diagnoses uitvoeren](../logs/log-query-overview.md)

