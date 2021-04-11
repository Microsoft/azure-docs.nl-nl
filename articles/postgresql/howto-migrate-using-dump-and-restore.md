---
title: Dump en Restore-Azure Database for PostgreSQL-één server
description: U kunt een PostgreSQL-data base in een dump bestand uitpakken. Vervolgens kunt u herstellen vanuit een bestand dat is gemaakt door pg_dump in Azure Database for PostgreSQL enkele server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 809ff06fe460a06a689d7bbc11cdbd5ee247f585
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450052"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>Uw PostgreSQL-data base migreren met dump en herstel
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

U kunt [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) gebruiken om een postgresql-data base in een dump bestand te extra heren. Gebruik vervolgens [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) om de postgresql-data base terug te zetten vanuit een archief bestand dat is gemaakt door `pg_dump` .

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- Een [Azure database for postgresql-server](quickstart-create-server-database-portal.md), met inbegrip van firewall regels om toegang toe te staan.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) en [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) opdracht regel Programma's geïnstalleerd.

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>Een dump bestand maken dat de gegevens bevat die moeten worden geladen

Voer de volgende opdracht uit om een back-up te maken van een bestaande PostgreSQL-Data Base op locatie of in een VM:

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Als u bijvoorbeeld een lokale server en een Data Base met de naam **testdb** , voert u de volgende handelingen uit:

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>De gegevens in de doel database herstellen

Nadat u de doel database hebt gemaakt, kunt u de `pg_restore` opdracht en de  `--dbname` para meter gebruiken om de gegevens vanuit het dump bestand te herstellen in de doel database.

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

Met de `--no-owner` para meter worden alle objecten die zijn gemaakt tijdens het herstellen, het eigendom van de gebruiker die is opgegeven met `--username` . Zie de [postgresql-documentatie](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)voor meer informatie.

> [!NOTE]
> Op Azure Database for PostgreSQL servers zijn TLS/SSL-verbindingen standaard ingeschakeld. Als uw PostgreSQL-server TLS/SSL-verbindingen vereist, maar deze niet heeft, stelt u een omgevings variabele `PGSSLMODE=require` in, zodat het pg_restore-hulp programma verbinding maakt met TLS. Zonder TLS wordt de fout mogelijk gelezen: ' fataal: SSL-verbinding is vereist. Geef SSL-opties op en probeer het opnieuw. " Voer in de Windows-opdracht regel de opdracht uit `SET PGSSLMODE=require` voordat u de `pg_restore` opdracht uitvoert. In Linux of bash voert u de opdracht uit `export PGSSLMODE=require` voordat u de `pg_restore` opdracht uitvoert. 
>

In dit voor beeld herstelt u de gegevens uit het dump bestand **testdb. dump** in de Data Base **mypgsqldb** op de doel server **mydemoserver.postgres.database.Azure.com**.

Hier volgt een voor beeld van hoe u dit kunt gebruiken `pg_restore` voor één server:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

Hier volgt een voor beeld van hoe u dit kunt gebruiken `pg_restore` voor flexibele servers:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>Het migratie proces optimaliseren

Een manier om uw bestaande PostgreSQL-Data Base naar Azure Database for PostgreSQL te migreren, is door een back-up te maken van de Data Base op de bron en deze te herstellen in Azure. Als u de benodigde tijd voor het volt ooien van de migratie wilt beperken, kunt u de volgende para meters gebruiken met de opdrachten back-up en herstellen.

> [!NOTE]
> Zie [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) en [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)voor gedetailleerde informatie over de syntaxis.
>

### <a name="for-the-backup"></a>Voor de back-up

Maak de back-up met de `-Fc` Switch, zodat u de herstel bewerking parallel kunt uitvoeren om het proces te versnellen. Bijvoorbeeld:

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>Voor de herstel bewerking

- Verplaats het back-upbestand naar een virtuele machine van Azure in dezelfde regio als de Azure Database for PostgreSQL-server waarnaar u migreert. Voer de `pg_restore` van de VM uit om de netwerk latentie te verminderen. Maak de virtuele machine met [versneld netwerken](../virtual-network/create-vm-accelerated-networking-powershell.md) ingeschakeld.

- Open het dump bestand om te controleren of de instructies Create Index na het invoegen van de gegevens zijn. Als dat niet het geval is, verplaatst u de instructies Create Index nadat de gegevens zijn ingevoegd. Dit moet standaard al worden gedaan, maar het is een goed idee om te bevestigen.

- Herstel met de switches `-Fc` en `-j` (met een getal) om het herstel te parallelliseren. Het getal dat u opgeeft, is het aantal kernen op de doel server. U kunt ook instellen op twee maal het aantal kernen van de doel server om de impact te bekijken.

    Hier volgt een voor beeld van hoe u dit kunt gebruiken `pg_restore` voor één server:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    Hier volgt een voor beeld van hoe u dit kunt gebruiken `pg_restore` voor flexibele servers:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- U kunt het dump bestand ook bewerken door de opdracht toe te voegen `set synchronous_commit = off;` aan het begin en de opdracht `set synchronous_commit = on;` aan het einde. Als u de app niet aan het einde inschakelt, kan dit leiden tot verlies van gegevens, voordat de gegevens worden gewijzigd.

- Overweeg het volgende bij de doel-Azure Database for PostgreSQL server voordat de herstel bewerking wordt uitgevoerd:
    
  - Schakel het bijhouden van query prestaties uit. Deze statistieken zijn niet nodig tijdens de migratie. U kunt dit doen door in `pg_stat_statements.track` te stellen, `pg_qs.query_capture_mode` , en `pgms_wait_sampling.query_capture_mode` `NONE` .

  - Gebruik een high Compute-SKU met hoge hoeveelheid geheugen, zoals 32 vCore geheugen geoptimaliseerd, om de migratie te versnellen. Nadat het terugzetten is voltooid, kunt u de voor Keurs-SKU eenvoudig weer omlaag schalen. Hoe hoger de SKU, hoe meer parallellisme u kunt krijgen door de overeenkomstige `-j` para meter in de `pg_restore` opdracht te verhogen.

  - Meer IOPS op de doel server kunnen de prestaties van het herstel verbeteren. U kunt meer IOPS inrichten door de opslag grootte van de server te verhogen. Deze instelling kan niet worden omkeerbaar, maar houd er rekening mee dat een hogere IOPS in de toekomst in aanmerking komt voor uw werkelijke werk belasting.

Vergeet niet om deze opdrachten te testen en te valideren in een test omgeving voordat u ze in productie gebruikt.

## <a name="next-steps"></a>Volgende stappen

- Zie [uw postgresql-data base migreren met behulp van exporteren en importeren](howto-migrate-using-export-and-import.md)om een postgresql-data base te migreren met behulp van exporteren en importeren.
- Zie de [hand leiding voor database migratie](https://aka.ms/datamigration)voor meer informatie over het migreren van data bases naar Azure database for PostgreSQL.


