---
title: 'Concepten: identiteit en toegang'
description: Meer informatie over de identiteits-en toegangs concepten van de Azure VMware-oplossing
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 07a7ac8093524ef4240b8f7607d649520b9439e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586247"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Concepten van Azure VMware-oplossings identiteiten

Persoonlijke Clouds van Azure VMware-oplossingen worden ingericht met een vCenter-Server en NSX-T-beheer. U kunt vCenter gebruiken om werk belastingen van virtuele machines (VM) te beheren. U gebruikt de NSX-T-Manager om het particuliere cloud netwerk te beheren en uit te breiden.

De vCenter-toegang en identiteits beheer maakt gebruik van de buildin CloudAdmin. De NSX-T-beheerder gebruikt beperkte beheerders machtigingen. Dit is de aard van de beheerde service en zorgt ervoor dat uw persoonlijke Cloud platform wordt bijgewerkt met de nieuwste functies en patches zoals verwacht.  Zie het [artikel concepten van upgrades][concepts-upgrades]van de privécloud voor meer informatie.

## <a name="vcenter-access-and-identity"></a>toegang tot vCenter en identiteit

De vCenter-CloudAdmin-groep definieert en biedt de bevoegdheden in vCenter. Een andere optie is om toegang en identiteit te bieden via de integratie van single-aanmelden voor de vCenter LDAP met Azure Active Directory. U schakelt deze integratie in nadat u uw privécloud hebt geïmplementeerd. 

De tabel bevat de **CloudAdmin** -en **CloudGlobalAdmin** -bevoegdheden.

|  Machtigingenset           | CloudAdmin | CloudGlobalAdmin | Opmerking |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Waarschuwingen                  | Een CloudAdmin-gebruiker heeft alle alarm bevoegdheden voor waarschuwingen in de Compute-ResourcePool en Vm's.     |          --        |  -- |
|  Automatisch implementeren             |  --  |        --        |  Micro soft beheert hostbeheer.  |
|  Certificaten            |  --  |        --       |  Micro soft biedt certificaat beheer.  |
|  Inhoudsbibliotheek         | Een CloudAdmin-gebruiker heeft bevoegdheden voor het maken en gebruiken van bestanden in een inhouds bibliotheek.    |         Ingeschakeld met SSO.         |  Micro soft zal bestanden in de inhouds bibliotheek distribueren naar ESXi-hosts.  |
|  Datacenter              |  --  |        --          |  Micro soft voert alle Data Center-bewerkingen uit.  |
|  Gegevensarchief               | Data Store. AllocateSpace, Data Store. browse, Datastore.Config, Data Store. Delete File, Data Store. File Management, Data Store. UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX-agent beheer       |  --  |         --       |  Micro soft voert alle bewerkingen uit.  |
|  Map                  |  Een CloudAdmin-gebruiker heeft alle machtigingen voor de map.     |  --  |  --  |
|  Globaal                  |  Global. CancelTask, Global. GlobalTag, Global. Health, Global. LogEvent, Global. ManageCustomFields, Global. ServiceManagers, Global. SetCustomField, Global.SystemTag         |                  |    |
|  Host                    |  Host. HBR. HbrManagement      |        --          |  Micro soft doet alle andere host-bewerkingen.  |
|  InventoryService        |  InventoryService. tagging      |        --          |  --  |
|  Netwerk                 |  Netwerk. assign    |                  |  Micro soft doet alle andere netwerk bewerkingen.  |
|  Machtigingen             |  --  |        --       |  Micro soft heeft alle machtigings bewerkingen.  |
|  Op profielen gebaseerde opslag  |  --  |        --       |  Micro soft voert alle profiel bewerkingen uit.  |
|  Resource                |  Een CloudAdmin-gebruiker heeft alle resource bevoegdheden.        |      --       | --   |
|  Geplande taak          |  Een CloudAdmin-gebruiker heeft alle ScheduleTask-bevoegdheden.   |   --   | -- |
|  Sessies                |  Sessions. GlobalMessage, sessies. ValidateSession      |   --   |  Micro soft doet alle andere sessie bewerkingen.  |
|  Opslag weergaven           |  StorageViews. View   |        --          |  Micro soft doet alle andere bewerkingen voor opslag weergave (service configureren).  |
|  Taken                   |  --  |  --   |  Micro soft beheert uitbrei dingen waarmee taken worden beheerd.  |
|  vApp                    |  Een CloudAdmin-gebruiker heeft alle vApp-bevoegdheden.  |  --  |  --  |
|  Virtuele machine         |  Een CloudAdmin-gebruiker heeft alle VirtualMachine-bevoegdheden.  |  --  |  --  |
|  vService                |  Een CloudAdmin-gebruiker heeft alle vService-bevoegdheden.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Toegangs-en identiteits beheer NSX-T

Gebruik het *beheerders* account om toegang te krijgen tot NSX-T-beheer. Het heeft volledige bevoegdheden en stelt u in staat om laag 1 (T1)-gateways, segmenten (logische switches) en alle services te maken en te beheren. Dit account biedt ook toegang tot de NSX-T-laag-0 (T0)-gateway. Wees mindfull over het aanbrengen van dergelijke wijzigingen, omdat dit kan leiden tot gedegradeerde netwerk prestaties of geen toegang tot de privécloud. Open een ondersteunings aanvraag in de Azure Portal om eventuele wijzigingen aan uw NSX-T T0-gateway aan te vragen.
  
## <a name="next-steps"></a>Volgende stappen

Nu u Azure VMware-oplossings toegang en identiteits concepten hebt behandeld, kunt u het volgende weten:

- [Concepten](concepts-upgrades.md)van de upgrade van de privécloud.
- [vSphere op rollen gebaseerd toegangs beheer voor de Azure VMware-oplossing](concepts-role-based-access-control.md).
- [Informatie over het inschakelen van de Azure VMware Solution-resource](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
