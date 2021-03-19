---
title: Achtergrond installatie van Azure Backup Server v2
description: Gebruik een Power shell-script om Azure Backup Server v2 op de achtergrond te installeren. Dit type installatie wordt ook wel een installatie zonder toezicht genoemd.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74172245"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Een installatie zonder toezicht van Azure Backup Server uitvoeren

Meer informatie over het uitvoeren van een installatie zonder toezicht van Azure Backup Server.

Deze stappen zijn niet van toepassing als u Azure Backup Server v1 installeert.

## <a name="install-backup-server"></a>Back-upserver installeren

1. Maak een tekst bestand op de server die als host fungeert voor Azure Backup Server v2 of hoger. (U kunt het bestand in Klad blok of in een andere tekst editor maken.) Sla het bestand op als MABSSetup.ini.

2. Plak de volgende code in het MABSSetup.ini-bestand. Vervang de tekst tussen de haakjes ( \< \> ) door waarden uit uw omgeving. De volgende tekst is een voor beeld:

   ```text
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Sla het bestand op. Voer vervolgens de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid op de installatie server:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

U kunt deze vlaggen gebruiken voor de installatie:</br>
**/f**:. ini-bestandspad</br>
**/l**: pad naar logboek</br>
**/i**: installatiepad</br>
**/x**: pad uninstall</br>

## <a name="next-steps"></a>Volgende stappen

Nadat u de back-upserver installeert, leert u hoe u uw server voorbereidt of een werk belasting gaat beveiligen.

- [Werk belastingen voor de back-upserver voorbereiden](backup-azure-microsoft-azure-backup.md)
- [Backup Server gebruiken om back-ups te maken van een VMware-server](backup-azure-backup-server-vmware.md)
- [Backup Server gebruiken om back-ups te maken van SQL Server](backup-azure-sql-mabs.md)
- [Modern Backup Storage toevoegen aan Backup Server](backup-mabs-add-storage.md)
