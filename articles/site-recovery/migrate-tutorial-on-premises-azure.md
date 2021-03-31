---
title: On-premises machines migreren met Azure Migrate
description: Dit artikel bevat een overzicht van het migreren van on-premises machines naar Azure en doet een aanbeveling voor Azure Migrate.
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: e0e60ee346d20113b2ec7970390d9874522cc770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87847309"
---
# <a name="migrate-on-premises-machines-to-azure"></a>On-premises machines migreren naar Azure

In dit artikel worden opties voor het migreren van on-premises machines naar Azure beschreven. 

## <a name="migrate-with-azure-migrate"></a>Migreren met Azure Migrate

We raden aan dat u machines naar Azure migreert met behulp van de service [Azure Migrate](../migrate/migrate-services-overview.md). Azure Migrate is speciaal ontworpen voor servermigratie. Azure Migrate biedt een centrale hub voor detectie, evaluatie en migratie van on-premises machines naar Azure.

Volg deze koppelingen om te migreren met Azure Migrate:

- [Verken](../migrate/server-migrate-overview.md) migratieopties voor VMware-VM’s en migreer VM’s vervolgens naar Azure via migratie [zonder agents](../migrate/tutorial-migrate-vmware.md) of [met agents](../migrate/tutorial-migrate-vmware-agent.md).
- [Migreer Hyper-V-VM’s](../migrate/tutorial-migrate-hyper-v.md) naar Azure.
- Migreer [fysieke servers of andere VM’s](../migrate/tutorial-migrate-physical-virtual-machines.md), waaronder [AWS-exemplaren](../migrate/tutorial-migrate-aws-virtual-machines.md), naar Azure.

## <a name="migrate-with-site-recovery"></a>Migreren met Site Recovery
Site Recovery moet alleen worden gebruikt voor herstel na noodgevallen, niet voor migratie.

Als u Azure Site Recovery al gebruikt en u dit wilt blijven gebruiken voor migratie, volgt u dezelfde stappen die u gebruikt voor herstel na noodgevallen.

- VMware-VM's: [Bereid Azure](tutorial-prepare-azure.md) en [VMware voor](vmware-azure-tutorial-prepare-on-premises.md), begin [machines te repliceren](vmware-azure-tutorial.md), [controleer](tutorial-dr-drill-azure.md) of alles werkt en [voer een failover uit](vmware-azure-tutorial-failover-failback.md).
- Hyper-V-VM's: [Bereid Azure](tutorial-prepare-azure-for-hyperv.md) en [Hyper-V voor](hyper-v-prepare-on-premises-tutorial.md), begin [machines te repliceren](hyper-v-azure-tutorial.md), [controleer](tutorial-dr-drill-azure.md) of alles werkt en [voer een failover uit](hyper-v-azure-failover-failback-tutorial.md).
- Fysieke servers: [Volg de procedures](physical-azure-disaster-recovery.md) om Azure voor te bereiden, machines voor te bereiden voor herstel na noodgevallen en replicatie in te stellen.

> [!NOTE]
> Wanneer u een failover uitvoert voor herstel na noodgevallen, moet u als laatste stap de failover doorvoeren. Wanneer u on-premises machines migreert, is de optie **Doorvoeren** niet relevant. Selecteer in plaats daarvan de optie **Migratie voltooien**. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lees de veelgestelde vragen](../migrate/resources-faq.md) over Azure Migrate.

  
