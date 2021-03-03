---
title: Oracle ASM op een virtuele Azure Linux-machine instellen | Microsoft Docs
description: Krijg snel en eenvoudig Oracle-ASM in uw Azure-omgeving.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: b180a0f148db5222a288958724ad248acd18b565
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673863"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Oracle ASM op een virtuele Azure Linux-machine instellen  

Virtuele machines in Azure bieden een volledig geconfigureerde en flexibele computeromgeving. In deze zelf studie wordt de basis implementatie van virtuele Azure-machines besproken, gecombineerd met de installatie en configuratie van Oracle Automated Storage Management (ASM).  In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een Oracle Database-VM maken en er verbinding mee tot stand brengen
> * Geautomatiseerd opslag beheer voor Oracle installeren en configureren
> * De Oracle-raster infrastructuur installeren en configureren
> * Een Oracle ASM-installatie initialiseren
> * Een Oracle DB maken dat wordt beheerd door ASM


Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie Azure CLI 2.0.4 of nieuwer uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. 

## <a name="prepare-the-environment"></a>De omgeving voorbereiden

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik de opdracht [AZ Group Create](/cli/azure/group) om een resource groep te maken. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In dit voor beeld wordt een resource groep met de naam *myResourceGroup* in de regio *eastus* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Een virtuele machine maken

Gebruik de opdracht [AZ VM Create](/cli/azure/vm) om een virtuele machine te maken op basis van de Oracle database installatie kopie en deze te configureren voor het gebruik van Oracle asm. 

In het volgende voor beeld wordt een VM gemaakt met de naam myVM die een Standard_DS2_v2 grootte heeft met vier gekoppelde gegevens schijven van 50 GB. Als ze nog niet bestaan op de standaard locatie van de sleutel, worden er ook SSH-sleutels gemaakt.  Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Nadat u de VM hebt gemaakt, geeft de Azure CLI informatie weer die lijkt op het volgende voorbeeld. Noteer de waarde voor `publicIpAddress`. Dit adres wordt gebruikt voor toegang tot de virtuele machine.

   ```output
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Gebruik de volgende opdracht als u een SSH-sessie met de virtuele machine wilt maken en aanvullende instellingen wilt configureren. Vervang het IP-adres door de waarde `publicIpAddress` voor uw VM.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Oracle ASM installeren

Voer de volgende stappen uit om Oracle ASM te installeren. 

Zie [Oracle ASMLib down loads for Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)(Engelstalig) voor meer informatie over het installeren van Oracle asm.  

1. U moet zich aanmelden als root om door te gaan met de installatie van ASM:

   ```bash
   sudo su -
   ```
   
2. Voer deze extra opdrachten uit om Oracle ASM-onderdelen te installeren:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Controleer of Oracle ASM is geïnstalleerd:

   ```bash
   rpm -qa |grep oracleasm
   ```

    De uitvoer van deze opdracht moet de volgende onderdelen vermelden:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM vereist specifieke gebruikers en rollen om goed te kunnen functioneren. Met de volgende opdrachten worden de vereiste gebruikers accounts en-groepen gemaakt: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Controleer of de gebruikers en groepen correct zijn gemaakt:

   ```bash
   id grid
   ```

    De uitvoer van deze opdracht moet de volgende gebruikers en groepen weer geven:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Maak een map voor gebruikers *raster* en wijzig de eigenaar:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Oracle ASM instellen

Voor deze zelf studie is de standaard gebruiker *raster* en is de standaard groep *asmadmin*. Zorg ervoor dat de *Oracle* -gebruiker deel uitmaakt van de asmadmin-groep. Voer de volgende stappen uit om uw Oracle ASM-installatie in te stellen:

1. Het instellen van het stuur programma voor de Oracle ASM-bibliotheek omvat het definiëren van de standaard gebruiker (raster) en de standaard groep (asmadmin), en het configureren van het station om te starten bij opstarten (Kies y) en om te scannen op schijven bij het opstarten (Kies y). U moet de prompts beantwoorden met de volgende opdracht:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   De uitvoer van deze opdracht moet er ongeveer als volgt uitzien, met de prompts die moeten worden beantwoord.

    ```output
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. De schijf configuratie weer geven:

   ```bash
   cat /proc/partitions
   ```

   De uitvoer van deze opdracht moet er ongeveer uitzien als de volgende lijst met beschik bare schijven

   ```output
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Format teer de schijf */dev/SDC* door de volgende opdracht uit te voeren en de prompts te beantwoorden met:
   - *n* voor nieuwe partitie
   - *p* voor primaire partitie
   - *1* om de eerste partitie te selecteren
   - Druk op `enter` de standaard eerste cilinder
   - Druk op `enter` de standaard laatste cilinder
   - Druk op *w* om de wijzigingen in de partitie tabel te schrijven  

   ```bash
   fdisk /dev/sdc
   ```
   
   Met de hierboven vermelde antwoorden moet de uitvoer voor de `fdisk` opdracht er als volgt uitzien:

   ```output
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Herhaal de voor gaande `fdisk` opdracht voor `/dev/sdd` , `/dev/sde` en `/dev/sdf` .

5. Controleer de schijf configuratie:

   ```bash
   cat /proc/partitions
   ```

   De uitvoer van de opdracht moet er als volgt uitzien:

   ```output
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Controleer de status van de Oracle ASM-service en start de Oracle ASM-service:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   De uitvoer van de opdracht moet er als volgt uitzien:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Oracle ASM-schijven maken:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   De uitvoer van de opdracht moet er als volgt uitzien:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Oracle ASM-schijven weer geven:

   ```bash
   service oracleasm listdisks
   ```

   De uitvoer van de opdracht moet de volgende Oracle ASM-schijven vermelden:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Wijzig de wacht woorden voor de hoofd-, Oracle-en grid-gebruikers. **Noteer deze nieuwe wacht woorden** wanneer u deze later tijdens de installatie gebruikt.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Wijzig de machtiging voor de map:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>De Oracle-raster infrastructuur downloaden en voorbereiden

Voer de volgende stappen uit om de Oracle Grid-infrastructuur software te downloaden en voor te bereiden:

1. Down load de Oracle grid-infra structuur vanaf de [Oracle ASM-download pagina](https://www.oracle.com/database/technologies/oracle19c-linux-downloads.html). 

   Down load de twee zip-bestanden onder de down load met de titel **Oracle database 12c-versie 1 (12.1.0.2.0) voor linux x86-64**.

2. Nadat u de zip-bestanden hebt gedownload naar de client computer, kunt u het Secure Copy Protocol (SCP) gebruiken om de bestanden te kopiëren naar uw VM:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH terug naar uw Oracle-VM in azure om de zip-bestanden te verplaatsen naar de map/opt. Wijzig vervolgens de eigenaar van de bestanden:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Pak de bestanden uit. (Installeer het hulp programma voor Linux-Unzip als dit nog niet is geïnstalleerd.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Machtiging wijzigen:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Geconfigureerde wissel ruimte bijwerken. Onderdelen van Oracle grid hebben ten minste 6,8 GB wissel ruimte nodig om raster te installeren. De standaard grootte van het wissel bestand voor het Oracle Linux van installatie kopieën in Azure is alleen 2048 MB. U moet het bestand verg Roten `ResourceDisk.SwapSizeMB` `/etc/waagent.conf` en de WALinuxAgent-service opnieuw starten om de bijgewerkte instellingen van kracht te laten worden. Omdat het een alleen-lezen bestand is, moet u de bestands machtigingen wijzigen om schrijf toegang in te scha kelen.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Zoek `ResourceDisk.SwapSizeMB` en wijzig de waarde in **8192**. U moet op `insert` de Invoeg modus drukken, de waarde **8192** invoeren en vervolgens op drukken klikken `esc` om terug te gaan naar de opdracht modus. Als u de wijzigingen wilt schrijven en het bestand wilt afsluiten, typt u `:wq` en drukt u op `enter` .
   
   > [!NOTE]
   > U wordt ten zeerste aangeraden altijd `WALinuxAgent` te gebruiken voor het configureren van de wissel ruimte, zodat deze altijd wordt gemaakt op de lokale tijdelijke schijf (tijdelijke schijf) voor de beste prestaties. Zie [How to add a swap file in virtuele machines van Linux Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)voor meer informatie.

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Bereid uw lokale client en VM voor op het uitvoeren van X11
Voor het configureren van Oracle ASM is een grafische interface vereist om de installatie en configuratie te volt ooien. We gebruiken het X11-protocol om deze installatie te vergemakkelijken. Als u een-client systeem (Mac of Linux) gebruikt waarop al X11-functies zijn ingeschakeld en geconfigureerd, kunt u deze configuratie en instellingen voor Windows-computers overs Laan. 

1. [Down load putty](https://www.putty.org/) en [down load Xming](https://xming.en.softonic.com/) naar uw Windows-computer. U moet de installatie van beide toepassingen met de standaard waarden volt ooien voordat u kunt door gaan.

2. Nadat u PuTTy hebt geïnstalleerd, opent u een opdracht prompt, wijzigt u in de map PuTTy (bijvoorbeeld C:\Program Files\PuTTY) en voert u `puttygen.exe` uit om een sleutel te genereren.

3. In PuTTy-sleutel Generator:
   
   1. Genereer een sleutel door de `Generate` knop te selecteren.
   2. Kopieer de inhoud van de sleutel (CTRL + C).
   3. Selecteer de knop `Save private key`.
   4. Negeer de waarschuwing over het beveiligen van de sleutel met een wachtwoordzin en selecteer deze `OK` .

   ![Scherm opname van PuTTy-sleutel generator](./media/oracle-asm/puttykeygen.png)

4. Voer de volgende opdrachten uit in uw virtuele machine:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Maak een bestand met de naam `authorized_keys`. Plak de inhoud van de sleutel in dit bestand en sla het bestand op.

   > [!NOTE]
   > De sleutel moet de teken reeks bevatten `ssh-rsa` . De inhoud van de sleutel moet ook één tekst regel zijn.
   >  

6. Start PuTTy op het client systeem. Ga in het deel venster **categorie** naar   >  **SSH**-  >  **verificatie** voor verbindingen. Blader in het vak **persoonlijk sleutel bestand voor verificatie** naar de sleutel die u eerder hebt gegenereerd.

   ![Scherm afbeelding van de opties voor SSH-verificatie](./media/oracle-asm/setprivatekey.png)

7. Ga in het deel venster **categorie** naar **verbinding**  >  **SSH**-  >  **X11**. Schakel het selectie vakje **X11 door sturen inschakelen** in.

   ![Scherm afbeelding van de opties voor het door sturen van SSH-X11](./media/oracle-asm/enablex11.png)

8. Ga in het deel venster **categorie** naar **sessie**. Voer uw Oracle ASM VM `<publicIPaddress>` in het dialoog venster hostnaam in, vul een nieuwe `Saved Session` naam in en klik op `Save` .  Zodra de opslag is opgeslagen, klikt `open` u op om verbinding te maken met uw virtuele Oracle ASM-machine.  De eerste keer dat u verbinding maakt, wordt u gewaarschuwd het externe systeem is niet in de cache van het REGI ster opgeslagen. Klik op `yes` toevoegen om deze toe te voegen en door te gaan.

   ![Scherm afbeelding van de opties voor de PuTTy-sessie](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>De Oracle-raster infrastructuur installeren

Voer de volgende stappen uit om de Oracle-raster infrastructuur te installeren:

1. Meld u aan als **raster**. (U moet zich kunnen aanmelden zonder dat u wordt gevraagd om een wacht woord op te vragen.) 

   > [!NOTE]
   > Als u Windows gebruikt, moet u ervoor zorgen dat u Xming hebt gestart voordat u met de installatie begint.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Het installatie programma voor de Oracle grid Infrastructure 12c release 1 wordt geopend. (Het kan enkele minuten duren voordat het installatie programma wordt gestart.)

2. Op de pagina **installatie optie selecteren** selecteert u de **Oracle-raster infrastructuur installeren en configureren voor een zelfstandige server**.

   ![Scherm afbeelding van de pagina installatie optie selecteren van het installatie programma](./media/oracle-asm/install01.png)

3. Zorg ervoor dat op de pagina **product talen selecteren de optie** **Engels** of de gewenste taal is geselecteerd.  Klik op `next`.

4. Op de pagina **ASM-schijf groep maken** :
   - Voer een naam in voor de schijf groep.
   - Onder **Redundantie** selecteert u **extern**.
   - Onder **grootte van toewijzings eenheid** selecteert u **4**.
   - Onder **schijven toevoegen** selecteert u **ORCLASMSP**.
   - Klik op `next`.

5. Selecteer op de pagina **ASM-wacht woord opgeven** de optie **zelfde wacht woorden voor deze accounts gebruiken** en voer een wacht woord in.

   ![Scherm afbeelding van de pagina ASM-wacht woord opgeven van het installatie programma](./media/oracle-asm/install04.png)

6. Op de pagina **beheer opties opgeven** kunt u de optie em-Cloud beheer configureren. Deze optie wordt overgeslagen. Klik op `next` om door te gaan. 

7. Gebruik de standaard instellingen op de pagina **geprivilegieerde besturings systeem groepen** . Klik `next` om door te gaan.

8. Gebruik de standaard instellingen op de pagina **installatie locatie opgeven** . Klik `next` om door te gaan.

9. Op de pagina **inventaris maken** wijzigt u de map Inventory in `/u01/app/grid/oraInventory` . Klik `next` om door te gaan.

   ![Scherm afbeelding van de pagina voor het maken van een inventaris van het installatie programma](./media/oracle-asm/install08.png)

10. Schakel op de pagina **basis configuratie van uitvoering van script** het selectie vakje **configuratie scripts automatisch uitvoeren** in. Selecteer vervolgens de optie **basis gebruikers referenties gebruiken** en voer het wacht woord voor de hoofd gebruiker in.

    ![Scherm afbeelding van de configuratie pagina voor het uitvoeren van het installatie programma](./media/oracle-asm/install09.png)

11. Op de pagina **vereisten controles uitvoeren** mislukt de huidige installatie met fouten. Dit is een verwacht gedrag. Selecteer `Fix & Check Again`.

12. Klik in het dialoog venster **correctie script** op `OK` .

13. Controleer de geselecteerde instellingen op de pagina **samen vatting** en klik vervolgens op `Install` .

    ![Scherm afbeelding van de pagina samen vatting van het installatie programma](./media/oracle-asm/install12.png)

14. Er wordt een waarschuwing weer gegeven waarin wordt gemeld dat configuratie scripts moeten worden uitgevoerd als een bevoegde gebruiker. Klik `Yes` om door te gaan.

15. Klik op de pagina **volt ooien** op `Close` om de installatie te volt ooien.

## <a name="set-up-your-oracle-asm-installation"></a>Uw Oracle ASM-installatie instellen

Voer de volgende stappen uit om uw Oracle ASM-installatie in te stellen:

1. Zorg ervoor dat u nog steeds bent aangemeld als **raster** vanuit uw X11-sessie. Mogelijk moet u `enter` de Terminal actief blijven. Start vervolgens de Oracle-configuratie-assistent voor geautomatiseerd opslag beheer:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   De Oracle ASM Configuration-assistent wordt geopend.

2. Klik in het dialoog venster **ASM: schijf groepen configureren** op de `Create` knop en klik vervolgens op `Show Advanced Options` .

3. In het dialoog venster **schijf groep maken** :

   - Voer de naam **gegevens** van de schijf groep in.
   - Selecteer **ORCL_DATA** en **ORCL_DATA1** onder **andere schijven selecteren**.
   - Onder **grootte van toewijzings eenheid** selecteert u **4**.
   - Klik `ok` om de schijf groep te maken.
   - Klik `ok` om het bevestigings venster te sluiten.

   ![Scherm afbeelding van het dialoog venster schijf groep maken](./media/oracle-asm/asm02.png)

4. Klik in het dialoog venster **ASM: schijf groepen configureren** op de `Create` knop en klik vervolgens op `Show Advanced Options` .

5. In het dialoog venster **schijf groep maken** :

   - Voer de naam van de schijf groep **fra** in.
   - Onder **Redundantie** selecteert u **extern (geen)**.
   - Selecteer bij **Selecteer schijven selecteren de** optie **ORCL_FRA**.
   - Onder **grootte van toewijzings eenheid** selecteert u **4**.
   - Klik `ok` om de schijf groep te maken.
   - Klik `ok` om het bevestigings venster te sluiten.

   ![Scherm opname van het dialoog venster schijf groep maken en markeert de externe (geen) optie.](./media/oracle-asm/asm04.png)

6. Selecteer **Exit** om de ASM-configuratie-assistent te sluiten.

   ![Scherm afbeelding van het dialoog venster ASM: schijf groepen configureren met de knop Afsluiten](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>De database maken

De Oracle data base-software is al geïnstalleerd op de Azure Marketplace-installatie kopie. Voer de volgende stappen uit om een Data Base te maken:

1. Schakel gebruikers over naar de Oracle-super gebruiker en Initialiseer vervolgens de listener voor logboek registratie:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Data base configuratie-assistent wordt geopend.

2. Klik op de pagina **database bewerking** op `Create Database` .

3. Op de pagina **aanmaak modus** :

   - Voer een naam in voor de data base.
   - Zorg ervoor dat voor **opslag type** **automatische opslag beheer (ASM)** is geselecteerd.
   - Voor de **locatie van database bestanden** gebruikt u de standaard voorgestelde ASM-locatie.
   - Voor een **snel herstel gebied** gebruikt u de standaard voorgestelde ASM-locatie.
   - Typ een **beheerders wachtwoord** en **Bevestig het wacht woord**.
   - Zorg ervoor dat `create as container database` is geselecteerd.
   - Typ een `pluggable database name` waarde.

4. Controleer de geselecteerde instellingen op de pagina **samen vatting** en klik vervolgens op `Finish` om de data base te maken.

   ![Schermopname van de pagina Samenvatting](./media/oracle-asm/createdb03.png)

5. De data base is gemaakt. Op de pagina **volt ooien** hebt u de mogelijkheid om extra accounts te ontgrendelen voor het gebruik van deze data base en de wacht woorden te wijzigen. Als u dit wilt doen, selecteert u **wachtwoord beheer** . Klik anders op `close` .

## <a name="delete-the-vm"></a>De VM verwijderen

U hebt geautomatiseerd beheer van Oracle automatisch geconfigureerd op de Oracle DB installatie kopie van de Azure Marketplace.  Wanneer u deze virtuele machine niet meer nodig hebt, kunt u de volgende opdracht gebruiken om de resource groep, de VM en alle gerelateerde resources te verwijderen:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

[Zelf studie: Oracle-Dataguard configureren configureren](configure-oracle-dataguard.md)

[Zelf studie: Oracle-Golden Gate configureren](Configure-oracle-golden-gate.md)

[Architect een Oracle DB](oracle-design.md) evalueren
