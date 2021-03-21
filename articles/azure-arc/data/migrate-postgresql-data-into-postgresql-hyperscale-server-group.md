---
title: Gegevens uit een PostgreSQL-data base migreren naar een PostgreSQL grootschalige-Server groep voor Azure-Arc
titleSuffix: Azure Arc enabled database services
description: Gegevens uit een PostgreSQL-data base migreren naar een PostgreSQL grootschalige-Server groep voor Azure-Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d9cbfc30b10373ad2a4f4304987dac426b5dcabe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101643572"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>PostgreSQL-data base migreren naar Azure Arc enabled PostgreSQL grootschalige-Server groep

In dit document worden de stappen beschreven voor het ophalen van uw bestaande PostgreSQL-data base (een die niet wordt gehost in azure Arc enabled Data Services) in uw Azure-PostgreSQL grootschalige-Server groep.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>Overwegingen

De PostgreSQL grootschalige-Server groep van Azure Arc is de Community-versie van PostgreSQL en wordt uitgevoerd met de uitbrei ding CitusData ingeschakeld. Een hulp programma dat werkt op PostgreSQL buiten Azure Arc moet dus werken met de Azure-PostgreSQL grootschalige-Server groep.


Met de set hulpprogram ma's die u nu gebruikt voor post gres, moet u het volgende kunnen doen:
1. Back-up maken van uw post gres-data base vanuit uw exemplaar dat buiten Azure Arc wordt gehost
2. Deze herstellen in uw PostgreSQL grootschalige-Server groep voor Azure-Arc

Wat u nog meer kunt doen, is:
- de server parameters opnieuw instellen
- de beveiligings contexten opnieuw instellen: opnieuw maken van gebruikers, rollen en machtigingen opnieuw instellen...

Als u deze back-up-of herstel bewerking wilt uitvoeren, kunt u elk hulp programma gebruiken dat back-up/herstel kan uitvoeren voor post gres. Bijvoorbeeld:
- Azure Data Studio en de post gres-extensie
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>Voorbeeld
Laten we deze stappen zien met het `pgAdmin` hulp programma.
Houd rekening met de volgende instellingen:
- **Bron**  
    Een post gres-server die on-premises wordt uitgevoerd op een bare-metal server en met de naam JEANYDSRV. Het is van versie 12 en fungeert als host voor een Data Base met de naam MyOnPremPostgresDB die één tabel bevat met 1 rij :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="migreren-bron":::

- **Beoogde**  
    Een post gres-server die wordt uitgevoerd in een Azure-Arc-omgeving en met de naam postgres01. Het is van versie 12. Deze bevat geen data base, met uitzonde ring van de standaard post gres-data base.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Migratie-bestemming":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>Maak een back-up van de bron database op locatie

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migreren-bron-back-up":::

Configureren:
1. Geef een bestands naam op: **MySourceBackup**
2. De indeling instellen op **aangepaste** 
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="migratie-bron-back-up-configureren":::

De back-up is voltooid:  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migreren-bron-back-up-voltooid":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Een lege data base maken op het doel systeem in uw PostgreSQL grootschalige-Server groep voor Azure-Arc

> [!NOTE]
> Als u een post gres-exemplaar in het `pgAdmin` hulp programma wilt registreren, moet u het open bare IP-adres van uw exemplaar in uw Kubernetes-cluster gebruiken en de poort en beveiligings context op de juiste wijze instellen. Nadat u de volgende opdracht hebt uitgevoerd, vindt u deze details op de `psql` eindpunt regel:

```console
azdata arc postgres endpoint list -n postgres01
```
Hiermee wordt een uitvoer als resultaat gegeven als:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

We noemen de doel database **RESTORED_MyOnPremPostgresDB**.

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migreren-bestemming-DB-maken" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>De data base in uw Arc-installatie herstellen

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migratre-DB-herstellen":::

Het herstel configureren:
1. Wijs naar het bestand met de back-up die u wilt herstellen: **MySourceBackup**
2. Zorg ervoor dat de indeling is ingesteld op **aangepast of tar** 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="migrate-data base-Restore-configure":::

3. Klik op **herstellen**.  

   De herstel bewerking is voltooid.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migreren-DB-herstellen-voltooid":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Controleer of de data base is hersteld in uw PostgreSQL grootschalige-Server groep voor Azure-Arc

Gebruik een van de volgende methoden:

**Van `pgAdmin` :**  

Vouw het post gres-exemplaar dat wordt gehost in uw Azure Arc Setup uit. U ziet de tabel in de data base die u hebt hersteld en wanneer u de gegevens selecteert, wordt dezelfde rij weer gegeven als in het on-premises exemplaar:

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migreren-DB-herstellen-verificatie":::

**Vanuit `psql` uw Azure Arc-installatie:**  

In de Arc-installatie kunt u gebruiken `psql` om verbinding te maken met uw post gres-exemplaar, de database context in te stellen op `RESTORED_MyOnPremPostgresDB` en de gegevens op te vragen:

1. Vermeld de eind punten voor hulp van uw `psql` Connection String:

   ```console
   azdata arc postgres endpoint list -n postgres01
   [
     {
       "Description": "PostgreSQL Instance",
       "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. Gebruik in uw `psql` Connection String de `-d` para meter om de naam van de data base aan te geven. Met de onderstaande opdracht wordt u gevraagd om het wacht woord:

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` verbinding maakt met.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. Selecteer de tabel en u ziet de gegevens die u hebt hersteld vanuit het on-premises post gres-exemplaar:

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - U ziet niet zoveel prestatie voordelen van het uitvoeren op Azure Arc enabled PostgreSQL grootschalige tot u uitschaalt en u Shard/distribueert de gegevens over de worker-knoop punten van uw PostgreSQL grootschalige-Server groep. Zie de [volgende stappen](#next-steps).
>
> - Het is momenteel niet mogelijk om te ' in azure Arc ' een bestaand post gres-exemplaar dat op locatie of in een andere Cloud zou worden uitgevoerd. Met andere woorden, het is niet mogelijk om een bepaalde sortering van ' Azure Arc agent ' te installeren op uw bestaande post gres-exemplaar om ervoor te zorgen dat er een post gres-installatie is ingeschakeld door Azure Arc. In plaats daarvan moet u een nieuw post gres-exemplaar maken en er gegevens naar verzenden. U kunt de hierboven weer gegeven techniek gebruiken om dit te doen of u kunt elk ETL-hulp programma van uw keuze gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Lees de concepten en instructies van Azure Database for PostgreSQL grootschalige om uw gegevens over meerdere grootschalige-knoop punten van PostgreSQL te verdelen en om te profiteren van de kracht van Azure Database for PostgreSQL grootschalige:
    * [Knooppunten en tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Toepassingstype bepalen](../../postgresql/concepts-hyperscale-app-type.md)
    * [Een distributiekolom kiezen](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabelcolocatie](../../postgresql/concepts-hyperscale-colocation.md)
    * [Tabellen distribueren en bewerken](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Een multi tenant-data base ontwerpen](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Een real-time analyse dashboard ontwerpen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> * Sla in deze documenten de secties over van **de Azure Portal** en **Maak een Azure-Data Base voor post gres-grootschalige (Citus)**. Implementeer de resterende stappen in de implementatie van Azure Arc. Deze secties zijn specifiek voor de Azure Database for PostgreSQL grootschalige (Citus) die worden aangeboden als een PaaS-service in de Azure-Cloud, maar de andere onderdelen van de documenten zijn rechtstreeks van toepassing op uw PostgreSQL grootschalige van Azure Arc.

- [Uw Azure Database for PostgreSQL Hyperscale-servergroep uitschalen](scale-out-postgresql-hyperscale-server-group.md)
