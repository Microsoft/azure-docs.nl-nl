---
title: Toegang tot werkruimten, gegevens en pijplijnen beheren
description: Meer informatie over toegangsbeheer voor werkruimten, gegevens en pijplijnen in Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: c4304aeadf2950c1a91ee50ba9ecd895b2561b41
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461352"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Toegang tot werkruimten, gegevens en pijplijnen beheren

Meer informatie over toegangsbeheer voor werkruimten, gegevens en pijplijnen in Azure Synapse Analytics.

> [!NOTE]
> Voor GA is Azure RBAC beter ontwikkeld door de introductie van Synapse-specifieke Azure-rollen

## <a name="access-control-for-workspace"></a>Toegangsbeheer voor werkruimte

Voor een productie-implementatie in een Azure Synapse-werkruimte raden we u aan om uw omgeving zo te organiseren dat u gebruikers en beheerders eenvoudig kunt inrichten.

> [!NOTE]
> Dit wordt hier gedaan door verschillende beveiligingsgroepen te maken en de werkruimte vervolgens te configureren om deze consistent te gebruiken. Nadat de groepen zijn ingesteld, hoeft een beheerder alleen het lidmaatschap van de beveiligingsgroepen te beheren.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Stap 1: Beveiligingsgroepen instellen met namen die voldoen aan dit patroon

1. Beveiligingsgroep maken met de naam `Synapse_WORKSPACENAME_Users`
2. Beveiligingsgroep maken met de naam `Synapse_WORKSPACENAME_Admins`
3. `Synapse_WORKSPACENAME_Admins` is toegevoegd aan `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Meer informatie over het maken van een beveiligingsgroep vindt u in [dit artikel](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).
>
> Meer informatie over het toevoegen van een beveiligings groep vanuit een andere beveiligings groep vindt u in [dit artikel](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal).
>
> WORKSPACENAME: vervang dit deel door de naam van uw werkruimte.

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Stap 2: Het standaard ADLS Gen2-account voorbereiden

Toen u uw werkruimte inrichtte moest u een [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)-account selecteren en een container voor het bestandssysteem voor de werkruimte die u wilt gebruiken.

1. Open de [Azure-portal](https://portal.azure.com)
2. Ga naar het Azure Data Lake Storage Gen2-account
3. Ga naar de container (bestandssysteem) die u voor de Azure Synapse-werkruimte hebt gekozen
4. Selecteer **Toegangsbeheer (IAM)**
5. Wijs de volgende rollen toe:
   1. De rol van **Lezer** aan: `Synapse_WORKSPACENAME_Users`
   2. De rol van **Eigenaar van opslagblobgegevens** aan: `Synapse_WORKSPACENAME_Admins`
   3. De rol van **Bijdrager voor opslagblobgegevens** aan: `Synapse_WORKSPACENAME_Users`
   4. De rol van **Eigenaar van opslagblobgegevens** aan: `WORKSPACENAME`

> [!NOTE]
> WORKSPACENAME: vervang dit deel door de naam van uw werkruimte.

### <a name="step-3-configure-the-workspace-admin-list"></a>Stap 3: De lijst met werkruimtebeheerders configureren

1. Ga naar de [**Azure Synapse-webinterface**](https://web.azuresynapse.net)
2. Ga naar **Beheren**  > **Beveiliging** > **Toegangsbeheer**
3. Klik op **Beheerder toevoegen** en selecteer `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Stap 4: SQL-beheerderstoegang voor de werkruimte configureren

1. Ga naar de [Azure-portal](https://portal.azure.com)
2. Navigeer naar de werkruimte
3. Ga naar **Instellingen** > **Active Directory-beheerder**
4. Selecteer **Beheerder instellen**
5. Selecteer `Synapse_WORKSPACENAME_Admins`
6. Kies **Selecteren**
7. Selecteer **Opslaan**

> [!NOTE]
> WORKSPACENAME: vervang dit deel door de naam van uw werkruimte.

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Stap 5: Gebruikers en beheerders toevoegen aan en verwijderen uit beveiligingsgroepen

1. Gebruikers toevoegen die beheerderstoegang nodig hebben tot `Synapse_WORKSPACENAME_Admins`
2. Alle andere gebruikers toevoegen aan `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Meer informatie over het toevoegen van een gebruiker als een lid aan een beveiligingsgroep vindt u in [dit artikel](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal)
> 
> WORKSPACENAME: vervang dit deel door de naam van uw werkruimte.

## <a name="access-control-to-data"></a>Toegangsbeheer voor gegevens

Toegangsbeheer voor de onderliggende gegevens wordt in drie delen gesplitst:

- Toegang op gegevensvlak tot het opslagaccount (hierboven geconfigureerd in stap 2)
- Toegang op gegevensvlak tot de SQL-databases (voor zowel toegewezen SQL-pools als serverloze SQL-pools)
- Een referentie maken voor serverloze SQL-pooldatabases via het opslagaccount

## <a name="access-control-to-sql-databases"></a>Toegangsbeheer voor SQL-databases

> [!TIP]
> De onderstaande stappen moeten worden uitgevoerd voor **elke** SQL-database om gebruikerstoegang te verlenen tot alle SQL-databases, met uitzondering van de sectie [Machtigingen op serverniveau](#server-level-permission). Hier kunt u de sysadmin-rol aan een gebruiker toewijzen.

### <a name="serverless-sql-pool"></a>Serverloze SQL-pool

In deze sectie vindt u voorbeelden van hoe u gebruikers een machtiging kunt geven voor een bepaalde database of volledige servermachtigingen.

#### <a name="database-level-permission"></a>Machtiging op databaseniveau

Volg de stappen in dit voorbeeld als u een gebruiker toegang wilt verlenen tot een **individuele** serverloze SQL-pooldatabase:

1. AANMELDING maken

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. GEBRUIKER maken

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. GEBRUIKER toevoegen aan leden van de opgegeven rol

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

> [!NOTE]
> Vervang de alias door de alias van de gebruiker die u toegang wilt geven en het domein door het bedrijfsdomein dat u gebruikt.

#### <a name="server-level-permission"></a>Machtiging op serverniveau

Volg de stappen in dit voorbeeld als u een gebruiker toegang wilt verlenen tot **alle** serverloze SQL-pooldatabases:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="dedicated-sql-pool"></a>Toegewezen SQL-pool

Volg deze stappen als u een gebruiker toegang wilt verlenen tot een **individuele** SQL-database:

1. Maak de gebruiker in de database door de volgende opdracht uit te voeren, gericht op de gewenste database in de contextkiezer (vervolgkeuzelijst voor het selecteren van databases):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Zo kent u de gebruiker een rol toe om toegang te krijgen tot de database:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* en *db_datawriter* kunnen worden gebruikt voor lees- en schrijfmachtigingen als het verlenen van *db_owner*-toestemming niet wenselijk is.
> Een Spark-gebruiker heeft de *db_owner*-machtiging nodig om rechtstreeks vanuit Spark naar/vanuit een toegewezen SQL-pool te lezen en te schrijven.

Wanneer de gebruikers zijn gemaakt, controleert u of u met de serverloze SQL-pool een query kan uitvoeren voor het opslagaccount.

## <a name="access-control-to-workspace-pipeline-runs"></a>Toegangsbeheer voor pijplijnuitvoeringen in een werkruimte

### <a name="workspace-managed-identity"></a>Door een werkruimte beheerde identiteit

> [!IMPORTANT]
> Als u pijplijnen wilt uitvoeren die gegevenssets of activiteiten bevatten die verwijzen naar een toegewezen SQL-pool, moet de werkruimte-id rechtstreeks toegang hebben tot de SQL-pool.

Voer de volgende opdrachten uit voor elke toegewezen SQL-pool, zodat de door de werkruimte beheerde identiteit pijplijnen kan uitvoeren op de database van de SQL-pool:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Deze machtiging kan worden verwijderd door in dezelfde SQL-pool het volgende script uit te voeren:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Volgende stappen

Zie [Door Azure Synapse-werkruimte beheerde identiteit](../security/synapse-workspace-managed-identity.md) voor een overzicht van door de Synapse-werkruimte beheerde identiteit. Zie [Principals](https://msdn.microsoft.com/library/ms181127.aspx) voor meer informatie over database-principals. Meer informatie over databaserollen vindt u in het artikel [Databaserollen](https://msdn.microsoft.com/library/ms189121.aspx).
