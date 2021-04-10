---
title: Azure Automation gebruiken om uit te vinden welke software is geïnstalleerd op uw VM's | Microsoft Docs
description: In dit artikel wordt de software beschreven die is geïnstalleerd op virtuele machines in uw omgeving.
services: automation
keywords: inventaris, automation, wijzigingen bijhouden
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 8451067ae86e95269c0c2f22554e1654ac64c1e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593785"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>Uitvinden welke software is geïnstalleerd op uw VM's

In deze zelfstudie leert u hoe u de functies Wijzigingen bijhouden en Inventaris van Azure Automation kunt gebruiken om erachter te komen welke software in uw omgeving is geïnstalleerd. U kunt inventarisgegevens verzamelen en weergeven voor software, bestanden, Linux-daemons, Windows-services en Windows-registersleutels op uw computers. Door de configuraties van uw computer bij te houden, is het voor u gemakkelijker vast te stellen waar in uw omgeving zich operationele problemen voordoen, en kunt u beter beoordelen wat de status van uw computers is.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Wijzigingen bijhouden en Inventaris inschakelen
> * Een Azure-VM inschakelen
> * Een niet-Azure-VM inschakelen
> * Geïnstalleerde software weergeven
> * Inventarislogboeken doorzoeken op geïnstalleerde software

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een [Automation-account](./index.yml) voor het opslaan van de watcher- en actie-runbooks en de Watcher-taak.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md) die u voor de functie wilt inschakelen.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

Voor deze zelfstudie moet u eerst Wijzigingen bijhouden en Inventaris inschakelen. Als u de functie al eerder hebt ingeschakeld, is deze stap niet nodig.

>[!NOTE]
>Als de velden niet beschikbaar zijn, is er een andere Automation-functie ingeschakeld voor de VM en moet u dezelfde werkruimte en hetzelfde Automation-account gebruiken.

1. Navigeer naar uw Automation-account en selecteer **Inventaris** of **Wijzigingen bijhouden** onder **Configuratiebeheer**.

2. Kies de [Log Analytics](../azure-monitor/logs/log-query-overview.md)-werkruimte. In deze werkruimte worden gegevens verzameld die worden gegenereerd door functies als Wijzigingen bijhouden en Inventaris. De werkruimte biedt één locatie om gegevens uit meerdere bronnen te bekijken en te analyseren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Selecteer het Automation-account dat u wilt gebruiken.

4. Configureer de locatie voor de implementatie.

5. Klik op **Inschakelen** om de functie voor uw virtuele machine te implementeren. 

    ![Banner voor configuratie van Inventaris](./media/automation-tutorial-installed-software/enableinventory.png)

Tijdens de installatie wordt de virtuele machine ingericht met de Log Analytics-agent voor Windows en een [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Schakel Wijzigingen bijhouden en Inventaris in. Dit kan maximaal 15 minuten duren. Gedurende deze tijd mag u het browservenster niet sluiten.

Nadat de functie is ingeschakeld, wordt informatie over geïnstalleerde software en wijzigingen aan de VM-stromen naar Azure Monitor-logboeken verzonden. Het duurt tussen 30 minuten en 6 uur voordat de gegevens beschikbaar zijn voor analyse.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Een virtuele Azure-machine toevoegen voor Wijzigingen bijhouden en Inventaris

1. Navigeer in uw Automation-account naar **Inventaris** of **Wijzigingen bijhouden** onder **Configuratiebeheer**.

2. Selecteer **+ Azure-VM toevoegen**.

3. Selecteer uw virtuele machine in de lijst met VM's. 

4. Klik op **Inschakelen** om Wijzigingen bijhouden en Inventaris op de VM in te scha kelen. De Log Analytics-agent voor Windows wordt geïmplementeerd op de VM en configureert de virtuele machine om te communiceren met de Log Analytics-werkruimte. De installatiebewerking kan enkele minuten duren. 

5. Op dit moment kunt u desgewenst een nieuwe VM selecteren in de lijst die u wilt inschakelen voor de functie.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Een virtuele niet-Azure-machine toevoegen aan Wijzigingen bijhouden en Inventaris

Niet-Azure-machines inschakelen voor de functie:

1. Installeer de [Log Analytics-agent voor Windows](../azure-monitor/agents/agent-windows.md) of [Log Analytics-agent voor Linux](automation-linux-hrw-install.md), afhankelijk van uw besturingssysteem. 

2. Navigeer naar uw Automation-account en ga naar **Inventaris** of **Wijzigingen bijhouden** onder **Configuratiebeheer**. 

3. Klik op **Machines beheren**. Er wordt een lijst weergegeven van de computers die rapporteren aan uw Log Analytics-werkruimte en waarvoor Wijzigingen bijhouden en Inventaris niet is ingeschakeld. Selecteer de gewenste optie voor uw omgeving:

    * **Inschakelen op alle beschikbare machines**: met deze optie schakelt u de functie in op alle computers die momenteel aan uw Log Analytics-werkruimte rapporteren.
    * **Inschakelen op alle beschikbare machines en toekomstige machines**: met deze optie schakelt u de functie in op alle computers die momenteel aan uw Log Analytics-werkruimte rapporteren en op alle toekomstige computers die aan de werkruimte worden toegevoegd.
    * **Inschakelen op de geselecteerde machines**: met deze optie schakelt u de functie alleen in op de machines die u hebt geselecteerd.

    ![Machines beheren](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Geïnstalleerde software weergeven

Zodra de functies Wijzigingen bijhouden en Inventaris zijn ingeschakeld, kunt u de resultaten op de pagina Inventaris bekijken.

1. Selecteer in uw Automation-account **Inventaris** onder  **Configuratiebeheer**.

2. Op de pagina Inventaris klikt u op het tabblad **Software**.

3. U ziet een tabel waarin de gevonden software wordt weergegeven. De software is gegroepeerd op de naam en versie van de software. De algemene details voor elke softwarerecord worden in de tabel weergegeven. Deze gegevens omvatten de naam van de software, de versie, uitgever, tijd laatst vernieuwd (het meest recente tijdstip waarop is vernieuwd, gemeld door een computer in de groep) en de computers (het aantal computers met die software).

    ![Software-inventaris](./media/automation-tutorial-installed-software/inventory-software.png)

4. Klik op een rij om de eigenschappen van de softwarerecord te bekijken evenals de namen van de computers met die software.

5. Als u wilt zoeken naar specifieke software of de softwaregroep, kunt u dat doen via het tekstvak direct boven de lijst met software.
Het filter kunt u gebruiken om te zoeken op basis van de naam van de software, de versie of de uitgever. Als u bijvoorbeeld zoekt naar **Contoso**, wordt alle software met **Contoso** in de naam, uitgever of versie geretourneerd.

## <a name="search-inventory-logs-for-installed-software"></a>Inventarislogboeken doorzoeken op geïnstalleerde software

Wijzigingen bijhouden en Inventaris genereren logboekgegevens die naar Azure Monitor-logboeken worden verzonden. Als u de logboeken wilt doorzoeken door query's uit te voeren, selecteert u **Log Analytics** boven in pagina Inventaris. Inventarisgegevens worden opgeslagen onder het type `ConfigurationData`.

De volgende voorbeeldquery voor Log Analytics retourneert de inventarisatieresultaten voor de uitgever Microsoft Corporation.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Zie [Azure Monitor-logboeken](../azure-monitor/logs/log-query-overview.md) voor meer informatie over het uitvoeren en doorzoeken van logboekbestanden in Azure Monitor-logboeken.

## <a name="see-the-software-inventory-for-a-single-machine"></a>De software-inventaris voor één computer bekijken

Als u een overzicht van de software voor één computer wilt zien, kunt u Inventaris openen vanuit de pagina met VM-resources in Azure, of kunt u met behulp van de filterfunctie in Azure Monitor-logboeken naar de overeenkomstige computer gaan. De volgende voorbeeldquery van Log Analytics retourneert de lijst met software voor een computer met de naam **ContosoVM**.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u software-inventaris kunt weergeven:

> [!div class="checklist"]
> * Wijzigingen bijhouden en Inventaris inschakelen
> * Een Azure-VM inschakelen
> * Een niet-Azure-VM inschakelen
> * Geïnstalleerde software weergeven
> * Inventarislogboeken doorzoeken op geïnstalleerde software

Ga verder naar het overzicht van de functie Wijzigingen bijhouden en Inventaris als u er meer over wilt weten.

> [!div class="nextstepaction"]
> [Overzicht Wijzigingen bijhouden en Inventaris](change-tracking/overview.md)
