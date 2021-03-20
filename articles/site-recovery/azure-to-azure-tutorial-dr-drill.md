---
title: Zelfstudie om een Azure VM-herstelanalyse uit te voeren met Azure Site Recovery
description: In deze zelfstudie voert u een Azure VM-herstelanalyse uit op een andere regio met behulp van Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395597"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>Zelfstudie: Herstelanalyse uitvoeren voor Azure-VM's

Leer hoe u voor Azure-VM's een herstelanalyse kunt uitvoeren op een andere Azure-regio met behulp van [Azure Site Recovery](site-recovery-overview.md). In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De vereisten controleren
> * VM-instellingen controleren vóór de analyse
> * Een testfailover uitvoeren
> * Opschonen na de analyse


> [!NOTE]
> Deze zelfstudie biedt minimale stappen voor het uitvoeren van een herstelanalyse. Als u een analyse met volledige infrastructuurtests wilt uitvoeren, leert u meer over [netwerken](azure-to-azure-about-networking.md), [automatisering](azure-to-azure-powershell.md) en [probleemoplossing](azure-to-azure-troubleshoot-errors.md) voor Azure-VM's.

## <a name="prerequisites"></a>Vereisten

Voordat u deze zelfstudie start, moet u herstel na noodgevallen inschakelen voor één of meer Azure-VM's. Hiertoe [voltooit u de eerste zelfstudie](azure-to-azure-tutorial-enable-replication.md) in deze serie.

## <a name="verify-vm-settings"></a>VM-instellingen verifiëren

1. Selecteer de VM in de kluis > **Gerepliceerde items**.

    ![Optie om pagina Herstel na noodgevallen in VM-eigenschappen te openen](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. Controleer op de pagina **Overzicht** of de VM beveiligd en gezond is.
3. Wanneer u een testfailover uitvoert, selecteert u een virtueel Azure-netwerk in de doelregio. De Azure-VM die na de failover is gemaakt, wordt in dit netwerk geplaatst. 

    - In deze zelfstudie selecteren we een bestaand netwerk wanneer we de testfailover uitvoeren.
    - We raden u aan een niet-productienetwerk voor de analyse te kiezen, zodat IP-adressen en netwerkcomponenten beschikbaar blijven in productienetwerken.
   - U kunt ook netwerkinstellingen vooraf configureren die moeten worden gebruikt voor de testfailover. Gedetailleerde instellingen die u voor elke NIC kunt toewijzen, omvatten subnet, privé IP-adres, openbaar IP-adres, load balancer en netwerkbeveiligingsgroep. We gebruiken deze methode hier niet, maar u kunt [dit artikel bekijken](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations) voor meer informatie.


## <a name="run-a-test-failover"></a>Een testfailover uitvoeren


1. Op de pagina **Overzicht** selecteert u **Testfailover**.

    
    ![Knop Testfailover voor het gerepliceerde item](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. Kies een herstelpunt in **Testfailover**. De Azure-VM in de doelregio wordt gemaakt met behulp van gegevens uit dit herstelpunt.
  
   - **Laatst verwerkt**: Gebruikt het laatste door Site Recovery verwerkte herstelpunt. Het tijdstempel wordt weergegeven. Er wordt geen tijd besteed aan het verwerken van gegevens, zodat er sprake is van een lage RTO (Recovery Time Objective).
   -  **Laatste**: Verwerkt alle gegevens die naar Site Recovery worden verzonden, om een herstelpunt voor elke VM te maken voordat er een failover naar wordt uitgevoerd. Biedt de laagste RPO (Recovery Point Objective), omdat alle gegevens worden gerepliceerd naar Site Recovery wanneer de failover wordt geactiveerd.
   - **Laatste toepassingsconsistente punt**: Met deze optie wordt er een failover uitgevoerd van VM's naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: hiermee voert u een failover uit naar een bepaald herstelpunt. Aangepast is alleen beschikbaar wanneer u een failover uitvoert voor één VM en geen herstelplan gebruikt.

3. Selecteer in **Virtueel Azure-netwerk** het doelnetwerk waarin Azure-VM's moeten worden geplaatst die na de failover zijn gemaakt. Selecteer indien mogelijk een niet-productienetwerk, en niet het netwerk dat werd gemaakt toen u replicatie inschakelde.

    ![Pagina Testfailover-instellingen](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. Selecteer **OK** om de failover te starten.
5. Controleer de testfailover in meldingen.

    ![Voortgangsmelding](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![Geslaagd-melding](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. Nadat de failover is voltooid, wordt de Azure-VM die in de doelregio is gemaakt, weergegeven in **Virtuele machines** van de Azure-portal. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het netwerk dat u hebt geselecteerd.

## <a name="clean-up-resources"></a>Resources opschonen

1. Op de pagina **Essentials** selecteert u **Testfailover opschonen**.

    ![Knop om het opschoningsproces te starten](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. Leg in **Testfailover opschonen** > **Notities** eventuele opmerkingen over de testfailover vast en sla deze op. 
3. Selecteer **Testen is voltooid** om VM's te verwijderen die tijdens de testfailover zijn gemaakt.

    ![Pagina met opschoningsopties](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. Controleer de opschoningsvoortgang in meldingen.

    ![Melding voor opschoningsvoortgang](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![Melding voor geslaagde opschoning](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een herstelanalyse uitgevoerd om te controleren of de failover werkt zoals verwacht. U kunt nu een volledige failover uitproberen.

> [!div class="nextstepaction"]
> [Een productie-failover uitvoeren](azure-to-azure-tutorial-failover-failback.md)
