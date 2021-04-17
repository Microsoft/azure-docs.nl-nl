---
title: Veelgestelde vragen - Azure Database Migration Service
description: Veelgestelde vragen over het gebruik van Azure Database Migration Service databasemigraties uit te voeren.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 83ffd405bf8e39737d2d36c294eac27f9bc76daf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588816"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Veelgestelde vragen over het gebruik van Azure Database Migration Service

In dit artikel vindt u veelgestelde vragen over het gebruik Azure Database Migration Service samen met gerelateerde antwoorden.

## <a name="overview"></a>Overzicht

**V. Wat is Azure Database Migration Service?**
Azure Database Migration Service is een volledig beheerde service die is ontworpen om naadloze migratie van meerdere databasebronnen naar Azure-gegevensplatforms mogelijk te maken met minimale downtime. De service is momenteel algemeen beschikbaar, met voortdurende ontwikkelingsinspanningen die gericht zijn op:

* Betrouwbaarheid en prestaties.
* Iteratieve toevoeging van bron-doelparen.
* Voortdurende investering in migraties zonder frictie.

**V. Welke bron-/doelparen worden Azure Database Migration Service ondersteund?**
De service ondersteunt momenteel verschillende bron-/doelparen of migratiescenario's. Zie het artikel Status van migratiescenario's die worden ondersteund door de Azure Database Migration Service voor een [volledig overzicht van de status van elk beschikbaar migratiescenario.](https://github.com/MicrosoftDocs/azure-docs-pr/pull/resource-scenario-status.md)

**V. Welke versies van SQL Server worden Azure Database Migration Service als bron ondersteund?**
Bij het migreren van SQL Server worden ondersteunde bronnen voor Azure Database Migration Service SQL Server 2005 tot SQL Server 2019.

**V: Wat is Azure Database Migration Service een offlinemigratie en een onlinemigratie?**
U kunt deze Azure Database Migration Service offline- en onlinemigraties uit te voeren. Bij een *offlinemigratie* begint de downtime van de toepassing wanneer de migratie wordt gestart. Bij een *onlinemigratie* is downtime beperkt tot de tijd die nodig is om aan het einde van de migratie over te zetten. We raden u aan eerst een offlinemigratie te testen om te bepalen of de downtime aanvaardbaar is. Zo niet, voer dan een onlinemigratie uit.

> [!NOTE]
> Als Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie.

**V. Hoe verhoudt Azure Database Migration Service zich tot andere hulpprogramma's voor databasemigratie van Microsoft, zoals de Database Migration Assistant (DMA) of SQL Server Migration Assistant (SSMA)?**
Azure Database Migration Service is de voorkeursmethode voor databasemigratie naar Microsoft Azure schaal. Zie het blogbericht [Differentiating Microsoft's Database Migration Tools and Services (Microsoft-hulpprogramma's](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)voor databasemigratie en -services differentiëren) voor meer informatie over hoe Azure Database Migration Service zich verhoudt tot andere hulpprogramma's voor databasemigratie van Microsoft en aanbevelingen voor het gebruik van de service voor verschillende scenario's.

**V. Hoe verhoudt Azure Database Migration Service zich tot de Azure Migrate-aanbieding?**
Azure Migrate helpt bij de migratie van on-premises virtuele machines naar Azure IaaS. De service beoordeelt de geschiktheid voor migratie en de op prestaties gebaseerde formaatschattingen en biedt kostenramingen voor het uitvoeren van uw on-premises virtuele machines in Azure. Azure Migrate is handig voor lift-and-shift-migraties van on-premises VM-gebaseerde workloads naar Azure IaaS-VM's. In tegenstelling tot Azure Database Migration Service is Azure Migrate echter geen gespecialiseerde databasemigratieservice voor relationele Azure PaaS-databaseplatforms zoals Azure SQL Database of Azure SQL Managed Instance.

**V. Worden Database Migration Service gegevens van klanten opgeslagen?**
Nee. Database Migration Service worden geen klantgegevens opgeslagen.

## <a name="setup"></a>Instellen

**V. Wat zijn de vereisten voor het gebruik van Azure Database Migration Service?**
Er zijn verschillende vereisten om ervoor te zorgen dat Azure Database Migration Service soepel werkt bij het uitvoeren van databasemigraties. Sommige van de vereisten zijn van toepassing op alle scenario's (bron-doelparen) die door de service worden ondersteund, terwijl andere vereisten uniek zijn voor een specifiek scenario.

Azure Database Migration Service de volgende vereisten voor alle ondersteunde migratiescenario's zijn:

* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit geeft site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Zorg ervoor dat de regels van uw virtuele netwerknetwerkbeveiligingsgroep poort 443 voor ServiceTags van ServiceBus, Storage en AzureMonitor niet blokkeren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.

Zie de Azure Database Migration Service gerelateerde zelfstudies in de Azure Database Migration Service-documentatie over docs.microsoft.com voor een lijst met [](./dms-overview.md) alle vereisten die nodig zijn om specifieke migratiescenario's te kunnen docs.microsoft.com.

**V. Hoe kan ik ip-adres voor de Azure Database Migration Service zodat ik een lijst met toegestane firewallregels kan maken voor toegang tot mijn brondatabase voor migratie?**
Mogelijk moet u firewallregels toevoegen die toegang Azure Database Migration Service tot uw brondatabase voor migratie. Het IP-adres voor de service is dynamisch, maar als u ExpressRoute gebruikt, wordt dit adres privé toegewezen door uw bedrijfsnetwerk. De eenvoudigste manier om het juiste IP-adres te identificeren, is door te zoeken in dezelfde resourcegroep als uw in Azure Database Migration Service resource om de bijbehorende netwerkinterface te vinden. Meestal begint de naam van de resource netwerkinterface met het NIC-voorvoegsel en gevolgd door een uniek teken en een unieke nummerreeks, bijvoorbeeld NIC-jj6tnztnmarpsskr82rbndyp. Als u deze netwerkinterfaceresource selecteert, ziet u het IP-adres dat moet worden opgenomen in de lijst met toegestane ip-adressen op de Azure Portal resourceoverzicht.

Mogelijk moet u ook de poortbron opnemen die SQL Server luistert op de lijst met toegestane gegevens. Standaard is dit poort 1433, maar de broncode SQL Server geconfigureerd om ook op andere poorten te luisteren. In dit geval moet u deze poorten ook opnemen in de lijst met toegestane poorten. U kunt bepalen op welke poort SQL Server luistert met behulp van een dynamische beheerweergavequery:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

U kunt ook de poort bepalen die SQL Server luistert door een query uit te voeren op SQL Server foutlogboek:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**V. Hoe kan ik een Microsoft Azure Virtual Network?**
Hoewel er meerdere Microsoft-zelfstudies zijn die u kunnen helpen bij het instellen van een virtueel netwerk, wordt de officiële documentatie weergegeven in het artikel [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Gebruik

**V. Wat is een samenvatting van de stappen die nodig zijn om een Azure Database Migration Service databasemigratie uit te voeren?**
Tijdens een typische, eenvoudige databasemigratie kunt u het volgende doen:

1. Maak een of meer doeldatabases.
2. Evalueer uw brondatabase(s).
    * Evalueer uw bestaande database(s) met behulp van DMA voor [homogene migraties.](https://www.microsoft.com/download/details.aspx?id=53595)
    * Voor heterogene migraties (van concurrentiebronnen) moet u uw bestaande database(s) evalueren met [SSMA](/sql/ssma/sql-server-migration-assistant). U gebruikt SSMA ook om databaseobjecten te converteren en het schema naar uw doelplatform te migreren.
3. Maak een exemplaar van de Azure Database Migration Service.
4. Maak een migratieproject waarin de brondatabase(s), doeldatabase(s) en de te migreren tabellen worden opgegeven.
5. Start de volledige load.
6. Kies de volgende validatie.
7. Voer een handmatige overschakel van uw productieomgeving naar de nieuwe clouddatabase uit.

## <a name="troubleshooting-and-optimization"></a>Probleemoplossing en optimalisatie

**V. Ik ben bezig met het instellen van een migratieproject in DMS en ik heb moeite om verbinding te maken met mijn brondatabase. Wat moet ik doen?**
Als u problemen hebt met het maken van verbinding met uw brondatabasesysteem tijdens het werken aan de migratie, maakt u een virtuele machine in hetzelfde subnet van het virtuele netwerk waarmee u uw DMS-exemplaar hebt ingesteld. Op de virtuele machine moet u een verbindingstest kunnen uitvoeren, zoals het gebruik van een UDL-bestand om een verbinding met SQL Server te testen of Robo 3T te downloaden om MongoDB-verbindingen te testen. Als de verbindingstest slaagt, hebt u geen probleem met het maken van verbinding met uw brondatabase. Als de verbindingstest niet lukt, neemt u contact op met uw netwerkbeheerder.

**V. Waarom is mijn Azure Database Migration Service niet beschikbaar of gestopt?**
Als de gebruiker expliciet Azure Database Migration Service (DMS) stopt of als de service 24 uur inactief is, heeft de service de status Gestopt of Automatisch onderbroken. In elk geval is de service niet beschikbaar en heeft deze de status Gestopt.  Start de service opnieuw om actieve migraties te hervatten.

**V. Zijn er aanbevelingen voor het optimaliseren van de prestaties van Azure Database Migration Service?**
U kunt een aantal dingen doen om de migratie van uw database te versnellen met behulp van de service:

* Gebruik de prijscategorie voor Algemeen CPU-gebruik wanneer u uw service-exemplaar maakt, zodat de service kan profiteren van meerdere vCCPUs voor parallellisatie en snellere gegevensoverdracht.
* Schaal uw Azure SQL Database doel-exemplaar tijdelijk op naar de SKU van de Premium-laag tijdens de gegevensmigratiebewerking om de Azure SQL Database beperking te minimaliseren die van invloed kan zijn op activiteiten voor gegevensoverdracht bij het gebruik van SKU's op een lager niveau.

## <a name="next-steps"></a>Volgende stappen

Zie het artikel Wat [is](dms-overview.md)de Azure Database Migration Service voor een overzicht van de Azure Database Migration Service.
