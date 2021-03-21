---
title: Wat is Azure SQL?
description: 'Meer informatie over de verschillende opties in de services van de Azure SQL-serie: Azure SQL Database, Azure SQL Managed Instance en SQL Server in Azure VM.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server cloud, SQL Server in the cloud, PaaS database, cloud SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: ea097119111d5dbd5eba3c11aba549d201186e3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592180"
---
# <a name="what-is-azure-sql"></a>Wat is Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL is een serie beheerde, veilige en intelligente producten waarvoor de SQL Server-database-engine in de Azure-cloud wordt gebruikt.

- **Azure SQL Database**: ondersteun moderne cloudtoepassingen met een intelligente, beheerde databaseservice die serverloze rekenkracht biedt. 
- **Azure SQL Managed Instance**: moderniseer uw bestaande SQL Server-toepassingen op schaal met een intelligente, volledig beheerde instantie als een service, met bijna 100% functiepariteit met de SQL Server-database-engine. Geschikt voor de meeste migraties naar de cloud.
- **SQL Server op Azure-VM's**: maak gemakkelijk gebruik van lift-and-shift voor uw SQL Server-workloads en behoud 100% SQL Server-compatibiliteit en toegang op besturingssysteemniveau. 
 
Azure SQL is gebaseerd op de vertrouwde SQL Server-engine, zodat u toepassingen eenvoudig kunt migreren en de hulpmiddelen, talen en bronnen die u kent, kunt blijven gebruiken. Uw vaardigheden en ervaring worden overgebracht naar de cloud, zodat u nog meer kunt doen met wat u al hebt. 

Ontdek hoe elk product in het Azure SQL-gegevensplatform van Microsoft past en zo de juiste optie voor uw zakelijke vereisten biedt. Of u nu kostenbesparingen of minimaal beheer het belangrijkst vindt, met dit artikel kunt u beslissen welke benadering dat biedt wat u het belangrijkst voor het bedrijf vindt.

Als u geen ervaring hebt met Azure SQL, bekijk dan de video *Wat is Azure SQL* uit onze diepgaande [serie Azure SQL-video's](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Hoe kunnen we Azure SQL beter maken? [Doe de enquête](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="overview"></a>Overzicht

In de huidige gegevensgestuurde wereld is het bevorderen van digitale transformaties in toenemende mate afhankelijk van de mogelijkheid om enorme hoeveelheden gegevens te beheren en de mogelijkheden ervan te benutten. De huidige gegevens zijn echter steeds complexer, met gegevens die on-premises, in de cloud of aan de rand van het netwerk worden gehost. Ontwikkelaars die intelligente en gedetailleerde toepassingen bouwen, kunnen tegen beperkingen aanlopen die uiteindelijk van invloed kunnen zijn op hun ervaring. Beperkingen die voortvloeien uit incompatibele platforms, onvoldoende gegevensbeveiliging, ontoereikende resources en prijs-prestatiebarrières, leiden tot complexiteit waardoor de modernisering en ontwikkeling van apps kunnen worden belemmerd. 

Een van de eerste belangrijke punten in een discussie over Azure ten opzichte van on-premises SQL Server-databases is dat u alles kunt gebruiken. Het gegevensplatform van Microsoft maakt gebruik van SQL Server-technologie en maakt deze beschikbaar op fysieke on-premises machines, privécloudomgevingen, externe gehoste privécloudomgevingen en de openbare cloud. 


### <a name="fully-managed-and-always-up-to-date"></a>Volledig beheerd en altijd up-to-date 

Besteed meer tijd aan innoveren en minder tijd aan het patchen en bijwerken van databases en het maken van back-ups. Azure is de enige cloud met 'evergreen' SQL waarmee automatisch de meest recente updates en patches worden toegepast, zodat uw databases altijd up-to-date zijn en er geen gedoe met het aflopen van ondersteuning is. Zelfs complexe taken, zoals prestaties bijstellen, hoge beschikbaarheid, herstel na noodgevallen en back-ups, zijn geautomatiseerd, zodat u zich op de toepassingen kunt richten.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Uw gegevens beveiligen met ingebouwde intelligente beveiliging 

Azure controleert uw gegevens voortdurend op bedreigingen. Met Azure SQL kunt u het volgende doen:

- Mogelijke bedreigingen in realtime oplossen met intelligente [Advanced Threat Detection](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) en proactieve waarschuwingen voor de evaluatie van beveiligingsproblemen. 
- Profiteren van toonaangevende beveiliging met meerdere lagen met [ingebouwd beveiligingsbeheer](https://azure.microsoft.com/overview/security/), waaronder T-SQL, verificatie, netwerken en sleutelbeheer. 
- Gebruikmaken van de meest uitgebreide [nalevings](https://azure.microsoft.com/overview/trusted-cloud/compliance/)dekking van elke clouddatabaseservice. 


### <a name="business-motivations"></a>Zakelijke motivaties

Er zijn verschillende factoren die van invloed kunnen zijn op uw beslissing om te kiezen tussen de verschillende gegevensaanbiedingen:

- [Kosten](#cost): bij zowel PaaS als IaaS worden bij de basisprijs de onderliggende infrastructuur en de licenties gedekt. Met de optie IaaS moet u echter extra tijd en resources investeren om uw database te beheren, terwijl bij de optie PaaS deze beheerfuncties in de prijs zijn opgenomen. Met IaaS kunt u resources afsluiten als u ze niet gebruikt om de kosten te verlagen, terwijl PaaS altijd wordt uitgevoerd, tenzij u uw resources verwijdert en opnieuw maakt wanneer dat nodig is.
- [Beheer](#administration): PaaS-opties verminderen de hoeveelheid tijd die u moet investeren om de database te beheren. Maar bij deze opties kunt u echter ook minder aangepaste beheertaken en -scripts uitvoeren. Zo wordt CLR niet ondersteund bij SQL Database, maar wordt wel ondersteuning geboden bij een instantie van SQL Managed Instance. Daarnaast bieden de implementatieopties in PaaS geen ondersteuning voor het gebruik van traceringsvlaggen.
- [Service Level Agreement](#service-level-agreement-sla): zowel IaaS als PaaS bieden een hoge, voor de branche standaard SLA. De PaaS-optie garandeert een SLA van 99,99%, terwijl IaaS een SLA van 99,95% voor infrastructuur garandeert, wat betekent dat u extra mechanismen moet implementeren om de beschikbaarheid van uw databases te garanderen. U kunt een SLA van 99,99% bereiken door een extra virtuele SQL-machine te maken en de SQL Server-oplossing voor AlwaysOn-beschikbaarheidsgroepen met hoge beschikbaarheid te implementeren. 
- [Tijd om over te stappen op Azure](#market): SQL Server op een virtuele Azure-machine is gelijk aan uw omgeving, dus migratie van on-premises naar de Azure-VM is niet anders dan het verplaatsen van de databases van een on-premises server naar een andere. Met SQL Managed Instance kunt u ook eenvoudig migreren; u moet echter enkele wijzigingen toepassen vóór de migratie. 


## <a name="service-comparison"></a>Servicevergelijking

   ![SQL Server-cloudopties: SQL Server op IaaS of SaaS-SQL Database in de cloud.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Zoals te zien is in het diagram, wordt elke serviceaanbieding gekenmerkt door het niveau van beheer dat u hebt over de infrastructuur en de mate van kostenefficiëntie.

In Azure kunt u uw SQL Server-workloads uitvoeren als een gehoste service ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) of een gehoste infrastructuur ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). Binnen PaaS hebt u meerdere productopties en servicelagen binnen elke optie. Bij het kiezen tussen PaaS of IaaS is het belangrijk te bepalen of u uw database wilt beheren, patches wilt toepassen en back-ups wilt maken, of dat u deze bewerkingen wilt delegeren naar Azure.

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md) is een relationele database-as-a-service (DBaaS) die wordt gehost in Azure en die in de branchecategorie *Platform-as-a-Service (PaaS)* valt. 
- Geschikt voor moderne cloudtoepassingen waarvoor u de nieuwste stabiele SQL Server-functies wilt gebruiken en weinig tijd in ontwikkeling en marketing wilt investeren. 
- Een volledig beheerde SQL Server-database-engine die is gebaseerd op de nieuwste stabiele Enterprise Edition van SQL Server. SQL Database heeft twee implementatieopties die zijn gebouwd op gestandaardiseerde hardware en software die eigendom is van, en wordt gehost en beheerd door Microsoft. 

Met SQL Server kunt u ingebouwde functies en functionaliteit gebruiken die een uitgebreide configuratie vereisen (on-premises of op een virtuele Azure-machine). Als u SQL Database gebruikt, betaalt u per gebruik met opties voor het omhoog schalen voor meer vermogen zonder onderbrekingen. SQL Database heeft enkele extra functies die niet beschikbaar zijn in SQL Server, zoals ingebouwde hoge beschikbaarheid, intelligentie en beheer.


Azure SQL Database biedt de volgende implementatieopties:
  - Als een [*individuele database*](database/single-database-overview.md) met een eigen set resources die wordt beheerd via een [logische SQL-server](database/logical-servers.md). Een individuele database is vergelijkbaar met een [ingesloten database](/sql/relational-databases/databases/contained-databases) in SQL Server. Deze optie is geoptimaliseerd voor de ontwikkeling van moderne nieuwe toepassingen voor de cloud. Er zijn [hyperscale](database/service-tier-hyperscale.md)- en [serverloze](database/serverless-tier-overview.md) opties beschikbaar.
  - Een [*elastische pool*](database/elastic-pool-overview.md) ofwel een verzameling databases met een gedeelde set resources die wordt beheerd via een [logische SQL-server](database/logical-servers.md). Individuele databases kunnen naar en uit een elastische pool worden verplaatst. Deze optie is geoptimaliseerd voor de ontwikkeling van moderne nieuwe toepassingen voor de cloud met behulp van het multitenant-SaaS-toepassingspatroon. Elastische pools bieden een rendabele oplossing voor het beheer van de prestaties van meerdere databases die variabele gebruikspatronen hebben.

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

[Azure SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) valt in de branchecategorie *Platform-as-a-Service (PaaS)* en is geschikt voor de meeste migraties naar de cloud. SQL Managed Instance is een verzameling systeem- en gebruikersdatabases met een gedeelde set resources die gereed is voor lift-and-shift.  
- Geschikt voor nieuwe toepassingen of bestaande on-premises toepassingen waarvoor de nieuwste stabiele SQL Server-functies moeten worden gebruikt en die met minimale wijzigingen worden gemigreerd naar de cloud. Een instantie van SQL Managed Instance is vergelijkbaar met een instantie van de [Microsoft SQL Server-database-engine](/sql/database-engine/sql-server-database-engine-overview) waarbij gedeelde resources voor databases en aanvullende functies voor het bereik van de instantie worden geboden. 
- SQL Managed Instance ondersteunt databasemigratie vanaf on-premises met minimale of geen wijzigingen aan databases. Deze optie biedt alle PaaS-voordelen van Azure SQL Database, maar voegt mogelijkheden toe die eerder alleen beschikbaar waren in SQL Server-VM's. Hieronder vallen een systeemeigen virtueel netwerk en een compatibiliteit van bijna 100% met on-premises SQL Server. Instanties van SQL Managed Instance bieden volledige compatibiliteit met SQL Server-toegang en -functies voor de migratie van SQL Server-exemplaren naar Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server op virtuele Azure-machine

[SQL Server op een virtuele Azure-machine](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) valt in de branchecategorie *Infrastructure-as-a-Service (IaaS)* en stelt u in staat SQL Server uit te voeren binnen een volledig beheerde virtuele machine (VM) in Azure. 
- Geschikt voor migraties en toepassingen waarvoor toegang op besturingssysteemniveau is vereist. Virtuele SQL-machines in Azure zijn klaar voor lift-and-shift voor bestaande toepassingen waarvoor een snelle migratie naar de cloud met minimale of geen wijzigingen nodig is. Virtuele SQL-machines bieden volledige beheermogelijkheden voor de SQL Server-instantie en het onderliggende besturingssysteem voor migratie naar Azure. 
- Snelle ontwikkelings- en testscenario's als u geen on-premises SQL Server-hardware wilt kopen die niet bestemd is voor productie. Virtuele SQL-machines worden ook uitgevoerd op gestandaardiseerde hardware die eigendom is van en die wordt gehost en beheerd door Microsoft. Wanneer u virtuele SQL-machines gebruikt, kunt u eenvoudig een bestaande licentie gebruiken, of kunt u een SQL Server-licentie gebruiken die al is opgenomen in een installatiekopie van SQL Server en alleen betalen voor wanneer u deze gebruikt. U kunt de VM ook stoppen of hervatten als dat nodig is. 
- SQL Server die is geïnstalleerd en wordt gehost in de cloud, wordt uitgevoerd op virtuele Windows Server- of Linux-machines die worden uitgevoerd in Azure, ook wel bekend als een infrastructuur als een service (IaaS). Virtuele SQL-machines zijn een goede optie voor het migreren van on-premises SQL Server-databases en -toepassingen zonder databases te wijzigen. Alle recente versies en edities van SQL Server zijn beschikbaar voor installatie op een virtuele IaaS-machine. 

    Het belangrijkste verschil van SQL Database en SQL Managed Instance is dat met SQL Server op virtuele Azure-machines volledige controle over de database-engine mogelijk is. U kunt kiezen wanneer u het onderhoud of de toepassing van een patch wilt starten, het herstelmodel wijzigen in eenvoudig of met bulksgewijs geregistreerde wijzigingen, de service onderbreken of starten wanneer dit nodig is en de SQL Server-database-engine volledig aanpassen. Deze extra beheermogelijkheden brengen met zich mee dat u verantwoordelijk bent voor het beheer van de virtuele machine.
- Geoptimaliseerd om bestaande toepassingen te migreren naar Azure of bestaande on-premises toepassingen uit te breiden naar de cloud in hybride implementaties. Bovendien kunt u SQL Server op een virtuele machine gebruiken om traditionele SQL Server-toepassingen te ontwikkelen en te testen. Met virtuele SQL-machines hebt u de volledige beheerdersrechten van een speciale instantie van SQL Server en een cloud-VM. Het is een ideale keuze als een organisatie al IT-bronnen beschikbaar heeft voor het onderhouden van de virtuele machines. Met deze mogelijkheden kunt u een aangepast systeem bouwen voor de specifieke prestaties en beschikbaarheidsvereisten van uw toepassing.

Andere verschillen vindt u in de volgende tabel, maar *zowel SQL Database als SQL Managed Instance zijn geoptimaliseerd om de totale beheerkosten te verlagen tot het minimum voor het inrichten en beheren van veel databases.* Lopende beheerkosten worden verminderd omdat u geen virtuele machines, besturingssysteem of databasesoftware hoeft te beheren. U hoeft upgrades, hoge beschikbaarheid of [back-ups](database/automated-backups-overview.md) niet te beheren. 

Over het algemeen kunnen SQL Database en SQL Managed Instance het aantal databases dat wordt beheerd door één IT- of ontwikkelingsresource aanzienlijk verhogen. [Elastische pools](database/elastic-pool-overview.md) ondersteunen ook SaaS-multitenant-toepassingsarchitecturen met functies zoals tenantisolatie en de mogelijkheid om te schalen om de kosten te verlagen door resources te delen tussen databases. [SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) biedt ondersteuning voor functies voor het bereik van de instantie, waardoor bestaande toepassingen eenvoudig kunnen worden gemigreerd en resources worden gedeeld tussen databases.

### <a name="comparison-table"></a>Vergelijkingstabel

| Azure SQL Database | Azure SQL Managed Instance | SQL Server op virtuele Azure-machine |
| :--- | :--- | :--- |
|Ondersteunt de meeste on-premises mogelijkheden op databaseniveau. De meest gebruikte SQL Server-functies zijn beschikbaar.<br/>Gegarandeerde beschikbaarheid van 99,995%.<br/>Ingebouwde back-ups, patches, herstel.<br/>Nieuwste stabiele versie van de database-engine.<br/>De mogelijkheid om de benodigde resources (CPU/opslag) toe te wijzen aan afzonderlijke databases.<br/>Ingebouwde geavanceerde intelligentie en beveiliging.<br/>Onlinewijziging van resources (CPU/opslag).| Biedt ondersteuning voor vrijwel alle on-premises mogelijkheden op instantie- en databaseniveau. Hoge compatibiliteit met SQL Server.<br/>Gegarandeerde beschikbaarheid van 99,99%.<br/>Ingebouwde back-ups, patches, herstel.<br/>Nieuwste stabiele versie van de database-engine.<br/>Eenvoudige migratie vanaf SQL Server.<br/>Privé-IP-adres in Azure Virtual Network.<br/>Ingebouwde geavanceerde intelligentie en beveiliging.<br/>Onlinewijziging van resources (CPU/opslag).| U hebt volledige controle over de SQL Server-engine. Ondersteunt alle on-premises mogelijkheden.<br/>Maximale beschikbaarheid van 99,99%.<br/>Volledige pariteit met de overeenkomende versie van on-premises SQL Server.<br/>Gecorrigeerde, bekende versie van de database-engine.<br/>Eenvoudige migratie vanaf SQL Server.<br/>Privé-IP-adres in Azure Virtual Network.<br/>U hebt de mogelijkheid om toepassingen of services te implementeren op de host waarop SQL Server zich bevindt.|
|Migratie vanaf SQL Server kan lastig zijn.<br/>Sommige SQL Server-functies zijn niet beschikbaar.<br/>Geen gegarandeerde exacte onderhoudstijd (maar bijna transparant).<br/>De compatibiliteit met de SQL Server-versie kan alleen worden bereikt door databasecompatibiliteitsniveaus te gebruiken.<br/>Ondersteuning van privé-IP-adressen met [Azure Private Link](database/private-endpoint-overview.md).|Er is nog steeds een klein aantal SQL Server-functies niet beschikbaar.<br/>Geen gegarandeerde exacte onderhoudstijd (maar bijna transparant).<br/>De compatibiliteit met de SQL Server-versie kan alleen worden bereikt door databasecompatibiliteitsniveaus te gebruiken.|U moet uw back-ups en patches beheren.<br>U moet uw eigen oplossing voor hoge beschik baarheid implementeren.<br/>Er is een downtime bij het wijzigen van de resources (CPU/opslag)|
| Databases met een grootte van maximaal 100 TB. | Maximaal 8 TB. | SQL Server-exemplaren met maximaal 256 TB aan opslag. Het exemplaar kan zoveel databases ondersteunen als nodig is. |
| On-premises toepassing heeft toegang tot gegevens in Azure SQL Database. | [Systeemeigen implementatie van virtuele netwerken](managed-instance/vnet-existing-add-subnet.md) en connectiviteit met uw on-premises omgeving met behulp van Azure Express Route of VPN Gateway. | Met virtuele SQL-machines beschikt u over toepassingen die deels in de cloud draaien en deels on-premises. Zo kunt u uw on-premises netwerk en Active Directory-domein uitbreiden naar de cloud met [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Zie [On-premises gegevensoplossingen uitbreiden naar de cloud](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud) voor meer informatie over hybride cloudoplossingen. |


## <a name="cost"></a>Kosten

Of u nu een startup bent die geld nodig heeft of een team in een goedlopend bedrijf met een krap budget, beperkte middelen zijn vaak de belangrijkste reden bij het bepalen hoe u uw databases wilt hosten. In deze sectie vindt u meer informatie over de grondbeginselen voor facturering en licenties in Azure die horen bij de services uit de Azure SQL-serie.  Ook leest u over hoe u de totale kosten voor de toepassing berekent.

### <a name="billing-and-licensing-basics"></a>Grondbeginselen facturering en licenties

Op dit moment worden zowel **SQL Database** als **SQL Managed Instance** als een service verkocht en zijn ze beschikbaar met verschillende opties en in verschillende servicelagen met verschillende prijzen voor resources die allemaal per uur worden gefactureerd tegen een vast tarief op basis van de servicelaag en de gekozen rekenkracht. Zie [Aankoopmodel op basis van DTU voor SQL Database](database/service-tiers-dtu.md) en [Aankoopmodel op basis van vCore voor zowel SQL Database als SQL Managed Instance](database/service-tiers-vcore.md) voor de meest recente informatie over de huidige ondersteunde servicelagen, rekengrootten en opslaghoeveelheden.

- Bij SQL Database kunt u een servicelaag kiezen die aan uw behoeften voldoet met diverse prijzen vanaf $ 5/maand voor de basislaag en u kunt [elastische pools](database/elastic-pool-overview.md) maken om resources te delen tussen databases om de kosten te verlagen en gebruikspieken het hoofd te bieden.
- Bij SQL Managed Instance kunt u ook uw eigen licentie meenemen. Zie voor meer informatie over het meenemen van uw eigen licentie [License Mobility via Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/) of gebruik de [Azure Hybrid Benefit-calculator](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) om te zien hoe u **tot 40% bespaart**.

Bovendien wordt uitgaand internetverkeer in rekening gebracht bij u tegen het reguliere [tarief voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/). U kunt servicelagen en de rekenkracht dynamisch aanpassen om te voldoen aan de uiteenlopende doorvoerbehoeften van uw toepassing.

De databasesoftware wordt bij **SQL Database** en **SQL Managed Instance** automatisch geconfigureerd, gepatched en bijgewerkt door Azure, waardoor uw beheerkosten worden verlaagd. Bovendien kunt u met de [ingebouwde back-up](database/automated-backups-overview.md)mogelijkheden  aanzienlijk kosten besparen, vooral wanneer u een groot aantal databases hebt.

Met **SQL op Azure-VM's** kunt u alle door het platform geleverde SQL Server-installatiekopieën (met een licentie) gebruiken of uw SQL Server-licentie meenemen. Alle ondersteunde SQL Server-versies (2008R2, 2012, 2014, 2016, 2017, 2019) en edities (Developer, Express, Web, Standard, Enterprise) zijn beschikbaar. Bovendien zijn er Bring Your Own License-versies (BYOL) van de installatiekopieën beschikbaar. Wanneer u de door Azure geleverde installatiekopieën gebruikt, zijn de operationele kosten afhankelijk van de VM-grootte en de versie van SQL Server die u kiest. U betaalt licentiekosten van SQL Server en Windows of Linux Server per minuut, samen met de kosten van Azure Storage voor de VM-schijven, ongeacht de VM-grootte of versie van SQL Server. Met de optie factureren per minuut kunt u SQL Server blijven gebruiken zonder aanvullende SQL Server-licenties te kopen. Als u uw eigen SQL Server-licentie meeneemt naar Azure, worden alleen de kosten voor de server en opslag in rekening gebracht. Zie voor meer informatie over meenemen van uw eigen licentie [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/). Bovendien wordt uitgaand internetverkeer in rekening gebracht bij u tegen het reguliere [tarief voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>De totale kosten voor de toepassing berekenen

Als u een cloudplatform gaat gebruiken, bestaan de kosten van het uitvoeren van uw toepassing uit de kosten voor nieuwe ontwikkelingen en lopende beheerkosten, en de servicekosten voor het openbare cloudplatform.

Zie de volgende bronnen voor meer informatie over prijzen:

- [Prijzen van SQL Database en SQL Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/)
- [Prijzen van virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/) voor [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure prijscalculator](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Beheer

Veel bedrijven besluiten over te stappen naar een cloudservice zowel vanwege offloading van beheercomplexiteit als de kosten. Bij IaaS en PaaS wordt in Azure de onderliggende infrastructuur beheerd en worden alle gegevens automatisch gerepliceerd voor herstel na noodgevallen. Ook wordt de databasesoftware geconfigureerd en bijgewerkt, wordt de taakverdeling beheerd en wordt er transparante failover uitgevoerd als er een serverfout in het datacentrum optreedt.

- Met **SQL Database** en **SQL Managed Instance** kunt u uw database blijven beheren, maar u hoeft de database-engine, het besturingssysteem of de hardware niet langer te beheren. Voorbeelden van items die u kunt blijven beheren zijn onder meer databases en aanmeldingen, index en query afstemmen, en controle en beveiliging.  Daarnaast is voor het configureren van hoge beschikbaarheid voor een ander datacentrum nauwelijks configuratie of beheer vereist.
- Met **SQL op een Azure-VM** hebt u volledige controle over het besturingssysteem en de configuratie van het SQL Server-exemplaar. Met een VM kunt u zelf bepalen wanneer een upgrade van het besturingssysteem en de databasesoftware nodig is en wanneer u aanvullende software zoals antivirusprogramma's moet installeren. Sommige geautomatiseerde functies worden geleverd om patchen, het maken van een back-up en hoge beschikbaarheid aanzienlijk te vereenvoudigen. Bovendien kunt u de grootte van de VM, het aantal schijven en hun opslagconfiguraties beheren. Met Azure kunt de grootte van een VM indien nodig wijzigen. Zie voor meer informatie [Virtual Machine en Cloud Service Sizes for Azure](../virtual-machines/sizes.md).

## <a name="service-level-agreement-sla"></a>Service Level Agreement (SLA)

Voor veel IT-afdelingen is het voldoen aan uptimeverplichtingen van een Service Level Agreement (SLA) een topprioriteit. In dit gedeelte kijken we welke SLA van toepassing is op de databasehostingopties.

Voor zowel **Azure SQL Database** als **Azure SQL Managed Instance** biedt Microsoft een SLA met een beschikbaarheid van 99,99%. Zie [Service Level Agreement](https://azure.microsoft.com/support/legal/sla/sql-database/) voor de meest recente informatie.

Voor **SQL op een Azure-VM** biedt Microsoft een beschikbaarheids-SLA van 99,95% die alleen van toepassing is op de virtuele machine. Deze SLA heeft geen betrekking op de processen (zoals SQL Server) die worden uitgevoerd op de VM en vereist dat u ten minste twee exemplaren van de virtuele machine in een beschikbaarheidsset host. Voor de meest recente informatie, zie de [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Voor hoge beschikbaarheid (HA) van de database binnen virtuele machines moet u een van de ondersteunde opties voor hoge beschikbaarheid in SQL Server configureren, zoals [AlwaysOn-beschikbaarheidsgroepen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Het gebruik van een ondersteunde optie voor hoge beschikbaarheid biedt geen extra SLA, maar u kunt wel een databasebeschikbaarheid van >99,99% behalen.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Tijd om over te stappen op Azure

**Azure SQL Database** is de juiste oplossing voor toepassingen die zijn ontworpen voor de cloud, wanneer de productiviteit van ontwikkelaars en snelle implementatietijd voor nieuwe oplossingen essentieel zijn. Met programmatische DBA-functies is het ideaal voor cloud-architecten en -ontwikkelaars aangezien het de noodzaak voor het beheren van het onderliggende besturingssysteem en de database vermindert.

**Azure SQL Managed Instance** vereenvoudigt in hoge mate de migratie van bestaande toepassingen naar Azure, waardoor u snel gemigreerde databasetoepassingen naar de markt kunt brengen in Azure.

**SQL op een Azure-VM** is perfect als voor uw bestaande of nieuwe toepassingen grote databases of toegang tot alle functies in SQL Server of Windows/Linux vereist zijn en u geen tijd en geld wilt investeren in het aanschaffen van nieuwe on-premises hardware. Het is ook geschikt wanneer u bestaande on-premises toepassingen en databases zonder aanpassingen naar Azure wilt migreren. Dit is het geval wanneer SQL Database of SQL Managed Instance niet geschikt is. Omdat u de presentatie-, toepassings- en gegevenslagen niet hoeft te wijzigen, bespaart u tijd en geld op het opnieuw vormgeven van uw architectuur. In plaats daarvan kunt u zich richten op het migreren van uw oplossingen naar Azure en het uitvoeren van prestatieoptimalisatie die mogelijk wordt vereist door het Azure-platform. Zie voor meer informatie [Best practices prestaties for SQL Server on Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [Uw eerste Azure SQL Database](database/single-database-create-quickstart.md) om aan de slag te gaan met SQL Database.
- Zie [Uw eerste Azure SQL Managed Instance](managed-instance/instance-create-quickstart.md) om aan de slag te gaan met SQL Managed Instance. 
- Zie [Prijzen van SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Zie [Provision a SQL Server virtual machine in Azure](virtual-machines/windows/create-sql-vm-portal.md) om aan de slag te kunnen met SQL Server op Azure VM’s.
- [Identificeer de juiste SQL Database- of SQL Managed Instance-SKU voor uw on-premises database](/sql/dma/dma-sku-recommend-sql-db/).