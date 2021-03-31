---
title: Een verbinding met een opslag account instellen met behulp van een beheerde identiteit
titleSuffix: Azure Cognitive Search
description: Meer informatie over het instellen van een verbinding met een Indexeer functie met een Azure Storage account met behulp van een beheerde identiteit
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f26ca04955dfa854a8ee17b7aa255a6ed991b8df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358368"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>Een verbinding met een Azure Storage-account instellen met behulp van een beheerde identiteit

Op deze pagina wordt beschreven hoe u een Indexeer functie verbinding kunt instellen met een Azure-opslag account met behulp van een beheerde identiteit in plaats van referenties op te geven in het gegevens bron object connection string.

Voor meer informatie over deze functie kunt u het beste inzicht krijgen in wat een indexer is en hoe u een Indexeer functie instelt voor uw gegevens bron. Meer informatie vindt u op de volgende koppelingen:
* [Overzicht van de indexeerfunctie](search-indexer-overview.md)
* [Indexer van Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Indexeer functie Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexeer functie van Azure-tabel](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>De verbinding instellen

### <a name="1---turn-on-system-assigned-managed-identity"></a>1-door het systeem toegewezen beheerde identiteit inschakelen

Wanneer een door het systeem toegewezen beheerde identiteit is ingeschakeld, maakt Azure een identiteit voor uw zoek service die kan worden gebruikt om te verifiëren bij andere Azure-Services binnen dezelfde Tenant en hetzelfde abonnement. U kunt deze identiteit vervolgens gebruiken in azure RBAC-toewijzingen (op rollen gebaseerd toegangs beheer) waarmee toegang tot gegevens tijdens het indexeren wordt toegestaan.

![Door het systeem toegewezen beheerde identiteit inschakelen](./media/search-managed-identities/turn-on-system-assigned-identity.png "Door het systeem toegewezen beheerde identiteit inschakelen")

Nadat u **Opslaan** hebt geselecteerd, ziet u een object-id die is toegewezen aan uw zoek service.

![Object-id](./media/search-managed-identities/system-assigned-identity-object-id.png "Object-id")
 
### <a name="2---add-a-role-assignment"></a>2-een roltoewijzing toevoegen

In deze stap geeft u uw Azure Cognitive Search-service toestemming om gegevens uit uw opslag account te lezen.

1. Navigeer in het Azure Portal naar het opslag account dat de gegevens bevat die u wilt indexeren.
2. Selecteer **Toegangsbeheer (IAM)**
3. Selecteer **toevoegen** en vervolgens **functie toewijzing toevoegen**

    ![Roltoewijzing toevoegen](./media/search-managed-identities/add-role-assignment-storage.png "Roltoewijzing toevoegen")

4. Selecteer de gewenste rol (len) op basis van het type opslag account dat u wilt indexeren:
    1. Voor Azure Blob-opslag moet u uw zoek service toevoegen aan de rol van **BLOB-gegevens lezer voor opslag** .
    1. Azure Data Lake Storage Gen2 moet u uw zoek service toevoegen aan de rol van **BLOB-gegevens lezer voor opslag** .
    1. Voor Azure Table-opslag moet u uw zoek service toevoegen aan de rol **lezer en gegevens toegang** .
5.  **Wijs toegang toe** als **Azure AD-gebruiker,-groep of-Service-Principal**
6.  Zoek naar uw zoek service, Selecteer deze en selecteer vervolgens **Opslaan** .

    Voor beeld voor Azure Blob Storage en Azure Data Lake Storage Gen2:

    ![Roltoewijzing voor gegevens lezer van opslag-BLOB toevoegen](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Roltoewijzing voor gegevens lezer van opslag-BLOB toevoegen")

    Voor beeld voor Azure-tabel opslag:

    ![Toewijzing van de rol lezer en gegevens toegang toevoegen](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Toewijzing van de rol lezer en gegevens toegang toevoegen")

### <a name="3---create-the-data-source"></a>3: de gegevens bron maken

De [rest API](/rest/api/searchservice/create-data-source), Azure Portal en de [.NET-SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) ondersteunen de beheerde identiteits Connection String. Hieronder ziet u een voor beeld van het maken van een gegevens bron voor het indexeren van gegevens uit een opslag account met behulp van de [rest API](/rest/api/searchservice/create-data-source) en een beheerde identiteit Connection String. De indeling van de beheerde identiteits connection string is hetzelfde voor de REST API, de .NET-SDK en de Azure Portal.

Bij het indexeren van een opslag account moet de gegevens bron de volgende vereiste eigenschappen hebben:

* **naam** is de unieke naam van de gegevens bron in uw zoek service.
* **type**
    * Azure Blob-opslag: `azureblob`
    * Azure-tabel opslag: `azuretable`
    * Azure Data Lake Storage Gen2: het **type** wordt gegeven nadat u zich hebt aangemeld voor het voor beeld met behulp van [dit formulier](https://aka.ms/azure-cognitive-search/mi-preview-request).
* **aanmeldingsgegevens**
    * Wanneer u een beheerde identiteit gebruikt om te verifiëren, is de notatie van de **referenties** anders dan wanneer u geen beheerde identiteit gebruikt. Hier geeft u een ResourceId op die geen account sleutel of wacht woord heeft. De ResourceId moet de abonnements-ID van het opslag account, de resource groep van het opslag account en de naam van het opslag account bevatten.
    * Indeling beheerde identiteit: 
        * *ResourceId =/Subscriptions/**uw abonnement-id**/resourceGroups/**de naam van uw resource groep**/providers/Microsoft.Storage/storageAccounts/**uw opslag accountnaam**/;*
* **container** bevat een container-of tabel naam in uw opslag account. Standaard kunnen alle blobs in de container worden opgehaald. Als u alleen blobs wilt indexeren in een bepaalde virtuele map, kunt u die directory opgeven met behulp van de optionele **query** parameter.

Voor beeld van het maken van een BLOB-gegevens bron object met behulp van de [rest API](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

### <a name="4---create-the-index"></a>4-de index maken

De index specificeert de velden in een document, kenmerken en andere constructies die de zoek ervaring vormen.

Ga als volgt te werk om een index met een doorzoekbaar `content` veld te maken voor het opslaan van de tekst die uit blobs is geëxtraheerd:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Zie [index maken](/rest/api/searchservice/create-index) voor meer informatie over het maken van indexen

### <a name="5---create-the-indexer"></a>5: de Indexeer functie maken

Een Indexeer functie verbindt een gegevens bron met een doel zoek index en biedt een planning voor het automatiseren van de gegevens vernieuwing.

Zodra de index en gegevens bron zijn gemaakt, kunt u de Indexeer functie maken.

Voor beeld van een indexerings definitie voor een BLOB-Indexer:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Deze Indexeer functie wordt elke twee uur uitgevoerd (schema-interval is ingesteld op "PT2H"). Als u een Indexeer functie elke 30 minuten wilt uitvoeren, stelt u het interval in op ' PT30M '. Het kortste ondersteunde interval is 5 minuten. Het schema is optioneel: als u dit weglaat, wordt een Indexeer functie slechts eenmaal uitgevoerd wanneer deze wordt gemaakt. U kunt echter op elk gewenst moment een Indexeer functie op aanvraag uitvoeren.   

Bekijk [Indexeer functie maken](/rest/api/searchservice/create-indexer)voor meer informatie over het maken van Indexeer functie-API.

Zie [Indexeer functies plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van de planningen voor de Indexeer functie.

## <a name="accessing-secure-data-in-storage-accounts"></a>Toegang tot beveiligde gegevens in opslag accounts

Azure Storage-accounts kunnen verder worden beveiligd met firewalls en virtuele netwerken. Als u inhoud wilt indexeren van een Blob Storage-account of Data Lake Gen2-opslag account dat is beveiligd met een firewall of een virtueel netwerk, volgt u de instructies voor het [veilig benaderen van gegevens in opslag accounts via een vertrouwde service-uitzonde ring](search-indexer-howto-access-trusted-service-exception.md).

## <a name="see-also"></a>Zie ook

Meer informatie over Azure Storage Indexeer functies:

* [Indexer van Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Indexeer functie Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexeer functie van Azure-tabel](search-howto-indexing-azure-tables.md)