---
title: Instellingen van Windows Update configureren voor Updatebeheer van Azure Automation
description: In dit artikel leest u hoe u Windows Update instellingen configureert om te werken met Azure Automation Updatebeheer.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: a1f95ca856223628974a9519b7c4811bde43965e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92222159"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>Instellingen van Windows Update configureren voor Updatebeheer van Azure Automation

Azure Automation Updatebeheer is afhankelijk van de [Windows Update-client](/windows/deployment/update/windows-update-overview) om Windows-updates te downloaden en te installeren. Er zijn specifieke instellingen die worden gebruikt door de Windows Update-client wanneer er verbinding wordt gemaakt met Windows Server Update Services (WSUS) of Windows Update. Veel van deze instellingen kunnen worden beheerd met:

- Editor voor lokaal groepsbeleid
- Groepsbeleid
- PowerShell
- Het REGI ster rechtstreeks bewerken

Updatebeheer veel van de instellingen die zijn opgegeven om de Windows Update-client te beheren. Als u instellingen gebruikt om niet-Windows-updates in te scha kelen, worden deze updates ook door Updatebeheer beheerd. Als u het downloaden van updates wilt inschakelen voordat een update-implementatie plaatsvindt, kan de update-implementatie sneller, efficiënter en minder waarschijnlijk het onderhouds venster overschrijden.

Raadpleeg [uw implementatie plannen voor het bijwerken van virtuele Windows-machines in azure met WSUS](/azure/architecture/example-scenario/wsus/)voor aanvullende aanbevelingen voor het instellen van WSUS in uw Azure-abonnement en uw virtuele Windows-machines veilig te houden.

## <a name="pre-download-updates"></a>Updates vooraf downloaden

Als u het automatisch downloaden van updates wilt configureren zonder ze automatisch te installeren, kunt u groepsbeleid gebruiken om [de automatische updates-instelling te configureren](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) op 3. Met deze instelling kunnen de vereiste updates op de achtergrond worden gedownload en wordt u gewaarschuwd dat de updates gereed zijn om te worden geïnstalleerd. Op deze manier blijft Updatebeheer controle over de planning, maar kunt u updates downloaden buiten het onderhouds venster Updatebeheer. Dit gedrag voor komt `Maintenance window exceeded` fouten in updatebeheer.

U kunt deze instelling inschakelen in Power shell:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Instellingen voor opnieuw opstarten configureren

De register sleutels die worden vermeld in [Automatische updates configureren door het REGI ster te bewerken en de](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) [register sleutels die worden gebruikt voor het beheren van opnieuw opstarten](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , kunnen ertoe leiden dat uw computers opnieuw worden opgestart, zelfs als u in de instellingen voor **Update-implementatie** **nooit opnieuw opstarten** opgeeft. Configureer deze register sleutels op het beste aansluiten bij uw omgeving.

## <a name="enable-updates-for-other-microsoft-products"></a>Updates voor andere micro soft-producten inschakelen

De Windows Update-client is standaard zo geconfigureerd dat alleen updates voor Windows worden geleverd. Als u de optie **updates voor andere micro soft-producten opgeven wanneer ik Windows Update** inschakelt, ontvangt u ook updates voor andere producten, waaronder beveiligings patches voor Microsoft SQL Server en andere micro soft-software. U kunt deze optie configureren als u de meest recente [beheer sjabloon bestanden](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) hebt gedownload en gekopieerd die beschikbaar zijn voor Windows 2016 en hoger.

Als u computers hebt met Windows Server 2012 R2, kunt u deze instelling niet configureren via groepsbeleid. Voer de volgende Power shell-opdracht uit op deze computers:

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>WSUS-configuratie-instellingen maken

Updatebeheer ondersteunt WSUS-instellingen. U kunt bronnen opgeven voor het scannen en downloaden van updates met behulp van de instructies in de [locatie van intranet-Microsoft Update service opgeven](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). De Windows Update-client is standaard geconfigureerd om updates te downloaden van Windows Update. Wanneer u een WSUS-server als bron voor uw machines opgeeft en de updates niet zijn goedgekeurd in WSUS, mislukt de update-implementatie. 

Als u computers wilt beperken tot de interne Update service, stelt u [geen verbinding maken met een Windows Update Internet locaties](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)in.

## <a name="next-steps"></a>Volgende stappen

Plan een update-implementatie met behulp van de instructies in [updates en patches voor uw virtuele machines beheren](manage-updates-for-vm.md).
