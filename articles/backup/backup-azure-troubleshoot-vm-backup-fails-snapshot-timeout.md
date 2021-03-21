---
title: Problemen met agent-en uitbrei dingen oplossen
description: Symptomen, oorzaken en oplossingen voor Azure Backup fouten met betrekking tot agent, uitbrei ding en schijven.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 0313394ad149460f82c98c63cab95b922b4a3da2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519602"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup fout oplossen: problemen met de agent of extensie

Dit artikel bevat probleemoplossings stappen die u kunnen helpen bij het oplossen van Azure Backup fouten met betrekking tot communicatie met de VM-agent en-extensie.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>Stapsgewijze hand leiding voor het oplossen van back-upfouten

De meest voorkomende back-upfouten kunnen automatisch worden opgelost door de volgende stappen voor probleem oplossing te volgen:

### <a name="step-1-check-azure-vm-health"></a>Stap 1: de status van de Azure VM controleren

- **Zorg ervoor dat de inrichtings status van de Azure-VM wordt uitgevoerd**: als de [inrichtings status](../virtual-machines/states-billing.md) van de VM de status **gestopt/** ongedaan maken/bijwerken heeft, wordt de back-upbewerking verstoord. Open *Azure Portal > vm > overzicht >* en controleer de status van de virtuele machine om ervoor te zorgen dat deze **wordt uitgevoerd**  en voer de back-upbewerking opnieuw uit.
- **In behandeling zijnde besturingssysteem updates controleren of opnieuw opstarten**: Controleer of er geen besturingssysteem update in behandeling is of moet opnieuw worden opgestart op de VM.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>Stap 2: de status van de Azure VM Guest Agent-service controleren

- **Controleer of de Azure VM Guest Agent-service is gestart en up-to-date is**:
  - Op een Windows-VM:
    - Navigeer naar **Services. msc** en zorg ervoor dat de **Windows Azure VM Guest Agent-service** actief is. Zorg er ook voor dat de [meest recente versie](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) is geïnstalleerd. Zie problemen met de [Windows VM-gast agent](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)voor meer informatie.
    - De Azure VM-agent wordt standaard geïnstalleerd op een virtuele Windows-machine die is geïmplementeerd vanuit een Azure Marketplace-installatie kopie vanuit de portal, Power shell, de opdracht regel interface of een Azure Resource Manager sjabloon. Een [hand matige installatie van de agent](../virtual-machines/extensions/agent-windows.md#manual-installation) kan nodig zijn wanneer u een aangepaste VM-installatie kopie maakt die wordt geïmplementeerd in Azure.
    - Bekijk de ondersteunings matrix om te controleren of de VM wordt uitgevoerd op het [ondersteunde Windows-besturings systeem](backup-support-matrix-iaas.md#operating-system-support-windows).
  - Op Linux VM,
    - Zorg ervoor dat de Azure VM Guest Agent-service wordt uitgevoerd door de opdracht uit te voeren `ps-e` . Zorg er ook voor dat de [meest recente versie](../virtual-machines/extensions/update-linux-agent.md) is geïnstalleerd. Zie problemen met de [gast agent van Linux VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)voor meer informatie.
    - Zorg ervoor dat de [afhankelijkheden van de Linux VM-agent op systeem pakketten](../virtual-machines/extensions/agent-linux.md#requirements) de ondersteunde configuratie hebben. Bijvoorbeeld: de ondersteunde python-versie is 2,6 en hoger.
    - Bekijk de ondersteunings matrix om te controleren of de VM wordt uitgevoerd op het [ondersteunde Linux-besturings systeem.](backup-support-matrix-iaas.md#operating-system-support-linux)

### <a name="step-3-check-azure-vm-extension-health"></a>Stap 3: de status van de Azure VM-extensie controleren

- **Zorg ervoor dat alle Azure VM-extensies de status Provisioning geslaagd hebben**: als een uitbrei ding een mislukte status heeft, kan dit de back-up belemmeren.
- *Open Azure Portal > VM > instellingen > extensies > uitbrei dingen status* en controleer of alle uitbrei dingen de status **geslaagd** hebben.
- Zorg ervoor dat alle [extensie problemen](../virtual-machines/extensions/overview.md#troubleshoot-extensions) zijn opgelost en voer de back-upbewerking opnieuw uit.
- **Controleer of de com+-systeem toepassing** actief is. Daarnaast moet de **gedistribueerde transactie-service** worden uitgevoerd als **netwerk service account**. Volg de stappen in dit artikel om problemen [met com+ en MSDTC op te lossen](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error).

### <a name="step-4-check-azure-backup-vm-extension-health"></a>Stap 4: de status van de VM-extensie controleren Azure Backup

Azure Backup maakt gebruik van de extensie van de VM-moment opname om een toepassings consistente back-up te maken van de virtuele machine van Azure. Azure Backup installeert de uitbrei ding als onderdeel van de eerste geplande back-up die is geactiveerd na het inschakelen van back-ups.

- **Zorg ervoor dat de VMSnapshot-extensie niet de status Mislukt heeft**: Volg de stappen in deze [sectie](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) om te controleren of de uitbrei ding van de Azure backup in orde is.

- **Controleer of de uitbrei ding door anti virus wordt geblokkeerd**: bepaalde antivirus software kan verhinderen dat extensies worden uitgevoerd.
  
  Controleer op het moment van de back-upfout of er logboek vermeldingen zijn in ***Logboeken toepassings logboeken** _ met _ *_fout toepassings naam: IaaSBcdrExtension.exe_* *. Als er vermeldingen worden weer geven, is het mogelijk dat de antivirus software die in de virtuele machine is geconfigureerd, de uitvoering van de back-upextensie beperkt. Test door de volgende mappen in de antivirus configuratie uit te sluiten en voer de back-upbewerking opnieuw uit.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **Controleer of netwerk toegang is vereist**: extensie pakketten worden gedownload uit de Azure Storage extensie opslagplaats en de uitbreidings status uploads worden naar Azure Storage gepost. [Meer informatie](../virtual-machines/extensions/features-windows.md#network-access).
  - Als u een niet-ondersteunde versie van de agent hebt, moet u uitgaande toegang tot Azure Storage in die regio vanuit de VM toestaan.
  - Als u de toegang tot `168.63.129.16` het gebruik van de gast firewall of met een proxy hebt geblokkeerd, zullen uitbrei dingen mislukken, ongeacht het bovenstaande. Poorten 80, 443 en 32526 zijn vereist. meer [informatie](../virtual-machines/extensions/features-windows.md#network-access).

- **Zorg ervoor dat DHCP is ingeschakeld in de gast-VM**: dit is vereist voor het ophalen van de host of het infrastructuur adres van DHCP voor het werken met de back-up van de IaaS-VM. Als u een statisch privé IP-adres nodig hebt, moet u dit configureren via de Azure Portal of Power shell en ervoor zorgen dat de DHCP-optie in de virtuele machine is ingeschakeld, [meer informatie](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken).

- **Zorg ervoor dat de VSS Writer-service actief is**: Volg deze stappen om [VSS Writer problemen op te lossen](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state).
- **Volg de richt lijnen voor back-upbest practice**: Bekijk de [Aanbevolen procedures voor het inschakelen van back-ups van Azure VM](backup-azure-vms-introduction.md#best-practices).
- **Lees de richt lijnen voor versleutelde schijven**: als u back-up voor virtuele machines met versleutelde schijf inschakelt, moet u alle vereiste machtigingen hebben opgegeven. Zie [back-up en herstel van versleutelde virtuele Azure-machines](backup-azure-vms-encryption.md)voor meer informatie.

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable: VM-agent kan niet communiceren met Azure Backup

**Fout code**: UserErrorGuestAgentStatusUnavailable <br>
**Fout bericht**: de VM-agent kan niet communiceren met Azure backup<br>

De Azure VM-agent is mogelijk gestopt, verouderd, in een inconsistente status of niet geïnstalleerd. Deze statussen verhinderen dat de Azure Backup-Service moment opnamen kan starten.

- **Open Azure Portal > VM-> instellingen > deel venster eigenschappen** > Controleer of de **status** van de VM **actief** is en de **agent status** **gereed** is. Als de VM-agent is gestopt of een inconsistente status heeft, start u de agent opnieuw op<br>
  - Voor virtuele Windows-machines voert u de volgende [stappen uit](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) om de gast agent opnieuw te starten.<br>
  - Voor Linux Vm's voert u deze [stappen uit](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) om de gast agent opnieuw te starten.
- **Open Azure Portal > VM > instellingen > extensies** > ervoor te zorgen dat alle uitbrei dingen de status **voltooid** hebben. Als dat niet het geval is, volgt u deze [stappen](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) om het probleem op te lossen.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError: kan niet communiceren met de VM-agent voor de status van de momentopname

**Fout code**: GuestAgentSnapshotTaskStatusError<br>
**Fout bericht**: kan niet communiceren met de VM-agent voor de status van de moment opname <br>

Nadat u een virtuele machine voor de Azure Backup-service hebt geregistreerd en gepland, wordt de taak door de back-up gestart door te communiceren met de back-upextensie van de VM om een moment opname van een bepaald tijdstip te maken. Een van de volgende situaties kan verhinderen dat de moment opname wordt geactiveerd. Als de moment opname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stappen voor probleem oplossing in de aangegeven volg orde uit en voer de bewerking opnieuw uit:  

**Oorzaak 1: [de agent is geïnstalleerd op de VM, maar reageert niet (voor Windows-vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Oorzaak 2: [de agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Oorzaak 3: [de status van de moment opname kan niet worden opgehaald of een moment opname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Oorzaak 4: er [zijn geen configuratie opties voor de VM-agent ingesteld (voor Linux-vm's)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**Oorzaak 5: de [oplossing voor toepassings beheer blokkeert IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed: de VM bevindt zich in een mislukte inrichtingsstatus

**Fout code**: UserErrorVmProvisioningStateFailed<br>
**Fout bericht**: de inrichtings status van de virtuele machine is mislukt<br>

Deze fout treedt op wanneer een van de uitbrei dingen de virtuele machine inricht bij het inrichten van de status mislukt.<br>**Open Azure Portal > VM > instellingen > extensies > uitbrei dingen status** en controleer of alle uitbrei dingen de status **geslaagd** hebben. Zie [inrichtings statussen](../virtual-machines/states-billing.md)voor meer informatie.

- Als een uitbrei ding een mislukte status heeft, kan deze de back-up verstoren. Zorg ervoor dat de problemen met de extensie zijn opgelost en voer de back-upbewerking opnieuw uit.
- Als de inrichtings status van de virtuele machine een update status heeft, kan deze de back-up verstoren. Zorg ervoor dat deze in orde is en voer de back-upbewerking opnieuw uit.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached: de limiet voor de verzameling herstelpunten is bereikt

**Fout code**: UserErrorRpCollectionLimitReached <br>
**Fout bericht**: de maximale limiet voor de verzameling met herstel punten is bereikt. <br>

- Dit probleem kan zich voordoen als er een vergren deling van de resource groep herstel punt wordt voor komen dat het automatisch opruimen van herstel punten wordt verhinderd.
- Dit probleem kan ook optreden als meerdere back-ups per dag worden geactiveerd. Momenteel wordt slechts één back-up per dag aanbevolen, omdat de onmiddellijke herstel punten gedurende 1-5 dagen worden bewaard per de geconfigureerde moment opname en slechts 18 direct RPs kunnen worden gekoppeld aan een VM op een bepaald moment. <br>
- Het aantal herstel punten tussen herstel punt verzamelingen en resource groepen voor een virtuele machine mag niet groter zijn dan 18. Als u een nieuw herstel punt wilt maken, verwijdert u de bestaande herstel punten.

Aanbevolen actie:<br>
U kunt dit probleem oplossen door de vergren deling van de resource groep van de virtuele machine te verwijderen en de bewerking opnieuw uit te voeren om het opschonen te activeren.
> [!NOTE]
> Backup-service maakt een afzonderlijke resource groep dan de resource groep van de virtuele machine om de herstel punt verzameling op te slaan. U wordt aangeraden de resource groep die is gemaakt voor gebruik door de back-upservice, niet te vergren delen. De naamgevings indeling van de resource groep die is gemaakt door de back-upservice is: AzureBackupRG_ `<Geo>` _ `<number>` . Bijvoorbeeld: *AzureBackupRG_northeurope_1*

**Stap 1: [de vergren deling van de resource groep voor het herstel punt verwijderen](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Stap 2: [herstel punt verzameling opschonen](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured-backup heeft niet voldoende machtigingen voor de sleutel kluis voor het maken van een back-up van versleutelde Vm's

**Fout code**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Fout bericht**: back-up beschikt niet over voldoende machtigingen voor de sleutel kluis voor het maken van back-ups van versleutelde vm's. <br>

Een back-upbewerking kan alleen worden uitgevoerd op versleutelde Vm's als deze over machtigingen voor toegang tot de sleutel kluis beschikt. Machtigingen kunnen worden ingesteld via de [Azure Portal](./backup-azure-vms-encryption.md) of via [Power shell](./backup-azure-vms-automation.md#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - Momentopname mislukt omdat er geen netwerkverbinding beschikbaar is op de virtuele machine

**Fout code**: ExtensionSnapshotFailedNoNetwork<br>
**Fout bericht**: de momentopname bewerking is mislukt omdat er geen netwerk verbinding is op de virtuele machine<br>

Nadat u een virtuele machine voor de Azure Backup-service hebt geregistreerd en gepland, wordt de taak door de back-up gestart door te communiceren met de back-upextensie van de VM om een moment opname van een bepaald tijdstip te maken. Een van de volgende situaties kan verhinderen dat de moment opname wordt geactiveerd. Als de moment opname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stap voor het oplossen van problemen uit en voer de bewerking opnieuw uit:

**[De status van de moment opname kan niet worden opgehaald, of een moment opname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks: VMSnapshot-extensiebewerking mislukt

**Fout code**: ExtensionOperationFailedForManagedDisks <br>
**Fout bericht**: de VMSnapshot-extensie bewerking is mislukt<br>

Nadat u een virtuele machine voor de Azure Backup-service hebt geregistreerd en gepland, wordt de taak door de back-up gestart door te communiceren met de back-upextensie van de VM om een moment opname van een bepaald tijdstip te maken. Een van de volgende situaties kan verhinderen dat de moment opname wordt geactiveerd. Als de moment opname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stappen voor probleem oplossing in de aangegeven volg orde uit en voer de bewerking opnieuw uit:  
**Oorzaak 1: [de status van de moment opname kan niet worden opgehaald of een moment opname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Oorzaak 2: [de agent is geïnstalleerd in de VM, maar reageert niet (voor Windows-vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Oorzaak 3: [de agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - Back-up mislukt met een interne fout

**Fout code**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Fout bericht**: de back-up is mislukt met een interne fout. Voer de bewerking over een paar minuten opnieuw uit <br>

Nadat u een virtuele machine voor de Azure Backup-service hebt geregistreerd en gepland, wordt met de back-up de taak gestart door te communiceren met de back-upextensie van de VM om een tijdgebonden moment opname te maken. Een van de volgende situaties kan verhinderen dat de moment opname wordt geactiveerd. Als de moment opname niet wordt geactiveerd, kan er een back-upfout optreden. Voer de volgende stappen voor probleem oplossing in de aangegeven volg orde uit en voer de bewerking opnieuw uit:  
**Oorzaak 1: [de agent die is geïnstalleerd op de VM, maar die niet reageert (voor Windows-vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Oorzaak 2: [de agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Oorzaak 3: [de status van de moment opname kan niet worden opgehaald of een moment opname kan niet worden gemaakt](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Oorzaak 4: [de back-upservice heeft geen machtiging voor het verwijderen van de oude herstel punten vanwege een vergren deling van een resource groep](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize: de geconfigureerde schijf grootte (s) wordt momenteel niet ondersteund door de Azure Backup

**Fout code**: UserErrorUnsupportedDiskSize <br>
**Fout bericht**: de geconfigureerde schijf grootte (s) wordt momenteel niet ondersteund door Azure backup. <br>

De back-upbewerking kan mislukken bij het maken van een back-up van een virtuele machine met een schijf grootte van meer dan 32 TB. Het maken van een back-up van versleutelde schijven met een grootte van meer dan 4 TB wordt momenteel niet ondersteund. Zorg ervoor dat de schijf grootte (s) kleiner is dan of gelijk is aan de ondersteunde limiet door de schijven te splitsen.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress: kan geen back-up initiëren omdat er momenteel een andere back-upbewerking wordt uitgevoerd

**Fout code**: UserErrorBackupOperationInProgress <br>
**Fout bericht**: kan de back-up niet starten omdat er momenteel een andere back-upbewerking wordt uitgevoerd<br>

De recente back-uptaak is mislukt, omdat er een bestaande back-uptaak wordt uitgevoerd. U kunt pas een nieuwe back-uptaak starten als de huidige taak is voltooid. Controleer of de back-upbewerking die momenteel wordt uitgevoerd, is voltooid voordat u een andere back-up maakt of plant. Als u de status van de back-uptaken wilt controleren, voert u de volgende stappen uit:

1. Meld u aan bij de Azure Portal en selecteer **alle services**. Typ Recovery Services en selecteer **Recovery Services kluizen**. De lijst met Recovery Services-kluizen wordt weergegeven.
2. Selecteer in de lijst met Recovery Services kluizen een kluis waarin de back-up is geconfigureerd.
3. Selecteer in het menu van het kluis dashboard **back-uptaken** . alle back-uptaken worden weer gegeven.
   - Als er een back-uptaak wordt uitgevoerd, wacht u totdat deze klaar is of annuleert u de back-uptaak.
     - Als u de back-uptaak wilt annuleren, klikt u met de rechter muisknop op de back-uptaak en selecteert u **Annuleren** of [Power shell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob)gebruiken.
   - Als u de back-up opnieuw hebt geconfigureerd in een andere kluis, controleert u of er geen back-uptaken worden uitgevoerd in de oude kluis. Als deze bestaat, annuleert u de back-uptaak.
     - Als u de back-uptaak wilt annuleren, klikt u met de rechter muisknop op de back-uptaak en selecteert u **Annuleren** of [Power shell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) gebruiken
4. Voer de back-upbewerking opnieuw uit.

Als de geplande back-upbewerking langer duurt, conflicteert met de volgende back-upconfiguratie, raadpleegt u de [Aanbevolen procedures](backup-azure-vms-introduction.md#best-practices), [back-upprestaties](backup-azure-vms-introduction.md#backup-performance)en [Restore-overwegingen](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError: de back-up is mislukt vanwege een fout. Zie de details in het bericht met de taakfout voor meer informatie

**Fout code**: UserErrorCrpReportedUserError <br>
**Fout bericht**: het maken van de back-up is mislukt vanwege een fout. Zie Details van het taak fout bericht voor meer informatie.

Deze fout wordt gerapporteerd vanuit de IaaS-VM. Als u de hoofd oorzaak van het probleem wilt vaststellen, gaat u naar de Recovery Services kluis instellingen. Selecteer in de sectie **controle** de optie **back-uptaken** om de status te filteren en weer te geven. Selecteer **fouten** om de details van het onderliggende fout bericht te controleren. Onderneem verdere acties op basis van de aanbevelingen op de pagina met fout Details.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent-back-up is mislukt: deze virtuele machine is niet (actief) beveiligd door Azure Backup

**Fout code**: UserErrorBcmDatasourceNotPresent <br>
**Fout bericht**: het maken van de back-up is mislukt: deze virtuele machine wordt niet (actief) beveiligd door Azure backup.

Controleer of de opgegeven virtuele machine actief is (niet de status PAUSE) die wordt beveiligd door Azure Backup. Als u dit probleem wilt verholpen, controleert u of de virtuele machine actief is en voert u de bewerking opnieuw uit.

## <a name="causes-and-solutions"></a>Oorzaken en oplossingen

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>De agent is geïnstalleerd op de VM, maar reageert niet (voor Windows-Vm's)

#### <a name="solution-for-this-error"></a>Oplossing voor deze fout

De VM-agent is mogelijk beschadigd of de service is gestopt. Het opnieuw installeren van de VM-agent helpt de meest recente versie op te halen. Het helpt ook om communicatie met de service opnieuw te starten.

1. Bepaal of de Windows Azure Guest Agent-service wordt uitgevoerd in de VM-Services (Services. msc). Probeer de service Windows Azure Guest agent opnieuw te starten en de back-up te initiëren.
2. Als de Windows Azure Guest Agent-service niet wordt weer gegeven in Services, gaat u in het configuratie scherm naar **Program ma's en onderdelen** om te bepalen of de service Windows Azure Guest agent is geïnstalleerd.
3. Als de Windows Azure Guest-agent wordt weer gegeven in **Program ma's en onderdelen**, verwijdert u de Windows Azure-gast agent.
4. Down load en installeer de [nieuwste versie van de agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). U moet over beheerders rechten beschikken om de installatie te volt ooien.
5. Controleer of de services van de Windows Azure Guest agent in Services worden weer gegeven.
6. Een back-up op aanvraag uitvoeren:
   - Selecteer in de portal **Nu back-up maken**.

Controleer ook of [Microsoft .NET 4,5 is geïnstalleerd](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) in de VM. .NET 4,5 is vereist voor de VM-agent om te communiceren met de service.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>De agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-Vm's)

#### <a name="solution"></a>Oplossing

De meeste fout-en extensie-gerelateerde storingen voor Linux-Vm's worden veroorzaakt door problemen die van invloed zijn op een verouderde VM-agent. Volg deze algemene richt lijnen om dit probleem op te lossen:

1. Volg de instructies voor [het bijwerken van de Linux VM-agent](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > We *raden u ten zeerste* aan de agent alleen bij te werken via een distributie opslagplaats. Het is niet raadzaam de agent code rechtstreeks vanuit GitHub te downloaden en bij te werken. Als de meest recente agent voor uw distributie niet beschikbaar is, neemt u contact op met de distributie ondersteuning voor instructies over het installeren ervan. Als u de meest recente agent wilt controleren, gaat u naar de pagina van de [Windows Azure Linux-agent](https://github.com/Azure/WALinuxAgent/releases) in de GitHub-opslag plaats.

2. Zorg ervoor dat de Azure-agent op de virtuele machine wordt uitgevoerd door de volgende opdracht uit te voeren: `ps -e`

   Als het proces niet wordt uitgevoerd, start u het opnieuw met behulp van de volgende opdrachten:

   - Voor Ubuntu: `service walinuxagent start`
   - Voor andere distributies: `service waagent start`

3. [Configureer de agent voor automatisch opnieuw opstarten](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Voer een nieuwe test back-up uit. Als de fout zich blijft voordoen, verzamelt u de volgende logboeken van de VM:

   - /var/lib/waagent/*. XML
   - /var/log/waagent.log
   - /var/log/azure/*

Als u uitgebreide logboek registratie voor waagent nodig hebt, voert u de volgende stappen uit:

1. Zoek in het bestand/etc/waagent.conf de volgende regel: **uitgebreide logboek registratie inschakelen (y | n)**
2. Wijzig de **Logboeken. uitgebreide** waarde van *n* tot en met *y*.
3. Sla de wijziging op en start waagent opnieuw door de stappen uit te voeren die eerder in deze sectie zijn beschreven.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>Er zijn geen VM-Agent configuratie opties ingesteld (voor Linux-Vm's)

Een configuratie bestand (/etc/waagent.conf) regelt de acties van waagent. Opties voor configuratie bestand **extensies. enable** moet zijn ingesteld op **y** en **inrichting.** voor het werken moet de agent worden ingesteld op **auto** .
Zie voor een volledige lijst met opties voor VM-Agent configuratie bestand <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>De oplossing voor toepassings beheer blokkeert IaaSBcdrExtension.exe

Als u [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (of een andere oplossing voor toepassings beheer) uitvoert en de regels zijn gebaseerd op een uitgever of pad, kunnen ze ervoor zorgen dat het uitvoer bare bestand van **IaaSBcdrExtension.exe** niet kan worden uitgevoerd.

#### <a name="solution-to-this-issue"></a>Oplossing voor dit probleem

Sluit het `/var/lib` pad of het uitvoer bare **IaaSBcdrExtension.exe** uit van AppLocker (of andere software voor toepassings beheer).

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>De status van de moment opname kan niet worden opgehaald, of een moment opname kan niet worden gemaakt

De back-up van de virtuele machine is afhankelijk van het uitgeven van een momentopname opdracht aan het onderliggende opslag account. Het maken van een back-up kan mislukken omdat deze geen toegang heeft tot het opslag account, of omdat de uitvoering van de momentopname taak is vertraagd.

#### <a name="solution-for-this-issue"></a>Oplossing voor dit probleem

De volgende voor waarden kunnen ertoe leiden dat de momentopname taak mislukt:

| Oorzaak | Oplossing |
| --- | --- |
| De status van de virtuele machine wordt onjuist gerapporteerd, omdat de VM wordt afgesloten in Remote Desktop Protocol (RDP). | Als u de virtuele machine in RDP afsluit, controleert u de portal om te bepalen of de status van de virtuele machine juist is. Als dat niet het geval is, sluit u de virtuele machine in de portal af met de optie **Afsluiten** in het VM-dash board. |
| De VM kan de host of het infrastructuur adres niet ophalen van DHCP. | DHCP moet zijn ingeschakeld in de gast voor het werken met de IaaS VM-back-up. Als de VM de host of het infrastructuur adres niet kan ophalen van DHCP-antwoord 245, kan er geen uitbrei dingen worden gedownload of uitgevoerd. Als u een statisch privé IP-adres nodig hebt, moet u dit configureren via de **Azure Portal** of **Power shell** en ervoor zorgen dat de DHCP-optie in de virtuele machine is ingeschakeld. Meer [informatie](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) over het instellen van een statisch IP-adres met Power shell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Vergren deling van de resource groep herstel punt verwijderen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Ga naar de **optie alle resources** en selecteer de resource groep voor de verzameling van herstel punten in de volgende indeling AzureBackupRG_ `<Geo>` _ `<number>` .
3. Selecteer in de sectie **instellingen** de optie **vergren** delen om de vergren delingen weer te geven.
4. Als u de vergren deling wilt verwijderen, selecteert u het weglatings teken en selecteert u **verwijderen**.

    ![Vergren deling verwijderen](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> Herstel punt verzameling opruimen

Na het verwijderen van de vergren deling, moeten de herstel punten worden opgeruimd.

Als u de resource groep van de virtuele machine of de virtuele machine zelf verwijdert, blijven de moment opnamen voor direct terugzetten van beheerde schijven actief en verlopen op basis van de Bewaar termijn. Als u de moment opnamen voor direct terugzetten wilt verwijderen (als u deze niet meer nodig hebt) die zijn opgeslagen in de herstel punt verzameling, reinigt u de verzameling met herstel punten op basis van de onderstaande stappen.

Volg een van de volgende methoden om de herstel punten op te schonen:<br>

- [Herstel punt verzameling opschonen door back-ups op aanvraag uit te voeren](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Herstel punt verzameling opschonen van Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Herstel punt verzameling opschonen door back-ups op aanvraag uit te voeren

Nadat u de vergren deling hebt verwijderd, moet u een back-up op aanvraag starten. Met deze actie wordt ervoor gezorgd dat de herstel punten automatisch worden opgeruimd. Verwacht dat deze on-demand-bewerking de eerste keer mislukt. Het zorgt er echter voor dat automatisch opschonen in plaats van het hand matig verwijderen van herstel punten. Na het opschonen moet de volgende geplande back-up slagen.

> [!NOTE]
> Automatische opschoning gebeurt na enkele uren dat de back-up op aanvraag wordt geactiveerd. Als uw geplande back-up nog steeds mislukt, probeert u de verzameling met herstel punten hand matig te verwijderen met behulp van de stappen die [hier](#clean-up-restore-point-collection-from-azure-portal)worden beschreven.

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Herstel punt verzameling opschonen van Azure Portal <br>

Voer de volgende stappen uit om de verzameling met herstel punten hand matig te wissen, wat niet wordt gewist vanwege de vergren deling van de resource groep:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer **alle resources** in het menu **hub** , selecteer de resource groep met de volgende indeling AzureBackupRG_ `<Geo>` _ `<number>` waar de virtuele machine zich bevindt.

    ![De resourcegroep selecteren](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Selecteer resource groep, het deel venster **overzicht** wordt weer gegeven.
4. Selecteer de optie **verborgen typen weer geven** om alle verborgen resources weer te geven. Selecteer de herstel punt verzamelingen met de volgende indeling AzureBackupRG_ `<VMName>` _ `<number>` .

    ![De verzameling herstel punten selecteren](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Selecteer **verwijderen** om de verzameling met herstel punten op te schonen.
6. Voer de back-upbewerking opnieuw uit.

> [!NOTE]
 >Als de resource (RP-verzameling) een groot aantal herstel punten heeft, kan de time-out van de-portal worden verwijderd en mislukt. Dit is een bekend CRP-probleem, waarbij alle herstel punten niet worden verwijderd binnen de vastgestelde tijd en er een time-out optreedt voor de bewerking. De verwijderings bewerking slaagt echter normaal gesp roken na twee of drie nieuwe pogingen.
