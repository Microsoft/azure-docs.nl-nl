---
title: Beperkingen-Azure Database for MariaDB
description: In dit artikel worden beperkingen beschreven in Azure Database for MariaDB, zoals het aantal opties voor de verbinding en de opslag engine.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 10/2/2020
ms.openlocfilehash: de561f0fdea7ea7085a4a1d3ec6f95071c36f57e
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664296"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Beperkingen in Azure Database for MariaDB
In de volgende secties worden capaciteit, ondersteuning voor opslag-engine, ondersteuning van bevoegdheden, ondersteuning voor gegevens manipulatie en functionele limieten in de database service beschreven.

## <a name="server-parameters"></a>Serverparameters

> [!NOTE]
> Als u op zoek bent naar de minimale/maximale waarden voor server parameters zoals `max_connections` en `innodb_buffer_pool_size` , wordt deze informatie verplaatst naar het artikel **[server parameters](./concepts-server-parameters.md)** .

Azure Database for MariaDB biedt ondersteuning voor het afstemmen van de waarden van server parameters. De minimum-en maximum waarde van sommige para meters (bijvoorbeeld `max_connections`, `join_buffer_size` , `query_cache_size` ) wordt bepaald door de prijs categorie en vCores van de-server. Raadpleeg [server parameters](./concepts-server-parameters.md) voor meer informatie over deze limieten.

Bij de eerste implementatie bevat een Azure voor MariaDB-server systeem tabellen voor tijdzone gegevens, maar deze tabellen worden niet ingevuld. De tijdzone tabellen kunnen worden gevuld door de `mysql.az_load_timezone` opgeslagen procedure aan te roepen vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. Raadpleeg de [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) -of [Azure cli](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) -artikelen voor informatie over het aanroepen van de opgeslagen procedure en het instellen van de tijd zones globaal of sessie niveau.

De invoeg toepassingen voor wacht woorden, zoals ' validate_password ' en ' caching_sha2_password ', worden niet ondersteund door de service.

## <a name="storage-engine-support"></a>Ondersteuning voor opslag engine

### <a name="supported"></a>Ondersteund
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [GEHEUGENMETABASE](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Niet ondersteund
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [FAXBERICHTEN](https://mariadb.com/kb/en/library/archive/)

## <a name="privileges--data-manipulation-support"></a>Bevoegdheden & ondersteuning voor gegevens manipulatie

Veel server parameters en-instellingen kunnen per ongeluk de prestaties van de server afnemen of de zuur eigenschappen van de MariaDB-server negatien. Voor het onderhouden van de service-integriteit en SLA op het niveau van een product, worden met deze service niet meerdere rollen beschikbaar. 

De MariaDB-service staat geen directe toegang tot het onderliggende bestands systeem toe. Sommige opdrachten voor het bewerken van gegevens worden niet ondersteund. 

## <a name="privilege-support"></a>Ondersteuning van bevoegdheden

### <a name="unsupported"></a>Niet ondersteund

Het volgende wordt niet ondersteund:
- Rol van DBA: beperkt. U kunt ook de gebruiker beheerder (gemaakt tijdens het maken van een nieuwe server) gebruiken om de meeste DDL-en DML-instructies uit te voeren. 
- SUPER bevoegdheid: ook is [Super privileges](https://mariadb.com/kb/en/library/grant/#global-privileges) beperkt.
- DEFINE: vereist Super privileges om te maken en beperkt. Als u gegevens importeert met behulp van een back-up, verwijdert u de `CREATE DEFINER` opdrachten hand matig of gebruikt u de `--skip-definer` opdracht bij het uitvoeren van een mysqldump.
- Systeem databases: de [MySQL-systeem database](https://mariadb.com/kb/en/the-mysql-database-tables/) is alleen-lezen en wordt gebruikt ter ondersteuning van verschillende PaaS-functionaliteit. U kunt geen wijzigingen aanbrengen in de `mysql` systeem database.
- `SELECT ... INTO OUTFILE`: Wordt niet ondersteund in de service.

### <a name="supported"></a>Ondersteund
- `LOAD DATA INFILE` wordt ondersteund, maar de `[LOCAL]` para meter moet worden opgegeven en worden omgeleid naar een UNC-pad (Azure-opslag gekoppeld aan SMB).

## <a name="functional-limitations"></a>Functionele beperkingen

### <a name="scale-operations"></a>Schaal bewerkingen
- Dynamisch schalen naar en van de basis prijs categorieën wordt momenteel niet ondersteund.
- Het verminderen van de grootte van de server opslag wordt niet ondersteund.

### <a name="server-version-upgrades"></a>Server versie-upgrades
- Automatische migratie tussen de primaire data base-engine versies wordt momenteel niet ondersteund.

### <a name="point-in-time-restore"></a>Een punt in de tijd herstellen
- Wanneer u de functie PITR gebruikt, wordt de nieuwe server gemaakt met dezelfde configuraties als de server waarop deze is gebaseerd.
- Het herstellen van een verwijderde server wordt niet ondersteund.

### <a name="subscription-management"></a>Abonnementsbeheer
- Het dynamisch verplaatsen van vooraf gemaakte servers over het abonnement en de resource groep wordt momenteel niet ondersteund.

### <a name="vnet-service-endpoints"></a>VNeT-service-eindpunten
- Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.

### <a name="storage-size"></a>Opslag grootte
- Raadpleeg de [prijs categorie](concepts-pricing-tiers.md) voor de limieten voor opslag grootte per prijs categorie.

## <a name="current-known-issues"></a>Huidige bekende problemen
- MariaDB-Server exemplaar geeft de onjuiste server versie weer nadat de verbinding tot stand is gebracht. Gebruik de opdracht om de juiste engine versie van Server exemplaar op te halen `select version();` .

## <a name="next-steps"></a>Volgende stappen
- [Wat is er beschikbaar in elke servicelaag](concepts-pricing-tiers.md)
- [Ondersteunde versies van MariaDB-data base](concepts-supported-versions.md)
