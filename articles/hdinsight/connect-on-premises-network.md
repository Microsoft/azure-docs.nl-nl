---
title: Azure HDInsight verbinden met uw on-premises netwerk
description: Meer informatie over het maken van een HDInsight-cluster in een Azure Virtual Network en vervolgens verbinding maken met uw on-premises netwerk. Meer informatie over het configureren van naam omzetting tussen HDInsight en uw on-premises netwerk met behulp van een aangepaste DNS-server.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: cd787e1c846bfe4728577cbbce069385ce064a10
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943396"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>HDInsight verbinden met uw on-premises netwerk

Informatie over het verbinden van HDInsight met uw on-premises netwerk met behulp van Azure Virtual Networks en een VPN-gateway. Dit document bevat plannings informatie over:

* HDInsight gebruiken in een Azure-Virtual Network die verbinding maakt met uw on-premises netwerk.
* DNS-naam omzetting configureren tussen het virtuele netwerk en uw on-premises netwerk.
* Netwerk beveiligings groepen configureren om de Internet toegang tot HDInsight te beperken.
* Poorten van HDInsight in het virtuele netwerk.

## <a name="overview"></a>Overzicht

Als u HDInsight en resources in het gekoppelde netwerk wilt toestaan om te communiceren op naam, moet u de volgende acties uitvoeren:

1. Azure Virtual Network maken.
1. Maak een aangepaste DNS-server in de Azure-Virtual Network.
1. Configureer het virtuele netwerk voor het gebruik van de aangepaste DNS-server in plaats van de standaard recursieve resolver van Azure.
1. Configureer door sturen tussen de aangepaste DNS-server en uw on-premises DNS-server.

Deze configuraties bieden het volgende gedrag:

* Aanvragen voor volledig gekwalificeerde domein namen die het DNS-achtervoegsel __voor het virtuele netwerk__ hebben, worden doorgestuurd naar de aangepaste DNS-server. De aangepaste DNS-server stuurt deze aanvragen vervolgens door naar de recursieve resolver van Azure, waarmee het IP-adres wordt geretourneerd.
* Alle andere aanvragen worden doorgestuurd naar de on-premises DNS-server. Zelfs aanvragen voor open bare Internet bronnen zoals microsoft.com worden doorgestuurd naar de on-premises DNS-server voor naam omzetting.

In het volgende diagram zijn groene lijnen aanvragen voor bronnen die eindigen op het DNS-achtervoegsel van het virtuele netwerk. Blauwe lijnen zijn aanvragen voor bronnen in het on-premises netwerk of op het open bare Internet.

![Diagram van hoe DNS-aanvragen worden omgezet in de configuratie](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Vereisten

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Als u Power shell gebruikt, hebt u de [AZ-module](/powershell/azure/)nodig.
* Als u Azure CLI wilt gebruiken en u deze nog niet hebt geïnstalleerd, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Configuratie van het virtuele netwerk maken

Gebruik de volgende documenten voor meer informatie over het maken van een Azure-Virtual Network die is verbonden met uw on-premises netwerk:

* [Azure Portal gebruiken](../vpn-gateway/tutorial-site-to-site-portal.md)
* [Azure PowerShell gebruiken](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Azure CLI gebruiken](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Aangepaste DNS-server maken

> [!IMPORTANT]  
> U moet de DNS-server maken en configureren voordat u HDInsight in het virtuele netwerk installeert.

In deze stappen wordt gebruikgemaakt van de [Azure Portal](https://portal.azure.com) om een virtuele Azure-machine te maken. Zie [VM maken-Azure cli](../virtual-machines/linux/quick-create-cli.md) en [VM-Azure PowerShell maken](../virtual-machines/linux/quick-create-powershell.md)voor andere manieren om een virtuele machine te maken.  Als u een virtuele Linux-machine wilt maken die gebruikmaakt van de [BIND](https://www.isc.org/downloads/bind/) -DNS-software, gebruikt u de volgende stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
  
1. Selecteer **+ Een resource maken** in het menu aan de bovenkant.

    ![Een virtuele Ubuntu-machine maken](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Selecteer   >  **virtuele machine** berekenen om naar de pagina **een virtuele machine maken** te gaan.

1. Voer op het tabblad __basis beginselen__ de volgende gegevens in:  
  
    | Veld | Waarde |
    | --- | --- |
    |Abonnement |Selecteer het juiste abonnement.|
    |Resourcegroep |Selecteer de resource groep met het virtuele netwerk dat u eerder hebt gemaakt.|
    |Naam van de virtuele machine | Voer een beschrijvende naam in waarmee deze virtuele machine wordt geïdentificeerd. In dit voor beeld wordt **DNSProxy** gebruikt.|
    |Regio | Selecteer dezelfde regio als het virtuele netwerk dat u eerder hebt gemaakt.  Niet alle VM-grootten zijn in alle regio's beschikbaar.  |
    |Beschikbaarheidsopties |  Selecteer het gewenste niveau van Beschik baarheid.  Azure biedt verschillende opties voor het beheren van de beschik baarheid en tolerantie voor uw toepassingen.  Ontwikkel uw oplossing voor het gebruik van gerepliceerde Vm's in Beschikbaarheidszones-of beschikbaarheids sets om uw apps en gegevens te beschermen tegen Data Center-storingen en onderhouds gebeurtenissen. In dit voor beeld wordt **geen infra structuur-redundantie vereist**. |
    |Installatiekopie | Verlaat **Ubuntu Server 18,04 LTS**. |
    |Verificatietype | __Open bare__ __wacht woord__ of SSH-sleutel: de verificatie methode voor het SSH-account. We raden u aan open bare sleutels te gebruiken, omdat ze veiliger zijn. In dit voor beeld wordt **wacht woord** gebruikt.  Zie het document [SSH-sleutels voor Linux-Vm's maken en gebruiken](../virtual-machines/linux/mac-create-ssh-keys.md) voor meer informatie.|
    |Gebruikersnaam |Voer de gebruikers naam van de beheerder voor de virtuele machine in.  In dit voor beeld wordt **sshuser** gebruikt.|
    |Wacht woord of open bare SSH-sleutel | Het beschik bare veld wordt bepaald door uw keuze voor **verificatie type**.  Voer de juiste waarde in.|
    |Openbare poorten voor inkomend verkeer|Selecteer **Geselecteerde poorten toestaan**. Selecteer vervolgens **SSH (22)** in de vervolg keuzelijst **Selecteer binnenkomende poorten** .|

    ![Basis configuratie van virtuele machine](./media/connect-on-premises-network/virtual-machine-basics.png)

    Geef andere vermeldingen op bij de standaard waarden en selecteer vervolgens het tabblad **netwerken** .

4. Voer op het tabblad **netwerken** de volgende gegevens in:

    | Veld | Waarde |
    | --- | --- |
    |Virtueel netwerk | Selecteer het virtuele netwerk dat u eerder hebt gemaakt.|
    |Subnet | Selecteer het standaard subnet voor het virtuele netwerk dat u eerder hebt gemaakt. Selecteer __niet__ het subnet dat wordt gebruikt door de VPN-gateway.|
    |Openbare IP | Gebruik de waarde die automatisch is ingevuld.  |

    ![Instellingen voor virtuele HDInsight-netwerken](./media/connect-on-premises-network/virtual-network-settings.png)

    Geef andere vermeldingen op bij de standaard waarden en selecteer vervolgens de **beoordeling + maken**.

5. Selecteer op het tabblad **controleren en maken** de optie **maken** om de virtuele machine te maken.

### <a name="review-ip-addresses"></a>IP-adressen controleren

Als de virtuele machine eenmaal is gemaakt, ontvangt u een melding over de **implementatie** met een knop **naar de resource** .  Selecteer **Ga naar resource** om naar de nieuwe virtuele machine te gaan.  Voer in de standaard weergave voor uw nieuwe virtuele machine de volgende stappen uit om de bijbehorende IP-adressen te identificeren:

1. Selecteer **Eigenschappen** uit **instellingen**.

2. Noteer de waarden voor **openbaar IP-adres/DNS-naam label** en **privé-IP-adres** voor later gebruik.

   ![Open bare en privé-IP-adressen](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Binding installeren en configureren (DNS-software)

1. Gebruik SSH om verbinding te maken met het __open bare IP-adres__ van de virtuele machine. Vervang door `sshuser` het SSH-gebruikers account dat u hebt opgegeven bij het maken van de virtuele machine. In het volgende voor beeld wordt verbinding gemaakt met een virtuele machine op 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Als u BIND wilt installeren, gebruikt u de volgende opdrachten uit de SSH-sessie:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Als u BIND wilt configureren om aanvragen voor naam omzetting door te sturen naar uw on-premises DNS-server, gebruikt u de volgende tekst als de inhoud van het `/etc/bind/named.conf.options` bestand:

    ```DNS Zone file
    acl goodclients {
        10.0.0.0/16; # Replace with the IP address range of the virtual network
        10.1.0.0/16; # Replace with the IP address range of the on-premises network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            192.168.0.1; # Replace with the IP address of the on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

    > [!IMPORTANT]  
    > Vervang de waarden in de `goodclients` sectie door het IP-adres bereik van het virtuele netwerk en het on-premises netwerk. In deze sectie worden de adressen gedefinieerd waarvan deze DNS-server aanvragen accepteert.
    >
    > Vervang de `192.168.0.1` vermelding in de `forwarders` sectie door het IP-adres van uw on-PREMISES DNS-server. Met deze vermelding worden DNS-aanvragen naar uw on-premises DNS-server gerouteerd voor oplossing.

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
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    De `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` tekst is het __DNS-achtervoegsel__ voor dit virtuele netwerk. Sla deze waarde op, zoals deze later wordt gebruikt.

5. Als u BIND wilt configureren om DNS-namen voor resources binnen het virtuele netwerk op te lossen, gebruikt u de volgende tekst als de inhoud van het `/etc/bind/named.conf.local` bestand:

    ```DNS Zone file
    // Replace the following with the DNS suffix for your virtual network
    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > U moet de vervangen `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` door het DNS-achtervoegsel dat u eerder hebt opgehaald.

    Als u dit bestand wilt bewerken, gebruikt u de volgende opdracht:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Als u het bestand wilt opslaan, gebruikt u __CTRL + X__, __Y__ en __voert__ u in.

6. Als u BIND wilt starten, gebruikt u de volgende opdracht:

    ```bash
    sudo service bind9 restart
    ```

7. Gebruik de volgende opdrachten om te controleren of de binding de namen van resources in uw on-premises netwerk kan omzetten:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Vervang door `dns.mynetwork.net` de Fully Qualified Domain Name (FQDN) van een bron in uw on-premises netwerk.
    >
    > Vervang door `10.0.0.4` het __interne IP-adres__ van uw aangepaste DNS-server in het virtuele netwerk.

    Het antwoord wordt weer gegeven zoals in de volgende tekst:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Het virtuele netwerk configureren voor het gebruik van de aangepaste DNS-server

Als u het virtuele netwerk wilt configureren voor het gebruik van de aangepaste DNS-server in plaats van de recursieve resolver van Azure, gebruikt u de volgende stappen uit de [Azure Portal](https://portal.azure.com):

1. Ga in het menu links naar **alle services**  >  **netwerken**  >  **virtuele netwerken**.

2. Selecteer het virtuele netwerk in de lijst, waarmee de standaard weergave voor uw virtuele netwerk wordt geopend.  

3. Selecteer in de standaard weergave onder **instellingen** de optie **DNS-servers**.  

4. Selecteer __aangepast__ en voer het **privé-IP-adres** van de aangepaste DNS-server in.

5. Selecteer __Opslaan__.  <br />  

    ![De aangepaste DNS-server voor het netwerk instellen](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>On-premises DNS-server configureren

In de vorige sectie hebt u de aangepaste DNS-server geconfigureerd voor het door sturen van aanvragen naar de on-premises DNS-server. Vervolgens moet u de on-premises DNS-server configureren voor het door sturen van aanvragen naar de aangepaste DNS-server.

Raadpleeg de documentatie voor uw DNS-server software voor specifieke stappen voor het configureren van uw DNS-server. Zoek naar de stappen voor het configureren van een __voorwaardelijke doorstuur server__.

Een voorwaardelijke voorwaarts stuurt alleen aanvragen voor een specifiek DNS-achtervoegsel door. In dit geval moet u een doorstuur server configureren voor het DNS-achtervoegsel van het virtuele netwerk. Aanvragen voor dit achtervoegsel moeten worden doorgestuurd naar het IP-adres van de aangepaste DNS-server. 

De volgende tekst is een voor beeld van een configuratie voor voorwaardelijke doorstuur servers voor de **BIND** -DNS-software:

```DNS Zone file
zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
    type forward;
    forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
};
```

Zie de documentatie van [add-DnsServerConditionalForwarderZone](/powershell/module/dnsserver/add-dnsserverconditionalforwarderzone) voor meer informatie over het gebruik van DNS op **Windows Server 2016**...

Zodra u de on-premises DNS-server hebt geconfigureerd, kunt u gebruiken `nslookup` vanuit het on-premises netwerk om te controleren of u namen in het virtuele netwerk kunt omzetten. Het volgende voor beeld 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

In dit voor beeld wordt de on-premises DNS-server op 196.168.0.4 gebruikt om de naam van de aangepaste DNS-server op te lossen. Vervang het IP-adres door het voor de on-premises DNS-server. Vervang het `dnsproxy` adres door de Fully Qualified Domain name van de aangepaste DNS-server.

## <a name="optional-control-network-traffic"></a>Optioneel: netwerk verkeer regelen

U kunt netwerk beveiligings groepen (NSG) of door de gebruiker gedefinieerde routes (UDR) gebruiken om netwerk verkeer te beheren. Met Nsg's kunt u inkomend en uitgaand verkeer filteren, en het verkeer toestaan of weigeren. Met Udr's kunt u bepalen hoe verkeer loopt tussen bronnen in het virtuele netwerk, het internet en het on-premises netwerk.

> [!WARNING]  
> HDInsight vereist inkomende toegang tot specifieke IP-adressen in de Azure-Cloud en onbeperkte uitgaande toegang. Wanneer u Nsg's of Udr's gebruikt om verkeer te beheren, moet u de volgende stappen uitvoeren:

1. Zoek de IP-adressen voor de locatie die het virtuele netwerk bevat. Zie [vereiste IP-adressen](./hdinsight-management-ip-addresses.md)voor een lijst met vereiste ip's per locatie.

2. Voor de IP-adressen die u in stap 1 hebt geïdentificeerd, wordt binnenkomend verkeer van die IP-adressen toegestaan.

   * Als u __NSG__ gebruikt: __binnenkomend__ verkeer op poort __443__ toestaan voor de IP-adressen.
   * Als u __UDR__ gebruikt: Stel het type van de __volgende hop__ van de route naar __Internet__ in voor de IP-adressen.

Voor een voor beeld van het gebruik van Azure PowerShell of de Azure CLI voor het maken van Nsg's, raadpleegt u het document [HDInsight uitbreiden met Azure Virtual Networks](hdinsight-create-virtual-network.md#hdinsight-nsg) .

## <a name="create-the-hdinsight-cluster"></a>Het HDInsight-cluster maken

> [!WARNING]  
> U moet de aangepaste DNS-server configureren voordat u HDInsight in het virtuele netwerk installeert.

Volg de stappen in het [een hdinsight-cluster maken met behulp van het Azure Portal](./hdinsight-hadoop-create-linux-clusters-portal.md) -document om een hdinsight-cluster te maken.

> [!WARNING]  
> * Tijdens het maken van het cluster moet u de locatie kiezen die het virtuele netwerk bevat.
> * In het onderdeel __Geavanceerde instellingen__ van configuratie moet u het virtuele netwerk en het subnet selecteren dat u eerder hebt gemaakt.

## <a name="connecting-to-hdinsight"></a>Verbinding maken met HDInsight

De meeste documentatie op HDInsight veronderstelt dat u via internet toegang hebt tot het cluster. Bijvoorbeeld, dat u verbinding met het cluster kunt maken op `https://CLUSTERNAME.azurehdinsight.net`. Dit adres maakt gebruik van de open bare gateway, die niet beschikbaar is als u Nsg's of Udr's hebt gebruikt om de toegang vanaf internet te beperken.

Sommige documentatie bevat ook informatie over `headnodehost` het maken van verbinding met het cluster vanuit een SSH-sessie. Dit adres is alleen beschikbaar voor knoop punten in een cluster en kan niet worden gebruikt op clients die zijn verbonden via het virtuele netwerk.

Als u rechtstreeks verbinding wilt maken met HDInsight via het virtuele netwerk, gebruikt u de volgende stappen:

1. Gebruik een van de volgende methoden om de interne volledig gekwalificeerde domein namen van de HDInsight-cluster knooppunten te detecteren:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Zie de poorten die worden [gebruikt door Apache Hadoop Services in HDInsight](./hdinsight-hadoop-port-settings-for-services.md) -document voor het bepalen van de poort waarop een service beschikbaar is.

    > [!IMPORTANT]  
    > Sommige services die op de hoofd knooppunten worden gehost, zijn op één knoop punt tegelijk actief. Als u probeert toegang te krijgen tot een service op één hoofd knooppunt en dit mislukt, gaat u naar het andere hoofd knooppunt.
    >
    > Apache Ambari is bijvoorbeeld slechts actief op één hoofd knooppunt tegelijk. Als u probeert toegang te krijgen tot Ambari op één hoofd knooppunt en er wordt een 404-fout geretourneerd, wordt deze uitgevoerd op het andere hoofd knooppunt.

## <a name="next-steps"></a>Volgende stappen

* Zie [een implementatie van een virtueel netwerk plannen voor Azure HDInsight-clusters](./hdinsight-plan-virtual-network-deployment.md)voor meer informatie over het gebruik van HDInsight in een virtueel netwerk.

* Zie [overzicht van azure Virtual Network](../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele Azure-netwerken.

* Zie [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md)voor meer informatie over netwerk beveiligings groepen.

* Zie door de gebruiker [gedefinieerde routes en door sturen via IP](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over door de gebruiker gedefinieerde routes.