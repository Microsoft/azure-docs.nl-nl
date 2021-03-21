---
title: Het juiste implementatie type-Azure Database for MariaDB selecteren
description: In dit artikel wordt beschreven welke factoren u moet overwegen voordat u Azure Database for MariaDB implementeert als Infrastructure as a Service (IaaS) of platform as a Service (PaaS).
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d8885e374142b3d916803fc472ae18351ca6d470
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664515"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Kies de juiste MariaDB-Server optie in azure

Met Azure kunnen de workloads van uw MariaDB-server worden uitgevoerd in een gehoste VM-infra structuur als een service (IaaS) of als een gehoste platform as a Service (PaaS). PaaS heeft meerdere implementatie opties en er zijn service lagen binnen elke implementatie optie. Wanneer u een keuze maakt tussen IaaS en PaaS, moet u beslissen of u uw database wilt beheren, patches wilt toepassen en back-ups wilt maken, of dat u deze bewerkingen delegeert aan Azure.

Houd rekening met de volgende twee opties bij het nemen van uw beslissing:

- **Azure database for MariaDB:** Deze optie is een volledig beheerde MariaDB-data base-engine op basis van de stabiele versie van de MariaDB-Community-editie. Deze relationele Database as a Service (DBaaS), die wordt gehost op het Azure-Cloud platform, valt in de branche categorie van PaaS.

  Met een beheerd exemplaar van MariaDB op Azure kunt u ingebouwde functies gebruiken die anders uitgebreide configuratie vereisen wanneer de MariaDB-server on-premises of in een Azure-VM is.

  Wanneer u MariaDB als een service gebruikt, betaalt u met opties om te schalen of uit te schalen voor meer controle zonder onderbreking. En in tegens telling tot zelfstandige MariaDB-server heeft Azure Database for MariaDB extra functies, zoals ingebouwde hoge Beschik baarheid, intelligentie en beheer.

- **MariaDB op Azure-vm's:** Deze optie valt buiten de branche categorie van IaaS. Met deze service kunt u MariaDB server uitvoeren in een volledig beheerde virtuele machine op het Azure-Cloud platform. Alle recente versies en edities van MariaDB kunnen worden geïnstalleerd op een virtuele machine met IaaS.

  In het belangrijkste verschil van Azure Database for MariaDB biedt MariaDB op virtuele machines van Azure controle over de data base-engine. Met deze controle gaat echter ook de verantwoordelijkheid voor het beheren van de VM's en veel databasebeheertaken gepaard. Deze taken omvatten het onderhouden van en toepassen van patches op databaseservers, gegevensherstel en ontwerp met hoge beschikbaarheid.

De belangrijkste verschillen tussen deze opties worden weergegeven in de volgende tabel:

| Kenmerk          | Azure Database for MariaDB | MariaDB op Azure Vm's    |
|:-------------------|:-----------------------------|:--------------------|
| Service Level Agreement (SLA)                | Biedt een SLA van 99,99% Beschik baarheid| Maxi maal 99,95% Beschik baarheid met twee of meer exemplaren in dezelfde beschikbaarheidsset.<br/><br/>99,9% Beschik baarheid met een virtuele machine met één exemplaar met Premium Storage.<br/><br/>99,99% met Beschikbaarheidszones met meerdere exemplaren in meerdere beschikbaarheids sets.<br/><br/>Zie de [virtual machines Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Patches besturings systeem        | Automatisch  | Beheerd door klanten |
| MariaDB-patching     | Automatisch  | Beheerd door klanten |
| Hoge beschikbaarheid | Het model voor hoge Beschik baarheid (HA) is gebaseerd op ingebouwde failover-mechanismen voor wanneer een onderbreking op knooppunt niveau optreedt. In dergelijke gevallen maakt de service automatisch een nieuw exemplaar en koppelt de opslag aan dit exemplaar. | Klanten bouwen, implementeren, testen en onderhouden hoge Beschik baarheid. Mogelijkheden zijn onder meer Failover Clustering, altijd op groeps replicatie, logboek verzending of transactionele replicatie.|
| Zoneredundantie | Momenteel niet ondersteund | Virtuele Azure-machines kunnen worden ingesteld om te worden uitgevoerd in verschillende beschikbaarheids zones. Voor een on-premises oplossing moeten klanten hun eigen secundaire datacentrum maken, beheren en onderhouden.|
| Hybride scenario's | Met [replicatie van inkomende gegevens](concepts-data-in-replication.md)kunt u gegevens van een externe MariaDB-server synchroniseren met de Azure database for MariaDB-service. De externe server kan on-premises, in virtuele machines of een database service worden gehost door andere cloud providers.<br/><br/> Met de functie [replica lezen](concepts-read-replicas.md) kunt u gegevens van een Azure database for MariaDB bron server naar Maxi maal vijf alleen-lezen replica servers repliceren. De replica's bevinden zich in dezelfde Azure-regio of in verschillende regio's. Alleen-lezen replica's worden asynchroon bijgewerkt met behulp van binlog-replicatie technologie.<br/><br/>De Lees replicatie tussen regio's is momenteel beschikbaar als open bare preview.| Beheerd door klanten
| Back-ups maken en herstellen | Maakt automatisch [Server back-ups](concepts-backup.md#backups) en slaat deze op in door de gebruiker geconfigureerde opslag die lokaal redundant of geo-redundant is. De service maakt gebruik van volledige, differentiële en back-ups van transactie logboeken | Beheerd door klanten |
| Database bewerkingen bewaken | Biedt klanten de mogelijkheid om [waarschuwingen](concepts-monitoring.md) in te stellen voor de database bewerking en om te reageren op drempel waarden. | Beheerd door klanten |
| Advanced Threat Protection | Biedt [geavanceerde beveiliging tegen bedreigingen](howto-database-threat-protection-portal.md). Deze beveiliging detecteert afwijkende activiteiten die ongebruikelijke en potentieel schadelijke pogingen voor toegang tot of exploiten van data bases aanduiden.<br/><br/>Advanced Threat Protection is momenteel beschikbaar als open bare preview.| Klanten moeten deze bescherming zelf bouwen.
| Herstel na noodgeval | Hiermee worden automatische back-ups opgeslagen in [lokaal redundante of geografisch redundante opslag](howto-restore-server-portal.md)die door de gebruiker is geconfigureerd. Met back-ups kunt u ook een server herstellen naar een bepaald tijdstip. De retentie tijd is een periode van 7 tot 35 dagen. De herstel bewerking wordt uitgevoerd met behulp van de Azure Portal. | Volledig beheerd door klanten. Verantwoordelijkheden zijn onder andere, maar zijn niet beperkt tot het plannen, testen, archiveren, opslaan en bewaren. Een extra optie is een Azure Recovery Services kluis gebruiken om back-ups te maken van Azure Vm's en data bases op Vm's. Deze optie is beschikbaar als preview-versie. |
| Aanbevelingen voor prestaties | Biedt klanten [aanbevelingen voor prestaties](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) op basis van door het systeem gegenereerde gebruiks logboek bestanden. De aanbevelingen helpen bij het optimaliseren van werk belastingen.<br/><br/>Aanbevelingen voor prestaties zijn momenteel beschikbaar als open bare preview. | Beheerd door klanten |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Zakelijke motivaties voor het kiezen van PaaS of IaaS

Er zijn verschillende factoren die van invloed kunnen zijn op uw beslissing om PaaS of IaaS te kiezen voor het hosten van uw MariaDB-data bases.

### <a name="cost"></a>Kosten

Beperkte financiering is vaak de primaire overweging voor het bepalen van de beste oplossing voor het hosten van uw data bases. Dit is waar, of u nu een startende onderneming met weinig geld bent of een team in een gevestigde onderneming dat met een strak budget werkt. In deze sectie worden de basis beginselen van facturering en licenties in azure beschreven, omdat deze van toepassing zijn op Azure Database for MariaDB en MariaDB op Azure-Vm's.

#### <a name="billing"></a>Billing

Azure Database for MariaDB is momenteel beschikbaar als een service in meerdere lagen met verschillende prijzen voor resources. Alle resources worden per uur gefactureerd tegen een vast bedrag. Zie [vCore-gebaseerd inkoop model](concepts-pricing-tiers.md)voor de meest recente informatie over de momenteel ondersteunde service lagen, reken grootten en opslag bedragen. U kunt Service lagen en reken grootten dynamisch aanpassen zodat deze overeenkomen met de verschillende doorvoer behoeften van uw toepassing. Uitgaand internetverkeer wordt bij u in rekening gebracht tegen het reguliere [tarief voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/).

Met Azure Database for MariaDB worden de database software automatisch geconfigureerd en bijgewerkt door micro soft. Deze geautomatiseerde acties verminderen de beheerkosten. Daarnaast heeft Azure Database for MariaDB [ingebouwde back-](concepts-backup.md) upfunctionaliteiten. Dankzij deze mogelijkheden kunt u aanzienlijk op kosten besparen, vooral wanneer u een groot aantal databases hebt. Daarentegen kunt u met MariaDB op virtuele machines van Azure een wille keurige MariaDB-versie kiezen en uitvoeren. Ongeacht welke MariaDB-versie u gebruikt, betaalt u voor de ingerichte virtuele machine en de kosten voor het specifieke MariaDB-licentie type dat wordt gebruikt.

Azure Database for MariaDB biedt ingebouwde hoge Beschik baarheid voor elk soort onderbreking op knooppunt niveau, terwijl de SLA-garantie van 99,99% voor de service nog steeds wordt gehandhaafd. Voor een hoge Beschik baarheid van de data base binnen Vm's moeten klanten echter de opties voor hoge Beschik baarheid gebruiken, zoals [MariaDB-replicatie](https://mariadb.com/kb/en/library/setting-up-replication/) die beschikbaar zijn in een MariaDB-data base. Het gebruik van een ondersteunde optie voor hoge beschikbaarheid biedt geen extra SLA. Maar u kunt wel een databasebeschikbaarheid van hoger dan 99,99% behalen tegen extra kosten en administratieve overhead.

Zie de volgende artikelen voor meer informatie over prijzen:
* [Azure Database for MariaDB prijzen](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Prijzen voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Beheer

Veel bedrijven besluiten zowel vanwege offloading van beheercomplexiteit als de kosten naar een cloudservice over te stappen. Met IaaS en PaaS, micro soft:

- Beheer van de onderliggende infrastructuur.
- Repliceert automatisch alle gegevens om herstel na nood gevallen mogelijk te maken.
- Hiermee wordt de database software geconfigureerd en bijgewerkt.
- Beheert de taak verdeling.
- Voert een transparante failover uit als er een server fout is opgetreden.

In de volgende lijst worden administratieve aandachtspunten voor elke optie beschreven:

* Met Azure Database for MariaDB kunt u uw data base blijven beheren. Maar u hoeft de database-engine, het besturingssysteem en de hardware niet langer te beheren. Voorbeelden van items die u kunt blijven beheren, zijn onder meer:

  - Databases
  - Aanmelden
  - Indexafstemming
  - Queryafstemming
  - Controleren
  - Beveiliging

  Daarnaast vereist het configureren van hoge beschikbaarheid voor een ander datacentrum nauwelijks tot geen configuratie of beheer.

* Met MariaDB op Azure Vm's hebt u volledige controle over het besturings systeem en de configuratie van de MariaDB-Server instantie. Met een VM kunt u bepalen wanneer u het besturings systeem en de database software bijwerkt of bijwerkt. U beslist ook wanneer u extra software, zoals een antivirustoepassing, installeert. Sommige geautomatiseerde functies worden geleverd om het toepassen van patches, het maken van een back-up en hoge beschikbaarheid aanzienlijk te vereenvoudigen. U kunt de grootte van de VM, het aantal schijven en hun opslagconfiguraties beheren. Zie [Groottes van virtuele machines en cloudservices voor Azure](../virtual-machines/sizes.md) voor meer informatie.

### <a name="time-to-move-to-azure"></a>Tijd om over te stappen op Azure

* Azure Database for MariaDB is de juiste oplossing voor toepassingen die in de Cloud zijn ontworpen wanneer ontwikkel aars productiviteit en snelle time-to-Market voor nieuwe oplossingen cruciaal zijn. Met programmatische functionaliteit die lijkt op DBA is de service geschikt voor cloudarchitecten en -ontwikkelaars omdat het de noodzaak voor het beheren van het onderliggende besturingssysteem en de database vermindert.

* Wanneer u de tijd en kosten voor het verkrijgen van nieuwe on-premises hardware wilt voor komen, is MariaDB op Azure Vm's de juiste oplossing voor toepassingen die een MariaDB-data base nodig hebben of toegang hebben tot MariaDB-functies in Windows of Linux. Deze oplossing is ook geschikt voor het migreren van bestaande on-premises toepassingen en data bases naar Azure, voor gevallen waarin Azure Database for MariaDB een slechte plaats is.

  Omdat het niet nodig is om de presentatie-, toepassings-en gegevens lagen te wijzigen, bespaart u tijd en budget bij het opnieuw ontwerpen van uw bestaande oplossing. In plaats daarvan kunt u zich richten op het migreren van al uw oplossingen naar Azure en het adresseren van een aantal prestatie optimalisaties die het Azure-platform mogelijk vereist.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure database for MariaDB prijzen](https://azure.microsoft.com/pricing/details/MariaDB/).
* Aan de slag gaan met het [maken van uw eerste server](quickstart-create-mariadb-server-database-using-azure-portal.md).
