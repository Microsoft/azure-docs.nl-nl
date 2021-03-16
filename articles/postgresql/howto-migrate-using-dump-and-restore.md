---
title: Dump en Restore-Azure Database for PostgreSQL-één server
description: Hierin wordt beschreven hoe u een PostgreSQL-data base ophaalt in een dump bestand en herstelt vanuit een bestand dat is gemaakt door pg_dump in Azure Database for PostgreSQL-één server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 16166183b56b371fe8338894f83dbacf2e659c53
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563552"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Uw PostgreSQL-database migreren met dumpen en herstel
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

U kunt [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) gebruiken om een postgresql-data base te extra heren naar een dump bestand en [Pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) om de postgresql-data base te herstellen vanuit een archief bestand dat door pg_dump is gemaakt.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- Een [Azure database for postgresql server](quickstart-create-server-database-portal.md) met firewall regels om toegang en data base daaronder toe te staan.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) en [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) opdracht regel Programma's geïnstalleerd

Voer de volgende stappen uit om uw PostgreSQL-data base te dumpen en te herstellen:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Een dump bestand maken met behulp van pg_dump dat de gegevens bevat die moeten worden geladen
Voer de volgende opdracht uit om een back-up te maken van een bestaande PostgreSQL-Data Base op locatie of in een VM:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Als u bijvoorbeeld een lokale server en een Data Base met de naam **testdb**
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Herstel de gegevens in het doel Azure Database for PostgreSQL met behulp van pg_restore
Nadat u de doel database hebt gemaakt, kunt u de opdracht pg_restore en de para meter-d,--dbname gebruiken om de gegevens vanuit het dump bestand te herstellen in de doel database.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

Met de para meter--no-owner worden alle objecten die zijn gemaakt tijdens het herstellen, het eigendom van de gebruiker die is opgegeven met--username. Zie de officiële PostgreSQL-documentatie over [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)voor meer informatie.

> [!NOTE]
> Als uw PostgreSQL-server TLS/SSL-verbindingen (standaard op Azure Database for PostgreSQL servers) vereist, stelt u een omgevings variabele in, `PGSSLMODE=require` zodat het hulp programma pg_restore verbinding maakt met TLS. Zonder TLS kan de fout worden gelezen  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Voer in de Windows-opdracht regel de opdracht uit `SET PGSSLMODE=require` voordat u de opdracht pg_restore uitvoert. Voer in Linux of bash de opdracht uit `export PGSSLMODE=require` voordat u de opdracht pg_restore uitvoert.
>

In dit voor beeld herstelt u de gegevens uit het dump bestand **testdb. dump** in de Data Base **mypgsqldb** op de doel server **mydemoserver.postgres.database.Azure.com**.

Hier volgt een voor beeld van het gebruik van deze **pg_restore** voor **één server**:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```
Hier volgt een voor beeld van het gebruik van deze **pg_restore** voor **flexibele server**:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```
---

## <a name="optimizing-the-migration-process"></a>Het migratie proces optimaliseren

Een manier om uw bestaande PostgreSQL-data base te migreren naar Azure Database for PostgreSQL-service is door een back-up te maken van de Data Base op de bron en deze te herstellen in Azure. Als u de benodigde tijd voor het volt ooien van de migratie wilt beperken, kunt u de volgende para meters gebruiken met de opdrachten back-up en herstellen.

> [!NOTE]
> Zie de artikelen [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) en [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)voor gedetailleerde informatie over de syntaxis.
>

### <a name="for-the-backup"></a>Voor de back-up
- Maak de back-up met de-FC-switch, zodat u de terugzet bewerking parallel kunt uitvoeren om deze sneller te maken. Bijvoorbeeld:

    ```bash
    pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
    ```

### <a name="for-the-restore"></a>Voor de herstel bewerking
- We raden u aan om het back-upbestand naar een virtuele machine van Azure te verplaatsen in dezelfde regio als de Azure Database for PostgreSQL-server waarnaar u migreert en de pg_restore van die virtuele machine uit te voeren om de netwerk latentie te verminderen. We raden u ook aan om de virtuele machine te maken met [versneld netwerken](../virtual-network/create-vm-accelerated-networking-powershell.md) ingeschakeld.

- Deze moet standaard al worden uitgevoerd, maar open het dump bestand om te controleren of de instructies Create Index na het invoegen van de gegevens zijn. Als dat niet het geval is, verplaatst u de instructies Create Index nadat de gegevens zijn ingevoegd.

- Herstel met de switches-FC en-j *#* om parallelliseren te herstellen. *#* is het aantal kernen op de doel server. U kunt ook proberen het *#* aantal kernen van de doel server twee maal op te geven om de impact te bekijken. Bijvoorbeeld:

Hier volgt een voor beeld van het gebruik van deze **pg_restore** voor **één server**:
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
```
Hier volgt een voor beeld van het gebruik van deze **pg_restore** voor **flexibele server**:
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
 ```

- U kunt het dump bestand ook bewerken door de opdracht *set synchronous_commit = uit* te voegen aan het begin en de opdracht *set synchronous_commit = aan* aan het einde. Als u deze niet aan het einde inschakelt, kan dit leiden tot verlies van gegevens, voordat de gegevens worden gewijzigd.

- Overweeg het volgende bij de doel-Azure Database for PostgreSQL server voordat de herstel bewerking wordt uitgevoerd:
    - Schakel het bijhouden van query prestaties uit, omdat deze statistieken niet nodig zijn tijdens de migratie. U kunt dit doen door pg_stat_statements. track, pg_qs. query_capture_mode en pgms_wait_sampling. query_capture_mode in te stellen op geen.

    - Gebruik een high Compute-SKU met hoge hoeveelheid geheugen, zoals 32 vCore geheugen geoptimaliseerd, om de migratie te versnellen. Nadat het terugzetten is voltooid, kunt u de voor Keurs-SKU eenvoudig weer omlaag schalen. Hoe hoger de SKU, hoe meer parallellisme u kunt betrekken door de overeenkomstige `-j` para meter te verhogen in de pg_restore opdracht.

    - Meer IOPS op de doel server kunnen de prestaties van het herstel verbeteren. U kunt meer IOPS inrichten door de opslag grootte van de server te verhogen. Deze instelling kan niet ongedaan worden gemaakt, maar u kunt ook overwegen of een hogere IOPS in de toekomst voor deel zou zijn van uw werkelijke workload.

Vergeet niet om deze opdrachten te testen en te valideren in een test omgeving voordat u ze in productie gebruikt.

## <a name="next-steps"></a>Volgende stappen
- Zie [uw postgresql-data base migreren met behulp van exporteren en importeren](howto-migrate-using-export-and-import.md)om een postgresql-data base te migreren met behulp van exporteren en importeren.
- Zie de [hand leiding voor database migratie](https://aka.ms/datamigration)voor meer informatie over het migreren van data bases naar Azure database for PostgreSQL.
