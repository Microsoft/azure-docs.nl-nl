---
title: Connectiviteitsarchitectuur
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over de communicatie-en connectiviteits architectuur van Azure SQL Managed instance en hoe de onderdelen direct verkeer naar een beheerd exemplaar.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: 58563629b30e7be764732a9810162e1a0b1931e6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725833"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Connectiviteitsarchitectuur van Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel wordt de communicatie in Azure SQL Managed instance uitgelegd. Ook wordt de connectiviteits architectuur beschreven en hoe de onderdelen direct verkeer naar een beheerd exemplaar.  

SQL Managed instance wordt in het virtuele Azure-netwerk geplaatst en het subnet dat is toegewezen aan beheerde exemplaren. Deze implementatie biedt:

- Een beveiligd privé-IP-adres.
- De mogelijkheid om een on-premises netwerk te verbinden met een door SQL beheerd exemplaar.
- De mogelijkheid om SQL Managed instance te verbinden met een gekoppelde server of een ander on-premises gegevens archief.
- De mogelijkheid om SQL Managed instance te verbinden met Azure-resources.

## <a name="communication-overview"></a>Communicatie overzicht

In het volgende diagram ziet u entiteiten die verbinding maken met een door SQL beheerd exemplaar. Ook worden de bronnen weer gegeven die moeten communiceren met een beheerd exemplaar. Het communicatie proces aan de onderkant van het diagram vertegenwoordigt klant toepassingen en hulpprogram ma's die verbinding maken met een beheerd exemplaar van SQL als gegevens bronnen.  

![Entiteiten in connectiviteits architectuur](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL Managed instance is een PaaS-aanbieding (platform as a Service). Azure gebruikt geautomatiseerde agents (beheer, implementatie en onderhoud) voor het beheren van deze service op basis van telemetriegegevens. Omdat Azure verantwoordelijk is voor beheer, hebben klanten geen toegang tot de virtuele cluster machines van SQL Managed instance via Remote Desktop Protocol (RDP).

Sommige bewerkingen die door eind gebruikers of toepassingen worden gestart, hebben mogelijk een door SQL beheerd exemplaar nodig om te kunnen communiceren met het platform. Een voor beeld is het maken van een SQL Managed instance-data base. Deze resource wordt weer gegeven via de Azure Portal, Power shell, Azure CLI en de REST API.

SQL Managed instance is afhankelijk van Azure-Services zoals Azure Storage voor back-ups, Azure Event Hubs voor telemetrie, Azure Active Directory (Azure AD) voor verificatie, Azure Key Vault voor Transparent Data Encryption (TDE) en een aantal Azure-platform services die functies voor beveiliging en ondersteuning bieden. Met SQL Managed instance worden verbindingen met deze services gemaakt.

Alle communicatie wordt versleuteld en ondertekend met behulp van certificaten. Om de betrouw baarheid van communicatie partijen te controleren, controleert SQL Managed instance voortdurend deze certificaten via certificaatintrekkingslijsten. Als de certificaten zijn ingetrokken, sluit SQL Managed instance de verbindingen voor het beveiligen van de gegevens.

## <a name="high-level-connectivity-architecture"></a>Connectiviteits architectuur op hoog niveau

Op hoog niveau is SQL Managed instance een set service onderdelen. Deze onderdelen worden gehost op een speciale set geïsoleerde virtuele machines die worden uitgevoerd in het subnet van het virtuele netwerk van de klant. Deze machines vormen een virtueel cluster.

Een virtueel cluster kan meerdere beheerde instanties hosten. Als dat het geval is, wordt het cluster automatisch uitgebreid of gecontracteerd wanneer de klant het aantal ingerichte instanties in het subnet wijzigt.

Klant toepassingen kunnen verbinding maken met een beheerd exemplaar van SQL en kunnen query's uitvoeren op data bases binnen het virtuele netwerk, het gekoppelde virtuele netwerk of het netwerk dat is verbonden via VPN of Azure ExpressRoute. Dit netwerk moet een eind punt en een privé-IP-adres gebruiken.  

![Diagram van connectiviteits architectuur](./media/connectivity-architecture-overview/connectivityarch002.png)

Azure-beheer-en implementatie services worden uitgevoerd buiten het virtuele netwerk. SQL Managed instance en Azure Services maken verbinding via de eind punten die een openbaar IP-adres hebben. Wanneer met SQL Managed instance een uitgaande verbinding wordt gemaakt, ziet de verbinding op het ontvangende eind netwerk adres NAT (NAT) dat deze van het open bare IP-adres afkomstig lijkt te zijn.

Beheer verkeer loopt door het virtuele netwerk van de klant. Dit betekent dat elementen van de infra structuur van het virtuele netwerk het beheer verkeer kunnen schaden door te zorgen dat het exemplaar mislukt en niet meer beschikbaar is.

> [!IMPORTANT]
> Ter verbetering van de gebruikers ervaring en de beschik baarheid van de service, past Azure een netwerk intentie beleid toe op infra structuur-elementen van Azure Virtual Network. Het beleid kan invloed hebben op de werking van SQL Managed instance. Dit platform mechanisme doorloopt transparante netwerk vereisten voor gebruikers. Het belangrijkste doel van het beleid is om te voor komen dat de netwerk configuratie niet kan worden geconfigureerd en om te zorgen voor normale bewerkingen van SQL-beheerde exemplaren. Wanneer u een beheerd exemplaar verwijdert, wordt het beleid voor netwerk intentie ook verwijderd.

## <a name="virtual-cluster-connectivity-architecture"></a>Architectuur van Virtual cluster-connectiviteit

Laten we een diep gaande van de connectiviteits architectuur voor het beheerde exemplaar van SQL volgen. Het volgende diagram toont de conceptuele indeling van het virtuele cluster.

![Connectiviteits architectuur van het virtuele cluster](./media/connectivity-architecture-overview/connectivityarch003.png)

Clients maken verbinding met een SQL-beheerd exemplaar met behulp van een hostnaam die het formulier bevat `<mi_name>.<dns_zone>.database.windows.net` . Deze hostnaam wordt omgezet in een privé-IP-adres, maar is wel geregistreerd in een open bare Domain Name System (DNS)-zone en kan openbaar worden omgezet. De `zone-id` wordt automatisch gegenereerd wanneer u het cluster maakt. Als een nieuw cluster als host fungeert voor een secundaire beheerde instantie, wordt de zone-ID gedeeld met het primaire cluster. Zie voor meer informatie [automatische failover-groepen gebruiken om transparante en gecoördineerde failover van meerdere data bases in te scha kelen](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Dit privé-IP-adres maakt deel uit van de interne load balancer voor SQL Managed instance. De load balancer stuurt verkeer naar de gateway van het SQL Managed instance. Omdat meerdere beheerde instanties binnen hetzelfde cluster kunnen worden uitgevoerd, gebruikt de gateway de hostnaam van het door SQL beheerde exemplaar om verkeer om te leiden naar de juiste SQL engine-service.

Beheer-en implementatie services maken verbinding met een SQL beheerd exemplaar met behulp van een [beheer eindpunt](#management-endpoint) dat is toegewezen aan een externe Load Balancer. Verkeer wordt alleen gerouteerd naar de knoop punten als het wordt ontvangen op een vooraf gedefinieerde set poorten die alleen de beheer onderdelen van het door SQL beheerde exemplaar gebruiken. Een ingebouwde firewall op de knoop punten is ingesteld om alleen verkeer vanaf micro soft IP-bereiken toe te staan. Certificaten verifiëren wederzijds alle communicatie tussen beheer onderdelen en het beheer vlak.

## <a name="management-endpoint"></a>Beheereindpunt

Azure beheert SQL Managed instance met behulp van een beheer eindpunt. Dit eind punt bevindt zich in het virtuele cluster van een exemplaar. Het beheer eindpunt wordt beveiligd door een ingebouwde firewall op het netwerk niveau. Op toepassings niveau wordt het beveiligd door wederzijdse verificatie van certificaten. Zie [het IP-adres van het beheer eindpunt bepalen](management-endpoint-find-ip-address.md)om het IP-adres van het eind punt te vinden.

Wanneer verbindingen worden gestart binnen een SQL Managed instance (net als bij back-ups en audit Logboeken), lijkt het verkeer te starten vanuit het open bare IP-adres van het beheer eindpunt. U kunt de toegang tot open bare Services vanuit SQL Managed instance beperken door firewall regels in te stellen zodat alleen het IP-adres voor SQL Managed instance wordt toegestaan. Zie [de ingebouwde firewall van SQL Managed instance controleren](management-endpoint-verify-built-in-firewall.md)voor meer informatie.

> [!NOTE]
> Verkeer dat wordt verplaatst naar Azure-Services die zich binnen de regio voor het beheerde exemplaar van SQL bevindt, is geoptimaliseerd en daarom niet is genateeerd naar het open bare IP-adres voor het beheer eindpunt. Daarom moet de service zich in een andere regio bevinden dan een SQL Managed instance als u op IP gebaseerde firewall regels moet gebruiken, meestal voor opslag.

## <a name="service-aided-subnet-configuration"></a>Met service ondersteunde subnetconfiguratie

Om beveiligings-en beheer vereisten van de klant op te lossen, wordt door SQL beheerd exemplaar overgeschakeld van hand matig naar service-aided subnet-configuratie.

Met de service-gewerkte subnet-configuratie heeft de gebruiker volledig beheer van gegevens (TDS)-verkeer, terwijl SQL Managed instance verantwoordelijk is voor een ononderbroken stroom van beheer verkeer om te voldoen aan een SLA.

Service-geautomatiseerd subnet-configuratie bouwt voort op de functie voor het [delegeren van subnetten](../../virtual-network/subnet-delegation-overview.md) van het virtuele netwerk om automatisch netwerk configuratie beheer te bieden en service-eind punten in te scha kelen. 

Service-eind punten kunnen worden gebruikt voor het configureren van firewall regels voor virtuele netwerken op opslag accounts die back-ups en audit logboeken bewaren. Zelfs als service-eind punten zijn ingeschakeld, worden klanten aangemoedigd om een [persoonlijke koppeling](../../private-link/private-link-overview.md) te gebruiken die extra beveiliging biedt dan service-eind punten.

> [!IMPORTANT]
> Als gevolg van de configuratie kenmerken van het besturings vlak, worden service-geaidede subnetten niet ingeschakeld voor de configuratie van de services in nationale Clouds. 

### <a name="network-requirements"></a>Netwerkvereisten

Implementeer SQL Managed instance in een toegewezen subnet in het virtuele netwerk. Het subnet moet de volgende eigenschappen hebben:

- **Toegewezen subnet:** Het subnet van het SQL Managed instance kan geen andere Cloud service bevatten die eraan is gekoppeld en kan geen gateway-subnet zijn. Het subnet mag geen resource bevatten, maar een door SQL beheerd exemplaar, en u kunt later geen andere typen resources toevoegen in het subnet.
- **Subnet-overdracht:** Het subnet van het SQL-beheerde exemplaar moet worden gedelegeerd naar de `Microsoft.Sql/managedInstances` resource provider.
- **Netwerk beveiligings groep (NSG):** Een NSG moet worden gekoppeld aan het subnet met SQL Managed instance. U kunt een NSG gebruiken voor het beheren van de toegang tot het gegevens eind punt van de SQL Managed instance door verkeer te filteren op poort 1433 en poorten 11000-11999 wanneer SQL Managed instance is geconfigureerd voor omleidings verbindingen. Met de service worden automatisch de huidige [regels](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) ingericht en bewaard die nodig zijn om een ononderbroken stroom van beheer verkeer toe te staan.
- Door de **gebruiker gedefinieerde route tabel (UDR):** Een UDR-tabel moet worden gekoppeld aan het subnet met SQL Managed instance. U kunt vermeldingen toevoegen aan de routetabel om verkeer met on-premises privé-IP-bereiken als bestemming te routeren via de gateway van het virtuele netwerk of het virtuele netwerkapparaat (NVA). De service zal automatisch de huidige [vermeldingen](#user-defined-routes-with-service-aided-subnet-configuration) inrichten en handhaven die vereist zijn voor een niet-onderbroken stroom van beheerverkeer.
- **Voldoende IP-adressen:** Het subnet van het SQL-beheerde exemplaar moet ten minste 32 IP-adressen hebben. Zie [de grootte van het subnet voor SQL Managed instance bepalen](vnet-subnet-determine-size.md)voor meer informatie. U kunt beheerde exemplaren in [het bestaande netwerk](vnet-existing-add-subnet.md) implementeren nadat u deze hebt geconfigureerd om te voldoen aan [de netwerk vereisten voor SQL Managed instance](#network-requirements). Als dat niet mogelijk is, kunt u een [nieuw netwerk en subnet](virtual-network-subnet-create-arm-template.md) maken.

> [!IMPORTANT]
> Wanneer u een beheerd exemplaar maakt, wordt een netwerk intentie beleid toegepast op het subnet om niet-compatibele wijzigingen in de netwerk installatie te voor komen. Nadat het laatste exemplaar van het subnet is verwijderd, wordt het netwerkintentiebeleid ook verwijderd. De onderstaande regels zijn alleen bedoeld ter informatie en u moet ze niet implementeren met ARM-sjabloon/Power shell/CLI. Als u de meest recente officiële sjabloon wilt gebruiken, kunt u [deze altijd ophalen uit de portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Verplichte regels voor binnenkomende beveiliging met configuratie van geaidede subnetten

| Name       |Poort                        |Protocol|Bron           |Doel|Bewerking|
|------------|----------------------------|--------|-----------------|-----------|------|
|beheer  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI-SUBNET  |Toestaan |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI-SUBNET  |Toestaan |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI-SUBNET  |Toestaan |
|mi_subnet   |Alle                         |Alle     |MI-SUBNET        |MI-SUBNET  |Toestaan |
|health_probe|Alle                         |Alle     |AzureLoadBalancer|MI-SUBNET  |Toestaan |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Verplichte uitgaande beveiligings regels met een service-aided subnet-configuratie

| Name       |Poort          |Protocol|Bron           |Doel|Bewerking|
|------------|--------------|--------|-----------------|-----------|------|
|beheer  |443, 12000    |TCP     |MI-SUBNET        |AzureCloud |Toestaan |
|mi_subnet   |Alle           |Alle     |MI-SUBNET        |MI-SUBNET  |Toestaan |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Door de gebruiker gedefinieerde routes met de service-aided subnet-configuratie

|Name|Adresvoorvoegsel|Volgende hop|
|----|--------------|-------|
|subnet-to-vnetlocal|MI-SUBNET|Virtueel netwerk|
|Mi-13-64-11-nexthop-Internet|13.64.0.0/11|Internet|
|Mi-13-104-14-nexthop-Internet|13.104.0.0/14|Internet|
|Mi-20-33-16-nexthop-Internet|20.33.0.0/16|Internet|
|Mi-20-34-15-nexthop-Internet|20.34.0.0/15|Internet|
|Mi-20-36-14-nexthop-Internet|20.36.0.0/14|Internet|
|Mi-20-40-13-nexthop-Internet|20.40.0.0/13|Internet|
|Mi-20-48-12-nexthop-Internet|20.48.0.0/12|Internet|
|Mi-20-64-10-nexthop-Internet|20.64.0.0/10|Internet|
|Mi-20-128-16-nexthop-Internet|20.128.0.0/16|Internet|
|Mi-20-135-16-nexthop-Internet|20.135.0.0/16|Internet|
|Mi-20-136-16-nexthop-Internet|20.136.0.0/16|Internet|
|Mi-20-140-15-nexthop-Internet|20.140.0.0/15|Internet|
|Mi-20-143-16-nexthop-Internet|20.143.0.0/16|Internet|
|Mi-20-144-14-nexthop-Internet|20.144.0.0/14|Internet|
|Mi-20-150-15-nexthop-Internet|20.150.0.0/15|Internet|
|Mi-20-160-12-nexthop-Internet|20.160.0.0/12|Internet|
|Mi-20-176-14-nexthop-Internet|20.176.0.0/14|Internet|
|Mi-20-180-14-nexthop-Internet|20.180.0.0/14|Internet|
|Mi-20-184-13-nexthop-Internet|20.184.0.0/13|Internet|
|Mi-20-192-10-nexthop-Internet|20.192.0.0/10|Internet|
|Mi-40-64-10-nexthop-Internet|40.64.0.0/10|Internet|
|Mi-51-4-15-nexthop-Internet|51.4.0.0/15|Internet|
|Mi-51-8-16-nexthop-Internet|51.8.0.0/16|Internet|
|Mi-51-10-15-nexthop-Internet|51.10.0.0/15|Internet|
|Mi-51-18-16-nexthop-Internet|51.18.0.0/16|Internet|
|Mi-51-51-16-nexthop-Internet|51.51.0.0/16|Internet|
|Mi-51-53-16-nexthop-Internet|51.53.0.0/16|Internet|
|Mi-51-103-16-nexthop-Internet|51.103.0.0/16|Internet|
|Mi-51-104-15-nexthop-Internet|51.104.0.0/15|Internet|
|Mi-51-132-16-nexthop-Internet|51.132.0.0/16|Internet|
|Mi-51-136-15-nexthop-Internet|51.136.0.0/15|Internet|
|Mi-51-138-16-nexthop-Internet|51.138.0.0/16|Internet|
|Mi-51-140-14-nexthop-Internet|51.140.0.0/14|Internet|
|Mi-51-144-15-nexthop-Internet|51.144.0.0/15|Internet|
|Mi-52-96-12-nexthop-Internet|52.96.0.0/12|Internet|
|Mi-52-112-14-nexthop-Internet|52.112.0.0/14|Internet|
|Mi-52-125-16-nexthop-Internet|52.125.0.0/16|Internet|
|Mi-52-126-15-nexthop-Internet|52.126.0.0/15|Internet|
|Mi-52-130-15-nexthop-Internet|52.130.0.0/15|Internet|
|Mi-52-132-14-nexthop-Internet|52.132.0.0/14|Internet|
|Mi-52-136-13-nexthop-Internet|52.136.0.0/13|Internet|
|Mi-52-145-16-nexthop-Internet|52.145.0.0/16|Internet|
|Mi-52-146-15-nexthop-Internet|52.146.0.0/15|Internet|
|Mi-52-148-14-nexthop-Internet|52.148.0.0/14|Internet|
|Mi-52-152-13-nexthop-Internet|52.152.0.0/13|Internet|
|Mi-52-160-11-nexthop-Internet|52.160.0.0/11|Internet|
|Mi-52-224-11-nexthop-Internet|52.224.0.0/11|Internet|
|Mi-64-4-18-nexthop-Internet|64.4.0.0/18|Internet|
|Mi-65-52-14-nexthop-Internet|65.52.0.0/14|Internet|
|Mi-66-119-144-20-nexthop-Internet|66.119.144.0/20|Internet|
|Mi-70-37-17-nexthop-Internet|70.37.0.0/17|Internet|
|Mi-70-37-128-18-nexthop-Internet|70.37.128.0/18|Internet|
|Mi-91-190-216-21-nexthop-Internet|91.190.216.0/21|Internet|
|Mi-94-245-64-18-nexthop-Internet|94.245.64.0/18|Internet|
|Mi-103-9-8-22-nexthop-Internet|103.9.8.0/22|Internet|
|Mi-103-25-156-24-nexthop-Internet|103.25.156.0/24|Internet|
|Mi-103-25-157-24-nexthop-Internet|103.25.157.0/24|Internet|
|Mi-103-25-158-23-nexthop-Internet|103.25.158.0/23|Internet|
|Mi-103-36-96-22-nexthop-Internet|103.36.96.0/22|Internet|
|Mi-103-255-140-22-nexthop-Internet|103.255.140.0/22|Internet|
|Mi-104-40-13-nexthop-Internet|104.40.0.0/13|Internet|
|Mi-104-146-15-nexthop-Internet|104.146.0.0/15|Internet|
|Mi-104-208-13-nexthop-Internet|104.208.0.0/13|Internet|
|Mi-111-221-16-20-nexthop-Internet|111.221.16.0/20|Internet|
|Mi-111-221-64-18-nexthop-Internet|111.221.64.0/18|Internet|
|Mi-129-75-16-nexthop-Internet|129.75.0.0/16|Internet|
|Mi-131-107-16-nexthop-Internet|131.107.0.0/16|Internet|
|Mi-131-253-1-24-nexthop-Internet|131.253.1.0/24|Internet|
|Mi-131-253-3-24-nexthop-Internet|131.253.3.0/24|Internet|
|Mi-131-253-5-24-nexthop-Internet|131.253.5.0/24|Internet|
|Mi-131-253-6-24-nexthop-Internet|131.253.6.0/24|Internet|
|Mi-131-253-8-24-nexthop-Internet|131.253.8.0/24|Internet|
|Mi-131-253-12-22-nexthop-Internet|131.253.12.0/22|Internet|
|Mi-131-253-16-23-nexthop-Internet|131.253.16.0/23|Internet|
|Mi-131-253-18-24-nexthop-Internet|131.253.18.0/24|Internet|
|Mi-131-253-21-24-nexthop-Internet|131.253.21.0/24|Internet|
|Mi-131-253-22-23-nexthop-Internet|131.253.22.0/23|Internet|
|Mi-131-253-24-21-nexthop-Internet|131.253.24.0/21|Internet|
|Mi-131-253-32-20-nexthop-Internet|131.253.32.0/20|Internet|
|Mi-131-253-61-24-nexthop-Internet|131.253.61.0/24|Internet|
|Mi-131-253-62-23-nexthop-Internet|131.253.62.0/23|Internet|
|Mi-131-253-64-18-nexthop-Internet|131.253.64.0/18|Internet|
|Mi-131-253-128-17-nexthop-Internet|131.253.128.0/17|Internet|
|Mi-132-245-16-nexthop-Internet|132.245.0.0/16|Internet|
|Mi-134-170-16-nexthop-Internet|134.170.0.0/16|Internet|
|Mi-134-177-16-nexthop-Internet|134.177.0.0/16|Internet|
|Mi-137-116-15-nexthop-Internet|137.116.0.0/15|Internet|
|Mi-137-135-16-nexthop-Internet|137.135.0.0/16|Internet|
|Mi-138-91-16-nexthop-Internet|138.91.0.0/16|Internet|
|Mi-138-196-16-nexthop-Internet|138.196.0.0/16|Internet|
|Mi-139-217-16-nexthop-Internet|139.217.0.0/16|Internet|
|Mi-139-219-16-nexthop-Internet|139.219.0.0/16|Internet|
|Mi-141-251-16-nexthop-Internet|141.251.0.0/16|Internet|
|Mi-146-147-16-nexthop-Internet|146.147.0.0/16|Internet|
|Mi-147-243-16-nexthop-Internet|147.243.0.0/16|Internet|
|Mi-150-171-16-nexthop-Internet|150.171.0.0/16|Internet|
|Mi-150-242-48-22-nexthop-Internet|150.242.48.0/22|Internet|
|Mi-157-54-15-nexthop-Internet|157.54.0.0/15|Internet|
|Mi-157-56-14-nexthop-Internet|157.56.0.0/14|Internet|
|Mi-157-60-16-nexthop-Internet|157.60.0.0/16|Internet|
|Mi-167-105-16-nexthop-Internet|167.105.0.0/16|Internet|
|Mi-167-220-16-nexthop-Internet|167.220.0.0/16|Internet|
|Mi-168-61-16-nexthop-Internet|168.61.0.0/16|Internet|
|Mi-168-62-15-nexthop-Internet|168.62.0.0/15|Internet|
|Mi-191-232-13-nexthop-Internet|191.232.0.0/13|Internet|
|Mi-192-32-16-nexthop-Internet|192.32.0.0/16|Internet|
|Mi-192-48-225-24-nexthop-Internet|192.48.225.0/24|Internet|
|Mi-192-84-159-24-nexthop-Internet|192.84.159.0/24|Internet|
|Mi-192-84-160-23-nexthop-Internet|192.84.160.0/23|Internet|
|Mi-192-197-157-24-nexthop-Internet|192.197.157.0/24|Internet|
|Mi-193-149-64-19-nexthop-Internet|193.149.64.0/19|Internet|
|Mi-193-221-113-24-nexthop-Internet|193.221.113.0/24|Internet|
|Mi-194-69-96-19-nexthop-Internet|194.69.96.0/19|Internet|
|Mi-194-110-197-24-nexthop-Internet|194.110.197.0/24|Internet|
|Mi-198-105-232-22-nexthop-Internet|198.105.232.0/22|Internet|
|Mi-198-200-130-24-nexthop-Internet|198.200.130.0/24|Internet|
|Mi-198-206-164-24-nexthop-Internet|198.206.164.0/24|Internet|
|Mi-199-60-28-24-nexthop-Internet|199.60.28.0/24|Internet|
|Mi-199-74-210-24-nexthop-Internet|199.74.210.0/24|Internet|
|Mi-199-103-90-23-nexthop-Internet|199.103.90.0/23|Internet|
|Mi-199-103-122-24-nexthop-Internet|199.103.122.0/24|Internet|
|Mi-199-242-32-20-nexthop-Internet|199.242.32.0/20|Internet|
|Mi-199-242-48-21-nexthop-Internet|199.242.48.0/21|Internet|
|Mi-202-89-224-20-nexthop-Internet|202.89.224.0/20|Internet|
|Mi-204-13-120-21-nexthop-Internet|204.13.120.0/21|Internet|
|Mi-204-14-180-22-nexthop-Internet|204.14.180.0/22|Internet|
|Mi-204-79-135-24-nexthop-Internet|204.79.135.0/24|Internet|
|Mi-204-79-179-24-nexthop-Internet|204.79.179.0/24|Internet|
|Mi-204-79-181-24-nexthop-Internet|204.79.181.0/24|Internet|
|Mi-204-79-188-24-nexthop-Internet|204.79.188.0/24|Internet|
|Mi-204-79-195-24-nexthop-Internet|204.79.195.0/24|Internet|
|Mi-204-79-196-23-nexthop-Internet|204.79.196.0/23|Internet|
|Mi-204-79-252-24-nexthop-Internet|204.79.252.0/24|Internet|
|Mi-204-152-18-23-nexthop-Internet|204.152.18.0/23|Internet|
|Mi-204-152-140-23-nexthop-Internet|204.152.140.0/23|Internet|
|Mi-204-231-192-24-nexthop-Internet|204.231.192.0/24|Internet|
|Mi-204-231-194-23-nexthop-Internet|204.231.194.0/23|Internet|
|Mi-204-231-197-24-nexthop-Internet|204.231.197.0/24|Internet|
|Mi-204-231-198-23-nexthop-Internet|204.231.198.0/23|Internet|
|Mi-204-231-200-21-nexthop-Internet|204.231.200.0/21|Internet|
|Mi-204-231-208-20-nexthop-Internet|204.231.208.0/20|Internet|
|Mi-204-231-236-24-nexthop-Internet|204.231.236.0/24|Internet|
|Mi-205-174-224-20-nexthop-Internet|205.174.224.0/20|Internet|
|Mi-206-138-168-21-nexthop-Internet|206.138.168.0/21|Internet|
|Mi-206-191-224-19-nexthop-Internet|206.191.224.0/19|Internet|
|Mi-207-46-16-nexthop-Internet|207.46.0.0/16|Internet|
|Mi-207-68-128-18-nexthop-Internet|207.68.128.0/18|Internet|
|Mi-208-68-136-21-nexthop-Internet|208.68.136.0/21|Internet|
|Mi-208-76-44-22-nexthop-Internet|208.76.44.0/22|Internet|
|Mi-208-84-21-nexthop-Internet|208.84.0.0/21|Internet|
|Mi-209-240-192-19-nexthop-Internet|209.240.192.0/19|Internet|
|Mi-213-199-128-18-nexthop-Internet|213.199.128.0/18|Internet|
|Mi-216-32-180-22-nexthop-Internet|216.32.180.0/22|Internet|
|Mi-216-220-208-20-nexthop-Internet|216.220.208.0/20|Internet|
|Mi-23-96-13-nexthop-Internet|23.96.0.0/13|Internet|
|Mi-42-159-16-nexthop-Internet|42.159.0.0/16|Internet|
|Mi-51-13-17-nexthop-Internet|51.13.0.0/17|Internet|
|Mi-51-107-16-nexthop-Internet|51.107.0.0/16|Internet|
|Mi-51-116-16-nexthop-Internet|51.116.0.0/16|Internet|
|Mi-51-120-16-nexthop-Internet|51.120.0.0/16|Internet|
|Mi-51-120-128-17-nexthop-Internet|51.120.128.0/17|Internet|
|Mi-51-124-16-nexthop-Internet|51.124.0.0/16|Internet|
|Mi-102-37-18-nexthop-Internet|102.37.0.0/18|Internet|
|Mi-102-133-16-nexthop-Internet|102.133.0.0/16|Internet|
|Mi-199-30-16-20-nexthop-Internet|199.30.16.0/20|Internet|
|Mi-204-79-180-24-nexthop-Internet|204.79.180.0/24|Internet|
||||

\* MI-SUBNET verwijst naar het IP-adres bereik voor het SUBNET in de vorm x. x. x. x/y. U kunt deze informatie vinden in het Azure Portal, in eigenschappen van subnet.

\** Als het doel adres is voor een van de services van Azure, stuurt Azure het verkeer rechtstreeks naar de service via het backbone-netwerk van Azure, in plaats van het verkeer naar Internet te routeren. Verkeer tussen Azure-services loopt niet via internet, ongeacht de Azure-regio waarin het virtuele netwerk zich bevindt of in welke Azure-regio een instantie van de Azure-service is geïmplementeerd. Raadpleeg de UDR- [documentatie pagina](../../virtual-network/virtual-networks-udr-overview.md)voor meer informatie.

Daarnaast kunt u vermeldingen aan de route tabel toevoegen om verkeer met on-premises privé-IP-adresbereiken als bestemming te routeren via de gateway van het virtuele netwerk of het virtuele netwerk apparaat (NVA).

Als het virtuele netwerk een aangepaste DNS bevat, moet de aangepaste DNS-server open bare DNS-records kunnen omzetten. Voor het gebruik van aanvullende functies, zoals Azure AD-verificatie, moet u mogelijk aanvullende FQDN-kenmerken oplossen. Zie [een aangepaste DNS instellen](custom-dns-configure.md)voor meer informatie.

### <a name="networking-constraints"></a>Netwerk beperkingen

**TLS 1,2 wordt afgedwongen voor uitgaande verbindingen**: In januari 2020 heeft micro soft TLS 1,2 afgedwongen voor verkeer binnen de service in alle Azure-Services. Voor Azure SQL Managed instance heeft dit tot gevolg dat TLS 1,2 wordt afgedwongen voor uitgaande verbindingen die worden gebruikt voor replicatie en gekoppelde server verbindingen met SQL Server. Als u versies van SQL Server ouder dan 2016 met SQL Managed instance gebruikt, moet u ervoor zorgen dat er [TLS 1,2-specifieke updates](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) zijn toegepast.

De volgende functies van het virtuele netwerk worden momenteel *niet ondersteund* met SQL Managed instance:

- **Micro soft-peering**: het inschakelen van [micro soft-peering](../../expressroute/expressroute-faqs.md#microsoft-peering) op ExpressRoute-circuits die rechtstreeks of buiten gebruik worden gepeerd met een virtueel netwerk waarbij het SQL Managed instance-exemplaar van invloed is op de verkeers stroom tussen onderdelen van een SQL Managed instance binnen het virtuele netwerk en de services waarvan deze afhankelijk is, waardoor Implementaties van SQL Managed instance naar virtueel netwerk waarop micro soft-peering al is ingeschakeld, zullen naar verwachting mislukken.
- **Globale Virtual Network-peering**: de [peering van virtuele netwerken](../../virtual-network/virtual-network-peering-overview.md) tussen Azure-regio's werkt niet voor door SQL beheerde instanties die worden geplaatst in subnetten die zijn gemaakt vóór 9/22/2020.
- **AzurePlatformDNS**: het gebruik van [de AzurePlatformDNS-servicetag voor](../../virtual-network/service-tags-overview.md) het blok keren van de DNS-omzetting van het platform zou het SQL Managed instance niet beschikbaar laten. Hoewel SQL Managed instance de door de klant gedefinieerde DNS voor DNS-omzetting in de Engine ondersteunt, is er een afhankelijkheid van platform-DNS voor platform bewerkingen.
- **NAT-gateway**: door [Azure Virtual Network NAT](../../virtual-network/nat-overview.md) te gebruiken voor het beheren van uitgaande verbindingen met een specifiek openbaar IP-adres, wordt het door SQL beheerde exemplaar niet beschikbaar weer gegeven. De service SQL Managed instance is momenteel beperkt tot het gebruik van basis load balancer die geen samen werking van binnenkomende en uitgaande stromen biedt met Virtual Network NAT.
- **IPv6 voor Azure Virtual Network**: het implementeren van een beheerd exemplaar naar [dual stack IPv4/IPv6-virtuele netwerken](../../virtual-network/ipv6-overview.md) wordt verwacht. Het koppelen van een netwerk beveiligings groep (NSG) of route tabel (UDR) met IPv6-adres voorvoegsels naar het subnet van het SQL-beheerde exemplaar of het toevoegen van IPv6-adres voorvoegsels aan NSG of UDR die al is gekoppeld aan het subnet van het beheerde exemplaar, zou het SQL Managed instance-exemplaar niet beschikbaar weer geven. Implementaties van SQL Managed instance naar een subnet met NSG en UDR die al IPv6-voor voegsels hebben, zullen naar verwachting mislukken.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure SQL Managed instance?](sql-managed-instance-paas-overview.md)voor een overzicht.
- Meer informatie over het [instellen van een nieuw virtueel Azure-netwerk](virtual-network-subnet-create-arm-template.md) of een [bestaand virtueel Azure-netwerk](vnet-existing-add-subnet.md) waar u een SQL-beheerd exemplaar kunt implementeren.
- [Bereken de grootte van het subnet](vnet-subnet-determine-size.md) waar u het SQL Managed instance wilt implementeren.
- Meer informatie over het maken van een beheerd exemplaar:
  - Van de [Azure Portal](instance-create-quickstart.md).
  - Met behulp van [Power shell](scripts/create-configure-managed-instance-powershell.md).
  - Met behulp van [een Azure Resource Manager sjabloon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Met behulp van [een Azure Resource Manager sjabloon (met behulp van JumpBox, met SSMS inbegrepen)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).