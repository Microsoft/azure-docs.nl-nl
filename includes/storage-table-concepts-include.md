---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 91a52e7eac40c0ac2ab682f251a2ae0013259b25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77013836"
---
## <a name="what-is-table-storage"></a>Wat is Table Storage
Met Azure Table Storage kunnen grote hoeveelheden gestructureerde gegevens worden opgeslagen. De service is een NoSQL-gegevensarchief die geverifieerde aanroepen in en buiten de Azure-cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens. Enkele voorbeelden van veelvoorkomende toepassingen van Tabel Storage:

* Opslaan van terabytes aan gestructureerde gegevens die kunnen worden geleverd aan webschaaltoepassingen
* Opslaan van gegevenssets die geen complexe joins, refererende sleutels of opgeslagen procedures vereisen en die kunnen worden gedenormaliseerd voor snelle toegang
* Snel een query voor gegevens uitvoeren met een geclusterde index
* Toegang tot gegevens krijgen met het OData-protocol en LINQ-query's met WCF Data Service .NET-bibliotheken

U kunt Table Storage gebruiken om zeer grote sets gestructureerde, niet-relationele gegevens op te slaan en query’s op de gegevens uit te voeren. En wanneer de vraag toeneemt, worden uw tabellen opgeschaald.

## <a name="table-storage-concepts"></a>Concepten van Table Storage
Table Storage omvat de volgende onderdelen:

![Diagram van onderdelen van Table Storage][Table1]

* **URL-indeling:** Azure Table Storage-accounts maken gebruik van deze indeling: `http://<storage account>.table.core.windows.net/<table>`

  Accounts voor de tabel-API van Azure Cosmos DB maken gebruik van deze indeling: `http://<storage account>.table.cosmosdb.azure.com/<table>`  

  U kunt rechtstreeks naar Azure-tabellen verwijzen met dit adres met het OData-protocol. Zie [OData.org][OData.org] voor meer informatie.
* **Account** alle toegang tot Azure Storage vindt plaats via een opslagaccount. Zie [Overzicht van opslagaccount](../articles/storage/common/storage-account-overview.md) voor meer informatie over opslagaccounts.

    Alle toegang tot Azure Cosmos DB vindt plaats via een Table-API-account. Zie [Een tabel-API-account maken](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account) voor meer informatie het maken van een tabel-API-account.
* **Tabel**: een tabel is een verzameling entiteiten. Met tabellen wordt geen schema voor entiteiten afgedwongen, wat betekent dat één tabel entiteiten kan bevatten die verschillende sets eigenschappen hebben.  
* **Entiteit**: een entiteit is een set eigenschappen die vergelijkbaar is met een databaserij. Een entiteit in Azure Storage kan maximaal 1 MB groot zijn. Een entiteit in Azure Cosmos DB kan maximaal 2 MB groot zijn.
* **Eigenschappen**: een eigenschap is een naamwaardepaar. Elke entiteit kan maximaal 252 eigenschappen voor het opslaan van gegevens bevatten. Elke entiteit heeft ook drie systeemeigenschappen waarmee een partitiesleutel, een rijsleutel en een timestamp worden opgegeven. Entiteiten met dezelfde partitiesleutel kunnen sneller worden opgevraagd en in atomische bewerkingen worden ingevoegd/bijgewerkt. De rijsleutel van een entiteit is de unieke id in een partitie.

Zie [Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (Inzicht krijgen in het Table Service-gegevensmodel) voor meer informatie over de naamgeving van tabellen en eigenschappen.

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
