---
title: Hulp programma voor diagnostische gegevens en oplossingen
description: Meer informatie over het oplossen van problemen met uw app in Azure App Service met het hulp programma voor diagnostische gegevens en oplossingen in de Azure Portal.
keywords: app service, Azure app service, diagnostische gegevens, ondersteuning, Web-app, probleem oplossing, zelf hulp
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: d58341979b0bbe0699a5ca293b20394c43cde1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962805"
---
# <a name="azure-app-service-diagnostics-overview"></a>Overzicht van Azure App Service diagnostische gegevens

Wanneer u een webtoepassing uitvoert, wilt u voor bereid zijn op problemen die zich kunnen voordoen, van 500 fouten aan uw gebruikers door gegeven dat uw site niet beschikbaar is. App Service Diagnostics is een intelligente en interactieve ervaring om u te helpen bij het oplossen van problemen met uw app zonder dat hiervoor configuratie is vereist. Wanneer u problemen ondervindt met uw app, App Service diagnostische gegevens naar aanleiding van een probleem met de juiste informatie om eenvoudiger en snel problemen op te lossen en het probleem op te lossen.

Hoewel deze ervaring het handigst is wanneer u problemen ondervindt met uw app in de afgelopen 24 uur, zijn alle diagnostische grafieken altijd beschikbaar om te analyseren.

App Service diagnostische gegevens werken alleen voor uw app in Windows, maar ook apps op [Linux/containers](./overview.md#app-service-on-linux), [app service Environment](./environment/intro.md)en [Azure functions](../azure-functions/functions-overview.md).

## <a name="open-app-service-diagnostics"></a>App Service diagnostische gegevens openen

Om toegang te krijgen tot App Service diagnostische gegevens, gaat u naar uw App Service web-app of App Service Environment in de [Azure Portal](https://portal.azure.com). Klik in de linkernavigatiebalk op **problemen vaststellen en oplossen**.

Voor Azure Functions gaat u naar de functie-app en klikt u in de bovenste navigatie balk op **platform functies** en selecteert u **problemen vaststellen en oplossen** van het **resource beheer** gedeelte.

Op de start pagina van App Service diagnostische gegevens kunt u de categorie kiezen die het probleem met uw app het beste beschrijft met behulp van de tref woorden in elke start pagina tegel. Op deze pagina vindt u ook **Diagnostische Hulpprogram ma's** voor Windows-apps. Zie [Diagnostische hulpprogram ma's (alleen voor Windows-apps)](#diagnostic-tools-only-for-windows-app).

![Startpagina](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Als uw app niet beschikbaar is of langzaam wordt uitgevoerd, kunt u [een profilerings tracering verzamelen](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) om de hoofd oorzaak van het probleem te achterhalen. Profile ring is licht gewicht en is ontworpen voor productie scenario's.
>

## <a name="interactive-interface"></a>Interactieve interface

Wanneer u een startpagina categorie selecteert die het beste past bij het probleem van uw app, kan App Service diagnostische gegevens van de interactieve interface, genie, u helpen bij het vaststellen en oplossen van problemen met uw app. U kunt de snelkoppelingen voor tegels in genie gebruiken om het volledige diagnostische rapport van de probleem categorie die u wilt bekijken weer te geven. De snelkoppelingen voor tegels bieden u een directe manier om toegang te krijgen tot uw diagnostische gegevens.

![Snelkoppelingen voor tegels](./media/app-service-diagnostics/tile-shortcuts-2.png)

Nadat u op deze tegels hebt geklikt, kunt u een lijst met onderwerpen weer geven die betrekking hebben op het probleem dat wordt beschreven in de tegel. Deze onderwerpen bevatten fragmenten van belang rijke informatie uit het volledige rapport. U kunt op een van deze onderwerpen klikken om de problemen verder te onderzoeken. U kunt ook klikken op **volledig rapport weer geven** om alle onderwerpen op één pagina te verkennen.

![Onderwerpen](./media/app-service-diagnostics/application-logs-insights-3.png)

![Volledig rapport weer geven](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnostisch rapport

Nadat u hebt gekozen om het probleem verder te onderzoeken door te klikken op een onderwerp, kunt u meer informatie bekijken over het onderwerp dat vaak wordt aangevuld met grafieken en prijs opgaven. Diagnostisch rapport kan een krachtig hulp middel zijn voor het lokaliseren van het probleem met uw app.

![Diagnostisch rapport](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Status controle van agenten

Als u niet weet wat er mis is met uw app of als u niet weet waar u uw problemen kunt oplossen, is de status controle van agenten een goede plaats om te starten. De controle van agenten van de status analyseert uw toepassingen om u een snel, interactief overzicht te geven waarin wordt uitgelegd wat er in orde is en wat het probleem is, zodat u kunt zien waar het moet worden onderzocht. De intelligente en interactieve interface bevat richt lijnen voor het oplossen van problemen. De status controle van agenten is geïntegreerd in de genie-ervaring voor Windows-apps en web-app-diagnose rapport voor Linux-apps.

### <a name="health-checkup-graphs"></a>Grafieken voor status controle van agenten

Er zijn vier verschillende grafieken in de status controle van agenten.

- **aanvragen en fouten:** Een grafiek met het aantal aanvragen dat in de afgelopen 24 uur wordt gemaakt, samen met HTTP-server fouten.
- **app-prestaties:** Een grafiek waarin de reactie tijd in de afgelopen 24 uur voor verschillende percentiel groepen wordt weer gegeven.
- **CPU-gebruik:** Een grafiek waarin het totale percentage CPU-gebruik per instantie wordt weer gegeven in de afgelopen 24 uur.  
- **geheugen gebruik:** Een grafiek met het totale percentage fysiek geheugen gebruik per instantie in de afgelopen 24 uur.

![Status controle van agenten](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Problemen met toepassings code onderzoeken (alleen voor Windows-apps)

Omdat veel app-problemen zijn gerelateerd aan problemen in de toepassings code, worden App Service diagnostische gegevens geïntegreerd met [Application Insights](../azure-monitor/app/app-insights-overview.md) om uitzonde ringen en afhankelijkheids kwesties te markeren om te correleren met de geselecteerde uitval tijd. Application Insights moet afzonderlijk worden ingeschakeld.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Als u Application Insights-uitzonde ringen en-afhankelijkheden wilt weer geven, selecteert u de snelkoppelingen **Web-app down** of **Web-app langzaam** naast elkaar.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Probleemoplossings stappen (alleen voor Windows-apps)

Als er in de afgelopen 24 uur een probleem met een specifieke probleem categorie wordt gedetecteerd, kunt u het volledige diagnostische rapport weer geven en kunnen App Service diagnostische gegevens u vragen om meer informatie over probleem oplossing en volgende stappen voor een meer begeleide ervaring.

![Application Insights en probleem oplossing en volgende stappen](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnostische hulpprogram ma's (alleen voor Windows-apps)

Diagnostische Hulpprogram Ma's bevatten meer geavanceerde diagnostische hulpprogram ma's die u helpen bij het onderzoeken van problemen met toepassings codes, vertragingen, verbindings reeksen en nog veel meer. en proactieve hulp middelen die u helpen bij het oplossen van problemen met CPU-gebruik,-aanvragen en-geheugen.

### <a name="proactive-cpu-monitoring"></a>Proactieve CPU-bewaking

Proactieve CPU-bewaking biedt u een eenvoudige, proactieve manier om een actie uit te voeren wanneer uw app of een onderliggend proces voor uw app hoge CPU-bronnen verbruikt. U kunt uw eigen drempel waarden voor de CPU instellen om een hoog CPU-probleem tijdelijk op te lossen totdat de echte oorzaak van het onverwachte probleem wordt gevonden. Zie voor meer informatie [problemen met uw CPU oplossen voordat deze optreden](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Proactieve CPU-bewaking](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Automatisch herstel en proactief automatisch herstel

Automatisch herstel is een actie die u kunt uitvoeren wanneer uw app onverwachte gedrag heeft. U kunt uw eigen regels instellen op basis van het aantal aanvragen, de langzame aanvraag, de geheugen limiet en de HTTP-status code om beperkings acties te activeren. Gebruik het hulp programma om tijdelijk een onverwacht gedrag te beperken tot u de hoofd oorzaak hebt gevonden. Zie [de nieuwe automatische Retoucheer ervaring aankondigen in app service Diagnostics](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)voor meer informatie.

![Automatisch herstel](./media/app-service-diagnostics/auto-healing-10.png)

Net als proactieve CPU-bewaking is proactieve automatische herstel bewerking een oplossing om het onverwachte gedrag van uw app te beperken. Met proactieve automatische herstel bewerking wordt uw app opnieuw gestart wanneer App Service bepaalt dat uw app een onherstelbare status heeft. Zie [Inleiding tot proactieve automatische Retouchies](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)voor meer informatie.

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigator-en wijzigings analyse (alleen voor Windows-apps)

In een groot team met continue integratie en waar uw app veel afhankelijkheden heeft, kan het lastig zijn om de specifieke wijziging te lokaliseren die een slecht gedrag veroorzaakt. Met Navigator krijgt u inzicht in de topologie van uw app door automatisch een afhankelijkheids kaart van uw app en alle resources in hetzelfde abonnement te renderen. Met Navigator kunt u een geconsolideerde lijst met wijzigingen bekijken die door uw app en de afhankelijkheden ervan zijn gemaakt en een beperking instellen voor een wijziging die een slecht gedrag veroorzaakt. U kunt deze openen via de tegel **Navigator** van de start pagina en moet worden ingeschakeld voordat u deze de eerste keer gebruikt. Zie [inzicht in de afhankelijkheden van uw app verkrijgen met Navigator](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)voor meer informatie.

![Standaard pagina Navigator](./media/app-service-diagnostics/navigator-default-page-11.png)

![Diff-weer gave](./media/app-service-diagnostics/diff-view-12.png)

Wijzigings analyse voor app-wijzigingen is toegankelijk via tegel snelkoppelingen, **toepassings wijzigingen** en **crashes van toepassingen** in **Beschik baarheid en prestaties** , zodat u deze gelijktijdig met andere metrische gegevens kunt gebruiken. Voordat u de functie kunt gebruiken, moet u deze eerst inschakelen. Zie voor meer informatie [de nieuwe ervaring voor wijzigings analyse aankondigen in app service Diagnostics](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Post uw vragen of feedback op [UserVoice](https://feedback.azure.com/forums/169385-web-apps) door ' [diag] ' toe te voegen in de titel.