---
title: Concepten-opslag
description: Meer informatie over de mogelijkheden voor de belangrijkste opslag in azure VMware-oplossingen voor persoonlijke Clouds.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a4c34f8767b20de3ca0647e09c5dc9edad3d45fb
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200548"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Concepten van Azure VMware-oplossingen opslag

Persoonlijke Clouds van Azure VMware bieden systeem eigen opslag voor het hele cluster met VMware vSAN. Alle lokale opslag van elke host in een cluster wordt gebruikt in een vSAN-Data Store, en versleuteling van gegevens op rest is standaard beschikbaar en is ingeschakeld. U kunt Azure Storage resources gebruiken om de opslag mogelijkheden van uw persoonlijke Clouds uit te breiden.

## <a name="vsan-clusters"></a>vSAN-clusters

Lokale opslag in elke clusterhost wordt gebruikt als onderdeel van een vSAN-gegevens opslag. Alle diskgroups gebruiken een NVMe-cache-laag van 1,6 TB met de onbewerkte, per host op SSD gebaseerde capaciteit van 15,4 TB. De grootte van de laag voor onbewerkte capaciteit van een cluster is de capaciteit per host van het aantal hosts. Zo biedt een cluster met vier hosts een onbewerkte capaciteit van 61,6 TB in de laag vSAN-capaciteit.

Lokale opslag in clusterhosts wordt gebruikt in vSAN gegevens opslag voor het hele cluster. Alle gegevens opslag worden gemaakt als onderdeel van een privécloud-implementatie en zijn direct beschikbaar voor gebruik. De cloudadmin-gebruiker en alle gebruikers in de groep CloudAdmin kunnen gegevens opslag beheren met deze vSAN bevoegdheden:
- Data Store. AllocateSpace
- Datastore.Browse
- Datastore.Config
- Data Store. Delete File
- Data Store. File Management
- Data Store. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Versleuteling van data-at-rest

vSAN data stores gebruiken standaard versleuteling voor Data-at-rest. De versleutelings oplossing is op KMS gebaseerd en ondersteunt vCenter-bewerkingen voor sleutel beheer. Sleutels worden opgeslagen versleuteld, verpakt door een Azure Key Vault hoofd sleutel. Wanneer een host om de een of andere reden uit een cluster wordt verwijderd, worden de gegevens op Ssd's onmiddellijk ongeldig gemaakt.

## <a name="scaling"></a>Schalen

De capaciteit van de systeem eigen cluster opslag wordt geschaald door hosts toe te voegen aan een cluster. Voor clusters die gebruikmaken van AVS36-hosts, wordt de onbewerkte cluster capaciteit met 15,4 TB verhoogd met elke toegevoegde host. Het duurt ongeveer 10 minuten om hosts aan een cluster toe te voegen. Zie voor instructies voor het schalen van clusters de [zelf studie][tutorial-scale-private-cloud]over het schalen van een privécloud.

## <a name="azure-storage-integration"></a>Integratie van Azure Storage

U kunt Azure Storage-services gebruiken voor workloads die worden uitgevoerd in uw privécloud. De Azure Storage-services omvatten opslag accounts, Table Storage en Blob Storage. Met de verbinding van werk belastingen voor Azure Storage-services wordt het Internet niet gepasseren. Deze connectiviteit biedt meer beveiliging en maakt het u mogelijk om op SLA gebaseerde Azure Storage-services te gebruiken in uw werk belastingen in uw privécloud.

## <a name="next-steps"></a>Volgende stappen

Nu u de concepten van Azure VMware-oplossings opslag hebt behandeld, wilt u mogelijk meer informatie over:

- [Concepten van identiteit van privécloud](concepts-identity.md).
- [vSphere op rollen gebaseerd toegangs beheer voor de Azure VMware-oplossing](concepts-role-based-access-control.md).
- [Informatie over het inschakelen van de Azure VMware Solution-resource](enable-azure-vmware-solution.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
