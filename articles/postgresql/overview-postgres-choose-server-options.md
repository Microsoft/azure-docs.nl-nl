---
title: De juiste PostgreSQL-serveroptie in Azure kiezen
description: Biedt richtlijnen voor het kiezen van de juiste PostgreSQL-serveroptie voor uw implementaties.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: db80ae66761e49ce90f25a22f0e74cf8a1300796
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797237"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>De juiste PostgreSQL-serveroptie in Azure kiezen

Met Azure kunnen de workloads van uw PostgreSQL-server in een gehoste VM-infrastructuur als IaaS (Infrastructure as a Service) of als een gehoste PaaS (Platform as a Service) worden uitgevoerd. PaaS heeft meerdere implementatieopties, elk met meerdere servicelagen. Wanneer u een keuze maakt tussen IaaS en PaaS, moet u beslissen of u uw database wilt beheren, patches wilt toepassen en back-ups wilt maken, of dat u deze bewerkingen delegeert aan Azure.

Overweeg bij het nemen van uw beslissing de volgende drie opties in PaaS of kies voor virtuele Azure-machines (IaaS)
- [Azure Database for PostgreSQL Single Server](./overview-single-server.md)
- [Azure database for PostgreSQL Flexible Server](./flexible-server/overview.md)
- [Azure database for PostgreSQL Hyperscale (Citus)](hyperscale/index.yml)

De optie **PostgreSQL op Azure-VM's** valt in de branchecategorie van IaaS. Met deze service kunt u een PostgreSQL-server uitvoeren in een volledig beheerde virtuele machine op het Azure-cloudplatform. Alle recente versies en edities van PostgreSQL kunnen worden geïnstalleerd op een virtuele IaaS-machine. Het belangrijkste verschil van Azure Database for PostgreSQL is dat PostgreSQL op virtuele machines van Azure controle biedt over de database-engine. Met deze controle gaat echter ook de verantwoordelijkheid voor het beheren van de VM's en veel databasebeheertaken gepaard. Deze taken omvatten het onderhouden van en toepassen van patches op databaseservers, gegevensherstel en ontwerp met hoge beschikbaarheid.

De belangrijkste verschillen tussen deze opties worden weergegeven in de volgende tabel:

| **Kenmerk** | **Postgres op Azure-VM's** | **PostgreSQL als PaaS** |
| ----- | ----- | ----- |
| <B> SLA voor de beschikbaarheid |- 99,99% met beschikbaarheidssets <br> - 99,95% met enkele VM | - Single Server: 99,99% <br> - Flexible Server: niet beschikbaar tijdens de preview-versie <br> - Hyperscale (Citus): 99,95% (als hoge beschikbaarheid is ingeschakeld)|
| <B> Patches voor besturingssysteem en PostgreSQL | - Door de klant beheerd | - Single Server: automatisch <br> - Flexible Server: automatisch met optioneel door de klant beheerd venster <br> - Hyperscale (Citus): automatisch |
| <B> Hoge beschikbaarheid | - Klanten ontwerpen, implementeren, testen en onderhouden hoge beschikbaarheid. Mogelijkheden zijn onder andere clustering, replicatie, enzovoort. | - Single Server: ingebouwd <br> - Flexible Server: ingebouwd <br> - Hyperscale (Citus): gebouwd met stand-by |
| <B> Zoneredundantie | - Azure-VM's kunnen worden ingesteld om te worden uitgevoerd in verschillende beschikbaarheidszones. Voor een on-premises oplossing moeten klanten hun eigen secundaire datacentrum maken, beheren en onderhouden. | - Single Server: Nee <br> - Flexible Server: Ja <br> - Hyperscale (Citus): Nee |
| <B> Hybride scenario | - Door de klant beheerd |- Single Server: Leesreplica <br> - Flexible Server: Niet beschikbaar tijdens de preview-versie <br> - Hyperscale (Citus): Nee |
| <B>Back-up en herstel | - Door de klant beheerd | - Eén server: ingebouwd met gebruikersconfiguratie voor lokaal en geo <br> - Flexible Server: ingebouwd met gebruikersconfiguratie in zone-redundante opslag <br> Hyperscale (Citus): ingebouwd |
| <B> Databasebewerkingen bewaken | - Door de klant beheerd | - Single Server, Flexible Server en Hyperscale (Citus): Alle bieden klanten de mogelijkheid om waarschuwingen in te stellen voor de databasebewerking en om te reageren bij het bereiken van drempelwaarden. |
| <B> Advanced Threat Protection | - Klanten moeten deze bescherming zelf bouwen. |- Single Server: Ja <br> - Flexible Server: Niet beschikbaar tijdens de preview-versie <br> - Hyperscale (Citus): Nee |
| <B> Herstel na noodgevallen | - Door de klant beheerd | - Single Server: Geografisch redundante back-up en geo-lezen replica <br> - Flexible Server: Niet beschikbaar tijdens de preview-versie <br> - Hyperscale (Citus): Nee |
| <B> Intelligente prestaties | - Door de klant beheerd | - Single Server: Ja <br> - Flexible Server: Niet beschikbaar tijdens de preview-versie <br> - Hyperscale (Citus): Nee |

## <a name="total-cost-of-ownership-tco"></a>Totale eigendomskosten (TCO)

TCO is vaak de primaire overweging die de beste oplossing voor het hosten van uw databases bepaalt. Dit is waar, of u nu een startende onderneming met weinig geld bent of een team in een gevestigde onderneming dat met een strak budget werkt. In deze sectie worden de basisbeginselen van facturering en licenties in Azure beschreven, omdat deze van toepassing zijn op Azure Database for PostgreSQL en PostgreSQL op Azure-VM's.

## <a name="billing"></a>Billing

Azure Database for PostgreSQL is momenteel beschikbaar als een service in meerdere prijscategorieën met verschillende prijzen voor resources. Alle resources worden per uur gefactureerd tegen een vast bedrag. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/server/) voor de meest recente informatie over de momenteel ondersteunde servicelagen, rekengrootten en opslaghoeveelheden. U kunt servicelagen en rekengrootten dynamisch kunt aanpassen zodat deze overeenkomen met de verschillende doorvoerbehoeften van uw toepassing. Uitgaand internetverkeer wordt bij u in rekening gebracht tegen het reguliere [tarief voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/).

Met Azure Database for PostgreSQL wordt de databasesoftware automatisch geconfigureerd en bijgewerkt door Microsoft. Deze geautomatiseerde acties verminderen de beheerkosten. Daarnaast biedt Azure Database for PostgreSQL mogelijkheden voor [automatische back-up](). Dankzij deze mogelijkheden kunt u aanzienlijk op kosten besparen, vooral wanneer u een groot aantal databases hebt. Met PostgreSQL op virtuele machines van Azure kunt u daarentegen een willekeurige PostgreSQL-versie kiezen en uitvoeren. U moet echter betalen voor de ingerichte virtuele machine, opslagkosten die zijn gekoppeld aan de gegevens, back-up, bewaking van gegevens en logboek opslag en de kosten voor het specifieke PostgreSQL-licentietype (indien van toepassing).

Azure Database for PostgreSQL biedt ingebouwde hoge beschikbaarheid voor elk soort onderbreking op knooppuntniveau, waarbij de SLA-garantie van 99,99% voor de service gehandhaafd blijft. Voor hoge beschikbaarheid van de database binnen VM's gebruikt u echter de opties voor hoge beschikbaarheid als [streamingreplicatie](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION) die beschikbaar zijn in een PostgreSQL-database. Het gebruik van een ondersteunde optie voor hoge beschikbaarheid biedt geen extra SLA. Maar u kunt wel een databasebeschikbaarheid van hoger dan 99,99% behalen tegen extra kosten en administratieve overhead.

Zie de volgende artikelen voor meer informatie over prijzen:
- [Prijzen voor Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [Prijzen voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Beheer

Veel bedrijven besluiten zowel vanwege offloading van beheercomplexiteit als de kosten naar een cloudservice over te stappen.

Met IaaS biedt Microsoft het volgende:

- Beheer van de onderliggende infrastructuur.
- Automatische toepassing van patches voor onderliggende hardware en besturingssysteem

Met PaaS biedt Microsoft het volgende:

- Beheer van de onderliggende infrastructuur.
- Automatische toepassing van patches voor onderliggende hardware, besturingssysteem en database-engine.
- Beheer van hoge beschikbaarheid van de database.
- Automatisch uitvoeren van back-ups en repliceren van alle gegevens om herstel na noodgevallen mogelijk te maken.
- Versleuteling van inactieve en actieve gegevens (standaard).
- Bewaking van uw server en bieden van functies voor queryprestaties en aanbevelingen voor prestaties.

Met Azure Database for PostgreSQL kunt u uw database blijven beheren. Maar u hoeft de database-engine, het besturingssysteem en de hardware niet langer te beheren. Voorbeelden van items die u kunt blijven beheren, zijn onder meer:

- Databases
- Aanmelden
- Indexafstemming
- Queryafstemming
- Controleren
- Beveiliging

Daarnaast vereist het configureren van hoge beschikbaarheid voor een ander datacentrum nauwelijks tot geen configuratie of beheer.

- Met PostgreSQL op Azure VM's hebt u volledige controle over het besturingssysteem en de configuratie van het PostgreSQL-serverexemplaar. Met een VM kunt u bepalen wanneer het besturingssysteem en de databasesoftware moeten worden bijgewerkt of geüpgraded en welke patches moeten worden toegepast. U beslist ook wanneer u extra software, zoals een antivirustoepassing, installeert. Sommige geautomatiseerde functies worden geleverd om het toepassen van patches, het maken van een back-up en hoge beschikbaarheid aanzienlijk te vereenvoudigen. U kunt de grootte van de VM, het aantal schijven en hun opslagconfiguraties beheren. Zie [Groottes van virtuele machines en cloudservices voor Azure](../virtual-machines/sizes.md) voor meer informatie.

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Tijd om over te stappen naar Azure PostgreSQL Service (PaaS)

- Azure Database for PostgreSQL is de juiste oplossing voor toepassingen die zijn ontworpen voor de cloud wanneer de productiviteit van ontwikkelaars en snelle implementatietijd voor nieuwe oplossingen essentieel zijn. Met programmatische functionaliteit die lijkt op DBA is de service geschikt voor cloudarchitecten en -ontwikkelaars omdat het de noodzaak voor het beheren van het onderliggende besturingssysteem en de database vermindert.

- Wanneer u de tijd en kosten voor het verkrijgen van nieuwe on-premises hardware wilt vermijden, is PostgreSQL op Azure-VM's de juiste oplossing voor toepassingen die een nauwkeurige controle en aanpassing van de PostgreSQL-engine vereisen die niet worden ondersteund door de service of waarvoor toegang tot het onderliggende besturingssysteem vereist is.

## <a name="next-steps"></a>Volgende stappen

- Zie de [prijzen voor Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).
- Ga aan de slag met het maken van uw eerste server.