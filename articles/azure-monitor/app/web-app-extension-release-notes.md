---
title: Release opmerkingen voor de extensie van Azure web app-Application Insights
description: Geeft opmerkingen over Azure Web Apps-extensie voor runtime-instrumentatie met Application Insights.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 07ba61f630b849a377f1c7ba881f95518eb73606
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042603"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Release opmerkingen voor de Azure web app-extensie voor Application Insights

Dit artikel bevat de release opmerkingen voor Azure Web Apps-extensie voor runtime-instrumentatie met Application Insights. Dit is alleen van toepassing op vooraf geïnstalleerde uitbrei dingen.

Meer [informatie](azure-web-apps.md) over de extensie van Azure web-apps voor Application Insights.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

- Hoe vind ik de versie van de uitbrei ding die ik momenteel heb?
    - Ga naar `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`. Ga naar [de stapsgewijze hand leiding voor het oplossen van problemen met de bewaking van extensies en agents](./azure-web-apps.md?tabs=net#troubleshooting) voor meer informatie.

- Wat gebeurt er als ik persoonlijke extensies gebruik?
    - Verwijder de uitbrei dingen van de persoonlijke site, omdat deze niet meer worden ondersteund.

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="2838"></a>2.8.38

- JAVA-extensie: bijgewerkt naar [Java Agent 3.0.2 (ga)](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.0.2) van 2.5.1.
- Node.js extensie: de AI SDK is bijgewerkt naar [1.8.8](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/1.8.8) vanuit 1.8.7.
- .NET core: er zijn niet-ondersteunde versies (2,0, 2,2, 3,0) verwijderd. Ondersteunde versies zijn 2,1 en 3,1.

### <a name="2837"></a>2.8.37

- AppSvc Windows-extensie: er is al een versie van System.Diagnostics.DiagnosticSource.dll .net Core gebruikt.

### <a name="2836"></a>2.8.36

- AppSvc Windows-uitbrei ding: Inter-op inschakelen met AI SDK in .NET core.

### <a name="2835"></a>2.8.35

- AppSvc Windows-extensie: er is ondersteuning voor .NET Core 3,1 toegevoegd.

### <a name="2833"></a>2.8.33

- .NET-, .NET core-, Java-en Node.js-agents en de Windows-extensie: ondersteuning voor soevereine Clouds. Verbindings reeksen kunnen worden gebruikt voor het verzenden van gegevens naar soevereine Clouds.

### <a name="2831"></a>2.8.31

- ASP.NET Core agent: probleem opgelost in een van de bijgewerkte Application Insights SDK-verwijzingen (zie bekende problemen voor 2.8.26). Als er al een onjuiste versie van `System.Diagnostics.DiagnosticSource.dll` is geladen door runtime, wordt de toepassing niet door de code-uitbrei ding gestopt en wordt er geen back-ups meer weer gegeven. Voor klanten waarop dit probleem betrekking heeft, wordt het aanbevolen om de `System.Diagnostics.DiagnosticSource.dll` map uit de bin te verwijderen of de oudere versie van de uitbrei ding te gebruiken door ' ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24 ' in te stellen. anders is toepassings bewaking niet ingeschakeld.

### <a name="2826"></a>2.8.26

- ASP.NET Core agent: probleem opgelost met betrekking tot bijgewerkte Application Insights SDK. De agent probeert niet te laden `AiHostingStartup` als de ApplicationInsights.dll al aanwezig is in de bin-map. Hiermee worden problemen met reflectie via assembly opgelost \<AiHostingStartup\> . GetTypes().
- Bekende problemen: `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` Er kan een uitzonde ring worden gegenereerd als een andere versie van `DiagnosticSource` dll wordt geladen. Dit kan bijvoorbeeld gebeuren als `System.Diagnostics.DiagnosticSource.dll` de map Publish aanwezig is. Als oplossing kunt u de vorige versie van de uitbrei ding gebruiken door app-instellingen in app Services in te stellen: ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24.

### <a name="2824"></a>2.8.24

- De versie van 2.8.21 is opnieuw ingepakt.

### <a name="2823"></a>2.8.23

- Er is ASP.NET Core ondersteuning voor de bewaking van code ring (3,0) toegevoegd.
- ASP.NET Core SDK is bijgewerkt naar [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) voor runtime versie 2,1, 2,2 en 3,0. Apps die zijn gericht op .NET Core 2,0, blijven gebruikmaken van 2.1.1 van de SDK.

### <a name="2814"></a>2.8.14

- ASP.NET Core SDK-versie bijgewerkt van 2.3.0 naar de nieuwste (2.6.1) voor apps die zijn gericht op .NET Core 2,1, 2,2. Apps die zijn gericht op .NET Core 2,0, blijven gebruikmaken van 2.1.1 van de SDK.

### <a name="2812"></a>2.8.12

- Ondersteuning voor ASP.NET Core 2,2-apps.
- Er is een fout opgelost in ASP.NET Core-uitbrei ding waardoor de SDK wordt geïnjecteerd, zelfs wanneer de toepassing al instrumenteert met de SDK. Voor 2,1-en 2,2-apps zorgt de aanwezigheid van ApplicationInsights.dll in de toepassingsmap ervoor dat de uitbrei ding nu weer uitvalt. Voor 2,0-apps is de uitbrei ding alleen van toepassing als ApplicationInsights is ingeschakeld met een `UseApplicationInsights()` aanroep.

- Permanente oplossing voor onvolledig HTML-antwoord voor ASP.NET Core-Apps. Deze oplossing is nu uitgebreid voor het werken met .NET Core 2,2-apps.

- Er is ondersteuning toegevoegd om Java script-injectie uit te scha kelen voor ASP.NET Core-Apps ( `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` ). Voor ASP.NET core bevindt de Java script-injectie zich standaard in de modus opt-out, tenzij expliciet is uitgeschakeld. (De standaard instelling wordt uitgevoerd om het huidige gedrag te behouden.)

- Vaste ASP.NET Core extensie bug die de injectie heeft veroorzaakt, zelfs als iKey niet aanwezig is.
- Er is een fout opgelost in de SDK-versie voorvoegsel logica die een onjuiste SDK-versie in telemetrie heeft veroorzaakt.

- Het voor voegsel van de SDK-versie voor ASP.NET Core-Apps is toegevoegd om te bepalen hoe telemetrie is verzameld.
- Vaste SCM-ApplicationInsights-pagina om de versie van de vooraf geïnstalleerde uitbrei ding correct weer te geven.

### <a name="2810"></a>2.8.10

- Oplossen voor onvolledige HTML-antwoorden voor ASP.NET Core-Apps.

## <a name="next-steps"></a>Volgende stappen

- Ga naar de [Azure app service-documentatie](azure-web-apps.md) voor meer informatie over het configureren van bewaking voor Azure-app Services. 
