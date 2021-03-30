---
title: Azure Cosmos DB prestatie niveaus buiten gebruik gesteld
description: Meer informatie over de prestatie niveaus S1, S2 en S3 die eerder beschikbaar zijn in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4cb07d9d19d85cd8dff9a52eeeb7e173b60f4d6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93080767"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Het buiten gebruik stellen van de prestatieniveaus S1, S2 en S3
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!IMPORTANT] 
> De prestatie niveaus S1, S2 en S3 die in dit artikel worden besproken, worden buiten gebruik gesteld en zijn niet langer beschikbaar voor nieuwe Azure Cosmos DB accounts.

In dit artikel vindt u een overzicht van de prestatie niveaus S1, S2 en S3, en wordt beschreven hoe de verzamelingen die gebruikmaken van deze prestatie niveaus kunnen worden gemigreerd naar enkele gepartitioneerde verzamelingen. Na het lezen van dit artikel kunt u de volgende vragen beantwoorden:

- [Waarom worden de prestatie niveaus S1, S2 en S3 buiten gebruik gesteld?](#why-retired)
- [Hoe worden afzonderlijke partitie verzamelingen en gepartitioneerde verzamelingen vergeleken met de prestatie niveaus S1, S2 en S3?](#compare)
- [Wat moet ik doen om te zorgen voor ononderbroken toegang tot mijn gegevens?](#uninterrupted-access)
- [Hoe wordt de verzameling gewijzigd na de migratie?](#collection-change)
- [Hoe wordt mijn facturering gewijzigd nadat ik ben gemigreerd naar verzamelingen met één partitie?](#billing-change)
- [Wat moet ik doen als ik meer dan 20 GB aan opslag nodig heb?](#more-storage-needed)
- [Kan ik wisselen tussen de prestatie niveaus S1, S2 en S3 vóór de geplande migratie?](#change-before)
- [Hoe kan ik migreren van de prestatie niveaus S1, S2 en S3 naar enkelvoudige partitie verzamelingen?](#migrate-diy)
- [Hoe kan ik last hebben van een EA-klant?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Waarom worden de prestatie niveaus S1, S2 en S3 buiten gebruik gesteld?

De prestatie niveaus S1, S2 en S3 bieden niet de flexibiliteit die de standaard Azure Cosmos DB aanbieding biedt. Met de prestatie niveaus S1, S2 en S3 zijn de prestaties van de door Voer en opslag capaciteit vooraf ingesteld en zijn er geen beschik bare mogelijkheden. Azure Cosmos DB biedt nu de mogelijkheid om uw door Voer en opslag aan te passen, waardoor u veel meer flexibiliteit hebt in de mogelijkheid om te schalen wanneer uw behoeften veranderen.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Hoe worden afzonderlijke partitie verzamelingen en gepartitioneerde verzamelingen vergeleken met de prestatie niveaus S1, S2 en S3?

De volgende tabel vergelijkt de door Voer en opslag opties die beschikbaar zijn in verzamelingen met één partitie, gepartitioneerde verzamelingen en de prestatie niveaus S1, S2 en S3. Hier volgt een voor beeld van de regio VS Oost 2:

| Quotumnaam  |Gepartitioneerde verzameling|Verzameling met één partitie|S1|S2|S3|
|---|---|---|---|---|---|
|Maximale doorvoer|Onbeperkt|10.000 RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Minimale door Voer|2,5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Maximale opslag|Onbeperkt|20 GB|20 GB|20 GB|20 GB|
|Prijs (maandelijks)|Door Voer: $6/100 RU/s<br><br>Opslag: $0,25/GB|Door Voer: $6/100 RU/s<br><br>Opslag: $0,25/GB|$25 USD|$50 USD|$100 USD|

Bent u een EA-klant? Als dat het geval is, raadpleegt [u hoe weet ik of ik een EA-klant heb?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Wat moet ik doen om te zorgen voor ononderbroken toegang tot mijn gegevens?

Als u een S1-, S2-of S3-verzameling hebt, moet u de verzameling programmatisch met [behulp van de .NET SDK](#migrate-diy)migreren naar een enkele partitie verzameling. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Hoe wordt de verzameling gewijzigd na de migratie?

Als u een S1-verzameling hebt, kunt u deze migreren naar een enkele partitie verzameling met een door Voer van 400 RU/s. 400 RU/s is de laagste door Voer die beschikbaar is voor verzamelingen met één partitie. De kosten voor 400 RU/s in één partitie verzameling zijn echter ongeveer hetzelfde als u hebt betaald met uw S1-verzameling en 250 RU/s. u betaalt dus niet voor de extra 150 RU/s die voor u beschikbaar zijn.

Als u een S2-verzameling hebt, kunt u deze migreren naar een enkele partitie verzameling met 1 K RU/s. U ziet dat uw doorvoer niveau niet wordt gewijzigd.

Als u een S3-verzameling hebt, kunt u deze migreren naar een enkele partitie verzameling met 2,5 K RU/s. U ziet dat uw doorvoer niveau niet wordt gewijzigd.

In elk van deze gevallen kunt u, nadat u de verzameling hebt gemigreerd, uw doorvoer niveau aanpassen of het omhoog en omlaag schalen naar behoefte om toegang te bieden tot uw gebruikers met lage latentie. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Hoe wordt mijn facturering gewijzigd nadat ik ben gemigreerd naar de verzamelingen met enkele partities?

Ervan uitgaande dat u 10 S1-verzamelingen hebt, 1 GB aan opslag voor elke, in de regio VS Oost en migreert u deze 10 S1-verzamelingen naar tien enkelvoudige partitie verzamelingen op 400 RU/SEC (het minimale niveau). Uw factuur ziet er als volgt uit als u de tien afzonderlijke partitie verzamelingen voor een volledige maand wilt houden:

:::image type="content" source="./media/performance-levels/s1-vs-standard-pricing.png" alt-text="Hoe S1 prijzen voor 10 verzamelingen vergelijkt met 10 verzamelingen met prijzen voor een enkele partitie verzameling" border="false":::

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Wat moet ik doen als ik meer dan 20 GB aan opslag nodig heb?

Of u een verzameling hebt met het prestatie niveau S1, S2 of S3, of een enkele partitie verzameling hebt, die allemaal 20 GB aan opslag ruimte beschikbaar heeft, kunt u het hulp programma voor gegevens migratie van Azure Cosmos DB gebruiken om uw gegevens te migreren naar een gepartitioneerde verzameling met vrijwel onbeperkte opslag. Zie [partitioneren en schalen in azure Cosmos DB](partitioning-overview.md)voor meer informatie over de voor delen van een gepartitioneerde verzameling. 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Kan ik wisselen tussen de prestatie niveaus S1, S2 en S3 vóór de geplande migratie?

Alleen bestaande accounts met de prestaties S1, S2 en S3 kunnen met [behulp van de .NET SDK](#migrate-diy)worden gewijzigd en de niveaus van de prestatie niveaus programmatisch wijzigen. Als u S1, S3 of S3 van een enkele partitie verzameling wijzigt, kunt u niet terugkeren naar de prestatie niveaus S1, S2 of S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Hoe kan ik migreren van de prestatie niveaus S1, S2 en S3 naar enkelvoudige partitie verzamelingen?

U kunt met [behulp van de .NET SDK](#migrate-diy)van de prestatie niveaus S1, S2 en S3 via een programma worden gemigreerd naar enkelvoudige partitie verzamelingen. U kunt dit zelf doen vóór de geplande migratie om te profiteren van de flexibele doorvoer opties die beschikbaar zijn voor verzamelingen met één partitie.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migreren naar enkelvoudige partitie verzamelingen met behulp van de .NET SDK

Deze sectie heeft alleen betrekking op het wijzigen van het prestatie niveau van een verzameling met behulp van de [SQL .net API](sql-api-sdk-dotnet.md), maar het proces is vergelijkbaar voor onze andere sdk's.

Hier volgt een code fragment voor het wijzigen van de door Voer van de verzameling tot 5.000 aanvraag eenheden per seconde:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Ga naar [MSDN](/dotnet/api/microsoft.azure.documents.client.documentclient) om aanvullende voor beelden te bekijken en meer te weten te komen over onze aanbiedings methoden:

* [**ReadOfferAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync)
* [**ReadOffersFeedAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.readoffersfeedasync)
* [**ReplaceOfferAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync)
* [**CreateOfferQuery**](/previous-versions/azure/dn975114(v=azure.100))

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Hoe kan ik last hebben van een EA-klant?

EA-klanten worden beschermd tegen het einde van hun huidige contract.

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende bronnen voor meer informatie over prijzen en het beheren van gegevens met Azure Cosmos DB:

1.  [Gegevens partitioneren in Cosmos DB](partitioning-overview.md). Inzicht in het verschil tussen container met één partitie en gepartitioneerde containers, en tips voor het implementeren van een strategie voor partitionering om probleemloos te schalen.
2.  [Cosmos DB prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/). Meer informatie over de kosten voor het inrichten van de door Voer en opslag verbruik.
3.  [Aanvraag eenheden](request-units.md). Inzicht in het verbruik van door Voer voor verschillende bewerkings typen, bijvoorbeeld lezen, schrijven, query.