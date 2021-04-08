---
title: Een hoofddoel server voor virtuele Linux VM-failback installeren met Azure Site Recovery
description: Meer informatie over het instellen van een Linux-hoofddoel server voor failback naar een on-premises site tijdens nood herstel van virtuele VMware-machines naar Azure met Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: mayg
ms.openlocfilehash: 9e1008f7acbfe0685b7a171176c7dc54592d1491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019239"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Een Linux-hoofddoelserver voor failback installeren
Nadat u uw virtuele machines naar Azure hebt gefailovert, kunt u een failback uitvoeren voor de virtuele machines naar de on-premises site. Als u een failback wilt uitvoeren, moet u de virtuele machine opnieuw beveiligen van Azure naar de on-premises site. Voor dit proces hebt u een on-premises Master doel server nodig om het verkeer te ontvangen. 

Als uw beveiligde virtuele machine een virtuele Windows-machine is, hebt u een Windows-hoofd doel nodig. Voor een virtuele Linux-machine hebt u een Linux-hoofd doel nodig. Lees de volgende stappen om te leren hoe u een Linux-hoofd doel maakt en installeert.

> [!IMPORTANT]
> Vanaf de release van de hoofddoel server van 9.10.0, kan de meest recente hoofddoel server alleen worden geïnstalleerd op een Ubuntu 16,04-server. Nieuwe installaties zijn niet toegestaan op CentOS 6,6-servers. U kunt echter door gaan met het bijwerken van uw oude hoofddoel servers met behulp van de 9.10.0-versie.
> Hoofddoel server op LVM wordt niet ondersteund.

## <a name="overview"></a>Overzicht
Dit artikel bevat instructies voor het installeren van een Linux-hoofd doel.

Post opmerkingen of vragen aan het einde van dit artikel of op de [pagina micro soft Q&een vraag voor Azure Recovery Services](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Vereisten

* Als u de host wilt kiezen waarop het hoofd doel moet worden geïmplementeerd, moet u bepalen of de failback naar een bestaande on-premises virtuele machine of naar een nieuwe virtuele machine gaat gaan. 
    * Voor een bestaande virtuele machine moet de host van het hoofd doel toegang hebben tot de gegevens archieven van de virtuele machine.
    * Als de on-premises virtuele machine niet bestaat (in het geval van een alternatieve locatie herstel), wordt de virtuele machine voor failback gemaakt op dezelfde host als het hoofd doel. U kunt een wille keurige ESXi-host kiezen om het hoofd doel te installeren.
* Het hoofd doel moet zich in een netwerk bevindt dat kan communiceren met de proces server en de configuratie server.
* De versie van het hoofd doel moet gelijk zijn aan of lager zijn dan de versies van de proces server en de configuratie server. Als de versie van de configuratie server bijvoorbeeld 9,4 is, kan de versie van het hoofd doel 9,4 of 9,3 zijn, maar niet 9,5.
* Het hoofd doel kan alleen een virtuele VMware-machine zijn en niet een fysieke server.

> [!NOTE]
> Zorg ervoor dat u Storage vMotion niet inschakelt voor beheer onderdelen zoals een hoofd doel. Als het hoofd doel wordt verplaatst nadat de beveiliging is geslaagd, kunnen de schijven van de virtuele machine (Vmdk's) niet worden losgekoppeld. In dit geval mislukt de failback.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Richt lijnen voor het maken van de hoofddoel server aanpassen

Maak het hoofd doel in overeenstemming met de volgende richt lijnen voor het aanpassen:
- **RAM**: 6 GB of meer
- **Grootte van de besturingssysteem schijf**: 100 GB of meer (om besturings systeem te installeren)
- **Aanvullende schijf grootte voor Bewaar station**: 1 TB
- **CPU-kernen**: 4 kernen of meer
- **Kernel**: 4,16. *

## <a name="deploy-the-master-target-server"></a>De hoofddoel server implementeren

### <a name="install-ubuntu-16042-minimal"></a>Ubuntu 16.04.2 Mini maal installeren

Voer de volgende stappen uit om het Ubuntu 16.04.2 64-bits besturings systeem te installeren.

1.   Ga naar de [Download koppeling](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), kies de dichtstbijzijnde mirror en down load een Ubuntu 16.04.2 mini maal 64 bits ISO.
Bewaar een Ubuntu 16.04.2 minimale 64-bits ISO in het DVD-station en start het systeem.

1.  Selecteer **Engels** als voorkeurs taal en selecteer vervolgens **Enter**.
    
    ![Een taal selecteren](./media/vmware-azure-install-linux-master-target/image1.png)
1. Selecteer **Ubuntu-Server installeren** en selecteer vervolgens **Enter**.

    ![Selecteer Ubuntu-Server installeren](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Selecteer **Engels** als voorkeurs taal en selecteer vervolgens **Enter**.

    ![Selecteer Engels als voorkeurs taal](./media/vmware-azure-install-linux-master-target/image3.png)

1. Selecteer de gewenste optie in de lijst **tijd zone** opties en selecteer vervolgens **Enter**.

    ![Selecteer de juiste tijd zone](./media/vmware-azure-install-linux-master-target/image4.png)

1. Selecteer **Nee** (de standaard optie) en selecteer vervolgens **Enter**.

     ![Het toetsen bord configureren](./media/vmware-azure-install-linux-master-target/image5.png)
1. Selecteer **Engels (VS)** als land/regio van oorsprong voor het toetsen bord en selecteer vervolgens **Enter**.

1. Selecteer **Engels (Verenigde Staten)** als toetsenbord indeling en selecteer vervolgens **Enter**.

1. Voer de hostnaam voor uw server in het vak **hostnaam** in en selecteer **door gaan**.

1. Als u een gebruikers account wilt maken, voert u de gebruikers naam in en selecteert u vervolgens **door gaan**.

      ![Een gebruikersaccount maken](./media/vmware-azure-install-linux-master-target/image9.png)

1. Voer het wacht woord in voor het nieuwe gebruikers account en selecteer vervolgens **door gaan**.

1.  Bevestig het wacht woord voor de nieuwe gebruiker en selecteer vervolgens **door gaan**.

    ![De wacht woorden bevestigen](./media/vmware-azure-install-linux-master-target/image11.png)

1.  Selecteer in de volgende selectie voor het versleutelen van uw basis directory **Nee** (de standaard optie) en selecteer vervolgens **Enter**.

1. Als de weer gegeven tijd zone juist is, selecteert u **Ja** (de standaard optie) en selecteert u vervolgens **Enter**. Als u uw tijd zone opnieuw wilt configureren, selecteert u **Nee**.

1. Selecteer in de opties voor de partitie methode **begeleide-volledige schijf gebruiken** en selecteer vervolgens **Enter**.

     ![De optie voor partitionering selecteren](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Selecteer de juiste schijf in de opties **schijf selecteren voor partitie** en selecteer vervolgens **Enter**.

    ![De schijf selecteren](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Selecteer **Ja** om de wijzigingen naar de schijf te schrijven en selecteer vervolgens **Enter**.

    ![De standaard optie selecteren](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Selecteer in de selectie proxy configureren de standaard optie, selecteer **door gaan** en selecteer vervolgens **Enter**.
     
     ![Scherm afbeelding die laat zien waar u door gaan selecteert en vervolgens ENTER selecteert.](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Selecteer **geen optie voor automatische updates** in de selectie voor het beheren van upgrades op uw systeem en selecteer vervolgens **Enter**.

     ![Selecteren hoe upgrades moeten worden beheerd](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Omdat de Azure Site Recovery hoofddoel server een zeer specifieke versie van de Ubuntu vereist, moet u ervoor zorgen dat de kernel-upgrades voor de virtuele machine zijn uitgeschakeld. Als ze zijn ingeschakeld, wordt de hoofddoel server niet goed door een regel matige upgrade van het doel. Zorg ervoor dat u de optie **geen automatische updates** selecteert.

1.  Selecteer standaard opties. Als u openSSH voor SSH-verbinding wilt, selecteert u de optie **openssh server** en selecteert u **door gaan**.

    ![Software selecteren](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Selecteer in de selectie voor het installeren van de GRUB-opstart lader **Ja** en selecteer vervolgens **Enter**.
     
    ![Installatie programma voor GRUB opstarten](./media/vmware-azure-install-linux-master-target/image20.png)


1. Selecteer het juiste apparaat voor de installatie van de opstart lader (bij voor keur **/dev/sda**), en selecteer vervolgens **Enter**.
     
    ![Het juiste apparaat selecteren](./media/vmware-azure-install-linux-master-target/image21.png)

1. Selecteer **door gaan** en selecteer vervolgens **Enter** om de installatie te volt ooien.

    ![De installatie volt ooien](./media/vmware-azure-install-linux-master-target/image22.png)

1. Nadat de installatie is voltooid, meldt u zich aan bij de VM met de nieuwe gebruikers referenties. (Zie **stap 10** voor meer informatie.)

1. Gebruik de stappen die worden beschreven in de volgende scherm afbeelding om het hoofd gebruikers wachtwoord in te stellen. Meld u vervolgens aan als hoofd gebruiker.

    ![Het hoofd gebruikers wachtwoord instellen](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>De machine als hoofddoel server configureren

Als u de ID voor elke SCSI harde schijf in een virtuele Linux-machine wilt ophalen, de **schijf. De para meter EnableUUID = TRUE** moet worden ingeschakeld. Voer de volgende stappen uit om deze para meter in te scha kelen:

1. Sluit de virtuele machine af.

2. Klik met de rechter muisknop op de vermelding voor de virtuele machine in het linkerdeel venster en selecteer vervolgens **Instellingen bewerken**.

3. Selecteer het tabblad **Opties** .

4. Selecteer **Geavanceerd** algemeen in het linkerdeel venster  >  en selecteer vervolgens de knop **configuratie parameters** in het rechter gedeelte van het scherm.

    ![Configuratie parameter openen](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    De optie **configuratie parameters** is niet beschikbaar wanneer de computer wordt uitgevoerd. Als u dit tabblad actief wilt maken, sluit u de virtuele machine af.

5. Bekijk of een rij met een **schijf. EnableUUID** bestaat al.

   - Als de waarde bestaat en is ingesteld op **Onwaar**, wijzigt u de waarde in **True**. (De waarden zijn niet hoofdletter gevoelig.)

   - Als de waarde bestaat en is ingesteld op **waar**, selecteert u **Annuleren**.

   - Als de waarde niet bestaat, selecteert u **rij toevoegen**.

   - Voeg in de kolom naam de **schijf toe. EnableUUID** en stel de waarde in op **waar**.

     ![Controleren of de schijf is. EnableUUID bestaat al](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Kernel-upgrades uitschakelen

Azure Site Recovery hoofddoel server een specifieke versie van de Ubuntu vereist, moet u ervoor zorgen dat de kernel-upgrades voor de virtuele machine zijn uitgeschakeld. Als kernel-upgrades zijn ingeschakeld, kan dit ertoe leiden dat de hoofddoel server niet goed werkt.

#### <a name="download-and-install-additional-packages"></a>Aanvullende pakketten downloaden en installeren

> [!NOTE]
> Zorg ervoor dat u verbinding hebt met internet om extra pakketten te downloaden en te installeren. Als u geen Internet verbinding hebt, moet u deze deb-pakketten hand matig zoeken en installeren.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Het installatie programma downloaden voor de installatie

Als uw hoofd doel internet connectiviteit heeft, kunt u de volgende stappen gebruiken om het installatie programma te downloaden. Als dat niet het geval is, kunt u het installatie programma kopiëren van de proces server en vervolgens installeren.

#### <a name="download-the-master-target-installation-packages"></a>De Master doel installatie pakketten downloaden

[Down load de meest recente Linux-Master doel installatie-bits](https://aka.ms/latestlinuxmobsvc).

Als u het programma wilt downloaden met Linux, typt u:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Zorg ervoor dat u het installatie programma in uw basismap downloadt en uitpakt. Als u uitpakt naar **/usr/local**, mislukt de installatie.


#### <a name="access-the-installer-from-the-process-server"></a>Het installatie programma openen vanaf de proces server

1. Ga op de proces server naar **C:\Program Files (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Kopieer het vereiste installatie bestand van de proces server en sla het op als **latestlinuxmobsvc. tar. gz** in uw basismap.


### <a name="apply-custom-configuration-changes"></a>Aangepaste configuratie wijzigingen Toep assen

Als u aangepaste configuratie wijzigingen wilt Toep assen, gebruikt u de volgende stappen als hoofd gebruiker:

1. Voer de volgende opdracht uit om het binaire bestand untar.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Scherm opname van de opdracht die moet worden uitgevoerd](./media/vmware-azure-install-linux-master-target/image16.png)

2. Voer de volgende opdracht uit om toestemming te geven.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Voer de volgende opdracht uit om het script uit te voeren.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Voer het script slechts één keer uit op de server. Sluit vervolgens de server af. Start de server opnieuw op nadat u een schijf hebt toegevoegd, zoals beschreven in de volgende sectie.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Een Bewaar schijf toevoegen aan de virtuele machine van het Linux-hoofd doel

Gebruik de volgende stappen om een Bewaar schijf te maken:

1. Koppel een nieuwe schijf van 1 TB aan de virtuele machine met het Linux-hoofd doel en start de computer.

2. Gebruik de **multipath-ll** opdracht voor het leren van de id van het meerdere paden van de Bewaar schijf: **meerdere paden-ll**

    ![ID voor meerdere paden](./media/vmware-azure-install-linux-master-target/image27.png)

3. Format teer het station en maak vervolgens een bestands systeem op het nieuwe station: **mkfs. ext4/dev/mapper/ \<Retention disk's multipath id>**.
    
    ![Bestandssysteem](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Nadat u het bestands systeem hebt gemaakt, koppelt u de retentie schijf.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Maak de vermelding **fstab** om het Bewaar station te koppelen telkens wanneer het systeem wordt gestart.
    
    `vi /etc/fstab`
    
    Selecteer **Invoegen** om te beginnen met het bewerken van het bestand. Maak een nieuwe regel en voeg de volgende tekst toe. Bewerk de schijf-ID voor meerdere paden op basis van de gemarkeerde multipath ID van de vorige opdracht.

    **/dev/mapper/ \<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Selecteer **ESC** en typ **: wq** (schrijven en sluiten) om het editor venster te sluiten.

### <a name="install-the-master-target"></a>Het hoofd doel installeren

> [!IMPORTANT]
> De versie van de hoofddoel server moet gelijk zijn aan of lager zijn dan de versies van de proces server en de configuratie server. Als niet aan deze voor waarde wordt voldaan, kunt u de beveiliging opnieuw beveiligen, maar de replicatie mislukt.


> [!NOTE]
> Controleer voordat u de hoofddoel server installeert of het **bestand/etc/hosts** -bestand op de virtuele machine vermeldingen bevat waarmee de lokale hostnaam wordt toegewezen aan de IP-adressen die zijn gekoppeld aan alle netwerk adapters.

1. Voer de volgende opdracht uit om het hoofd doel te installeren.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    ```

2. Kopieer de wachtwoordzin van **C:\ProgramData\Microsoft Azure site Recovery\private\connection.passphrase** op de configuratie server. Sla het vervolgens op als **passphrase.txt** in dezelfde lokale map door de volgende opdracht uit te voeren:

    `echo <passphrase> >passphrase.txt`

    Voorbeeld: 

    `echo itUx70I47uxDuUVY >passphrase.txt`
    

3. Noteer het IP-adres van de configuratie server. Voer de volgende opdracht uit om de server te registreren bij de configuratie server.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Voorbeeld: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Wacht totdat het script is voltooid. Als het hoofd doel is geregistreerd, wordt het hoofd doel weer gegeven op de pagina **site Recovery-infra structuur** van de portal.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Het hoofd doel installeren met behulp van interactieve installatie

1. Voer de volgende opdracht uit om het hoofd doel te installeren. Voor de rol agent kiest u **hoofd doel**.

    ```
    ./install
    ```

2. Kies de standaard locatie voor de installatie en selecteer vervolgens **Enter** om door te gaan.

    ![Een standaard locatie kiezen voor de installatie van het hoofd doel](./media/vmware-azure-install-linux-master-target/image17.png)

Nadat de installatie is voltooid, registreert u de configuratie server via de opdracht regel.

1. Noteer het IP-adres van de configuratie server. U hebt deze nodig in de volgende stap.

2. Voer de volgende opdracht uit om de server te registreren bij de configuratie server.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh
    ```

     Wacht totdat het script is voltooid. Als het hoofd doel is geregistreerd, wordt het hoofd doel weer gegeven op de pagina **site Recovery-infra structuur** van de portal.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>VMware-hulpprogram ma's/open-vm-hulpprogram ma's installeren op de hoofddoel server

U moet VMware-hulpprogram ma's of open-vm-hulpprogram ma's installeren op het hoofd doel zodat de gegevens archieven kunnen worden gedetecteerd. Als de hulpprogram ma's niet zijn geïnstalleerd, wordt het scherm opnieuw beveiligen niet weer gegeven in de gegevens archieven. Nadat de VMware-hulpprogram ma's zijn geïnstalleerd, moet u de computer opnieuw opstarten.

### <a name="upgrade-the-master-target-server"></a>De hoofddoel server bijwerken

Voer het installatieprogramma uit. Er wordt automatisch gedetecteerd dat de agent is geïnstalleerd op het hoofd doel. Selecteer **Y** als u een upgrade wilt uitvoeren.  Nadat de installatie is voltooid, controleert u de versie van het hoofd doel dat is geïnstalleerd met behulp van de volgende opdracht:

`cat /usr/local/.vx_version`


U ziet dat het versie **veld het versie nummer** van het hoofd doel bevat.

## <a name="common-issues"></a>Algemene problemen

* Zorg ervoor dat u Storage vMotion niet inschakelt voor beheer onderdelen zoals een hoofd doel. Als het hoofd doel wordt verplaatst nadat de beveiliging is geslaagd, kunnen de schijven van de virtuele machine (Vmdk's) niet worden losgekoppeld. In dit geval mislukt de failback.

* Het hoofd doel mag geen moment opnamen hebben op de virtuele machine. Als er moment opnamen zijn, mislukt de failback.

* Als gevolg van sommige aangepaste NIC-configuraties, wordt de netwerk interface tijdens het opstarten uitgeschakeld en kan de hoofddoel agent niet worden geïnitialiseerd. Zorg ervoor dat de volgende eigenschappen juist zijn ingesteld. Controleer deze eigenschappen in de/etc/network/interfaces. van het Ethernet-kaart bestand
    * automatische ETH0
    * iface eth0 inet dhcp <br>

    Start de netwerk service opnieuw met de volgende opdracht: <br>

`sudo systemctl restart networking`


## <a name="next-steps"></a>Volgende stappen
Nadat de installatie en registratie van het hoofd doel is voltooid, ziet u dat het hoofd doel wordt weer gegeven in de sectie **hoofd doel** van **site Recovery-infra structuur**, onder het overzicht van de configuratie server.

U kunt nu door gaan met het opnieuw [beveiligen](vmware-azure-reprotect.md), gevolgd door failback.

