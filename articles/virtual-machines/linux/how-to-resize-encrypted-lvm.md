---
title: Het formaat van schijven die zijn versleuteld met behulp van Azure Disk Encryption
description: Dit artikel bevat instructies voor het wijzigen van de grootte van met ADE versleutelde schijven met behulp van beheer van logische volumes.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 92e3be9111abe5712549ba85ef4550a9ac1473f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596804"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Het formaat van apparaten voor beheer van logische volumes aanpassen die gebruikmaken van Azure Disk Encryption

In dit artikel leert u hoe u de grootte van gegevens schijven die gebruikmaken van Azure Disk Encryption kunt wijzigen. Als u de schijven wilt verg Roten of verkleinen, gebruikt u LVM (Logical Volume Management) in Linux. De stappen zijn van toepassing op meerdere scenario's.

U kunt dit proces voor het wijzigen van de grootte gebruiken in de volgende omgevingen:

- Linux-distributies:
    - Red Hat Enterprise Linux (RHEL) 7 of hoger
    - Ubuntu 16 of hoger
    - SUSE 12 of hoger
- Azure Disk Encryption versies: 
    - Extensie voor eenmalige overdracht
    - Dubbele Pass-extensie

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u het volgende hebt:

- Een bestaande LVM-configuratie. Zie [LVM configureren op een virtuele Linux-machine](/previous-versions/azure/virtual-machines/linux/configure-lvm)voor meer informatie.

- Schijven die al zijn versleuteld door Azure Disk Encryption. Zie [Configure LVM and RAID on encrypted devices](how-to-configure-lvm-raid-on-crypt.md)(Engelstalig) voor meer informatie.

- Ervaring met Linux en LVM.

- Ervaring met het gebruik van */dev/disk/SCSI1/* -paden voor gegevens schijven in Azure. Zie [problemen met de naam van Linux VM-apparaten oplossen](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)voor meer informatie. 

## <a name="scenarios"></a>Scenario's

De procedures in dit artikel zijn van toepassing op de volgende scenario's:

- Traditionele LVM-en LVM-on-cryptografie configuraties
- Traditionele LVM-versleuteling 
- LVM-on-cryptografie 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Traditionele LVM-en LVM-on-cryptografie configuraties

Traditionele LVM-en LVM-on-cryptografie configuraties uitbreiden een logisch volume (LV) wanneer de volume groep (VG) beschik bare ruimte heeft.

### <a name="traditional-lvm-encryption"></a>Traditionele LVM-versleuteling 

In traditionele LVM versleuteling zijn LVs versleuteld. De hele schijf is niet versleuteld.

Door traditionele LVM-versleuteling te gebruiken, kunt u het volgende doen:

- Breid de LV uit wanneer u een nieuw fysiek volume (HW) toevoegt.
- De LV uitbreiden wanneer u de grootte van een bestaande HW wijzigt.

### <a name="lvm-on-crypt"></a>LVM-on-cryptografie 

De aanbevolen methode voor het versleutelen van schijven is LVM. Met deze methode wordt de hele schijf, niet alleen de LV, versleuteld.

Door LVM te gebruiken, kunt u het volgende doen: 

- Breid de LV uit wanneer u een nieuwe HW toevoegt.
- De LV uitbreiden wanneer u de grootte van een bestaande HW wijzigt.

> [!NOTE]
> Het is niet raadzaam traditionele LVM-versleuteling en LVM-op-cryptografie te mengen op dezelfde VM.

In de volgende secties vindt u voor beelden van het gebruik van LVM en LVM-on-crypt. In de voor beelden worden prebestaande waarden gebruikt voor schijven, PVs, VGs, LVs, bestands systemen, UUID (Universally Unique Identifiers) en koppel punten. Vervang deze waarden door uw eigen waarden zodat deze overeenkomen met uw omgeving.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>Een LV uitbreiden wanneer de VG beschik bare ruimte heeft

De traditionele manier om de grootte van LVs te wijzigen, is om een LV uit te breiden wanneer er ruimte beschikbaar is op de VG. U kunt deze methode gebruiken voor niet-versleutelde schijven, traditionele, met LVM versleutelde volumes en LVM-op-cryptografie configuraties.

1. Controleer de huidige grootte van het bestands systeem dat u wilt verg Roten:

    ``` bash
    df -h /mountpoint
    ```

    ![Scherm afbeelding met code waarmee de grootte van het bestands systeem wordt gecontroleerd. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Controleer of de VG voldoende ruimte heeft om de LV te verg Roten:

    ``` bash
    vgs
    ```

    ![Scherm opname van de code die controleert op ruimte op de VG. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    U kunt ook het `vgdisplay` volgende gebruiken:

    ``` bash
    vgdisplay vgname
    ```

    ![Scherm opname van de V G-weergave code die op ruimte op de VG controleert. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Geef aan voor welke LV het formaat moet worden gewijzigd:

    ``` bash
    lsblk
    ```

    ![Scherm afbeelding met het resultaat van de opdracht l s b l k. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Voor LVM-op-cryptografie is het verschil dat deze uitvoer laat zien dat de versleutelde laag zich op schijf niveau bevindt.

    ![Scherm afbeelding met het resultaat van de opdracht l s b l k. De uitvoer is gemarkeerd. De versleutelde laag wordt weer gegeven.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Controleer de LV-grootte:

    ``` bash
    lvdisplay lvname
    ```

    ![Scherm afbeelding met de code die de grootte van het logische volume controleert. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Verg root de LV-grootte door `-r` het bestands systeem online te verg Roten of verkleinen:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Scherm afbeelding met de code die de grootte van het logische volume verg root. De opdracht en de resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Controleer de nieuwe grootten voor de LV en het bestands systeem:

    ``` bash
    df -h /mountpoint
    ```

    ![Scherm afbeelding met de code die de grootte van de LV en het bestands systeem verifieert. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    De grootte-uitvoer geeft aan dat de grootte van de LV en het bestands systeem is gewijzigd.

U kunt de LV-gegevens opnieuw controleren om de wijzigingen op het niveau van de LV te bevestigen:

``` bash
lvdisplay lvname
```

![Scherm afbeelding met de code die de nieuwe grootten bevestigt. De grootten zijn gemarkeerd.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Een traditioneel LVM-volume uitbreiden door een nieuwe HW toe te voegen

Als u een nieuwe schijf wilt toevoegen om de grootte van de VG te verg Roten, breidt u het traditionele LVM-volume uit door een nieuwe HW toe te voegen.

1. Controleer de huidige grootte van het bestands systeem dat u wilt verg Roten:

    ``` bash
    df -h /mountpoint
    ```

    ![Scherm opname van de code waarmee de huidige grootte van een bestands systeem wordt gecontroleerd. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Controleer de huidige HW-configuratie:

    ``` bash
    pvs
    ```

    ![Scherm opname van de code waarmee de huidige HW-configuratie wordt gecontroleerd. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Controleer de huidige VG-gegevens:

    ``` bash
    vgs
    ```

    ![Scherm opname van de code die de huidige volume groeps informatie controleert. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Controleer de huidige lijst met schijven. Identificeer gegevens schijven door de apparaten in */dev/disk/Azure/SCSI1/* te controleren.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Scherm afbeelding van de code waarmee de huidige lijst met schijven wordt gecontroleerd. De opdracht en de resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Controleer de uitvoer van `lsblk` : 

    ``` bash
    lsbk
    ```

    ![Scherm opname van de code waarmee de uitvoer van l s b l k wordt gecontroleerd. De opdracht en de resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Koppel de nieuwe schijf aan de virtuele machine door de instructies in [een gegevens schijf koppelen aan een virtuele Linux-machine te](attach-disk-portal.md)volgen.

7. Controleer de lijst met schijven en Let op de nieuwe schijf.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Scherm afbeelding van de code waarmee de lijst met schijven wordt gecontroleerd. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![Scherm opname van de code waarmee de lijst met schijven wordt gecontroleerd met behulp van l s b l k. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Maak een nieuwe HW boven op de nieuwe gegevens schijf:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Scherm afbeelding met de code die een nieuwe HW maakt. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Deze methode gebruikt de hele schijf als een HW zonder partitie. U kunt ook gebruiken `fdisk` om een partitie te maken en vervolgens die partitie voor te gebruiken `pvcreate` .

9. Controleer of de PV is toegevoegd aan de lijst met hw:

    ``` bash
    pvs
    ```

    ![Scherm opname van de code die de lijst met fysieke volumes weergeeft. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Breid de VG uit door de nieuwe HW toe te voegen:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Scherm afbeelding met de code die de volume groep uitbreidt. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Controleer de nieuwe VG-grootte:

    ``` bash
    vgs
    ```

    ![Scherm afbeelding met de code die de grootte van de volume groep controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Gebruiken `lsblk` voor het identificeren van de LV waarvan de grootte moet worden aangepast:

    ``` bash
    lsblk
    ```

    ![Scherm opname van de code die het lokale volume aangeeft waarvan u het formaat wilt wijzigen. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. De LV-grootte uitbreiden door gebruik te maken van `-r` om het bestands systeem online te verg Roten:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Scherm afbeelding met code die de grootte van het bestands systeem online verg root. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Controleer de nieuwe grootten van de LV en het bestands systeem:

    ``` bash
    df -h /mountpoint
    ```

    ![Scherm afbeelding met de code die de grootte van het lokale volume en het bestands systeem controleert. De opdracht en het resultaat zijn gemarkeerd.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Als Azure Data Encryption wordt gebruikt voor traditionele LVM-configuraties, wordt de versleutelde laag op LV-niveau gemaakt, niet op schijf niveau.
    >
    >Op dit moment wordt de versleutelde laag uitgebreid naar de nieuwe schijf. De werkelijke gegevens schijf heeft geen versleutelings instellingen op platform niveau, waardoor de versleutelings status niet wordt bijgewerkt.
    >
    >Dit zijn een aantal redenen waarom LVM-on-cryptografie de aanbevolen aanpak is. 

15. Controleer de versleutelings informatie in de portal:

    ![Scherm afbeelding met de versleutelings informatie in de portal. De naam van de schijf en de versleuteling zijn gemarkeerd.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Als u de versleutelings instellingen op de schijf wilt bijwerken, voegt u een nieuwe LV toe en schakelt u de extensie op de VM in.
    
16. Voeg een nieuwe LV toe, maak een bestands systeem aan en voeg deze toe aan `/etc/fstab` .

17. Stel de versleutelings extensie opnieuw in. Hiermee stemt u de versleutelings instellingen op de nieuwe gegevens schijf op platform niveau af. Hier volgt een CLI-voor beeld:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Controleer de versleutelings informatie in de portal:

    ![Scherm afbeelding met de versleutelings informatie in de portal. De naam van de schijf en de versleutelings gegevens zijn gemarkeerd.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

Nadat de versleutelings instellingen zijn bijgewerkt, kunt u de nieuwe LV verwijderen. Verwijder ook de vermelding uit de `/etc/fstab` en `/etc/crypttab` die u hebt gemaakt.

![Scherm afbeelding met de code waarmee het nieuwe logische volume wordt verwijderd. Op het tabblad voor het verwijderen van F S en het tabblad crypt worden de items gemarkeerd.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

Voer de volgende stappen uit om het opruimen te volt ooien:

1. De LV ontkoppelen:

    ``` bash
    umount /mountpoint
    ```

1. De versleutelde laag van het volume sluiten:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. De LV verwijderen:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Een traditioneel LVM-volume uitbreiden door de grootte van een bestaande HW te wijzigen

In sommige gevallen moet u mogelijk de grootte van een bestaande schijf wijzigen. U doet dit als volgt:

1. Identificeer uw versleutelde schijven:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Scherm afbeelding van de code waarmee versleutelde schijven worden geïdentificeerd. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Scherm opname met alternatieve code voor het identificeren van versleutelde schijven. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Controleer de PV-gegevens:

    ``` bash
    pvs
    ```

    ![Scherm afbeelding met de code die informatie over het fysieke volume controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    De resultaten in de afbeelding geven aan dat alle ruimte op alle PVs momenteel wordt gebruikt.

3. Controleer de VG-gegevens:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Scherm opname van de code waarmee informatie over de volume groep wordt gecontroleerd. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Controleer de grootte van de schijf. U kunt `fdisk` of gebruiken `lsblk` om de grootte van het station weer te geven.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Scherm afbeelding van de code waarmee de schijf grootte wordt gecontroleerd. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Hier hebben we geïdentificeerd welke PVs zijn gekoppeld aan welke LVs met behulp van `lsblk -fs` . U kunt de koppelingen identificeren door uit te voeren `lvdisplay` .

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Scherm afbeelding met een alternatieve manier om fysieke volume koppelingen met lokale volumes te identificeren. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    In dit geval zijn alle vier de gegevens stations onderdeel van hetzelfde VG en één LV. Uw configuratie kan verschillen.

5. Controleer het huidige bestandssysteem gebruik:

    ``` bash
    df -h /datalvm*
    ```

    ![Scherm afbeelding van de code waarmee het bestands systeem wordt gecontroleerd. De opdracht en de resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Wijzig de grootte van de gegevens schijven door de instructies in [een Azure Managed disk uit te breiden](expand-disks.md#expand-an-azure-managed-disk). U kunt de portal, de CLI of Power shell gebruiken.

    >[!IMPORTANT]
    >U kunt de grootte van virtuele schijven niet wijzigen terwijl de virtuele machine wordt uitgevoerd. De toewijzing van de virtuele machine voor deze stap ongedaan maken.

7. Start de virtuele machine en controleer de nieuwe grootten met behulp van `fdisk` .

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Scherm afbeelding met de code die de schijf grootte controleert. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    In dit geval `/dev/sdd` is het formaat gewijzigd van 5 g tot 20 g.

8. De huidige PV-grootte controleren:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Scherm afbeelding met de code die de grootte van de P V controleert. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Hoewel het formaat van de schijf is gewijzigd, heeft de PV nog steeds de vorige grootte.

9. Het formaat van de PV aanpassen:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Scherm afbeelding van de code waarmee het fysieke volume wordt aangepast. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Controleer de PV-grootte:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Scherm afbeelding met de code die de grootte van het fysieke volume controleert. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Pas dezelfde procedure toe voor alle schijven waarvan u de grootte wilt wijzigen.

11. Controleer de VG-gegevens.

    ``` bash
    vgdisplay vgname
    ```

    ![Scherm afbeelding met de code die de informatie voor de volume groep controleert. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    De VG heeft nu voldoende ruimte om te worden toegewezen aan de LVs.

12. Grootte van LV wijzigen:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![Scherm afbeelding met de code waarmee de grootte van de L V wordt gewijzigd. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Controleer de grootte van het bestands systeem:

    ``` bash
    df -h /datalvm2
    ```

    ![Scherm afbeelding met de code die de grootte van het bestands systeem controleert. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Een LVM-op-cryptografie volume uitbreiden door een nieuwe HW toe te voegen

U kunt ook een LVM-op-cryptografie volume uitbreiden door een nieuwe HW toe te voegen. Deze methode volgt de stappen in [LVM en RAID configureren op versleutelde apparaten](how-to-configure-lvm-raid-on-crypt.md#general-steps). Zie de secties waarin wordt uitgelegd hoe u een nieuwe schijf kunt toevoegen en instellen in een LVM-op-cryptografie configuratie.

U kunt deze methode gebruiken om ruimte toe te voegen aan een bestaande LV. U kunt ook nieuwe VGs of LVs maken.

1. Controleer de huidige grootte van uw VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Scherm afbeelding met de code die de grootte van de volume groep controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Controleer de grootte van het bestands systeem en de LV die u wilt uitbreiden:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Scherm afbeelding met de code die de grootte van het lokale volume controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Scherm afbeelding van de code waarmee de grootte van het bestands systeem wordt gecontroleerd. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Voeg een nieuwe gegevens schijf toe aan de VM en Identificeer deze.

    Controleer de schijven voordat u de nieuwe schijf toevoegt:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Scherm afbeelding met de code die de grootte van de schijven controleert. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Hier volgt een andere manier om de schijven te controleren voordat u de nieuwe schijf toevoegt:

    ``` bash
    lsblk
    ```

    ![Scherm opname met een alternatieve code waarmee de grootte van de schijven wordt gecontroleerd. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Als u de nieuwe schijf wilt toevoegen, kunt u Power shell, de Azure CLI of de Azure Portal gebruiken. Zie [een gegevens schijf koppelen aan een virtuele Linux-machine](attach-disk-portal.md)voor meer informatie.

    Het kernel name-schema is van toepassing op het toegevoegde apparaat. Aan een nieuw station wordt normaal gesp roken de volgende beschik bare letter toegewezen. In dit geval is de toegevoegde schijf `sdd` .

4. Controleer de schijven om er zeker van te zijn dat de nieuwe schijf is toegevoegd:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Scherm afbeelding van de code waarin de schijven worden weer gegeven. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Scherm opname van de zojuist toegevoegde schijf in de uitvoer.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Maak een bestands systeem boven op de recent toegevoegde schijf. Vergelijk de schijf met de gekoppelde apparaten op `/dev/disk/azure/scsi1/` .

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Scherm afbeelding met de code die een bestands systeem maakt. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Scherm afbeelding met aanvullende code die een bestands systeem maakt en overeenkomt met de schijf aan de gekoppelde apparaten. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Maak een tijdelijk koppel punt voor de nieuwe toegevoegde schijf:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Voeg het onlangs gemaakte bestands systeem toe aan `/etc/fstab` .

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Het nieuw gemaakte bestands systeem koppelen:

    ``` bash
    mount -a
    ```

9. Controleer of het nieuwe bestands systeem is gekoppeld:

    ``` bash
    df -h
    ```

    ![Scherm afbeelding met de code die controleert of het bestands systeem is gekoppeld. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Scherm opname met aanvullende code die controleert of het bestands systeem is gekoppeld. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Start de versleuteling die u eerder hebt gestart voor gegevens stations opnieuw.

    >[!TIP]
    >Voor LVM-op-cryptografie raden we u aan om te gebruiken `EncryptFormatAll` . Anders ziet u mogelijk een dubbele versleuteling terwijl u extra schijven instelt.
    >
    >Zie [Configure LVM and RAID on encrypted devices](how-to-configure-lvm-raid-on-crypt.md)(Engelstalig) voor meer informatie.

    Hier volgt een voorbeeld:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Wanneer de versleuteling is voltooid, ziet u een crypt-laag op de zojuist toegevoegde schijf:

    ``` bash
    lsblk
    ```

    ![Scherm opname van de code waarmee de Crypt-laag wordt gecontroleerd. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Ontkoppelen van de versleutelde laag van de nieuwe schijf:

    ``` bash
    umount ${newmount}
    ```

12. De huidige HW-informatie controleren:

    ``` bash
    pvs
    ```

    ![Scherm afbeelding met de code die informatie over het fysieke volume controleert. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Maak een hw aan de bovenkant van de versleutelde laag van de schijf. Haal de apparaatnaam op uit de vorige `lsblk` opdracht. Voeg een `/dev/` Mapper toe voor de naam van het apparaat om de PV te maken:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Scherm afbeelding met de code die een fysiek volume op de versleutelde laag maakt. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Er wordt een waarschuwing weer gegeven over het wissen van de huidige `ext4 fs` hand tekening. Deze waarschuwing wordt verwacht. Beantwoord deze vraag met `y` .

14. Controleer of de nieuwe hw is toegevoegd aan de LVM-configuratie:

    ``` bash
    pvs
    ```

    ![Scherm afbeelding met de code die controleert of het fysieke volume is toegevoegd aan de LVM-configuratie. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Voeg de nieuwe HW toe aan de VG die u wilt verhogen.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Scherm opname van de code waarmee een fysiek volume wordt toegevoegd aan een volume groep. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Controleer de nieuwe grootte en beschik bare ruimte van de VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Scherm opname van de code die de grootte en beschik bare ruimte van de volume groep verifieert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Let op de toename van het `Total PE` aantal en de `Free PE / Size` .

17. Verg root de grootte van de LV en het bestands systeem. Gebruik de `-r` optie op `lvextend` . In dit voor beeld voegen we de totale beschik bare ruimte in de VG toe aan de opgegeven LV.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Scherm afbeelding met de code die de grootte van het lokale volume en het bestands systeem verg root. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

Volg de volgende stappen om uw wijzigingen te verifiëren.

1. Controleer de grootte van de LV:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Scherm afbeelding met de code die de nieuwe grootte van het lokale volume verifieert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. De nieuwe grootte van het bestands systeem controleren:

    ``` bash
    df -h mountpoint
    ```

    ![Scherm opname van de code die de nieuwe grootte van het bestands systeem verifieert. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. Controleer of de laag LVM zich boven op de versleutelde laag bevindt:

    ``` bash
    lsblk
    ```

    ![Scherm opname van de code die controleert of de laag LVM zich boven op de versleutelde laag bevindt. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Als u `lsblk` zonder opties gebruikt, ziet u de koppel punten meerdere keren. De opdracht wordt gesorteerd op apparaat-en LVs. 

    U kunt het beste gebruiken `lsblk -fs` . Met deze opdracht wordt `-fs` de sorteer volgorde omgekeerd zodat de koppel punten eenmaal worden weer gegeven. De schijven worden meerdere keren weer gegeven.

    ``` bash
    lsblk -fs
    ```

    ![Scherm opname met alternatieve code die controleert of de laag LVM zich boven op de versleutelde laag bevindt. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Een LVM op een crypt-volume uitbreiden door een bestaande PV te verg Roten of verkleinen

1. Identificeer uw versleutelde schijven:

    ``` bash
    lsblk
    ```

    ![Scherm afbeelding van de code waarmee de versleutelde schijven worden geïdentificeerd. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![Scherm opname met alternatieve code die de versleutelde schijven identificeert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Controleer uw HW-gegevens:

    ``` bash
    pvs
    ```

    ![Scherm afbeelding met de code die de informatie voor fysieke volumes controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Controleer uw VG-gegevens:

    ``` bash
    vgs
    ```

    ![Scherm opname van de code waarmee informatie voor volume groepen wordt gecontroleerd. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Controleer uw LV-gegevens:

    ``` bash
    lvs
    ```

    ![Scherm afbeelding met de code die de informatie voor het lokale volume controleert. Het resultaat wordt gemarkeerd.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Controleer het gebruik van het bestands systeem:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Scherm opname van de code die controleert hoeveel van het bestands systeem wordt gebruikt. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Controleer de grootte van uw schijven:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Scherm afbeelding met de code die de grootte van de schijven controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Wijzig de grootte van de gegevens schijf. U kunt de portal, CLI of Power shell gebruiken. Zie voor meer informatie de sectie schijf grootte in [uitvouwen virtuele harde schijven op een Linux-VM](expand-disks.md#expand-an-azure-managed-disk). 

    >[!IMPORTANT]
    >U kunt de grootte van virtuele schijven niet wijzigen terwijl de virtuele machine wordt uitgevoerd. De toewijzing van de virtuele machine voor deze stap ongedaan maken.

8. Controleer de grootte van uw schijven:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Scherm afbeelding met code waarmee de schijf grootte wordt gecontroleerd. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    In dit geval is het formaat van beide schijven gewijzigd van 2 GB in 4 GB. Maar de grootte van het bestands systeem, LV en HW blijft hetzelfde.

9. Controleer de huidige PV-grootte. Houd er rekening mee dat op LVM-op-cryptografie de HW het `/dev/mapper/` apparaat is, niet het `/dev/sd*` apparaat.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Scherm afbeelding met de code die de grootte van het huidige fysieke volume controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Het formaat van de PV aanpassen:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Scherm afbeelding van de code waarmee het fysieke volume wordt aangepast. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Controleer de nieuwe HW-grootte:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Scherm afbeelding met de code die de grootte van het fysieke volume controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Wijzig de grootte van de versleutelde laag op de PV:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Pas dezelfde procedure toe voor alle schijven waarvan u de grootte wilt wijzigen.

13. Controleer uw VG-gegevens:

    ``` bash
    vgdisplay vgname
    ```

    ![Scherm afbeelding met de code die de informatie voor de volume groep controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    De VG heeft nu voldoende ruimte om te worden toegewezen aan de LVs.

14. Controleer de LV-gegevens:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Scherm afbeelding met de code die de informatie voor het lokale volume controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Controleer het gebruik van het bestands systeem:

    ``` bash
    df -h /mountpoint
    ```

    ![Scherm afbeelding met de code die het gebruik van het bestands systeem controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Grootte van LV wijzigen:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Scherm afbeelding van de code waarmee de grootte van het lokale volume wordt gewijzigd. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Hier gebruiken we de `-r` optie om ook het formaat van het bestands systeem te wijzigen.

17. Controleer de LV-gegevens:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Scherm afbeelding met de code die informatie over het lokale volume krijgt. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Controleer het gebruik van het bestands systeem:

    ``` bash
    df -h /mountpoint
    ```

    ![Scherm opname van de code die het bestands systeem gebruik controleert. De resultaten worden gemarkeerd.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Pas dezelfde procedure voor het wijzigen van de grootte toe op andere LV waarvoor deze is vereist.

## <a name="next-steps"></a>Volgende stappen

[Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)