---
title: Problemen met een defecte virtuele machine in azure oplossen met behulp van geneste virtualisatie in azure | Microsoft Docs
description: Problemen met een Azure VM oplossen met geneste virtualisatie in azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: f950cb63b5083a85ab5420434abdd9a720115b1a
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734545"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Problemen met een defecte virtuele machine in azure oplossen met geneste virtualisatie in azure

In dit artikel wordt beschreven hoe u een geneste Virtualization Environment maakt in Microsoft Azure, zodat u de schijf van de beschadigde virtuele machine op de Hyper-V-host (herstel-VM) kunt koppelen voor het oplossen van problemen.

## <a name="prerequisites"></a>Vereisten

Als u de beschadigde VM wilt koppelen, moet de virtuele machine van de werk ruimte gebruikmaken van hetzelfde type opslag account (Standard of Premium) als de beschadigde VM.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Stap 1: een herstel-VM maken en de Hyper-V-functie installeren

1.  Een nieuwe herstel-VM maken:

    -  Besturings systeem: Windows Server 2016 Data Center

    -  Grootte: elke V3-serie met ten minste twee kernen die ondersteuning bieden voor geneste virtualisatie. Zie [Inleiding tot de nieuwe Dv3-en EV3 VM-grootten](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)voor meer informatie.

    -  Dezelfde locatie, hetzelfde opslag account en dezelfde resource groep als de beschadigde VM.

    -  Selecteer hetzelfde opslag type als de beschadigde VM (Standard of Premium).

2.  Nadat de herstel-VM is gemaakt, wordt extern bureau blad naar de virtuele machine voor herstel.

3.  In Serverbeheer klikt u op **beheer**  >  **toevoegen rollen en onderdelen**.

4.  Selecteer in de sectie **type installatie** de optie installatie die op de **functie of het onderdeel is gebaseerd**.

5.  Controleer in de sectie de **doel server selecteren** of de virtuele machine voor herstel is geselecteerd.

6.  Selecteer de **Hyper-V-functie**  >  **onderdelen toevoegen**.

7.  Selecteer **volgende** in het gedeelte **functies** .

8.  Als er een virtuele switch beschikbaar is, selecteert u deze. Anders selecteert u **volgende**.

9.  Selecteer in de sectie **migratie** de optie **volgende**

10. Selecteer **volgende** in het gedeelte **standaard archieven** .

11. Schakel het selectie vakje in als u de server automatisch opnieuw wilt opstarten als dat nodig is.

12. Selecteer **Installeren**.

13. Hiermee staat u toe dat de server de Hyper-V-functie installeert. Dit duurt enkele minuten en de server wordt automatisch opnieuw opgestart.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Stap 2: de beschadigde VM maken op de Hyper-V-Server van de virtuele machine voor herstel

1.  [Maak een momentopname schijf](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) voor de besturingssysteem schijf van de virtuele machine met een probleem en voeg de momentopname schijf vervolgens toe aan de RECUSE-VM.

2.  Extern bureau blad naar de virtuele machine voor herstel.

3.  Open schijf beheer (diskmgmt. msc). Zorg ervoor dat de schijf van de defecte VM is ingesteld op **offline**.

4.  Open Hyper-V-beheer: in **Serverbeheer** selecteert u de **hyper-v-rol**. Klik met de rechter muisknop op de server en selecteer **Hyper-V-beheer**.

5.  Klik in Hyper-V-beheer met de rechter muisknop op de virtuele machine voor herstel en selecteer vervolgens **nieuwe** VM  >  **Virtual Machine**  >  **volgende**.

6.  Typ een naam voor de virtuele machine en selecteer vervolgens **volgende**.

7.  Selecteer **generatie 1**.

8.  Stel het opstart geheugen in op 1024 MB of meer.

9. Als dit van toepassing is, selecteert u de Hyper-V-netwerk switch die is gemaakt. Ga anders naar de volgende pagina.

10. Selecteer **later een virtuele harde schijf koppelen**.

    ![de afbeelding over de optie later een virtuele harde schijf koppelen](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Selecteer **volt ooien** wanneer de virtuele machine wordt gemaakt.

12. Klik met de rechter muisknop op de virtuele machine die u hebt gemaakt en selecteer **instellingen**.

13. Selecteer **IDE-controller 0**, selecteer **harde schijf** en klik vervolgens op **toevoegen**.

    ![de afbeelding over het toevoegen van nieuwe harde schijf](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. Selecteer bij **fysieke harde schijf** de schijf van de defecte virtuele machine die u hebt gekoppeld aan de Azure VM. Als er geen schijven worden weer gegeven, controleert u of de schijf is ingesteld op offline met behulp van schijf beheer.

    ![de afbeelding over het koppelen van de schijf](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Selecteer **Apply** en vervolgens **OK**.

16. Dubbel klik op de virtuele machine en start deze.

17. Nu kunt u op de virtuele machine werken als de on-premises VM. U kunt de stappen voor probleem oplossing volgen die u nodig hebt.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Stap 3: de besturingssysteem schijf vervangen die wordt gebruikt door de beschadigde VM

1.  Nadat u de VM weer online hebt gezet, sluit u de virtuele machine in Hyper-V-beheer af.

2.  [Ontkoppel de gerepareerde besturingssysteem schijf en ontkoppel deze](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-the-original-virtual-hard-disk
).
3.  [Vervang de besturingssysteem schijf die wordt gebruikt door de virtuele machine door de gerepareerde besturingssysteem schijf](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met RDP-verbindingen met een Azure VM oplossen](troubleshoot-rdp-connection.md)als u problemen ondervindt bij het maken van verbinding met uw virtuele machine. Zie problemen met [toepassings connectiviteit oplossen op een Windows-VM](troubleshoot-app-connection.md)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.
