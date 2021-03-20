---
title: Een back-up van een Azure VM maken op basis van de VM-instellingen
description: In dit artikel vindt u informatie over het maken van een back-up van een enkelvoudige Azure-VM of meerdere virtuele Azure-machines met de Azure Backup-service.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 55b71d2a2901cdde984df3ebfd68a2a643b78b74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89667511"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Een back-up van een Azure VM maken op basis van de VM-instellingen

In dit artikel wordt uitgelegd hoe u een back-up van virtuele Azure-machines maakt met de [Azure backup](backup-overview.md) -service. U kunt een back-up van virtuele Azure-machines maken met behulp van een aantal methoden:

- Eén Azure VM: in dit artikel wordt beschreven hoe u rechtstreeks vanuit de VM-instellingen een back-up maakt van een Azure-VM.
- Meerdere virtuele machines van Azure: u kunt een Recovery Services kluis instellen en een back-up configureren voor meerdere virtuele Azure-machines. Volg de instructies in [dit artikel](backup-azure-arm-vms-prepare.md) voor dit scenario.

## <a name="before-you-start"></a>Voordat u begint

1. [Meer informatie](backup-architecture.md#how-does-azure-backup-work) over de werking van back-ups en het [controleren](backup-support-matrix.md#azure-vm-backup-support) van de ondersteunings vereisten.
2. [Bekijk een overzicht](backup-azure-vms-introduction.md) van back-ups van Azure-vm's.

### <a name="azure-vm-agent-installation"></a>Installatie van de Azure VM-agent

Azure Backup installeert een uitbrei ding op de VM-agent die op de computer wordt uitgevoerd om back-ups te maken van virtuele Azure-machines. Als uw virtuele machine is gemaakt op basis van een installatie kopie van Azure Marketplace, wordt de agent uitgevoerd. In sommige gevallen, bijvoorbeeld als u een aangepaste VM maakt of als u een machine van on-premises migreert, moet u de agent mogelijk hand matig installeren.

- Als u de VM-agent hand matig moet installeren, volgt u de instructies voor [Windows](../virtual-machines/extensions/agent-windows.md) -of [Linux](../virtual-machines/extensions/agent-linux.md) -vm's.
- Wanneer de agent is geïnstalleerd en u back-up inschakelt, wordt de back-upextensie door Azure Backup op de agent geïnstalleerd. Hiermee wordt de extensie zonder tussen komst van de gebruiker bijgewerkt en opgelost.

## <a name="back-up-from-azure-vm-settings"></a>Back-ups maken van Azure VM-instellingen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer **alle services** en typ **virtuele machines** in het filter en selecteer vervolgens **virtuele machines**.
3. Selecteer in de lijst met virtuele machines de virtuele machine waarvan u een back-up wilt maken.
4. Selecteer in het menu VM de optie **back-up**.
5. Ga in **Recovery Services kluis** als volgt te werk:
   - Als u al een kluis hebt, selecteert u **bestaande selecteren** en selecteert u een kluis.
   - Als u geen kluis hebt, selecteert u **nieuwe maken**. Geef een naam op voor de kluis. Deze wordt gemaakt in dezelfde regio en resource groep als de VM. U kunt deze instellingen niet wijzigen wanneer u back-up rechtstreeks vanuit de VM-instellingen inschakelt.

        ![Wizard Back-up inschakelen](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. Voer in **back-upbeleid kiezen** een van de volgende handelingen uit:

   - Het standaard beleid blijven. Hiermee wordt een back-up gemaakt van de VM eenmaal per dag op de opgegeven tijd en worden de back-ups gedurende 30 dagen in de kluis bewaard.
   - Selecteer een bestaand back-upbeleid als u er een hebt.
   - Maak een nieuw beleid en definieer de beleids instellingen.  

       ![Back-upbeleid selecteren](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Selecteer **Back-up inschakelen**. Hiermee wordt het back-upbeleid gekoppeld aan de virtuele machine.

    ![Knop Backup inschakelen](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. U kunt de voortgang van de configuratie in de portal meldingen volgen.
9. Nadat de taak is voltooid, selecteert u in het menu VM de optie **back-up**. De pagina toont de back-upstatus voor de virtuele machine, informatie over herstel punten, actieve taken en waarschuwingen die worden gegeven.

   ![Back-upstatus](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Na het inschakelen van back-up wordt een eerste back-up uitgevoerd. U kunt de eerste back-up onmiddellijk starten of wachten totdat deze begint volgens het back-upschema.
    - Totdat de eerste back-up is voltooid, wordt de **laatste back-upstatus** weer gegeven als **waarschuwing (eerste back-up in behandeling)**.
    - Selecteer de naam van het back-upbeleid om te zien wanneer de volgende geplande back-up wordt uitgevoerd.

## <a name="run-a-backup-immediately"></a>Direct een back-up uitvoeren

1. Als u direct een back-up wilt uitvoeren, selecteert u in het VM-menu nu **back**  >  **-up** maken.

    ![Back-up uitvoeren](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. Gebruik in **back-up nu** het besturings element kalender om te selecteren tot wanneer het herstel punt wordt bewaard > en **OK**.

    ![Dag voor back-up bewaren](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Met portal meldingen kunt u weten dat de back-uptaak is geactiveerd. Selecteer **alle taken weer geven om de** voortgang van de back-up te bewaken.

## <a name="back-up-from-the-recovery-services-vault"></a>Back-ups maken van Recovery Services kluis

Volg de instructies in [dit artikel](backup-azure-arm-vms-prepare.md) om back-ups van virtuele Azure-machines in te scha kelen door een Azure backup Recovery Services kluis in te stellen en back-ups in de kluis in te scha kelen.

## <a name="next-steps"></a>Volgende stappen

- Als u problemen ondervindt met een van de procedures in dit artikel, raadpleegt u de [hand leiding](backup-azure-vms-troubleshoot.md)voor het oplossen van problemen.
- [Meer informatie over](backup-azure-manage-vms.md) het beheren van uw back-ups.
