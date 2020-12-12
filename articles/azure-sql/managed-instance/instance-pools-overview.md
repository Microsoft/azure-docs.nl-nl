---
title: Wat is een Azure SQL Managed instance-groep?
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over Azure SQL Managed instance Pools (preview), een functie die een handige en rendabele manier biedt om kleinere SQL Server data bases naar de cloud te migreren op schaal en meerdere beheerde instanties te beheren.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: bc345509db1c2a14afb0ae781eccad8f77395c18
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347061"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>Wat is een Azure SQL Managed instance-groep (preview)?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Exemplaar groepen in Azure SQL Managed instance bieden een handige en rendabele manier om kleinere SQL Server-exemplaren naar de Cloud op schaal te migreren.

Met exemplaarpools kunt u rekenresources vooraf inrichten overeenkomstig uw totale migratievereisten. Vervolgens kunt u verschillende afzonderlijke beheerd exemplaren implementeren, tot uw vooraf ingerichte rekenniveau. Als u bijvoorbeeld 8 vCores vooraf inricht, kunt u het exemplaar 2 2-vCore en 1 4-vCore implementeren en vervolgens de data bases migreren naar deze instanties. Voordat instantie groepen beschikbaar zijn, moeten kleinere en minder Compute-workloads vaak worden geconsolideerd in een groter beheerd exemplaar wanneer deze naar de cloud worden gemigreerd. De nood zaak om groepen data bases te migreren naar een grote instantie vereist meestal een zorgvuldige capaciteits planning en resource governance, extra veiligheids overwegingen en sommige extra gegevens consolidatie op het niveau van de instantie.

Daarnaast ondersteunen instantie groepen systeem eigen VNet-integratie zodat u meerdere exemplaar groepen en meerdere afzonderlijke instanties in hetzelfde subnet kunt implementeren.

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Exemplaar groepen bieden de volgende voor delen:

1. Mogelijkheid om 2 vCore-instanties te hosten. *\* Alleen voor instanties in de exemplaar groepen*.
2. Voorspel bare en snelle exemplaar implementatie tijd (Maxi maal 5 minuten).
3. Minimale toewijzing van IP-adressen.

In het volgende diagram ziet u een exemplaar groep met meerdere beheerde instanties die zijn geïmplementeerd in een subnet van een virtueel netwerk.

![exemplaar groep met meerdere exemplaren](./media/instance-pools-overview/instance-pools1.png)

Met exemplaar groepen kunnen meerdere exemplaren op dezelfde virtuele machine worden geïmplementeerd, waarbij de reken grootte van de virtuele machine is gebaseerd op het totale aantal toegewezen vCores voor de pool. Met deze architectuur kan de virtuele machine in meerdere exemplaren worden *gepartitioneerd* . Dit kan elke ondersteunde grootte zijn, waaronder 2 vCores (2-vCore-exemplaren zijn alleen beschikbaar voor instanties in Pools).

Na de eerste implementatie zijn beheer bewerkingen op instanties in een groep veel sneller. Dit komt doordat de implementatie of uitbrei ding van een [virtueel cluster](connectivity-architecture-overview.md#high-level-connectivity-architecture) (toegewezen set virtuele machines) geen deel uitmaakt van het inrichten van het beheerde exemplaar.

Omdat alle instanties in een groep dezelfde virtuele machine delen, is de totale IP-toewijzing niet afhankelijk van het aantal geïmplementeerde instanties, wat handig is voor implementatie in subnetten met een beperkt IP-bereik.

Elke pool heeft een vaste IP-toewijzing van slechts negen IP-adressen (exclusief de vijf IP-adressen in het subnet die zijn gereserveerd voor eigen behoeften). Zie de [vereisten voor de subnet-grootte voor afzonderlijke instanties](vnet-subnet-determine-size.md)voor meer informatie.

## <a name="application-scenarios"></a>Toepassingsscenario's

De volgende lijst bevat de belangrijkste gebruiks situaties waarbij exemplaar groepen moeten worden overwogen:

- Migratie van *een groep SQL Server instanties* tegelijk, waarbij de meerderheid een kleiner formaat heeft (bijvoorbeeld 2 of 4 vCores).
- Scenario's waarin het *maken of schalen van voorspel bare en korte instanties* belang rijk is. Bijvoorbeeld implementatie van een nieuwe Tenant in een multi tenant SaaS-toepassings omgeving waarvoor mogelijkheden op exemplaar niveau zijn vereist.
- Scenario's waarbij een *vaste kosten* -of *bestedings limiet* van belang is. Bijvoorbeeld: het uitvoeren van gedeelde ontwikkel-of demo omgevingen van een vaste (of niet vaak veranderende) grootte, waarbij u regel matig beheerde instanties implementeert wanneer dat nodig is.
- Scenario's waarbij *minimale toewijzing van IP-adressen* in een VNet-subnet belang rijk is. Alle exemplaren in een groep delen een virtuele machine, dus het aantal toegewezen IP-adressen is lager dan in het geval van één exemplaar.

## <a name="architecture"></a>Architectuur

Exemplaar groepen hebben een vergelijk bare architectuur voor reguliere (*enkelvoudige*) beheerde exemplaren. Voor de ondersteuning van [implementaties in azure Virtual Networks](../../virtual-network/virtual-network-for-azure-services.md) en voor het afschermen en beveiligen van klanten kunnen exemplaar groepen ook worden gebaseerd op [virtuele clusters](connectivity-architecture-overview.md#high-level-connectivity-architecture). Virtuele clusters vertegenwoordigen een specifieke set geïsoleerde virtuele machines die zijn geïmplementeerd in het subnet van het virtuele netwerk van de klant.

Het belangrijkste verschil tussen de twee implementatie modellen is dat instantie groepen meerdere implementaties van SQL Server processen toestaan op hetzelfde knoop punt van de virtuele machine, die resources best rijken met behulp van [Windows-taak objecten](/windows/desktop/ProcThread/job-objects), terwijl afzonderlijke instanties altijd op een knoop punt van een virtuele machine worden uitgevoerd.

In het volgende diagram ziet u een exemplaar groep en twee afzonderlijke instanties die in hetzelfde subnet zijn geïmplementeerd en worden de belangrijkste details van de architectuur voor beide implementatie modellen geïllustreerd:

![Exemplaar groep en twee afzonderlijke exemplaren](./media/instance-pools-overview/instance-pools2.png)

Elke instantie groep maakt onder andere een afzonderlijk virtueel cluster. Instanties binnen een groep en single instances die in hetzelfde subnet zijn geïmplementeerd, delen geen reken resources die zijn toegewezen aan SQL Server processen en gateway onderdelen, waardoor de prestaties voorspelbaar worden.

## <a name="resource-limitations"></a>Resourcebeperkingen

Er bestaan verschillende resourcebeperkingen met betrekking tot exemplaarpools en exemplaren binnen pools:

- Exemplaar groepen zijn alleen beschikbaar op GEN5-hardware.
- Beheerde exemplaren in een groep hebben toegewezen CPU en RAM, dus het geaggregeerde aantal vCores voor alle exemplaren moet kleiner zijn dan of gelijk zijn aan het aantal vCores dat aan de pool is toegewezen.
- Alle [limieten op exemplaar niveau](resource-limits.md#service-tier-characteristics) zijn van toepassing op instanties die in een groep zijn gemaakt.
- Naast limieten op exemplaar niveau zijn er ook twee limieten opgelegd *voor het niveau van de instantie groep*:
  - Totale opslag grootte per pool (8 TB).
  - Totaal aantal gebruikers databases per groep. Deze limiet is afhankelijk van de vCores waarde van de groep:
    - 8 vCores-pool ondersteunt Maxi maal 200 data bases,
    - 16 vCores-pool ondersteunt Maxi maal 400 data bases,
    - 24-en grotere vCores-groepen ondersteunen Maxi maal 500 data bases.
- AAD-beheerder kan niet worden ingesteld voor de instanties die in de exemplaar groep worden geïmplementeerd. Daarom kan AAD-verificatie niet worden gebruikt.

De totale opslag toewijzing en het aantal data bases voor alle exemplaren moeten kleiner zijn dan of gelijk zijn aan de limieten die worden weer gegeven door exemplaar groepen.

- Exemplaar groepen ondersteunen 8, 16, 24, 32, 40, 64 en 80 vCores.
- Beheerde exemplaren binnen Pools ondersteunen 2, 4, 8, 16, 24, 32, 40, 64 en 80 vCores.
- Beheerde instanties in Pools ondersteunen opslag grootten tussen 32 GB en 8 TB, met uitzonde ring van:
  - 2 vCore-instanties ondersteunen de omvang van 32 GB en 640 GB.
  - 4 vCore-instanties ondersteunen de omvang van 32 GB en 2 TB.
- Beheerde exemplaren binnen Pools hebben een limiet van Maxi maal 100 gebruikers databases per instantie, met uitzonde ring van 2 vCore-instanties die ondersteuning bieden voor Maxi maal 50 gebruikers databases per instantie.

De [eigenschap service tier](resource-limits.md#service-tier-characteristics) is gekoppeld aan de bron van de exemplaar groep, dus alle exemplaren in een pool moeten dezelfde servicelaag zijn als de servicelaag van de groep. Op dit moment is alleen de servicelaag Algemeen beschikbaar (Zie de volgende sectie over beperkingen in de huidige preview).

### <a name="public-preview-limitations"></a>Beperkingen voor openbare preview

De open bare preview heeft de volgende beperkingen:

- Momenteel is alleen de servicelaag Algemeen beschikbaar.
- Exemplaar groepen kunnen tijdens de open bare preview-periode niet worden geschaald, dus zorgvuldige capaciteits planning voordat de implementatie belang rijk is.
- Azure Portal ondersteuning voor het maken en configureren van een exemplaar groep is nog niet beschikbaar. Alle bewerkingen voor exemplaar groepen worden alleen ondersteund via Power shell. De initiële implementatie van een exemplaar in een vooraf gemaakte groep wordt ook alleen ondersteund via Power shell. Wanneer beheerde exemplaren eenmaal in een groep zijn geïmplementeerd, kunnen ze worden bijgewerkt met behulp van de Azure Portal.
- Beheerde instanties die buiten de groep zijn gemaakt, kunnen niet worden verplaatst naar een bestaande groep en instanties die in een groep zijn gemaakt, kunnen niet worden verplaatst naar een enkel exemplaar of een andere groep.
- Prijzen voor [reserve capaciteits](../database/reserved-capacity-overview.md) instanties zijn niet beschikbaar.

## <a name="sql-features-supported"></a>Ondersteunde SQL-functies

Beheerde instanties die zijn gemaakt in Pools ondersteunen dezelfde [compatibiliteits niveaus en functies die worden ondersteund in single Managed instances](sql-managed-instance-paas-overview.md#sql-features-supported).

Elk beheerd exemplaar dat in een groep wordt geïmplementeerd, heeft een afzonderlijk exemplaar van SQL-Agent.

Optionele functies of functies waarvoor u moet kiezen welke specifieke waarden moeten worden gekozen (zoals sortering op exemplaar niveau, tijd zone, openbaar eind punt voor gegevens verkeer, failover-groepen) worden geconfigureerd op het niveau van de instantie en kunnen verschillen voor elk exemplaar van een groep.

## <a name="performance-considerations"></a>Prestatieoverwegingen

Hoewel beheerde instanties binnen Pools toegewezen vCore en RAM-geheugen hebben, delen ze lokale schijf (voor gebruik van tempdb) en netwerk bronnen. Het is niet waarschijnlijk, maar het is wel mogelijk om te zien wat het effect van de ruis op het *lawaai* is als meerdere instanties in de groep tegelijkertijd een hoog Resource verbruik hebben. Als u dit gedrag ziet, kunt u deze instanties implementeren in een grotere groep of als afzonderlijke exemplaren.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Omdat instanties die in een groep zijn geïmplementeerd, dezelfde virtuele machine delen, kunt u overwegen om functies uit te scha kelen die hogere beveiligings Risico's veroorzaken, of om de toegangs machtigingen voor deze functies stevig te controleren. Bijvoorbeeld CLR-integratie, systeem eigen back-up en herstel, data base-e-mail, enzovoort.

## <a name="instance-pool-support-requests"></a>Ondersteunings aanvragen voor exemplaar groepen

Ondersteunings aanvragen voor exemplaar groepen maken en beheren in de [Azure Portal](https://portal.azure.com).

Als u problemen ondervindt met betrekking tot de implementatie van een exemplaar groep (maken of verwijderen), moet u ervoor zorgen dat u **instantie groepen** opgeeft in het veld **probleem subtype** .

![Ondersteunings aanvraag voor groepen van instanties](./media/instance-pools-overview/support-request.png)

Als u problemen ondervindt met betrekking tot één beheerd exemplaar of data base binnen een groep, moet u een regel matig ondersteunings ticket maken voor Azure SQL Managed instance.

Als u grotere implementaties van SQL-beheerde exemplaren wilt maken (met of zonder exemplaar groepen), moet u mogelijk een groter regionaal quotum verkrijgen. Zie [aanvraag quotum verhogingen voor Azure SQL database](../database/quota-increase-request.md)voor meer informatie. De implementatie logica voor exemplaar groepen vergelijkt het totale vCore-verbruik *op groeps niveau* op basis van uw quotum om te bepalen of u nieuwe bronnen mag maken zonder uw quotum verder te verhogen.

## <a name="instance-pool-billing"></a>Facturering van exemplaar groep

Met instantie groepen kunnen reken kracht en opslag onafhankelijk worden geschaald. Klanten betalen voor de reken kracht die is gekoppeld aan de pool resource gemeten in vCores, en de opslag die is gekoppeld aan elk exemplaar dat wordt gemeten in gigabytes (de eerste 32 GB is voor elk exemplaar gratis).

de vCore prijs voor een pool wordt in rekening gebracht, ongeacht het aantal exemplaren dat in die groep is geïmplementeerd.

Voor de berekenings prijs (gemeten in vCores) zijn twee prijs opties beschikbaar:

  1. *Inbegrepen licentie*: prijs van SQL Server licenties is inbegrepen. Dit geldt voor de klanten die ervoor kiezen geen bestaande SQL Server licenties met Software Assurance toe te passen.
  2. *Azure Hybrid Benefit*: een gereduceerde prijs met inbegrip van Azure Hybrid Benefit voor SQL Server. Klanten kunnen zich op deze prijs aanmelden door gebruik te maken van hun bestaande SQL Server licenties met Software Assurance. Zie [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)voor Beschik baarheid en andere informatie.

Het is niet mogelijk om verschillende prijs opties in te stellen voor afzonderlijke exemplaren in een pool. Alle exemplaren in de bovenliggende groep moeten de prijs van de licentie of de Azure Hybrid Benefit prijs zijn. Het licentie model voor de groep kan worden gewijzigd nadat de groep is gemaakt.

> [!IMPORTANT]
> Als u een licentie model opgeeft voor het exemplaar dat verschilt van de pool, wordt de prijs van de groep gebruikt en wordt de waarde voor het exemplaar niveau genegeerd.

Als u exemplaar groepen maakt op [abonnementen die in aanmerking komen voor het voor deel van dev-test](https://azure.microsoft.com/pricing/dev-test/), ontvangt u automatisch kortings tarieven van maxi maal 55 procent op Azure SQL Managed instance.

Raadpleeg de sectie *exemplaar groepen* op de [pagina met prijzen voor SQL Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor volledige details over de prijzen van de exemplaar groep.

## <a name="next-steps"></a>Volgende stappen

- Zie instructies voor het gebruik van [SQL Managed instances](instance-pools-configure.md)om aan de slag te gaan met instantie groepen.
- Zie de [Quickstart-gids](instance-create-quickstart.md) voor meer informatie over het maken van uw eerste beheerde exemplaar.
- Zie [algemene Azure SQL-functies](../database/features-comparison.md)voor een lijst met functies en vergelijkingen.
- Zie [VNet-configuratie van SQL Managed Instance](connectivity-architecture-overview.md) voor meer informatie over VNet-configuratie.
- Zie [Beheerd exemplaar maken](instance-create-quickstart.md) voor een quickstart waarmee u een beheerd exemplaar kunt maken en een database vanuit een back-upbestand kunt herstellen.
- Zie [Migratie van SQL Managed Instance met behulp van Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) voor een zelfstudie over Azure Database Migration Service voor migratie.
- Zie [Azure SQL Managed Instance bewaken met Azure SQL-analyse](../../azure-monitor/insights/azure-sql.md) voor geavanceerde bewaking van de databaseprestaties in SQL Managed Instance, met ingebouwde intelligentie voor het oplossen van problemen.
- Zie [prijzen van SQL Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijs informatie.
