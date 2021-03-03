---
title: Een Oracle-database maken in een Azure-VM | Microsoft Docs
description: Richt snel een Oracle Database 12c-database in in uw Azure-omgeving.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: ec6a8382e2c0ce2cb359a62dd3f80fc977c4b1c2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674654"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Een Oracle-database maken in een Azure-VM

In deze hand leiding vindt u informatie over het gebruik van de Azure CLI om een virtuele Azure-machine te implementeren vanuit de [Galerie met Oracle Marketplace-afbeeldingen](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) om een Oracle 19c-data base te maken. Zodra de server is geïmplementeerd, kunt u verbinding maken via SSH om de Oracle-database te configureren. 

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Quickstart gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voor beeld wordt een resource groep met de naam *RG-Oracle* gemaakt op de locatie *eastus* .

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>Virtuele machine maken

Maak een virtuele machine (VM) met de opdracht [az vm create](/cli/azure/vm). 

In het volgende voorbeeld wordt een virtuele machine met de naam `vmoracle19c` gemaakt. Er worden ook SSH-sleutels gemaakt, indien deze niet al op een standaardsleutellocatie aanwezig zijn. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

Nadat u de VM hebt gemaakt, geeft de Azure CLI informatie weer die lijkt op het volgende voorbeeld. Noteer de waarde voor `publicIpAddress`. Dit adres wordt gebruikt voor toegang tot de virtuele machine.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Een nieuwe schijf maken en koppelen voor Oracle datafiles en FRA

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>Poorten openen voor connectiviteit
In deze taak moet u een aantal externe eind punten voor de data base-listener en EM Express configureren voor gebruik door de Azure-netwerk beveiligings groep in te stellen die de virtuele machine beveiligt. 

1. Als u het eind punt dat u gebruikt voor toegang tot de Oracle-Data Base op afstand wilt openen, maakt u als volgt een regel voor de netwerk beveiligings groep:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Als u het eindpunt dat u gebruikt voor toegang tot de Oracle EM Express op afstand wilt openen, moet u een regel maken voor de netwerkbeveiligingsgroep met az network nsg rule create:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. Haal indien nodig het openbare IP-adres van uw VM nogmaals op met az network public-ip show:

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>De VM-omgeving voorbereiden

1. Verbinding maken met de virtuele machine

   Gebruik de volgende opdracht om voor de VM een SSH-sessie te maken. Vervang het IP-adres door de waarde `publicIpAddress` voor uw VM.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. Overschakelen naar de hoofd gebruiker

   ```bash
   sudo su -
   ```

3. Controleren op het laatst gemaakte schijf apparaat dat wij gaan Format teren voor gebruik van Oracle datafiles

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   De uitvoer ziet er ongeveer als volgt uit:
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. Het apparaat Format teren. 
   Als hoofd gebruiker wordt op het apparaat uitgevoerd 
   
   Maak eerst een schijf label:
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   Maak vervolgens een primaire partitie die de hele schijf overspant:
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   Controleer de details van het apparaat ten slotte door de meta gegevens af te drukken:
   ```bash
   parted /dev/sdc print
   ```
   De uitvoer moet er ongeveer als volgt uitzien:
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. Een bestands systeem maken op de partitie van het apparaat

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. Een koppel punt maken
   ```bash
   mkdir /u02
   ```

7. De schijf koppelen

   ```bash
   mount /dev/sdc1 /u02
   ```

8. Machtigingen op het koppel punt wijzigen

   ```bash
   chmod 777 /u02
   ```

9. Voeg de koppeling toe aan het bestand/etc/fstab-bestand. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. Werk het ***bestand/etc/hosts*** -bestand bij met het open bare IP-adres en de hostnaam.

    Wijzig het ***open bare IP-adres en de VMname*** om uw werkelijke waarden weer te geven:
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. Het hostname-bestand bijwerken
    
    Gebruik de volgende opdracht om de domein naam van de virtuele machine toe te voegen aan het **/etc/hostname** -bestand. Hierbij wordt ervan uitgegaan dat u de resource groep en VM in de regio **oostus** hebt gemaakt:
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. Firewall poorten openen
    
    Als SELinux is standaard ingeschakeld op de Marketplace-installatie kopie, moet de firewall worden geopend voor verkeer voor de data base luisteren poort 1521 en Enter prise Manager Express-poort 5502. Voer de volgende opdrachten uit als hoofd gebruiker:

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>De database maken

De Oracle-software is al geïnstalleerd op de Marketplace-installatiekopie. Maak als volgt een voorbeelddatabase. 

1.  Schakel over naar de **Oracle** -gebruiker:

    ```bash
    $ sudo su - oracle
    ```
2. De data base-listener starten

   ```bash
   $ lsnrctl start
   ```
   De uitvoer lijkt op het volgende:
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Een gegevensdirectory maken voor de Oracle-gegevens bestanden:

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Voer de assistent voor het maken van de data base uit:

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    Het duurt een paar minuten om de database te maken.

    De uitvoer ziet er ongeveer als volgt uit:

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Oracle-variabelen instellen

    Voordat u verbinding maakt, moet u de omgevings variabele *ORACLE_SID* instellen:

    ```bash
        export ORACLE_SID=test
    ```

    U moet de variabele ORACLE_SID ook toevoegen aan het `oracle` gebruikers `.bashrc` bestand voor toekomstige aanmeldingen met behulp van de volgende opdracht:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express-connectiviteit

Als GUI-beheerprogramma dat u kunt gebruiken om de database te verkennen, stelt u Oracle EM Express in. Als u verbinding wilt maken met Oracle EM Express, moet u hiervoor eerst de poort instellen in Oracle. 

1. Maak verbinding met uw database met behulp van sqlplus:

    ```bash
    sqlplus sys as sysdba
    ```

2. Nadat de verbinding is gemaakt, stelt u poort 5502 in voor EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  Maak verbinding met EM Express vanuit uw browser. Zorg ervoor dat uw browser compatibel is met EM Express (Flash-installatie is vereist): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    U kunt zich aanmelden met behulp van het **SYS**-account en moet hierbij het selectievakje **als sysdba** selecteren. Gebruik het wachtwoord **OraPasswd1** dat u tijdens de installatie hebt ingesteld. 

    ![Schermafbeelding van de Oracle OEM Express-aanmeldingspagina](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>Databases automatisch opstarten en afsluiten

De Oracle-database wordt standaard niet automatisch gestart wanneer u de virtuele machine opnieuw opstart. Als u de Oracle-database wilt instellen om automatisch te starten, meldt u zich eerst aan als rootgebruiker. Maak en werk vervolgens enkele systeembestanden bij.

1. Aanmelden als rootgebruiker

    ```bash
    sudo su -
    ```

2.  Voer de volgende opdracht uit om de vlag voor automatisch opstarten te wijzigen van `N` `Y` in in het `/etc/oratab` bestand:

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  Maak een bestand met de naam `/etc/init.d/dbora` en kopieer de volgende inhoud naar het bestand:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Wijzig de machtigingen voor bestanden met *chmod*, als volgt:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Maak als volgt symbolische koppelingen voor opstarten en afsluiten:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Als u uw wijzigingen wilt testen, start u de VM opnieuw op:

    ```bash
    reboot
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het verkennen van uw eerste Oracle-database in Azure en de virtuele machine niet meer nodig is, kunt u de opdracht [az group delete](/cli/azure/group) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beveiligen van uw data base in azure met [Oracle-back-Upstrategieen](./oracle-database-backup-strategies.md)

Ontdek meer informatie over andere [Oracle-oplossingen op Azure](./oracle-overview.md). 

Probeer de zelfstudie voor het [installeren en configureren van geautomatiseerd opslagbeheer van Oracle](configure-oracle-asm.md).
