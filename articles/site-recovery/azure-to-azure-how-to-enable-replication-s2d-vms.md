---
title: Virtuele Azure-machines met Opslagruimten Direct repliceren met Azure Site Recovery
description: Meer informatie over het repliceren van virtuele Azure-machines met Opslagruimten Direct met behulp van Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: f438fadb73f7e3bd25cd7ab9aef0bc46285e30e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424826"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Virtuele Azure-machines met Opslagruimten Direct repliceren naar een andere regio

In dit artikel wordt beschreven hoe u herstel na nood gevallen van Azure-Vm's met opslag ruimten direct inschakelt.

>[!NOTE]
>Alleen crash-consistente herstel punten worden ondersteund voor opslag ruimten direct-clusters.
>

[Opslag ruimten direct (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) is software-gedefinieerde opslag, waarmee u [gast clusters](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering) kunt maken in Azure.  Een gast cluster in Microsoft Azure is een failovercluster dat bestaat uit IaaS Vm's. Op die manier kunnen gehoste VM-workloads worden overgenomen door gast clusters, waardoor er een hogere Beschik baarheid van de beschikbaarheids voor toepassingen kan worden geboden dan met één virtuele machine in Azure. Het is handig in scenario's waarin een virtuele machine als host fungeert voor een essentiële toepassing, zoals SQL of scale-out Bestands server.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Herstel na nood geval met Storage Spaces direct

In een typisch scenario hebt u mogelijk virtuele machines gast cluster in azure voor een hogere tolerantie van uw toepassing, zoals scale-out Bestands server. Hoewel dit een hogere Beschik baarheid van uw toepassing kan bieden, kunt u deze toepassingen beveiligen met Site Recovery voor elk probleem op regio niveau. Site Recovery repliceert de gegevens van de ene regio naar een andere Azure-regio en brengt het cluster in een herstel regio voor nood gevallen in een gebeurtenis van de failover.

Onder diagram ziet u een Azure VM-failovercluster met twee knoop punten die gebruikmaken van Storage Spaces direct.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Twee virtuele machines van Azure in een Windows-failovercluster en elke virtuele machine hebben twee of meer gegevens schijven.
- S2D synchroniseert de gegevens op de gegevens schijf en geeft de gesynchroniseerde opslag weer als een opslag groep.
- De opslag groep presenteert een CSV (cluster Shared volume) voor het failovercluster.
- Het failovercluster gebruikt het CSV voor de gegevens stations.

**Aandachtspunten voor herstel na nood gevallen**

1. Wanneer u een Cloud- [Witness](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) voor het cluster instelt, moet u de witness in het gebied voor herstel na nood gevallen blijven gebruiken.
2. Als u een failover wilt uitvoeren voor de virtuele machines naar het subnet in de DR-regio die afwijkt van de bron regio, moet het cluster-IP-adres worden gewijzigd na de failover.  Als u het IP-adres van het cluster wilt wijzigen, moet u het script voor de Site Recovery [herstel plan gebruiken.](./site-recovery-runbook-automation.md)</br>
[Voorbeeld script](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) voor het uitvoeren van de opdracht in de virtuele machine met behulp van aangepaste script extensie 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Site Recovery inschakelen voor S2D-cluster:

1. Klik in de Recovery Services-kluis op "+ repliceren"
1. Alle knoop punten in het cluster selecteren en deze deel uitmaken van een [consistentie groep met meerdere vm's](./azure-to-azure-common-questions.md#multi-vm-consistency)
1. Replicatie beleid met toepassings consistentie selecteren * (alleen ondersteuning voor crash consistentie is beschikbaar)
1. De replicatie inschakelen

   ![Scherm afbeelding die laat zien waar u de replicatie-instellingen configureert.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Ga naar gerepliceerde items en u kunt de status van de virtuele machine bekijken.
3. De virtuele machines worden beveiligd en worden ook weer gegeven als onderdeel van de multi-VM-consistentie groep.

   ![Scherm opname van de virtuele machines die worden beveiligd en een deel van een consistentie groep met meerdere VM'S.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Een herstel plan maken
Een herstel plan ondersteunt het sequentiëren van verschillende lagen in een toepassing met meerdere lagen tijdens een failover. Met sequentiëren kunt u de consistentie van toepassingen onderhouden. Wanneer u een herstel plan voor een webtoepassing met meerdere lagen maakt, voert u de stappen uit die worden beschreven in [een herstel plan maken met behulp van site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failover-groepen

1.  Maak een herstel plan door de virtuele machines toe te voegen.
2.  Klik op aanpassen om de Vm's te groeperen. Standaard maken alle Vm's deel uit van ' groep 1 '.


### <a name="add-scripts-to-the-recovery-plan"></a>Scripts toevoegen aan het herstel plan
Voor een goede werking van uw toepassingen moet u mogelijk bepaalde bewerkingen uitvoeren op de virtuele Azure-machines na de failover of tijdens een testfailover. U kunt bewerkingen na de failover automatiseren. Hier koppelen we bijvoorbeeld load balancer en wijzigen van het cluster-IP-adres.


### <a name="failover-of-the-virtual-machines"></a>Failover van de virtuele machines 
De knoop punten van de virtuele machines moeten een failover uitvoeren met het Site Recovery [herstel plan](./site-recovery-create-recovery-plans.md) 

![storagespacesdirect-beveiliging](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren
1.  Selecteer uw Recovery Services kluis in de Azure Portal.
2.  Selecteer het herstel plan dat u hebt gemaakt.
3.  Selecteer **Failover testen**.
4.  Als u het proces testfailover wilt starten, selecteert u het herstel punt en het virtuele Azure-netwerk.
5.  Voer validaties uit wanneer de secundaire omgeving actief is.
6.  Wanneer de validaties zijn voltooid, selecteert u testfailover **opschonen** om de failover-omgeving op te schonen.

Zie [failover testen naar Azure in site Recovery](site-recovery-test-failover-to-azure.md)voor meer informatie.

## <a name="run-a-failover"></a>Een failover uitvoeren

1.  Selecteer uw Recovery Services kluis in de Azure Portal.
2.  Selecteer het herstel plan dat u hebt gemaakt voor SAP-toepassingen.
3.  Selecteer **Failover**.
4.  Als u het failoverproces wilt starten, selecteert u het herstel punt.

Zie [failover in site Recovery](site-recovery-failover.md)voor meer informatie.
## <a name="next-steps"></a>Volgende stappen

Meer [informatie](./azure-to-azure-tutorial-failover-failback.md) over het uitvoeren van failback.
