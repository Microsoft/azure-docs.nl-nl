---
title: Over de MARS-agent
description: Meer informatie over hoe de MARS-agent ondersteuning biedt voor back-upscenario's
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9e01694aca386482f9ff7ba52593c88326ba3d62
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517739"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informatie over Microsoft Azure Recovery Services-agent (MARS)

In dit artikel wordt beschreven hoe de Azure Backup-service de MARS-agent (Microsoft Azure Recovery Services) gebruikt om back-up te maken van bestanden, mappen en het volume of de systeemtoestand van een on-premises computer naar Azure.

## <a name="backup-scenarios"></a>Back-upscenario's

De MARS-agent ondersteunt de volgende back-upscenario's:

![BACK-upscenario's voor MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Bestanden en mappen:** Windows-bestanden en -mappen selectief beveiligen.
- **Volumeniveau:** bebeveiligen een volledig Windows-volume van uw computer.
- **Systeemniveau:** bebeveiligen van een volledige Windows-systeemtoestand.

### <a name="additional-scenarios"></a>Overige scenario's

- Back-up maken van specifieke bestanden en mappen in virtuele **Azure-machines:** de primaire methode voor het maken van back-up van virtuele Azure-machines (VM's) is het gebruik van een Azure Backup-extensie op de virtuele machine. De extensie back-up van de hele VM. Als u een back-up wilt maken van specifieke bestanden en mappen in een VM, kunt u de MARS-agent installeren in de Azure-VM's. Zie [Architecture: Built-in Azure VM Backup (Architectuur: ingebouwde Azure VM Backup) voor meer informatie.](./backup-architecture.md#architecture-built-in-azure-vm-backup)

- **Offline seeding:** initiële volledige back-ups van gegevens naar Azure dragen doorgaans grote hoeveelheden gegevens over en vereisen meer netwerkbandbreedte. Volgende back-ups dragen alleen de delta- of incrementele hoeveelheid gegevens over. Azure Backup worden de eerste back-ups gecomprimeerd. Met behulp van *offline seeding* kunnen Azure Backup schijven gebruiken om de gecomprimeerde initiële back-upgegevens offline te uploaden naar Azure. Zie offline [back-Azure Backup maken met behulp](offline-backup-azure-data-box.md)van Azure Data Box voor meer Azure Data Box.

## <a name="restore-scenarios"></a>Herstelscenario's

De MARS-agent ondersteunt de volgende herstelscenario's:

![MARS-herstelscenario's](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Dezelfde server:** de server waarop de back-up oorspronkelijk is gemaakt.
  - **Bestanden en mappen:** kies de afzonderlijke bestanden en mappen die u wilt herstellen.
  - **Volumeniveau:** kies het volume en herstelpunt dat u wilt herstellen. Herstel deze vervolgens naar dezelfde locatie of een alternatieve locatie op dezelfde computer.  Maak een kopie van bestaande bestanden, overschrijf bestaande bestanden of sla het herstellen van bestaande bestanden over.
  - **Systeemniveau:** kies de systeemtoestand en het herstelpunt om op een opgegeven locatie te herstellen naar dezelfde computer.

- **Alternatieve server:** een andere server dan de server waarop de back-up is gemaakt.
  - **Bestanden en mappen:** kies de afzonderlijke bestanden en mappen waarvan u het herstelpunt wilt herstellen naar een doelmachine.
  - **Volumeniveau:** kies het volume en herstelpunt dat u wilt herstellen naar een andere locatie. Maak een kopie van bestaande bestanden, overschrijf bestaande bestanden of sla het herstellen van bestaande bestanden over.
  - **Systeemniveau:** kies de systeemtoestand en het herstelpunt dat u wilt herstellen als een systeemtoestandbestand naar een alternatieve machine.

## <a name="backup-process"></a>Back-upproces

1. Maak in Azure Portal Recovery [Services-kluis](install-mars-agent.md#create-a-recovery-services-vault)en kies bestanden, mappen en de systeemtoestand uit de **Back-updoelen.**
2. [Download de referenties van de Recovery Services-kluis en het installatieprogramma van de agent](./install-mars-agent.md#download-the-mars-agent) naar een on-premises computer.

3. [Installeer de agent en](./install-mars-agent.md#install-and-register-the-agent) gebruik de gedownloade kluisreferenties om de computer te registreren bij de Recovery Services-kluis.
4. Configureer vanuit de agentconsole op de client de back-up om op te geven waar een back-up van moet worden gemaakt, wanneer er een back-up moet worden gemaakt (het schema), hoe lang de back-ups moeten worden bewaard in Azure (het retentiebeleid) en beginnen met beveiligen. [](./backup-windows-with-mars-agent.md#create-a-backup-policy)

![Azure Backup agentdiagram](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Aanvullende informatie

- De **eerste back-up** (eerste back-up) wordt uitgevoerd volgens uw back-upinstellingen.  De MARS-agent gebruikt VSS om een momentopname te maken van de volumes die zijn geselecteerd voor back-up. De agent gebruikt alleen de bewerking Windows System Writer om de momentopname vast te leggen. Het maakt geen gebruik van VSS Writers voor toepassingen en legt geen app-consistente momentopnamen vast. Nadat de momentopname met VSS is gemaakt, maakt de MARS-agent een virtuele harde schijf (VHD) in de cachemap die u hebt opgegeven tijdens het configureren van de back-up. De agent slaat ook controlesums op voor elk gegevensblok.

- **Incrementele back-ups** (volgende back-ups) worden uitgevoerd volgens het schema dat u opgeeft. Tijdens incrementele back-ups worden gewijzigde bestanden geïdentificeerd en wordt er een nieuwe VHD gemaakt. De VHD wordt gecomprimeerd en versleuteld en vervolgens naar de kluis verzonden. Nadat de incrementele back-up is gemaakt, wordt de nieuwe VHD samengevoegd met de VHD die is gemaakt na de initiële replicatie. Deze samengevoegde VHD biedt de meest recente status die moet worden gebruikt voor vergelijking voor continue back-up.

- De MARS-agent kan de back-upfunctie **uitvoeren** in de geoptimaliseerde modus met behulp van het USN-wijzigingsboek (Update Sequence Number) of in niet-geoptimaliseerde **modus** door te controleren op wijzigingen in mappen of bestanden via het scannen van het hele volume. De niet-geoptimiseerde modus is langzamer omdat de agent elk bestand op het volume moet scannen en moet vergelijken met de metagegevens om de gewijzigde bestanden te bepalen.  De **eerste back-up** wordt altijd uitgevoerd in de niet-geoptimiseerde modus. Als de vorige back-up is mislukt, wordt de volgende geplande back-up job uitgevoerd in de niet-geoptimaliseerde modus. Raadpleeg dit artikel voor meer informatie over deze modi en hoe u deze [kunt controleren.](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)

## <a name="next-steps"></a>Volgende stappen

[Ondersteuningsmatrix voor MARS-agent](./backup-support-matrix-mars-agent.md)

[Veelgestelde vragen over MARS-agent](./backup-azure-file-folder-backup-faq.yml)
