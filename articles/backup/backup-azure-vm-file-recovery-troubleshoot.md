---
title: Problemen met Azure VM File Recovery oplossen
description: Problemen oplossen bij het herstellen van bestanden en mappen vanuit een Azure VM-back-up.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: bd369577e320cf6dca510183948f41e6cf91779b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605289"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Problemen oplossen in bestands herstel van Azure VM backup

Dit artikel bevat probleemoplossings stappen die u kunnen helpen bij het oplossen van Azure Backup fouten met betrekking tot problemen bij het herstellen van bestanden en mappen vanuit een back-up van een Azure-VM.

## <a name="common-error-messages"></a>Veelvoorkomende foutberichten

### <a name="exception-caught-while-connecting-to-target"></a>Uitzonde ring opgetreden tijdens het verbinden met het doel

**Mogelijke oorzaak**: het script kan geen toegang krijgen tot het herstel punt.

**Aanbevolen actie**: Controleer of de computer voldoet aan alle [toegangs vereisten](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>Het doel is al aangemeld via een iSCSI-sessie

**Mogelijke oorzaak**: het script is al uitgevoerd op dezelfde computer en de stations zijn gekoppeld.

**Aanbevolen actie**: de volumes van het herstel punt zijn al gekoppeld. Ze kunnen niet worden gekoppeld met dezelfde stationsletters van de oorspronkelijke VM. Blader door alle beschik bare volumes in Verkenner.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Dit script is ongeldig omdat de schijven zijn ontkoppeld via de portal, de limiet van 12 uur is overschreden. Een nieuw script downloaden vanuit de portal

**Mogelijke oorzaak**: de schijven zijn ontkoppeld van de portal of de limiet van 12 uur is overschreden.

**Aanbevolen actie**: het script is ongeldig na 12 uur na het moment dat het is gedownload en kan niet worden uitgevoerd. Ga naar de portal en down load een nieuw script om door te gaan met het herstellen van bestanden.

## <a name="troubleshooting-common-scenarios"></a>Oplossingen voor veelvoorkomende problemen

In deze sectie vindt u de stappen voor het oplossen van problemen die zich kunnen voordoen tijdens het downloaden en uitvoeren van het script voor bestands herstel.

### <a name="cant-download-the-script"></a>Kan het script niet downloaden

Aanbevolen actie:

1. Zorg ervoor dat u over de [vereiste machtigingen beschikt om het script te downloaden](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
1. Zorg ervoor dat er verbinding is met de Azure doel-Ip's

Voer een van de volgende opdrachten uit vanaf een opdracht prompt met verhoogde bevoegdheid om de verbinding te controleren.

`nslookup download.microsoft.com`

of

`ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>Het script wordt gedownload, maar kan niet worden uitgevoerd

#### <a name="for-sles-12-sp4-os-linux"></a>Voor SLES 12 SP4 OS (Linux)

**Fout**: kan de iscsi_tcp_module niet vinden

**Mogelijke oorzaak**: tijdens het uitvoeren van het python-script voor herstel op item niveau (ILR) op SuSE Linux OS-versie 12sp4, mislukt de fout **_iscsi_tcp module kan niet worden geladen_* _.

De ILR-module maakt gebruik van _ *iscsi_tcp** om een TCP-verbinding met de back-upservice tot stand te brengen. Als onderdeel van de 12SP4-release heeft SUSE **iscsi_tcp** verwijderd uit het open-iscsi-pakket, waardoor de ILR-bewerking mislukt.

**Aanbevolen actie**: de uitvoering van het script voor bestands herstel wordt niet ondersteund op SuSE 12SP4 vm's. Voer de herstel bewerking uit op een oudere versie van SUSE 12SP4.

### <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>Het script wordt uitgevoerd, maar er is geen verbinding met het iSCSI-doel

**Fout: er** is een uitzonde ring opgetreden bij het verbinden met het doel

1. Zorg ervoor dat de computer waarop het script wordt uitgevoerd, voldoet aan alle [toegangs vereisten](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
1. Controleer op de connectiviteit met Azure target Ip's.
Voer een van de volgende opdrachten uit vanaf een opdracht prompt met verhoogde bevoegdheid om de verbinding te controleren.

   `nslookup download.microsoft.com`

   of

   `ping download.microsoft.com`
1. Zorg ervoor dat u toegang hebt tot de uitgaande iSCSI-poort 3260.
1. Zorg ervoor dat er geen firewall-of NSG is die verkeer naar Azure target Ip's of Url's van de Recovery service blokkeert.
1. Controleer of de uitvoering van het script wordt geblokkeerd door de anti virus.

### <a name="connected-to-recovery-point-but-disks-didnt-get-attached"></a>Verbonden met herstel punt, maar de schijven zijn niet gekoppeld

Zorg ervoor dat u de [juiste computer hebt om het script uit te voeren](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

#### <a name="on-a-windows-vm"></a>Op een Windows-VM

De **opslag groep op de VM wordt gekoppeld in de modus alleen-lezen**: in Windows 2012 R2 en Windows 2016 (met opslag groepen) wanneer het script voor de eerste keer wordt uitgevoerd, kan de opslag groep die is gekoppeld aan de virtuele machine de status alleen-lezen hebben.

Om dit probleem op te lossen, moet u de Read-Write voor de eerste keer hand matig instellen voor de opslag groep en de virtuele schijven koppelen. Volg de onderstaande stappen:

1. Stel Read-Write toegang in.

   Navigeer naar **Serverbeheer**  >  opslag groepen voor **Bestands-en opslag Services**  >    >  .

   ![Windows-opslag](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. Klik in het venster **opslag groep** met de rechter muisknop op de beschik bare opslag groep en selecteer de optie **Read-Write toegang instellen** .

   ![Lees-en schrijf bewerkingen Windows-opslag](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Nadat de opslag groep is toegewezen met lees-en schrijf toegang, koppelt u de virtuele schijf.

   Navigeer naar **Serverbeheer gebruikers interface**. Klik onder de sectie **virtuele schijven** > met de rechter muisknop om de optie **virtuele schijf koppelen** te selecteren.

   ![Virtuele schijf van server beheer](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="on-a-linux-vm"></a>Op een virtuele Linux-machine

##### <a name="file-recovery-fails-to-auto-mount-because-disk-doesnt-contain-volumes"></a>Bestands herstel kan niet automatisch worden gekoppeld omdat de schijf geen volumes bevat

Tijdens het herstel van bestanden detecteert de back-upservice volumes en auto koppelingen. Als de schijven met een back-up echter onbewerkte partities hebben, worden deze schijven niet automatisch gekoppeld en kunt u de gegevens schijf niet voor herstel zien.

Volg de stappen die in dit [artikel](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)worden beschreven om dit probleem op te lossen.

##### <a name="the-os-couldnt-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>Het besturings systeem kan het bestands systeem niet identificeren waardoor Linux-bestands herstel mislukt tijdens het koppelen van schijven

Tijdens het uitvoeren van het script voor bestands herstel kan de gegevens schijf niet worden gekoppeld met de volgende fout:

"De volgende partities zijn niet gekoppeld omdat het besturings systeem het bestands systeem niet kan identificeren"

U kunt dit probleem oplossen door te controleren of het volume is versleuteld met een toepassing van derden. Als het is versleuteld, wordt de schijf of virtuele machine niet weer gegeven als versleuteld op de portal.

1. Meld u aan bij de back-up van de virtuele machine en voer de volgende opdracht uit:

   `*lsblk -f*`

   ![Schijf zonder volume](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Controleer het bestands systeem en de versleuteling.
3. Als het volume is versleuteld, wordt bestands herstel niet ondersteund. [Meer informatie](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

### <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Schijven zijn gekoppeld, maar kunnen geen volumes koppelen

- Zorg ervoor dat u de [juiste computer hebt om het script uit te voeren](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

#### <a name="on-windows-vms"></a>Op Windows-Vm's

Tijdens het uitvoeren van het script voor bestands herstel voor Windows wordt er een bericht weer gegeven dat er ***0 volumes zijn gekoppeld** _. De schijven worden echter wel gedetecteerd in de schijf beheer-console. Bij het koppelen van volumes via iSCSI, gaan sommige gedetecteerde volumes naar een offline status. Wanneer het iSCSI-kanaal tussen de virtuele machine en de service communiceert, detecteert deze volumes en brengt deze online, maar ze worden niet gekoppeld.

   ![De schijf is niet gekoppeld](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Voer de volgende stappen uit om dit probleem op te sporen en op te lossen:

1. Ga naar _ *schijf beheer** door de opdracht **diskmgmt** uit te voeren in het CMD-venster.
1. Controleer of er extra schijven worden weer gegeven. In het volgende voor beeld is schijf 2 een extra schijf.

   ![Schijf management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Klik met de rechter muisknop op het **nieuwe volume** en selecteer **stationsletter en paden wijzigen**.

   ![Schijf management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. Selecteer in het venster stationsletter **of pad toevoegen** de optie **de volgende stationsletter toewijzen** en wijs een beschikbaar station toe en selecteer **OK**.

   ![Schijf management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Bekijk in de Verkenner de stationsletter die u hebt gekozen en verken de bestanden.

#### <a name="on-linux-vms"></a>Op virtuele Linux-machines

- Zorg ervoor dat u de [juiste computer hebt om het script uit te voeren](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Als de beveiligde virtuele Linux-machine gebruikmaakt van LVM-of RAID-matrices, volgt u de stappen in dit [artikel](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

### <a name="cant-copy-the-files-from-mounted-volumes"></a>Kan de bestanden niet kopiëren van gekoppelde volumes

De kopie kan mislukken met de fout **0x80070780: het bestand kan niet worden geopend door het systeem.** Controleer in dit geval of schijf ontdubbeling is ingeschakeld op de bron server. Zorg ervoor dat voor de herstel server ook ontdubbeling is ingeschakeld op de stations. U kunt de functie ontdubbeling niet-geconfigureerd laten, zodat u de stations op de Restore-server niet ontdubbelt.

## <a name="next-steps"></a>Volgende stappen

- [Bestanden en mappen herstellen vanuit back-up van virtuele Azure-machine](backup-azure-restore-files-from-vm.md)
