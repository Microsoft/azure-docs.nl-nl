---
title: Azure Monitor voor SAP Solutions-providers | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure monitor for SAP Solutions providers.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 1282d1916d669f1026707e15cc8d5437d885087f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668992"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure monitor voor SAP-oplossingen providers (preview-versie)

## <a name="overview"></a>Overzicht  

In de context van Azure Monitor voor SAP-oplossingen verwijst een *provider type* naar een specifieke *provider*. Bijvoorbeeld *SAP Hana*, dat is geconfigureerd voor een specifiek onderdeel in het SAP-landschap, zoals SAP Hana-data base. Een provider bevat de verbindings gegevens voor het bijbehorende onderdeel en helpt u bij het verzamelen van telemetriegegevens van dat onderdeel. Een Azure Monitor voor SAP-oplossingen resource (ook wel bekend als SAP-monitor bron) kan worden geconfigureerd met meerdere providers van hetzelfde provider type of meerdere providers van meerdere provider typen.
   
Klanten kunnen kiezen voor het configureren van verschillende provider typen om het verzamelen van gegevens van een bijbehorend onderdeel in hun SAP-landschap in te scha kelen. Klanten kunnen bijvoorbeeld een provider voor SAP HANA provider type configureren, een andere provider voor een cluster provider type met hoge Beschik baarheid, enzovoort.  

Klanten kunnen er ook voor kiezen om meerdere providers van een specifiek provider type te configureren om dezelfde SAP-monitor bron en de bijbehorende beheerde groep opnieuw te gebruiken. Meer informatie over de beheerde resource groep. Voor een open bare preview worden de volgende provider typen ondersteund:   
- SAP HANA
- Cluster met hoge beschikbaarheid
- Microsoft SQL Server

![Azure Monitor voor SAP Solutions-providers](./media/azure-monitor-sap/azure-monitor-providers.png)

Klanten wordt aangeraden ten minste één provider te configureren van de beschik bare provider typen op het moment van de implementatie van de SAP-monitor bron. Door een provider te configureren, initiëren klanten gegevens verzameling vanuit het bijbehorende onderdeel waarvoor de provider is geconfigureerd.   

Als klanten geen providers configureren op het moment van de implementatie van de SAP-bewakings resource, wordt de SAP-bewakings resource wel geïmplementeerd, maar worden er geen telemetriegegevens verzameld. Klanten hebben de mogelijkheid om providers toe te voegen na de implementatie met behulp van de SAP-bewakings resource binnen Azure Portal. Klanten kunnen providers op elk gewenst moment toevoegen aan of verwijderen uit de SAP-monitor bron.

> [!Tip]
> Als u wilt dat micro soft een specifieke provider implementeert, kunt u aan het einde van dit document feedback geven via een koppeling of uw account team bereiken.  

## <a name="provider-type-sap-hana"></a>Provider type SAP HANA

Klanten kunnen een of meer providers van provider type configureren *SAP Hana* om het verzamelen van gegevens vanuit SAP Hana data base in te scha kelen. De SAP HANA provider maakt verbinding met de SAP HANA data base via SQL-poort, haalt telemetriegegevens op uit de data base en duwt deze naar de Log Analytics-werk ruimte in het abonnement van de klant. De SAP HANA provider verzamelt gegevens elke 1 minuut van de SAP HANA-data base.  

In de open bare Preview kunnen klanten verwachten dat de volgende gegevens worden weer gegeven met SAP HANA provider: onderliggend infrastructuur gebruik, SAP HANA host-status, SAP HANA systeem replicatie en SAP HANA back-upgegevens voor telemetrie. Als u SAP HANA provider wilt configureren, moet u het IP-adres van de host, HANA SQL-poort nummer en SYSTEMDB-gebruikers naam en-wacht woord opgeven. Klanten wordt aangeraden SAP HANA provider te configureren op basis van SYSTEMDB, maar meer providers kunnen worden geconfigureerd voor andere database tenants.

![Azure Monitor voor SAP Solutions-providers-SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Type cluster met hoge Beschik baarheid van provider
Klanten kunnen een of meer providers van provider type met een *hoge Beschik baarheid* configureren om het verzamelen van gegevens vanuit het pacemaker-cluster in het SAP-landschap in te scha kelen. De cluster provider met hoge Beschik baarheid verbindt met [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) -eind punt telemetrie-gegevens uit de data base en duwt deze naar log Analytics werk ruimte in het abonnement van de klant. Met een cluster provider met hoge Beschik baarheid worden gegevens elke 60 seconden verzameld van pacemaker.  

In de open bare Preview kunnen klanten verwachten dat de volgende gegevens worden weer gegeven met een cluster provider met hoge Beschik baarheid:   
 - De cluster status wordt weer gegeven als totalisatie van het knoop punt en de resource status 
 - [computers](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor voor SAP-oplossingen providers-cluster met hoge Beschik baarheid](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Voor het configureren van een cluster provider met hoge Beschik baarheid, zijn er twee primaire stappen betrokken:

1. Installeer [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in *elk* knoop punt in het pacemaker-cluster.

   U hebt twee opties voor het installeren van ha_cluster_exporter:
   
   - Gebruik Azure Automation scripts voor het implementeren van een cluster met hoge Beschik baarheid. De scripts worden [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) op elk cluster knooppunt geïnstalleerd.  
   - Een [hand matige installatie](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build)uitvoeren. 

2. Configureer een cluster provider voor maximale Beschik baarheid voor *elk* knoop punt in het pacemaker-cluster.

   Als u de cluster provider met maximale Beschik baarheid wilt configureren, is de volgende informatie vereist:
   
   - **Naam**. Een naam voor deze provider. Deze moet uniek zijn voor dit Azure Monitor voor SAP-oplossingen.
   - **Prometheus-eind punt**. http \: // \<servername or ip address\> : 9664/meet waarden.
   - **Sid**. Voor SAP-systemen gebruikt u de SAP-SID. Gebruik voor andere systemen (bijvoorbeeld NFS-clusters) een naam die uit drie tekens bestaan voor het cluster. De SID moet uniek zijn van andere clusters die worden bewaakt.   
   - **Cluster naam**. De cluster naam die wordt gebruikt bij het maken van het cluster. De cluster naam kan worden gevonden in de eigenschap cluster `cluster-name` .
   - **Hostnaam**. De Linux-hostnaam van de virtuele machine.  


## <a name="provider-type-os-linux"></a>Provider type besturings systeem (Linux)
Klanten kunnen een of meer providers van provider type OS (Linux) configureren om gegevens verzameling van BareMetal of VM-knoop punt in te scha kelen. De OS-provider (Linux) maakt verbinding met BareMetal-of VM-knoop punten met behulp van [Node_Exporter](https://github.com/prometheus/node_exporter)   -eind punt, haalt telemetriegegevens op van de knoop punten en duwt deze naar log Analytics werk ruimte in het klant abonnement. De provider van het besturings systeem (Linux) verzamelt gegevens elke 60 seconden voor de meeste meet waarden van knoop punten. 

In de open bare Preview kunnen klanten verwachten de volgende gegevens te zien met de OS-provider (Linux): 
   - CPU-gebruik, CPU-gebruik per proces 
   - Schijf gebruik, I/O-lees & schrijven 
   - Geheugen distributie, geheugen gebruik, geheugen gebruik wisselen 
   - Netwerk gebruik, binnenkomend netwerk & Details uitgaand verkeer. 

Als u een OS-provider (Linux) wilt configureren, zijn er twee primaire stappen betrokken:
1. Installeer [Node_Exporter](https://github.com/prometheus/node_exporter)   op elke BareMetal of VM-knoop punten.
   U hebt twee opties voor het installeren van [Node_exporter](https://github.com/prometheus/node_exporter): 
      - Voor een automatiserings installatie met Ansible gebruikt u [Node_Exporter](https://github.com/prometheus/node_exporter) op elke BAREMETAL of VM-knoop punten om de OS-provider (Linux) te installeren.  
      - Een [hand matige installatie](https://prometheus.io/docs/guides/node-exporter/)uitvoeren.

2. Configureer een OS-provider (Linux) voor elk exemplaar van het BareMetal-of VM-knoop punt in uw omgeving. 
   Als u de provider van het besturings systeem (Linux) wilt configureren, is de volgende informatie vereist: 
      - Naam. Een naam voor deze provider. Deze moet uniek zijn voor dit Azure Monitor voor SAP-oplossingen. 
      - Eind punt van knoop punt exporteren. Doorgaans http:// <servername or ip address> : 9100/meet waarden 

> [!NOTE]
> 9100 is een poort die beschikbaar is voor Node_Exporter-eind punt.

> [!Warning]
> Zorg ervoor dat de knooppunt Exporter actief blijft na het opnieuw opstarten van het knoop punt. 


## <a name="provider-type-microsoft-sql-server"></a>Provider type micro soft SQL Server

Klanten kunnen een of meer providers van provider type configureren *Microsoft SQL Server* om het verzamelen van gegevens vanuit [SQL Server op virtual machines](https://azure.microsoft.com/services/virtual-machines/sql-server/)in te scha kelen. SQL Server provider verbinding maakt met Microsoft SQL Server via de SQL-poort, haalt telemetriegegevens op uit de data base en duwt deze naar de Log Analytics-werk ruimte in het abonnement van de klant. De SQL Server moet worden geconfigureerd voor SQL-verificatie en een SQL Server aanmelding, met SAP DB als de standaard database voor de provider, moet worden gemaakt. SQL Server provider verzamelt gegevens van elke 60 seconden tot elk uur van SQL Server.  

In de open bare Preview kunnen klanten verwachten dat de volgende gegevens worden weer gegeven met SQL Server provider: onderliggend infrastructuur gebruik, de belangrijkste SQL-instructies, de belangrijkste grootste tabel, problemen die zijn vastgelegd in de SQL Server fout logboeken, het blok keren van processen en anderen.  

Als u Microsoft SQL Server provider wilt configureren, zijn de SAP-systeem-ID, het IP-adres van de host, het SQL Server poort nummer en de SQL Server aanmeldings naam en het wacht woord vereist.

![Azure Monitor voor SAP Solutions-providers-SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Volgende stappen

- Maak uw eerste Azure Monitor voor SAP-oplossingen resource.
- Hebt u vragen over Azure Monitor voor SAP-oplossingen? Raadpleeg de sectie [Veelgestelde vragen](./azure-monitor-faq.md)
