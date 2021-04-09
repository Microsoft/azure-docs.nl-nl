---
title: Apps bewaken
description: Meer informatie over het bewaken van apps in Azure App Service met behulp van de Azure Portal. Meer informatie over de quota's en metrische gegevens die worden gerapporteerd.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 04/23/2020
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: bf230032afe80680dc392c2a74da2a5aef381983
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586100"
---
# <a name="monitor-apps-in-azure-app-service"></a>Apps in Azure App Service bewaken
[Azure app service](./overview.md) biedt ingebouwde functionaliteit voor het controleren van web apps, mobiele apps en api's in de [Azure Portal](https://portal.azure.com).

In de Azure Portal kunt u *quota's* en *metrische gegevens* voor een app bekijken en app service plannen, en *waarschuwingen* en regels voor automatisch *schalen* instellen op basis van metrische gegevens.

## <a name="understand-quotas"></a>Meer informatie over quota's

Voor apps die worden gehost in App Service gelden bepaalde beperkingen voor de resources die ze kunnen gebruiken. De limieten worden gedefinieerd door het App Service plan dat is gekoppeld aan de app.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Als de app wordt gehost in een *gratis* of *gedeeld* abonnement, worden de limieten voor de resources die de app kan gebruiken door quota gedefinieerd.

Als de app wordt gehost in een *Basic*-, *Standard*-of *Premium* -abonnement, worden de limieten voor de resources die ze kunnen gebruiken, ingesteld op de *grootte* (klein, gemiddeld, groot) en aantal *instanties* (1, 2, 3, enzovoort) van het app service plan.

Quota voor gratis of gedeelde apps zijn:

| Quota | Description |
| --- | --- |
| **CPU (kort)** | De hoeveelheid CPU die is toegestaan voor deze app in een interval van 5 minuten. Dit quotum wordt om de vijf minuten opnieuw ingesteld. |
| **CPU (dag)** | De totale hoeveelheid CPU die op een dag is toegestaan voor deze app. Dit quotum wordt om de 24 uur opnieuw ingesteld om middernacht UTC. |
| **Geheugen** | De totale hoeveelheid geheugen die is toegestaan voor deze app. |
| **Bandbreedte** | De totale hoeveelheid uitgaande band breedte die op een dag is toegestaan voor deze app. Dit quotum wordt om de 24 uur opnieuw ingesteld om middernacht UTC. |
| **Bestandssysteem** | De totale hoeveelheid toegestane opslag ruimte. |

Het enige quotum dat van toepassing is op apps die worden gehost in *Basic*, *Standard* en *Premium* , is bestands systeem.

Zie [service limieten voor Azure-abonnementen](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor meer informatie over de specifieke quota's, limieten en functies die beschikbaar zijn voor de verschillende app service sku's.

### <a name="quota-enforcement"></a>Quota afdwingen

Als een app de *CPU (kort)*, *CPU (dag)* of *bandbreedte* quotum overschrijdt, wordt de app gestopt totdat het quotum opnieuw wordt ingesteld. Gedurende deze periode resulteert alle inkomende aanvragen in een HTTP 403-fout.

![403-fout bericht][http403]

Als het geheugen quotum van de app wordt overschreden, wordt de app tijdelijk gestopt.

Als het bestandssysteem quotum wordt overschreden, mislukt elke schrijf bewerking. Fouten bij schrijf bewerkingen zijn onder andere schrijf bewerkingen naar Logboeken.

U kunt quota's verhogen of verwijderen uit uw app door uw App Service-abonnement bij te werken.

## <a name="understand-metrics"></a>Metrische gegevens begrijpen

> [!NOTE]
> Het gebruik van het **Bestands systeem** is een nieuwe waarde die wereld wijd wordt getotaliseerd. er worden geen gegevens verwacht, tenzij uw app wordt gehost in een app service environment.
> 

> [!IMPORTANT]
> De **gemiddelde reactie tijd** wordt afgeschaft om Verwar ring met metrische aggregaties te voor komen. Gebruik de **reactie tijd** als vervanging.

> [!NOTE]
> Metrische gegevens voor een app bevatten de aanvragen voor de SCM-site van de app (kudu).  Dit omvat aanvragen om de logstream van de site weer te geven met behulp van kudu.  Logstream-aanvragen kunnen enkele minuten duren, wat van invloed is op de metrische aanvraag tijd.  Gebruikers moeten op de hoogte zijn van deze relatie wanneer ze deze metrische gegevens gebruiken met logica voor automatisch schalen.
> 

Metrische gegevens geven informatie over de app of het gedrag van het App Service plan.

De beschik bare metrische gegevens voor een app zijn:

| Metrisch | Beschrijving |
| --- | --- |
| **Reactie tijd** | De tijd die nodig is voor het uitvoeren van aanvragen voor de app, in seconden. |
| **Gemiddelde reactie tijd (afgeschaft)** | De gemiddelde tijd die nodig is voor het verwerken van aanvragen in de app. |
| **Gemiddelde werkset geheugen** | De gemiddelde hoeveelheid geheugen die wordt gebruikt door de app, in mega bytes (MiB). |
| **Verbindingen** | Het aantal gekoppelde sockets dat in de sandbox is bestaande (w3wp.exe en de onderliggende processen).  Een gebonden socket wordt gemaakt door binding-Api's ()/Connect () aan te roepen en blijft totdat de andere socket is gesloten met CloseHandle ()/closesocket (). |
| **CPU-tijd** | De hoeveelheid CPU die wordt verbruikt door de app, in seconden. Zie [CPU-tijd versus CPU-percentage](#cpu-time-vs-cpu-percentage)voor meer informatie over deze metrische gegevens. |
| **Huidige Assembly's** | Het huidige aantal Assembly's dat is geladen in alle AppDomains in deze toepassing. |
| **Gegevens in** | De hoeveelheid inkomende band breedte die door de app wordt gebruikt in MiB. |
| **Gegevens uit** | De hoeveelheid uitgaande band breedte die door de app wordt gebruikt in MiB. |
| **Gebruik van bestands systeem** | De hoeveelheid gebruik in bytes door de opslag share. |
| **Schone verzamelingen van 0 gen** | Het aantal keren dat de generatie 0-objecten permanent zijn verzameld sinds het begin van het app-proces. Een hogere generatie GCs bevatten alle lagere GCs.|
| **1 garbagecollection-verzamelingen** | Het aantal keren dat de generatie 1-objecten permanent zijn verzameld sinds het begin van het app-proces. Een hogere generatie GCs bevatten alle lagere GCs.|
| **Opschoon verzamelingen van generatie 2** | Het aantal keren dat de generatie 2-objecten permanent zijn verzameld sinds het begin van het app-proces.|
| **Aantal ingangen** | Het totale aantal ingangen dat momenteel door het app-proces is geopend.|
| **Status van de status controle** | De gemiddelde integriteits status voor de instanties van de toepassing in het App Service plan.|
| **Http-2xx** | Het aantal aanvragen dat resulteert in een HTTP-status code ≥ 200, maar < 300. |
| **HTTP-3xx** | Het aantal aanvragen dat resulteert in een HTTP-status code ≥ 300, maar < 400. |
| **HTTP 401** | Het aantal aanvragen dat resulteert in de HTTP 401-status code. |
| **HTTP 403** | Het aantal aanvragen dat resulteert in de HTTP 403-status code. |
| **Http 404** | Het aantal aanvragen dat resulteert in de HTTP 404-status code. |
| **Http 406** | Het aantal aanvragen dat resulteert in de HTTP 406-status code. |
| **Http-4xx** | Het aantal aanvragen dat resulteert in een HTTP-status code ≥ 400, maar < 500. |
| **Http-server fouten** | Het aantal aanvragen dat resulteert in een HTTP-status code ≥ 500, maar < 600. |
| **Andere i/o-bytes per seconde** | De snelheid waarmee het app-proces bytes uitgeeft aan I/O-bewerkingen die geen gegevens omvatten, zoals controle bewerkingen.|
| **Andere i/o-bewerkingen per seconde** | De snelheid waarmee I/O-bewerkingen worden uitgevoerd die geen lees-of schrijf bewerkingen zijn.|
| **I/o gelezen bytes per seconde** | De snelheid waarmee het app-proces bytes van I/O-bewerkingen leest.|
| **I/o-Lees bewerkingen per seconde** | De snelheid waarmee het app-proces Lees-I/O-bewerkingen uitgeeft.|
| **I/o-schrijf bewerkingen in bytes per seconde** | De snelheid waarmee het app-proces bytes naar I/O-bewerkingen schrijft.|
| **I/o-schrijf bewerkingen per seconde** | De snelheid waarmee het app-proces I/O-schrijf bewerkingen uitgeeft.|
| **Werkset geheugen** | De huidige hoeveelheid geheugen die wordt gebruikt door de app in MiB. |
| **Privé-bytes** | Eigen bytes is de huidige grootte, in bytes, van het geheugen dat het app-proces heeft toegewezen en dat niet met andere processen kan worden gedeeld.|
| **Aanvragen** | Het totale aantal aanvragen, ongeacht de resulterende HTTP-status code. |
| **Aanvragen in de wachtrij van de toepassing** | Het aantal aanvragen in de wachtrij voor toepassings aanvragen.|
| **Aantal threads** | Het aantal threads dat momenteel actief is in het app-proces.|
| **Totaal aantal app-domeinen** | Het huidige aantal AppDomains dat is geladen in deze toepassing.|
| **Totaal aantal verwijderde app-domeinen** | Het totale aantal AppDomains dat sinds het begin van de toepassing is verwijderd.|


De beschik bare metrische gegevens voor een App Service plan zijn:

> [!NOTE]
> De metrische gegevens voor het App Service plan zijn alleen beschikbaar voor abonnementen in de lagen *Basic*, *Standard* en *Premium* .
> 

| Metrisch | Beschrijving |
| --- | --- |
| **CPU-percentage** | De gemiddelde CPU die wordt gebruikt voor alle exemplaren van het plan. |
| **Geheugen percentage** | Het gemiddelde geheugen dat wordt gebruikt voor alle exemplaren van het plan. |
| **Gegevens in** | De gemiddelde binnenkomende band breedte die wordt gebruikt voor alle exemplaren van het abonnement. |
| **Gegevens uit** | De gemiddelde uitgaande band breedte die wordt gebruikt voor alle exemplaren van het abonnement. |
| **Wachtrij lengte voor schijf** | Het gemiddelde aantal lees-en schrijf aanvragen dat in de wachtrij is geplaatst op opslag. Een hoge wachtrij lengte voor de schijf is een indicatie van een app die kan vertragen vanwege een buitensporige schijf-I/O. |
| **Lengte van http-wachtrij** | Het gemiddelde aantal HTTP-aanvragen dat aan de wachtrij is gewacht voordat wordt voldaan. Een hoge of verhoogde HTTP-wachtrij lengte is een symptoom van een plan onder zware belasting. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU-tijd versus CPU-percentage
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Er zijn twee metrische gegevens die het CPU-gebruik weer spie gelen:

**CPU-tijd**: handig voor apps die worden gehost in een gratis of gedeeld abonnement, omdat een van de quota's is gedefinieerd in CPU-minuten die door de app worden gebruikt.

**CPU-percentage**: handig voor apps die worden gehost in Basic-, Standard-en Premium-abonnementen, omdat ze kunnen worden uitgeschaald. CPU-percentage is een goede indicatie van het totale gebruik voor alle exemplaren.

## <a name="metrics-granularity-and-retention-policy"></a>Granulatie van metrische gegevens en bewaar beleid
Metrische gegevens voor een app-en app service-plan worden geregistreerd en geaggregeerd door de service en [bewaard volgens deze regels](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics).

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Quota en metrische gegevens bewaken in de Azure Portal
Als u de status van de verschillende quota's en metrische gegevens wilt bekijken die van invloed zijn op een app, gaat u naar de [Azure Portal](https://portal.azure.com).

![De grafiek quota's in het Azure Portal][quotas]

Selecteer **instellingen** quota's om quota's te vinden  >  . In de grafiek kunt u het volgende controleren: 
1. De naam van het quotum.
1. Het interval voor opnieuw instellen.
1. De huidige limiet.
1. De huidige waarde.

![Grafiek met metrische gegevens in de Azure Portal ][metrics] hebt u rechtstreeks toegang tot metrische gegevens via de pagina resource **overzicht** . Hier ziet u grafieken die enkele van de metrische gegevens van de app vertegenwoordigen.

Wanneer u op een van deze grafieken klikt, wordt u naar de weer gave metrische gegevens geleid, waar u aangepaste grafieken kunt maken, verschillende metrische gegevens en nog veel meer wilt opvragen. 

Zie [metrische service gegevens bewaken](../azure-monitor/data-platform.md)voor meer informatie over metrische gegevens.

## <a name="alerts-and-autoscale"></a>Waarschuwingen en automatisch schalen
Metrische gegevens voor een app of een App Service plan kunnen worden aangesloten op waarschuwingen. Zie [Meldingen van waarschuwingen ontvangen](../azure-monitor/alerts/alerts-classic-portal.md) voor meer informatie.

App Service-apps die worden gehost in basis-of snellere App Service plannen ondersteunen automatisch schalen. Met automatisch schalen kunt u regels configureren die de metrische gegevens van het App Service plan bewaken. Regels kunnen het aantal instanties verg Roten of verkleinen, zodat u indien nodig aanvullende bronnen kunt opgeven. Regels kunnen u helpen bij het besparen van geld wanneer de app te veel is ingericht.

Zie [How to scale](../azure-monitor/autoscale/autoscale-get-started.md) and [Best practices for Azure monitor automatisch schalen](../azure-monitor/autoscale/autoscale-best-practices.md)voor meer informatie over automatisch schalen.

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
