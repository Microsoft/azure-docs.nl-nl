---
title: Een Oracle-database maken in een Azure-VM | Microsoft Docs
description: Richt snel een Oracle Database 12c-database in in uw Azure-omgeving.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: d16153a7dc9a3164a5127b80a474bf9c398684ac
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945135"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Een Oracle-database maken in een Azure-VM

In deze handleiding vindt u informatie over hoe u met de Azure CLI een virtuele Azure-machine kunt implementeren vanuit de [Oracle-installatiekopie uit de Marketplace-galerie](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) om een Oracle 12c-database te maken. Zodra de server is geïmplementeerd, kunt u verbinding maken via SSH om de Oracle-database te configureren. 

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Quickstart gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuele machine maken

Maak een virtuele machine (VM) met de opdracht [az vm create](/cli/azure/vm). 

In het volgende voorbeeld wordt een virtuele machine met de naam `myVM` gemaakt. Er worden ook SSH-sleutels gemaakt, indien deze niet al op een standaardsleutellocatie aanwezig zijn. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Nadat u de VM hebt gemaakt, geeft de Azure CLI informatie weer die lijkt op het volgende voorbeeld. Noteer de waarde voor `publicIpAddress`. Dit adres wordt gebruikt voor toegang tot de virtuele machine.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Gebruik de volgende opdracht om voor de VM een SSH-sessie te maken. Vervang het IP-adres door de waarde `publicIpAddress` voor uw VM.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>De database maken

De Oracle-software is al geïnstalleerd op de Marketplace-installatiekopie. Maak als volgt een voorbeelddatabase. 

1.  Ga naar de gebruiker *oracle* en start de Oracle-listener:

    ```bash
    $ sudo -su oracle
    $ lsnrctl start
    ```

    De uitvoer lijkt op het volgende:

    ```output
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```
2. Een gegevensmap maken voor de Oracle-gegevensbestanden

    ```bash
        mkdir /u01/app/oracle/oradata
    ```

3.  Maak de database:

    ```bash
    dbca -silent \
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
           -datafileDestination "/u01/app/oracle/oradata/" \
           -ignorePreReqs
    ```

    Het duurt een paar minuten om de database te maken.

    De uitvoer ziet er ongeveer als volgt uit:

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
        Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
    ```

4. Oracle-variabelen instellen

    Voordat u verbinding maakt, moet u twee omgevingsvariabelen instellen: *ORACLE_HOME* en *ORACLE_SID*.

    ```bash
        ORACLE_SID=cdb1; export ORACLE_SID
    ```

    U kunt de variabelen ORACLE_HOME en ORACLE_SID ook toevoegen aan het .bashrc-bestand. Hierdoor worden de omgevingsvariabelen opgeslagen voor toekomstige aanmeldingen. Controleer of de volgende instructies zijn toegevoegd aan het bestand `~/.bashrc` met behulp van de editor van uw keuze.

    ```bash
    # Add ORACLE_SID. 
    export ORACLE_SID=cdb1 
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

3. Open de container PDB1, als deze nog niet is geopend, maar controleer eerst de status:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    De uitvoer lijkt op het volgende:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Als de OPEN_MODE voor `PDB1` niet READ WRITE is, voert u de volgende opdrachten uit om PDB1 te openen:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

U moet `quit` typen om de sqlplus-sessie te beëindigen en `exit` typen om de Oracle-gebruiker af te melden.

## <a name="automate-database-startup-and-shutdown"></a>Databases automatisch opstarten en afsluiten

De Oracle-database wordt standaard niet automatisch gestart wanneer u de virtuele machine opnieuw opstart. Als u de Oracle-database wilt instellen om automatisch te starten, meldt u zich eerst aan als rootgebruiker. Maak en werk vervolgens enkele systeembestanden bij.

1. Aanmelden als rootgebruiker

    ```bash
    sudo su -
    ```

2.  Bewerk het bestand `/etc/oratab` met uw favoriete editor en wijzig de standaard `N` naar `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Maak een bestand met de naam `/etc/init.d/dbora` en kopieer de volgende inhoud naar het bestand:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
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

## <a name="open-ports-for-connectivity"></a>Poorten openen voor connectiviteit

De laatste taak is het configureren van sommige externe eindpunten. Als u de Azure-netwerkbeveiligingsgroep wilt instellen die de virtuele machine beveiligt, moet u eerst uw SSH-sessie in de VM sluiten (deze zou moeten zijn gesloten na het opnieuw opstarten in de vorige stap). 

1.  Als u het eindpunt dat u gebruikt voor toegang tot de Oracle-database op afstand wilt openen, moet u een regel maken voor de netwerkbeveiligingsgroep met [az network nsg rule create](/cli/azure/network/nsg/rule): 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Als u het eindpunt dat u gebruikt voor toegang tot de Oracle EM Express op afstand wilt openen, moet u een regel maken voor de netwerkbeveiligingsgroep met [az network nsg rule create](/cli/azure/network/nsg/rule):

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Haal indien nodig het openbare IP-adres van uw VM nogmaals op met [az network public-ip show](/cli/azure/network/public-ip):

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Maak verbinding met EM Express vanuit uw browser. Zorg ervoor dat uw browser compatibel is met EM Express (Flash-installatie is vereist): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

U kunt zich aanmelden met behulp van het **SYS**-account en moet hierbij het selectievakje **als sysdba** selecteren. Gebruik het wachtwoord **OraPasswd1** dat u tijdens de installatie hebt ingesteld. 

![Schermafbeelding van de Oracle OEM Express-aanmeldingspagina](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het verkennen van uw eerste Oracle-database in Azure en de virtuele machine niet meer nodig is, kunt u de opdracht [az group delete](/cli/azure/group) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Ontdek meer informatie over andere [Oracle-oplossingen op Azure](./oracle-overview.md). 

Probeer de zelfstudie voor het [installeren en configureren van geautomatiseerd opslagbeheer van Oracle](configure-oracle-asm.md).
