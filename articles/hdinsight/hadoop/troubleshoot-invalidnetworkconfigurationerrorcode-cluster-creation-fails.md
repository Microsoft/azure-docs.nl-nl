---
title: InvalidNetworkConfigurationErrorCode-fout-Azure HDInsight
description: Verschillende redenen voor mislukte cluster creaties met InvalidNetworkConfigurationErrorCode in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.openlocfilehash: 83d4819ecb1da91bda5fb4f1cb445bbc34fd007f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98926999"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Het maken van een cluster mislukt met InvalidNetworkConfigurationErrorCode in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

Als u fout code ziet `InvalidNetworkConfigurationErrorCode` met de beschrijving Virtual Network configuratie niet compatibel is met HDInsight-vereiste, duidt dit meestal op een probleem met de configuratie van het [virtuele netwerk](../hdinsight-plan-virtual-network-deployment.md) voor uw cluster. Op basis van de rest van de fout beschrijving, volgt u de onderstaande secties om het probleem op te lossen.

## <a name="hostname-resolution-failed"></a>' Omzetting van HostNamen mislukt '

### <a name="issue"></a>Probleem

Fout beschrijving bevat ' omzetting van hostnaam is mislukt '.

### <a name="cause"></a>Oorzaak

Deze fout wijst op een probleem met de aangepaste DNS-configuratie. DNS-servers binnen een virtueel netwerk kunnen DNS-query's door sturen naar recursieve resolvers van Azure om hostnamen in dat virtuele netwerk op te lossen (Zie [naam omzetting in virtuele netwerken](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voor meer informatie). Toegang tot recursieve resolvers van Azure wordt geboden via de 168.63.129.16 van het virtuele IP-adres. Dit IP-adres is alleen toegankelijk vanaf de Azure-Vm's. Het werkt dus niet als u een premises DNS-server gebruikt of als uw DNS-server een Azure-VM is die geen deel uitmaakt van het virtuele netwerk van het cluster.

### <a name="resolution"></a>Oplossing

1. SSH naar de virtuele machine die deel uitmaakt van het cluster en voer de opdracht uit `hostname -f` . Hiermee wordt de Fully Qualified Domain Name van de host geretourneerd (zoals `<host_fqdn>` beschreven in de onderstaande instructies).

1. Voer vervolgens de opdracht uit `nslookup <host_fqdn>` (bijvoorbeeld `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net` ). Als met deze opdracht de naam wordt omgezet in een IP-adres, betekent dit dat uw DNS-server goed werkt. In dit geval kunt u een ondersteunings aanvraag met HDInsight genereren en uw probleem onderzoeken. Neem in uw ondersteunings aanvraag de stappen voor het oplossen van problemen op die u hebt uitgevoerd. Dit helpt ons het probleem sneller op te lossen.

1. Als de bovenstaande opdracht geen IP-adres retourneert, voert u uit `nslookup <host_fqdn> 168.63.129.16` (bijvoorbeeld `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16` ). Als met deze opdracht het IP-adres kan worden omgezet, betekent dit dat de DNS-server de query niet doorstuurt naar de DNS van Azure, of geen VM is die deel uitmaakt van hetzelfde virtuele netwerk als het cluster.

1. Als u geen Azure-VM hebt die kan fungeren als aangepaste DNS-server in het virtuele netwerk van het cluster, moet u dit eerst toevoegen. Maak een VM in het virtuele netwerk, die wordt geconfigureerd als DNS-doorstuur server.

1. Wanneer u een VM in uw virtuele netwerk hebt geïmplementeerd, configureert u de DNS-doorstuur regels op deze VM. Stuur alle Idn's-naam omzettings aanvragen door naar 168.63.129.16, en de rest naar uw DNS-server. [Hier](../hdinsight-plan-virtual-network-deployment.md) volgt een voor beeld van deze installatie voor een aangepaste DNS-server.

1. Voeg het IP-adres van deze virtuele machine toe als eerste DNS-vermelding voor de Virtual Network DNS-configuratie.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>' Kan geen verbinding maken met Azure Storage account '

### <a name="issue"></a>Probleem

Fout beschrijving bevat ' kan geen verbinding maken met Azure Storage account ' of ' kan geen verbinding maken met Azure SQL '.

### <a name="cause"></a>Oorzaak

Azure Storage en SQL hebben geen vaste IP-adressen, dus moeten uitgaande verbindingen met alle Ip's worden toegestaan om toegang tot deze services toe te staan. De exacte stappen zijn afhankelijk van het feit of u een netwerk beveiligings groep (NSG) of User-Defined regels (UDR) hebt ingesteld. Raadpleeg de sectie over het [beheren van netwerk verkeer met HDInsight met netwerk beveiligings groepen en door de gebruiker gedefinieerde routes](../control-network-traffic.md) voor meer informatie over deze configuraties.

### <a name="resolution"></a>Oplossing

* Als uw cluster gebruikmaakt van een [netwerk beveiligings groep (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Ga naar de Azure Portal en Identificeer de NSG die is gekoppeld aan het subnet waar het cluster wordt geïmplementeerd. Geef in de sectie **uitgaande beveiligings regels** uitgaande toegang tot internet zonder enige beperking (Houd er rekening mee dat een kleiner **prioriteits** nummer een hogere prioriteit heeft). Controleer ook in het gedeelte **subnets** of deze NSG wordt toegepast op het subnet van het cluster.

* Als uw cluster gebruikmaakt van een door de [gebruiker gedefinieerde routes (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Ga naar de Azure Portal en Identificeer de route tabel die is gekoppeld aan het subnet waar het cluster wordt geïmplementeerd. Wanneer u de route tabel voor het subnet hebt gevonden, inspecteert u de sectie **routes** hierin.

    Als er routes zijn gedefinieerd, moet u ervoor zorgen dat er routes zijn voor IP-adressen voor de regio waarin het cluster is geïmplementeerd en dat de **NextHopType** voor elke route **Internet** is. Er moet een route worden gedefinieerd voor elk vereiste IP-adres dat in het voor noemde artikel wordt beschreven.

## <a name="failed-to-establish-an-outbound-connection-from-the-cluster-for-the-communication-with-the-hdinsight-resource-provider-please-ensure-that-outbound-connectivity-is-allowed"></a>"Kan geen uitgaande verbinding van het cluster tot stand brengen voor de communicatie met de HDInsight-resource provider. Controleer of de uitgaande connectiviteit is toegestaan.

### <a name="issue"></a>Probleem

Fout beschrijving bevat ' kan geen uitgaande verbinding met het cluster tot stand brengen voor de communicatie met de HDInsight-resource provider. Controleer of de uitgaande connectiviteit is toegestaan.

### <a name="cause"></a>Oorzaak

Bij het gebruik van persoonlijke gekoppelde HDInsight-clusters moet uitgaande toegang vanuit het cluster worden geconfigureerd zodat verbindingen kunnen worden gemaakt met de HDInsight-resource provider.

### <a name="resolution"></a>Oplossing

* Om dit probleem op te lossen, raadpleegt u de stappen voor het instellen van de HDInsight-persoonlijke koppelingen tijdens de installatie van een [persoonlijke](../hdinsight-private-link.md)
---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"De configuratie van het virtuele netwerk is niet compatibel met de HDInsight-vereiste"

### <a name="issue"></a>Probleem

Fout beschrijvingen bevatten de volgende berichten:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Oorzaak

Er is waarschijnlijk een probleem met de aangepaste DNS-installatie.

### <a name="resolution"></a>Oplossing

Controleer of 168.63.129.16 zich in de aangepaste DNS-keten bevindt. DNS-servers binnen een virtueel netwerk kunnen DNS-query's door sturen naar recursieve resolvers van Azure om hostnamen in dat virtuele netwerk op te lossen. Zie [naam omzetting in virtuele netwerken](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)voor meer informatie. Toegang tot recursieve resolvers van Azure wordt geboden via de 168.63.129.16 van het virtuele IP-adres.

1. Gebruik de [ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Voer de volgende opdracht uit:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    De uitvoer moet er ongeveer als volgt uitzien:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Kies een van de volgende stappen op basis van het resultaat:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 is niet in deze lijst

**Optie 1**  
Voeg 168.63.129.16 toe als de eerste aangepaste DNS voor het virtuele netwerk met behulp van de stappen die worden beschreven in [een virtueel netwerk voor Azure HDInsight plannen](../hdinsight-plan-virtual-network-deployment.md). Deze stappen zijn alleen van toepassing als uw aangepaste DNS-server wordt uitgevoerd op Linux.

**Optie 2**  
Implementeer een DNS-Server-VM voor het virtuele netwerk. Dit omvat de volgende stappen:

* Maak een VM in het virtuele netwerk, dat wordt geconfigureerd als DNS-doorstuur server (dit kan een Linux-of Windows-VM zijn).
* Configureer DNS-doorstuur regels op deze VM (Stuur alle Idn's-naam omzettings aanvragen naar 168.63.129.16, en de rest naar uw DNS-server).
* Voeg het IP-adres van deze VM toe als eerste DNS-vermelding voor Virtual Network DNS-configuratie.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 staat in de lijst

In dit geval kunt u een ondersteunings aanvraag met HDInsight maken en uw probleem onderzoeken. Neem het resultaat van de onderstaande opdrachten op in uw ondersteunings aanvraag. Dit helpt ons het probleem sneller te onderzoeken en op te lossen.

Bewerk en voer de volgende handelingen uit vanuit een SSH-sessie op het hoofd knooppunt:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```
### <a name="cause"></a>Oorzaak

Een andere oorzaak van deze `InvalidNetworkConfigurationErrorCode` fout code is het gebruik van de gedeprecieerde para meter `EnableVmProtection` in Power shell of een Azure-Runbook.

### <a name="resolution"></a>Oplossing

Gebruik de geldige para meters voor `Get-AzVirtualNetwork` zoals beschreven in de [AZ Power shell SDK](/powershell/module/az.network/get-azvirtualnetwork)

---

### <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
