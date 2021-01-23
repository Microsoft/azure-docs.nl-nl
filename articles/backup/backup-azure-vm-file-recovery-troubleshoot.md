---
title: Problemen met Azure VM File Recovery oplossen
description: Problemen oplossen bij het herstellen van bestanden en mappen vanuit een Azure VM-back-up.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: c4d0d233237cb477d72efea0b91d4e5288e2a302
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735874"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Problemen oplossen in bestands herstel van een Azure VM-back-up

Dit artikel bevat probleemoplossings stappen die u kunnen helpen bij het oplossen van problemen met het herstellen van bestanden en mappen vanaf een back-up van een virtuele Azure-machine (VM).

## <a name="common-error-messages"></a>Veelvoorkomende foutberichten

In deze sectie vindt u de stappen voor het oplossen van fout berichten die u kunt zien.

### <a name="exception-caught-while-connecting-to-target"></a>"Uitzonde ring opgetreden bij het verbinden met het doel"

**Mogelijke oorzaak**: het script kan geen toegang krijgen tot het herstel punt.

**Aanbevolen actie**: Volg de stappen in het script om dit probleem op te lossen, [maar de verbinding is mislukt](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"Het doel is al aangemeld via een iSCSI-sessie"

**Mogelijke oorzaak**: het script wordt al uitgevoerd op dezelfde computer en de stations zijn gekoppeld.

**Aanbevolen actie**: de volumes van het herstel punt zijn al gekoppeld. Ze kunnen niet worden gekoppeld met dezelfde stationsletters van de oorspronkelijke VM. Blader door de beschik bare volumes in bestanden Verkenner.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"Dit script is ongeldig omdat de schijven zijn ontkoppeld via de portal, de limiet van 12 uur is overschreden. Down load een nieuw script uit de portal "

**Mogelijke oorzaak**: de schijven zijn ontkoppeld van de portal of de tijds limiet van 12 uur is overschreden.

**Aanbevolen actie**: 12 uur nadat u het script hebt gedownload, wordt het ongeldig en kan het niet worden uitgevoerd. Ga naar de portal en down load een nieuw script om door te gaan met het herstellen van bestanden.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>iscsi_tcp module kan niet worden geladen (of) iscsi_tcp_module niet gevonden

**Aanbevolen actie**: als u dit probleem wilt oplossen, volgt u de stappen in [het script downloaden geslaagd, maar kunnen ze niet worden uitgevoerd](#the-script-downloads-successfully-but-fails-to-run).

## <a name="common-problems"></a>Algemene problemen

Deze sectie bevat stappen voor het oplossen van veelvoorkomende problemen die zich kunnen voordoen tijdens het downloaden en uitvoeren van het script voor bestands herstel.

### <a name="you-cant-download-the-script"></a>U kunt het script niet downloaden

1. Zorg ervoor dat u over de [vereiste machtigingen beschikt om het script te downloaden](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script).
1. Controleer de verbinding met de doel-Ip's van Azure. Voer een van de volgende opdrachten uit vanaf een opdracht prompt met verhoogde bevoegdheid:

   `nslookup download.microsoft.com`

    of

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>Het script wordt gedownload, maar kan niet worden uitgevoerd

Wanneer u het python-script uitvoert voor herstel op item niveau (ILR) op SUSE Linux Enterprise Server 12 SP4, mislukt de fout ' iscsi_tcp module kan niet worden geladen ' of ' iscsi_tcp_module niet gevonden '.

**Mogelijke oorzaak**: de ILR-module gebruikt **ISCSI_TCP** om een TCP-verbinding met de back-upservice tot stand te brengen. Als onderdeel van de SLES-release van 12 SP4, heeft SUSE **iscsi_tcp** verwijderd uit het open-iscsi-pakket, waardoor de ILR-bewerking mislukt.

**Aanbevolen actie**: de uitvoering van het script voor bestands herstel wordt niet ondersteund op SuSE 12 SP4-vm's. Voer de herstel bewerking uit op een oudere versie van SUSE 12 SP4.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>Het script wordt uitgevoerd, maar de verbinding met het iSCSI-doel is mislukt

U ziet mogelijk een fout bericht ' uitzonde ring opgetreden bij het verbinden met het doel '.

1. Zorg ervoor dat de computer waarop het script wordt uitgevoerd, voldoet aan de [toegangs vereisten](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script).
1. Controleer de verbinding met de doel-Ip's van Azure. Voer een van de volgende opdrachten uit vanaf een opdracht prompt met verhoogde bevoegdheid:

   `nslookup download.microsoft.com`

   of

   `ping download.microsoft.com`
1. Zorg ervoor dat de toegang tot de uitgaande iSCSI-poort 3260.
1. Controleer of een firewall of NSG verkeer naar Azure target Ip's of Recovery service-Url's blokkeert.
1. Zorg ervoor dat de antivirus software de uitvoering van het script niet blokkeert.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>U bent verbonden met het herstel punt, maar de schijven zijn niet gekoppeld

Los dit probleem op door de stappen voor uw besturings systeem te volgen.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>Windows-bestands herstel mislukt op de server met opslag groepen

Wanneer u het script voor de eerste keer uitvoert op Windows Server 2012 R2 en Windows Server 2016 (met opslag groepen), kan de opslag groep worden gekoppeld aan de virtuele machine in alleen-lezen.

>[!Tip]
> Zorg ervoor dat u de [juiste computer hebt om het script uit te voeren](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

U kunt dit probleem oplossen door hand matig lees-/schrijftoegang te verlenen aan de opslag groep en de virtuele schijven te koppelen:

1. Ga naar **Serverbeheer**  >  opslag groepen voor **Bestands-en opslag Services**  >    >  .

   ![Scherm opname van opties voor opslag groepen.](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. Klik in het venster **opslag groep** met de rechter muisknop op de beschik bare opslag groep en selecteer **set Read-Write Access**.

   ![Scherm afbeelding van met de rechter muisknop op Opties voor een opslag wachtrij.](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Nadat de opslag groep is toegewezen lees-/schrijftoegang, klikt u met de rechter muisknop in het gedeelte **virtuele schijven** en selecteert u **virtuele schijf koppelen**.

   ![Scherm opname van met de rechter muisknop op Opties voor een virtuele schijf.](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>Linux-bestands herstel kan niet automatisch worden gekoppeld omdat de schijf geen volumes bevat

Tijdens het herstel van bestanden detecteert de back-upservice volumes en automatische koppeling. Als de schijven met een back-up echter onbewerkte partities hebben, worden deze schijven niet automatisch gekoppeld en kunt u de gegevens schijf niet voor herstel zien.

Als u dit probleem wilt oplossen, gaat u naar [bestanden herstellen vanuit back-up van virtuele Azure-machines](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>Linux-bestands herstel mislukt omdat het bestands systeem niet kan worden geïdentificeerd door het besturings systeem

Wanneer u het script voor bestands herstel uitvoert, kan de gegevens schijf niet worden gekoppeld. U ziet een ' de volgende partities zijn niet gekoppeld omdat het besturings systeem de fout niet kan identificeren van het bestands systeem '.

U kunt dit probleem oplossen door te controleren of het volume is versleuteld met een toepassing van derden. Als het is versleuteld, wordt de schijf of virtuele machine niet weer gegeven als versleuteld op de portal.

1. Meld u aan bij de back-up van de virtuele machine en voer deze opdracht uit:

   `lsblk -f`

   ![Scherm opname van de resultaten van de opdracht voor het weer geven van blok apparaten.](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. Controleer het bestands systeem en de versleuteling. Als het volume is versleuteld, wordt bestands herstel niet ondersteund. Meer informatie vindt u in de [ondersteunings matrix voor Azure VM-back-up](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>Schijven zijn gekoppeld, maar de volumes zijn niet gekoppeld

Los dit probleem op door de stappen voor uw besturings systeem te volgen.

#### <a name="windows"></a>Windows

Wanneer u het script voor bestands herstel voor Windows uitvoert, wordt het bericht ' 0 herstel volumes toegevoegd ' weer gegeven. De schijven worden echter wel gedetecteerd in de schijf beheer-console.

**Mogelijke oorzaak**: wanneer u volumes hebt gekoppeld via iSCSI, zijn sommige gedetecteerde volumes offline gezet. Wanneer het iSCSI-kanaal tussen de virtuele machine en de service communiceert, detecteert deze volumes en brengt deze online, maar ze worden niet gekoppeld.

   ![Scherm opname met de 0 herstel volumes die zijn gekoppeld.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Voer de volgende stappen uit om dit probleem op te sporen en op te lossen:

>[!Tip]
>Zorg ervoor dat u de [juiste computer hebt om het script uit te voeren](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

1. Voer in het **cmd** -venster **diskmgmt** uit om **schijf beheer** te openen.
1. Zoek naar extra schijven. In het volgende voor beeld is **schijf 2** een extra schijf.

   ![Scherm opname van het venster schijf beheer met extra schijf.](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Klik met de rechter muisknop op **Nieuw volume** en selecteer **stationsletter en paden wijzigen**.

   ![Scherm afbeelding met de opties voor klikken met de rechter muisknop op de extra schijf.](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. Selecteer in het venster stationsletter **of pad wijzigen** **de volgende stationsletter toewijzen**, wijs een beschik bare schijf toe en selecteer vervolgens **OK**.

   ![Scherm afbeelding van het venster stationsletter of pad wijzigen.](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Open bestanden Verkenner om het gekozen station weer te geven en de bestanden te verkennen.

#### <a name="linux"></a>Linux

>[!Tip]
>Zorg ervoor dat u de [juiste computer hebt om het script uit te voeren](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Als de beveiligde virtuele Linux-machine gebruikmaakt van LVM-of RAID-matrices, volgt u de stappen in [bestanden herstellen van back-up van virtuele Azure-machines](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>U kunt de bestanden niet van gekoppelde volumes kopiëren

De kopie kan mislukken met de fout ' 0x80070780: het bestand kan niet worden geopend door het systeem. ' 

Controleer of schijf ontdubbeling is ingeschakeld op de bron server. Als dit het geval is, moet u ervoor zorgen dat de herstel server ook ontdubbeling op de stations heeft ingeschakeld. U kunt ontdubbeling ongedaan laten, zodat u de stations op de Restore-server niet ontdubbelt.

## <a name="next-steps"></a>Volgende stappen

- [Bestanden en mappen herstellen vanuit back-up van virtuele Azure-machine](backup-azure-restore-files-from-vm.md)