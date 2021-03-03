---
title: Het juiste implementatie type-Azure Database for MySQL selecteren
description: In dit artikel wordt beschreven welke factoren u moet overwegen voordat u Azure Database for MySQL implementeert als Infrastructure as a Service (IaaS) of platform as a Service (PaaS).
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 125431e6630ccfdd9e0e5d6b2a4ec5fa9b9e58fd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736182"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Kies de optie juiste MySQL-server in azure

Met Azure kunnen de werk belasting van uw MySQL-server worden uitgevoerd in een gehoste VM-infra structuur als een service (IaaS) of als een gehoste platform as a Service (PaaS). PaaS heeft meerdere implementatie opties en er zijn service lagen binnen elke implementatie optie. Wanneer u een keuze maakt tussen IaaS en PaaS, moet u beslissen of u uw database wilt beheren, patches wilt toepassen en back-ups wilt maken, of dat u deze bewerkingen delegeert aan Azure.

Houd rekening met de volgende twee opties bij het nemen van uw beslissing:

- **Azure database for MySQL**. Deze optie is een volledig beheerde MySQL-data base-engine op basis van de stabiele versie van MySQL Community Edition. Deze relationele Database as a Service (DBaaS), die wordt gehost op het Azure-Cloud platform, valt in de branche categorie van PaaS.

  Met een beheerd exemplaar van MySQL in azure kunt u ingebouwde functies gebruiken die gebruikmaken van geautomatiseerde patches, hoge Beschik baarheid, geautomatiseerde back-ups, elastisch schalen, beveiliging op het niveau van de onderneming, naleving en governance, bewaking en waarschuwingen waarvoor een andere uitgebreide configuratie nodig is wanneer de MySQL-server on-premises of in een Azure-VM is. Wanneer u MySQL als een service gebruikt, betaalt u per gebruik, met opties om omhoog of omlaag te schalen voor meer controle zonder onderbreking.
  
  [Azure database for MySQL](overview.md), ingeschakeld door de MySQL Community Edition, is beschikbaar in twee implementatie modi:

  - [Eén server](single-server-overview.md) is een volledig beheerde database service met minimale vereisten voor aanpassingen van de data base. Het Single Server-platform is ontworpen voor het verwerken van de meeste databasebeheerfuncties, zoals het toepassen van patches, back-ups, hoge beschikbaarheid, beveiliging met minimale gebruikersconfiguratie en beheer. De architectuur is geoptimaliseerd voor een beschikbaarheid van 99,99% in één beschikbaarheidszone. Eén server is het meest geschikt voor cloudtoepassingen die zijn ontworpen voor de verwerking van automatische patches, waarbij geen nauwkeurige controle op het patchschema en aangepaste configuratie-instellingen voor MySQL nodig zijn.

  - [Flexibele server (preview)](flexible-server/overview.md) is een volledig beheerde database service die is ontworpen om nauw keurige controle en flexibiliteit te bieden ten opzichte van database beheer functies en configuratie-instellingen. Over het algemeen biedt de service meer flexibiliteit en aanpassingen aan de server configuratie vergeleken met de implementatie van één server op basis van de gebruikers vereisten. Met de flexibele server architectuur kunnen gebruikers kiezen voor hoge Beschik baarheid binnen één beschikbaarheids zone en in meerdere beschikbaarheids zones. Flexibele servers bieden ook betere besturings elementen voor kosten optimalisatie met de mogelijkheid om uw server en uitstekende Sku's te starten/stoppen, ideaal voor werk belastingen die voortdurend volledige reken capaciteit nodig hebben.

  Flexibele servers zijn het meest geschikt voor:

  - Toepassings ontwikkeling vereist betere controle en aanpassingen van de MySQL-engine.
  - Zone-redundante hoge beschikbaarheid
  - Beheerde onderhoudsvensters

- **MySQL op virtuele Azure-machines**. Deze optie valt buiten de branche categorie van IaaS. Met deze service kunt u MySQL-server uitvoeren binnen een beheerde virtuele machine op het Azure-Cloud platform. Alle recente versies en edities van MySQL kunnen worden geïnstalleerd op de virtuele machine.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>De MySQL-implementatie opties in azure vergelijken

De belangrijkste verschillen tussen deze opties worden weergegeven in de volgende tabel:

| Kenmerk          | Azure Database for MySQL<br/>Enkele server |Azure Database for MySQL<br/>Flexibele server  |MySQL op virtuele Azure-machines                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| Ondersteuning voor MySQL-versie | 5,6, 5,7 & 8,0| 5,7 & 8,0 | Wille keurige versie|
| Berekening schalen | Ondersteund (schalen van en naar Basic-laag wordt niet ondersteund)| Ondersteund | Ondersteund|
| Opslag grootte | 5 GiB tot 16 TiB| 5 GiB tot 16 TiB | 32 GiB tot 32.767 GiB|
| Online opslag schalen | Ondersteund| Ondersteund| Niet ondersteund|
| Automatisch schalen van opslag | Ondersteund| Niet ondersteund in preview-versie| Niet ondersteund|
| Meer IOPs-schaling | Niet ondersteund| Ondersteund| Niet ondersteund|
| Netwerk verbinding | -Open bare eind punten met Server firewall.<br/> -Persoonlijke toegang met ondersteuning voor persoonlijke koppelingen.|-Open bare eind punten met Server firewall.<br/> -Persoonlijke toegang met Virtual Network-integratie.| -Open bare eind punten met Server firewall.<br/> -Persoonlijke toegang met ondersteuning voor persoonlijke koppelingen.|
| Service Level Agreement (SLA) | SLA voor 99,99% Beschik baarheid |Geen SLA in preview-versie| 99,99% met Beschikbaarheidszones|
| Patches besturings systeem| Automatisch  | Automatisch met aangepast besturings element voor onderhouds venster | Beheerd door eind gebruikers |
| MySQL-patches     | Automatisch  | Automatisch met aangepast besturings element voor onderhouds venster | Beheerd door eind gebruikers |
| Hoge beschikbaarheid | Ingebouwde HA in één beschikbaarheids zone| Ingebouwde HA binnen en tussen beschikbaarheids zones | Aangepast beheerd met clustering, replicatie, enzovoort.|
| Zoneredundantie | Niet ondersteund | Ondersteund | Ondersteund|
| Zone plaatsing | Niet ondersteund | Ondersteund | Ondersteund|
| Hybride scenario's | Ondersteund met [replicatie van inkomende gegevens](./concepts-data-in-replication.md)| Niet beschikbaar als preview-versie | Beheerd door eind gebruikers |
| Leesreplica's | Ondersteund (Maxi maal 5 replica's)| Ondersteund (Maxi maal 10 replica's)| Beheerd door eind gebruikers |
| Backup | Geautomatiseerd met een retentie van 7-35 dagen | Geautomatiseerd met een retentie van 1-35 dagen | Beheerd door eind gebruikers |
| Database bewerkingen bewaken | Ondersteund | Ondersteund | Beheerd door eind gebruikers |
| Herstel na noodgeval | Ondersteund met geo-redundante back-upopslag en het lezen van replica's met kruis regio's | Niet ondersteund in preview-versie| Aangepast beheerd met replicatie technologieën |
| Inzicht in queryprestaties | Ondersteund | Niet beschikbaar als preview-versie| Beheerd door eind gebruikers |
| Prijzen van gereserveerde instanties | Ondersteund | Niet beschikbaar als preview-versie | Ondersteund |
| Azure Active Directory-verificatie | Ondersteund | Niet beschikbaar als preview-versie | Niet ondersteund|
| Gegevens versleuteling in rust | Ondersteund met door de klant beheerde sleutels | Ondersteund met door service beheerde sleutels | Niet ondersteund|
| SSL/TLS | Standaard ingeschakeld met ondersteuning voor TLS v 1.2, 1,1 en 1,0 | Afgedwongen met TLS v 1.2 | Ondersteund met TLS v 1.2, 1,1 en 1,0 |
| Vloot beheer | Ondersteund met Azure CLI, Power shell, REST en Azure Resource Manager | Ondersteund met Azure CLI, Power shell, REST en Azure Resource Manager  | Ondersteund voor Vm's met Azure CLI, Power shell, REST en Azure Resource Manager |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Zakelijke motivaties voor het kiezen van PaaS of IaaS

Er zijn verschillende factoren die van invloed kunnen zijn op uw beslissing om PaaS of IaaS te kiezen voor het hosten van uw MySQL-data bases.

### <a name="cost"></a>Kosten

Kosten reductie is vaak de primaire overweging voor het bepalen van de beste oplossing voor het hosten van uw data bases. Dit is waar, of u nu een startende onderneming met weinig geld bent of een team in een gevestigde onderneming dat met een strak budget werkt. In deze sectie worden de basis beginselen van facturering en licenties in azure beschreven, omdat deze van toepassing zijn op Azure Database for MySQL en MySQL op virtuele Azure-machines.

#### <a name="billing"></a>Billing

Azure Database for MySQL is momenteel beschikbaar als een service in meerdere lagen met verschillende prijzen voor resources. Alle resources worden per uur gefactureerd tegen een vast bedrag. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/mysql/)voor de meest recente informatie over de momenteel ondersteunde service lagen, reken grootten en opslag bedragen. U kunt Service lagen en reken grootten dynamisch aanpassen zodat deze overeenkomen met de verschillende doorvoer behoeften van uw toepassing. Uitgaand internetverkeer wordt bij u in rekening gebracht tegen het reguliere [tarief voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/).

Met Azure Database for MySQL worden de database software automatisch geconfigureerd en bijgewerkt door micro soft. Deze geautomatiseerde acties verminderen de beheerkosten. Daarnaast heeft Azure Database for MySQL [automatische back-ups](./concepts-backup.md) . Dankzij deze mogelijkheden kunt u aanzienlijk op kosten besparen, vooral wanneer u een groot aantal databases hebt. In tegens telling tot de virtuele machines van Azure kunt u met MySQL een MySQL-versie kiezen en uitvoeren. Ongeacht de MySQL-versie die u gebruikt, betaalt u voor de ingerichte virtuele machine, opslag kosten die zijn gekoppeld aan de gegevens, back-up, bewaking van gegevens en logboek opslag en de kosten voor het specifieke MySQL-licentie type dat wordt gebruikt (indien van toepassing).

Azure Database for MySQL biedt ingebouwde hoge Beschik baarheid voor elk soort onderbreking op knooppunt niveau, terwijl de SLA-garantie van 99,99% voor de service nog steeds wordt gehandhaafd. Voor een hoge Beschik baarheid van de data base binnen Vm's, gebruikt u echter de opties voor hoge Beschik baarheid zoals [MySQL-replicatie](https://dev.mysql.com/doc/refman/8.0/en/replication.html) die beschikbaar zijn in een MySQL-data base. Het gebruik van een ondersteunde optie voor hoge beschikbaarheid biedt geen extra SLA. Maar u kunt wel een databasebeschikbaarheid van hoger dan 99,99% behalen tegen extra kosten en administratieve overhead.

Zie de volgende artikelen voor meer informatie over prijzen:

- [Azure Database for MySQL prijzen](https://azure.microsoft.com/pricing/details/mysql/)
- [Prijzen voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Beheer

Veel bedrijven besluiten zowel vanwege offloading van beheercomplexiteit als de kosten naar een cloudservice over te stappen.

Met IaaS biedt Microsoft het volgende:

- Beheer van de onderliggende infrastructuur.
- Voorziet in geautomatiseerde patches voor onderliggende hardware en besturings systeem.
  
Met PaaS biedt Microsoft het volgende:

- Beheer van de onderliggende infrastructuur.
- Voorziet in geautomatiseerde patches voor onderliggende hardware, besturings systeem en data base-engine.
- Beheer van hoge beschikbaarheid van de database.
- Automatisch uitvoeren van back-ups en repliceren van alle gegevens om herstel na noodgevallen mogelijk te maken.
- Versleuteling van inactieve en actieve gegevens (standaard).
- Bewaakt uw server en biedt functies voor inzicht in de prestaties en aanbevelingen voor query's

In de volgende lijst worden administratieve aandachtspunten voor elke optie beschreven:

- Met Azure Database for MySQL kunt u uw data base blijven beheren. Maar u hoeft de database-engine, het besturingssysteem en de hardware niet langer te beheren. Voorbeelden van items die u kunt blijven beheren, zijn onder meer:

  - Databases
  - Aanmelden
  - Indexafstemming
  - Queryafstemming
  - Controleren
  - Beveiliging

  Daarnaast vereist het configureren van hoge beschikbaarheid voor een ander datacentrum nauwelijks tot geen configuratie of beheer.

- Met MySQL op Azure Vm's hebt u volledige controle over het besturings systeem en de configuratie van het MySQL-Server exemplaar. Met een VM kunt u bepalen wanneer het besturingssysteem en de databasesoftware moeten worden bijgewerkt of geüpgraded en welke patches moeten worden toegepast. U beslist ook wanneer u extra software, zoals een antivirustoepassing, installeert. Sommige geautomatiseerde functies worden geleverd om het toepassen van patches, het maken van een back-up en hoge beschikbaarheid aanzienlijk te vereenvoudigen. U kunt de grootte van de VM, het aantal schijven en hun opslagconfiguraties beheren. Zie [Groottes van virtuele machines en cloudservices voor Azure](../virtual-machines/sizes.md) voor meer informatie.

### <a name="time-to-move-to-azure"></a>Tijd om over te stappen op Azure

- Azure Database for MySQL is de juiste oplossing voor toepassingen die in de Cloud zijn ontworpen wanneer ontwikkel aars productiviteit en snelle time-to-Market voor nieuwe oplossingen cruciaal zijn. Met programmatische functionaliteit die lijkt op DBA is de service geschikt voor cloudarchitecten en -ontwikkelaars omdat het de noodzaak voor het beheren van het onderliggende besturingssysteem en de database vermindert.

- Als u de tijd en kosten voor het verkrijgen van nieuwe on-premises hardware wilt voor komen, is MySQL op Azure Vm's de juiste oplossing voor toepassingen die een nauw keurige controle en aanpassing van de MySQL-engine vereisen die niet wordt ondersteund door de service of waarvoor toegang tot het onderliggende besturings systeem is vereist. Deze oplossing is ook geschikt voor het migreren van bestaande on-premises toepassingen en data bases naar Azure, voor gevallen waarin Azure Database for MySQL een slechte plaats is.

Omdat het niet nodig is om de presentatie-, toepassings-en gegevens lagen te wijzigen, bespaart u tijd en budget bij het opnieuw ontwerpen van uw bestaande oplossing. In plaats daarvan kunt u zich richten op het migreren van al uw oplossingen naar Azure en het adresseren van een aantal prestatie optimalisaties die het Azure-platform mogelijk vereist.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure database for MySQL prijzen](https://azure.microsoft.com/pricing/details/MySQL/).
- Aan de slag gaan met het [maken van uw eerste server](./quickstart-create-mysql-server-database-using-azure-portal.md).
