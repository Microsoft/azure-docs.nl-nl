---
title: HTTP 502-en HTTP 503-fouten oplossen
description: Problemen oplossen met 502 ongeldige gateway en de 503-Service niet-beschik bare fouten in uw app die wordt gehost in Azure App Service.
tags: top-support-issue
keywords: 502 ongeldige gateway, 503-Service niet beschikbaar, fout 503, fout 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 801f4840d0d1447f839e5c2c4bc3168741b62b63
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100586250"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Problemen met HTTP-fouten van "502 ongeldige gateway" en "503-Service niet beschikbaar" in Azure App Service
"502 ongeldige gateway" en "503-Service niet beschikbaar" zijn veelvoorkomende fouten in uw app die wordt gehost in [Azure app service](./overview.md). Dit artikel helpt u bij het oplossen van deze fouten.

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en de stack overflow-forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en klik op **Ontvang ondersteuning**.

## <a name="symptom"></a>Symptoom
Wanneer u naar de app bladert, wordt een HTTP-fout ' 502-ongeldige gateway ' weer gegeven of is de fout melding HTTP "503-Service niet beschikbaar".

## <a name="cause"></a>Oorzaak
Dit probleem wordt vaak veroorzaakt door problemen op toepassings niveau, zoals:

* aanvragen nemen een lange tijd in beslag
* toepassing met hoge geheugen/CPU
* toepassing is vastgelopen vanwege een uitzonde ring.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Probleemoplossings stappen voor het oplossen van fouten met de fout "502 ongeldige gateway" en "503-Service niet beschikbaar"
Probleem oplossing kan worden onderverdeeld in drie afzonderlijke taken, in sequentiële volg orde:

1. [Gedrag van de toepassing observeren en bewaken](#observe)
2. [Gegevens verzamelen](#collect)
3. [Het probleem oplossen](#mitigate)

[App service](overview.md) biedt u verschillende opties bij elke stap.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. gedrag van de toepassing observeren en bewaken
#### <a name="track-service-health"></a>Service status bijhouden
Microsoft Azure bekendmaking telkens wanneer er sprake is van een onderbreking van de service of prestaties. U kunt de status van de service in de [Azure-Portal](https://portal.azure.com/)volgen. Zie [service status bijhouden](../service-health/service-notifications.md)voor meer informatie.

#### <a name="monitor-your-app"></a>Uw app controleren
Met deze optie kunt u nagaan of uw toepassing problemen ondervindt. Klik in de Blade van uw app op de tegel **aanvragen en fouten** . Op de Blade **metrische gegevens** ziet u alle metrische gegevens die u kunt toevoegen.

Enkele van de metrische gegevens die u mogelijk wilt bewaken voor uw app, zijn

* Gemiddelde werkset geheugen
* Gemiddelde reactie tijd
* CPU-tijd
* Werkset geheugen
* Aanvragen

![app bewaken bij het oplossen van HTTP-fouten van 502 ongeldige gateway en de 503-Service is niet beschikbaar](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Zie voor meer informatie:

* [Apps in Azure App Service bewaken](web-sites-monitor.md)
* [Waarschuwingen ontvangen](../azure-monitor/alerts/alerts-overview.md)

<a name="collect"></a>

### <a name="2-collect-data"></a>2. gegevens verzamelen
#### <a name="use-the-diagnostics-tool"></a>Het hulp programma voor diagnostische gegevens gebruiken
App Service biedt een intelligente en interactieve ervaring om u te helpen bij het oplossen van problemen met uw app zonder dat hiervoor configuratie is vereist. Wanneer u problemen ondervindt met uw app, wordt in het diagnostische hulp programma aangegeven wat er mis is met de juiste informatie om snel te kunnen werken en het probleem op te lossen.

Om toegang te krijgen tot App Service diagnostische gegevens, gaat u naar uw App Service app of App Service Environment in de [Azure Portal](https://portal.azure.com). Klik in de linkernavigatiebalk op **problemen vaststellen en oplossen**.

#### <a name="use-the-kudu-debug-console"></a>De kudu-console voor fout opsporing gebruiken
App Service wordt geleverd met een console voor fout opsporing die u kunt gebruiken voor het opsporen van fouten, het verkennen, uploaden van bestanden en JSON-eind punten voor het verkrijgen van informatie over uw omgeving. Dit wordt de *kudu-console* of het *SCM-dash board* voor uw app genoemd.

U kunt dit dash board openen door naar de koppeling te gaan **https:// &lt; uw app-naam>. scm.azurewebsites.net/**.

Enkele van de dingen die kudu biedt:

* omgevings instellingen voor uw toepassing
* logboek stroom
* diagnostische dump
* console voor fout opsporing, waarin u Power shell-cmdlets en Basic DOS-opdrachten kunt uitvoeren.

Een andere handige functie van kudu is dat, als uw toepassing uitzonde ringen voor de eerste kans verkrijgt, u kudu en het hulp programma SysInternals kunt gebruiken Procdump om geheugen dumps te maken. Deze geheugen dumps zijn moment opnamen van het proces en kunnen u vaak helpen om complexere problemen met uw app op te lossen.

Zie voor meer informatie over de beschik bare functies in kudu [Azure websites online-hulpprogram ma's waarover u moet weten](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. het probleem oplossen
#### <a name="scale-the-app"></a>De app schalen
In Azure App Service kunt u voor betere prestaties en door Voer de schaal aanpassen waarop u uw toepassing uitvoert. Voor het omhoog schalen van een app zijn twee gerelateerde acties vereist: het wijzigen van uw App Service plan in een hogere prijs categorie en het configureren van bepaalde instellingen nadat u bent overgeschakeld naar de hogere prijs categorie.

Zie [een app schalen in azure app service](manage-scale-up.md)voor meer informatie over schalen.

Daarnaast kunt u ervoor kiezen om uw toepassing uit te voeren op meer dan één exemplaar. Dit biedt niet alleen meer verwerkings mogelijkheden, maar biedt ook een zekere mate van fout tolerantie. Als het proces wordt uitgevoerd op één instantie, blijft de andere instantie nog steeds aanvragen verwerken.

U kunt de schaal instellen op hand matig of automatisch.

#### <a name="use-autoheal"></a>Automatisch herstellen gebruiken
Met auto Retoucheer wordt het werk proces voor uw app gerecycled op basis van de instellingen die u kiest (zoals configuratie wijzigingen, aanvragen, limieten op basis van geheugen of de tijd die nodig is om een aanvraag uit te voeren). In de meeste gevallen is het recyclen van het proces de snelste manier om een probleem op te lossen. Hoewel u de app altijd rechtstreeks vanuit Azure Portal kunt opnieuw opstarten, wordt deze automatisch door hergebruikt. U hoeft alleen maar enkele triggers toe te voegen aan het hoofd web.config voor uw app. Houd er rekening mee dat deze instellingen op dezelfde manier werken, zelfs als uw toepassing geen .NET-versie is.

Zie voor meer informatie [automatisch herstel van Azure web sites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>De app opnieuw starten
Dit is vaak de eenvoudigste manier om eenmalige problemen op te lossen. In [Azure Portal](https://portal.azure.com/)op de Blade van uw app hebt u de opties om uw app te stoppen of opnieuw op te starten.

 ![de app opnieuw starten voor het oplossen van HTTP-fouten met de 502-ongeldige gateway en de 503-Service is niet beschikbaar](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

U kunt uw app ook beheren met Azure Power shell. Zie [Azure PowerShell gebruiken met Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md) voor meer informatie.