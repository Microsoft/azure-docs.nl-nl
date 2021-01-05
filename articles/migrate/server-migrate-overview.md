---
title: Selecteer een optie voor VMware-migratie met Azure Migrate server migratie
description: Biedt een overzicht van de opties voor het migreren van virtuele VMware-machines naar Azure met Azure Migrate server migratie
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 075f113975e5f340493fe33584d8072190be77c8
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896478"
---
# <a name="select-a-vmware-migration-option"></a>Selecteer een VMware-migratie optie

U kunt virtuele VMware-machines migreren naar Azure met behulp van het hulp programma voor migratie van Azure Migrate-server. Dit hulp programma biedt een aantal opties voor de migratie van virtuele VMware-machines:

- Migratie met replicatie zonder agent. Migreer Vm's zonder dat u iets hoeft te installeren.
- Migratie met een agent voor replicatie. Installeer een agent op de virtuele machine voor replicatie.


## <a name="compare-migration-methods"></a>Migratie methoden vergelijken

Gebruik deze geselecteerde vergelijkingen om u te helpen beslissen welke methode u wilt gebruiken. U kunt ook volledige ondersteunings vereisten voor niet- [gemachtigde](migrate-support-matrix-vmware-migration.md#agentless-migration) en [op agents gebaseerde](migrate-support-matrix-vmware-migration.md#agent-based-migration) migraties bekijken.

**Instelling** | **Zonder agent** | **Op basis van een agent**
--- | --- | ---
**Azure-machtigingen** | U hebt machtigingen nodig voor het maken van een Azure Migrate project en voor het registreren van Azure AD-apps die zijn gemaakt bij het implementeren van het Azure Migrate apparaat. | U hebt Inzender machtigingen nodig voor het Azure-abonnement. 
**Replicatie** | Er kunnen Maxi maal 300 Vm's gelijktijdig worden gerepliceerd vanuit een vCenter Server.<br/> Als u meer dan 50 Vm's voor migratie hebt, maakt u meerdere batches met virtuele machines.<br/> Het repliceren van meer dan één keer is van invloed op de prestaties.<br/><br/> U kunt in de portal maximaal 10 machines tegelijk selecteren voor replicatie. Als u meer machines wilt repliceren, voegt u die toe in batches van 10.| De replicatie capaciteit wordt verhoogd door het replicatie apparaat te schalen.
**Implementatie van het apparaat** | Het [Azure migrate apparaat](migrate-appliance.md) wordt on-premises geïmplementeerd. | Het [Azure migrate replicatie apparaat](migrate-replication-appliance.md) wordt on-premises geïmplementeerd.
**Site Recovery compatibiliteit** | Browsercompatibele. | U kunt niet repliceren met Azure Migrate server migratie als u replicatie voor een machine hebt ingesteld met behulp van Site Recovery.
**Doel schijf** | Managed Disks | Managed Disks
**Schijf limieten** | BESTURINGSSYSTEEM schijf: 2 TB<br/><br/> Gegevens schijf: 32 TB<br/><br/> Maximum aantal schijven: 60 | BESTURINGSSYSTEEM schijf: 2 TB<br/><br/> Gegevens schijf: 8 TB<br/><br/> Maximum aantal schijven: 63
**Passthrough-schijven** | Niet ondersteund | Ondersteund
**UEFI-opstart** | Ondersteund. | Ondersteund.

## <a name="compare-deployment-steps"></a>Implementaties tappen vergelijken

Nadat u de beperkingen hebt bekeken, kunt u aan de hand van de stappen in de implementatie van elke oplossing bepalen welke optie u moet kiezen.

**Taak** | **Details** |**Zonder agent** | **Op basis van een agent**
--- | --- | --- | ---
**Het Azure Migrate-apparaat implementeren** | Een licht gewicht apparaat dat wordt uitgevoerd op een virtuele VMware-machine.<br/><br/> Het apparaat wordt gebruikt om computers te detecteren en te beoordelen en om machines te migreren met migratie zonder agent. | Vereist.<br/><br/> Als u het apparaat al voor beoordeling hebt ingesteld, kunt u hetzelfde apparaat gebruiken voor migratie zonder agent. | Niet vereist.<br/><br/> Als u een apparaat voor beoordeling hebt ingesteld, kunt u dit op de juiste plaats laten of het verwijderen als u klaar bent met de evaluatie.
**Het hulp programma voor Server evaluatie gebruiken** | Beoordeel computers met het Azure Migrate: Server assessment tool. | U kunt computers beoordelen voordat u ze migreert, maar u hoeft dit niet te doen. | De evaluatie is optioneel.
**Het hulp programma voor server migratie gebruiken** | Voeg het hulp programma voor migratie van Azure Migrate-server toe in het Azure Migrate-project. | Vereist | Vereist
**VMware voorbereiden voor migratie** | Configureer instellingen op VMware-servers en virtuele machines. | Vereist | Vereist
**De Mobility-service installeren op Vm's** | Mobility service wordt uitgevoerd op elke virtuele machine die u wilt repliceren | Niet vereist | Vereist
**Het replicatie apparaat implementeren** | Het [replicatie apparaat](migrate-replication-appliance.md) wordt gebruikt voor migratie op basis van een agent. Het maakt verbinding tussen de Mobility-service die wordt uitgevoerd op Vm's en server migratie. | Niet vereist | Vereist
**Virtuele machines repliceren**. Schakel VM-replicatie in. | Configureer de replicatie-instellingen en selecteer de Vm's die u wilt repliceren | Vereist | Vereist
**Een testmigratie uitvoeren** | Voer een testmigratie uit om te controleren of alles goed werkt. | Vereist | Vereist
**Een volledige migratie uitvoeren** | Migreer de Vm's. | Vereist | Vereist



## <a name="next-steps"></a>Volgende stappen

[Migreer VMware-vm's](tutorial-migrate-vmware.md) met agentloze migratie.



