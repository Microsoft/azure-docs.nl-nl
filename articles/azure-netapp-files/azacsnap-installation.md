---
title: Installeer het hulp programma Azure-toepassing consistente moment opname voor Azure NetApp Files | Microsoft Docs
description: In dit onderwerp vindt u een hand leiding voor de installatie van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 458f4d3f29cb08a94095167ed45133f5cd70f5f4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869188"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Azure-toepassing consistent snap shot tool installeren (preview-versie)

Dit artikel bevat een hand leiding voor het installeren van het Azure-toepassing consistente momentopname hulpmiddel dat u kunt gebruiken met Azure NetApp Files.

## <a name="introduction"></a>Introductie

Het Download bare zelf-installatie programma is ontworpen om de snap shot-hulpprogram ma's eenvoudig in te stellen en uit te voeren met niet-hoofd gebruikers bevoegdheden (bijvoorbeeld azacsnap). Het installatie programma stelt de gebruiker in en plaatst de momentopname hulpprogramma's in de `$HOME/bin` submap gebruikers (standaard = `/home/azacsnap/bin` ).
Het zelf-installatie programma probeert de juiste instellingen en paden te bepalen voor alle bestanden op basis van de configuratie van de gebruiker die de installatie uitvoert (bijvoorbeeld root). Als de vereiste stappen (communicatie met opslag en SAP HANA inschakelen) zijn uitgevoerd als root, worden de persoonlijke sleutel en de `hdbuserstore` locatie van de back-upgebruiker gekopieerd met de installatie. Het is echter wel mogelijk om de stappen uit te voeren die communicatie mogelijk maken met de back-end van de opslag en SAP HANA hand matig moet worden uitgevoerd door een beschik bare beheerder na de installatie.

## <a name="prerequisites-for-installation"></a>Vereisten voor installatie

Volg de richt lijnen voor het instellen en uitvoeren van de moment opnamen en herstel opdrachten voor nood gevallen. U kunt het beste de volgende stappen uitvoeren als root voordat u de momentopname hulpprogramma's installeert en gebruikt.

1. Voor het **besturings systeem is een patch** uitgevoerd: Zie patches en SMT Setup in [How to install and Configure SAP Hana (grote exemplaren) in azure](../virtual-machines/workloads/sap/hana-installation.md#operating-system).
1. **Tijd synchronisatie is ingesteld**. De klant dient een NTP-compatibele tijd server op te geven en het besturings systeem dienovereenkomstig te configureren.
1. **Hana is geïnstalleerd** : Zie Hana-installatie-instructies in de [installatie van SAP netweave op Hana data base](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database).
1. **[Communicatie met opslag inschakelen](#enable-communication-with-storage)** (zie een afzonderlijke sectie voor meer informatie): de klant moet ssh met een privé/openbaar sleutel paar instellen en de open bare sleutel opgeven voor elk knoop punt waar de moment opname-hulpprogram ma's worden gepland om te worden uitgevoerd op micro soft-bewerkingen voor het instellen van de back-end van de opslag.
   1. **Voor Azure NetApp files (zie een afzonderlijke sectie voor meer informatie)**: de klant moet het Service-Principal-verificatie bestand genereren.
   1. **Voor een groot Azure-exemplaar (zie een afzonderlijke sectie voor meer informatie)**: de klant moet ssh instellen met een privé/openbaar sleutel paar en de open bare sleutel opgeven voor elk knoop punt waar de moment opname-hulpprogram ma's zijn gepland om te worden uitgevoerd op micro soft-bewerkingen voor het instellen van de back-end van de opslag.

      U kunt dit testen met behulp van SSH om verbinding te maken met een van de knoop punten (bijvoorbeeld `ssh -l <Storage UserName> <Storage IP Address>` ).
      Typ `exit` om de opslag prompt af te melden.

      Micro soft-bewerkingen bieden de opslag gebruiker en het opslag-IP-adres op het moment van inrichten.
  
1. **[Communicatie met SAP Hana inschakelen](#enable-communication-with-sap-hana)** (zie een afzonderlijke sectie voor meer informatie): de klant moet een geschikte SAP Hana gebruiker met de vereiste bevoegdheden instellen om de moment opname uit te voeren.
   1. Deze instelling kan vanaf de opdracht regel als volgt worden getest met behulp van de tekst in `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - De bovenstaande voor beelden zijn voor niet-SSL-communicatie met SAP HANA.

## <a name="enable-communication-with-storage"></a>Communicatie met opslag inschakelen

In deze sectie wordt uitgelegd hoe u communicatie met opslag inschakelt.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Een RBAC-service-principal maken

1. Zorg er in een Azure Cloud Shell-sessie voor dat u bent aangemeld bij het abonnement waar u standaard aan de Service-Principal wilt koppelen:

    ```azurecli-interactive
    az account show
    ```

1. Als het abonnement niet juist is, gebruikt u

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Een service-principal maken met behulp van Azure CLI volgens het volgende voor beeld

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. Hiermee wordt een uitvoer gegenereerd zoals in het volgende voor beeld:

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > Met deze opdracht wordt automatisch een rol van RBAC-Inzender toegewezen aan de Service-Principal op abonnements niveau. u kunt het bereik beperken tot de specifieke resource groep waar uw tests de resources gaan maken.

1. Knip en plak de uitvoer inhoud in een bestand met de naam `azureauth.json` opgeslagen op hetzelfde systeem als de `azacsnap` opdracht en Beveilig het bestand met de juiste systeem machtigingen.

### <a name="azure-large-instance"></a>Azure large-exemplaar

Communicatie met de back-end van de opslag voert een versleuteld SSH-kanaal uit. In de volgende voorbeeld stappen vindt u richt lijnen voor het instellen van SSH voor deze communicatie.

1. Het `/etc/ssh/ssh_config` bestand wijzigen

    Raadpleeg de volgende uitvoer waar de `MACs hmac-sha1` regel is toegevoegd:

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Een persoonlijk/openbaar sleutel paar maken

    Met de volgende voorbeeld opdracht voor het genereren van het sleutel paar voert u geen wacht woord in bij het genereren van een sleutel.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. De open bare sleutel naar micro soft-bewerkingen verzenden

    De uitvoer van de `cat /root/.ssh/id_rsa.pub` opdracht (voor beeld hieronder) verzenden naar micro soft-bewerkingen zodat de momentopname hulpprogramma's kunnen communiceren met het opslag subsysteem.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>Communicatie met SAP HANA inschakelen

De momentopname hulpprogramma's communiceren met SAP HANA en hebben een gebruiker met de juiste machtigingen nodig om het opslag punt van de data base te initiëren en vrij te geven. In het volgende voor beeld ziet u de instellingen van de SAP HANA v2-gebruiker en de `hdbuserstore` voor communicatie met de SAP Hana-data base.

Met de volgende voorbeeld opdrachten wordt een gebruiker ingesteld (AZACSNAP) in de SYSTEMDB op SAP HANA 2.
Wijzig het IP-adres, de gebruikers naam en het wacht woord, indien van toepassing:

1. Maak verbinding met de SYSTEMDB om de gebruiker te maken

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. De gebruiker maken

    In dit voor beeld wordt de AZACSNAP-gebruiker gemaakt in de SYSTEMDB.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. De gebruikers machtigingen verlenen

    In dit voor beeld wordt de machtiging voor de AZACSNAP-gebruiker ingesteld op het uitvoeren van een database consistente opslag momentopname.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *Optioneel* : voor komen dat het wacht woord van de gebruiker verloopt

    > [!NOTE]
    > Neem contact op met het bedrijfs beleid voordat u deze wijziging aanbrengt.

   In dit voor beeld wordt het verlopen van het wacht woord voor de AZACSNAP-gebruiker uitgeschakeld, zonder dat het wacht woord van de gebruiker wordt gewijzigd, waardoor moment opnamen niet correct kunnen worden uitgevoerd.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. Het SAP HANA beveiligde gebruikers archief instellen (Wijzig het wacht woord) in dit voor beeld wordt de `hdbuserstore` opdracht van de Linux-shell gebruikt om het SAP Hana beveiligde gebruikers archief in te stellen.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Raadpleeg het SAP HANA beveiligde gebruikers archief om te controleren of het beveiligde gebruikers archief juist is ingesteld, gebruikt `hdbuserstore` u de opdracht om de uitvoer weer te geven zoals in het volgende voor beeld. Meer informatie over het gebruik van `hdbuserstore` zijn beschikbaar op de SAP-website.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>SSL gebruiken voor communicatie met SAP HANA

Het `azacsnap` hulp programma gebruikt de opdracht van SAP Hana `hdbsql` om te communiceren met SAP Hana. Dit geldt ook voor het gebruik van SSL-opties bij het versleutelen van de communicatie met SAP HANA. `azacsnap` maakt gebruik `hdbsql` van de SSL-opties van de opdracht als volgt.

Het volgende wordt altijd gebruikt wanneer u de `azacsnap --ssl` optie gebruikt:

- `-e` -Hiermee schakelt u TLS-encryptionTLS/SSL-versleuteling. De server kiest het hoogste beschik bare niveau.
- `-ssltrustcert` -Hiermee geeft u op of het server certificaat moet worden gevalideerd.
- `-sslhostnameincert "*"` -Hiermee geeft u de hostnaam op die wordt gebruikt om de identiteit van de server te controleren. Door de hostnaam op te geven `"*"` , wordt de hostnaam van de server niet gevalideerd.

SSL-communicatie vereist ook sleutel opslag-en vertrouwens archief bestanden.  Hoewel het mogelijk is dat deze bestanden worden opgeslagen op de standaard locaties van een Linux-installatie, om ervoor te zorgen dat het juiste sleutel materiaal wordt gebruikt voor de verschillende SAP HANA systemen (dat wil zeggen, in de gevallen waarin verschillende sleutel opslag en vertrouwens archiefbestanden worden gebruikt voor elk SAP HANA systeem), `azacsnap` wordt verwacht dat de sleutel opslag en de vertrouwens Bewaar bestanden worden opgeslagen op de `securityPath` locatie zoals opgegeven in het `azacsnap` configuratie bestand.

#### <a name="key-store-files"></a>Sleutel archiefbestanden

- Als u meerdere Sid's met hetzelfde sleutel materiaal gebruikt, is het eenvoudiger om koppelingen te maken naar de locatie van de securityPath zoals gedefinieerd in het `azacsnap` configuratie bestand.  Zorg ervoor dat deze waarden voor elke SID bestaan met SSL.
  - Voor openssl:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Voor commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- Als u meerdere Sid's met het andere sleutel materiaal per SID gebruikt, kopieert u de bestanden naar de securityPath-locatie (of verplaatst en wijzigt u de naam) zoals gedefinieerd in het `azacsnap` configuratie bestand voor sid's.
  - Voor openssl:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Voor commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

Wanneer u `azacsnap` aanroept `hdbsql` , wordt deze toegevoegd `-sslkeystore=<securityPath>/<SID>_keystore` aan de opdracht regel.

#### <a name="trust-store-files"></a>Trust-archief bestanden

- Als u meerdere Sid's met hetzelfde sleutel materiaal gebruikt, maakt u vaste koppelingen op de locatie van de securityPath zoals gedefinieerd in het `azacsnap` configuratie bestand.  Zorg ervoor dat deze waarden voor elke SID bestaan met SSL.
  - Voor openssl:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Voor commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- Als u meerdere Sid's met het andere sleutel materiaal per SID gebruikt, kopieert u de bestanden naar de securityPath-locatie (of verplaatst en wijzigt u de naam) zoals gedefinieerd in het `azacsnap` configuratie bestand voor sid's.
  - Voor openssl:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Voor commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> Het `<SID>` onderdeel van de bestands namen moet de SAP Hana systeem-id zijn in hoofd letters (bijvoorbeeld, `H80` `PR1` enzovoort)

Wanneer u `azacsnap` aanroept `hdbsql` , wordt deze toegevoegd `-ssltruststore=<securityPath>/<SID>_truststore` aan de opdracht regel.

`azacsnap -c test --test hana --ssl openssl` `SID` Als dat het geval is `H80` in het configuratie bestand, wordt de `hdbsql` verbinding als volgt uitgevoerd:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> Het `\` teken is een opdracht regel regel om de duidelijkheid te verbeteren van de meerdere para meters die worden door gegeven op de opdracht regel.

## <a name="installing-the-snapshot-tools"></a>De snap shot-hulpprogram ma's installeren

Het Download bare zelf-installatie programma is ontworpen om de snap shot-hulpprogram ma's eenvoudig in te stellen en uit te voeren met niet-hoofd gebruikers bevoegdheden (bijvoorbeeld azacsnap). Het installatie programma stelt de gebruiker in en plaatst de momentopname hulpprogramma's in de `$HOME/bin` submap gebruikers (standaard = `/home/azacsnap/bin` ).

Het zelf-installatie programma probeert de juiste instellingen en paden te bepalen voor alle bestanden op basis van de configuratie van de gebruiker die de installatie uitvoert (bijvoorbeeld root). Als de vorige installatie stappen (communicatie met opslag en SAP HANA inschakelen) zijn uitgevoerd als root, worden de persoonlijke sleutel en de `hdbuserstore` locatie van de back-upgebruiker gekopieerd met de installatie. Het is echter mogelijk dat de stappen die communicatie met de back-end van de opslag mogelijk maken en SAP HANA hand matig door een beschik bare beheerder moeten worden uitgevoerd na de installatie.

> [!NOTE]
> Voor eerdere SAP HANA van installaties van grote Azure-instanties was de map met vooraf geïnstalleerde momentopname hulpprogramma's `/hana/shared/<SID>/exe/linuxx86_64/hdb` .

Nu de [vereiste stappen](#prerequisites-for-installation) zijn voltooid, kunt u de momentopname hulpprogramma's als volgt installeren met behulp van het zelf-installatie programma:

1. Kopieer het gedownloade zelf-installatie programma naar het doel systeem.
1. Voer het zelf-installatie programma uit als de `root` gebruiker. Zie het volgende voor beeld. Maak, indien nodig, het uitvoer bare bestand met behulp van de `chmod +x *.run` opdracht.

Als u de opdracht zelf Installer uitvoert zonder argumenten, wordt Help weer gegeven over het installatie programma om de momentopname hulpprogramma's als volgt te installeren:

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> Het zelf-installatie programma heeft een optie voor het extra heren (-X) van de momentopname hulpprogramma's van de bundel zonder het maken en instellen van een gebruiker in te stellen. Hiermee kan een ervaren beheerder de installatie stappen hand matig volt ooien of de opdrachten Kopiëren om een bestaande installatie bij te werken.

### <a name="easy-installation-of-snapshot-tools-default"></a>Eenvoudige installatie van moment opname-hulpprogram ma's (standaard)

Het installatie programma is ontworpen om snel de momentopname hulpprogramma's voor SAP HANA op Azure te installeren. Als het installatie programma alleen de optie-I uitvoert, worden de volgende stappen uitgevoerd:

1. Maak een moment opname gebruiker ' azacsnap ', basismap en stel groepslid maatschap in.
1. De aanmelding van de azacsnap-gebruiker configureren `~/.profile` .
1. Zoek bestands systeem voor mappen die u wilt toevoegen aan azacsnap `$PATH` . Dit zijn doorgaans de paden naar de SAP Hana-hulpprogram ma's, zoals `hdbsql` en `hdbuserstore` .
1. Zoek bestands systeem voor mappen die u wilt toevoegen aan azacsnap `$LD_LIBRARY_PATH` . Voor veel opdrachten moet een bibliotheekpad worden ingesteld om correct te kunnen worden uitgevoerd. Hiermee configureert u het pad voor de geïnstalleerde gebruiker.
1. Kopieer de SSH-sleutels voor back-azacsnap van de hoofd gebruiker (de gebruiker die de installatie uitvoert). Hierbij wordt ervan uitgegaan dat de hoofd gebruiker al verbinding met de opslag heeft geconfigureerd
    - Zie de sectie '[communicatie met opslag inschakelen](#enable-communication-with-storage)'.
1. Kopieer het beveiligde gebruikers archief van de SAP HANA-verbinding voor de doel gebruiker azacsnap. Hierbij wordt ervan uitgegaan dat de ' root '-gebruiker al het beveiligde gebruikers archief heeft geconfigureerd. Zie de sectie "communicatie inschakelen met SAP HANA".
1. De snap shot-hulpprogram ma's worden geëxtraheerd in `/home/azacsnap/bin/` .
1. De machtigingen van de opdrachten in `/home/azacsnap/bin/` zijn ingesteld (eigendom en uitvoer bare bit, enz.).

In het volgende voor beeld ziet u de juiste uitvoer van het installatie programma wanneer dit wordt uitgevoerd met de standaard installatie optie.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>De momentopname hulpprogramma's verwijderen

Als de snap shot-hulpprogram ma's zijn geïnstalleerd met de standaard instellingen, hoeft alleen te worden verwijderd als de gebruiker de opdrachten heeft geïnstalleerd (standaard = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>Hand matige installatie van de momentopname hulpprogramma's

In sommige gevallen is het nood zakelijk om de hulpprogram ma's hand matig te installeren, maar de aanbeveling is de standaard optie van het installatie programma te gebruiken om dit proces te vereenvoudigen.

Elke regel die begint met een `#` teken, toont de voorbeeld opdrachten die volgen op het teken worden uitgevoerd door de hoofd gebruiker. `\`Aan het einde van een regel is het standaard regel vervolg teken voor een shell opdracht.

Als basis gebruiker kan een hand matige installatie als volgt worden bereikt:

1. De groeps-ID ' sapsys ' ophalen, in dit geval de groeps-ID = 1010

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. Maak een moment opname gebruiker ' azacsnap ', basismap en stel groepslid maatschap in op basis van de groeps-ID uit stap 1.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Zorg ervoor dat de aanmelding van de gebruiker azacsnap `.profile` bestaat.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Zoek bestands systeem voor mappen die u wilt toevoegen aan de $PATH van azacsnap. Dit zijn doorgaans de paden naar de SAP HANA-hulpprogram ma's, zoals `hdbsql` en `hdbuserstore` .

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Het bijgewerkte pad naar het profiel van de gebruiker toevoegen

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Zoek bestands systeem naar mappen die u wilt toevoegen aan de $LD-_LIBRARY_PATH van azacsnap.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Het bijgewerkte bibliotheekpad toevoegen aan het profiel van de gebruiker

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. In azure large instances
    1. Kopieer de SSH-sleutels voor back-azacsnap van de hoofd gebruiker (de gebruiker die de installatie uitvoert). Hierbij wordt ervan uitgegaan dat de hoofd gebruiker al verbinding met de opslag heeft geconfigureerd
       > Zie de sectie '[communicatie met opslag inschakelen](#enable-communication-with-storage)'.

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. De gebruikers machtigingen op de juiste wijze instellen voor de SSH-bestanden

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. Op Azure NetApp Files
    1. Configureer het pad van de gebruiker `DOTNET_BUNDLE_EXTRACT_BASE_DIR` volgens de .net core-richt lijnen voor het extra heren van één bestand.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Kopieer het beveiligde gebruikers archief van de SAP HANA-verbinding voor de doel gebruiker azacsnap. Hierbij wordt ervan uitgegaan dat de hoofd gebruiker het beveiligde gebruikers archief al heeft geconfigureerd.
    > Zie de sectie '[communicatie met SAP Hana inschakelen](#enable-communication-with-sap-hana)'.

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. De gebruikers machtigingen op de juiste wijze instellen voor de `hdbuserstore` bestanden

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. De momentopname hulpprogramma's extra heren in/Home/azacsnap/bin/.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. De opdrachten uitvoeren als uitvoerbaar bestand

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Zorg ervoor dat de juiste machtigingen voor eigendom zijn ingesteld op de basismap van de gebruiker

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>De installatie van snap shot tools volt ooien

Het installatie programma bevat de stappen die u moet uitvoeren nadat de installatie van de momentopname hulpprogramma's is voltooid.
Volg deze stappen om de momentopname hulpprogramma's te configureren en te testen.  Nadat de tests zijn voltooid, voert u de eerste database consistente opslag momentopname uit.

In de volgende uitvoer ziet u de stappen die u moet uitvoeren na het uitvoeren van het installatie programma met de standaard installatie opties:

1. Wijzigingen aanbrengen in het gebruikers account van de moment opname
    1. `su - azacsnap`
1. De HANA Secure User Store instellen
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Wijzigen in locatie van opdrachten
   1. `cd /home/azacsnap/bin/`
1. Het bestand met klant Details configureren
   1. `azacsnap -c configure –-configuration new`
1. De verbinding met opslag testen....
   1. `azacsnap -c test –-test storage`
1. Test de verbinding met HANA....
    1. zonder SSL
       1. `azacsnap -c test –-test hana`
    1. met SSL moet u de juiste SSL-optie kiezen
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Uw eerste moment opname van de back-up uitvoeren
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

Stap 2 is nood zakelijk als '[communicatie inschakelen met SAP Hana](#enable-communication-with-sap-hana)' niet is uitgevoerd vóór de installatie.

> [!NOTE]
> De test opdrachten moeten correct worden uitgevoerd. Anders kunnen de opdrachten mislukken.

## <a name="configuring-the-database"></a>De data base configureren

In deze sectie wordt uitgelegd hoe u de data base configureert.

### <a name="sap-hana-configuration"></a>SAP HANA configuratie

Er zijn enkele aanbevolen wijzigingen die worden toegepast op SAP HANA om te zorgen voor beveiliging van de logboek back-ups en catalogus. Standaard worden de `basepath_logbackup` bestanden door en `basepath_catalogbackup` naar de `$(DIR_INSTANCE)/backup/log` Directory uitgevoerd en is het onwaarschijnlijk dat dit pad zich op een volume bevindt dat `azacsnap` is geconfigureerd voor moment opnamen deze bestanden worden niet beveiligd met opslag momentopnamen.

De volgende `hdbsql` opdracht voorbeelden zijn bedoeld om te demonstreren hoe u de logboek-en catalogus paden instelt op locaties die zich op opslag volumes bevinden die moment opnamen kunnen zijn door `azacsnap` . Controleer of de waarden op de opdracht regel overeenkomen met de configuratie van de lokale SAP HANA.

### <a name="configure-log-backup-location"></a>Locatie voor logboek back-up configureren

In dit voor beeld wordt de wijziging aangebracht in de `basepath_logbackup` para meter.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>Back-uplocatie voor catalogus configureren

In dit voor beeld wordt de wijziging aangebracht in de `basepath_catalogbackup` para meter. Controleer eerst of het `basepath_catalogbackup` pad zich op het bestands systeem bevindt, als u het pad niet met dezelfde eigenaar als de map maakt.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Als het pad moet worden gemaakt, wordt in het volgende voor beeld het pad gemaakt en worden de juiste rechten en machtigingen ingesteld. Deze opdrachten moeten worden uitgevoerd als root.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

In het volgende voor beeld wordt de instelling SAP HANA gewijzigd.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>Controleren of de back-uplocatie van het logboek en de catalogus

Nadat u de bovenstaande wijzigingen hebt aangebracht, controleert u of de instellingen juist zijn met de volgende opdracht.
In dit voor beeld worden de instellingen die zijn ingesteld volgens de bovenstaande richt lijnen, weer gegeven als systeem instellingen.

> Met deze query worden ook de standaard instellingen voor vergelijking geretourneerd.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Time-out voor logboek back-up configureren

De standaard instelling voor SAP HANA voor het uitvoeren van een logboek back-up is 900 seconden (15 minuten). U kunt deze waarde het beste verlagen tot 300 seconden (5 minuten).  Vervolgens kunt u regel matig back-ups uitvoeren (bijvoorbeeld om de 10 minuten) door het log_backups volume toe te voegen aan de sectie ander volume van het configuratie bestand.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Time-out voor logboek back-up controleren

Nadat u de wijziging hebt aangebracht in de time-out van de logboek back-up, controleert u of dit als volgt is ingesteld.
In dit voor beeld worden de instellingen die zijn ingesteld, weer gegeven als de systeem instellingen, maar deze query retourneert ook de standaard instellingen voor vergelijking.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Volgende stappen

- [Azure-toepassing consistent momentopname hulp programma configureren](azacsnap-cmd-ref-configure.md)
