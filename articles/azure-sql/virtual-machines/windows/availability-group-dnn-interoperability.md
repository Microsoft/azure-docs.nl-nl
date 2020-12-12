---
title: Functie compatibiliteit met beschikbaarheids groepen en DNN-listener
description: "Meer informatie over de aanvullende overwegingen bij het werken met bepaalde SQL Server functies en een DNN-listener (Distributed Network name) met een always on-beschikbaarheids groep op SQL Server op Azure-Vm's. "
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
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 19b4b7407468b19419e2f85193b1f8fb6ace39c3
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359401"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>Functie compatibiliteit met AG en DNN-listener 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Er zijn bepaalde SQL Server functies die afhankelijk zijn van een in code vastgelegd virtueel netwerk naam (VNN). Wanneer u de DNN-listener (Distributed Network name) gebruikt met uw AlwaysOn-beschikbaarheids groep en SQL Server op virtuele Azure-machines, kunnen er ook enkele extra overwegingen zijn. 

In dit artikel vindt u informatie SQL Server functies en interoperabiliteit met de DNN-listener van de beschikbaarheids groep. 


## <a name="client-drivers"></a>Clientstuurprogramma's

Voor ODBC-, OLEDB-, ADO.NET-, JDBC-, PHP-en Node.js-Stuur Programma's moeten gebruikers expliciet de naam en poort van de DNN-listener opgeven als de server naam in de connection string. Als u wilt zorgen voor snelle connectiviteit bij failover, voegt `MultiSubnetFailover=True` u toe aan de Connection String als de SQL-Client dit ondersteunt. 

## <a name="tools"></a>Hulpprogramma's

Gebruikers van [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [Sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)en [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) moeten expliciet de naam en poort van de DNN-listener opgeven als server naam in de Connection String om verbinding te maken met de listener. 

Het maken van de DNN-listener via de gebruikers interface van de SQL Server Management Studio (SSMS) wordt momenteel niet ondersteund. 


## <a name="availability-groups-and-fci"></a>Beschikbaarheids groepen en FCI

U kunt een always on-beschikbaarheids groep configureren met behulp van een FCI (failover cluster instance) als een van de replica's. Voor een goede werking van deze configuratie met de DNN-listener [moet het failovercluster ook de DNN gebruiken](failover-cluster-instance-distributed-network-name-dnn-configure.md) omdat er geen manier is om het FCI virtuele IP-adres in de IP-lijst van de AG DNN te plaatsen. 

In deze configuratie moet de URL voor het spie gelen van eind punten voor de replica van FCI de FCI-DNN gebruiken. Als de FCI wordt gebruikt als een alleen-lezen replica, moet de FCI DNN ook worden gebruikt voor de alleen-lezen routering naar de replica van de FCI. 

De notatie voor het mirroring-eind punt is: `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` . 

Als de DNS-naam van uw FCI DNN bijvoorbeeld is `dnnlsnr` en `5022` de poort van het spiegel eindpunt van de FCI is, is het code fragment Transact-SQL (T-SQL) voor het maken van de eind punt-URL er als volgt uit te zien: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

De indeling voor de alleen-lezen routerings-URL is ook: `TCP://<FCI DNN DNS name>:<SQL Server instance port>` . 

Als de DNS-naam van uw DNN bijvoorbeeld is `dnnlsnr` , en `1444` de poort is die wordt gebruikt door het alleen-lezen doel SQL Server FCI, ziet u het code fragment T-SQL om de alleen-lezen routerings-URL te maken, zoals: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

U kunt de poort in de URL weglaten als dit de standaard 1433-poort is. Configureer voor een benoemd exemplaar een statische poort voor het benoemde exemplaar en geef deze op in de routerings-URL met het kenmerk alleen-lezen.  

## <a name="distributed-availability-group"></a>Gedistribueerde beschikbaarheids groep

Gedistribueerde beschikbaarheids groepen worden momenteel niet ondersteund met de DNN-listener. 

## <a name="replication"></a>Replicatie

Transactionele, merge-en momentopname replicatie alle ondersteuning voor het vervangen van de VNN-listener met de DNN-listener en poort in replicatie objecten die verbinding maken met de listener. 

Zie voor meer informatie over het gebruik van replicatie met beschikbaarheids groepen [Publisher en AG](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server), [SUBSCRIBer, AG](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server)en [Distributor en AG](/sql/relational-databases/replication/configure-distribution-availability-group).

## <a name="msdtc"></a>MSDTC

Zowel lokale als geclusterde MSDTC worden ondersteund, maar MSDTC gebruikt een dynamische poort, waarvoor een standaard Azure Load Balancer vereist is om de HA-poort te configureren. Daarom moet de virtuele machine gebruikmaken van een standaard IP-reserve ring of kan deze niet worden blootgesteld aan Internet. 

Definieer twee regels, één voor de RPC-eindpunttoewijzer poort 135 en een voor de Real MSDTC-poort. Na een failover wijzigt u de LB-regel naar de nieuwe MSDTC-poort nadat deze is gewijzigd op het nieuwe knoop punt. 

Als de MSDTC lokaal is, moet u ervoor zorgen dat uitgaande communicatie is toegestaan. 

## <a name="distributed-query"></a>Gedistribueerde query 

Gedistribueerde query's zijn afhankelijk van een gekoppelde server, die kan worden geconfigureerd met de DNN-listener en-poort van de AG. Als de poort niet 1433 is, kiest u de optie **andere gegevens bron gebruiken** in SQL Server Management Studio (SSMS) bij het configureren van de gekoppelde server. 

## <a name="filestream"></a>-

FileStream wordt ondersteund, maar niet voor scenario's waarbij gebruikers toegang hebben tot de scoped bestands share met behulp van de Windows-bestands-API. 

## <a name="filetable"></a>Bestandstabel

Bestands tabel wordt ondersteund, maar niet voor scenario's waarbij gebruikers toegang hebben tot de scoped bestands share met behulp van de Windows-bestands-API. 

## <a name="linked-servers"></a>Gekoppelde servers

Configureer de gekoppelde server met de naam en poort van de AG DNN-listener. Als de poort niet 1433 is, kiest u de optie **andere gegevens bron gebruiken** in SQL Server Management Studio (SSMS) bij het configureren van de gekoppelde server. 


## <a name="frequently-asked-questions"></a>Veelgestelde vragen


- Welke SQL Server versie brengt de ondersteuning van de AG DNN-listener? 

   SQL Server 2019 CU8 en hoger.

- Wat is de verwachte failover-tijd wanneer de DNN-listener wordt gebruikt?

   Voor DNN-listener is de failover-tijd alleen de AG-failovertijd, zonder enige extra tijd (zoals een test tijd wanneer u Azure Load Balancer gebruikt).

- Zijn er versie vereisten voor SQL-clients om DNN te ondersteunen met OLEDB en ODBC?

   We raden `MultiSubnetFailover=True` Connection String ondersteuning voor DNN listener aan. Het is beschikbaar vanaf SQL Server 2012 (11. x).

- Zijn er SQL Server configuratie wijzigingen die zijn vereist voor het gebruik van de DNN-listener? 

   Voor SQL Server is geen configuratie wijziging vereist om DNN te gebruiken, maar sommige SQL Server functies vereisen mogelijk meer aandacht. 

- Biedt DNN ondersteuning voor clusters met meerdere subnetten?

   Ja. Het cluster koppelt de DNN in DNS aan de fysieke IP-adressen van alle replica's in de beschik baarheid, ongeacht het subnet. De SQL-client probeert alle IP-adressen van de DNS-naam, ongeacht het subnet. 



## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie: 

- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)   
- [AlwaysOn-beschikbaarheids groep](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

