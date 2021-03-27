---
title: Fouten opsporen in azure Service Fabric-apps in Windows
description: Meer informatie over het bewaken en diagnosticeren van uw services die zijn geschreven met behulp van Microsoft Azure Service Fabric op een lokale ontwikkel machine.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: a2502c24ef233c286872e2c265dcfdae6883f8ed
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628829"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Services controleren en diagnosticeren in een ontwikkelomgeving met lokale machines
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Voor het bewaken, detecteren, diagnosticeren en oplossen van problemen kunnen services worden voortgezet met een minimale onderbreking van de gebruikers ervaring. Terwijl bewaking en diagnostische gegevens kritiek zijn in een productie omgeving die daad werkelijk wordt geïmplementeerd, is de efficiëntie afhankelijk van het aannemen van een vergelijkbaar model tijdens de ontwikkeling van services om ervoor te zorgen dat ze werken wanneer u overstapt op een installatie in de praktijk. Service Fabric maakt het eenvoudig voor service ontwikkelaars om diagnostische gegevens te implementeren die naadloos kunnen worden gebruikt voor de installatie van lokale en productie clusters met één computer.

## <a name="event-tracing-for-windows"></a>Event Tracing for Windows
[Event Tracing for Windows](/windows/win32/etw/event-tracing-portal) (etw) is de aanbevolen technologie voor het traceren van berichten in service Fabric. Enkele voor delen van het gebruik van ETW zijn:

* **ETW is snel.** Het is gebouwd als tracerings technologie die minimale gevolgen heeft voor de uitvoering van code.
* **ETW-tracering werkt naadloos in lokale ontwikkel omgevingen en ook in de configuratie van het echte cluster.** Dit betekent dat u de tracerings code niet hoeft te herschrijven wanneer u klaar bent voor het implementeren van uw code naar een echt cluster.
* **Service Fabric systeem code gebruikt ook ETW voor interne tracering.** Zo kunt u uw toepassings traceringen Interleaved weer geven met Service Fabric systeem traceringen. Het helpt u ook om meer inzicht te krijgen in de reeksen en relaties tussen uw toepassings code en gebeurtenissen in het onderliggende systeem.
* **Er is ingebouwde ondersteuning in Service Fabric Visual Studio tools om ETW-gebeurtenissen weer te geven.** ETW-gebeurtenissen worden weer gegeven in de weer gave diagnostische gebeurtenissen van Visual Studio zodra Visual Studio correct is geconfigureerd met Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Service Fabric systeem gebeurtenissen weer geven in Visual Studio
Service Fabric ETW-gebeurtenissen meenemen om toepassings ontwikkelaars te helpen begrijpen wat er gebeurt in het platform. Als u dit nog niet hebt gedaan, gaat u verder met de stappen in het [maken van uw eerste toepassing in Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Deze informatie helpt u bij het uitvoeren van een toepassing met de diagnostische gebeurtenissen viewer die de traceer berichten weergeeft.

1. Als het venster diagnostische gebeurtenissen niet automatisch wordt weer gegeven, gaat u naar het tabblad **weer gave** in Visual Studio, kiest u **andere Windows** en vervolgens de **Viewer voor diagnostische gebeurtenissen**.
2. Elke gebeurtenis heeft standaard informatie over de meta gegevens waarmee u het knoop punt, de toepassing en de service van waaruit de gebeurtenis afkomstig is. U kunt ook de lijst met gebeurtenissen filteren met behulp van het vak **filter gebeurtenissen** aan de bovenkant van het venster gebeurtenissen. U kunt bijvoorbeeld filteren op de naam van het **knoop punt** of de **service naam.** En wanneer u de details van de gebeurtenis bekijkt, kunt u ook pauzeren met behulp van de knop **pause** boven in het venster gebeurtenissen en het later hervatten zonder dat er gebeurtenissen verloren gaan.
   
   ![Viewer voor diagnostische gegevens van Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Uw eigen aangepaste traceringen toevoegen aan de toepassings code
De Service Fabric Visual Studio-project sjablonen bevatten voorbeeld code. De code laat zien hoe u aangepaste toepassings code ETW-traceringen kunt toevoegen die worden weer gegeven in de Visual Studio ETW viewer naast systeem traceringen van Service Fabric. Het voor deel van deze methode is dat meta gegevens automatisch worden toegevoegd aan traceringen en dat de Visual Studio diagnostische gebeurtenis Viewer al is geconfigureerd om ze weer te geven.

Voor projecten die zijn gemaakt op basis van de **service Sjablonen** (stateless of stateful), zoekt u alleen naar de `RunAsync` implementatie:

1. De aanroep van `ServiceEventSource.Current.ServiceMessage` in de- `RunAsync` methode toont een voor beeld van een aangepaste etw-tracering van de toepassings code.
2. In het bestand **ServiceEventSource. cs** vindt u een overbelasting voor de `ServiceEventSource.ServiceMessage` methode die moet worden gebruikt voor gebeurtenissen met hoge frequentie als gevolg van de oorzaak van de prestaties.

Voor projecten die zijn gemaakt op basis van de **actor sjablonen** (stateless of stateful):

1. Open het bestand **' ProjectName". cs '** , waarbij *ProjectName* de naam is die u hebt gekozen voor uw Visual Studio-project.  
2. Zoek de code `ActorEventSource.Current.ActorMessage(this, "Doing Work");` in de methode *DoWorkAsync* .  Dit is een voor beeld van een aangepaste ETW-tracering die is geschreven vanuit toepassings code.  
3. In het bestand **ActorEventSource. cs** vindt u een overbelasting voor de `ActorEventSource.ActorMessage` methode die moet worden gebruikt voor gebeurtenissen met hoge frequentie als gevolg van de oorzaak van de prestaties.

Nadat u aangepaste ETW-tracering aan uw service code hebt toegevoegd, kunt u de toepassing opnieuw bouwen, implementeren en uitvoeren om uw gebeurtenis (sen) in de viewer voor diagnostische gegevens weer te geven. Als u fouten opspoort in de toepassing met **F5**, wordt de viewer voor diagnostische gebeurtenissen automatisch geopend.

## <a name="next-steps"></a>Volgende stappen
Dezelfde tracerings code die u hebt toegevoegd aan uw toepassing hierboven voor lokale diagnoses, werkt met hulpprogram ma's die u kunt gebruiken om deze gebeurtenissen te bekijken wanneer u uw toepassing uitvoert op een Azure-cluster. Bekijk deze artikelen over de verschillende opties voor de hulpprogram ma's en beschrijf hoe u deze kunt instellen.

* [Logboeken verzamelen met Azure Diagnostics](./service-fabric-diagnostics-event-aggregation-wad.md)
* [Gebeurtenis aggregatie en verzameling met behulp van Event flow](service-fabric-diagnostics-event-aggregation-eventflow.md)
