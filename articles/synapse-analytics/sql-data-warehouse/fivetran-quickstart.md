---
title: 'Snelstartgids: Fivetran en toegewezen SQL-pool (voorheen SQL DW)'
description: Ga aan de slag met Fivetran en toegewezen SQL-pool (voorheen SQL DW) in azure Synapse Analytics.
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
ms.openlocfilehash: f332c3b0bd53d80d4a8471f53c56ecab611787c1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456375"
---
# <a name="quickstart-fivetran-with-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Quick Start: Fivetran met toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics 

In deze Quick Start wordt beschreven hoe u een nieuwe Fivetran-gebruiker instelt voor gebruik met een toegewezen SQL-groep (voorheen SQL DW). In dit artikel wordt ervan uitgegaan dat u een bestaande, toegewezen SQL-groep hebt (voorheen SQL DW).

## <a name="set-up-a-connection"></a>Een verbinding instellen

1. Zoek de volledig gekwalificeerde server naam en database naam die u gebruikt om verbinding te maken met uw toegewezen SQL-groep (voorheen SQL DW).
    
    Zie [verbinding maken met uw toegewezen SQL-groep (voorheen SQL DW)](sql-data-warehouse-connection-strings.md)als u hulp nodig hebt bij het vinden van deze informatie.

2. Kies in de installatie wizard of u rechtstreeks verbinding wilt maken met uw data base of met behulp van een SSH-tunnel.

   Als u ervoor kiest om rechtstreeks verbinding te maken met uw data base, moet u een firewall regel maken om toegang toe te staan. Deze methode is de eenvoudigste en veiligste methode.

   Als u ervoor kiest om verbinding te maken via een SSH-tunnel, maakt Fivetran verbinding met een afzonderlijke server in uw netwerk. De server biedt een SSH-tunnel voor uw data base. U moet deze methode gebruiken als uw data base zich in een niet-toegankelijk subnet in een virtueel netwerk bevindt.

3. Voeg het IP-adres **52.0.2.4** toe aan de firewall op server niveau zodat binnenkomende verbindingen met uw toegewezen SQL-groep (voorheen SQL DW) van Fivetran worden toegestaan.

   Zie [Een serverfirewallregel maken](create-data-warehouse-portal.md#create-a-server-level-firewall-rule) voor meer informatie.

## <a name="set-up-user-credentials"></a>Gebruikers referenties instellen

1. Maak verbinding met uw toegewezen SQL-groep (voorheen SQL DW) met behulp van SQL Server Management Studio (SSMS) of het hulp programma dat u wilt gebruiken. Meld u aan als een server beheerder gebruiker. Voer vervolgens de volgende SQL-opdrachten uit om een gebruiker te maken voor Fivetran:

    - In de hoofd database: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - In de toegewezen SQL-groep (voorheen SQL DW) Data Base:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Ken de Fivetran-gebruiker de volgende machtigingen toe aan uw toegewezen SQL-groep (voorheen SQL DW):

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

Als u verbinding wilt maken met uw toegewezen SQL-groep (voorheen SQL DW) vanuit uw Fivetran-account, voert u de referenties in die u gebruikt om toegang te krijgen tot uw toegewezen SQL-groep (voorheen SQL DW): 

* Host (uw server naam).
* Importeer.
* Enddatabase.
* Gebruiker (de gebruikers naam moet **fivetran \@ _server_name_** waarbij *server_name* deel uitmaakt van de URI van uw Azure-host: **_server \_ naam_. database.Windows.net**).
* Wacht woord.
