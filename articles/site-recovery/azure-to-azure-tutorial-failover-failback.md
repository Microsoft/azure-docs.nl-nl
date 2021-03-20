---
title: Zelfstudie om failover-overschakeling uit te voeren voor Azure-VM's naar een secundaire regio voor herstel na noodgevallen met Azure Site Recovery.
description: Zelfstudie om te leren hoe u een failover-overschakeling uitvoert voor Azure-VMs die worden gerepliceerd naar een secundaire Azure-regio als onderdeel van herstel na een noodgeval met de Azure Site Recovery-service en hoe u deze VM's opnieuw kunt beveiligen.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392734"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Zelfstudie: Failover-overschakeling uitvoeren van Azure-VM's naar een secundaire regio

Leer hoe u een failover-overschakeling kunt uitvoeren van Azure-VM's die met [Azure Site Recovery](site-recovery-overview.md) zijn ingeschakeld voor herstel na een noodgeval, naar een secundaire Azure-regio. Na een failover kunt u de VM's in de doelregio opnieuw beveiligen, zodat deze weer worden gerepliceerd naar de primaire regio. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Vereisten controleren
> * VM-instellingen verifiëren
> * Een failover uitvoeren naar de secundaire regio
> * Begin met het repliceren van de VM weer terug naar de primaire regio.


> [!NOTE]
> In deze zelfstudie wordt uitgelegd hoe u een failover van VM's uitvoert met minimale stappen. Als u een failover met volledige instellingen wilt uitvoeren, leert u meer over [netwerken](azure-to-azure-about-networking.md), [automatisering](azure-to-azure-powershell.md) en [probleemoplossing](azure-to-azure-troubleshoot-errors.md) voor Azure-VM.



## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst het volgende te doen:

1. Replicatie instellen voor meerdere Azure-VM's. Als u dat nog niet hebt gedaan, [voltooit u hiervoor de eerste zelfstudie](azure-to-azure-tutorial-enable-replication.md) in deze serie.
2. U kunt het beste een [herstelanalyse uitvoeren](azure-to-azure-tutorial-dr-drill.md) voor gerepliceerde VM's. Als u een analyse uitvoert voordat u een volledige failover uitvoert, zorgt u ervoor dat alles naar behoren werkt, zonder dat dit van invloed is op uw productie-omgeving. 


## <a name="verify-the-vm-settings"></a>De VM-instellingen controleren

1. Selecteer de VM in de kluis > **Gerepliceerde items**.

    ![Optie om de eigenschappen van de virtuele machine op de overzichtspagina te openen](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. Controleer op de VM-pagina **Overzicht** of de VM beveiligd en in orde is, voordat u een failover uitvoert.
    ![Pagina om de eigenschappen en status van de VM te controleren](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. Voordat u een failover uitvoert, controleert u of:
    - Op de VM een ondersteund [Windows](azure-to-azure-support-matrix.md#windows)- of [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage)-besturingssysteem wordt uitgevoerd.
    - De VM voldoet aan vereisten voor [compute](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [opslag](azure-to-azure-support-matrix.md#replicated-machines---storage) en [netwerken](azure-to-azure-support-matrix.md#replicated-machines---networking).

## <a name="run-a-failover"></a>Een failover uitvoeren


1. Selecteer **Failover** op de pagina **Overzicht** van de VM.

    ![Knop Failover voor het gerepliceerde item](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. Kies een herstelpunt in **Failover**. De Azure-VM in de doelregio wordt gemaakt met behulp van gegevens uit dit herstelpunt.
  
   - **Laatst verwerkt**: Gebruikt het laatste door Site Recovery verwerkte herstelpunt. Het tijdstempel wordt weergegeven. Er wordt geen tijd besteed aan het verwerken van gegevens, zodat er sprake is van een lage RTO (Recovery Time Objective).
   -  **Laatste**: Verwerkt alle gegevens die naar Site Recovery worden verzonden, om een herstelpunt voor elke VM te maken voordat er een failover naar wordt uitgevoerd. Biedt de laagste RPO (Recovery Point Objective), omdat alle gegevens worden gerepliceerd naar Site Recovery wanneer de failover wordt geactiveerd.
   - **Laatste toepassingsconsistente punt**: Met deze optie wordt er een failover uitgevoerd van VM's naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: hiermee voert u een failover uit naar een bepaald herstelpunt. Aangepast is alleen beschikbaar wanneer u een failover uitvoert voor één VM en geen herstelplan gebruikt.

    > [!NOTE]
    > Als u een schijf aan een VM hebt toegevoegd nadat replicatie is ingeschakeld, worden in replicatiepunten de schijven weergegeven die beschikbaar zijn voor herstel. Een replicatiepunt dat is gemaakt voordat u een tweede schijf hebt toegevoegd, wordt bijvoorbeeld weergegeven als '1 van 2 schijven'.

4. Selecteer **De computer afsluiten voordat de failover wordt gestart** als u wilt dat Site Recovery probeert de bron-VM's af te sluiten voordat de failover wordt gestart. Deze optie zorgt ervoor dat er geen gegevens verloren gaan. De failover wordt voortgezet zelfs als het afsluiten is mislukt. 

    ![De pagina Failoverinstellingen](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. Selecteer **OK** om de failover te starten.
4. Controleer de failover in meldingen.

    ![Voortgangsmelding](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![Melding Geslaagd](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. Na de failover wordt de Azure-VM die in de doelregio is gemaakt, weergegeven in **Virtuele machines**. Controleer of de VM wordt uitgevoerd en de juiste grootte heeft. Als u een ander herstelpunt voor de virtuele machine wilt gebruiken, selecteert u **Herstelpunt wijzigen** op de pagina **Essentials**.
6. Als u tevreden bent met de virtuele machine waarvoor een failover is uitgevoerd, selecteert u **Doorvoeren** op de overzichtspagina om de failover te voltooien.

    ![De knop Doorvoeren](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. Selecteer in **Doorvoeren** de optie **OK** om te bevestigen. Met Doorvoeren worden alle beschikbare herstelpunten voor de virtuele machine in Site Recovery verwijderd. U kunt het herstelpunt daarna niet meer wijzigen.

8. Bewaak de voortgang van het doorvoeren in meldingen.

    ![Melding voor doorvoervoortgang](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![Melding voor geslaagde doorvoer](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. Site Recovery schoont de bron-VM niet op na een failover. U moet dat handmatig doen.


## <a name="reprotect-the-vm"></a>De VM opnieuw beveiligen

Na een failover moet u de VM opnieuw beveiligen in de secundaire regio, zodat deze terug naar de primaire regio wordt gerepliceerd. 

1. Zorg ervoor dat de **Status *van de VM* Failover doorgevoerd** is voordat u begint.
2. Controleer of u toegang hebt tot de primaire regio en of u gemachtigd bent om virtuele machines te maken.
3. Selecteer op de VM-pagina **Overzicht** **Opnieuw beveiligen**.

   ![Knop om een VM opnieuw te beveiligen.](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. In **Opnieuw beveiligen** controleert u de replicatierichting (secundaire naar primaire regio) en controleert u de doelinstellingen voor de primaire regio. Resources die zijn gemarkeerd als nieuw worden door Site Recovery gemaakt als onderdeel van het opnieuw beveiligen.

     ![Pagina Instellingen voor opnieuw beveiligen](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Selecteer **OK** om het proces van het opnieuw beveiligen te starten. Het proces verzendt initiële gegevens naar de doellocatie en repliceert vervolgens delta-informatie voor de VM's naar het doel.
7. Controleer de voortgang van het opnieuw beveiligen in de meldingen. 

    ![Melding voor voortgang opnieuw beveiligen](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![Melding voor opnieuw beveiligen geslaagd](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een failover uitgevoerd van de primaire regio naar de secundaire regio en bent u begonnen met het repliceren van VM's weer terug naar de primaire regio. Nu kunt u een failback uitvoeren van de secundaire regio naar de primaire regio.

> [!div class="nextstepaction"]
> [Een failback naar de primaire regio uitvoeren](azure-to-azure-tutorial-failback.md)
