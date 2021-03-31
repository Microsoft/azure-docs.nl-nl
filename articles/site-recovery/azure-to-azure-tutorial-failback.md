---
title: Zelfstudie om failback-overschakeling uit te voeren voor virtuele Azure-machines naar een primaire regio voor herstel tijdens noodgevallen met Azure Site Recovery.
description: Zelfstudie om meer te weten te komen over failbacks van virtuele Azure-machines naar een primaire regio met Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93393888"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>Zelfstudie: Een failback naar de primaire regio uitvoeren voor virtuele Azure-machines

Nadat u een failover heeft uitgevoerd van een virtuele Azure-machine naar een secundaire Azure-regio, volgt u deze zelfstudie om een failover van de virtuele machine uit te voeren naar de primaire Azure-regio met behulp van [Azure Site Recovery](site-recovery-overview.md).  In dit artikel leert u het volgende:

> [!div class="checklist"]
> 
> * Controleer de vereisten.
> * Een failback uitvoeren van de virtuele machine in de secundaire regio.
> * De primaire virtuele machines opnieuw beveiligen naar de secundaire regio.
> 
> [!NOTE]
> In deze zelfstudie wordt uitgelegd hoe u een failback uitvoert met minimale stappen. Als u een failover met volledige instellingen wilt uitvoeren, leert u meer over [netwerken](azure-to-azure-about-networking.md), [automatisering](azure-to-azure-powershell.md) en [probleemoplossing](azure-to-azure-troubleshoot-errors.md) voor Azure-VM.



## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst het volgende te doen:

1. [Stel replicatie in](azure-to-azure-tutorial-enable-replication.md) voor ten minste één virtuele Azure-machine en probeer [DR-herstelanalyse](azure-to-azure-tutorial-dr-drill.md) uit.
2. [ Mislukt over de VM ](azure-to-azure-tutorial-failover-failback.md) van de primaire regio naar een secundaire regio en deze opnieuw beveiligd zodat deze repliceert van de secundaire regio naar de primaire regio. 
3. Controleer of de primaire regio beschikbaar is en of u er nieuwe resources in kunt maken en er toegang toe hebt.

## <a name="fail-back-to-the-primary-region"></a>Een failback naar de primaire regio uitvoeren

Nadat de virtuele machines opnieuw zijn beveiligd, kunt u desgewenst een failback uitvoeren naar de primaire regio.

1. Selecteer de VM in de kluis > **Gerepliceerde items**.

2. Controleer op de overzichtspagina van de virtuele machine of de virtuele machine in orde is en of de synchronisatie is voltooid, voordat u een failover uitvoert. De VM moet de status *Beschermd* hebben.

    ![VM-overzichtspagina met de VM in een beschermde staat](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. Selecteer **Failover** op de overzichtspagina. Aangezien we deze keer geen testfailover uitvoeren, wordt u gevraagd dit te verifiëren.

    [ Pagina waarop we zien dat we akkoord gaan om een failover uit te voeren zonder een testfailover ](./media/azure-to-azure-tutorial-failback/no-test.png)

4. Noteer in **Failover** de richting van secundair naar primair en selecteer een herstelpunt De Azure-VM in het doel (primaire regio) wordt gemaakt met behulp van gegevens vanaf dit punt.
   - **Laatst verwerkt**: Gebruikt het laatste door Site Recovery verwerkte herstelpunt. Het tijdstempel wordt weergegeven. Er wordt geen tijd besteed aan het verwerken van gegevens, zodat er sprake is van een lage RTO (Recovery Time Objective).
   -  **Laatste**: Verwerkt alle gegevens die naar Site Recovery worden verzonden, om een herstelpunt voor elke VM te maken voordat er een failover naar wordt uitgevoerd. Biedt de laagste RPO (Recovery Point Objective), omdat alle gegevens worden gerepliceerd naar Site Recovery wanneer de failover wordt geactiveerd.
   - **Laatste toepassingsconsistente punt**: Met deze optie wordt er een failover uitgevoerd van VM's naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: hiermee voert u een failover uit naar een bepaald herstelpunt. Aangepast is alleen beschikbaar wanneer u een failover uitvoert voor één VM en geen herstelplan gebruikt.

    > [!NOTE]
    > Als u een failover uitvoert op een virtuele machine waaraan u een schijf hebt toegevoegd nadat u replicatie voor de virtuele machine hebt ingeschakeld, worden op replicatiepunten de schijven weergegeven die beschikbaar zijn voor herstel. Een replicatiepunt dat is gemaakt voordat u een tweede schijf hebt toegevoegd, wordt bijvoorbeeld weergegeven als '1 van 2 schijven'.

4. Selecteer **De computer afsluiten voordat de failover wordt gestart** als u wilt dat Site Recovery probeert de bron-VM's af te sluiten voordat de failover wordt gestart. Deze optie zorgt ervoor dat er geen gegevens verloren gaan. De failover wordt voortgezet zelfs als het afsluiten is mislukt. 

    ![De pagina Failoverinstellingen](./media/azure-to-azure-tutorial-failback/failover.png)    

3. Selecteer **OK** om de failover te starten.
4. Controleer de failover in meldingen.

    ![Melding voor de voortgang van de failover](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![Melding voor succes van failover](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>VM's opnieuw beschermen

Nadat u VM's hebt teruggezet naar de primaire regio, moet u ze opnieuw beveiligen, zodat ze weer beginnen met repliceren naar de secundaire regio.

1. Selecteer **Opnieuw beveiligen** op de pagina **Overzicht** voor de VM.

    ![Knop om opnieuw te beveiligen vanuit de primaire regio](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. Bekijk de doelinstellingen voor de primaire regio. Resources die zijn gemarkeerd als nieuw worden door Site Recovery gemaakt als onderdeel van het opnieuw beveiligen.
3. Selecteer **OK** om het proces van het opnieuw beveiligen te starten. Het proces verzendt initiële gegevens naar de doellocatie en repliceert vervolgens delta-informatie voor de VM's naar het doel.

     ![Pagina met replicatie-instellingen](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. Bewaak de voortgang van opnieuw beveiligen in meldingen. 

    ![ Voortgangsmelding opnieuw beveiligen ](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [ Voortgangsmelding opnieuw beveiligen ](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png)
    
  

## <a name="clean-up-resources"></a>Resources opschonen

Voor VM's met beheerde schijven, nadat de failback is voltooid en VM's opnieuw zijn beveiligd voor replicatie van primair naar secundair, ruimt Site Recovery automatisch machines op in de secundaire regio voor noodherstel. U hoeft VM's en NIC's in de secundaire regio niet handmatig te verwijderen. Vm's met onbeheerde schijven worden niet opgeschoond.

Als u replicatie volledig uitschakelt nadat u een back-up heeft gemaakt, schoont Site Recovery machines die erdoor worden beschermd op. In dit geval worden ook schijven opgeschoond voor virtuele machines die geen beheerde schijven gebruiken. 
 
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u VM's mislukt terug van de secundaire regio naar de primaire. Dit is de laatste stap in het proces, waaronder het inschakelen van replicatie voor een virtuele machine, het uitproberen van een noodhersteloefening, een failover van de primaire regio naar de secundaire en uiteindelijk een failover.

> [!div class="nextstepaction"]
> Probeer nu noodherstel naar Azure uit voor een [ virtuele machine op locatie ](vmware-azure-tutorial-prepare-on-premises.md)

