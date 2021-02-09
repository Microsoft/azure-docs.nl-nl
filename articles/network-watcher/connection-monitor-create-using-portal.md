---
title: Verbindings monitor maken-Azure Portal
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u een monitor maakt in de verbindings monitor met behulp van de Azure Portal.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: bd13712d137ec5a1fdfa6dec8e6f6d1e0a7432cb
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833164"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>Een monitor maken in de verbindings monitor met behulp van de Azure Portal

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe tests toevoegen in een bestaande werk ruimte of een nieuwe werk ruimte inschakelen in Netwerkprestatiemeter. U kunt ook geen nieuwe monitors voor verbinding toevoegen in de verbindings monitor (klassiek). U kunt de tests en verbindings monitors die zijn gemaakt vóór 1 juli 2021 blijven gebruiken. Als u de service onderbreking voor uw huidige workloads wilt minimaliseren, [migreert u uw tests van Netwerkprestatiemeter ](migrate-to-connection-monitor-from-network-performance-monitor.md) of  [migreert u van het klassieke verbindings](migrate-to-connection-monitor-from-connection-monitor-classic.md) controleprogramma naar de nieuwe verbindings monitor in azure Network Watcher vóór 29 februari 2024.

Meer informatie over het gebruik van verbindings monitor om de communicatie tussen uw resources te bewaken. In dit artikel wordt beschreven hoe u een monitor maakt met behulp van de Azure Portal. De verbindings monitor ondersteunt hybride en Azure-Cloud implementaties.


## <a name="before-you-begin"></a>Voordat u begint 

In verbindings monitors die u maakt met behulp van verbindings beheer, kunt u on-premises machines en Azure-Vm's als bronnen toevoegen. Deze verbindings monitors kunnen ook de connectiviteit met eind punten bewaken. De eind punten kunnen zich op Azure of op een andere URL of een ander IP-adres bevindt.

Hier volgen enkele definities om aan de slag te gaan:

* **Verbindings monitor-resource**. Een regio-specifieke Azure-resource. Alle volgende entiteiten zijn eigenschappen van een verbindingscontrole resource.
* **Eind punt**. Een bron of doel die deel uitmaakt van connectiviteits controles. Voor beelden van eind punten zijn:
    * Azure-Vm's.
    * Virtuele netwerken van Azure.
    * Azure-subnetten.
    * On-premises agents.
    * On-premises subnetten.
    * On-premises aangepaste netwerken die meerdere subnetten bevatten.
    * Url's en Ip's.
* **Configuratie testen**. Een protocol-specifieke configuratie voor een test. Op basis van het protocol dat u kiest, kunt u de poort, drempel waarden, test frequentie en andere para meters definiëren.
* **Test groep**. De groep die bron-eind punten, bestemmings eindpunten en test configuraties bevat. Een verbindings monitor kan meer dan één test groep bevatten.
* **Testen**. De combi natie van een bron eindpunt, bestemmings eindpunt en test configuratie. Een test is het meest gedetailleerde niveau waarmee bewakings gegevens beschikbaar zijn. De bewakings gegevens omvatten het percentage controles dat is mislukt en de round-trip tijd (RTT).

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="Diagram waarin een verbindings monitor wordt weer gegeven en de relatie tussen test groepen en tests wordt gedefinieerd.":::


## <a name="create-a-connection-monitor"></a>Een verbindingsmonitor maken

Een monitor in de verbindings monitor maken met behulp van de Azure Portal:

1. Ga op de start pagina van Azure Portal naar **Network Watcher**.
1. Selecteer in het linkerdeel venster, in de sectie **bewaking** , de optie **verbindings monitor**.

   U ziet alle verbindings monitors die zijn gemaakt in de verbindings monitor. Ga naar het tabblad **verbindings monitor** om de verbindings monitors weer te geven die zijn gemaakt in de klassieke verbindings monitor.

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Scherm afbeelding met verbindings monitors die zijn gemaakt in de verbindings monitor.":::
   
    
1. Selecteer in de linkerbovenhoek in het dash board **verbindings controle** de optie **maken**.

   

1. Voer op het tabblad **basis beginselen** informatie in voor uw verbindings monitor: 
   * **Naam van de verbindings monitor**: Voer een naam in voor de verbindings monitor. Gebruik de standaard naamgevings regels voor Azure-resources.
   * **Abonnement**: Selecteer een abonnement voor uw verbindings monitor.
   * **Regio**: Selecteer een regio voor de verbindings monitor. U kunt alleen de bron-Vm's selecteren die in deze regio worden gemaakt.
   * **Werkruimte configuratie**: Kies een aangepaste werk ruimte of de standaardwerk ruimte. Uw werk ruimte bevat uw bewakings gegevens.
       * Als u de standaardwerk ruimte wilt gebruiken, schakelt u het selectie vakje in. 
       * Als u een aangepaste werk ruimte wilt kiezen, schakelt u het selectie vakje uit. Selecteer vervolgens het abonnement en de regio voor uw aangepaste werk ruimte. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="Scherm opname van het tabblad basis principes in verbindings monitor.":::
   
1. Selecteer onder aan het tabblad **volgende: test groepen**.

1. Voeg bronnen, doelen en test configuraties toe aan uw test groepen. Zie [test groepen maken in verbindings monitor](#create-test-groups-in-a-connection-monitor)voor meer informatie over het instellen van uw test groepen. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="Scherm opname van het tabblad test groepen in de verbindings monitor.":::

1. Selecteer onder aan het tabblad **volgende: waarschuwingen maken**. Zie [Create Alerts in connection monitor](#create-alerts-in-connection-monitor)(Engelstalig) voor meer informatie over het maken van waarschuwingen.

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="Scherm opname van het tabblad waarschuwing maken.":::

1. Selecteer onder aan het tabblad **volgende: controleren + maken**.

1. Controleer op het tabblad **controleren en maken** de basis gegevens en test groepen voordat u de verbindings monitor maakt. Als u de verbindings monitor wilt bewerken, kunt u dit doen door terug te gaan naar de desbetreffende tabbladen. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="Scherm opname van het tabblad controleren + maken in de verbindings monitor.":::
   > [!NOTE] 
   > Op het tabblad **controleren en maken** worden de kosten per maand weer gegeven tijdens de fase van de verbindings monitor. Op dit moment worden er geen kosten in rekening gebracht voor de kolom **Huidig kosten/maand** . Als verbindings monitor algemeen beschikbaar wordt, wordt in deze kolom een maandelijks bedrag weer gegeven. 
   > 
   > Zelfs tijdens de fase van de verbindings monitor zijn Log Analytics opname kosten van toepassing.

1. Wanneer u klaar bent om de verbindings monitor te maken, selecteert u onder aan het tabblad **controleren en maken** de optie **maken**.

Met verbindings monitor wordt de bron van de verbindings monitor gemaakt op de achtergrond.

## <a name="create-test-groups-in-a-connection-monitor"></a>Test groepen maken in een verbindings monitor

Elke test groep in een verbindings monitor bevat bronnen en bestemmingen die worden getest op netwerk parameters. Ze zijn getest op het percentage controles dat mislukt en de RTT over test configuraties.

Als u in de Azure Portal een test groep wilt maken in een verbindings monitor, geeft u waarden op voor de volgende velden:

* **Test groep uitschakelen**: u kunt dit selectie vakje inschakelen om de bewaking uit te scha kelen voor alle bronnen en doelen die door de test groep worden opgegeven. Deze selectie is standaard uitgeschakeld.
* **Naam**: Geef een naam op voor de test groep.
* **Bronnen**: u kunt zowel virtuele machines van Azure als lokale computers opgeven als bronnen als er agents op zijn geïnstalleerd. Zie [bewakings agenten installeren](./connection-monitor-overview.md#install-monitoring-agents)voor meer informatie over het installeren van een agent voor uw bron.
   * Als u Azure-agents wilt kiezen, selecteert u het tabblad **Azure-eind punten** . Hier ziet u alleen de virtuele machines die zijn gebonden aan de regio die u hebt opgegeven tijdens het maken van de verbindings monitor. Standaard worden Vm's gegroepeerd in het abonnement waarvan ze deel uitmaken. Deze groepen worden samengevouwen. 
   
       U kunt inzoomen op het **abonnements** niveau tot andere niveaus in de hiërarchie:

      **Abonnement**  >  **Resource groep**  >  **VNET**  >  **Subnet**  >  **Vm's met agents**

      U kunt ook de **Group by** -selector wijzigen om de structuur te starten vanaf een ander niveau. Als u bijvoorbeeld per virtueel netwerk groepeert, ziet u de virtuele machines met agents in het hiërarchie- **VNET**-  >  **subnet**  >  **vm's met agents**.

       Wanneer u een VNET, subnet of één virtuele machine selecteert, wordt de bijbehorende resource-ID ingesteld als het eind punt. Standaard worden alle virtuele machines in het geselecteerde VNET of subnet waaraan de Azure Network Watcher-uitbrei ding deel nemen, opgenomen in de bewaking. Als u het bereik wilt beperken, selecteert u specifieke subnetten of agents of wijzigt u de waarde van de eigenschap scope. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="Scherm opname van het deel venster bronnen toevoegen en het tabblad Azure-eind punten in de verbindings monitor.":::

   * Als u on-premises agents wilt kiezen, selecteert u het tabblad **niet-Azure-eind punten** . Standaard worden agents gegroepeerd in werk ruimten per regio. Voor al deze werk ruimten is Netwerkprestatiemeter geconfigureerd. 
   
       Als u Netwerkprestatiemeter aan uw werk ruimte wilt toevoegen, haalt u deze op via [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Zie [oplossingen controleren in azure monitor](../azure-monitor/insights/solutions.md)voor informatie over het toevoegen van Netwerkprestatiemeter. 
   
       Klik onder **verbindings monitor maken** op het tabblad **basis beginselen** , de standaard regio is geselecteerd. Als u de regio wijzigt, kunt u agents kiezen uit werk ruimten in de nieuwe regio. U kunt een of meer agents of subnetten selecteren. In de weer gave **subnet** kunt u specifieke IP-adressen voor bewaking selecteren. Als u meerdere subnetten toevoegt, wordt er een aangepast on-premises netwerk met de naam **OnPremises_Network_1** gemaakt. U kunt ook de **Group by** -selector wijzigen om te groeperen op agents.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="Scherm opname van het deel venster bronnen toevoegen en het tabblad niet-Azure-eind punten in de verbindings monitor.":::

   * Als u recent gebruikte eind punten wilt kiezen, kunt u het tabblad **recent eind punt** gebruiken 
   
   * Wanneer u klaar bent met het instellen van bronnen, selecteert u **gereed** onder aan het tabblad. U kunt de basis eigenschappen, zoals de naam van het eind punt, nog steeds bewerken door het eind punt te selecteren in de weer gave **test groep maken** . 

* **Doelen**: u kunt de verbinding met een virtuele machine van Azure, een on-premises computer of een wille keurig eind punt (een openbaar IP-adres, URL of FQDN) bewaken door dit op te geven als een bestemming. U kunt in één test groep Azure-Vm's, on-premises machines, Office 365 Url's, Dynamics 365 Url's en aangepaste eind punten toevoegen.

    * Als u virtuele Azure-machines als doelen wilt kiezen, selecteert u het tabblad **Azure-eind punten** . De virtuele machines van Azure worden standaard gegroepeerd in een abonnements hiërarchie in de regio die u hebt geselecteerd onder **verbindings controle maken** op het tabblad **basis beginselen** . U kunt de regio wijzigen en Azure-Vm's kiezen uit de nieuwe regio. Vervolgens kunt u inzoomen op het **abonnements** niveau naar andere niveaus in de hiërarchie, net zoals u dat kunt doen wanneer u de Azure-bron-eind punten instelt.

      U kunt VNETs, subnetten of enkele Vm's selecteren, zoals u kunt doen wanneer u de Azure-bron-eind punten instelt. Wanneer u een VNET, subnet of één virtuele machine selecteert, wordt de bijbehorende resource-ID ingesteld als het eind punt. Standaard worden alle virtuele machines in het geselecteerde VNET of subnet waaraan de Network Watcher-extensie deel nemen, opgenomen in de bewaking. Als u het bereik wilt beperken, selecteert u specifieke subnetten of agents of wijzigt u de waarde van de eigenschap scope. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<scherm opname waarin het deel venster doelen toevoegen en het tabblad Azure-eind punten worden weer gegeven. >":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<scherm opname van het deel venster bestemmingen toevoegen op het abonnements niveau. >":::
       
    
    * Als u niet-Azure-agents als doelen wilt kiezen, selecteert u het tabblad **niet-Azure-eind punten** . Standaard worden agents gegroepeerd in werk ruimten per regio. Al deze werk ruimten zijn Netwerkprestatiemeter geconfigureerd. 
    
      Als u Netwerkprestatiemeter aan uw werk ruimte wilt toevoegen, haalt u deze op via Azure Marketplace. Zie [oplossingen controleren in azure monitor](../azure-monitor/insights/solutions.md)voor informatie over het toevoegen van Netwerkprestatiemeter. 

      Klik onder **verbindings monitor maken** op het tabblad **basis beginselen**   , de standaard regio is geselecteerd. Als u de regio wijzigt, kunt u agents kiezen uit werk ruimten in de nieuwe regio. U kunt een of meer agents of subnetten selecteren. In de weer gave **subnet** kunt u specifieke IP-adressen voor bewaking selecteren. Als u meerdere subnetten toevoegt, wordt er een aangepast on-premises netwerk met de naam **OnPremises_Network_1** gemaakt.  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="Scherm opname van het deel venster doelen toevoegen en het tabblad niet-Azure-eind punten.":::
    
    * Als u open bare eind punten als bestemming wilt kiezen, selecteert u het tabblad **externe adressen** . De lijst met eind punten omvat Office 365-test-Url's en Dynamics 365-test-Url's, gegroepeerd op naam. U kunt ook eind punten kiezen die zijn gemaakt in andere test groepen in dezelfde verbindings monitor. 
    
        Als u een eind punt wilt toevoegen, selecteert u in de rechter bovenhoek **eind punt toevoegen**. Geef vervolgens de naam en URL, het IP-adres of de FQDN van het eind punt op.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="Scherm opname van de plaats waar open bare eind punten als bestemmingen moeten worden toegevoegd in de verbindings monitor.":::

    * Als u recent gebruikte eind punten wilt kiezen, gaat u naar het tabblad **recente eind punt**   .
    * Wanneer u klaar bent met het kiezen van bestemmingen, selecteert u **gereed**. U kunt de basis eigenschappen, zoals de naam van het eind punt, nog steeds bewerken door het eind punt te selecteren in de weer gave **test groep maken** . 

* **Test configuraties**: u kunt een of meer test configuraties toevoegen aan een test groep. Maak een nieuwe test configuratie met behulp van het tabblad **nieuwe configuratie** . U kunt ook een test configuratie toevoegen vanuit een andere test groep in dezelfde verbindings monitor op het tabblad **bestaande kiezen** .

    * **Test configuratie naam**: Geef de test configuratie een naam.
    * **Protocol**: Selecteer **TCP**, **ICMP** of **http**. Als u HTTP-naar-HTTPS wilt wijzigen, selecteert u **http** als protocol en selecteert u vervolgens **443** als poort.
        * **Configuratie voor TCP-test maken**: dit selectie vakje wordt alleen weer gegeven als u **http** in de lijst **protocol** selecteert. Schakel dit selectie vakje in om een andere test configuratie te maken die gebruikmaakt van dezelfde bronnen en doelen die u elders hebt opgegeven in uw configuratie. De nieuwe test configuratie heet **\<name of test configuration> _networkTestConfig**.
        * **Traceroute uitschakelen**: dit selectie vakje is van toepassing wanneer het protocol TCP of ICMP is. Schakel dit selectie vakje in om te voor komen dat bronnen worden gedetecteerd voor de detectie van de topologie en de hop-by-Hop-RTT.
    * **Doel poort**: u kunt een doel poort van uw keuze opgeven.
        * **Luis teren op poort**: dit selectie vakje is van toepassing wanneer het protocol TCP is. Schakel dit selectie vakje in om de gekozen TCP-poort te openen als deze nog niet is geopend. 
    * **Test frequentie**: Geef in deze lijst op hoe vaak bronnen worden gepingd op het protocol en de poort die u hebt opgegeven. U kunt kiezen uit 30 seconden, 1 minuut, 5 minuten, 15 minuten of 30 minuten. Selecteer **aangepast** om een andere frequentie tussen 30 seconden en 30 minuten in te voeren. Bronnen testen de connectiviteit met bestemmingen op basis van de waarde die u kiest. Als u bijvoorbeeld 30 seconden selecteert, wordt de verbinding met de bestemming ten minste één keer in elke periode van 30 seconden gecontroleerd.
    * **Drempel waarde voor geslaagde pogingen**: u kunt drempels instellen voor de volgende netwerk parameters:
       * **Controles mislukt**: Stel het percentage controles in dat kan mislukken wanneer bronnen de connectiviteit met bestemmingen controleren door gebruik te maken van de criteria die u hebt opgegeven. Voor het TCP-of ICMP-protocol kan het percentage mislukte controles worden vergeleken met het percentage pakket verlies. Voor het HTTP-protocol vertegenwoordigt deze waarde het percentage HTTP-aanvragen dat geen antwoord heeft ontvangen.
       * **Retour tijd**: Stel in milliseconden de RTT in voor hoe lang bronnen kunnen worden uitgevoerd om verbinding te maken met de bestemming via de test configuratie.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="Scherm afbeelding die laat zien waar u een test configuratie instelt in de verbindings monitor.":::
       
## <a name="create-alerts-in-connection-monitor"></a>Waarschuwingen maken in de verbindings monitor

U kunt waarschuwingen instellen voor tests die zijn mislukt op basis van de drempel waarden die zijn ingesteld in test configuraties.

Als u in de Azure Portal waarschuwingen voor een verbindings monitor wilt maken, geeft u waarden op voor deze velden: 

- **Waarschuwing maken**: u kunt dit selectie vakje inschakelen om een metrische waarschuwing te maken in azure monitor. Wanneer u dit selectie vakje inschakelt, worden de andere velden voor bewerken ingeschakeld. Er zijn extra kosten voor de waarschuwing van toepassing, op basis van de [prijzen voor waarschuwingen](https://azure.microsoft.com/pricing/details/monitor/). 

- **Bereik**  >  **Resource**  >  **Hiërarchie**: deze waarden worden automatisch ingevuld op basis van de waarden die zijn opgegeven op het tabblad **basis beginselen** .

- **Voorwaarde naam**: de waarschuwing wordt gemaakt op basis van de `Test Result(preview)` metriek. Wanneer het resultaat van de test van de verbindings monitor een mislukt resultaat is, wordt de waarschuwings regel geactiveerd. 

- **Naam van de actie groep**: u kunt uw e-mail adres rechtstreeks invoeren of u kunt waarschuwingen maken via actie groepen. Als u uw e-mail adres rechtstreeks invoert, wordt er een actie groep gemaakt met de naam **NPM e-mail ActionGroup** . De e-mail-ID wordt toegevoegd aan die actie groep. Als u actie groepen wilt gebruiken, moet u een eerder gemaakte actie groep selecteren. Zie [actie groepen maken in de Azure Portal](../azure-monitor/platform/action-groups.md)voor meer informatie over het maken van een actie groep. Nadat de waarschuwing is gemaakt, kunt u [uw waarschuwingen beheren](../azure-monitor/platform/alerts-metric.md#view-and-manage-with-azure-portal). 

- **Naam van waarschuwings regel**: de naam van de verbindings monitor.

- **Regel inschakelen bij het maken**: Schakel dit selectie vakje in om de waarschuwings regel in te scha kelen op basis van de voor waarde. Schakel dit selectie vakje uit als u de regel wilt maken zonder deze in te scha kelen. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="Scherm opname van het tabblad waarschuwing maken in de verbindings monitor.":::

## <a name="scale-limits"></a>Schaal limieten

Verbindings monitors hebben de volgende schaal limieten:

* Maximum aantal verbindings monitors per abonnement per regio: 100
* Maximum aantal test groepen per verbindings monitor: 20
* Maximum aantal bronnen en bestemmingen per verbindings monitor: 100
* Maximum aantal test configuraties per verbindings monitor: 2 via de Azure Portal

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het analyseren van bewakings gegevens en het instellen van waarschuwingen](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Meer informatie [over het vaststellen van problemen in uw netwerk](./connection-monitor-overview.md#diagnose-issues-in-your-network).
