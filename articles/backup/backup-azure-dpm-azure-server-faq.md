---
title: Veelgestelde vragen over Azure Backup Server en DPM
description: In dit artikel vindt u antwoorden op veelgestelde vragen over de Microsoft Azure Backup-Server (MABS) en DPM (Data Protection Manager).
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 1663a842b7e00c611543451d4caef96b5b5a913f
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954988"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server en DPM: veelgestelde vragen

## <a name="general-questions"></a>Algemene vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Backup Server en DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Kan ik de Azure Backup-server gebruiken om een BMR-back-up (Bare Metal Recovery) te maken voor een fysieke server?

Ja.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Kan ik de server registreren bij meerdere kluizen?

Nee. U kunt een DPM-of Azure Backup-Server alleen op één kluis registreren.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Kan ik DPM gebruiken om back-ups te maken van apps in Azure Stack?

Nee. U kunt Azure Backup gebruiken om Azure Stack te beveiligen, Azure Backup biedt geen ondersteuning voor het gebruik van DPM om een back-up te maken van apps in Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Als ik Azure Backup-Agent heb geïnstalleerd om mijn bestanden en mappen te beveiligen, kan ik System Center DPM installeren om een back-up te maken van on-premises workloads naar Azure?

Ja. U moet eerst DPM instellen en vervolgens de Azure Backup-Agent installeren.  Als u onderdelen in deze volg orde installeert, zorgt u ervoor dat de Azure Backup-agent werkt met DPM. Het installeren van de agent voordat DPM wordt geïnstalleerd, wordt niet aanbevolen of ondersteund.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Waarom kan ik geen externe DPM-server toevoegen na de installatie van UR7 en de nieuwste Azure Backup-Agent?

Voor de DPM-servers met gegevens bronnen die zijn beveiligd met de Cloud (met behulp van een update pakket dat ouder is dan update pakket 7), moet u ten minste één dag na de installatie van de UR7 en de nieuwste Azure Backup-Agent wachten om een **externe DPM-server toe te voegen**. De periode van één dag is nodig voor het uploaden van de meta gegevens van de DPM-beveiligings groepen naar Azure. Meta gegevens van beveiligings groepen worden de eerste keer geüpload met een nacht taak.

### <a name="are-there-recommendations-for-configuring-exclusions-for-antivirus-software"></a>Zijn er aanbevelingen voor het configureren van uitsluitingen voor antivirus software?

Ja, u kunt het beste antivirus uitsluiting configureren. Zie [antivirus software uitvoeren op de DPM-server](/system-center/dpm/run-antivirus-server)voor uitsluitingen voor dpm. Zie [Configure Anti Virus for MABS server](backup-azure-mabs-troubleshoot.md#configure-antivirus-for-mabs-server)(Engelstalig) voor uitsluitingen voor MABS.

## <a name="vmware-and-hyper-v-backup"></a>VMware en Hyper-V-back-up

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Kan ik een back-up maken van VMware vCenter-servers naar Azure?

Ja. U kunt Azure Backup Server gebruiken om een back-up te maken van VMware vCenter Server-en ESXi-hosts naar Azure.

- Meer [informatie](backup-mabs-protection-matrix.md) over ondersteunde versies.
- [Volg deze stappen](backup-azure-backup-server-vmware.md) om een back-up te maken van een VMware-Server.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Heb ik een afzonderlijke licentie nodig om een volledig on-premises VMware/Hyper-V-cluster te herstellen?

U hebt geen afzonderlijke licenties nodig voor VMware/Hyper-V-beveiliging.

- Als u een System Center-klant bent, kunt u System Center Data Protection Manager (DPM) gebruiken om virtuele VMware-machines te beveiligen.
- Als u geen System Center-klant bent, kunt u Azure Backup Server (betalen per gebruik) gebruiken om virtuele VMware-machines te beveiligen.

### <a name="can-i-restore-a-backup-of-a-hyper-v-or-vmware-vm-stored-in-azure-to-azure-as-an-azure-vm"></a>Kan ik een back-up van een Hyper-V-of VMware-machine die is opgeslagen in azure, herstellen naar Azure als een Azure VM?

Nee, dit is op dit moment niet mogelijk. U kunt alleen herstellen naar een lokale host.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Kan ik een share point-item herstellen naar de oorspronkelijke locatie als share point is geconfigureerd met behulp van SQL AlwaysOn (met beveiliging op schijf)?

Ja, het item kan worden hersteld naar de oorspronkelijke share point-site.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Kan ik een share point-data base herstellen naar de oorspronkelijke locatie als share point is geconfigureerd met behulp van SQL AlwaysOn?

Omdat share point-data bases zijn geconfigureerd in SQL AlwaysOn, kunnen ze niet worden gewijzigd tenzij de beschikbaarheids groep wordt verwijderd. Als gevolg hiervan kan DPM geen data base herstellen naar de oorspronkelijke locatie. U kunt een SQL Server-Data Base herstellen naar een andere SQL Server-instantie.

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

- Meer [informatie](backup-support-matrix-mabs-dpm.md) over de ondersteunings matrix Azure backup server en DPM.
- Meer [informatie](backup-azure-mabs-troubleshoot.md) over de richt lijnen voor het oplossen van problemen met Azure backup server en DPM.
