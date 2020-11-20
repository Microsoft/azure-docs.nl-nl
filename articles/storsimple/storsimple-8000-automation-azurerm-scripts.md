---
title: AzureRM Power shell-scripts gebruiken voor het beheren van StorSimple-apparaten
description: Meer informatie over het gebruik van Azure Resource Manager SDK-scripts voor het beheren van uw StorSimple 8000 Series-apparaat.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: e3339f883d177366a64187e0c090752527802c8d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968771"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Azure Resource Manager SDK-scripts gebruiken voor het beheren van StorSimple-apparaten

In dit artikel wordt beschreven hoe u met Azure Resource Manager SDK gebaseerde scripts kunt gebruiken om uw StorSimple 8000 Series-apparaat te beheren. Er is ook een voorbeeld script opgenomen waarmee u de stappen voor het configureren van uw omgeving voor het uitvoeren van deze scripts kunt door lopen.

Dit artikel is van toepassing op apparaten met de StorSimple 8000-serie die alleen in Azure Portal worden uitgevoerd.

## <a name="sample-scripts"></a>Voorbeeldscripts

De volgende voorbeeld scripts zijn beschikbaar voor het automatiseren van verschillende StorSimple-taken.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabel met Azure Resource Manager SDK-voorbeeld scripts

| Azure Resource Manager script                    | Beschrijving                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Met dit script kunt u uw StorSimple-apparaat toestemming geven om de versleutelings sleutel van de service gegevens te wijzigen.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Met dit script maakt u een 8010-of 8020-StorSimple Cloud Appliance. Het Cloud apparaat kan vervolgens worden geconfigureerd en geregistreerd bij uw StorSimple Data Manager service.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Met dit script worden StorSimple-volumes gemaakt of gewijzigd.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Dit script bevat alle back-ups voor een apparaat dat is geregistreerd bij de StorSimple-Apparaatbeheer service.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Dit script is alle back-upbeleid voor uw StorSimple-apparaat.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Met dit script worden alle StorSimple-taken opgehaald die worden uitgevoerd op uw StorSimple-Apparaatbeheer service.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Met dit script wordt de update server gescand en kunt u zien of er updates beschikbaar zijn voor installatie op uw StorSimple-apparaat.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Met dit script worden de beschik bare updates op uw StorSimple-apparaat geïnstalleerd.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Met dit script wordt een hand matige Cloud momentopname gestart en worden Cloud momentopnamen ouder dan opgegeven Bewaar dagen verwijderd.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Met dit Azure Automation Power shell-script voor Runbook wordt de status van alle back-uptaken gerapporteerd.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Met dit script wordt één back-upobject verwijderd.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Met dit script wordt een hand matige back-up van uw StorSimple-apparaat gestart.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Met dit script wordt de versleutelings sleutel voor service gegevens bijgewerkt voor alle Cloud apparaten van 8010/8020 StorSimple die zijn geregistreerd bij uw StorSimple-Apparaatbeheer service.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Dit script markeert de ontbrekende back-ups na het analyseren van alle schema's die zijn gekoppeld aan het back-upbeleid. Ook wordt de back-catalogus gecontroleerd met behulp van de lijst met beschik bare back-ups.             |




## <a name="configure-and-run-a-sample-script"></a>Een voorbeeld script configureren en uitvoeren

In deze sectie wordt een voorbeeld script gebruikt en worden de verschillende stappen beschreven die nodig zijn om het script uit te voeren.

### <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat:

*   Azure PowerShell geïnstalleerd. Azure PowerShell-modules installeren:
    * Volg in een Windows-omgeving de stappen in [Install and configure Azure PowerShell](/powershell/azure/install-az-ps). U kunt Azure PowerShell op uw Windows Server-host installeren voor uw StorSimple als u er een gebruikt.
    * Volg de stappen in [Install and configure Azure PowerShell in MacOS of Linux voor](/powershell/azure/install-az-ps)een Linux-of MacOS-omgeving.

Ga voor meer informatie over het gebruik van Azure PowerShell naar aan de [slag met Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Azure PowerShell script uitvoeren

In het script dat in dit voor beeld wordt gebruikt, worden alle taken op een StorSimple-apparaat weer gegeven. Dit omvat de taken die geslaagd, mislukt of worden uitgevoerd. Voer de volgende stappen uit om het script te downloaden en uit te voeren.

1. Start Azure PowerShell. Maak een nieuwe map en wijzig de map in de nieuwe map.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Down load NUGET cli](https://www.nuget.org/downloads) in de map die u in de vorige stap hebt gemaakt. Er zijn verschillende versies van _nuget.exe_. Kies de versie die overeenkomt met uw SDK. Elke download koppeling verwijst rechtstreeks naar een _exe_ -bestand. Zorg ervoor dat u met de rechter muisknop klikt en het bestand op uw computer opslaat in plaats van het uit te voeren vanuit de browser.

    U kunt ook de volgende opdracht uitvoeren om het script te downloaden en op te slaan in dezelfde map die u eerder hebt gemaakt.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Down load de afhankelijke SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Down load het script uit het voorbeeld project GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Voer het script uit. Geef uw Azure-referenties op wanneer u wordt gevraagd om te verifiëren. Met dit script wordt een gefilterde lijst met alle taken op uw StorSimple-apparaat uitgevoerd.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Voorbeelduitvoer

De volgende uitvoer wordt weer gegeven wanneer het voorbeeld script wordt uitgevoerd. De uitvoer bevat alle taken die zijn uitgevoerd op een geregistreerd apparaat dat is gestart op 25 september 2017 en is voltooid op 2 oktober 2017.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Volgende stappen

[Gebruik StorSimple Apparaatbeheer service om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).