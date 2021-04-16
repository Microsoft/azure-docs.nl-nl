---
title: Azure Monitor for SAP Solutions providers| Microsoft Docs
description: Dit artikel bevat antwoorden op veelgestelde vragen over Azure Monitor voor SAP-oplossingenproviders.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: fe8ac81a8b04aa88ce91a978c2bc9b979a065370
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576156"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Providers van Azure Monitor voor SAP-oplossingen (preview)

## <a name="overview"></a>Overzicht  

In de context van Azure Monitor for SAP Solutions verwijst *een providertype* naar een specifieke *provider*. Bijvoorbeeld *SAP HANA*, die is geconfigureerd voor een specifiek onderdeel binnen het SAP-landschap, zoals SAP HANA database. Een provider bevat de verbindingsgegevens voor het bijbehorende onderdeel en helpt bij het verzamelen van telemetriegegevens van dat onderdeel. Een Azure Monitor for SAP Solutions resource (ook wel SAP Monitor-resource genoemd) kan worden geconfigureerd met meerdere providers van hetzelfde providertype of meerdere providers van meerdere providertypen.
   
Klanten kunnen ervoor kiezen om verschillende providertypen te configureren om het verzamelen van gegevens uit het bijbehorende onderdeel in hun SAP-landschap mogelijk te maken. Klanten kunnen bijvoorbeeld één provider configureren voor SAP HANA providertype, een andere provider voor het type clusterprovider met hoge beschikbaarheid, en meer.  

Klanten kunnen er ook voor kiezen om meerdere providers van een specifiek providertype te configureren om dezelfde SAP Monitor-resource en de bijbehorende beheerde groep opnieuw te gebruiken. Meer informatie over beheerde resourcegroep. Voor openbare preview worden de volgende providertypen ondersteund:   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- Cluster met hoge beschikbaarheid
- Besturingssysteem

![Azure Monitor voor SAP-oplossingenproviders](https://user-images.githubusercontent.com/75772258/115047655-5a5b2c00-9ef6-11eb-9e0c-073e5e1fcd0e.png)

Klanten wordt aangeraden ten minste één provider van de beschikbare providertypen te configureren op het moment dat de SAP Monitor-resource wordt geïmplementeerd. Door een provider te configureren, starten klanten gegevensverzameling vanuit het bijbehorende onderdeel waarvoor de provider is geconfigureerd.   

Als klanten op het moment van de implementatie van de SAP Monitor-resource geen providers configureren, worden er geen telemetriegegevens verzameld, hoewel de SAP Monitor-resource wel wordt geïmplementeerd. Klanten hebben de mogelijkheid om providers toe te voegen na de implementatie via SAP Monitor-resource binnen Azure Portal. Klanten kunnen op elk moment providers toevoegen aan of verwijderen uit de SAP Monitor-resource.

## <a name="provider-type-sap-netweaver"></a>Providertype: SAP NetWeaver

Klanten kunnen een of meer providers van het providertype SAP NetWeaver configureren om gegevensverzameling vanuit de SAP NetWeaver-laag mogelijk te maken. De AMS NetWeaver-provider maakt gebruik van de bestaande [SAPControl-webserviceinterface](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) om de juiste telemetriegegevens op te halen.

Voor de huidige release vindt u hieronder de standaard out-of-box SOAP-webmethoden die worden aangeroepen door AMS.

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

In de openbare preview kunnen klanten verwachten dat ze de volgende gegevens zien met de SAP NetWeaver-provider: 
- Beschikbaarheid van systeem en exemplaar
- Gebruik van werkproces
- Wachtrijgebruik
- Vergrendelingsstatistieken in dequeuue.

![image](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>Providertype: SAP HANA

Klanten kunnen een of meer providers van *providertype* configureren om SAP HANA gegevensverzameling uit de database SAP HANA inschakelen. De SAP HANA-provider maakt verbinding met de SAP HANA-database via SQL-poort, haalt telemetriegegevens op uit de database en pusht deze naar de Log Analytics-werkruimte in het klantabonnement. De SAP HANA-provider verzamelt elke 1 minuut gegevens uit de SAP HANA database.  

In de openbare preview kunnen klanten de volgende gegevens verwachten met SAP HANA-provider: Onderliggend infrastructuurgebruik, SAP HANA Hoststatus, SAP HANA System Replication en SAP HANA Backup-telemetriegegevens. Als u de SAP HANA wilt configureren, zijn het IP-adres van de host, het HANA SQL-poortnummer en de gebruikersnaam en het wachtwoord van SYSTEMDB vereist. Klanten wordt aangeraden om een SAP HANA te configureren voor SYSTEMDB, maar er kunnen meer providers worden geconfigureerd voor andere databaseten tenants.

![Azure Monitor voor SAP-oplossingenproviders - SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>Providertype: Microsoft SQL Server

Klanten kunnen een of meer providers van providertype configureren *Microsoft SQL Server* gegevensverzameling vanuit [SQL Server op Virtual Machines.](https://azure.microsoft.com/services/virtual-machines/sql-server/) SQL Server-provider maakt verbinding met Microsoft SQL Server via de SQL-poort, haalt telemetriegegevens op uit de database en pusht deze naar de Log Analytics-werkruimte in het klantabonnement. De SQL Server moet worden geconfigureerd voor SQL-verificatie en er moet een SQL Server-aanmelding worden gemaakt, met de SAP DB als de standaarddatabase voor de provider. SQL Server-provider verzamelt gegevens tussen elke 60 seconden tot elk uur van SQL Server.  

In de openbare preview kunnen klanten verwachten dat ze de volgende gegevens zien met SQL Server-provider: onderliggend infrastructuurgebruik, bovenste SQL-instructies, grootste tabel, problemen die zijn vastgelegd in de SQL Server-foutlogboeken, blokkeringsprocessen en andere.  

Als u Microsoft SQL Server-provider wilt configureren, zijn de SAP-systeem-id, het IP-adres van de host, het SQL Server-poortnummer en de SQL Server-aanmeldingsnaam en het wachtwoord vereist.

![Azure Monitor voor SAP-oplossingenproviders - SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>Providertype: Cluster met hoge beschikbaarheid
Klanten kunnen een of meer providers van het providertype Cluster met hoge beschikbaarheid configureren om gegevensverzameling vanuit het Pacemaker-cluster binnen het *SAP-landschap* mogelijk te maken. De clusterprovider met hoge beschikbaarheid maakt [](https://github.com/ClusterLabs/ha_cluster_exporter) verbinding met Pacemaker, met behulp van ha_cluster_exporter-eindpunt, haalt telemetriegegevens op uit de database en pusht deze naar de Log Analytics-werkruimte in het klantabonnement. Clusterprovider met hoge beschikbaarheid verzamelt elke 60 seconden gegevens van Pacemaker.  

In de openbare preview kunnen klanten de volgende gegevens verwachten met een clusterprovider met hoge beschikbaarheid:   
 - Clusterstatus weergegeven als roll-up van knooppunt en resourcestatus 
 - [Anderen](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor voor SAP-oplossingenproviders - cluster met hoge beschikbaarheid](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Er zijn twee primaire stappen nodig om een clusterprovider met hoge beschikbaarheid te configureren:

1. Installeer [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in *elk knooppunt* in het Pacemaker-cluster.

   U hebt twee opties voor het installeren van ha_cluster_exporter:
   
   - Gebruik Azure Automation om een cluster met hoge beschikbaarheid te implementeren. De scripts installeren [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) op elk clusterknooppunt.  
   - Een [handmatige installatie uitvoeren.](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build) 

2. Configureer een clusterprovider met hoge beschikbaarheid *voor elk* knooppunt in het Pacemaker-cluster.

   Als u de clusterprovider voor hoge beschikbaarheid wilt configureren, is de volgende informatie vereist:
   
   - **Naam**. Een naam voor deze provider. Deze moet uniek zijn voor deze Azure Monitor voor sap-oplossingen.
   - **Prometheus-eindpunt**. \: // \<servername or ip address\> http:9664/metrics.
   - **SID**. Gebruik voor SAP-systemen de SAP SID. Gebruik voor andere systemen (bijvoorbeeld NFS-clusters) een naam van drie tekens voor het cluster. De SID moet verschillen van andere clusters die worden bewaakt.   
   - **Clusternaam**. De clusternaam die wordt gebruikt bij het maken van het cluster. De clusternaam vindt u in de cluster-eigenschap `cluster-name` .
   - **Hostnaam**. De Linux-hostnaam van de VM.  

## <a name="provider-type-os-linux"></a>Providertype: BESTURINGSSYSTEEM (Linux)
Klanten kunnen een of meer providers van providertype OS (Linux) configureren om gegevensverzameling vanuit BareMetal of VM-knooppunt mogelijk te maken. De provider van het besturingssysteem (Linux) maakt verbinding met BareMetal- of VM-knooppunten, haalt met behulp van [het Node_Exporter-eindpunt](https://github.com/prometheus/node_exporter)telemetriegegevens op uit de knooppunten en pusht deze naar de Log Analytics-werkruimte in het   klantabonnement. De provider van het besturingssysteem (Linux) verzamelt elke 60 seconden gegevens voor de meeste metrische gegevens van knooppunten. 

In openbare preview kunnen klanten verwachten dat ze de volgende gegevens zien met de provider van het besturingssysteem (Linux) : 
   - CPU-gebruik, CPU-gebruik per proces 
   - Schijfgebruik, I/O-& schrijven 
   - Geheugendistributie, geheugengebruik, geheugengebruik wisselen 
   - Netwerkgebruik, details van het uitgaande & netwerkverkeer. 

Er zijn twee primaire stappen nodig om een provider van het besturingssysteem (Linux) te configureren:
1. Installeer [Node_Exporter](https://github.com/prometheus/node_exporter)   op elke BareMetal- of VM-knooppunten.
   U hebt twee opties voor het installeren [van Node_exporter:](https://github.com/prometheus/node_exporter) 
      - Gebruik voor Automation-installatie met Ansible [Node_Exporter](https://github.com/prometheus/node_exporter) op elke BareMetal- of VM-knooppunten om de OS-provider (Linux) te installeren.  
      - Een [handmatige installatie uitvoeren.](https://prometheus.io/docs/guides/node-exporter/)

2. Configureer een provider van het besturingssysteem (Linux) voor elk BareMetal- of VM-knooppunt in uw omgeving. 
   Als u de provider van het besturingssysteem (Linux) wilt configureren, is de volgende informatie vereist: 
      - Naam. Een naam voor deze provider. Deze moet uniek zijn voor deze Azure Monitor voor sap-oplossingen. 
      - Knooppunt-export-eindpunt. Meestal http:// <servername or ip address> :9100/metrics 

> [!NOTE]
> 9100 is een poort die wordt blootgesteld aan Node_Exporter eindpunt.

> [!Warning]
> Zorg ervoor dat knooppunt Export actief blijft na het opnieuw opstarten van het knooppunt. 

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [de stappen voor onboarding](./azure-monitor-sap-quickstart.md) en maak uw eerste Azure Monitor voor SAP-oplossingenresource.
- Hebt u vragen over Azure Monitor for SAP Solutions? Raadpleeg de [sectie Veelgestelde](./azure-monitor-faq.md) vragen
