---
title: Functie compatibiliteit met SQL Server FCI & DNN
description: 'Meer informatie over de aanvullende overwegingen bij het werken met bepaalde SQL Server functies en een gedistribueerde netwerk naam (DNN) met een failover-cluster exemplaar op SQL Server op virtuele machines van Azure. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358211"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>Functie compatibiliteit met SQL Server FCI & DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Er zijn bepaalde SQL Server functies die afhankelijk zijn van een in code vastgelegd virtueel netwerk naam (VNN). Wanneer u de DNN-resource (Distributed Network name) gebruikt met uw failovercluster en SQL Server op Azure-Vm's, zijn er enkele aanvullende overwegingen. 

In dit artikel leert u hoe u de netwerk alias kunt configureren bij gebruik van de DNN-resource, en welke SQL Server-functies extra aandacht vereisen.

## <a name="create-network-alias-fci"></a>Netwerk alias maken (FCI)

Sommige Server onderdelen zijn afhankelijk van een in code vastgelegde VNN-waarde en vereisen een netwerk alias waarmee de VNN wordt toegewezen aan de DNN DNS-naam om correct te werken. Volg de stappen in [een server alias maken](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) om een alias te maken waarmee de VNN wordt toegewezen aan de DNS-naam van DNN. 

Voor een standaard exemplaar kunt u de VNN rechtstreeks toewijzen aan de DNS-naam van DNN, zodat de DNS-naam VNN = DNN.
Als de naam van de VNN bijvoorbeeld is, de naam van `FCI1` `MSSQLSERVER` het exemplaar en de DNN `FCI1DNN` (clients die eerder zijn verbonden met `FCI` , en nu verbinding maken met `FCI1DNN` ), wijst u de VNN `FCI1` toe aan de DNN `FCI1DNN` . 

Voor een benoemd exemplaar moet de toewijzing van de netwerk alias worden uitgevoerd voor de volledige instantie `VNN\Instance`  =  `DNN\Instance` . Als de naam van de VNN bijvoorbeeld is, de naam van `FCI1` `instA` het exemplaar en de DNN `FCI1DNN` (clients die eerder zijn verbonden met `FCI1\instA` , en nu verbinding maken met `FCI1DNN\instaA` ), wijst u de VNN `FCI1\instaA` toe aan de DNN `FCI1DNN\instaA` . 



## <a name="client-drivers"></a>Clientstuurprogramma's

Voor ODBC-, OLEDB-, ADO.NET-, JDBC-, PHP-en Node.js-Stuur Programma's moeten gebruikers expliciet de DNS-naam van DNN opgeven als server naam in de connection string. Als u wilt zorgen voor snelle connectiviteit bij failover, voegt `MultiSubnetFailover=True` u toe aan de Connection String als de SQL-Client dit ondersteunt. 

## <a name="tools"></a>Hulpprogramma's

Gebruikers van [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [Sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)en [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) moeten expliciet de DNS-naam van DNN opgeven als server naam in de Connection String. 

## <a name="availability-groups-and-fci"></a>Beschikbaarheids groepen en FCI

U kunt een AlwaysOn-beschikbaarheids groep configureren met behulp van een failover-cluster exemplaar als een van de replica's. In deze configuratie moet de URL voor het spie gelen van eind punten voor de replica van FCI de FCI-DNN gebruiken. Als de FCI wordt gebruikt als een alleen-lezen replica, moet de FCI DNN ook worden gebruikt voor de alleen-lezen routering naar de replica van de FCI. 

De notatie voor het mirroring-eind punt is: `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` . 

Als de DNS-naam van uw DNN bijvoorbeeld is `dnnlsnr` en `5022` de poort is van het spiegel eindpunt van de FCI, is het code fragment Transact-SQL (T-SQL) voor het maken van de eind punt-URL er als volgt uit te zien: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

De indeling voor de alleen-lezen routerings-URL is ook: `TCP://<DNN DNS name>:<SQL Server instance port>` . 

Als de DNS-naam van uw DNN bijvoorbeeld is `dnnlsnr` , en `1444` de poort is die wordt gebruikt door het alleen-lezen doel SQL Server FCI, ziet u het code fragment T-SQL om de alleen-lezen routerings-URL te maken, zoals: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

U kunt de poort in de URL weglaten als dit de standaard 1433-poort is. Configureer voor een benoemd exemplaar een statische poort voor het benoemde exemplaar en geef deze op in de routerings-URL met het kenmerk alleen-lezen.  

## <a name="replication"></a>Replicatie

Replicatie heeft drie onderdelen: Publisher, Distributor, Subscriber. Elk van deze onderdelen kan een failover-cluster exemplaar zijn. Omdat de FCI VNN intensief wordt gebruikt in de replicatie Configuratie, zowel expliciet als impliciet, kan de replicatie alleen worden uitgevoerd als er een netwerk alias is die de VNN aan de DNN toewijst. 

Blijf de naam van de VNN gebruiken als de FCI-naam binnen replicatie, maar maak een netwerk alias in de volgende externe situaties *voordat u de replicatie configureert*:

| **Replicatie onderdeel (FCI met DNN)** | **Extern onderdeel** | **Toewijzing van netwerk alias** | **Server met netwerk toewijzing**| 
|---------|---------|---------|-------- | 
|Publisher | Verdeler | Publisher VNN naar Publisher DNN| Verdeler| 
|Verdeler|Gehaald |Distributeur VNN naar Distributor DNN| Gehaald | 
|Verdeler|Publisher | Distributeur VNN naar Distributor DNN | Publisher| 
|Gehaald| Verdeler| VNN voor abonnee-DNN | Verdeler| 

Stel dat u een uitgever hebt die als FCI is geconfigureerd met behulp van DNN in een replicatie topologie en dat de Distributor extern is. In dit geval maakt u een netwerk alias op de Distributor-server om de uitgever VNN toe te wijzen aan de uitgever DNN: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="Configureer de DNS-naam van DNN als de netwerk alias met behulp van SQL Server Configuration Manager." :::

Gebruik de volledige exemplaar naam voor een benoemd exemplaar, zoals in het volgende voor beeld: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="Gebruik de volledige naam van het exemplaar bij het configureren van een netwerk alias voor een benoemd exemplaar." :::

## <a name="database-mirroring"></a>Databasespiegeling

U kunt het spie gelen van data bases met een FCI configureren als partner voor database spiegeling. Configureer het met behulp van [Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) in plaats van de SQL Server Management Studio GUI. Met T-SQL zorgt u ervoor dat het data base mirroring-eind punt wordt gemaakt met behulp van de DNN in plaats van de VNN. 

Als de DNS-naam van uw DNN bijvoorbeeld is `dnnlsnr` en het eind punt voor database spiegeling 7022 is, configureert het volgende T-SQL-code fragment de partner voor database spiegeling: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

Voor client toegang kan de eigenschap van de **failover-partner** de data base mirroring-failover verwerken, maar geen FCI-failover. 

## <a name="msdtc"></a>MSDTC

De FCI kan deel nemen aan gedistribueerde trans acties gecoördineerd door micro soft Distributed Transaction Coordinator (MSDTC). Hoewel zowel geclusterde MSDTC als lokale MSDTC worden ondersteund met FCI DNN in azure, is er nog steeds een load balancer nodig voor geclusterde MSDTC. De DNN die is gedefinieerd in de FCI, vervangt niet de Azure Load Balancer vereiste voor de geclusterde MSDTC in Azure. 

## <a name="filestream"></a>-

Hoewel FileStream wordt ondersteund voor een data base in een FCI, kan toegang tot FileStream of bestands tabel met behulp van File System-Api's met DNN niet worden ondersteund. 

## <a name="linked-servers"></a>Gekoppelde servers

Het gebruik van een gekoppelde server met een FCI-DNN wordt ondersteund. Gebruik de DNN rechtstreeks voor het configureren van een gekoppelde server of gebruik een netwerk alias om de VNN toe te wijzen aan de DNN. 


`dnnlsnr` `insta1` Gebruik bijvoorbeeld de volgende Transact-SQL-opdracht (T-SQL) om een gekoppelde server te maken met DNN DNS-naam voor het benoemde exemplaar:

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

U kunt ook de gekoppelde server maken met behulp van de naam van het virtuele netwerk (VNN), maar dan moet u vervolgens een netwerk alias definiëren om de VNN toe te wijzen aan de DNN. 

`insta1` `vnnname` `dnnlsnr` Gebruik bijvoorbeeld de volgende Transact-SQL-opdracht (T-SQL) om een gekoppelde server te maken met behulp van de VNN voor de naam van het exemplaar, de naam van de VNN en de DNN.

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

Maak vervolgens een netwerk alias om aan toe te wijzen `vnnname\insta1` `dnnlsnr\insta1` . 



## <a name="frequently-asked-questions"></a>Veelgestelde vragen


- Welke SQL Server versie brengt DNN-ondersteuning over? 

   SQL Server 2019 CU2 en hoger.

- Wat is de verwachte failover-tijd wanneer DNN wordt gebruikt?

   Voor DNN is de failover-tijd alleen de FCI failover-tijd, zonder dat er een tijd wordt toegevoegd (zoals een test tijd wanneer u Azure Load Balancer gebruikt).

- Zijn er versie vereisten voor SQL-clients om DNN te ondersteunen met OLEDB en ODBC?

   U wordt aangeraden `MultiSubnetFailover=True` Connection String ondersteuning voor DNN te bieden. Het is beschikbaar vanaf SQL Server 2012 (11. x).

- Zijn er SQL Server configuratie wijzigingen vereist zodat ik DNN gebruik? 

   Voor SQL Server is geen configuratie wijziging vereist om DNN te gebruiken, maar sommige SQL Server functies vereisen mogelijk meer aandacht. 

- Biedt DNN ondersteuning voor clusters met meerdere subnetten?

   Ja. Het cluster koppelt de DNN in DNS aan de fysieke IP-adressen van alle knoop punten in het cluster, ongeacht het subnet. De SQL-client probeert alle IP-adressen van de DNS-naam, ongeacht het subnet. 



## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie: 

- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instanties van een SQL Server-failovercluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

