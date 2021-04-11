---
title: Problemen met Azure Cosmos DB onjuiste uitzonde ringen oplossen
description: Meer informatie over het vaststellen en oplossen van ongeldige aanvraag uitzonderingen, zoals invoer inhoud of partitie sleutel is ongeldig, de partitie sleutel komt niet overeen met Azure Cosmos DB.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e0ce00331eb524d064fd6e7c5205be8b66d4dbc2
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556092"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Problemen vaststellen en problemen oplossen met onjuiste aanvraag uitzonderingen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De HTTP-status code 400 vertegenwoordigt de aanvraag bevat ongeldige gegevens of de vereiste para meters ontbreken.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>De eigenschap ID ontbreekt
In dit scenario is het gebruikelijk om de fout te zien:

*De invoer inhoud is ongeldig omdat de vereiste eigenschappen-id; -ontbreken*

Een antwoord met deze fout houdt in het JSON-document dat naar de service wordt verzonden, de vereiste ID-eigenschap ontbreekt.

### <a name="solution"></a>Oplossing
Geef een `id` eigenschap op met een teken reeks waarde als in de [rest-specificatie](/rest/api/cosmos-db/documents) als onderdeel van uw document. de sdk's genereren geen waarden voor deze eigenschap.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>Ongeldig partitie sleutel type
In dit scenario ziet u veelvoorkomende fouten als:

*Partitie sleutel... is ongeldig.*

Een antwoord met deze fout betekent dat de waarde van de partitie sleutel van een ongeldig type is.

### <a name="solution"></a>Oplossing
De waarde van de partitie sleutel moet een teken reeks of een getal zijn, Controleer of de waarde van de verwachte typen is.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>Verkeerde partitie sleutel waarde
In dit scenario is het gebruikelijk om de fout te zien:

*De PartitionKey geëxtraheerd uit het document komt niet overeen met de naam die is opgegeven in de header*

Een antwoord met deze fout betekent dat u een bewerking uitvoert en een partitie sleutel waarde door geven die niet overeenkomt met de hoofd waarde van het document voor de verwachte eigenschap. Als het pad van de partitie sleutel van de verzameling zich bevindt `/myPartitionKey` , heeft het document een eigenschap met de naam `myPartitionKey` die niet overeenkomt met de waarde die is opgegeven als partitie sleutel waarde bij het aanroepen van de SDK-methode.

### <a name="solution"></a>Oplossing
Verzend de para meter voor de partitie sleutel waarde die overeenkomt met de waarde van de eigenschap document.

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) wanneer u de Azure Cosmos db .NET SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md).
* Problemen [vaststellen en oplossen](troubleshoot-java-sdk-v4-sql.md) wanneer u de Azure Cosmos DB Java v4 SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
