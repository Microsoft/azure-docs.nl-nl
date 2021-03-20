---
title: De data base oplossen Azure Database for MySQL beschadigd
description: In dit artikel vindt u informatie over het oplossen van beschadigde database problemen in Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91766898"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Problemen met beschadigde databases oplossen in Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Beschadiging van de data base kan leiden tot uitval tijd voor uw toepassing. Het is ook cruciaal om beschadigings problemen op tijd op te lossen om gegevens verlies te voor komen. Wanneer de data base is beschadigd, wordt deze fout weer gegeven in de server logboeken: `InnoDB: Database page corruption on disk or a failed.`

In dit artikel leert u hoe u problemen met een Data Base of tabel kunt oplossen. Azure Database for MySQL maakt gebruik van de InnoDB-engine. Het bevat automatische beschadigings controles en herstel bewerkingen. InnoDB controleert op beschadigde pagina's door controle sommen uit te voeren op elke pagina die wordt gelezen. Als er een controlesom verschil wordt gevonden, wordt de MySQL-server automatisch gestopt.

Voer de volgende opties uit om snel problemen met uw Data Base op te lossen.

## <a name="restart-your-mysql-server"></a>De MySQL-server opnieuw opstarten

Normaal gesp roken ziet u dat er een Data Base of tabel is beschadigd wanneer uw toepassing toegang heeft tot de tabel of Data Base. InnoDB bevat een mechanisme voor crash herstel dat de meeste problemen kan oplossen wanneer de server opnieuw wordt opgestart. Het opnieuw opstarten van de server kan ervoor zorgen dat de server kan worden hersteld na een storing waardoor de status van de data base niet is beschadigd.

## <a name="use-the-dump-and-restore-method"></a>De methode dump en Restore gebruiken

U wordt aangeraden beschadigings problemen op te lossen met behulp van een *dump-en herstel* methode. Deze methode omvat:
1. De beschadigde tabel wordt geopend.
1. Het hulp programma mysqldump gebruiken om een logische back-up van de tabel te maken. Met de back-up worden de tabel structuur en de gegevens erin bewaard.
1. De tabel wordt opnieuw geladen in de data base.

### <a name="back-up-your-database-or-tables"></a>Maak een back-up van uw data base of tabellen

> [!Important]
> - Zorg ervoor dat u een firewall regel hebt geconfigureerd voor toegang tot de server vanaf uw client computer. Zie [een firewall regel configureren op één server](howto-manage-firewall-using-portal.md) en [een firewall regel configureren op een flexibele server](flexible-server/how-to-connect-tls-ssl.md)voor meer informatie.
> - Gebruik de SSL-optie `--ssl-cert` voor mysqldump als SSL is ingeschakeld.

Maak een back-upbestand op de opdracht regel met behulp van mysqldump. Gebruik deze opdracht:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parameter beschrijvingen:
- `[ssl-cert=/path/to/pem]`: Het pad naar het SSL-certificaat. Down load het SSL-certificaat op uw client computer en stel het pad in de opdracht in. Gebruik deze para meter niet als SSL is uitgeschakeld.
- `[host]`: Uw Azure Database for MySQL-server.
- `[uname]`: De gebruikers naam van de server beheerder.
- `[pass]`: Het wacht woord voor de gebruiker met beheerders rechten.
- `[dbname]`: De naam van uw data base.
- `[backupfile.sql]`: De bestands naam van de back-up van de data base.

> [!Important]
> - Voor één server gebruikt u de indeling `admin-user@servername` voor vervangen `myserveradmin` in de volgende opdrachten.
> - Voor een flexibele server gebruikt u de indeling `admin-user` om te vervangen `myserveradmin` in de volgende opdrachten.

Als een specifieke tabel is beschadigd, selecteert u specifieke tabellen in de data base om een back-up te maken:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Als u een back-up wilt maken van een of meer data bases, gebruikt u de `--database` Switch en geeft u de database namen op, gescheiden door spaties:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Uw data base of tabellen herstellen

De volgende stappen laten zien hoe u uw data base of tabellen kunt herstellen. Nadat u het back-upbestand hebt gemaakt, kunt u de tabellen of data bases herstellen met behulp van het hulp programma mysql. Voer deze opdracht uit:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Hier volgt een voor beeld van het terugzetten `testdb` van een back-upbestand dat is gemaakt met mysqldump: 

> [!Important]
> - Voor één server gebruikt u de indeling `admin-user@servername` voor vervangen `myserveradmin` in de volgende opdracht.
> - Voor een flexibele server gebruikt u de indeling ```admin-user``` voor vervangen `myserveradmin` in de volgende opdracht. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Volgende stappen
Als de voor gaande stappen het probleem niet verhelpen, kunt u altijd de volledige server herstellen:
- [Server herstellen in Azure Database for MySQL-één server](howto-restore-server-portal.md)
- [Server herstellen in Azure Database for MySQL-flexibele server](flexible-server/how-to-restore-server-portal.md)



