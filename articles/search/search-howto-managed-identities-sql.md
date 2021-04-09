---
title: Een verbinding met Azure SQL Database instellen met behulp van een beheerde identiteit
titleSuffix: Azure Cognitive Search
description: Meer informatie over hoe u een Indexeer functie verbinding kunt instellen met Azure SQL Database met behulp van een beheerde identiteit
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b940da2cf754e7e1cac91df6b517ecebe55e8c40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358419"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>Een Indexeer functie verbinding instellen met Azure SQL Database met behulp van een beheerde identiteit

Op deze pagina wordt beschreven hoe u een Indexeer functie verbinding kunt instellen met Azure SQL Database met behulp van een beheerde identiteit in plaats van referenties op te geven in het gegevens bron object connection string.

Voor meer informatie over deze functie kunt u het beste inzicht krijgen in wat een indexer is en hoe u een Indexeer functie instelt voor uw gegevens bron. Meer informatie vindt u op de volgende koppelingen:

* [Overzicht van de indexeerfunctie](search-indexer-overview.md)
* [Indexeerfunctie voor Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Een verbinding instellen met behulp van een beheerde identiteit

### <a name="1---turn-on-system-assigned-managed-identity"></a>1-door het systeem toegewezen beheerde identiteit inschakelen

Wanneer een door het systeem toegewezen beheerde identiteit is ingeschakeld, maakt Azure een identiteit voor uw zoek service die kan worden gebruikt om te verifiëren bij andere Azure-Services binnen dezelfde Tenant en hetzelfde abonnement. U kunt deze identiteit vervolgens gebruiken in azure RBAC-toewijzingen (op rollen gebaseerd toegangs beheer) waarmee toegang tot gegevens tijdens het indexeren wordt toegestaan.

![Door het systeem toegewezen beheerde identiteit inschakelen](./media/search-managed-identities/turn-on-system-assigned-identity.png "Door het systeem toegewezen beheerde identiteit inschakelen")

Nadat u **Opslaan** hebt geselecteerd, ziet u een object-id die is toegewezen aan uw zoek service.

![Object-id](./media/search-managed-identities/system-assigned-identity-object-id.png "Object-id")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2-Azure Active Directory beheerder inrichten voor SQL Server

Wanneer u in de volgende stap verbinding maakt met de data base, moet u verbinding maken met Azure Active Directory een Azure AD-account dat beheerders toegang heeft tot de data base om de zoek service toegang te geven tot de data base.

Volg de instructies [hier](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database) om uw Azure ad-account beheerder toegang te geven tot de-data base.

### <a name="3---assign-the-search-service-permissions"></a>3: de machtigingen van de zoek service toewijzen

Volg de onderstaande stappen om de machtiging Search service toe te wijzen om de data base te lezen.

1. Verbinding maken met Visual Studio

    ![Verbinding maken met Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "Verbinding maken met Visual Studio")

2. Verifiëren met uw Azure AD-account

    ![Verifieer](./media/search-managed-identities/visual-studio-authentication.png "Verifiëren")

3. Voer de volgende opdrachten uit:

    Voeg de haken toe om de naam van de zoek service.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Nieuwe query](./media/search-managed-identities/visual-studio-new-query.png "Nieuwe query")

    ![Query uitvoeren](./media/search-managed-identities/visual-studio-execute-query.png "Query uitvoeren")

>[!NOTE]
> Als de id van de zoek service uit stap 1 is gewijzigd nadat deze stap is voltooid, moet u het rollidmaatschap verwijderen en de gebruiker verwijderen in de SQL database, en vervolgens de machtigingen opnieuw toevoegen door stap 3 opnieuw uit te voeren.
> Het verwijderen van het lidmaatschap en de gebruiker van de rol kan worden gerealiseerd door de volgende opdrachten uit te voeren:
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4-een roltoewijzing toevoegen

In deze stap geeft u uw Azure Cognitive Search-service toestemming om gegevens van uw SQL Server te lezen.

1. Ga in het Azure Portal naar uw Azure SQL Server-pagina.
2. Selecteer **Toegangsbeheer (IAM)**
3. Selecteer **toevoegen** en vervolgens **functie toewijzing toevoegen**

    ![Roltoewijzing toevoegen](./media/search-managed-identities/add-role-assignment-sql-server.png "Roltoewijzing toevoegen")

4. Selecteer de juiste rol van **lezer** .
5. **Wijs toegang toe** als **Azure AD-gebruiker,-groep of-Service-Principal**
6. Zoek naar uw zoek service, Selecteer deze en selecteer vervolgens **Opslaan** .

    ![Toewijzing van lezers functie toevoegen](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Toewijzing van lezers functie toevoegen")

### <a name="5---create-the-data-source"></a>5: de gegevens bron maken

De [rest API](/rest/api/searchservice/create-data-source), Azure Portal en de [.NET-SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) ondersteunen de beheerde identiteits Connection String. Hieronder ziet u een voor beeld van het maken van een gegevens bron voor het indexeren van gegevens van een Azure SQL Database met behulp van de [rest API](/rest/api/searchservice/create-data-source) en een beheerde identiteit Connection String. De indeling van de beheerde identiteits connection string is hetzelfde voor de REST API, de .NET-SDK en de Azure Portal.

Wanneer u een gegevens bron maakt met behulp van de [rest API](/rest/api/searchservice/create-data-source), moet de gegevens bron de volgende vereiste eigenschappen hebben:

* **naam** is de unieke naam van de gegevens bron in uw zoek service.
* **type** is `azuresql`
* **aanmeldingsgegevens**
    * Wanneer u een beheerde identiteit gebruikt om te verifiëren, is de notatie van de **referenties** anders dan wanneer u geen beheerd-identiteit gebruikt. Hier geeft u een initiële catalogus-of database naam en een ResourceId op die geen account sleutel of wacht woord heeft. De ResourceId moet de abonnements-ID van Azure SQL Database, de resource groep van SQL Database en de naam van de SQL database bevatten. 
    * Connection string indeling beheerde identiteit:
        * *Initiële catalogus | Data Base =**database naam**; ResourceId =/Subscriptions/**uw abonnements-id**/resourceGroups/**de naam van uw resource groep**/providers/Microsoft.SQL/servers/**uw SQL Server naam**/; Verbindingstime-out = verbindingstime **-out**;*
* **container** Hiermee geeft u de naam op van de tabel of weer gave die u wilt indexeren.

Voor beeld van het maken van een Azure SQL-gegevens bron object met behulp van de [rest API](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6-de index maken

De index specificeert de velden in een document, kenmerken en andere constructies die de zoek ervaring vormen.

Ga als volgt te werk om een index te maken met een doorzoekbaar `booktitle` veld:   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Zie [index maken](/rest/api/searchservice/create-index) voor meer informatie over het maken van indexen

### <a name="7---create-the-indexer"></a>7: de Indexeer functie maken

Een Indexeer functie verbindt een gegevens bron met een doel zoek index en biedt een planning voor het automatiseren van de gegevens vernieuwing.

Zodra de index en gegevens bron zijn gemaakt, kunt u de Indexeer functie maken.

Voor beeld van een indexerings definitie voor een Azure SQL-Indexeer functie:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Deze Indexeer functie wordt elke twee uur uitgevoerd (schema-interval is ingesteld op "PT2H"). Als u een Indexeer functie elke 30 minuten wilt uitvoeren, stelt u het interval in op ' PT30M '. Het kortste ondersteunde interval is 5 minuten. Het schema is optioneel: als u dit weglaat, wordt een Indexeer functie slechts eenmaal uitgevoerd wanneer deze wordt gemaakt. U kunt echter op elk gewenst moment een Indexeer functie op aanvraag uitvoeren.   

Bekijk [Indexeer functie maken](/rest/api/searchservice/create-indexer)voor meer informatie over het maken van Indexeer functie-API.

Zie [Indexeer functies plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van de planningen voor de Indexeer functie.

## <a name="troubleshooting"></a>Problemen oplossen

Als er een fout optreedt wanneer de Indexeer functie probeert verbinding te maken met de gegevens bron die aangeeft dat de client geen toegang heeft tot de server, bekijkt u [veelvoorkomende fouten in Indexeer functie](./search-indexer-troubleshooting.md).

## <a name="see-also"></a>Zie ook

Meer informatie over de Azure SQL-Indexer:
* [Indexeerfunctie voor Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)