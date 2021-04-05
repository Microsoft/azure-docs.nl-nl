---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f002fec531a0355e803a1545990fc0b13b535742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582178"
---
Als u problemen ondervindt met het apparaat, kunt u een ondersteunings pakket maken op basis van de systeem Logboeken. Microsoft Ondersteuning gebruikt dit pakket om de problemen op te lossen. Volg deze stappen voor het maken van een ondersteunings pakket:

1. [Verbinding maken met de Power shell-interface van uw apparaat](#connect-to-the-powershell-interface).
2. Gebruik de `Get-HcsNodeSupportPackage` opdracht om een ondersteunings pakket te maken. Het gebruik van de cmdlet is als volgt:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    De cmdlet verzamelt logboeken van uw apparaat en kopieert deze logboeken naar een opgegeven netwerk of lokale share.

    De volgende para meters worden gebruikt:

    - `-Path` -Geef het netwerk of het lokale pad op waarnaar u het ondersteunings pakket wilt kopiëren. lang
    - `-Credential` -Geef de referenties op voor toegang tot het beveiligde pad.
    - `-Zip` -Geef op of u een zip-bestand wilt genereren.
    - `-Include` -Geef op of u de onderdelen wilt opnemen die moeten worden opgenomen in het ondersteunings pakket. Als niet wordt opgegeven, `Default` wordt ervan uitgegaan.
    - `-IncludeArchived` -Geef op of u gearchiveerde logboeken wilt toevoegen aan het ondersteunings pakket.
    - `-IncludePeriodicStats` -Geef op of u periodieke Statie logboeken wilt gebruiken in het ondersteunings pakket.

    
