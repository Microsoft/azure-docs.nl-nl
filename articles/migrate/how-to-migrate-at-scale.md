---
title: Migratie machine migratie automatiseren in Azure Migrate
description: Hierin wordt beschreven hoe u scripts gebruikt voor het migreren van een groot aantal machines in Azure Migrate
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/01/2019
ms.openlocfilehash: 01196b23ce5bf9ce842da89d0773a77e6a1d5107
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754229"
---
# <a name="scale-migration-of-vms"></a>Migratie van Vm's schalen 

Dit artikel helpt u inzicht in het gebruik van scripts voor het migreren van een groot aantal virtuele machines (Vm's). Als u de migratie wilt schalen, gebruikt u [Azure site Recovery](../site-recovery/site-recovery-overview.md). 

Site Recovery scripts zijn beschikbaar voor uw down load op [Azure PowerShell samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) opslag plaats op github. De scripts kunnen worden gebruikt om VMware-, AWS-, GCP-Vm's en fysieke servers te migreren naar beheerde schijven in Azure. U kunt deze scripts ook gebruiken om virtuele Hyper-V-machines te migreren als u de virtuele machines als fysieke servers migreert. De scripts die gebruikmaken van Azure Site Recovery Power shell, worden [hier](../site-recovery/vmware-azure-disaster-recovery-powershell.md)beschreven.

## <a name="current-limitations"></a>Huidige beperkingen
- Ondersteuning voor het opgeven van het statische IP-adres voor de primaire NIC van de doel-VM
- De scripts nemen geen Azure Hybrid Benefit gerelateerde invoer. u moet de eigenschappen van de gerepliceerde VM hand matig bijwerken in de portal

## <a name="how-does-it-work"></a>Hoe werkt het?

### <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, moet u de volgende stappen uitvoeren:
- Zorg ervoor dat de Site Recovery kluis is gemaakt in uw Azure-abonnement
- Zorg ervoor dat de configuratie server en de proces server zijn geïnstalleerd in de bron omgeving en dat de kluis de omgeving kan detecteren
- Zorg ervoor dat er een replicatie beleid is gemaakt en gekoppeld aan de configuratie server
- Zorg ervoor dat u het VM-beheerders account hebt toegevoegd aan de configuratie server (die wordt gebruikt om de on-premises Vm's te repliceren)
- Zorg ervoor dat de doel artefacten in azure zijn gemaakt
    - Doel resource groep
    - Doel-opslag account (en de bijbehorende resource groep): Maak een Premium-opslag account als u van plan bent om te migreren naar Premium-beheerde schijven
    - Cache-opslag account (en de bijbehorende resource groep): Maak een standaard-opslag account in dezelfde regio als de kluis
    - Doel Virtual Network voor failover (en de bijbehorende resource groep)
    - Doel-subnet
    - Doel Virtual Network voor testfailover (en de bijbehorende resource groep)
    - Beschikbaarheidsset (indien nodig)
    - De netwerk beveiligings groep van het doel en de bijbehorende resource groep
- Zorg ervoor dat u hebt besloten over de eigenschappen van de doel-VM
    - Doel-VM-naam
    - Grootte van de doel-VM in azure (kan worden besloten met Azure Migrate beoordeling)
    - Privé-IP-adres van de primaire NIC in de VM
- De scripts downloaden van [Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) -voor beelden opslag plaats op github

### <a name="csv-input-file"></a>CSV-invoer bestand
Zodra alle vereisten zijn voltooid, moet u een CSV-bestand maken dat gegevens bevat voor elke bron machine die u wilt migreren. De invoer CSV moet een kopregel met de invoer gegevens en een rij met details bevatten voor elke machine die moet worden gemigreerd. Alle scripts zijn ontworpen om te werken met hetzelfde CSV-bestand. Een voor beeld van een CSV-sjabloon is beschikbaar in de map scripts voor uw verwijzing.

### <a name="script-execution"></a>Script uitvoering
Zodra het CSV is voltooid, kunt u de volgende stappen uitvoeren om de migratie van de on-premises Vm's uit te voeren:

**Wizardstap #** | **Script naam** | **Beschrijving**
--- | --- | ---
1 | asr_startmigration.ps1 | Replicatie inschakelen voor alle virtuele machines die worden vermeld in het CSV-bestand, maakt het script een CSV-uitvoer met de taak Details voor elke VM
2 | asr_replicationstatus.ps1 | Controleer de status van de replicatie, het script maakt een CSV met de status voor elke VM
3 | asr_updateproperties.ps1 | Zodra de Vm's zijn gerepliceerd/beveiligd, gebruikt u dit script om de doel eigenschappen van de virtuele machine bij te werken (reken-en netwerk eigenschappen)
4 | asr_propertiescheck.ps1 | Controleren of de eigenschappen op de juiste manier zijn bijgewerkt
5 | asr_testmigration.ps1 |  Start de testfailover van de virtuele machines die worden vermeld in het CSV-bestand. het script maakt een CSV-uitvoer met de taak Details voor elke VM
6 | asr_cleanuptestmigration.ps1 | Wanneer u de virtuele machines die zijn getest van een geslaagd hand matig hebt gevalideerd, kunt u dit script gebruiken om de Vm's voor de testfailover op te schonen
7 | asr_migration.ps1 | Een niet-geplande failover uitvoeren voor de Vm's die worden vermeld in het CSV-bestand, maakt het script een CSV-uitvoer met de taak Details voor elke virtuele machine. Het script sluit de on-premises Vm's niet af voordat de failover wordt geactiveerd. voor toepassings consistentie wordt aanbevolen dat u de virtuele machines hand matig afsluit voordat u het script uitvoert.
8 | asr_completemigration.ps1 | Voer de doorvoer bewerking op de Vm's uit en verwijder de Azure Site Recovery entiteiten
9 | asr_postmigration.ps1 | Als u van plan bent om netwerk beveiligings groepen toe te wijzen aan de Nic's na failover, kunt u dit script hiervoor gebruiken. Er wordt een NSG toegewezen aan een NIC in de doel-VM.

## <a name="how-to-migrate-to-managed-disks"></a>Hoe kan ik migreren naar Managed disks?
Met het script worden standaard de virtuele machines gemigreerd naar beheerde schijven in Azure. Als het beschik bare doel opslag account een Premium-opslag account is, worden Premium-beheerde schijven gemaakt na migratie. Het cache-opslag account kan nog steeds een standaard account zijn. Als het doel opslag account een Standard-opslag account is, worden standaard schijven gemaakt na migratie. 

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](../site-recovery/migrate-tutorial-on-premises-azure.md) over het migreren van servers naar Azure met Azure site Recovery
