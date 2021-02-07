---
title: Maak een back-up van een Oracle Database 19c-Data Base op een Azure Linux-VM met RMAN en Azure Storage
description: Meer informatie over het maken van een back-up van een Oracle Database 19c-Data Base naar Azure-Cloud opslag.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: fce947c43e8559f4ea2a65645805e987a9015d3f
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806270"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Back-ups maken en herstellen van een Oracle Database 19c-Data Base op een virtuele Azure Linux-machine met Azure Storage

In dit artikel wordt het gebruik van Azure Storage als een medium beschreven voor het maken van back-ups en het herstellen van een Oracle-data base die wordt uitgevoerd op een virtuele machine van Azure. U maakt een back-up van de data base met behulp van Oracle RMAN naar Azure file storage die is gekoppeld aan de virtuele machine met het SMB-protocol. Het gebruik van Azure Storage voor back-upmedia is zeer rendabel en wordt uitgevoerd. Voor zeer grote data bases biedt Azure Backup echter een betere oplossing.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Als u het back-up-en herstel proces wilt uitvoeren, moet u eerst een virtuele Linux-machine maken met een geïnstalleerd exemplaar van Oracle Database 19c. De Marketplace-installatie kopie die momenteel wordt gebruikt om de virtuele machine te maken, is  **Oracle: Oracle-data base-19-3: Oracle-data base-19-0904: meest recent**. Volg de stappen in de [Snelstartgids Oracle data base maken](./oracle-database-quick-create.md) om een Oracle-Data Base te maken om deze zelf studie te volt ooien.

## <a name="prepare-the-database-environment"></a>De database omgeving voorbereiden

1. Gebruik de volgende opdracht om een SSH-sessie (Secure Shell) met de virtuele machine te maken. Vervang het IP-adres en de hostnaam combi natie met de `publicIpAddress` waarde voor uw VM.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Schakel over naar de ***hoofd*** gebruiker:
 
   ```bash
   sudo su -
   ```
    
3. Voeg de Oracle-gebruiker toe aan het ***/etc/sudoers*** -bestand:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Bij deze stap wordt ervan uitgegaan dat u een Oracle-exemplaar (test) hebt dat wordt uitgevoerd op een virtuele machine met de naam *vmoracle19c*.

   Gebruiker overschakelen naar de *Oracle* -gebruiker:

   ```bash
   sudo su - oracle
   ```
    
5. Voordat u verbinding maakt, moet u de omgevings variabele ORACLE_SID instellen:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   U moet de variabele ORACLE_SID ook toevoegen aan het `oracle` gebruikers `.bashrc` bestand voor toekomstige aanmeldingen met behulp van de volgende opdracht:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Start de Oracle-listener als deze nog niet wordt uitgevoerd:
    
   ```bash
   $ lsnrctl start
   ```

    De uitvoer moet er als in het volgende voorbeeld uitzien:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

7.  De locatie voor snel herstel gebied maken (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Verbinding maken met de Data Base:

    ```bash
    sqlplus / as sysdba
    ```

9.  Start de Data Base als deze nog niet wordt uitgevoerd:

    ```bash
    SQL> startup
    ```

10. Omgevings variabelen voor de data base instellen voor een snel herstel gebied:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Zorg ervoor dat de data base zich in de archief modus Archive bevindt om online back-ups in te scha kelen.
    Controleer eerst de status van het logboek archief:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    In de modus NOARCHIVELOG voert u de volgende opdrachten uit in sqlplus:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Een tabel maken om de back-up-en herstel bewerkingen te testen:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Back-up naar Azure Files

Voer de volgende stappen uit om een back-up te maken naar Azure Files:

1. Azure File Storage instellen.
1. Koppel de Azure Storage-bestands share aan uw VM.
1. Maak een back-up van de data base.
1. De data base herstellen en herstellen.

### <a name="set-up-azure-file-storage"></a>Azure File Storage instellen

In deze stap maakt u een back-up van de Oracle-data base met behulp van Oracle Recovery Manager (RMAN) naar Azure file storage. Azure-bestands shares zijn volledig beheerde bestands shares die zich in de cloud bevinden. Ze kunnen worden geopend via het SMB-protocol (Server Message Block) of het NFS-protocol (Network File System). In deze stap wordt beschreven hoe u een bestands share maakt die het SMB-protocol gebruikt om te koppelen aan uw VM. Zie [Blob Storage koppelen met behulp van het nfs 3,0-protocol](../../../storage/blobs/network-file-system-protocol-support-how-to.md)voor meer informatie over het koppelen met NFS.

Wanneer u de Azure Files koppelt, gebruiken we de `cache=none` om caching van bestands share gegevens uit te scha kelen. En om ervoor te zorgen dat bestanden die zijn gemaakt in de share eigendom zijn van de Oracle-gebruiker, stelt u `uid=oracle` ook de opties en in `gid=oinstall` . 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Stel eerst uw opslag account in.

1. File Storage configureren in het Azure Portal

    Selecteer in de Azure Portal ***+ een resource maken** _ en zoek en selecteer _ *_opslag account_**
    
    ![Scherm afbeelding die laat zien waar u een resource maakt en opslag account selecteert.](./media/oracle-backup-recovery/storage-1.png)
    
2. Kies op de pagina opslag account maken de bestaande resource groep ***RG-Oracle** _, geef uw opslag account de naam _*_Oracbkup1_*_ en kies _*_opslag v2 (Generalpurpose v2)_*_ voor het soort account. Wijzig de replicatie naar _*_lokaal redundante opslag (LRS)_*_ en stel de prestaties in op _ *_standaard_* *. Zorg ervoor dat de locatie is ingesteld op dezelfde regio als alle andere resources in de resource groep. 
    
    ![Scherm afbeelding die laat zien waar u de bestaande resource groep kunt kiezen.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Klik op het tabblad ***Geavanceerd** _ en onder Azure files _*_grote bestands shares_*_ instellen op _ *_ingeschakeld_* *. Klik op controleren + maken en klik vervolgens op maken.
    
    ![Scherm opname van de locatie waar grote bestands shares moeten worden ingesteld.](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. Wanneer het opslag account is gemaakt, gaat u naar de resource en kiest u ***Bestands shares***
    
    ![Scherm afbeelding die laat zien waar u bestands shares selecteert.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Klik op ***+ Bestands share** _ en op de Blade _*_nieuwe bestands share_*_ de naam van de bestands share _*_orabkup1_*_. Stel _*_quota_*_ in op _*_10240_*_ GiB en controleer of de _*_trans actie is geoptimaliseerd_*_ als laag. Het quotum weerspiegelt een bovengrens waarmee de bestands share kan worden uitgebreid. Omdat we standaard opslag gebruiken, worden de resources PAYG en niet ingericht, dus als u deze instelt op 10 TiB, worden er geen kosten in rekening gebracht die verder gaan dan wat u gebruikt. Als uw back-upstrategie meer opslag ruimte vereist, moet u het quotum instellen op een geschikt niveau voor alle back-ups.   Wanneer u de Blade nieuwe bestands share hebt voltooid, klikt u op _ *_maken_* *.
    
    ![Scherm afbeelding die laat zien waar een nieuwe bestands share moet worden toegevoegd.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Wanneer u dit hebt gemaakt, klikt u op ***orabkup1*** op de pagina instellingen voor bestands share. 
    Klik op het tabblad ***verbinding maken** om de Blade verbinding maken te openen en klik vervolgens op het tabblad _ *_Linux_**. Kopieer de opdrachten voor het koppelen van de bestands share met het SMB-protocol. 
    
    ![Pagina voor toevoegen van opslag account](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u uw opslag account en bestands share wilt instellen, voert u de volgende opdrachten uit in azure CLI.

1. Maak het opslag account in dezelfde resource groep en locatie als uw VM:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Maak een bestands share in het opslag account met een quotum van 10 TiB:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Haal de primaire sleutel (Key1) van het opslag account op die nodig is bij het koppelen van de bestands share aan uw VM:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>De Azure Storage bestands share koppelen aan uw VM

1. Het koppel punt maken:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Referenties instellen:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   Vervang door `<Your Storage Account Key1>` de sleutel van het opslag account die u eerder hebt opgehaald, voordat u de volgende opdracht uitvoert:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Machtigingen voor het referentie bestand wijzigen:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Voeg de koppeling toe aan de bestand/etc/fstab:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Voer de volgende opdrachten uit om de Azure Files-opslag te koppelen met het SMB-protocol:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Als er een fout bericht wordt weer gegeven dat vergelijkbaar is met:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   het CIFS-pakket kan niet worden geïnstalleerd op uw Linux-host. 
   
   Als u wilt controleren of CIS is geïnstalleerd, voert u de volgende opdracht uit:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Als de opdracht geen uitvoer retourneert, installeert u het CIFS-pakket met de volgende opdracht:

   ```bash 
   sudo yum install cifs-utils
   ```

   Voer de bovenstaande koppelings opdracht uit om Azure Files-opslag te koppelen.

6. Controleer of de bestands share correct is gekoppeld met de volgende opdracht:

   ```bash
   df -h
   ```

   De uitvoer moet er ongeveer als volgt uitzien:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Back-up van de data base maken

In deze sectie wordt gebruikgemaakt van Oracle Recovery Manager (RMAN) om een volledige back-up van de data base te maken en de logboeken te archiveren en de back-up te schrijven als een back-upset naar de Azure-bestands share die u eerder hebt gekoppeld. 

1. Configureer RMAN voor het maken van een back-up naar het Azure Files koppel punt:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Omdat de bestands shares van Azure standaard een maximale grootte hebben van 1 TiB, worden de grootte van RMAN-back-uponderdelen beperkt tot 1 TiB. (Houd er rekening mee dat Premium-bestands shares de maximale bestands grootte limiet van 4 TiB hebben. Zie [Azure files schaal baarheid en prestatie doelen](../../../storage/files/storage-files-scale-targets.md)voor meer informatie.)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Bevestig de details van de configuratie wijziging:

    ```bash
    RMAN> show all;
    ```

4. Voer nu de back-up uit. Met de volgende opdracht wordt een volledige back-up van de data base, inclusief archief bestanden, gemaakt als back-upset in gecomprimeerde vorm:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

U hebt nu online een back-up van de data base gemaakt met behulp van Oracle RMAN, met de back-up op Azure file storage. Deze methode biedt het voor deel van het gebruik van de functies van RMAN bij het opslaan van back-ups in azure File Storage, waar ze toegankelijk zijn vanaf andere virtuele machines. Dit is handig als u de Data Base wilt klonen.  
    
Hoewel het gebruik van RMAN en Azure File Storage voor database back-ups veel voor delen heeft, wordt de back-up-en herstel tijd gekoppeld aan de grootte van de data base, dus voor zeer grote data bases kan het veel tijd kosten om deze bewerkingen uit te voeren.  Een alternatief back-upmechanisme, met behulp van Azure Backup toepassings consistente back-ups van virtuele machines, maakt gebruik van een snap shot-technologie voor het uitvoeren van back-ups, die het voor deel heeft van zeer snelle back-ups, ongeacht de database grootte Integratie met Recovery Services kluis biedt veilige opslag van uw Oracle Database back-ups in azure-Cloud opslag die toegankelijk zijn vanuit andere Vm's en Azure-regio's. 

### <a name="restore-and-recover-the-database"></a>De data base herstellen en herstellen

1.  Het Oracle-exemplaar afsluiten:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  De datafiles en back-ups verwijderen:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. De volgende opdrachten gebruiken RMAN om de ontbrekende datafiles te herstellen en de data base te herstellen:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Controleer of de inhoud van de data base volledig is hersteld:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


De back-up en het herstel van de Oracle Database 19c-Data Base op een Azure Linux VM is nu voltooid.

## <a name="delete-the-vm"></a>De VM verwijderen

Wanneer u de virtuele machine niet meer nodig hebt, kunt u de volgende opdracht gebruiken om de resource groep, de virtuele machine en alle gerelateerde resources te verwijderen:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Volgende stappen

[Zelf studie: Maxi maal beschik bare Vm's maken](../../linux/create-cli-complete.md)

[Azure CLI-voor beelden van VM-implementatie verkennen](../../linux/cli-samples.md)
