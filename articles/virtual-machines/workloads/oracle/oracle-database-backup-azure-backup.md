---
title: Back-ups maken en herstellen van een Oracle Database 19c-Data Base op een virtuele Azure Linux-machine met Azure Backup
description: Meer informatie over het maken van een back-up en het herstellen van een Oracle Database 19c-data base via de Azure Backup-service.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 3122b1c5d7ac8b9dca0e244a4b7e73a57c4c5fca
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072401"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Back-ups maken en herstellen van een Oracle Database 19c-Data Base op een virtuele Azure Linux-machine met Azure Backup

In dit artikel wordt het gebruik van Azure Backup beschreven om moment opnamen van schijven te maken van de VM-schijven, waaronder de database bestanden en het gebied voor snel herstel. Met Azure Backup kunt u moment opnamen van de volledige schijf gebruiken als back-ups, die zijn opgeslagen in [Recovery Services kluis](../../../backup/backup-azure-recovery-services-vault-overview.md).  Azure Backup biedt ook toepassings consistente back-ups, waardoor er geen extra oplossingen nodig zijn om de gegevens te herstellen. Herstellen van toepassingsconsistente gegevens verkort de hersteltijd, zodat u snel weer normaal aan het werk kunt.

> [!div class="checklist"]
>
> * Maak een back-up van de data base met toepassings consistente back-up
> * De data base herstellen en herstellen vanaf een herstel punt
> * De VM herstellen vanaf een herstel punt

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Als u het back-up-en herstel proces wilt uitvoeren, moet u eerst een virtuele Linux-machine maken met een geïnstalleerd exemplaar van Oracle Database 19c. De Marketplace-installatie kopie die momenteel wordt gebruikt om de virtuele machine te maken, is  **Oracle: Oracle-data base-19-3: Oracle-data base-19-0904: meest recent**. Volg de stappen in de [Snelstartgids Oracle data base maken](./oracle-database-quick-create.md) om een Oracle-Data Base te maken om deze zelf studie te volt ooien.


## <a name="prepare-the-environment"></a>De omgeving voorbereiden

Voer de volgende stappen uit om de omgeving voor te bereiden:

1. Maak verbinding met de VM.
1. Bereid de Data Base voor.

### <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

1. Gebruik de volgende opdracht om een SSH-sessie (Secure Shell) met de virtuele machine te maken. Vervang het IP-adres en de hostnaam combi natie met de `<publicIpAddress>` waarde voor uw VM.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Schakel over naar de *hoofd* gebruiker:

   ```bash
   sudo su -
   ```
    
1. Voeg de Oracle-gebruiker toe aan het */etc/sudoers* -bestand:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>De data base voorbereiden

Bij deze stap wordt ervan uitgegaan dat u een Oracle-exemplaar (*test*) hebt dat wordt uitgevoerd op een virtuele machine met de naam *vmoracle19c*.

1. Gebruiker overschakelen naar de *Oracle* -gebruiker:
 
   ```bash
    sudo su - oracle
    ```
    
2. Voordat u verbinding maakt, moet u de omgevings variabele ORACLE_SID instellen:
    
    ```bash
    export ORACLE_SID=test;
    ```

    U moet de variabele ORACLE_SID ook toevoegen aan het `oracle` gebruikers `.bashrc` bestand voor toekomstige aanmeldingen met behulp van de volgende opdracht:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. De Oracle-listener starten als deze nog niet wordt uitgevoerd:

    ```output
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

4.  De locatie voor snel herstel gebied maken (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Verbinding maken met de Data Base:

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Start de Data Base als deze nog niet wordt uitgevoerd:

    ```bash
    SQL> startup
    ```

7.  Omgevings variabelen voor de data base instellen voor een snel herstel gebied:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Zorg ervoor dat de data base zich in de archief modus Archive bevindt om online back-ups in te scha kelen.

    Controleer eerst de status van het logboek archief:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Als het zich in de NOARCHIVELOG-modus bevindt, voert u de volgende opdrachten uit:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  Een tabel maken om de back-up-en herstel bewerkingen te testen:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. Configureer RMAN om een back-up te maken naar het snelle herstel gebied op de VM-schijf:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Bevestig de details van de configuratie wijziging:

    ```bash
    RMAN> show all;
    ```    

12.  Voer nu de back-up uit. Met de volgende opdracht wordt een volledige back-up van de data base, inclusief archief bestanden, gemaakt als back-upset in gecomprimeerde vorm:

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup"></a>Azure Backup gebruiken

De Azure Backup-service biedt eenvoudige, beveiligde en kosteneffectieve oplossingen om een back-up te maken van uw gegevens en die te herstellen vanuit Microsoft Azure-cloud. Azure Backup biedt onafhankelijke en geïsoleerde back-ups om te voorkomen dat oorspronkelijke gegevens per ongeluk worden vernietigd. Back-ups worden opgeslagen in een Recovery Services-kluis met ingebouwd beheer van herstelpunten. Configuratie en schaalbaarheid zijn eenvoudig, back-ups zijn geoptimaliseerd en kunt u eenvoudig naar behoefte herstellen.

Azure Backup-service biedt een [Framework](../../../backup/backup-azure-linux-app-consistent.md) om toepassings consistentie te krijgen tijdens het maken van back-ups van Windows-en Linux-vm's voor diverse toepassingen, zoals Oracle, MySQL, Mongo DB, SAP Hana en postgresql. Dit omvat het aanroepen van een pre-script (om de toepassingen stil te leggen) voordat een moment opname van schijven wordt gemaakt en het aanroepen van post script (opdrachten voor het opheffen van de toepassingen) nadat de moment opname is voltooid, om de toepassingen te retour neren naar de normale modus. Hoewel voor beelden van pre-scripts en post-scripts worden gegeven in GitHub, is het maken en onderhouden van deze scripts uw verantwoordelijkheid. 

Azure Backup is nu voorzien van een verbeterd pre-scripts en post-script-Framework, waarbij de Azure Backup-Service vooraf gebundelde pre-scripts en post scripts voor geselecteerde toepassingen biedt. Azure Backup gebruikers hoeven alleen de naam van de toepassing te noemen en vervolgens wordt de relevante pre-post scripts automatisch door Azure VM-back-up aangeroepen. De verpakte pre-scripts en post-scripts worden onderhouden door het Azure Backup-team, zodat gebruikers kunnen worden verzekerd van de ondersteuning, het eigendom en de geldigheid van deze scripts. Momenteel zijn de ondersteunde toepassingen voor het uitgebreide Framework *Oracle* en *MySQL*.

In deze sectie gaat u Azure Backup verbeterd Framework gebruiken om toepassings consistente moment opnamen van uw actieve virtuele machine en Oracle Data Base te maken. De data base wordt in de back-upmodus geplaatst, zodat er een transactionele, consistente online back-up wordt uitgevoerd terwijl Azure Backup een moment opname van de VM-schijven maakt. De moment opname is een volledige kopie van de opslag en niet een incrementele of gekopieerde moment opname van een schrijf bewerking. het is dus een effectief medium om uw data base van te herstellen. Het voor deel van het gebruik van Azure Backup toepassings consistente moment opnamen is dat ze zeer snel te maken krijgen, hoe groot uw data base is en dat een moment opname kan worden gebruikt voor herstel bewerkingen zodra deze worden uitgevoerd, zonder dat u hoeft te wachten totdat de gegevens naar de Recovery Services kluis worden overgebracht.

Voer de volgende stappen uit om Azure Backup te gebruiken voor het maken van een back-up van de Data Base:

1. De omgeving voorbereiden voor een toepassings consistente back-up.
1. Stel toepassings consistente back-ups in.
1. Activeer een toepassings consistente back-up van de virtuele machine.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>De omgeving voorbereiden voor een toepassings consistente back-up

1. Schakel over naar de *hoofd* gebruiker:

   ```bash
   sudo su -
   ```

1. Nieuwe back-upgebruiker maken:

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. Stel de gebruikers omgeving van de back-up in:

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Externe authenticatie instellen voor de nieuwe back-upgebruiker. De back-upgebruiker moet toegang hebben tot de data base met behulp van externe verificatie, zodat deze niet wordt aangevraagd met een wacht woord.

   Ga eerst terug naar de *Oracle* -gebruiker:

   ```bash
   su - oracle
   ```

   Meld u aan bij de data base met behulp van sqlplus en controleer de standaard instellingen voor externe verificatie:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   De uitvoer moet er als volgt uitzien: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Maak nu een database gebruiker *azbackup* geverifieerd extern en wijs sysbackup-bevoegdheid toe:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Als er een fout bericht wordt weer gegeven `ORA-46953: The password file is not in the 12.2 format.`  Wanneer u de `GRANT` instructie uitvoert, voert u de volgende stappen uit om het orapwd-bestand te migreren naar de 12,2-indeling:
   >
   > 1. Sluit sqlplus af.
   > 1. Verplaats het wachtwoord bestand met de oude indeling naar een nieuwe naam.
   > 1. Migreer het wachtwoord bestand.
   > 1. Verwijder het oude bestand.
   > 1. Voer de volgende opdracht uit:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. Voer de `GRANT` bewerking opnieuw uit in sqlplus.
   >
   
4. Een opgeslagen procedure maken voor het vastleggen van back-upberichten in het logboek voor database waarschuwingen:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>Toepassings consistente back-ups instellen  

1. Schakel over naar de *hoofd* gebruiker:

   ```bash
   sudo su -
   ```

2. Maak de toepassings consistente werkmap voor back-ups:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. Maak een bestand in de */etc/Azure* -map met de naam *workload. conf* met de volgende inhoud, die moet beginnen met `[workload]` . Met de volgende opdracht wordt het bestand gemaakt en wordt de inhoud gevuld:

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

4. Down load de scripts preOracleMaster. SQL en postOracleMaster. SQL van de [github-opslag plaats](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) en kopieer deze naar de map */etc/Azure* .

5. De bestands machtigingen wijzigen

```bash
   chmod 744 workload.conf preOracleMaster.sql postOracleMaster.sql 
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Een toepassings consistente back-up van de virtuele machine activeren

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Ga in het Azure Portal naar de resource groep **RG-Oracle** en klik op de **Vmoracle19c** van de virtuele machine.

2.  Maak op de Blade **back-up** een nieuwe **Recovery Services kluis** in de resource groep **RG-Oracle** met de naam **myVault**.
    Gebruik **(nieuw) DailyPolicy** voor het kiezen van het **back-upbeleid**. Als u de back-upfrequentie of het Bewaar bereik wilt wijzigen, selecteert u in plaats daarvan **een nieuw beleid maken** .

    ![Pagina Recovery Services kluizen toevoegen](./media/oracle-backup-recovery/recovery-service-01.png)

3.  Klik op **back-up inschakelen** om door te gaan.

    > [!IMPORTANT]
    > Nadat u op **back-up inschakelen** hebt geklikt, wordt het back-upproces pas gestart nadat de geplande tijd is verstreken. Voer de volgende stap uit om een onmiddellijke back-up in te stellen.

4. Klik op de pagina resource groep op de zojuist gemaakte Recovery Services kluis **myVault**. Hint: u moet de pagina mogelijk vernieuwen om deze te zien.

5.  Selecteer op de Blade **myVault-back-** **upitems, onder aantal back-** items, het aantal back-upitems.

    ![Detail pagina Recovery Services kluizen myVault](./media/oracle-backup-recovery/recovery-service-02.png)

6.  Klik op de Blade **Back-upitems (virtuele machine van Azure)** aan de rechter kant van de pagina op de knop met het weglatings teken (**...**) en klik vervolgens op **Nu back-up maken**.

    ![De opdracht nu back-ups Recovery Services-kluizen](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Accepteer de standaard waarde back-up behouden en klik op de knop **OK** . Wacht totdat het back-upproces is voltooid. 

    Als u de status van de back-uptaak wilt weer geven, klikt u op **back-uptaken**.

    ![Taak pagina Recovery Services kluizen](./media/oracle-backup-recovery/recovery-service-04.png)

    De status van de back-uptaak wordt weer gegeven in de volgende afbeelding:

    ![Taak pagina Recovery Services kluizen met status](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Houd er rekening mee dat er slechts enkele seconden nodig zijn om de moment opname uit te voeren. Dit kan enige tijd duren voordat de back-uptaak wordt overgezet naar de kluis. het is niet mogelijk om de overdracht te volt ooien.

8. Voor een toepassings consistente back-up verhelpt u eventuele fouten in het logboek bestand. Het logboek bestand bevindt zich op/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Een Recovery Services-kluis maken:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. Back-upbeveiliging inschakelen voor de VM:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. Activeer nu een back-up om te worden uitgevoerd in plaats van te wachten totdat de back-up wordt geactiveerd met het standaard schema (5 AM UTC): 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   U kunt de voortgang van de back-uptaak bewaken met `az backup job list` en `az backup job show` .

---

## <a name="recovery"></a>Herstel

Voer de volgende stappen uit om de data base te herstellen:

1. Verwijder de database bestanden.
1. Genereer een herstel script van de Recovery Services kluis.
1. Koppel het herstel punt.
1. Herstel uit te voeren.

### <a name="remove-the-database-files"></a>De database bestanden verwijderen 

Verderop in dit artikel leert u hoe u het herstel proces kunt testen. Voordat u het herstel proces kunt testen, moet u de database bestanden verwijderen.

1.  Het Oracle-exemplaar afsluiten:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  De datafiles en back-ups verwijderen:

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Een herstel script genereren vanuit de Recovery Services kluis

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zoek in het Azure Portal naar het item *myVault* Recovery Services kluizen en selecteer dit.

    ![Recovery Services kluizen myVault back-upitems](./media/oracle-backup-recovery/recovery-service-06.png)

2. Selecteer op de Blade **overzicht** de optie **back-** upitems en de **virtuele machine van Azure** selecteren. deze moet veri-nul hebben.

    ![Recovery Services kluizen voor het aantal back-items van Azure virtual machine](./media/oracle-backup-recovery/recovery-service-07.png)

3. Op de pagina Back-upitems (Azure Virtual Machines) wordt uw VM- **vmoracle19c** weer gegeven. Klik op het weglatings teken aan de rechter kant om het menu te openen en **bestands herstel** te selecteren.

    ![Scherm afbeelding van de pagina Recovery Services kluizen bestands herstel](./media/oracle-backup-recovery/recovery-service-08.png)

4. Klik in het deel venster **bestands herstel (preview)** op **script downloaden**. Sla het Download bestand (. py) vervolgens op in een map op de client computer. Er wordt een wacht woord gegenereerd voor het uitvoeren van het script. Kopieer het wacht woord naar een bestand voor later gebruik. 

    ![Opties voor downloaden script bestand opslaan](./media/oracle-backup-recovery/recovery-service-09.png)

5. Kopieer het. py-bestand naar de virtuele machine.

    In het volgende voor beeld ziet u hoe u een Secure copy-opdracht (SCP) gebruikt om het bestand naar de virtuele machine te verplaatsen. U kunt de inhoud ook kopiëren naar het klem bord en vervolgens de inhoud plakken in een nieuw bestand dat is ingesteld op de virtuele machine.

    > [!IMPORTANT]
    > Zorg er in het volgende voor beeld voor dat u de waarden voor het IP-adres en de map bijwerkt. De waarden moeten worden toegewezen aan de map waarin het bestand is opgeslagen.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik AZ backup Recovery Point List om herstel punten voor uw virtuele machine weer te geven. In dit voorbeeld wordt het meest recente herstelpunt geselecteerd voor de VM met de naam myVM, die wordt beveiligd in myRecoveryServicesVault:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Gebruik az backup restore files mount-rp om het script op te halen waarmee het herstelpunt met de VM wordt verbonden. In het volgende voorbeeld wordt het script opgehaald voor de VM met de naam myVM, die wordt beveiligd in myRecoveryServicesVault.

Vervang myRecoveryPointName door de naam van het herstelpunt dat u hebt verkregen in de voorgaande opdracht:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

Het script wordt gedownload en er wordt een wachtwoord weergegeven, zoals getoond in het volgende voorbeeld:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Kopieer het. py-bestand naar de virtuele machine.

In het volgende voor beeld ziet u hoe u een Secure copy-opdracht (SCP) gebruikt om het bestand naar de virtuele machine te verplaatsen. U kunt de inhoud ook kopiëren naar het klem bord en vervolgens de inhoud plakken in een nieuw bestand dat is ingesteld op de virtuele machine.

> [!IMPORTANT]
> Zorg er in het volgende voor beeld voor dat u de waarden voor het IP-adres en de map bijwerkt. De waarden moeten worden toegewezen aan de map waarin het bestand is opgeslagen.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Het herstel punt koppelen

1. Maak een koppel punt voor herstel en kopieer het script ernaar.

    In het volgende voor beeld maakt u een */Restore* -map voor de moment opname die u wilt koppelen, verplaatst u het bestand naar de map en wijzigt u het bestand zodat het eigendom is van de hoofd gebruiker en het uitvoer bare bestand.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Voer nu het script uit om de back-up te herstellen. U wordt gevraagd het wacht woord op te geven dat is gegenereerd in Azure Portal. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    In het volgende voor beeld ziet u wat u moet zien nadat u het voor gaande script hebt uitgevoerd. Wanneer u wordt gevraagd om door te gaan, voert u **Y** in.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. De toegang tot de gekoppelde volumes wordt bevestigd.

    Als u wilt afsluiten, typt u **q** en zoekt u naar de gekoppelde volumes. Als u een lijst met de toegevoegde volumes wilt maken, voert u bij de opdracht prompt **VG-h** in.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Herstel uitvoeren

1. Kopieer de ontbrekende back-upbestanden terug naar het gebied voor snel herstel:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. De volgende opdrachten gebruiken RMAN om de ontbrekende datafiles te herstellen en de data base te herstellen:

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Controleer of de inhoud van de data base volledig is hersteld:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Ontkoppel het herstel punt.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    Klik in de Azure Portal op de Blade **bestands herstel (preview-versie)** op **schijven ontkoppelen**.

    ![Schijven ontkoppelen, opdracht](./media/oracle-backup-recovery/recovery-service-10.png)
    
    U kunt de herstel volumes ook ontkoppelen door het python-script opnieuw uit te voeren met de optie **-clean** .

## <a name="restore-the-entire-vm"></a>De volledige VM herstellen

In plaats van de verwijderde bestanden van de Recovery Services kluizen te herstellen, kunt u de hele virtuele machine herstellen.

Voer de volgende stappen uit om de hele virtuele machine te herstellen:

1. Stop en verwijder vmoracle19c.
1. Herstel de virtuele machine.
1. Stel het open bare IP-adres in.
1. Maak verbinding met de VM.
1. Start de data base om het stadium te koppelen en herstel uit te voeren.

### <a name="stop-and-delete-vmoracle19c"></a>Vmoracle19c stoppen en verwijderen

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in het Azure Portal naar de virtuele machine **vmoracle19c** en selecteer **stoppen**.

1. Wanneer de virtuele machine niet meer wordt uitgevoerd, selecteert u **verwijderen** en vervolgens **Ja**.

   ![Opdracht kluis verwijderen](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. De virtuele machine stoppen en de toewijzing ongedaan maken:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. Verwijder de virtuele machine. Voer ' y ' in wanneer u hierom wordt gevraagd:

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>De VM herstellen

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Maak een opslag account voor fase ring in de Azure Portal.

   1. Selecteer in de Azure Portal **+ een resource maken** en zoek en selecteer een **opslag account**.
    
      ![Pagina voor toevoegen van opslag account](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. Kies op de pagina opslag account maken de bestaande resource groep **RG-Oracle**, geef uw opslag account de naam **Oracrestore** en kies **Storage v2 (Generalpurpose v2)** voor het soort account. Wijzig de replicatie naar **lokaal redundante opslag (LRS)** en stel de prestaties in op **standaard**. Zorg ervoor dat de locatie is ingesteld op dezelfde regio als alle andere resources in de resource groep. 
    
      ![Pagina voor toevoegen van opslag account](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Klik op controleren + maken en klik vervolgens op maken.

2. Zoek in het Azure Portal naar het item *myVault* Recovery Services kluizen en klik erop.

    ![Recovery Services kluizen myVault back-upitems](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  Selecteer op de Blade **overzicht** de optie **back-** upitems en de **virtuele machine van Azure** selecteren. deze moet veri-nul hebben.

    ![Recovery Services kluizen voor het aantal back-items van Azure virtual machine](./media/oracle-backup-recovery/recovery-service-07.png)

4.  Op de back-upitems (Azure Virtual Machines) wordt pagina uw VM- **vmoracle19c** weer gegeven. Klik op de naam van de virtuele machine.

    ![Pagina herstel-VM](./media/oracle-backup-recovery/recover-vm-02.png)

5.  Kies op de Blade **vmoracle19c** een herstel punt met het consistentie type **toepassings consistent** en klik op het weglatings teken (**...**) aan de rechter kant om het menu weer te geven.  Klik in het menu op **VM herstellen**.

    ![VM-opdracht herstellen](./media/oracle-backup-recovery/recover-vm-03.png)

6.  Kies op de Blade **virtuele machine herstellen** de optie **nieuwe maken** en **nieuwe virtuele machine maken**. Voer de naam van de virtuele machine **vmoracle19c** in en kies het VNet- **vmoracle19cVNET**. het subnet wordt automatisch ingevuld op basis van uw VNet-selectie. Het proces voor het herstellen van een virtuele machine vereist een Azure-opslag account in dezelfde resource groep en regio. U kunt het opslag account kiezen **orarestore** u eerder hebt ingesteld.

    ![Configuratie waarden herstellen](./media/oracle-backup-recovery/recover-vm-04.png)

7.  Als u de virtuele machine wilt herstellen, klikt u op de knop **herstellen** .

8.  Als u de status van het herstel proces wilt weer geven, klikt u op **taken** en vervolgens op **back-uptaken**.

    ![Opdracht status van back-uptaken](./media/oracle-backup-recovery/recover-vm-05.png)

    Klik op de bewerking **bezig** met terugzetten om de status van het herstel proces weer te geven:

    ![Status van het herstel proces](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u uw opslag account en bestands share wilt instellen, voert u de volgende opdrachten uit in azure CLI.

1. Maak het opslag account in dezelfde resource groep en locatie als uw VM:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Haal de lijst met beschik bare herstel punten op. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. Herstel het herstel punt naar het opslag account. Vervang door `<myRecoveryPointName>` een herstel punt uit de lijst die in de vorige stap is gegenereerd:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. Haal de details van de herstel taak op. Met de volgende opdracht worden meer details opgehaald voor de geactiveerde herstelde taak, met inbegrip van de naam, die nodig is om de sjabloon-URI op te halen. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   De uitvoer ziet er ongeveer als `(Note down the name of the restore job)` volgt uit:

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. Haal de details op van de URI die moet worden gebruikt voor het opnieuw maken van de virtuele machine. Vervang de naam van de restore-taak uit de vorige stap voor `<RestoreJobName>` .

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   De uitvoer ziet er ongeveer als volgt uit:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   De naam van de sjabloon, die zich aan het einde van de URL van de sjabloon-blob, in dit voor beeld bevindt, `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` en de naam van de BLOB-container, die wordt `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` weer gegeven. 

   Gebruik deze waarden in de volgende opdracht om variabelen toe te wijzen in de voor bereiding voor het maken van de virtuele machine. Er wordt een SAS-sleutel voor de opslag container gegenereerd met een duur van 30 minuten.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Implementeer nu de sjabloon om de virtuele machine te maken.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   U wordt gevraagd een naam voor de virtuele machine op te geven.

---

### <a name="set-the-public-ip-address"></a>Het open bare IP-adres instellen

Nadat de virtuele machine is hersteld, moet u het oorspronkelijke IP-adres aan de nieuwe virtuele machine toewijzen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Ga in het Azure Portal naar de **vmoracle19c** van de virtuele machine. U ziet dat er een nieuw openbaar IP-adres en een NIC zijn toegewezen vergelijkbaar met vmoracle19c-NIC-XXXXXXXXXXXX, maar heeft geen DNS-adressen. Wanneer de oorspronkelijke virtuele machine is verwijderd, wordt het open bare IP-adres en de NIC bewaard en worden de volgende stappen opnieuw gekoppeld aan de nieuwe virtuele machine.

    ![Lijst met open bare IP-adressen](./media/oracle-backup-recovery/create-ip-01.png)

2.  De virtuele machine stoppen

    ![IP-adres maken](./media/oracle-backup-recovery/create-ip-02.png)

3.  Ga naar **netwerken**

    ![IP-adres koppelen](./media/oracle-backup-recovery/create-ip-03.png)

4.  Klik op **netwerk interface koppelen**, kies de oorspronkelijke NIC * * vmoracle19cVMNic, waar het oorspronkelijke open bare IP-adres nog steeds aan is gekoppeld en klik op **OK**

    ![Resource type en NIC-waarden selecteren](./media/oracle-backup-recovery/create-ip-04.png)

5.  Nu moet u de NIC die is gemaakt met de bewerking voor het terugzetten van de VM, loskoppelen omdat deze is geconfigureerd als de primaire interface. Klik op **netwerk interface ontkoppelen** en kies de nieuwe NIC vergelijkbaar met **vmoracle19c-NIC-XXXXXXXXXXXX** en klik vervolgens op **OK** .

    ![Waarde van IP-adres](./media/oracle-backup-recovery/create-ip-05.png)
    
    De opnieuw gemaakte VM heeft nu de oorspronkelijke NIC, die is gekoppeld aan het oorspronkelijke IP-adres en de regels voor de netwerk beveiligings groep
    
    ![Waarde van IP-adres](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  Ga terug naar het **overzicht** en klik op **Start** . 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. De virtuele machine stoppen en de toewijzing ongedaan maken:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. De huidige weer geven, gegenereerde VM-NIC herstellen

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   De uitvoer ziet er ongeveer als volgt uit, waarin de naam van de gegenereerde NIC wordt weer gegeven als `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. Koppel de oorspronkelijke NIC, die in dit geval een naam moet hebben `<VMName>VMNic` `vmoracle19cVMNic` . Het oorspronkelijke open bare IP-adres is nog steeds gekoppeld aan deze NIC en wordt teruggezet naar de virtuele machine wanneer de NIC opnieuw wordt gekoppeld. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. De door herstel gegenereerde NIC ontkoppelen

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. Start de virtuele machine:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Gebruik het volgende script om verbinding te maken met de virtuele machine:

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>De data base starten om het stadium te koppelen en herstel uit te voeren

1. Mogelijk ziet u dat het exemplaar wordt uitgevoerd, omdat er een poging is gedaan om de data base te starten op het opstarten van de VM. De data base vereist echter herstel en is waarschijnlijk alleen in de koppel fase, zodat de voorbereidende afsluiting eerst wordt uitgevoerd.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Controleer of de inhoud van de data base is hersteld:

    ```bash
    SQL> select * from scott.scott_table;
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
