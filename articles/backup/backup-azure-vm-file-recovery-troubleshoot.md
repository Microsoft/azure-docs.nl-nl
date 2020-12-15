---
title: Problemen met Azure VM File Recovery oplossen
description: Problemen oplossen die specifiek zijn voor het herstellen van bestanden en mappen vanuit een Azure VM-back-up.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513338"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Problemen oplossen in bestands herstel van Azure VM backup

Dit artikel bevat probleemoplossings stappen die u kunnen helpen bij het oplossen van Azure Backup fouten met betrekking tot problemen die specifiek zijn voor het herstellen van bestanden en mappen vanuit een Azure VM-back-up. 

## <a name="exception-caught-while-connecting-to-target"></a>Uitzonde ring opgetreden tijdens het verbinden met het doel

Mogelijke oorzaak: het script kan geen toegang krijgen tot de aanbevolen actie voor het herstel punt: Controleer of de computer [voldoet aan alle toegangs vereisten](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>Het doel is al aangemeld via een iSCSI-sessie

Mogelijke oorzaak: het script is al uitgevoerd op dezelfde computer en de stations zijn gekoppeld.
Aanbevolen actie: de volumes van het herstel punt zijn al gekoppeld. Ze kunnen niet worden gekoppeld met dezelfde stationsletters van de oorspronkelijke VM. Blader door alle beschik bare volumes in Verkenner.

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Dit script is ongeldig omdat de schijven zijn ontkoppeld via de portal, de limiet van 12 uur is overschreden. Een nieuw script downloaden vanuit de portal

Mogelijke oorzaak: de schijven zijn ontkoppeld van de portal of de limiet van 12 uur is overschreden.
Aanbevolen actie: het script is na 12 uur na het downloaden van de tijd ongeldig en kan niet worden uitgevoerd. Ga naar de portal en down load een nieuw script om door te gaan met het herstellen van bestanden.

## <a name="troubleshooting-common-issues"></a>Oplossen van algemene problemen

Deze sectie bevat stappen voor het oplossen van problemen die zijn opgetreden tijdens het downloaden en uitvoeren van het script voor bestands herstel.

## <a name="cannot-download-the-script"></a>Kan het script niet downloaden

Aanbevolen actie:

1. Zorg ervoor dat u over de [vereiste machtigingen beschikt om het script te downloaden](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
2. Zorg ervoor dat er verbinding is met Azure-doel-IP ('s).
Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid om de verbinding te controleren. 
    - *nslookup download.Microsoft.com* of
    - *ping download.microsoft.com*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>Het script wordt gedownload, maar kan niet worden uitgevoerd

### <a name="for-sles-12-sp4-os-linux"></a>Voor SLES 12 SP4 OS (Linux)

Fout: kan de iscsi_tcp_module niet vinden

Mogelijke oorzaak: tijdens het uitvoeren van het python-script voor herstel op item niveau in SUSE Linux OS-versie 12sp4, mislukt dit met een fout ***iscsi_tcp module kan niet worden geladen** _. De ILR-module maakt gebruik van iscsi_tcp om een TCP-verbinding tot stand te brengen met de back-upservice, SUSE als onderdeel van de 12SP4-release verwijderd iscsi_tcp van een open-iscsi-pakket, waardoor de ILR-bewerking mislukt.

Aanbevolen actie: het uitvoeren van het script voor het herstellen van bestanden wordt niet ondersteund op SUSE 12SP4 Vm's en probeer de herstel bewerking uit te voeren op een oudere versie van SUSE 12SP4.

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>Het script wordt uitgevoerd, maar er is geen verbinding met het iSCSI-doel

Fout: er is een uitzonde ring opgetreden bij het verbinden met het doel

1. Zorg ervoor dat de computer waarop het script wordt uitgevoerd, voldoet aan alle [toegangs vereisten](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
2. Controleer op de connectiviteit met Azure-doel-IP (s).
Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid om de verbinding te controleren. 
    - _nslookup download.microsoft.com * of<br>
    - *ping download.microsoft.com*
3. Zorg ervoor dat er toegang is tot de uitgaande iSCSI-poort 3260.
4. Zorg ervoor dat er geen firewall-NSG zijn die verkeer naar Azure-doel-IP (s) of Url's van de herstel service blokkeert.
5. Controleer of de uitvoering van het script wordt geblokkeerd door anti virus.

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>Verbonden met herstel punt, maar de schijven zijn niet gekoppeld

Zorg ervoor dat u de [juiste computer hebt om het script uit te voeren](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

### <a name="on-windows-vm"></a>Op Windows VM

De **opslag groep op de VM wordt gekoppeld in de modus alleen-lezen** Voor Windows 2012 R2 en Windows 2016 (met opslag groep), terwijl het script voor de eerste keer wordt uitgevoerd, kan de opslag groep die is gekoppeld aan de virtuele machine de status alleen-lezen hebben.

Om dit probleem op te lossen, moet u de Read-Write de toegang voor de eerste keer hand matig instellen voor de opslag groep en de virtuele schijven koppelen. Volg de onderstaande stappen:

1. Stel Read-Write toegang in.
Navigeer naar Serverbeheer > bestands-en opslag Services > volumes > opslag groepen.

   ![Windows-opslag](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. Klik in het venster **opslag groep** met de rechter muisknop op de beschik bare opslag groep en selecteer **instellen Read-Write optie toegang** .

   ![Lees-en schrijf bewerkingen Windows-opslag](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. Nadat de opslag groep is toegewezen met lees-en schrijf toegang, moeten we de virtuele schijf koppelen.
Navigeer naar **Serverbeheer gebruikers interface**, onder de sectie virtuele schijven > Klik met de rechter muisknop om de optie **virtuele schijf koppelen** te selecteren.

   ![Virtuele schijf van server beheer](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>Op Linux VM

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>Bestands herstel kan niet automatisch worden gekoppeld omdat de schijf geen volumes bevat

Bij het uitvoeren van bestands herstel detecteert de back-upservice volumes en automatische koppelingen. Als de schijven met een back-up echter onbewerkte partities hebben, worden deze schijven niet automatisch gekoppeld en kunnen de gegevens schijf niet worden weer gegeven voor herstel.

Volg de stappen die in dit [artikel](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)worden beschreven om dit probleem op te lossen.
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>Het besturings systeem kan het bestands systeem niet identificeren waardoor Linux-bestands herstel mislukt tijdens het koppelen van schijven

Tijdens het uitvoeren van de bestands herstel script gegevens schijf kan niet worden gekoppeld met de onderstaande fout: *de volgende partities zijn niet gekoppeld, omdat het besturings systeem het bestands systeem niet kan identificeren*

- U kunt dit probleem oplossen door te controleren of het volume is versleuteld met een toepassing van derden. Als versleuteld, wordt de schijf of virtuele machine niet weer gegeven als versleuteld op de portal.
1. Meld u aan bij de back-up van de virtuele machine en voer de volgende opdracht uit: *lsblk-f*<br>

   ![Schijf zonder volume](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Controleer het bestands systeem en de versleuteling.
3. Als het volume is versleuteld, wordt bestands herstel niet ondersteund. [Meer informatie](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Schijven zijn gekoppeld, maar kunnen geen volumes koppelen

- Zorg ervoor dat u de [juiste computer hebt om het script uit te voeren](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="on-windows-vm"></a>Op Windows VM

Tijdens het uitvoeren van het script voor bestands herstel voor Windows wordt er een bericht weer gegeven dat er ***0 volumes zijn gekoppeld** _, maar de schijven worden gedetecteerd in de schijf beheer console. Bij het koppelen van volumes via iSCSI, gaan sommige volumes die worden gedetecteerd, naar de offline status. Wanneer het iSCSI-kanaal tussen de virtuele machine en de service communiceert, worden deze volumes gedetecteerd en online gebracht, maar niet gekoppeld.

   ![De schijf is niet gekoppeld](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Voer de volgende stappen uit om dit probleem op te sporen en op te lossen:

1. Ga naar _ *schijf beheer** door de opdracht **diskmgmt** uit te voeren in het venster cmd.
2. Controleer of er extra schijven worden weer gegeven. In het onderstaande voor beeld is schijf 2 een extra schijf.

   ![Schijf management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. Klik met de rechter muisknop op het **volume** en selecteer **stationsletter en paden wijzigen**.

   ![Schijf management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. Selecteer in het venster stationsletter **of pad toevoegen** **de volgende stationsletter toewijzen** en wijs een beschikbaar station toe en klik op **OK**. 

   ![Schijf management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. Vanuit Verkenner kunt u het station weer geven.

   ![Schijf management3](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. De nieuwe volumes moeten worden bijgevoegd.  

   ![Schijf niet koppelen](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. In Verkenner zijn de nieuwe volumes zichtbaar nadat het script opnieuw is uitgevoerd.

   ![De schijf is niet mounting1](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![De schijf is niet mounting2](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>Op virtuele Linux-machines 

- Zorg ervoor dat u de [juiste computer hebt om het script uit te voeren](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Als de beveiligde virtuele Linux-machine gebruikmaakt van LVM-en/of RAID-matrices, volgt u de stappen die in dit [artikel](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)worden beschreven.

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>Kan de bestanden niet kopiëren van gekoppelde volumes

De kopie kan mislukken met een fout 0x80070780: het bestand kan niet worden geopend door het systeem. Controleer in dit geval of schijf ontdubbeling is ingeschakeld op de bron server. Zorg ervoor dat voor de herstel server ook ontdubbeling is ingeschakeld op de stations. U kunt de functie ontdubbeling ongedaan laten, zodat u de stations op de Restore-server niet ontdubbelt.
