---
title: Een virtuele Azure-machine bewaken met Azure Monitor
description: Lees hier meer over het verzamelen en analyseren van gegevens voor een virtuele Azure-machine in Azure Monitor.
ms.service: azure-monitor
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 7efd8baf54aeacbd2f55640240a15f2517dcd904
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046921"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Een virtuele Azure-machine bewaken met Azure Monitor
[Azure Monitor](../overview.md) begint met het verzamelen van gegevens van virtuele Azure-machines op het moment dat ze worden gemaakt. In deze quickstart krijgt u een beknopt overzicht van de gegevens die automatisch worden verzameld voor een Azure-VM, en ziet u hoe u deze kunt weergeven in de Azure-portal. Vervolgens schakelt u [VM Insights](../vm/vminsights-overview.md) in voor uw virtuele machine, waarmee agents op de VM gegevens van het gast besturingssysteem kunnen verzamelen en analyseren, inclusief processen en hun afhankelijkheden.

In deze Quick Start wordt ervan uitgegaan dat u een bestaande Azure-VM hebt. Als dit niet het geval is, kunt u een [Windows-VM](../../virtual-machines/windows/quick-create-portal.md) of [Linux-VM](../../virtual-machines/linux/quick-create-cli.md) maken door de stappen te volgen in de VM-quickstarts.

Raadpleeg [Virtuele Azure-machines bewaken met Azure Monitor](./monitor-vm-azure.md) voor meer gedetailleerde beschrijvingen van het bewaken van gegevens die zijn verzameld uit Azure-resources.


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Voltooi de quickstart Een Azure-resource bewaken.
Voltooi [Een Azure-resource bewaken met Azure Monitor](../essentials/quick-monitor-azure-resource.md) om de overzichtspagina, het activiteitenlogboek, en de metrische gegevens voor een VM in uw abonnement te bekijken. Azure-VM's verzamelen dezelfde bewakingsgegevens als elke andere Azure-resource, maar dit geldt alleen voor de host-VM. In de rest van deze quickstart ligt de focus op het bewaken van het gastbesturingssysteem en de bijbehorende workloads.


## <a name="enable-vm-insights"></a>VM Insights inschakelen
De metrische gegevens en activiteitenlogboeken worden verzameld voor de host-VM, maar u hebt een agent en een configuratie nodig om bewakingsgegevens van het gastbesturingssysteem en de bijbehorende workloads te verzamelen en te analyseren. Met VM Insights worden deze agents geïnstalleerd en beschikt u over extra krachtige functies voor het bewaken van uw virtuele machines.

1. Ga naar het menu voor de virtuele machine.
2. Klik in de tegel op de **Overzichtspagina** op **Ga naar Insights**, of klik in het menu **Bewaking** op **Insights**.

    ![Overzichtspagina](media/quick-monitor-azure-vm/overview-insights.png)

3. Als VM Insights nog niet is ingeschakeld voor de virtuele machine, klikt u op **inschakelen**. 

    ![Insights inschakelen](media/quick-monitor-azure-vm/enable-insights.png)

4. Als de virtuele machine nog niet is gekoppeld aan een Log Analytics-werkruimte, wordt u gevraagd om een bestaande werkruimte te selecteren of een nieuwe werkruimte te maken. Selecteer de standaardwaarde. Dit is een werkruimte met een unieke naam in dezelfde regio als de virtuele machine.

    ![Werkruimte selecteren](media/quick-monitor-azure-vm/select-workspace.png)

5. Onboarding duurt enkele minuten, omdat extensies moeten worden ingeschakeld en agents moeten worden geïnstalleerd op de virtuele machine. Wanneer dit is voltooid, ontvangt u een bericht dat de implementatie van Insights is geslaagd. Klik op **Azure monitor** om VM Insights te openen.

    ![Azure Monitor openen](media/quick-monitor-azure-vm/azure-monitor.png)

6. U ziet de VM met eventuele andere VM’s in uw abonnement waarvoor onboarding is uitgevoerd. Selecteer het tabblad **Niet bewaakt** als u virtuele machines in uw abonnement wilt weergeven waarvoor onboarding niet is uitgevoerd.

    ![Aan de slag](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Werkruimte configureren
Wanneer u een nieuwe Log Analytics-werkruimte maakt, moet deze worden geconfigureerd om logboeken te verzamelen. Deze configuratie hoeft slechts eenmaal te worden uitgevoerd, omdat de configuratie wordt verzonden naar alle virtuele machines waarmee verbinding wordt gemaakt.

1. Selecteer **Werkruimteconfiguratie** en selecteer vervolgens uw werkruimte.

2. Selecteer **Geavanceerde instellingen**

    ![Geavanceerde instellingen van Log Analytics](../vm/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Gegevens verzamelen van virtuele Windows-machines


2. Selecteer **Gegevens** en selecteer vervolgens **Windows-gebeurtenislogboeken**.

3. Voeg een gebeurtenislogboek toe door de naam van het logboek te typen.  Typ **System** en selecteer vervolgens het plusteken **+**.

4. Schakel in de tabel de ernstcategorieën **Fout** en **Waarschuwing** in.

5. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

### <a name="data-collection-from-linux-vm"></a>Gegevens verzamelen van virtuele Linux-machines

1. Selecteer **Gegevens** en selecteer vervolgens **Syslog**.

2. Voeg een gebeurtenislogboek toe door de naam van het logboek te typen.  Typ **Syslog** en selecteer vervolgens het plusteken **+**.  

3. Schakel in de tabel de ernstcategorieën **Info**, **Kennisgeving** en **Fouten opsporen** uit. 

4. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

## <a name="view-data-collected"></a>Verzamelde gegevens weergeven

7. Klik op de virtuele machine, en selecteer vervolgens het tabblad **Prestaties** onder de tegel **Insights** in het menu **Bewaking**. U ziet nu een groep geselecteerde prestatiemeters die zijn verzameld uit het gastbesturingssysteem van de VM. Schuif omlaag om meer meters te bekijken, en beweeg de muis over een grafiek om het gemiddelde en de percentielen te zien op verschillende tijdstippen.

    ![Schermopname met het deelvenster Prestaties.](media/quick-monitor-azure-vm/performance.png)

9. Selecteer **Kaart** om de kaartfunctie te openen. Hiermee worden de processen weergegeven die worden uitgevoerd op de virtuele machine, en de bijbehorende afhankelijkheden. Selecteer **Eigenschappen** om het eigenschappendeelvenster te openen als dit nog niet is geopend.

    ![Schermopname met het deelvenster Kaart.](media/quick-monitor-azure-vm/map.png)

11. Vouw de processen voor uw virtuele machine uit. Selecteer een van de processen om de details te bekijken en de bijbehorende afhankelijkheden te markeren.

    ![Schermopname met het deelvenster Kaart met de uitgevouwen processen voor een virtuele machine.](media/quick-monitor-azure-vm/processes.png)

12. Selecteer de virtuele machine opnieuw, en selecteer vervolgens **Gebeurtenissen vastleggen in logboek**. 

    ![Gebeurtenissen vastleggen in logboek](media/quick-monitor-azure-vm/log-events.png)

13. U ziet een lijst met tabellen die zijn opgeslagen in de Log Analytics-werkruimte voor de virtuele machine. Deze lijst verschilt afhankelijk van of u een virtuele Windows- of Linux-machine gebruikt. Klik op de tabel **Gebeurtenis**. Dit omvat alle gebeurtenissen uit het Windows-gebeurtenislogboek. Log Analytics wordt geopend met een eenvoudige query om logboekvermeldingen op te halen.

    ![Logboekanalyses](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u VM Insights ingeschakeld voor een virtuele machine en de Log Analytics-werk ruimte geconfigureerd voor het verzamelen van gebeurtenissen voor het gast besturingssysteem. Voor informatie over het weergeven en analyseren van de gegevens gaat u verder met de zelfstudie.

> [!div class="nextstepaction"]
> [Gegevens weergeven of analyseren in Log Analytics](../logs/log-analytics-tutorial.md)