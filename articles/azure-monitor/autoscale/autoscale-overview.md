---
title: Automatisch schalen in Microsoft Azure
description: Automatisch schalen in Microsoft Azure
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4727d562e21b92e58c8091f1161cf53198ff0b26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725999"
---
# <a name="overview-of-autoscale-in-microsoft-azure"></a>Overzicht van automatische schaalaanpassing in Microsoft Azure
In dit artikel wordt beschreven wat Microsoft Azure automatisch schalen is, wat de voor delen zijn en hoe u het kunt gaan gebruiken.  

Azure Monitor automatisch schalen is alleen van toepassing op [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web apps](https://azure.microsoft.com/services/app-service/web/), [API Management Services](../../api-management/api-management-key-concepts.md)en [Azure Data Explorer clusters](/azure/data-explorer/).

> [!NOTE]
> Azure heeft twee methoden voor automatisch schalen. Een oudere versie van automatisch schalen is van toepassing op Virtual Machines (beschikbaarheids sets). Deze functie biedt beperkte ondersteuning en wij raden u aan om te migreren naar schaal sets voor virtuele machines voor snellere en betrouwbaardere ondersteuning voor automatisch schalen. In dit artikel vindt u een koppeling naar het gebruik van de oudere technologie.  
>

## <a name="what-is-autoscale"></a>Wat is automatisch schalen?
Met automatisch schalen kunt u de juiste hoeveelheid resources uitvoeren om de belasting van uw toepassing te verwerken. U kunt hiermee resources toevoegen voor het afhandelen van toename van de belasting en bespaart u geld door resources te verwijderen die niet actief zijn. U geeft een minimum en maximum aantal exemplaren op dat moet worden uitgevoerd en die automatisch moeten worden toegevoegd of verwijderd op basis van een set regels. Als u Mini maal hebt, moet uw toepassing altijd worden uitgevoerd, zelfs onder geen belasting. Als u een maximum hebt, beperkt u het totale aantal kosten per uur. U kunt automatisch schalen tussen deze twee extremees met behulp van de regels die u maakt.

 ![Automatisch schalen uitgelegd. VM's toevoegen en verwijderen](./media/autoscale-overview/AutoscaleConcept.png)

Wanneer aan de voor waarden van de regel wordt voldaan, worden een of meer acties voor automatisch schalen geactiveerd. U kunt Vm's toevoegen en verwijderen of andere acties uitvoeren. In het volgende conceptuele diagram ziet u dit proces.  

 ![Stroom diagram voor automatisch schalen](./media/autoscale-overview/Autoscale_Overview_v4.png)

De volgende uitleg is van toepassing op de onderdelen van het vorige diagram.   

## <a name="resource-metrics"></a>Metrische gegevens voor resource
Resources die metrische gegevens verzenden, worden deze metrische gegevens later verwerkt door regels. Metrische gegevens zijn beschikbaar via verschillende methoden.
Virtuele-machine schaal sets gebruiken telemetriegegevens van Azure Diagnostics-agents, terwijl telemetrie voor web-apps en Cloud Services rechtstreeks afkomstig is van de Azure-infra structuur. Enkele veelgebruikte statistieken zijn CPU-gebruik, geheugen gebruik, aantal threads, wachtrij lengte en schijf gebruik. Zie [algemene metrische gegevens automatisch schalen](autoscale-common-metrics.md)voor een lijst met de telemetriegegevens die u kunt gebruiken.

## <a name="custom-metrics"></a>Aangepaste metrische gegevens
U kunt ook uw eigen aangepaste metrische gegevens gebruiken die door uw toepassing (en) kunnen worden verzonden. Als u uw toepassing (en) hebt geconfigureerd voor het verzenden van metrische gegevens naar Application Insights, kunt u gebruikmaken van deze metrische gegevens om beslissingen te nemen over het al dan niet schalen.

## <a name="time"></a>Tijd
Regels op basis van een schema zijn gebaseerd op UTC. U moet uw tijd zone op de juiste wijze instellen bij het instellen van uw regels.  

## <a name="rules"></a>Regels
In het diagram wordt slechts één regel voor automatisch schalen weer gegeven, maar u kunt er veel van hebben. U kunt complexe overlappende regels maken die nodig zijn voor uw situatie.  Regel typen zijn  

* **Op basis van metrische gegevens** : u kunt bijvoorbeeld deze actie uitvoeren wanneer het CPU-gebruik hoger is dan 50%.
* **Op basis van tijd** , bijvoorbeeld een webhook activeren elke 8 A.m. op zaterdag in een bepaalde tijd zone.

Regels op basis van metrische gegevens meten het laden van toepassingen en het toevoegen of verwijderen van Vm's op basis van die belasting. Met regels op basis van een schema kunt u schalen wanneer er tijd patronen in de belasting worden weer gegeven en wilt schalen voordat een mogelijke belasting toename of afname optreedt.  

## <a name="actions-and-automation"></a>Acties en automatisering
Regels kunnen een of meer typen acties activeren.

* Vm's **in-of uitschalen**
* **E** -mail bericht verzenden naar abonnements beheerders, mede beheerders en/of aanvullend e-mail adres dat u opgeeft
* **Automatiseren via webhooks** -webhooks aanroepen, waarmee u meerdere complexe acties binnen of buiten Azure kunt activeren. In azure kunt u een Azure Automation runbook, Azure function of een Azure Logic-app starten. Voor beeld van een URL van een derde partij buiten Azure bevat services zoals toegestane vertraging en Twilio.

## <a name="autoscale-settings"></a>Instellingen voor automatisch schalen
Voor automatisch schalen worden de volgende terminologie en structuur gebruikt.

- Een **instelling voor automatisch schalen** wordt gelezen door de engine voor automatisch schalen om te bepalen of u omhoog of omlaag wilt schalen. Het bevat een of meer profielen, informatie over de doel bron en meldings instellingen.

  - Een **profiel voor automatisch schalen** is een combi natie van een:

    - de **capaciteits instelling**, waarmee de minimum-, maximum-en standaard waarden voor het aantal exemplaren worden aangegeven.
    - **set met regels**, elk met een trigger (tijd of metrische waarde) en een schaal actie (omhoog of omlaag).
    - **terugkeer patroon**, waarmee wordt aangegeven wanneer dit profiel door automatisch schalen moet worden geplaatst.

      U kunt meerdere profielen hebben, zodat u rekening moet houden met verschillende overlappende vereisten. U kunt bijvoorbeeld verschillende profielen voor automatisch schalen hebben voor verschillende tijdstippen van de dag of dagen van de week.

  - Een **instelling voor meldingen** definieert welke meldingen moeten worden uitgevoerd wanneer een gebeurtenis voor automatisch schalen plaatsvindt op basis van het voldoen aan de criteria van een van de profiel instellingen voor automatisch schalen. Met automatisch schalen kunt u een of meer e-mail adressen melden of aanroepen naar een of meer webhooks.


![Instelling, profiel en regel structuur voor Azure automatisch schalen](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

De volledige lijst met Configureer bare velden en beschrijvingen is beschikbaar in het [rest API automatisch schalen](/rest/api/monitor/autoscalesettings).

Zie voor code voorbeelden.

* [Geavanceerde configuratie voor automatisch schalen met Resource Manager-sjablonen voor VM Scale Sets](autoscale-virtual-machine-scale-sets.md)  
* [REST API automatisch schalen](/rest/api/monitor/autoscalesettings)

## <a name="horizontal-vs-vertical-scaling"></a>Horizon taal versus verticaal schalen
Automatisch schalen schaalt alleen horizon taal, wat een toename (' out ') of afneemt (in) in het aantal VM-exemplaren.  Horizon taal is flexibel in een Cloud situatie, omdat u mogelijk duizenden virtuele machines kunt uitvoeren om de belasting te verwerken.

Verticaal schalen daarentegen wijkt af van elkaar af. Het houdt hetzelfde aantal Vm's in, maar maakt de Vm's meer (' up ') of minder (' down ') krachtig. De kracht wordt gemeten in het geheugen, de CPU-snelheid, de schijf ruimte, enzovoort.  Verticaal schalen heeft meer beperkingen. Het is afhankelijk van de beschik baarheid van grotere hardware, waarmee snel een bovenlimiet kan worden bereikt en per regio kan variëren. Verticaal schalen vereist ook dat een virtuele machine wordt gestopt en opnieuw wordt opgestart.

## <a name="methods-of-access"></a>Toegangs methoden
U kunt automatisch schalen instellen via

* [Azure-portal](autoscale-get-started.md)
* [PowerShell](../powershell-samples.md#create-and-manage-autoscale-settings)
* [Platformonafhankelijke opdrachtregelinterface (CLI)](../cli-samples.md#autoscale)
* [Azure Monitor REST API](/rest/api/monitor/autoscalesettings)

## <a name="supported-services-for-autoscale"></a>Ondersteunde services voor automatisch schalen
| Service | & documenten voor schema |
| --- | --- |
| Web Apps |[Web Apps schalen](autoscale-get-started.md) |
| Cloud Services |[Een Cloud service automatisch schalen](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtual Machines: klassiek |[De beschik baarheid van klassieke virtuele machines schalen](/archive/blogs/kaevans/autoscaling-azurevirtual-machines) |
| Virtual Machines: Windows-schaal sets |[Schaal sets voor virtuele machines in Windows schalen](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtual Machines: Linux-schaal sets |[Schaal sets voor virtuele machines in Linux schalen](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtual Machines: Windows-voor beeld |[Geavanceerde configuratie voor automatisch schalen met Resource Manager-sjablonen voor VM Scale Sets](autoscale-virtual-machine-scale-sets.md) |
| Azure App Service |[Een app omhoog schalen in Azure-app service](../../app-service/manage-scale-up.md)|
| API Management-service|[Exemplaar van Azure API Management automatisch schalen](../../api-management/api-management-howto-autoscale.md)
| Azure Data Explorer-clusters|[Het schalen van Azure Data Explorer-clusters beheren voor het wijzigen van de vraag](/azure/data-explorer/manage-cluster-horizontal-scaling)|
| Logic Apps |[De capaciteit van de Integration service Environment (ISE) toevoegen](../../logic-apps/ise-manage-integration-service-environment.md#add-ise-capacity)|
| Spring Cloud |[Automatische schaalaanpassing instellen voor microservicetoepassingen](../../spring-cloud/spring-cloud-tutorial-setup-autoscale.md)|
| Service Bus |[Bericht eenheden van een Azure Service Bus naam ruimte automatisch bijwerken](../../service-bus-messaging/automate-update-messaging-units.md)|

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over automatisch schalen gebruikt u de instructies voor automatisch schalen die eerder zijn vermeld of raadpleegt u de volgende bronnen:

* [Azure Monitor automatisch schalen van algemene metrische gegevens](autoscale-common-metrics.md)
* [Best practices voor automatische schaalaanpassing in Azure Monitor](autoscale-best-practices.md)
* [Acties voor automatisch schalen gebruiken om e-mail berichten en webhook-waarschuwings meldingen te verzenden](autoscale-webhook-email.md)
* [REST API automatisch schalen](/rest/api/monitor/autoscalesettings)
* [Problemen oplossen Virtual Machine Scale Sets automatisch schalen](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)