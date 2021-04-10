---
title: Een virtuele machine verplaatsen naar een andere regio (Azure Site Recovery)
description: Meer informatie over hoe u uw SQL Server virtuele machine vanuit de ene regio naar de andere kunt migreren in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: migration
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 789554121af1c83d9077e6153ca9db01477bde25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97360149"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>Een SQL Server VM verplaatsen naar een andere regio in azure met Azure Site Recovery
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel leert u hoe u Azure Site Recovery kunt gebruiken om uw SQL Server virtuele machine (VM) te migreren van de ene regio naar een andere in Azure. 

Als u een SQL Server VM naar een andere regio wilt verplaatsen, moet u het volgende doen:
1. [Voorbereiden](#prepare-to-move): Controleer of de bron SQL Server virtuele machine en de doel regio adequaat zijn voor bereid voor de verplaatsing. 
1. [Configureren](#configure-azure-site-recovery-vault): het verplaatsen van uw SQL Server VM vereist dat het een gerepliceerd object binnen de Azure site Recovery kluis is. U moet uw SQL Server-VM toevoegen aan de Azure Site Recovery kluis. 
1. [Testen](#test-move-process): het migreren van de SQL Server VM vereist een failover van de bron regio naar de gerepliceerde doel regio. Om ervoor te zorgen dat het proces kan worden voltooid, moet u eerst testen of de failover van uw SQL Server-VM naar de doel regio kan worden uitgevoerd. Op deze manier kunt u alle problemen weer geven en voor komen wanneer u de daad werkelijke verplaatsing uitvoert. 
1. [Verplaatsen](#move-the-sql-server-vm): zodra de testfailover is voltooid en u weet dat u veilig bent om uw SQL Server virtuele machine te migreren, kunt u de virtuele machine verplaatsen naar de doel regio. 
1. [Opschonen](#clean-up-source-resources): als u facturerings kosten wilt voor komen, verwijdert u de SQL Server virtuele machine uit de kluis en overbodige resources die resteren in de resource groep. 

## <a name="verify-prerequisites"></a>De vereisten controleren 

- Controleer of het verplaatsen van de bron regio naar de doel regio [wordt ondersteund](../../../site-recovery/azure-to-azure-support-matrix.md#region-support).  
- Bekijk de [scenario architectuur en-onderdelen](../../../site-recovery/azure-to-azure-architecture.md) , evenals de [beperkingen en vereisten voor ondersteuning](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Controleer de accountmachtigingen. Als u een gratis Azure-account hebt gemaakt, bent u de beheerder van uw abonnement. Als u niet de abonnementsbeheerder bent, vraagt u de beheerder om u de machtigingen toe te wijzen die u nodig hebt. Als u replicatie wilt inschakelen voor een VM en gegevens wilt kopiëren met behulp van Azure Site Recovery, hebt u het volgende nodig: 
    - Machtigingen voor het maken van een virtuele machine. De ingebouwde rol *Inzender voor virtuele machines* beschikt over deze machtigingen, waaronder: 
        - Machtigingen voor het maken van een virtuele machine in de geselecteerde resource groep. 
        - Machtigingen voor het maken van een virtuele machine in het geselecteerde virtuele netwerk. 
        - Machtigingen voor het schrijven naar het geselecteerde opslag account. 
      - Machtigingen om Azure Site Recovery-bewerkingen te beheren. De rol *site Recovery Inzender* heeft alle machtigingen die nodig zijn voor het beheren van site Recovery bewerkingen in een Recovery Services kluis.  

## <a name="prepare-to-move"></a>Voorbereiden om te verplaatsen
Bereid de bron SQL Server-VM en de doel regio voor de verplaatsing voor. 

### <a name="prepare-the-source-sql-server-vm"></a>De bron SQL Server VM voorbereiden

- Zorg ervoor dat alle meest recente basis certificaten zich op de SQL Server VM bevinden die u wilt verplaatsen. Als de meest recente basis certificaten niet aanwezig zijn, voor komt u dat de gegevens naar de doel regio worden gekopieerd. 
- Voor virtuele Windows-machines installeert u alle meest recente Windows-updates op de VM, zodat alle vertrouwde basis certificaten zich op de computer bevinden. In een niet-verbonden omgeving volgt u de standaard Windows Update en het update proces van het certificaat voor uw organisatie. 
- Voor Linux-VM’s volgt u de richtlijnen van de Linux-distributeur voor het verkrijgen van de meest recente basiscertificaten en de certificaatintrekkingslijst op de VM. 
- Zorg ervoor dat u geen verificatie proxy gebruikt voor het beheren van de netwerk verbinding voor de virtuele machines die u wilt verplaatsen. 
- Controleer de vereisten als de VM die u wilt verplaatsen, geen toegang tot internet heeft, of een firewallproxy gebruikt voor het beheren van uitgaande toegang. 
- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Dit omvat, maar is niet beperkt tot load balancers, NSG’s (netwerkbeveiligingsgroepen), en openbare IP-adressen. 

### <a name="prepare-the-target-region"></a>De doelregio voorbereiden

- Controleer of u een Azure-abonnement hebt waarmee u in staat bent om VM’s te maken in de doelregio die wordt gebruikt voor herstel na noodgevallen. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum. 
- Zorg ervoor dat uw abonnement voldoende bronnen heeft voor de ondersteuning van Vm's met een grootte die overeenkomt met uw bron-Vm's. Als u Site Recovery gebruikt voor het kopiëren van gegevens naar het doel, Site Recovery kiest voor dezelfde grootte of de dichtstbijzijnde grootte voor de doel-VM. 
- Zorg ervoor dat u een doelresource maakt voor elk onderdeel dat is geïdentificeerd in de bronnetwerkindeling. Deze stap is belangrijk om ervoor te zorgen dat de VM’s in de doelregio beschikken over alle functionaliteit en functies, die u had in de bronregio. 
    - Azure Site Recovery detecteert en maakt automatisch een virtueel netwerk wanneer u replicatie inschakelt voor de bron-VM. U kunt ook een netwerk vooraf maken en dit toewijzen aan de virtuele machine in de gebruikers stroom voor het inschakelen van replicatie. U moet hand matig andere resources in de doel regio maken.
- Raadpleeg de volgende documentatie om de meest gebruikte netwerkresources te maken die relevant zijn voor u, op basis van de bron-VM-configuratie: 
    - [Netwerkbeveiligingsgroepen](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md)
    - [Openbaar IP-adres](../../../virtual-network/virtual-network-public-ip-address.md)
    - Raadpleeg de [documentatie bij netwerken](../../../virtual-network/virtual-networks-overview.md)voor aanvullende netwerk onderdelen.
- Maak hand matig een niet-productie netwerk in de doel regio als u de configuratie wilt testen voordat u de laatste overstap naar de doel regio uitvoert. We raden u aan deze stap uit te voeren omdat deze minimale interferentie van het productie netwerk garandeert. 

## <a name="configure-azure-site-recovery-vault"></a>Azure Site Recovery kluis configureren

De volgende stappen laten zien hoe u Azure Site Recovery kunt gebruiken om gegevens te kopiëren naar de doel regio. Maak de Recovery Services kluis in een andere regio dan de bron regio. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
1. Kies ervoor om **een resource te maken** in de linkerbovenhoek van het navigatie deel venster. 
1. Selecteer **deze &-beheer hulpprogramma's** en selecteer vervolgens **back-up en site Recovery**. 
1. Op het tabblad **basis beginselen** onder **Project Details** maakt u een nieuwe resource groep in de doel regio of selecteert u een bestaande resource groep in de doel regio. 
1. Geef onder **Details van exemplaar** een naam op voor uw kluis en selecteer vervolgens uw doel **regio** in de vervolg keuzelijst. 
1. Selecteer **controleren + maken** om uw Recovery Services kluis te maken. 
1. Selecteer **alle services** in de linkerbovenhoek van het navigatie deel venster en typ in het zoekvak `recovery services` . 
1. Eventueel Selecteer de ster naast **Recovery Services kluizen** om deze toe te voegen aan uw snelle navigatie balk. 
1. Selecteer **Recovery Services-kluizen** en selecteer vervolgens de Recovery Services kluis die u hebt gemaakt. 
1. Selecteer in het deel venster **overzicht** de optie **repliceren**. 

   ![Replicatie configureren](./media/move-sql-vm-different-region/configure-replication.png)

1. Selecteer **bron** en selecteer vervolgens **Azure** als bron. Selecteer de juiste waarden voor de vervolg keuzelijst andere velden, zoals de locatie voor de bron-Vm's. Alleen resource groepen die zich in de **bron locatie** regio bevinden, worden weer gegeven in het veld **bron resource groep** . 
1. Selecteer **virtuele machines** en kies vervolgens de virtuele machines die u wilt migreren. Selecteer **OK** om uw VM-selectie op te slaan. 
1. Selecteer **instellingen** en kies vervolgens uw **doel locatie** in de vervolg keuzelijst. Dit moet de resource groep zijn die u eerder hebt voor bereid. 
1. Wanneer u de replicatie hebt aangepast, selecteert u **doel resources maken** om de resources op de nieuwe locatie te maken. 
1. Nadat het maken van de resource is voltooid, selecteert u **replicatie inschakelen** om de replicatie van uw SQL Server-VM te starten vanuit de bron naar de doel regio.
1. U kunt de status van de replicatie controleren door te navigeren naar uw Recovery-kluis, **gerepliceerde items** te selecteren en de **status** van uw SQL Server virtuele machine weer te geven. De status **Protected** geeft aan dat de replicatie is voltooid. 

   ![Replicatiestatus controleren](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>Verplaats proces testen
De volgende stappen laten zien hoe u Azure Site Recovery kunt gebruiken om het verplaatsings proces te testen. 

1. Navigeer naar uw **Recovery Services kluis** in de [Azure Portal](https://portal.azure.com) en selecteer **gerepliceerde items**. 
1. Selecteer de SQL Server VM die u wilt verplaatsen, Controleer of de **replicatie status** **in orde** wordt weer gegeven en selecteer vervolgens **failover testen**. 

   ![Testfailover voor uw VM](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. Selecteer op de pagina **failover testen** het **meest recente toepassings consistente** herstel punt dat voor de failover moet worden gebruikt, zoals dit het enige type moment opname is dat de consistentie van SQL Server gegevens kan garanderen. 
1. Selecteer het virtuele netwerk onder **Azure Virtual Network** en selecteer **OK** om de failover te testen. 
   
   >[!IMPORTANT]
   > U wordt aangeraden een afzonderlijk Azure VM-netwerk te gebruiken voor de failover-test. Gebruik niet het productie netwerk dat is ingesteld toen u replicatie inschakelde en dat u de Vm's uiteindelijk wilt verplaatsen. 

1. Als u de voortgang wilt bewaken, gaat u naar uw kluis, selecteert u **site Recovery taken** onder **bewaking** en selecteert u vervolgens de taak **testfailover** die wordt uitgevoerd.

   ![Voortgang van testen van testfailover controleren](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. Wanneer de test is voltooid, gaat u naar **virtuele machines** in de portal en bekijkt u de nieuwe virtuele machine. Zorg ervoor dat de SQL Server virtuele machine wordt uitgevoerd, de juiste grootte heeft en is verbonden met het juiste netwerk. 
1. Verwijder de virtuele machine die is gemaakt als onderdeel van de test, omdat de **failover** -optie grijs wordt weer gegeven tot de failover-test resources zijn opgeschoond. Ga terug naar de kluis, selecteer **gerepliceerde items**, selecteer de SQL Server VM en selecteer vervolgens **testfailover opschonen**. Leg alle waarnemingen vast die zijn gekoppeld aan de test in de sectie **notities** en schakel het selectie vakje in naast **testen is voltooid. Virtuele machines voor testfailover verwijderen**. Selecteer **OK** om de resources na de test op te schonen. 

   ![items opschonen na failover-test](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>De SQL Server VM verplaatsen 
De volgende stappen laten zien hoe u de SQL Server VM van de bron regio kunt verplaatsen naar de doel regio. 

1. Ga naar de **Recovery Services** kluis, selecteer **gerepliceerde items**, selecteer de virtuele machine en selecteer vervolgens **failover**. 

   ![Failover initiëren](./media/move-sql-vm-different-region/initiate-failover.png)

1. Selecteer het **meest recente toepassings consistente** herstel punt onder **herstel punt**. 
1. Schakel het selectie vakje in naast **de computer afsluiten voordat de failover wordt gestart**. Site Recovery probeert de bron-VM af te sluiten voordat de failover wordt geactiveerd. Failover wordt voortgezet, zelfs als afsluiten mislukt. 
1. Selecteer **OK** om de failover te starten.
1. U kunt het failoverproces bewaken op dezelfde **site Recovery** -pagina die u hebt bekeken bij het controleren van de failover-test in de vorige sectie. 
1. Nadat de taak is voltooid, controleert u of de SQL Server virtuele machine wordt weer gegeven in de doel regio zoals verwacht. 
1. Ga terug naar de kluis, selecteer **gerepliceerde items**, selecteer de SQL Server virtuele machine en selecteer **door voeren** om het verplaatsings proces te volt ooien naar de doel regio. Wacht tot de doorvoertaak is voltooid. 
1. Registreer uw SQL Server-VM met de SQL IaaS agent-extensie om de beheer baarheid van **virtuele SQL-machines** in te scha kelen in de Azure Portal en de functies die aan de uitbrei ding zijn gekoppeld. Zie [SQL Server VM registreren met de extensie van SQL IaaS-agent](sql-agent-extension-manually-register-single-vm.md)voor meer informatie. 

  > [!WARNING]
  > SQL Server consistentie van gegevens wordt alleen gegarandeerd met app-consistente moment opnamen. De **laatste verwerkte** moment opname kan niet worden gebruikt voor SQL Server failover, omdat een moment opname van een crash herstel niet kan garanderen SQL Server consistentie van de gegevens. 

## <a name="clean-up-source-resources"></a>Bron resources opschonen
Als u facturerings kosten wilt voor komen, verwijdert u de SQL Server virtuele machine uit de kluis en verwijdert u overbodige gekoppelde resources. 

1. Ga terug naar de **site Recovery** kluis, selecteer **gerepliceerde items** en selecteer de SQL Server VM. 
1. Selecteer **Replicatie uitschakelen**. Selecteer een reden voor het uitschakelen van de beveiliging en selecteer **OK** om de replicatie uit te scha kelen. 

   >[!IMPORTANT]
   > Het is belang rijk om deze stap uit te voeren om te voor komen dat Azure Site Recovery-replicatie in rekening wordt gebracht. 

1. Als u geen plannen hebt om een van de resources in de bron regio opnieuw te gebruiken, verwijdert u alle relevante netwerk resources en de bijbehorende opslag accounts. 


## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over Windows-VM'S SQL Server](frequently-asked-questions-faq.md)
* [SQL Server over de prijs informatie voor Windows-VM'S](pricing-guidance.md)
* [Opmerkingen bij de release van een Windows-VM SQL Server](doc-changes-updates-release-notes.md)