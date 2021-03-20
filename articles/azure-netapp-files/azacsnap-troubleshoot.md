---
title: Problemen met het hulp programma Azure-toepassing consistente moment opname oplossen voor Azure NetApp Files | Microsoft Docs
description: Biedt informatie over het oplossen van problemen met behulp van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 903cb3323b9441ec8bb382054f065760875e3e89
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632686"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>Problemen met het hulp programma Azure-toepassing consistente moment opname oplossen (preview)

In dit artikel vindt u informatie over het oplossen van problemen met behulp van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.

Hier volgen enkele veelvoorkomende problemen die kunnen optreden tijdens het uitvoeren van de opdrachten. Volg de oplossings instructies die worden vermeld om het probleem op te lossen. Als u nog steeds een probleem ondervindt, opent u een service aanvraag van Azure Portal en wijst u de aanvraag toe aan de SAP HANA grote instantie wachtrij voor Microsoft Ondersteuning om te reageren.

## <a name="log-files"></a>Logboekbestanden

Een van de beste informatie bronnen voor het opsporen van fouten met AzAcSnap zijn de logboek bestanden.  

### <a name="log-file-location"></a>Locatie van logboek bestand

De logboek bestanden worden opgeslagen in de map die is geconfigureerd volgens de `logPath` para meter in het configuratie bestand AzAcSnap.  De standaard naam van de configuratie is `azacsnap.json` en de standaard waarde voor `logPath` is `"./logs"` dat de logboek bestanden naar de map worden geschreven `./logs` ten opzichte van waar de `azacsnap` opdracht wordt uitgevoerd.  Het maken van `logPath` een absolute locatie (bijvoorbeeld `/home/azacsnap/logs` ) zorgt ervoor dat `azacsnap` de logboeken worden uitgevoerd in `/home/azacsnap/logs` plaats van waar de `azacsnap` opdracht is uitgevoerd.

### <a name="log-file-naming"></a>Naamgeving van logboek bestanden

De naam van het logboek bestand is gebaseerd op de naam van de toepassing (bijvoorbeeld `azacsnap` ), de opdracht optie ( `-c` ) gebruikt (bijvoorbeeld,,, `backup` `test` `details` enzovoort) en de configuratie bestandsnaam (bijvoorbeeld standaard = `azacsnap.json` ).  Als u de `-c backup` opdracht gebruikt, wordt de logboek bestandsnaam standaard ingesteld op `azacsnap-backup-azacsnap.log` en wordt deze geschreven in de map die is geconfigureerd door `logPath` .  

Deze naamgevings Conventie is tot stand gebracht voor meerdere configuratie bestanden, één per data base, en zorgt voor een gemakkelijke manier om de bijbehorende logboeken te vinden.  Als de configuratie bestandsnaam is, is `SID.json` de bestands naam van het resultaat als u de `azacsnap -c backup --configfile SID.json` opties gebruikt `azacsnap-backup-SID.log` .

### <a name="result-file-and-syslog"></a>Resultaat bestand en syslog

Voor de `-c backup` opdracht optie AzAcSnap schrijft u naar een `*.result` bestand en het systeem logboek ( `/var/log/messages` ) met behulp van de `logger` opdracht.  De `*.result` Bestands naam heeft dezelfde basis naam als het [logboek bestand](#log-file-naming) en gaat naar dezelfde [locatie als het logboek bestand](#log-file-location).  Het is een eenvoudig één regel uitvoer bestand volgens de volgende voor beelden.

Voorbeeld uitvoer van een `*.result` bestand.
```output
Database # 1 (PR1) : completed ok
```

Voorbeeld uitvoer van een `/var/log/messages` bestand.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-sap-hana"></a>Communicatie met SAP HANA is mislukt

Bij het valideren van de communicatie met SAP HANA door een test uit `azacsnap -c test --test hana` te voeren, wordt de volgende fout weer geboden:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Oplossing:**

1. Controleer het configuratie bestand (bijvoorbeeld `azacsnap.json` ) voor elk Hana-exemplaar om ervoor te zorgen dat de SAP Hana database waarden juist zijn.
1. Voer de onderstaande opdracht uit om te controleren of de `hdbsql` opdracht zich in het pad bevindt en verbinding kan maken met de SAP Hana server. In het volgende voor beeld ziet u de juiste uitvoering van de opdracht en de uitvoer ervan.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    In dit voor beeld `hdbsql` bevindt de opdracht zich niet in de gebruikers `$PATH` .

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    In dit voor beeld `hdbsql` wordt de opdracht tijdelijk toegevoegd aan de gebruiker `$PATH` , maar wanneer het uitvoeren wordt weer gegeven, is de verbindings sleutel niet correct ingesteld met de `hdbuserstore Set` opdracht (zie aan de slag-hand leiding voor meer informatie):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > Als u permanent wilt toevoegen aan de gebruiker `$PATH` , werkt u het bestand van de gebruiker bij `$HOME/.profile`

## <a name="the-hdbuserstore-location"></a>De `hdbuserstore` locatie

Wanneer u communicatie met SAP HANA instelt, `hdbuserstore` wordt het programma gebruikt voor het maken van de instellingen voor beveiligde communicatie.  Het `hdbuserstore` programma wordt meestal gevonden onder `/usr/sap/<SID>/SYS/exe/hdb/` of `/usr/sap/hdbclient` .  Normaal gesp roken voegt het installatie programma de juiste locatie toe aan de `azacsnap` gebruiker `$PATH` .

## <a name="failed-test-with-storage"></a>Kan niet testen met opslag

De opdracht `azacsnap -c test --test storage` wordt niet voltooid.

### <a name="azure-large-instance"></a>Azure large-exemplaar

Het volgende voor beeld wordt uitgevoerd `azacsnap` op SAP Hana van een grote Azure-instantie:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Oplossing:** De bovenstaande fout wordt normaal gesp roken weer gegeven wanneer de gebruiker van een grote instantie van Azure-opslag geen toegang heeft tot de onderliggende opslag. Als u de toegang tot opslag met de gegeven opslag gebruiker wilt valideren, voert `ssh` u de opdracht uit om de communicatie met het opslag platform te valideren.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Een voor beeld met een verwachte uitvoer:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>De authenticiteit van de host 172.18.18.11 (172.18.18.11) kan niet tot stand worden gebracht

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Oplossing:** Selecteer niet ja. Controleer of het IP-adres van uw opslag juist is. Als er nog steeds een probleem is, controleert u het IP-adres van de opslag met het micro soft Operations-team.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Het volgende voor beeld is het uitvoeren `azacsnap` van een virtuele machine met Azure NetApp files:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Oplossing:**

1. Controleer of het Service-Principal-bestand aanwezig is, `azureauth.json` zoals ingesteld in het `azacsnap.json` configuratie bestand.
1. Controleer het logboek bestand (bijvoorbeeld `logs/azacsnap-test-azacsnap.log` ) om te zien of de Service-Principal ( `azureauth.json` ) de juiste inhoud heeft.  Voor beeld van logboek op de volgende manier:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Controleer het logboek bestand (bijvoorbeeld `logs/azacsnap-test-azacsnap.log` ) om te zien of de Service-Principal ( `azureauth.json` ) is verlopen. Voor beeld van logboek op de volgende manier:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Volgende stappen

- [Tips](azacsnap-tips.md)
