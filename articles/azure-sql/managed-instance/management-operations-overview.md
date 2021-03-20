---
title: Overzicht beheerbewerkingen
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over de duur van en de best practices voor beheerbewerkingen van Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 2da7311e61aa39be69a6a0a29eff686baaad7ebf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323189"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Overzicht van beheerbewerkingen van Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance biedt beheerbewerkingen die u kunt gebruiken om automatisch nieuwe beheerde exemplaren te implementeren, eigenschappen van exemplaren bij te werken en exemplaren te verwijderen als ze niet meer nodig zijn.

## <a name="what-are-management-operations"></a>Wat zijn beheerbewerkingen?

Alle beheerbewerkingen kunnen als volgt worden gecategoriseerd:

- Implementatie van exemplaar (maken van nieuw exemplaar)
- Update van exemplaar (eigenschappen van exemplaar wijzigen, zoals vCores of gereserveerde opslag)
- Verwijdering van exemplaar

Voor de ondersteuning van [implementaties in virtuele Azure-netwerken](../../virtual-network/virtual-network-for-azure-services.md) en het bieden van isolatie en beveiliging voor klanten, is SQL Managed Instance afhankelijk van [virtuele clusters](connectivity-architecture-overview.md#high-level-connectivity-architecture). Het virtuele cluster vertegenwoordigt een speciale set geïsoleerde virtuele machines die is geïmplementeerd in het subnet van het virtuele netwerk van de klant. In wezen resulteert elk beheerd exemplaar dat voor een leeg subnet wordt geïmplementeerd in een uitrol van een nieuw virtueel cluster.

Navolgende beheerbewerkingen op beheerde exemplaren kunnen van invloed zijn op het onderliggende virtuele cluster. Wijzigingen die van invloed zijn op het onderliggende virtuele cluster, kunnen van invloed zijn op de duur van beheerbewerkingen. Het implementeren van extra virtuele machines brengt namelijk een overhead met zich mee waar u rekening mee moet houden wanneer u nieuwe implementaties of updates voor bestaande beheerde exemplaren wilt plannen.


## <a name="duration"></a>Duur

De duur van de bewerkingen op het virtuele cluster kan variëren, maar duren doorgaans het langst. 

Hierna volgen de waarden die u normaal gesproken kunt verwachten, op basis van de bestaande telemetriegegevens van de service:

- **Maken van virtueel cluster**:  Het maken is een synchrone stap binnen beheerbewerkingen van exemplaren. <br/> **90% van de bewerkingen is binnen 4 uur voltooid**.
- **Het uitbreiden of inkrimpen van een virtueel cluster**: Uitbreiding is een synchrone stap, terwijl inkrimping asynchroon wordt uitgevoerd (zonder dat dit van invloed is op de duur van de beheerbewerkingen van exemplaren). <br/>**90% van de clusteruitbreidingen wordt in minder dan 2,5 uur voltooid**.
- **Verwijderen van virtuele clusters**: Verwijdering is een asynchrone stap, maar kan ook [handmatig geïnitieerd](virtual-cluster-delete.md) worden op een leeg virtueel cluster. In dat geval wordt het synchroon uitgevoerd. <br/>**90% van het aantal verwijderingen van virtuele clusters wordt binnen 1,5 uur uitgevoerd**.

Daarnaast kan het beheer van exemplaren ook een van de bewerkingen op gehoste databases omvatten, wat tot een langere duur leidt:

- **Databasebestanden koppelen vanuit Azure Storage**:  Een synchrone stap, zoals het schalen van rekenkracht (vCores) of het opslaan in de servicelaag Algemeen. <br/>**90% van deze bewerkingen is binnen 5 minuten voltooid**.
- **Seeding van AlwaysOn-beschikbaarheidsgroepen**: Een synchrone stap, zoals rekenkracht (vCores), of schalen van opslag in de servicelaag Bedrijfskritiek en ook in het wijzigen van de servicelaag van Algemeen in Bedrijfskritiek (of andersom). De duur van deze bewerking is evenredig met de totale databasegrootte en met de huidige databaseactiviteit (aantal actieve transacties). De databaseactiviteit bij het bijwerken van een instantie kan tot aanzienlijke variatie in de totale duur aanleiding geven. <br/>**90% van deze bewerkingen worden uitgevoerd bij 220 GB/uur of hoger**.

In de volgende tabellen vindt u een overzicht van de bewerkingen en de gebruikelijke totale tijdsduur, op basis van de categorie van de bewerking:

**Categorie: implementatie**

|Bewerking  |Langlopend segment  |Geschatte duur  |
|---------|---------|---------|
|Eerste instantie in een leeg subnet|Maken van virtuele clusters|90% van de bewerkingen is binnen 4 uur voltooid.|
|Eerste instantie van een andere hardwaregeneratie in een niet-leeg subnet (bijvoorbeeld eerste vijfde-generatie-instantie in een subnet met vierde-generatie-instanties)|Maken van virtuele clusters<sup>1</sup>|90% van de bewerkingen is binnen 4 uur voltooid.|
|Het maken van de volgende instantie binnen het niet-lege subnet (2e, 3e instantie, enzovoort)|Grootte van virtuele clusters wijzigen|90% van de bewerkingen is binnen 2,5 uur voltooid.|
| | | 

<sup>1</sup> Virtueel cluster wordt per hardwaregeneratie gebouwd.

**Categorie: bijwerken**

|Bewerking  |Langlopend segment  |Geschatte duur  |
|---------|---------|---------|
|Wijziging van instantie-eigenschap (beheerderswachtwoord, Azure AD-aanmelding, Azure Hybrid Benefit-vlag)|N.v.t.|Maximaal 1 minuut.|
|Opslag van instanties omhoog/omlaag schalen (servicelaag Algemeen)|Databasebestanden koppelen|90% van de bewerkingen is binnen 5 minuten voltooid.|
|Opslag van instanties omhoog/omlaag schalen (servicelaag Bedrijfskritiek)|- Grootte van virtuele clusters wijzigen<br>- Seeding van AlwaysOn-beschikbaarheidsgroepen|90% van de bewerkingen is binnen 2,5 uur voltooid, plus de tijd om alle databases te seeden (220 GB/uur).|
|Rekenkracht van instantie (vCores) omhoog en omlaag schalen (Algemeen)|- Grootte van virtuele clusters wijzigen<br>- Databasebestanden koppelen|90% van de bewerkingen is binnen 2,5 uur voltooid.|
|Rekenkracht van instantie (vCores) omhoog en omlaag schalen (Bedrijfskritiek)|- Grootte van virtuele clusters wijzigen<br>- Seeding van AlwaysOn-beschikbaarheidsgroepen|90% van de bewerkingen is binnen 2,5 uur voltooid, plus de tijd om alle databases te seeden (220 GB/uur).|
|Wijziging van de servicelaag van de instantie (van Algemeen naar Bedrijfskritiek en omgekeerd)|- Grootte van virtuele clusters wijzigen<br>- Seeding van AlwaysOn-beschikbaarheidsgroepen|90% van de bewerkingen is binnen 2,5 uur voltooid, plus de tijd om alle databases te seeden (220 GB/uur).|
| | | 

**Categorie: verwijderen**

|Bewerking  |Langlopend segment  |Geschatte duur  |
|---------|---------|---------|
|Verwijdering van exemplaar|Laatste deel van back-up voor alle databases registreren|90% van de bewerkingen is binnen 1 minuten voltooid.<br>Opmerking: als de laatste instantie uit het subnet wordt verwijderd, wordt de verwijdering van het virtuele cluster na 12 uur gepland.<sup>1</sup>|
|Verwijderen van virtueel cluster (als een door de gebruiker geïnitieerde bewerking)|Verwijderen van virtuele cluster|90% van de bewerkingen is binnen 1,5 uur voltooid.|
| | | 

<sup>1</sup>12 uur is de huidige configuratie, maar dit is afhankelijk van toekomstige wijzigingen. Als u een virtueel cluster eerder wilt verwijderen (bijvoorbeeld om het subnet vrij te geven), raadpleegt u [Een subnet verwijderen nadat u een beheerd exemplaar hebt verwijderd](virtual-cluster-delete.md).

## <a name="instance-availability"></a>Beschikbaarheid van instanties

SQL Managed Instance **is beschikbaar tijdens updatebewerkingen**, met uitzondering van een korte downtime die wordt veroorzaakt door de failover die aan het einde van de update plaatsvindt. Deze duurt meestal maximaal 10 seconden, zelfs in het geval van onderbroken, langlopende transacties, dankzij het [versnelde databaseherstel](../accelerated-database-recovery.md).

Tijdens implementaties en verwijderingen zijn SQL Managed Instances niet beschikbaar voor clienttoepassingen.

> [!IMPORTANT]
> Het is niet raadzaam om berekeningen of opslag van een Azure SQL Managed Instance te schalen of de servicelaag tegelijkertijd te wijzigen met langlopende transacties (gegevens importeren, gegevensverwerkingstaken, het herbouwen van indexen, enzovoort). De failover van de database aan het einde van de bewerking annuleert alle lopende transacties. 

## <a name="management-operations-steps"></a>Beheerbewerkingsstappen

Beheerbewerkingen bestaan uit meerdere stappen. Met de [introductie van de Operations API](management-operations-monitor.md) worden deze stappen blootgesteld aan een subset van bewerkingen (implementatie en update). De implementatiebewerking bestaat uit drie stappen terwijl de updatebewerking in zes stappen wordt uitgevoerd. Zie de sectie [Duur beheerbewerkingen](#duration) voor meer informatie over de duur van bewerkingen. De stappen worden vermeld op volgorde van uitvoering.

### <a name="managed-instance-deployment-steps"></a>Implementatiestappen van beheerd exemplaar

|Naam van stap  |Beschrijving van stap  |
|----|---------|
|Validatie aanvragen |Ingediende parameters worden gevalideerd. Bij een onjuiste configuratie treedt er een fout op. |
|Grootte van virtuele clusters wijzigen/virtuele clusters maken |Afhankelijk van de status van het subnet, worden virtuele clusters gemaakt of de grootte ervan aangepast. |
|Opnieuw starten van SQL-exemplaar |Het SQL-proces wordt gestart op het geïmplementeerde virtuele cluster. |

### <a name="managed-instance-update-steps"></a>Bijwerkstappen van beheerd exemplaar

|Naam van stap  |Beschrijving van stap  |
|----|---------|
|Validatie aanvragen | Ingediende parameters worden gevalideerd. Bij een onjuiste configuratie treedt er een fout op. |
|Grootte van virtuele clusters wijzigen/virtuele clusters maken |Afhankelijk van de status van het subnet, worden virtuele clusters gemaakt of de grootte ervan aangepast. |
|Opnieuw starten van SQL-exemplaar | Het SQL-proces wordt gestart op het geïmplementeerde virtuele cluster. |
|Databasebestanden seeden/databasebestanden koppelen |Afhankelijk van het type van de updatebewerking, wordt seeden van de database of koppelen van databasebestanden uitgevoerd. |
|Failover voorbereiden en uitvoeren |Nadat de gegevens zijn geseed of databasebestanden opnieuw zijn gekoppeld, wordt het systeem op failover voorbereid. Wanneer alles is ingesteld, wordt failover uitgevoerd **met een korte downtime**. |
|Opschonen van oud SQL-exemplaar |Verwijderen van oud SQL-proces uit het virtuele cluster |

> [!NOTE]
> Als gevolg van het schalen van exemplaren, doorloopt het onderliggende virtuele cluster een proces waarbij ongebruikte capaciteit wordt vrijgegeven en defragmentatie van de capaciteit kan optreden. Dit kan van invloed zijn op exemplaren die niet deelnemen aan maak- of schaalbewerkingen. 


## <a name="management-operations-cross-impact"></a>Wederzijdse impact van beheerbewerkingen

Beheerbewerkingen op een beheerd exemplaar kunnen van invloed zijn op andere beheerbewerkingen van de exemplaren die binnen hetzelfde virtuele cluster zijn geplaatst:

- **Langlopende herstelbewerkingen** in een virtueel cluster leiden ertoe dat maak- of schaalbewerkingen van andere instanties in hetzelfde subnet in de wacht worden geplaatst.<br/>**Voorbeeld:** Als er een langlopende herstelbewerking gaande is en er in hetzelfde subnet een aanvraag voor maken of schalen wordt gedaan, duurt het langer voordat deze aanvraag is voltooid, omdat er wordt gewacht tot de herstelbewerking is voltooid voordat er verder wordt gegaan.

- **Een volgende maak- of schaalbewerkingen van een exemplaar** wordt in de wacht geplaatst door een eerder geïnitieerde maak- of schaalbewerking van een exemplaar waarmee de grootte van het virtuele cluster is geïnitieerd.<br/>**Voorbeeld:** Als er meerdere aanvragen voor maken en/of schalen in hetzelfde subnet onder hetzelfde virtuele cluster worden uitgevoerd, en een ervan initieert een formaatwijziging van het virtuele cluster, zullen alle aanvragen die meer dan vijf minuten na de eerste bewerkingsaanvraag zijn ingediend, meer tijd kosten dan verwacht. Deze aanvragen moeten namelijk wachten tot de formaatwijziging is voltooid voordat er verder kan worden gegaan.

- **Maak- en schaalbewerkingen die in een vijfminutenvenster worden ingediend** worden als batch verwerkt en parallel uitgevoerd.<br/>**Voorbeeld:** Er wordt slechts één formaatwijziging van het virtuele cluster uitgevoerd voor alle bewerkingen die binnen een tijdvenster van vijf minuten zijn ingediend (gemeten vanaf het moment waarop de eerste bewerkingsaanvraag wordt uitgevoerd). Als een andere aanvraag meer dan vijf minuten na het indienen van de eerste wordt ingediend, wordt gewacht tot de formaatwijziging van het virtuele cluster is voltooid voordat de uitvoering wordt gestart.

> [!IMPORTANT]
> Beheerbewerkingen die in de wacht worden gezet omdat een andere bewerking wordt uitgevoerd, worden automatisch hervat wanneer aan alle voorwaarden wordt voldaan. Er is geen gebruikersactie nodig om de tijdelijk onderbroken beheerbewerkingen te hervatten.

## <a name="monitoring-management-operations"></a>Beheerbewerkingen bewaken

Zie [Beheerbewerkingen bewaken](management-operations-monitor.md) voor meer informatie over het bewaken van de voortgang en status van beheerbewerkingen.

## <a name="canceling-management-operations"></a>Beheerbewerkingen annuleren

Zie [Beheerbewerkingen annuleren](management-operations-cancel.md) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- Zie de [Quickstart-gids](instance-create-quickstart.md) voor meer informatie over het maken van uw eerste beheerde exemplaar.
- Zie [Veelvoorkomende SQL-functies](../database/features-comparison.md) voor een lijst met functies en vergelijkingen.
- Zie [VNet-configuratie van SQL Managed Instance](connectivity-architecture-overview.md) voor meer informatie over VNet-configuratie.
- Zie [Beheerd exemplaar maken](instance-create-quickstart.md) voor een quickstart waarmee u een beheerd exemplaar kunt maken en een database vanuit een back-upbestand kunt herstellen.
- Zie [Migratie van SQL Managed Instance met behulp van Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) voor een zelfstudie over Azure Database Migration Service voor migratie.
