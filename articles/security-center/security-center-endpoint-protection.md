---
title: Aanbevelingen voor Endpoint Protection in azure Security Centers
description: Hoe de Endpoint Protection-oplossingen worden gedetecteerd en in orde worden geïdentificeerd.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: 1ce20deed8b26dc5f5bebf4656dd3f1c370d766f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561225"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint Protection-evaluatie en aanbevelingen in Azure Security Center

Azure Security Center biedt status beoordelingen van [ondersteunde](security-center-services.md#endpoint-supported) versies van Endpoint Protection-oplossingen. In dit artikel worden de scenario's beschreven die leiden tot Security Center om de volgende twee aanbevelingen te genereren:

* **Endpoint Protection-oplossingen op uw virtuele machine installeren**
* **Problemen met de status van Endpoint Protection op uw computers oplossen**

## <a name="windows-defender"></a>Windows Defender

* Security Center raadt u **aan om Endpoint Protection-oplossingen te installeren op de virtuele machine** wanneer [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) wordt uitgevoerd en het resultaat **AMServiceEnabled is: False**

* Security Center raadt u **aan om problemen met de Endpoint Protection-status op uw computers op te lossen** wanneer [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) wordt uitgevoerd en een van de volgende situaties zich voordoet:

  * Een van de volgende eigenschappen is onwaar:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Als een of beide van de volgende eigenschappen 7 of meer zijn:

    - **AntispywareSignatureAge**
    - **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Micro soft System Center Endpoint Protection

* Security Center raadt u **aan om Endpoint Protection-oplossingen op virtuele machine te installeren** bij het importeren van **SCEPMpModule (' $env:P rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1 ')** en het uitvoeren van **Get-MProtComputerStatus** resultaten in **AMServiceEnabled = False**.

* Security Center raadt u **aan om problemen met de Endpoint Protection-status op uw computers op te lossen** wanneer **Get-MprotComputerStatus** wordt uitgevoerd en een van de volgende situaties zich voordoet:

  * Ten minste één van de volgende eigenschappen is onwaar:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Als een of beide van de volgende handtekening updates groter of gelijk zijn aan 7:

    * **AntispywareSignatureAge**
    * **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Security Center raadt u **aan om Endpoint Protection-oplossingen te installeren op de virtuele machine** wanneer een van de volgende controles niet is voldaan:
    - **HKLM: \ SOFTWARE\TrendMicro\Deep Security-Agent** bestaat
    - **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** bestaat
    - Het bestand **dsa_query. cmd** bevindt zich in de installatiemap
    - Resultaten van **dsa_query. cmd** uitvoeren met **component. am. mode: on-trend micro diepe beveiligings agent gedetecteerd**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Security Center raadt u **aan om Endpoint Protection-oplossingen te installeren op de virtuele machine** wanneer een van de volgende controles niet is voldaan:

- **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

of

- **HKLM: \ Software\Wow6432Node\Symantec\Symantec endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM: \ Software\Wow6432Node\Symantec\Symantec endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Security Center raadt u **aan om problemen met de Endpoint Protection-status op uw computers op te lossen** wanneer niet aan een van de volgende controles wordt voldaan:

- Raadpleeg Symantec version >= 12: register locatie: **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion "-waarde" PRODUCTVERSION "**
- Controleer de Real-Time beveiligings status: **HKLM: \ Software\Wow6432Node\Symantec\Symantec endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**
- Controleer de status van de handtekening update: **HKLM\Software\Symantec\Symantec endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 dagen**
- Volledige scan status controleren: **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 dagen**
- Zoek handtekening versie nummer pad naar versie van de hand tekening voor Symantec 12: **register paden + "CurrentVersion\SharedDefs"-waarde "SRTSP"** 
- Pad naar versie van de hand tekening voor Symantec 14: **register paden + "CurrentVersion\SharedDefs\SDSDefs"-waarde "SRTSP"**

Register paden:
- **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
- **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection voor Windows

Security Center raadt u **aan om Endpoint Protection-oplossingen te installeren op de virtuele machine** wanneer een van de volgende controles niet is voldaan:

- **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** bestaat
- **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Security Center raadt u **aan om problemen met de Endpoint Protection-status op uw computers op te lossen** wanneer niet aan een van de volgende controles wordt voldaan:

- McAfee-versie: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**
- Handtekening versie zoeken: **HKLM: \ Software\McAfee\AVSolution\DS\DS-waarde "dwContentMajorVersion"**
- Handtekening datum zoeken: **HKLM: \ Software\McAfee\AVSolution\DS\DS-value "szContentCreationDate" >= 7 dagen**
- Zoek scan datum: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-value "LastFullScanOdsRunTime" >= 7 dagen**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security voor Linux Threat Prevention 

Security Center raadt u **aan om Endpoint Protection-oplossingen te installeren op de virtuele machine** wanneer een van de volgende controles niet is voldaan:

- Het bestand **/opt/ISEC/ens/threatprevention/bin/isecav** bestaat
- de uitvoer van de **/opt/ISEC/ens/threatprevention/bin/isecav-versie** is: **McAfee name = McAfee Endpoint Security for Linux Threat Prevention en McAfee version >= 10**

Security Center raadt u **aan om problemen met de Endpoint Protection-status op uw computers op te lossen** wanneer niet aan een van de volgende controles wordt voldaan:

- **'/opt/ISEC/ens/threatprevention/bin/isecav--listtask '** retourneert **snelle scan, volledige scan** en beide scans <= 7 dagen
- **"/opt/ISEC/ens/threatprevention/bin/isecav--listtask"** retourneert **dat-en Engine-update tijdstip** en beide <= 7 dagen
- **"/opt/ISEC/ens/threatprevention/bin/isecav--getoasconfig--Summary"** retourneert de status **van de toegangs controle**

## <a name="sophos-antivirus-for-linux"></a>Sophos Anti Virus voor Linux 

Security Center raadt u **aan om Endpoint Protection-oplossingen te installeren op de virtuele machine** wanneer een van de volgende controles niet is voldaan:
- Bestand **/opt/Sophos-AV/bin/savdstatus** wordt afgesloten of zoekt naar een aangepaste locatie **' readlink $ (wat savscan) '**
- **"/opt/Sophos-AV/bin/savdstatus--version"** retourneert Sophos name = **Sophos Anti-virus-en sophos-versie >= 9**

Security Center raadt u **aan om problemen met de Endpoint Protection-status op uw computers op te lossen** wanneer niet aan een van de volgende controles wordt voldaan:
- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep-i "geplande scan. \* voltooid | ' staart 1 '**, retourneert een waarde
- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep "scannen voltooid"** | ' staart 1 ', retourneert een waarde
- **"/opt/Sophos-AV/bin/savdstatus--Last update"** retourneert Last update, die <= 7 dagen moet zijn 
- **'/opt/Sophos-AV/bin/savdstatus-v '** is gelijk aan **' on-Access scanning ' wordt uitgevoerd '** 
- **'/opt/Sophos-AV/bin/savconfig Get LiveProtection '** retourneert ingeschakeld

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Micro soft antimalware extension-logboeken zijn beschikbaar op: **%systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (of PaaSAntimalware) \1.5.5.x (versie nummer) \CommandExecution.log**

### <a name="support"></a>Ondersteuning

Neem contact op met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/)voor meer informatie. Of het bestand een ondersteunings incident van Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.