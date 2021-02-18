---
title: Slimme detectie in Azure-toepassing Insights | Microsoft Docs
description: Application Insights voert een automatische diep gaande analyse van uw app-telemetrie uit en waarschuwt u mogelijke problemen.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 974795e853416ff7a4c051530b050fc663c9423d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585658"
---
# <a name="smart-detection-in-application-insights"></a>Slimme detectie in Application Insights
 Slimme detectie waarschuwt automatisch mogelijke prestatie problemen en fout afwijkingen in uw webtoepassing. Er wordt proactieve analyse uitgevoerd van de telemetrie die uw app naar [Application Insights](./app-insights-overview.md)verzendt. Als er sprake is van een plotselinge toename van de fout frequentie of abnormale patronen in de prestaties van de client of server, ontvangt u een waarschuwing. Deze functie heeft geen configuratie nodig. Het werkt als uw toepassing voldoende telemetrie verzendt.

U hebt toegang tot de detecties die zijn uitgegeven door slimme detectie, zowel van het e-mail bericht dat u ontvangt, als op de Blade Slimme detectie.

## <a name="review-your-smart-detections"></a>Uw slimme detecties controleren
U kunt detecties op twee manieren detecteren:

* **U ontvangt een e-mail** van Application Insights. Hier volgt een kenmerkend voorbeeld:
  
    ![E-mail waarschuwing](./media/proactive-diagnostics/03.png)
  
    Klik op de knop Big om meer details te openen in de portal.
* **De Blade Slimme detectie** in Application Insights. Selecteer **Slimme detectie** onder het menu **onderzoeken** om een lijst met recente detecties weer te geven.

![Recente detecties weer geven](./media/proactive-diagnostics/04.png)

Selecteer een detectie om de details ervan weer te geven.

## <a name="what-problems-are-detected"></a>Welke problemen worden gedetecteerd?
Slimme detectie detecteert en waarschuwt over diverse problemen, zoals:

* [Slimme detectie-fout afwijkingen](./proactive-failure-diagnostics.md). We gebruiken machine learning om het verwachte aantal mislukte aanvragen voor uw app in te stellen en te correleren met belasting en andere factoren. Als het uitval aantal buiten de verwachte envelop komt, verzenden we een waarschuwing.
* [Slimme detectie: prestatie afwijkingen](./proactive-performance-diagnostics.md). U ontvangt meldingen als de reactie tijd van een bewerking of afhankelijkheids duur verloopt ten opzichte van de historische basis lijn of als we een afwijkend patroon identificeren in reactie tijd of pagina laadtijd.   
* Algemene degradatie en problemen, zoals [tracering afbraak](./proactive-trace-severity.md), [geheugenlek,](./proactive-potential-memory-leak.md) [abnormaal stijgen in uitzonderings volume](./proactive-exception-volume.md) en [beveiligings anti patronen](./proactive-application-security-detection-pack.md).

(De Help-koppelingen in elke melding nemen u naar de relevante artikelen.)

## <a name="smart-detection-email-notifications"></a>E-mail meldingen voor slimme detectie

Alle regels voor slimme detectie, met uitzonde ring van regels die als _Preview_ zijn gemarkeerd, worden standaard geconfigureerd voor het verzenden van e-mail meldingen wanneer detecties worden gevonden.

Het configureren van e-mail meldingen voor een specifieke detectie regel kan worden uitgevoerd door de Blade Slimme detectie- **instellingen** te openen en de regel te selecteren, waarmee de Blade **regel bewerken** wordt geopend.

U kunt de configuratie ook wijzigen met Azure Resource Manager sjablonen. [Zie Application Insights Slimme detectie regels beheren met behulp van Azure Resource Manager-sjablonen](./proactive-arm-config.md) voor meer informatie.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Volgende stappen
Met deze diagnostische hulpprogram ma's kunt u de telemetrie van uw app inspecteren:

* [Metrische Explorer](../essentials/metrics-charts.md)
* [Zoek Verkenner](./diagnostic-search.md)
* [Analyse-krachtige query taal](../logs/log-analytics-tutorial.md)

Slimme detectie is volledig automatisch. Maar misschien wilt u nog meer waarschuwingen instellen?

* [Hand matig geconfigureerde metrische waarschuwingen](../alerts/alerts-log.md)
* [Webtests voor beschikbaarheid](./monitor-web-app-availability.md)