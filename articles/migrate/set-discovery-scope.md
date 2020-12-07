---
title: Het bereik voor VMware-VM-detectie instellen met Azure Migrate
description: Hierin wordt beschreven hoe u het detectie bereik voor VMware VM-evaluatie en-migratie instelt met Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dc5bd178c837deea7a22fb3be5ba438085c0e748
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753549"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Detectie bereik instellen voor VMware-Vm's

In dit artikel wordt beschreven hoe u het bereik van VMware-Vm's die u detecteert, kunt beperken wanneer u het volgende bent:

- Het detecteren van Vm's met het [Azure migrate apparaat](migrate-appliance-architecture.md) wanneer u het hulp programma Azure migrate: Server Assessment gebruikt.
- Vm's detecteren met het Azure Migrate- [apparaat](migrate-appliance-architecture.md) wanneer u Azure migrate het hulp programma voor server migratie gebruikt, voor de migratie van virtuele machines van VMware naar Azure zonder agents.

Wanneer u het apparaat instelt, wordt er verbinding gemaakt met vCenter Server en wordt detectie gestart. Voordat u het apparaat verbindt met vCenter Server, kunt u de detectie beperken tot vCenter Server Data Centers, clusters, een map met clusters, hosts, een map van hosts of afzonderlijke Vm's. Als u het bereik wilt instellen, wijst u machtigingen toe voor het account dat het apparaat gebruikt voor toegang tot de vCenter Server.

## <a name="before-you-start"></a>Voordat u begint

Als u geen vCenter-gebruikers account hebt ingesteld dat Azure Migrate gebruikt voor detectie, doet u dat nu voor [evaluatie](./tutorial-discover-vmware.md#prepare-vmware) of [agentloze migratie](./migrate-support-matrix-vmware-migration.md#agentless-migration).


## <a name="assign-permissions-and-roles"></a>Machtigingen en rollen toewijzen

U kunt machtigingen toewijzen aan VMware-inventaris objecten met een van de volgende twee methoden:

- Wijs op het account dat door het apparaat wordt gebruikt, een rol toe met de vereiste machtigingen voor de objecten die u wilt bereiken.
- U kunt ook een rol toewijzen aan het account op het datacenter niveau en door geven aan de onderliggende objecten. Geef vervolgens voor elk object dat u niet binnen het bereik wilt, het account een rol voor **toegang** . We raden u aan deze benadering niet aan te bevelen, omdat het lastig is en mogelijk toegangs beheer beschikbaar stellen, omdat aan elk nieuw onderliggend object automatisch toegang wordt verleend die wordt overgenomen van de bovenliggende map.

U kunt de detectie van de inventarisatie op het niveau van de vCenter-VM-map niet bereiken. Als u de reik wijdte van de virtuele machines in een VM-map wilt detecteren, maakt u een gebruiker en verleent u afzonderlijk toegang tot elke vereiste VM. Host-en cluster mappen worden ondersteund.


### <a name="assign-a-role-for-assessment"></a>Een rol voor evaluatie toewijzen

1. Op het apparaat vCenter-account dat u voor detectie gebruikt, past u de **alleen-lezen** rol toe voor alle bovenliggende objecten die als host dienen voor vm's die u wilt detecteren en beoordelen (host, cluster, map hosts, map clusters, Maxi maal Data Center).
2. Deze machtigingen door geven aan onderliggende objecten in de hiërarchie.

    ![Machtigingen toewijzen](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Een rol toewijzen voor migratie zonder agent

1. Op het apparaat vCenter-account dat u voor migratie gebruikt, past u een door de gebruiker gedefinieerde rol toe die de [benodigde machtigingen](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)heeft, tot alle bovenliggende objecten die virtuele machines hosten die u wilt detecteren en migreren.
2. U kunt de rol een naam geven die eenvoudiger is te identificeren. Bijvoorbeeld <em>Azure_Migrate</em>.

## <a name="work-around-vm-folder-restriction"></a>Tijdelijke oplossing voor de beperking van de VM-map

Het hulp programma Server assessment kan momenteel geen Vm's detecteren als toegang wordt verleend op het niveau van de vCenter-VM-map. Als u de detectie en evaluatie van VM-mappen wilt beperken, gebruikt u deze tijdelijke oplossing.

1. Wijs alleen-lezen-machtigingen toe op alle Vm's die zich in de VM-mappen bevinden die u wilt bereiken voor detectie en evaluatie.
2. Ken alleen-lezen toegang toe aan alle bovenliggende objecten die fungeren als host voor de Vm's host, cluster, map hosts, de map clusters, tot Data Center. U hoeft de machtigingen niet door te geven aan alle onderliggende objecten.
3. Als u de referenties voor detectie wilt gebruiken, selecteert u het Data Center als **verzamelings bereik**.


Het installatie programma voor toegangs beheer op basis van rollen zorgt ervoor dat het bijbehorende vCenter-gebruikers account toegang heeft tot alleen Tenant-specifieke Vm's.


## <a name="next-steps"></a>Volgende stappen

[Het apparaat instellen](how-to-set-up-appliance-vmware.md)