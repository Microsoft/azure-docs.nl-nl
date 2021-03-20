---
title: Het deel venster instellingen voor Azure-toepassing Insights Profiler gebruiken | Microsoft Docs
description: Bekijk de status van de Profiler en start profilerings sessies
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9d9cc377ead0c297e8334d34255bd2c7c7cd39fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86499406"
---
# <a name="configure-application-insights-profiler"></a>Application Insights Profiler configureren

## <a name="updated-profiler-agent"></a>Profilerings agent bijgewerkt
De trigger functies werken alleen met versie 2,6 of hoger van de Profiler-agent. Als u een Azure App Service uitvoert, wordt uw agent automatisch bijgewerkt. U kunt zien welke versie van de agent u uitvoert als u naar de kudu-URL voor uw website gaat en \DiagnosticServices aan het eind ervan toevoegt, bijvoorbeeld:  `https://yourwebsite.scm.azurewebsites.net/diagnosticservices` . De Application Insights Profiler Webtaak moet versie 2,6 of nieuwer hebben. U kunt een upgrade afdwingen door de web-app opnieuw te starten. 

Als u de Profiler uitvoert op een VM of Cloud service, moet u 16.0.4 of hoger van de WAD-extensie (Windows Azure Diagnostics) hebben geïnstalleerd. U kunt de versie van WAD controleren door u aan te melden bij uw virtuele machine en deze map te zoeken: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. De naam van de map is de versie van WAD die is geïnstalleerd. De Azure VM-agent werkt WAD automatisch bij wanneer er nieuwe versies beschikbaar zijn.

## <a name="profiler-settings-page"></a>Pagina instellingen Profiler

Om het deel venster instellingen voor Azure-toepassing Insights Profiler te openen, gaat u naar het deel venster Application Insights prestaties en selecteert u vervolgens de knop Profiler **configureren** .

![Koppeling naar de pagina instellingen van open Profiler][configure-profiler-entry]

Hiermee opent u een pagina die er als volgt uitziet:

![Pagina instellingen Profiler][configure-profiler-page]

De pagina **configure Application Insights Profiler** heeft de volgende functies:

| Functie | Beschrijving |
|-|-|
Profiel nu | Hiermee start u de profilerings sessies voor alle apps die zijn gekoppeld aan dit exemplaar van Application Insights.
Triggers | Hiermee kunt u triggers configureren die ervoor zorgen dat de Profiler wordt uitgevoerd. 
Recente profilerings sessies | Geeft informatie weer over eerdere profilerings sessies.

## <a name="profile-now"></a>Profiel nu
Met deze optie kunt u een profilerings sessie op aanvraag starten. Wanneer u op deze koppeling klikt, worden alle Profiler-agents die gegevens naar deze Application Insights-exemplaar verzenden, een profiel gaan vastleggen. Na 5 tot 10 minuten wordt de profiel sessie weer gegeven in de onderstaande lijst.

Een gebruiker kan een Profiler-sessie alleen hand matig activeren als deze ten minste ' schrijf toegang ' vereist voor de rol van het onderdeel Application Insights. In de meeste gevallen krijgt u deze toegang automatisch en is er geen extra werk nodig. Als u problemen ondervindt, zou de rol van het abonnements bereik moeten worden toegevoegd de rol ' Application Insights onderdeel Inzender '. [Zie meer over Role Access Control met Azure-bewaking](./resources-roles-access-control.md).

## <a name="trigger-settings"></a>Trigger instellingen
![Flyout trigger instellingen][trigger-settings-flyout]

Als u op de knop triggers op de menu balk klikt, wordt het dialoog venster trigger instellingen geopend. U kunt trigger instellen om te beginnen met profile ring wanneer het percentage CPU-of geheugen gebruik het niveau bereikt dat u instelt.

| Instelling | Beschrijving |
|-|-|
Aan/uit-knop | Op: Profiler kan worden gestart door deze trigger. Uit: Profiler wordt niet gestart door deze trigger.
Drempel waarde voor geheugen | Als dit percentage geheugen wordt gebruikt, wordt de Profiler gestart.
Duur | Hiermee stelt u in hoe lang de Profiler moet worden uitgevoerd wanneer deze wordt geactiveerd.
Cooldown | Hiermee stelt u de tijds duur in die de Profiler moet wachten voordat er opnieuw wordt gecontroleerd of het geheugen of CPU-gebruik is geactiveerd.

## <a name="recent-profiling-sessions"></a>Recente profilerings sessies
Deze sectie van de pagina bevat informatie over recente profilerings sessies. Een profilerings sessie vertegenwoordigt de periode waarin de Profiler-agent een profiel heeft genomen op een van de computers die als host fungeren voor uw toepassing. U kunt de profielen openen vanuit een sessie door te klikken op een van de rijen. Voor elke sessie worden het volgende weer gegeven:

| Instelling | Beschrijving |
|-|-|
Geactiveerd door | Hoe de sessie is gestart, hetzij door een trigger, profiel nu of standaard steekproef. 
App-naam | De naam van de toepassing die is profileeerd.
Machine-exemplaar | Naam van de computer waarop de Profiler-agent is uitgevoerd.
Tijdstempel | Tijdstip waarop het profiel is vastgelegd.
Traceren | Het aantal traceringen dat aan afzonderlijke aanvragen is gekoppeld.
CPU-percentage | Percentage van de CPU dat werd gebruikt tijdens het uitvoeren van de Profiler.
Geheugenmetabase | Percentage van het geheugen dat werd gebruikt tijdens het uitvoeren van de Profiler.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a> Gebruik web performance-tests om verkeer naar uw toepassing te genereren

U kunt Profiler hand matig activeren met één klik. Stel dat u een webprestatiess test uitvoert. U hebt traceringen nodig om inzicht te krijgen in de werking van uw web-app onder belasting. Het is essentieel om te controleren wanneer traceringen worden vastgelegd, omdat u weet wanneer de belasting test wordt uitgevoerd. Maar het interval voor wille keurige steek proeven kan dit missen.

In de volgende secties ziet u hoe dit scenario werkt:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Stap 1: het verkeer naar uw web-app genereren door een test voor webprestaties te starten

Als uw web-app al inkomend verkeer heeft of als u alleen hand matig verkeer wilt genereren, slaat u deze sectie over en gaat u verder met stap 2.

1. Selecteer in de Application Insights Portal de optie  >  **prestatie testen** configureren. 

1. Selecteer de knop **Nieuw** om een nieuwe prestatie test te starten.

   ![nieuwe prestatie test maken][create-performance-test]

1. Configureer in het deel venster **nieuwe prestatie test** de doel-URL voor de test. Accepteer alle standaard instellingen en selecteer vervolgens **test uitvoeren** om te beginnen met het uitvoeren van de load test.

    ![Laad test configureren][configure-performance-test]

    De nieuwe test wordt eerst in de wachtrij geplaatst, gevolgd door de status wordt *uitgevoerd*.

    ![De belasting test wordt verzonden en in de wachtrij geplaatst][load-test-queued]

    ![De belasting test wordt uitgevoerd][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Stap 2: een profilerings sessie op aanvraag starten

1. Wanneer de belasting test wordt uitgevoerd, start u Profiler voor het vastleggen van traceringen voor de web-app terwijl de belasting wordt ontvangen.

1. Ga naar het deel venster **Profiler configureren** .


### <a name="step-3-view-traces"></a>Stap 3: traceringen weer geven

Als Profiler is voltooid, volgt u de instructies in de melding om naar het deel venster prestaties te gaan en traceringen weer te geven.

## <a name="next-steps"></a>Volgende stappen
[Profiler inschakelen en traceringen weer geven](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
