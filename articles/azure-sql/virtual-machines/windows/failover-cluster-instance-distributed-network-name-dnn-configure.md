---
title: DNN configureren voor het failover-cluster exemplaar
description: Meer informatie over het configureren van een gedistribueerde netwerk naam (DNN) voor het door sturen van verkeer naar uw SQL Server op het Azure VM-failovercluster (FCI).
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
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8549592ace00e712929ebc76045a32531b9db659
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358313"
---
# <a name="configure-a-dnn-for-failover-cluster-instance"></a>Een DNN configureren voor het failover-cluster exemplaar
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In azure Virtual Machines stuurt de gedistribueerde netwerk naam (DNN) verkeer naar de juiste geclusterde bron. Het biedt een eenvoudige manier om verbinding te maken met het SQL Server failovercluster (FCI) dan de naam van het virtuele netwerk (VNN), zonder dat er een Azure Load Balancer nodig is. 

In dit artikel leert u hoe u een DNN-bron configureert om verkeer te routeren naar uw failovercluster-exemplaar met SQL Server op Azure-Vm's voor hoge Beschik baarheid en herstel na nood gevallen (HADR). 

De functie DNN is momenteel alleen beschikbaar op SQL Server 2019 CU2 en hoger op Windows Server 2016 en hoger. 

Voor een alternatieve connectiviteits optie kunt u in plaats daarvan een [naam en Azure Load Balancer voor het virtuele netwerk](failover-cluster-instance-vnn-azure-load-balancer-configure.md) overwegen. 

## <a name="overview"></a>Overzicht

De naam van het gedistribueerde netwerk (DNN) vervangt de naam van het virtuele netwerk (VNN) als het verbindings punt wanneer het wordt gebruikt met een [Always on-failover-cluster exemplaar op SQL Server vm's](failover-cluster-instance-overview.md). Hiermee wordt de nood zaak van een Azure Load Balancer routerings verkeer naar de VNN, waardoor implementatie, onderhoud en verbetering van de failover wordt vereenvoudigd. 

Met een FCI-implementatie bestaat de VNN nog steeds, maar de client maakt verbinding met de DNN DNS-naam in plaats van de naam van de VNN. 

## <a name="prerequisites"></a>Vereisten 

Voordat u de stappen in dit artikel hebt voltooid, hebt u het volgende nodig:

- SQL Server 2019 op CU2 of hoger, op Windows Server 2016 en hoger
- Heeft besloten dat de naam van het gedistribueerde netwerk de juiste [connectiviteits optie is voor uw HADR-oplossing](hadr-cluster-best-practices.md#connectivity).
- De exemplaren van uw [failovercluster](failover-cluster-instance-overview.md)zijn geconfigureerd. 
- De nieuwste versie van [Power shell](/powershell/azure/install-az-ps)is geïnstalleerd. 

## <a name="create-dnn-resource"></a>DNN-resource maken 

De DNN-resource wordt gemaakt in dezelfde cluster groep als de SQL Server FCI. Gebruik Power shell om de DNN-resource in de FCI-cluster groep te maken. 

Met de volgende Power shell-opdracht wordt een DNN-resource toegevoegd aan de FCI-cluster groep SQL Server met de resource naam `<dnnResourceName>` . De resource naam wordt gebruikt om een resource uniek te identificeren. Gebruik een van de voor deel en uniek in het cluster. Het resource type moet zijn `Distributed Network Name` . 

De `-Group` waarde moet de naam zijn van de cluster groep die overeenkomt met de SQL Server FCI waaraan u de naam van het gedistribueerde netwerk wilt toevoegen. Voor een standaard exemplaar is de standaard indeling `SQL Server (MSSQLSERVER)` . 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

`dnn-demo`Gebruik bijvoorbeeld de volgende Power shell-opdracht om uw DNN-resource te maken voor een standaard SQL Server FCI:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>DNS-naam van cluster DNN instellen

Stel de DNS-naam voor de DNN-resource in het cluster in. Het cluster gebruikt deze waarde vervolgens om het verkeer om te leiden naar het knoop punt dat momenteel de SQL Server FCI wordt gehost. 

Clients gebruiken de DNS-naam om verbinding te maken met de SQL Server FCI. U kunt een unieke waarde kiezen. Als u al een bestaande FCI hebt en client verbindings reeksen niet wilt bijwerken, kunt u de DNN configureren voor het gebruik van de huidige VNN die clients al gebruiken. Hiervoor moet u [de naam van de VNN wijzigen](#rename-the-vnn) voordat u de DNN in DNS instelt.

Gebruik deze opdracht om de DNS-naam voor uw DNN in te stellen: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

De `DNSName` waarde is wat clients gebruiken om verbinding te maken met de SQL Server FCI. `FCIDNN`Gebruik bijvoorbeeld de volgende Power shell-opdracht voor clients om verbinding mee te maken:

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

Clients voeren nu `FCIDNN` hun Connection String in wanneer er verbinding wordt gemaakt met de SQL Server FCI. 

   > [!WARNING]
   > Verwijder de huidige naam van het virtuele netwerk (VNN) niet omdat het een benodigd onderdeel van de FCI-infra structuur is. 


### <a name="rename-the-vnn"></a>De naam van de VNN wijzigen 

Als u een bestaande naam voor een virtueel netwerk hebt en u wilt dat clients deze waarde blijven gebruiken om verbinding te maken met de SQL Server FCI, moet u de naam van de huidige VNN wijzigen in een waarde voor de tijdelijke aanduiding. Nadat de naam van de huidige VNN is gewijzigd, kunt u de DNS-naam waarde voor de DNN instellen op de VNN. 

Er zijn enkele beperkingen van toepassing voor het wijzigen van de naam van de VNN. Zie [de naam van een FCI wijzigen](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance)voor meer informatie.

Als het gebruik van de huidige VNN niet nodig is voor uw bedrijf, kunt u deze sectie overs Laan. Nadat u de naam van de VNN hebt gewijzigd, moet u [de DNS-naam van het cluster DNN instellen](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>DNN-resource instellen op Internet

Nadat uw DNN-resource de juiste naam heeft en u de DNS-naam waarde in het cluster hebt ingesteld, gebruikt u Power shell om de DNN-bron in het cluster online in te stellen: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

`dnn-demo`Gebruik bijvoorbeeld de volgende Power shell-opdracht om de DNN-resource te starten: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Mogelijke eigen aren configureren

Het cluster koppelt standaard de DNS-naam DNN aan alle knoop punten in het cluster. Knoop punten in het cluster die geen deel uitmaken van de SQL Server FCI moeten echter worden uitgesloten van de lijst met DNN-mogelijke eigen aars. 

Voer de volgende stappen uit om mogelijke eigen aars bij te werken:

1. Ga naar uw DNN-resource in Failoverclusterbeheer. 
1. Klik met de rechter muisknop op de resource DNN en selecteer **Eigenschappen**. 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="Snelmenu voor de DNN-resource met de opdracht eigenschappen gemarkeerd.":::

1. Schakel het selectie vakje uit voor knoop punten die geen deel uitmaken van het failover-cluster exemplaar. De lijst met mogelijke eigen aars voor de DNN-resource moet overeenkomen met de lijst met mogelijke eigen aars voor de SQL Server-exemplaar bron. Als er bijvoorbeeld wordt aangenomen dat DATA3 geen deel uitmaakt van de FCI, is de volgende afbeelding een voor beeld van het verwijderen van DATA3 uit de lijst met mogelijke eigen aars voor de DNN-resource: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="Schakel het selectie vakje uit naast de knoop punten die geen deel uitmaken van de FCI voor mogelijke eigen aren van de DNN-resource":::

1. Selecteer **OK** om uw wijzigingen op te slaan. 


## <a name="restart-sql-server-instance"></a>SQL Server exemplaar opnieuw starten 

Gebruik Failoverclusterbeheer om het SQL Server exemplaar opnieuw op te starten. Volg deze stappen:

1. Ga naar de SQL Server-Resource in Failoverclusterbeheer.
1. Klik met de rechter muisknop op de SQL Server Resource en zet deze offline. 
1. Nadat alle gekoppelde resources offline zijn, klikt u met de rechter muisknop op de SQL Server Resource en brengt u deze weer online. 

## <a name="update-connection-string"></a>connection string bijwerken

Als u wilt zorgen voor snelle connectiviteit bij failover, voegt `MultiSubnetFailover=True` u toe aan de Connection String als de versie van de SQL-Client ouder is dan 4.6.1. 

Als de DNN niet de oorspronkelijke VNN gebruikt, moeten SQL-clients die verbinding maken met de SQL Server FCI hun connection string ook bijwerken naar de DNS-naam van DNN. U kunt dit voor komen door de DNS-naam waarde bij te werken als de naam van de VNN. Maar u moet eerst [de bestaande VNN vervangen door een tijdelijke aanduiding](#rename-the-vnn) . 

## <a name="test-failover"></a>Testfailover


Testfailover van de geclusterde bron om de cluster functionaliteit te valideren. 


Voer de volgende stappen uit om de failover te testen: 

1. Maak verbinding met een van de SQL Server cluster knooppunten met behulp van RDP.
1. Open **Failoverclusterbeheer**. Selecteer **Rollen**. U ziet welk knoop punt eigenaar is van de SQL Server rol FCI.
1. Klik met de rechter muisknop op de SQL Server FCI rol. 
1. Selecteer **verplaatsen** en selecteer vervolgens **best mogelijke knoop punt**.

**Failoverclusterbeheer** toont de rol en de bijbehorende resources gaan offline. De resources worden vervolgens verplaatst en weer online in het andere knoop punt.

## <a name="test-connectivity"></a>Connectiviteit testen

Als u de verbinding wilt testen, meldt u zich aan bij een andere virtuele machine in hetzelfde virtuele netwerk. Open **SQL Server Management Studio** en maak verbinding met de SQL Server FCI door de DNS-naam van DNN te gebruiken.

Als dat het geval is, kunt u [SQL Server Management Studio downloaden](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="avoid-ip-conflict"></a>IP-conflict vermijden

Dit is een optionele stap om te voor komen dat het VIP-adres (virtuele IP) dat door de FCI-resource wordt gebruikt, wordt toegewezen aan een andere resource in azure als een duplicaat. 

Hoewel klanten nu de DNN gebruiken om verbinding te maken met de SQL Server FCI, kunnen de naam van het virtuele netwerk (VNN) en het virtuele IP-adres niet worden verwijderd omdat ze de vereiste onderdelen van de FCI-infra structuur zijn. Omdat er echter geen load balancer voor het reserveren van het virtuele IP-adres in azure, is er een risico dat een andere bron in het virtuele netwerk hetzelfde IP-adres krijgt als het virtuele IP-adres dat door de FCI wordt gebruikt. Dit kan mogelijk leiden tot een dubbel probleem met een IP-conflict. 

Configureer een APIPA-adres of een speciale netwerk adapter om het IP-adres te reserveren. 

### <a name="apipa-address"></a>APIPA-adres

Om te voor komen dat dubbele IP-adressen worden gebruikt, configureert u een APIPA-adres (ook wel bekend als een link-local adres). Voer hiertoe de volgende opdrachten uit:

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

In deze opdracht staat ' virtueel IP-adres ' voor de naam van de geclusterde VIP-adres bron en ' 169.254.1.1 ' is het APIPA-adres dat is gekozen voor het VIP-adres. Kies het adres dat het beste bij uw bedrijf past. Stel in dat `OverrideAddressMatch=1` het IP-adres zich op elk netwerk bevindt, met inbegrip van de APIPA-adres ruimte. 

### <a name="dedicated-network-adapter"></a>Speciale netwerk adapter

U kunt ook een netwerk adapter in azure configureren om het IP-adres te reserveren dat door de bron van het virtuele IP-adres wordt gebruikt. Dit maakt echter gebruik van het adres in de adres ruimte van het subnet en de extra belasting van de netwerk adapter wordt niet gebruikt voor een ander doel.

## <a name="limitations"></a>Beperkingen

- Op dit moment wordt een DNN met FCI alleen ondersteund voor SQL Server 2019 CU2 en hoger op Windows Server 2016 en hoger. 
- Er zijn mogelijk meer overwegingen bij het werken met andere SQL Server functies en een FCI met een DNN. Zie [FCI met DNN-interoperabiliteit](failover-cluster-instance-dnn-interoperability.md)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over SQL Server-HADR-functies in azure [beschikbaarheids groepen](availability-group-overview.md) en een [failover-cluster exemplaar](failover-cluster-instance-overview.md). U kunt ook de [Aanbevolen procedures](hadr-cluster-best-practices.md) voor het configureren van uw omgeving voor hoge Beschik baarheid en herstel na nood gevallen leren. 


