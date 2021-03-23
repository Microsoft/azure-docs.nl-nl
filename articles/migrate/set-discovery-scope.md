---
title: Het bereik voor detectie van servers in VMware instellen met Azure Migrate
description: Hierin wordt beschreven hoe u het detectie bereik instelt voor servers die worden gehost op VMware-evaluatie en migratie met Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775354"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>Detectie bereik instellen voor servers in VMware-omgeving

In dit artikel wordt beschreven hoe u het bereik van detectie voor servers in VMware-omgeving kunt beperken wanneer u het volgende bent:

- Servers detecteren met het [Azure migrate apparaat](migrate-appliance-architecture.md) wanneer u het hulp programma Azure migrate: detectie en evaluatie gebruikt.
- Servers detecteren met het [Azure migrate apparaat](migrate-appliance-architecture.md) wanneer u Azure migrate het hulp programma voor server migratie gebruikt voor de migratie van servers vanuit VMware-omgeving naar Azure.

Wanneer u het apparaat instelt, wordt er verbinding gemaakt met vCenter Server en wordt detectie gestart. Voordat u het apparaat verbindt met vCenter Server, kunt u de detectie beperken tot vCenter Server Data Centers, clusters, een map met clusters, hosts, een map van hosts of afzonderlijke servers. Als u het bereik wilt instellen, wijst u machtigingen toe voor het account dat het apparaat gebruikt voor toegang tot de vCenter Server.

## <a name="before-you-start"></a>Voordat u begint

Als u geen vCenter-gebruikers account hebt ingesteld dat Azure Migrate gebruikt voor detectie, doet u dat nu voor [evaluatie](./tutorial-discover-vmware.md#prepare-vmware) of [agentloze migratie](./migrate-support-matrix-vmware-migration.md#agentless-migration).


## <a name="assign-permissions-and-roles"></a>Machtigingen en rollen toewijzen

U kunt machtigingen toewijzen aan VMware-inventaris objecten met een van de volgende twee methoden:

- Wijs op het account dat door het apparaat wordt gebruikt, een rol toe met de vereiste machtigingen voor de objecten die u wilt bereiken.
- U kunt ook een rol toewijzen aan het account op het datacenter niveau en door geven aan de onderliggende objecten. Geef vervolgens voor elk object dat u niet binnen het bereik wilt, het account een rol voor **toegang** . We raden u aan deze benadering niet aan te bevelen, omdat het lastig is en mogelijk toegangs beheer beschikbaar stellen, omdat aan elk nieuw onderliggend object automatisch toegang wordt verleend die wordt overgenomen van de bovenliggende map.

U kunt de detectie van inventarisatie op het niveau van de vCenter-Server niet bereiken. Als u de reik wijdte van de servers in een map wilt detecteren, maakt u een gebruiker en verleent u afzonderlijk toegang tot elke vereiste server. Host-en cluster mappen worden ondersteund.


### <a name="assign-a-role-for-assessment"></a>Een rol voor evaluatie toewijzen

1. Op het apparaat vCenter-account dat u voor detectie gebruikt, past u de **alleen-lezen** rol toe voor alle bovenliggende objecten die als host fungeren voor servers die u wilt detecteren en beoordelen (host, cluster, map hosts, map clusters, Maxi maal Data Center).
2. Deze machtigingen door geven aan onderliggende objecten in de hiërarchie.

    ![Machtigingen toewijzen](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Een rol toewijzen voor migratie zonder agent

1. Op het apparaat vCenter-account dat u voor migratie gebruikt, past u een door de gebruiker gedefinieerde rol toe die de [benodigde machtigingen](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)heeft, tot alle bovenliggende objecten die als host fungeren voor servers die u wilt detecteren en migreren.
2. U kunt de rol een naam geven die eenvoudiger is te identificeren. Bijvoorbeeld <em>Azure_Migrate</em>.

## <a name="work-around-for-server-folder-restriction"></a>Tijdelijke oplossing voor het beperken van de Server mappen

Op dit moment kan de Azure Migrate: het hulp programma detectie en evaluatie geen servers detecteren als toegang wordt verleend op het niveau van de vCenter-Server. Als u de detectie en evaluatie van de Server mappen wilt beperken, gebruikt u deze tijdelijke oplossing.

1. Wijs alleen-lezen machtigingen toe voor alle servers die zich in de mappen bevinden die u wilt bereiken voor detectie en evaluatie.
2. Ken alleen-lezen toegang toe aan alle bovenliggende objecten die fungeren als host voor de servers host, cluster, map hosts, map clusters, tot Data Center. U hoeft de machtigingen niet door te geven aan alle onderliggende objecten.
3. Als u de referenties voor detectie wilt gebruiken, selecteert u het Data Center als **verzamelings bereik**.


Het installatie programma voor toegangs beheer op basis van rollen zorgt ervoor dat het bijbehorende vCenter-gebruikers account toegang heeft tot alleen Tenant-specifieke servers.


## <a name="next-steps"></a>Volgende stappen

[Het apparaat instellen](how-to-set-up-appliance-vmware.md)