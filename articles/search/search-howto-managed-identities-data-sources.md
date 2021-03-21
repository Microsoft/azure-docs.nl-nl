---
title: Een verbinding met een gegevens bron instellen met behulp van een beheerde identiteit
titleSuffix: Azure Cognitive Search
description: Meer informatie over het instellen van een Indexeer functie verbinding met een gegevens bron met behulp van een beheerde identiteit
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 95f1c4bf9b599da8285ac69e299549e5aa73c2f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519585"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>Een Indexeer functie verbinding met een gegevens bron instellen met behulp van een beheerde identiteit

> [!IMPORTANT] 
> Het instellen van een verbinding met een gegevens bron met een beheerde identiteit wordt niet ondersteund met de gratis Azure Cognitive Search-laag.

Een [Indexeer functie](search-indexer-overview.md) in azure Cognitive Search is een verkenning die een manier biedt om gegevens uit uw gegevens bron op te halen in azure Cognitive Search. Een Indexeer functie haalt een gegevens bron verbinding op van het gegevens bron object dat u maakt. Het gegevens bron object bevat gewoonlijk referenties voor de gegevens bron van het doel. Het gegevens bron object kan bijvoorbeeld een Azure Storage account sleutel bevatten als u gegevens wilt indexeren van een BLOB storage-container.

In veel gevallen is het niet mogelijk om referenties rechtstreeks in het gegevens bron object op te geven, maar er zijn enkele uitdagingen die kunnen worden bereikt:
* Hoe kan ik de referenties beveiligd blijven in mijn code waarmee het gegevens bron object wordt gemaakt?
* Als mijn account sleutel of wacht woord is aangetast en ik dit moet wijzigen, moet ik mijn gegevens bron objecten nu bijwerken met het nieuwe account of wacht woord, zodat mijn Indexeer functie opnieuw verbinding kan maken met de gegevens bron.

Deze problemen kunnen worden opgelost door uw verbinding in te stellen met behulp van een beheerde identiteit.

## <a name="using-managed-identities"></a>Beheerde identiteiten gebruiken

[Beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md) is een functie die Azure-Services biedt met een automatisch beheerde identiteit in azure Active Directory (Azure AD). U kunt deze functie in azure Cognitive Search gebruiken om een gegevens bron object te maken met een connection string dat geen referenties bevat. In plaats daarvan krijgt uw zoek service toegang tot de gegevens bron via op rollen gebaseerd toegangs beheer van Azure (Azure RBAC).

Bij het instellen van een gegevens bron met behulp van een beheerde identiteit kunt u de referenties van uw gegevens bron wijzigen. uw Indexeer functies kunnen nog steeds verbinding maken met de gegevens bron. U kunt ook gegevens bron objecten maken in uw code zonder een account sleutel op te vragen of Key Vault te gebruiken om een account sleutel op te halen.

## <a name="limitations"></a>Beperkingen

De volgende gegevens bronnen bieden ondersteuning voor het instellen van een indexerings verbinding met beheerde identiteiten. 

* [Azure Blob-opslag, Azure Data Lake Storage Gen2 (preview), Azure-tabel opslag](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

De volgende functies bieden momenteel geen ondersteuning voor het gebruik van beheerde identiteiten voor het instellen van de verbinding:
* Knowledge Store
* Aangepaste vaardigheden
 
## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een verbinding met een Indexeer functie met beheerde identiteiten:

* [Azure Blob-opslag, Azure Data Lake Storage Gen2 (preview), Azure-tabel opslag](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)