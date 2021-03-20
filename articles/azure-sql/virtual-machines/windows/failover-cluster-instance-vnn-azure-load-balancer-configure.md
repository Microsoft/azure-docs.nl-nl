---
title: Azure Load Balancer een VNN van een failovercluster configureren
description: Meer informatie over het configureren van een Azure Load Balancer voor het routeren van verkeer naar de naam van het virtuele netwerk (VNN) voor uw failovercluster (FCI) met SQL Server op Azure-Vm's voor hoge Beschik baarheid en herstel na nood gevallen (HADR).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5670a29e86eb201a707e5ceef28043aafe4839d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97357973"
---
# <a name="configure-azure-load-balancer-for-failover-cluster-instance-vnn"></a>Azure Load Balancer configureren voor VNN van het failovercluster
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In azure Virtual Machines gebruiken clusters een load balancer om een IP-adres op te slaan dat op één cluster knooppunt tegelijk moet zijn. In deze oplossing bevat de load balancer het IP-adres voor de naam van het virtuele netwerk (VNN) dat wordt gebruikt door de geclusterde resource in Azure. 

In dit artikel leert u hoe u een load balancer kunt configureren met behulp van de Azure Load Balancer-service. Het load balancer stuurt het verkeer naar uw [FCI (failover cluster instance)](failover-cluster-instance-overview.md) door SQL Server op Azure-vm's voor hoge Beschik baarheid en herstel na nood gevallen (HADR). 

Voor een alternatieve connectiviteits optie voor SQL Server 2019 CU2 en hoger kunt u een [gedistribueerde netwerk naam](failover-cluster-instance-distributed-network-name-dnn-configure.md) gebruiken in plaats van een vereenvoudigde configuratie en een verbeterde failover. 


## <a name="prerequisites"></a>Vereisten

Voordat u de stappen in dit artikel hebt voltooid, hebt u het volgende nodig:

- Heeft besloten dat Azure Load Balancer de juiste [connectiviteits optie is voor uw HADR-oplossing](hadr-cluster-best-practices.md#connectivity).
- Uw listener-of [failover-cluster exemplaren](failover-cluster-instance-overview.md)voor de [beschikbaarheids groep](availability-group-overview.md) zijn geconfigureerd. 
- De nieuwste versie van [Power shell](/powershell/azure/install-az-ps)is geïnstalleerd. 


## <a name="create-load-balancer"></a>Load balancer maken

Gebruik de [Azure Portal](https://portal.azure.com) om de Load Balancer te maken:

1. Ga in het Azure Portal naar de resource groep die de virtuele machines bevat.

1. Selecteer **Toevoegen**. Zoek in azure Marketplace naar **Load Balancer**. Selecteer **Load Balancer**.

1. Selecteer **Maken**.

1. Stel de load balancer in met behulp van de volgende waarden:

   - **Abonnement**: uw Azure-abonnement.
   - **Resource groep**: de resource groep die uw virtuele machines bevat.
   - **Naam**: een naam die de Load Balancer aanduidt.
   - **Regio**: de Azure-locatie waar uw virtuele machines zich bevinden.
   - **Type**: ofwel openbaar of privé. Er kan vanuit het virtuele netwerk toegang worden verkregen tot een persoonlijke load balancer. De meeste Azure-toepassingen kunnen een privé-load balancer gebruiken. Als uw toepassing rechtstreeks via internet toegang moet hebben tot SQL Server, gebruik dan een open bare load balancer.
   - **SKU**: Standard.
   - **Virtueel netwerk**: hetzelfde netwerk als de virtuele machines.
   - **IP-adres toewijzing**: statisch. 
   - **Privé-IP-adres**: het IP-adres dat u hebt toegewezen aan de geclusterde netwerk bron.

   De volgende afbeelding toont de gebruikers interface **Create Load Balancer** :

   ![De load balancer instellen](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>Back-end-groep configureren

1. Ga terug naar de Azure-resource groep die de virtuele machines bevat en zoek de nieuwe load balancer. Mogelijk moet u de weer gave van de resource groep vernieuwen. Selecteer de load balancer.

1. Selecteer **back-endservers** en selecteer vervolgens **toevoegen**.

1. Koppel de back-endpool met de beschikbaarheidsset die de virtuele machines bevat.

1. Onder **IP-configuraties** voor het doelnet **werk selecteert u virtuele machine** en kiest u de virtuele machines die als cluster knooppunten zullen worden beschouwd. Zorg ervoor dat u alle virtuele machines opneemt die als host moeten fungeren voor de FCI of beschikbaarheids groep.

1. Selecteer **OK** om de back-endpool te maken.

## <a name="configure-health-probe"></a>Status test configureren

1. Selecteer in het deel venster load balancer **status controles**.

1. Selecteer **Toevoegen**.

1. Stel in het deel venster **status test toevoegen** de volgende Health probe para meters in: <span id="probe"></span>

   - **Naam**: een naam voor de status test.
   - **Protocol**: TCP.
   - **Poort**: de poort die u hebt gemaakt in de firewall voor de status test [wanneer de virtuele machine wordt voor bereid](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1). In dit artikel gebruikt het voor beeld TCP-poort `59999` .
   - **Interval**: 5 seconden.
   - **Drempel waarde voor onjuiste status**: 2 opeenvolgende fouten.

1. Selecteer **OK**.

## <a name="set-load-balancing-rules"></a>Taakverdelings regels instellen

1. Selecteer **regels voor taak verdeling** in het deel venster Load Balancer.

1. Selecteer **Toevoegen**.

1. De para meters voor de taakverdelings regel instellen:

   - **Naam**: een naam voor de regels voor taak verdeling.
   - **Frontend-IP-adres**: het IP-adres voor de geclusterde netwerk resource van de SQL Server FCI of de AG-listener.
   - **Poort**: de SQL Server TCP-poort. De standaard instantie poort is 1433.
   - **Back-end-poort**: dezelfde poort als de **poort** waarde wanneer u **zwevende IP (direct server return)** inschakelt.
   - **Back-end-pool**: de naam van de back-endserver die u eerder hebt geconfigureerd.
   - **Health probe**: de status test die u eerder hebt geconfigureerd.
   - **Sessie persistentie**: geen.
   - **Time-out voor inactiviteit (minuten)**: 4.
   - **Zwevend IP (direct server return)**: ingeschakeld.

1. Selecteer **OK**.

## <a name="configure-cluster-probe"></a>Cluster testen configureren

Stel de para meter cluster probe Port in Power shell in.

Als u de para meter voor de cluster test poort wilt instellen, werkt u de variabelen in het volgende script bij met waarden uit uw omgeving. Verwijder de punt haken ( `<` en `>` ) van het script.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

In de volgende tabel worden de waarden beschreven die u moet bijwerken:


|**Waarde**|**Beschrijving**|
|---------|---------|
|`Cluster Network Name`| De naam van het Windows Server-failovercluster voor het netwerk. Klik in **Failoverclusterbeheer**  >  **netwerken** met de rechter muisknop op het netwerk en selecteer **Eigenschappen**. De juiste waarde ziet u op het tabblad **Algemeen**, bij **Naam**.|
|`SQL Server FCI/AG listener IP Address Resource Name`|De resource naam voor het IP-adres van de SQL Server-FCI of AG-listener. Klik in **Failoverclusterbeheer**  >  **rollen** onder de rol SQL Server FCI onder **Server naam** met de rechter muisknop op de IP-adres bron en selecteer **Eigenschappen**. De juiste waarde ziet u op het tabblad **Algemeen**, bij **Naam**.|
|`ILBIP`|Het IP-adres van de interne load balancer (ILB). Dit adres wordt geconfigureerd in de Azure Portal als het front-end-adres van de ILB. Dit is ook het IP-adres van de SQL Server FCI. U kunt deze in **Failoverclusterbeheer** vinden op dezelfde eigenschappen pagina waar u de hebt opgeslagen `<SQL Server FCI/AG listener IP Address Resource Name>` .|
|`nnnnn`|De test poort die u hebt geconfigureerd in de status test van de load balancer. Elke ongebruikte TCP-poort is geldig.|
|SubnetMask| Het subnetmasker voor de cluster parameter. Dit moet het TCP IP-broadcast adres zijn: `255.255.255.255` .| 


Nadat u de cluster test hebt ingesteld, kunt u alle cluster parameters in Power shell zien. Voer dit script uit:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>Testfailover


Testfailover van de geclusterde bron om de cluster functionaliteit te valideren. 

Voer de volgende stappen uit:

1. Maak verbinding met een van de SQL Server cluster knooppunten met behulp van RDP.
1. Open **Failoverclusterbeheer**. Selecteer **Rollen**. U ziet welk knoop punt eigenaar is van de SQL Server rol FCI.
1. Klik met de rechter muisknop op de SQL Server FCI rol. 
1. Selecteer **verplaatsen** en selecteer vervolgens **best mogelijke knoop punt**.

**Failoverclusterbeheer** toont de rol en de bijbehorende resources gaan offline. De resources worden vervolgens verplaatst en weer online in het andere knoop punt.


## <a name="test-connectivity"></a>Connectiviteit testen

Als u de verbinding wilt testen, meldt u zich aan bij een andere virtuele machine in hetzelfde virtuele netwerk. Open **SQL Server Management Studio** en maak verbinding met de naam van de SQL Server FCI. 

>[!NOTE]
>Als dat het geval is, kunt u [SQL Server Management Studio downloaden](/sql/ssms/download-sql-server-management-studio-ssms).



## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over SQL Server-HADR-functies in azure [beschikbaarheids groepen](availability-group-overview.md) en een [failover-cluster exemplaar](failover-cluster-instance-overview.md). U kunt ook de [Aanbevolen procedures](hadr-cluster-best-practices.md) voor het configureren van uw omgeving voor hoge Beschik baarheid en herstel na nood gevallen leren. 



