---
title: Over de MARS-agent
description: Meer informatie over hoe de MARS-agent de back-upscenario's ondersteunt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 8e4ace0c17dbe75e989981db56583ed9477b3716
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87562596"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informatie over de Microsoft Azure Recovery Services-agent (MARS)

In dit artikel wordt beschreven hoe de Azure Backup-service de Microsoft Azure Recovery Services-agent (MARS) gebruikt voor het maken van back-ups en het herstellen van bestanden, mappen en het volume of de systeem status van een on-premises computer naar Azure.

## <a name="backup-scenarios"></a>Back-upscenario's

De MARS-agent ondersteunt de volgende back-upscenario's:

![Scenario's voor MARS-back-ups](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Bestanden en mappen**: selectief Windows-bestanden en-mappen beveiligen.
- **Volume niveau**: Beveilig een volledig Windows-volume van uw computer.
- **Systeem niveau**: de volledige Windows-systeem status beveiligen.

### <a name="additional-scenarios"></a>Overige scenario's

- **Back-ups maken van specifieke bestanden en mappen in azure virtual machines**: de primaire methode voor het maken van back-ups van virtuele Azure-machines (vm's) is het gebruik van een Azure backup-extensie op de VM. De uitbrei ding maakt een back-up van de volledige VM. Als u een back-up wilt maken van specifieke bestanden en mappen in een VM, kunt u de MARS-agent installeren in de virtuele machines van Azure. Zie [architectuur: ingebouwde Azure VM-back-up](./backup-architecture.md#architecture-built-in-azure-vm-backup)voor meer informatie.

- **Offline seeding**: eerste volledige back-ups van gegevens naar Azure worden doorgaans grote hoeveel heden gegevens overgebracht en vereisen meer netwerk bandbreedte. Bij de volgende back-ups wordt alleen de Delta, of incrementele hoeveelheid gegevens overgedragen. Azure Backup comprimeert de eerste back-ups. Dankzij het proces van *offline seeding* kan Azure backup schijven gebruiken om de gecomprimeerde initiële back-upgegevens offline te uploaden naar Azure. Zie [Azure backup offline back-up maken met Azure data Box](offline-backup-azure-data-box.md)voor meer informatie.

## <a name="restore-scenarios"></a>Herstelscenario's

De MARS-agent ondersteunt de volgende herstel scenario's:

![MARS-herstel scenario's](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Dezelfde server**: de server waarop de back-up oorspronkelijk is gemaakt.
  - **Bestanden en mappen**: Kies de afzonderlijke bestanden en mappen die u wilt herstellen.
  - **Volume niveau**: Kies het volume en het herstel punt dat u wilt herstellen. Zet het vervolgens terug op dezelfde locatie of op een andere locatie op dezelfde computer.  Een kopie van bestaande bestanden maken, bestaande bestanden overschrijven of bestaande bestanden overs Laan.
  - **Systeem niveau**: Kies de systeem status en het herstel punt die u wilt herstellen op dezelfde computer op een opgegeven locatie.

- **Alternatieve server**: een andere server dan de server waar de back-up is gemaakt.
  - **Bestanden en mappen**: Kies de afzonderlijke bestanden en mappen waarvan u het herstel punt wilt herstellen naar een doel machine.
  - **Volume niveau**: Kies het volume en herstel punt dat u wilt herstellen op een andere locatie. Een kopie van bestaande bestanden maken, bestaande bestanden overschrijven of bestaande bestanden overs Laan.
  - **Systeem niveau**: Kies de systeem status en het herstel punt dat u wilt herstellen als een systeem status bestand naar een andere computer.

## <a name="backup-process"></a>Back-upproces

1. Maak vanuit het Azure Portal een [Recovery Services kluis](install-mars-agent.md#create-a-recovery-services-vault)en kies bestanden, mappen en de systeem status van de **Back-updoelen**.
2. [Down load de Recovery Services kluis referenties en het installatie programma van de agent](./install-mars-agent.md#download-the-mars-agent) naar een on-premises machine.

3. [Installeer de agent](./install-mars-agent.md#install-and-register-the-agent) en gebruik de gedownloade kluis referenties om de machine te registreren bij de Recovery Services kluis.
4. Configureer vanuit de agent console op de client [de back-up](./backup-windows-with-mars-agent.md#create-a-backup-policy) om op te geven waarvan u een back-up wilt maken, wanneer u back-ups maakt (het schema), hoe lang de back-ups in azure moeten worden bewaard (het Bewaar beleid) en te beginnen met het beveiligen.

![Diagram van Azure Backup Agent](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Aanvullende informatie

- De eerste **back-** up (eerste back-up) wordt uitgevoerd op basis van uw back-upinstellingen.  De MARS-agent gebruikt VSS om een tijdgebonden moment opname te maken van de volumes die zijn geselecteerd voor back-up. De agent maakt alleen gebruik van de Windows System Writer-bewerking voor het vastleggen van de moment opname. Er worden geen VSS-schrijvers van toepassingen gebruikt en er worden geen app-consistente moment opnamen vastgelegd. Na het maken van de moment opname met VSS, maakt de MARS-agent een virtuele harde schijf (VHD) in de cachemap die u hebt opgegeven tijdens het configureren van de back-up. De agent slaat ook de controle sommen voor elk gegevens blok op.

- **Incrementele back-ups** (volgende back-ups) worden uitgevoerd volgens het schema dat u opgeeft. Tijdens incrementele back-ups worden gewijzigde bestanden geïdentificeerd en wordt een nieuwe VHD gemaakt. De VHD wordt gecomprimeerd en versleuteld en vervolgens naar de kluis verzonden. Nadat de incrementele back-up is voltooid, wordt de nieuwe VHD samengevoegd met de VHD die is gemaakt na de initiële replicatie. Deze samengevoegde VHD bevat de meest recente status om te vergelijken voor continue back-ups.

- De MARS-agent kan de back-uptaak uitvoeren in de **geoptimaliseerde modus** met het USN (Update Sequence Number)-wijzigings logboek of in niet- **geoptimaliseerde modus** door te controleren op wijzigingen in mappen of bestanden via het scannen van het hele volume. De niet-geoptimaliseerde modus is langzamer omdat de agent elk bestand op het volume moet scannen en vergelijken met de meta gegevens om de gewijzigde bestanden te bepalen.  De **eerste back-up** wordt altijd uitgevoerd in niet-geoptimaliseerde modus. Als de vorige back-up is mislukt, wordt de volgende geplande back-uptaak uitgevoerd in niet-geoptimaliseerde modus. Raadpleeg [dit artikel](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)voor meer informatie over deze modi en hoe u deze kunt controleren.

## <a name="next-steps"></a>Volgende stappen

[Ondersteuningsmatrix voor MARS-agent](./backup-support-matrix-mars-agent.md)

[Veelgestelde vragen over MARS-agent](./backup-azure-file-folder-backup-faq.md)
