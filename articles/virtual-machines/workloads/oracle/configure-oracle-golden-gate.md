---
title: Oracle Golden-Gate implementeren op een Azure Linux-VM | Microsoft Docs
description: U kunt snel een Oracle Golden-Gate maken en uitvoeren in uw Azure-omgeving.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: fee6770108cbcc2334b2d8fb3ccc3b1e923772d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673244"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Oracle Golden-Gate implementeren op een Azure Linux-VM 

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. Deze hand leiding bevat informatie over het gebruik van de Azure CLI voor het implementeren van een Oracle 12c-data base vanuit de galerie met Azure Marketplace-installatie kopieën. 

Dit document bevat stapsgewijze instructies voor het maken, installeren en configureren van Oracle Golden-Gate op een virtuele machine van Azure. In deze zelf studie worden twee virtuele machines ingesteld in een beschikbaarheidsset in één regio. Dezelfde zelf studie kan worden gebruikt voor het instellen van OracleGolden-Gate voor virtuele machines in verschillende Beschikbaarheidszones in één Azure-regio of voor het instellen van Vm's in twee verschillende regio's.

Voordat u begint, moet u controleren of de Azure-CLI is geïnstalleerd. Zie voor meer informatie de [Installatiehandleiding van de Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>De omgeving voorbereiden

Als u de Oracle Golden-Gate-installatie wilt uitvoeren, moet u twee virtuele Azure-machines maken op dezelfde beschikbaarheidsset. De Marketplace-installatie kopie die u gebruikt om de Vm's te maken, is **Oracle: Oracle-data base-ee: 12.1.0.2: Latest**.

U moet ook bekend zijn met UNIX editor VI en een basis kennis hebben van X11 (X Windows).

Hier volgt een samen vatting van de omgevings configuratie:
> 
> |  | **Primaire site** | **Site repliceren** |
> | --- | --- | --- |
> | **Oracle-release** |Oracle 12c release 2 – (12.1.0.2) |Oracle 12c release 2 – (12.1.0.2)|
> | **Computer naam** |myVM1 |myVM2 |
> | **Besturingssysteem** |Oracle Linux 6. x |Oracle Linux 6. x |
> | **Oracle-SID** |CDB1 |CDB1 |
> | **Replicatie schema** |TEST|TEST |
> | **Gouden poort eigenaar/repliceren** |C##GGADMIN |REPUSER |
> | **Golden-poort proces** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Aanmelden bij Azure 

Meld u aan bij uw Azure-abonnement met de opdracht [AZ login](/cli/azure/reference-index) . Volg vervolgens de instructies op het scherm.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resource groep is een logische container waarin Azure-resources worden geïmplementeerd en van waaruit ze kunnen worden beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken

De volgende stap is optioneel, maar wordt aanbevolen. Zie de [hand leiding voor Azure-beschikbaarheids sets](/previous-versions/azure/virtual-machines/windows/infrastructure-example)voor meer informatie.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een VM met de opdracht [az vm create](/cli/azure/vm). 

In het volgende voor beeld worden twee virtuele machines gemaakt met de naam `myVM1` en `myVM2` . Maak SSH-sleutels als deze nog niet bestaan op een standaard sleutel locatie. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>MyVM1 maken (primair):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Nadat de VM is gemaakt, toont de Azure CLI informatie die lijkt op het volgende voor beeld. (Noteer de `publicIpAddress` . Dit adres wordt gebruikt voor toegang tot de virtuele machine.)

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>MyVM2 maken (repliceren):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Noteer de `publicIpAddress` maar ook nadat deze is gemaakt.

### <a name="open-the-tcp-port-for-connectivity"></a>Open de TCP-poort voor connectiviteit

De volgende stap is het configureren van externe eind punten, waarmee u op afstand toegang kunt krijgen tot de Oracle-data base. Voer de volgende opdrachten uit om de externe eind punten te configureren.

#### <a name="open-the-port-for-myvm1"></a>Open de poort voor myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

De resultaten moeten er ongeveer uitzien als in het volgende antwoord:

```output
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>Open de poort voor myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Verbinding maken met de virtuele machine

Gebruik de volgende opdracht om een SSH-sessie te starten voor de virtuele machine. Vervang het IP-adres door het `publicIpAddress` van de virtuele machine.

```bash
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>De Data Base op myVM1 maken (primair)

De Oracle-software is al geïnstalleerd op de Marketplace-installatie kopie, dus de volgende stap is het installeren van de data base. 

Voer de software uit als de ' Oracle '-beheerder:

```bash
sudo su - oracle
```

Maak de database:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

De uitvoer moet er ongeveer uitzien als het volgende antwoord:

```output
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Stel de ORACLE_SID-en ORACLE_HOME-variabelen in.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Desgewenst kunt u ORACLE_HOME en ORACLE_SID toevoegen aan het. bashrc-bestand, zodat deze instellingen worden opgeslagen voor toekomstige aanmeldingen:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle-listener starten

```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>De data base maken op myVM2 (repliceren)

```bash
sudo su - oracle
```

Maak de database:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

Stel de ORACLE_SID-en ORACLE_HOME-variabelen in.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Desgewenst kunt u ORACLE_HOME en ORACLE_SID toevoegen aan het. bashrc-bestand, zodat deze instellingen worden opgeslagen voor toekomstige aanmeldingen.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle-listener starten

```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Gouden poort configureren 
Volg de stappen in deze sectie voor het configureren van een gouden poort.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Logboek modus voor archiveren op myVM1 inschakelen (primair)

```bash
$ sqlplus / as sysdba
SQL> SELECT log_mode FROM v$database;

LOG_MODE
------------
NOARCHIVELOG

SQL> SHUTDOWN IMMEDIATE;
SQL> STARTUP MOUNT;
SQL> ALTER DATABASE ARCHIVELOG;
SQL> ALTER DATABASE OPEN;
```
Schakel logboek registratie in en zorg ervoor dat er ten minste één logboek bestand aanwezig is.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Gouden poort software downloaden
Voer de volgende stappen uit om de Oracle Golden-Gate-software te downloaden en voor te bereiden:

1. Down load het **fbo_ggs_Linux_x64_shiphome.zip** -bestand van de [Oracle Golden Gate-download pagina](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). Onder de Download titel **Oracle Golden Gate 12. x. x. x voor Oracle Linux x86-64** moet er een set zip-bestanden worden gedownload.

2. Nadat u de zip-bestanden naar uw client computer hebt gedownload, gebruikt u het Secure Copy Protocol (SCP) om de bestanden te kopiëren naar uw VM:

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Verplaats de zip-bestanden naar de map **/opt** . Wijzig de eigenaar van de bestanden als volgt:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Pak de bestanden uit (Installeer het hulp programma voor Linux-unzip) als dit nog niet is gebeurd):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Machtiging wijzigen:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>De client en VM voorbereiden voor het uitvoeren van X11 (alleen voor Windows-clients)
Dit is een optionele taak. U kunt deze stap overs Laan als u een Linux-client gebruikt of als u al een X11-installatie hebt.

1. PuTTy-en Xming downloaden naar uw Windows-computer:

   * [PuTTy downloaden](https://www.putty.org/)
   * [Xming downloaden](https://xming.en.softonic.com/)

2. Nadat u PuTTy hebt geïnstalleerd, voert u in de map PuTTy (bijvoorbeeld C:\Program Files\PuTTY) puttygen.exe uit (sleutel Generator van PuTTy).

3. In PuTTy-sleutel Generator:

   - Selecteer de knop **genereren** om een sleutel te genereren.
   - Kopieer de inhoud van de sleutel (**CTRL + C**).
   - Selecteer de knop **persoonlijke sleutel opslaan** .
   - Negeer de waarschuwing die wordt weer gegeven en selecteer **OK**.

   ![Scherm afbeelding van de pagina PuTTy-sleutel generator](./media/oracle-golden-gate/puttykeygen.png)

4. Voer de volgende opdrachten uit in uw virtuele machine:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Maak een bestand met de naam **authorized_keys**. Plak de inhoud van de sleutel in dit bestand en sla het bestand op.

   > [!NOTE]
   > De sleutel moet de teken reeks bevatten `ssh-rsa` . De inhoud van de sleutel moet ook één tekst regel zijn.
   >  

6. Start PuTTY. Selecteer in het deel venster **categorie** de optie **verbinding**  >  **SSH**  >  **auth**. Blader in het vak **persoonlijk sleutel bestand voor verificatie** naar de sleutel die u eerder hebt gegenereerd.

   ![Scherm afbeelding van de pagina persoonlijke sleutel instellen](./media/oracle-golden-gate/setprivatekey.png)

7. Selecteer in het deel venster **categorie** de optie **verbinding**  >  **SSH**-  >  **X11**. Schakel vervolgens het **doorstuur selectie vakje X11 inschakelen** in.

   ![Scherm afbeelding van de pagina X11 inschakelen](./media/oracle-golden-gate/enablex11.png)

8. Ga in het deel venster **categorie** naar **sessie**. Voer de gegevens van de host in en selecteer vervolgens **openen**.

   ![Scherm afbeelding van de sessie pagina](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Gouden poort software installeren

Voer de volgende stappen uit om Oracle Golden Gate te installeren:

1. Meld u aan als Oracle. (U moet zich kunnen aanmelden zonder dat u wordt gevraagd om een wacht woord op te vragen.) Zorg ervoor dat Xming wordt uitgevoerd voordat u begint met de installatie.

   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```

2. Selecteer Oracle Golden Gate for Oracle Database 12c. Selecteer **volgende** om door te gaan.

   ![Scherm afbeelding van het installatie programma installatie pagina selecteren](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Wijzig de locatie van de software. Selecteer vervolgens het vak **Start Manager** en voer de locatie van de data base in. Selecteer **Volgende** om door te gaan.

   ![Scherm afbeelding van de installatie pagina selecteren](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Wijzig de map Inventory en selecteer **volgende** om door te gaan.

   ![Scherm afbeelding van de installatie pagina selecteren waarop de installatie directory wordt weer gegeven.](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Selecteer in het scherm **samen vatting** de optie **installeren** om door te gaan.

   ![Scherm afbeelding met de installatie pagina selecteren en de knop installeren.](./media/oracle-golden-gate/golden_gate_install_04.png)

6. U wordt mogelijk gevraagd om een script uit te voeren als root. Als dit het geval is, opent u een afzonderlijke sessie, SSH naar de virtuele machine, sudo naar root en voert u het script uit. Selecteer **OK** door gaan.

   ![Scherm opname van de script locatie en hoe het configuratie script moet worden uitgevoerd.](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Wanneer de installatie is voltooid, selecteert u **sluiten** om het proces te volt ooien.

   ![Scherm afbeelding van de installatie pagina selecteren waarop de knop Sluiten wordt weer gegeven.](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Service instellen op myVM1 (primair)

1. Maak het bestand Tnsnames. ora-bestand of werk het bij:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Maak de gouden poort-eigenaar en gebruikers accounts.

   > [!NOTE]
   > Het eigenaars account moet C# # prefix hebben.
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Het gouden poort test gebruikers account maken:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. Configureer het uitpak parameter bestand.

   Start de gouden poort-opdracht regel interface (ggsci):

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```

5. Voeg het volgende toe aan het UITPAK-parameter bestand (met behulp van VI-opdrachten). Druk op ESC-toets,: wq! om het bestand op te slaan. 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```

6. REGI ster ophalen--geïntegreerde extract:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```

7. Uitstel controlepunten instellen en real-time extract starten:

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```

   In deze stap vindt u het starten van SCN, dat later wordt gebruikt in een andere sectie:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>Service instellen op myVM2 (repliceren)


1. Maak het bestand Tnsnames. ora-bestand of werk het bij:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Een gerepliceerd account maken:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Een gouden poort test gebruikers account maken:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. Parameter bestand REPLICEREN om wijzigingen te repliceren: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```

   Inhoud van REPORA-parameter bestand:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. Een replica controlepunt instellen:

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>De replicatie instellen (myVM1 en myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. de replicatie op myVM2 (repliceren) instellen

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```

Werk het bestand bij met het volgende:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```

Start vervolgens de Manager-service opnieuw:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. de replicatie op myVM1 instellen (primair)

Start de eerste belasting en controleer op fouten:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```

#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. de replicatie op myVM2 (repliceren) instellen

Wijzig het SCN-nummer met het nummer dat u hebt verkregen vóór:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```

De replicatie is gestart en u kunt deze testen door nieuwe records in te voegen in tabel testen.


### <a name="view-job-status-and-troubleshooting"></a>Taak status en probleem oplossing weer geven

#### <a name="view-reports"></a>Rapporten weergeven
Voer de volgende opdrachten uit om rapporten weer te geven op myVM1:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Voer de volgende opdrachten uit om rapporten weer te geven op myVM2:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Status en geschiedenis weer geven
Als u de status en geschiedenis van myVM1 wilt weer geven, voert u de volgende opdrachten uit:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Als u de status en geschiedenis van myVM2 wilt weer geven, voert u de volgende opdrachten uit:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Hiermee voltooit u de installatie en configuratie van de gouden poort op Oracle Linux.


## <a name="delete-the-virtual-machine"></a>Verwijder de virtuele machine

Wanneer deze niet meer nodig is, kan de volgende opdracht worden gebruikt om de resource groep, de virtuele machine en alle gerelateerde resources te verwijderen.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie voor het maken van virtuele machines met een hoge beschikbaarheid](../../linux/create-cli-complete.md)

[CLI-voorbeelden voor VM-implementatie verkennen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)