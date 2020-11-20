---
title: Veelgestelde vragen-Azure Database Migration Service
description: Veelgestelde vragen over het gebruik van Azure Database Migration Service om database migraties uit te voeren.
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
ms.openlocfilehash: bf6e61ef3dfb1c50166cf17168b4deeb21e958d7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962906"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Veelgestelde vragen over het gebruik van Azure Database Migration Service

Dit artikel bevat veelgestelde vragen over het gebruik van Azure Database Migration Service in combi natie met gerelateerde antwoorden.

## <a name="overview"></a>Overzicht

**Nils. Wat is Azure Database Migration Service?**
Azure Database Migration Service is een volledig beheerde service die is ontworpen om naadloze migratie van meerdere database bronnen naar Azure-gegevens platforms mogelijk te maken met minimale downtime. De service is momenteel in algemene Beschik baarheid, met voortdurende ontwikkelings inspanningen gericht op:

* Betrouw baarheid en prestaties.
* Herhaling toevoegen van bron-doel paren.
* Voortdurende investeringen in migraties zonder wrijving.

**Nils. Welke bron-en doel paren Azure Database Migration Service momenteel ondersteund?**
De service biedt momenteel ondersteuning voor verschillende bron-en doel paren of migratie scenario's. Zie de artikel [status van migratie scenario's die worden ondersteund door de Azure database Migration service](./resource-scenario-status.md)voor een volledige lijst met de status van elk beschikbaar migratie scenario.

Andere migratie scenario's zijn beschikbaar als preview-versie en vereisen het indienen van een benoeming via de DMS-preview-site. Voor een volledig overzicht van de scenario's in de preview-versie en om u aan te melden om deel te nemen aan een van deze aanbiedingen, raadpleegt u de [DMS preview-site](https://aka.ms/dms-preview/).

**Nils. Welke versies van SQL Server ondersteunen Azure Database Migration Service als bron?**
Bij de migratie van SQL Server worden ondersteunde bronnen voor Azure Database Migration Service SQL Server 2005 tot en met SQL Server 2019.

**V Azure Database Migration Service: wat is het verschil tussen een offline-en online migratie?**
U kunt Azure Database Migration Service gebruiken om offline-en online migraties uit te voeren. Bij een *offline* migratie wordt de uitval tijd van toepassingen gestart wanneer de migratie wordt gestart. Bij een *online* migratie is downtime beperkt tot de tijd die aan het einde van de migratie moet worden afgekapt. We raden u aan eerst een offlinemigratie te testen om te bepalen of de downtime aanvaardbaar is. Zo niet, voer dan een onlinemigratie uit.

> [!NOTE]
> Als Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie.

**Nils. Hoe werkt Azure Database Migration Service vergelijken met andere hulpprogram ma's voor migratie van micro soft-data bases, zoals de data base Migration Assistant (DMA) of SQL Server Migration Assistant (SSMA)?**
Azure Database Migration Service is de voorkeurs methode voor database migratie naar Microsoft Azure op schaal. Zie voor meer informatie over de manier waarop Azure Database Migration Service vergelijkt met andere hulpprogram ma's voor migratie van micro soft-data bases en voor aanbevelingen over het gebruik van de service voor verschillende scenario's, de blog berichten over de [hulp middelen en services voor database migratie van micro soft](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529).

**Nils. Hoe werkt Azure Database Migration Service vergelijken met de Azure Migrate-aanbieding?**
Azure Migrate helpt bij het migreren van on-premises virtuele machines naar Azure IaaS. De service beoordeelt de geschiktheid voor migratie en op basis van prestaties en biedt een schatting van de kosten voor het uitvoeren van uw on-premises virtuele machines in Azure. Azure Migrate is nuttig voor lift-en-Shift-migraties van on-premises VM-gebaseerde workloads naar Azure IaaS Vm's. Maar in tegens telling tot Azure Database Migration Service is Azure Migrate geen gespecialiseerde data base Migration service-oplossing voor relationele data base-platforms van Azure PaaS, zoals Azure SQL Database of Azure SQL Managed instance.

## <a name="setup"></a>Instellen

**Nils. Wat zijn de vereisten voor het gebruik van Azure Database Migration Service?**
Er zijn verschillende vereisten vereist om ervoor te zorgen dat Azure Database Migration Service probleemloos wordt uitgevoerd bij het uitvoeren van database migraties. Sommige van de vereisten gelden voor alle scenario's (bron-doel paren) die door de service worden ondersteund, terwijl andere vereisten uniek zijn voor een specifiek scenario.

Azure Database Migration Service vereisten die gemeen schappelijk zijn voor alle ondersteunde migratie scenario's zijn onder andere het volgende:

* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit geeft site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Zorg ervoor dat in de regels voor de netwerk beveiligings groep van uw virtuele netwerk niet de volgende communicatie poorten 443, 53, 5671-5672, 9350-9354, 445, 12000 worden geblokkeerd. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.

Zie de gerelateerde zelf studies in de Azure Database Migration Service [documentatie](./dms-overview.md) op docs.Microsoft.com voor een lijst met alle vereisten die vereist zijn voor het concurreren van specifieke migratie scenario's met behulp van Azure database Migration service.

**Nils. Hoe kan ik het IP-adres voor Azure Database Migration Service vinden zodat ik een acceptatie lijst kan maken voor de firewall regels die worden gebruikt voor toegang tot mijn bron database voor migratie?**
Mogelijk moet u firewall regels toevoegen om Azure Database Migration Service toegang te geven tot uw bron database voor migratie. Het IP-adres voor de service is dynamisch, maar als u ExpressRoute gebruikt, wordt dit adres privé toegewezen door uw bedrijfs netwerk. De eenvoudigste manier om het juiste IP-adres te identificeren, is om de bijbehorende netwerk interface te zoeken in dezelfde resource groep als uw ingerichte Azure Database Migration Service bron. Normaal gesp roken begint de naam van de netwerk interface bron met het voor voegsel van de NIC en gevolgd door een unieke teken-en nummer reeks, bijvoorbeeld NIC-jj6tnztnmarpsskr82rbndyp. Als u deze netwerk interface bron selecteert, ziet u het IP-adres dat moet worden opgenomen in de acceptatie lijst op de pagina Resource overzicht Azure Portal.

Mogelijk moet u ook de poort bron opnemen die SQL Server luistert op de acceptatie lijst. Standaard is dit poort 1433, maar de bron SQL Server kan worden geconfigureerd voor het Luis teren op andere poorten. In dit geval moet u ook deze poorten opnemen in de acceptatie lijst. U kunt bepalen op welke poort SQL Server wordt geluisterd met behulp van een query voor dynamische beheer weergave:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

U kunt ook bepalen welke poort SQL Server luistert door het SQL Server fouten logboek op te vragen:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**Nils. Hoe kan ik een Microsoft Azure Virtual Network ingesteld?**
Hoewel u meerdere micro soft-zelf studies hebt waarmee u het proces voor het instellen van een virtueel netwerk kunt door lopen, wordt de officiële documentatie weer gegeven in het artikel [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Gebruik

**Nils. Wat is een overzicht van de stappen die nodig zijn om Azure Database Migration Service te gebruiken voor het uitvoeren van een database migratie?**
Tijdens een typische, eenvoudige database migratie:

1. Een doel database (s) maken.
2. Evalueer uw bron database (s).
    * Voor homogene migraties kunt u uw bestaande data base (s) beoordelen met behulp van [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Voor heterogene migraties (van concurrentie bronnen) kunt u uw bestaande data base (s) beoordelen met [SSMA](/sql/ssma/sql-server-migration-assistant). U kunt SSMA ook gebruiken om database objecten te converteren en het schema te migreren naar uw doel platform.
3. Maak een exemplaar van de Azure Database Migration Service.
4. Maak een migratie project waarin de bron database (s), doel database (s) en de te migreren tabellen worden opgegeven.
5. Start de volledige belasting.
6. Kies de volgende validatie.
7. Voer een hand matige overschakeling van uw productie omgeving uit naar de nieuwe cloud-gebaseerde data base.

## <a name="troubleshooting-and-optimization"></a>Problemen oplossen en optimaliseren

**Nils. Ik stel een migratie project in op DMS en ik ondervind problemen bij het maken van verbinding met mijn bron database. Wat moet ik doen?**
Als u problemen ondervindt bij het maken van verbinding met uw bron database systeem tijdens het werken met de migratie, maakt u een virtuele machine in het virtuele netwerk waarmee u uw DMS-exemplaar hebt ingesteld. In de virtuele machine moet u een verbindings test kunnen uitvoeren, zoals het gebruik van een UDL-bestand om een verbinding met SQL Server te testen of om Robo 3T gebruiken te downloaden om MongoDB-verbindingen te testen. Als de verbindings test is geslaagd, hebt u geen probleem met het maken van verbinding met de bron database. Als de verbindings test niet slaagt, neemt u contact op met uw netwerk beheerder.

**Nils. Waarom is mijn Azure Database Migration Service niet beschikbaar of gestopt?**
Als de gebruiker de Azure Database Migration Service (DMS) expliciet stopt of als de service gedurende een periode van 24 uur inactief is, wordt de status van de service gestopt of automatisch onderbroken. In elk geval is de service niet beschikbaar en is de status gestopt.  Als u actieve migraties wilt hervatten, start u de service opnieuw.

**Nils. Zijn er aanbevelingen voor het optimaliseren van de prestaties van Azure Database Migration Service?**
U kunt een aantal dingen doen om uw database migratie te versnellen met behulp van de service:

* Gebruik de prijs categorie multi CPU-Algemeen wanneer u een service-exemplaar maakt waarmee de service kan profiteren van meerdere Vcpu's voor parallel Lise ring en een snellere gegevens overdracht.
* U kunt uw Azure SQL Database doel exemplaar tijdens de gegevens migratie tijdelijk opschalen naar de SKU voor de Premium-laag om Azure SQL Database beperking te minimaliseren die van invloed kan zijn op de activiteiten voor gegevens overdracht wanneer u Sku's van een lager niveau gebruikt.

## <a name="next-steps"></a>Volgende stappen

Zie het artikel [Wat is de Azure database Migration service](dms-overview.md)voor een overzicht van de Azure database Migration service en regionale Beschik baarheid.