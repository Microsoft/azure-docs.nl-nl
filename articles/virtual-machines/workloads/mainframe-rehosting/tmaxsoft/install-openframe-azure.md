---
title: TmaxSoft open frame installeren op Azure Virtual Machines
description: Meer informatie over het instellen van een open-frame omgeving in azure die geschikt is voor ontwikkelings-, demonstratie-, test-en productie werk belastingen.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.openlocfilehash: ad3dbd8cdc6fff7eca8c24bb6e5258022d52cb9b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018491"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>TmaxSoft open frame installeren op Azure

Meer informatie over het instellen van een open-frame omgeving in azure die geschikt is voor ontwikkelings-, demonstratie-, test-en productie werk belastingen. Deze zelf studie begeleidt u bij elke stap.

Open frame bevat meerdere onderdelen die de mainframe-emulatie omgeving maken in Azure. OpenFrame onlineservices bijvoorbeeld de mainframe-middleware vervangen, zoals IBM Customer Information Control System (CICS) en OpenFrame batch, met het TJES-onderdeel, vervangt het subsysteem voor taak invoer van IBM mainframe (JES).

OpenFrame werkt met een relationele data base, waaronder Oracle Database, Microsoft SQL Server, IBM Db2 en MySQL. In deze installatie van OpenFrame wordt de relationele data base TmaxSoft Tibero gebruikt. Zowel open frame als Tibero worden uitgevoerd op een Linux-besturings systeem. In deze zelf studie wordt CentOS 7,3 geïnstalleerd, hoewel u andere ondersteunde Linux-distributies kunt gebruiken. De open-frame toepassings server en de Tibero-Data Base zijn geïnstalleerd op één virtuele machine (VM).

In de zelf studie wordt u stapsgewijs begeleid bij de installatie van de onderdelen van de component OpenFrame. Sommige moeten afzonderlijk worden geïnstalleerd.

Belangrijkste onderdelen van openstaande frames:

- Vereiste installatie pakketten.
- Tibero-data base.
- Open Database Connectivity (ODBC) wordt door toepassingen in OpenFrame gebruikt om te communiceren met de Tibero-data base.
- OpenFrame Base, de middleware die het hele systeem beheert.
- OpenFrame batch, de oplossing waarmee de batch systemen van het mainframe worden vervangen.
- TACF, een service module waarmee gebruikers toegang tot systemen en bronnen wordt beheerd.
- Sorteer hulpprogramma voor batch-trans acties.
- OFCOBOL, een compiler die de COBOL-Program ma's van het mainframe interpreteert.
- OFASM, een compiler die de assembler-program ma's van het mainframe interpreteert.
- OpenFrame server type C (OSC), de oplossing die de middleware van het mainframe en IBM CICS vervangt.
- Java Enter prise User Solution (JEUS), een Web Application Server die is gecertificeerd voor Java Enter prise Edition 6.
- OFGW, het gateway onderdeel OpenFrame dat een 3270-listener biedt.
- OFManager, een oplossing die de bewerkings-en beheer functies van OpenFrame biedt in de webomgeving.

Andere vereiste openstaande onderdelen:

- OSI, de oplossing waarmee de mainframe-middleware en de IMS-domein controller worden vervangen.
- TJES, de oplossing die de JES-omgeving van het mainframe levert.
- OFTSAM, de oplossing waarmee (V) SAM-bestanden kunnen worden gebruikt in het open systeem.
- OFHiDB, de oplossing waarmee de IMS-data base van het mainframe wordt vervangen.
- OFPLI, een compiler die de PL/I-Program ma's van het mainframe interpreteert.
- PROTRIEVE, een oplossing die de mainframe-taal CA-Easytrieve uitvoert.
- OFMiner, een oplossing waarmee de mainframes-assets worden geanalyseerd en vervolgens naar Azure wordt gemigreerd.

## <a name="architecture"></a>Architectuur

De volgende afbeelding bevat een overzicht van de in deze zelf studie geïnstalleerde ' OpenFrame 7,0-architectuur onderdelen:

![OpenFrame-onderdelen](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Systeem vereisten voor Azure

De volgende tabel bevat de vereisten voor de installatie van Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Vereiste</th><th>Beschrijving</th></tr>
</thead>
<tbody>
<tr><td>Ondersteunde Linux-distributies op Azure
</td>
<td>
Linux x86 2,6 (32-bits, 64-bits)<br/>
Red Hat 7. x<br/>
CentOS 7. x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Kernen: 2 (mini maal)<br/>
Geheugen: 4 GB (mini maal)<br/>
Wissel ruimte: 1 GB (mini maal)<br/>
Harde schijf: 100 GB (mini maal)<br/>
</td>
</tr>
<tr><td>Optionele software voor Windows-gebruikers
</td>
<td>PuTTy: wordt gebruikt in deze hand leiding voor het configureren van VM-functies<br/>
WinSCP: een populaire SFTP-client en FTP-client die u kunt gebruiken<br/>
Eclips voor Windows: een ontwikkelings platform dat wordt ondersteund door TmaxSoft<br/>
(Micro soft Visual Studio wordt op dit moment niet ondersteund)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Vereisten

Plan de uitgaven van een paar dagen om alle vereiste software samen te stellen en alle hand matige processen te volt ooien.

Ga als volgt te werk voordat u aan de slag gaat:

- Down load het medium voor OpenFrame-installatie van TmaxSoft. Als u een bestaande TmaxSoft-klant bent, neemt u contact op met uw TmaxSoft-vertegenwoordiger voor een gelicentieerd exemplaar. Vraag anders een proef versie aan vanuit [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Vraag de documentatie van OpenFrame aan door een e-mail te verzenden naar <support@tmaxsoft.com> .

- Ontvang een Azure-abonnement als u er nog geen hebt. U kunt ook een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maken voordat u begint.

- Optioneel. Stel een site-naar-site-VPN-tunnel in of een JumpBox die de toegang tot de Azure-VM beperkt tot de toegestane gebruikers in uw organisatie. Deze stap is niet vereist, maar het is een best practice.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Een virtuele machine in azure instellen voor open frame en Tibero

U kunt de OpenFrame-omgeving instellen met behulp van verschillende implementatie patronen, maar in de volgende procedure ziet u hoe u de open frame-toepassings server en de Tibero-Data Base op één virtuele machine implementeert. In grotere omgevingen en voor grote werk belastingen is een best practice de data base afzonderlijk te implementeren op een eigen virtuele machine voor betere prestaties.

**Een virtuele machine maken**

1. Ga naar de Azure Portal op <https://portal.azure.com> en meld u aan bij uw account.

2. Klik op **Virtuele machines**.

    ![Resource lijst in Azure Portal](media/vm-01.png)

3. Klik op **Add**.

    ![Optie toevoegen in Azure Portal](media/vm-02.png)

4. Klik rechts van **besturings systemen** op **meer**.

     ![Meer optie in Azure Portal](media/vm-03.png)

5. Klik op **CentOS 7,3** om deze procedure precies te volgen of u kunt een andere ondersteunde Linux-distributie kiezen.

     ![Opties voor het besturings systeem in Azure Portal](media/vm-04.png)

6. Voer in de **basis** instellingen de **naam**, **de gebruikers naam**, het **verificatie type**, het **abonnement** (betalen per gebruik is de AWS-stijl van betaling) en de **resource groep** (gebruik een bestaande of een TmaxSoft-groep) in.

7. Wanneer dit is voltooid (inclusief het open bare/persoonlijke sleutel paar voor **verificatie type**), klikt u op **verzenden**.

> [!NOTE]
> Als u een open bare SSH-sleutel gebruikt voor **verificatie type**, raadpleegt u de stappen in de volgende sectie om het paar open bare/persoonlijke sleutels te genereren en hervat u vervolgens de stappen hier.

### <a name="generate-a-publicprivate-key-pair"></a>Een openbaar/persoonlijk sleutel paar genereren

Als u een Windows-besturings systeem gebruikt, hebt u PuTTYgen nodig om een openbaar/persoonlijk sleutel paar te genereren.

De open bare sleutel kan vrij worden gedeeld, maar de persoonlijke sleutel moet volledig geheim blijven en mag nooit worden gedeeld met een andere partij. Nadat u de sleutels hebt gegenereerd, moet u de **open bare SSH-sleutel** in de configuratie plakken: in feite wordt deze geüpload naar de virtuele Linux-machine. Het wordt opgeslagen in geautoriseerde \_ sleutels in de \~ map/.SSH van de basismap van het gebruikers account. De virtuele Linux-machine kan de verbinding vervolgens herkennen en valideren zodra u de bijbehorende **persoonlijke SSH-sleutel** hebt verstrekt in de SSH-client (in ons geval putty).

Bij het verlenen van nieuwe personen toegang tot de virtuele machine: 

- Elke nieuwe persoon genereert hun eigen open bare/persoonlijke sleutels met behulp van PuTTYgen.
- Personen slaan hun eigen persoonlijke sleutels afzonderlijk op en verzenden de informatie van de open bare sleutel naar de beheerder van de virtuele machine.
- De beheerder plakt de inhoud van de open bare sleutel naar het \~ /.ssh/authorized- \_ sleutel bestand.
- De nieuwe persoon maakt verbinding via PuTTy.

**Een openbaar/persoonlijk sleutel paar genereren**

1.  Down load PuTTYgen uit <https://www.putty.org/> en installeer het met de standaard instellingen.

2.  Om PuTTYgen te openen, zoekt u de PuTTy-installatie directory in C: \\ programma bestanden \\ putty.

    ![PuTTy-interface](media/puttygen-01.png)

3.  Klik op **Genereren**.

    ![Scherm opname van het dialoog venster PuTTy-sleutel generator en de knop genereren gemarkeerd.](media/puttygen-02.png)

4.  Sla na het genereren zowel de open bare sleutel als de persoonlijke sleutel op. Plak de inhoud van de open bare sleutel in de sectie **open bare SSH-sleutel** van het deel venster **\> basis principes voor virtuele machines maken** (zie stap 6 en 7 in de vorige sectie).

    ![Het dialoog venster PuTTy-sleutel generator](media/puttygen-03.png)

### <a name="configure-vm-features"></a>VM-functies configureren

1. Kies in Azure Portal op de Blade **een grootte kiezen** de gewenste hardware-instellingen voor de Linux-machine. De *minimale* vereisten voor het installeren van zowel Tibero als OpenFrame zijn twee cpu's en 4 GB RAM-geheugen, zoals wordt weer gegeven in deze voorbeeld installatie:

    ![Virtuele machine maken-basis beginselen](media/create-vm-01.png)

2. Klik op **3 instellingen** en gebruik de standaard instellingen om optionele functies te configureren.
3. Controleer uw betalings gegevens.

    ![Virtuele machine maken-kopen](media/create-vm-02.png)

4. Verzend uw selecties. Azure begint met het implementeren van de virtuele machine. Dit proces duurt doorgaans enkele minuten.

5. Wanneer de virtuele machine wordt geïmplementeerd, wordt het dash board weer gegeven met alle instellingen die zijn geselecteerd tijdens de configuratie. Noteer het **open bare IP-adres**.

    ![tmax op Azure-dash board](media/create-vm-03.png)

6. Open PuTTY.

7. Typ voor **hostnaam** de gebruikers naam en het open bare IP-adres dat u hebt gekopieerd. Bijvoorbeeld publicip voor **gebruikers \@ naam**.

    ![Scherm opname van het dialoog venster PuTTy-configuratie en markeert het veld hostnaam (of IP-adres).](media/putty-01.png)

8. Klik in het vak **categorie** op **verbinding \> SSH \> auth**. Geef het pad op naar uw **persoonlijke sleutel** bestand.

    ![Dialoog venster PuTTy-configuratie](media/putty-02.png)

9. Klik op **openen** om het venster putty te starten. Als dat lukt, bent u verbonden met uw nieuwe CentOS-VM die wordt uitgevoerd op Azure.

10. Als u zich wilt aanmelden als hoofd gebruiker, typt u **sudo bash**.

    ![Aanmelding hoofd gebruiker in opdracht venster](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>De omgeving en pakketten instellen

Nu de virtuele machine is gemaakt en u bent aangemeld, moet u enkele installatie stappen uitvoeren en de vereiste voor installatie pakketten installeren.

1. Wijs de naam **ofdemo** met behulp van VI toe aan het lokale IP-adres om het bestand hosts () te bewerken `vi /etc/hosts` . Ervan uitgaande dat het IP-adres 192.168.96.148 ofdemo is, is dit vóór de wijziging:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Dit is na de wijziging:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Groepen en gebruikers maken:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Wijzig het wacht woord voor de gebruiker oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. De kernel-para meters bijwerken in/etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. De kernel-para meters dynamisch vernieuwen zonder opnieuw op te starten:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. De vereiste pakketten ophalen: Zorg ervoor dat de server is verbonden met internet, down load de volgende pakketten en installeer ze:

     - dos2unix
     - glibc
     - glibc. i686 glibc. x86 \_ 64
     - libaio
     - ncurses

          > [!NOTE]
          > Nadat u het ncurses-pakket hebt geïnstalleerd, maakt u de volgende symbolische koppelingen:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c + +
     - libaio-devel. x86 \_ 64
     - strace
     - ltrace
     - gdb

7. In het geval van installatie van Java-RPM gaat u als volgt te werk:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>De Tibero-data base installeren

Tibero biedt de verschillende belang rijke functies in de open frame-omgeving van Azure:

- Tibero wordt gebruikt als het interne gegevens archief van OpenFrame voor verschillende systeem functies.
- VSAM-bestanden, waaronder KSDS, RRDS en ESDS, gebruiken de Tibero-data base intern voor gegevens opslag.
- De TACF-gegevens opslagplaats wordt opgeslagen in Tibero.
- De catalogus gegevens van OpenFrame worden opgeslagen in Tibero.
- De Tibero-data base kan worden gebruikt als vervanging voor IBM Db2 om toepassings gegevens op te slaan.

**Tibero installeren**

1. Controleer of het Tibero binaire installatie bestand aanwezig is en controleer het versie nummer.
2. Kopieer de Tibero-software naar het Tibero-gebruikers account (oframe). Bijvoorbeeld:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Open. bash- \_ profiel in VI ( `vi .bash_profile` ) en plak het volgende in het bestand:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Als u het bash-profiel wilt uitvoeren, typt u bij de opdracht prompt het volgende:

    ```
    source .bash_profile
    ```

5. Genereer het Tip-bestand (een configuratie bestand voor Tibero) en open het vervolgens in VI. Bijvoorbeeld:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Wijzig \$ TB \_ Home/client/config/tbdsn. TBR en plaats 127.0.0.1 in het oflocalhost, zoals wordt weer gegeven:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Maak de data base. De volgende uitvoer wordt weergegeven:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Als u de Tibero wilt recyclen, moet u deze eerst afsluiten met behulp van de `tbdown` opdracht. Bijvoorbeeld:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Start Tibero nu op met `tbboot` . Bijvoorbeeld:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Als u een tabel ruimte wilt maken, opent u de data base met SYS-gebruiker (sys/tmax) en maakt u de benodigde tabel ruimte voor het standaard volume en TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Typ nu de volgende SQL-opdrachten:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Start Tibero en controleer of de Tibero-processen worden uitgevoerd:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Uitvoer:

![Tibero uitvoer](media/tibero-01.png)

## <a name="install-odbc"></a>ODBC installeren

Toepassingen in OpenFrame communiceren met de Tibero-data base met behulp van de ODBC API van het open-source unixODBC-project.

ODBC installeren:

1. Controleer of het installatie bestand unixODBC-2.3.4. tar. gz aanwezig is of gebruik de `wget unixODBC-2.3.4.tar.gz` opdracht. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Pak het binaire bestand uit. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Ga naar de map unixODBC-2.3.4 en Genereer de Makefile met behulp van de controle van de computer gegevens. Bijvoorbeeld:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     UnixODBC is standaard geïnstalleerd in/usr/local, dus wordt `--prefix` een waarde door gegeven om de locatie te wijzigen. Op dezelfde manier worden configuratie bestanden standaard in/etc geïnstalleerd, zodat `--sysconfdir` de waarde van de gewenste locatie wordt door gegeven.

4. Makefile uitvoeren: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Kopieer het uitvoer bare bestand in de map Program ma's na het compileren. Bijvoorbeeld:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Gebruik VI om het bash Profiel () te bewerken `vi ~/.bash_profile` en het volgende toe te voegen:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Pas de ODBC toe. Bewerk de volgende bestanden dienovereenkomstig. Bijvoorbeeld:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Een symbolische koppeling maken en de verbinding met de Tibero-data base valideren:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

De volgende uitvoer wordt weer gegeven:

![ODBC-uitvoer met verbinding met SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Basis van openstaande frames installeren

De basis toepassings server wordt geïnstalleerd vóór de afzonderlijke services die OpenFrame gebruiken om het systeem op Azure te beheren, met inbegrip van de processen voor het verwerken van trans acties.

**Basis voor het installeren van openstaande frames**

1. Controleer of de installatie van Tibero is geslaagd en controleer vervolgens of het volgende openstaande frame \_ Base7 \_ 0 \_ Linux \_ x86 \_ 64. bin Installer File en base. Properties aanwezig zijn.

2. Werk het bash-profiel bij met de volgende Tibero-specifieke informatie:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Voer het bash-profiel uit:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Zorg ervoor dat de Tibero-processen worden uitgevoerd. Bijvoorbeeld:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Baseer](media/base-01.png)

     > [!IMPORTANT]
     > Zorg ervoor dat u Tibero start vóór de installatie.

5. Genereer een licentie op [TechNet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) en plaats de OpenFrame Base, batch, TACF en osc-licenties in de juiste map:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Down load het bestand met de basis-en basis bestanden van OpenFrame. eigenschappen:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Voer het installatie programma uit met het bestand Base. Properties. Bijvoorbeeld:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Als u klaar bent, is het bericht installatie voltooid diplayed.

8. Controleer de basis mapstructuur van OpenFrame met behulp van de `ls -ltr` opdracht. Bijvoorbeeld:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Begin OpenFrame-basis:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![uitvoer van tmboot-opdracht](media/base-02.png)

10. Controleer of de status van het proces gereed is met de opdracht tmadmin in si. RDY wordt weer gegeven in de kolom **status** voor elk van de processen:

     ![uitvoer van tmadmin-opdracht](media/base-03.png)

11. Opensluitende basis van het openstaande frame:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>OpenFrame batch installeren

OpenFrame batch bestaat uit verschillende onderdelen die mainframe batch-omgevingen simuleren en wordt gebruikt om batch-taken uit te voeren op Azure.

**Batch installeren**

1. Zorg ervoor dat de basis installatie is geslaagd en controleer vervolgens of het OpenFrame \_ Batch7 \_ 0 \_ Fix2 \_ MVS \_ Linux \_ x86 \_ 64. bin Installer-bestand en batch. Properties-configuratie bestand aanwezig zijn:

2. Typ bij de opdracht prompt `vi batch.properties` om het bestand batch. Properties te bewerken met behulp van VI.

3. Wijzig de para meters als volgt:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Als u het batch-installatie programma wilt uitvoeren, typt u bij de opdracht prompt:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Wanneer de installatie is voltooid, start u de geïnstalleerde open frame-suites door te typen `tmboot` op de opdracht regel.

    ![tmboot uitvoer](media/tmboot-01.png)

6. Typ `tmadmin` bij de opdracht prompt om het openstaande kader proces te controleren.

    ![Tmax-beheer scherm](media/tmadmin-01.png)

7. Voer de volgende opdrachten uit:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Gebruik de `tmdown` opdracht om batch te starten en af te sluiten:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>TACF installeren

TACF Manager is een open frame-service module waarmee gebruikers toegang tot systemen en bronnen wordt beheerd via RACF Security.

**TACF installeren**

1. Controleer of het \_ configuratie bestand OpenFrame Tacf7 \_ 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin Installer en tacf. Properties aanwezig zijn.
2. Controleer of de batch is geïnstalleerd en gebruik vervolgens VI om het bestand tacf. Properties () te openen `vi tacf.properties` .
3. Wijzig de TACF-para meters:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Nadat TACF Installer is voltooid, past u de omgevings variabelen TACF toe. Typ in de opdrachtprompt:

     ```
     source \~/.bash\_profile
     ```

5. Voer het installatie programma TACF uit. Typ in de opdrachtprompt:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     De uitvoer ziet er ongeveer zo uit:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. Typ bij de opdracht prompt `tmboot` om OpenFrame opnieuw te starten. De uitvoer ziet er ongeveer zo uit:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Controleer of de status van het proces gereed is met `tmadmin` de `si` opdracht. Bijvoorbeeld:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     In de kolom **status** wordt RDY weer gegeven:

    ![RDY in de kolom Status](media/tmboot-02.png)

8. Voer de volgende opdrachten uit:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Sluit de server af met behulp van de `tmdown` opdracht. De uitvoer ziet er ongeveer zo uit:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Installatie vooraf sorteren

Prosorteren is een hulp programma dat wordt gebruikt in batch-trans acties voor het sorteren van gegevens.

**Een sorteer bewerking installeren**

1. Zorg ervoor dat de batch-installatie is voltooid en controleer vervolgens of het installatie bestand voor de **prosorteren-bin- \_ 2sp3-linux64-2123-opt. tar. gz** is geïnstalleerd.

2. Voer het installatie programma uit met behulp van het eigenschappen bestand. Typ in de opdrachtprompt:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Verplaats de sorteer Directory naar de thuis locatie. Typ in de opdrachtprompt:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Maak een licentie-submap en kopieer het licentie bestand daar. Bijvoorbeeld:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Open bash. profile in VI ( `vi .bash_profile` ) en werk het als volgt bij:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Als u het bash-profiel wilt uitvoeren, typt u bij de opdracht prompt: `. .bash_profile`

7. Maak het configuratie bestand. Bijvoorbeeld:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Maak de symbolische koppeling. Bijvoorbeeld:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Controleer de installatie van de sorteer bewerking door de opdracht uit te voeren `prosort -h` . Bijvoorbeeld:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>OFCOBOL installeren

OFCOBOL is de OpenFrame-compiler waarmee de COBOL-Program ma's van het mainframe worden geïnterpreteerd. 

**OFCOBOL installeren**

1. Zorg ervoor dat de batch/online-installatie is geslaagd en controleer vervolgens of het OpenFrame \_ COBOL3 \_ 0 \_ 40 \_ Linux \_ x86 \_ 64. bin Installer-bestand aanwezig is.

2. Als u het OFCOBOL-installatie programma wilt uitvoeren, typt u bij de opdracht prompt:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Lees de gebruiksrecht overeenkomst en druk op ENTER om door te gaan.

4. Accepteer de gebruiksrecht overeenkomst. Wanneer de installatie is voltooid, wordt het volgende weer gegeven:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Open het bash-profiel in VI ( `vi .bash_profile` ) en controleer of het is bijgewerkt met OFCOBOL-variabelen.
6. Voer het bash-profiel uit. Typ in de opdrachtprompt:

     ```
      source ~/.bash_profile
     ```

7. Kopieer de OFCOBOL-licentie naar de geïnstalleerde map. Bijvoorbeeld:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Ga naar het configuratie bestand OpenFrame tjclrun. conf en open het in VI. Bijvoorbeeld:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Hier volgt de sectie SYSLIB voor de wijziging:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Hier volgt de sectie SYSLIB na de wijziging:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Controleer het bestand OpenFrame \_ COBOL \_ InstallLog. log in VI en controleer of er geen fouten zijn. Bijvoorbeeld:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Gebruik de `ofcob --version` opdracht en controleer het versie nummer om de installatie te controleren. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Start OpenFrame opnieuw met behulp van de `tmdown/tmboot` opdracht.

## <a name="install-ofasm"></a>OFASM installeren

OFASM is de OpenFrame-compiler waarmee de assembler-program ma's van het mainframe worden geïnterpreteerd.

**OFASM installeren**

1. Zorg ervoor dat de batch/online-installatie is geslaagd en controleer vervolgens of het OpenFrame \_ ASM3 \_ 0 \_ Linux \_ x86 \_ 64. bin Installer-bestand aanwezig is.

2. Voer het installatie programma uit. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Lees de gebruiksrecht overeenkomst en druk op ENTER om door te gaan.
4. Accepteer de gebruiksrecht overeenkomst.
5. Controleer of het bash-profiel is bijgewerkt met OFASM-variabelen. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Open het configuratie bestand OpenFrame tjclrun. conf in VI en bewerk dit als volgt:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Hier volgt de sectie [SYSLIB] *vóór* de wijziging:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Hier volgt de sectie [SYSLIB] *na* de wijziging:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Open het bestand OpenFrame \_ ASM \_ InstallLog. log in VI en controleer of er geen fouten zijn. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Start OpenFrame opnieuw op door een van de volgende opdrachten uit te geven:

     ```
     tmdown / tmboot
     ```

     of

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>OSC installeren

OSC is de OpenFrame-omgeving vergelijkbaar met IBM CICS die ondersteuning biedt voor grootschalige OLTP-trans acties en andere beheer functies.

**OSC installeren**

1. Controleer of de basis installatie is geslaagd en controleer vervolgens of het bestand OpenFrame \_ OSC7 \_ 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin Installer en osc. Properties aanwezig zijn.
2. Bewerk de volgende para meters in het bestand osc. Properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Voer het installatie programma uit met behulp van het eigenschappen bestand, zoals wordt weer gegeven:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Wanneer u klaar bent, wordt het bericht installatie voltooid weer gegeven.

4. Controleer of het bash-profiel is bijgewerkt met OSC-variabelen.
5. Controleer het bestand OpenFrame \_ OSC7 \_ 0 \_ Fix2 \_ InstallLog. log. Het ziet er ongeveer als volgt uit:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Gebruik VI om het configuratie bestand ofsys. seq te openen. Bijvoorbeeld:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Bewerk in de \# secties basis en \# batch de para meters zoals weer gegeven.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Kopieer het licentie bestand. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Als u OSC wilt opstarten en afsluiten, initialiseert u de CICS-regio gedeeld geheugen door te typen `osctdlinit OSCOIVP1` bij de opdracht prompt.

10. Voer uit `oscboot` om osc op te starten. De uitvoer ziet er ongeveer zo uit:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Als u wilt controleren of de status van het proces gereed is, gebruikt u de `tmadmin` opdracht in si. Alle processen moeten RDY weer geven in de kolom **status** .

    ![Processen die RDY weer geven](media/tmadmin-02.png)

12. Sluit OSC af met de `oscdown` opdracht.

## <a name="install-jeus"></a>JEUS installeren

JEUS (Java Enter prise-gebruikers oplossing) biedt de presentatielaag van de Web Application Server van OpenFrame.

Voordat u JEUS installeert, installeert u het Apache Ant-pakket, dat de bibliotheken en opdracht regel Programma's bevat die nodig zijn om JEUS te installeren.

**Apache Ant installeren**

1. Down load ant binary met behulp van de `wget` opdracht. Bijvoorbeeld:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Gebruik het `tar` hulp programma om het binaire bestand uit te pakken en te verplaatsen naar een geschikte locatie. Bijvoorbeeld:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Maak voor efficiëntie een symbolische koppeling:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Open het bash-profiel in VI ( `vi .bash_profile` ) en werk het bij met de volgende variabelen:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Pas de gewijzigde omgevings variabele toe. Bijvoorbeeld:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**JEUS installeren**

1. Vouw het installatie programma uit met het `tar` hulp programma. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Maak een **Jeus** -map ( `mkdir jeus7` ) en pak het binaire bestand uit.
3. Ga naar de installatiemap (of **Gebruik de para** meter JEUS voor uw eigen omgeving). Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Voer uit `ant clean-all` voordat u de build uitvoert. De uitvoer ziet er ongeveer zo uit:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Maak een back-up van het bestand config. Properties van het domein. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Open het bestand Domain-config-Temp late. Properties in VI:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Wijzigen `jeus.password=jeusadmin nodename=Tmaxsoft` in `jeus.password=tmax1234 nodename=ofdemo`

8. Voer de `ant install` opdracht uit om JEUS te bouwen.
9.  Werk het. bash- \_ profiel bestand bij met de JEUS-variabelen, zoals wordt weer gegeven:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Voer het bash-profiel uit. Bijvoorbeeld:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Optioneel*. Een alias maken voor het eenvoudig afsluiten en opstarten van JEUS-onderdelen:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Als u de installatie wilt controleren, start u de domein beheerders server als volgt:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Controleer door een webaanmelding met de volgende syntaxis:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     <http://192.168.92.133:9736/webadmin/login.>Het aanmeldings scherm wordt bijvoorbeeld weer gegeven:
    
     ![Aanmeldings scherm JEUS webbeheer](media/jeus-01.png)

     > [!NOTE]
     > Als u problemen ondervindt met poort beveiliging, opent u poort 9736 of schakelt u de firewall ( `systemctl stop firewall` ) uit.

14. Als u de hostnaam voor Server1 wilt wijzigen, klikt u op **vergren delen & bewerken** en klikt u vervolgens op **Server1**. Wijzig de hostnaam in het server venster als volgt:

    1.  Wijzig de **nodenaam** in **ofdemo**.
    2.  Klik op **OK** aan de rechter kant van het venster.
    3.  Klik op **wijzigingen Toep assen** linksonder in het venster en typ bij Beschrijving de *hostnaam wijzigen*.

    ![JEUS-webbeheer scherm](media/jeus-02.png)

15. Controleer of de configuratie is geslaagd in het bevestigings scherm.

    ![scherm jeus_domain server](media/jeus-03.png)

16. Start het beheerde server proces "server1" met behulp van de volgende opdracht:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>OFGW installeren

OFGW is de open frame-gateway die communicatie ondersteunt tussen de 3270-terminal emulator en de OSI-basis en de sessies tussen de terminal emulator en OSI beheert.

**OFGW installeren**

1. Controleer of JEUS is geïnstalleerd en controleer vervolgens of het \_ installatie bestand van de algemene OFGW7 0 \_ 1 \_ aanwezig is.
2. Voer het installatie programma uit. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Gebruik de volgende locaties voor de bijbehorende prompts:
     -   JEUS-basismap
     -   Domein naam JEUS
     -   JEUS-server naam
     -   Tibero-stuur programma
     -   Tmax knoop punt-ID ofdemo

4. Accepteer de overige standaard waarden en druk vervolgens op ENTER om het installatie programma af te sluiten.

5. Controleer of de URL voor OFGW werkt zoals verwacht:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Het volgende scherm wordt weer gegeven:

    ![OpenFrame-webterminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>OFManager installeren

OFManager biedt bewerkings-en beheer functies voor OpenFrame in de webomgeving.

**OFManager installeren**

1. Controleer of het \_ installatie bestand GENERIC. bin OFManager7 aanwezig is.
2. Voer het installatie programma uit. Bijvoorbeeld:

     ```
     OFManager7_Generic.bin
     ```

3.  Druk op ENTER om door te gaan en accepteer de gebruiksrecht overeenkomst.
4.  Kies de installatiemap.
5.  Accepteer de standaardwaarden.
6.  Kies Tibero als de data base.
7.  Druk op ENTER om het installatie programma af te sluiten.
8.  Controleer of de URL voor OFManager werkt zoals verwacht:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Het Start scherm wordt weer gegeven:

![Aanmeldings scherm tmax OpenFrame Manager](media/ofmanager-01.png)

Hiermee wordt de installatie van de onderdelen van OpenFrame voltooid.

## <a name="next-steps"></a>Volgende stappen

Als u een mainframe-migratie overweegt, is onze groeiende partner ecosysteem beschikbaar om u te helpen. Raadpleeg voor meer informatie over het kiezen van een partner oplossing het [platform modernisatie Alliance](https://datamigration.microsoft.com/).

-   [Aan de slag met Azure](../../../../index.yml)
-   [Documentatie voor Host Integration Server (HIS)](/host-integration-server/)
-   [Azure Virtual Data Center Lift-and-Shift-gids](/archive/blogs/azurecat/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide)
