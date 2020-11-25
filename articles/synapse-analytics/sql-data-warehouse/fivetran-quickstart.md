---
title: 'Quick Start: Fivetran en Data Warehouse'
description: Ga aan de slag met Fivetran en een Azure Synapse Analytics-Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 96e679c0b284cc649dbde3fba1b640f4e09df05e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001844"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Quick Start: Fivetran met Data Warehouse 

In deze Quick Start wordt beschreven hoe u een nieuwe Fivetran-gebruiker instelt voor gebruik met een Azure Synapse Analytics-Data Warehouse dat is ingericht met een SQL-groep. In het artikel wordt ervan uitgegaan dat u een bestaand Data Warehouse hebt.

## <a name="set-up-a-connection"></a>Een verbinding instellen

1. Zoek de volledig gekwalificeerde server naam en database naam die u gebruikt om verbinding te maken met uw data warehouse.
    
    Als u hulp nodig hebt bij het vinden van deze informatie, raadpleegt u [verbinding maken met uw data warehouse](../sql/connect-overview.md).

2. Kies in de installatie wizard of u rechtstreeks verbinding wilt maken met uw data base of met behulp van een SSH-tunnel.

   Als u ervoor kiest om rechtstreeks verbinding te maken met uw data base, moet u een firewall regel maken om toegang toe te staan. Deze methode is de eenvoudigste en veiligste methode.

   Als u ervoor kiest om verbinding te maken via een SSH-tunnel, maakt Fivetran verbinding met een afzonderlijke server in uw netwerk. De server biedt een SSH-tunnel voor uw data base. U moet deze methode gebruiken als uw data base zich in een niet-toegankelijk subnet in een virtueel netwerk bevindt.

3. Voeg het IP-adres **52.0.2.4** toe aan de firewall op server niveau voor het toestaan van binnenkomende verbindingen met uw data warehouse-instantie van Fivetran.

   Zie [Een serverfirewallregel maken](create-data-warehouse-portal.md#create-a-server-level-firewall-rule) voor meer informatie.

## <a name="set-up-user-credentials"></a>Gebruikers referenties instellen

1. Maak verbinding met uw data warehouse met behulp van SQL Server Management Studio (SSMS) of het hulp programma dat u wilt gebruiken. Meld u aan als een server beheerder gebruiker. Voer vervolgens de volgende SQL-opdrachten uit om een gebruiker te maken voor Fivetran:

    - In de hoofd database: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - In de Data Warehouse-Data Base:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Ken de Fivetran-gebruiker de volgende machtigingen toe aan uw data warehouse:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    De machtiging beheren is vereist voor het maken van data base-Scope-referenties die worden gebruikt wanneer een gebruiker bestanden uit Azure Blob-opslag laadt met poly base.

3. Voeg een geschikte resource klasse toe aan de Fivetran-gebruiker. De resource klasse die u gebruikt, is afhankelijk van het geheugen dat vereist is voor het maken van een column store-index. Integraties met producten als Marketo en Sales Force vereisen bijvoorbeeld een hogere resource klasse vanwege het grote aantal kolommen en de grotere hoeveelheid gegevens die de producten gebruiken. Een hogere resource klasse vereist meer geheugen voor het maken van Column Store-indexen.

    U wordt aangeraden statische resource klassen te gebruiken. U kunt beginnen met de `staticrc20` resource klasse. De `staticrc20` resource klasse wijst 200 MB toe voor elke gebruiker, ongeacht het prestatie niveau dat u gebruikt. Als column Store-indexering mislukt op het eerste niveau van de resource klasse, verhoogt u de resource klasse.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Lees voor meer informatie over [geheugen en gelijktijdigheids limieten](memory-concurrency-limits.md) en [resource klassen](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="connect-from-fivetran"></a>Verbinding maken vanaf Fivetran

Als u vanuit uw Fivetran-account verbinding wilt maken met uw data warehouse, voert u de referenties in die u gebruikt om toegang te krijgen tot uw data warehouse: 

* Host (uw server naam).
* Importeer.
* Enddatabase.
* Gebruiker (de gebruikers naam moet **fivetran \@ _server_name_** waarbij *server_name* deel uitmaakt van de URI van uw Azure-host: **_server \_ naam_. database.Windows.net**).
* Wacht woord.
