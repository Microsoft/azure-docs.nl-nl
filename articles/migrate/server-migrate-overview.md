---
title: Selecteer een optie voor VMware-migratie met Azure Migrate Server Migration
description: Biedt een overzicht van de opties voor het migreren van VMware-VM's naar Azure met Azure Migrate Server Migration
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 22a0629d50ee8181ffcbfe7dad32ab76fb3e68fd
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714160"
---
# <a name="select-a-vmware-migration-option"></a>Selecteer een optie voor VMware-migratie

U kunt VMware-VM's migreren naar Azure met behulp Azure Migrate servermigratieprogramma. Dit hulpprogramma biedt een aantal opties voor VMware-VM-migratie:

- Migratie met behulp van replicatie zonder agent. Migreert VM's zonder dat u er iets op hoeft te installeren.
- Migratie met een agent voor replicatie. Installeer een agent op de VM voor replicatie.


## <a name="compare-migration-methods"></a>Migratiemethoden vergelijken

Gebruik deze geselecteerde vergelijkingen om te bepalen welke methode u wilt gebruiken. U kunt ook de volledige ondersteuningsvereisten voor [migratie zonder agent](migrate-support-matrix-vmware-migration.md#agentless-migration) en op basis van een [agent](migrate-support-matrix-vmware-migration.md#agent-based-migration) bekijken.

**Instelling** | **Zonder agent** | **Op basis van agent**
--- | --- | ---
**Azure-machtigingen** | U hebt machtigingen nodig om een Azure Migrate te maken en om Azure AD-apps te registreren die zijn gemaakt bij het implementeren van Azure Migrate apparaat. | U hebt inzendermachtigingen nodig voor het Azure-abonnement. 
**Replicatie** | Er kunnen maximaal 500 VM's tegelijk worden gerepliceerd vanuit een vCenter Server. U kunt in de portal maximaal 10 machines tegelijk selecteren voor replicatie. Als u meer machines wilt repliceren, voegt u die toe in batches van 10.| De replicatiecapaciteit neemt toe door het replicatieapparaat te schalen.
**Implementatie van het apparaat** | Het [Azure Migrate apparaat](migrate-appliance.md) wordt on-premises geïmplementeerd. | Het [Azure Migrate Replication-apparaat](migrate-replication-appliance.md) wordt on-premises geïmplementeerd.
**Site Recovery compatibiliteit** | Compatibel. | U kunt niet repliceren met Azure Migrate Server Migration als u replicatie hebt ingesteld voor een machine met behulp van Site Recovery.
**Doelschijf** | Managed Disks | Managed Disks
**Schijflimieten** | Besturingssysteemschijf: 2 TB<br/><br/> Gegevensschijf: 32 TB<br/><br/> Maximum aantal schijven: 60 | Besturingssysteemschijf: 2 TB<br/><br/> Gegevensschijf: 32 TB<br/><br/> Maximum aantal schijven: 63
**Passthrough-schijven** | Niet ondersteund | Ondersteund
**UEFI opstarten** | Ondersteund. | Ondersteund. 
**Connectiviteit** | Openbaar internet <br/> ExpressRoute met Microsoft-peering <br/> <br/> [Meer informatie over](./replicate-using-expressroute.md) het gebruik van privé-eindpunten voor replicatie via een persoonlijke ExpressRoute-peering of een S2S VPN-verbinding. |Openbaar internet <br/> ExpressRoute met privé-peering <br/> ExpressRoute met Microsoft-peering <br/> Site-naar-site-VPN

## <a name="compare-deployment-steps"></a>Implementatiestappen vergelijken

Nadat u de beperkingen hebt door genomen, kunt u bepalen welke optie u moet kiezen als u de stappen begrijpt die nodig zijn om elke oplossing te implementeren.

**Taak** | **Details** |**Zonder agent** | **Op basis van agent**
--- | --- | --- | ---
**Het Azure Migrate-apparaat implementeren** | Een lichtgewicht apparaat dat wordt uitgevoerd op een VMware-VM.<br/><br/> Het apparaat wordt gebruikt om machines te ontdekken en te evalueren, en om machines te migreren met behulp van migratie zonder agent. | Vereist.<br/><br/> Als u het apparaat al hebt ingesteld voor evaluatie, kunt u hetzelfde apparaat gebruiken voor migratie zonder agent. | Niet vereist.<br/><br/> Als u een apparaat hebt ingesteld voor evaluatie, kunt u het laten staan of verwijderen als u klaar bent met de evaluatie.
**Het hulpprogramma Voor serverevaluatie gebruiken** | Machines evalueren met het hulpprogramma Azure Migrate:ServerEvaluatie. | Evaluatie is optioneel. | Evaluatie is optioneel.
**Het hulpprogramma Server Migration gebruiken** | Voeg het hulpprogramma Azure Migrate Server Migration toe aan het Azure Migrate project. | Vereist | Vereist
**VMware voorbereiden voor migratie** | Configureer instellingen op VMware-servers en -VM's. | Vereist | Vereist
**De Mobility-service op VM's installeren** | Mobility-service wordt uitgevoerd op elke VM die u wilt repliceren | Niet vereist | Vereist
**Het replicatieapparaat implementeren** | Het [replicatieapparaat wordt](migrate-replication-appliance.md) gebruikt voor migratie op basis van een agent. Er wordt verbinding gemaakt tussen de Mobility-service die worden uitgevoerd op VM's en servermigratie. | Niet vereist | Vereist
**Repliceer VM's.** Schakel VM-replicatie in. | Replicatie-instellingen configureren en VM's selecteren die moeten worden gerepliceerd | Vereist | Vereist
**Een testmigratie uitvoeren** | Voer een testmigratie uit om te controleren of alles goed werkt. | Vereist | Vereist
**Een volledige migratie uitvoeren** | Migreert de VM's. | Vereist | Vereist



## <a name="next-steps"></a>Volgende stappen

[VMware-VM's migreren](tutorial-migrate-vmware.md) met migratie zonder agent.



