---
title: Azure Cosmos DB Explorer gebruiken voor het beheren van uw gegevens
description: Azure Cosmos DB Explorer is een zelfstandige webgebaseerde interface waarmee u de gegevens kunt bekijken en beheren die zijn opgeslagen in Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.author: dech
ms.openlocfilehash: d4baa92fe4aa2ed402c394198684c4deec2bf9f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348599"
---
# <a name="work-with-data-using-azure-cosmos-db-explorer"></a>Werken met gegevens met behulp van Azure Cosmos DB Explorer 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Explorer is een zelfstandige webgebaseerde interface waarmee u de gegevens kunt bekijken en beheren die zijn opgeslagen in Azure Cosmos DB. Azure Cosmos DB Explorer is gelijk aan het bestaande **Data Explorer** tabblad dat beschikbaar is in azure Portal wanneer u een Azure Cosmos DB-account maakt. De belangrijkste voor delen van Azure Cosmos DB Explorer over de bestaande Data Explorer zijn:

* U hebt een volledig scherm om uw gegevens weer te geven, query's uit te voeren, opgeslagen procedures, triggers en de resultaten weer te geven.  

* U kunt tijdelijke of permanente Lees-en lees-en schrijf toegang bieden voor uw database account en de bijbehorende verzamelingen naar andere gebruikers die geen toegang hebben tot Azure Portal of het abonnement.  

* U kunt de query resultaten delen met andere gebruikers die geen toegang hebben tot Azure Portal of het abonnement.  

## <a name="access-azure-cosmos-db-explorer"></a>Toegang tot Azure Cosmos DB Verkenner

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/). 

2. Ga vanuit **alle resources** naar uw Azure Cosmos DB-account, selecteer sleutels en kopieer de **primaire verbindings reeks**.  

3. Ga naar https://cosmos.azure.com/ , plak de Connection String en selecteer **verbinding maken**. Als u de connection string gebruikt, hebt u zonder enige tijd toegang tot de Azure Cosmos DB Explorer.  

   Als u tijdelijke gebruikers toegang wilt geven tot uw Azure Cosmos DB-account, kunt u dit doen met behulp van de Url's voor lezen en schrijven en lees toegang. 

4. Open de Blade **Data Explorer** en selecteer **Open volledige scherm**. In het pop-upvenster kunt u twee toegangs-Url's bekijken: **lezen/schrijven** en **lezen**. Met deze Url's kunt u uw Azure Cosmos DB-account tijdelijk delen met andere gebruikers. De toegang tot het account verloopt over 24 uur waarna u opnieuw verbinding kunt maken met behulp van een nieuwe toegangs-URL of de connection string. 

   **Lezen/schrijven** : wanneer u de Read-Write URL met andere gebruikers deelt, kunnen ze de data bases, verzamelingen, query's en andere resources die zijn gekoppeld aan dit specifieke account weer geven en wijzigen.

   **Lezen** : wanneer u de alleen-lezen URL deelt met andere gebruikers, kunnen ze de data bases, verzamelingen, query's en andere resources weer geven die zijn gekoppeld aan dit specifieke account. Als u bijvoorbeeld resultaten wilt delen van een query met uw team leden die geen toegang hebben tot Azure Portal of uw Azure Cosmos DB account, kunt u deze opgeven met deze URL.

   Kies het type toegang waarmee u het account wilt openen en klik op **openen**. Nadat u de Explorer hebt geopend, is de ervaring hetzelfde als bij de Data Explorer tabblad in Azure Portal.

   :::image type="content" source="./media/data-explorer/open-data-explorer-with-access-url.png" alt-text="Open Azure Cosmos DB Explorer openen":::

## <a name="known-issues"></a>Bekende problemen

Momenteel is de ervaring voor het **openen van volledig scherm** , waarmee u tijdelijke lees-/schrijftoegang of lees toegang kunt delen, maar nog niet wordt ondersteund voor Azure Cosmos DB Gremlin-en Table-API-accounts. U kunt nog steeds uw Gremlin-en Table-API-accounts weer geven door de connection string aan Azure Cosmos DB Explorer door te geven. 

Het weer geven van documenten die een UUID bevatten, wordt momenteel niet ondersteund in Data Explorer. Dit heeft geen invloed op het laden van verzamelingen, waarbij alleen afzonderlijke documenten of query's worden weer gegeven die deze documenten bevatten. Om deze documenten weer te geven en te beheren, moeten gebruikers het hulp programma blijven gebruiken dat oorspronkelijk werd gebruikt om deze documenten te maken.

Klanten die HTTP-401-fouten ontvangen, kunnen worden veroorzaakt door onvoldoende Azure RBAC-machtigingen voor het Azure-account van de klant, met name als het account een aangepaste rol heeft. Aangepaste rollen moeten actie hebben `Microsoft.DocumentDB/databaseAccounts/listKeys/*` om Data Explorer te gebruiken als u zich aanmeldt met behulp van hun Azure Active Directory referenties.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u aan de slag gaat met Azure Cosmos DB Explorer om uw gegevens te beheren, kunt u het volgende doen:

* Begin met het definiëren van [query's](./sql-query-getting-started.md) met behulp van de SQL-syntaxis en het [Program meren](stored-procedures-triggers-udfs.md) van de server aan de hand van opgeslagen procedures, udf's en triggers.
