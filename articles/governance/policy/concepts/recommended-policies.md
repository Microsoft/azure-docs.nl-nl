---
title: Aanbevolen beleid voor Azure-Services
description: Hierin wordt beschreven hoe u aanbevolen beleids regels voor Azure-Services, zoals Azure Virtual Machines, kunt zoeken en Toep assen.
ms.date: 09/02/2020
ms.topic: conceptual
ms.customer: generated
ms.openlocfilehash: 019541f034ce8f0c3728c38d2ae4425308b4e2a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96350276"
---
# <a name="recommended-policies-for-azure-services"></a>Aanbevolen beleid voor Azure-Services

Klanten die nieuw zijn voor Azure Policy vaak zoeken naar algemene beleids definities voor het beheren en bepalen van hun resources. De **Aanbevolen beleids regels** van Azure policy bieden een lijst met algemene beleids definities waarmee u kunt beginnen. De **Aanbevolen beleids** ervaring voor ondersteunde bronnen is inge sloten in de portal-ervaring voor die bron.

Zie [Azure Policy-ingebouwde definities](../samples/built-in-policies.md)voor aanvullende Azure Policy-modules.

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Het **aanbevolen beleid** voor [Azure virtual machines](../../../virtual-machines/index.yml) bevindt zich op de **overzichts** pagina voor virtuele machines en op het tabblad **mogelijkheden** . Selecteer in de _Azure Policy_ kaart de tekst ' niet geconfigureerd ' of ' # toegewezen ' om een zijvenster met het aanbevolen beleid te openen. Een beleids definitie die al is toegewezen aan een bereik waarvan de virtuele machine lid is, is niet beschikbaar. Selecteer de aanbevolen beleids regels om toe te passen op deze virtuele machine en selecteer **beleid toewijzen** om een toewijzing voor elk te maken.

Als een organisatie een verval datum heeft bereikt bij het [ordenen van hun resources en resource hiërarchie](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions), wordt aangeraden deze beleids toewijzingen van één per resource over te zetten naar het niveau van het abonnement of het [beheer groep](../../management-groups/index.yml) .

### <a name="azure-virtual-machines-recommended-policies"></a>Aanbevolen beleid voor Azure Virtual Machines

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtuele machines controleren waarop geen herstel na noodgevallen is geconfigureerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Controleer virtuele machines waarop herstel na noodgevallen niet is geconfigureerd. Ga naar [https://aka.ms/asr-doc](../../../site-recovery/index.yml) voor meer informatie over herstel na noodgevallen. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Controleer virtuele machines die niet gebruikmaken van beheerde schijven](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Dit beleid controleert virtuele machines die niet gebruikmaken van beheerde schijven |controleren |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[Azure Backup moet zijn ingeschakeld voor virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Dit beleid helpt te controleren of Azure Backup-service is ingeschakeld voor alle virtuele machines. Azure Backup is een voordelige back-upoplossing voor eenvoudig gegevensherstel en is makkelijker in te schakelen dan andere services voor cloudback-ups. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees [Informatie over de effecten van het beleid](./effects.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).