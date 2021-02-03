---
title: 'Concepten: identiteit en toegang'
description: Meer informatie over de identiteits-en toegangs concepten van de Azure VMware-oplossing
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: bd1b4cd918ce977cf57d641908b127f7a2d1b0cc
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509296"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Concepten van Azure VMware-oplossings identiteiten

Persoonlijke Clouds van Azure VMware-oplossingen worden ingericht met een vCenter-Server en NSX-T-beheer. U kunt vCenter gebruiken om werk belastingen van virtuele machines (VM) te beheren. U gebruikt de NSX-T-Manager om de privécloud uit te breiden.

Toegang en identiteits beheer gebruiken CloudAdmin groeps rechten voor vCenter en beperkte beheerders rechten voor NSX-T-beheer. Zo zorgt u ervoor dat uw persoonlijke Cloud platform automatisch wordt bijgewerkt met de nieuwste functies en patches.  Zie het [artikel concepten van upgrades][concepts-upgrades]van de privécloud voor meer informatie.

## <a name="vcenter-access-and-identity"></a>toegang tot vCenter en identiteit

De CloudAdmin-groep biedt de bevoegdheden in vCenter. U beheert de groep lokaal in vCenter. Een andere optie is de integratie van de single-aanmelding voor vCenter LDAP met Azure Active Directory. U schakelt deze integratie in nadat u uw privécloud hebt geïmplementeerd. 

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

Gebruik het account Administrator om toegang te krijgen tot NSX-T-beheer. Het heeft volledige bevoegdheden en stelt u in staat T1-routers, logische switches en alle services te maken en te beheren. De bevoegdheden bieden u toegang tot de NSX-T T0-router. Een wijziging in de T0-router kan leiden tot gedegradeerde netwerk prestaties of geen toegang tot de privécloud. Open een ondersteunings aanvraag in de Azure Portal om eventuele wijzigingen aan uw NSX-T T0-router aan te vragen.
  
## <a name="next-steps"></a>Volgende stappen

Nu u Azure VMware-oplossings toegang en identiteits concepten hebt behandeld, kunt u het volgende weten:

- [Concepten](concepts-upgrades.md)van de upgrade van de privécloud.
- [vSphere op rollen gebaseerd toegangs beheer voor de Azure VMware-oplossing](concepts-role-based-access-control.md).
- [Informatie over het inschakelen van de Azure VMware Solution-resource](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md