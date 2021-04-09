---
title: HBase cluster replicatie in virtuele netwerken-Azure HDInsight
description: Meer informatie over het instellen van HBase-replicatie van één HDInsight-versie naar een andere voor taak verdeling, hoge Beschik baarheid, migratie van nul en uitval tijd en herstel na nood gevallen.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: cfcb3a5a601afadb9f3fcd71c24e18a9d7f27b9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946416"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Apache HBase-cluster replicatie in virtuele Azure-netwerken instellen

Meer informatie over het instellen van [Apache HBase](https://hbase.apache.org/) -replicatie binnen een virtueel netwerk of tussen twee virtuele netwerken in Azure.

Cluster replicatie maakt gebruik van een methode voor bron push. Een HBase-cluster kan een bron of een bestemming zijn, maar kan beide rollen tegelijk vervullen. Replicatie is asynchroon. Het doel van replicatie is uiteindelijk consistentie. Wanneer de bron een bewerking naar een kolom familie ontvangt wanneer replicatie is ingeschakeld, wordt de bewerking door gegeven aan alle doel clusters. Wanneer gegevens van het ene naar het andere cluster worden gerepliceerd, worden het bron cluster en alle clusters die al de gegevens hebben gebruikt, bijgehouden om replicatie lussen te voor komen.

In dit artikel stelt u een bron-doel replicatie in. Zie de [Naslag Gids voor Apache HBase](https://hbase.apache.org/book.html#_cluster_replication)voor andere cluster topologieën.

Hieronder vindt u de HBase voor één virtueel netwerk:

* Taakverdeling. U kunt bijvoorbeeld scans of MapReduce-taken uitvoeren op het doel cluster en gegevens opnemen in het bron cluster.
* Hoge Beschik baarheid toevoegen.
* Gegevens migreren van het ene HBase-cluster naar het andere.
* Een Azure HDInsight-cluster upgraden van de ene versie naar een andere.

Hieronder vindt u een HBase voor twee virtuele netwerken:

* Herstel na nood geval instellen.
* Taak verdeling en partitioneren van de toepassing.
* Hoge Beschik baarheid toevoegen.

U kunt clusters repliceren met behulp van [script actie](../hdinsight-hadoop-customize-cluster-linux.md) scripts van [github](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Vereisten
Voordat u aan dit artikel begint, moet u een Azure-abonnement hebben. Zie [een gratis proef versie van Azure downloaden](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>De omgevingen instellen

U hebt drie configuratie opties:

- Twee Apache HBase-clusters in één virtueel Azure-netwerk.
- Twee Apache HBase-clusters in twee verschillende virtuele netwerken in dezelfde regio.
- Twee Apache HBase-clusters in twee verschillende virtuele netwerken in twee verschillende regio's (geo-replicatie).

In dit artikel wordt het geo-replicatie scenario besproken.

We hebben enkele [Azure Resource Manager sjablonen](../../azure-resource-manager/management/overview.md)gemaakt om u te helpen bij het instellen van de omgevingen. Als u de omgevingen liever via andere methoden wilt instellen, raadpleegt u:

- [Apache Hadoop clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Apache HBase-clusters maken in azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Twee virtuele netwerken in twee verschillende regio's instellen

Als u een sjabloon wilt gebruiken waarmee twee virtuele netwerken in twee verschillende regio's en de VPN-verbinding tussen de VNets worden gemaakt, selecteert u de volgende knop **implementeren in azure** . De definitie van de sjabloon wordt opgeslagen in een [open bare Blob-opslag](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Enkele van de in code vastgelegde waarden in de sjabloon:

**VNet 1**

| Eigenschap | Waarde |
|----------|-------|
| Locatie | VS - west |
| VNet-naam | &lt;ClusterNamePrevix>-vnet1 |
| Adres ruimte voorvoegsel | 10.1.0.0/16 |
| Subnetnaam | subnet 1 |
| Subnetvoorvoegsel | 10.1.0.0/24 |
| Subnet-naam (gateway) | GatewaySubnet (kan niet worden gewijzigd) |
| Voor voegsel van subnet (gateway) | 10.1.255.0/27 |
| Gateway naam | vnet1gw |
| Gatewaytype | VPN |
| VPN-type gateway | RouteBased |
| Gateway-SKU | Basic |
| Gateway-IP | vnet1gwip |

**VNet 2**

| Eigenschap | Waarde |
|----------|-------|
| Locatie | VS - oost |
| VNet-naam | &lt;ClusterNamePrevix>-vnet2 |
| Adres ruimte voorvoegsel | 10.2.0.0/16 |
| Subnetnaam | subnet 1 |
| Subnetvoorvoegsel | 10.2.0.0/24 |
| Subnet-naam (gateway) | GatewaySubnet (kan niet worden gewijzigd) |
| Voor voegsel van subnet (gateway) | 10.2.255.0/27 |
| Gateway naam | vnet2gw |
| Gatewaytype | VPN |
| VPN-type gateway | RouteBased |
| Gateway-SKU | Basic |
| Gateway-IP | vnet1gwip |

## <a name="setup-dns"></a>DNS instellen

In de laatste sectie maakt de sjabloon een virtuele Ubuntu-machine in elk van de twee virtuele netwerken.  In deze sectie installeert u Bind op de twee virtuele DNS-machines en configureert u vervolgens de DNS-door Voer op de twee virtuele machines.

Voor het installeren van een BIND moet yon het open bare IP-adres van de twee virtuele DNS-machines vinden.

1. Open de [Azure Portal](https://portal.azure.com).
2. Open de virtuele DNS-machine door **resource groepen te selecteren > [naam resource groep] > [vnet1DNS]**.  De naam van de resource groep is het account dat u in de laatste procedure maakt. De standaard namen van de virtuele DNS-machines zijn *vnet1DNS* en *vnet2NDS*.
3. Selecteer **Eigenschappen** om de pagina eigenschappen van het virtuele netwerk te openen.
4. Noteer het **open bare IP-adres** en Controleer ook het **privé-IP-adres**.  Het privé-IP-adres is **10.1.0.4** voor vnet1DNS en **10.2.0.4** voor vnet2DNS.  
5. Wijzig de DNS-servers voor beide virtuele netwerken zodat de standaard (door Azure meegeleverde) DNS-servers worden gebruikt om binnenkomende en uitgaande toegang toe te staan om pakketten te downloaden voor het installeren van binding in de volgende stappen.

Als u BIND wilt installeren, gebruikt u de volgende procedure:

1. Gebruik SSH om verbinding te maken met het __open bare IP-adres__ van de virtuele DNS-machine. In het volgende voor beeld wordt verbinding gemaakt met een virtuele machine op 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Vervang door `sshuser` het SSH-gebruikers account dat u hebt opgegeven bij het maken van de DNS-virtuele machine.

    > [!NOTE]  
    > Er zijn verschillende manieren om het `ssh` hulp programma te verkrijgen. In Linux, UNIX en macOS wordt het meegeleverd als onderdeel van het besturings systeem. Als u met Windows werkt, kunt u een van de volgende opties gebruiken:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash op Ubuntu in Windows 10](/windows/wsl/about)
    > * [Githttps://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSHhttps://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Als u BIND wilt installeren, gebruikt u de volgende opdrachten uit de SSH-sessie:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Configureer BIND om aanvragen voor naam omzetting door te sturen naar uw on-premises DNS-server. Als u dit wilt doen, gebruikt u de volgende tekst als de inhoud van het `/etc/bind/named.conf.options` bestand:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > Vervang de waarden in de `goodclients` sectie door het IP-adres bereik van de twee virtuele netwerken. In deze sectie worden de adressen gedefinieerd waarvan deze DNS-server aanvragen accepteert.

    Als u dit bestand wilt bewerken, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Als u het bestand wilt opslaan, gebruikt u __CTRL + X__, __Y__ en __voert__ u in.

4. Gebruik in de SSH-sessie de volgende opdracht:

    ```bash
    hostname -f
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

    ```output
    vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    De `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` tekst is het __DNS-achtervoegsel__ voor dit virtuele netwerk. Sla deze waarde op, aangezien u die later nog nodig hebt.

    U moet ook het DNS-achtervoegsel van de andere DNS-server vinden. U hebt deze nodig in de volgende stap.

5. Als u BIND wilt configureren om DNS-namen voor resources binnen het virtuele netwerk op te lossen, gebruikt u de volgende tekst als de inhoud van het `/etc/bind/named.conf.local` bestand:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > U moet de door `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` het DNS-achtervoegsel van het andere virtuele netwerk vervangen. En het IP-adres van de doorstuur server bevindt zich in een ander virtueel netwerk.

    Als u dit bestand wilt bewerken, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Als u het bestand wilt opslaan, gebruikt u __CTRL + X__, __Y__ en __voert__ u in.

6. Als u BIND wilt starten, gebruikt u de volgende opdracht:

    ```bash
    sudo service bind9 restart
    ```

7. Gebruik de volgende opdrachten om te controleren of bind de namen van resources in het andere virtuele netwerk kan omzetten:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Vervang door `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` de Fully Qualified Domain Name (FQDN) van de virtuele DNS-machine in het andere netwerk.
    >
    > Vervang door `10.2.0.4` het __interne IP-adres__ van uw aangepaste DNS-server in het andere virtuele netwerk.

    Het antwoord wordt weer gegeven zoals in de volgende tekst:

    ```output
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Tot nu toe kunt u het IP-adres niet opzoeken vanuit het andere netwerk zonder het IP-adres van de DNS-server opgegeven.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Het virtuele netwerk configureren voor het gebruik van de aangepaste DNS-server

Als u het virtuele netwerk wilt configureren voor het gebruik van de aangepaste DNS-server in plaats van de recursieve resolver van Azure, moet u de volgende stappen uitvoeren:

1. In de [Azure Portal](https://portal.azure.com)selecteert u het virtuele netwerk en selecteert u vervolgens __DNS-servers__.

2. Selecteer __aangepast__ en voer het __interne IP-adres__ van de aangepaste DNS-server in. Selecteer ten slotte __Opslaan__.

6. Open de virtuele machine van de DNS-server in vnet1 en klik op **opnieuw opstarten**.  U moet alle virtuele machines in het virtuele netwerk opnieuw opstarten om de DNS-configuratie van kracht te laten worden.
7. Herhaal de stappen om de aangepaste DNS-server voor vnet2 te configureren.

Als u de DNS-configuratie wilt testen, kunt u via SSH verbinding maken met de twee virtuele DNS-machines en de DNS-server van het andere virtuele netwerk pingen met behulp van de hostnaam. Als het niet werkt, gebruikt u de volgende opdracht om de DNS-status te controleren:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Apache HBase-clusters maken

Maak een [Apache HBase](https://hbase.apache.org/) -cluster in elk van de twee virtuele netwerken met de volgende configuratie:

- **Naam van resource groep**: gebruik dezelfde naam voor de resource groep als u de virtuele netwerken hebt gemaakt.
- **Cluster type**: HBase
- **Versie**: HBase 1.1.2 (HDI 3,6)
- **Locatie**: gebruik dezelfde locatie als het virtuele netwerk.  Vnet1 is standaard VS- *West* en VNET2 is VS- *Oost*.
- **Opslag**: Maak een nieuw opslag account voor het cluster.
- **Virtueel netwerk** (van geavanceerde instellingen op de portal): Selecteer vnet1 die u in de laatste procedure hebt gemaakt.
- **Subnet**: de standaard naam die in de sjabloon wordt gebruikt, is **subnet1**.

Om ervoor te zorgen dat de omgeving correct is geconfigureerd, moet u de FQDN van de hoofd knooppunt tussen de twee clusters kunnen pingen.

## <a name="load-test-data"></a>Testgegevens laden

Wanneer u een cluster repliceert, moet u de tabellen opgeven die u wilt repliceren. In deze sectie laadt u enkele gegevens in het bron cluster. In de volgende sectie schakelt u de replicatie tussen de twee clusters in.

Volg de instructies in [Apache HBase-zelf studie: aan de slag met Apache HBase in HDInsight](apache-hbase-tutorial-get-started-linux.md)om een tabel **contact personen** te maken en gegevens in de tabel in te voegen.

> [!NOTE]
> Als u tabellen uit een aangepaste naam ruimte wilt repliceren, moet u ervoor zorgen dat ook de juiste aangepaste naam ruimten op het doel cluster worden gedefinieerd.
>

## <a name="enable-replication"></a>Replicatie inschakelen

In de volgende stappen wordt beschreven hoe u de script actie script aanroept vanuit het Azure Portal. Zie [HDInsight-clusters aanpassen met behulp van script acties](../hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het uitvoeren van een script actie met behulp van Azure PowerShell en de klassieke Azure-cli.

**HBase-replicatie inschakelen vanuit de Azure Portal**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Open het bron HBase-cluster.
3. Selecteer **script acties** in het menu cluster.
4. Selecteer aan de bovenkant van de pagina de optie **Nieuw verzenden**.
5. Selecteer of voer de volgende gegevens in:

   1. **Naam**: Voer **replicatie inschakelen in**.
   2. **URL van bash-script**: Voer in **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** .
   3. **Kop**: Zorg ervoor dat dit is geselecteerd. Wis de andere knooppunt typen.
   4. **Para meters**: de volgende voorbeeld parameters maken replicatie voor alle bestaande tabellen mogelijk en kopiëren alle gegevens van het bron cluster naar het doel cluster:

    `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata`
    
      > [!NOTE]
      > Gebruik hostname in plaats van FQDN voor zowel de bron-als het doel cluster DNS-naam.
      >
      > In dit scenario wordt ervan uitgegaan dat HN1 als actieve hoofd knooppunt. Controleer uw cluster om het actieve hoofd knooppunt te identificeren.

6. Selecteer **Maken**. Het uitvoeren van het script kan enige tijd duren, vooral wanneer u het argument **-copydata** gebruikt.

Vereiste argumenten:

|Naam|Beschrijving|
|----|-----------|
|-s,--src-cluster | Hiermee geeft u de DNS-naam van het bron HBase-cluster. Bijvoorbeeld:-s hbsrccluster,--src-cluster = hbsrccluster |
|-d,--DST-cluster | Hiermee geeft u de DNS-naam van het doel (replica) HBase-cluster. Bijvoorbeeld:-s dsthbcluster,--src-cluster = dsthbcluster |
|-SP,--src-ambari-Password | Hiermee geeft u het beheerders wachtwoord op voor Ambari op het HBase-bron cluster. |
|-DP,--DST-ambari-Password | Hiermee geeft u het beheerders wachtwoord op voor Ambari op het doel-HBase-cluster.|

Optionele argumenten:

|Naam|Beschrijving|
|----|-----------|
|-su,--src-ambari-User | Hiermee geeft u de gebruikers naam van de beheerder voor Ambari op het HBase-bron cluster. De standaard waarde is **admin**. |
|-du,--DST-ambari-User | Hiermee geeft u de gebruikers naam van de beheerder voor Ambari op het cluster van de doel-HBase. De standaard waarde is **admin**. |
|-t,--tabel lijst | Hiermee worden de tabellen opgegeven die moeten worden gerepliceerd. Bijvoorbeeld:--Table-List = "Tabel1; tabel2; table3". Als u geen tabellen opgeeft, worden alle bestaande HBase-tabellen gerepliceerd.|
|-m,--computer | Hiermee geeft u het hoofd knooppunt op waarop de script actie wordt uitgevoerd. De waarde moet worden gekozen op basis van het actieve hoofd knooppunt. Gebruik deze optie wanneer u het script $0 als script actie uitvoert vanuit de HDInsight-portal of Azure PowerShell.|
|-CP,-copydata | Hiermee schakelt u de migratie van bestaande gegevens in op de tabellen waarvoor replicatie is ingeschakeld. |
|-rpm,-repliceren-Phoenix-meta | Hiermee schakelt u replicatie in voor Breda-systeem tabellen. <br><br>*Gebruik deze optie met een waarschuwing.* U wordt aangeraden Bredae tabellen opnieuw te maken op replica clusters voordat u dit script gebruikt. |
|-h,--Help | Hiermee worden gebruiks gegevens weer gegeven. |

De `print_usage()` sectie van het [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) bevat een gedetailleerde uitleg van para meters.

Nadat de script actie is geïmplementeerd, kunt u SSH gebruiken om verbinding te maken met het doel-HBase-cluster en vervolgens controleren of de gegevens zijn gerepliceerd.

### <a name="replication-scenarios"></a>Replicatiescenario's

In de volgende lijst ziet u enkele algemene gebruiks gevallen en de bijbehorende parameter instellingen:

- **Schakel replicatie in voor alle tabellen tussen de twee clusters**. Voor dit scenario hoeven geen bestaande gegevens in de tabellen te worden gekopieerd of gemigreerd en worden er geen Phoenix-tabellen gebruikt. Gebruik de volgende para meters:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>`

- **Schakel replicatie in voor specifieke tabellen**. Gebruik de volgende para meters om replicatie in Tabel1, Tabel2 en table3 in te scha kelen:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"`

- **Schakel replicatie in voor specifieke tabellen en kopieer de bestaande gegevens**. Gebruik de volgende para meters om replicatie in Tabel1, Tabel2 en table3 in te scha kelen:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata`

- **Schakel replicatie in voor alle tabellen en repliceer bredae meta gegevens van bron naar doel**. Phoenix-meta gegevens replicatie is niet perfect. Wees voorzichtig met het gebruik ervan. Gebruik de volgende para meters:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta`

## <a name="copy-and-migrate-data"></a>Gegevens kopiëren en migreren

Er zijn twee afzonderlijke script actie scripts beschikbaar voor het kopiëren of migreren van gegevens nadat de replicatie is ingeschakeld:

- [Script voor kleine tabellen](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabellen met een paar gigabytes grootte en de algehele kopie wordt in minder dan een uur naar verwachting voltooid)

- [Script voor grote tabellen](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabellen die naar verwachting langer dan één uur moeten worden gekopieerd)

U kunt dezelfde procedure volgen die wordt beschreven in [replicatie inschakelen](#enable-replication) om de script actie aan te roepen. Gebruik de volgende para meters:

`-m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]`

De `print_usage()` sectie van het [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) bevat een gedetailleerde beschrijving van para meters.

### <a name="scenarios"></a>Scenario's

- **Kopieer specifieke tabellen (test1, Test2 en Test3) voor alle rijen die tot nu toe zijn bewerkt (huidige tijds tempel)**:

  `-m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

  Of

  `-m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

- **Specifieke tabellen kopiëren met een opgegeven tijds bereik**:

  `-m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"`

## <a name="disable-replication"></a>Replicatie uitschakelen

Als u de replicatie wilt uitschakelen, gebruikt u een ander script actie script van [github](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). U kunt dezelfde procedure volgen die wordt beschreven in [replicatie inschakelen](#enable-replication) om de script actie aan te roepen. Gebruik de volgende para meters:

`-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">`

De `print_usage()` sectie van het [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) bevat een gedetailleerde uitleg van para meters.

### <a name="scenarios"></a>Scenario's

- **Replicatie voor alle tabellen uitschakelen**:

  `-m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all`

  of

  `--src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>`

- **Replicatie voor opgegeven tabellen uitschakelen (Tabel1, Tabel2 en table3)**:

  `-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"`

> [!NOTE]
> Als u van plan bent om het doel cluster te verwijderen, moet u dit verwijderen uit de lijst met peers van het bron cluster. U kunt dit doen door de opdracht remove_peer ' 1 ' uit te voeren op de hbase-shell van het bron cluster. Als dit mislukt, werkt het bron cluster mogelijk niet goed.
>

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Apache HBase-replicatie kunt instellen binnen een virtueel netwerk of tussen twee virtuele netwerken. Raadpleeg de volgende artikelen voor meer informatie over HDInsight en Apache HBase:

* [Aan de slag met Apache HBase in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Overzicht van HDInsight Apache HBase](./apache-hbase-overview.md)
* [Apache HBase-clusters maken in azure Virtual Network](./apache-hbase-provision-vnet.md)