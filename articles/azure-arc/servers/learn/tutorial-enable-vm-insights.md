---
title: 'Zelfstudie: Een hybride machine bewaken met Azure Monitor VM-inzichten'
description: Lees hier meer over het verzamelen en analyseren van gegevens voor een hybride machine in Azure Monitor.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: f59ad448440110e2c5e6dd1fa1b2858d9cf42e91
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834260"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-vm-insights"></a>Zelfstudie: Een hybride machine bewaken met VM-inzichten

[Azure Monitor](../../../azure-monitor/overview.md) kan gegevens rechtstreeks vanuit uw hybride machines verzamelen en onderbrengen in een Log Analytics-werkruimte voor uitvoerige analyse en correlatie. Dit betekent meestal dat de [Log Analytics-agent](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) op de machine wordt geïnstalleerd met behulp van een script, handmatig of via een geautomatiseerde methode volgens de standaarden voor configuratiebeheer. Op Arc-servers is onlangs ondersteuning geïntroduceerd voor het installeren van de VM-extensies voor Log Analytics en Dependency Agent voor Windows en Linux, waardoor [VM-inzichten](../../../azure-monitor/vm/vminsights-overview.md) kunnen worden verzameld van uw niet-Azure-VM's. [](../manage-vm-extensions.md)

In deze zelfstudie leert u hoe u gegevens van uw Linux- of Windows-machines configureert en verzamelt door VM-inzichten in te stellen na een vereenvoudigde reeks stappen, die de ervaring stroomlijnen en een kortere tijd duren.  

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* De functionaliteit van de VM-extensie is alleen beschikbaar in de lijst met [ondersteunde regio's](../overview.md#supported-regions).

* Zie [Ondersteunde besturingssystemen om](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) ervoor te zorgen dat het besturingssysteem van de servers dat u inschakelen, wordt ondersteund door VM-inzichten.

* Bekijk de firewallvereisten voor de Log Analytics-agent in het [overzicht van de Log Analytics-agent](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements). De map dependency agent voor VM-inzichten verzendt zelf geen gegevens en vereist geen wijzigingen in firewalls of poorten.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-vm-insights"></a>VM-inzichten inschakelen

1. Start de Azure Arc-service in Azure Portal door op **Alle services** te klikken en dan **Machines - Azure Arc** te zoeken en te selecteren.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Arc-servers zoeken in Alle services" border="false":::

1. Selecteer op de pagina **Machines - Azure Arc** de verbonden machine die u in het artikel [Quickstart](quick-enable-hybrid-vm.md) hebt gemaakt.

1. Selecteer in het linkerdeelvenster onder de sectie **Bewaking** de optie **Inzichten** en vervolgens **Inschakelen**.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="De optie Inzichten in het linkermenu selecteren" border="false":::

1. Op de pagina **Inzichten onboarden** van Azure Monitor wordt u gevraagd om een werkruimte te maken. Voor deze zelfstudie raden wij u niet aan een bestaande Log Analytics-werkruimte te selecteren als u er al een hebt. Selecteer de standaardwaarde. Dit is een werkruimte met een unieke naam in dezelfde regio als de geregistreerde verbonden machine. Deze werkruimte wordt voor u gemaakt en geconfigureerd.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="De pagina VM-inzichten inschakelen" border="false":::

1. U ontvangt statusberichten wanneer de configuratie wordt uitgevoerd. Dit proces duurt enkele minuten omdat uitbreidingen op de verbonden computer worden geïnstalleerd.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Bericht over voortgangsstatus van VM-inzichten inschakelen" border="false":::

    Wanneer het proces is voltooid, wordt er een bericht weergegeven dat de onboarding van de machine is geslaagd en dat het inzicht is geïmplementeerd.

## <a name="view-data-collected"></a>Verzamelde gegevens weergeven

Wanneer de implementatie en configuratie zijn voltooid, selecteert u **Inzichten** en vervolgens het tabblad **Prestaties**. Op het tabblad 'Prestaties' ziet u een selecte groep van prestatiemeters die zijn verzameld uit het gastbesturingssysteem van uw machine. Schuif omlaag om meer tellers weer te geven en beweeg de muis over een grafiek om het gemiddelde en de percentielen weer te geven die worden uitgevoerd vanaf het moment waarop de Log Analytics VM-extensie op de computer is geïnstalleerd.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Prestatiegrafieken voor VM-inzichten voor de geselecteerde machine" border="false":::

Selecteer **Kaart** om de kaartfunctie te openen. Hiermee worden de processen weergegeven die worden uitgevoerd op de machine en de bijbehorende afhankelijkheden. Selecteer **Eigenschappen** om het eigenschappendeelvenster te openen als dit nog niet is geopend.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="VM-inzichtenkaart voor geselecteerde machine" border="false":::

Breid de processen voor uw virtuele machine uit. Selecteer een van de processen om de details te bekijken en de bijbehorende afhankelijkheden te markeren.

Selecteer uw machine opnieuw en selecteer vervolgens **Gebeurtenissen vastleggen**. U ziet een lijst met tabellen die zijn opgeslagen in de Log Analytics-werkruimte voor de machine. Deze lijst verschilt afhankelijk van of u een Windows- of Linux-machine gebruikt. Selecteer de tabel **Gebeurtenis**. De tabel **Gebeurtenis** omvat alle gebeurtenissen uit het Windows-gebeurtenislogboek. Log Analytics wordt geopend met een eenvoudige query om verzamelde logboekvermeldingen voor gebeurtenissen op te halen.

## <a name="next-steps"></a>Volgende stappen

Bekijk het volgende artikel voor meer informatie over Azure Monitor:

> [!div class="nextstepaction"]
> [Overzicht van Azure Monitor](../../../azure-monitor/overview.md)