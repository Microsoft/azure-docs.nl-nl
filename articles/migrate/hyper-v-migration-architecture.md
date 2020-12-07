---
title: Hoe werkt de migratie van Hyper-V in Azure Migrate?
description: Meer informatie over Hyper-V-migratie met Azure Migrate
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751016"
---
# <a name="how-does-hyper-v-replication-work"></a>Hoe werkt Hyper-V-replicatie?

Dit artikel bevat een overzicht van de architectuur en processen die worden gebruikt wanneer u virtuele Hyper-V-machines migreert met het hulp programma voor migratie van Azure Migrate-server.

[Azure Migrate](migrate-services-overview.md) biedt een centrale hub om de detectie, evaluatie en migratie van on-premises apps en workloads en openbare en privécloud-VM's bij te houden via Azure. De hub biedt Azure Migrate-hulpprogramma's voor evaluatie en migratie, evenals externe onafhankelijke hulpprogramma's van onafhankelijke softwareverkopers (ISV's).

## <a name="agentless-migration"></a>Migratie zonder agent

Het hulp programma voor migratie van Azure Migrate server biedt replicatie zonder agent voor on-premises virtuele Hyper-V-machines, met behulp van een migratie werk stroom die is geoptimaliseerd voor Hyper-V. U installeert een software agent alleen op Hyper-V-hosts of cluster knooppunten. Er hoeft niets te worden geïnstalleerd op virtuele Hyper-V-machines.

## <a name="server-migration-and-azure-site-recovery"></a>Server migratie en Azure Site Recovery

Azure Migrate server migratie is een hulp programma voor het migreren van on-premises workloads en cloud-gebaseerde Vm's naar Azure. Site Recovery is een hulp programma voor nood herstel. De hulpprogram ma's delen enkele veelvoorkomende technologie onderdelen die worden gebruikt voor gegevens replicatie, maar hebben verschillende doel einden. 


## <a name="architectural-components"></a>Architectuuronderdelen

![In het diagram ziet u een Hyper-V-bron netwerk met een H T/m P S gegevens kanaal voor Microsoft Azure, met details die in een tabel worden uitgelegd.](./media/hyper-v-replication-architecture/architecture.png)



**Onderdeel** | **Implementatie** | 
--- | --- 
**Replicatie provider** | De Microsoft Azure Site Recovery provider is geïnstalleerd op Hyper-V-hosts en is geregistreerd bij de migratie van de Azure-migratie server.<br/> De provider organiseert de replicatie voor virtuele Hyper-V-machines.
**Recovery Services-agent** | De agent voor de Microsoft Azure Recovery-service verwerkt de gegevens replicatie. Het werkt met de provider om gegevens van virtuele Hyper-V-machines te repliceren naar Azure.<br/> De gerepliceerde gegevens worden geüpload naar een opslag account in uw Azure-abonnement. Het hulp programma voor server migratie de verwerkt de gerepliceerde gegevens en past deze toe op replica schijven in het abonnement. De replica schijven worden gebruikt voor het maken van de virtuele Azure-machines wanneer u migreert.

- Onderdelen worden geïnstalleerd door één installatie bestand, gedownload van Azure Migrate server migratie in de portal.
- De provider en het apparaat gebruiken uitgaande HTTPS-poort 443-verbindingen om te communiceren met Azure Migrate server migratie.
- De communicatie van de provider en de agent is beveiligd en versleuteld.


## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie inschakelt voor een Hyper-V-VM, begint de eerste replicatie.
2. Er wordt een Hyper-V-VM-moment opname gemaakt.
3. Vhd's op de virtuele machine worden één voor één gerepliceerd, totdat ze allemaal zijn gekopieerd naar Azure. De initiële replicatie tijd is afhankelijk van de grootte van de virtuele machine en de netwerk bandbreedte.
4. Schijf wijzigingen die zich voordoen tijdens de eerste replicatie worden bijgehouden met Hyper-V replica en opgeslagen in logboek bestanden (HRL-bestanden).
    - Logboek bestanden bevinden zich in dezelfde map als de schijven.
    - Elke schijf heeft een bijbehorend HRL-bestand dat wordt verzonden naar de secundaire opslag.
    - De momentopname- en logboekbestanden nemen schijfbronnen in beslag terwijl de eerste replicatie wordt uitgevoerd.
4. Nadat de initiële replicatie is voltooid, wordt de moment opname van de virtuele machine verwijderd en begint de replicatie van verschillen.
5. Incrementele schijf wijzigingen worden bijgehouden in HRL-bestanden. Replicatie logboeken worden periodiek geüpload naar een Azure Storage-account door de Recovery Services-agent.


## <a name="performance-and-scaling"></a>Prestaties en schalen

De replicatie prestaties voor Hyper-V worden beïnvloed door factoren die VM-grootte, de wijzigings snelheid van gegevens (verloop) van de virtuele machines, beschik bare ruimte op de Hyper-V-host voor de opslag van logboek bestanden, het uploaden van de band breedte voor replicatie gegevens en de doel opslag in azure beïnvloeden.

- Als u meerdere machines tegelijk repliceert, gebruikt u de [Azure site Recovery Deployment planner](../site-recovery/hyper-v-deployment-planner-overview.md) voor Hyper-V om de replicatie te optimaliseren.
- Plan uw Hyper-V-replicatie en distribueer replicatie over Azure Storage-accounts, in overeenstemming met capaciteit.

### <a name="control-upload-throughput"></a>Upload doorvoer beheren

U kunt de hoeveelheid band breedte die wordt gebruikt voor het uploaden van gegevens naar Azure, beperken op elke Hyper-V-host. Wees voorzichtig. Als u de waarden te laag instelt, heeft dit een negatieve invloed op de replicatie en wordt de migratie vertraagd.


1. Meld u aan bij de Hyper-V-host of het cluster knooppunt.
2. Voer **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc** uit om de MMC-module Windows Azure backup te openen.
3. Selecteer in de module **Eigenschappen wijzigen**.
4. Selecteer in **beperking** de optie **beperking van Internet bandbreedte gebruik inschakelen voor back-upbewerkingen**. Stel de limieten voor werk-en niet-werk uren in. Geldige bereiken zijn van 512 Kbps tot 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Invloed op Upload efficiëntie

Als u een reserve band breedte hebt voor replicatie en u uploads wilt verhogen, kunt u het aantal threads dat voor de upload taak is toegewezen als volgt verhogen:

1. Open het REGI ster met Regedit.
2. Navigeren naar Key HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Verhoog de waarde voor het aantal threads dat wordt gebruikt voor het uploaden van gegevens voor elke replicerende VM. De standaard waarde is 4 en de maximum waarde is 32. 




## <a name="next-steps"></a>Volgende stappen

Probeer de [migratie van Hyper-V](tutorial-migrate-hyper-v.md) uit te proberen met behulp van Azure migrate server migratie.
